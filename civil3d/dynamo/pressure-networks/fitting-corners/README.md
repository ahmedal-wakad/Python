# `fitting-corners` — أركان الـ Pressure Fitting → COGO Points

| | |
|---|---|
| **الاسم المختصر** | `fitting-corners` |
| **البرنامج** | Civil 3D 2022 أو أحدث + Dynamo |
| **المحرك** | CPython3 |
| **الكود (جاهز للنسخ)** | [`fitting-corners.txt`](fitting-corners.txt) |

ملف الكود فيه الكود بس، من غير أي تعليقات أو شرح، علشان تنسخه كله مرة واحدة.
الشرح والتعليمات كلها في الملف ده.

---

## الكود بيعمل إيه
ده أخو [`corners-cogo`](../../pipe-networks/corners-cogo/)، بس للـ Catches (أو أي موديل) اللي متعرّفة كـ **Fitting** جوه **Pressure Network**، مش كـ Structure.

1. بيقرا الـ Fittings من الـ Pressure Network، أو من اللي تحدده بإيدك في الرسمة.
2. بياخد منها اللي اسمها فيه الكلمة اللي تكتبها (مثلاً `catch`)، علشان الـ Elbows والـ Tees مايدخلوش في الحساب.
3. بيحسب الأربع أركان من **المجسم الـ 3D بتاع الـ Fitting نفسه**، حتى لو الـ Fitting مايل.
4. بيعمل COGO Point على كل ركن، ووصفه بالشكل ده: `اسم_الـFitting_الركن`، مثلاً `CB-1_TR`.

### ليه مش نفس طريقة `corners-cogo`؟
الـ Structure ليها خصائص جاهزة: طول وعرض وزاوية دوران، و`corners-cogo` بيحسب منها الأركان.
الـ Fitting ملوش في الـ API خصائص للطول والعرض والدوران. علشان كده الكود هنا بياخد المجسم الـ 3D بتاع الـ Fitting، وبيحسب **أصغر مستطيل يحتويه في المسقط الأفقي**.
لو الموديل بتاعك صندوق، المستطيل ده هو أركانه بالظبط، ومش مهم الموديل اتعمل إزاي ولا مقاساته متسجلة فين.

---

## طريقة الاستخدام
1. افتح الرسمة في Civil 3D، وبعدها افتح Dynamo، وخلي وضع التشغيل **Manual**.
2. ضيف **Python Script** node، واختار المحرك **CPython3**.
3. افتح ملف [`fitting-corners.txt`](fitting-corners.txt) على GitHub، ودوس **Copy raw file**، والصق الكود جوه الـ node.
4. كبّر الـ node لحد ما يبقى فيها **٤ مداخل** (بزرار `+`)، ووصّل فيها اللي في الجدول اللي تحت. **لازم الأربعة يبقوا متوصلين**، حتى لو String فاضي.
5. وصّل **Watch** node على المخرج.
6. خلي `IN[1]` على `False` ودوس **Run**. دي **معاينة**: هتشوف الـ Fittings اللي هتتحسب ومقاس كل واحد وزاويته، من غير ما أي نقطة تتعمل.
7. راجع المعاينة: المقاس اللي طالع لكل Catch لازم يبقى نفس مقاسه الحقيقي.
8. لو تمام، خلي `IN[1]` على `True` ودوس **Run**، والنقط هتتعمل.
9. **رجّع `IN[1]` لـ `False`**، علشان النقط ماتتكررش لو الجراف اشتغل تاني.

## المدخلات
| المدخل | الـ Node اللي توصّلها | القيمة |
|---|---|---|
| `IN[0]` | **String** أو **Select Objects** | بيحدد أنهي Fittings. الطريقتين مشروحين تحت |
| `IN[1]` | Boolean | `False` معاينة بس · `True` يعمل النقط |
| `IN[2]` | String | منسوب النقط: `top` أو `bottom` أو `insertion`. لو فاضي هياخد `top` |
| `IN[3]` | String | فلتر الاسم، مثلاً `catch`. لو فاضي هياخد كل الـ Fittings |

