# Management-Summary: KI-Support-Strategie Enreach/Swyx

## Ausgangslage

Enreach/Swyx betreibt einen ueberwiegend **L2/L3-lastigen Support** mit ca. 180.000 historischen Tickets in Zendesk. Davon sind **111.000 Tickets qualitativ verwertbar** (mehr als 2 Antworten, nicht aelter als 5 Jahre). Die vorhandenen Help-Center-Artikel sind stark Swyx-lastig und decken die Communications-Produkte nur ungenuegend ab.

**Kernaussage:** Echter KI-Mehrwert entsteht **ausschliesslich durch RAG** (Retrieval-Augmented Generation) ueber die gefilterten 111k Tickets. Alle Wege ohne RAG sind als temporaere Proof-of-Concept-Loesungen zu verstehen und liefern fuer Communications-Produkte keine belastbaren Ergebnisse.

**Wichtig — Kein Fine-Tuning:** RAG ist die korrekte Architektur fuer diesen Use Case. Fine-Tuning wuerde das Modell-Verhalten aendern, nicht das Wissen erweitern — und waere mit ~1.224 EUR/Monat reinen Hosting-Kosten dauerhaft unwirtschaftlich. Ticket-Wissen veraltet kontinuierlich; RAG erlaubt jederzeit aktualisierbare Wissensbasis ohne Neu-Training.

---

## Wissensbasis-Qualitaet im Vergleich

| Weg | RAG | Ticketbasis | Artikel-Basis | Qualitaet Communications |
|---|---|---|---|---|
| 1 — Zendesk Native | Nein | Nein | Swyx-lastig | Ungenuegend |
| 1a — Flask direkt | Nein | Sehr begrenzt | Swyx-lastig | Ungenuegend |
| 1b — DialoX | Nein | Volumen zu gross | Swyx-lastig | Ungenuegend |
| 2 — Azure RAG | Ja | 111k Tickets | Vollstaendig | Vollstaendig |
| 3 — Eigener Server | Ja | 111k Tickets | Vollstaendig | Vollstaendig |
| 4 — Hetzner GPU | Ja | 111k Tickets | Vollstaendig | Vollstaendig |
| 5 — Rovo | Nur nach Migration | Nur wenn migriert | Swyx-lastig | Abhaengig von Migration |

---

## Entscheidungsmatrix

| Kriterium | W1 Zendesk | W1a Flask | W1b DialoX | W2 Azure RAG | W3 Server | W4 Hetzner | W5 Rovo |
|---|---|---|---|---|---|---|---|
| **RAG / Ticketwissen** | Nein | Nein | Nein | Ja | Ja | Ja | Nur nach Migration |
| **Communications-Abdeckung** | Nein | Nein | Nein | Ja | Ja | Ja | Abhaengig |
| **Einrichtungsaufwand** | Mittel | Minimal | Gering | Gering | Hoch | Mittel | Sehr hoch + Migration |
| **Inference-Kosten/Mon** | enthalten | ~6 EUR | ~6 EUR | ~23-115 EUR | keine | keine | enthalten |
| **DSGVO / Datenkontrolle** | US-Cloud | Azure | Azure | Azure | On-Premise | DE | Atlassian Cloud |
| **Hardware-Investment** | keines | keines | keines | keines | 22.000 EUR | keines | keines |
| **Migrationskosten einmalig** | — | — | — | — | — | — | 20.000-60.000 EUR |
| **KI-Kosten/Jahr** | 72.000 EUR | ~77 EUR | ~77 EUR | 1.440-2.000 EUR | ~13.800 EUR (J1) | ~4.200 EUR | ~50.640 EUR |
| **Gesamt/Jahr lfd. Betrieb** | 127.200 EUR | 55.277 EUR | 55.577 EUR | 56.640-57.200 EUR | 69.200 EUR (J1) | 59.600 EUR | 105.840 EUR |
| **Skalierbarkeit** | Eingeschraenkt | Eingeschraenkt | Eingeschraenkt | Hoch | Hardware-Limit | Hoch | Credit-Limit |
| **Zendesk-Migration noetig** | Nein | Nein | Nein | Nein | Nein | Nein | Ja, zu JSM |

---

## Zendesk → JSM Migration: Kostenrealitaet

Fuer Weg 5 (Rovo) ist eine vollstaendige Migration von Zendesk nach JSM Voraussetzung. Vorliegendes Angebot: ~8.000 EUR fuer reine Ticket-Uebernahme. Der Marktdurchschnitt zeigt jedoch ein deutlich komplexeres Bild:

| Kostenblock | Betrag |
|---|---|
| Externe Dienstleister (Professional Services) | 10.000–50.000 EUR |
| Internes Admin-Personal (Zendesk + JSM, 15-30 PT a 800 EUR) | 12.000–24.000 EUR |
| Parallelbetrieb / Doppellizenz (~2 Wochen) | ~4.000–6.000 EUR |
| Nacharbeiten Custom Fields / Automatisierungsregeln (15-20% Datenverlust) | schwer kalkulierbar |
| **Gesamtmigration realistisch** | **20.000–60.000 EUR einmalig** |

