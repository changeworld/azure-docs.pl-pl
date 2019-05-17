---
title: Specyfikacja techniczna StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano informacje dotyczące składników sprzętowych StorSimple zgodności standardowych wymagań prawnych i specyfikacje techniczne.
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
ms.openlocfilehash: 33becd46747f957cce67fb7b2ace170a9aa402ff
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606924"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Przedstawiono dane techniczne i zgodności dla urządzenia StorSimple

## <a name="overview"></a>Omówienie

Składniki sprzętowe w urządzeniu usługa Microsoft Azure StorSimple stosować się do specyfikacji technicznych i standardowych wymagań prawnych opisane w tym artykule. Specyfikacja techniczna opisują zasilania i chłodzenia modułów (PCMs), stacje dysków, pojemności magazynu i obudowy. Informacje o zgodności obejmuje takich zadań jak międzynarodowych standardów bezpieczeństwa i emisji i okablowania.

## <a name="power-and-cooling-module-specifications"></a>Specyfikacje zasilania i chłodzenia modułu

Urządzenie StorSimple ma dwa 100 – 240 V podwójną wentylator, zgodne SBB Power chłodzenie modułów (PCMs). Dzięki temu konfiguracji nadmiarowy. W przypadku niepowodzenia PCM urządzenie w dalszym ciągu działać normalnie w module PCM, dopóki nie jest zastępowana modułu nie powiodło się.

Obudowa EBOD używa 580 W PCM i obudowy głównej używa 764 W PCM. W poniższej tabeli wymieniono skojarzone z PCMs specyfikacje techniczne.

| Specyfikacja | 580 W MODULE PCM (EBOD) | 764 W module PCM (podstawowy) |
| --- | --- | --- |
| Maksymalna moc zasilania |580 W |764 |
| Częstotliwość |50/60 Hz |50/60 Hz |
| Wybór zakresu napięcia |Auto, zakresu: 90-264 V programu AC 47/63 Hz |Auto, zakresu: 90 - 264 V programu AC 47/63 Hz |
| Bieżąca maksymalna zasypania |20 A |20 A |
| Korekta współczynnik zasilania |> nominalna napięcie wejściowe 95% |> nominalna napięcie wejściowe 95% |
| Składowe harmoniczne |Spełnia EN61000-3-2 |Spełnia EN61000-3-2 |
| Dane wyjściowe |Napięcie wstrzymania 5V \@ A w wersji 2.0 |Napięcie wstrzymania 5V \@ A w wersji 2.7 |
| +5V \@ 42 A |+5V \@ 40 A | |
| + 12 V \@ 38 A |+ 12 V \@ 38 A | |
| Hot plug |Tak |Tak |
| Przełączniki i diody LED |Przełącznik/wyłączenie AC i stan czterech diod LED |Przełącznik/wyłączenie AC i stan sześć diod LED |
| Obudowa chłodzenia |Osiowa chłodzenie fanów kontrolką szybkość wentylator zmiennej |Osiowa chłodzenie fanów kontrolką szybkość wentylator zmiennej |

## <a name="power-consumption-statistics"></a>Statystyki zużycia energii

W poniższej tabeli wymieniono typowe power dane o zużyciu (rzeczywiste wartości mogą się różnić od opublikowanego) dla różnych modeli urządzenia StorSimple.

| Warunki | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Dyski powoli, wentylatorów bezczynności |WYSOKOŚCI 1,45 A |0.31 kW |1057.76 BTU/hr |3.19 A |0,34 kW |1160.13 BTU/hr |
|  Powolne fanów, uzyskiwanie dostępu do dysków |1.54 A |0,33 kW |1126.01 BTU/hr |3.27 A |0.36 kW |1228.37 BTU/hr |
|  Obsługiwane dwa PSUs fanów szybkiego działania, dyski bezczynności, |2.14 A |0.49 kW |1671.95 BTU/godz. |4.99 A |0.54 kW |1842.56 BTU/godz. |
|  Fanów szybkiego działania, dyski bezczynności, obsługiwane zasilania jednego, jeden w stanie bezczynności |2.05 A |0.48 kW |1637.83 BTU/godz. |4.58 A |0,50 kW |1706.07 BTU/hr |
|  Dyski szybkiego działania, fanów, dostęp do dwóch PSUs zasilania |2.26 A |0.51 kW |1740.19 BTU/hr |4.95 A |0.54 kW |1842.56 BTU/godz. |
|  Szybkie wentylatory, dyski, uzyskując dostęp do, zasilania jednego obsługiwane jeden w stanie bezczynności |2.14 A |0.49 kW |1671.95 BTU/godz. |4.81 A |0.53 kW |1808.44 BTU/hr |

