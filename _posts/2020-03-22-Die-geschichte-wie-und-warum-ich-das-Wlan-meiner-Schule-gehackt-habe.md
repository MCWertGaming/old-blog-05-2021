---
title: Die geschichte wie und warum ich das Wlan meiner Schule gehackt habe
Pagepublished: false
---

Da ich selbst einfache bis erweiterte Kenntnisse über Networking und Sicherheit besitze, helfe ich gerne anderen auf diesem Gebiet. Als ich neu auf meiner Schule war, habe ich sehr schnell gemerkt, dass dort leider nicht wirklich auf Sicherheit geachtet wird und auch niemand so richtig Ahnung hat, wie man so etwas richtig angeht. Das ich schade, aber ich bin ja auch noch da ;)

# wie war die Lage am Anfang? 

Als ich neu auf meiner Schule war, sah ich recht schnell, dass die Anmeldeseite des Wlans unverschlüsselt ist. Das bedeutet, dass wenn ich mich anmelde, kann das erstmal jeder mitlesen. Dazu kommt, dass das Wlan selbst natürlich unverschlüsselt ist. Das bedeutet, dass man nur in der Nähe vom Opfer sein muss, um an seine Login date zu kommen. Dass das ein Sicherheitsrisiko ist, ist daher sowieso außer Frage.  

Daraufhin habe ich mich natürlich mit unserem Schulleiter, welcher sich selbst mit einem anderen Lehrer um unser Internet kümmert, in Verbindung gesetzt. Das passierte über E-Mail mit einigen anderen Themen, die man auf jeden Fall verbessern kann. Er hat es sich durchgelesen und meinte zu mir, dass er eigentlich nicht so richtig viel davon versteht, aber mich zu einem Gespräch mit dem Dienstleiter einlud. Also der Firma, welche sich um das Internet an unserer Schule kümmert.  

Nach einer Verschiebung des Gesprächs fand es schlussendlich wie geplant statt. Ich bin ja selbst Linux fan, daher hat es mich gefreut, dass der Vertreter der Firma sich auch damit auskannte (auch wenn er Debian bevorzugt). Als es dann um die Verschlüsslung der Login Seite ging, hieß es aber von ihm, dass ein SSL Zertifikat (welches für die Verschlüsslung benötigt wird) zu teuer sei (was es in 2020 aber nicht mehr ist). Nach kurzer Diskussion haben wir das Thema mit (schauen wir mal) beendet. 

# “Schauen wir mal” ist doch super ;)

Deswegen habe ich ein bisschen gewartet, mit der Hoffnung, dass sich doch darum gekümmert wird. Leider passierte gar nichts. Weil ich mich dazu verpflichtet gefühlt habe, es selbst in die Hand zu nehmen, habe ich beschlossen das Problem zu demonstrieren. Also habe ich Wireshark auf meinen (Linux) Computer installiert. Eine Wlankarte, welche den “Monitormodus” unterstützt habe ich zum Glück vor einiger Zeit bereits besorgt. Der monitor Modus ist wichtig, weil bei diesem die Wlankarte auf alle Funkverbindung lauscht, andernfalls empfängt sie nur, was sie will/braucht. Also habe ich die Wlankarte in den Monitormodus geschaltet und den Wireshark gestartet. Diesen habe ich benutzt, um die empfangenen daten in einer Tabelle einfach darzustellen.  

Kleine Info vorab – Es zu großen Problemen oder rechtlicher Verfolgung führen, fremde Netzwerke oder Datenverkehre mit zu lesen/schreiben. Also bitte nur bei euren eigenen Geräten. 

Genau deswegen habe ich dann mit meinem Handy und meinem Laptop die versuche gemacht. Ich habe mich daher mit meinem Handy auf der Login Seite angemeldet, während der Laptop mitgeschnitten hat. Angemeldet habe ich natürlich mit Test User Name und Test Passwort. Es ist ja nicht wichtig, ob es korrekt ist, mitlesen kann man es so oder so. 

30 Minuten probieren und feinjustieren und ich hatte Nutzer Name und Passwort in meinem Wireshark. 

![](assets/wireshark_hack_screenshot.jpg)

Oben sieht man die Empfangenen Pakete, unten den Inhalt des Pakets. Der rot eingekreiste Eintrag oben ist das Packet, welches mein Handy zu Router, also der Login Seite des Internets geschickt hat. Unten eingekreist, sieht man die URL der Login Seite (als beweis, dass das wirklich der Login beim Wlan ist). Ganz unten sind in Rot die Variablen vom User Namen und Passwort. In grüne nochmal genauer unterlegt kann man sehr klar den eingegebenen User Namen und Passwort sehen. 

# Was ist das Ende der Geschichte? 

