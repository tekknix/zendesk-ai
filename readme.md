# KI-Support-Strategie: Variantenvergleich

> **Hinweis Zendesk Light Agents:** Light Agents sind in der Suite Professional bereits inklusive — keine zusätzliche Lizenz.  
> **Hinweis Rovo:** Jeder User mit Zugriff wird berechnet, also auch alle 100 Light Agents. Rovo-Abfragen sind auf den jeweiligen Zugriffsbereich des Agenten beschränkt — Tickets ausserhalb seines Bereichs werden nicht beruecksichtigt.

---

## Weg 1 — Zendesk Native AI

> **Empfehlung:** Nur geeignet wenn kein eigenes technisches Know-how vorhanden ist und L1-Tickets dominieren. Fuer L2/L3-lastigen Support wie Enreach/Swyx **nicht empfohlen** — teuerste Option ohne Zugriff auf historische Ticket-Loesungen.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Intelligent Triage<br>Klassifizierung + Routing]
    B --> C{Help Center<br>Artikel vorhanden?}
    C -->|Ja| D[AI Agent antwortet<br>automatisch]
    C -->|Nein| E[Ticket wird<br>Agent zugewiesen]
    D --> F{Kunde zufrieden?<br>72h kein Reply}
    F -->|Ja| G[Automated Resolution<br>1.70 EUR pro Ticket]
    F -->|Nein / Eskalation| E
    E --> H[Agent sieht Ticket<br>+ Copilot-Vorschlag]
    H --> I[Agent antwortet manuell]
    I --> J([Ticket geloest])

    style A fill:#20808D,color:#fff
    style G fill:#E8A838,color:#fff
    style J fill:#27AE60,color:#fff
    style D fill:#2EA8B5,color:#fff
    style H fill:#2EA8B5,color:#fff

block-beta
    columns 2
    A["Suite Professional"]:1 B["40 x 115 EUR = 4600 EUR/Mon"]:1
    C["Copilot Add-on"]:1 D["40 x 50 EUR = 2000 EUR/Mon"]:1
    E["Automated Resolutions"]:1 F["50/Agent x 2 EUR = 3.390 EUR/Mon"]:1
    G["Light Agents"]:1 H["inklusive"]:1
    I["Gesamt pro Monat"]:1 J["ca. 10.600 EUR"]:1
    K["Gesamt pro Jahr"]:1 L["ca. 127.200 EUR"]:1

    style I fill:#20808D,color:#fff
    style J fill:#20808D,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
```

Weg 1a — Flask-Server direkt via Azure OpenAI (RZ Holland)
Empfehlung: Schnellster und schlankster Einstieg als Proof-of-Concept. Webhook-Integration bereits vorhanden, kein zusaetzlicher Middleware-Layer. Flask-Code ruft Azure OpenAI direkt auf — volle Kontrolle, minimale Latenz, keine DialoX-Lizenzabhaengigkeit. Kein RAG bedeutet: Bei steigender Ticket-Menge sinkt die Antwortqualitaet, da kein semantisches Retrieval stattfindet. Geeignet fuer Help-Center-Artikel als Wissensbasis. Fuer historische Tickets nur mit Limit sinnvoll. Mittelfristig durch Weg 2 oder 4 ersetzen.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B -->|Webhook| C[Flask-Server RZ Holland<br>bereitet Prompt vor]
    C -->|Azure OpenAI API direkt| E[LLM-Inference<br>auf Artikel-Kon]
    E --> F{Antwort<br>gefunden?}
    F -->|Ja| G[Antwortvorschlag<br>via Webhook zurueck]
    F -->|Nein / Unsicher| H[Ticket wird<br>Agent zugewiesen]
    G --> I[Vorschlag im<br>Zendesk App-Panel]
    I -->|Gut genug| J[Agent sendet<br>an Kunde]
    I -->|Anpassen| K[Agent editiert<br>und sendet]
    H --> L[Agent bearbeitet<br>manuell]
    J --> M([Ticket geloest])
    K --> M
    L --> M

    subgraph RZ [RZ Holland - bestehende Infrastruktur]
        C
    end

    subgraph AZURE [Azure - bestehende Instanz]
        E
    end

    subgraph WISSEN [Wissensbasis - kein RAG]
        Z1[Help Center Artikel<br>als Prompt-Kon] --> E
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

block-beta
    columns 2
    A["Flask-Server RZ Holland"]:1 B["bereits vorhanden"]:1
    C["DialoX Lizenz"]:1 D["nicht benoetigt"]:1
    E["Azure OpenAI Inference"]:1 F["bestehende Instanz"]:1
    G["Zusaetzliche Infrastruktur"]:1 H["keine"]:1
    I["Einrichtungsaufwand"]:1 J["minimal - direkter API-Call"]:1
    K["RAG / Vektorsuche"]:1 L["nicht vorhanden"]:1
    M["Wissensbasis"]:1 N["Artikel + opt. Tickets als Kon"]:1
    O["Zusatzkosten pro Monat"]:1 P["nur Azure Inference ~25 EUR"]:1
    Q["Gesamt pro Jahr inkl. Zendesk"]:1 R["ca. 55.200 EUR"]:1

    style I fill:#27AE60,color:#fff
    style J fill:#27AE60,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
    style Q fill:#20808D,color:#fff
    style R fill:#20808D,color:#fff
```

