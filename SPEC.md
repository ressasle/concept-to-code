# concept-to-code (c2c) – Technische Spezifikation

> **Status:** Draft v0.1
> **Autor:** Cedi (Mensch) + KI-Assistenz
> **Datum:** 2025-04-15
> **Basis:** [KONZEPT.md](./KONZEPT.md)

---

## 1. Was & Warum

### Was macht das Tool?
**concept-to-code (c2c)** ist eine lokale CLI/Chat-Anwendung, die den Prozess von der Idee zum fertigen Code strukturiert und (zunehmend) automatisiert.

### Welches Problem löst es?
Heute fehlt eine Brücke zwischen "ich habe eine Idee" und "ein KI-Agent kann das implementieren". Konzepte werden unstrukturiert festgehalten (Notizen, Sprachaufnahmen, Skizzen), und KI-Agenten bekommen entweder zu wenig oder zu vage Input. Das Ergebnis: schlechter Code, viel Nacharbeit, Frust.

### Warum bauen wir es?
1. **Persönlicher Bedarf:** Cedi will seinen eigenen Dev-Workflow optimieren (aktuell: ImmoAlpha-Projekt)
2. **Proof of Concept** für eine potenzielle SaaS-Lösung
3. **Lernprojekt:** Erfahrungen sammeln mit AI-Agent-Orchestrierung und Prompt-Engineering

---

## 2. Für wen

### Primärer User (Phase 1): Cedi selbst
- **Kontext:** Solo-Entwickler, arbeitet am MacBook, nutzt VS Code + Terminal
- **Workflow:** Ideen kommen als Textnotizen, Sprachnotizen, oder im Gespräch mit KI
- **Pain Point:** Bestehende KI-Coding-Tools (Cursor, Claude) sind gut bei klaren Aufgaben, aber schlecht wenn das Konzept selbst noch vage ist
- **Erwartung:** "Ich will meine Idee reinwerfen und am Ende eine Spec rausbekommen die so gut ist, dass ein Agent sie 1:1 umsetzen kann"

### Sekundärer User (Phase 2+): Andere Solo-Devs / kleine Teams
- Erst relevant wenn das Tool als Produkt Richtung SaaS geht
- Vorerst nicht im Scope, aber Architektur soll das nicht verbauen

---

## 3. Scope & Phasen

### Phase 0 – MVP: Concept Builder (CLI)
> Das ist was wir JETZT bauen.

**Im Scope:**
- CLI-Tool das einen geführten Chat startet
- Der Chat arbeitet eine Checkliste ab (basierend auf KONZEPT.md Notiz 3)
- Am Ende wird eine strukturierte `spec.md` generiert
- Lücken-Erkennung: fehlende Pflichtfelder werden angezeigt
- Logging: jeder Schritt wird in ein Log geschrieben

**Explizit NICHT im Scope (Phase 0):**
- Multi-Agent Code-Generierung
- UI Builder / visuelle Komponenten
- Projekt-Gedächtnis / Codebase-Awareness
- Sprach-/Bild-/Video-Input (nur Text)
- SaaS / Web-UI
- Automatisches Deployment

### Phase 1 – Spec → Code (Single Agent)
- Ein Agent der `spec.md` nimmt und Code generiert
- Human-in-the-Loop mit expliziten Entscheidungspunkten
- Delta-Tracking über Git

### Phase 2 – Multi-Agent + Projekt-Gedächtnis
- Multi-Agent-Orchestrierung
- RAG über Codebase
- Multimodaler Input (Sprache, Bilder, Skizzen)

### Phase 3 – SaaS
- Web-UI
- Team-Features
- Billing

---

## 4. Phase 0 im Detail: Concept Builder

### 4.1 User Flow

```
User startet: `c2c new`
        │
        ▼
┌─────────────────────────┐
│  Projekt-Setup           │
│  - Name                  │
│  - Typ (neues Feature,   │
│    neues Projekt, Bugfix)│
│  - Zielverzeichnis       │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  Geführter Chat          │
│  Bot stellt Fragen aus   │
│  der Checkliste:         │
│                          │
│  A) Was & Warum          │
│  B) Für wen              │
│  C) Look & Feel          │
│  D) Business Logic       │
│  E) Edge Cases           │
│  F) Scope-Grenzen        │
│  G) Datenquellen         │
│  H) Kreative Entsch.     │
│                          │
│  Bot bohrt nach wo nötig │
│  Bot fasst zusammen      │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  Review & Lücken         │
│  - Zusammenfassung       │
│  - Markierte Lücken      │
│  - User kann ergänzen    │
│    oder bewusst offen    │
│    lassen                │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  Output: spec.md         │
│  + session.log           │
│  + decisions.json        │
└─────────────────────────┘
```

### 4.2 Die Checkliste (= was der Chat abfragt)

Basierend auf KONZEPT.md Notiz 3, Kategorie A (nur Mensch kann liefern):

