---
title: Wskaźniki monitorowania StorSimple | Dokumenty firmy Microsoft
description: Opisuje diody elektroluminescencyjne (LED) i alarmy dźwiękowe używane do monitorowania stanu urządzenia StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60630613"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Zarządzanie urządzeniem za pomocą wskaźników monitorowania StorSimple


## <a name="overview"></a>Omówienie
Urządzenie StorSimple zawiera diody emitujące światło (LED) i alarmy, za pomocą których można monitorować moduły i ogólny stan urządzenia StorSimple. Wskaźniki monitorowania można znaleźć na komponentach sprzętowych głównej obudowy urządzenia i obudowy EBOD. Wskaźnikiem monitorowania mogą być diody LED lub alarmy dźwiękowe.

Istnieją trzy stany LED używane do wskazania stanu modułu: zielony, migający na zielono do czerwono-bursztynowego lub czerwono-bursztynowy.  

* Zielone diody LED reprezentują zdrowy stan pracy.  
* Migające na zielono do czerwono-bursztynowych diod LED reprezentują obecność niekrytycznych warunków, które mogą wymagać interwencji użytkownika.  
* Czerwono-bursztynowe diody LED wskazują, że w module występuje krytyczna usterka.  

Poniżej dalsza część artykułu Opisano różne diody LED wskaźników monitorowania, ich lokalizacje na urządzeniu StorSimple, stan urządzenia na podstawie stanów LED i wszelkie związane z nimi alarmy dźwiękowe.

## <a name="front-panel-indicator-leds"></a>Diody LED na panelu przednim
Na panelu przednim, znanym również jako *panel operacyjny* lub *panel operacyjny,* wyświetlany jest stan zagregowany wszystkich modułów w systemie. Panel przedni jest identyczny na storsimple podstawowych i obudowy EBOD, i jest zilustrowany poniżej.  

   ![Przedni panel urządzenia][1]

Przedni panel zawiera następujące wskaźniki:  

