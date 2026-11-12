# مكتبة أكواد Ahmed — Civil 3D / Dynamo / Python

مكتبة لأكواد الأتمتة الخاصة بمشاريع البنية التحتية.
كل كود محفوظ كملف `.txt` علشان يتنسخ بسهولة ويتلصق في الـ Python Script node جوه Dynamo.

## تقسيم المكتبة

```
civil3d/                      ← كل ما يخص Civil 3D
└── dynamo/                   ← أكواد Dynamo (CPython3)
    └── pipe-networks/        ← شبكات المواسير والـ Structures
        ├── corners-cogo/
        │   ├── README.md          ← الشرح والمدخلات والمخرجات
        │   └── corners-cogo.txt   ← الكود
        └── swap-structures/
            ├── README.md
            └── swap-structures.txt
templates/                    ← قوالب جاهزة تبدأ منها أي كود جديد
```

## فهرس المشاريع

كل مشروع ليه **اسم مختصر**، وهو نفس اسم الفولدر ونفس اسم ملف الكود.
لما تحب تعدّل في مشروع، قول لـ Claude اسمه المختصر بس، مثلاً: «عدّل في `corners-cogo`».

| الاسم المختصر | البرنامج | القسم | الوصف |
|---|---|---|---|
| [`corners-cogo`](civil3d/dynamo/pipe-networks/corners-cogo/) | Civil 3D + Dynamo (CPython3) | Pipe Networks | يقرا الـ Structures ويطلع COGO Point على الأربع أركان |
| [`swap-structures`](civil3d/dynamo/pipe-networks/swap-structures/) | Civil 3D + Dynamo (CPython3) | Pipe Networks | يعمل Swap لكذا Structure مرة واحدة علشان تبقى زي Structure مرجع |

## قواعد إضافة مشروع جديد

1. اختار القسم المناسب، مثلاً `civil3d/dynamo/alignments/` أو `civil3d/dynamo/corridors/` أو `civil3d/dynamo/surfaces/`. ولو القسم مش موجود اعمله.
2. اختار للمشروع **اسم مختصر**: كلمتين إنجليزي صغيرين بينهم شَرطة، مثلاً `pipe-labels`. اتأكد إنه مش مكرر في الفهرس.
   الفولدر بيتسمى بالاسم ده، وملف الكود كمان (`pipe-labels.txt`).
3. جوه الفولدر حط:
   - `README.md` فيه الشرح والمدخلات والمخرجات.
   - ملف الكود بامتداد `.txt`، فيه الكود بس من غير شرح.
4. ابدأ الكود من القالب [`templates/dynamo_civil3d_cpython3_template.txt`](templates/dynamo_civil3d_cpython3_template.txt).
5. ضيف سطر للمشروع في جدول **فهرس المشاريع** اللي فوق.

## نسخ الكود بسرعة من GitHub
افتح ملف الـ `.txt` ودوس زرار **Copy raw file** (أيقونة النسخ فوق الملف على اليمين)، وبعدها الصقه في الـ Python node.
