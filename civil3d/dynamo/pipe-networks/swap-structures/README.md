# `swap-structures` — Swap لكذا Structure مرة واحدة من Dynamo Player

| | |
|---|---|
| **الاسم المختصر** | `swap-structures` |
| **البرنامج** | Civil 3D + Dynamo Player |
| **المحرك** | CPython3 |
| **الكود (جاهز للنسخ)** | [`swap-structures.txt`](swap-structures.txt) |

ملف الكود فيه الكود بس، من غير أي تعليقات أو شرح، علشان تنسخه كله مرة واحدة.
الشرح والتعليمات كلها في الملف ده.

---

## الكود بيعمل إيه
بدل ما تعمل **Swap Part** لكل Structure لوحدها:
1. من **Dynamo Player** بتدوس **Select**، وتحدد كل الـ Structures اللي عاوز تغيّرها.
2. بتدوس **Run**، فبتطلعلك شاشة صغيرة فيها ٣ قوايم:
   - **Parts List**: كل الـ Parts Lists اللي في الرسمة.
   - **Family**: الـ Structures اللي في الـ Parts List اللي اخترته.
   - **Size**: مقاسات الـ Family اللي اخترتها.
3. بتدوس **Swap**، وكل الـ Structures اللي حددتها بتتغيّر مرة واحدة.
4. بتطلعلك رسالة فيها عدد اللي اتغيّروا، واللي اتخطاهم، واللي فشلوا وسبب الفشل.

### شكل الشاشة
```
┌ Swap Structures ──────────────────────────────────────────────┐
│ Selected structures: 12                                       │
│ Parts List  [ Standard                                    ▼ ] │
│ Family      [ Concentric Cylindrical Structure            ▼ ] │
│ Size        [ Concentric Structure 48 dia 18 frame ...    ▼ ] │
│                                        [ Swap ]  [ Cancel ]   │
└───────────────────────────────────────────────────────────────┘
```
- كل قايمة بتتملا على أساس اختيارك في اللي فوقها.
- قايمة **Parts List** بتفتح لوحدها على الـ Parts List بتاع الـ Network اللي فيها الـ Structures اللي حددتها.
- الـ Parts Lists اللي مفيهاش Structures مش بتظهر في القايمة.

### ليه القوايم في شاشة منفصلة ومش جوه الـ Player نفسه؟
الـ Dynamo Player مايقدرش يعمل قايمة اختيارات (Dropdown) **بتتغيّر حسب اختيارك في قايمة تانية**، ولا يقرا الـ Parts Lists من الرسمة وقت التشغيل. القوايم اللي بيعرضها ثابتة، وبتتحدد وانت بتعمل الجراف.
علشان كده الكود هو اللي بيعمل الشاشة دي بنفسه لما تدوس Run، وبيملا القوايم من الرسمة المفتوحة.

---

## الـ Nodes اللي هتحتاجها
| الـ Node | توصّلها في | تعمل عليها في الـ Player | وظيفتها |
|---|---|---|---|
| **Select Objects** | `IN[0]` | **Is Input** | زرار Select تحدد بيه الـ Structures |
| **Python Script** (CPython3) | — | — | فيها الكود، ومحتاجة **مدخل واحد** بس |
| **Watch** (اختياري) | على مخرج الـ Python | **Is Output** (اختياري) | تشوف فيها التقرير التفصيلي |

> مش محتاج Boolean ولا أي Node تانية. زرار **Run** في الـ Player بيشغّل، وزرار **Swap** في الشاشة هو التأكيد.
> مش محتاج تنزّل أي Package.

```
[Select Objects] ──► IN[0] ─► [Python Script] ──► [Watch]
```

---

## تجهيز الجراف (مرة واحدة بس)
1. افتح Dynamo من Civil 3D، وخلي وضع التشغيل **Manual** (تحت على الشمال). من غيره الشاشة هتطلعلك كل ما تغيّر حاجة في الجراف.
2. ضيف **Select Objects** و **Python Script** و **Watch**، ووصّلهم زي الرسمة اللي فوق.
3. في الـ Python Script: اختار المحرك **CPython3**، والصق الكود من [`swap-structures.txt`](swap-structures.txt).
4. اعمل كليك يمين على **Select Objects**، واختار **Rename** وسمّيها مثلاً «اختار الـ Structures». وبعدين كليك يمين تاني واختار **Is Input**.
5. لو عاوز تشوف التقرير في الـ Player: كليك يمين على الـ **Watch** واختار **Is Output**.
6. احفظ الجراف كملف `.dyn` في فولدر ثابت، مثلاً فولدر للجرافات بتاعتك.