### `IN[0]`: طريقتين تحدد بيهم الـ Fittings
| توصّل إيه | الكود ياخد إيه |
|---|---|
| **String** فاضي | كل الـ Fittings في كل الـ Pressure Networks اللي في الرسمة |
| **String** فيه اسم Network | الـ Fittings اللي في الـ Network دي بس |
| **Select Objects** | الـ Fittings اللي تحددها بإيدك. لو حددت معاها Pipes أو أي حاجة تانية بتتخطاها لوحدها |

في الحالات الثلاثة، فلتر الاسم في `IN[3]` بيتطبق بعد كده.

### `IN[3]`: فلتر الاسم
- الكود بيدوّر على الكلمة في **اسم الـ Fitting** وفي **الـ Part Description** وفي **الـ Part Family Name**.
- مش مهم الحروف كبيرة ولا صغيرة: `catch` و `Catch` و `CATCH` واحد.
- لو مش عارف تكتب إيه: سيب الفلتر فاضي وشغّل معاينة، وهتلاقي في آخر الملخص أسماء كل الـ Parts وعدد كل واحد.
- **لو سبته فاضي وشغّلت على `True`، هيتعمل نقط على الـ Elbows والـ Tees كمان.**

### تشغيله من Dynamo Player
احفظ الجراف كملف `.dyn`، واعمل كليك يمين على كل Node من المداخل الأربعة واختار **Is Input** (وسمّي كل واحدة باسم واضح بـ **Rename**). بعدها شغّله من **Manage ← Dynamo Player**.

---

## المخرجات
`OUT` ليستة فيها ٣ حاجات:

1. **الملخص** في سطر واحد: عدد الـ Fittings اللي اتحسبت، وعدد اللي فشلت، وعدد النقط، وأسماء الـ Parts وعدد كل واحد.
2. **تقرير الـ Fittings**، سطر لكل Fitting: `[الاسم، الـ Part، المقاس، الزاوية، الحالة]`.
   - **المقاس**: طول وعرض المستطيل اللي اتحسب، مثلاً `0.600 x 0.900`. قارنه بمقاس الـ Catch الحقيقي.
   - **الزاوية**: ميل المستطيل عن اتجاه الرسمة بالدرجات، ودايماً بين ‎-45 و ‎+45.
   - **الحالة**: `OK` أو `Failed` وجنبها السبب.
3. **جدول النقط**، سطر لكل نقطة: `[اسم الـ Fitting، الركن، X، Y، Z، رقم النقطة]`. في المعاينة رقم النقطة بيبقى `-`.

## أسماء الأركان
`BL` تحت شمال · `BR` تحت يمين · `TR` فوق يمين · `TL` فوق شمال.

الأسماء هنا **بالنسبة للرسمة** (الشمال فوق)، مش بالنسبة لاتجاه الـ Fitting نفسه، لإن الـ Fitting ملوش "وش" معروف زي الـ Structure.
يعني `BL` هو الركن اللي ناحية تحت شمال وانت باصص على الرسمة. ولو الـ Fitting مايل ٤٥ درجة بالظبط، التسمية ممكن تبدأ من أي ركن، بس الأربع أركان نفسهم صح.

## ارتفاع النقط (Z)
| قيمة `IN[2]` | مكان النقط في المجسم |
|---|---|
| `top` (الافتراضي) | **فوق**، على أعلى نقطة في الموديل |
| `bottom` | **تحت**، على أوطى نقطة في الموديل من بره |
| `insertion` | على منسوب نقطة إدراج الـ Fitting |

> هنا `bottom` هو أسفل المجسم من بره فعلاً، لإن الارتفاعات متاخدة من الموديل الـ 3D نفسه.
> ولو كتبت `rim` أو `sump` زي `corners-cogo`، الكود بيفهمهم على إنهم `top` و `bottom`.

---

