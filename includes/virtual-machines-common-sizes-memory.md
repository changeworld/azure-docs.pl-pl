---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 09f379279a7247f87b9e0830414a5e4363f41cdb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466690"
---
Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci oferują duże proporcje pamięci dla serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizę w pamięci. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart sieciowych, a także przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

* Serie EV3 są dostępne w przypadku procesorów Intel® Xeon® 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) w konfiguracji wielowątkowej, co zapewnia lepszą propozycję wartości dla większości obciążeń ogólnego przeznaczenia i przełączenie EV3 do wyrównanie z maszynami wirtualnymi ogólnego przeznaczenia dla większości innych chmur.  Pamięć została rozszerzona (od 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane dla poszczególnych rdzeni, aby dostosować je do przenoszenia do wielowątkowości.  EV3 to kolejne rozmiary maszyn wirtualnych o dużej ilości pamięci w rodzinach D/Dv2.

* Serie Eav4 i Easv4 wykorzystują procesor<sup>AMD 7452 z</sup> 2.35 GHz EPYC, w konfiguracji wielowątkowej, z maksymalnie 256 MB pamięci podręcznej L3, zwiększając opcje uruchamiania większości zoptymalizowanych pod kątem pamięci.  Serie Eav4 i Easv4 mają takie same konfiguracje pamięci i dysków jak seria EV3 & Esv3.

* Seria Mv2 oferuje najwyższą liczbę vCPU (do 416 procesorów wirtualnych vCPU) i największą ilość pamięci (do 8,19 TiB) dowolnej maszyny wirtualnej w chmurze. Jest to idealne rozwiązanie dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby procesorów wirtualnych vCPU i dużych ilości pamięci.

* Seria M oferuje dużą liczbę vCPU (do 128 procesorów wirtualnych vCPU) i dużą ilość pamięci (do 3,8 TiB). Jest on również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby vCPU i dużych ilości pamięci.

* Dv2 serii, G i DSv2/GS są idealnym rozwiązaniem w przypadku aplikacji wymagających szybszego procesorów wirtualnych vCPU, lepszej wydajności magazynu i większej ilości pamięci. Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.

* Seria Dv2, kontynuacja oryginalnej serii D, jest wyposażona w procesor CPU o większych możliwościach. Seria Dv2 jest o około 35% szybsza niż seria D. Jest ona uruchamiana na procesorach Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) oraz z technologią Intel Turbo — rozwiązanie 2,0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

* Usługa Azure Compute oferuje różne rozmiary maszyn wirtualnych, które są izolowane pod kątem konkretnego rodzaju sprzętu i przeznaczone dla jednego klienta.  Te rozmiary maszyn wirtualnych są dostosowane do obciążeń wymagających wysokiego stopnia izolacji od innych klientów, a zwłaszcza obciążeń związanych z takimi elementami jak zgodność z przepisami i wymogi prawne.  Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Zapoznaj się z tabelami poniższych rodzin maszyn wirtualnych dla opcji izolowanych maszyn wirtualnych.

## <a name="esv3-series"></a>Seria Esv3

ACU: 160-190 <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Wystąpienia serii ESv3 są wyposażone w procesory Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) i można osiągnąć 3,5 GHz z technologią Intel Turbo — rozwiązanie 2,0 i korzystać z magazynu Premium Storage. Wystąpienia serii Ev3 są doskonałe dla aplikacji korporacyjnych intensywnie korzystających z pamięci.


| Rozmiar             | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standardowa_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16 000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> platforma maszyny wirtualnej z serii Esv3 firmy Intel® technologię wielowątkowości.

dostępne <sup>dwa</sup> ograniczone rozmiary rdzeni.

<sup>3</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

## <a name="easv4-series"></a>Seria Easv4

ACU: 230 – 260

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Rozmiary serii Easv4 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć podwyższaną maksymalną częstotliwość 3.35 GHz i korzystać z dysku SSD Premium. Rozmiary serii Easv4 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16 000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | | 
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | | 
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Te rozmiary są w wersji zapoznawczej. Jeśli interesuje Cię wypróbowanie większych rozmiarów, zarejestruj się w [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="ev3-series"></a>Seria Ev3 

ACU: 160 – 190 <sup>1</sup>

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Wystąpienia serii EV3 są wyposażone w procesory Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) i można osiągnąć 3,5 GHz z technologią Intel Turbo — rozwiązanie 2,0. Wystąpienia serii Ev3 są doskonałe dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów ESv3. Liczniki cen i rozliczeń dla rozmiarów ESv3 są takie same jak dla serii Ev3. 


