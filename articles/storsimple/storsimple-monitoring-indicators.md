---
title: Monitorowanie wskaźników StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano diody elektroluminescencyjne (diod LED) i alarm dźwiękowy służy do monitorowania stanu urządzenia StorSimple.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630613"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Umożliwia zarządzanie urządzeniem StorSimple wskaźniki monitorowania


## <a name="overview"></a>Omówienie
Urządzenia StorSimple obejmuje diody elektroluminescencyjne (diod LED) i alarmów, można użyć do monitorowania, moduły i ogólny stan urządzenia StorSimple. Wskaźniki monitorowania można znaleźć w ramach składników sprzętowych obudowy podstawowego urządzenia i obudowy EBOD. Wskaźniki monitorowania może być diod LED lub alarm dźwiękowy.

Istnieją trzy stany LED służy do wskazywania stanu modułu: zielony, od zielonego do czerwonego żółtą lub czerwone żółtą miga.  

* Zielony diod LED reprezentują dobrej kondycji operacyjnej.  
* Migające od zielonego do czerwonego żółtą diod LED reprezentują obecności niekrytyczne warunki, które mogą wymagać interwencji użytkownika.  
* Diod LED żółtą czerwony oznacza, że obecna w module błędów krytycznych.  

W dalszej części tego artykułu opisano różne diod LED wskaźnika monitorowania, ich lokalizacji na urządzeniu StorSimple, stan urządzenia na podstawie stanów LED i wszystkie skojarzone alarm dźwiękowy.

## <a name="front-panel-indicator-leds"></a>Na panelu przednim diod LED
Znany także jako frontonu panelu *panelu Operacje* lub *panelu ops*, wyświetla zagregowany stan wszystkich modułów w systemie. Panelu przedniego jest taka sama na podstawowym StorSimple i obudowy EBOD i jest przedstawione poniżej.  

   ![Urządzenie panelu przedniego][1]

Panelu przedniego zawiera następujących wskaźników:  