| # | Bereich | Pflicht? | Beschreibung |
|---|---------|----------|--------------|
| 1 | **Was & Warum** | ✅ Ja | Was macht das Feature/Tool? Welches Problem löst es? |
| 2 | **Zielgruppe & Kontext** | ✅ Ja | Wer nutzt es? In welchem Kontext? (Gerät, Situation) |
| 3 | **Look & Feel** | ⚠️ Empfohlen | Wie soll es aussehen/sich anfühlen? Referenzen, Skizzen |
| 4 | **Business-Entscheidungen** | ✅ Ja | Zugang, Preismodell, Limits |
| 5 | **Edge Cases** | ⚠️ Empfohlen | Was passiert bei Fehlern, fehlenden Daten, etc. |
| 6 | **Scope-Grenzen** | ✅ Ja | Was gehört explizit NICHT dazu? |
| 7 | **Datenquellen** | ✅ Ja | Woher kommen die Daten? |
| 8 | **Kreative Entscheidungen** | ⚠️ Empfohlen | Farben, Wording, Ton, Branding |
| 9 | **Entscheidungspunkte** | ⚠️ Empfohlen | "Dinge über die ich erst entscheiden will wenn ich es sehe" |

Zusätzlich optional (Grauzone / Kategorie C):

| # | Bereich | Beschreibung |
|---|---------|--------------|
| 10 | **Komponentenstruktur** | Grobe Vorstellung wie es aufgeteilt sein soll |
| 11 | **State Management** | Präferenzen für Zustandsverwaltung |
| 12 | **Performance-Anforderungen** | Gibt es harte Anforderungen? |

### 4.3 Spec-Output-Format

Die generierte `spec.md` hat ein festes Schema:

```markdown
---
# YAML Frontmatter (maschinenlesbar)
name: "feature-name"
type: "feature" | "project" | "bugfix"
status: "draft" | "ready" | "in-progress" | "done"
created: "2025-04-15"
version: 1
target_project: "/pfad/zum/projekt"
completeness:
  required_fields: 8
  filled_fields: 7
  missing: ["edge_cases"]
decisions_deferred: 2
---

# Feature: [Name]

## Was & Warum
[Freitext vom User, vom Bot zusammengefasst und strukturiert]

## Zielgruppe & Kontext
[...]

## Look & Feel
[Beschreibung + Referenzen]

## Business Logic & Entscheidungen
[...]

## Edge Cases & Fehlerbehandlung
[...]

## Scope-Grenzen
### Im Scope
- ...
### Nicht im Scope
- ...

## Datenquellen
[...]

## Kreative Entscheidungen
[...]

## Entscheidungspunkte (Human-in-the-Loop)
> Diese Punkte werden während der Implementierung dem User vorgelegt.
- [ ] [Beschreibung der offenen Entscheidung]
- [ ] [...]

## KI-Notizen
> Automatisch generiert – was die KI aus dem Gespräch abgeleitet hat
> und was sie empfiehlt, aber nicht selbst entscheiden sollte.
- Empfehlung Komponentenstruktur: ...
- Empfehlung State Management: ...
```

### 4.4 Session Log Format

Jeder Chat wird vollständig geloggt:

```json
{
  "session_id": "uuid",
  "started_at": "ISO-timestamp",
  "ended_at": "ISO-timestamp",
  "project_name": "feature-name",
  "steps": [
    {
      "step": 1,
      "category": "was_und_warum",
      "bot_question": "Was genau soll das Feature machen?",
      "user_response": "...",
      "bot_summary": "...",
      "completeness": "complete|partial|missing",
      "follow_up_needed": false
    }
  ],
  "gaps_identified": ["edge_cases"],
  "decisions_deferred": [
    {
      "topic": "Platzhalter vs. Feld weglassen bei fehlendem Profilbild",
      "reason": "User will es erst sehen bevor er entscheidet"
    }
  ]
}
```

---

## 5. Tech-Stack (Phase 0)

### Entscheidungen

| Bereich | Entscheidung | Begründung |
|---------|-------------|------------|
| **Sprache** | TypeScript | Passt zu Cedis Stack (ImmoAlpha), gute LLM-SDK-Unterstützung |
| **Runtime** | Node.js | Standard für TS CLI-Tools |
| **CLI Framework** | `commander` + `inquirer` | Bewährt, leichtgewichtig, gute Prompts |
| **LLM API** | Anthropic (Claude) | Bestes Reasoning für strukturierte Konzeptextraktion |
| **Paketierung** | npm/npx lokal | Kein Publishing nötig in Phase 0 |
| **Konfiguration** | `.c2c/` Verzeichnis im Projekt | Lokale Konfiguration und Session-Daten |
| **Logging** | Eigenes JSON-Format | Einfach, durchsuchbar, parsbar |

### Projektstruktur (geplant)

