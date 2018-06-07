---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/22/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 4b4dcb16adf207b775bd8411848d9e5d69c397a9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669802"
---
Ten artykuł zawiera informacje na poprzedniej generacji rozmiarów maszyn wirtualnych. Rozmiary można nadal używać, ale są dostępne nowsze generacji.


## <a name="ds-series"></a>Seria DS

ACU: 160

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS1 |1 |3,5 |7 |4 |4000 / 32 (43) |3200 / 32 |2 / 500 |
| Standardowa_DS2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 64 |2 / 1000 |
| Standardowa_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2000 |
| Standardowa_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4000 |

<br>

## <a name="ds-series---memory-optimized"></a>Serii DS - zoptymalizowanych pod kątem pamięci

ACU: 160 <sup>1</sup>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS11 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 64 |2 / 1000 |
| Standardowa_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2000 |
| Standardowa_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4000 |
| Standardowa_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> przepustowość maksymalna liczba dyskowych operacji to (IOPS lub MB/s), możliwe z serii DS maszyny Wirtualnej może być ograniczony przez kod, rozmiar i rozkładanie dołączone dyski.  Aby uzyskać szczegółowe informacje, zobacz [Premium Storage: High-performance storage for Azure virtual machine workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure).



## <a name="d-series"></a>Seria D 

ACU: 160

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| Standardowa_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standardowa_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standardowa_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |

<br>

## <a name="d-series---memory-optimized"></a>D-series - zoptymalizowanych pod kątem pamięci

ACU: 160

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standardowa_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standardowa_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |
| Standardowa_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<br>

## <a name="a-series---compute-intensive-instances"></a>Seria A — wystąpienia intensywnie korzystające z mocy obliczeniowej

ACU: 225

Rozmiary A8–A11 i serii H są również nazywane *wystąpieniami intensywnie korzystającymi z mocy obliczeniowej*. Sprzęt, na którym działają te rozmiary maszyn wirtualnych, został zaprojektowany i zoptymalizowany pod kątem aplikacji intensywnie korzystających z mocy obliczeniowej i sieci, w tym aplikacji klastrów obliczeń o wysokiej wydajności, modelowania i symulacji. Maszyny wirtualne serii A8–A11 korzystają z procesorów Intel Xeon E5-2670 o częstotliwości 2,6 GHz, a seria H korzysta z procesorów Intel Xeon E5-2667 v3 o częstotliwości 3,2 GHz.  Ten artykuł zawiera informacje o liczbie Vcpu, dyski danych i karty sieciowe, a także przepustowości przepływności i sieć magazynu dla każdego rozmiaru w tej metodzie grupowania. 

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standardowa_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standardowa_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>dla aplikacji MPI dedykowanej sieci wewnętrznej bazy danych RDMA jest włączana przez FDR InfiniBand sieci, co zapewnia bardzo min. opóźnienie i wysokiej przepustowości.

<br>

## <a name="a-series"></a>Seria A

ACU: 50–100

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2 / 100 |
| Standardowa_A1 |1 |1,75 |70 |2 |2 x 500 |2 / 500  |
| Standardowa_A2 |2 |3,5 |135 |4 |4 x 500 |2 / 500 |
| Standardowa_A3 |4 |7 |285 |8 |8 x 500 |2 / 1000 |
| Standardowa_A4 |8 |14 |605 |16 |16 x 500 |4 / 2000 |
| Standardowa_A5 |2 |14 |135 |4 |4 x 500 |2 / 500 |
| Standardowa_A6 |4 |28 |285 |8 |8 x 500 |2 / 1000 |
| Standardowa_A7 |8 |56 |605 |16 |16 x 500 |4 / 2000 |
<br>

<sup>1</sup> A0 rozmiar jest nadmiernie subskrybowanego na sprzęcie fizycznym. Tylko w przypadku tego konkretnego rozmiaru inne wdrożenia klienta mogą mieć wpływ na wydajność uruchomionego obciążenia. Wydajność względna jest przedstawiona poniżej jako oczekiwana linia bazowa, podlegająca przybliżonej zmienności w granicach 15 procent.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standardowa_A0–A4 w przypadku używania interfejsu wiersza polecenia i programu PowerShell
W klasycznym modelu wdrażania niektóre nazwy rozmiarów maszyny wirtualnej są nieco inne w interfejsie wiersza polecenia i programie PowerShell:

* Standardowa_A0 = Bardzo mała 
* Standardowa_A1 = Mała
* Standardowa_A2 = Średnia
* Standardowa_A3 = Duża
* Standardowa_A4 = Bardzo duża

## <a name="basic-a"></a>Podstawowa A

Rozmiary warstwy Podstawowa są przeznaczone głównie dla obciążeń związanych z tworzeniem aplikacji i innych aplikacji, które nie wymagają równoważenia obciążenia, automatycznego skalowania ani maszyn wirtualnych korzystających z dużej ilości pamięci.

|Rozmiar — rozmiar\nazwa | Procesor wirtualny |Memory (Pamięć)|Karty sieciowe (maks.)|Maksymalny rozmiar dysku tymczasowego |Maksymalnie z dyski danych (1023 GB każda)|Maksymalnie z liczba operacji we/wy na sekundę (300 na dysk)|
|---|---|---|---|---|---|---|
|A0\Podstawowa_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Podstawowa_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Podstawowa_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Podstawowa_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Podstawowa_A4|8|14 GB|2| 240 GB |16|16x300|
 