## الاستخدام اليومي من Dynamo Player
1. في Civil 3D: تبويب **Manage** ← **Dynamo Player**.
2. اختار الفولدر اللي فيه الجراف، ودوس على أيقونة التعديل (القلم) جنب اسم الجراف علشان تظهر المداخل.
3. دوس **Select** جنب «اختار الـ Structures»، وحدد الـ Structures في الرسمة، ودوس **Enter**.
4. دوس **Run**.
5. في الشاشة: اختار **Parts List**، وبعده **Family**، وبعده **Size**، ودوس **Swap**.
6. هتطلعلك رسالة بالنتيجة.

> **كل مرة دوس Select وحدد من جديد قبل Run.** ده بيضمن إن الجراف هيتنفّذ من الأول وعلى الـ Structures اللي انت عاوزها. لو دوست Run تاني من غير ما تغيّر الاختيار والشاشة مطلعتش، حدد تاني ودوس Run.

---

## المدخلات
| المدخل | النوع | الوصف |
|---|---|---|
| `IN[0]` | Objects | الـ Structures اللي عاوز تغيّرها. لو فيه Pipes أو أي حاجة تانية في الاختيار، بتتخطاها لوحدها |

## المخرجات
- **رسالة** بعد التنفيذ: الـ Part الجديد، وعدد اللي اتغيّروا، وعدد اللي اتخطاهم، وعدد اللي فشلوا، ولحد ١٠ أسباب فشل.
- **`OUT`** (في الـ Watch): نفس الملخص، وبعده تقرير لكل Structure: `[الاسم، الـ Part القديم، الحالة]`.

### الحالات في التقرير
| الحالة | معناها |
|---|---|
| `Swapped` | اتغيّرت |
| `Skipped: already the same part` | هي أصلاً نفس الـ Part اللي اخترته |
| `Failed: <رسالة خطأ>` | Civil 3D رفض التغيير. مثلاً الـ Layer مقفول، أو الـ Structure جاية من Data Shortcut |

### رسايل ممكن تطلعلك
| الرسالة | معناها |
|---|---|
| `Select the structures first (Select Objects).` | مفيش حاجة متحددة. دوس Select وحدد |
| `No structures in the selection.` | اللي حددته مفيهوش ولا Structure |
| `No parts list with structures in this drawing.` | مفيش ولا Parts List في الرسمة فيه Structures |
| `Cancelled.` | دوست Cancel، ومفيش حاجة اتغيّرت |

---

## ملاحظات
- الكود بيعمل نفس اللي بيعمله أمر **Swap Part**: بيغيّر الـ Family والـ Size بس. الـ Style والـ Labels ومكان الـ Structure مش بيتغيّروا.
- **الأفضل تختار نفس الـ Parts List اللي الـ Network شغالة بيه**، وده بيبقى مختار لوحده في الشاشة. لو اخترت Parts List تاني وCivil 3D رفض، هيظهرلك السبب في الرسالة.
- الـ Structures الجاية من **Data Shortcut** مش هتتغيّر من الرسمة دي. لازم تغيّرها في الرسمة الأصلية.
- لو شغّلته مرتين على نفس الـ Structures مفيش مشكلة. المرة التانية هيلاقيهم بقوا نفس الـ Part ويتخطاهم.
- النسخة القديمة اللي كانت بتاخد Structure مرجع محفوظة في تاريخ الـ commits على GitHub لو احتجتها.

---

## شرح الكود حتة حتة

### 1) تحميل المكتبات
```python
import clr

clr.AddReference('AcMgd')
clr.AddReference('AcCoreMgd')
clr.AddReference('AcDbMgd')
clr.AddReference('AecBaseMgd')
clr.AddReference('AeccDbMgd')
clr.AddReference('System.Windows.Forms')
clr.AddReference('System.Drawing')
```
- أول ٥ سطور هي مكتبات AutoCAD و Civil 3D، زي `corners-cogo`.
- **`System.Windows.Forms`**: مكتبة Windows اللي بنعمل بيها الشاشة (Form) والقوايم (ComboBox) والزراير (Button).
- **`System.Drawing`**: بنستخدم منها `Point` (مكان العنصر في الشاشة) و `Size` (مقاسه).

