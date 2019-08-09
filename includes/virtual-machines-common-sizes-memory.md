---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3d45defa9ff8e7b2e03d550b76c0e18192c58c4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881616"
---
Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci oferują duże proporcje pamięci dla serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizę w pamięci. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart sieciowych, a także przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

* EV3 Series oferuje procesor E5-2673 v4 2,3 GHz (Broadwell) w konfiguracji wielowątkowej, co zapewnia lepszą wartość propozycji dla większości obciążeń ogólnego przeznaczenia, a EV3 do wyrównania z maszynami wirtualnymi ogólnego przeznaczenia większości innych chmur.  Pamięć została rozszerzona (od 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane dla poszczególnych rdzeni, aby dostosować je do przenoszenia do wielowątkowości.  EV3 to kolejne rozmiary maszyn wirtualnych o dużej ilości pamięci w rodzinach D/Dv2.

* Serii Eav3 i Easv3 wykorzystują procesor AMD 2.35 GHz EPYC<sup>TM</sup> 7452V w konfiguracji wielowątkowej z maksymalnie 256GBą pamięcią podręczną L3, zwiększając opcje uruchamiania większości zoptymalizowanych pod kątem pamięci.  Serie Eav3 i Easv3 mają takie same konfiguracje pamięci i dysków jak seria EV3 & Esv3.

* Seria Mv2 oferuje najwyższą liczbę vCPU (do 208 procesorów wirtualnych vCPU) i największą ilość pamięci (do 5,7 TiB) dowolnej maszyny wirtualnej w chmurze. Jest to idealne rozwiązanie dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby procesorów wirtualnych vCPU i dużych ilości pamięci.

* Seria M oferuje dużą liczbę vCPU (do 128 procesorów wirtualnych vCPU) i dużą ilość pamięci (do 3,8 TiB). Jest on również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby vCPU i dużych ilości pamięci.

* Dv2 serii, G i DSv2/GS są idealnym rozwiązaniem w przypadku aplikacji wymagających szybszego procesorów wirtualnych vCPU, lepszej wydajności magazynu i większej ilości pamięci. Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.

* Seria Dv2, kontynuacja oryginalnej serii D, jest wyposażona w procesor CPU o większych możliwościach. Procesor CPU serii Dv2 jest o około 35% szybszy niż procesor CPU serii D. Jest ona oparta na procesorach Intel 2,4® Xeon o częstotliwości 2,4 GHz (Haswell) lub E5-2673 v4 2,3 GHz (Broadwell) z 2,0 technologią Intel Turbo, która pozwala uzyskać do 3,1 GHz. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

* Usługa Azure Compute oferuje różne rozmiary maszyn wirtualnych, które są izolowane pod kątem konkretnego rodzaju sprzętu i przeznaczone dla jednego klienta.  Te rozmiary maszyn wirtualnych są dostosowane do obciążeń wymagających wysokiego stopnia izolacji od innych klientów, a zwłaszcza obciążeń związanych z takimi elementami jak zgodność z przepisami i wymogi prawne.  Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Zapoznaj się z tabelami poniższych rodzin maszyn wirtualnych dla opcji izolowanych maszyn wirtualnych.

## <a name="esv3-series"></a>Seria Esv3

ACU 160-190 <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Wystąpienia serii ESv3 są oparte na procesorach Intel XEON® E5-2673 v4 (Broadwell) z zegarem 2,3 GHz, które dzięki technologii Intel Turbo Boost 2.0 mogą osiągnąć częstotliwość 3,5 GHz i korzystają z magazynu Premium Storage. Wystąpienia serii Ev3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.


| Size             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
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

## <a name="easv3-series"></a>Seria Easv3

Magazyn w warstwie Premium: Obsługiwane

Buforowanie Premium Storage: Obsługiwane

Rozmiary serii Easv3 są oparte na procesorze 7452V 2.35 GHz AMD EPYC<sup>TM</sup> , który może osiągnąć podwyższenie poziomu Fmax — GHz i korzystać z magazynu Premium Storage. Rozmiary serii Easv3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Seria Ev3 

ACU 160 – 190 <sup>1</sup>

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

Wystąpienia serii Ev3 są oparte na procesorach Intel XEON® E5-2673 v4 (Broadwell) 2,3 GHz i technologią Intel Turbo Boost 2.0, dzięki której mogą osiągnąć częstotliwość 3,5 GHz. Wystąpienia serii Ev3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów ESv3. Liczniki cen i rozliczeń dla rozmiarów ESv3 są takie same jak dla serii Ev3. 


| Size            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba kart sieciowych / przepustowość sieci |
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

## <a name="eav3-series"></a>Seria Eav3

Magazyn w warstwie Premium: Brak obsługi

Buforowanie Premium Storage: Brak obsługi

Rozmiary serii Eav3 są oparte na procesorze 7452V 2.35 GHz AMD EPYC<sup>TM</sup> , który może osiągnąć podwyższenie poziomu Fmax — GHz i korzystać z magazynu Premium Storage. Rozmiary serii Eav3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci. Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów serii Easv3. Liczniki cen i rozliczeń dla rozmiarów Easv3 są takie same jak dla serii Eav3.

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB |
|---|---|---|---|---|---|
| Standard_E2a_v3  | 2  | 16  | 50   |
| Standard_E4a_v3  | 4  | 32  | 100  |
| Standard_E8a_v3  | 8  | 64  | 200  |
| Standard_E16a_v3 | 16 | 128 | 400  |
| Standard_E32a_v3 | 32 | 256 | 800  |
| Standard_E48a_v3 | 48 | 384 | 1200 |
| Standard_E64a_v3 | 64 | 432 | 1600 |

## <a name="mv2-series"></a>Seria Mv2

Magazyn w warstwie Premium: Obsługiwane

Buforowanie Premium Storage: Obsługiwane

Akcelerator zapisu: [Obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Funkcja serii Mv2 ma wysoką przepływność, małe opóźnienia, bezpośrednio mapowane lokalne magazyny interfejsu NVMe działające w procesorze Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) z podstawową częstotliwością bazową wynoszącą 2,5 GHz i maksymalną częstotliwość Turbo 3,8 GHz. Wszystkie rozmiary maszyn wirtualnych z serii Mv2 mogą korzystać z dysków trwałych w warstwach Standardowa i Premium. Wystąpienia serii Mv2 to rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci, które zapewniają niezrównaną wydajność obliczeniową w celu obsługi dużych baz danych i obciążeń w pamięci, dzięki czemu jest idealnym rozwiązaniem z dużą ilością pamięci w przypadku serwerów relacyjnych baz danych, dużych pamięci podręcznych i w pamięci wersję. 

|Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |

Funkcja maszyny wirtualnej z serii Mv2 Intel® technologia wielowątkowości funkcji Hyper-Threading  

<sup>1</sup> te duże maszyny wirtualne wymagają jednego z następujących obsługiwanych systemów operacyjnych gościa: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> maszyny wirtualne z serii Mv2 są tylko 2 generacji. Jeśli używasz systemu Linux, zapoznaj się z sekcją jak znaleźć i wybrać obraz systemu SUSE Linux.

#### <a name="find-a-suse-image"></a>Znajdowanie obrazu SUSE

Aby wybrać odpowiedni obraz SUSE Linux w Azure Portal: 

1. W Azure Portal wybierz pozycję **Utwórz zasób** . 
1. Wyszukaj frazę "SUSE SAP" 
1. SLES for SAP Generation 2 są dostępne jako płatność zgodnie z rzeczywistym użyciem lub w ramach subskrypcji (BYOS). W wynikach wyszukiwania rozwiń żądaną kategorię obrazu:

    * SUSE Linux Enterprise Server (SLES) dla oprogramowania SAP
    * SUSE Linux Enterprise Server (SLES) dla oprogramowania SAP (BYOS)
    
1. Obrazy SUSE zgodne z seriami Mv2 są poprzedzone nazwą `GEN2:`. Następujące obrazy SUSE są dostępne dla maszyn wirtualnych z serii Mv2:

    * GEN2 SUSE Linux Enterprise Server (SLES) 12 z dodatkiem SP4 dla aplikacji SAP
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 dla aplikacji SAP
    * GEN2 SUSE Linux Enterprise Server (SLES) 12 z dodatkiem SP4 dla aplikacji SAP (BYOS)
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 for SAP Applications (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Wybierz obraz SUSE za pośrednictwem interfejsu wiersza polecenia platformy Azure

Aby wyświetlić listę dostępnych obecnie SLES dla obrazów SAP dla maszyn wirtualnych z serii Mv2, użyj następującego [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) polecenia:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Polecenie wyprowadza dostępne maszyny wirtualne 2. generacji dostępne z programu SUSE dla maszyn wirtualnych z serii Mv2. 

Przykładowe dane wyjściowe:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>Seria M 

ACU 160-180 <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Akcelerator zapisu:  [Obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
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
| Standard_M128&nbsp;2<sup>  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;2<sup> | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> funkcja maszyny wirtualnej z serii M Intel® technologia wielowątkowości funkcji Hyper-Threading

<sup>2</sup> więcej niż 64 vCPU wymaga jednego z tych obsługiwanych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 z dodatkiem SP2 i Red Hat Enterprise Linux, CentOS 7,3 lub Oracle Linux 7,3 z podsystemem LIS 4.2.1.

dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.

<sup>4</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 — Seria 11-15

ACU 210 – 250 <sup>1</sup>

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej serii DSv2 może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.  
dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.  
<sup>4</sup> 25000 MB/s z przyspieszoną siecią. 

<br>

## <a name="dv2-series-11-15"></a>Dv2 — Seria 11-15

ACU 210 – 250

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size              | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standardowa_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standardowa_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standardowa_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.  
<sup>2</sup> 25000 MB/s z przyspieszoną siecią. 
