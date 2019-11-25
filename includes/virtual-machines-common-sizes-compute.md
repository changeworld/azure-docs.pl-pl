---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 21b982389b186e949b21352f4b11bd6b4aa06dcb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279202"
---
<!-- F-series, Fs-series* -->

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń mają duży stosunek procesora CPU do pamięci. Te rozmiary są dobre dla serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart interfejsu sieciowego. Zawiera również informacje o przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

Seria Fsv2 jest oparta na procesorze Intel® Xeon® Platinum 8168. Oferuje on stałą prędkość zegara Turbo o częstotliwości 3,4 GHz i maksymalną jednordzeniową częstotliwość Turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w skalowalnych procesorach Intel. Te instrukcje umożliwiają zwiększenie wydajności do obciążeń przetwarzania wektorowego zarówno w przypadku operacji zmiennoprzecinkowych o pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie w przypadku obciążeń obliczeniowych.

Na niższą cenę za godzinę cennika seria Fsv2 jest najlepszą wartością wydajności w portfolio Azure w oparciu o jednostkę obliczeniową platformy Azure (ACU) na vCPU.

## <a name="fsv2-series-sup1sup"></a>Fsv2 — seria <sup>1</sup>

ACU: 195 – 210

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar             | Procesorów wirtualnych vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127 (128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255 (256)        | 25600/380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800/1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024 (1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000/1152 (1520)     | 80000/1100             | 8 / 30000              |

<sup>1</sup> maszyny wirtualne serii Fsv2 z technologią Hyper-Threading firmy Intel®.

<sup>2</sup> użycie więcej niż 64 vCPU wymaga jednego z obsługiwanych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 i Red Hat Enterprise Linux, CentOS 7,3 lub Oracle Linux 7,3 z prze4.2.1.

<sup>3</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