Hinzu kommt: Nach erfolgreicher Migration steigen die laufenden Lizenzkosten von ~55.200 EUR/Jahr (Zendesk) auf ~105.840 EUR/Jahr (JSM + Rovo) — ein Anstieg von **+50.640 EUR/Jahr**. Der Migrationsaufwand amortisiert sich damit nie, sofern keine weiteren strategischen Gruende fuer JSM vorliegen.

---

## Inference-Kosten erklaert

Bei allen Wegen mit Azure OpenAI fallen Pay-per-Use Token-Kosten an. Der Unterschied zwischen den Wegen:

| Weg | Input-Token/Anfrage | Output-Token/Anfrage | Kosten/1.000 Anfragen |
|---|---|---|---|
| 1a / 1b (ohne RAG) | ~800 (Artikel-Kontext) | ~500 | ~6 EUR |
| 2 (mit RAG) | ~4.000 (Artikel + Top-3 Tickets) | ~1.500 | ~23 EUR |
| 3 / 4 (lokales LLM) | — | — | keine variablen Kosten |

RAG erhoert die Token-Kosten pro Anfrage, liefert aber qualitativ ueberlegene Antworten. Bei Weg 3 und 4 (lokales LLM auf eigener oder Hetzner-Hardware) entfallen variable Inference-Kosten vollstaendig.

---

## Empfohlene Roadmap

**Phase 1 — Sofort (Monat 1-2): Weg 1a**
- Flask-Server ruft Azure OpenAI direkt auf — Webhook bereits vorhanden, kein DialoX-Overhead
- Wissensbasis: Help-Center-Artikel, bewusst als PoC mit bekannten Einschraenkungen akzeptiert
- Inference-Kosten: ~6 EUR/Monat bei 1.000 Anfragen (~800/500 Token ohne RAG-Kontext)
- Ziel: Technischen Stack validieren, Zendesk-App entwickeln, Prozesse einueben

**Phase 2 — Kurzfristig (Monat 3-6): Weg 2 — Azure RAG**
- Azure AI Search S1 aktivieren (74 EUR/Monat Fixkosten)
- 111k Tickets einmalig vektorisieren (~275 EUR Einmalkosten)
- Erst jetzt: echte RAG-Qualitaet, vollstaendige Communications-Abdeckung
- Inference-Kosten steigen auf ~23-69 EUR/Monat durch groesseren Kontext (4k/1.5k Token)
- Kein Fine-Tuning, kein Modell-Hosting — Basis-Modell bleibt unveraendert

**Phase 3 — Mittelfristig (ab Jahr 2): Weg 4 — Hetzner GPU-Cloud**
- Nach Qualitaetsvalidierung Migration auf Hetzner DE
- DSGVO-konform, kein Hardware-Investment, keine variablen Inference-Kosten
- Zendesk App muss nicht angepasst werden — Drop-in-Ersatz fuer Weg 2

---

## Kostenperspektive

| Phase | Weg | KI-Kosten/Jahr | Gesamt lfd. Betrieb/Jahr inkl. Zendesk | Einmalig |
|---|---|---|---|---|
| Phase 1 | 1a — Flask direkt | ~77 EUR | ~55.277 EUR | — |
| Phase 2 | 2 — Azure RAG | ~1.440-2.000 EUR | ~56.640-57.200 EUR | ~275 EUR |
| Phase 3 | 4 — Hetzner GPU | ~4.200 EUR | ~59.600 EUR | — |
| Nicht empfohlen | 1 — Zendesk Native | ~72.000 EUR | ~127.200 EUR | — |
| Nicht empfohlen | 5 — Rovo | ~50.640 EUR | ~105.840 EUR | 20.000-60.000 EUR Migration |

Die KI-Zusatzkosten in der empfohlenen Roadmap bleiben selbst in Phase 3 unter 4.200 EUR/Jahr — gemessen an den Zendesk-Lizenzkosten (~55.200 EUR/Jahr) ein marginaler Aufwand bei signifikantem Produktivitaetsgewinn.

---

## Nicht empfohlene Optionen — Begruendung

- **Weg 1 (Zendesk Native):** Teuerste Option, kein Zugriff auf historische Tickets, keine L2/L3-Kompetenz, Communications-Produkte nicht abgedeckt
- **Weg 1b (DialoX):** Tickets volumenseitig nicht verarbeitbar, nur Artikel-Basis moeglich — kein Mehrwert gegenueber Weg 1a bei hoeherer Komplexitaet und Lizenzabhaengigkeit
- **Weg 3 (Eigener Server):** Erst ab Jahr 2 sinnvoll wenn weitere KI-Anwendungsfaelle hinzukommen — 22.000 EUR Hardware ohne vorherige Validierung nicht empfohlen
- **Weg 5 (Rovo):** Migrationskosten 20.000-60.000 EUR einmalig; laufende Kosten +50.640 EUR/Jahr gegenueber Zendesk; ohne Migration nur Swyx-lastige Artikel; Credit-Limit (70 Abfragen/User/Monat) fuer produktiven Support ungenuegend — Migration amortisiert sich nicht allein durch KI-Nutzen
