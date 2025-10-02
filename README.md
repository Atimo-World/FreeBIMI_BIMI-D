# FreeBIMI_BIMI-D
# BIMI-D / FreeBIMI – Offener Vorschlag  Dieses Repository enthält ein Whitepaper und Draft-Spezifikation für ein DNS-basiertes BIMI (Brand Indicators for Message Identification), das ohne teure Zertifikate auskommt. Ziel: faire E-Mail-Sichtbarkeit für alle, von Unternehmen bis zu Vereinen, Kulturschaffenden und Einzelpersonen.
# Whitepaper

## Faires BIMI ohne VMC – DNS‑basierte Markenverifikation und freie Logo-Wahl

**Autor**: Roberto Weinhold (aka *Atimo*), Atimo.World – Dresden
**Stand**: 02.10.2025

---

## 0) Executive Summary (eine Seite)

**Problem**: BIMI (Brand Indicators for Message Identification) zeigt Markenlogos in Postfächern. De‑facto verlangen große Provider ein **Verified Mark Certificate (VMC)**. Das kostet i. d. R. **1.000–1.500 € pro Domain/Jahr** und ist für KMU, Vereine und Kulturschaffende untragbar. Technisch ist ein VMC **nicht erforderlich**, weil Domain‑Kontrolle und DMARC‑Durchsetzung bereits vorliegen müssen.

**These**: Wer die DNS‑Zone kontrolliert und starke Absenderauthentisierung (SPF, DKIM, **DMARC: p=quarantine|reject**) betreibt, erbringt den relevanten technischen Eigentumsbeweis. Sichtbarkeit darf nicht an eine externe, kostenpflichtige EV‑Zertifizierung gekoppelt werden.

**Zusatz-These**: Sichtbarkeit darf auch **nicht allein vom Besitz eines Markenrechts** abhängen. Jeder Domaininhaber sollte das Recht haben, ein frei gewähltes Logo anzeigen zu lassen – solange die Domainhoheit technisch nachgewiesen ist. Markenregister können ein optionales Zusatzsignal sein, aber nicht die einzige Eintrittskarte.

**Lösungsvorschlag (BIMI‑D + FreeBIMI)**: Ein **DNS‑natives, kostenfreies** Verifikationsschema als Alternative/Ergänzung zum VMC:

* Erweiterung des BIMI‑TXT‑Records um **prüfbare Felder** (Markenname, Register, Jurisdiktion, Registernummer).
* Mailbox‑Provider prüfen: (1) DMARC‑Alignment, (2) DNS‑Ownership, (3) optionalen Abgleich mit Markenregistern (DPMA/EUIPO/WIPO).
* **Optional**: Freie Logo-Wahl („FreeBIMI“) ohne Markenrechtseintrag, solange DNS‑Ownership gegeben ist. Provider können Kennzeichnungen nutzen, um zwischen „frei“ und „verifiziert“ zu unterscheiden.
* **Backwards‑kompatibel**: VMC bleibt möglich, ist aber nicht mehr Voraussetzung für Logoanzeige.

**Ergebnis**:

* **Fairness**: Kein Kostenzwang → Sichtbarkeit für KMU, Vereine, Kultur.
* **Vielfalt**: Auch nicht eingetragene Marken oder Symbole von Communities werden sichtbar.
* **Sicherheit**: Gleichwertig, da DMARC+DANE/DNSSEC nutzbar; Registerabgleich minimiert Missbrauch.
* **Wettbewerb**: Ende des Oligopols weniger Zertifizierungsstellen.
* **Souveränität**: Offene Standards, keine Paywall.

**Politische Bitte**:

* Öffentliche Stellen sollen **keine** VMC‑Anschaffungen empfehlen/voraussetzen.
* **Standardisierung anstoßen** (DIN/ETSI/IETF) und **förderprogrammatisch** unterstützen.
* Provider sollen **zwei Modi** anbieten: „Verified BIMI“ (mit Registerdaten) und „FreeBIMI“ (nur DNS‑Ownership).

---

## 1) Ausgangslage & Problemstellung

* BIMI steigert Absender‑Vertrauen und UX.
* Markierung wird aber häufig **nur mit VMC** angezeigt (z. B. bei Google/Yahoo).
* **Kostenbarriere**: 1.000–1.500 € / Jahr / Domain (plus ggf. Beratungs-/Anwaltskosten).
* **Marktverzerrung**: Konzerne profitieren, kleine Anbieter bleiben unsichtbar – trotz einwandfreier Authentisierung.
* **Kulturelle Beschränkung**: Logos sind oft nur dann erlaubt, wenn eine Marke juristisch eingetragen ist. Damit bleiben freie Szenen, Kollektive und Projekte außen vor.

**Kernkritik**: Die *tatsächliche* Sicherheitsleistung erbringen SPF/DKIM/DMARC samt Domain‑Kontrolle. VMC ist eine juristische Besitzbestätigung, die die Sichtbarkeit „monetarisiert“ und damit ein Oligopol bedient. Markenrecht wird missbraucht, um Sichtbarkeit an juristische Registrierung zu koppeln.

---

## 2) Technischer Hintergrund (prägnant)

* **SPF**: IP‑Berechtigung für Envelope‑From.
* **DKIM**: Kryptografische Signatur über Header/Body mit DNS‑Public‑Key.
* **DMARC**: Richtlinie & Alignment (Header‑From ↔ DKIM/SPF), Aggregate‑ und Forensik‑Reports; wirksam erst mit **p=quarantine|reject**.
* **BIMI**: DNS‑TXT unter `default._bimi.<domain>` → verweist auf **SVG‑Logo** und optional **VMC**.

