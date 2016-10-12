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

Das `src` und `test` Verzeichnis enthält den Quell- bzw. Testcode. Clojure Quellcode ist in sogenannten Namespaces organisiert. Namespaces (also Namensräume) dienen dazu den Quellcode in logisch voneinander abtrennbare Einheiten zu unterteilen. Als Programmierer ist man hier relativ frei. Wir werden mit der Zeit ein Gefühl dafür entwickeln, welche Namespaces ein Projekt üblicherweise hat und wie wir unseren Quellcode aufteilen. Ein Namensraum ist in den meisten Clojure Projekten vorhanden. Dieser Namensraum heißt `<my-project>.core`. In Bibliotheken enthält dieser Core-Namensraum meist die Public API. In Applikationen enthält der Core-Namensraum üblicherweise die `-main` Funktion.

### Erste Hello World Ausgabe

Wir passen unseren Core-Namensraum `coffee.core` an, indem wir die Funktion `foo` durch folgende Funktion ersetzen:
 
```clojure
(defn -main [& args]
  (println "Hello, World!"))
```

weiterhin spezifizieren wir in der `project.clj` unseren Core-Namensraum als Hauptnamensraum:

```clojure
:main coffee.core
```

Danach konnen wir unser Programm mittels `lein run` ausführen.

### Der erste Webserver

Wir fügen die Bibliothek `[aleph "0.4.1"]` zu unseren Projektabhänigkeiten hinzu. [Aleph][2] stellt neben Dingen einen Webserver bereit. Wir ersetzen unseren Core-Namensraum durch folgenden Quellcode:

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

## Session 2

Heute wollen wir ein HTML Formular erzeugen, welches es erlaubt ein Getränk abzurechnen. Wir werden uns dabei auf die HTML Ausgabe konzentrieren und lassen die Datenbank erst einmal weg. Wir implementieren das Feature also von der Oberfläche hin zum Backend. Dies ist ein übliches Vorgehen, um im Backend nur das absolut Notwendige umzusetzen.

### Klonen des Github Projektes Takelist

Ich habe unser Projekt vom letzten Mal unter [takelist][3] auf Github gestellt. Wir klonen das Repository mittels IDEA über `VCS` -> `Checkout from Version Control` -> `Git`. Danach starten wir unseren Webserver mittels `lein run` und schauen, dass auf `http://localhost:8080` die Hello World Ausgabe sehen.

### Erstellen eines User Namespaces (Issue 1)


Wir erstellen auf der obersten Ebene unseres Projektverzeichnisses ein `dev` Verzeichnis. Dort landen alle Quellen, die nur zur Entwicklungszeit benötigt werden. In der `project.clj` tragen wir unter `:profiles` Folgendes ein:

```clojure
{:dev
 {:source-paths ["dev"]
  :dependencies [[org.clojure/tools.namespace "0.2.11"]]}
 
 :production
 {:main takelist.core}}
```

Damit verschieben wir die Definition des Hauptnamensraumes in das Profil `:production` und fügen das Verzeichnis `dev` zu den Quellcode Verzeichnissen hinzu. Die Abhängigkeit `tools.namespace` benötigen wir für das Neuladen unseres Quellcodes zur Laufzeit. Nach einem Reload im Leiningen Toolfenster, sollte das `dev` Verzeichnis blau eingefärbt werden.

Wir legen im `dev` Verzeichnis folgende `user.clj` an:

```clojure
(ns user
  (:require [clojure.tools.namespace.repl :refer [refresh]]))

(comment
  (refresh)
  )
```

Ein Aufruf der `refresh` Funktion wird später unseren Quellcode zur Laufzeit neu laden. Wir erstellen im IDEA eine Run Konfiguration, welche uns eine REPL zur Verfügung stellt. Dazu gehen wir auf `Run` -> `Edit Configurations` und legen dort eine neue `Clojure REPL` -> `Local` an. Wir nennen die Run Konfiguration `REPL` und ändern die `Before launch` Konfiguration auf `Synchronize Leiningen Projects` ab. Danach starten wir unsere REPL. Die Ausgabe sollte folgendermaßen aussehen:

```
Starting nREPL server...
/Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/bin/java ...
Connecting to local nREPL server...
Clojure 1.8.0
nREPL server started on port 59747 on host 127.0.0.1 - nrepl://127.0.0.1:59747
```

Nachdem wir unsere REPL gestartet haben, wir automatisch der `user` Namensraum geladen. Unser `takelist.core` Namensraum wird hingegen nicht geladen und selbst dann wird nicht die dort enthaltene `-main` Funktion ausgeführt. Dies geschied lediglich beim Aufruf von `lein run` mit entsprechender `:main` Konfiguration. D.h. wir können entweder die `-main` Funktion in der REPL aufrufen oder den Server anderweitig starten. Wir entscheiden uns für letzteres, da wir die `-main` Funktion speziell als produktiven Einstiegspunkt erhalten wollen, wohingegen wir in der REPL zur Entwicklungszeit eine etwas abweichende Startprozedur verwenden, die es uns erlaubt den Quellcode zur Laufzeit neu zu laden.

