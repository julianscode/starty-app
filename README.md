# Starter: Blockstart+Reaction

Erstelle eine mobile Web-App (PWA) (iOS/Android) mit dem Namen „Starter: Blockstart+Reaction“. Ziel ist es, Nutzer:innen beim Sprintstart aus dem Startblock zu unterstützen: Die App sagt eine Startsequenz an, erzeugt einen zufälligen Startschuss und misst die Reaktionszeit über die Bewegung des Handys (Handy liegt hinten am Startblock).

## Kernfunktion (Ablauf)

Ausgelöst durch einen großen Button „Start“:

1. Nach Klick auf „Start“:
   - Spiele/zeige „Auf die Plätze“ (großer Text + TTS-Sprachausgabe).
   - Warte eine feste Pause von 10,0 Sekunden (Option „Vorbereitungszeit“).
2. Danach:
   - Spiele/zeige „Fertig“ (großer Text + TTS).
3. Danach:
   - Warte eine zufällige Zeit zwischen MinDelay und MaxDelay.
   - Standard: MinDelay = 1,0 s, MaxDelay = 3,0 s.
   - Werte müssen über UI einstellbar sein (Slider).
4. Nach Ablauf der Zufallszeit:
   - Spiele einen Startschuss-Sound („Peng“; kurzer, klarer Impuls).
   - Exakt ab/mit dem Startschuss beginnt die Zeitmessung.

## Bewegungserkennung & Messlogik (Reaktionszeit)

Nach dem Startschuss:

- Starte eine hochpräzise Stoppuhr (Millisekunden).
- Nutze Gerätesensoren:
  - bevorzugt DeviceMotion/Accelerometer, alternativ Gyro.
- Messe die Reaktionszeit, indem du die Zeit stoppst, sobald das Handy eine kleine Bewegung registriert.

**Detection-Regeln**

- Bewegungs-Schwelle (Threshold) über Beschleunigungsänderung:
  - Betrag/Norm der Beschleunigung berechnen (ggf. accelerationIncludingGravity mit Filter).
  - Wenn Betrag über Threshold für mindestens N Millisekunden anliegt → Bewegung.
- Standardwerte:
  - Threshold: 0,25 g (oder äquivalent m/s²).
  - Mindestdauer: 30 ms.
- Kurze Sperrzeit (Debounce) z. B. 150 ms.
- Bei erkannter Bewegung:
  - Stoppuhr stoppen.
  - Ergebnis anzeigen („Reaktionszeit: 184 ms“).

## UI/Bedienung (einfach & sporttauglich)

Ein Screen mit klaren, großen Elementen, dunkler Modus geeignet fürs Stadion, an Apples Designsprache orientiert.

**Hauptbereich**

- Großer Status-Text: „Bereit“, „Auf die Plätze“, „Fertig“, „LOS!“
- Großer Button:
  - Zustand „Start“
  - während Sequenz: „Abbrechen“
  - nach Ergebnis: „Nochmal“

**Einstellungen** (unterhalb oder als aufklappbares Panel)

- Range-Slider / Doppelslider (oder zwei Slider/Inputs):
  - „Min. Zufallszeit“ (MinDelay)
  - „Max. Zufallszeit“ (MaxDelay)
  - Range: 0,1 s bis 10,0 s
  - Sicherstellen: MinDelay < MaxDelay (automatisch korrigieren)
  - Anzeige mit einer Dezimalstelle
- Option: „Vorbereitungszeit“ (Standard 10,0 s; Range 0–30 s)
- Option: „Sound an/aus“
- Option: „Sprachausgabe an/aus“
- Option: „Empfindlichkeit“ (Threshold) als Slider
- Button „Sensor testen“:
  - Zeigt live einen Bewegungsbalken (aktueller Beschleunigungswert)

**Ergebnisbereich**

- Letzte Reaktionszeit groß anzeigen
- Liste „Letzte 10 Versuche“
- Button „Reset Historie“

## Technische Anforderungen

- Sehr genaue Zeitmessung: `performance.now()` (Web) oder vergleichbar hochpräzise Timer.
- Sensorberechtigungen sauber anfragen:
  - iOS erfordert explizite Motion-Permission → Hinweis + Button „Sensorzugriff erlauben“.
- Offline-fähig als PWA (Caching der App-Shell).
- Keine Accounts/Cloud, Speicherung lokal (LocalStorage/IndexedDB).
- Audio:
  - Startschuss latency-arm, Audio vorab laden.
  - Optional kurze haptische Vibration beim Startschuss (falls verfügbar).

## Fehlerfälle & Sicherheit

- Wenn keine Sensoren verfügbar/Permission verweigert:
  - Fallback: Zeitmessung endet bei Screen-Tap („Ich bin los“), klar als Fallback markieren.
- Bewegungserkennung erst nach dem Startschuss aktivieren.
- „Abbrechen“ stoppt alle Timer und Sensorlistener sofort und setzt Status auf „Bereit“.

## Copy/Labels (Deutsch)

- App-Name: Starter
- Button: Start / Abbrechen / Nochmal
- Status: Bereit / Auf die Plätze / Fertig / LOS!
- Einstellungen: Zufallszeit (Min/Max), Vorbereitungszeit, Sound, Sprachausgabe, Empfindlichkeit
- Ergebnis: Reaktionszeit

## Akzeptanzkriterien

- Jede Runde hat eine neue Zufallszeit zwischen MinDelay und MaxDelay.
- Startschuss kommt immer erst nach „Auf die Plätze“ + Wartezeit + „Fertig“ + Zufallszeit.
- Timer startet exakt beim Startschuss und stoppt bei erster validierter Bewegung.
- Nutzer kann Min/Max Delay und Empfindlichkeit einstellen.
- App läuft stabil auf iOS/Android (Browser) und fragt Motion-Permission korrekt an.
