# concept-to-code (c2c)

## Die Idee

Coden komplett automatisieren. Du machst die kreative Arbeit – Konzepte, Architektur, Design-Entscheidungen – und KI übernimmt die Implementierung.

**Input:** Alles was ein Konzept beschreibt – Meeting-Aufnahmen, Sprachnotizen, handgezeichnete Skizzen, Whiteboard-Fotos, Textdokumente, Videos.

**Output:** Eine maschinenlesbare technische Spezifikation (`spec.md`), die direkt von einem KI-Agenten implementiert werden kann.

**Kerngedanke:** Die Arbeitsteilung umdrehen. Der Mensch übernimmt das, was KI nicht kann – das *Erschaffen*, das kreative Denken, die Vision. KI übernimmt das Mechanische – den Code schreiben, testen, deployen.

---

## Notiz 1: Concept Builder

Das Tool soll nicht nur fertige Konzepte *verarbeiten* – es soll beim *Erstellen* des Konzepts helfen.

**Erkenntnis:** Das Erarbeiten eines Konzepts ist selbst schon Teil des Prozesses. Ein "Concept Builder" als erster Schritt, der dich geführt durch die Konzepterstellung bringt.

**Ideen dazu:**

- **Railguards:** Das Tool gibt Struktur vor und stellt sicher, dass alle Bereiche abgedeckt werden die KI alleine nicht lösen kann (UX-Entscheidungen, Business Logic, Edge Cases, Look & Feel, etc.)
- **Geführter Chatbot:** Ein KI-Chatbot der speziell darauf trainiert/gepromptet ist, effizient ein Konzept herauszuarbeiten – er stellt die richtigen Fragen, bohrt nach wo nötig, fasst zusammen
- **Automatische Eignung:** Durch die Guardrails ist das fertige Konzept automatisch in einem Format das sich direkt zur Code-Generierung eignet – keine manuelle Nacharbeit nötig
- **Lücken-Erkennung:** Das Tool erkennt wenn im Konzept etwas fehlt das die KI zum Coden braucht und fragt aktiv nach

---

## Notiz 2: Vom Konzept zur Umsetzung – UI Builder & Agent-Orchestrierung

**Frage:** Wenn das Konzept fertig ist – wie muss es genau aussehen und wie geht man es an?

### UI Builder

- Soll man eine Art "UI Builder" haben wo man das Interface quasi aufmalen kann?
- Dazu dann spezifische Instruktionen pro Komponente/Element
- Visuell + Text kombiniert: "So soll es aussehen" + "So soll es sich verhalten"

### Agent-Orchestrierung

- Wie strukturiert man das Konzept so, dass Agenten in einem Multi-Agent-Framework alles einordnen können?
- Reihenfolge: Was muss zuerst gebaut werden? (DB-Schema → API → Frontend)
- Ineinandergreifende Funktionen: Wie erkennen Agenten Abhängigkeiten?
- Sortierung: Welcher Agent übernimmt welchen Teil?

---

## Notiz 3: Was muss ein Konzept beinhalten? (Beispiel: Neues Tool auf ImmoAlpha)

**Gedankenexperiment:** Wir wollen ein neues Tool auf der ImmoAlpha-Website einbauen. Was braucht die Pipeline als Input?

### A) Dinge die NUR der Mensch liefern kann

1. **Was & Warum** – Was macht das Tool? Welches Problem löst es? Warum bauen wir es?
2. **Für wen** – Wer nutzt es? In welchem Kontext? (Investor am Laptop vs. Makler unterwegs am Handy)
3. **Look & Feel** – Wie soll es sich anfühlen? (Skizze, Referenz-Screenshots, "so wie bei X aber mit Y")
4. **Business-Entscheidungen** – Kostenlos oder Premium? Wer hat Zugang? Limits?
5. **Edge Cases / Sonderfälle** – Was passiert wenn keine Daten da sind? Wenn der User was Falsches eingibt? Wenn die API nicht antwortet?
6. **Scope-Grenzen** – Was gehört NICHT dazu? Was ist explizit out of scope?
7. **Datenquellen** – Woher kommen die Daten? API? User-Input? Scraping? Manuell?
8. **Kreative Entscheidungen** – Farben, Wording, Ton, Markensprache

