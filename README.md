# ACI 1.4.1 – Paper / Minecraft 1.21.3

## Voraussetzungen
- Paper 1.21.3 (Java 21)
- Maven 3.9+

## Plugin bauen
Im Projektordner `ACI` ausführen:
```bash
mvn -B -ntp clean package
```
Die fertige Datei liegt danach unter `target/ACI-1.4.1.jar`.
Der GitHub-Actions-Workflow baut sie bei Push/Tag automatisch. Ein Tag `v1.4.1` veröffentlicht JAR und Resourcepack als Release-Anhänge.

## Installation
1. Server stoppen und `plugins/ACI/` sichern.
2. Alle alten ACI-JARs aus `plugins/` entfernen (nur eine ACI-JAR laden).
3. Die in GitHub Actions gebaute `ACI-1.4.1.jar` nach `plugins/` kopieren.
4. Server starten. Beim ersten Upgrade wird `items.yml` gesichert und die eingebauten alten Kristalle werden durch exakt 38 neue Kristalle ersetzt. Andere Custom-Items bleiben erhalten.
5. Alte Kristall-Stacks im Inventar werden beim Join aktualisiert. Falls ein alter Kristall noch das alte Modell zeigt, ihn neu mit `/aci give <id>` ausgeben.

## Ingame-Settings
- `/aci` öffnen, einen Eintrag anklicken und im Item-Editor den Komparator **Attributes, cooldown & other settings** auswählen.
- Cooldown in Sekunden: `0` löst einmal beim Ablegen in die Nebenhand aus; Werte größer `0` lösen die eingestellte Fähigkeit während des Tragens in der Nebenhand erneut nach Ablauf aus.
- `Auto offhand` ein-/ausschalten.
- Fähigkeit im Chat hinzufügen: `RESISTANCE 4 10` (Effekt, Stufe, Sekunden). Shift-Klick entfernt den letzten Effekt.
- Attribut im Chat hinzufügen: `MAX_HEALTH 4 ADD_NUMBER ANY` (Attribut, Wert, Operation, Slotgruppe). Shift-Klick entfernt den letzten Modifier.
- Enchantment hinzufügen: `sharpness 5`. Shift-Klick entfernt die letzte Verzauberung.
- Shulker-Reihen durch Klicken von 1 bis 6 einstellen; Auto-Pickup kann umgeschaltet werden. Wenn das Spielerinventar voll ist, wird der Rest gedroppt.
- Passive Effekte bleiben im separaten Effekte-Menü; Effekt-Auswahl nutzt passende Item-Icons.

## Crafting
- Custom-Items sind als Zutaten für andere ACI-Rezepte verwendbar.
- ACI blockiert Vanilla-Crafting nicht; Custom-Items zählen in Vanilla-Rezepten als ihr Basismaterial (z. B. ein Custom-Enderauge als `ENDER_EYE`).

## 38 Kristalle / Resourcepack
- Genau 38 einzelne Kristall-Items, basierend auf dem gelieferten Bild; alle verwenden separate transparente 128×128-PNG-Texturen.
- Jede Farbe hat einen MiniMessage-Gradientnamen.
- Java 1.21.3 Resourcepack-Format: `42`.
- `CustomModelData` 71001–71038 wird in Plugin und Pack in derselben Reihenfolge verwendet.
- Für manuelle Installation das **Resourcepack-ZIP direkt** in `%appdata%\.minecraft\resourcepacks` legen und unter Optionen → Ressourcenpakete aktivieren. Nicht das komplette Quellcode-/Installations-ZIP in den Minecraft-Ordner legen.

## Automatisches Resourcepack beim Join
Standardmäßig ist der interne HTTP-Host aktiviert:
- öffentliche Adresse: `neptunsmp.de`
- TCP-Port: `8123` (muss beim Hoster freigegeben/weitergeleitet sein)
- URL: `http://neptunsmp.de:8123/aci-pack.zip`

Für GitHub Releases nach dem Tag `v1.4.1` in `plugins/ACI/config.yml` den direkten Asset-Link und SHA-1 eintragen:
```yaml
resource-pack:
  send-on-join: true
  url: "https://github.com/OWNER/REPO/releases/latest/download/ACI-Crystals-ResourcePack-1.21.3-v1.4.1.zip"
  sha1: "4cb2fba185d3885289e67b034da8d05b67275fb3"
  host:
    enabled: false
```
`OWNER/REPO` muss durch das echte Repository ersetzt werden. Der direkte GitHub-Release-Link funktioniert erst, nachdem der Release hochgeladen wurde.

## GitHub Release erstellen
1. Den Inhalt des Projektordners `ACI` in den Stamm deines GitHub-Repositories hochladen.
2. Den Branch `main` pushen. GitHub Actions baut dann eine JAR als Workflow-Artefakt.
3. Den Tag `v1.4.1` erstellen und pushen:
   `git tag v1.4.1` und `git push origin v1.4.1`.
4. Im GitHub-Release erscheinen `ACI-1.4.1.jar` und `ACI-Crystals-ResourcePack-1.21.3-v1.4.1.zip` als Assets.

## Teststatus / bekannte Grenzen
- Resourcepack-Dateistruktur, PNG-Texturen, 38 Modell-Dateien und 38 `CustomModelData`-Zuordnungen wurden lokal geprüft.
- Java-Quellcode und Paper-API-Kompatibilität sind für Paper 1.21.3 ausgelegt. In dieser Arbeitsumgebung sind Maven und Paper-API nicht verfügbar und der Maven-Host war nicht erreichbar; darum wurde hier keine endgültige JAR kompiliert und kein Live-Serverstart durchgeführt. Die GitHub-Actions-Workflow-Datei kompiliert die JAR im GitHub-Runner.
- Bukkit-Cooldown-Anzeige ist materialbasiert. Da alle 38 Kristalle `AMETHYST_SHARD` verwenden, teilen sie sich die sichtbare clientseitige Cooldown-Anzeige; die internen Timer sind pro Spieler und Item-ID getrennt.
- Custom-Shulker sind virtuelle Inventare mit 9–54 Slots, deren Inhalte am platzierten Block gespeichert werden. Vor Live-Einsatz bitte auf einer Serverkopie testen, besonders zusammen mit Protection- und Inventar-Plugins.
