# X Model Automation — die Arbeit hinter dem Postingablauf

[Übersicht](../README.de.md) · [English](CASE-STUDY.md)

Ein vorbereitetes Bild zu veröffentlichen ist überschaubar. Aufwendiger wird es, wenn mehrere Accounts, kommende Beiträge und fehlgeschlagene Anfragen zusammenkommen. Das Projekt verbindet Medienvorbereitung, KI-generierte Texte, Planung und Berichte. Die Datenbank hält fest, was mit jedem geplanten Beitrag bereits passiert ist.

Ich habe die Anwendung mit KI-Unterstützung, unter anderem durch Codex, entwickelt. Diese Fallstudie greift Entscheidungen und Fehlerfälle aus dem Projektbericht auf. Die [Statusnotizen](STATUS.de.md) ordnen die berichteten Ergebnisse und die noch offenen Arbeiten ein.

## 1. Nach einem Timeout ist der Ausgang noch offen

Eine Anfrage kann bei X angekommen sein, obwohl die Anwendung keine Antwort erhält. Würde sie dieselbe Anfrage einfach wiederholen, könnte daraus ein zweiter Beitrag entstehen.

Deshalb speichert das System den Veröffentlichungszustand und hält Account, Medium, Text und geplanten Zeitpunkt vor dem Senden fest. Bleibt der Ausgang unklar, stoppt die automatische Wiederholung, bis sich der Zustand beim Anbieter klären lässt. Ein unbestätigter Versuch wird nicht als erfolgreicher Post verbucht.

Dasselbe Prinzip gilt beim Archivieren: Ist der Beitrag veröffentlicht, aber das Verschieben der Datei in Drive scheitert, bleibt die Nutzung in der Datenbank vermerkt. Der Ablageort einer Datei darf ihre Veröffentlichungshistorie nicht aufheben.

## 2. Eine Wartezeit muss den Neustart überstehen

Nach einer fehlgeschlagenen Textvorbereitung bestand zunächst das Risiko, dass jeder Scheduler-Durchlauf einen weiteren Versuch auslöst. So können viele Anfragen beim Anbieter entstehen, ohne dass sich die Ursache verbessert.

Der nächste erlaubte Versuch und die Anzahl der Fehler werden deshalb in der Datenbank gespeichert. Bis zu diesem Zeitpunkt lässt der Scheduler den Vorgang liegen. Auch ein Neustart setzt die Wartezeit nicht zurück. Eine Prozesssperre verhindert zusätzlich, dass zwei Scheduler gleichzeitig dieselbe Warteschlange bearbeiten.

Die Wiederholung wird damit Teil des gespeicherten Ablaufs. Sie hängt nicht nur von einer Pause innerhalb eines laufenden Prozesses ab.

## 3. Die Wiederholungsprüfung kann die Vorbereitung blockieren

Bei kurzen Texten, die zum Besuch eines Profils auffordern, ist die natürliche sprachliche Vielfalt begrenzt. Der Generator kann immer wieder Formulierungen vorschlagen, die bereits verwendet wurden oder im Vorrat liegen. Diese Texte abzulehnen ist richtig. Wiederholte Ablehnungen können aber dazu führen, dass ein kommender Zeitslot ohne gültigen Text bleibt.

Die Generierung berücksichtigt deshalb neben der Veröffentlichungshistorie auch bereits reservierte Formulierungen für zukünftige Beiträge. Zusätzlich wurde eine regelbasierte Ersatzlösung ergänzt. So erhält die Vorbereitung mehr Kontext, während die Regeln gegen Textwiederholungen bestehen bleiben.

## 4. Bericht und Test müssen denselben Zeitraum betrachten

Ein technischer Testpost war in die geschäftliche Auswertung eingeflossen. Die Korrektur schloss solche Testposts aus den Geschäftsberichten aus; ihre Messwerte blieben für die technische Diagnose erhalten.

Danach zeigte sich ein eigener Fehler in den Abnahmeprüfungen: Bericht und Prüfprogramm verwendeten unterschiedliche Zeiträume. Die vergangenen sieben Tage, vollständige lokale Kalendertage und ein Zeitfenster für Beiträge mit bereits abgeschlossener Sieben-Tage-Beobachtung enthalten nicht dieselben Datensätze.

Der Vergleich wurde auf das vorgesehene Zeitfenster der Anwendung angepasst. Die Erfahrung daraus: Auch das erwartete Testergebnis kann falsch sein. Die Anwendung vorschnell an einen fehlerhaften Vergleich anzupassen hätte das eigentliche Problem nicht gelöst.

## Die Erweiterung um Videos

Bei Videos kommen Formatprüfung, Umwandlung, Stichproben aus Einzelbildern und die asynchrone Verarbeitung des Uploads hinzu. Das Original bleibt erhalten. Die Upload-Version erhält eine eigene Identität und eine Prüfsummenverknüpfung zum Ausgangsmedium.

Die Erweiterung wurde bei deaktiviertem automatischem Video-Zeitplan installiert. Zum am 19. Oktober 2026 bestätigten Stand waren 103 Videos eingelesen und normalisiert. KI-Auswertung, ein echter Video-Testpost und die automatische Veröffentlichung standen noch aus. Vier wiederkehrende Fehler beim Einlesen waren weiterhin offen.

Der Projektbericht beschrieb außerdem einen geringen Vorrat vorbereiteter Bildposts trotz vorhandener Medien. Dieses Problem bei der Vorbereitung bleibt auch während der Video-Erweiterung relevant. Eine installierte Erweiterung bedeutet noch nicht, dass jeder ihrer Schritte für den unbeaufsichtigten Betrieb bereit ist.

[Architektur](ARCHITECTURE.de.md) · [Status und Prüfungen](STATUS.de.md) · [Rechtehinweis](../RIGHTS.md)