Weg 1b — DialoX als Middleware-Layer via Flask-Server (RZ Holland)
Empfehlung: Nur sinnvoll wenn DialoX aktiv fuer weitere Dialog-Features genutzt wird (Multi-Bot-Routing, Session-Management). Als reiner Pass-Through zu Azure OpenAI bringt DialoX keinen architektonischen Mehrwert gegenueber Weg 1a — ein zusaetzlicher API-Hop, eine zusaetzliche Abhaengigkeit. DialoX ruft Azure OpenAI selbststaendig auf; kein eigener API-Call im Flask-Code noetig. Mittelfristig durch Weg 2 oder 4 ersetzen.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B -->|Webhook| C[Flask-Server RZ Holland<br>erstellt User + Konversation]
    C -->|DialoX API| D[DialoX Routing-Layer]
    D -->|Azure OpenAI API| E[LLM-Inference<br>auf Artikel-Kon]
    E --> F{Antwort<br>gefunden?}
    F -->|Ja| G[Antwortvorschlag<br>via Webhook zurueck]
    F -->|Nein / Unsicher| H[Ticket wird<br>Agent zugewiesen]
    G --> I[Vorschlag im<br>Zendesk App-Panel]
    I -->|Gut genug| J[Agent sendet<br>an Kunde]
    I -->|Anpassen| K[Agent editiert<br>und sendet]
    H --> L[Agent bearbeitet<br>manuell]
    J --> M([Ticket geloest])
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
        Z1[Help Center Artikel<br>als Prompt-Kon] --> E
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

block-beta
    columns 2
    A["Flask-Server RZ Holland"]:1 B["bereits vorhanden"]:1
    C["DialoX Lizenz"]:1 D["bestehender Vertrag"]:1
    E["Azure OpenAI Inference"]:1 F["bestehende Instanz"]:1
    G["Zusaetzliche Infrastruktur"]:1 H["keine"]:1
    I["Einrichtungsaufwand"]:1 J["gering - Webhook vorhanden"]:1
    K["RAG / Vektorsuche"]:1 L["nicht vorhanden"]:1
    M["Wissensbasis"]:1 N["Artikel + opt. Tickets als Kon"]:1
    O["Zusatzkosten pro Monat"]:1 P["nur Azure Inference ~25 EUR"]:1
    Q["Gesamt pro Jahr inkl. Zendesk"]:1 R["ca. 55.500 EUR"]:1

    style I fill:#27AE60,color:#fff
    style J fill:#27AE60,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
    style Q fill:#20808D,color:#fff
    style R fill:#20808D,color:#fff
