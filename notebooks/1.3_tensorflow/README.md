# 1.3 dalis — „TensorFlow“ ir „TensorFlow Decision Forests“

Čia pateikiamos **dvi užrašinės (angl. _notebooks_)**, atitinkančios užduoties 1.3 punktą („AdventureWorks
DW using TensorFlow“ ir „… using TensorFlow Decision Forests“ „Colab“ dokumentai suprasti
ir perdaryti su šio projekto duomenimis).

| Užrašinė                                  | Originalo analogas                                         | Ką modelis daro                                     |
| ----------------------------------------- | ---------------------------------------------------------- | --------------------------------------------------- |
| `1_3a_tensorflow_intent_classifier.ipynb` | „TensorFlow“ tankusis tinklas su DW duomenimis             | TF-IDF požymiai → Dense/MLP → 6 ketinimų klasės     |
| `1_3b_tensorflow_decision_forests.ipynb`  | TF-DF sprendimų medžių modeliai su lenteliniais duomenimis | Tas pats be rankinės požymių inžinerijos (RF + GBT) |

**Duomenys:** 252 (X, y) poros iš `data/customer_support_dataset.csv` (X = kliento
užklausos tekstas, y = ketinimo klasė). Vertinimui visur naudojama ta pati
kanoninė 6 užklausų aibė kaip ir 1.2.2 „Gemini“ prototipe — todėl visų dalių
modelius galima objektyviai palyginti.

Paleidimas: atidarykite užrašinę per „Google Colab“ (_File → Upload notebook_);
GPU nebūtinas — užtenka CPU.
