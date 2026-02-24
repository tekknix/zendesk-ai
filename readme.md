# KI-Support-Strategie: Variantenvergleich

> **Hinweis Zendesk Light Agents:** Light Agents sind in der Suite Professional bereits inklusive — keine zusaetzliche Lizenz.  
> **Hinweis Rovo:** Jeder User mit Zugriff wird berechnet, also auch alle 100 Light Agents. Rovo-Abfragen sind auf den jeweiligen Zugriffsbereich des Agenten beschraenkt — Tickets ausserhalb seines Bereichs werden nicht beruecksichtigt.

---

## Wissensbasis — Kritische Grundlage fuer alle Wege

> **Wichtiger Hinweis zur Datenqualitaet:** Die vorhandenen Help-Center-Artikel sind **stark Swyx-lastig** und decken die Produkte der Communications-Sparte nur unzureichend ab. Eine rein artikel-basierte Wissensbasis liefert daher fuer einen Grossteil der eingehenden Tickets **keine relevanten Antworten**.

> **Ticketbasis:** Gesamt ~180.000 Tickets im System. Fuer die KI-Verarbeitung relevant: **ca. 111.000 Tickets** (mehr als 2 Antworten, nicht aelter als 5 Jahre). Nur diese gefilterte Basis liefert qualitativ verwertbare Loesungsmuster.

| Wissensbasis | Quelle | Qualitaet | Abdeckung |
|---|---|---|---|
| Nur Help-Center-Artikel | Swyx-lastig, lueckenhaft | Gering | L1-Tickets, Swyx-Produkte |
| Artikel + RAG ueber 111k Tickets | Gefilterte Ticketbasis | Hoch | L1/L2/L3, alle Produkte |

**Fazit:** Echter Mehrwert entsteht **nur durch RAG** ueber die gefilterten 111k Tickets — unabhaengig davon, ob Azure AI Search, Qdrant auf eigenem Server oder Qdrant bei Hetzner genutzt wird. Alle Wege ohne RAG sind als **temporaere PoC-Loesung** zu verstehen.

---

## Weg 1 — Zendesk Native AI

> **Empfehlung:** Nur geeignet wenn kein eigenes technisches Know-how vorhanden ist und L1-Tickets dominieren. Fuer L2/L3-lastigen Support wie Enreach/Swyx **nicht empfohlen** — teuerste Option ohne Zugriff auf historische Ticket-Loesungen. Wissensbasis beschraenkt auf Help-Center-Artikel (Swyx-lastig, keine Communications-Produkte).

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
```

```mermaid
block-beta
    columns 2
    A["Suite Professional"]:1 B["40 x 115 EUR = 4.600 EUR/Mon"]:1
    C["Copilot Add-on"]:1 D["40 x 50 EUR = 2.000 EUR/Mon"]:1
    E["Automated Resolutions"]:1 F["50/Agent x 2 EUR = 3.390 EUR/Mon"]:1
    G["Light Agents"]:1 H["inklusive"]:1
    I["Gesamt pro Monat"]:1 J["ca. 10.600 EUR"]:1
    K["Gesamt pro Jahr"]:1 L["ca. 127.200 EUR"]:1

    style I fill:#20808D,color:#fff
    style J fill:#20808D,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
```

---

## Weg 1a — Flask-Server direkt via Azure OpenAI (RZ Holland)

> **Empfehlung:** Schnellster und schlankster Einstieg als Proof-of-Concept. Webhook-Integration bereits vorhanden, kein zusaetzlicher Middleware-Layer. Flask-Code ruft Azure OpenAI direkt auf — volle Kontrolle, minimale Latenz, keine DialoX-Lizenzabhaengigkeit.  
> **Wissensbasis:** Help-Center-Artikel als Prompt-Kontext + optional wenige historische Tickets. Antwortqualitaet bei Communications-Produkten eingeschraenkt. Kein RAG. **Nur als PoC bis Weg 2 produktiv ist.**  
> **Inference-Kalkulation:** ~800 Input-Token (System-Prompt + Artikel + Ticket-Text) + ~500 Output-Token. GPT-4o: ~6 EUR/Monat bei 1.000 Anfragen/Monat (~77 EUR/Jahr).

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B -->|Webhook| C[Flask-Server RZ Holland<br>bereitet Prompt vor]
    C -->|Azure OpenAI API direkt| E[LLM-Inference<br>auf Artikel-Kontext]
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

```mermaid
block-beta
    columns 2
    A["Flask-Server RZ Holland"]:1 B["bereits vorhanden"]:1
    C["DialoX Lizenz"]:1 D["nicht benoetigt"]:1
    E["Azure OpenAI Inference (800/500 Token)"]:1 F["~6 EUR/Monat (1.000 Anfragen)"]:1
    G["Zusaetzliche Infrastruktur"]:1 H["keine"]:1
    I["Einrichtungsaufwand"]:1 J["minimal - direkter API-Call"]:1
    K["RAG / Vektorsuche"]:1 L["nicht vorhanden"]:1
    M["Wissensbasis"]:1 N["Nur Artikel (Swyx-lastig) + opt. wenige Tickets"]:1
    O["KI-Kosten pro Jahr (1k Anfragen/Mon)"]:1 P["ca. 77 EUR"]:1
    Q["Gesamt pro Jahr inkl. Zendesk"]:1 R["ca. 55.277 EUR"]:1

    style I fill:#27AE60,color:#fff
    style J fill:#27AE60,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
    style Q fill:#20808D,color:#fff
    style R fill:#20808D,color:#fff