### B) Dinge die KI selbst herausfinden kann (= muss man NICHT im Konzept liefern)

- Dateistruktur / wo welche Komponente hinkommt
- Code-Patterns (kann bestehenden Code lesen)
- DB-Schema-Design (wenn man die Daten beschreibt)
- API-Routing & Endpoint-Design
- Build/Deploy (Pipeline existiert schon)
- Responsive-Umsetzung (Standard)
- Error Handling Patterns (kann man ableiten)

### C) Grauzone – besser wenn der Mensch entscheidet, aber KI kann Vorschlag machen

- Exakte Komponentenstruktur (1 große oder 3 kleine Komponenten?)
- State Management Ansatz (lokal vs. Zustand Store?)
- Caching-Strategie
- Animationen / Micro-Interactions (welche? wo?)

---

## Notiz 4: Logging & Testläufe

**Ansatz:** Iterativ entwickeln – Konzepte entwerfen, durch die Pipeline schicken, Output manuell bewerten, Probleme identifizieren, Pipeline verbessern.

**Anforderung:** Jeder Schritt der Pipeline muss geloggt werden, damit man am Ende eines Testlaufs nachvollziehen kann:

- Was wurde als Input erkannt?
- Was hat die KI daraus extrahiert?
- Welche Entscheidungen hat die KI getroffen?
- Wo ist die Qualität der Ausgabe schlecht und warum?

**Ziel:** Am Ende eines Testlaufs kann man das Log + die Ausgabe nehmen, manuelles Feedback geben ("hier war die Spec falsch", "diese Edge Cases hat er ignoriert", "das UI-Konzept wurde falsch interpretiert"), und daraus die Prompts/Pipeline verbessern.

---

## Notiz 5: Human-in-the-Loop während der Implementierung

**Problem:** Auch mit perfektem Konzept wird es während des Codens Situationen geben wo die KI nicht weiterkommt oder wo eine kurze menschliche Entscheidung effizienter wäre als 20 Minuten KI-Rumraten.

### Fehlermeldungen / Notifications

- Wenn etwas nicht funktioniert (Build bricht ab, Test failt, API antwortet nicht) → sofortige Meldung an den User
- Nicht einfach stumm weiterversuchen oder raten – klar kommunizieren was schiefläuft
- Verschiedene Severity-Levels: Info ("hab mich für X entschieden") vs. Blocker ("kann ohne dein Input nicht weiter")

### Smarte Rückfragen

- Mechanismen einbauen die *erkennen*, wann es sinnvoller ist den User kurz zu fragen statt selbst zu entscheiden
- Beispiel: "Im Konzept steht nichts dazu was passiert wenn ein User kein Profilbild hat – soll ich einen Platzhalter nehmen oder das Feld weglassen?"
- Lieber einmal kurz fragen als 30 Minuten Code bauen der dann falsch ist
- **Aber:** Nicht bei jeder Kleinigkeit fragen – nur wenn die Entscheidung echte Auswirkung hat

### Übergabe an den User

- Manche Dinge kann/sollte der User einfach selbst machen weil es schneller geht
- Beispiel: API-Key eintragen, externen Service konfigurieren, Design-Entscheidung bei der man es sehen muss
- Das System soll das erkennen und klar sagen: "Das solltest du selbst machen, hier ist was du tun musst"

### Umsetzungs-Überlegungen

- **Schwierigkeit:** Automatisch erkennen wann man fragen soll ist das härteste Problem hier. Die KI muss wissen was sie *nicht* weiß.
- **Confidence Scoring:** Agent bewertet intern seine Sicherheit pro Entscheidung. Unter Threshold → fragen. Darüber → machen.
- **Kategorie-basiert:** Design-Entscheidungen → fragen. Implementierungs-Details → selbst lösen.
- **Pragmatischer Ansatz:** Statt auf automatische Erkennung zu setzen, lieber im Concept Builder **explizite Entscheidungspunkte** definieren lassen. Eine eigene Sektion: *"Dinge über die ich erst entscheiden will wenn ich es sehe"* – dann weiß der Agent vorher schon wo er pausieren muss.

