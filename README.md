graph TD subgraph Google Chatbot A[Google Chat User] --> B(Google Chat) B -->|Sends Message| C[Google Cloud Function: respond()] C -->|Event Trigger| D{Message Type?}

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
        G -->|"Send Error Message"| B
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

**Explanation of the Diagram:**

*   **Google Chat User**: Initiates interaction by sending messages to the Google Chatbot.
*   **Google Chat**: The platform where the user interacts with the bot.
*   **Google Cloud Function: `respond()`**: This is the main entry point for all incoming Google Chat events. It's triggered by messages from Google Chat.
*   **Message Type?**: A decision point within the `respond()` function to handle different types of messages.
    *   **Direct Message with `/setup`**: If the user sends a direct message with the `/setup` command, the **OAuth Manager** is invoked to initiate the authorization flow, providing a link back to Google Chat.
    *   **Other Direct Message**: For other direct messages, the **Google Chat Manager** sends an informative message back to the user.
    *   **Threaded Message (General)**: For regular threaded messages, the **Google Chat Manager** first sends a "Bot is thinking..." card to the chat. It then retrieves space members and thread messages from the **Google Chat API**.
    *   **Google Secret Manager**: Used by the **Google Chat Manager** to retrieve necessary credentials (likely for user authorization to read threads).
    *   **LLM Manager**: This is the core component for processing the user's query.
        *   **Vertex AI Search Retriever**: The LLM Manager uses this to retrieve relevant documents from the **Google Cloud Vertex AI Search (Data Store)**, which acts as the knowledge base for the bot.
        *   **Vertex AI (LLM)**: The LLM Manager sends the user's message, chat history, and retrieved documents to the Vertex AI Large Language Model (e.g., Gemini) to generate a response.
        *   **Prompts**: The LLM uses predefined prompt templates to guide its response generation.
    *   Once the LLM generates a response, the **LLM Manager** sends it back to the **Google Chat Manager**, which then updates the "Bot is thinking..." card with the actual response in Google Chat.
*   **App Logging**: All significant events and actions are logged for monitoring and debugging.
*   **Error Handling**: If any errors occur during processing, they are logged, and an appropriate error message is sent back to the user in Google Chat.

This diagram illustrates the flow of information and the interaction between various Google Cloud services and the Python application components to provide the Google Chatbot functionality.