```

---

## Weg 1b — DialoX als Middleware-Layer via Flask-Server (RZ Holland)

> **Empfehlung:** Nur sinnvoll wenn DialoX aktiv fuer weitere Dialog-Features genutzt wird (Multi-Bot-Routing, Session-Management). Als reiner Pass-Through zu Azure OpenAI kein architektonischer Mehrwert gegenueber Weg 1a — ein zusaetzlicher API-Hop, eine zusaetzliche Abhaengigkeit.  
> **Wissensbasis:** Ausschliesslich Help-Center-Artikel — DialoX kann die 111k Tickets volumenseitig nicht verarbeiten. Strukturell vergleichbar mit Weg 1 (Zendesk Native). **Nur als PoC geeignet.**  
> **Inference-Kalkulation:** Gleich wie Weg 1a — ~800 Input-Token + ~500 Output-Token. GPT-4o: ~6 EUR/Monat bei 1.000 Anfragen/Monat (~77 EUR/Jahr).

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B -->|Webhook| C[Flask-Server RZ Holland<br>erstellt User + Konversation]
    C -->|DialoX API| D[DialoX Routing-Layer]
    D -->|Azure OpenAI API| E[LLM-Inference<br>auf Artikel-Kontext]
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

    subgraph WISSEN [Wissensbasis - NUR Help-Center-Artikel, kein RAG]
        Z1[Help Center Artikel<br>als Prompt-Kontext] --> E
        Z2[Tickets: nicht moeglich<br>Volumen zu gross fuer DialoX] --> E
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

```mermaid
block-beta
    columns 2
    A["Flask-Server RZ Holland"]:1 B["bereits vorhanden"]:1
    C["DialoX Lizenz"]:1 D["bestehender Vertrag"]:1
    E["Azure OpenAI Inference (800/500 Token)"]:1 F["~6 EUR/Monat (1.000 Anfragen)"]:1
    G["Zusaetzliche Infrastruktur"]:1 H["keine"]:1
    I["Einrichtungsaufwand"]:1 J["gering - Webhook vorhanden"]:1
    K["RAG / Vektorsuche"]:1 L["nicht vorhanden"]:1
    M["Wissensbasis"]:1 N["NUR Artikel (Swyx-lastig), keine Tickets moeglich"]:1
    O["KI-Kosten pro Jahr (1k Anfragen/Mon)"]:1 P["ca. 77 EUR"]:1
    Q["Gesamt pro Jahr inkl. Zendesk"]:1 R["ca. 55.577 EUR"]:1

    style I fill:#27AE60,color:#fff
    style J fill:#27AE60,color:#fff
    style K fill:#E85858,color:#fff
    style L fill:#E85858,color:#fff
    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
    style Q fill:#20808D,color:#fff
    style R fill:#20808D,color:#fff
