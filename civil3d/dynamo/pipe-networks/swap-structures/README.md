# `swap-structures` — Swap لكذا Structure مرة واحدة

| | |
|---|---|
| **الاسم المختصر** | `swap-structures` |
| **البرنامج** | Civil 3D + Dynamo |
| **المحرك** | CPython3 |
| **الكود (جاهز للنسخ)** | [`swap-structures.txt`](swap-structures.txt) |

ملف الكود فيه الكود بس، من غير أي تعليقات أو شرح، علشان تنسخه كله مرة واحدة.
الشرح والتعليمات كلها في الملف ده.

---

## الكود بيعمل إيه
بدل ما تعمل **Swap Part** لكل Structure لوحدها:
1. بتحدد كل الـ Structures اللي عاوز تغيّرها مرة واحدة.
2. بتختار Structure **مرجع**، وهي اللي عاوز الباقي يبقى زيها.
3. الكود بيغيّر كل الـ Structures اللي حددتها لنفس الـ **Part Family** ونفس الـ **Part Size** بتوع المرجع، مرة واحدة.

وفيه **وضع معاينة**: قبل ما يغيّر أي حاجة، بيوريك هيغيّر إيه بالظبط.

---

## الـ Nodes اللي هتحتاجها
| الـ Node | تدوّر عليها في الـ Library باسم | توصّلها في | وظيفتها |
|---|---|---|---|
| **Select Objects** | `Select Objects` | `IN[0]` | تحدد بيها الـ Structures اللي عاوز تغيّرها (كذا واحدة) |
| **Select Object** | `Select Object` | `IN[1]` | تختار بيها الـ Structure المرجع (واحدة بس) |
| **Boolean** | `Boolean` | `IN[2]` | `False` معاينة · `True` تنفيذ |
| **Python Script** | `Python Script` | — | فيها الكود |
| **Watch** | `Watch` | على مخرج الـ Python | تشوف فيها النتيجة |

> لو حددت بالغلط Pipes أو أي حاجة تانية مع الـ Structures، مش مشكلة. الكود بيتخطاها لوحده.

### شكل التوصيل
```
[Select Objects] ──► IN[0] ┐
[Select Object ] ──► IN[1] ├─► [Python Script] ──► [Watch]
[Boolean       ] ──► IN[2] ┘
```

---

## طريقة الاستخدام
1. في Dynamo، خلي وضع التشغيل **Manual** (تحت على الشمال). كده الجراف مش هيشتغل لوحده وانت بتحدد.
2. ضيف **Python Script** node، واختار المحرك **CPython3**، والصق الكود من [`swap-structures.txt`](swap-structures.txt).
3. كبّر الـ node لحد ما يبقى فيها **٣ مداخل** (بزرار `+`).
4. ضيف باقي الـ Nodes اللي في الجدول ووصّلها زي الرسمة اللي فوق.
5. في **Select Objects** دوس **Select**، وحدد الـ Structures في الرسمة، ودوس **Enter**.
6. في **Select Object** دوس **Select**، واختار الـ Structure المرجع.
7. خلي الـ **Boolean** على `False`، ودوس **Run**، وراجع المعاينة في الـ Watch.
8. لو المعاينة تمام، خلي الـ **Boolean** على `True` ودوس **Run**، والـ Structures هتتغيّر مرة واحدة.
9. رجّع الـ **Boolean** لـ `False`.

### للاستخدام اليومي: Dynamo Player
علشان تشغّله بعد كده من غير ما تفتح Dynamo:
1. احفظ الجراف كملف `.dyn`.
2. اعمل كليك يمين على **Select Objects** و **Select Object** و **Boolean**، واختار **Is Input**. ممكن كمان تغيّر اسم كل واحدة (Rename) لاسم واضح، مثلاً «الـ Structures» و«المرجع» و«نفّذ».
3. من Civil 3D: تبويب **Manage** ← **Dynamo Player**، واختار الجراف. هتلاقي المداخل الثلاثة قدامك، فتحدد وتشغّل على طول.

