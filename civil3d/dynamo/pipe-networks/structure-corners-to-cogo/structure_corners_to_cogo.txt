# -*- coding: utf-8 -*-
"""
Dynamo (CPython3) - Civil 3D
Read the structures of a pipe network, compute the 4 corners of each
structure and create a COGO point on every corner.

Inputs (Python Script node):
    IN[0] : network name (str)  -> "" or None = all networks in the drawing
    IN[1] : run (bool)          -> True to create the points
    IN[2] : elevation mode (str)-> "rim" | "sump" | "insertion"   (default "rim")
Output:
    OUT   : list of [structure name, corner, x, y, z, point number]
"""

# ---------------------------------------------------------------------------
# 1) Load the AutoCAD / Civil 3D .NET assemblies
# ---------------------------------------------------------------------------
import clr
import math

clr.AddReference('AcMgd')
clr.AddReference('AcCoreMgd')
clr.AddReference('AcDbMgd')
clr.AddReference('AecBaseMgd')
clr.AddReference('AeccDbMgd')

from Autodesk.AutoCAD.ApplicationServices import Application
from Autodesk.AutoCAD.DatabaseServices import OpenMode
from Autodesk.AutoCAD.Geometry import Point3d
from Autodesk.Civil.ApplicationServices import CivilApplication
from Autodesk.Civil.DatabaseServices import Network, Structure, CogoPoint

# ---------------------------------------------------------------------------
# 2) Read the node inputs
# ---------------------------------------------------------------------------
network_name = IN[0] if len(IN) > 0 else None
run = IN[1] if len(IN) > 1 else False
elev_mode = (IN[2] if len(IN) > 2 and IN[2] else "rim").lower()


# ---------------------------------------------------------------------------
# 3) Helper: structure size (length along its X axis, width along its Y axis)
# ---------------------------------------------------------------------------
def get_structure_size(struct):
    """Return (length, width) of the structure in drawing units.
    Box     -> Length x Width
    Cylinder-> Diameter x Diameter (bounding square of the circle)"""
    shape = str(struct.BoundingShape)          # "Box", "Cylinder", ...
    width = struct.DiameterOrWidth
    if "Box" in shape:
        length = struct.Length
    else:
        length = width                          # circular manhole
    return length, width


# ---------------------------------------------------------------------------
# 4) Helper: elevation used for the COGO points
# ---------------------------------------------------------------------------
def get_elevation(struct):
    if elev_mode == "sump":
        return struct.SumpElevation
    if elev_mode == "insertion":
        return struct.Position.Z
    return struct.RimElevation


# ---------------------------------------------------------------------------
# 5) Helper: the 4 corners rotated with the structure rotation
# ---------------------------------------------------------------------------
def get_corners(struct):
    center = struct.Position                    # Point3d (insertion point)
    rot = struct.Rotation                       # radians
    length, width = get_structure_size(struct)
    hl, hw = length / 2.0, width / 2.0
    z = get_elevation(struct)

    cos_r, sin_r = math.cos(rot), math.sin(rot)

    # local offsets (before rotation) - counter-clockwise starting bottom-left
    local = [("BL", -hl, -hw),
             ("BR",  hl, -hw),
             ("TR",  hl,  hw),
             ("TL", -hl,  hw)]

    corners = []
    for tag, dx, dy in local:
        x = center.X + dx * cos_r - dy * sin_r
        y = center.Y + dx * sin_r + dy * cos_r
        corners.append((tag, Point3d(x, y, z)))
    return corners


# ---------------------------------------------------------------------------
# 6) Main
# ---------------------------------------------------------------------------
def main():
    if not run:
        return "Set IN[1] = True to create the COGO points."

    adoc = Application.DocumentManager.MdiActiveDocument
    db = adoc.Database
    civdoc = CivilApplication.ActiveDocument
    result = []

    with adoc.LockDocument():
        with db.TransactionManager.StartTransaction() as t:

            # 6.1 loop on all pipe networks in the drawing
            for net_id in civdoc.GetPipeNetworkIds():
                net = t.GetObject(net_id, OpenMode.ForRead)
                if network_name and net.Name != network_name:
                    continue

                # 6.2 loop on the structures of this network
                for st_id in net.GetStructureIds():
                    st = t.GetObject(st_id, OpenMode.ForRead)

                    # 6.3 the 4 corners -> COGO points
                    for tag, pt in get_corners(st):
                        pid = civdoc.CogoPoints.Add(pt, True)
                        cogo = t.GetObject(pid, OpenMode.ForWrite)
                        cogo.RawDescription = "{}_{}".format(st.Name, tag)
                        result.append([st.Name, tag,
                                       round(pt.X, 3), round(pt.Y, 3),
                                       round(pt.Z, 3), cogo.PointNumber])

            t.Commit()

    if not result:
        return "No structures found (check the network name)."
    return result


try:
    OUT = main()
except Exception as ex:
    import traceback
    OUT = "Error: {}\n{}".format(ex, traceback.format_exc())