```
concept-to-code/
├── KONZEPT.md                 # Ursprüngliches Konzeptdokument
├── SPEC.md                    # Diese Datei
├── package.json
├── tsconfig.json
├── src/
│   ├── index.ts               # CLI Entry Point
│   ├── commands/
│   │   ├── new.ts             # `c2c new` – Neues Konzept starten
│   │   ├── resume.ts          # `c2c resume` – Unterbrochene Session fortsetzen
│   │   └── review.ts          # `c2c review` – Bestehende Spec reviewen
│   ├── chat/
│   │   ├── engine.ts          # Chat-Orchestrierung (Fragen, Zusammenfassungen)
│   │   ├── checklist.ts       # Die Checkliste mit allen Kategorien
│   │   ├── prompts.ts         # System-Prompts für den Concept Builder Bot
│   │   └── gap-detector.ts    # Lücken-Erkennung in der Spec
│   ├── output/
│   │   ├── spec-generator.ts  # Generiert die spec.md
│   │   ├── log-writer.ts      # Schreibt Session Logs
│   │   └── templates/         # Mustache/Handlebars Templates für Output
│   ├── llm/
│   │   ├── client.ts          # LLM API Client (Anthropic)
│   │   └── types.ts           # Typen für LLM Requests/Responses
│   └── utils/
│       ├── config.ts          # Konfiguration laden/speichern
│       └── logger.ts          # Internes Logging
├── templates/
│   └── spec.md.hbs            # Template für die generierte Spec
└── tests/
    └── ...
```

---

## 6. Offene Entscheidungen (für mich als User)

> Diese Punkte muss ich entscheiden bevor / während wir coden:

### Must-Decide (vor dem Start)
- [ ] **API Key Management:** Eigener Anthropic-Key? Soll der im `.env` liegen oder bei Setup abgefragt werden?
- [ ] **Session-Persistenz:** Sollen unterbrochene Sessions wieder aufnehmbar sein? (Empfehlung: Ja, via JSON-Datei)
- [ ] **Sprache des Chatbots:** Deutsch oder Englisch? (Empfehlung: Deutsch für User-Interaktion, Englisch für Spec-Output)

### Can-Decide-Later (beim ersten Test)
- [ ] **Wie tief soll der Bot nachbohren?** Aggressiv ("Erzähl mir mehr über...") vs. sanft ("Optional: hast du noch Details zu...")?
- [ ] **Soll der Bot eigene Vorschläge machen?** ("Basierend auf deiner Beschreibung würde ich empfehlen...") oder nur sammeln?
- [ ] **Max Gesprächsdauer / Fragen-Limit** pro Kategorie?

---

## 7. Edge Cases & Fehlerbehandlung

| Szenario | Verhalten |
|----------|----------|
| User bricht Session ab (Ctrl+C) | Session wird in `.c2c/sessions/` gespeichert, `c2c resume` möglich |
| API-Key fehlt oder ungültig | Klare Fehlermeldung + Anleitung wie man den Key setzt |
| LLM API nicht erreichbar | Retry mit Backoff, nach 3 Versuchen abbrechen mit gespeichertem State |
| User antwortet mit "weiß ich noch nicht" | Als bewusst offene Entscheidung markieren, in `decisions_deferred` aufnehmen |
| User will Kategorie überspringen | Erlaubt, wird als `missing` in Completeness markiert |
| Spec wird zu lang (>5000 Wörter) | Warnung + Empfehlung den Scope aufzuteilen |

---

## 8. Verifizierungsplan

### Automatisiert
- Unit Tests für Spec-Generator (Template + YAML Frontmatter korrekt?)
- Unit Tests für Gap-Detector (erkennt fehlende Pflichtfelder?)
- Integration Test: Dummy-Session → generierte Spec → Schema-Validierung

### Manuell (Testlauf)
1. `c2c new` ausführen mit einem echten ImmoAlpha-Feature als Testcase
2. Durchlaufen des gesamten Chatflows
3. Generierte `spec.md` manuell reviewen: Ist die Spec vollständig und korrekt?
4. Session-Log prüfen: Sind alle Schritte nachvollziehbar?
5. Edge Cases testen: Session abbrechen, Session fortsetzen, Kategorie überspringen

---

## 9. Erfolgs-Kriterien (Phase 0)

Die Phase 0 ist erfolgreich wenn:

1. ✅ `c2c new` startet einen geführten Chat der alle Checklisten-Kategorien abfragt
2. ✅ Am Ende wird eine valide, strukturierte `spec.md` generiert
3. ✅ Lücken werden erkannt und dem User angezeigt
4. ✅ Sessions sind unterbrechbar und wieder aufnehmbar
5. ✅ Jeder Schritt wird geloggt
6. ✅ Ein echter Testlauf mit einem ImmoAlpha-Feature erzeugt eine Spec die sich "richtig" anfühlt

---

## Meta: Warum dieses Dokument so aussieht

Dieses Dokument ist selbst ein Prototyp des Outputs den der Concept Builder später automatisch erzeugen soll. Die Struktur hier (Was/Warum → Für wen → Scope → Details → Edge Cases → Verifizierung) ist das Zielformat.

Der Unterschied zur späteren automatisierten Version:
- **Jetzt:** Für einen Menschen geschrieben, mit Erklärungen und Kontext
- **Später:** Maschinenoptimiert, mit strukturierten Feldern und weniger Prosa
- **Noch später:** Vom Tool selbst generiert, basierend auf einem Chat mit dem User
