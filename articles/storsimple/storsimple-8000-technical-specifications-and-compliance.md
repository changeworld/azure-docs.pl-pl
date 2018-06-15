---
title: Dane techniczne StorSimple | Dokumentacja firmy Microsoft
description: Opisuje technicznych oraz informacje o zgodności norm dla składników sprzętowych StorSimple.
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
ms.openlocfilehash: 95dbd80e740210c3800a0af10071875a6d6f0939
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2018
ms.locfileid: "27785530"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Dane techniczne i zgodności dla urządzenia StorSimple

## <a name="overview"></a>Przegląd

Składniki sprzętowe urządzenia Microsoft Azure StorSimple stosować się do specyfikacji technicznych i norm opisane w tym artykule. Specyfikacja techniczna opisano zasilania i chłodzenia modułów (PCMs), stacji dysków, pojemności magazynu i obudowy. Informacje o zgodności omówiono czynności, takich jak międzynarodowe standardy, bezpieczeństwa i emisji i okablowanie.

## <a name="power-and-cooling-module-specifications"></a>Specyfikacje zasilania i chłodzenia modułu

Urządzenie StorSimple ma dwa 100 – 240 V podwójną wentylator, zgodne SBB Power chłodzenia modułów (PCMs). Zapewnia to konfiguracji nadmiarowy. W przypadku niepowodzenia PCM urządzenie w dalszym ciągu działać normalnie na inne PCM, dopóki nie zostanie zastąpiony modułu nie powiodło się.

Obudowa EBOD używa 580 W PCM i obudowy głównej używa 764 W PCM. W poniższych tabelach przedstawiono techniczne skojarzone z PCMs.

| Specyfikacja | 580 W PCM (EBOD) | 764 W PCM (podstawowy) |
| --- | --- | --- |
| Maksymalna moc zasilania |580 W |764 |
| Częstotliwość |50/60 Hz |50/60 Hz |
| Wybranego zakresu napięcia |Automatycznie zakresu: V AC 90 — 264, 47/63 Hz |Automatycznie zakresu: 90-264 V AC, 47/63 Hz |
| Bieżąca maksymalna zasypania |20 A |20 A |
| Korekcja współczynnik zasilania |> 95% nominalnego napięcie wejściowe |> 95% nominalnego napięcie wejściowe |
| Składowe harmoniczne |Spełnia EN61000-3-2 |Spełnia EN61000-3-2 |
| Dane wyjściowe |Napięcia wstrzymania 5V @ 2.0 A |Napięcia wstrzymania 5V @ 2.7 A |
| + 5V @ 42 A |+ 5V @ 40 A | |
| + 12V 38 A |+ 12V 38 A | |
| Hot plug |Yes |Yes |
| Przełączniki i LED |Ak lub wyłącz przełącznika i wskaźnik stanu cztery LED |Ak lub wyłącz przełącznika i wskaźnik stanu sześciu LED |
| Obudowa chłodzące |Osiowa chłodzenia wentylatory za pomocą zmiennej wentylator szybkości formantu |Osiowa chłodzenia wentylatory za pomocą zmiennej wentylator szybkości formantu |

## <a name="power-consumption-statistics"></a>Statystyki zużycia energii

W poniższej tabeli wymieniono danymi o zużyciu energii typowe (rzeczywiste wartości mogą się różnić od publikowane) dla różnych modeli urządzenia StorSimple.

| Warunki | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Dyski powoli, wentylatorów bezczynności |WYSOKOŚCI 1,45 A |0.31 kW |1057.76 BTU/godz. |3.19 A |0.34 kW |1160.13 BTU/godz. |
|  Powolna wentylatory, uzyskiwanie dostępu do dysków |1.54 A |0,33 kW |1126.01 BTU/godz. |3.27 A |0.36 kW |1228.37 BTU/godz. |
|  Obsługiwane PSUs dwa wentylatory szybkie, dyski bezczynności, |2.14 A |0.49 kW |1671.95 BTU/godz. |4.99 A |0.54 kW |1842.56 BTU/godz. |
|  Wentylatory szybkie, dyski bezczynny, co zasilania zasilane jedną bezczynności |2.05 A |0.48 kW |1637.83 BTU/godz. |4.58 A |0,50 kW |1706.07 BTU/godz. |
|  Wentylatory dysków szybkie, dostęp do dwóch PSUs zasilania |2.26 A |0,51 kW |1740.19 BTU/godz. |MASIE 4,95 A |0.54 kW |1842.56 BTU/godz. |
|  Szybkie wentylatory, dyski dostęp do, co zasilania zasilane jedną bezczynności |2.14 A |0.49 kW |1671.95 BTU/godz. |4.81 A |0.53 kW |1808.44 BTU/godz. |