| Rozmiar            | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych / przepustowość sieci |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standardowa_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standardowa_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standardowa_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standardowa_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standardowa_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16 000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standardowa_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> platforma maszyny wirtualnej z serii EV3 firmy Intel® technologię wielowątkowości.

dostępne <sup>dwa</sup> ograniczone rozmiary rdzeni.

<sup>3</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

## <a name="eav4-series"></a>Seria Eav4

ACU: 230 – 260

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Rozmiary serii Eav4 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć podwyższaną maksymalną częstotliwość 3.35 GHz i korzystać z dysku SSD Premium. Rozmiary serii Eav4 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci. Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysku SSD Premium, użyj rozmiarów serii Easv4. Liczniki cen i rozliczeń dla rozmiarów Easv4 są takie same jak dla serii Eav3.

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standardowa\_E2A\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Standardowa\_E4A\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Standardowa\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standardowa\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standardowa\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| Standardowa\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16 000 |
| Standardowa\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standardowa\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standardowa\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Te rozmiary są w wersji zapoznawczej.  Jeśli interesuje Cię wypróbowanie większych rozmiarów, zarejestruj się w [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="mv2-series"></a>Seria Mv2

ACU: 188-280<sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Akcelerator zapisu: [obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Seria Mv2 oferuje wysoką przepływność, platformę o małym opóźnieniu działającą na procesorze Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) z podstawową częstotliwością 2,5 GHz i maksymalną częstotliwością Turbo 3,8 GHz. Wszystkie rozmiary maszyn wirtualnych z serii Mv2 mogą korzystać z dysków trwałych w warstwach Standardowa i Premium. Wystąpienia serii Mv2 to rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci, które zapewniają niezrównaną wydajność obliczeniową w celu obsługi dużych baz danych i obciążeń w pamięci, dzięki czemu jest idealnym rozwiązaniem z dużą ilością pamięci w przypadku serwerów relacyjnych baz danych, dużych pamięci podręcznych i w pamięci wersję.

|Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> funkcja maszyny wirtualnej z serii Mv2 Intel® technologia wielowątkowości funkcji Hyper-Threading

<sup>2</sup> maszyny wirtualne z serii Mv2 są tylko 2 generacji. Jeśli używasz systemu Linux, zobacz [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../articles/virtual-machines/linux/generation-2.md) , aby uzyskać instrukcje dotyczące znajdowania i wybierania obrazu.

<sup>3</sup> dla rozmiaru M416ms_v2 i M416s_v2 należy zauważyć, że istnieje początkowa pomoc techniczna tylko dla następujących obrazów: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 dla aplikacji SAP".

## <a name="m-series"></a>Seria M 

ACU: 160-180 <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Rozmiary serii M są oparte na procesorze Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz   

Akcelerator zapisu: [obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Rozmiar            | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16 000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000/800 (6348) | 40000/1000 | 8 / 16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16 000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> funkcja maszyny wirtualnej z serii M Intel® technologia wielowątkowości funkcji Hyper-Threading

<sup>2</sup> więcej niż 64 vCPU wymagają jednego z obsługiwanych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 i Red Hat Enterprise Linux, CentOS 7,3 lub Oracle Linux 7,3 z prze4.2.1.

dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.

<sup>4</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 — Seria 11-15

ACU: 210 – 250 <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Rozmiary serii DSv2 są uruchamiane na procesorach Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej serii DSv2 może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> wystąpienie jest izolowane do sprzętu opartego na architekturze Intel Haswell i przeznaczone dla jednego klienta.  
dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.  
<sup>4</sup> 25000 MB/s z przyspieszoną siecią. 

<br>

## <a name="dv2-series-11-15"></a>Dv2 — Seria 11-15

ACU: 210–250

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Rozmiary serii DSv2 są uruchamiane na procesorach Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Rozmiar              | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba dysków danych / przepływność: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standardowa_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.  
<sup>2</sup> 25000 MB/s z przyspieszoną siecią. 
