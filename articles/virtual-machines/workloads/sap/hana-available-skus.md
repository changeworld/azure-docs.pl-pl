---
title: Jednostki SKU dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jednostki SKU dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b80f872c82061c0cb87f4f1e2714183e71cf02cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794039"
---
# <a name="available-skus-for-hli"></a>Dostępne jednostki SKU dla oprogramowania SAP HANA na platformie Azure — duże wystąpienia

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) usługi jest dostępny w wielu konfiguracji w regionach platformy Azure zachodnie stany USA i wschodnie stany USA, Australia Wschodnia, Australia Południowo-Wschodnia, Europa Zachodnia, Europa Północna, Japonia, część wschodnia i Japonia, część zachodnia.

[Duże wystąpienia jednostki SKU HANA z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) listy, takich jak:

| Rozwiązanie SAP | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Optymalizacja pod kątem OLAP: SAP BW, BW/4HANA<br /> lub SAP HANA dla ogólnych obciążeń OLAP | Oprogramowanie SAP HANA na platformie Azure S72<br /> — 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora CPU i 72 wątki procesora CPU |  768 GB |  3 TB | Nie jest już dostępna |
| --- | Oprogramowanie SAP HANA na platformie Azure S144<br /> — 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzeni procesora CPU i 144 wątki procesora CPU |  1,5 TB |  6 TB | Nie jest już dostępna |
| --- | Oprogramowanie SAP HANA na platformie Azure S192<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora CPU |  W WERSJI 2.0 TB |  8 TB | Dostępne |
| --- | Oprogramowanie SAP HANA na platformie Azure S384<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  4.0 TB |  16 TB | Dostępne |
| Optymalizacja pod kątem OLTP: SAP Business Suite<br /> SAP HANA lub S/4HANA (OLTP)<br /> Ogólne OLTP | Oprogramowanie SAP HANA na platformie Azure S72m<br /> — 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora CPU i 72 wątki procesora CPU |  1,5 TB |  6 TB | Nie jest już dostępna |
|---| Oprogramowanie SAP HANA na platformie Azure S144m<br /> — 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzeni procesora CPU i 144 wątki procesora CPU |  3.0 TB |  12 TB | Nie jest już dostępna |
|---| Oprogramowanie SAP HANA na platformie Azure S192m<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora CPU  |  4.0 TB |  16 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S384m<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  W WERSJI 6.0 TB |  18 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S384xm<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  8.0 TB |  22 TB |  Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S576m<br /> — 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora CPU |  12.0 TB |  28 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S768m<br /> — 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora CPU |  16.0 TB |  36 TB | Dostępne |
|---| Oprogramowanie SAP HANA na platformie Azure S960m<br /> — 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 rdzeni procesora CPU i 960 wątki procesora CPU |  20.0 TB |  46 TB | Dostępne |


W obszarze TDIv5 HANA SAP SAP umożliwia skonfigurowanie rozmiaru właściwe dla klienta i projektów specyficznych dla klientów, które mogą prowadzić do konfiguracji serwera, które nie są wymienione jako certyfikat w procesie:

