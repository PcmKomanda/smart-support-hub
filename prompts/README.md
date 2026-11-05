# `prompts/` direktorija — agento ir testavimo užklausos (angl. *prompts*)

Užduoties **1.2.1** dalis: čia pateikiamos testavimo užklausos (angl. *prompts*), kurias pateikus
kalbos modelio agentui („ChatGPT“ arba „Gemini“), gaunamas atsakymas į kliento klausimą —
tai pilno ciklo (angl. *End-to-End*) demonstracija.

## Failai

| Failas | Paskirtis |
|---|---|
| `system_prompt.txt` | DI agento sisteminė instrukcija (taisyklės, kalba, formatas, eskalacija) |
| `test_prompt_1.txt` | E2E testas: užsakymo būsenos klausimas (KB-001) |
| `test_prompt_2.txt` | E2E testas: grąžinimo klausimas (KB-003) |
| `test_prompt_3.txt` | E2E testas: pristatymo klausimas (KB-002) |
| `test_prompt_4.txt` | E2E testas: eskalacijos atvejis — žinių bazėje nėra atsakymo |

## Kaip atlikti E2E testą

1. Atidarykite „ChatGPT“ / „Google AI Studio“ arba paleiskite „Colab“ prototipą
   (`notebooks/smart_customer_service_gemini.ipynb`).
2. Nukopijuokite `system_prompt.txt` turinį kaip sisteminę žinutę.
3. Iš pasirinkto testavimo failo nukopijuokite KONTEKSTĄ ir KLIENTO
   UŽKLAUSĄ (X) į naudotojo žinutę.
4. Palyginkite agento atsakymą su LAUKIAMU ATSAKYMU (y) iš to failo.

Keturi testai apima tris sėkmingus RAG atsakymo atvejus ir vieną
eskalacijos atvejį, kai žinių bazėje nėra reikiamos informacijos.
