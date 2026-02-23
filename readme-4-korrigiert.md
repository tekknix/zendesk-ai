# KI-Support-Strategie: Variantenvergleich

> **Hinweis Zendesk Light Agents:** Light Agents sind in der Suite Professional bereits inklusive — keine zusätzliche Lizenz.  
> **Hinweis Rovo:** Jeder User mit Zugriff wird berechnet, also auch alle 100 Light Agents. Rovo-Abfragen sind auf den jeweiligen Zugriffsbereich des Agenten beschränkt — Tickets außerhalb seines Bereichs werden nicht berücksichtigt.

---

## Weg 1 — Zendesk Native AI

> **Empfehlung:** Nur geeignet wenn kein eigenes technisches Know-how vorhanden ist und L1-Tickets dominieren. Für L2/L3-lastigen Support wie Enreach/Swyx **nicht empfohlen** — teuerste Option ohne Zugriff auf historische Ticket-Lösungen.

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk empfängt Ticket]
    B --> C[Zendesk App<br>Agent öffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text an<br>Azure OpenAI API]
    D -->|HTTPS| E[Azure AI Search<br>Suche in 111k Tickets]
    E --> F[Top-3 ähnliche<br>Lösungen gefunden]
    F --> G[Azure OpenAI<br>Antwortvorschlag]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket gelöst])
    K --> L

    B -.->|Später automatisch| M[Ticket analysiert<br>bei Eingang]
    M --> N[Vorschlag als<br>interner Kommentar]

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style M fill:#f0f0f0,color:#333
    style N fill:#f0f0f0,color:#333
```

### Kosten Weg 1

| Position | Kosten |
|----------|--------|
| Suite Professional | 40 × 115 EUR = 4.600 EUR/Monat |
| Copilot Add-on | 40 × 50 EUR = 2.000 EUR/Monat |
| Automated Resolutions | 50/Agent × 2 EUR = 3.390 EUR/Monat |
| Light Agents | inklusive |
| **Gesamt pro Monat** | **ca. 10.600 EUR** |
| **Gesamt pro Jahr** | **ca. 127.200 EUR** |

---

## Weg 1b — DialoX via Flask-Server (RZ Holland)

> **Empfehlung:** Guter Zwischenschritt als schneller Proof-of-Concept, da Webhook-Integration bereits vorhanden ist. Kein RAG bedeutet jedoch: Bei steigender Ticket-Menge sinkt die Antwortqualität, da kein semantisches Retrieval stattfindet. Geeignet für Help-Center-Artikel als Wissensbasis. Für historische Tickets nur mit Limit sinnvoll. Mittelfristig durch Weg 2 oder 4 ersetzen.

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk empfängt Ticket]
    B -->|Webhook| C[Flask-Server RZ Holland<br>erstellt User + Konversation]
    C -->|DialoX API| D[DialoX Routing-Layer]
    D -->|Azure OpenAI API| E[LLM-Inference<br>auf Artikel-Kontext]
    E --> F{Antwort<br>gefunden?}
    F -->|Ja| G[Antwortvorschlag<br>via Webhook zurück]
    F -->|Nein / Unsicher| H[Ticket wird<br>Agent zugewiesen]
    G --> I[Vorschlag im<br>Zendesk App-Panel]
    I -->|Gut genug| J[Agent sendet<br>an Kunde]
    I -->|Anpassen| K[Agent editiert<br>und sendet]
    H --> L[Agent bearbeitet<br>manuell]
    J --> M([Ticket gelöst])
    K --> M
    L --> M

    subgraph RZ [RZ Holland - bestehende Infrastruktur]
        C
    end

    subgraph AZURE [Azure - bestehende Instanz]
        D
        E
    end

    subgraph WISSEN [Wissensbasis - kein RAG]
        Z1[Help Center Artikel<br>als Prompt-Kontext] --> E
        Z2[Tickets optional<br>begrenzte Anzahl] --> E
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style I fill:#E8A838,color:#333
    style M fill:#27AE60,color:#fff
    style H fill:#E85858,color:#fff
    style RZ fill:#eef5ff,color:#333
    style AZURE fill:#f0f4ff,color:#333
    style WISSEN fill:#f9f0d0,color:#333
```

### Kosten Weg 1b

| Position | Kosten |
|----------|--------|
| Flask-Server RZ Holland | bereits vorhanden |
| DialoX Lizenz | bestehender Vertrag |
| Azure OpenAI Inference | bestehende Instanz |
| Zusätzliche Infrastruktur | keine |
| Einrichtungsaufwand | gering - Webhook vorhanden |
| RAG / Vektorsuche | nicht vorhanden |
| Wissensbasis | Artikel + opt. Tickets als Kontext |
| Zusatzkosten pro Monat | nur Azure Inference ~25 EUR |
| **Gesamt pro Jahr inkl. Zendesk** | **ca. 55.500 EUR** |

