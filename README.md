# mq-message-consumer

```mermaid
graph TD;
    A[JMS Queue Listener] --> B[JMS Transaction];
    B --> C[Message Processing Flow];
    C --> D[Enrichment Service];
    C --> E[Validation Service];
    D --> F[Deduplication Check];
    E --> F[Deduplication Check];
    F --> G{Deduplication Table Check};
    G -->|Found| H[Ignore and Exit];
    G -->|Not Found| I[Proceed];
    I --> J[JPA Transaction];
    J --> K[Save to Database];
    K --> L[Insert Data into Application Table];
    K --> M[Insert Message ID into Deduplication Table];
    K --> N[Commit Database Transaction];
    N --> O{Database Commit Success};
    O -->|Success| P[End of JPA Transaction Boundary];
    O -->|Failure| Q[Rollback JPA Transaction];
    Q --> P;
```



+-----------------------------------------------+
|                 JMS Queue Listener            |
|-----------------------------------------------|
|  Step 1: Receive Message --> JMS Transaction  |
|  - JMS Transaction Starts                     |
+-----------------------------------------------+
                |
                v
+-----------------------------------------------+
|              Message Processing Flow          |
|-----------------------------------------------|
|  1. Enrichment Service                        |
|  2. Validation Service                        |
+-----------------------------------------------+
                |
                v
+-----------------------------------------------+
|             Deduplication Check               |
|-----------------------------------------------|
|  - Check Message ID in Deduplication Table    |
|  - If Found: Ignore and Exit                  |
|  - If Not Found: Proceed                      |
+-----------------------------------------------+
                |
                v
+-----------------------------------------------+
|                JPA Transaction                |
|-----------------------------------------------|
|  Step 2: Save to Database                     |
|  - Insert Data into Application Table         |
|  - Insert Message ID into Deduplication Table |
|  - Commit Database Transaction                |
|  - If Database Commit Fails: Rollback JPA Tx  |
+-----------------------------------------------+
                |
                v
+-----------------------------------------------+
|       End of JPA Transaction Boundary         |
+-----------------------------------------------+
                |
                v
+-----------------------------------------------+
|             JMS Acknowledgment (Tx)           |
|-----------------------------------------------|
|  Step 3: Acknowledge JMS Message              |
|  - JMS Transaction Commit                     |
|  - If JMS Commit Fails: Message Redelivery    |
+-----------------------------------------------+
