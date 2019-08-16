---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 8d90d71b8d29d26f09ef617ddd56ce91eb4e5e2e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541517"
---
Ta sekcja zawiera informacje dotyczące wcześniejszych generacji rozmiarów maszyn wirtualnych. Nadal mogą być używane te rozmiary, ale dostępne są nowsze generacji. 

## <a name="f-series"></a>Seria F

Seria F jest oparta na procesorze Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz, który dzięki technologii Intel Turbo Boost 2.0 może osiągnąć prędkość zegara do 3,1 GHz. Wydajność procesora CPU jest taka sama jak w przypadku maszyn wirtualnych serii Dv2.  

Maszyny wirtualne z serii F są doskonałym wyborem dla obciążeń wymagających szybszych procesorów CPU, ale nie potrzebujących tak dużej ilości pamięci lub magazynu tymczasowego na każdy procesor wirtualny vCPU.  Z wartości serii F skorzystają obciążenia takie jak analizy, serwery gier, serwery sieci Web i przetwarzanie wsadowe.

ACU 210 – 250

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size         | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standardowa_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standardowa_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS — seria <sup>1</sup>

Seria Fs ma magazyn w warstwie Premium i wszystkie zalety serii F.

ACU 210 – 250

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_F1s |1 |2 |4 |4 |4000/32 (12) |3200/48 |2 / 750 |
| Standardowa_F2s |2 |4 |8 |8 |8000/64 (24) |6400/96 |2 / 1500 |
| Standardowa_F4s |4 |8 |16 |16 |16000/128 (48) |12800/192 |4 / 3000 |
| Standardowa_F8s |8 |16 |32 |32 |32000/256 (96) |25600/384 |8 / 6000 |
| Standardowa_F16s |16 |32 |64 |64 |64000/512 (192) |51200/768 |8 / 12000 |

MB/s = 10^6 bajtów na sekundę, GiB = 1024^3 bajtów.

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej z serii FS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="nvv2-series"></a>Seria NVv2

**Nowe zalecenie dotyczące rozmiaru**: [Seria NVv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series-preview-1)

Maszyny wirtualne z serii NVv2 są obsługiwane przez procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU i technologię NVIDIA GRID z procesorami Intel Broadwell. Te maszyny wirtualne są przeznaczone dla procesorów GPU i pulpitów wirtualnych, na których klienci chcą wizualizować swoje dane, symulują wyniki do wyświetlania, pracy w programie CAD lub renderowania i przesyłania strumieniowego zawartości. Dodatkowo te maszyny wirtualne mogą uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. NVv2 maszyny wirtualne obsługują Premium Storage i mają dwa razy większą ilość pamięci systemowej (RAM) w porównaniu z poprzednią serią NV.  

Każdy procesor GPU w wystąpieniach NVv2 zawiera licencję siatki. Ta licencja zapewnia elastyczność używania wystąpienia TRWAŁEgo jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 jednoczesnych użytkowników może połączyć się z MASZYNą wirtualną w scenariuszu aplikacji wirtualnej.

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesor GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |
