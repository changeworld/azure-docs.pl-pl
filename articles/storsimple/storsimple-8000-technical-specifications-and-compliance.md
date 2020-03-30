---
title: StorSimple specyfikacje techniczne | Dokumenty firmy Microsoft
description: Zawiera opis specyfikacji technicznych i standardów regulacyjnych dotyczących składników sprzętowych StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965285"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Specyfikacje techniczne i zgodność urządzenia StorSimple

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Składniki sprzętowe urządzenia Microsoft Azure StorSimple są zgodne ze specyfikacjami technicznymi i normami regulacyjnymi opisanymi w tym artykule. Specyfikacje techniczne opisują moduły zasilania i chłodzenia (PCM), dyski, pojemność pamięci masowej i obudowy. Informacje o zgodności obejmują takie kwestie, jak normy międzynarodowe, bezpieczeństwo i emisje oraz okablowanie.

## <a name="power-and-cooling-module-specifications"></a>Specyfikacja modułu zasilania i chłodzenia

Urządzenie StorSimple ma dwa dwa wentylatory 100-240 V, zgodne z SBB moduły chłodzenia zasilania (PCM). Zapewnia to nadmiarową konfigurację zasilania. Jeśli pcm ulegnie awarii, urządzenie będzie działać normalnie na innym komputerze PCM do czasu wymiany nieudanego modułu.

Obudowa EBOD wykorzystuje 580 W PCM, a obudowa podstawowa wykorzystuje 764 W PCM. W poniższych tabelach przedstawiono specyfikacje techniczne związane z pcms.

| Specyfikacja | 580 W PCM (EBOD) | 764 W PCM (podstawowy) |
| --- | --- | --- |
| Maksymalna moc wyjściowa |580 W |764 |
| Częstotliwość |50/60 Hz |50/60 Hz |
| Wybór zakresu napięcia |Automatyczny zakres: 90 – 264 V AC, 47/63 Hz |Automatyczny zakres: 90- 264 V AC, 47/63 Hz |
| Maksymalny prąd rozruchu |20 A |20 A |
| Korekcja współczynnika mocy |>95% nominalnego napięcia wejściowego |>95% nominalnego napięcia wejściowego |
| Harmonicznych |Spełnia norma EN61000-3-2 |Spełnia norma EN61000-3-2 |
| Dane wyjściowe |Napięcie \@ czuwania 5V 2,0 A |Napięcie \@ czuwania 5V 2,7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Gorąca podłączalna |Tak |Tak |
| Przełączniki i diody LED |Przełącznik AC ON/OFF i cztery diody LED wskaźnika stanu |Przełącznik AC ON/OFF i sześć diod LED wskaźnika stanu |
| Chłodzenie obudowy |Osiowe wentylatory chłodzące ze zmienną regulacją prędkości obrotowej wentylatora |Osiowe wentylatory chłodzące ze zmienną regulacją prędkości obrotowej wentylatora |

## <a name="power-consumption-statistics"></a>Statystyki zużycia energii

W poniższej tabeli wymieniono typowe dane zużycia energii (rzeczywiste wartości mogą się różnić od opublikowanych) dla różnych modeli urządzenia StorSimple.

| Warunki | 240 V ac | 240 V ac | 240 V ac | 110 V ac | 110 V ac | 110 V ac |
| --- | --- | --- | --- | --- | --- | --- |
|  Wentylatory są powolne, dyski bezczynne |1.45 A |0,31 kW |1057.76 BTU/godz. |3.19 A |0,34 kW |1160.13 BTU/godz. |
|  Wentylatory są powolne, dostęp do dysków |1,54 A |0,33 kW |1126.01 BTU/godz. |3.27 A |0,36 kW |1228.37 BTU/godz. |
|  Wentylatory szybkie, dyski bezczynne, dwa zasilacze zasilane |2.14 A |0,49 kW |1671,95 BTU/godz. |4.99 A |0,54 kW |1842.56 BTU/godz. |
|  Wentylatory szybkie, dyski bezczynne, jeden zasilacz zasilany jeden bezczynny |2.05 A |0,48 kW |1637.83 BTU/godz. |4.58 A |0,50 kW |1706.07 BTU/godz. |
|  Wentylatory szybko, dyski dostęp, dwa zasilacze zasilane |2.26 A |0,51 kW |1740.19 BTU/godz. |4.95 A |0,54 kW |1842.56 BTU/godz. |
|  Wentylatory szybko, dyski dostęp, jeden zasilacz zasilany jeden bezczynny |2.14 A |0,49 kW |1671,95 BTU/godz. |4.81 A |0,53 kW |1808.44 BTU/godz. |