---

## Weg 2 — Azure Full Stack

> **Empfehlung:** **Beste Option für den Einstieg.** Infrastruktur ist bereits vorhanden, Extraktion läuft, minimales Hardware-Risiko. Ideal für die ersten 6-12 Monate bis die Qualität validiert ist.

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk empfängt Ticket]
    B --> C[Zendesk App<br>Agent öffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text an<br>Azure OpenAI API]
    D -->|HTTPS| E[Azure AI Search<br>Suche in 111k Tickets]
    E --> F[Top-3 ähnliche<br>Lösungen gefunden]
    F --> G[Azure OpenAI<br>Antwortvorschlag]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket gelöst])
    K --> L

    subgraph PHASE2 [Später: Automatisch bei Eingang]
        B --> M[Ticket analysiert<br>bei Eingang]
        M --> N[Vorschlag als<br>interner Kommentar]
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style PHASE2 fill:#f0f0f0,color:#333
```

### Kosten Weg 2

| Position | Kosten |
|----------|--------|
| Extraktion 111k Tickets | 255 EUR einmalig |
| Azure AI Search S1 | 74 EUR/Monat |
| Azure OpenAI Inference | 30 EUR/Monat |
| Light Agents | inklusive |
| **KI-Kosten pro Jahr** | **ca. 1.300 EUR** |
| **Gesamt pro Jahr inkl. Zendesk** | **ca. 56.500 EUR** |

---

## Weg 3 — Eigener Server RZ Holland

> **Empfehlung:** Sinnvoll ab Jahr 2 wenn Weg 2 validiert ist und weitere KI-Anwendungsfälle (z.B. Sprach-Transkription, interne Tools) hinzukommen. Volle Datenkontrolle, höchste DSGVO-Sicherheit, aber signifikantes Hardware-Investment und Betriebsaufwand.

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk empfängt Ticket]
    B --> C[Zendesk App<br>Agent öffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text<br>via HTTPS API]
    D -->|intern| E[RZ Holland<br>Qdrant Vektorsuche]
    E --> F[Top-3 Lösungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral / Qwen2.5<br>RTX 6000 Ada]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket gelöst])
    K --> L

    subgraph INFRA [On-Premise RZ Holland]
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

### Kosten Weg 3

| Position | Kosten |
|----------|--------|
| 2× RTX 6000 Ada + Server | 22.000 EUR einmalig |
| Abschreibung 3 Jahre | 7.000 EUR/Jahr |
| RZ-Hosting Holland | 4.800 EUR/Jahr |
| Strom ca. 2kW | 2.000 EUR/Jahr |
| **KI-Infrastruktur Jahr 1** | **13.800 EUR** |
| **KI-Infrastruktur ab Jahr 2** | **6.800 EUR** |
| **Gesamt Jahr 1 inkl. Zendesk** | **ca. 69.200 EUR** |
| **Gesamt ab Jahr 2 inkl. Zendesk** | **ca. 62.200 EUR** |

---

## Weg 4 — Hetzner GPU-Cloud

> **Empfehlung:** **Beste Langzeit-Option** nach Validierung mit Weg 2. Kein Hardware-Investment, DSGVO-konform in Deutschland, On-Demand skalierbar. Migration von Weg 2 auf Weg 4 erfordert keine Änderungen an der Zendesk App.

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[Zendesk empfängt Ticket]
    B --> C[Zendesk App<br>Agent öffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text<br>via HTTPS API]
    D -->|HTTPS| E[Hetzner GPU-Cloud DE<br>Qdrant Vektorsuche]
    E --> F[Top-3 Lösungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral / Qwen2.5<br>Hetzner GPU]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket gelöst])
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

### Kosten Weg 4

| Position | Kosten |
|----------|--------|
| Hardware-Investment | keines |
| On-Demand GPU | ca. 350 EUR/Monat |
| Dauerbetrieb GEX130 | ca. 1.094 EUR/Monat |
| Bürozeiten GEX44 | ca. 102 EUR/Monat |
| **KI-Infrastruktur On-Demand/Jahr** | **ca. 4.200 EUR** |
| **Gesamt pro Jahr inkl. Zendesk** | **ca. 59.600 EUR** |

---

## Weg 5 — Atlassian Rovo

> **Empfehlung:** Nur sinnvoll wenn eine **vollständige Migration von Zendesk auf JSM** geplant ist. Credit-Limit von 70 Interaktionen/User/Monat ist für produktiven Support unzureichend. Rovo berücksichtigt nur Tickets im Zugriffsbereich des jeweiligen Agenten — kein globaler Wissenspool.

```mermaid
flowchart TD
    A([Ticket eingeht]) --> B[JSM empfängt Ticket]
    B --> C[Agent öffnet Ticket<br>in Jira]
    C --> D[Rovo Chat<br>im Jira-Panel]
    D --> E[Rovo durchsucht<br>Ticket-Index]
    E --> F[Rovo durchsucht<br>Jira + Confluence]
    F --> G{Lösung gefunden?}
    G -->|Ja| H[Rovo-Vorschlag<br>im Jira-Panel]
    G -->|Nein| X([Keine Antwort<br>oder Halluzination])
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    X --> J[Agent manuell]
    I --> L([Ticket gelöst])
    K --> L
    J --> L

    subgraph ROVO [Atlassian Rovo - Cloud]
        E
        F
        G
    end

    subgraph IMPORT [Datengrundlage - einmalig importiert]
        Z1[Importierte Zendesk-Tickets] --> E
        Z2[Jira + Confluence-Artikel] --> F
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style X fill:#E85858,color:#fff
    style ROVO fill:#f0f0f0,color:#333
    style IMPORT fill:#eef5ff,color:#333