Um den Server im `user` Namensraum starten zu können fügen wir folgende Abhänigkeiten hinzu:
 
```clojure
[aleph.http :as http]
[takelist.core :refer [handler]]
```

Weiterhin definieren wir die Variable `server` wie folgt:

```clojure
(def server (http/start-server handler {:port 8080}))
```

und fügen `(.close server)` in den Kommentarblock vor `(refresh)` hinzu.

Wenn wir jetzt einmal die REPL komplet neu starten, sollte unser Server laufen und `Hello, World!` ausgeben. Danach ändern wir die Hello World Ausgabe etwas ab, speichern die Datei und rufen nacheinander `(.close server)` und `(refresh)` auf. Die Ausgabe sollte sich im Browser entsprechend ändern.

Wir müssen den Server schließen, damit dieser seine Ressourcen (den offenen Port) freigeben kann. Beim Neuladen des `user` Namensraumes wird unser Server dann automatisch neu gestartet, da die Definition der Variablen `server` neu ausgeführt wird. Später werden wir das starten und stoppen des Servers noch verbessern.

## Session 3 (30.08.16)

### Ausgabe des HTML Formulars zur Getränkeabrechnung

Zur Ausgabe von HTML werden wir die Bibliothek [Hiccup][4] verwenden. Dazu binden wir die Abhängigkeit `[hiccup "1.0.5"]` in unsere `project.clj` ein. Hiccup erlaubt es uns HTML Seiten in Clojure Datenstrukturen anzulegen. D.h. wir können darauf verzichten Strings zusammenbauen zu müssen. Eine andere Möglichkeit ware es ein Template System zu verwenden. Das hätte den Vorteil, dass ein Designer das Template der Webseite erstellen kann, welcher Clojure nicht beherrscht. Solch ein Template System ist beispielsweise [Enlive][5]. Für uns ist es aber vorteilhafter direkt in Clojure zu bleiben. Ein Template System wäre zu aufwendig. 

TODO: vervollständigen

## Session 4 (13.09.16)

Wir haben heute unser HTML Formular weiter ausgebaut. Dabei haben wir [Bootstrap][6] als Basis für CSS eingesetzt. Wir werden auch weiterhin regen Gebrauch von HTML und CSS machen. Wer in dem Gebiet nicht so fit ist, für den ist die Bootstrap Dokumentation und die Seite [w3schools.com][6] ein guter Startpunkt.

Weiterhin haben wir die Seite [ClojureDocs][7] und dort speziell die [Quickref for Clojure Core][8] benutzt, um geeignete Funktionen zu finden. Ganz neu dabei war die [Sequence Abstraktion][9], die ich so noch nicht behandelt hatte. Sequenzen stellen dabei als Konzept eine Obermenge von Listen, Vektoren und Lazy Sequenzen dar, mit deren Hilfe auf diese unterschiedlichen Datenstrukturen gleichartig zugegriffen werden kann.

Lazy Sequenzen werden im Gegensatz zu normalen Listen erst beim Zugriff realisiert. Dadurch kann man mit unendlich langen Sequenzen arbeiten, solange man davon nur eine endliche Anzahl von Elementen benutzt. 

Ein Beispiel ist die Liste aller natürlicher Zahlen, die man durch die `(range)` Funktion erzeugen lassen kann. Wenn man davon nur die ersten 5 Elemente nimmt, kann das Ergebnis problemlos ausgegeben werden.

```Clojure
(take 5 (range))
;;=> (0 1 2 3 4)
```

Lazy Sequenzen ermöglichen es eine geringe Kopplung und damit ein hohes Maß an Unabhängigkeit zwischen Producer und Consumer herzustellen. Der Producer `(range)` muss in unserem Fall nicht wissen wie viele Elemente der Consumer `(take 5 ...)` benötigt. Lazy Sequenzen sind ein wichtiger Baustein der Funktionalen Programmierung. Dabei gehen Sprachen wie [Haskell][10] noch einen Schritt weiter, indem diese auf jeglicher Ebene Lazy sind. Clojure hat diese [Lazy Evaluation][11] nicht und ist damit grundsätzlich [strict][12]. Lediglich Sequenzen können in Clojure lazy sein.

TODO: vervollständigen

## Session 6 (11.10.16)

Wir haben eine Middleware programmiert, welche ein statisches Dummy Produkt in den Request legt.

[1]: <https://daringfireball.net/projects/markdown/>
[2]: <http://aleph.io>
[3]: <https://github.com/alexanderkiel/takelist>
[4]: <https://github.com/weavejester/hiccup>
[5]: <https://github.com/cgrand/enlive>
[6]: <http://www.w3schools.com/>
[7]: <https://clojuredocs.org/>
[8]: <https://clojuredocs.org/quickref>
[9]: <https://clojuredocs.org/quickref#sequences>
[10]: <https://www.haskell.org/>
[11]: <https://en.wikipedia.org/wiki/Lazy_evaluation>
[12]: <https://en.wikipedia.org/wiki/Eager_evaluation>
