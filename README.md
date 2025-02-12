# Lucky Miner LV06 Boardversion 204

## Achtung, Nutzung auf eigene Gefahr!
## Ich übernehme keine Haftung für Schäden jeglicher Art!

### Nur mit LuckyMiner LV06 verwenden 

Meine LuckyMiner haben alle die Leiterplatten-Revision: XSL 24001PCV11 mit Datum 2024.03.20

Die hier angebotene esp-miner.bin ändert die im NVS (kleiner Speicherbereich im ESP32) geschriebene boardversion von 204 auf 0.11

## Update 12.02.2025
Über den Webflasher kann nun direkt eine factory.bin für den LuckyMinerLV06 geflasht werden. Diese enthält die richtige Boardversion.

https://github.com/matlen67/bitaxe-web-flasher


### Hintergrund

Im August 2024 hatte ich beim rum Google'n vom Minig mittels ESP32 gelesen.
Ich hatte mir dann über AliExpress ein Liligo ESP32-S3 bestellt. Angefixt vom Minig, bin ich dann auch bald auf die LuckyMiner und Bitaxe gestoßen. Zu dem Zeitpunkt kannte ich noch nicht wirklich die Unterschiede und habe dann, aber auch ein wenig verständlich, drei LuckyMiner LV06 zu je ca. 70€ erworben.
Zu dem Zeitpunkt hatte ich nur Shops gefunden die für einen Bitaxe Ultra zwischen 170€ - 240€ haben wollten, und das dann auch noch mit Bitcoin Zahlung. Und ca. 200€ zu um die 600€ sind halt für 'Ich spiel da mal ein wenig rum' schon ein krasser Preisunterschied.

Das Bitaxe OpenSource Projekt ist schon mega krass, aber wer bitte soll PCB's selber bestellen und SMD's auflöten. Ja ich habe vor 20 Jahren, als es noch keine ESP's gab kleine Schaltungen selber mittels Eagle entworfen und in China (itead) fertigen lassen, und hinterher auch selber draht und SMD Bauteile verlötet. Heute möchte ich mir das aber nicht mehr antun.

Getreu meinem Motto 'Was man flashen kann, wird geflasht' habe ich mir dann wohl irgendwelche *.bin's aufgespielt, die die Boardversion auf 204 angehoben haben. Von da an wollten die LuckyMiner nur noch sporadisch funktionieren. Nach gefühlen 20 mal 'Strom an / Strom aus' liefen die dann komischer weise bis zur nächsten Spannungsunterbrechung einwandfrei. Das war nun schlussendlich der Grund, mich auf die Suche nach einer Lösung zu begeben, und habe den Sourcecode und die PCB's ein wenig studiert.

#### Was hat das mit der Boardversion auf sich?
Ich bin nun nicht der mega Profi, und ich habe auch keine Lust noch tiefer in den Code einzusteigen (ich will ja nur ein wenig Minen), aber wie ich das sehe wurde nach den ersten Boardrevisionen 0.11/0.12 usw. bei den 2xx Board's noch ein Mosfet zu einem Spannungsregler verbaut der als Power_Enabled bezeichnet wird und über den GPIO10 des ESP32 angesteuert wird. Im Sourcecode finden sich einige Stellen wo das Powermanagement duch die Boardversion gesteuert wird. Wahrscheinlich hat der LuckMiner den Mosfet nicht, da er auf der Leiterplattenrevision 0.11 aufbaut und startet dann nicht, da die Software auf irgend eine Rückmeldung wartet. Merkwürdig ist nur das es nach 10 -20 mal Steckerziehen dann doch los geht. Evtl ein Bug? Oder so gewollt? :)

## Was mach diese esp-miner.bin?
Ich habe einen kleinen patch ab Zeile 59 in die main.c eingefügt, der Prüft ob die Boardversion = 204 ist, und dann die Boardversion im NVS Speicher mit 0.11 überschreibt 

```c++
 // @matlen67
 // check if boardversion = 204 change boardverion to string 0.11 (int = 0)
 if (GLOBAL_STATE.board_version == 204) {
     ESP_LOGI("matlen67 -> ", "detect boardversion = 204 in NVS");
     nvs_config_set_string(NVS_CONFIG_BOARD_VERSION, "0.11");
     GLOBAL_STATE.board_version = atoi(nvs_config_get_string(NVS_CONFIG_BOARD_VERSION, "000"));
     ESP_LOGI("matlen67 -> ", "change boardversion to 0.11");
  }
```


#### ASIC bekommt keine Spannung Boardversion 204
<img src="https://github.com/matlen67/LuckMinerLV06/blob/main/image/LuckyMiner_AxeOS_boardversion_204.png" width="256"> 

<img src="https://github.com/matlen67/LuckMinerLV06/blob/main/image/LuckyMiner_boardversion_204_no_mining.png" width="512"> 

### Patch funktioniert
Auch mit Schreibfehler im Log (Taste s hängt wohl manchmal) :blush:

<img src="https://github.com/matlen67/LuckMinerLV06/blob/main/image/luckyMiner_bootlog.png" width="512"> 