## <a name="disk-drive-specifications"></a>Specyfikacje dysku

Urządzenia StorSimple obsługuje maksymalnie 12 dysków Serial Attached SCSI (SAS) współczynnik 3.5 cala formularza. Rzeczywiste dyski mogą być kombinację dysków półprzewodnikowych (SSD) lub dysków twardych (HDD), w zależności od konfiguracji produktu. 12 miejsc dysku znajdują się w konfiguracji 3 na 4 przed obudowa. Obudowa EBOD umożliwia dodatkowy magazyn dla innego 12 dysków twardych. Są one zawsze dysków twardych.

## <a name="storage-specifications"></a>Specyfikacje magazynu

Urządzenia StorSimple mieć kombinację dysków twardych i SSD 8100 i 8600. Łączna pojemność można używać 8100 i 8600 to około 15 TB i 38 TB odpowiednio. Poniższa tabela dokumentów szczegóły dysków SSD, dysk twardy i pojemność w kontekście pojemności rozwiązania StorSimple w chmurze.

| Model urządzenia / pojemności | 8100 | 8600 |
| --- | --- | --- |
| Liczba dysków twardych (HDD) |8 |19 |
| Liczba dysków półprzewodnikowych (SSD) |4 |5 |
| Pojedynczy pojemność dysku twardego |4 TB |4 TB |
| Pojedynczy pojemność dysków SSD |400 GB |800 GB |
| Wolnego miejsca |4 TB |4 TB |
| Można używać pojemność dysku twardego |14 TB |36 TB |
| Można używać pojemność dysków SSD |800 GB |2 TB |
| Całkowita liczba można używać pojemności * |~ 15 TB |~ 38 TB |
| Pojemność rozwiązania maksymalna (w tym chmury) |200 TB |500 TB. |

