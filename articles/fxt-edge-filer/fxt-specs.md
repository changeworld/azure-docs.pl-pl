---
title: Microsoft Azure FXT Edge Filer specyfikacje | Dokumenty firmy Microsoft
description: Specyfikacje fizyczne i środowiskowe dla sprzętu Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c06b0c79e01257eebf566b9752269cb88c072d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264728"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specyfikacje usługi Azure FXT Edge Filer

W tym artykule opisano specyfikacje sprzętowe dla węzłów sprzętowych usługi Azure FXT Edge Filer. W praktyce trzy lub więcej węzłów są skonfigurowane razem w celu zapewnienia systemu klastrowanej pamięci podręcznej.

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Składnik | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Rdzenie procesora CPU |  16 | 16 |
| Dram  | 1536 GB | 768 GB |
| Porty sieciowe | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| Pojemność NVMe SSD | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specyfikacja dysku

System posiada dziesięć wnęk dyskowych, dostępnych od przodu. Każdy wypełniony dysk jest oznaczony po prawej stronie informacjami o pojemności. 

Numery dysków są drukowane na przestrzeni między dyskami. W usłudze Azure FXT Edge Filer dysk 0 jest lewym górnym dyskiem, a dysk 1 znajduje się bezpośrednio pod nim.

![zdjęcie jednej wnęki na dysk twardy w obudowie FXT, przedstawiające numery dysków i etykiety pojemności](media/fxt-drives-photo.png)

| Numery dysków    |  Użycie   |  Specyfikacje |
|------------------|--------|-----------------|
| 0, 1             | System operacyjny     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Dane   | FXT 6600: dysk SSD NVMe o pojemności 3,2 TB <br> FXT 6400: dysk SSD NVMe o pojemności 1,6 TB |


## <a name="dimensions-and-weight"></a>Wymiary i waga

Azure FXT Edge Filer został zaprojektowany tak, aby zmieścić się w standardowym 19-calowym stelażu i jest o wysokości jednego stelaża (1U). 

<!-- 10x2.5 inches version -->

| Wymiary filera           |                          |
|-----------------------------|--------------------------|
| Właściwość Height                      | 42,8 mm (1,68 cala)    |
| Szerokość (z uszami stelaża) | 482,0 mm (18,97 cala)  |
| Szerokość - obudowa główna      | 434,0 mm (17,08 cala) |
| Głębokość - uszy stojaka do tyłu głównej obudowy                   | 733,82 mm (29,61 cala) |
| Głębokość - uszy stojaka do najdalej tylnego występu                 | 772,67 mm (30,42 cala) |
| Głębokość - uszy stojaka do najdalej przedniego występu, bez ramki | 22,0 mm (0,87 cala)  |
| Głębokość - uszy stojaka do najdalej przedniego występu, z ramką    | 35,84 mm (1,41 cala) |

| Waga | |
|-----------------|----------------------|
| Masa węzła (bez opakowania, bez akcesoriów) | 40 funtów (18,1 kg) |
| Waga netto (bez opakowania, w tym akcesoria) | 51 funtów (23,1 kg)|
| Waga brutto (jako wysłane, zawiera wszystkie opakowania) |  64 funty (29,0 kg) |

### <a name="shipping-dimensions"></a>Wymiary wysyłki

| Wymiar pakietu | Milimetrach | Cali |
|-------------------|-------------|--------|
| Właściwość Height            | 311.2       | 12.25" |
| impulsów             | 642.8       | 25.31" |
| Długość            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Specyfikacje zasilania i termiczne

Ta sekcja zawiera oceny mocy i pomiary dla usługi Azure FXT Edge Filer.

### <a name="nameplate-ratings"></a>Oceny tabliczki znamionowej

| Oceny tabliczki znamionowej dla modeli z serii FXT 6000 |
|----------------|
| 100 - 240V~    |
| 10A - 5A (X2)  |
| 50/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Pomiary mocy i ciepła 

Węzły usługi Azure FXT Edge Filer używają wentylatorów o zmiennej prędkości, więc moc zależy od temperatury i obciążenia. Maksymalne prędkości wentylatorów można osiągnąć przy pewnych kombinacjach wysokiego obciążenia i podwyższonych temperatur otoczenia. 