## الحالات والرسايل
### في تقرير الـ Fittings
| الحالة | معناها |
|---|---|
| `OK` | الأركان اتحسبت |
| `Failed: Get3dBody is not available (needs Civil 3D 2022 or newer)` | نسخة Civil 3D قديمة ومفيهاش الدالة اللي بتجيب المجسم الـ 3D |
| `Failed: the part has no 3D body` | الـ Part ده مفيهوش مجسم 3D |
| `Failed: the 3D body is not at the fitting position` | المجسم اللي رجع من Civil 3D مش في مكان الـ Fitting. الكود بيرفض يعمل نقط في مكان غلط. لو طلعتلك، ابعتهالي |
| `Failed: <رسالة تانية>` | Civil 3D رجّع خطأ مع الـ Fitting ده. ابعتلي الرسالة |

الـ Fitting اللي حالته `Failed` مش بيتعمل له نقط، والباقي بيكمّل عادي.

### رسايل بتطلع بدل النتيجة
| الرسالة | معناها |
|---|---|
| `Nothing selected. ...` | موصّل Select Objects ومحددتش حاجة |
| `Pressure network '...' not found. Networks in this drawing: ...` | اسم الـ Network غلط. الأسماء الصح مكتوبة في نفس الرسالة |
| `No pressure fittings found.` | مفيش Fittings في الـ Network أو في اللي حددته |
| `No fitting matches the filter '...'. Parts found: ...` | مفيش Fitting اسمه فيه كلمة الفلتر. أسماء الـ Parts الموجودة مكتوبة في نفس الرسالة |
| `Error: ...` | خطأ عام. ابعتلي النص كله |

---

## ملاحظات مهمة
- **المستطيل بيحتوي الموديل كله.** لو الموديل فيه حاجة بارزة عن جسم الـ Catch (وصلة ماسورة طالعة منه، أو إطار غطا أكبر من الجسم)، الأركان هتطلع على حدودها هي. علشان كده راجع المقاس في المعاينة.
- **الموديل الدايري** بيطلع له مربع حوالين الدايرة، زي `corners-cogo`.
- **ماتشغلش على `True` مرتين**، علشان هيعمل نقط متكررة.
- **الوقت:** الكود بيقيس كل Fitting حوالي ٧٧ مرة علشان يوصل للزاوية. لو عندك مئات الـ Fittings أو الموديل تفاصيله كتير، ممكن ياخد من ثواني لدقيقة.

---

## شرح الكود حتة حتة

### 1) تحميل المكتبات
```python
import clr
import math

clr.AddReference('AcMgd')
clr.AddReference('AcCoreMgd')
clr.AddReference('AcDbMgd')
clr.AddReference('AecBaseMgd')
clr.AddReference('AeccDbMgd')
clr.AddReference('AeccPressurePipesMgd')
```
- أول ٥ مكتبات هي بتوع AutoCAD و Civil 3D، زي `corners-cogo`.
- **`AeccPressurePipesMgd`**: مكتبة الـ Pressure Networks. من غيرها Python مش هيشوف الـ Pressure Fittings خالص.

```python
from Autodesk.AutoCAD.ApplicationServices import Application
from Autodesk.AutoCAD.DatabaseServices import OpenMode, ObjectId
from Autodesk.AutoCAD.Geometry import Matrix3d, Point3d, Vector3d
from Autodesk.Civil.ApplicationServices import CivilApplication, CivilDocumentPressurePipesExtension
from Autodesk.Civil.DatabaseServices import PressureFitting
```
- `Matrix3d` و `Vector3d`: بنعمل بيهم دوران لنسخة من المجسم.
- `CivilDocumentPressurePipesExtension`: منها بنجيب كل الـ Pressure Networks اللي في الرسمة.
- `PressureFitting`: علشان نتأكد إن العنصر Fitting فعلاً، مش Pipe ولا حاجة تانية.

