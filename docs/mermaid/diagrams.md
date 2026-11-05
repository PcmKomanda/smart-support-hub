# Išmanioji klientų aptarnavimo ir žinių bazės sistema — „Mermaid“ diagramos

Šis failas — 1.1 užduoties dalis: „PlantUML“ diagramos perdarytos į **Mermaid** formatą,
kad būtų atvaizduojamos tiesiogiai „GitHub“ platformoje. Semantika ir vardai išlaikyti tie patys
kaip ir `docs/plantuml/` versijoje (kodas anglų kalba, paaiškinimai lietuviški).

## 1. Naudojimo atvejų diagrama (use case → flowchart)

Aktorius („Customer“) užduoda klausimą ir gauna DI sugeneruotą atsakymą;
sistemos viduje vykdomas ketinimo klasifikavimas, RAG paieška ir atsakymo generavimas
per „Gemini API“. Administratorius prižiūri žinių bazę, o klientų aptarnavimo specialistas
sprendžia eskaluotas užklausas.

```mermaid
flowchart LR
    Customer([Customer])
    Agent([Support Agent])
    Admin([Administrator])

    subgraph System["Smart Customer Service & Knowledge Base System"]
        UC1["Ask a question via chat"]
        UC2["Receive AI-generated answer"]
        UC3["Escalate to human agent"]
        UC4["Handle escalated ticket"]
        UC5["Manage knowledge base (CRUD documents)"]
        UC6["Review conversation history & analytics"]
        UC7["Classify intent"]
        UC8["Retrieve knowledge base documents (RAG)"]
        UC9["Generate answer via Gemini API"]
    end

    Customer --> UC1
    Customer --> UC2
    Agent --> UC4
    Agent --> UC6
    Admin --> UC5

    UC1 -.->|include| UC7
    UC7 -.->|include| UC8
    UC8 -.->|include| UC9
    UC9 -.->|include| UC2
    UC1 -.->|extend: AI cannot answer| UC3
    UC3 -.->|extend| UC4
```

## 2. Klasių diagrama (classDiagram)

Pagrindiniai dalykinės srities objektai (`ChatSession`, `Message`) ir DI agento komponentai
(`IntentClassifier`, `RAGRetriever`, `AnswerGenerator`, `LLMClient`) su ryšiais:
pokalbio seansas turi daug žinučių, RAG paieškos modulis pateikia užklausą žinių bazei, atsakymų
generatorius kviečia „Gemini API“ ir tikrina eskalacijos taisykles.

```mermaid
classDiagram
    class ChatSession {
        +String sessionId
        +String customerId
        +DateTime startedAt
        +SessionStatus status
        +addMessage(Message m) void
        +getHistory() List~Message~
    }
    class Message {
        +String messageId
        +String sessionId
        +MessageRole role
        +String content
        +DateTime createdAt
    }
    class IntentClassifier {
        +classify(String text) Intent
    }
    class Intent {
        +String intentId
        +IntentName name
        +float confidence
    }
    class RAGRetriever {
        +int topK
        +retrieve(String query) List~KBDocument~
    }
    class KnowledgeBase {
        +search(String query) List~KBDocument~
        +addDocument(KBDocument doc) void
        +updateDocument(String id, KBDocument doc) void
        +deleteDocument(String id) void
    }
    class KBDocument {
        +String documentId
        +String title
        +String content
        +String category
        +float[] embedding
    }
    class LLMClient {
        -String apiKey
        +String model
        +generate(String prompt, List~KBDocument~ context) String
    }
    class AnswerGenerator {
        +buildPrompt(String query, List~KBDocument~ context) String
        +generateAnswer(ChatSession session, List~KBDocument~ docs) String
    }
    class EscalationService {
        +shouldEscalate(Intent intent, List~KBDocument~ docs) boolean
        +createTicket(ChatSession session) Ticket
    }
    class Ticket {
        +String ticketId
        +String sessionId
        +String assignedAgentId
        +Priority priority
    }
    class AnalyticsLogger {
        +logInteraction(ChatSession session, Intent intent, int latencyMs) void
        +exportReport() Report
    }

    ChatSession "1" *-- "0..*" Message : contains
    ChatSession ..> AnswerGenerator : uses
    IntentClassifier ..> Intent : produces
    RAGRetriever ..> KnowledgeBase : queries
    RAGRetriever ..> KBDocument : returns
    AnswerGenerator ..> LLMClient : calls
    AnswerGenerator ..> RAGRetriever : uses
    AnswerGenerator ..> EscalationService : checks
    EscalationService ..> Ticket : creates
    AnalyticsLogger ..> ChatSession : observes
    KnowledgeBase "1" o-- "1..*" KBDocument : stores
```

## 3. Sekų diagrama (sequenceDiagram)