<sup>* </sup>- *Całkowita pojemność można używać obejmuje pojemności dla danych, metadane i bufory. Można alokować woluminy przypięte lokalnie do 8,5 TB na urządzeniu 8100 lub do 22,5 TB na większym urządzeniu 8600. Aby uzyskać więcej informacji, przejdź do [woluminów przypiętych lokalnie StorSimple](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Obudowa wymiarów i specyfikacje wagi

W poniższej tabeli wymieniono różne specyfikacje obudowa wymiarów i wagi.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli wymieniono Wymiary obudowy w milimetrach i cala.

| Obudowa | Milimetry | Cali |
| --- | --- | --- |
| Wysokość |87.9 |3.46 |
| Szerokość między kołnierza instalowanie |483 |19.02 |
| Szerokość w treści obudowy |443 |17.44 |
| Głębokość montażu do wierzchołka obudowa treści |577 |22.72 |
| Głębokość z panelu Operacje najdalej krawędzi obudowy |630.5 |24.82 |
| Głębokość kołnierza instalowanie najdalej krawędzi obudowy |603 |23.74 |

### <a name="enclosure-weight"></a>Obudowa wagi

W zależności od konfiguracji całkowitym załadowaniem obudowa podstawowego można porównać z 21 do kg 33 i wymaga dwóch osób do jego obsługi.

| Obudowa | Waga |
| --- | --- |
| Maksymalna waga (zależy od konfiguracji) |30 kg — 33 kg |
| Pusta (nie zainstalowane dyski) |21 – kg 23 |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji wymieniono wymagania związane ze środowiskiem obudowy. Temperatury, wilgotności, wysokość, uderzenia, wibrację, orientacji, bezpieczeństwa i zgodności kompatybilność (EMC) znajdują się w tej kategorii.

### <a name="temperature-and-humidity"></a>Temperatury i wilgotności

| Obudowa | Zakres temperatury otoczenia | Wilgotność względna otoczenia | Maksymalna pomysłów wet |
| --- | --- | --- | --- |
| Działa |5-35° C (41° F - 95° F) |20-80% z systemem innym niż-skondensowanie trzech- |28 C (82° F) |
| Nie działa |-C 40-70° 40° F - 158° F |5-100% bez kondensacji |29 C (84° F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Powietrza, wysokość uderzenia, wibrację, orientacji, bezpieczeństwa i EMC

| Obudowa | Specyfikacji operacyjnych |
| --- | --- |
| Powietrza |System powietrza jest przodu do tyłu. System musi działać z instalacją Niskociśnieniowa, spalin tyłu. Wstecz wykorzystania utworzone przez drzwi w stojaku i wąskich gardeł nie może przekraczać paskalach 5 (miernika wody 0,5 mm). |
| Wysokość operacyjne |-30 liczników i 3045 (stopy-100 do 10 000 stopy) maksymalna temperatura cofnąć sklasyfikowane przez 5 C powyżej stopy 7000. |
| Wysokość nie działa |liczniki-305 do liczników 12,192 (-1,000 stopy na stopy 40 000) |
| Uderzenia operacyjne |sinus 10 ms ½ 5 GB/s |
| Uderzenia nie działa |sinus 10 ms ½ 30 GB/s |
| Wibrację operacyjne |0.21g RMS 5 – 500 Hz losowych |
| Wibrację nie działa |1.04g RMS 2 – 200 Hz losowych |
| Wibrację, relokacji |sinus 2 – 200 Hz sieci 3g |
| Orientacja i instalowanie |19" zamontować w stojaku (2 EIA jednostki) |
| Stojaku |Aby dopasować głębokość minimalna 700 mm (31.50 cala) stojakami zgodne z IEC 297 |
| Bezpieczeństwo i zatwierdzenia |CE i UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| FIRMY EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Zgodność ze standardami międzynarodowych

Urządzenia Microsoft Azure StorSimple spełnia następujące normy międzynarodowe:  

* CE - EN 60950-1
* Raport CB IEC 60950-1
* UL i cUL do UL 60950-1

## <a name="safety-compliance"></a>Zgodność bezpieczeństwa

Microsoft Azure StorSimple urządzenie spełnia następujące klasyfikacje bezpieczeństwa:

* Zatwierdzenie typu produktu systemu: UL, cUL, CE
* Zgodność bezpieczeństwa: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Zgodność firmy EMC

Microsoft Azure StorSimple urządzenie spełnia następujące klasyfikacje EMC.

### <a name="emissions"></a>Emisji

Urządzenie jest zgodne EMC poziomów przeprowadzane i WYPROMIENIOWANEJ emisji.

* Przeprowadzone emisji ograniczenia poziomów: CFR 47 część 15B klasy A EN55022 Klasa CISPR klasy A
* Promieniowania ograniczyć poziomów: CFR 47 część 15B klasy A EN55022 Klasa CISPR klasy A

### <a name="harmonics-and-flicker"></a>Składowe harmoniczne i migotania

Urządzenie będzie zgodne z EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Poziomy odporności limit

Urządzenie będzie zgodne z EN55024.

## <a name="ac-power-cord-compliance"></a>Ak zasilania przewód zgodności

Wtyczka i pełny zestaw przewód muszą spełniać normy odpowiednie dla kraju, w którym jest używana urządzenia i mieć zatwierdzenia bezpieczeństwa, które są dozwolone w tym kraju. W poniższych tabelach przedstawiono standardy USA i Europie.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Kable AC - USA (musi być wymienione NRTL)

| Składnik | Specyfikacja |
| --- | --- |
| Typ kabel |Maksymalna długość 2.0 liczników SV lub SVT, 18 minimum Trójprzewodowy, 3 przewodnika |
| Wtyczki |NEMA P 5 – 15 załącznika uziemiający spełnia bardzo typu plug oceną 120 V 10 A; lub IEC 320 C14 250 V, A 10 |
| Gniazda |IEC 320 C-13, 250 V 10 A |

### <a name="ac-power-cords---europe"></a>Kable AC - Europy

| Składnik | Specyfikacja |
| --- | --- |
| Typ kabel |Ujednolicone H05-VVF-3G1.0 |
| Gniazda |IEC 320 C-13, 250 V 10 A |

## <a name="supported-network-cables"></a>Kable sieciowe obsługiwane

10 GbE interfejsy sieciowe dane 2 i dane 3, można znaleźć w [listę obsługiwanych kable i moduły](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do wdrażania urządzenia StorSimple w centrum danych. Aby uzyskać więcej informacji, zobacz [wdrażanie lokalnego urządzenia](storsimple-8000-deployment-walkthrough-u2.md).

