---
title: Specyfikacje techniczne i zgodność usługi Microsoft Azure Data Box Edge| Dokumenty firmy Microsoft
description: Dowiedz się więcej o specyfikacjach technicznych i zgodności urządzenia Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252029"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Specyfikacje techniczne usługi Azure Data Box Edge

Składniki sprzętowe urządzenia usługi Microsoft Azure Data Box Edge są zgodne ze specyfikacjami technicznymi i standardami regulacyjnymi opisanymi w tym artykule. Specyfikacje techniczne opisują zasilacze, pojemność pamięci masowej, obudowy i normy środowiskowe. 

## <a name="compute-memory-specifications"></a>Obliczenia, specyfikacje pamięci

Urządzenie Data Box Edge ma następujące specyfikacje dotyczące obliczeń i pamięci:

| Specyfikacja           | Wartość                  |
|-------------------------|----------------------------|
| Procesor CPU    | 2 X 10-rdzeniowy procesor                     |
| Memory (Pamięć)              | 128 GB pamięci RAM                  |


## <a name="fpga-specifications"></a>Specyfikacje FPGA

Field Programmable Gate Array (FPGA) znajduje się na każdym urządzeniu data box edge, które umożliwia scenariusze uczenia maszynowego (ML). 

| Specyfikacja           | Wartość                  |
|-------------------------|----------------------------|
| Fpga   | Intel Arria 10 <br> Dostępne modele głębokiej sieci neuronowej (DNN) są takie same jak modele [obsługiwane przez wystąpienia FPGA w chmurze.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)| 


## <a name="power-supply-unit-specifications"></a>Specyfikacja zasilacza

Urządzenie Data Box Edge ma dwa zasilacze 100-240 V z wentylatorami o wysokiej wydajności. Dwa zasilacze zapewniają nadmiarową konfigurację zasilania. Jeśli zasilacz ulegnie awarii, urządzenie będzie działać normalnie na innym zasilaczu, dopóki nie zostanie wymieniony moduł. W poniższej tabeli wymieniono specyfikacje techniczne zasilaczy.

| Specyfikacja           | Zasilacz 750 W                  |
|-------------------------|----------------------------|
| Maksymalna moc wyjściowa    | 750 W                     |
| Częstotliwość               | 50/60 Hz                   |
| Wybór zakresu napięcia | Automatyczny zakres: 100-240 V AC |
| Gorąca podłączalna           | Tak                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Specyfikacje interfejsu sieciowego

Urządzenie Data Box Edge ma 6 interfejsów sieciowych PORT1- PORT6.

| Specyfikacja           | Opis                 |
|-------------------------|----------------------------|
|  Interfejsy sieciowe    | 2 interfejsy 1 GbE — 1 do zarządzania, bez możliwości konfigurowania przez użytkownika, używany do konfiguracji początkowej. Drugi interfejs może być konfigurowany przez użytkownika i służyć do transferu danych. Domyślnie jest interfejsem DHCP. <br>2 interfejsy 25 GbE — mogą również działać jako interfejsy 10 GbE. Te interfejsy danych mogą być konfigurowane przez użytkownika jako DHCP (ustawienie domyślne) lub statyczne. <br> 2 interfejsy 25 GbE — te interfejsy danych mogą być konfigurowane przez użytkownika jako DHCP (ustawienie domyślne) lub statyczne.                  |

## <a name="storage-specifications"></a>Specyfikacje pamięci masowej

Urządzenia Data Box Edge mają dyski SSD NVMe o przekątnej 9 x 2,5", z których każdy ma pojemność 1,6 TB. Z tych dysków SSD 1 to dysk systemu operacyjnego, a pozostałe 8 to dyski z danymi. Całkowita pojemność użytkowa urządzenia wynosi około 12,5 TB. W poniższej tabeli przedstawiono szczegółowe informacje dotyczące pojemności urządzenia.

|     Specyfikacja                          |     Wartość             |
|--------------------------------------------|-----------------------|
|    Liczba dysków PÓŁPRZEWODNIKOWYCH (SSD)     |    8                  |
|    Pojedyncza pojemność SSD                     |    1,6 TB             |
|    Całkowita pojemność                          |    12,8 TB            |
|    Całkowita pojemność użytkowa*                  |    ~ 12,5 TB            |

