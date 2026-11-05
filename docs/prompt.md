# 1.1 dalis — „GLM 5.3“ užklausa (angl. _prompt_) sistemos sukūrimui

Šiame faile pateikiama užklausa (angl. _prompt_), kuri buvo pateikta GLM 5.3 modeliui siekiant sugeneruoti
„Išmaniosios klientų aptarnavimo ir žinių bazės sistemos“ projektą pradžioje naudojant **PlantUML** diagramas,
o vėliau perdarius jas į **Mermaid** formatą (žr. `mermaid/` direktoriją).

---

## Užklausa („PlantUML“ versijai)

```text
Tu esi patyręs programinės įrangos architektas ir UML modeliavimo ekspertas.

Sukurk išsamią informacinės sistemos, kuri naudoja generatyvųjį dirbtinį intelektą,
projektinę dokumentaciją tema:

„Išmanioji klientų aptarnavimo ir žinių bazės sistema“ (Smart Customer Service
and Knowledge Base System).

Sistemos kontekstas:
- Klientas užduoda klausimus elektronikos parduotuvės „TechStore“ klientų aptarnavimo
  kanale (užsakymų būsena, pristatymas, grąžinimai, apmokėjimas, gaminio
  naudojimas, garantijos).
- Sistema naudoja LLM („GLM API“ — GLM 5.3 modelį) atsakymams generuoti.
- Sistema klasifikuoja užklausos ketinimą (intent), atsakymą grąžina remdamasi
  vidine žinių baze (Knowledge Base), naudoja RAG (Retrieval-Augmented
  Generation) principą.
- Jei sistema negali atsakyti, užklausa perduodama (escalation) žmogaus
  operatoriui.
- Visos užklausos saugomos istorijoje analitikai ir tobulinimui.

Prašau sugeneruoti šias PlantUML diagramas su angliškais pavadinimais
(kodas anglų kalba, komentarai gali būti lietuviški):

1. Use case (naudojimo atvejų) diagrama — aktoriai: Customer, Support Agent,
   Administrator; su sistemos ribomis (system boundary).
2. Klasių (class) diagrama — pagrindiniai komponentai: ChatSession, Message,
   IntentClassifier, KnowledgeBase, KBDocument, RAGRetriever, LLMClient,
   AnswerGenerator, EscalationService, AnalyticsLogger.
3. Sekų (sequence) diagrama — End-to-End pranešimų eiga: Customer → ChatUI →
   IntentClassifier → RAGRetriever → KnowledgeBase → LLMClient → AnswerGenerator,
   įskaitant eskalacijos (escalation) atvejį.
4. Veiklos (activity) diagrama — užklausos apdorojimo eiga su sprendimų
   taškais (ar rastas ketinimas? ar rasti KB dokumentai? ar reikalinga
   eskalacija?).
5. Komponentų (component) diagrama — sluoksniai: UI, AI Agent sluoksnis
   (classification, retrieval, generation), duomenų sluoksnis (KB, istorija,
   analitika), išorinės paslaugos (GLM API / Vector DB).
6. Diegimo (deployment) diagrama, jei tinka.

Kiekvienai diagramai pateik trumpą paaiškinimą lietuvių kalba (1–2 sakiniai),
kokie yra jos elementai ir kodėl pasirinkta būtent tokia struktūra.
Diagramų elementų vardus rašyk anglų kalba.
```

---

## Užklausa (perdarymui į „Mermaid“)

````text
Paimk anksčiau sugeneruotas „Išmaniosios klientų aptarnavimo ir žinių bazės
sistemos“ UML diagramas (use case, class, sequence, activity, component) ir
perdaryk jas į Mermaid formatą, kuris veikia GitHub Markdown faile:

- Use case → Mermaid `flowchart` (aktoriai kaip mazgai, sistemos ribos kaip
  subgraph);
- Class → Mermaid `classDiagram` su tais pačiais vardais ir ryšiais
  (association, composition, multiplicity);
- Sequence → Mermaid `sequenceDiagram` su tais pačiais dalyviais (participants) ir
  žinutėmis, įskaitant alt fragmentą eskalacijos atvejui;
- Activity → Mermaid `flowchart` su sprendimų (decision diamond) mazgais
  (intent found? / docs found? / escalate?);
- Component → Mermaid `flowchart` su subgraph sluoksniais (UI Layer, AI Agent
  Layer, Data Layer, External Services).

Reikalavimai:
- Išlaikyk tą pačią semantiką ir vardus kaip PlantUML versijoje.
- Kodas anglų kalba, be specialių simbolių, kurie laužytų Mermaid sintaksę.
- Pateik kopijuojamus ```mermaid``` blokus.
````

---

## Gautų rezultatų apibendrinimas

GLM 5.3 sugeneruotos diagramos peržiūrėtos, patikslintos ir patalpintos:

- `docs/plantuml/` — penkios „PlantUML“ diagramos (naudojimo atvejų, klasių,
  sekų, veiklos, komponentų).
- `docs/mermaid/diagrams.md` — tos pačios diagramos „Mermaid“ formatu,
  atvaizduojamos tiesiogiai „GitHub“ platformoje.
