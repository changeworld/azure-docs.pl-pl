---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ec718449e9f9db3f816d327f3d2483de813d755c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391274"
---
<!-- F-series, Fs-series* -->

Obliczenia zoptymalizowane rozmiarów maszyn wirtualnych mają wysoki współczynnik procesora CPU w stosunku do pamięci i są odpowiednie dla serwerów sieci web lub średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, w przypadku dysków z danymi i kart sieciowych, a także przepustowość przepływności i sieć magazynu dla każdego rozmiaru w tej metodzie grupowania.

Fsv2 — seria jest oparta na procesorze Intel® Xeon® Platinum 8168, oferujący funkcje stałego wszystkich głównych prędkość zegara Turbo 3.4 GHz oraz częstotliwości maksymalna turbo jednordzeniowy 3,7 GHz. Instrukcje Intel® AVX-512, będące nową funkcją w skalowalnych procesory Intel, będzie udostępniać maksymalnie 2 X zwiększeniu wydajności obciążeń przetwarzania wektora na pojedyncze i Podwójna precyzja operacji zmiennoprzecinkowych. Innymi słowy są one naprawdę szybkiej w przypadku wszystkich obciążeń obliczeniowych. 

Przy niższej cenie listy za godzinę seria Fsv2 jest wartość najlepsze ceny do wydajności w portfolio platformy Azure, oparta na platformie Azure obliczenia jednostki (ACU) na każdy procesor wirtualny.

## <a name="fsv2-series-sup1sup"></a>Seria Fsv2 <sup>1</sup>

ACU: 195 - 210

Magazyn w warstwie Premium:  Obsługiwane

Buforowanie Premium Storage:  Obsługiwane

| Rozmiar             | procesory wirtualne | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: Operacje We/Wy / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: IOPS / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |


<sup>1</sup> maszyny Wirtualne serii Fsv2 są wyposażone w technologię Intel® Hyper-Threading

<sup>2</sup> więcej niż 64 procesory wirtualne wymagają jednego z tych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 z dodatkiem SP2 i Red Hat Enterprise Linux, CentOS 7.3 lub Oracle Linux 7.3 z usługami LIS 4.2.1

<sup>3</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.