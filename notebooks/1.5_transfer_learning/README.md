# 1.5 dalis — Perkeltasis mokymas (angl. _Transfer learning_)

Užduoties 1.5 punktui skirtos **dvi užrašinės (angl. _notebooks_)**, atitinkančios „Transfer
learning 1“ ir „Transfer learning 2“ „Colab“ dokumentus (suprasti ir perdaryti su
savais duomenimis).

| Užrašinė                                          | Originalo analogas  | Metodas                                                                                        |
| ------------------------------------------------- | ------------------- | ---------------------------------------------------------------------------------------------- |
| `1_5a_transfer_learning_feature_extraction.ipynb` | Transfer learning 1 | Užšaldyta „MobileNetV2“ bazė + mokoma tik klasifikavimo galvutė                                |
| `1_5b_transfer_learning_fine_tuning.ipynb`        | Transfer learning 2 | 1 etapas (požymių išskyrimas) → 2 etapas (paskutinių sluoksnių tikslusis derinimas, `lr=1e-5`) |

**Duomenys:** tos pačios (X, y) poros kaip ir 1.4 dalyje, tik užklausos
paverčiamos **RGB 96×96 vaizdais** — „MobileNetV2“ reikalauja 3 kanalų.
Vertinama ta pačia kanonine 6 užklausų aibe.

**Svarbios techninės detalės** (paaiškintos ir užrašinėse):

- pikseliai perkeliami į `[-1, 1]` intervalą (`Rescaling` sluoksnis) — tokios įvesties
  tikisi su „ImageNet“ apmokytas modelis;
- tiksliojo derinimo (angl. _fine-tuning_) metu mokymosi greitis sumažinamas 100× (`1e-3 → 1e-5`), kitaip
  perkelti svoriai būtų sugadinti;
- `BatchNormalization` sluoksniai visada lieka užšaldyti.

Rekomenduojama naudoti GPU: _Runtime → Change runtime type →_ **T4 GPU**.