---

## المدخلات
| المدخل | النوع | الوصف |
|---|---|---|
| `IN[0]` | Objects | الـ Structures اللي عاوز تغيّرها |
| `IN[1]` | Object | الـ Structure المرجع |
| `IN[2]` | Boolean | `False` معاينة بس · `True` تنفيذ التغيير |

## المخرجات
ليستة فيها حاجتين:
1. **ملخص** في سطر واحد: الـ Part الجديد، وعدد اللي اتغيّروا (أو هيتغيّروا)، وعدد اللي اتخطاهم، وعدد اللي فشلوا.
2. **تقرير** لكل عنصر: `[اسم الـ Structure، الـ Part القديم، الحالة]`.

### الحالات
| الحالة | معناها |
|---|---|
| `Will swap` | معاينة: هتتغيّر لما تخلي `IN[2] = True` |
| `Swapped` | اتغيّرت خلاص |
| `Skipped: already the same part` | هي أصلاً نفس الـ Part بتاع المرجع، أو هي المرجع نفسه |
| `Skipped: not a structure` | العنصر ده مش Structure، زي Pipe مثلاً |
| `Failed: reference family not in parts list ...` | الـ Network بتاعة الـ Structure دي بتستخدم Parts List مفيهوش الـ Family بتاعة المرجع |
| `Failed: reference size not in parts list ...` | الـ Family موجودة، بس المقاس بتاع المرجع مش موجود في الـ Parts List بتاع الـ Network دي |
| `Failed: <رسالة خطأ>` | Civil 3D رفض التغيير. مثلاً الـ Layer مقفول، أو الـ Structure جاية من Data Shortcut |

---

## ملاحظات
- الكود بيعمل نفس اللي بيعمله أمر **Swap Part**: بيغيّر الـ Family والـ Size بس. الـ Style والـ Labels ومكان الـ Structure مش بيتغيّروا.
- الكود بيدوّر على الـ Family والـ Size بتوع المرجع **في الـ Parts List بتاع الـ Network بتاعة كل Structure**، زي ما شاشة Swap Part بتعمل. علشان كده ممكن تغيّر Structures في Networks مختلفة مرة واحدة، بشرط إن الـ Parts List بتاعها فيه الـ Part ده.
- الـ Structures الجاية من **Data Shortcut** مش هتتغيّر من الرسمة دي. لازم تغيّرها في الرسمة الأصلية.
- لو شغّلته على `True` مرتين مفيش مشكلة. المرة التانية هيلاقيهم بقوا زي المرجع ويتخطاهم.

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

from Autodesk.AutoCAD.ApplicationServices import Application
from Autodesk.AutoCAD.DatabaseServices import OpenMode, ObjectId
from Autodesk.Civil import DomainType
from Autodesk.Civil.DatabaseServices import Structure
```
نفس فكرة `corners-cogo`: `clr` بتحمّل مكتبات AutoCAD و Civil 3D. والجديد هنا:
- `ObjectId`: رقم العنصر جوه الرسمة، وبيه بنفتح أي عنصر.
- `DomainType`: بنستخدمه علشان نطلب من الـ Parts List الـ Families بتاعة الـ **Structures** بس، من غير الـ Pipes.
- `Structure`: بنستخدمه علشان نتأكد إن العنصر اللي اتحدد Structure فعلاً.

### 2) المدخلات
```python
targets_in = IN[0] if len(IN) > 0 else None
reference_in = IN[1] if len(IN) > 1 else None
run = IN[2] if len(IN) > 2 else False
```
- `targets_in`: الـ Structures اللي جاية من **Select Objects**.
- `reference_in`: المرجع اللي جاي من **Select Object**.
- `run`: لو `False` الكود بيعمل معاينة بس، ولو `True` بينفّذ.

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
`flatten` بتحوّل أي مدخل لليستة واحدة مسطّحة:
- لو المدخل عنصر واحد (زي Select Object) بتحطه في ليستة.
- لو ليستة جواها ليستات بتفكها كلها.
كده الكود بيشتغل سواء وصّلت عنصر واحد أو ليستة.

```python
def to_object_id(item):
    if isinstance(item, ObjectId):
        return item
    if hasattr(item, "InternalObjectId"):
        return item.InternalObjectId
    return None
