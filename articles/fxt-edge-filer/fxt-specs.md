---
title: Microsoft Azure FXT krawędzi filtr specyfikacje | Dokumentacja firmy Microsoft
description: Fizyczna i środowiska specyfikacje sprzętowe filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0679bce8eae515aa6b90e34fcfd15ee9b4e56b31
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542887"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Usługa Azure specyfikacje filtr Edge FXT

W tym artykule opisano wymagania dotyczące sprzętu dla węzłów sprzętu filtr Edge FXT platformy Azure. W praktyce co najmniej trzema węzłami skonfigurowano razem stanowią system klastra pamięci podręcznej.

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Składnik | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Rdzenie procesora CPU |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Porty sieciowe | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Pojemność dysków SSD NVMe | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specyfikację dysku

System ma dziesięć kieszenie, dostępna z przodu. Każdy dysk wypełnione nazywa się po prawej stronie z informacjami o pojemności. 

Numery dysków są drukowane na miejsce między dysków. W filtr Edge FXT platformy Azure dysk 0 jest u góry z lewej stacji, a dysk 1 jest bezpośrednio poniżej.

![zdjęcie jeden dysk twardy wnękami w obudowie FXT wyświetlane etykiety liczb i pojemność dysku](media/fxt-drives-photo.png)

| Numery dysków    |  Użycie   |  Specyfikacje |
|------------------|--------|-----------------|
| 0, 1             | OS     | DYSKI SSD SATA 480 GB |
| 2, 3, 4, 5, 6, 7, 8, 9 | Data   | FXT 6600: 3.2 TB NVMe dysków SSD <br> FXT 6400: W wersji 1.6 TB NVMe dysków SSD |


## <a name="dimensions-and-weight"></a>Wymiary i wagę rekordu

Filtr Edge FXT Azure zaprojektowano w celu dopasowania w stojaku sprzętu standardowa 19" i jednym stojaku jednostka wysokiej (1U). 

<!-- 10x2.5 inches version -->

| Wymiary filtr           |                          |
|-----------------------------|--------------------------|
| Wysokość                      | 42.8 mm (1.68 cala)    |
| Szerokość (w tym uszy rack) | 482.0 mm (18.97 cala)  |
| Szerokość — obudowa główna      | 434.0 mm (17.08 cala) |
| Głębokość - uszy stojak na tylnej stronie obudowa główna                   | 733.82 mm (29.61 cala) |
| Głębokość - uszy stojak się najdalej perforacji tylnej                 | 772.67 mm (30.42 cala) |
| Głębokość - rack uszy się najdalej perforacji frontonu, bez osłony | 22.0 mm (0.87 cala)  |
| Głębokość - rack uszy się najdalej perforacji frontonu, z ramką    | 35.84 mm (1.41 cala) |

| Waga | |
|-----------------|----------------------|
| Waga węzła (bez opakowania, bez Akcesoria) | 40 modułów równoważenia obciążenia (18.1 kg) |
| Waga netto (bez opakowania, włącznie z) | 51 modułów równoważenia obciążenia (23.1 kg)|
| Waga brutto (tak jak wysłane, obejmuje wszystkie opakowania) |  64 modułów równoważenia obciążenia (29.0 kg) |

### <a name="shipping-dimensions"></a>Wymiary wysyłki

| Wymiar pakietu | Milimetry | Cala |
|-------------------|-------------|--------|
| Wysokość            | 311.2       | 12.25" |
| Szerokość             | 642.8       | 25.31" |
| Długość            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Możliwości i specyfikacje termiczny

W tej sekcji przedstawiono power ocen i pomiary dla filtr Edge FXT platformy Azure.

### <a name="nameplate-ratings"></a>Tablica umieszczona klasyfikacji

| Tablica umieszczona klasyfikację dla modeli szeregów FXT 6000 |
|----------------|
| 100 - 240 ~    |
| 10A - 5A (X 2)  |
| 50 / 60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Możliwości i pomiary termiczny 

Filtr Edge FXT węzły na platformie Azure użyj zmiennej szybkości wentylatory, więc power zależy od temperatury i obciążenia. Wentylator maksymalną szybkość można skontaktować, niektóre kombinacje dużym obciążeniem i temperatury otoczenia z podwyższonym poziomem uprawnień. 

Te wykresy zapewniają zużycie energii i pomiary termiczny danych wyjściowych dla często używanych częstotliwość napięcia kombinacji. 