Ich habe den Screenshot mit einer ausführlichen und einfachen Anleitung an meinen Schulleiter gesendet. Hier ein (zensierter) Auszug davon: 
```
Hallo Herr XYZ, 
 
Ich habe mich noch einmal mit der Sicherheitsthematik vom WLAN der XYZ-schule befasst und nach ca 20 Minuten habe ich es geschafft (meine eigene anfrage) an die Anmelde Seite abzufangen. Ist tatsächlich einfacher, als ich zuerst dachte. Ein Programm runterladen und ein paar Knöpfe drücken kombiniert mit ein wenig Geduld und sie können jegliche Anmeldung mitlesen. Ihre Kollegen der IT Firmer müssten aber schon lange von dem Problem wissen, da man mit ein wenig JavaScript vor dem Versenden Benutzer Name und Passwort in den variablen auseinander schneidet (wahrscheinlich um es schwieriger zu machen automatisierte Angriffe zu starten) aber dann trotzdem die variablen "username" und "password" heißen ist mir nicht so ganz klar. auch dass eine "login.xml" Datei mit den Account Daten verschickt wird. Da bei HTTP und offenem wlan eh schon jeder mitlesen kann, sind Standard Namen sehr schlimm, da z.B. mein Wireshark solche Sachen direkt anzeigt, wodurch einem sogar das suchen erspart wird. Problem ist leider auch, dass ich bei meinem Test natürlich auch den HTTP Traffics von anderen Leuten mitlesen kann. Auch wenn heute, dass meiste SSL verschlüsselt ist, ist es halt trotzdem nicht so toll. 
  
Wie ich ja schon gesagt habe, empfehle ich ihnen bzw. ihren IT-Unternehmen die Schlossschul Domain um eine Subdomain wie z.B. login.XYZ-schule.de zu erstellen (können sie bei ihrem Domain Provider einfach machen) dann entweder ein Wildcard Zertifikat auf die Seite legen (Wildcard Zertifikate sind auf der Haupt und Subdomain gültig) oder sie nutzen let's encrypt. 
 
Dann erstellen sie auf ihrem VMware Server eine neue Maschine mit einem Reverse Proxy (z.B. NginX oder Treafik oder Caddy) und hinterlegen in ihm entweder das Wildcard Zertifikat der XZY-schule Domain oder lassen ihn mit let's encrypt ein kostenloses Zertifikat ausstellen, welches er automatisch regelmäßig erneuert. zuletzt fehlt noch die Änderung der Login-Seite auf die Subdomain der XYZ-schule und das Beantworten der anfrage auf die Subdomain von außen z.B. mit einer Fehler Seite. 
 
Eigentlich sollte die IT-Firma das ohne Aufpreis machen können, Es ist kein Riesen Aufwand. (das könnte sogar ich ihnen machen und das obwohl ich mich relativ wenig mit sowas beschäftige) 

Daher hoffe ich, dass sie sich um das Problem kümmern. Ich bedanke mich wie immer für ihre Kooperation und dass sie an dem Thema dranbleiben. 
 
Wenn sie eine Demonstration von dem Problem haben möchten, kann ich es ihnen gerne persönlich vorführen. 
 
Angehängt ist wie immer ein Screenshot, diesmal von Wireshark mit den entsprechenden wichtigen Sachen wie immer rot markiert (Username und Passwort grün). 
 
(bevor sie fragen, ja die Login Daten sind falsch, da ich einfach falsche Daten eingegeben habe und dann natürlich nicht eingeloggt wurde. Funktioniert aber bei funktionierenden Daten genauso) 
 
 
Schönes Wochenende ihnen! 
Gruß

Damon Leven 
```

Seine Antwort darauf war: 
```
Besten Dank für die Info, ich leite sie weiter … 
```

Aber Spaß bei Seite, ein paar Wochen später (in den Ferien) hat es die IT-firma direkt gemacht. Auch recht vernünftig, da kann ich nichts mehr groß aussetzen. 

# Was hast du jetzt von dieser Geschichte? 

Du hast hoffentlich gelernt, dass es sehr einfach ist login daten von unverschluesselten seiten abzufangen und kannst vielleicht selbst jemanden hinweisen, falls dir so etwas auffaellt. Wenn du im browser oben links ein schloss und kein umkreistes Ausrufezeichen siehst ist alles gut. Sollte das nicht so sein, vielleicht lieber mal melden und nachsehen, ob das auch bei anderen so ist. 
hier sind screenshots, damit klar ist, was ich meine:

chrome:
![](assets/chrome_secure_connection.jpg)

Brave:
![](assets/brave_secure_connection.jpg)

# kleine Info 

Ich habe absichtlich keine Namen von Personen oder Firmen genannt, weil ich nicht sagen möchte, dass jemand ein schlechtes Verständnis haben, oder keinen guten Job machen. Mir geht es darum, dass man aufmerksam in der modernen Welt umhergehen muss und dass Leute besser verstehen wie Hacking funktionier und wie man sich und andere davor schützen kann. 


Bleibt sicher!
### Damon Leven
