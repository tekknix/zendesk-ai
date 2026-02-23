```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk Intelligent Triage<br>Klassifizierung + Routing]
    B --> C{Help Center<br>Artikel vorhanden?}
    C -->|Ja| D[AI Agent antwortet<br>automatisch]
    C -->|Nein| E[Ticket wird<br>Agent zugewiesen]
    D --> F{Kunde zufrieden?<br>72h kein Reply}
    F -->|Ja| G[Automated Resolution<br>$2 verrechnet]
    F -->|Nein / Eskalation| E
    E --> H[Agent sieht Ticket<br>+ Copilot-Vorschlaege]
    H --> I[Agent antwortet manuell]
    I --> J([Ticket geloest])

    style A fill:#20808D,color:#fff
    style G fill:#E8A838,color:#fff
    style J fill:#27AE60,color:#fff
    style D fill:#2EA8B5,color:#fff
    style H fill:#2EA8B5,color:#fff
```
```mermaid
flowchart TD
    A([Ticket eingeht in Zendesk]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[App sendet Ticket-Text<br>an Azure OpenAI API]
    D -->|HTTPS API| E[Azure AI Search<br>Vektor-Suche in 111k Tickets]
    E --> F[Top-3 aehnliche<br>Loesungen gefunden]
    F --> G[Azure OpenAI<br>generiert Antwortvorschlag]
    G --> H[Vorschlag im<br>Zendesk App-Panel]
    H -->|Gut genug| I[Agent uebernimmt<br>und sendet an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph PHASE2 [Spaeter: Automatisch bei Ticket-Eingang]
        B --> M[Ticket auto-analysiert<br>bei Eingang]
        M --> N[Loesungsvorschlag als<br>interner Kommentar]
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style PHASE2 fill:#f0f0f0,color:#333
```
```mermaid
flowchart TD
    A([Ticket eingeht in Zendesk]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[App sendet Ticket-Text<br>via HTTPS API]
    D -->|HTTPS intern| E[Eigener Server RZ Holland<br>Qdrant Vektor-Suche]
    E --> F[Top-3 aehnliche Loesungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral-Small / Qwen2.5<br>auf RTX 6000 Ada]
    G --> H[Vorschlag im<br>Zendesk App-Panel]
    H -->|Gut genug| I[Agent uebernimmt<br>und sendet an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph INFRA [Infrastruktur RZ Holland - On-Premise]
        E
        F
        G
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style INFRA fill:#eef5ff,color:#333
```
```mermaid
flowchart TD
    A([Ticket eingeht in Zendesk]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[App sendet Ticket-Text<br>via HTTPS API]
    D -->|HTTPS| E[Hetzner GPU-Cloud DE<br>Qdrant Vektor-Suche]
    E --> F[Top-3 aehnliche Loesungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral-Small / Qwen2.5<br>auf Hetzner GPU]
    G --> H[Vorschlag im<br>Zendesk App-Panel]
    H -->|Gut genug| I[Agent uebernimmt<br>und sendet an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph HETZNER [Hetzner Deutschland - DSGVO-konform]
        E
        F
        G
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style HETZNER fill:#fff3e0,color:#333
```
```mermaid
flowchart TD
    A([Ticket eingeht in Zendesk]) --> B{Zendesk nach Jira<br>Sync aktiv?}
    B -->|Nein| Z([Rovo hat keinen Zugriff])
    B -->|Ja| C[Ticket wird nach<br>Jira Service Management gespiegelt]
    C --> D[Rovo indiziert Ticket<br>in Atlassian-Vektorindex]
    D --> E[Agent oeffnet Ticket<br>in Jira + Rovo Chat]
    E --> F[Rovo sucht in<br>Jira + Confluence Index]
    F -->|Treffer| G[Rovo-Vorschlag<br>im Jira-Panel]
    F -->|Kein Treffer| X([Keine Antwort<br>oder Halluzination])
    G --> H[Agent kopiert Loesung<br>zurueck nach Zendesk]
    H --> L([Ticket geloest - Doppel-Workflow])

    style A fill:#20808D,color:#fff
    style Z fill:#E85858,color:#fff
    style X fill:#E85858,color:#fff
    style L fill:#E8A838,color:#333
    style B fill:#f0f0f0,color:#333
```
