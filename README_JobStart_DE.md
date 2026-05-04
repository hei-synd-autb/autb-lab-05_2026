<h1 align="left">
  <br>
  <img src="./img/hei-en.png" alt="HEI-Vs Logo" width="350">
  <br>
  HEI-Vs Engineering School - Industrial Automation Base
  <br>
</h1>

Cours AutB

Author: [Cédric Lenoir](mailto:cedric.lenoir@hevs.ch)
> Version 2026, V1.0 

# LAB 05 (Fortsetzung)
## Inbetriebnahme einer elektrischen Achse mit Kugelumlaufspindel.

Job starts with:

# CtrlX Drive Engineering
Diese Software wurde entwickelt, um:
- Parameter der elektrischen Achsen einsehen und ggf. ändern.
- Visualisieren Sie das Verhalten der Achse mithilfe eines integrierten Oszilloskops.
- Steuern Sie die Achse im manuellen Modus, um ihr Verhalten zu optimieren.
- Starten Sie einen **Auto-Tuning**-Vorgang.

<figure>
    <img src="./img/ctrlX Drive Engineering.png"
         alt="Image lost: ctrlX Drive Engineering">
    <figcaption>Use CtrlX Drive Engineering</figcaption>
</figure>

# Connect

Schließen Sie das Laufwerk über USB-C an und verwenden Sie die **X-Achse**. Möglich ist dies, indem Sie Ihren eigenen PC verwenden, um den Labor-PC für andere Aufgaben freizuhalten.
 
 <div style="text-align: center;">
<figure>
    <img src="./img/DriveSelectConnection.png"
         alt="Image lost: DriveSelectConnection.png">
    <figcaption>Connection to drive with USB-C</figcaption>
</figure>
</div>

> Es ist möglich, auf verschiedene Arten eine Verbindung herzustellen.

# WICHTIG: Speichern der Parameter
Speichern Sie die aktuellen Parameter, damit Sie sie am Ende des Labors wiederherstellen können.

Bevor Sie die Parameter speichern, sollten Sie in den PM-Modus (Parameter Mode) wechseln. Dazu darf der Motor nicht eingeschaltet sein.

Select Parameter Mode

<div style="text-align: center;">
<figure>
    <img src="./img/SelectParameterMode.png"
         alt="Image lost: SelectParameterMode">
    <figcaption>Set Axis in PM, Parameter Mode</figcaption>
</figure>
 
<figure>
    <img src="./img/Save_Backup_Parameters.png"
         alt="Image lost: /img/Save_Backup_Parameters.png">
    <figcaption>Save a backup of drive parameters to restore them if needed</figcaption>
</figure>
</div>

