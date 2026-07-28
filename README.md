# Хэрэглэгчийн цуцлалт (Customer Churn) таамаглал — Mini Project

Телеком компанийн хэрэглэгчийн үйлчилгээгээ цуцлах эсэхийг (churn) урьдчилан
таамаглах жижиг машин сургалтын төсөл. Дата нь **синтетик (fake/masked)**,
жинхэнэ хэрэглэгчийн мэдээлэл биш.

## Яагаад чухал вэ (асуудал)

Шинэ хэрэглэгч татахаас илүү, байгаа хэрэглэгчээ хадгалах нь хямд. Аль
хэрэглэгч цуцлах магадлалтайг эрт мэдэж чадвал компани урьдчилж арга хэмжээ
авах боломжтой (хямдрал санал болгох, дэмжлэг үзүүлэх гэх мэт).

## Дата

`data/B_customer_churn.csv` — 8,080 мөр, 10 багана (customer_id, plan_type,
tenure_months, monthly_spend, monthly_usage_gb, call_minutes, message_count,
days_since_last_payment, complaints_count, is_churned).

Дата эхнээсээ цэвэр байгаагүй:
- `monthly_spend`, `monthly_usage_gb` баганад missing утга байсан
- `plan_type` их/жижиг үсэг, зайтай холилдсон байсан (`basic`, `Basic`, `BASIC`, ` basic`...)
- давхардсан мөрүүд байсан
- `tenure_months`-д сөрөг утга, `monthly_spend`-д мэдэгдэхүйц outlier
  (max 11,538) байсан

Target нь тэнцвэргүй: цуцалсан хэрэглэгч ердөө **~18.6%**.

## Хандлага

Notebook-ууд дараах дарааллаар ажилладаг (тус бүр өмнөхийнхөө гаралтыг ашигладаг):

| # | Notebook | Юу хийдэг |
|---|----------|-----------|
| 01 | `01_eda.ipynb` | Анхны дата шинжилгээ — хэлбэр, төрөл, missing утга, ангиллын тархалт |
| 02 | `02_data_cleaning.ipynb` | Цэвэрлэгээ — missing утга дүүргэх, `plan_type` стандартчлах, давхардал устгах, буруу утга (сөрөг tenure, extreme spend) арилгах |
| 03 | `03_eda_visuals.ipynb` | Цэвэрлэсэн дата дээрх визуал шинжилгээ — тархалт, churn rate segment-ээр |
| 04 | `04_feature_engineering.ipynb` | Feature үүсгэх — tenure/complaint/payment bucket, usage-per-spend харьцаа, plan_type dummy encoding |
| 05 | `05_baseline_model.ipynb` | Хамгийн энгийн baseline (Dummy Classifier) vs Logistic Regression харьцуулалт |
| 06 | `06_model_evaluation.ipynb` | Logistic Regression-ийг чанартай үнэлгээ (precision/recall/F1, ROC-AUC, 5-fold cross-validation) |
| 07 | `07_customer_segmentation.ipynb` | K-Means ашиглан хэрэглэгчийг segment-лэх, churn rate-ийг segment/tenure/usage-аар харах |

## Хэрхэн ажиллуулах вэ

```bash
pip install -r requirements.txt
```

`data/` фолдерт `B_customer_churn.csv`-г байрлуулаад, `notebooks/`-ийн дотроос
01-ээс 07 хүртэл дарааллаараа ажиллуулна (тус бүр нь дараагийнхаа хэрэглэдэг
CSV-г `../data/` дотор үүсгэдэг):

```
01_eda.ipynb                    → зөвхөн уншиж шинжилнэ
02_data_cleaning.ipynb          → data/B_customer_churn_cleaned.csv үүсгэнэ
03_eda_visuals.ipynb            → цэвэрлэсэн дата дээр графикууд
04_feature_engineering.ipynb    → data/features_df.csv үүсгэнэ
05_baseline_model.ipynb         → baseline vs logistic regression
06_model_evaluation.ipynb       → эцсийн загварын дэлгэрэнгүй үнэлгээ
07_customer_segmentation.ipynb  → K-Means segment-үүд
```

## Гол үр дүн (шударгаар — зөвхөн accuracy биш)

Logistic Regression, test set (20%, stratified) дээр:

- **Accuracy: 83%** — гэхдээ target нь тэнцвэргүй (~81% нь churn=0) тул
  accuracy дангаараа төдийлөн үнэ цэнэтэй биш.
- **Churn (1) классын хувьд: precision 0.62, recall 0.28, F1 0.38** — өөрөөр
  хэлбэл жинхэнэ цуцалсан хэрэглэгчдийн ердөө ~28%-ийг л зөв илрүүлж чадаж
  байна.
- **ROC-AUC: 0.79** (5-fold cross-validation дундаж 0.79, std 0.014 — тогтвортой).

**Гол дүгнэлт:** загвар нь баримт хоосон биш ч (dummy baseline-аас дээр),
churn-ийг практикт ашиглахад **recall хэт бага** — өөрөөр хэлбэл цуцлах гэж
буй хэрэглэгчдийн дийлэнх хувийг алдаж байна. Одоогийн байдлаар энэ загварыг
"хэн цуцлах вэ" гэсэн эрсдэлийн жагсаалт гаргахад шууд найдаж болохгүй.

## Хязгаарлалт

- Дата бүхэлдээ **синтетик** — жинхэнэ хэрэглэгчийн зан төлөвийг бүрэн
  илэрхийлэхгүй байж болно.
- Загвар зөвхөн Logistic Regression — non-linear харьцаа барихгүй.
- Class imbalance-ийг тусгайлан шийдээгүй (жишээ нь `class_weight`,
  SMOTE ашиглаагүй) — энэ нь бага recall-ийн нэг шалтгаан.
- Threshold нь анхны 0.5 хэвээрээ — business context-д тааруулж
  тохируулаагүй.
- K-Means segmentation (07) нь тайлбарлах зорилготой, урьдчилан таамаглах
  загвартай шууд холбогдоогүй.

## Цаашид цаг байвал юу хийх вэ

- `class_weight='balanced'` эсвэл SMOTE/undersampling ашиглаж recall-ийг
  сайжруулах
- Tree-based загвар (Random Forest, Gradient Boosting) туршиж non-linear
  харьцааг барих
- Decision threshold-ийг business cost (алдсан хэрэглэгчийн үнэ vs дэмжлэгийн
  зардал) дээр үндэслэн сонгох
- Segmentation (07) болон churn таамаглалыг нэгтгэж, segment тус бүрт өөр
  загвар/threshold туршиж үзэх

## Файлын бүтэц

```
.
├── README.md
├── reading_log.md
├── requirements.txt
├── .gitignore
├── data/                          # CSV-үүдийг энд байрлуулна (repo-д ороогүй)
└── notebooks/
    ├── 01_eda.ipynb
    ├── 02_data_cleaning.ipynb
    ├── 03_eda_visuals.ipynb
    ├── 04_feature_engineering.ipynb
    ├── 05_baseline_model.ipynb
    ├── 06_model_evaluation.ipynb
    └── 07_customer_segmentation.ipynb
```