---

## Notiz 6: Iteratives Fine-Tuning nach dem ersten Durchlauf

**Gedanke:** Nach dem ersten Implementierungs-Durchlauf ist man selten fertig. Es braucht Fine-Tuning-Schritte wo man die Ausgabe überarbeitet.

### Kontext-Erhalt

- Die Agenten müssen beim Fine-Tuning den **gesamten Kontext** behalten: Ursprüngliches Konzept, alle Entscheidungen die getroffen wurden, den generierten Code
- Wenn der User dann sagt "ändere X", muss der Agent verstehen *warum* X ursprünglich so gebaut wurde und was sich durch die Änderung alles mitändert

### Delta-Nachvollziehbarkeit

- Jede manuelle Änderung/Entscheidung des Users wird als **Delta** erfasst
- Der Agent kann nachvollziehen: "Im Konzept stand A, der erste Durchlauf hat B gebaut, der User hat dann auf C geändert"
- Das verhindert, dass der Agent bei der nächsten Iteration die manuellen Änderungen wieder überschreibt

### Ablauf

1. Erster Durchlauf → Code-Output
2. User reviewed, markiert was geändert werden soll
3. Agent bekommt: Original-Konzept + bisheriger Code + User-Feedback als Deltas
4. Agent passt gezielt an, ohne den Rest kaputtzumachen
5. Wiederholen bis es passt

### Überlegungen

- **Selbst-optimierender Loop:** Wenn man über die Zeit trackt welche *Art von Konzeptlücken* immer wieder zu Nacharbeit führen, kann man den Concept Builder (Notiz 1) automatisch verbessern. Das System lernt aus seinen eigenen Iterationen.
- **Technisch:** Im Grunde ein Git-artiges System für Entscheidungen – jede Iteration ist ein "Commit" mit Kontext. Nicht schwer zu bauen, aber extrem wertvoll.

---

## Notiz 7: Konzept-Organisator für das Gesamtprojekt

**Problem:** Neue Features müssen fast immer an bestehende Teile angeschlossen werden. Man muss wissen was es schon gibt, wie es aufgebaut ist, und wo die Andockpunkte sind.

### Das Tool als Projekt-Gedächtnis

- Das Tool soll nicht nur einzelne Konzepte verwalten, sondern einen **intuitiven Überblick über das gesamte Projekt** bieten
- Alle bisherigen Konzepte, Features und deren Verknüpfungen sind sichtbar
- Man sieht sofort: "Das Scoring-Modul hängt am Backend-Datenmodell, das hängt an der Supabase-DB, das hängt an den Edge Functions"

### Codebase-Aware Chatbot

- Ein KI-Chatbot der das Projekt **kennt** – Codebase, Architektur, Datenbank, APIs, bisherige Konzepte
- Beispiel-Frage: "Ich will den KI-Exposé-Analysator an die Backend-Zahlen anschließen die wir schon haben – wie?"
- Chatbot antwortet: "Die Zahlen liegen in der `property_analyses` Tabelle, Feld `alpha_score`. Du brauchst einen neuen Edge Function Call in `exposeAnalyzer.ts` der dieses Feld liest. Hier ist die Schnittstelle..."
- **Entscheidend:** Der Chatbot gibt nicht nur abstrakte Antworten sondern zeigt konkret *welche Dateien, Tabellen, Funktionen* betroffen sind

### Warum das wichtig ist

- Ohne diesen Überblick passiert genau das was heute passiert: Man baut Features die nicht richtig angeschlossen sind, oder baut versehentlich Dinge doppelt
- Der Organisator wird quasi zur "lebenden Dokumentation" die automatisch aktuell bleibt

---

## Produkt-Design: Der Concept Builder im Detail

Der Concept Builder führt den User Schritt für Schritt durch die Konzepterstellung. Jeder Schritt hat einen klaren Input, einen klaren Output, und definierte KI-Touchpoints.