## <a name="disk-drive-specifications"></a>Specyfikacje dysku

Urządzenie StorSimple obsługuje do 12 3,5-calowych dysków seryjnych (SAS) w formie. Rzeczywiste dyski mogą być mieszanką dysków PÓŁPRZEWODNIKOWYCH (SSD) lub dysków twardych (HDD), w zależności od konfiguracji produktu. 12 gniazd dysków znajduje się w konfiguracji 3 na 4 przed obudową. Obudowa EBOD umożliwia dodatkową pamięć masową dla kolejnych 12 dysków. Są to zawsze dyski twarde.

## <a name="storage-specifications"></a>Specyfikacje pamięci masowej

Urządzenia StorSimple mają mieszankę dysków twardych i dysków PÓŁPRZEWODNIKOWYCH zarówno dla 8100, jak i 8600. Całkowita pojemność użytkowa dla 8100 i 8600 wynosi odpowiednio około 15 TB i 38 TB. Poniższa tabela dokumentuje szczegóły dotyczące dysków SSD, HDD i pojemności chmury w kontekście pojemności rozwiązania StorSimple.

| Model urządzenia / Pojemność | 8100 | 8600 |
| --- | --- | --- |
| Liczba dysków twardych (hdd) |8 |19 |
| Liczba dysków PÓŁPRZEWODNIKOWYCH (SSD) |4 |5 |
| Pojemność pojedynczego dysku twardego |4 TB |4 TB |
| Pojedyncza pojemność SSD |400 GB |800 GB |
| Wolne moce produkcyjne |4 TB |4 TB |
| Pojemność dysków twardych |14 TB |36 TB |
| Pojemność ssd użytkowa |800 GB |2 TB |
| Całkowita pojemność użytkowa* |~ 15 TB |~ 38 TB |
| Maksymalna pojemność rozwiązania (w tym chmura) |200 TB |500 TB |