- [Oprogramowanie SAP HANA certyfikowane urządzenia](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Platform IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

W wielu przypadkach te konfiguracje specyficzne dla klienta serwera wykonać więcej pamięci niż jednostek serwera, certyfikat z oprogramowaniem SAP. W pracy z SAP, klienci mają możliwość uzyskanie pomocy technicznej SAP, a także zaświadczasz, w przypadku ich konfiguracji serwera wielkości właściwe dla klienta. Na platformie Azure następujące dużych wystąpień HANA standardowej jednostki SKU są dostępne, a w programie Microsoft cennik dla projektów TDIv5 rozmiaru właściwe dla klienta.

| SKU|Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| ---| --- | --- | --- | --- |
| S96 | Oprogramowanie SAP HANA na S96 platformy Azure<br /> — 2 x procesor Intel® Xeon® E7-8890 v4<br /> 48 rdzeni procesora CPU i 96 wątki procesora CPU |  768 GB |  3 TB | Dostępne |


| Oryginalnej jednostki SKU, które mogą być <br /> rozszerzony w pamięci | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Platformie S192m może zostać rozszerzony do | Oprogramowanie SAP HANA na S192xm platformy Azure<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora CPU |  W WERSJI 6.0 TB |  16 TB | Dostępne |
| S384xm może zostać rozszerzony do | Oprogramowanie SAP HANA na S384xxm platformy Azure<br /> — 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora CPU |  12.0 TB |  28 TB | Dostępne |
| S576m może zostać rozszerzony do | Oprogramowanie SAP HANA na S576xm platformy Azure<br /> — 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora CPU |  WERSJACH 18.0 TB |  41 TB | Dostępne |
| S768m może zostać rozszerzony do | Oprogramowanie SAP HANA na S768xm platformy Azure<br /> — 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora CPU |  24.0 TB |  56 TB | Dostępne |

- Liczba rdzeni procesora CPU = suma nie hyper Threading rdzeni procesora CPU sumy liczby procesorów jednostki serwera.
- Wątki procesora CPU = Suma wątków obliczeniowe udostępniane przez hyper Threading rdzeni procesora CPU sumy liczby procesorów jednostki serwera. Większość jednostek są domyślnie skonfigurowane, aby użyć technologię hiperwątkowości.
- Na podstawie dostawcy zaleceń S768m, S768xm i S960m nie są skonfigurowane do użycia funkcji Hyper-Threading na potrzeby uruchamiania oprogramowania SAP HANA.


Konkretne konfiguracje wybrane są zależne od obciążenia, zasobów procesora CPU i pamięci żądaną. Jest możliwe w przypadku obciążeń OLTP do użycia jednostek SKU, które są zoptymalizowane na potrzeby obciążeń OLAP. 

Podstawowy dla oferty, z wyjątkiem jednostki dla projektów właściwe dla klienta, ustalanie rozmiaru sprzętu są z certyfikatem TDI i programu SAP HANA. Dwoma różnymi klasami sprzętu Podziel jednostki SKU do:

- S72, S72m, S96, S144, S144m, platformie S192, platformie S192m i S192xm, które są określane jako "Type I klasy" jednostek SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm i S960m, które są określane jako "Typ klasy II" jednostek SKU.

Pełną sygnaturę dużych wystąpień HANA nie jest przeznaczona wyłącznie przydzielone dla jednego klienta&#39;s użycia. Fakt ten dotyczy regałów zasoby obliczeniowe i magazynowe, które są połączone za pośrednictwem sieci szkieletowej wdrożonych na platformie Azure, jak również. Duże wystąpienie oprogramowania HANA infrastruktury, takich jak Azure, wdraża różnych klientów &quot;dzierżaw&quot; , które są odizolowane od siebie nawzajem w następujących trzech poziomach:

- **Sieć**: Izolacja za pośrednictwem sieci wirtualnych w ramach sygnatury dużych wystąpień HANA.
- **Magazyn**: Izolacja za pośrednictwem magazynu maszyn wirtualnych, które mają woluminy magazynu przypisana, a izolowania woluminów magazynu między dzierżawami.
- **Obliczenia**: Dedykowane przypisanie jednostek serwera do pojedynczej dzierżawy. Nie trudne lub nietrwałe partycjonowanie jednostek serwera. Bez udostępniania pojedynczego serwera lub hosta jednostka między dzierżawami. 

Wdrażanie dużych wystąpień HANA jednostek między różnych dzierżawach nie są widoczne ze sobą. Jednostki dużych wystąpień HANA wdrożonych w różnych dzierżawach nie mogą komunikować się bezpośrednio ze sobą na poziomie sygnatury dużych wystąpień HANA. Tylko dużych wystąpień HANA jednostek w ramach jednej dzierżawy mogą komunikować się ze sobą na poziomie sygnatury dużych wystąpień HANA.

Dzierżawcy wdrożone w sygnaturze duże wystąpienie jest przypisany do jednej subskrypcji platformy Azure na potrzeby rozliczeń. Dla sieci może on dostępny z sieci wirtualnych z innych subskrypcji platformy Azure w ramach tej samej rejestracji na platformie Azure. W przypadku wdrożenia przy użyciu innej subskrypcji platformy Azure, w tym samym regionie platformy Azure, również możesz poprosić o rozdzielonych dzierżawy dużych wystąpień HANA.

Istnieją znaczne różnice między systemem SAP HANA na dużych wystąpień HANA lub SAP HANA, działające na maszynach wirtualnych wdrożonych na platformie Azure:

- Nie ma żadnych warstwa wirtualizacji dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia). Możesz uzyskać wydajność bazowego sprzętu bez systemu operacyjnego.
- W przeciwieństwie do platformy Azure SAP HANA na platformie Azure (duże wystąpienia) serwera jest dedykowany dla określonego klienta. Istnieje możliwość, jednostki serwera lub hosta jest trudne lub nietrwałe podzielona na partycje. W rezultacie duże wystąpienie oprogramowania HANA jest używany przypisany jako całość do dzierżawy i który do Ciebie. Ponowne uruchomienie lub zamknięcie serwera nie automatycznie prowadzić do systemu operacyjnego i oprogramowania SAP HANA wdrażane na innym serwerze. (Dla typu klasy I jednostki SKU, jedynym wyjątkiem jest, jeśli serwer napotyka problemy i ponownego wdrożenia komputera musi zostać wykonana na innym serwerze.)
- W przeciwieństwie do platformy Azure, w których typy procesorów hosta są wybrane dla najlepsze stosunek ceny do wydajności, typy procesorów, wybrany dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) są najwyższy wykonywania wierszu procesor Intel E7v3 i E7v4.

**Następne kroki**
- Zapoznaj się [HLI zmiany rozmiaru](hana-sizing.md)