Die Parameter sind nach dem [Sercos](https://www.sercos.org)-System nummeriert.
Auf eine Vielzahl von Parametern kann in Echtzeit oder Nicht-Echtzeit, lesend oder schreibend, zugegriffen werden. Bestimmte Parameter können nur geändert werden, wenn der Motor kein Drehmoment mehr hat oder sich der Antrieb im Parametermodus befindet.

- **Sicherungsparameter** für Konfigurationsparameter.
- **Alle Parameter**, archiviert absolut alle Parameter. Dies ist nützlich, um eine Diagnose zu stellen oder im Rahmen eines Kurses eine Achse nur in Form von Parametern darzustellen.

Stellen Sie nach dem Archivieren der Einstellungen den OM-Modus wieder her.

<div style="text-align: center;">
<figure>
    <img src="./img/ActivateOperatingMode.png"
         alt="Image lost: ActivateOperatingMode">
    <figcaption>Restore OM Operating Mode</figcaption>
</figure>
</div>

# Scaling
Um die Encoderposition in anwendungsgerechte Einheiten umrechnen zu können, muss die Achse die mechanischen Parameter des Systems kennen.

In unserem Fall wird die Position des Encoders unter anderem in mm für die lineare Position der X-Achse umgerechnet.

<div style="text-align: center;">
<figure>
    <img src="./img/AxisMechanicalScaling.png"
         alt="Image lost: AxisMechanicalScaling">
    <figcaption>Go to Axis Mechanical Scaling</figcaption>
</figure>
</div>

Um die Ergebnisse leichter interpretierbar zu machen, ändern wir einen Parameter, sodass das System das Motordrehmoment in eine Kraft umwandelt, um die lineare Kraft am Ausgang der Kugelumlaufspindel abzulesen. **Vorsicht mit der Geschwindigkeit**, hier arbeiten wir in $\ [m/s]$.

<div style="text-align: center;">
<figure>
    <img src="./img/ChangeScalingToForce.png"
         alt="Image lost: /img/ChangeScalingToForce">
    <figcaption>Change scaling to force</figcaption>
</figure>
</div>

Es ist zu beachten, dass ein Wechsel der Einheit unter keinen Umständen möglich ist. Die Achse muss sich im **CM**-Modus, **Configuration Mode**, befinden, um eine Änderung der Einheit zuzulassen.

<div style="text-align: center;">
<figure>
    <img src="./img/ConfigurationMode.png"
         alt="Image lost: GoToParameterModeAgain">
    <figcaption>You must be in Configuration Mode to modify a scaling parameter</figcaption>
</figure>
</div>

## Die zu überprüfenden Grenzwerte
Das System weiß nicht, wie Grenzwerte beim Ändern der Einheit von Drehmoment in Kraft korrekt umgerechnet werden.
Sie müssen die Grenzwerte überprüfen, sonst können Sie die Achse nicht verwenden!

<div style="text-align: center;">
<figure>
    <img src="./img/ParametersForLimits.png"
         alt="Image lost: ParametersForLimits">
    <figcaption>Display some parameters used for this lab</figcaption>
</figure>
</div>

Sie können die Liste der anzuzeigenden Parameter speichern. Dateityp ipg.
Sie können auch die mit den Dokumenten bereitgestellte Datei verwenden, siehe: ``..\ctrlxDriveFiles\WatchListForTuning.ipg``.

Sie können die Grenzwerte entweder in dieser Parameterliste ändern. Seien Sie vorsichtig, Sie müssen sich im **PM**-Modus befinden.
Gehen Sie entweder zu dem in der Einleitung erwähnten Limitfenster.

### Zu überprüfende Grenzwerte
-   ``P-0-0109.0.0 ``   :   1766 [N], Torque / Force limitation : Peak limit value
-   ``S-0-0082.0.0``    :   1500 [N], Torque / Force limitation : Limit value positive
-   ``S-0-0083.0.0``    :   -1500 [N], Torque / Force limitation : Limit value negative

> Das System führt bei einem Einheitenwechsel keine automatische Kraft-/Stärkeumrechnung an den Grenzen durch, weshalb diese Grenzen bei einem Einheitenwechsel angepasst werden müssen.

> Wenn diese Grenzwerte zu niedrig sind, kann das System insbesondere in Beschleunigungs- und Verzögerungsphasen nicht richtig arbeiten.

> Umgekehrt besteht bei zu hohen Grenzwerten die Gefahr einer Beschädigung der Mechanik.

Berechnen Sie die maximale theoretische Kraft der Kugelumlaufspindel.
Zur theoretischen Kraft müsste man den Wirkungsgrad der Kugelumlaufspindel multiplizieren, der **schätzungsweise zwischen 0,5 und 0,9 liegt**. Um Ihren Bericht einzureichen.

Wir kennen die Eigenschaften des Kugelgewindetriebs.

|Axe |Smax [mm]|u[mm/U]    |Vmax[m/s]   |amax [m/s2]   |Mmax[Nm] | d  |i   |
|----|---------|-----------|------------|--------------|---------|----|----|
|x   |565      |5.0        |0.3         |15            |**8.22** |ccw |1   |

# Fahren Sie den Motor im manuellen Modus

Ziel: Mit dem Tool „Easy Startup Mode“ eine Hin- und Herbewegung der Achse ausführen.
Vorgehensweise: Nach dem Öffnen des Tools „Easy Startup Mode“ die folgenden Parameter anwenden:

<div style="text-align: center;">
<figure>
    <img src="./img/EasyStartupMode.png"
         alt="Image lost: EasyStartupMode.png">
    <figcaption>You should be able to move your motor</figcaption>
</figure>
</div>

<div style="text-align: center;">
<figure>
    <img src="./img/EasyStartupModeDangerous.png"
         alt="Image lost: EasyStartupModeDangerous.png">
    <figcaption>Only for info, click OK</figcaption>
</figure>
</div>


# Trace data
Zeichnen Sie die Kurve der Position, Geschwindigkeit, Kraft und Nachlauffehler in Abhängigkeit von der Zeit auf.

<div style="text-align: center;">
<figure>
    <img src="./img/OscilloscopeTraceYourData.png"
         alt="Image lost: OscilloscopeTraceYourData">
    <figcaption>Trace the motion of Easy Startup Mode</figcaption>
</figure>
</div>

## Parameter zum Anzeigen und Kommentieren:
``S-0-0084`` Stärke
``S-0-0051``-Position
``S-0-0040`` Geschwindigkeit
``S-0-0189`` Tracking-Fehler

<div style="text-align: center;">
<figure>
    <img src="./img/OscilloscopeTraceConfigureData.png"
         alt="Image lost: OscilloscopeTraceConfigureData">
    <figcaption>Configure data to trace</figcaption>
</figure>

<figure>
    <img src="./img/OscilloscopeScaleData.png"
         alt="Image lost: OscilloscopeScaleData">
    <figcaption>Scale data</figcaption>
</figure>
</div>

# Statische Reibung.
- Messen Sie die Kraft, die zur Überwindung der Haftreibung erforderlich ist.
Verwenden Sie dazu den Modus **Torque/Force Control** und erhöhen Sie **schrittweise** den Kraftsollwert von 250 [N] aus.

Wie groß ist ungefähr die Kraft, die erforderlich ist, um die Haftreibung zu überwinden?
 Wie viel Prozent des Motordrehmoments wird für diesen Vorgang verwendet?

> Der Befehl am Eingang des Strom-/Drehmoment-/Kraftreglers entspricht dem Ausgang des hier deaktivierten Geschwindigkeitsreglers.

> Der Tempomat-Integrator macht in gewisser Weise das Gleiche wie Sie. **Es erhöht die Kraft mit der Zeit**. Dies ist einer der Gründe, warum ein Integrator begrenzt ist, da er sonst den Strom am Motor auf unbestimmte Zeit erhöht. Bei einer blockierten Achse erwärmt sich der Motor schnell, dies ist oft ein Zeichen für eine mechanische Blockierung.

# Bei der manuellen Abstimmung handelt es sich um einen Vergleich mit der automatischen Abstimmung.

<div style="text-align: center;">
<figure>
    <img src="./img/VelocityControllerOverview.png"
         alt="Image lost: VelocityControllerOverview">
    <figcaption>Velocity controller</figcaption>
</figure>
</div>

1.    S-0-0100: Proportionalverstärkung Kp.
2.    S-0-0101: Integrationskonstante Tn.
3.    S-0-0163: Gewichtsausgleich für vertikale Achsen.

<div style="text-align: center;">
<figure>
    <img src="./img/VelocityControllerTransferFunction.png"
         alt="Image lost: VelocityControllerTransferFunction">
    <figcaption>Velocity controller transfer function</figcaption>
</figure>
</div>

## Verfahren
### Empirisches Setting
Basierend auf der Ziegler- und Nichols-Methode

> Lassen Sie den Motor idealerweise im Drehzahlmodus mit etwa 20 U/min im **Easy Startup Mode** laufen. Wenn die Mechanik es nicht zulässt, arbeiten Sie im Stillstand. **Da wir ein lineares System mit endlicher Grenze haben, arbeiten wir mit einer Geschwindigkeit von Null**.

> Achtung, Feed-Forward; ``P-0-1126.0.0 ``auf 0!

### Verstärkung S-0-0100
> Abreisebedingungen:
- Lageregler ``S-0-0104`` = 1
$$\ S-0-0100 = 1000 * Motorträgheit = 1000 * P-0-0510 $$
- ``S-0-0101`` = 0 [ms] ohne Integrator
- ``P-0-0510`` = 0,0001600

> Also start mit ``S-0-0100`` = **0,16**

Erhöhen Sie die Verstärkung **schrittweise**, bis das System zu vibrieren beginnt (mit dem Ohr hörbar). 



- Verringern Sie die Verstärkung, bis die Vibration aufhört. Das ist der entscheidende Gewinn.
- Teilen Sie den kritischen Gewinn durch 2.

#### Beispiel :
- Startverstärkung: S-0-0100 = 1000 * P-0-0510 = 0,01
- Die Schwingung erscheint bei 0,09 und verschwindet bei 0,065. Der kritische Gewinn beträgt 0,065
- S-0-0100 geschätzt auf 0,065 / 2 = 0,0325

### Integrationszeitkonstante Tn S-0-0101
-   Verringern Sie die Integrationszeitkonstante Tn S-0-0101 schrittweise (beginnend bei ca. 100 ms), bis Sie den Oszillationspunkt erreichen.
-   Erhöhen Sie die Integrationszeitkonstante Tn, bis die Schwingung aufhört. Der erhaltene Wert ist die kritische Zeit.
-   Anschließend multiplizieren Sie den Wert mit 2, um den Endwert von S-0-0101 zu erhalten.

#### Beispiel :
Startzeit: ``S-0-0101`` = 100 ms
Die Schwingung erscheint bei 0,6 und verschwindet bei 0,7. Der kritische Wert liegt bei 0,07
``S-0-0101`` geschätzt auf 0,7 * 2 = 1,4

### Visualisierung mit dem Oszilloskop
Führen Sie eine Hin- und Herbewegung mit Geschwindigkeitsregelung und den folgenden Parametern durch :

<div style="text-align: center;">
<figure>
    <img src="./img/VelocityControl.png"
         alt="Image lost: VelocityControl.png">
    <figcaption>Velocity control</figcaption>
</figure>
</div>

Zeichnen Sie die Kurve der gemessenen Geschwindigkeit als Funktion der Zeit auf.

Wird der Sollwert für die Umkehrgeschwindigkeit erreicht?

Wie groß ist die Schwankung der Geschwindigkeit in [mm/min] und in [%] im Dauerbetrieb ?


## Visualise your data en mode **Velocity Control**,
 
Die folgenden Informationen 4 Sekunden lang auf dem Oszilloskop anzeigen 
- ``S-0-0084`` Stärke
- ``S-0-0051`` Position.
- ``S-0-0040`` Geschwindigkeit
- ``S-0-0189`` Tracking-Fehler



> Kommentieren Sie Ihre Ergebnisse

## Vergleichen Sie mit Autotuning
Merken Sie sich Ihre Werte von ``S-0-0100`` und ``S-0-0101`` und vergleichen Sie diese mit der automatischen Abstimmung.

<div style="text-align: center;">
<figure>
    <img src="./img/AutoTuning_1.png"
         alt="Image lost: AutoTuning_1">
    <figcaption>Start Auto-Tuning </figcaption>
</figure>
</div>
 
Konfiguriert ohne Feed-Forward und ohne Filter.

<div style="text-align: center;">
<figure>
    <img src="./img/AutoTuning_2.png"
         alt="Image lost: AutoTuning_2">
    <figcaption>Continue in Expert Mode</figcaption>
</figure>
</div>

**Do not** optimize position controller and frequency response.
**Do not** optimize  acceleration feedforward.

<div style="text-align: center;">
<figure>
    <img src="./img/AutoTuning_3.png"
         alt="Image lost: AutoTuning_3">
    <figcaption>Select Handling and parameters to set</figcaption>
</figure>
</div>
 
Ergebnisse ansehen und kommentieren

> Vergleichen Sie die Lastträgheit: *Ihr Maß* mit dem des Motors „P-0-0510“.
Dein Kommentar…

<br>

Versuchen Sie es mit Feed-Forward und vergleichen Sie


## Einfluss des Jerk-Werts

Führen Sie mehrere Achsenbewegungen mit unterschiedlichen Jerk-Werten durch.
Welche negativen Auswirkungen hat ein hoher Jerk-Wert auf die Systemdynamik ?


<br>

<div style="text-align: center;">
<figure>
    <img src="./img/DriveControlledPositioning.png"
         alt="Image lost: DriveControlledPositioning">
    <figcaption>Drive Controlled-Positioning</figcaption>
</figure>
</div>



# Dynamische Reibung
- Messen Sie die erforderliche Kraft bei niedriger konstanter Geschwindigkeit
- Verwenden Sie den Modus **Velocity control**, aber erhöhen Sie bei +- 50 mm die Messzeit auf der Spur, um diese Messung durchzuführen.
- Messungen mit 600 mm/min durchführen
- Dann 6000 mm/min


Klicken Sie mit der rechten Maustaste, um die Daten anzuzeigen:

<div style="text-align: center;">
<figure>
    <img src="./img/OscilloscopeShowCharValues.png"
         alt="Image lost: OscilloscopeShowCharValues">
    <figcaption>Show characteristic values</figcaption>
</figure>
</div>

# Letzten Endes

> Sie können die im Anhang bereitgestellten PLC- und Node-RED-Programme verwenden, um den Betrieb in den Zuständen **Auto** und **Execute** zu überprüfen.

## Bereiten Sie eine Bewegung mit vor
$\ +/- 50 [mm]$ mit einer Geschwindigkeit von $\ 0.04 [m/s]$ und einer Beschleunigung von $\ 1 [m/s^2] $.
- Stärke in Rot
- Geschwindigkeit in Blau
- Die Position in Grün
- Geschwindigkeitsfehler in Schwarz

## Schlagen Sie Ihr ideales Tuning vor, begründen Sie es und kommentieren Sie es
Wissend, dass:

|Zu vermeidendes Überschwingen|Besser!|
|-----------|--------------|
|![](./img/TuningZuVermeiden.png)|![](./img/TuningMieux.png)|


## Hilfsfragen
- Wie hoch ist die Spannung am DC-Bus?
- Wie lang ist die Zykluszeit der EtherCAT-Kommunikation zwischen dem Antrieb und der SPS ?
- Welche Register sendet die SPS an den Antrieb?
- Welches Register ermöglicht es der SPS, die Position des Motors zu erkennen?
- Warum ist es in unserem Fall unnötig, den Encoder zu konfigurieren?
- Wie viele Punkte pro Umdrehung erhält der X-Achsen-Motor über den Ethercat-Bus, wenn die X-Achsen-Geschwindigkeitsgrenze erreicht ist?
- Erklären Sie, warum der X-Achsen-Motor im Vergleich zum maximal zulässigen Drehmoment der Kugelumlaufspindel überdimensioniert erscheint.
- Wozu dient die Bremse an der Z-Achse?



<br><br>

# WICHTIG : 
## Am Ende des Labors durchzuführende Schritte

1) Stellen Sie die zu Beginn des Labors gespeicherten Parameter des Laufwerks wieder her (*.par-Datei).



<div style="text-align: center;">
<figure>
    <img src="./img/LoadParameters.png"
         alt="Image lost: LoadParameters">
    <figcaption>Load parameters</figcaption>
</figure>
</div>



2) Schalten Sie den Antrieb in den Modus **OM** (Operating Mode).

3) **Beenden Sie den Modus „Easy Startup Mode”**.



<div style="text-align: center;">
<figure>
    <img src="./img/EasyStartupMode_OperatingMode.png"
         alt="Image lost: EasyStartupMode_OperatingMode">
    <figcaption>Easy startup mode and Operating mode</figcaption>
</figure>
</div>