### 2) المدخلات والثوابت
```python
source_in = IN[0] if len(IN) > 0 else ""
run = IN[1] if len(IN) > 1 else False
elev_mode = str(IN[2] if len(IN) > 2 and IN[2] else "top").strip().lower()
name_filter = str(IN[3] if len(IN) > 3 and IN[3] else "").strip().lower()
```
- `source_in`: اسم الـ Network (String)، أو العناصر اللي جاية من Select Objects.
- `run`: `False` معاينة، و`True` تنفيذ.
- `elev_mode` و `name_filter`: `.strip()` بتشيل المسافات اللي في الأول والآخر، و`.lower()` بتخلي الحروف صغيرة علشان المقارنة ماتفرقش بين `Top` و `top`.

```python
COARSE_STEP = math.radians(2.0)
ANGLE_TOL = 1e-7
QUARTER_TURN = math.pi / 2.0
```
ثوابت البحث عن زاوية الـ Fitting (مشروحة في الخطوة ٨):
- `COARSE_STEP`: البحث الأولاني بيجرب زاوية كل **درجتين**.
- `ANGLE_TOL`: دقة الزاوية النهائية بالـ radian، وهي رقم صغير جداً.
- `QUARTER_TURN`: ربع لفة (٩٠ درجة). المستطيل شكله بيتكرر كل ٩٠ درجة، فالبحث في ٩٠ درجة بس كفاية.

### 3) `Scope`: قفل وتنضيف أكيد
```python
class Scope(object):
    def __init__(self, target):
        self.target = target

    def __enter__(self):
        return self.target

    def __exit__(self, exc_type, exc_value, exc_tb):
        self.target.Dispose()
        return False
```
أي حاجة بنفتحها من AutoCAD (قفل الرسمة، Transaction، مجسم مؤقت) لازم تتقفل بـ `Dispose()` لما نخلص منها، حتى لو حصل خطأ في النص.
`Scope` بتخلينا نكتب `with Scope(حاجة) as x:`، وأول ما الكود يخرج من تحت الـ `with` بتتنادى `Dispose()` لوحدها.
عملتها كلاس صغير في الكود نفسه علشان تشتغل بنفس الشكل على أي محرك Python في Dynamo.

### 4) تحويل اللي جاي من Select Objects لـ ObjectIds
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


def to_object_id(item):
    if isinstance(item, ObjectId):
        return item
    if hasattr(item, "InternalObjectId"):
        return item.InternalObjectId
    return None


def get_ids(items):
    ids = []
    for item in flatten(items):
        oid = to_object_id(item)
        if oid is not None and not oid.IsNull and not oid.IsErased:
            ids.append(oid)
    return ids
```
نفس الثلاث دوال بتوع `swap-structures`:
- `flatten`: بتحوّل المدخل لليستة واحدة، سواء جالها عنصر واحد أو ليستة جواها ليستات.
- `to_object_id`: الـ Select Objects بتطلع "غلاف" بتاع Dynamo حوالين العنصر، وجواه `InternalObjectId`، وهو رقم العنصر الحقيقي في الرسمة.
- `get_ids`: بتجمع الأرقام، وبتشيل أي عنصر فاضي أو اتمسح من الرسمة.

### 5) اسم الـ Fitting والفلتر
```python
def text_of(obj, prop):
    try:
        value = getattr(obj, prop)
    except Exception:
        return ""
    return str(value) if value is not None else ""
```
بتقرا خاصية نصية من العنصر. لو الخاصية مش موجودة في نسخة Civil 3D اللي عندك، بترجّع نص فاضي بدل ما الكود يقف.

```python
def part_label(fitting):
    return text_of(fitting, "PartDescription") or text_of(fitting, "PartFamilyName") or "Fitting"
```
اسم الـ Part اللي بيظهر في التقرير: الـ Part Description، ولو فاضي يبقى الـ Family Name.

```python
def matches_filter(fitting):
    if not name_filter:
        return True
    text = " ".join([text_of(fitting, "Name"),
                     text_of(fitting, "PartDescription"),
                     text_of(fitting, "PartFamilyName")])
    return name_filter in text.lower()
