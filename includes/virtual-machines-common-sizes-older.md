---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7196a2ea794c1d17a2c55c05accb447d83929972
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744438"
---
Ta sekcja zawiera informacje dotyczące starszych generacjach rozmiarów maszyn wirtualnych. Te rozmiary są nadal obsługiwane, ale nie będą otrzymywali dodatkowej pojemności. Brak rozmiary alternatywnych lub nowsza, które są ogólnie dostępne. Zapoznaj się [rozmiary dla Windows maszyn wirtualnych na platformie Azure](../articles/virtual-machines/windows/sizes.md) lub [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](../articles/virtual-machines/linux/sizes.md) wybrać maszynę Wirtualną rozmiarów, które będzie najlepiej dopasować Twoich potrzeb.  

Aby uzyskać więcej informacji na temat zmiany rozmiaru maszyny Wirtualnej z systemem Linux, zobacz [zmienić rozmiar maszyny wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/change-vm-size.md). Jeśli używasz maszyn wirtualnych Windows i chcesz przy użyciu programu PowerShell, zobacz [zmienić rozmiar maszyny Wirtualnej z systemem Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Podstawowa A  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

Rozmiary warstwy Podstawowa są przeznaczone głównie dla obciążeń związanych z tworzeniem aplikacji i innych aplikacji, które nie wymagają równoważenia obciążenia, automatycznego skalowania ani maszyn wirtualnych korzystających z dużej ilości pamięci.

|Rozmiar — rozmiar\nazwa | Procesor wirtualny |Memory (Pamięć)|Karty sieciowe (maks.)|Maksymalny rozmiar dysku tymczasowego |Maksymalnie z dyski z danymi (1023 GB każdy)|Maksymalnie z liczba operacji we/wy na sekundę (300 na dysk)|
|---|---|---|---|---|---|---|
|A0\Podstawowa_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Podstawowa_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Podstawowa_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Podstawowa_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Podstawowa_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="a-series"></a>Seria A  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2 / 100 |
| Standardowa_A1 |1 |1,75 |70 |2 |2 x 500 |2 / 500  |
| Standardowa_A2 |2 |3,5 |135 |4 |4 x 500 |2 / 500 |
| Standardowa_A3 |4 |7 |285 |8 |8 x 500 |2 / 1000 |
| Standardowa_A4 |8 |14 |605 |16 |16 x 500 |4 / 2000 |
| Standardowa_A5 |2 |14 |135 |4 |4 x 500 |2 / 500 |
| Standardowa_A6 |4 |28 |285 |8 |8 x 500 |2 / 1000 |
| Standardowa_A7 |8 |56 |605 |16 |16 x 500 |4 / 2000 |

<sup>1</sup> rozmiar A0 jest nadmiernie subskrybowany na sprzęcie fizycznym. Tylko w przypadku tego konkretnego rozmiaru inne wdrożenia klienta mogą mieć wpływ na wydajność uruchomionego obciążenia. Wydajność względna jest przedstawiona poniżej jako oczekiwana linia bazowa, podlegająca przybliżonej zmienności w granicach 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>Seria A — wystąpienia intensywnie korzystające z mocy obliczeniowej  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

Rozmiary A8–A11 i serii H są również nazywane *wystąpieniami intensywnie korzystającymi z mocy obliczeniowej*. Sprzęt, na którym działają te rozmiary maszyn wirtualnych, został zaprojektowany i zoptymalizowany pod kątem aplikacji intensywnie korzystających z mocy obliczeniowej i sieci, w tym aplikacji klastrów obliczeń o wysokiej wydajności, modelowania i symulacji. Maszyny wirtualne serii A8–A11 korzystają z procesorów Intel Xeon E5-2670 o częstotliwości 2,6 GHz, a seria H korzysta z procesorów Intel Xeon E5-2667 v3 o częstotliwości 3,2 GHz.  

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standardowa_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standardowa_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>w przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączane przez sieć FDR InfiniBand, która zapewnia bardzo ultraniskie opóźnienia i wysoką przepustowość.  

<br>

### <a name="d-series"></a>Seria D  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Operacje We/Wy / odczyt MB/s / Zapis MB/s | Maksymalna liczba dysków danych / przepływność: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| Standardowa_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standardowa_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standardowa_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |

<sup>1</sup> rodziny maszyn wirtualnych można uruchomić na jednym z następujących procesora CPU: 2.2 GHz Intel Xeon® E5 2660 w wersji 2 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Seria D — zoptymalizowane pod kątem pamięci  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Operacje We/Wy / odczyt MB/s / Zapis MB/s | Maksymalna liczba dysków danych / przepływność: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standardowa_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standardowa_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |
| Standardowa_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup> rodziny maszyn wirtualnych można uruchomić na jednym z następujących procesora CPU: 2.2 GHz Intel Xeon® E5 2660 w wersji 2 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>Seria DS  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Operacje We/Wy / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS1 |1 |3,5 |7 |4 |4000 / 32 (43) |3200 / 32 |2 / 500 |
| Standardowa_DS2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 64 |2 / 1000 |
| Standardowa_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2000 |
| Standardowa_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4000 |

<sup>1</sup> rodziny maszyn wirtualnych można uruchomić na jednym z następujących procesora CPU: 2.2 GHz Intel Xeon® E5 2660 w wersji 2 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Seria DS — zoptymalizowane pod kątem pamięci  

**Zalecenie dotyczące rozmiaru nowszej**: [Seria DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Operacje We/Wy / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS11 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 64 |2 / 1000 |
| Standardowa_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2000 |
| Standardowa_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4000 |
| Standardowa_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> maksymalna przepływność dysków (na SEKUNDĘ lub MB/s) możliwa dla maszyny Wirtualnej serii DS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać więcej informacji, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> rodziny maszyn wirtualnych można uruchomić na jednym z następujących procesora CPU: 2.2 GHz Intel Xeon® E5 2660 w wersji 2 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>