```python
from Autodesk.AutoCAD.ApplicationServices import Application
from Autodesk.AutoCAD.DatabaseServices import OpenMode, ObjectId
from Autodesk.Civil import DomainType
from Autodesk.Civil.ApplicationServices import CivilApplication
from Autodesk.Civil.DatabaseServices import Structure
from System.Drawing import Point, Size
from System.Windows.Forms import (Form, Label, ComboBox, Button, MessageBox,
                                  ComboBoxStyle, DialogResult, FormBorderStyle,
                                  FormStartPosition, MessageBoxButtons, MessageBoxIcon)
```
- `CivilApplication`: منها بنوصل للـ Parts Lists اللي في الرسمة.
- `DomainType`: علشان نطلب من الـ Parts List الـ Families بتاعة الـ **Structures** بس، من غير الـ Pipes.
- `Structure`: علشان نتأكد إن العنصر اللي اتحدد Structure فعلاً.
- من `System.Windows.Forms`: `Form` الشاشة، و`Label` النص، و`ComboBox` القايمة، و`Button` الزرار، و`MessageBox` رسالة النتيجة. والباقي إعدادات شكلها.

### 2) المدخلات
```python
TITLE = "Swap Structures"
selection_in = IN[0] if len(IN) > 0 else None
```
- `TITLE`: عنوان الشاشة والرسايل.
- `selection_in`: اللي جاي من **Select Objects**.

### 3) تحويل اللي جاي من Dynamo لـ ObjectIds
```python
def flatten(items):
    if items is None:
        return []
    if not isinstance(items, (list, tuple)):
        return [items]
    flat = []
    for item in items:
        flat.extend(flatten(item))
    return flat
```
بتحوّل المدخل لليستة واحدة مسطّحة، سواء جالها عنصر واحد أو ليستة جواها ليستات.

```python
def to_object_id(item):
    if isinstance(item, ObjectId):
        return item
    if hasattr(item, "InternalObjectId"):
        return item.InternalObjectId
    return None
```
الـ Select Objects مش بتطلع الـ Structure نفسها. بتطلع "غلاف" (Wrapper) بتاع Dynamo حواليها، والغلاف ده جواه `InternalObjectId`، وهو رقم العنصر الحقيقي في الرسمة.

```python
def get_ids(items):
    ids = []
    for item in flatten(items):
        oid = to_object_id(item)
        if oid is not None and not oid.IsNull and not oid.IsErased:
            ids.append(oid)
    return ids
```
بتجمع الأرقام دي، وبتشيل أي عنصر فاضي (`IsNull`) أو اتمسح من الرسمة (`IsErased`).

### 4) فرز الـ Structures من باقي الاختيار
```python
def read_structures(t, ids):
    structures = []
    others = 0
    for oid in ids:
        if isinstance(t.GetObject(oid, OpenMode.ForRead), Structure):
            structures.append(oid)
        else:
            others += 1
    return structures, others
```
بتفتح كل عنصر اتحدد. لو Structure بتحطه في الليستة، ولو أي حاجة تانية بتعدّه بس، علشان الشاشة تقولك «other objects ignored».

### 5) قراية الـ Parts Lists من الرسمة
```python
def read_catalog(t, civdoc):
    catalog = []
    for list_id in civdoc.Styles.PartsListSet:
        parts_list = t.GetObject(list_id, OpenMode.ForRead)
        families = []
        for family_id in parts_list.GetPartFamilyIdsByDomain(DomainType.Structure):
            family = t.GetObject(family_id, OpenMode.ForRead)
            sizes = []
            for i in range(family.PartSizeCount):
                size_id = family[i]
                sizes.append((t.GetObject(size_id, OpenMode.ForRead).Name, size_id))
            if sizes:
                families.append((family.Name, family_id, sizes))
        if families:
            catalog.append((parts_list.Name, str(list_id), families))
    return catalog
```
دي الحتة اللي بتملا القوايم الثلاثة. بتلف على ٣ مستويات:
1. **`civdoc.Styles.PartsListSet`**: كل الـ Parts Lists اللي في الرسمة.
2. **`GetPartFamilyIdsByDomain(DomainType.Structure)`**: الـ Families بتاعة الـ Structures جوه كل Parts List.
3. **`family.PartSizeCount`** و **`family[i]`**: عدد المقاسات جوه كل Family، والمقاس رقم `i`.