```

Weg 2 — Azure Full Stack (RAG)
Wichtiger Hinweis: Weg 2 basiert auf RAG (Retrieval-Augmented Generation) ueber Azure AI Search — kein Fine-Tuning. Das bedeutet: Das Basis-Modell (z.B. GPT-4o) wird nicht veraendert oder neu trainiert. Stattdessen werden deine 111k Tickets als Vektorindex in Azure AI Search gespeichert und bei jeder Anfrage semantisch durchsucht. Die Daten (Vektorindex) liegen vollstaendig bei Azure. Ein Fine-Tuning waere ein grundlegend anderes Szenario mit deutlich hoeheren Kosten (~1.224 EUR/Monat nur fuer Modell-Hosting, unabhaengig von der Nutzung) und ist hier ausdruecklich nicht vorgesehen.

Empfehlung: Beste Option fuer den Einstieg. Infrastruktur ist bereits vorhanden, Extraktion laeuft, minimales Hardware-Risiko. Ideal fuer die ersten 6-12 Monate bis die Qualitaet validiert ist.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[Ticket- an<br>Azure OpenAI API]
    D -->|HTTPS| E[Azure AI Search<br>Suche in 111k Tickets]
    E --> F[Top-3 aehnliche<br>Loesungen gefunden]
    F --> G[Azure OpenAI<br>Antwortvorschlag]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph PHASE2 [Spaeter: Automatisch bei Eingang]
        B --> M[Ticket analysiert<br>bei Eingang]
        M --> N[Vorschlag als<br>interner Kommentar]
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style PHASE2 fill:#f0f0f0,color:#333

block-beta
    columns 2
    A["Extraktion 111k Tickets"]:1 B["255 EUR einmalig"]:1
    C["Embedding (Vektorisierung einmalig)"]:1 D["ca. 10-20 EUR einmalig"]:1
    E["Azure AI Search S1"]:1 F["74 EUR/Monat"]:1
    G["Azure OpenAI Inference"]:1 H["30 EUR/Monat"]:1
    I["Light Agents"]:1 J["inklusive"]:1
    K["Hinweis: kein Fine-Tuning"]:1 L["Basis-Modell, kein Modell-Hosting noetig"]:1
    M["KI-Kosten pro Jahr"]:1 N["ca. 1.300 EUR"]:1
    O["Gesamt pro Jahr inkl. Zendesk"]:1 P["ca. 56.500 EUR"]:1

    style M fill:#20808D,color:#fff
    style N fill:#20808D,color:#fff
    style O fill:#27AE60,color:#fff
    style P fill:#27AE60,color:#fff
    style K fill:#E8A838,color:#333
    style L fill:#E8A838,color:#333
```

Weg 3 — Eigener Server RZ Holland
Empfehlung: Sinnvoll ab Jahr 2 wenn Weg 2 validiert ist und weitere KI-Anwendungsfaelle (z.B. Sprach-Transkription, interne Tools) hinzukommen. Volle Datenkontrolle, hoechste DSGVO-Sicherheit, aber signifikantes Hardware-Investment und Betriebsaufwand.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[Ticket-<br>via HTTPS API]
    D -->|intern| E[RZ Holland<br>Qdrant Vektorsuche]
    E --> F[Top-3 Loesungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral / Qwen2.5<br>RTX 6000 Ada]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
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

block-beta
    columns 2
    A["2x RTX 6000 Ada + Server"]:1 B["22.000 EUR einmalig"]:1
    C["Abschreibung 3 Jahre"]:1 D["7.000 EUR/Jahr"]:1
    E["RZ-Hosting Holland"]:1 F["4.800 EUR/Jahr"]:1
    G["Strom ca. 2kW"]:1 H["2.000 EUR/Jahr"]:1
    I["KI-Infrastruktur Jahr 1"]:1 J["13.800 EUR"]:1
    K["KI-Infrastruktur ab Jahr 2"]:1 L["6.800 EUR"]:1
    M["Gesamt Jahr 1 inkl. Zendesk"]:1 N["ca. 69.200 EUR"]:1
    O["Gesamt ab Jahr 2 inkl. Zendesk"]:1 P["ca. 62.200 EUR"]:1

    style I fill:#E8A838,color:#333
    style J fill:#E8A838,color:#333
    style K fill:#20808D,color:#fff
    style L fill:#20808D,color:#fff
    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
    style O fill:#27AE60,color:#fff
    style P fill:#27AE60,color:#fff
```

Weg 4 — Hetzner GPU-Cloud
Empfehlung: Beste Langzeit-Option nach Validierung mit Weg 2. Kein Hardware-Investment, DSGVO-konform in Deutschland, On-Demand skalierbar. Migration von Weg 2 auf Weg 4 erfordert keine Aenderungen an der Zendesk App.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[Ticket-<br>via HTTPS API]
    D -->|HTTPS| E[Hetzner GPU-Cloud DE<br>Qdrant Vektorsuche]
    E --> F[Top-3 Loesungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral / Qwen2.5<br>Hetzner GPU]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
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

block-beta
    columns 2
    A["Hardware-Investment"]:1 B["keines"]:1
    C["On-Demand GPU"]:1 D["ca. 350 EUR/Monat"]:1
    E["Dauerbetrieb GEX130"]:1 F["ca. 1094 EUR/Monat"]:1
    G["Buerozeiten GEX44"]:1 H["ca. 102 EUR/Monat"]:1
    I["KI-Infrastruktur On-Demand/Jahr"]:1 J["ca. 4.200 EUR"]:1
    K["Gesamt pro Jahr inkl. Zendesk"]:1 L["ca. 59.600 EUR"]:1

    style I fill:#20808D,color:#fff
    style J fill:#20808D,color:#fff
    style K fill:#27AE60,color:#fff
    style L fill:#27AE60,color:#fff
```

