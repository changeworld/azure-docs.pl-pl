---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: d0802cfcf05874044b6e116ba194c16a79f9d309
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541606"
---
Ta sekcja zawiera informacje dotyczące poprzednie generacje rozmiarów maszyn wirtualnych. Nadal można korzystać z tych rozmiarów, ale są dostępne nowsze generacji. 

## <a name="f-series"></a>Seria F

Seria F jest oparta na procesorze Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz, który dzięki technologii Intel Turbo Boost 2.0 może osiągnąć prędkość zegara do 3,1 GHz. Wydajność procesora CPU jest taka sama jak w przypadku maszyn wirtualnych serii Dv2.  

Maszyny wirtualne z serii F są doskonałym wyborem dla obciążeń wymagających szybszych procesorów CPU, ale nie potrzebujących tak dużej ilości pamięci lub magazynu tymczasowego na każdy procesor wirtualny vCPU.  Z wartości serii F skorzystają obciążenia takie jak analizy, serwery gier, serwery sieci Web i przetwarzanie wsadowe.

ACU: 210 - 250

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Operacje We/Wy / odczyt MB/s / Zapis MB/s | Maksymalna liczba dysków danych / przepływność: Operacje wejścia/wyjścia | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standardowa_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standardowa_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Seria FS <sup>1</sup>

Seria Fs ma magazyn w warstwie Premium i wszystkie zalety serii F.

ACU: 210 - 250

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Operacje We/Wy / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MBps | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standardowa_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standardowa_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3000 |
| Standardowa_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6000 |
| Standardowa_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

MB/s = 10^6 bajtów na sekundę, GiB = 1024^3 bajtów.

<sup>1</sup> maksymalna przepływność dysków (na SEKUNDĘ lub MB/s) możliwa dla maszyny Wirtualnej serii Fs może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać więcej informacji, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Seria Ls

Maszyny wirtualne serii Ls oferują do 32 procesorów wirtualnych vCPU — procesor [Intel® Xeon® z rodziny E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Wydajność procesora CPU dla serii Ls jest taka sama jak w przypadku maszyn wirtualnych serii G/GS. Na procesor wirtualny vCPU przypada pamięć o pojemności 8 GiB.

Seria Ls nie obsługuje tworzenia lokalnej pamięci podręcznej w celu zwiększenia operacje We/Wy osiągalna dysków danych trwałych. Wysoka przepływność i operacje We/Wy dysku lokalnego sprawia, że maszyny wirtualne z serii Ls idealnym rozwiązaniem dla magazynów NoSQL, takie jak bazy danych Apache Cassandra i bazy danych MongoDB, które replikowanie danych między wieloma maszynami wirtualnymi do osiągnięcia trwałości w przypadku awarii jednej maszyny Wirtualnej.

ACU: 180-240

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Nieobsługiwane
 
| Rozmiar          | Procesor wirtualny | Pamięć (GiB) | Magazynu tymczasowego (GiB) | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego (na SEKUNDĘ / MB/s) | Maksymalna przepływność niebuforowanych dysków (na SEKUNDĘ / MB/s) | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standardowa_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5000 / 125  | 2 / 4,000  | 
| Standardowa_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10 000 / 250 | 4 / 8,000  | 
| Standardowa_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20 000 / 500 | 8 / 16,000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40 000 / 1000     | 8 / 20,000 | 

Maksymalna przepływność dysków możliwa przy użyciu maszyn wirtualnych serii Ls może być ograniczona przez liczbę, rozmiar i rozłożenie wszelkich dołączonych dysków. Aby uzyskać więcej informacji, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standardowa_A0–A4 w przypadku używania interfejsu wiersza polecenia i programu PowerShell

W klasycznym modelu wdrażania niektóre nazwy rozmiarów maszyny wirtualnej są nieco inne w interfejsie wiersza polecenia i programie PowerShell:

* Standardowa_A0 = Bardzo mała
* Standardowa_A1 = Mała
* Standardowa_A2 = Średnia
* Standardowa_A3 = Duża
* Standardowa_A4 = Bardzo duża