1. Przycisk Wycisz
2. Dioda LED wskaźnika zasilania (zielony/czerwono-bursztynowy)
3. Dioda LED wskaźnika usterek modułu (ON red-amber/OFF)
4. Wskaźnik błędu logicznego LED (ON red-amber/OFF
5. Wyświetlacz identyfikatora jednostki  

Główną różnicą między diodami LED na panelu przednim dla urządzenia a diodami dla obudowy EBOD jest **numer identyfikacyjny jednostki systemowej** wyświetlany na wyświetlaczu LED. Domyślny identyfikator jednostki wyświetlany na urządzeniu to **00,** natomiast domyślny identyfikator jednostki wyświetlany w obudowie EBOD to **01**. Pozwala to szybko odróżnić urządzenie od obudowy EBOD, gdy urządzenie jest włączone. Jeśli urządzenie jest wyłączone, użyj informacji podanych w [funkcji Włącz nowe urządzenie,](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) aby odróżnić je od obudowy EBOD.  

## <a name="front-panel-led-status"></a>Stan diody LED panelu przedniego
Poniższa tabela służy do identyfikacji stanu wskazanego przez diody LED na panelu przednim urządzenia lub obudowy EBOD.  

| Moc systemu | Usterka modułu | Błąd logiczny | Alarm | Stan |
| --- | --- | --- | --- | --- |
| Czerwono-bursztynowy |WYŁ. |WYŁ. |Nie dotyczy |Utrata zasilania prądem zmiennym, zasilanie zapasowe lub zasilanie prądem zmiennym włączone i moduły kontrolera zostały usunięte. |
| Zielony |ON |ON |Nie dotyczy |Stan testowy panelu ops (5s) |
| Zielony |WYŁ. |WYŁ. |Nie dotyczy |Włączanie, wszystkie funkcje są dobre |
| Zielony |ON |Nie dotyczy |Diody LED usterek PCM, diody LED z usterkami wentylatora |Wszelkie usterki PCM, usterka wentylatora, temperatura powyżej lub poniżej |
| Zielony |ON |Nie dotyczy |Diody LED modułu We/Wy |Wszelkie usterki modułu kontrolera |
| Zielony |ON |Nie dotyczy |Nie dotyczy |Usterka logiczna obudowy |
| Zielony |Flash |Nie dotyczy |Dioda LED stanu modułu na module kontrolera. Diody LED usterek PCM, diody LED z usterkami wentylatora |Zainstalowany nieznany typ modułu kontrolera, awaria magistrali I2C, błąd konfiguracji istotnych danych produktu modułu kontrolera (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Diody LED wskaźników modułu chłodzenia zasilania (PCM)
Diody LED modułu chłodzenia zasilania (PCM) można znaleźć z tyłu obudowy głównej lub obudowy EBOD na każdym module PCM. W tym temacie omówiono sposób monitorowania stanu urządzenia StorSimple za pomocą następujących diod LED.  

* Diody LED PCM do obudowy podstawowej
* Diody LED PCM do obudowy EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>Diody LED PCM do obudowy podstawowej
Urządzenie StorSimple ma moduł PCM o przekątnej 764 W z dodatkową baterią. Na poniższej ilustracji przedstawiono panel LED urządzenia.  

   ![Diody LED PCM na obudowie głównej][2]

Legenda LED:

1. Awaria zasilania prądem przemienna
2. Awaria wentylatora
3. Awaria baterii
4. PCM OK
5. Awaria kontrolera domeny
6. Bateria dobra  

Stan PCM jest wskazany na panelu LED. Panel LED PCM urządzenia ma sześć diod LED. Cztery z tych diod LED wyświetlają stan zasilacza i wentylatora. Pozostałe dwie diody LED wskazują stan zapasowego modułu baterii w pcm. Do określenia stanu pcm można użyć poniższych tabel.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Diody LED wskaźników PCM do zasilania i wentylatora
| Stan | PCM OK (zielony) | Niepowodzenie ac (bursztyn) | Niepowodzenie wentylatora (bursztynowy) | Niepowodzenie dc (bursztynowy) |
| --- | --- | --- | --- | --- |
| Brak zasilania prądem zmiennym (do obudowy) |WYŁ. |WYŁ. |WYŁ. |WYŁ. |
| Brak zasilania prądem przemienna (tylko ten PCM) |WYŁ. |ON |WYŁ. |ON |
| AC obecny PCM ON - OK |ON |WYŁ. |WYŁ. |WYŁ. |
| Niepowodzenie PCM (niepowodzenie wentylatora) |WYŁ. |WYŁ. |ON |Nie dotyczy |
| Usterka PCM (ponad amper, przepięcie, przetędłe) |WYŁ. |ON |ON |ON |
| PCM (wentylator z tolerancji) |ON |WYŁ. |WYŁ. |ON |
| Tryb czuwania |Miga |WYŁ. |WYŁ. |WYŁ. |
| Pobieranie oprogramowania układowego PCM |WYŁ. |Miga |Miga |Miga |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Diody LED wskaźnika PCM dla baterii zapasowej
| Stan | Bateria dobra (zielona) | Usterka baterii (bursztynowa) |
| --- | --- | --- |
| Bateria nie jest obecna |WYŁ. |WYŁ. |
| Bateria obecna i naładowana |ON |WYŁ. |
| Ładowanie lub rozładowywanie akumulatora |Miga |WYŁ. |
| "Miękka" usterka baterii (owalna do odzyskania) |WYŁ. |Miga |
| Awaria "twardego" akumulatora (nie do odzyskania) |WYŁ. |ON |
| Rozbroiona bateria |Miga |WYŁ. |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Diody LED PCM do obudowy EBOD
Obudowa EBOD ma 580W PCM i nie ma dodatkowej baterii. Panel PCM obudowy EBOD ma diody LED ze wskaźnikami tylko dla zasilaczy i wentylatora. Na poniższej ilustracji przedstawiono te diody LED.

   ![Diody LED PCM w obudowie EBOD][3] 

W poniższej tabeli można określić stan pcm.  

| Stan | PCM OK (zielony) | Niepowodzenie ac (bursztyn) | Niepowodzenie wentylatora (bursztynowy) | Niepowodzenie dc (bursztynowy) |
| --- | --- | --- | --- | --- |
| Brak zasilania prądem zmiennym (do obudowy) |WYŁ. |WYŁ. |WYŁ. |WYŁ. |
| Brak zasilania prądem przemienna (tylko ten PCM) |WYŁ. |ON |WYŁ. |ON |
| AC obecny PCM ON - OK |ON |WYŁ. |WYŁ. |WYŁ. |
| Niepowodzenie PCM (niepowodzenie wentylatora) |WYŁ. |WYŁ. |ON |X |
| Usterka PCM (ponad amper, przepięcie, przetędłe |WYŁ. |ON |ON |ON |
| PCM (wentylator z tolerancji) |ON |WYŁ. |WYŁ. |ON |
| Model gotowości |Miga |WYŁ. |WYŁ. |WYŁ. |
| Pobieranie oprogramowania układowego PCM |WYŁ. |Miga |Miga |Miga |

## <a name="controller-module-indicator-leds"></a>Diody LED wskaźników modułu kontrolera
Urządzenie StorSimple zawiera diody LED dla sterownika podstawowego i modułów kontrolera EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Diody LED do monitorowania sterownika głównego
Poniższa ilustracja ułatwia identyfikację diod LED na głównym kontrolerze. (Wszystkie składniki są wymienione w celu ułatwienia orientacji).  

   ![Diody LED do monitorowania - sterownik główny][4]

Poniższa tabela służy do określania, czy moduł kontrolera działa poprawnie.  

### <a name="controller-indicator-leds"></a>Diody LED wskaźników kontrolera
| Led | Opis |
| --- | --- |
| Dioda LED identyfikatora (niebieska) |Wskazuje, że moduł jest identyfikowany. Jeśli niebieska dioda LED miga na uruchomionym kontrolerze, sterownik jest aktywnym kontrolerem, a drugi kontrolerem rezerwowym. Aby uzyskać więcej informacji, zobacz [Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Dioda LED usterek (bursztynowa) |Wskazuje usterkę w sterowniku. |
| OK LED (zielony) |Stały zielony wskazuje, że kontroler jest OK. Miganie na zielono oznacza błąd konfiguracji vpd kontrolera. |
| Diody LED aktywności SAS (zielony) |Stały zielony oznacza połączenie bez bieżącej aktywności. Migający zielony oznacza, że połączenie ma ciągłą aktywność. |
| Diody LED stanu Ethernet |Po prawej stronie wskazuje łącze/aktywność sieci: (stały zielony) łącze aktywne, (migające na zielono) aktywność sieci. Po lewej stronie wskazuje prędkość sieci: (żółty) 1000 Mb/s, (zielony) 100 Mb/s i (WYŁ.) 10 Mb/s. W zależności od modelu komponentu ta lampka może migać, nawet jeśli interfejs sieciowy nie jest włączony. |
| Diody LED POST |Wskazuje postęp rozruchu po włączeniu kontrolera. Jeśli uruchomienie urządzenia StorSimple nie powiedzie się, ta dioda LED pomoże pomocy technicznej firmy Microsoft zidentyfikować punkt w procesie rozruchu, w którym wystąpił błąd. |

> [!IMPORTANT]
> Jeśli dioda LED usterki świeci się, występuje problem z modułem kontrolera, który może zostać rozwiązany przez ponowne uruchomienie kontrolera. Skontaktuj się z pomocą techniczną firmy Microsoft, jeśli ponowne uruchomienie kontrolera nie rozwiąże tego problemu.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Diody LED do monitorowania ebod (obudowa EBOD)
Każdy z kontrolerów SAS EBOD 6 Gb/s ma diody LED, które wskazują jego stan, jak pokazano na poniższej ilustracji.  

  ![Diody LED do monitorowania - obudowa EBOD][5]

Poniższa tabela służy do określenia, czy moduł kontrolera EBOD działa normalnie.  

### <a name="ebod-controller-module-indicator-leds"></a>Diody LED modułu sterownika EBOD
| Stan | Moduł we/wy OK (zielony) | Usterka modułu we/wy (bursztynowa) | Aktywność portu hosta (zielona) |
| --- | --- | --- | --- |
| Moduł kontrolera OK |ON |WYŁ. |- |
| Usterka modułu kontrolera |WYŁ. |ON |- |
| Brak połączenia z portem hosta zewnętrznego |- |- |WYŁ. |
| Zewnętrzne połączenie z portem hosta – brak działań |- |- |ON |
| Zewnętrzne połączenie portu hosta — działanie |- |- |Miga |
| Błąd metadanych modułu kontrolera |Miga |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Diody LED wskaźnika dysku dla obudowy głównej i obudowy EBOD
Urządzenie StorSimple ma dyski znajdujące się zarówno w obudowy podstawowej, jak i w obudowie EBOD. Każdy dysk zawiera diody LED ze wskaźnikiem monitorowania, jak opisano w tej sekcji. 

W przypadku dysków stan dysku jest wskazywany przez zieloną diodę LED i czerwono-bursztynową diodę LED zamontowaną z przodu każdego modułu nośnika napędu. Na poniższej ilustracji przedstawiono te diody LED.

  ![Diody LED dysków][6]

Poniższa tabela służy do określania stanu każdego dysku, co z kolei wpływa na ogólny stan diody LED panelu przedniego.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Diody LED wskaźnika dysku dla obudowy EBOD
| Stan | Aktywność OK LED (zielona) | Dioda LED usterek (czerwono-bursztynowa) | Powiązana dioda LED panelu operacji |
| --- | --- | --- | --- |
| Brak zainstalowanego dysku |WYŁ. |WYŁ. |Brak |
| Napęd zainstalowany i operacyjny |Miganie włączanie/wyłączanie z aktywnością |X |Brak |
| Zestaw tożsamości urządzeń usług obudowy SCSI (SES) |ON |Miga 1 sekunda włączonego/1 sekundy wyłączenia |Brak |
| Zestaw bitów błędów urządzenia SES |ON |ON |Błąd logiczny (czerwony) |
| Awaria obwodu sterowania zasilaniem |WYŁ. |ON |Usterka modułu (czerwony) |

## <a name="audible-alarms"></a>Alarmy dźwiękowe
Urządzenie StorSimple zawiera alarmy dźwiękowe związane zarówno z obudową podstawową, jak i z obudową EBOD. Na panelu przednim (zwanym również panelem operacyjnym) obu obudów znajduje się alarm dźwiękowy. Alarm dźwiękowy wskazuje, kiedy występuje stan usterki. W ysydakuje:  

* Usterka lub awaria wentylatora
* Napięcie poza zasięgiem
* Warunki powyżej lub w temperaturze
* Przekroczenie termiczne
* Usterka systemu
* Błąd logiczny
* Usterka zasilania
* Usunięcie modułu chłodzenia (PCM)  

W poniższej tabeli opisano różne stany alarmowe.  

### <a name="alarm-states"></a>Stany alarmowe
| Stan alarmu | Akcja | Akcja z wciśniętym przyciskiem wyciszenia |
| --- | --- | --- |
| S0 |Tryb normalny: cichy |Sygnał dźwiękowy dwa razy |
| S1 |Tryb usterki: 1 sekunda wł/1 sekundy wyłączenia |Przejście do S2 lub S3 (patrz uwagi) |
| S2 |Tryb przypominania: przerywany sygnał dźwiękowy |Brak |
| S3 |Tryb wyciszony: cichy |Brak |
| S4 |Tryb awarii krytycznej: alarm ciągły |Niedostępne: wyciszenie nieaktywne |

> [!NOTE]
> * W stanie alarmowym S1, jeśli nie naciśniesz wyciszenia w ciągu 2 minut, stan automatycznie przejdzie do S2 lub S3.  
> * Stany alarmowe S1 do S4 wracają do S0 po usunięciu usterki.  
> * Krytyczny stan usterki S4 można wprowadzić z dowolnego innego stanu.  


Alarm dźwiękowy można wyciszyć, naciskając przycisk wyciszenia na panelu ops. Automatyczne wyciszanie nastąpi po dwóch minutach, jeśli przełącznik wyciszenia nie jest obsługiwany ręcznie. Gdy alarm jest wyciszony, będzie nadal rozbrzmiewał krótkimi przerywanymi sygnałami dźwiękowymi, aby wskazać, że problem nadal istnieje. Alarm będzie cichy, gdy wszystkie problemy zostaną wyczyszczone.

W poniższej tabeli opisano różne warunki alarmowe.

### <a name="alarm-conditions"></a>Warunki alarmowe
| Stan | Ważność | Alarm | Dioda LED panelu ops |
| --- | --- | --- | --- |
| Alert PCM – utrata zasilania prądem stałym z jednego pcm |Usterka – brak utraty nadmiarowości |S1 |Usterka modułu |
| Alert PCM – utrata zasilania prądem stałym z jednego pcm |Usterka – utrata nadmiarowości |S1 |Usterka modułu |
| Niepowodzenie wentylatora PCM |Usterka – utrata nadmiarowości |S1 |Usterka modułu |
| Moduł SBB wykrył usterkę PCM |Usterki |S1 |Usterka modułu |
| PcM usunięte |Błąd konfiguracji |Brak |Usterka modułu |
| Błąd konfiguracji obudowy |Usterka – krytyczna |S1 |Usterka modułu |
| Alert o niskiej temperaturze ostrzegawczej |Ostrzeżenie |S1 |Usterka modułu |
| Alert o wysokiej temperaturze ostrzegawczej |Ostrzeżenie |S1 |Usterka modułu |
| Alarm przeceny temperatury |Usterka – krytyczna |S1 |Usterka modułu |
| Awaria magistrali I2C |Usterka – utrata nadmiarowości |S1 |Usterka modułu |
| Błąd komunikacji panelu ops (I2C) |Usterka – krytyczna |S1 |Usterka modułu |
| Błąd kontrolera |Usterka – krytyczna |S1 |Usterka modułu |
| Usterka modułu interfejsu SBB |Usterka – krytyczna |S1 |Usterka modułu |
| Usterka modułu interfejsu SBB – Brak pozostałych modułów funkcjonowania |Usterka – krytyczna |S4 |Usterka modułu |
| Usunięto moduł interfejsu SBB |Ostrzeżenie |Brak |Usterka modułu |
| Usterka sterowania zasilaniem napędu |Ostrzeżenie – brak utraty mocy napędowej |S1 |Usterka modułu |
| Usterka sterowania zasilaniem napędu |Usterka – krytyczna; utrata mocy napędowej |S1 |Usterka modułu |
| Napęd usunięty |Ostrzeżenie |Brak |Usterka modułu |
| Brak dostępnej mocy |Ostrzeżenie |brak |Usterka modułu |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [składnikach sprzętowych StorSimple i stanie](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


