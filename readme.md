Wichtige Vorabbemerkung zu Zendesk Light Agents: Light Agents sind in der Suite Professional bereits inklusive — sie zahlen keine zusätzliche Lizenz. Bei Rovo hingegen wird jeder User mit Zugriff berechnet, also auch die 100 Light Agents.


Zendesk Native AI

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk Intelligent Triage<br>Klassifizierung + Routing]
    B --> C{Help Center<br>Artikel vorhanden?}
    C -->|Ja| D[AI Agent antwortet<br>automatisch]
    C -->|Nein| E[Ticket wird<br>Agent zugewiesen]
    D --> F{Kunde zufrieden?<br>72h kein Reply}
    F -->|Ja| G[Automated Resolution<br>2€ verrechnet]
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
block-beta
    columns 2
    A["Suite Professional"]:1 B["40 x 115€ = 4.600€/Monat"]:1
    C["Copilot Add-on"]:1 D["40 x 50€ = 2.000€/Monat"]:1
    E["Automated Resolutions"]:1 F["je 50€/Agent x €2 = €4.000/Monat"]:1
    G["Light Agents"]:1 H["inklusive"]:1
    I["Gesamt/Monat"]:1 J["~10600€"]:1
    K["Gesamt/Jahr"]:1 L["~127200€"]:1

    style I fill:#20808D,color:#fff
    style J fill:#20808D,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
```

        
Azure Full Stack
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
block-beta
    columns 2
    A["Extraktion 111k Tickets"]:1 B["~255€ einmalig"]:1
    C["Azure AI Search S1"]:1 D["~74€/Monat"]:1
    E["Azure OpenAI Inference"]:1 F["~30€/Monat"]:1
    G["Light Agents"]:1 H["inklusive"]:1
    I["Azure KI-Kosten/Jahr"]:1 J["~1300€"]:1
    K["Gesamt/Jahr inkl. Zendesk"]:1 L["~56500€"]:1

    style I fill:#20808D,color:#fff
    style J fill:#20808D,color:#fff
    style K fill:#27AE60,color:#fff
    style L fill:#27AE60,color:#fff
```


Eigener Server RZ Holland
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
block-beta
    columns 2
    A["Hardware 2x RTX 6000 Ada + Server"]:1 B["22000€ einmalig"]:1
    C["Abschreibung 3 Jahre"]:1 D["7000€/Jahr"]:1
    E["RZ-Hosting Holland"]:1 F["~4800€/Jahr"]:1
    G["Strom"]:1 H["~2000€/Jahr"]:1
    I["KI-Infrastruktur Jahr 1"]:1 J["~13800€"]:1
    K["KI-Infrastruktur ab Jahr 2"]:1 L["~6800€"]:1
    M["Gesamt Jahr 1 inkl. Zendesk"]:1 N["~70800€"]:1
    O["Gesamt ab Jahr 2 inkl. Zendesk"]:1 P["~63200€"]:1

    style I fill:#E8A838,color:#333
    style J fill:#E8A838,color:#333
    style K fill:#20808D,color:#fff
    style L fill:#20808D,color:#fff
    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
    style O fill:#27AE60,color:#fff
    style P fill:#27AE60,color:#fff
```


Hetzner GPU-Cloud
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
block-beta
    columns 2
    A["Hardware-Investment"]:1 B["keines"]:1
    C["Hetzner On-Demand GPU"]:1 D["~350€/Monat"]:1
    E["Hetzner Dauerbetrieb GEX130"]:1 F["~1094€/Monat"]:1
    G["Hetzner Buerozeiten GEX44"]:1 H["~102€/Monat"]:1
    I["KI-Infrastruktur/Jahr On-Demand"]:1 J["~4200€"]:1
    K["Gesamt/Jahr inkl. Zendesk"]:1 L["~59600€"]:1

    style I fill:#20808D,color:#fff
    style J fill:#20808D,color:#fff
    style K fill:#27AE60,color:#fff
    style L fill:#27AE60,color:#fff
```


Atlassian Rovo
```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Jira Service Management<br>empfaengt Ticket]
    B --> C[Agent oeffnet Ticket<br>in Jira]
    C --> D[Rovo Chat<br>im Jira-Panel]
    D --> E[Rovo durchsucht<br>Ticket-Index]
    E --> F[Rovo durchsucht<br>Jira + Confluence Index]
    F --> G{Passende Loesung<br>gefunden?}
    G -->|Ja| H[Rovo-Vorschlag<br>im Jira-Panel]
    G -->|Nein| X([Keine Antwort<br>oder Halluzination])
    H -->|Gut genug| I[Agent uebernimmt<br>und sendet an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    X --> J[Agent bearbeitet<br>manuell]
    I --> L([Ticket geloest])
    K --> L
    J --> L

    subgraph ROVO [Atlassian Rovo - Cloud]
        E
        F
        G
    end

    subgraph IMPORT [Datengrundlage - einmalig importiert]
        Z1[Importierte Zendesk-Tickets<br>als Wissensbasis] --> E
        Z2[Jira-Tickets +<br>Confluence-Artikel] --> F
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style X fill:#E85858,color:#fff
    style ROVO fill:#f0f0f0,color:#333
    style IMPORT fill:#eef5ff,color:#333
```
```mermaid
block-beta
    columns 2
    A["JSM Premium"]:1 B["140 User x €47 = 6580€/Monat"]:1
    C["Rovo Add-on"]:1 D["140 User x €16 = 2240€/Monat"]:1
    E["Gesamt/Monat"]:1 F["~8820€"]:1
    G["Gesamt/Jahr"]:1 H["~105840€"]:1
    I["Credit-Limit Premium"]:1 J["70 Interaktionen/User/Monat"]:1
    K["Credits gesamt 140 User (ehemalige Zendesk-User"]:1 L["9800 Credits/Monat"]:1
    M["Zusaetzliche Credits"]:1 N["separat berechnet"]:1

    style E fill:#E8A838,color:#333
    style F fill:#E8A838,color:#333
    style G fill:#E85858,color:#fff
    style H fill:#E85858,color:#fff
    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
```


Direktvergleich KI-Infrastruktur (
```mermaid
xychart-beta
    title "KI-Infrastrukturkosten pro Jahr ohne Zendesk-Lizenz"
    x-axis ["Weg 1 Zendesk", "Weg 2 Azure", "Weg 3 J1", "Weg 3 J2+", "Weg 4 Hetzner", "Weg 5 Rovo"]
    y-axis "Euro pro Jahr" 0 --> 80000
    bar [72000, 1300, 13800, 6800, 4200, 50640] 
```