**Część miejsca jest zarezerwowana do użytku wewnętrznego.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagowe

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wagi.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli wymieniono wymiary obudowy w milimetrach i calach.

|     Obudowa     |     Milimetrach     |     Cali     |
|-------------------|---------------------|----------------|
|    Właściwość Height         |    44.45            |    1.75"          |
|    impulsów          |    434.1           |    17.09"          |
|    Długość          |    740.4           |    29.15"          |

W poniższej tabeli wymieniono wymiary przesyłki w milimetrach i calach.

|     Pakiet     |     Milimetrach     |     Cali     |
|-------------------|---------------------|----------------|
|    Właściwość Height         |    311.2            |    12.25"          |
|    impulsów          |    642.8          |    25.31"          |
|    Długość          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Masa obudowy

Opakowanie urządzenia waży 66 funtów. i wymaga od dwóch osób, aby sobie z tym poradzić. Waga urządzenia zależy od konfiguracji obudowy.

|     Obudowa                                 |     Waga          |
|-----------------------------------------------|---------------------|
|    Całkowita waga wraz z opakowaniem       |    61 funtów.          |
|    Waga urządzenia                       |    35 funtów.          |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji wymieniono specyfikacje związane ze środowiskiem obudowy, takie jak temperatura, wilgotność i wysokość.

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

|     Obudowa         |     Zakres temperatur otoczenia     |     Wilgotność względna otoczenia     |     Maksymalny punkt rosy     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacyjne        |    10°C - 35°C         |    10% - 80% bez kondensacji.         |    29°C (84°F)            |
|    Nieoperacyjne    |    Od -40°C do 65°C     |    5% - 95% bez kondensacji.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Przepływ powietrza, wysokość, wstrząsy, wibracje, orientacja, bezpieczeństwo i EMC

|     Obudowa                           |     Specyfikacje operacyjne                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Przepływ powietrza                              |    Przepływ powietrza systemowego jest od przodu do tyłu. System musi być obsługiwany przy instalacji niskociśnieniowej z tylnym układem wydechowym. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maksymalna wysokość, operacyjna        |    3048 metrów (10 000 stóp) przy maksymalnej temperaturze roboczej odsygnowanej przez [specyfikacje różnicy znamionowej temperatury roboczej.](#operating-temperature-de-rating-specifications)                                                                                |
|    Maksymalna wysokość, nieoperacyjna    |    12 000 metrów                                                                                                                                                                                         |
|    Wstrząsy, operacyjne                   |    6 G przez 11 milisekund w 6 orientacjach                                                                                                                                                                         |
|    Wstrząsy, nieoperacyjne               |    71 G przez 2 milisekundy w 6 orientacjach                                                                                                                                                                           |
|    Wibracje, operacyjne               |    Losowe 0,26 G<sub>RMS</sub> 5 Hz do 350 Hz                                                                                                                                                                                     |
|    Wibracje, nieoperacyjne           |    1,88 G<sub>RMS</sub> 10 Hz do 500 Hz przez 15 minut (wszystkie sześć testowanych stron).                                                                                                                                                  |
|    Orientacja i montaż             |    19-ek                                                                                                                                                                                        |
|    Bezpieczeństwo i aprobaty                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    Emc                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetyczna             |    Rozporządzenie Komisji (UE) nr 617/2013                                                                                                                                                                                        |
|    Rohs           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Specyfikacje odsysania temperatury roboczej

|     Obniżenie temperatury roboczej     |     Zakres temperatur otoczenia                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Do 35°C                       |    Temperatura maksymalna jest obniżona o 1°C(1°F/547 ft) powyżej 950 m (3,117 ft).    |
|    od 35°C do 40°C            |    Temperatura maksymalna jest obniżona o 1°C(175 m) powyżej 950 m(3,117 ft).    |
|    od 40°C do 45°C           |    Temperatura maksymalna jest obniżona o 1°C(125 m) powyżej 950 m(3,117 ft).    |


## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi Azure Data Box Edge](data-box-edge-deploy-prep.md)
