# mq-message-consumer

```mermaid
flowchart TD
    A[JMS Queue Listener] -->|Step 1: Receive Message| B[JMS Transaction]
    B --> C[Message Processing Flow]
    C -->|1. Enrichment Service<br>2. Validation Service| D[Deduplication Check]
    D -->|Check Message ID<br>- If Found: Ignore and Exit<br>- If Not Found: Proceed| E[JPA Transaction]
    E -->|Step 2: Save to Database<br>- Insert Data into Application Table<br>- Insert Message ID into Deduplication Table| F[End of JPA Transaction Boundary]
    F --> G[JMS Acknowledgment (Tx)]
    G -->|Step 3: Acknowledge JMS Message<br>- JMS Transaction Commit<br>- If JMS Commit Fails: Message Redelivery| H[End]
```

### Steps to Use It
1. Copy the above code snippet.
2. Paste it into your `README.md` file.
3. Ensure that your platform supports rendering Mermaid diagrams (like GitHub or other Markdown viewers that support it).

This will make the diagram visually appealing and easier to understand while maintaining a clear process flow. Let me know if you need further assistance!



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