Pilno ciklo (angl. *End-to-End*) eiga: kliento klausimas → ketinimo klasifikavimas → RAG paieška
žinių bazėje → atsakymo generavimas per „Gemini API“ → atsakymo pateikimas.
Jei žinių bazėje tinkamų dokumentų nerandama, vykdoma eskalacija žmogaus operatoriui
(`alt` fragmentas). Kiekviena sąveika užregistruojama analitikos žurnale.

```mermaid
sequenceDiagram
    actor Customer
    participant ChatUI
    participant IntentClassifier
    participant RAGRetriever
    participant KnowledgeBase
    participant AnswerGenerator
    participant LLMClient as LLMClient (Gemini API)
    participant EscalationService
    participant AnalyticsLogger
    participant Agent as Support Agent

    Customer->>ChatUI: ask(question)
    ChatUI->>IntentClassifier: classify(question)
    IntentClassifier-->>ChatUI: Intent(name, confidence)
    ChatUI->>RAGRetriever: retrieve(question)
    RAGRetriever->>KnowledgeBase: search(question)
    KnowledgeBase-->>RAGRetriever: List<KBDocument>
    RAGRetriever-->>ChatUI: List<KBDocument> (top-K)

    alt KB documents found and confidence OK
        ChatUI->>AnswerGenerator: generateAnswer(question, docs)
        AnswerGenerator->>LLMClient: generate(prompt with context)
        LLMClient-->>AnswerGenerator: answer text
        AnswerGenerator-->>ChatUI: Answer + source references
        ChatUI-->>Customer: display answer
    else No relevant documents
        ChatUI->>EscalationService: createTicket(session)
        EscalationService->>Agent: notify new ticket
        Agent-->>Customer: human agent answers
        EscalationService-->>ChatUI: ticketId
    end

    ChatUI->>AnalyticsLogger: logInteraction(session, intent, latency)
    AnalyticsLogger-->>ChatUI: ok
```

## 4. Veiklos diagrama (activity → flowchart)

Užklausos apdorojimo eiga su trimis sprendimo taškais: (a) ar ketinimas
klasifikuotas pakankamai tiksliai, (b) ar žinių bazėje rasti tinkami
dokumentai, (c) ar sugeneruotas atsakymas praeina kokybės patikrą.
Neigiamu atveju — eskalacija ir pranešimas klientų aptarnavimo specialistui.

```mermaid
flowchart TD
    A([Start]) --> B[Customer submits question via ChatUI]
    B --> C[Validate and sanitize input]
    C --> D{Intent classified with confidence >= threshold?}
    D -- yes --> E[IntentClassifier returns Intent]
    D -- no --> F[Fall back to general FAQ intent]
    E --> G[RAGRetriever searches KnowledgeBase top-K]
    F --> G
    G --> H{Relevant documents found?}
    H -- yes --> I[AnswerGenerator builds prompt: question + retrieved context]
    I --> J[LLMClient calls Gemini API]
    J --> K[Post-check answer: grounded in context?]
    K --> L{Answer passes quality check?}
    L -- yes --> M[Return answer with source references]
    L -- no --> N[EscalationService creates ticket]
    H -- no --> N
    N --> O[Notify human Support Agent]
    M --> P[AnalyticsLogger logs interaction: intent, latency, outcome]
    O --> P
    P --> Q[Display result to Customer]
    Q --> R([Stop])
```

## 5. Komponentų diagrama (component → flowchart su sluoksniais)

Sistemos sluoksniai: naudotojo sąsaja (pokalbių langas), DI agento sluoksnis
(ketinimų klasifikatorius, RAG paieškos modulis, atsakymų generatorius, eskalacijų
tarnyba), duomenų sluoksnis (žinių bazė su vektoriniu indeksu, pokalbių
istorija, analitikos saugykla) ir išorinės paslaugos („Gemini API“).

```mermaid
flowchart TB
    subgraph UILayer["UI Layer"]
        ChatUI["ChatUI (web chat widget)"]
    end
    subgraph AILayer["AI Agent Layer"]
        Classifier["IntentClassifier"]
        Retriever["RAGRetriever"]
        Generator["AnswerGenerator"]
        Escalations["EscalationService"]
    end
    subgraph DataLayer["Data Layer"]
        KB["KnowledgeBase + Vector Index"]
        History[("Conversation History DB")]
        Analytics["Analytics Store"]
    end
    subgraph External["External Services"]
        Gemini["Gemini API"]
    end

    ChatUI -->|question| Classifier
    ChatUI -->|query| Retriever
    Classifier -->|intent| Generator
    Retriever -->|search| KB
    Retriever -->|context documents| Generator
    Generator -->|prompt / completion| Gemini
    Generator -->|answer| ChatUI
    Generator -->|unanswerable request| Escalations
    Escalations -->|create ticket| History
    ChatUI -->|persist messages| History
    Generator -->|log interaction| Analytics
```