النتيجة `catalog`، وهي ليستة متداخلة بالشكل ده:
```
[ (اسم الـ Parts List، رقمه، [ (اسم الـ Family، رقمها، [ (اسم المقاس، رقمه), ... ]), ... ]), ... ]
```
- بنحفظ **الاسم** علشان نعرضه في القايمة، و**الـ ObjectId** علشان أمر الـ Swap بياخده هو مش الاسم.
- الـ Family اللي مفيهاش مقاسات، والـ Parts List اللي مفيهوش Structures، مش بيتضافوا، فمش هيظهروا في القوايم.

### 6) اختيار الـ Parts List الافتراضي
```python
def default_list_index(t, catalog, structure_id):
    st = t.GetObject(structure_id, OpenMode.ForRead)
    network = t.GetObject(st.NetworkId, OpenMode.ForRead)
    key = str(network.PartsListId)
    for i, item in enumerate(catalog):
        if item[1] == key:
            return i
    return 0
```
بتفتح أول Structure اتحددت، وتجيب الـ Network بتاعتها، وتاخد منها الـ Parts List اللي شغالة بيه (`PartsListId`). بعدين بتدوّر عليه في `catalog` وترجّع ترتيبه، علشان القايمة الأولى تفتح عليه لوحدها. ولو ملقتهوش بترجّع أول واحد.

### 7) بناء الشاشة
```python
def add_row(form, text, top):
    label = Label()
    label.Text = text
    label.Location = Point(15, top + 4)
    label.Size = Size(100, 20)
    combo = ComboBox()
    combo.DropDownStyle = ComboBoxStyle.DropDownList
    combo.Location = Point(120, top)
    combo.Size = Size(500, 24)
    form.Controls.Add(label)
    form.Controls.Add(combo)
    return combo
```
بتعمل سطر في الشاشة: نص على الشمال (Label)، وجنبه قايمة (ComboBox).
- `top`: بُعد السطر عن أول الشاشة من فوق، وكل سطر بنديله رقم مختلف علشان ييجوا تحت بعض.
- `DropDownList`: معناها إنك تختار من القايمة بس، ومتقدرش تكتب فيها.

```python
def add_button(form, text, left, result):
    button = Button()
    button.Text = text
    button.Location = Point(left, 160)
    button.Size = Size(85, 28)
    button.DialogResult = result
    form.Controls.Add(button)
    return button
```
بتعمل زرار. `DialogResult` هي اللي بتقفل الشاشة لما تدوس على الزرار، وبتقول اتقفلت إزاي: `OK` لو دوست Swap، و`Cancel` لو دوست Cancel.

```python
def fill(combo, names):
    combo.BeginUpdate()
    combo.Items.Clear()
    for name in names:
        combo.Items.Add(name)
    combo.EndUpdate()
    if combo.Items.Count > 0:
        combo.SelectedIndex = 0
```
بتفضّي القايمة، وتملاها بالأسماء الجديدة، وتختار أول اسم. `BeginUpdate`/`EndUpdate` بتخلي القايمة ماترسمش نفسها غير مرة واحدة في الآخر، فتبقى أسرع لو الأسماء كتير.

### 8) الشاشة نفسها والقوايم المتسلسلة
```python
def show_form(catalog, count, others, default_index):
    form = Form()
    form.Text = TITLE
    form.ClientSize = Size(640, 205)
    form.StartPosition = FormStartPosition.CenterScreen
    form.FormBorderStyle = FormBorderStyle.FixedDialog
    form.MaximizeBox = False
    form.MinimizeBox = False
    form.TopMost = True
```
بتعمل الشاشة: مقاسها ٦٤٠×٢٠٥، وبتظهر في نص الشاشة، ومقاسها ثابت ومن غير زراير تكبير وتصغير. و`TopMost` بتخليها تظهر فوق Civil 3D وماتستخباش وراه.

```python
    info = Label()
    info.Text = "Selected structures: {}".format(count)
    if others:
        info.Text += "   (other objects ignored: {})".format(others)
    info.Location = Point(15, 15)
    info.Size = Size(605, 20)
    form.Controls.Add(info)
```
سطر فوق بيقولك كام Structure هتتغيّر، علشان تتأكد قبل ما تدوس Swap.