---

## 3) Warum VMC keine technische Notwendigkeit ist

1. **Ownership** ist implizit nachgewiesen: Nur wer DNS kontrolliert, kann BIMI/DMARC setzen.
2. **Phishing‑Schutz** entsteht primär durch DMARC‑Enforcement, nicht durch ein EV‑Artefakt.
3. **Markenrechtliche Fragen** betreffen nur kommerzielle Namenskonflikte – technisch lösbar durch Provider-Policies.
4. **Freiheit der Logo-Wahl**: Provider können unterschiedliche Status („Verified“ vs. „Free“) klar kennzeichnen, statt Nicht-Marken komplett auszuschließen.

---

## 4) Vorschlag: BIMI‑D (DNS‑native Verifikation) & FreeBIMI

### 4.1 Ziel

Eine **frei nutzbare, standardisierte** Alternative zum VMC, die Provider implementieren können, ohne Lizenzkosten. Fokus: Fairness, Sicherheit, Interoperabilität, Vielfalt.

### 4.2 Record‑Syntax (erweiterter BIMI‑TXT)

**Beispiel (mit Marke):**

```
default._bimi.example.org. IN TXT 
"v=BIMI1; l=https://example.org/brand.svg; a=; m=marke=Ball Bizarr; r=DPMA; j=DE; n=3020252050562; mode=verified"
```

**Beispiel (frei gewählt, ohne Marke):**

```
default._bimi.example.org. IN TXT 
"v=BIMI1; l=https://example.org/community-logo.svg; a=; mode=free"
```

**Neue Variable:**

* `mode` = „verified“ oder „free“

### 4.3 Provider‑Prüfregeln (Kurzlogik)

1. DMARC‑Alignment muss bestehen (p≥quarantine).
2. DNS‑Ownership: Record + Logo erreichbar; **DNSSEC bevorzugt**.
3. Wenn `mode=verified` → Abgleich mit Markenregistern.
4. Wenn `mode=free` → Logo anzeigen, aber ggf. mit Hinweis „Nicht markenrechtlich geprüft“.
5. Bei Konflikten: Degradieren auf Initialen statt Logo blockieren.

### 4.4 Sicherheit & Missbrauchsabwehr

* **DNSSEC** empfohlen zur Integrität des Records.
* **SVG‑Policy** (Tiny‑PS, no scripts, bounded viewBox).
* **Meldemechanismus** für missbräuchliche Logos.
* **Kennzeichnung** in Mailclient: Verified BIMI (mit Check-Symbol), FreeBIMI (mit Hinweis-Symbol).

---

## 5) Implementierungsfahrplan

**Phase 1 – Pilot (3–6 Monate)**

* Öffentliche Spezifikation (GitHub/IETF‑Draft).
* Zwei Referenz‑Provider implementieren Parser & Prüfregeln.
* Öffentliche Test‑Suite, Generator/Validator.

**Phase 2 – Ökosystem (6–12 Monate)**

* DPMA/EUIPO/WIPO **öffentliche Abfragepfade** dokumentieren.
* Open‑Source‑Library für MTA/MUA‑Integrationen.
* Outreach an Thunderbird, Apple Mail, Outlook.

**Phase 3 – Standardisierung (12+ Monate)**

* IETF Draft, ETSI/DIN‑Begleitung.
* Empfehlungsschreiben von Verbraucher‑/KMU‑Verbänden.

---

## 6) Kosten‑/Nutzen‑Analyse

| Kriterium           | Status quo (VMC)       | BIMI‑D Verified     | FreeBIMI                |
| ------------------- | ---------------------- | ------------------- | ----------------------- |
| Fixkosten je Domain | 1.000–1.500 €/Jahr     | 0 €                 | 0 €                     |
| Technische Hürde    | hoch                   | niedrig             | sehr niedrig            |
| Sicherheit          | gut (jurist. Nachweis) | gut (Registercheck) | ausreichend (DNS+DMARC) |
| Fairness            | niedrig                | hoch                | sehr hoch               |
| Vielfalt            | niedrig                | mittel              | sehr hoch               |

---

## 7) Rechtliche Würdigung (Kurz)

* **Markenrecht**: Der *Schutz* entsteht durch Eintragung und Nutzung – FreeBIMI respektiert das, indem es Logos ohne Marke zwar zulässt, aber nicht als juristisch geprüft darstellt.
* **Wettbewerbsrecht**: Paywall-Sichtbarkeit benachteiligt kleine Anbieter → Wettbewerbsverzerrung.
* **Digitale Selbstbestimmung**: Domain = Identität. Logo-Wahl darf nicht exklusiv an Markenrecht gekoppelt sein.

---

## 8) Politische Empfehlungen

1. **Neutralität** der öffentlichen Hand: Keine VMC‑Pflicht/Empfehlung.
2. **Förderung** von DMARC/DNSSEC bei KMU und Vereinen.
3. **Standardisierung**: „BIMI‑D Verified“ & „FreeBIMI“ als gleichwertige Modi.
4. **Community-Sichtbarkeit**: Digitale Vielfalt auch ohne Markenrecht ermöglichen.

---

## 15) Schluss

Sichtbarkeit im Posteingang darf keine Frage des Budgets oder eines Markeneintrags sein. **BIMI‑D Verified** und **FreeBIMI** machen den Weg frei: gleiche Sicherheit, offene Standards, echte Fairness und Vielfalt.

Jetzt ist der richtige Moment, das Oligopol aufzubrechen und E‑Mail wieder ein Stück öffentlicher Infrastruktur zu machen – **für alle**.