### Step 1: Problem & Ziel

Der User beschreibt **was nicht funktioniert** (Problem) und **was stattdessen passieren soll** (Ziel). Das ist der minimale Kontext den die KI braucht um überhaupt sinnvolle Ansätze generieren zu können.

**Format:**

- **Das Problem:** Freitext – Was ist kaputt? Was fehlt? Was nervt?
- **Das Ziel:** Freitext – Wie soll der Zielzustand aussehen?

**Regeln:**

- Beide Felder sind Pflicht
- Es geht um die *Essenz*, nicht um die Lösung – noch keine technischen Details
- Der User soll in seiner eigenen Sprache beschreiben, nicht in Tech-Jargon

#### Beispiel (ImmoAlpha – Dynamische Zimmer-Analyse)

> **Das Problem:**
> Die Anzahl der Parameter die im Optik-Tab bei der Analyse angezeigt werden, ist statisch bei "Wohnzimmer, Schlafzimmer, ..." festgelegt. D.h. wenn eine Wohnung weniger oder mehr Zimmer hat, werden entweder nicht alle Zimmer ausgewertet oder es gibt Doppelnennungen o.Ä.
>
> **Das Ziel:**
> Bei einer Analyse soll gecheckt werden wie viele Zimmer es gibt und für jedes Zimmer soll es dann eine Analyse geben. Es sollen alle (nicht mehr, nicht weniger) Zimmer und Analysen angezeigt werden.

---

### Step 2: Einschätzung (KI-Analyse mit Codebase-Zugriff)

Bevor Ansätze erarbeitet werden, analysiert die KI **zuerst die Situation**. Das ist der erste KI-Schritt nach dem Klick auf **[🤖 Analyse starten]**. Die KI braucht dafür Zugriff auf die Codebase.

**Was die KI tut:**

1. Nimmt Problem + Ziel + optionale Ansätze als Input
2. **Scannt die Codebase** – findet die betroffenen Dateien, Funktionen, Datenstrukturen
3. Gibt eine strukturierte Einschätzung ab:

**Output der Einschätzung:**

| Dimension                           | Was wird bewertet                                                                           | Beispiel-Output                                                                             |
| ----------------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Workload**                  | Wie umfangreich sind die Änderungen? Wie viele Dateien/Schichten betroffen?                | "Mittel – betrifft 1 Edge Function, 1 Frontend-Komponente, keine DB-Änderung"             |
| **Scope**                     | Wie groß ist der Blast Radius? Gibt es Seiteneffekte auf andere Features?                  | "Gering – isoliert im Optik-Tab, keine Abhängigkeiten zu anderen Modulen"                 |
| **Erfolgswahrscheinlichkeit** | Wie wahrscheinlich ist eine erfolgreiche Umsetzung basierend auf der aktuellen Architektur? | "Hoch (85%) – Datenstruktur im Backend ist schon flexibel, Frontend muss angepasst werden" |
| **Risiken**                   | Was könnte schiefgehen? Wo sind die Fallstricke?                                           | "LLM-Prompt muss angepasst werden – Zimmer-Erkennung aus Freitext ist fehleranfällig"     |
| **Betroffene Dateien**        | Welche konkreten Dateien müssen angefasst werden?                                          | `exposeAnalyzer.ts`, `OptikTab.tsx`, evtl. `types.ts`                                 |

**Wozu das dient:**

- Der User bekommt **vor** dem Ausarbeiten der Ansätze ein Gefühl für den Aufwand
- Unrealistische Ziele werden früh erkannt ("Das ist kein Feature-Change sondern ein Refactoring von 3 Modulen")
- Falls der User Ansätze eingegeben hat: die KI prüft ob die Ansätze **mit der aktuellen Codebase kompatibel** sind
- Die Einschätzung fließt direkt in Step 3 ein – die KI nutzt ihr Codebase-Wissen um bessere Ansätze zu generieren

**Human-in-the-Loop:**