```
- لو الفلتر فاضي، كل الـ Fittings بتعدّي.
- غير كده بتجمع الاسم والـ Description والـ Family Name في نص واحد، وتشوف كلمة الفلتر موجودة فيه ولا لأ.

### 6) تجميع الـ Fittings
```python
def collect_ids(t, civdoc):
    if not isinstance(source_in, str):
        ids = get_ids(source_in)
        if not ids:
            return [], ("Nothing selected. Select the fittings (Select Objects), "
                        "or connect a String with the network name to IN[0].")
        return ids, ""
```
**الطريقة الأولى (Select Objects):** لو `IN[0]` مش نص، يبقى ده اختيار من الرسمة، فبناخد أرقام العناصر اللي اتحددت.
الدالة بترجّع حاجتين: الأرقام، ورسالة مشكلة (فاضية لو مفيش مشكلة).

```python
    wanted = source_in.strip()
    ids, networks = [], []
    for net_id in CivilDocumentPressurePipesExtension.GetPressurePipeNetworkIds(civdoc):
        net = t.GetObject(net_id, OpenMode.ForRead)
        networks.append(net.Name)
        if wanted and net.Name != wanted:
            continue
        for fitting_id in net.GetFittingIds():
            ids.append(fitting_id)
    if wanted and wanted not in networks:
        return [], "Pressure network '{}' not found. Networks in this drawing: {}".format(
            wanted, ", ".join(networks) or "none")
    return ids, ""
```
**الطريقة التانية (اسم Network):**
- `GetPressurePipeNetworkIds(civdoc)` بترجّع كل الـ Pressure Networks اللي في الرسمة.
- لو كاتب اسم، أي Network تانية بيتخطاها (`continue`). ولو الاسم فاضي بياخدهم كلهم.
- `GetFittingIds()` بترجّع كل الـ Fittings اللي في الـ Network.
- لو الاسم اللي كتبته مش موجود، الرسالة بتقولك أسماء الـ Networks الموجودة.

```python
def collect_fittings(t, civdoc):
    ids, problem = collect_ids(t, civdoc)
    if problem:
        return [], problem
    fittings, parts = [], set()
    for oid in ids:
        obj = t.GetObject(oid, OpenMode.ForRead)
        if not isinstance(obj, PressureFitting):
            continue
        parts.add(part_label(obj))
        if matches_filter(obj):
            fittings.append(obj)
    if not parts:
        return [], "No pressure fittings found."
    if not fittings:
        return [], "No fitting matches the filter '{}'. Parts found: {}".format(
            name_filter, ", ".join(sorted(parts)))
    return fittings, ""
```
- بتفتح كل عنصر، وأي حاجة مش `PressureFitting` بتتخطاها.
- `parts` بتحفظ أسماء كل الـ Parts اللي قابلتها. لو الفلتر ماجابش حاجة، الرسالة بتعرضهم علشان تعرف تكتب إيه.
- اللي بيعدّي من الفلتر بيتحط في `fittings`.

### 7) قياس الصندوق اللي حوالين المجسم
```python
def box_of(entity):
    ext = entity.GeometricExtents
    return (ext.MinPoint.X, ext.MinPoint.Y, ext.MinPoint.Z,
            ext.MaxPoint.X, ext.MaxPoint.Y, ext.MaxPoint.Z)
```
`GeometricExtents` هو الصندوق اللي حوالين المجسم، وأضلاعه **موازية لمحاور الرسمة**.
الدالة بترجّع ٦ أرقام: أقل X و Y و Z، وأكبر X و Y و Z.

```python
def box_at(body, pivot, angle):
    with Scope(body.Clone()) as copy:
        copy.TransformBy(Matrix3d.Rotation(-angle, Vector3d.ZAxis, pivot))
        return box_of(copy)
