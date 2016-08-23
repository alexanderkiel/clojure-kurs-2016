# Clojure Kurs 2016

Dieser Kurs bietet eine Einführung in die Programmiersprache Clojure und hat zum Ziel, dass die Teilnehmer eine einfache Webapplikation in Clojure implementieren können. Einzige Vorraussetzung sind gundlegende Kenntnisse der Clojure Syntax, der Datentypen und Datenstrukturen.

Der Kurs auf eine Stunde pro Woche ausgelegt. Jede Einheit wird Session genannt. Es wird ein Skript geben, welches jede Session für die Teilnehmer nachvollziehbar macht.

Gegenstand des gesamten Kurses ist die Implementierung einer elektronischen Kaffeeliste. Benutzern soll es ermöglicht werden, mittels QR Code ein Getränk abzuscannen, welches auf eine Entsprechende Liste gesetzt wird.

## Session 1

Ziel dieser Session ist es einen Überblick über die Anforderungen des Projektes der Kaffeeliste zu bekommen und das Projekt in Grundzügen anzulegen.

### Erstellen eines Clojure Projektes

Vorraussetzung ist eine funktionierende Leiningen Installation und IntelliJ IDEA mit Cursive als Entwicklungsumgebung.

Ein Clojure Projekt wird in der Kommandozeile mit dem Befehl `lein new <my-project>` angelegt. Der `lein new` Befehl ermöglicht es, Projekte nach verschiedenen Vorlagen anzulegen. Wir wählen hier die Standardvorlage. Unser Projekt nennen wir `coffee`:

    $ lein new coffee
    
Als Versionsverwaltungssystem setzen wir Git ein. Um Git in unserem Projekt zu initialisieren, wechseln wir einmal mit `cd coffee` in unser Projektverzeichnis und führen dort `git init` aus.
    
Danach öffnen wir unser Projekt in IDEA. Das Projekt sollte von IDEA automatisch als Leinigen Projekt erkannt werden. Gut zu erkennen ist das daran, dass im Leiningen Toolfenster ein Eintrag `coffee:0.1.0-SNAPSHOT` existiert. Weiterhin wird IDEA das initialisierte Git Repository erkennen. In den Einstellungen sollte unter `Version Control` ein Eintrag `<Project>` - `Git` auftauchen. Wenn wir schon einmal in den Einstellungen sind, können wir gleich den Hacken bei `Commit message right margin (columns) 72` setzen.

Weiterhin setzen wir unter `Editor` -> `Code Style` den rechten Rand (Right margin) auf 80 Zeichen. Damit bekommen wir im Editor einen vertikalen Strich angezeigt, der uns daran erinnert, dass unser Quellcode nicht breiter als 80 Zeichen werden soll. 80 Zeichen ist ein gutes Maß für Clojure Quellcode.

Als nächstes löschen wir Dateien und Verzeichnisse, die wir nicht benötigen. Das sind: `doc`,
`.hgignore` und `CHANGELOG.md`. Danach fügen wir die beiden Einträge `/.idea/` und `/*.iml` in der `.gitignore` hinzu.

Im Version Control Toolfenster sollten jetzt 6 unversionierte Dateien auftauchen. Wir fügen all diese Dateien hinzu, so dass diese jetzt unter der Changelist `Default` auftauchen.

### Struktur eines Clojure Projektes

Unser Clojure Projekt besteht aus folgenden Verzeichnissen:

* `.git` - Projektverwaltungsdateien von Git, werden nie manuell angefasst
* `.idea` - Projektverwaltungsdateien von IDEA, werden nicht ins Git eingecheckt (.gitignore) und werden nicht manuell editiert
* `resources` - Artefakte, wie Bilder und Textdateien, die kein Quellcode sind
* `src` - Quellcode
* `target` - Kompilate (werden durch `lein clean` gelöscht)
* `test` - Testcode
* `.gitignore` - Auflistung der von Git zu ignorierenden Dateien und Verzeichnisse
* `coffee.iml` - IDEA Projektdatei, wird nicht ins Git eingecheckt (.gitignore) und wird nicht manuell editiert
* `LICENSE` - Lizenzdatei, bei Clojure Projekten üblicherweise eine Eclipse Public License, kann natürlich ausgetauscht werden
* `project.clj` - Leiningen Clojure Projektdatei, unabhängig von IDEA, wird manuell editiert
* `README.md` - README Datei im [Markdown][1] Format

Das `src` und `test` Verzeichnis enthält den Quell- bzw. Testcode. Clojure Quellcode ist in sogenannten Namespaces organisiert. Namespaces (also Namensräume) dienen dazu den Quellcode in logisch voneinander abtrennbare Einheiten zu unterteilen. Als Programmierer ist man hier relativ frei. Wir werden mit der Zeit ein Gefühl dafür entwickeln, welche Namespaces ein Projekt üblicherweise hat und wie wir unseren Quellcode aufteilen. Ein Namespace ist in den meisten Clojure Projekten vorhanden. Dieser Namespace heißt `<my-project>.core`. In Bibliotheken enthält dieser Core-Namespace meist die Public API. In Applikationen enthält der Core-Namespace üblicherweise die `-main` Funktion.

### Erste Hello World Ausgabe

Wir passen unseren Core-Namespace `coffee.core` an, indem wir die Funktion `foo` durch folgende Funktion ersetzen:
 
```clojure
(defn -main [& args]
  (println "Hello, World!"))
```

weiterhin spezifizieren wir in der `project.clj` unseren Core-Namespace als Hauptnamensraum:

```clojure
:main coffee.core
```

Danach konnen wir unser Programm mittels `lein run` ausführen.

### Der erste Webserver

Wir fügen die Bibliothek `[aleph "0.4.1"]` zu unseren Projektabhänigkeiten hinzu. [Aleph][2] stellt neben Dingen einen Webserver bereit. Wir ersetzen unseren Core-Namespace durch folgenden Quellcode:

```clojure
(ns coffee.core
  (:require [aleph.http :as http]))

(defn handler [req]
  {:status 200
   :body "Hello, World!"})

(defn -main [& args]
  (println "Starting webserver...")
  (http/start-server handler {:port 8080})
  (println "done.")
  (Thread/sleep 60000))
```

Nachdem wir `lein run` ausgeführt haben, können wir im Browser auf `http://localhost:8080` welchseln. Wir sollten die Ausgabe `Hello, World!` sehen.

[1]: <https://daringfireball.net/projects/markdown/>
[2]: <http://aleph.io>
