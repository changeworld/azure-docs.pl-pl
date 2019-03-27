---
title: Zgodność i specyfikacje techniczne krawędź pola danych platformy Azure firmy Microsoft | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zgodności dla krawędzi pola danych sieci platformy Azure i specyfikacje techniczne
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449201"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Usługa Azure specyfikacji technicznej krawędź pola danych

Składniki sprzętowe w urządzeniu pole danych platformy Azure przeglądarki Microsoft Edge stosować się do specyfikacji technicznych i standardowych wymagań prawnych opisane w tym artykule. Specyfikacja techniczna opisują jednostki dostaw zasilania (PSUs), pojemność pamięci masowej, obudów i standardów środowiska. 

## <a name="power-supply-unit-specifications"></a>Specyfikacje jednostki dostaw energii

Urządzenie brzegowe pole danych ma dwa 100 – 240 V dostaw jednostki zasilania (PSUs) z fanów o wysokiej wydajności. Dwa PSUs zapewniają konfiguracji nadmiarowy. W przypadku awarii zasilania urządzenie w dalszym ciągu działać normalnie w innych zasilania, dopóki nie jest zastępowana modułu nie powiodło się. W poniższej tabeli wymieniono PSUs specyfikacji technicznej.

| Specyfikacja           | 750 ZASILANIA W                  |
|-------------------------|----------------------------|
| Maksymalna moc zasilania    | 750 W                     |
| Częstotliwość               | 50/60 Hz                   |
| Wybór zakresu napięcia | Auto, zakresu: 100-240 V AC |
| Hot plug           | Yes                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Specyfikacje magazynu

Urządzenia brzegowe pole danych ma 10 X 2.5" NVMe dysków SSD, każdy o pojemności do wersji 1.6 TB. Tych dysków SSD, 2 są dyski systemu operacyjnego i innych 8 dysków z danymi. Łączna liczba pojemności do wykorzystania dla urządzenia to około 12,5 TB. Poniższa tabela zawiera szczegóły na pojemność pamięci masowej urządzenia.

|     Specyfikacja                          |     Wartość             |
|--------------------------------------------|-----------------------|
|    Liczba dysków półprzewodnikowych (SSD)     |    8                  |
|    Pojedynczy pojemność dysków SSD                     |    1,6 TB             |
|    Całkowita pojemność                          |    12,8 TB            |
|    Łączna liczba użyteczne pojemności *                  |    ~ 12,5 TB            |

**Niektóre miejsca jest zarezerwowana do użytku wewnętrznego.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższej tabeli wymieniono różne specyfikacje obudowy do wymiarów i wagi.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli wymieniono Wymiary obudowy milimetry i cala.

|     Obudowy     |     Milimetry     |     Cala     |
|-------------------|---------------------|----------------|
|    Wysokość         |    44.45            |    1.75"          |
|    Szerokość          |    434.1           |    17.09"          |
|    Długość          |    740.4           |    29.15"          |

W poniższej tabeli wymieniono wymiary pakiet wysyłki w milimetrach i cali.

|     Pakiet     |     Milimetry     |     Cala     |
|-------------------|---------------------|----------------|
|    Wysokość         |    311.2            |    12.25"          |
|    Szerokość          |    642.8          |    25.31"          |
|    Długość          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Waga obudowy

Pakiet urządzenia zadowalająco uwzględni wagi 66 modułów równoważenia obciążenia. wymaga dwóch osób do jego obsługi. Waga urządzenia zależy od konfiguracji obudowa.

|     Obudowy                                 |     Waga          |
|-----------------------------------------------|---------------------|
|    Łączna waga tym pakowanie       |    61 modułów równoważenia obciążenia.          |
|    Waga urządzenia                       |    35 modułów równoważenia obciążenia.          |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

Ta sekcja zawiera specyfikacje dotyczące środowiska obudowy, takich jak temperatury i wilgotności, wysokość.

### <a name="temperature-and-humidity"></a>Temperatury i wilgotności

|     Obudowy         |     Zakres temperatury otoczenia     |     Wilgotność względna otoczenia     |     Maksymalny punkt Rosa     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Działa        |    10 C - 35 C (50° F - 86° F)         |    10-80% bez kondensacji.         |    29°C (84°F)            |
|    Nie działa    |    C-40 do 65 C (-40 ° F - 149 ° F)     |    5 – 95% bez kondensacji.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Powietrza, wysokość, próbuj wstrząsnąć, wibracje, orientacji, bezpieczeństwa i EMC

|     Obudowy                           |     Specyfikacje operacyjne                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Powietrza                              |    System powietrza jest przodu do tyłu. System musi działać z instalacją Niskociśnieniowa, spalin tyłu. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maksymalna wysokość operacyjne        |    3048 liczników (10 000 stóp) i maksymalna temperatura cofnąć oceniane ustalany na podstawie [temperatura cofnąć ocena specyfikacje](#operating-temperature-de-rating-specifications).                                                                                |
|    Maksymalna wysokość niedziałającą    |    liczniki 12 000 (39,370 metrów)                                                                                                                                                                                         |
|    Próbuj wstrząsnąć operacyjne                   |    G 6 do 11 milisekund orientacje 6                                                                                                                                                                         |
|    Próbuj wstrząsnąć niedziałającą               |    71 G dla 2 milisekund orientacje 6                                                                                                                                                                           |
|    Wibracje operacyjne               |    G 0,26<sub>RMS</sub> częstotliwości 5 – 350 Hz losowe                                                                                                                                                                                     |
|    Wibracje niedziałającą           |    Wzrostu 1,88 G<sub>RMS</sub> częstotliwości 10 – 500 Hz przez 15 minut (wszystkie sześć strony testowane.)                                                                                                                                                  |
|    Orientacja i instalowanie             |    19" zamontować w stojaku                                                                                                                                                                                        |
|    Bezpieczeństwo i zatwierdzeń                 |    EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011 / IEC 60950-1:2005 ed2 + A1:2009 + 62311:2008 A2:2013 EN                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (Klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Nie Komisja rozporządzenie (UE). 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Temperatura cofnąć ocena specyfikacji

|     Operacyjne temperatury bez oceny     |     Zakres temperatury otoczenia                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Maksymalnie 35 C (95 F)                       |    Maksymalna temperatura jest ograniczona przez 1 C/300 m 1° F/547 awaryjnej powyżej 950 m (3,117 ft).    |
|    35 C-40 C (95° F do 104° F)            |    Maksymalna temperatura jest ograniczona przez 1 C 175 minutę (1° F/319 ft) powyżej 950 m (3,117 ft).    |
|    40 C do 45 C (104° F do 113° F)           |    Maksymalna temperatura jest ograniczona przez 1 C/125 m 1° F/228 awaryjnej powyżej 950 m (3,117 ft).    |


## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie na krawędzi sieci usługi Azure Data Box](data-box-edge-deploy-prep.md)