```
الـ Select nodes مش بتطلع الـ Structure نفسها. بتطلع "غلاف" (Wrapper) بتاع Dynamo حواليها، والغلاف ده جواه `InternalObjectId`، وهو الـ ObjectId الحقيقي في الرسمة.

```python
def get_ids(items):
    ids = []
    for item in flatten(items):
        oid = to_object_id(item)
        if oid is not None and not oid.IsNull and not oid.IsErased:
            ids.append(oid)
    return ids
```
بتجمع الـ ObjectIds، وبتشيل أي عنصر فاضي (`IsNull`) أو اتمسح من الرسمة (`IsErased`). ده بيحصل لو مسحت Structure بعد ما حددتها في Dynamo.

### 4) البحث عن الـ Part في الـ Parts List
```python
def find_part_ids(t, parts_list_id, family_name, size_name):
    parts_list = t.GetObject(parts_list_id, OpenMode.ForRead)
    for family_id in parts_list.GetPartFamilyIdsByDomain(DomainType.Structure):
        family = t.GetObject(family_id, OpenMode.ForRead)
        if family.Name != family_name:
            continue
        for i in range(family.PartSizeCount):
            size_id = family[i]
            if t.GetObject(size_id, OpenMode.ForRead).Name == size_name:
                return family_id, size_id
        return family_id, None
    return None, None
```
أمر الـ Swap في الـ API مش بياخد أسماء. بياخد **ObjectId للـ Family** و **ObjectId للـ Size**، فالدالة دي بتجيبهم:
1. بتفتح الـ Parts List.
2. `GetPartFamilyIdsByDomain(DomainType.Structure)` بترجّع كل الـ Families بتاعة الـ Structures اللي فيه.
3. بتدوّر على الـ Family اللي اسمها نفس اسم Family المرجع.
4. جوه الـ Family بتلف على المقاسات (`PartSizeCount` عددهم، و `family[i]` بتجيب المقاس رقم `i`)، وبتدوّر على المقاس اللي اسمه نفس مقاس المرجع.
5. بترجّع الاتنين. ولو ملقتش حاجة بترجّع `None`، والكود بيكتب في التقرير إيه اللي ناقص.

### 5) التجهيز
```python
def main():
    target_ids = get_ids(targets_in)
    ref_ids = get_ids(reference_in)
    if not target_ids:
        return "Connect the structures to swap (Select Objects) to IN[0]."
    if not ref_ids:
        return "Connect the reference structure (Select Object) to IN[1]."
```
لو نسيت توصّل حاجة أو محددتش حاجة، بيقولك تعمل إيه ويقف.

```python
    adoc = Application.DocumentManager.MdiActiveDocument
    db = adoc.Database
    report = []
    counts = {"swap": 0, "skip": 0, "fail": 0}
```
- `adoc` و `db`: الرسمة المفتوحة وقاعدة بياناتها.
- `report`: التقرير اللي هيطلع في الـ Watch.
- `counts`: عدّادات للملخص.

### 6) قراءة المرجع
```python
    with adoc.LockDocument():
        with db.TransactionManager.StartTransaction() as t:
            ref = t.GetObject(ref_ids[0], OpenMode.ForRead)
            if not isinstance(ref, Structure):
                return "IN[1] is not a structure."

            family_name = ref.PartFamilyName
            size_name = ref.PartSizeName
            new_part = "{} / {}".format(family_name, size_name)
            cache = {}
