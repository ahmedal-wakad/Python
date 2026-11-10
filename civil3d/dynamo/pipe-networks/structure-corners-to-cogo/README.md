# Structure Corners → COGO Points

| | |
|---|---|
| **البرنامج** | Civil 3D + Dynamo |
| **المحرك** | CPython3 |
| **الملف** | [`structure_corners_to_cogo.txt`](structure_corners_to_cogo.txt) |

## الكود بيعمل إيه
1. بيقرا كل الـ Structures في Pipe Network معينة، أو في كل الـ networks.
2. بيحسب الأربع أركان لكل Structure بعد ما يطبّق عليها زاوية دورانها.
3. بيعمل COGO Point على كل ركن، ووصفه بالشكل ده: `اسم_الـStructure_الركن`، مثلاً `MH-1_TR`.

## المدخلات (Python Script node)
| المدخل | النوع | الوصف |
|---|---|---|
| `IN[0]` | String | اسم الـ Network. لو فاضي بيلف على كل الـ networks |
| `IN[1]` | Boolean | خليه `True` علشان ينفّذ |
| `IN[2]` | String | منسوب النقاط: `rim` (الافتراضي، الغطا) أو `sump` (القاع من جوه) أو `insertion` (نقطة الإدراج) |

## المخرجات
ليستة فيها لكل نقطة: `[اسم الـ Structure، الركن، X، Y، Z، رقم النقطة]`.

## أسماء الأركان
`BL` تحت شمال · `BR` تحت يمين · `TR` فوق يمين · `TL` فوق شمال (بالنسبة لاتجاه الـ Structure نفسه).

## ملاحظات
- الـ Structure الدايري (Cylinder) بتطلع أركانه على المربع اللي حوالين الدايرة (القطر × القطر).
- ماتشغلش الكود مرتين وهو على `True`، علشان هيعمل نقط متكررة.
- علشان تاخد المقاس الداخلي بدل الخارجي: غيّر `DiameterOrWidth` لـ `InnerDiameterOrWidth`، و`Length` لـ `InnerLength`.
- المنسوب `sump` هو أرضية الـ Structure من جوه. أسفل المجسم من بره أوطى منه بمقدار سُمك الأرضية.
