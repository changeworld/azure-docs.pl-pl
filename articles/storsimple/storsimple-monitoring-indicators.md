---
title: Wskaźniki monitorowania StorSimple | Dokumentacja firmy Microsoft
description: Opisuje światła — elektroluminescencyjne (LED) i dźwiękowych alarmy służy do monitorowania stanu urządzenia StorSimple.
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
ms.openlocfilehash: 9ae0caec211dc1199f0abd2ce9bc0c7ad11c02ec
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2017
ms.locfileid: "24030361"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Umożliwia zarządzanie urządzeniem StorSimple monitorowania wskaźników


## <a name="overview"></a>Omówienie
Urządzenia StorSimple zawiera światła — elektroluminescencyjne (LED) i alarmów, można monitorować, moduły i ogólny stan urządzenia StorSimple. Wskaźniki monitorowania można znaleźć w składniki sprzętowe obudowa głównej urządzenia i obudowy EBOD. Wskaźniki monitorowania może być LED lub dźwiękowe alarmy.

Istnieją trzy stany LED służy do wskazania stanu modułu: zielony miga zielony żółtą czerwony lub żółtą czerwony.  

* Zielony LED reprezentują prawidłowego stanu operacyjnego.  
* Miga zielonego na czerwony żółtą LED reprezentują obecności niekrytyczne warunki, które mogą wymagać interwencji użytkownika.  
* LED żółtą czerwony oznacza, że istnieje w module błąd krytyczny.  

W dalszej części tego artykułu opisano różne monitorowania LED wskaźnika, ich lokalizacji na urządzeniu StorSimple, stan urządzenia na podstawie stanów LED i wszystkie skojarzone alarmy dźwiękowego.

## <a name="front-panel-indicator-leds"></a>Wskaźnik panelu przedniego LED
Przodu panelu, znanej także jako *panelu Operacje* lub *panelu ops*, wyświetla zagregowany stan wszystkich modułów w systemie. Panelu przedniego jest identyczne w głównej StorSimple i obudowy EBOD i przedstawiono poniżej.  

   ![Urządzenie panelu przedniego][1]

Panelu przedniego zawiera następujące wskaźniki:  