## <a name="disk-drive-specifications"></a>Specyfikacje dysku

Urządzenie StorSimple obsługuje maksymalnie 12 dysków Serial Attached SCSI (SAS) współczynnik 3,5 cala formularza. Rzeczywiste dyski mogą być mieszane dysków półprzewodnikowych (SSD) lub dysków twardych (HDD), w zależności od konfiguracji produktu. 12 gniazda dysku znajdują się w konfiguracji 3, 4, przed obudowa. Obudowa EBOD umożliwia dodatkowy magazyn dla innego 12 dysków twardych. Są one zawsze dysków twardych.

## <a name="storage-specifications"></a>Specyfikacje magazynu

Urządzenia StorSimple mają różne dyski twarde i półprzewodnikowe dyski dla 8100 i 8600. Łączna liczba pojemności do wykorzystania dla 8100 i 8600 są około 15 TB i 38 TB. Poniższa tabela dokumenty szczegółowe informacje o dysku SSD, HDD i pojemność w kontekście pojemności rozwiązania StorSimple w chmurze.

| Model urządzenia / pojemności | 8100 | 8600 |
| --- | --- | --- |
| Liczba dysków twardych (HDD) |8 |19 |
| Liczba dysków półprzewodnikowych (SSD) |4 |5 |
| Pojedynczy pojemność dysku twardego |4 TB |4 TB |
| Pojedynczy pojemność dysków SSD |400 GB |800 GB |
| Wolnego miejsca |4 TB |4 TB |
| Pojemności do wykorzystania dysku twardego |14 TB |36 TB |
| Można używać pojemność dysków SSD |800 GB |2 TB |
| Łączna liczba użyteczne pojemności * |~ 15 TB |~ 38 TB |
| Rozwiązanie maksymalną pojemność (w tym chmury) |200 TB |500 TB |

