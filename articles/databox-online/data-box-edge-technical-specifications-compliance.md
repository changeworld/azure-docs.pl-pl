---
title: Microsoft Azure Data Box Edge specyfikacje techniczne i zgodność | Microsoft Docs
description: Poznaj specyfikacje techniczne i zgodność Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 365507f482217bd804bbd5017d4dbbaf8c187ad2
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326895"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge specyfikacje techniczne

Składniki sprzętowe Microsoft Azure Data Box Edge urządzeniu przestrzegają specyfikacji technicznych i standardów obowiązujących w tym artykule. Specyfikacje techniczne opisują jednostki zasilacza (PSUs), pojemność magazynu, obudowy i standardy środowiskowe. 

## <a name="compute-memory-specifications"></a>Obliczenia, specyfikacje pamięci

Urządzenie Data Box Edge ma następujące specyfikacje dotyczące obliczeń i pamięci:

| Określając           | Value                  |
|-------------------------|----------------------------|
| Procesor CPU    | 2 X 10 rdzeni procesora CPU                     |
| Memory (Pamięć)              | 128 GB pamięci RAM                  |


## <a name="fpga-specifications"></a>Specyfikacje FPGA

FPGA jest dołączana do wszystkich urządzeń Data Box Edge, które umożliwiają Machine Learning (ML) scenariusze. 

| Określając           | Value                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Dostępne modele sieci neuronowych Network (DNN) są takie same, jak te [obsługiwane przez wystąpienia FPGA chmury](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Specyfikacje jednostek zasilacza

Urządzenie Data Box Edge ma dwie jednostki dostarczania 100-240 V (PSUs) z wentylatorami o wysokiej wydajności. Dwa PSUs zapewniają nadmiarową konfigurację zasilacza. Jeśli PSU nie powiedzie się, urządzenie będzie nadal działać normalnie na innych PSU, dopóki nie zostanie zastąpiony moduł zakończony niepowodzeniem. W poniższej tabeli przedstawiono specyfikacje techniczne PSUs.

| Określając           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maksymalna moc wyjściowa    | 750 W                     |
| Częstotliwość               | 50/60 Hz                   |
| Wybór zakresu napięcia | Wybór z autozakresem: 100-240 V AC |
| Możliwość podłączenia gorąca           | Tak                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Specyfikacje magazynu

Data Box Edge urządzenia mają 9 X 2,5 "interfejsu NVMe dysków SSD, każdy z pojemnością 1,6 TB. Z tych dysków SSD 1 to dysk systemu operacyjnego, a drugi 8 to dyski z danymi. Całkowita pojemność urządzenia wynosi około 12,5 TB. Poniższa tabela zawiera szczegółowe informacje dotyczące pojemności magazynu urządzenia.

|     Określając                          |     Value             |
|--------------------------------------------|-----------------------|
|    Liczba dysków półprzewodnikowych (dysków SSD)     |    8                  |
|    Pojemność jednego dysku SSD                     |    1,6 TB             |
|    Całkowita pojemność                          |    12,8 TB            |
|    Łączna pojemność użyteczna *                  |    ~ 12,5 TB            |

**Niektóre miejsca są zarezerwowane do użytku wewnętrznego.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wag.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli przedstawiono wymiary obudowy w milimetrach i calach.

|     Obudowa     |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Wysokość         |    44,45            |    1,75 "          |
|    Szerokość          |    434,1           |    17,09 "          |
|    Długość          |    740,4           |    29,15 "          |

W poniższej tabeli przedstawiono wymiary pakietu wysyłki w milimetrach i calach.

|     Pakiet     |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Wysokość         |    311,2            |    12,25 "          |
|    Szerokość          |    642,8          |    25,31 "          |
|    Długość          |   1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Waga obudowy

Pakiet urządzenia waży 66 funtów. i wymaga od dwóch osób, aby je obsłużyć. Waga urządzenia zależy od konfiguracji obudowy.

|     Obudowa                                 |     Waga          |
|-----------------------------------------------|---------------------|
|    Łączna waga włącznie z opakowaniem       |    61 funtów          |
|    Waga urządzenia                       |    35 funtów          |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji przedstawiono specyfikacje dotyczące środowiska obudowy, takie jak temperatura, wilgotność i wysokość.

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

|     Obudowa         |     Zakres temperatury otoczenia     |     Wilgotność względna     |     Maksymalny punkt Rosa     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Działa        |    10 OC – 35 °C (50 °F-86 °F)         |    10%-80% bez kondensacji.         |    29 °C (84 °F)            |
|    Niedziałające    |    -40 °C do 65 °C (-40 °F-149 °F)     |    5%-95% bez kondensacji.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Przepływ powietrza, wysokość, wstrząsy, wibracje, Orientacja, bezpieczeństwo i EMC

|     Obudowa                           |     Specyfikacje operacyjne                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Przepływ powietrza                              |    Przepływ powietrza systemu jest przedsunięty do tyłu. System musi być obsługiwany przy użyciu instalacji z niską ilością wylotu. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maksymalna wysokość, operacyjna        |    3048 mierników (10 000 stóp) z maksymalną temperaturą działania, która jest określana przez niesklasyfikowane [wymagania dotyczące temperatury działania](#operating-temperature-de-rating-specifications).                                                                                |
|    Maksymalna wysokość, niedziałająca    |    12 000 metrów (39 370 stóp)                                                                                                                                                                                         |
|    Wstrząsy, operacyjne                   |    6 G dla 11 milisekund w 6 orientacjach                                                                                                                                                                         |
|    Wstrząsy, niedziałające               |    71 G przez 2 milisekundy w 6 orientacjach                                                                                                                                                                           |
|    Wibracje, operacyjne               |    0,26 G<sub>RMS</sub> 5 hz do 350 Hz                                                                                                                                                                                     |
|    Wibracje, niedziałające           |    1,88 G<sub>RMS</sub> 10 Hz do 500 Hz przez 15 minut (przetestowane wszystkie sześć stron).                                                                                                                                                  |
|    Orientacja i zamontowanie             |    19 "montaż w stojaku                                                                                                                                                                                        |
|    Bezpieczeństwo i zatwierdzenia                 |    EN 60950-1:2006 + A1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    ELEKTROMAGNETYCZNEJ                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetyczna             |    Rozporządzenie Komisji (UE) nr. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specyfikacje dotyczące nieklasyfikacji temperatury działania

|     Obniżenie temperatury działania     |     Zakres temperatury otoczenia                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Do 35 °C (95 °F)                       |    Maksymalną temperaturę zmniejsza się o 1 c/300 m (1 °F/547 ft) powyżej 950 m (3 117 ft).    |
|    35 oC do 40 °C (95 °F do 104 °F)            |    Maksymalna temperatura jest zmniejszona o 1 c/175 m (1 °F/319 ft) powyżej 950 m (3 117 ft).    |
|    40 °C do 45 °C (104 °F do 113 °F)           |    Maksymalna temperatura jest zmniejszona o 1 °C/125 m (1 °F/950 ft) powyżej m (3 117 ft).    |


## <a name="next-steps"></a>Następne kroki

- [Wdrażanie Azure Data Box Edge](data-box-edge-deploy-prep.md)
