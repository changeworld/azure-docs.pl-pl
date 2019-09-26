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
ms.openlocfilehash: 5d89feaa5225aea56af86aa7f70d3666994fdaec
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266895"
---
Rozmiary maszyn wirtualnych ogólnego przeznaczenia zapewniają zrównoważony współczynnik procesora CPU do pamięci. Idealne na potrzeby testowania i programowania, małych i średnich baz danych oraz serwerów sieci Web o niewielkim i średnim ruchu. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart sieciowych, a także przepływności magazynu dla rozmiarów w tym grupowaniu.

- [Seria DC](#dc-series) jest rodziną maszyn wirtualnych na platformie Azure, które mogą pomóc w ochronie poufności i integralności danych oraz kodu podczas przetwarzania w chmurze publicznej. Te maszyny korzystają z najnowszej generacji procesora Intel XEON E-2176G 3,7GHz z technologią SGX. Dzięki technologii Intel Turbo Boost Technology te maszyny mogą osiągać szybkość do 4,7 GHz. Wystąpienia z serii DC umożliwiają klientom tworzenie aplikacji opartych na bezpiecznych enklawach do ochrony kodu i danych w trakcie ich używania.

- Maszyny wirtualne z serii Av2 można wdrażać na różnych typach sprzętu i procesorach. Maszyny wirtualne serii A zapewniają wydajność procesora CPU i konfiguracje pamięci, które są najlepsze w przypadku obciążeń podstawowych, takich jak tworzenie i testowanie aplikacji. Rozmiar jest ograniczany w zależności od sprzętu, aby zapewnić spójną wydajność procesora dla uruchomionego wystąpienia niezależnie od sprzętu, na którym jest ono wdrożone. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej.

  Przykładowe przypadki użycia obejmują serwery deweloperskie i testowe, serwery sieci Web o małym ruchu, małe i średnie bazy danych, sprawdzanie poprawności koncepcji i repozytoria kodu.

- Seria Dv2, która jest zgodna z oryginalną serią D, oferuje bardziej wydajny procesor i optymalną konfigurację procesora CPU i pamięci, które są odpowiednie dla większości obciążeń produkcyjnych. Procesor CPU serii Dv2 jest o około 35% szybszy niż procesor CPU serii D. Jest on oparty na najnowszej generacji procesorów Intel Xeon® E5-2673 v3 2,4 GHz (Haswell) lub E5-2673 v4 2,3 GHz (Broadwell), a z technologią Intel Turbo Generation 2,0, można przejść do 3,1 GHz. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

- Seria Dv3 oferuje procesor Intel Xeon® E5-2673 v3 (Haswell) z 2,4 GHz lub 2,3 najnowszy procesor Intel XEON® E5-2673 v4 (Broadwell) w konfiguracji wielowątkowej, co zapewnia lepszą wartość dla większości obciążeń ogólnego przeznaczenia.  Pamięć została rozszerzona (od ~ 3,5 GiB/vCPU do 4 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane w oparciu o rdzeń, aby dostosować je do przechodzenia do wielowątkowości.  Dv3 nie ma już rozmiaru maszyn wirtualnych z rodziną D/Dv2, które zostały przeniesione do nowej rodziny EV3.

  Przykładowe przypadki użycia serii D obejmują aplikacje klasy korporacyjnej, relacyjne bazy danych, buforowanie w pamięci i analizy.

- Serii da i Dasv3 są nowe rozmiary wykorzystujące procesor AMD 2.35 GHz EPYC<sup>TM</sup> 7452V w konfiguracji wielowątkowej z maksymalnie 256 GB pamięci podręcznej L3 o pojemności 8 GB w tej pamięci podręcznej L3 do każdego 8 rdzeni, które zwiększają opcje klienta na potrzeby wykonywania ich ogólnego obciążenia przeznaczenie. Seria da i Dasv3 mają takie same konfiguracje pamięci i dysków jak seria D & Dsv3.
  
## <a name="b-series"></a>Seria B

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Brak obsługi

Maszyny wirtualne z serii B są idealne dla obciążeń, które nie wymagają pełnej wydajności procesora CPU, takich jak serwery sieci Web, małe bazy danych i środowiska deweloperskie i testowe. Te obciążenia zwykle mają wymagania dotyczące wydajności. Seria B zapewnia tym klientom możliwość kupowania rozmiaru maszyny wirtualnej przy użyciu ceny linii bazowej, która pozwala na wystąpienie maszyny wirtualnej do kompilowania kredytów, gdy maszyna wirtualna korzysta z mniejszej niż jej wydajność podstawowa. Po rozliczeniu na maszynę wirtualną, maszyna wirtualna może przekroczyć obciążenie maszyny wirtualnej za pomocą do 100% procesora, gdy aplikacja wymaga wyższych wydajności procesora CPU.

Przykładowe przypadki użycia obejmują serwery deweloperskie i testowe, serwery sieci Web o niewielkim ruchu, małe bazy danych, mikrousługi, serwery do sprawdzania koncepcji, serwery kompilacji.


| Size             | Procesor wirtualny  | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Podstawowa wydajność procesora CPU maszyny wirtualnej | Maksymalna wydajność procesora CPU maszyny wirtualnej | Początkowe środki | Zapisane środki/godz. | Maksymalna ilość zapisanych środków | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: IOPS / MB/s | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200/10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480/75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640/100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls jest obsługiwany tylko w systemie Linux

## <a name="dsv3-series-sup1sup"></a>Dsv3 — seria <sup>1</sup>

ACU 160-190

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

Rozmiary serii Dsv3 są oparte na procesorach Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 najnowszej 2,4 GHz Intel XEON® E5-2673 v4 (Broadwell), dzięki czemu można osiągnąć 3,5 GHz z technologią Intel Turbo — rozwiązanie 2,0 i korzystać z magazynu Premium Storage. Rozmiary serii Dsv3 oferują kombinację procesora wirtualnego vCPU, pamięci i magazynu tymczasowego spełniającą potrzeby większości obciążeń produkcyjnych.


| Size             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standardowa_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                      |
| Standardowa_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16 000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800/1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000/1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> funkcja maszyny wirtualnej z serii Dsv3 Intel® technologia wielowątkowości funkcji Hyper-Threading

## <a name="dasv3-series-preview"></a>Dasv3 — seria (wersja zapoznawcza)

Magazyn w warstwie Premium: Obsługiwane

Buforowanie Premium Storage: Obsługiwane

Rozmiary serii Dasv3 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć podwyższenie poziomu Fmax — 3.35 GHz i korzystać z magazynu Premium Storage. Rozmiary serii Dasv3 oferują kombinację vCPU, pamięci i magazynu tymczasowego dla większości obciążeń produkcyjnych.

[Kliknij tutaj, aby utworzyć konto w wersji zapoznawczej](http://aka.ms/azureamdpreview).

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB |
|---|---|---|---|
| Standard_D2as_v3  | 2  | 8   | 16  |
| Standard_D4as_v3  | 4  | 16  | 32  |
| Standard_D8as_v3  | 8  | 32  | 64  |
| Standard_D16as_v3 | 16 | 64  | 128 |
| Standard_D32as_v3 | 32 | 128 | 256 |
| Standard_D48as_v3 | 48 | 192 | 384 |
| Standard_D64as_v3 | 64 | 256 | 512 |

## <a name="dv3-series-sup1sup"></a>Dv3 — seria <sup>1</sup>

ACU 160-190

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

Rozmiary serii Dv3 są oparte 2,4 na procesorach Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell), które mogą osiągnąć 3,5 GHz z technologią Intel Turbo do wzbudzenia 2,0. Rozmiary serii Dv3 oferują kombinację procesora wirtualnego vCPU, pamięci i magazynu tymczasowego spełniającą potrzeby większości obciążeń produkcyjnych.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów Dsv3. Liczniki cen i rozliczeń dla rozmiarów Dsv3 są takie same jak dla serii Dv3. 


| Rozmiar            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba kart sieciowych / przepustowość sieci |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standardowa_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Maszyna wirtualna Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standardowa_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standardowa_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16 000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> funkcja maszyny wirtualnej z serii Dv3 Intel® technologia wielowątkowości funkcji Hyper-Threading

## <a name="dav3-series-preview"></a>Dav3 — seria (wersja zapoznawcza)

Magazyn w warstwie Premium: Brak obsługi

Buforowanie Premium Storage: Brak obsługi

Rozmiary serii Dav3 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć Fmax — o częstotliwości 3.35 GHz. Rozmiary serii Dav3 oferują kombinację vCPU, pamięci i magazynu tymczasowego dla większości obciążeń produkcyjnych. Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów Dasv3. Liczniki cen i rozliczeń dla rozmiarów Dasv3 są takie same jak dla serii Dav3.

[Kliknij tutaj, aby utworzyć konto w wersji zapoznawczej](http://aka.ms/azureamdpreview).

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB |
|---|---|---|---|
| Standard_D2a_v3  | 2  | 8   | 50   |
| Standard_D4a_v3  | 4  | 16  | 100  |
| Standard_D8a_v3  | 8  | 32  | 200  |
| Standard_D16a_v3 | 16 | 64  | 400  |
| Standard_D32a_v3 | 32 | 128 | 800  |
| Standard_D48a_v3 | 48 | 192 | 1200 |
| Standard_D64a_v3 | 64 | 256 | 1600 |

## <a name="dsv2-series"></a>Seria DSv2

ACU 210-250

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Size | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS1_v2 |1 |3,5 |7 |4 |4000/32 (43) |3200/48 |2 / 750 |
| Standardowa_DS2_v2 |2 |7 |14 |8 |8000/64 (86) |6400/96 |2 / 1500 |
| Standardowa_DS3_v2 |4 |14 |28 |16 |16000/128 (172) |12800/192 |4 / 3000 |
| Standardowa_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600/384 |8 / 6000 |
| Standardowa_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Seria Dv2

ACU 210-250

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi

| Size           | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maks. liczba dysków danych | Kazany IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standardowa_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4 x 500            | 2 / 750                                      |
| Maszyna wirtualna Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8 x 500            | 2 / 1500                                     |
| Maszyna wirtualna Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16 x 500           | 4 / 3000                                       |
| Standardowa_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32 x 500           | 8 / 6000                                       |
| Standardowa_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Seria Av2

ACU 100

Magazyn w warstwie Premium:  Brak obsługi

Buforowanie Premium Storage:  Brak obsługi


| Size            | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: Liczba IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standardowa_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standardowa_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standardowa_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |
| Standardowa_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2000                     |
| Standardowa_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standardowa_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |
| Standardowa_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2000                     |

## <a name="dc-series"></a>Seria DC

Magazyn w warstwie Premium: Obsługiwane

Buforowanie Premium Storage: Obsługiwane



| Size          | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |
