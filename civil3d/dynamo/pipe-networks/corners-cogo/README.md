# `corners-cogo` — Structure Corners → COGO Points

| | |
|---|---|
| **الاسم المختصر** | `corners-cogo` |
| **البرنامج** | Civil 3D + Dynamo |
| **المحرك** | CPython3 |
| **الكود (جاهز للنسخ)** | [`corners-cogo.txt`](corners-cogo.txt) |

ملف الكود فيه الكود بس، من غير أي تعليقات أو شرح، علشان تنسخه كله مرة واحدة.
الشرح والتعليمات كلها في الملف ده.

---

## الكود بيعمل إيه
1. بيقرا كل الـ Structures في Pipe Network معينة، أو في كل الـ networks.
2. بيحسب الأربع أركان لكل Structure بعد ما يطبّق عليها زاوية دورانها.
3. بيعمل COGO Point على كل ركن، ووصفه بالشكل ده: `اسم_الـStructure_الركن`، مثلاً `MH-1_TR`.

---

## طريقة الاستخدام
1. افتح الرسمة في Civil 3D، وبعدها افتح Dynamo.
2. ضيف **Python Script** node، واختار المحرك **CPython3**.
3. افتح ملف [`corners-cogo.txt`](corners-cogo.txt) على GitHub، ودوس **Copy raw file**، والصق الكود جوه الـ node.
4. كبّر الـ node لحد ما يبقى فيها **٣ مداخل** (بزرار `+`)، ووصّل فيها:

| المدخل | الـ Node اللي توصّلها | القيمة |
|---|---|---|
| `IN[0]` | String | اسم الـ Network. لو سبته فاضي هيلف على كل الـ networks |
| `IN[1]` | Boolean | خليه `True` علشان ينفّذ |
| `IN[2]` | String | منسوب النقاط: `rim` أو `sump` أو `insertion`. لو فاضي هياخد `rim` |

5. وصّل **Watch** node على المخرج علشان تشوف النتيجة.
6. **بعد ما ينفّذ، رجّع `IN[1]` لـ `False`**، علشان Dynamo مايعيدش التشغيل ويعمل نقط متكررة.

## المخرجات
ليستة فيها لكل نقطة: `[اسم الـ Structure، الركن، X، Y، Z، رقم النقطة]`.
لو حصل خطأ، نص الخطأ بيظهر كامل في الـ Watch node.

## أسماء الأركان
`BL` تحت شمال · `BR` تحت يمين · `TR` فوق يمين · `TL` فوق شمال (بالنسبة لاتجاه الـ Structure نفسه).

## ارتفاع النقاط (Z)
| قيمة `IN[2]` | مكان النقاط في المجسم | الخاصية |
|---|---|---|
| `rim` (الافتراضي) | **فوق**، على منسوب الغطا | `RimElevation` |
| `sump` | **تحت**، على أرضية الـ Structure من جوه | `SumpElevation` |
| `insertion` | على منسوب نقطة الإدراج، وغالباً هي نفس الـ Rim | `Position.Z` |

> المنسوب `sump` هو أرضية الـ Structure من جوه. أسفل المجسم من بره أوطى منه بمقدار سُمك الأرضية (Floor Thickness).

---

## شرح الكود حتة حتة

### 1) تحميل مكتبات AutoCAD و Civil 3D
```python
import clr
import math
clr.AddReference('AcMgd')
clr.AddReference('AcCoreMgd')
clr.AddReference('AcDbMgd')
clr.AddReference('AecBaseMgd')
clr.AddReference('AeccDbMgd')
```
`clr` هي اللي بتخلي Python يكلم مكتبات ‎.NET بتاعة Autodesk. كل `AddReference` بيحمّل ملف DLL:
- `AcMgd` و `AcCoreMgd`: الـ Application والـ Document بتوع AutoCAD.
- `AcDbMgd`: الـ Database والـ Transaction والـ Geometry، زي `Point3d`.
- `AecBaseMgd` و `AeccDbMgd`: عناصر Civil 3D، زي Network و Structure و CogoPoint.

`math` مكتبة Python العادية، ومستخدمينها علشان `cos` و `sin`.

بعدها بنعمل `import` للكلاسات اللي هنستخدمها:
```python
from Autodesk.AutoCAD.ApplicationServices import Application
from Autodesk.AutoCAD.DatabaseServices import OpenMode
from Autodesk.AutoCAD.Geometry import Point3d
from Autodesk.Civil.ApplicationServices import CivilApplication
```

### 2) قراءة المدخلات
```python
network_name = IN[0] if len(IN) > 0 else None
run = IN[1] if len(IN) > 1 else False
elev_mode = (IN[2] if len(IN) > 2 and IN[2] else "rim").lower()
```
- `IN` هي ليستة المداخل اللي جاية من الـ node.
- الشرط `len(IN) > ...` بيمنع الخطأ لو نسيت توصّل مدخل، وساعتها بيستخدم قيمة افتراضية.
- `.lower()` بتخلي `RIM` و `Rim` و `rim` كلهم نفس الحاجة.
- `run` هو زرار الأمان. من غيره، Dynamo هيعمل نقط كل ما تغيّر أي حاجة في الجراف.

### 3) مقاس الـ Structure
```python
def get_structure_size(struct):
    shape = str(struct.BoundingShape)
    width = struct.DiameterOrWidth
    if "Box" in shape:
        length = struct.Length
    else:
        length = width
    return length, width
```
- `BoundingShape` بيقول شكل الـ Structure: `Box` يعني مستطيل، و `Cylinder` يعني دايري.
- لو مستطيل بناخد الطول (`Length`) والعرض (`DiameterOrWidth`).
- لو دايري بناخد القطر في الاتجاهين، فالأركان بتطلع على المربع اللي حوالين الدايرة.
- علشان تاخد **المقاس الداخلي**، غيّر `DiameterOrWidth` لـ `InnerDiameterOrWidth`، و `Length` لـ `InnerLength`.

### 4) المنسوب (Z)
```python
def get_elevation(struct):
    if elev_mode == "sump":
        return struct.SumpElevation
    if elev_mode == "insertion":
        return struct.Position.Z
    return struct.RimElevation
```
بيرجّع المنسوب حسب قيمة `IN[2]`، ولو مكتوب أي حاجة غير `sump` و `insertion` بيرجّع الـ Rim.

### 5) حساب الأركان الأربعة مع الدوران
```python
def get_corners(struct):
    center = struct.Position
    rot = struct.Rotation
    length, width = get_structure_size(struct)
    hl, hw = length / 2.0, width / 2.0
    z = get_elevation(struct)
    cos_r, sin_r = math.cos(rot), math.sin(rot)

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
```
دي أهم حتة في الكود:
- `Position` هي نقطة إدراج الـ Structure (المركز)، و `Rotation` هي زاوية الدوران بالـ **radian**.
- `hl` و `hw` هما نص الطول ونص العرض.
- `local` فيها الأركان الأربعة كأن الـ Structure مش متلفّ، بالنسبة للمركز: `(±L/2, ±W/2)`.
- معادلة الدوران (Rotation Matrix) بتلفّ كل ركن بنفس زاوية الـ Structure، وبعدين بتجمعه على إحداثيات المركز:
  - `x = Xc + dx·cos(θ) − dy·sin(θ)`
  - `y = Yc + dx·sin(θ) + dy·cos(θ)`
- النتيجة إن النقط بتطلع على الأركان الحقيقية حتى لو الـ Structure مايل.

### 6) التنفيذ الأساسي
```python
def main():
    if not run:
        return "Set IN[1] = True to create the COGO points."

    adoc = Application.DocumentManager.MdiActiveDocument
    db = adoc.Database
    civdoc = CivilApplication.ActiveDocument
    result = []
```
- لو `run` مش `True` بيطلع رسالة وبيقف، ومش بيعمل أي حاجة.
- `adoc` هي الرسمة المفتوحة، و `db` قاعدة بياناتها، و `civdoc` هي نفس الرسمة بس من ناحية Civil 3D، ومنها بنوصل للـ networks والـ COGO points.

```python
    with adoc.LockDocument():
        with db.TransactionManager.StartTransaction() as t:
```
- **`LockDocument`**: لازم تقفل الرسمة قبل ما تعدّل فيها من Dynamo.
- **`Transaction`**: أي فتح أو تعديل لعنصر بيحصل جواها، ومن غير `Commit()` في الآخر مفيش حاجة هتتحفظ.

```python
            for net_id in civdoc.GetPipeNetworkIds():
                net = t.GetObject(net_id, OpenMode.ForRead)
                if network_name and net.Name != network_name:
                    continue
```
- بيلف على كل الـ Pipe Networks في الرسمة.
- `GetObject(id, ForRead)` بيحوّل الـ ObjectId لعنصر حقيقي تقدر تقرا منه.
- لو كاتب اسم Network في `IN[0]`، أي Network تانية بيتخطاها (`continue`).

```python
                for st_id in net.GetStructureIds():
                    st = t.GetObject(st_id, OpenMode.ForRead)
```
بيلف على كل الـ Structures في الـ Network دي، ويفتح كل واحدة للقراءة.

```python
                    for tag, pt in get_corners(st):
                        pid = civdoc.CogoPoints.Add(pt, True)
                        cogo = t.GetObject(pid, OpenMode.ForWrite)
                        cogo.RawDescription = "{}_{}".format(st.Name, tag)
                        result.append([st.Name, tag,
                                       round(pt.X, 3), round(pt.Y, 3),
                                       round(pt.Z, 3), cogo.PointNumber])
```
- لكل ركن، `CogoPoints.Add(pt, True)` بيعمل COGO Point. الـ `True` معناها ياخد الرقم الجاي تلقائياً من إعدادات الرسمة.
- بيفتح النقطة للكتابة (`ForWrite`) ويحط لها وصف، مثلاً `MH-1_TR`.
- بيضيف بيانات النقطة لليستة النتيجة، والإحداثيات مقرّبة لـ ٣ أرقام عشرية.

```python
            t.Commit()

    if not result:
        return "No structures found (check the network name)."
    return result
```
- `Commit()` بيحفظ كل النقط في الرسمة.
- لو مفيش ولا نقطة اتعملت، غالباً اسم الـ Network غلط أو الـ Network فاضية.

### 7) المخرجات ومسك الأخطاء
```python
try:
    OUT = main()
except Exception as ex:
    import traceback
    OUT = "Error: {}\n{}".format(ex, traceback.format_exc())
```
- `OUT` هو اللي بيطلع من الـ node.
- لو حصل أي خطأ، بيظهر نصه كامل ومكانه في الكود في الـ Watch node، بدل ما الـ node تقفل بالأحمر من غير ما تعرف السبب.

---

## ملاحظات
- ماتشغلش الكود مرتين وهو على `True`، علشان هيعمل نقط متكررة.
- الـ Structure الدايري بتطلع أركانه على المربع اللي حوالين الدايرة (القطر × القطر).
