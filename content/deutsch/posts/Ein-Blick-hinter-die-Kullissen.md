+++
title = "Ein Blick hinter die Kulissen"
date = "2020-12-27"
author = "Damon Leven"
description = "Dieser Post gibt eine kleine Übersicht, wie der OpenFoxBlog aufgebaut wurde und warum man sich für die jeweiligen Tools bzw. wegen entschieden hat."
+++

In diesem Post erkläre ich, wie der OpenFoxBlog hinter den Kulissen aus einer Technischen Sicht funktioniert. Hierbei werde ich auf die Technische Lösung und die aufgetauchten Probleme dieses Blogs eingehen. 

![Hits](https://hitcounter.pythonanywhere.com/count/tag.svg?url=https%3A%2F%2Fmcwertgaming.github.io%2Fde%2F2020%2Fein-blick-hinter-die-kulissen%2F)
[English version](/2020/a-look-behind-the-scenes/)

# Wie wurde dieser Blog erstellt?
Wie ich bereits [in meinem vorherigen Post](/de/2020/einführung/#wie-kam-es-zu-diesem-blog) beschrieben habe, benutze ich für diesen Blog Hugo als statischen Website Generator. Aber was bedeutet das eigentlich? Und warum schreibe ich nicht einfach richtige HTML Seiten oder nehme medium oder WordPress? 

# Warum nicht einfach medium oder WordPress?
Zunächst muss ich sagen, dass ich eine generelle Abneigung gegenüber WordPress habe. Geschrieben in altem PHP und durchlöchert mit vielen Problemen und Sicherheitsrisiken. Dazu kommt leider auch noch, dass ein Web Server Geld kostet und ich würde die Webseite ungern von zu Hause hosten, weil Web Server immer die ersten Ziele von automatischen Angriffen im Internet sind. Da WordPress damit aus meiner Entscheidung ausfällt, bleibt noch medium. Medium ist quasi eine Webseite, auf der man seine Blog Artikel einfach veröffentlichen kann. Da es hier nicht um ein generelles Thema geht, ist das tatsächlich sogar eine denkbare Alternative für mich. Allerdings habe ich mittlerweile viel Kritik an medium und daran, dass sie wohl aktiv versuchen die Artikel ihrer User aktiv zu monetarisieren. Ich möchte kein Geld mit meinen Artikeln machen und möchte auch, dass sie jeder kostenlos und ohne Werbung oder ähnlichen lesen kann. Ich möchte meine Gedanken auf den Bildschirm bringen und nicht Geld in meinen Geldbeutel. 

# Was ist ein Statischer Website Generator?
Bevor ich die Frage beantworte, möchte ich kurz erklären wie der Blog überhaupt bei euch auf dem Bildschirm landet. Dieser Blog liegt bei GitHub [in einem öffentlichen git repository](https://github.com/MCWertGaming/mcwertgaming.github.io) und wird bei jedem commit automatisch neu generiert. Diese Generierung macht der Statische Website Generator. Dieser bekommt ein selbst erstelltes, oder eines der vielen frei verfügbaren Themes und die Seiten, entweder direkt als HTML5 Seite, oder z.B. als markdown. Bei generieren der Webseite geht der Generator dann durch jede Seite und kopiert den Inhalt in das Theme. Das hat den Vorteil, dass man einmal das Layout der Seite erstellt und dann dieses auf alle Posts übertragen kann. Das erspart viel Arbeit, vor allem wenn man einige Zeit später etwas an der Seite ändern möchte. Gleichzeitig kann man auch RSS Feeds und Übersichten von Artikeln generieren lassen. Klar, dafuer speziell gibt es überall Funktionen, auch WordPress und ähnliche Tools können das.
Aber mein Lieblings Feature ist, dass ich die Artikel einfach in markdown schreiben kann. Markdown ist ein einfaches und mittlerweile auch weit verbreitetes Textformatierungssystem. Auch wenn man alle Formatierung Optionen von markdown auch direkt in HTML machen kann, ist die Syntax viel einfacher. Hier ein kleines Beispiel:

So sieht es gerendert aus:

> # Ein Test
> Dies ist ein kleiner Test für markdown / HTML Unterschiede.
> - Erster Teil einer Liste
> - Zweiter Teil einer Liste
> 
> ## Kleinere Überschrift
> Ein weiterer Test.
> 
> [Ein Link](/)
> 
> ![Ein Bild](/img/free-images/panorama.svg)
> 
> **Fett**
>
> *Kursiv*
>
> ~~Durch gestrichen~~

So wird es in markdown geschrieben:

```markdown
# Ein Test
Dies ist ein kleiner Test für markdown / HTMl Unterschiede.
- Erster Teil einer Liste
- Zweiter Teil einer Liste

## Kleinere Überschrift
Ein weiterer Test.

[Ein Link](/)

![Ein Bild](/img/free-images/panorama.svg)

**Fett**
*Kursiv*
~~Durch gestrichen~~
```

So wird es in HTML5 geschrieben:

```html
<h1 id="ein-test">Ein Test</h1>
<p>Dies ist ein kleiner Test für markdown / HTMl Unterschiede.</p>
<ul>
<li>Erster Teil einer Liste</li>
<li>Zweiter Teil einer Liste</li>
</ul>
<h2 id="kleinere-berschrift">Kleinere Überschrift</h2>
<p>Ein weiterer Test.</p>
<p><a href="https://mcwertgaming.github.io/">Ein Link</a></p>
<p><img src="https://mcwertgaming.github.io/img/free-images/panorama.svg" alt="Ein Bild"></p>
<p><strong>Fett</strong>
<em>Kursiv</em>
<del>Durch gestrichen</del></p>
```

Wie hier klar ersichtlich wird, ist markdown um einiges einfacher und schneller zu schreiben. Außerdem möchte ich kein Web Development machen, weil mir das einfach überhaupt nicht gefällt. Daher kann ich hiermit einfach meine Blogs in markdown schreiben ohne, dass ich auch nur eine Sekunde HTML berühren muss.

# Warum Hugo?
Wie ich ja auch schon in meinem letzten Post erwähnt habe, habe ich in der Alten Version dieses Blogs noch Jekyll als Statischen Webseiten Generator benutzt. Der Grund, warum ich Jekyll zuerst benutzt habe, war, dass Jekyll direkt in GitHub Pages eingebaut ist (welche ich übrigens benutze, um meine Webseite kostenlos hosten zu lassen). Das bringt den Vorteil, dass man keine Konfiguration vornehmen muss, damit der Blog funktioniert. Auch das Plugin System von Jekyll war sehr überzeugend, weil es einfach alles mitbringt und damit viel für mich einfacher macht, bzw. mir viel Arbeit abnimmt. Leider brachte der Generator auch viele Probleme mit sich. Z.B. habe ich es nicht hinbekommen meine Seite zweisprachig zu machen und die Themes haben mir alle nicht so wirklich gefallen.
Hugo auf der anderen Seite hat vollen support für zweisprachige Seiten eingebaut und bietet gleichzeitig auch viele andere Sachen, wie ein vernünftiges integrieren von Themes und der Möglichkeit diese Themes parallel zur Seite updaten zu können. Gleichzeitig habe ich das auf diesem Blog benutzte Theme dort gefunden.

# Probleme mit Hugo
So wie bei eigentlich allen Dingen in der Programmierung gab es auch bei Hugo ein Paar Probleme. Es fing an mit der Strukturierung der Webseite. Hugo erstellt die Webseiten ganz anders, als z.B. Jekyll.
Statt einfach HTML Seiten mit den jeweiligen Namen zu erstellen, erstellt Hugo Ordner mit dem Namen der jeweiligen Seite und erstellt in diesem Ordner eine index.html Datei. Das war sehr komisch am Anfang, hat aber den Vorteil, dass die URLs in der URL Leiste nicht mehr die .html Endung haben, sondern einfach den Ordner bzw. Seiten Namen.
Sonst lief alles super. Das Erstellen von neuen Seiten geht einfach so und auch das Bearbeiten des themes ist in der Hugo Konfigurations Datei gelöst, ohne dass ich das theme manuell bearbeiten muss.
Zum Schluss gab es allerdings dann doch noch ein großes Problem. Die RSS Feed Integration ist für meinen Anwendungsfall sehr schlecht gemacht. Er wird automatisch erstellt, sogar für alle Sprachen, aber nur einer von beiden wird als html tag in die Seite hinzugefügt (damit man die RSS Feeds einfach mit einem Feed Reader finden kann). Dazu kam, dass nur der erste Absatz in die RSS Feeds hinzugefügt wurde. Nach dem Ändern ging allerdings der RSS nicht mehr, weil das konvertieren von markdown zu html den RSS Feed ungültig gemacht hat. Wie ich dieses Problem gelöst habe, werde ich in einem kommenden Post genauerer erklären.

Das war es für den heutigen Artikel, ich hoffe, dass ich einen kleinen Einblick hinter die Kulissen dieses Blogs geben konnte und wünsche euch noch ein paar Rest Weihnachtsstimmung und einen guten Rutsch ins neue Jahr! 

#### Frohes Programmieren!

Quellen von hier benutzter software und assets koennen [hier](/de/über-mich/#in-dieser-seite-verwendete-software) gefunden werden.
