Wichtige Vorabbemerkung zu Zendesk Light Agents: Light Agents sind in der Suite Professional bereits inklusive — sie zahlen keine zusätzliche Lizenz. Bei Rovo hingegen wird jeder User mit Zugriff berechnet, also auch die 100 Light Agents.


Zendesk Native AI

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

KOSTEN1[Suite Professional: 40 x $115 = $4.600/Monat
        Light Agents: inklusive in Suite Professional
        Copilot Add-on: 40 x $50 = $2.000/Monat
        Automated Resolutions: Ø 50/Agent x $2 = $4.000/Monat
        ─────────────────────────────────────
        Gesamt/Monat: ~$10.600
        Gesamt/Jahr:  ~$127.200]

        
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
KOSTEN2[Einmalig Extraktion 111k Tickets: ~$255
        Azure AI Search S1: ~$74/Monat
        Azure OpenAI Inference: ~$30/Monat (hochgerechnet 40 Agenten)
        Zendesk Suite Professional: 40 x $115 = $4.600/Monat
        Light Agents: inklusive
        ─────────────────────────────────────
        Zendesk-Lizenz/Jahr: ~$55.200
        Azure-Kosten/Jahr:   ~$1.300
        Gesamt/Jahr:         ~$56.500]


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
KOSTEN3[Zendesk Suite Professional: 40 x $115 = $4.600/Monat
        Light Agents: inklusive
        ─────────────────────────────────────
        Hardware 2x RTX 6000 Ada + Server: ~22.000 EUR einmalig
        Abschreibung 3 Jahre: ~7.000 EUR/Jahr
        RZ-Hosting Holland: ~4.800 EUR/Jahr
        Strom: ~2.000 EUR/Jahr
        ─────────────────────────────────────
        Zendesk-Lizenz/Jahr:    ~$55.200
        KI-Infrastruktur Jahr 1: ~13.800 EUR
        KI-Infrastruktur ab J2:  ~6.800 EUR
        Gesamt Jahr 1:          ~$70.800
        Gesamt ab Jahr 2:       ~$63.200]


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
KOSTEN4[Zendesk Suite Professional: 40 x $115 = $4.600/Monat
        Light Agents: inklusive
        ─────────────────────────────────────
        Kein Hardware-Investment
        Hetzner On-Demand GPU: ~350 EUR/Monat
        ─────────────────────────────────────
        Zendesk-Lizenz/Jahr:  ~$55.200
        Hetzner/Jahr:         ~4.200 EUR
        Gesamt/Jahr:          ~$59.600]


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
KOSTEN5[JSM Premium: 140 User x $47/Monat = $6.580/Monat
        Rovo Add-on: 140 User x $16/Monat = $2.240/Monat
        ─────────────────────────────────────
        Gesamt/Monat: ~$8.820
        Gesamt/Jahr:  ~$105.840
        ─────────────────────────────────────
        Credit-Limit Premium: 70 Interaktionen/User/Monat
        Bei 140 Usern: 9.800 Credits/Monat gesamt
        Zusaetzliche Credits: separat berechnet]


Direktvergleich KI-Infrastruktur (
```mermaid
***
config:
  xyChart:
    width: 900
    height: 500
***
xychart-beta
    title "Jaehrliche KI-Infrastrukturkosten im Vergleich (ohne Zendesk-Lizenz)"
    x-axis ["Weg 1 Zendesk Native", "Weg 2 Azure", "Weg 3 Server J1", "Weg 3 Server J2+", "Weg 4 Hetzner", "Weg 5 Rovo/JSM"]
    y-axis "Kosten USD/Jahr" 0 --> 80000
    bar 
```