```
دي أساس الطريقة كلها:
- `body.Clone()`: بتعمل **نسخة** من المجسم في الذاكرة. الـ Fitting اللي في الرسمة مابيتلمسش.
- `Matrix3d.Rotation(-angle, Vector3d.ZAxis, pivot)`: دوران حوالين محور Z، مركزه `pivot`، بعكس الزاوية `angle`.
- بنلف النسخة ونقيس الصندوق اللي حواليها، وبعدين النسخة بتتمسح لوحدها (`Scope`).

يعني الدالة بتجاوب على سؤال: «لو الـ Fitting كان مايل بالزاوية دي وعدلناه، الصندوق اللي حواليه هيبقى قد إيه؟»

```python
def plan_area(box):
    return (box[3] - box[0]) * (box[4] - box[1])
```
مساحة الصندوق في المسقط الأفقي: فرق الـ X في فرق الـ Y.

### 8) البحث عن زاوية الـ Fitting
**الفكرة:** لو مستطيل مايل، الصندوق الموازي للمحاور اللي حواليه بيبقى أكبر منه. ولو لفّينا المستطيل لحد ما يتعدل، الصندوق بيبقى قده بالظبط، ودي **أصغر مساحة ممكنة**.
يبقى الزاوية اللي بتدّي أصغر مساحة هي زاوية ميل الـ Fitting.

```python
def golden_min(f, lo, hi):
    ratio = (math.sqrt(5.0) - 1.0) / 2.0
    c = hi - ratio * (hi - lo)
    d = lo + ratio * (hi - lo)
    fc, fd = f(c), f(d)
    while hi - lo > ANGLE_TOL:
        if fc < fd:
            hi, d, fd = d, c, fc
            c = hi - ratio * (hi - lo)
            fc = f(c)
        else:
            lo, c, fc = c, d, fd
            d = lo + ratio * (hi - lo)
            fd = f(d)
    return (lo + hi) / 2.0
```
دي طريقة رياضية معروفة اسمها **Golden-section search**: بتلاقي أقل قيمة لدالة جوه مدى معين (من `lo` لـ `hi`).
- بتقيس الدالة عند نقطتين جوه المدى.
- بترمي الجزء اللي أكيد مفيهوش أقل قيمة، فالمدى بيصغر.
- بتكرر لحد ما المدى يبقى أصغر من `ANGLE_TOL`.

كل لفة بتحتاج قياس واحد جديد بس، فبتوصل لدقة عالية جداً في حوالي ٣٠ قياس.

```python
def best_angle(measure):
    area_at = lambda angle: plan_area(measure(angle))
    base = area_at(0.0)
    steps = int(round(QUARTER_TURN / COARSE_STEP))
    half = steps // 2
    best, best_area = 0.0, base
    for i in range(-half, steps - half):
        if i == 0:
            continue
        value = area_at(i * COARSE_STEP)
        if value < best_area:
            best, best_area = i * COARSE_STEP, value
```
**المرحلة الأولى، بحث سريع:**
- `area_at(angle)`: مساحة الصندوق لو عدلنا المجسم بالزاوية دي.
- `base`: المساحة من غير أي دوران.
- بنجرب ٤٥ زاوية، من ‎-44 لـ ‎+44 درجة، كل درجتين، ونحفظ الزاوية اللي ادّت أصغر مساحة.

```python
    fine = golden_min(area_at, best - COARSE_STEP, best + COARSE_STEP)
    if base <= area_at(fine) * (1.0 + 1e-9):
        return 0.0
    while fine > QUARTER_TURN / 2.0:
        fine -= QUARTER_TURN
    while fine <= -QUARTER_TURN / 2.0:
        fine += QUARTER_TURN
    return fine
```
**المرحلة التانية، بحث دقيق:**
- `golden_min` بتدوّر حوالين أحسن زاوية (درجتين قبلها ودرجتين بعدها) لحد ما توصل للزاوية بالظبط.
- لو الدوران مافرقش في المساحة (الـ Fitting أصلاً معدول، أو الموديل دايري)، بترجّع زاوية `0` بالظبط.
- آخر سطور بتخلي الزاوية دايماً بين ‎-45 و ‎+45 درجة، علشان أسماء الأركان تبقى بالنسبة للرسمة.

### 9) الأركان الأربعة
```python
def rectangle(box, px, py, angle):
    cos_a, sin_a = math.cos(angle), math.sin(angle)
    local = [("BL", box[0], box[1]),
             ("BR", box[3], box[1]),
             ("TR", box[3], box[4]),
             ("TL", box[0], box[4])]
    corners = []
    for tag, x, y in local:
        dx, dy = x - px, y - py
        corners.append((tag,
                        px + dx * cos_a - dy * sin_a,
                        py + dx * sin_a + dy * cos_a))
    return corners