```python
    cmb_list = add_row(form, "Parts List", 45)
    cmb_family = add_row(form, "Family", 80)
    cmb_size = add_row(form, "Size", 115)
    btn_ok = add_button(form, "Swap", 440, DialogResult.OK)
    btn_cancel = add_button(form, "Cancel", 535, DialogResult.Cancel)
    form.AcceptButton = btn_ok
    form.CancelButton = btn_cancel
    btn_ok.Enabled = False
```
- بتعمل القوايم الثلاثة تحت بعض، وزرارين Swap و Cancel.
- `AcceptButton`: زرار **Enter** بيدوس Swap، و`CancelButton`: زرار **Esc** بيدوس Cancel.
- زرار Swap بيبدأ مقفول، ومش بيتفتح غير لما يبقى فيه مقاس متختار.

```python
    def families():
        i = cmb_list.SelectedIndex
        return catalog[i][2] if i >= 0 else []

    def sizes():
        i = cmb_family.SelectedIndex
        return families()[i][2] if i >= 0 else []
```
دالتين صغيرين بيرجّعوا:
- `families()`: الـ Families بتاعة الـ Parts List المختار دلوقتي.
- `sizes()`: المقاسات بتاعة الـ Family المختارة دلوقتي.

`SelectedIndex` هو ترتيب الاختيار في القايمة. ولو مفيش اختيار بيبقى `-1`، وساعتها بيرجّعوا ليستة فاضية.

```python
    def on_list_changed(sender, e):
        fill(cmb_family, [f[0] for f in families()])

    def on_family_changed(sender, e):
        fill(cmb_size, [s[0] for s in sizes()])

    def on_size_changed(sender, e):
        btn_ok.Enabled = cmb_size.SelectedIndex >= 0

    cmb_list.SelectedIndexChanged += on_list_changed
    cmb_family.SelectedIndexChanged += on_family_changed
    cmb_size.SelectedIndexChanged += on_size_changed
```
دي فكرة **القوايم المتسلسلة**:
- `SelectedIndexChanged` حدث (Event) بيحصل كل ما الاختيار في القايمة يتغيّر، و`+=` معناها «لما الحدث ده يحصل شغّل الدالة دي».
- تغيّر الـ **Parts List** ← `on_list_changed` تملا قايمة الـ **Family** من جديد.
- لما قايمة الـ Family تتملا وأول اختيار يتحدد ← `on_family_changed` تملا قايمة الـ **Size**.
- لما المقاس يتحدد ← `on_size_changed` تفتح زرار **Swap**.

يعني تغيير واحد فوق بيعدّي على القوايم اللي تحته كلها لوحده.

```python
    fill(cmb_list, [p[0] for p in catalog])
    cmb_list.SelectedIndex = default_index
```
بتملا قايمة الـ Parts List، وبتختار الـ Parts List بتاع الـ Network (من الخطوة ٦). وده لوحده بيملا قايمة الـ Family وقايمة الـ Size بالتسلسل اللي فوق.

```python
    if form.ShowDialog() == DialogResult.OK:
        family = families()[cmb_family.SelectedIndex]
        size = family[2][cmb_size.SelectedIndex]
        return family[0], family[1], size[0], size[1]
    return None
```
- `ShowDialog()` بتعرض الشاشة، والكود بيستنى لحد ما تقفلها.
- لو دوست **Swap**: بترجّع اسم الـ Family ورقمها، واسم المقاس ورقمه.
- لو دوست **Cancel** أو قفلت الشاشة: بترجّع `None`.

### 9) الـ Swap
```python
def swap_all(t, structure_ids, family_name, family_id, size_name, size_id):
    report = []
    counts = {"swap": 0, "skip": 0, "fail": 0}
    for oid in structure_ids:
        st = t.GetObject(oid, OpenMode.ForRead)
        old_part = "{} / {}".format(st.PartFamilyName, st.PartSizeName)
        if st.PartFamilyName == family_name and st.PartSizeName == size_name:
            counts["skip"] += 1
            report.append([st.Name, old_part, "Skipped: already the same part"])
            continue
```
بتلف على الـ Structures، وتحفظ الـ Part القديم لكل واحدة علشان التقرير. ولو الـ Structure أصلاً نفس الـ Part اللي اخترته بتتخطاها.