Weg 5 — Atlassian Rovo
Empfehlung: Nur sinnvoll wenn eine vollstaendige Migration von Zendesk auf JSM geplant ist. Credit-Limit von 70 Interaktionen/User/Monat ist fuer produktiven Support unzureichend. Rovo beruecksichtigt nur Tickets im Zugriffsbereich des jeweiligen Agenten — kein globaler Wissenspool.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[JSM empfaengt Ticket]
    B --> C[Agent oeffnet Ticket<br>in Jira]
    C --> D[Rovo Chat<br>im Jira-Panel]
    D --> E[Rovo durchsucht<br>Ticket-Index]
    E --> F[Rovo durchsucht<br>Jira + Confluence]
    F --> G{Loesung gefunden?}
    G -->|Ja| H[Rovo-Vorschlag<br>im Jira-Panel]
    G -->|Nein| X([Keine Antwort<br>oder Halluzination])
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    X --> J[Agent manuell]
    I --> L([Ticket geloest])
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

block-beta
    columns 2
    A["JSM Premium"]:1 B["140 User x 47 EUR = 6.580 EUR/Mon"]:1
    C["Rovo Add-on"]:1 D["140 User x 16 EUR = 2.240 EUR/Mon"]:1
    E["Gesamt pro Monat"]:1 F["ca. 8.820 EUR"]:1
    G["Gesamt pro Jahr"]:1 H["ca. 105.840 EUR"]:1
    I["Credit-Limit Premium"]:1 J["70 Abfragen/User/Monat"]:1
    K["Credits 140 User gesamt"]:1 L["9.800 Credits/Monat"]:1
    M["Zusaetzliche Credits"]:1 N["separat berechnet"]:1

    style E fill:#E8A838,color:#333
    style F fill:#E8A838,color:#333
    style G fill:#E85858,color:#fff
    style H fill:#E85858,color:#fff
    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
```

Direktvergleich KI-Infrastruktur

```mermaid
xychart-beta
    title "KI-Infrastrukturkosten pro Jahr ohne Zendesk-Lizenz (EUR)"
    x-axis ["W1 Zendesk", "W1a Direkt", "W1b DialoX", "W2 Azure", "W3 Jahr 1", "W3 ab Jahr 2", "W4 Hetzner", "W5 Rovo"]
    y-axis "EUR pro Jahr" 0 --> 80000
    bar
```
```mermaid
Direktvergleich Gesamtkosten inkl. Zendesk / Jira-Lizenzen

xychart-beta
    title "Gesamtkosten pro Jahr inkl. Zendesk-Lizenz (EUR)"
    x-axis ["W1 Zendesk", "W1a Direkt", "W1b DialoX", "W2 Azure", "W3 Jahr 1", "W3 ab Jahr 2", "W4 Hetzner", "W5 Rovo"]
    y-axis "EUR pro Jahr" 0 --> 180000
    bar 
```

***

Die wesentlichen Änderungen im Überblick:

- **Weg 1a** ist jetzt der direkte Flask → Azure OpenAI Weg (ohne DialoX, schlankster Ansatz, leicht günstigere Jahreskosten da keine DialoX-Abhängigkeit)
- **Weg 1b** ist der bisherige DialoX-Weg mit klargestelltem Hinweis, wann er sinnvoll ist
- **Weg 2** hat jetzt einen expliziten Hinweis: RAG, kein Fine-Tuning, inkl. Erklärung warum Fine-Tuning (~1.224 EUR/Monat Hosting allein) ausdrücklich nicht vorgesehen ist[1]
- Einmalige Embedding-Kosten (~10–20 EUR) für die Vektorisierung der 111k Tickets wurden ergänzt
- Die Vergleichsdiagramme am Ende enthalten jetzt beide 1a- und 1b-Balken