Wykresy te zapewniają zużycie energii i pomiary mocy cieplnej dla powszechnie używanych kombinacji napięcia i częstotliwości. 

| FxT 6600 moc w temperaturze pokojowej <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 | 
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Prąd (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Moc pozorna (VA) | 502 | 499 | 499 | 506 | 518|
| Współczynnik mocy | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Prawdziwa moc (W) | 497 |494 | 489 | 496 | 508 |
| Rozpraszanie termiczne (BTU/Godz)Thermal Dissipation (BTU/Hr) |1696 | 1686 | 1669 | 1692 | 1733 |

| FxT 6600 przy maksymalnej prędkości wentylatora | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napięcie (V) | 100 |120 | 208 | 230 | 240| 
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Prąd (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Moc pozorna (VA) | 598 | 601 | 584 | 587 | 595 |
| Współczynnik mocy | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Prawdziwa moc (W) | 592 | 595 | 573 | 575 | 583 |
| Rozpraszanie termiczne (BTU/Godz)Thermal Dissipation (BTU/Hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| FxT 6400 moc w temperaturze pokojowej <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) |60 | 60 | 60 | 50 | 50 |
| Prąd (A) | 4,63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Moc pozorna (VA) | 463 | 463 | 466 | 469 | 466 |
| Współczynnik mocy | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Prawdziwa moc (W) | 458 | 459 | 457 | 460 | 456 |
| Rozpraszanie termiczne (BTU/Godz)Thermal Dissipation (BTU/Hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FxT 6400 przy maksymalnej prędkości wentylatora | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Prąd (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Moc pozorna (VA) | 515 | 514 | 516 | 524 | 511 |
| Współczynnik mocy | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Prawdziwa moc (W) | 510 | 508 | 506 | 514 | 501 |
| Rozpraszanie termiczne (BTU/Godz)Thermal Dissipation (BTU/Hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Wymogi środowiskowe

W tej sekcji podano specyfikacje środowiska otoczenia sprzętu.

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

| Atrybut środowiskowy   | Zakres działania                   | Zakres pracy niezwiązanych z obsługą         |
|---------------------------|-----------------------------------|-----------------------------|
| Zakres temperatur otoczenia | Od 10°C do 35°C          | Od -40°C do 65°C (-40 - 149°F) |
| Wilgotność względna otoczenia | 10% - 80% bez kondensacji          | 5% - 95% bez kondensacji     |
| Maksymalny punkt rosy         | 29°C (84°F)                       | 33°C (91°F)                 |
| Wysokości                  | do 3048 metrów (10 000 stóp), z zastrzeżeniem odsyłania temperatury | do 12 000 metrów |

> [!NOTE] 
> **Obniżenie temperatury wysokości:** Temperatura maksymalna jest obniżona o 1°C(1°F/547 ft) powyżej 950 m (3,117 ft).

### <a name="airflow-shock-and-vibration"></a>Przepływ powietrza, wstrząsy i wibracje 

| Atrybut         | Specyfikacja |
|-------------------|---------------|
| Przepływ powietrza                    | Przepływ powietrza systemowego jest od przodu do tyłu. System musi być obsługiwany przy niskociśnieniowej tylnej instalacji wydechowej. |
| Wstrząsy, operacyjne         | 6 G przez 11 milisekund (testowane w 6 orientacjach) |
| Wstrząsy, nieoperacyjne     | 71 G przez 2 milisekundy (testowane w 6 orientacjach) |
| Wibracje, operacyjne     | Losowe 0,26 G<sub>RMS</sub> 5 Hz do 350 Hz         |
| Wibracje, nieoperacyjne | 1,88 G<sub>RMS</sub> 10 Hz do 500 Hz przez 15 minut (wszystkie sześć testowanych stron)  |

## <a name="safety-regulation-compliance"></a>Zgodność z przepisami bezpieczeństwa 

Usługa Azure FXT Edge Filer jest zgodna z wymienionymi przepisami. 

| Kategoria       | Specyfikacja regulacyjna | 
|----------------|--------------------------|
| Ogólne bezpieczeństwo | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| Emc            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energetyczna         | Rozporządzenie Komisji (UE) nr 617/2013  |
| Rohs           |    EN 50581:2012   |