```

---

## Weg 2 — Azure RAG (Full Stack)

> **Wichtiger Hinweis:** Der Name "Full Stack" ist erst dann zutreffend, wenn **Azure AI Search mit Vektorindex aktiv laeuft** — nur dann findet echtes semantisches Retrieval statt. Ohne Search-Komponente waere Weg 2 strukturell identisch mit Weg 1a.  
> **Kein Fine-Tuning:** Das Basis-Modell (z.B. GPT-4o) wird nicht veraendert oder trainiert. RAG liefert Wissen dynamisch zur Laufzeit. Fine-Tuning wuerde ein statisches, schnell veraltetes Modell erzeugen und ~1.224 EUR/Monat reines Hosting kosten — hier **nicht vorgesehen**.  
> **Wissensbasis:** 111k gefilterte Tickets (>2 Antworten, <5 Jahre) als Vektorindex + Help-Center-Artikel. Erstmals vollstaendige Abdeckung aller Produkte inkl. Communications.  
> **Inference-Kalkulation:** ~4.000 Input-Token/Anfrage (Ticket-Text + RAG-Kontext Top-3) + ~1.500 Output-Token. GPT-4o Global: $2,50/1M Input, $10,00/1M Output (Stand Feb 2026).

> **Empfehlung:** **Beste Option fuer den Einstieg mit echter KI-Qualitaet.** Infrastruktur bereits vorhanden, Extraktion laeuft, minimales Hardware-Risiko. Ideal fuer die ersten 6-12 Monate.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text an<br>Azure OpenAI API]
    D -->|HTTPS| E[Azure AI Search<br>Vektorsuche in 111k Tickets]
    E --> F[Top-3 aehnliche<br>Loesungen gefunden]
    F --> G[Azure OpenAI<br>Antwortvorschlag]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph AZURE_FULL [Azure Full Stack - RAG aktiv]
        D
        E
        F
        G
    end

    subgraph WISSEN [Wissensbasis - RAG aktiv]
        Z1[111k Tickets als Vektorindex<br>gefiltert: >2 Antworten, <5 Jahre] --> E
        Z2[Help Center Artikel<br>als zusaetzlicher Kontext] --> G
    end

    subgraph PHASE2 [Spaeter: Automatisch bei Eingang]
        B --> M[Ticket analysiert<br>bei Eingang]
        M --> N[Vorschlag als<br>interner Kommentar]
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style AZURE_FULL fill:#f0f4ff,color:#333
    style WISSEN fill:#e8f5e9,color:#333
    style PHASE2 fill:#f0f0f0,color:#333
```

```mermaid
block-beta
    columns 2
    A["Extraktion 111k Tickets"]:1 B["255 EUR einmalig"]:1
    C["Embedding / Vektorisierung (einmalig)"]:1 D["ca. 10-20 EUR einmalig"]:1
    E["Azure AI Search S1 (Vektorindex-Hosting)"]:1 F["74 EUR/Monat (Fixkosten)"]:1
    G["Inference GPT-4o 4k Input + 1.5k Output"]:1 H["~23 EUR/Monat (1.000 Anfragen)"]:1
    I["Inference GPT-4o skaliert"]:1 J["~69 EUR/Monat (3.000 Anfragen)"]:1
    K["Inference GPT-4o skaliert"]:1 L["~115 EUR/Monat (5.000 Anfragen)"]:1
    M["Hinweis: kein Fine-Tuning"]:1 N["Basis-Modell, kein Modell-Hosting noetig"]:1
    O["KI-Kosten/Jahr konservativ 1k Anfragen/Mon"]:1 P["ca. 1.440 EUR"]:1
    Q["KI-Kosten/Jahr realistisch 3k Anfragen/Mon"]:1 R["ca. 2.000 EUR"]:1
    S["Gesamt/Jahr inkl. Zendesk konservativ"]:1 T["ca. 56.640 EUR"]:1
    U["Gesamt/Jahr inkl. Zendesk realistisch"]:1 V["ca. 57.200 EUR"]:1

    style O fill:#20808D,color:#fff
    style P fill:#20808D,color:#fff
    style Q fill:#20808D,color:#fff
    style R fill:#20808D,color:#fff
    style S fill:#27AE60,color:#fff
    style T fill:#27AE60,color:#fff
    style U fill:#27AE60,color:#fff
    style V fill:#27AE60,color:#fff
    style M fill:#E8A838,color:#333
    style N fill:#E8A838,color:#333
```

---

## Weg 3 — Eigener Server RZ Holland (RAG)

> **Empfehlung:** Sinnvoll ab Jahr 2 wenn Weg 2 validiert ist und weitere KI-Anwendungsfaelle (z.B. Sprach-Transkription, interne Tools) hinzukommen. Volle Datenkontrolle, hoechste DSGVO-Sicherheit, aber signifikantes Hardware-Investment und Betriebsaufwand.  
> **Wissensbasis:** 111k gefilterte Tickets als Vektorindex in Qdrant (On-Premise) + Help-Center-Artikel. Gleiche RAG-Qualitaet wie Weg 2, keine variablen Inference-Kosten da lokales LLM.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text<br>via HTTPS API]
    D -->|intern| E[RZ Holland<br>Qdrant Vektorsuche]
    E --> F[Top-3 Loesungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral / Qwen2.5<br>RTX 6000 Ada]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph INFRA [On-Premise RZ Holland - RAG aktiv]
        E
        F
        G
    end

    subgraph WISSEN [Wissensbasis - RAG aktiv]
        Z1[111k Tickets als Vektorindex<br>Qdrant On-Premise] --> E
        Z2[Help Center Artikel<br>als zusaetzlicher Kontext] --> G
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style INFRA fill:#eef5ff,color:#333
    style WISSEN fill:#e8f5e9,color:#333
