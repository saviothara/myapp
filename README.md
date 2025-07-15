```mermaid
graph TD
    subgraph Google Chatbot
        A[Google Chat User] -->|Sends Message| B(Google Chat)
        B -->|Event Trigger| C[Google Cloud Function: respond()]
        C --> D["Message Type?"]

        D -->|Direct Message with /setup| E[OAuth Manager]
        E -->|Authorization Link| B

        D -->|Other Direct Message| F[Google Chat Manager]
        F -->|Instructions| B

        D -->|Threaded Message (General)| G[Google Chat Manager]
        G -->|Send 'Bot is thinking...' Card| B
        G -->|Get Space Members & Thread Messages| H[Google Chat API]
        G -->|Retrieve Credentials| I[Google Secret Manager]
        G -->|Prepare Chat History & Call LLM Manager| J[LLM Manager]

        J -->|Retrieve Documents| K[Vertex AI Search Retriever]
        K --> L[Google Cloud Vertex AI Search (Data Store)]

        J -->|Generate Response| M[Vertex AI (LLM)]
        M -->|Uses Prompt Template| N[Prompts]

        J -->|Response| G
        G -->|Update Card with Response| B

        C --> O[App Logging]
        G --> O
        J --> O

        subgraph Error Handling
            P[Error] --> O
            P --> G
            G -->|Send Error Message| B
        end
    end

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style C fill:#ccf,stroke:#333,stroke-width:2px
    style D fill:#fcf,stroke:#333,stroke-width:2px
    style E fill:#fcf,stroke:#333,stroke-width:2px
    style F fill:#fcf,stroke:#333,stroke-width:2px
    style G fill:#fcf,stroke:#333,stroke-width:2px
    style H fill:#ccf,stroke:#333,stroke-width:2px
    style I fill:#ccf,stroke:#333,stroke-width:2px
    style J fill:#fcf,stroke:#333,stroke-width:2px
    style K fill:#ccf,stroke:#333,stroke-width:2px
    style L fill:#bbf,stroke:#333,stroke-width:2px
    style M fill:#ccf,stroke:#333,stroke-width:2px
    style N fill:#fcf,stroke:#333,stroke-width:2px
    style O fill:#ccf,stroke:#333,stroke-width:2px
    style P fill:#fcc,stroke:#333,stroke-width:2px
```