```
- `box` هو الصندوق اللي اتقاس والنسخة **معدولة**، فأركانه الأربعة هي `local`.
- بس دي أركان النسخة المعدولة، مش الـ Fitting الحقيقي. فبنرجّع كل ركن لمكانه الحقيقي في الرسمة: بنلفه بنفس الزاوية حوالين نفس المركز (`px`, `py`)، بس عكس اتجاه اللفة اللي عدلنا بيها النسخة.
- دي نفس معادلة الدوران اللي في `corners-cogo`.

### 10) المجسم الـ 3D بتاع الـ Fitting
```python
def near(box, point):
    margin = 5.0 * max(box[3] - box[0], box[4] - box[1])
    return (box[0] - margin <= point.X <= box[3] + margin and
            box[1] - margin <= point.Y <= box[4] + margin)
```
فحص أمان: بيتأكد إن نقطة إدراج الـ Fitting قريبة من المجسم (في حدود ٥ أضعاف مقاسه). لو المجسم طلع في مكان تاني خالص، يبقى فيه حاجة غلط، والكود بيرفض يكمّل.

```python
def get_body(fitting):
    try:
        body = fitting.Get3dBody()
    except AttributeError:
        raise Exception("Get3dBody is not available (needs Civil 3D 2022 or newer)")
    if body is None:
        raise Exception("the part has no 3D body")
    return body
```
- **`Get3dBody()`**: دالة في Civil 3D بترجّع المجسم الـ 3D بتاع الـ Fitting كـ `Solid3d`. ده نفس الموديل اللي انت عامله.
- لو الدالة مش موجودة (نسخة قديمة)، أو الـ Part ملوش مجسم، بتطلع رسالة واضحة.

```python
def footprint(fitting):
    with Scope(get_body(fitting)) as body:
        start = box_of(body)
        if not near(start, fitting.Position):
            raise Exception("the 3D body is not at the fitting position")
        px = (start[0] + start[3]) / 2.0
        py = (start[1] + start[4]) / 2.0
        pivot = Point3d(px, py, 0.0)
        measure = lambda angle: box_at(body, pivot, angle)
        angle = best_angle(measure)
        box = measure(angle)
    return {"corners": rectangle(box, px, py, angle),
            "top": box[5],
            "bottom": box[2],
            "length": box[3] - box[0],
            "width": box[4] - box[1],
            "angle": math.degrees(angle)}
```
دي اللي بتجمع كل اللي فات لـ Fitting واحد:
1. بتجيب المجسم، وتقيس الصندوق اللي حواليه (`start`).
2. بتعمل فحص الأمان (`near`) مع `fitting.Position`، وهي نقطة إدراج الـ Fitting.
3. `pivot`: مركز الصندوق، وهو مركز الدوران. اخترناه قريب من المجسم علشان الحساب يفضل دقيق مع الإحداثيات الكبيرة.
4. `best_angle` بتلاقي الزاوية، وبعدين بنقيس الصندوق عند الزاوية دي.
5. بترجّع: الأركان الأربعة، وأعلى وأوطى منسوب في الموديل (`box[5]` و `box[2]`)، والطول والعرض، والزاوية بالدرجات.

المجسم بيتمسح من الذاكرة لوحده أول ما نخرج من الـ `with`.

### 11) المنسوب (Z)
```python
def elevation(fitting, shape):
    if elev_mode in ("bottom", "sump"):
        return shape["bottom"]
    if elev_mode == "insertion":
        return fitting.Position.Z
    return shape["top"]