```

```mermaid
block-beta
    columns 2
    A["2x RTX 6000 Ada + Server"]:1 B["22.000 EUR einmalig"]:1
    C["Abschreibung 3 Jahre"]:1 D["7.000 EUR/Jahr"]:1
    E["RZ-Hosting Holland"]:1 F["4.800 EUR/Jahr"]:1
    G["Strom ca. 2kW"]:1 H["2.000 EUR/Jahr"]:1
    I["Variable Inference-Kosten"]:1 J["keine (lokales LLM)"]:1
    K["KI-Infrastruktur Jahr 1"]:1 L["13.800 EUR"]:1
    M["KI-Infrastruktur ab Jahr 2"]:1 N["6.800 EUR"]:1
    O["Gesamt Jahr 1 inkl. Zendesk"]:1 P["ca. 69.200 EUR"]:1
    Q["Gesamt ab Jahr 2 inkl. Zendesk"]:1 R["ca. 62.200 EUR"]:1

    style K fill:#E8A838,color:#333
    style L fill:#E8A838,color:#333
    style M fill:#20808D,color:#fff
    style N fill:#20808D,color:#fff
    style O fill:#E85858,color:#fff
    style P fill:#E85858,color:#fff
    style Q fill:#27AE60,color:#fff
    style R fill:#27AE60,color:#fff
```

---

## Weg 4 — Hetzner GPU-Cloud (RAG)

> **Empfehlung:** **Beste Langzeit-Option** nach Validierung mit Weg 2. Kein Hardware-Investment, DSGVO-konform in Deutschland, On-Demand skalierbar. Migration von Weg 2 auf Weg 4 erfordert keine Aenderungen an der Zendesk App.  
> **Wissensbasis:** 111k gefilterte Tickets als Vektorindex in Qdrant (Hetzner DE) + Help-Center-Artikel. Gleiche RAG-Qualitaet wie Weg 2 und 3. Keine variablen Inference-Kosten da lokales LLM.

```mermaid
flowchart TD
    A([Ticket geht ein]) --> B[Zendesk empfaengt Ticket]
    B --> C[Zendesk App<br>Agent oeffnet Ticket]
    C -->|App-Aufruf| D[Ticket-Text<br>via HTTPS API]
    D -->|HTTPS| E[Hetzner GPU-Cloud DE<br>Qdrant Vektorsuche]
    E --> F[Top-3 Loesungen<br>aus 111k Tickets]
    F --> G[Lokales LLM<br>Mistral / Qwen2.5<br>Hetzner GPU]
    G --> H[Vorschlag im<br>App-Panel]
    H -->|Gut genug| I[Agent sendet<br>an Kunde]
    H -->|Anpassen| K[Agent editiert<br>und sendet]
    I --> L([Ticket geloest])
    K --> L

    subgraph HETZNER [Hetzner Deutschland - DSGVO-konform, RAG aktiv]
        E
        F
        G
    end

    subgraph WISSEN [Wissensbasis - RAG aktiv]
        Z1[111k Tickets als Vektorindex<br>Qdrant bei Hetzner DE] --> E
        Z2[Help Center Artikel<br>als zusaetzlicher Kontext] --> G
    end

    style A fill:#20808D,color:#fff
    style C fill:#E8A838,color:#333
    style H fill:#E8A838,color:#333
    style L fill:#27AE60,color:#fff
    style HETZNER fill:#fff3e0,color:#333
    style WISSEN fill:#e8f5e9,color:#333
```

```mermaid
block-beta
    columns 2
    A["Hardware-Investment"]:1 B["keines"]:1
    C["On-Demand GPU"]:1 D["ca. 350 EUR/Monat"]:1
    E["Dauerbetrieb GEX130"]:1 F["ca. 1.094 EUR/Monat"]:1
    G["Buerozeiten GEX44"]:1 H["ca. 102 EUR/Monat"]:1
    I["Variable Inference-Kosten"]:1 J["keine (lokales LLM)"]:1
    K["KI-Infrastruktur On-Demand/Jahr"]:1 L["ca. 4.200 EUR"]:1
    M["Gesamt pro Jahr inkl. Zendesk"]:1 N["ca. 59.600 EUR"]:1

    style K fill:#20808D,color:#fff
    style L fill:#20808D,color:#fff
    style M fill:#27AE60,color:#fff
    style N fill:#27AE60,color:#fff