```

### Kosten Weg 5

| Position | Kosten |
|----------|--------|
| JSM Premium | 140 User × 47 EUR = 6.580 EUR/Monat |
| Rovo Add-on | 140 User × 16 EUR = 2.240 EUR/Monat |
| **Gesamt pro Monat** | **ca. 8.820 EUR** |
| **Gesamt pro Jahr** | **ca. 105.840 EUR** |
| Credit-Limit Premium | 70 Abfragen/User/Monat |
| Credits 140 User gesamt | 9.800 Credits/Monat |
| Zusätzliche Credits | separat berechnet |

---

## Direktvergleich KI-Infrastruktur

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#20808D'}}}%%
xychart-beta
    title "KI-Infrastrukturkosten pro Jahr (ohne Zendesk/JSM-Lizenz)"
    x-axis [W1-Zendesk, W1b-DialoX, W2-Azure, W3-Jahr1, W3-Jahr2+, W4-Hetzner, W5-Rovo]
    y-axis "EUR pro Jahr" 0 --> 80000
    bar [72000, 300, 1300, 13800, 6800, 4200, 50640]
```

---

## Direktvergleich Gesamtkosten inkl. Zendesk / JSM-Lizenzen

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#27AE60'}}}%%
xychart-beta
    title "Gesamtkosten pro Jahr (inkl. Zendesk/JSM-Lizenzen)"
    x-axis [W1-Zendesk, W1b-DialoX, W2-Azure, W3-Jahr1, W3-Jahr2+, W4-Hetzner, W5-Rovo]
    y-axis "EUR pro Jahr" 0 --> 140000
    bar [127200, 55500, 56500, 69200, 62200, 59600, 105840]
```

---

## Zusammenfassung und Empfehlung

| Weg | Jahr 1 Kosten | Ab Jahr 2 | Vorteile | Nachteile |
|-----|---------------|-----------|----------|-----------|
| **Weg 1 – Zendesk Native** | 127.200 EUR | 127.200 EUR | Keine IT-Ressourcen nötig | Teuerste Option, kein Zugriff auf historische Tickets |
| **Weg 1b – DialoX** | 55.500 EUR | 55.500 EUR | Schnell umsetzbar, minimale Zusatzkosten | Kein RAG, begrenzte Skalierbarkeit |
| **Weg 2 – Azure** | 56.500 EUR | 56.500 EUR | ✅ **Beste Einstiegsoption**, minimales Risiko | Langfristig teurer als Weg 4 |
| **Weg 3 – RZ Holland** | 69.200 EUR | 62.200 EUR | Volle Kontrolle, DSGVO-maximal | Hohes Investment, Betriebsaufwand |
| **Weg 4 – Hetzner** | 59.600 EUR | 59.600 EUR | ✅ **Beste Langzeitoption**, flexibel skalierbar | Abhängigkeit von Hetzner |
| **Weg 5 – Rovo** | 105.840 EUR | 105.840 EUR | Atlassian-Integration | Teuer, Credit-Limits, nur JSM |

### Empfohlener Pfad

1. **Start:** Weg 2 (Azure Full Stack) für 6-12 Monate zur Validierung
2. **Langfristig:** Migration auf Weg 4 (Hetzner GPU-Cloud) nach erfolgreicher Validierung
3. **Alternative:** Weg 1b (DialoX) als schneller PoC, wenn sofortige Umsetzung benötigt wird