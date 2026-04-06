# Humanizer-DE

**Erster deutscher KI-Text-Detektor für OpenClaw.** Erkennt statistische Muster von KI-generiertem Text und korrigiert sie automatisch.

[![Version](https://img.shields.io/badge/version-1.1.2-orange)]()
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![ClawHub](https://img.shields.io/badge/ClawHub-Tikitackr%2Fhumanizer--de-blue)](https://clawhub.ai/Tikitackr/humanizer-de)
[![Node.js](https://img.shields.io/badge/Node.js-%3E%3D18-brightgreen)]()

> **Score 0** = klingt menschlich. **Score 100** = klingt wie ChatGPT auf Autopilot.

---

## Zwei Betriebsarten

| Modus | Was passiert | Umfang |
|-------|-------------|--------|
| **Agent-Modus** (SKILL.md) | OpenClaw führt die vollständige 5-Durchgangs-Analyse durch | 24 KI-Muster, 125+ Vokabeln in 3 Tiers, 48 Phrasen, 5 Statistik-Signale, Personality Injection, Stil-Layer |
| **CLI-Modus** (humanize-de.js) | Standalone Node.js-Script, läuft ohne KI | Tier-1/2-Vokabelcheck (90 Wörter), 48 Phrasen, 16 Chatbot-Artefakte, 5 Statistik-Signale, Co-Occurrence-Cluster, Auto-Fix |

Der **Agent-Modus** nutzt Claude als Analysemotor und hat Zugriff auf alle Reference-Dateien – inklusive 24 KI-Schreibmuster-Erkennung und Personality Injection. Das **CLI-Tool** ist ein regelbasiertes Subset für schnelle Checks ohne KI-Kosten.

---

## Installation

### Als OpenClaw Skill

Sag deinem OpenClaw:

> *„Installiere den Skill Tikitackr/humanizer-de"*

### CLI-Tool (standalone)

```bash
git clone https://github.com/Tikitackr/humanizer-de.git
cd humanizer-de/scripts
node humanize-de.js score datei.md
```

Keine externen Dependencies – nur Node.js `fs` und `path`.

---

## Befehle

### Agent-Modus (im Chat)

| Befehl | Was passiert |
|--------|-------------|
| `Check diesen Text` | Vollständiger Report: Score + Muster + Vokabeln + Statistik + Vorschläge |
| `Score: [Text]` | Nur der Score (0–100) mit Kurzeinschätzung |
| `Was klingt hier nach KI?` | Nur die problematischen Stellen markiert |
| `Humanisiere das` | Text umschreiben mit Personality Injection (Basis-Stil) |
| `Humanisiere das im Lesch-Stil` | Umschreiben mit Lesch-Layer (Visionär/Mahner/Erklärer) |

### CLI-Modus (Terminal)

```bash
node humanize-de.js score   datei.md    # Nur Score (0-100)
node humanize-de.js analyze datei.md    # Vollständiger Report
node humanize-de.js suggest datei.md    # Ersetzungsvorschläge
node humanize-de.js fix     datei.md    # Auto-Ersetzung (erstellt .bak-Backup)
```

---

## Was wird analysiert?

### 24 KI-Schreibmuster (Agent-Modus)

Erkennt 24 typische Muster, die KI-generierte deutsche Texte verraten – von Bedeutungsinflation über Aufzählungs-Sucht bis zu leeren Verstärkern. Jedes Muster hat einen Schweregrad (HOCH / MITTEL / NIEDRIG) und konkrete Umschreibvorschläge.

### KI-Vokabeln & Phrasen in 3 Tiers

Die Reference-Datenbank umfasst **125+ Vokabeln** und **48 Phrasen**:

- **Tier 1 (VERBOTEN):** 45 Wörter die sofort auffallen – „ermöglicht", „nahtlos", „maßgeblich"
- **Tier 2 (SPARSAM):** 46 Wörter die in Maßen okay sind, aber bei Häufung KI signalisieren
- **Tier 3 (BEOBACHTEN):** 34 Wörter die nur im Cluster verdächtig werden
- **Verbotene Phrasen:** 48 Phrasen die sofort raus müssen – „Es ist wichtig zu beachten", „In der heutigen Welt"

Plus **7 Co-Occurrence-Sets** – Wort-Cluster die gemeinsam auftreten und KI-Herkunft verraten.

### 5 Statistische Signale

| Signal | Was es misst |
|--------|-------------|
| Burstiness | Variation der Satzabstände (KI schreibt gleichmäßig) |
| Type-Token-Ratio | Wortschatz-Vielfalt pro 100-Wort-Fenster |
| Satzlängen-CoV | Variation der Satzlängen (KI = monoton) |
| Trigramm-Wiederholung | Wiederholte 3-Wort-Folgen |
| Flesch-DE | Lesbarkeit (KI-Texte landen oft im Sweetspot 40–50) |

### Morphologischer Auto-Fix (v1.1.0+)

Der `fix`-Befehl ersetzt KI-typische Vokabeln automatisch und überträgt dabei:
- **Adjektiv-Suffixe** (-e, -en, -em, -er, -es)
- **Nomen-Suffixe** (Genitiv-s, Plural)
- **Artikel-Korrektur** bei Genus-Wechsel (der/die/das)
- **Dativ-Heuristik** nach Präpositionen

Vor jeder Korrektur wird automatisch ein `.bak`-Backup erstellt.

---

## Kalibrierung

Getestet an 7+ Texten:

| Texttyp | Score | Erwartung |
|---------|-------|-----------|
| Menschlich geschrieben | 0–8 | 0–30 |
| Lesch-Stil (KI-bereinigt) | 2–6 | 0–30 |
| Subtiler KI-Text | 42 | 30–60 |
| Typischer ChatGPT-Text | 64–92 | 60–100 |
| Offensichtlicher KI-Text | 98 | 60–100 |

---

## Dateistruktur

```
humanizer-de/
├── _meta.json                          Skill-Metadaten
├── README.md                           Diese Datei
├── SKILL.md                            Agent-Workflow (5-Durchgangs-Analyse)
├── references/
│   ├── ki-muster.md                    24 KI-Schreibmuster (deutsch)
│   ├── vokabeln.md                     125+ KI-Vokabeln & Phrasen in 3 Tiers
│   ├── statistische-signale.md         5 statistische Signale mit Formeln
│   ├── personality-injection.md        5 Humanisierungs-Techniken
│   ├── examples.md                     6 Vorher/Nachher-Transformationen
│   └── stil-layer/
│       ├── basis.md                    Standard-Humanisierung
│       └── lesch.md                    Lesch-Erweiterung
├── scripts/
│   └── humanize-de.js                  Node.js CLI-Tool (~1220 Zeilen)
└── _dev/
    └── SESSION-LOG.md                  Entwicklungsprotokoll
```

---

## Teil des OpenClaw-Projekts

Dieser Skill ist eigenständig nutzbar – du brauchst kein Buch und kein Dashboard.

Wenn du mehr willst: Der Skill ist Teil des **OpenClaw Sachbuch-Projekts**, das erklärt wie man KI-Agenten baut, betreibt und qualitätssichert. Das Buch nutzt den Humanizer selbst für die eigene Textqualität (Dogfooding).

- [Dashboard](https://openclaw-buch.de)
- [Companion (mobiler Buch-Begleiter)](https://openclaw-buch.de/?module=companion)

---

## Lizenz

MIT – mach damit was du willst. Wenn du ihn verbesserst, teil es gern auf [ClawHub](https://clawhub.ai).