1. Wycisz przycisku
2. Wskaźnik zasilania LED (kolor zielony czerwony — żółtą)
3. Wskaźnik błędów modułu prowadzone (na czerwono żółtą/OFF)
4. Wskaźnik błędów logicznych prowadzone (na czerwono żółtą/OFF
5. Wyświetlanie Identyfikatora jednostki  

Główna różnica między diod LED panelu przedniego urządzenia oraz konfiguracje obudowy EBOD jest **numeru identyfikacyjnego jednostki systemu** wyświetlane na ekranie LED. Domyślna jednostka jest wyświetlana na urządzeniu identyfikator **00**, a domyślny identyfikator jednostki, wyświetlane na obudowę EBOD **01**. Dzięki temu można szybko rozróżnienia między urządzeniem i obudowy EBOD, gdy urządzenie jest włączone. Jeśli urządzenie jest wyłączone, użyj informacji podanych w [włączyć nowe urządzenie](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) do odróżnienia urządzenia obudowę EBOD.  

## <a name="front-panel-led-status"></a>Na panelu przednim stan diody LED
Poniższa tabela umożliwia zidentyfikowanie stan wskazywanym przez diod LED na panelu przednim urządzenia lub obudowy EBOD.  

| Zasilania systemu | Błąd modułu | Błąd logiczny | Alarm | Stan |
| --- | --- | --- | --- | --- |
| Red-amber |WYŁĄCZONE |WYŁĄCZONE |ND |Zasilacz utracone, wykonywanie operacji na mocy kopii zapasowej lub Przywrócenie zasilania sieciowego na i kontrolera, które moduły zostały usunięte. |
| Zielony |ON |ON |ND |Panel OPS Włącz 5 (s) testowanie stanu |
| Zielony |WYŁĄCZONE |WYŁĄCZONE |ND |Włącz wszystkie funkcje dobre |
| Zielony |ON |ND |Błąd modułu PCM diod LED, błędów wentylator diod LED |Winy PCM wentylator usterka, powyżej lub poniżej temperatury |
| Zielony |ON |ND |Moduł we/wy diody LED |Wszelkie usterki modułu kontrolera |
| Zielony |ON |ND |ND |Obudowa logiki błędów |
| Zielony |Flash |ND |Stan modułu prowadzone w module kontrolera. Błąd modułu PCM diod LED, błędów wentylator diod LED |Zainstalowany kontroler nieznany typ modułu, I2C magistrali niepowodzenie, błąd konfiguracji danych (VPD) kontrolera modułu produktu |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Chłodzenie w module (PCM) diod LED zasilania
Zużycia chłodzenia modułu (PCM) diod LED wskaźnika znajdują się na odwrocie podkładki głównej obudowy lub obudowy EBOD w każdym module PCM. W tym temacie omówiono sposób użycia następujących diod LED monitorowania stanu urządzenia StorSimple.  

* LED PCM dla podstawowego obudowy
* PCM diody obudowy EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LED PCM dla podstawowego obudowy
Urządzenie StorSimple ma modułu PCM 764W z dodatkowych baterii. Poniższa ilustracja przedstawia panel LED dla urządzenia.  

   ![PCM LED głównej obudowy][2]

Legenda LED:

1. Awarii zasilania programu AC
2. Awaria wentylatora
3. Błąd baterii
4. PCM OK
5. Błąd kontrolera domeny
6. Dobre baterii  

Stan modułu PCM, jest wskazany na panelu LED. Panel PCM LED urządzenia zawiera sześć diod LED. Cztery następujące diod LED wyświetlany stan zasilania i wychodzącą. Pozostałe dwie diody LED wskazują stan baterii zapasowej modułu w module PCM. Korzystanie z następujących tabel, aby określić stan modułu PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM diod LED wskaźnika do źródła zasilania i wentylatora
| Stan | PCM OK (zielony) | Niepowodzenie AC (żółte) | Wentylator kończyć się niepowodzeniem (żółte) | Niepowodzenie kontrolera domeny (żółte) |
| --- | --- | --- | --- | --- |
| Nie Przywrócenie zasilania sieciowego (w celu obudowa) |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |
| Nie Przywrócenie zasilania sieciowego (to PCM tylko) |WYŁĄCZONE |ON |WYŁĄCZONE |ON |
| Ak obecne w module PCM — OK |ON |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |
| Niepowodzenie PCM (wentylator po awarii) |WYŁĄCZONE |WYŁĄCZONE |ON |ND |
| Błąd modułu PCM (za pośrednictwem amp za pośrednictwem napięcia za pośrednictwem bieżącego) |WYŁĄCZONE |ON |ON |ON |
| PCM (wentylator poza okresem tolerancji) |ON |WYŁĄCZONE |WYŁĄCZONE |ON |
| Tryb rezerwy dynamicznej |Migające |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |
| Pobieranie oprogramowania układowego modułu PCM |WYŁĄCZONE |Migające |Migające |Migające |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM diod LED dla baterii zapasowej
| Stan | Bateria dobrą (zielony) | Błąd baterii (żółtą) |
| --- | --- | --- |
| Nie obecność baterii |WYŁĄCZONE |WYŁĄCZONE |
| Bateria obecne i płatne |ON |WYŁĄCZONE |
| Zrzut ładowania lub konserwację baterii |Migające |WYŁĄCZONE |
| Błąd "programowy" baterii (możliwe do odzyskania) |WYŁĄCZONE |Migające |
| Błąd "sprzętowy" baterii (nieodwracalny) |WYŁĄCZONE |ON |
| Pozbawionych baterii |Migające |WYŁĄCZONE |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM diody obudowy EBOD
Obudowa EBOD ma 580W PCM i nie dodatkowe baterii. Panel PCM obudowy EBOD ma diod LED wskaźnika tylko w przypadku zasilacze i wychodzącą. Poniższa ilustracja przedstawia te diod LED.

   ![PCM LED obudowy EBOD][3] 

Poniższa tabela służy do określenia stanu modułu PCM.  

| Stan | PCM OK (zielony) | Niepowodzenie AC (żółte) | Wentylator kończyć się niepowodzeniem (żółte) | Niepowodzenie kontrolera domeny (żółte) |
| --- | --- | --- | --- | --- |
| Nie Przywrócenie zasilania sieciowego (w celu obudowa) |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |
| Nie Przywrócenie zasilania sieciowego (to PCM tylko) |WYŁĄCZONE |ON |WYŁĄCZONE |ON |
| Ak obecne w module PCM — OK |ON |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |
| Niepowodzenie PCM (wentylator po awarii) |WYŁĄCZONE |WYŁĄCZONE |ON |X |
| Błąd modułu PCM (za pośrednictwem amp za pośrednictwem napięcia za pośrednictwem bieżącego |WYŁĄCZONE |ON |ON |ON |
| PCM (wentylator poza okresem tolerancji) |ON |WYŁĄCZONE |WYŁĄCZONE |ON |
| Model rezerwy dynamicznej |Migające |WYŁĄCZONE |WYŁĄCZONE |WYŁĄCZONE |
| Pobieranie oprogramowania układowego modułu PCM |WYŁĄCZONE |Migające |Migające |Migające |

## <a name="controller-module-indicator-leds"></a>Kontroler modułu diod LED
Urządzenie StorSimple zawiera diod LED dla modułów kontrolera EBOD i kontroler podstawowy.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Diod LED monitorowania dla podstawowego kontrolera
Na poniższej ilustracji pomaga zidentyfikować diod LED na podstawowym kontrolerze. (Wszystkie składniki są wymienione pomocy w orientacji.)  

   ![Diod LED monitorowania - kontroler podstawowy][4]

Skorzystaj z poniższej tabeli, aby określić, czy moduł kontrolera działa prawidłowo.  

### <a name="controller-indicator-leds"></a>Kontroler diod LED
| LED | Opis |
| --- | --- |
| Identyfikator LED (niebieski) |Wskazuje, że moduł jest zidentyfikowany. Jeśli diody LED jest migające uruchomionego kontrolera, kontrolera jest aktywnym kontrolerem, a druga jest kontroler zapasowy. Aby uzyskać więcej informacji, zobacz [identyfikowanie aktywnego kontrolera na urządzeniu z systemem](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Odporność LED (żółtą) |Wskazuje błędów w kontrolerze. |
| LED OK (zielony) |Stały zielony oznacza, że kontroler jest OK. Migające zielony oznacza kontroler VPD błąd konfiguracji. |
| Sygnatury dostępu Współdzielonego działania diod LED (zielony) |Stały zielony oznacza połączenie z nie bieżące działanie. Migające zielony oznacza, że połączenie ma bieżące działanie. |
| Stan sieci Ethernet diody LED |Po prawej stronie wskazuje na działanie łącza i sieci: aktywne (miga zielony) link (stały kolor zielony) działań w sieci. Po lewej stronie wskazuje szybkość sieci: (żółty) 1000 Mb/s (zielony) 100 Mb/s i (wyłączone) 10 Mb/s. W zależności od modelu składnik to light może blink nawet, jeśli interfejs sieciowy nie jest włączona. |
| WPIS diody LED |Wskazuje postęp rozruchu, gdy kontroler jest włączona. Jeśli urządzenie StorSimple zakończy się niepowodzeniem, rozruch, to LED pomoże Microsoft Support identyfikowania punktu w procesie rozruchu, w którym wystąpił błąd. |

> [!IMPORTANT]
> Świeci błędów diod LED, czy istnieje problem z modułem kontrolera, który może być można rozwiązać przez ponowne uruchomienie kontrolera. Jeśli ponowne uruchomienie kontrolera nie rozwiąże ten problem, skontaktuj się Microsoft Support.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Diod LED monitorowania dla EBOD (obudowa EBOD)
Każda 6 kontrolera EBOD sygnatury dostępu Współdzielonego Gb/s ma diod LED, które wskazują stan, jak pokazano na poniższej ilustracji.  

  ![Diod LED monitorowania - obudowy EBOD][5]

Skorzystaj z poniższej tabeli, aby określić, czy moduł kontrolera EBOD działają normalnie.  

### <a name="ebod-controller-module-indicator-leds"></a>Diod LED wskaźnika modułu kontrolera EBOD
| Stan | Moduł we/wy OK (zielony) | Błąd modułu operacji We/Wy (żółtą) | Aktywność port hosta (zielony) |
| --- | --- | --- | --- |
| Moduł kontrolera OK |ON |WYŁĄCZONE |- |
| Błąd modułu kontrolera |WYŁĄCZONE |ON |- |
| Brak połączenia portu zewnętrznego hosta |- |- |WYŁĄCZONE |
| Host zewnętrzny port połączenia — Brak działania |- |- |ON |
| Host zewnętrzny port połączenia — działanie |- |- |Migające |
| Błąd metadanych modułu kontrolera |Migające |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Dysk diod LED wskaźnika podstawowego obudowy i obudowy EBOD
Urządzenie StorSimple ma dysków znajdujących się w głównej obudowy i obudowy EBOD. Każdy dysk zawiera monitorowania diod LED wskaźnika, zgodnie z opisem w tej sekcji. 

Dla dysków twardych, stan dysku jest wskazywany przez kolor zielony LED i LED żółtą red zainstalowanych na na początku każdego modułu operatora dysku. Poniższa ilustracja przedstawia te diod LED.

  ![Dysk diody LED][6]

Skorzystaj z poniższej tabeli, aby określić stan każdego dysku twardego, który z kolei ma wpływ na panelu przedniego ogólny stan diody LED.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Diod LED wskaźnika dysku dla obudowy EBOD
| Stan | Działanie OK LED (zielony) | Odporność LED (czerwony żółtą) | Skojarzone panelu ops LED |
| --- | --- | --- | --- |
| Dysk nie jest zainstalowany |WYŁĄCZONE |WYŁĄCZONE |Brak |
| Dysk zainstalowany i działa |Migające wł. / wył. za pomocą działania |X |Brak |
| Zestaw tożsamości urządzenia usług obudowy SCSI (SES) |ON |Migające 1 sekundy na/1 sekundę wyłączone |Brak |
| Ustawiony bit błędów urządzenia SES |ON |ON |Błąd logiczny (czerwony) |
| Awarii obwodu kontroli zasilania |WYŁĄCZONE |ON |Błąd modułu (czerwony) |

## <a name="audible-alarms"></a>Alarm dźwiękowy
Urządzenie StorSimple zawiera skojarzone z podstawowym obudowy i obudowy EBOD alarm dźwiękowy. Alarm dźwiękowy znajduje się na panelu przednim (znany także jako panel ops) zarówno obudowy. Alarm dźwiękowy wskazuje, kiedy warunek błędu jest obecny. Następujące warunki uaktywni alarmu:  

* Wentylator błędów lub niepowodzenia
* Napięcie poza zakresem
* Za pośrednictwem lub w ramach warunku temperatury
* Przepełnienie termiczny
* Błąd systemowy
* Błąd logiczny
* Odporność dostaw energii
* Usuwanie mocy chłodzenie w module (PCM)  

W poniższej tabeli opisano różne stany alarmu.  

### <a name="alarm-states"></a>Stany alarmów
| Stan alarmów | Akcja | Akcja o Wycisz wciśnięcie przycisku |
| --- | --- | --- |
| S0 |Tryb normalny: dyskretna |Dwa razy dźwiękowego |
| S1 |Tryb błędów: 1 sekunda/1 sekundę wyłączone. |Przejście do S2 lub S3 (zobacz Uwagi) |
| S2 |Przypomnij tryb: Sporadyczne dźwiękowego |Brak |
| S3 |Tryb stonowane: dyskretna |Brak |
| S4 |Tryb błędów krytycznych: ciągłe alarmów |Nie jest dostępna: Wyciszenie nie jest aktywny |

> [!NOTE]
> * W stanie alarm S1 Jeśli nie zostanie naciśnięty Wycisz w ciągu 2 minut stan automatycznie zostanie przekazana do S2 lub S3.  
> * Po wyczyszczeniu warunku błędu, Stany alarm S1 do poziomu S4 powrócić do S0.  
> * Błąd krytyczny stan S4 może zostać podane z dowolnego innego stanu.  


Wycisz z alarmu, naciskając przycisk Wycisz na panelu ops. Automatyczne wyciszanie wystąpi po dwie minuty Wycisz przełącznika nie jest świadczona ręcznie. Gdy jest wyciszone alarmu, będzie dźwięku za krótki sporadyczne sygnały, aby wskazać, czy problem nadal istnieje. Alarm będzie dyskretnej, gdy wszystkie problemy zostaną wyczyszczone.

W poniższej tabeli opisano różne warunki alarmu.

### <a name="alarm-conditions"></a>Warunki alarmów
| Stan | Severity | Alarm | Platforma OPS panelu LED |
| --- | --- | --- | --- |
| Alarm PCM — utrata zasilania kontrolera domeny z jednego modułu PCM |Błąd — nie utratę nadmiarowości |S1 |Błąd modułu |
| Alarm PCM — utrata zasilania kontrolera domeny z jednego modułu PCM |Błąd — utratę nadmiarowości |S1 |Błąd modułu |
| Wentylator PCM kończyć się niepowodzeniem |Błąd — utratę nadmiarowości |S1 |Błąd modułu |
| Moduł SBB wykrył błąd modułu PCM |Błędów |S1 |Błąd modułu |
| Usunięte modułu PCM |Błąd konfiguracji |Brak |Błąd modułu |
| Błąd konfiguracji obudowy |Błąd — krytyczne |S1 |Błąd modułu |
| Niski alert temperatury ostrzeżenia |Ostrzeżenie |S1 |Błąd modułu |
| Wysoka alert temperatury ostrzeżenia |Ostrzeżenie |S1 |Błąd modułu |
| Za pośrednictwem alarm temperatury |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd magistrali I2C |Błąd — utratę nadmiarowości |S1 |Błąd modułu |
| Platforma OPS panelu błąd komunikacji (I2C) |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd kontrolera |Błąd — krytyczne |S1 |Błąd modułu |
| Błąd modułu interfejsu SBB |Błąd — krytyczne |S1 |Błąd modułu |
| Błędu modułu interfejsu SBB — nie działa modułów pozostałe |Błąd — krytyczne |S4 |Błąd modułu |
| Moduł interfejsu SBB usunięte |Ostrzeżenie |Brak |Błąd modułu |
| Dysk power kontroli błędów |Ostrzeżenie — bez utraty zasilania dysku |S1 |Błąd modułu |
| Dysk power kontroli błędów |Błąd — krytyczne; utrata zasilania dysku |S1 |Błąd modułu |
| Usunięto dysk |Ostrzeżenie |Brak |Błąd modułu |
| Zbyt niski poziom energii dostępne |Ostrzeżenie |Brak |Błąd modułu |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [StorSimple składniki sprzętowe i stan](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


