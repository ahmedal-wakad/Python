# مكتبة أكواد Ahmed — Civil 3D / Dynamo / Python

مكتبة لأكواد الأتمتة الخاصة بمشاريع البنية التحتية.
كل كود محفوظ كملف `.txt` علشان يتنسخ بسهولة ويتلصق في الـ Python Script node جوه Dynamo.

## تقسيم المكتبة

```
civil3d/                      ← كل ما يخص Civil 3D
└── dynamo/                   ← أكواد Dynamo (CPython3)
    └── pipe-networks/        ← شبكات المواسير والـ Structures
        └── structure-corners-to-cogo/
            ├── README.md                       ← الشرح والمدخلات والمخرجات
            └── structure_corners_to_cogo.txt   ← الكود
templates/                    ← قوالب جاهزة تبدأ منها أي كود جديد
```

## فهرس المشاريع

| المشروع | البرنامج | القسم | الوصف |
|---|---|---|---|
| [Structure Corners → COGO](civil3d/dynamo/pipe-networks/structure-corners-to-cogo/) | Civil 3D + Dynamo (CPython3) | Pipe Networks | يقرا الـ Structures ويطلع COGO Point على الأربع أركان |

## قواعد إضافة مشروع جديد

1. اختار القسم المناسب، مثلاً `civil3d/dynamo/alignments/` أو `civil3d/dynamo/corridors/` أو `civil3d/dynamo/surfaces/`. ولو القسم مش موجود اعمله.
2. اعمل فولدر للمشروع باسم إنجليزي صغير بشَرط، مثلاً `pipe-labels-export`.
3. جوه الفولدر حط:
   - `README.md` فيه الشرح والمدخلات والمخرجات.
   - ملف الكود بامتداد `.txt`.
4. ابدأ الكود من القالب [`templates/dynamo_civil3d_cpython3_template.txt`](templates/dynamo_civil3d_cpython3_template.txt).
5. ضيف سطر للمشروع في جدول **فهرس المشاريع** اللي فوق.

## نسخ الكود بسرعة من GitHub
افتح ملف الـ `.txt` ودوس زرار **Copy raw file** (أيقونة النسخ فوق الملف على اليمين)، وبعدها الصقه في الـ Python node.