- Der User reviewed die Einschätzung: Stimmt das so? Ist der Workload akzeptabel?
- Falls nicht: User kann den Scope anpassen (zurück zu Step 1) oder entscheiden, dass es trotzdem gemacht werden soll
- Erst nach Bestätigung geht es weiter zu Step 3

#### Beispiel (ImmoAlpha – Einschätzung)

> **[🤖 Analyse starten]** geklickt mit:
>
> - Problem: "Statische Zimmer-Parameter im Optik-Tab..."
> - Ziel: "Dynamische Zimmer-Anzahl mit individueller Analyse..."
> - Ansätze: *(leer)*
>
> **Einschätzung der KI:**
>
> |                                     |                                                                                                  |
> | ----------------------------------- | ------------------------------------------------------------------------------------------------ |
> | **Workload**                  | 🟡 Mittel                                                                                        |
> | **Scope**                     | 🟢 Gering – Änderungen isoliert im Analyse-Flow                                                |
> | **Erfolgswahrscheinlichkeit** | 🟡 70% – hängt davon ab wie zuverlässig Zimmer aus Exposé-Freitext extrahiert werden können |
>
> **Betroffene Stellen:**
>
> - `supabase/functions/exposeAnalyzer.ts` – LLM-Prompt muss Zimmer dynamisch erkennen
> - `src/components/OptikTab.tsx` – Rendering von statisch auf dynamisch umbauen
> - `src/types/analysis.ts` – TypeScript-Interface für optische Bewertung anpassen
>
> **Risiken:**
>
> - ⚠️ Die Zimmer-Erkennung aus Freitext ist der kritischste Punkt. Exposés sind nicht einheitlich formatiert.
> - ⚠️ Was zählt als "Zimmer"? (Küche, Bad, Flur – ja oder nein?) → **muss vom User entschieden werden**

---

### Step 3: Ansätze erarbeiten

Der User kann optional eigene Ideen/Ansätze vorab eingeben. Dann klickt er den **[🤖 Ansätze generieren]** Button. Die KI nutzt hier die Einschätzung aus Step 2 als Grundlage.

**Was die KI tut:**

- Nimmt Problem + Ziel + Einschätzung aus Step 2 als Kontext
- Falls der User Ansätze eingegeben hat: arbeitet diese aus, ergänzt fehlende Details, identifiziert Schwächen
- Falls keine Ansätze eingegeben: generiert eigenständig mehrere mögliche Ansätze
- Jeder Ansatz wird **vollständig und im Detail** ausgearbeitet – so präzise, dass ein Entwickler (oder Agent) ihn 1:1 umsetzen könnte
- **Der Unterschied zu Step 2:** Die KI hat jetzt Codebase-Wissen. Die Ansätze sind keine abstrakten Ideen, sondern konkrete Implementierungspläne die zur bestehenden Architektur passen.

**Human-in-the-Loop:**

- Die generierten Ansätze sind ein **Vorschlag**, kein Ergebnis
- Der User kann editieren, nachbohren, Ansätze verwerfen, neue anfordern
- Der Schritt ist erst abgeschlossen wenn der User aktiv bestätigt: "Dieser Ansatz ist perfekt"
- **Ziel:** Am Ende von Step 3 steht ein Ansatz der so detailliert ist, dass es bei der Implementierung keine offenen Fragen mehr gibt

#### Beispiel (ImmoAlpha – Ansätze, KI-generiert)

> **Ansätze:** *(leer – User hat keine eigenen Ideen eingegeben)*
>
> *[🤖 Ansätze generieren wurde geklickt...]*
>
> *(KI generiert basierend auf Problem + Ziel + Codebase-Analyse konkrete Ansätze:)*
>
> - **Ansatz 1:** LLM-Prompt in `exposeAnalyzer.ts` so anpassen, dass Zimmer als dynamisches Array zurückgegeben werden. Frontend `OptikTab.tsx` rendert über `.map()` statt statischer Felder.
> - **Ansatz 2:** Zweistufiger Ansatz – erst Zimmer-Extraktion als separater LLM-Call, dann individuelle Bewertung pro Zimmer. Mehr API-Calls, aber höhere Genauigkeit.
> - **Ansatz 3:** ...