```
بيرجّع المنسوب حسب `IN[2]`. أي حاجة غير `bottom` و `sump` و `insertion` بتاخد أعلى نقطة في الموديل.

### 12) التنفيذ الأساسي
```python
def main():
    adoc = Application.DocumentManager.MdiActiveDocument
    db = adoc.Database
    civdoc = CivilApplication.ActiveDocument
    report, points = [], []
    parts = {}
    failed = 0
```
- `adoc` الرسمة المفتوحة، و`db` قاعدة بياناتها، و`civdoc` نفس الرسمة من ناحية Civil 3D.
- `report` تقرير الـ Fittings، و`points` جدول النقط، و`parts` عدّاد لكل Part، و`failed` عدد اللي فشلوا.

```python
    with Scope(adoc.LockDocument()):
        with Scope(db.TransactionManager.StartTransaction()) as t:
            fittings, problem = collect_fittings(t, civdoc)
            if problem:
                return problem
```
- `LockDocument`: قفل الرسمة علشان نقدر نعدّل فيها.
- `StartTransaction`: أي قراية أو تعديل بيحصل جواها.
- لو تجميع الـ Fittings رجّع رسالة مشكلة، بنرجّعها ونقف.

```python
            for fitting in fittings:
                part = part_label(fitting)
                name = text_of(fitting, "Name") or part
                try:
                    shape = footprint(fitting)
                    z = elevation(fitting, shape)
                except Exception as ex:
                    failed += 1
                    report.append([name, part, "-", "-", "Failed: {}".format(ex)])
                    continue
```
لكل Fitting بنحسب الشكل والمنسوب. الـ `try` معناها إن لو Fitting واحد فشل، بيتكتب السبب في التقرير والكود يكمّل على الباقي.

```python
                parts[part] = parts.get(part, 0) + 1
                report.append([name, part,
                               "{:.3f} x {:.3f}".format(shape["length"], shape["width"]),
                               round(shape["angle"], 3), "OK"])
```
بيزوّد عدّاد الـ Part، ويكتب سطر في التقرير فيه المقاس والزاوية.

```python
                for tag, x, y in shape["corners"]:
                    number = "-"
                    if run:
                        point_id = civdoc.CogoPoints.Add(Point3d(x, y, z), True)
                        cogo = t.GetObject(point_id, OpenMode.ForWrite)
                        cogo.RawDescription = "{}_{}".format(name, tag)
                        number = cogo.PointNumber
                    points.append([name, tag, round(x, 3), round(y, 3), round(z, 3), number])

            t.Commit()
```
- لكل ركن: لو `run` بـ `True`، بيعمل COGO Point برقم تلقائي، ويكتب وصفه (مثلاً `CB-1_TR`)، وياخد رقمه.
- لو `run` بـ `False` (معاينة)، مفيش نقطة بتتعمل، ورقم النقطة بيبقى `-`.
- النقطة بتتعمل بالإحداثيات الكاملة. التقريب لـ ٣ أرقام عشرية في الجدول بس.
- `Commit()` بيحفظ النقط في الرسمة.

```python
    action = "Points created" if run else "Points to create (preview - set IN[1] = True)"
    summary = "Fittings: {} | Failed: {} | {}: {} | Parts: {}".format(
        len(fittings) - failed, failed, action, len(points),
        ", ".join("{} ({})".format(k, v) for k, v in sorted(parts.items())) or "-")
    return [summary, report, points]
```
بيعمل سطر الملخص، ويرجّع الثلاث حاجات: الملخص، وتقرير الـ Fittings، وجدول النقط.

### 13) مسك الأخطاء
```python
try:
    OUT = main()
except Exception as ex:
    import traceback
    OUT = "Error: {}\n{}".format(ex, traceback.format_exc())
```
لو حصل خطأ عام، نص الخطأ بيظهر كامل في الـ Watch بدل ما الـ node تقفل من غير ما تعرف السبب. وساعتها مفيش أي نقطة بتتحفظ.