<sup>* </sup>- *Łączna liczba pojemności do wykorzystania obejmuje dostępnej pojemności danych, metadane i bufory. Można alokować woluminy przypięte lokalnie do 8,5 TB na urządzeniu 8100 lub do 22,5 TB na większym urządzeniu 8600. Aby uzyskać więcej informacji, przejdź do [StorSimple lokalnie przypięte woluminy](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższej tabeli wymieniono różne specyfikacje obudowy do wymiarów i wagi.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli wymieniono Wymiary obudowy milimetry i cala.

| Obudowy | Milimetry | Cala |
| --- | --- | --- |
| Wysokość |87.9 |3.46 |
| Szerokość na listwie instalowania |483 |19.02 |
| Szerokość w treści obudowy |443 |17.44 |
| Głębokość montażu do wierzchołka treści obudowy |577 |22.72 |
| Szczegółowe informacje z panelu Operacje dotyczące najdalszego krawędzi obudowy |630.5 |24.82 |
| Głębokość kołnierza instalowania najdalszego krawędzi obudowy |603 |23.74 |

### <a name="enclosure-weight"></a>Waga obudowy

W zależności od konfiguracji załadowaniem obudowy podstawowego można porównać od 21 do 33 kg i wymaga dwóch osób do jego obsługi.

| Obudowy | Waga |
| --- | --- |
| Waga maksymalna (w zależności od konfiguracji) |30 kg — 33 kg |
| Pusty (nie zainstalowane dyski) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

Ta sekcja zawiera specyfikacje dotyczące środowiska obudowy. Temperatury, wilgotności, wysokość, próbuj wstrząsnąć, wibracje, orientacji, bezpieczeństwa i zgodności elektromagnetyczna (EMC) znajdują się w tej kategorii.

### <a name="temperature-and-humidity"></a>Temperatury i wilgotności

| Obudowy | Zakres temperatury otoczenia | Wilgotność względna otoczenia | Maksymalna zbiornika mokrą |
| --- | --- | --- | --- |
| Działa |5°C - 35°C(41°F - 95°F) |20-80% non-skondensowanie trzech- |28°C (82°F) |
| Nie działa |-40°C - 70°C(40°F - 158°F) |5 – 100% bez kondensacji |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Powietrza, wysokość, próbuj wstrząsnąć, wibracje, orientacji, bezpieczeństwa i EMC

| Obudowy | Specyfikacje operacyjne |
| --- | --- |
| Powietrza |System powietrza jest przodu do tyłu. System musi działać z instalacją Niskociśnieniowa, spalin tyłu. Ciśnienie utworzonych przez drzwi stojaku oraz pokonać przeszkody i nie powinna przekraczać paskalach 5 (0,5 mm wody miernika). |
| Wysokość, operacyjne |-30 i maksymalna temperatura cofnąć oceniane przez 5 C ponad 7000 metrów 3045 liczników (metrów-100 do 10 000 stóp). |
| Wysokość, nie działa |liczniki-305 do 12,192 liczników (-1,000 metrów do 40 000 stóp) |
| Próbuj wstrząsnąć operacyjne |sinus 10 ms ½ 5g |
| Próbuj wstrząsnąć niedziałającą |sinus 10 ms ½ 30g |
| Wibracje operacyjne |0.21g RMS 5 – 500 Hz losowe |
| Wibracje niedziałającą |1.04g RMS 2 – 200 Hz losowe |
| Wibracje, relokacji |sinus 2 – 200 Hz 3g |
| Orientacja i instalowanie |19" zamontować w stojaku (2 EIA jednostki) |
| Stojaku |Aby dopasować głębokość minimalne 700 mm (w calach 31.50) stojakami zgodne z IEC 297 |
| Bezpieczeństwo i zatwierdzeń |CE i UL EN 61000-3, IEC 61000-3, 61000 3 UL |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Zgodność ze standardami międzynarodowymi

Urządzenia usługi Microsoft Azure StorSimple spełnia następujące standardy międzynarodowe:  

* CE - EN 60950-1
* Raport CB IEC 60950-1
* UL i cUL do UL 60950-1

## <a name="safety-compliance"></a>Bezpieczeństwo, zgodność

Urządzenie sieci Microsoft Azure StorSimple spełnia następujące oceny bezpieczeństwa:

* Zatwierdzenie typ produktu systemu: UL, cUL, CE
* Zgodność bezpieczeństwa: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Zgodność firmy EMC

Urządzenie sieci Microsoft Azure StorSimple spełnia poniższe klasyfikacje EMC.

### <a name="emissions"></a>Emisji

Urządzenie jest zgodne firmy EMC dla poziomów przeprowadzane i WYPROMIENIOWANEJ emisji.

* Przeprowadzać emisji limit poziomy: CFR 47 Part 15B klasy A EN55022 klasy A CISPR klasy A
* Promieniowania emisji limit poziomy: CFR 47 Part 15B klasy A EN55022 klasy A CISPR klasy A

### <a name="harmonics-and-flicker"></a>Składowe harmoniczne i migotania

Urządzenie jest zgodna z EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Poziomy limit odporności

Urządzenie jest zgodna z EN55024.

## <a name="ac-power-cord-compliance"></a>Zgodność przewód zasilania programu AC

Wtyczka i pełny zestaw przewód muszą spełniać normy odpowiednich dla kraju/regionu, w którym urządzenie jest używane, i mieć pozwolenia związane z bezpieczeństwem, które są akceptowane w tym kraju/regionu. W poniższej tabeli wymieniono standardów dla USA i Europy.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Kable AC — Stany Zjednoczone (musi być NRTL na liście)

| Składnik | Specyfikacja |
| --- | --- |
| Typ kabel |Maksymalna długość 2.0 liczniki SV lub SVT, 18 minimum Trójprzewodowy, przewodowe 3 |
| Wtyczki |NEMA 5 – 15P załącznika uziemiający spełnia bardzo typu plug-in oceniane 120 V 10 A; lub IEC 320 C14 250 V, A 10 |
| Gniazda |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Kable AC - Europa

| Składnik | Specyfikacja |
| --- | --- |
| Typ kabel |Harmonized, H05-VVF-3G1.0 |
| Gniazda |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Kable sieciowe obsługiwane

Dla interfejsów sieciowych 10 GbE dane 2 i DATA 3 dotyczą [listę obsługiwanych kable i moduły](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do wdrażania urządzenia StorSimple w centrum danych. Aby uzyskać więcej informacji, zobacz [wdrażanie lokalnego urządzenia](storsimple-8000-deployment-walkthrough-u2.md).