| Power FXT 6600 w temperaturze otoczenia <br />(22 C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 | 
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Bieżąca (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Jawnego zasilania (oceny luk w zabezpieczeniach) | 502 | 499 | 499 | 506 | 518|
| Współczynnik zasilania | 0,99. | 0,99. |0.98 | 0.98 | 0.98 |
| Rzeczywista siła (W) | 497 |494 | 489 | 496 | 508 |
| Rozpraszanie (BTU/godz.) |1696 | 1686 | 1669 | 1692 | 1733 |

| Power FXT 6600 prędkości maksymalnej wentylatora | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napięcie (V) | 100 |120 | 208 | 230 | 240| 
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Bieżąca (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Jawnego zasilania (oceny luk w zabezpieczeniach) | 598 | 601 | 584 | 587 | 595 |
| Współczynnik zasilania | 0,99. | 0,99. | 0.98 | 0.98 | 0.98 |
| Rzeczywista siła (W) | 592 | 595 | 573 | 575 | 583 |
| Rozpraszanie (BTU/godz.) | 2020 |2031 | 1954 | 1961 | 1990 |

| Power FXT 6400 w temperaturze otoczenia <br />(22 C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) |60 | 60 | 60 | 50 | 50 |
| Bieżąca (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Jawnego zasilania (oceny luk w zabezpieczeniach) | 463 | 463 | 466 | 469 | 466 |
| Współczynnik zasilania | 0,99. | 0,99. | 0.98 | 0.98 | 0.98 | 
| Rzeczywista siła (W) | 458 | 459 | 457 | 460 | 456 |
| Rozpraszanie (BTU/godz.) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Power FXT 6400 prędkości maksymalnej wentylatora | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Bieżąca (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Jawnego zasilania (oceny luk w zabezpieczeniach) | 515 | 514 | 516 | 524 | 511 |
| Współczynnik zasilania | 0,99. | 0,99. | 0.98 | 0.98 | 0.98 |
| Rzeczywista siła (W) | 510 | 508 | 506 | 514 | 501 |
| Rozpraszanie (BTU/godz.) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Wymagania dotyczące środowiska

W tej sekcji przedstawiono wymagania dotyczące sprzętu otaczającego środowiska.

### <a name="temperature-and-humidity"></a>Temperatury i wilgotności

| Atrybut środowiska   | Zakres działania                   | Operacyjnego spoza zakresu         |
|---------------------------|-----------------------------------|-----------------------------|
| Zakres temperatury otoczenia | 10 C 35 c (50-86° F)          | C-40 do 65 C (-40-149 ° F) |
| Wilgotność względna otoczenia | 10-80% bez kondensacji          | 5 – 95% bez kondensacji     |
| Maksymalny punkt Rosa         | 29°C (84°F)                       | 33°C (91°F)                 |
| Altitude                  | maksymalnie 3048 liczniki (w 10 000 stóp) podlegają temperatury cofnąć ocena wskazanych poniżej | liczniki maksymalnie 12 000 (39,370 metrów) |

> [!NOTE] 
> **Wysokość temperatury cofnąć ocena:** Maksymalna temperatura jest ograniczona przez 1 C/300 m 1° F/547 awaryjnej powyżej 950 m (3,117 ft).

### <a name="airflow-shock-and-vibration"></a>Powietrza wstrząsy i wibracje 

| Atrybut         | Specyfikacja |
|-------------------|---------------|
| Powietrza                    | System powietrza jest przodu do tyłu. System musi działać z instalacją Niskociśnieniowa spalin tylnej. |
| Próbuj wstrząsnąć operacyjne         | G 6 do 11 MS (przetestowane w orientacji 6) |
| Próbuj wstrząsnąć niedziałającą     | 71 G przez 2 milisekund (przetestowane w orientacji 6) |
| Wibracje operacyjne     | G 0,26<sub>RMS</sub> częstotliwości 5 – 350 Hz losowe         |
| Wibracje niedziałającą | Wzrostu 1,88 G<sub>RMS</sub> częstotliwości 10 – 500 Hz przez 15 minut (wszystkie sześć strony przetestowane)  |

## <a name="safety-regulation-compliance"></a>Zgodność rozporządzenie bezpieczeństwa 

Azure filtr Edge FXT jest zgodna z regulacjami uwzględnione na liście. 

| Category       | Specyfikacja prawnych | 
|----------------|--------------------------|
| Ogólne bezpieczeństwo | EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011 / IEC 60950-1:2005 ed2 + A1:2009 + A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (Klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | Nie Komisja rozporządzenie (UE). 617/2013  |
| RoHS           |    EN 50581:2012   |