<sup>* </sup>- *Całkowita użyteczna pojemność obejmuje pojemność dostępną dla danych, metadanych i buforów. Na urządzeniu 8100 można aprowizować woluminy przypięte lokalnie o pojemności do 8,5 TB lub do 22,5 TB na większym urządzeniu 8600. Aby uzyskać więcej informacji, przejdź do [storproste lokalnie przypięte woluminy](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagowe

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wagi.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli wymieniono wymiary obudowy w milimetrach i calach.

| Obudowa | Milimetrach | Cali |
| --- | --- | --- |
| Właściwość Height |87.9 |3.46 |
| Szerokość na kołnierzu montażowym |483 |19.02 |
| Szerokość całej obudowy |443 |17.44 |
| Głębokość od przedniego kołnierza montażowego do krańca korpusu obudowy |577 |22.72 |
| Głębokość od panelu operacyjnego do najdalszej kończyny obudowy |630.5 |24.82 |
| Głębokość od kołnierza montażowego do najdalszej kończyny obudowy |603 |23.74 |

### <a name="enclosure-weight"></a>Masa obudowy

W zależności od konfiguracji w pełni załadowana obudowa podstawowa może ważyć od 21 do 33 kg i wymaga od dwóch osób, aby się z nią obsłużyć.

| Obudowa | Waga |
| --- | --- |
| Maksymalna waga (w zależności od konfiguracji) |30 kg – 33 kg |
| Pusty (bez zamontowanych napędów) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji wymieniono specyfikacje związane ze środowiskiem obudowy. Temperatura, wilgotność, wysokość, wstrząsy, wibracje, orientacja, bezpieczeństwo i kompatybilność elektromagnetyczna (EMC) są uwzględnione w tej kategorii.

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

| Obudowa | Zakres temperatur otoczenia | Wilgotność względna otoczenia | Maksymalna mokra żarówka |
| --- | --- | --- | --- |
| Operacyjne |5°C - 35°C (41°F - 95°F) |20% - 80% bez kondensacji |28°C (82°F) |
| Nieoperacyjne |-40°C - 70°C |5% - 100% bez kondensacji |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Przepływ powietrza, wysokość, wstrząsy, wibracje, orientacja, bezpieczeństwo i EMC

| Obudowa | Specyfikacje operacyjne |
| --- | --- |
| Przepływ powietrza |Przepływ powietrza systemowego jest od przodu do tyłu. System musi być obsługiwany przy instalacji niskociśnieniowej z tylnym układem wydechowym. Ciśnienie wsteczne wytwarzane przez drzwi i przeszkody stelaża nie powinno przekraczać 5 pasm (0,5 mm wodomierza). |
| Wysokość n.p.m., operacyjna |-30 metrów do 3045 metrów (-100 stóp do 10000 stóp) z maksymalną temperaturą roboczą odsypioną o 5°C powyżej 7000 stóp. |
| Wysokość bezwzględna, nieoperacyjna |-305 metrów do 12 192 metrów (-1000 stóp do 40 000 stóp) |
| Wstrząsy, operacyjne |5g 10 ms 1/2 sinus |
| Wstrząsy, nieoperacyjne |30g 10 ms 1/2 sinus |
| Wibracje, operacyjne |0,21 g RMS 5-500 Hz losowo |
| Wibracje, nieoperacyjne |1,04 g RMS 2-200 Hz losowo |
| Wibracje, relokacja |3g 2-200 Hz sinus |
| Orientacja i montaż |19-eks montuje szafy (2 jednostki EIA) |
| Szyny stelażowe |Aby dopasować stojaki na głębokość co najmniej 700 mm (31,50 cala) zgodne z IEC 297 |
| Bezpieczeństwo i aprobaty |CE i UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| Emc |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Zgodność z międzynarodowymi normami

Urządzenie Microsoft Azure StorSimple spełnia następujące standardy międzynarodowe:  

* CE - EN 60950 - 1
* Raport CB do IEC 60950 - 1
* UL i cUL do UL 60950 - 1

## <a name="safety-compliance"></a>Zgodność z przepisami dotyczącymi bezpieczeństwa

Urządzenie Microsoft Azure StorSimple spełnia następujące wymagania bezpieczeństwa:

* Homologacja typu produktu systemu: UL, cUL, CE
* Zgodność z przepisami bezpieczeństwa: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Zgodność z EMC

Urządzenie Microsoft Azure StorSimple spełnia następujące oceny EMC.

### <a name="emissions"></a>Emisji

Urządzenie jest zgodne z EMC dla poziomów emisji przewodowych i promieniowanych.

* Przeprowadzone poziomy graniczne emisji: CFR 47 Część 15B Klasa A EN55022 Klasa A CISPR klasa A
* Poziomy graniczne emisji promieniowanych: CFR 47 Część 15B Klasa A EN55022 Klasa A CISPR klasa A

### <a name="harmonics-and-flicker"></a>Harmoniczne i migotanie

Urządzenie jest zgodne z en61000-3-2/3.

### <a name="immunity-limit-levels"></a>Poziomy graniczne odporności

Urządzenie jest zgodne z en55024.

## <a name="ac-power-cord-compliance"></a>Zgodność z przewodami zasilających

Wtyczka i kompletny zespół przewodu zasilającego muszą spełniać normy właściwe dla kraju/regionu, w którym urządzenie jest używane, i muszą posiadać homologacje bezpieczeństwa, które są dopuszczalne w tym kraju/regionie. W poniższych tabelach przedstawiono standardy dla USA i Europy.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Przewodami zasilające - USA (musi być nrtl wymienione)

| Składnik | Specyfikacja |
| --- | --- |
| Typ przewodu |SV lub SVT, minimum 18 AWG, 3 przewody, maksymalna długość 2,0 metra |
| Podłącz |NEMA 5-15P uziemiająca wtyczka mocująca o poszepnieniu znamionowa 120 V, 10 A; lub IEC 320 C14, 250 V, 10 A |
| Gniazdo |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Przewodami zasilające - Europa

| Składnik | Specyfikacja |
| --- | --- |
| Typ przewodu |Zharmonizowany, H05-VVF-3G1.0 |
| Gniazdo |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Obsługiwane kable sieciowe

Informacje na temat interfejsów sieciowych 10 GbE, DATA 2 i DATA 3, znajdują się na [liście obsługiwanych kabli i modułów sieciowych.](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

## <a name="next-steps"></a>Następne kroki

Teraz można przystąpić do wdrożenia urządzenia StorSimple w centrum danych. Aby uzyskać więcej informacji, zobacz [Wdrażanie urządzenia lokalnego](storsimple-8000-deployment-walkthrough-u2.md).

