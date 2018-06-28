---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 638b52edb554b6bddb206943fca42b03bdc69060
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "30845135"
---
<!-- F-series, Fs-series* -->

Obliczenia bazy danych zoptymalizowanych rozmiarów maszyn wirtualnych mają wysoki współczynnik pamięć Procesora i są odpowiednie dla serwerów sieci web średnia ruchu, urządzenia sieciowe, procesów wsadowych i serwerów aplikacji. Ten artykuł zawiera informacje o liczbie Vcpu, dyski danych i karty sieciowe, a także przepustowości przepływności i sieć magazynu dla każdego rozmiaru w tej metodzie grupowania.

Seria Fsv2 opiera się na procesor Intel® Xeon® Platynowa 8168 częstotliwości podstawowej core 2.7 GHz i częstotliwości maksymalną turbo jednordzeniowy 3.7 GHz. Instrukcje Intel® AVX — 512, które są nowe na skalowalnych procesory Intel, zapewni maksymalnie 2 X wzrost wydajności przetwarzania wektor obciążeń na pojedyncze i Podwójna precyzja operacji zmiennoprzecinkowych. Innymi słowy są naprawdę fast dla dowolnego obciążeń obliczeniowych. 

W dolnym godzinową cennika serii Fsv2 jest wartością najlepsze w wydajności cen portfolio Azure oparta na platformie Azure obliczeniowe jednostki (ACU) na vCPU. 

Seria F jest oparta na procesorze Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz, który dzięki technologii Intel Turbo Boost 2.0 może osiągnąć prędkość zegara do 3,1 GHz. Wydajność procesora CPU jest taka sama jak w przypadku maszyn wirtualnych serii Dv2.  

Maszyny wirtualne z serii F są doskonałym wyborem dla obciążeń wymagających szybszych procesorów CPU, ale nie potrzebujących tak dużej ilości pamięci lub magazynu tymczasowego na każdy procesor wirtualny vCPU.  Z wartości serii F skorzystają obciążenia takie jak analizy, serwery gier, serwery sieci Web i przetwarzanie wsadowe.

Seria Fs ma magazyn w warstwie Premium i wszystkie zalety serii F.

## <a name="fsv2-series-sup1sup"></a>Seria Fsv2 <sup>1</sup>

ACU: 195-210

| Rozmiar             | w vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | 2 / 875                                        |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | 2 / 1,750                                      |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | 4 / 3,500                                      |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | 4 / 7000                                      |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | 8 / 14 000                                     |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | 8 / używane 28 000                                     |
| Standard_F72s_v2<sup>2, 3</sup> | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | 8 / 30,000                                     |

<sup>1</sup> serii Fsv2 maszyny Wirtualnej funkcji technologią Intel® Hyper-Threading

<sup>2</sup> więcej niż 64 vCPU, muszą mieć jedną z tych gościa obsługiwanych systemów operacyjnych: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 z dodatkiem SP2 i Red Hat Enterprise Linux, CentOS 7.3 lub Oracle 7.3 Linux z LIS 4.2.1

<sup>3</sup> wystąpienia jest izolowana sprzętu przeznaczonego do jednego odbiorcy.

## <a name="fs-series-sup1sup"></a>Seria FS <sup>1</sup>

ACU: 210–250

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standardowa_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standardowa_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3000 |
| Standardowa_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6000 |
| Standardowa_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

MB/s = 10^6 bajtów na sekundę, GiB = 1024^3 bajtów.

<sup>1</sup> przepustowość maksymalna liczba dyskowych operacji to (IOPS lub MB/s) możliwe z serii Fs maszyny Wirtualnej może być ograniczony przez kod, rozmiar i rozkładanie dołączone dyski.  Aby uzyskać szczegółowe informacje, zobacz [Premium Storage: High-performance storage for Azure virtual machine workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure).


<br>

## <a name="f-series"></a>Seria F

ACU: 210–250

| Rozmiar         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych / oczekiwano przepustowości sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standardowa_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standardowa_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |


<br>


