# Išmanioji klientų aptarnavimo ir žinių bazės sistema

### Smart Customer Service & Knowledge Base System

Kursinis darbas tema _„Informacinė sistema, naudojanti generatyvųjį dirbtinį intelektą“_.
DI agentas **„Aida“** atsakinėja į internetinės parduotuvės „TechStore“ klientų klausimus:
nustato užklausos ketinimą, ieško atsakymo vidinėje žinių bazėje (RAG), sugeneruoja
atsakymą per **Gemini API** ir, kai informacijos nėra, persiunčia klausimą žmogaus
operatoriui (eskalacija).

## E2E procesas

```
Kliento užklausa (X) → Ketinimo klasifikavimas → RAG paieška žinių bazėje
  → Atsakymo generavimas (Gemini API) → Atsakymas (y) + šaltiniai
                                        ↘ (nerasta) → Eskalacija žmogui
```

## Repozitorijos struktūra

| Vieta                                             | Užduoties dalis | Turinys                                                                                                 |
| ------------------------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------- |
| `docs/prompt.md`                                  | 1.1             | Užklausa (angl. _prompt_) „ChatGPT“ sistemai: sistemos sukūrimas per „PlantUML“, perdarymas į „Mermaid“ |
| `docs/plantuml/`                                  | 1.1             | 5 „PlantUML“ diagramos: naudojimo atvejų, klasių, sekų, veiklos, komponentų                             |
| `docs/mermaid/diagrams.md`                        | 1.1             | Tos pačios diagramos „Mermaid“ formatu (atvaizduojamos „GitHub“ platformoje)                            |
| `data/`                                           | 1.2.1           | (X, y) poros: 20 mokymo + 6 testavimo pavyzdžių + žinių bazė (6 dokumentai)                             |
| `prompts/`                                        | 1.2.1           | Sistemos instrukcija + 4 testavimo užklausos (3 atsakymo + 1 eskalacijos atvejis)                       |
| `1.1_smart_customer_service_gemini.ipynb`         | 1.1             | „Colab“ prototipas su „Gemini API“ (projekto apžvalga, paaiškintas kodas, E2E procesas, vizualizacijos) |
| `1.3a_tensorflow_intent_classifier.ipynb`         | 1.3(a)          | „TensorFlow“ Dense/MLP ketinimų klasifikatorius                                                         |
| `1.3b_tensorflow_decision_forests.ipynb`          | 1.3(b)          | TF-DF („RandomForest“ + GBT) ketinimų klasifikatoriai                                                   |
| `1.4_cnn_query_images.ipynb`                      | 1.4             | CNN — užklausos piešiamos kaip vaizdai, klasifikavimas pagal vaizdą                                     |
| `1.5a_transfer_learning_feature_extraction.ipynb` | 1.5(a)          | „MobileNetV2“ požymių išskyrimas (angl. _feature extraction_)                                           |
| `1.5b_transfer_learning_fine_tuning.ipynb`        | 1.5(b)          | „MobileNetV2“ tikslusis derinimas (angl. _fine-tuning_)                                                 |

## Kaip paleisti „Colab“ prototipą (1.1)

1. Atidarykite `1.1_smart_customer_service_gemini.ipynb` per
   [Google Colab](https://colab.research.google.com) (_File → Upload notebook_).
2. Įsidėkite API raktą: [Google AI Studio](https://aistudio.google.com/apikey)
   → nukopijuokite raktą → „Colab“ kairysis skydelis 🔑 **Secrets** →
   _Add secret_ → vardas `GOOGLE_API_KEY`. Raktas niekada neįrašomas į kodą.
3. Paleiskite langelius iš eilės (_Runtime → Run all_).

Užrašinėje (angl. _notebook_) pateikiami: projekto pavadinimas ir apžvalga, paaiškintas kodas kiekvienam
žingsniui, pilnas DI agento veikimo procesas nuo įvesties iki išvesties (angl. _E2E trace_),
eskalacijos demonstracija bei rezultatų vertinimas ir vizualizavimas
(ketinimų tikslumas, _LLM-as-judge_ balai, žingsnių trukmės).

## Ką atlieka DI, o ką — kodas

| DI (Gemini)                                  | Kodas (deterministinis)                       |
| -------------------------------------------- | --------------------------------------------- |
| Užklausos ketinimo klasifikavimas            | Įvesties apdorojimas ir validacija            |
| Atsakymo generavimas lietuvių kalba          | RAG paieška (TF-IDF, simbolių _n_-gramos)     |
| Atsakymo kokybės vertinimas (_LLM-as-judge_) | Slenksčio tikrinimas → eskalacija             |
|                                              | Metrikos, vizualizacijos, rezultatų eksportas |

## Dažniausios klaidos ir sprendimai

| Klaida                   | Priežastis                                                 | Sprendimas                                                |
| ------------------------ | ---------------------------------------------------------- | --------------------------------------------------------- |
| `API key not valid`      | neteisingas raktas / neįjungta parinktis „Notebook access“ | patikrinkite „Secrets“ įrašą ir prieigos teisę            |
| `429 RESOURCE_EXHAUSTED` | viršytas nemokamo plano limitas                            | palaukite ~1 min., pakartokite; sumažinkite testų skaičių |
| `503 / 504`              | modelis laikinai perkrautas                                | paleiskite langelį dar kartą                              |
| `PERMISSION_DENIED`      | raktas neturi „Gemini API“ prieigos teisės                 | sukurkite naują raktą „AI Studio“ aplinkoje               |

Užrašinė veikia ir be rakto — tuomet naudojami atsarginiai (angl. _fallback_) variantai,
kad demonstracija nenutrūktų.

## Modelių apžvalga (visos dalys)

Visi 1.3–1.5 modeliai sprendžia **tą patį uždavinį** — kliento užklausos
(X) ketinimo klasifikavimą (y) pagal 6 klases — ir yra vertinami ta pačia kanonine
6 užklausų aibe, todėl rezultatus galima tiesiogiai palyginti:

| Dalis  | Modelis                                              | Įvestis                                     |
| ------ | ---------------------------------------------------- | ------------------------------------------- |
| 1.1    | „Gemini“ _zero-shot_ + RAG                           | tekstas (neapmokytas)                       |
| 1.3(a) | „TensorFlow“ Dense/MLP                               | TF-IDF požymiai                             |
| 1.3(b) | TF-DF „RandomForest“ + GBT                           | neapdorotas tekstas                         |
| 1.4    | CNN 2×Conv                                           | 64×256 pustonių (angl. _grayscale_) vaizdas |
| 1.5(a) | „MobileNetV2“ (užšaldyta bazė) + galvutė             | 96×96 RGB vaizdas                           |
| 1.5(b) | tas pats po tiksliojo derinimo (angl. _fine-tuning_) | 96×96 RGB vaizdas                           |
