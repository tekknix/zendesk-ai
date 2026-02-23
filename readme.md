flowchart TD
    A([Ticket eingeht]) --> B[Zendesk Intelligent Triage\nKlassifizierung + Routing]
    B --> C{Help Center\nArtikel vorhanden?}
    C -->|Ja| D[AI Agent antwortet\nautomatisch]
    C -->|Nein| E[Ticket wird\nAgent zugewiesen]
    D --> F{Kunde zufrieden?\n72h kein Reply}
    F -->|Ja| G[Automated Resolution\n$2 verrechnet]
    F -->|Nein / Eskalation| E
    E --> H[Agent sieht Ticket\n+ Copilot-Vorschlaege]
    H --> I[Agent antwortet manuell]
    I --> J([Ticket geloest])

    style A fill:#20808D,color:#fff
    style G fill:#E8A838,color:#fff
    style J fill:#27AE60,color:#fff
    style D fill:#2EA8B5,color:#fff
    style H fill:#2EA8B5,color:#fff


flowchart TD
    A([Ticket eingeht in Zendesk]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App\nAgent oeffnet Ticket]
    C -->|App-Aufruf| D[App sendet Ticket-Text\nan Azure OpenAI API]
    D -->|HTTPS API| E[Azure AI Search\nVektor-Suche in 111k Tickets]
    E --> F[Top-3 aehnliche\nLoesungen gefunden]
    F --> G[Azure OpenAI\ngeneriert Antwortvorschlag]
    G --> H[Vorschlag im\nZendesk App-Panel]
    H -->|Gut genug| I[Agent uebernimmt\nund sendet an Kunde]
    H -->|Anpassen| K[Agent editiert\nund sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph PHASE2 [Spaeter: Automatisch bei Ticket-Eingang]
        B --> M[Ticket auto-analysiert\nbei Eingang]
        M --> N[Loesungsvorschlag als\ninterner Kommentar]
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style PHASE2 fill:#f0f0f0,color:#333



flowchart TD
    A([Ticket eingeht in Zendesk]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App\nAgent oeffnet Ticket]
    C -->|App-Aufruf| D[App sendet Ticket-Text\nvia HTTPS API]
    D -->|HTTPS intern| E[Eigener Server RZ Holland\nQdrant Vektor-Suche]
    E --> F[Top-3 aehnliche Loesungen\naus 111k Tickets]
    F --> G[Lokales LLM\nMistral-Small / Qwen2.5\nauf RTX 6000 Ada]
    G --> H[Vorschlag im\nZendesk App-Panel]
    H -->|Gut genug| I[Agent uebernimmt\nund sendet an Kunde]
    H -->|Anpassen| K[Agent editiert\nund sendet]
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



flowchart TD
    A([Ticket eingeht in Zendesk]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App\nAgent oeffnet Ticket]
    C -->|App-Aufruf| D[App sendet Ticket-Text\nvia HTTPS API]
    D -->|HTTPS| E[Hetzner GPU-Cloud DE\nQdrant Vektor-Suche]
    E --> F[Top-3 aehnliche Loesungen\naus 111k Tickets]
    F --> G[Lokales LLM\nMistral-Small / Qwen2.5\nauf Hetzner GPU]
    G --> H[Vorschlag im\nZendesk App-Panel]
    H -->|Gut genug| I[Agent uebernimmt\nund sendet an Kunde]
    H -->|Anpassen| K[Agent editiert\nund sendet]
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



flowchart TD
    A([Ticket eingeht in Zendesk]) --> B{Zendesk nach Jira\nSync aktiv?}
    B -->|Nein| Z([Rovo hat keinen Zugriff])
    B -->|Ja| C[Ticket wird nach\nJira Service Management gespiegelt]
    C --> D[Rovo indiziert Ticket\nin Atlassian-Vektorindex]
    D --> E[Agent oeffnet Ticket\nin Jira + Rovo Chat]
    E --> F[Rovo sucht in\nJira + Confluence Index]
    F -->|Treffer| G[Rovo-Vorschlag\nim Jira-Panel]
    F -->|Kein Treffer| X([Keine Antwort\noder Halluzination])
    G --> H[Agent kopiert Loesung\nzurueck nach Zendesk]
    H --> L([Ticket geloest - Doppel-Workflow])

    style A fill:#20808D,color:#fff
    style Z fill:#E85858,color:#fff
    style X fill:#E85858,color:#fff
    style L fill:#E8A838,color:#333
    style B fill:#f0f0f0,color:#333
