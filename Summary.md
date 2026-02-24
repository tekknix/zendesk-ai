Hier ist die Management-Summary mit Entscheidungsmatrix:

***

# Management-Summary: KI-Support-Strategie Enreach/Swyx

## Ausgangslage

Enreach/Swyx betreibt einen überwiegend **L2/L3-lastigen Support** mit ca. 180.000 historischen Tickets in Zendesk. Davon sind **111.000 Tickets qualitativ verwertbar** (mehr als 2 Antworten, nicht älter als 5 Jahre). Die vorhandenen Help-Center-Artikel sind stark Swyx-lastig und decken die Communications-Produkte nur unzureichend ab — eine rein artikel-basierte KI liefert damit für einen Großteil der eingehenden Tickets keine relevanten Antworten.

**Kernaussage:** Echter KI-Mehrwert entsteht **ausschließlich durch RAG** (Retrieval-Augmented Generation) über die gefilterten 111k Tickets — unabhängig von der gewählten Infrastruktur. Alle Wege ohne RAG sind als temporäre Proof-of-Concept-Lösungen zu verstehen.

***

## Wissensbasis-Qualität im Vergleich

| Weg | RAG | Ticketbasis | Artikel-Basis | Qualität Communications |
|---|---|---|---|---|
| 1 — Zendesk Native | ❌ | ❌ | ⚠️ Swyx-lastig | ❌ Unzureichend |
| 1a — Flask direkt | ❌ | ⚠️ Sehr begrenzt | ⚠️ Swyx-lastig | ❌ Unzureichend |
| 1b — DialoX | ❌ | ❌ Volumen zu groß | ⚠️ Swyx-lastig | ❌ Unzureichend |
| 2 — Azure RAG | ✅ | ✅ 111k Tickets | ✅ | ✅ Vollständig |
| 3 — Eigener Server | ✅ | ✅ 111k Tickets | ✅ | ✅ Vollständig |
| 4 — Hetzner GPU | ✅ | ✅ 111k Tickets | ✅ | ✅ Vollständig |
| 5 — Rovo | ⚠️ Nur nach Migration | ⚠️ Nur wenn migriert | ⚠️ Swyx-lastig | ❌/⚠️ Abhängig von Migration |

***

## Entscheidungsmatrix

| Kriterium | W1 Zendesk | W1a Flask direkt | W1b DialoX | W2 Azure RAG | W3 Eigener Server | W4 Hetzner GPU | W5 Rovo |
|---|---|---|---|---|---|---|---|
| **Einrichtungsaufwand** | ⚠️ Mittel | ✅ Minimal | ✅ Gering | ✅ Gering | ❌ Hoch | ⚠️ Mittel | ❌ Sehr hoch |
| **RAG / Ticketwissen** | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ | ⚠️ |
| **Communications-Abdeckung** | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ | ⚠️ |
| **DSGVO / Datenkontrolle** | ⚠️ US-Cloud | ⚠️ Azure | ⚠️ Azure | ⚠️ Azure | ✅ On-Premise | ✅ DE | ⚠️ Atlassian |
| **Hardware-Investment** | ❌ keines | ❌ keines | ❌ keines | ❌ keines | ❌ 22.000 EUR | ❌ keines | ❌ keines |
| **KI-Kosten/Jahr** | 72.000 EUR | ~300 EUR | ~300 EUR | ~1.300 EUR | ~13.800 EUR (J1) | ~4.200 EUR | ~50.640 EUR |
| **Gesamt/Jahr inkl. Zendesk** | 127.200 EUR | 55.200 EUR | 55.500 EUR | 56.500 EUR | 69.200 EUR (J1) | 59.600 EUR | 105.840 EUR |
| **Skalierbarkeit** | ⚠️ | ⚠️ | ⚠️ | ✅ | ⚠️ Hardware-Limit | ✅ | ⚠️ Credit-Limit |
| **Eigene Infrastruktur nötig** | ❌ | ✅ Flask (vorhanden) | ✅ Flask (vorhanden) | ❌ | ✅ Server nötig | ❌ | ❌ |
| **Zendesk-Migration nötig** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ Zu JSM |

***

## Empfohlene Roadmap

**Phase 1 — Sofort (Monat 1–2): Weg 1a**
- Flask-Server ruft Azure OpenAI direkt auf — Webhook bereits vorhanden
- Kein DialoX-Overhead, kein Zusatzaufwand
- Wissensbasis: Help-Center-Artikel, bewusst als PoC akzeptiert
- Ziel: Technischen Stack validieren, Prozesse einüben

**Phase 2 — Kurzfristig (Monat 3–6): Weg 2 — Azure RAG**
- Azure AI Search S1 dazuschalten (~74 EUR/Monat)
- 111k Tickets als Vektorindex — erstmals echte RAG-Qualität
- Erst jetzt wird die KI für Communications-Produkte belastbar nutzbar
- Kein Fine-Tuning, kein Modell-Hosting — Basis-Modell bleibt unverändert

**Phase 3 — Mittelfristig (ab Jahr 2): Weg 4 — Hetzner GPU-Cloud**
- Nach Qualitätsvalidierung Migration auf Hetzner DE
- DSGVO-konform, kein Hardware-Investment, On-Demand skalierbar
- Zendesk App muss **nicht angepasst** werden — Drop-in-Ersatz für Weg 2

***

## Kosten auf einen Blick

| Phase | Weg | KI-Zusatzkosten/Jahr | Gesamt/Jahr |
|---|---|---|---|
| Phase 1 | 1a — Flask direkt | ~300 EUR | ~55.200 EUR |
| Phase 2 | 2 — Azure RAG | ~1.300 EUR | ~56.500 EUR |
| Phase 3 | 4 — Hetzner GPU | ~4.200 EUR | ~59.600 EUR |
| ❌ Nicht empfohlen | 1 — Zendesk Native | ~72.000 EUR | ~127.200 EUR |
| ❌ Nicht empfohlen | 5 — Rovo | ~50.640 EUR | ~105.840 EUR |

Die KI-Zusatzkosten in der empfohlenen Roadmap (300 → 1.300 → 4.200 EUR/Jahr) sind im Vergleich zu den Zendesk-Lizenzkosten (~55.200 EUR/Jahr) marginal — bei gleichzeitig signifikantem Produktivitätsgewinn, sobald RAG über die 111k Tickets aktiv ist.

***

## Nicht empfohlene Optionen — Begründung

- **Weg 1 (Zendesk Native):** Teuerste Option, kein Zugriff auf historische Tickets, keine L2/L3-Kompetenz
- **Weg 1b (DialoX):** Nur Help-Center-Artikel möglich (Volumen zu groß), strukturell wie Weg 1 — kein Mehrwert gegenüber 1a bei höherer Komplexität
- **Weg 3 (Eigener Server):** Erst ab Jahr 2 sinnvoll wenn weitere KI-Anwendungsfälle (Transkription, interne Tools) hinzukommen — 22.000 EUR Hardware-Investment ohne vorherige Validierung nicht empfohlen
- **Weg 5 (Rovo):** Setzt vollständige JSM-Migration voraus; ohne Migration nur Swyx-lastige Artikel als Basis; Credit-Limit (70 Abfragen/User/Monat) für produktiven Support unzureichend; teuerste Nicht-Zendesk-Option