```

---

## Weg 5 — Atlassian Rovo (setzt JSM-Migration voraus)

> **Empfehlung:** Nur sinnvoll wenn eine **vollstaendige Migration von Zendesk auf JSM** geplant ist. Credit-Limit von 70 Interaktionen/User/Monat ist fuer produktiven Support unzureichend.  
> **Wissensbasis — kritischer Punkt:** Echter Mehrwert entsteht **nur wenn die 111k Zendesk-Tickets sauber nach JSM migriert werden**. Ohne Migration: nur Swyx-lastige Artikel — strukturell vergleichbar mit Weg 1.  
> **Migrationskosten Zendesk → JSM:** Externes Angebot vorliegend: ~8.000 EUR fuer Ticket-Uebernahme. Marktdurchschnitt laut Branchenquellen: 10.000–50.000 EUR fuer Professional Services. Hinzu kommen interne Admin-Kosten beider Seiten (Zendesk- und JSM-Admins), schaetzungsweise 15–30 Personentage a 800 EUR = 12.000–24.000 EUR. Datenverlust-Risiko: 15–20% der Custom Fields und Automatisierungsregeln nicht migrierbar. Parallelbetrieb waehrend Uebergang: ~2 Wochen Doppellizenz. **Gesamtmigration realistisch: 20.000–60.000 EUR einmalig.**

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

    subgraph IMPORT [Datengrundlage]
        Z1["Zendesk-Tickets (nur wenn migriert)<br>sonst: kein Ticket-Wissen"] --> E
        Z2[Jira + Confluence-Artikel<br>Swyx-lastig, lueckenhaft] --> F
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
    A["JSM Premium"]:1 B["140 User x 47 EUR = 6.580 EUR/Mon"]:1
    C["Rovo Add-on"]:1 D["140 User x 16 EUR = 2.240 EUR/Mon"]:1
    E["Migration Externe Dienstleister"]:1 F["8.000 EUR (Angebot) bis 50.000 EUR (Markt)"]:1
    G["Migration Interne Admin-Kosten"]:1 H["ca. 12.000-24.000 EUR einmalig"]:1
    I["Datenverlust-Risiko"]:1 J["15-20% Custom Fields nicht migrierbar"]:1
    K["Parallelbetrieb Doppellizenz"]:1 L["ca. 2 Wochen zusaetzlich"]:1
    M["Gesamt Migration einmalig"]:1 N["ca. 20.000-60.000 EUR"]:1
    O["Gesamt pro Monat lfd. Betrieb"]:1 P["ca. 8.820 EUR"]:1
    Q["Gesamt pro Jahr lfd. Betrieb"]:1 R["ca. 105.840 EUR"]:1
    S["Credit-Limit Premium"]:1 T["70 Abfragen/User/Monat"]:1
    U["Zusaetzliche Credits"]:1 V["separat berechnet"]:1

    style M fill:#E85858,color:#fff
    style N fill:#E85858,color:#fff
    style O fill:#E8A838,color:#333
    style P fill:#E8A838,color:#333
    style Q fill:#E85858,color:#fff
    style R fill:#E85858,color:#fff
    style E fill:#E85858,color:#fff
    style F fill:#E85858,color:#fff
    style G fill:#E85858,color:#fff
    style H fill:#E85858,color:#fff
    style I fill:#E85858,color:#fff
    style J fill:#E85858,color:#fff
```

---

## Direktvergleich KI-Infrastruktur

```mermaid
xychart-beta
    title "KI-Infrastrukturkosten pro Jahr ohne Zendesk-Lizenz (EUR)"
    x-axis ["W1 Zendesk", "W1a Direkt", "W1b DialoX", "W2 Azure RAG", "W3 Jahr 1", "W3 ab Jahr 2", "W4 Hetzner", "W5 Rovo"]
    y-axis "EUR pro Jahr" 0 --> 80000
    bar [72000, 77, 77, 2000, 14800, 7000, 4400, 50640]
```

## Direktvergleich Gesamtkosten inkl. Zendesk / Jira-Lizenzen

```mermaid
xychart-beta
    title "Gesamtkosten pro Jahr inkl. Zendesk-Lizenz (EUR)"
    x-axis ["W1 Zendesk", "W1a Direkt", "W1b DialoX", "W2 Azure RAG", "W3 Jahr 1", "W3 ab Jahr 2", "W4 Hetzner", "W5 Rovo"]
    y-axis "EUR pro Jahr" 0 --> 180000
    bar [127200, 55277, 55577, 57200, 69200, 62200, 59600, 105840]
```