1. Przycisk wyciszenia
2. Wskaźnik zasilania LED (zielony czerwony żółtą)
3. Wskaźnik błędów modułu DOPROWADZIŁY (na czerwono żółtą/wyłączona)
4. Wskaźnik błędów logicznych DOPROWADZIŁY (na czerwono żółtą/wyłączona
5. Wyświetlanie Identyfikatora jednostki  

Główna różnica między LED panelu przedniego urządzenia oraz konfiguracje obudowa EBOD jest **numeru identyfikacyjnego jednostki systemu** wyświetlane na ekranie LED. Jednostka domyślny identyfikator wyświetlany na urządzeniu jest **00**, jest wyświetlany na obudowę EBOD domyślny identyfikator jednostki **01**. Dzięki temu można szybko rozróżnianie między urządzeniem i obudowy EBOD, gdy urządzenie jest włączone. Jeśli urządzenie jest wyłączone, należy skorzystać z informacji zawartych w [włączyć nowe urządzenie](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) do odróżnienia urządzenia łączącego obudowę EBOD.  

## <a name="front-panel-led-status"></a>Stan LED panelu przedniego
Poniższa tabela umożliwia zidentyfikowanie stanu wskazuje LED na panelu przednim urządzenia lub obudowy EBOD.  

| Zasilania systemu | Błąd modułu | Błąd logiczny | Alarm | Stan |
| --- | --- | --- | --- | --- |
| Żółtą czerwony |WYŁĄCZANIE |WYŁĄCZANIE |Nie dotyczy |Zasilacz utracone, korzysta z kopii zapasowej zasilania lub zasilacz i kontroler, który modułów zostały usunięte. |
| Zielony |ON |ON |Nie dotyczy |Stan testu OPS panelu Włącz (5s) |
| Zielony |WYŁĄCZANIE |WYŁĄCZANIE |Nie dotyczy |Włącz wszystkie funkcje dobra |
| Zielony |ON |Nie dotyczy |Błędów PCM LED, błędów wentylator LED |Żadnych błędów PCM, wentylator usterka, powyżej lub poniżej temperatury |
| Zielony |ON |Nie dotyczy |We/Wy modułu LED |Błąd modułu dowolnego kontrolera |
| Zielony |ON |Nie dotyczy |Nie dotyczy |Obudowa logiki błędów |
| Zielony |Flash |Nie dotyczy |Stan modułu PRZEPROWADZONY w module kontrolera. Błędów PCM LED, błędów wentylator LED |Zainstalowany kontroler nieznany typ modułu, I2C magistrali błąd, błąd konfiguracji kontrolera modułu niezbędne produktu danych (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Chłodzenia modułu (PCM) wskaźnik LED zasilania
Zasilania chłodzenia modułu (PCM) wskaźnik LED można znaleźć tyłu obudowy podstawowego lub obudowa EBOD na każdy Moduł PCM. W tym temacie omówiono sposób użycia następujących LED do monitorowania stanu urządzenia StorSimple.  

* LED PCM dla podstawowego obudowy
* Wskaźniki LED PCM obudowa EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LED PCM dla podstawowego obudowy
Urządzenie StorSimple ma Moduł PCM 764W o dodatkowe baterii. Na poniższej ilustracji przedstawiono panelu LED urządzenia.  

   ![PCM LED obudowa podstawowego][2]

Legenda LED:

1. Ak awarii zasilania
2. Wentylator awarii
3. Uszkodzenia
4. PCM OK
5. Błąd kontrolera domeny
6. Dobrym baterii  

Stan PCM jest sygnalizowane na panelu LED. Panel PCM LED urządzenie ma sześć LED. Cztery te LED wyświetlanie stanu zasilania i wentylatora. Pozostałe dwie LED wskazują stan modułu baterii kopii zapasowej w PCM. Poniższe tabele można użyć do określenia stanu PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Wskaźnik PCM LED zasilania i wentylatora
| Stan | PCM OK (zielony) | Niepowodzenie AC (żółte) | Wentylator kończyć się niepowodzeniem (żółte) | Niepowodzenie kontrolera domeny (żółte) |
| --- | --- | --- | --- | --- |
| Nie zasilacza (aby obudowa) |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |
| Nie zasilacza (tego PCM tylko) |WYŁĄCZANIE |ON |WYŁĄCZANIE |ON |
| Ak przedstawia PCM ON - OK |ON |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |
| Błędów PCM (niepowodzenie wentylator) |WYŁĄCZANIE |WYŁĄCZANIE |ON |Nie dotyczy |
| Błędów PCM (za pośrednictwem amp za pośrednictwem napięcia przez bieżący) |WYŁĄCZANIE |ON |ON |ON |
| PCM (wentylator poza okresem tolerancji) |ON |WYŁĄCZANIE |WYŁĄCZANIE |ON |
| Tryb wstrzymania |Miga |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |
| Pobranie oprogramowania układowego PCM |WYŁĄCZANIE |Miga |Miga |Miga |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Wskaźnik PCM LED dla kopii zapasowej baterii
| Stan | Baterii dobrą (zielony) | Uszkodzenia (żółtą) |
| --- | --- | --- |
| Brak baterii |WYŁĄCZANIE |WYŁĄCZANIE |
| Baterii obecne i obciążona |ON |WYŁĄCZANIE |
| Procedury ładowania lub konserwacji baterii |Miga |WYŁĄCZANIE |
| "Ciąg soft" uszkodzenia (możliwe do odzyskania) |WYŁĄCZANIE |Miga |
| Błąd "sprzętowy" baterii (nieodwracalny) |WYŁĄCZANIE |ON |
| Pozbawionych baterii |Miga |WYŁĄCZANIE |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Wskaźniki LED PCM obudowa EBOD
Obudowa EBOD ma 580W PCM i nie dodatkowe baterii. Panel PCM obudowa EBOD ma wskaźnik LED tylko w przypadku zasilacze i wentylatora. Na poniższej ilustracji przedstawiono te LED.

   ![PCM LED obudowa EBOD][3] 

Poniższa tabela umożliwia określenie stanu PCM.  

| Stan | PCM OK (zielony) | Niepowodzenie AC (żółte) | Wentylator kończyć się niepowodzeniem (żółte) | Niepowodzenie kontrolera domeny (żółte) |
| --- | --- | --- | --- | --- |
| Nie zasilacza (aby obudowa) |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |
| Nie zasilacza (tego PCM tylko) |WYŁĄCZANIE |ON |WYŁĄCZANIE |ON |
| Ak przedstawia ON PCM — OK |ON |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |
| Błędów PCM (niepowodzenie wentylator) |WYŁĄCZANIE |WYŁĄCZANIE |ON |X |
| Błędów PCM (za pośrednictwem amp za pośrednictwem napięcia za pośrednictwem bieżącego |WYŁĄCZANIE |ON |ON |ON |
| PCM (wentylator poza okresem tolerancji) |ON |WYŁĄCZANIE |WYŁĄCZANIE |ON |
| Rezerwy modelu |Miga |WYŁĄCZANIE |WYŁĄCZANIE |WYŁĄCZANIE |
| Pobranie oprogramowania układowego PCM |WYŁĄCZANIE |Miga |Miga |Miga |

## <a name="controller-module-indicator-leds"></a>Kontroler modułu wskaźnik LED
Urządzenia StorSimple zawiera LED dla podstawowego kontrolera i moduły EBOD kontrolera.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorowanie LED dla podstawowego kontrolera
Poniższa ilustracja pomaga zidentyfikować LED na podstawowym kontrolerze. (Wszystkie składniki są wymienione pomocy w orientacji.)  

   ![Monitorowanie LED - podstawowego kontrolera][4]

Skorzystaj z poniższej tabeli, aby określić, czy moduł kontrolera działa prawidłowo.  

### <a name="controller-indicator-leds"></a>Wskaźnik kontrolera LED
| LED | Opis |
| --- | --- |
| Identyfikator LED (niebieski) |Wskazuje, czy moduł jest identyfikowany. Jeśli niebieski LED jest migający na działającego kontrolera, kontroler jest aktywne i jeden z nich jest kontrolerem wstrzymania. Aby uzyskać więcej informacji, zobacz [Zidentyfikuj active kontroler na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Błąd LED (żółtą) |Wskazuje błąd w kontrolerze. |
| LED OK (zielony) |Zielony stałej wskazuje, czy kontroler jest OK. Miga zielony oznacza kontroler VPD błąd konfiguracji. |
| Działanie SAS LED (zielony) |Stałej zielony oznacza nie bieżącego działania z nim połączenia. Miga zielony oznacza, że połączenie jest bieżące działanie. |
| Stan Ethernet LED |Po prawej stronie wskazuje działania łącza i sieci: link (zielony stałej) active (miga zielony) działań w sieci. Po lewej stronie wskazuje szybkość sieci: (żółty) 1000 Mb/s (zielony) 100 Mb/s i (OFF) 10 Mb/s. W zależności od modelu składnika powyższe może blink, nawet jeśli nie włączono interfejsu sieciowego. |
| LED POST |Wskazuje postęp rozruchu po włączeniu kontrolera. Jeśli urządzenie StorSimple zakończy się niepowodzeniem, rozruch, to LED pomoże Microsoft Support identyfikowania punktu w proces rozruchu, w którym wystąpił błąd. |

> [!IMPORTANT]
> Jeśli błąd LED jest włączone, występuje problem z modułem kontrolera, którego może rozwiązać przez ponowne uruchomienie kontrolera. Jeśli ponowne uruchomienie kontrolera nie rozwiązuje tego problemu, skontaktuj się Support firmy Microsoft.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorowanie LED EBOD (EBOD obudowa)
Poszczególnych kontrolerów 6 Gb/s EBOD SAS ma LED, które wskazać jej stan, jak pokazano na poniższej ilustracji.  

  ![Monitorowanie LED - obudowa EBOD][5]

Skorzystaj z poniższej tabeli, aby określić, czy moduł kontrolera EBOD działa normalnie.  

### <a name="ebod-controller-module-indicator-leds"></a>Wskaźnik modułu kontrolera EBOD LED
| Stan | We/Wy modułu OK (zielony) | Błąd modułu we/wy (żółtą) | Aktywność port hosta (zielony) |
| --- | --- | --- | --- |
| Moduł kontrolera OK |ON |WYŁĄCZANIE |- |
| Błąd modułu kontrolera |WYŁĄCZANIE |ON |- |
| Brak połączenia portu zewnętrznego hosta |- |- |WYŁĄCZANIE |
| Hoście zewnętrznym portu połączenia — Brak działania |- |- |ON |
| Połączenie z portu zewnętrznego hosta — działania |- |- |Miga |
| Błąd metadanych modułu kontrolera |Miga |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Wskaźnik dysku LED obudowy podstawowego i obudowy EBOD
Urządzenie StorSimple ma dysków znajduje się w głównej obudowa i obudowy EBOD. Każdy dysk zawiera monitorowania LED wskaźnika, zgodnie z opisem w tej sekcji. 

Dla stacji dysków, stan dysku jest określane przez zielona LED i LED żółtą czerwony zainstalowane na początku każdego modułu operatora dysku. Na poniższej ilustracji przedstawiono te LED.

  ![LED dysku][6]

Skorzystaj z poniższej tabeli, aby określić stan każdego dysku twardego, który z kolei ma wpływ na panelu przednim ogólny stan LED.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Dysk wskaźnik LED obudowa EBOD
| Stan | Działanie LED OK (zielony) | Błąd LED (czerwony żółtą) | Skojarzone panelu ops LED |
| --- | --- | --- | --- |
| Dysk nie jest zainstalowany |WYŁĄCZANIE |WYŁĄCZANIE |Brak |
| Dysk zainstalowany i działa |Miga/wyłączone z działania |X |Brak |
| Zestaw tożsamości urządzenia SCSI usług obudowy (SES) |ON |Miga 1 sekundę na/1 sekundę poza |Brak |
| Ustawiony bit błędów urządzenia SES |ON |ON |Błąd logiczny (czerwony) |
| Obwód sterowania zasilaniu |WYŁĄCZANIE |ON |Błąd modułu (czerwony) |

## <a name="audible-alarms"></a>Alarm dźwiękowy
Urządzenie StorSimple zawiera dźwiękowe alarmy związane z zarówno podstawowy obudowa i obudowy EBOD. Alarm dźwiękowy znajduje się na panelu przednim (znanej także jako panel ops) zarówno obudów. Alarmu wskazuje, kiedy występuje warunek błędu. Poniższe warunki uaktywni alarmu:  

* Wentylator błędów lub niepowodzenie
* Napięcia poza zakresem
* Powyżej lub poniżej temperatury warunku
* Przepełnienie cieplna
* Błąd systemowy
* Błąd logiczny
* Awarii zasilania zasilania
* Usunięcie mocy chłodzenia modułu (PCM)  

W poniższej tabeli opisano różne stany alarm.  

### <a name="alarm-states"></a>Alarm stanów
| Alarm stanu | Akcja | Działania przyciskiem wyciszenia naciśnięty |
| --- | --- | --- |
| S0 |Tryb normalny: dyskretnej |Dwa razy dźwiękowego |
| S1 |Tryb błędów: 1 sekundę na/1 sekundę poza |Przejście do S2 lub S3 (zobacz Uwagi) |
| S2 |Przypomnij tryb: Sporadyczne dźwiękowego |Brak |
| S3 |Tryb stonowane: dyskretnej |Brak |
| S4 |Tryb błędów krytycznych: alarm ciągłej |Nie jest dostępna: wyciszanie nie jest aktywny |

> [!NOTE]
> * W stanie alarm S1 Jeśli nie zostanie naciśnięty wyciszenia w ciągu 2 minut, stan automatycznie zostanie przekazana do S2 lub S3.  
> * Alarm Państwa S1 S4 zwracają S0 po awarii jest wyczyszczone.  
> * Stan krytyczny błąd S4 można wpisać w inny stan.  


Wyciszanie z alarmu, naciskając przycisk wyciszenia na panelu ops. Automatyczne wyciszanie nastąpi po dwóch minut Jeśli wyciszenia przełącznika nie jest obsługiwana ręcznie. Gdy wyciszeniu alarmu, będzie dźwięk z krótką sygnały sporadyczne wskazująca, czy problem nadal istnieje. Alarm będzie dyskretnej, gdy wszystkie problemy zostały wyczyszczone.

W poniższej tabeli opisano różne warunki alarm.

### <a name="alarm-conditions"></a>Alarm warunków
| Stan | Ważność | Alarm | OPS panelu LED |
| --- | --- | --- | --- |
| Alert PCM — utraty zasilania kontrolera domeny z jednym PCM |Błąd — nie utraty redundancji |S1 |Błąd modułu |
| Alert PCM — utraty zasilania kontrolera domeny z jednym PCM |Błąd — Utrata redundancji |S1 |Błąd modułu |
| Wentylator PCM kończyć się niepowodzeniem |Błąd — Utrata redundancji |S1 |Błąd modułu |
| Wykryto moduł SBB błędów PCM |Błąd |S1 |Błąd modułu |
| Usunięte PCM |Błąd konfiguracji |Brak |Błąd modułu |
| Błąd konfiguracji obudowy |Błąd — krytyczne |S1 |Błąd modułu |
| Niski alert temperatury ostrzeżenia |Ostrzeżenie |S1 |Błąd modułu |
| Wysoka alert temperatury ostrzeżenia |Ostrzeżenie |S1 |Błąd modułu |
| Za pośrednictwem alarm temperatury |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd magistrali I2C |Błąd — Utrata redundancji |S1 |Błąd modułu |
| Błąd komunikacji (I2C) panelu OPS |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd kontrolera |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd modułu interfejsu SBB |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd modułu interfejsu SBB — nie działa modułów pozostałych |Błąd — krytyczne |S4 |Błąd modułu |
| Moduł interfejsu SBB usunięte |Ostrzeżenie |Brak |Błąd modułu |
| Dysk awarii zasilania formantu |Ostrzeżenie — bez utraty zasilania dysku |S1 |Błąd modułu |
| Dysk awarii zasilania formantu |Błąd — krytyczne; utraty zasilania dysku |S1 |Błąd modułu |
| Usunięto dysk |Ostrzeżenie |Brak |Błąd modułu |
| Za mało dostępnych zasilania |Ostrzeżenie |Brak |Błąd modułu |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [StorSimple składniki sprzętowe i stan](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


