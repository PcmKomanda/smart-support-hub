# `data/` direktorija — (X, y) poros ir žinių bazė

Ši direktorija atitinka užduoties **1.2.1** reikalavimą: joje pateikiamos **(X, y) poros**,
demonstruojančios pilną (angl. _End-to-End_) sprendimą — nuo kliento užklausos **(X)** iki laukiamo
sistemos atsakymo **(y)**.

## Failai

| Failas                         | Paskirtis                                               |
| ------------------------------ | ------------------------------------------------------- |
| `customer_support_train.csv`   | 1.2.1 mokymo aibė: 20 pavyzdžių                         |
| `customer_support_test.csv`    | 1.2.1 testavimo aibė: 6 pavyzdžiai (E2E demonstravimui) |
| `customer_support_dataset.csv` | 1.3–1.5 giliojo mokymosi aibė: 252 (X, y) poros         |
| `knowledge_base.json`          | Žinių bazė (KB-001…KB-006), kurią naudoja RAG paieška   |

## (X, y) poros — struktūra

CSV stulpeliai:

| Stulpelis         | Vaidmuo | Paaiškinimas                                              |
| ----------------- | ------- | --------------------------------------------------------- |
| `query`           | **X**   | Kliento užklausa (natūralios kalbos tekstas)              |
| `intent`          | **y₁**  | Tikroji ketinimo klasė (6 klasės) — klasifikavimo etiketė |
| `expected_answer` | **y₂**  | Laukiamas galutinis sistemos atsakymas — E2E etalonas     |
| `source_doc_id`   | —       | Žinių bazės dokumento, kuriuo turi remtis atsakymas, ID   |

## Ketinimų klasės (y₁)

`order_status`, `delivery`, `returns`, `payment`, `warranty`, `product_usage`

## Kaip naudoti E2E testavimui

1. Paimkite `query` (X) iš `customer_support_test.csv` (arba parašykite naują užklausą).
2. Paleiskite agentą su `prompts/system_prompt.txt` ir žinių baze.
3. Sugeneruotą atsakymą palyginkite su `expected_answer` (y₂) — semantinis
   panašumas vertinamas „Colab“ prototipe `1.1_smart_customer_service_gemini.ipynb` (skyriuje „Vertinimas“).
