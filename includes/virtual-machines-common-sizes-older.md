---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541516"
---
Ta sekcja zawiera informacje dotyczące starszych generacji rozmiarów maszyn wirtualnych. Te rozmiary są nadal obsługiwane, ale nie będą otrzymywać dodatkowej pojemności. Istnieją również nowsze lub alternatywne rozmiary, które są ogólnie dostępne. Zapoznaj się z [rozmiarem maszyn wirtualnych z systemem Windows na platformie Azure](../articles/virtual-machines/windows/sizes.md) lub w [rozmiarach dla maszyn wirtualnych z systemem Linux na platformie Azure](../articles/virtual-machines/linux/sizes.md) , aby wybrać rozmiary maszyn wirtualnych, które najlepiej pasują do Twoich potrzeb.  

Aby uzyskać więcej informacji na temat zmiany rozmiaru maszyny wirtualnej z systemem Linux, zobacz [Zmienianie rozmiaru maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/change-vm-size.md). Jeśli używasz maszyn wirtualnych z systemem Windows i wolisz korzystać z programu PowerShell, zobacz [Zmienianie rozmiaru maszyny wirtualnej z systemem Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Podstawowa A  

**Nowe zalecenie dotyczące rozmiaru**: [Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

Rozmiary warstwy Podstawowa są przeznaczone głównie dla obciążeń związanych z tworzeniem aplikacji i innych aplikacji, które nie wymagają równoważenia obciążenia, automatycznego skalowania ani maszyn wirtualnych korzystających z dużej ilości pamięci.

|Rozmiar — rozmiar\nazwa | Procesor wirtualny |Memory (Pamięć)|Karty sieciowe (maks.)|Maksymalny rozmiar dysku tymczasowego |Maksymalnie z dyski z danymi (1023 GB każdego)|Maksymalnie z liczba operacji we/wy na sekundę (300 na dysk)|
|---|---|---|---|---|---|---|
|A0\Podstawowa_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Podstawowa_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Podstawowa_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Podstawowa_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Podstawowa_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standardowa_A0–A4 w przypadku używania interfejsu wiersza polecenia i programu PowerShell

W klasycznym modelu wdrażania niektóre nazwy rozmiarów maszyny wirtualnej są nieco inne w interfejsie wiersza polecenia i programie PowerShell:

* Standardowa_A0 = Bardzo mała
* Standardowa_A1 = Mała
* Standardowa_A2 = Średnia
* Standardowa_A3 = Duża
* Standardowa_A4 = Bardzo duża

### <a name="a-series"></a>Seria A  

**Nowe zalecenie dotyczące rozmiaru**: [Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU 50-100

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (dysk twardy): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku danych: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2 / 100 |
| Standardowa_A1 |1 |1,75 |70 |2 |2 x 500 |2 / 500  |
| Standardowa_A2 |2 |3,5 |135 |4 |4 x 500 |2 / 500 |
| Standardowa_A3 |4 |7 |285 |8 |8 x 500 |2 / 1000 |
| Standardowa_A4 |8 |14 |605 |16 |16 x 500 |4 / 2000 |
| Standardowa_A5 |2 |14 |135 |4 |4 x 500 |2 / 500 |
| Standardowa_A6 |4 |28 |285 |8 |8 x 500 |2 / 1000 |
| Standardowa_A7 |8 |56 |605 |16 |16 x 500 |4 / 2000 |

<sup>1</sup> rozmiar a0 jest nadmiernie subskrybowany na sprzęcie fizycznym. Tylko w przypadku tego konkretnego rozmiaru inne wdrożenia klienta mogą mieć wpływ na wydajność uruchomionego obciążenia. Wydajność względna jest przedstawiona poniżej jako oczekiwana linia bazowa, podlegająca przybliżonej zmienności w granicach 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>Seria A — wystąpienia intensywnie korzystające z mocy obliczeniowej  

**Nowe zalecenie dotyczące rozmiaru**: [Seria Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU 225

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

Rozmiary A8–A11 i serii H są również nazywane *wystąpieniami intensywnie korzystającymi z mocy obliczeniowej*. Sprzęt, na którym działają te rozmiary maszyn wirtualnych, został zaprojektowany i zoptymalizowany pod kątem aplikacji intensywnie korzystających z mocy obliczeniowej i sieci, w tym aplikacji klastrów obliczeń o wysokiej wydajności, modelowania i symulacji. Maszyny wirtualne serii A8–A11 korzystają z procesorów Intel Xeon E5-2670 o częstotliwości 2,6 GHz, a seria H korzysta z procesorów Intel Xeon E5-2667 v3 o częstotliwości 3,2 GHz.  

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (dysk twardy): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku danych: IOPS | Maksymalna liczba kart sieciowych|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standardowa_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standardowa_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> W przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączana przez sieć FDR InfiniBand, która zapewnia bardzo małe opóźnienia i wysoką przepustowość.  

<br>

### <a name="d-series"></a>Seria D  

**Nowe zalecenie dotyczące rozmiaru**: [Seria Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU 160-250 <sup>1</sup>

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| Standardowa_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standardowa_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standardowa_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |

<sup>1</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Seria D — zoptymalizowana pod kątem pamięci  

**Nowe zalecenie dotyczące rozmiaru**: [Seria Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU 160-250 <sup>1</sup>

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standardowa_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standardowa_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |
| Standardowa_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>Seria DS  

**Nowe zalecenie dotyczące rozmiaru**: [Seria DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU 160-250 <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS1 |1 |3,5 |7 |4 |4000 / 32 (43) |3200 / 32 |2 / 500 |
| Standardowa_DS2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 64 |2 / 1000 |
| Standardowa_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2000 |
| Standardowa_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4000 |

<sup>1</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Zoptymalizowane pod kątem pamięci z serii DS  

**Nowe zalecenie dotyczące rozmiaru**: [Seria DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU 160-250 <sup>1, 2</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS11 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 64 |2 / 1000 |
| Standardowa_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2000 |
| Standardowa_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4000 |
| Standardowa_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej z serii DS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Seria Ls

Maszyny wirtualne serii Ls oferują do 32 procesorów wirtualnych vCPU — procesor [Intel® Xeon® z rodziny E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Wydajność procesora CPU dla serii Ls jest taka sama jak w przypadku maszyn wirtualnych serii G/GS. Na procesor wirtualny vCPU przypada pamięć o pojemności 8 GiB.

Seria ls nie obsługuje tworzenia lokalnej pamięci podręcznej w celu zwiększenia liczby operacji we/wy osiągalnej przez trwałe dyski danych. Wysoka przepływność i liczba operacji we/wy na dysku lokalnym sprawia, że maszyny wirtualne z serii LS są idealne dla magazynów NoSQL, takich jak Apache Cassandra i MongoDB, które replikuje dane między wieloma maszynami wirtualnymi w celu osiągnięcia trwałości w przypadku awarii pojedynczej maszyny wirtualnej.

ACU 180-240

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Brak obsługi
 
| Size          | Procesor wirtualny | Pamięć (GiB) | Magazyn tymczasowy (GiB) | Maks. liczba dysków danych | Maksymalna przepływność magazynu (IOPS/MB/s) | Maksymalna przepływność dysku w pamięci podręcznej (IOPS/MB/s) | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standardowa_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standardowa_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standardowa_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16 000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20 000 | 

Maksymalna przepływność dysku możliwa w przypadku maszyn wirtualnych z serii LS może być ograniczona przez liczbę, rozmiar i rozłożenie dowolnych dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

### <a name="gs-series"></a>Seria GS 

ACU 180 – 240 <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5000 / 125 |2 / 2000 |
| Standardowa_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4000 |
| Standardowa_GS3 |8 |112 |224 |32 |40 000 / 400 (1056) |20 000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2112) |40 000 / 1000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1600 (4224) |80 000 / 2000 |8 / 20 000 |

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej serii GS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.

<br>

### <a name="g-series"></a>Seria G

ACU 180 – 240

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standardowa_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standardowa_G3  | 8         | 112         | 1536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standardowa_G4  | 16        | 224         | 3072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20 000           |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<br>