```python
        try:
            st.UpgradeOpen()
            st.SwapPartFamilyAndSize(family_id, size_id)
            counts["swap"] += 1
            report.append([st.Name, old_part, "Swapped"])
        except Exception as ex:
            counts["fail"] += 1
            report.append([st.Name, old_part, "Failed: {}".format(ex)])
    return counts, report
```
- `UpgradeOpen()`: الـ Structure كانت مفتوحة للقراءة بس، فده بيحوّلها للكتابة.
- **`SwapPartFamilyAndSize(family_id, size_id)`**: ده الأمر اللي بيعمل الـ Swap نفسه، وهو نفس أمر **Swap Part** في Civil 3D.
- الـ `try` معناها إن لو Structure واحدة فشلت، الكود بيكتب السبب ويكمّل على الباقي.

### 10) رسالة النتيجة
```python
def finish(text):
    MessageBox.Show(text, TITLE, MessageBoxButtons.OK, MessageBoxIcon.Information)
    return text
```
بتعرض رسالة فيها زرار OK، وبترجّع نفس النص علشان يطلع في `OUT` كمان. الرسالة دي مهمة في الـ Player، علشان من غيرها مش هتعرف إيه اللي حصل.

### 11) التنفيذ الأساسي
```python
def main():
    ids = get_ids(selection_in)
    if not ids:
        return finish("Select the structures first (Select Objects).")

    adoc = Application.DocumentManager.MdiActiveDocument
    db = adoc.Database
    civdoc = CivilApplication.ActiveDocument
```
لو مفيش حاجة متحددة بيقولك وبيقف. وبعدين بيجهّز الرسمة المفتوحة (`adoc`) وقاعدة بياناتها (`db`) والـ Civil Document (`civdoc`).

```python
    with adoc.LockDocument():
        with db.TransactionManager.StartTransaction() as t:
            structure_ids, others = read_structures(t, ids)
            catalog = read_catalog(t, civdoc)
            default_index = 0
            if structure_ids and catalog:
                default_index = default_list_index(t, catalog, structure_ids[0])
            t.Commit()

    if not structure_ids:
        return finish("No structures in the selection.")
    if not catalog:
        return finish("No parts list with structures in this drawing.")
```
**مرحلة القراية:** بيفرز الـ Structures، ويقرا الـ Parts Lists، ويحدد الـ Parts List الافتراضي. وبعدين بيقفل الـ Transaction **قبل** ما يعرض الشاشة، علشان الرسمة ماتفضلش مقفولة وانت بتختار.

```python
    choice = show_form(catalog, len(structure_ids), others, default_index)
    if choice is None:
        return "Cancelled."
    family_name, family_id, size_name, size_id = choice
```
بيعرض الشاشة. لو دوست Cancel بيقف ومفيش حاجة بتتغيّر.

```python
    with adoc.LockDocument():
        with db.TransactionManager.StartTransaction() as t:
            counts, report = swap_all(t, structure_ids, family_name, family_id, size_name, size_id)
            t.Commit()
```
**مرحلة التنفيذ:** Transaction جديدة بتعمل الـ Swap لكله، و`Commit()` بيحفظ التغييرات في الرسمة مرة واحدة.

```python
    summary = "New part: {} / {}\nSwapped: {}\nSkipped (already the same): {}\nFailed: {}".format(
        family_name, size_name, counts["swap"], counts["skip"], counts["fail"])
    failed = [r for r in report if r[2].startswith("Failed")]
    if failed:
        summary += "\n\n" + "\n".join("{}: {}".format(r[0], r[2]) for r in failed[:10])
    finish(summary)
    return [summary, report]
```
بيعمل الملخص. ولو فيه Structures فشلت بيضيف أسماءها وسبب الفشل (لحد ١٠ بس، علشان الرسالة ماتطولش). بعدين بيعرض الرسالة، ويرجّع الملخص والتقرير الكامل لـ `OUT`.

### 12) مسك الأخطاء
```python
try:
    OUT = main()
except Exception as ex:
    import traceback
    OUT = "Error: {}\n{}".format(ex, traceback.format_exc())
```
لو حصل خطأ عام، نص الخطأ بيظهر كامل في الـ Watch بدل ما الـ node تقفل بالأحمر من غير ما تعرف السبب.