```
- `LockDocument` و `Transaction`: نفس فكرة `corners-cogo`، علشان نقدر نعدّل في الرسمة.
- بيفتح المرجع، ويتأكد إنه Structure.
- بياخد منه **اسم الـ Family** (`PartFamilyName`) و**اسم المقاس** (`PartSizeName`).
- `cache`: بيحفظ نتيجة البحث لكل Parts List. لو عندك ١٠٠ Structure في نفس الـ Network، البحث بيحصل مرة واحدة بس.

### 7) اللف على الـ Structures
```python
            for oid in target_ids:
                st = t.GetObject(oid, OpenMode.ForRead)
                if not isinstance(st, Structure):
                    counts["skip"] += 1
                    report.append([st.GetType().Name, "-", "Skipped: not a structure"])
                    continue
```
بيفتح كل عنصر. لو مش Structure، بيكتب نوعه في التقرير (زي `Pipe`) ويتخطاه.

```python
                old_part = "{} / {}".format(st.PartFamilyName, st.PartSizeName)
                if st.PartFamilyName == family_name and st.PartSizeName == size_name:
                    counts["skip"] += 1
                    report.append([st.Name, old_part, "Skipped: already the same part"])
                    continue
```
بيحفظ الـ Part القديم علشان التقرير. ولو الـ Structure أصلاً زي المرجع، بيتخطاها.

```python
                net = t.GetObject(st.NetworkId, OpenMode.ForRead)
                key = str(net.PartsListId)
                if key not in cache:
                    cache[key] = find_part_ids(t, net.PartsListId, family_name, size_name)
                family_id, size_id = cache[key]
```
- بيفتح الـ Network بتاعة الـ Structure، وياخد منها الـ Parts List بتاعها (`PartsListId`).
- بيدوّر على Part المرجع جوه الـ Parts List ده، ولو دوّر فيه قبل كده بياخد النتيجة من الـ `cache`.

```python
                if family_id is None or size_id is None:
                    counts["fail"] += 1
                    missing = "family" if family_id is None else "size"
                    report.append([st.Name, old_part,
                                   "Failed: reference {} not in parts list of network '{}'".format(missing, net.Name)])
                    continue
```
لو الـ Family أو المقاس مش موجودين في الـ Parts List، بيكتب في التقرير مين الناقص وفي أنهي Network.

### 8) المعاينة أو التنفيذ
```python
                if not run:
                    counts["swap"] += 1
                    report.append([st.Name, old_part, "Will swap"])
                    continue
```
لو `IN[2] = False`: بيكتب «Will swap» من غير ما يغيّر أي حاجة.

```python
                try:
                    st.UpgradeOpen()
                    st.SwapPartFamilyAndSize(family_id, size_id)
                    counts["swap"] += 1
                    report.append([st.Name, old_part, "Swapped"])
                except Exception as ex:
                    counts["fail"] += 1
                    report.append([st.Name, old_part, "Failed: {}".format(ex)])
```
لو `IN[2] = True`:
- `UpgradeOpen()`: الـ Structure كانت مفتوحة للقراءة بس، فده بيحوّلها للكتابة.
- **`SwapPartFamilyAndSize(family_id, size_id)`**: ده الأمر اللي بيعمل الـ Swap نفسه، وهو نفس أمر **Swap Part** في Civil 3D.
- الـ `try` معناها إن لو Structure واحدة فشلت (مثلاً على Layer مقفول)، الكود بيكتب السبب ويكمّل على الباقي، ومش بيوقف كله.

```python
            t.Commit()
```
بيحفظ كل التغييرات في الرسمة مرة واحدة.

### 9) الملخص والمخرجات
```python
    mode = "Swapped" if run else "Will swap (preview - set IN[2] = True to apply)"
    summary = "Target part: {} | {}: {} | Skipped: {} | Failed: {}".format(
        new_part, mode, counts["swap"], counts["skip"], counts["fail"])
    return [summary, report]


try:
    OUT = main()
except Exception as ex:
    import traceback
    OUT = "Error: {}\n{}".format(ex, traceback.format_exc())
```
- بيطلع سطر ملخص، وبعده التقرير التفصيلي.
- لو حصل خطأ عام، نص الخطأ بيظهر كامل في الـ Watch.
