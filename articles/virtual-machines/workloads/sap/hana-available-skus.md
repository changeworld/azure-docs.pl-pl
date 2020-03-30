---
title: Jednostki SKU dla sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Jednostki SKU dla sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617261"
---
# <a name="available-skus-for-hli"></a>Dostępne jednostki SKU dla oprogramowania SAP HANA na platformie Azure — duże wystąpienia

Usługa SAP HANA na platformie Azure (duże wystąpienia) oparta na pieczęciach wersji 3 jest dostępna w kilku konfiguracjach w regionach platformy Azure:

- Zachodnie stany USA
- Wschodnie stany USA
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Europa Zachodnia
- Europa Północna
- Japonia Wschodnia
- Japonia Zachodnia

Usługa SAP HANA na platformie Azure (duże wystąpienia) oparta na pieczyciach wersji 4 jest dostępna w kilku konfiguracjach w regionach platformy Azure:

- Zachodnie stany USA 2
- Wschodnie stany USA
- Południowo-środkowe stany USA
- Europa Zachodnia
- Europa Północna



[Sap HANA certyfikowanych jednostek SKU hana dużych wystąpień](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) listy, takich jak:

| Rozwiązanie SAP | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Zoptymalizowany pod kątem OLAP: SAP BW, BW/4HANA<br /> lub SAP HANA dla ogólnego obciążenia OLAP | SAP HANA na platformie Azure S72<br /> – 2 x Procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora i 72 wątki procesora |  768 GB |  3 TB | Nie są już oferowane |
| --- | SAP HANA na platformie Azure S144<br /> – 4 x Procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzenie procesora i 144 wątki procesora |  1,5 TB |  6 TB | Nie są już oferowane |
| --- | SAP HANA na platformie Azure S192<br /> – 4 x Procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora i 192 wątki procesora |  2,0 TB |  8 TB | Nie są już oferowane |
| --- | SAP HANA na platformie Azure S224<br /> – 4 x Procesor Intel® Xeon® Platinum 8276 (znany również jako Cascade lake)<br /> 112 rdzeni procesora i 224 wątki procesora |  3,0 TB |  6,3 TB | Dostępne w znaczkach Revision3 i Revision4  |
| --- | SAP HANA na platformie Azure S384<br /> – 8 x Procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora i 384 wątki procesora |  4,0 TB |  16 TB | Dostępne w znaczkach Revision4 |
| Zoptymalizowany pod kątem OLTP: SAP Business Suite<br /> na SAP HANA lub S/4HANA (OLTP),<br /> ogólny OLTP | SAP HANA na platformie Azure S72m<br /> – 2 x Procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora i 72 wątki procesora |  1,5 TB |  6 TB | Nie są już oferowane |
|---| SAP HANA na platformie Azure S144m<br /> – 4 x Procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzenie procesora i 144 wątki procesora |  3,0 TB |  12 TB | Nie są już oferowane |
|---| SAP HANA na platformie Azure S192m<br /> – 4 x Procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora i 192 wątki procesora  |  4,0 TB |  16 TB | Nie są już oferowane |
| --- | SAP HANA na platformie Azure S224m<br /> – 4 x Procesor Intel® Xeon® Platinum 8276 (znany również jako Cascade lake)<br /> 112 rdzeni procesora i 224 wątki procesora |  6,0 TB |  10,5 TB | Dostępne w znaczkach Revision3 i Revision4  |
|---| SAP HANA na platformie Azure S384m<br /> – 8 x Procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora i 384 wątki procesora |  6,0 TB |  18 TB | Dostępne w znaczkach Revision4|
|---| SAP HANA na platformie Azure S384xm<br /> – 8 x Procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora i 384 wątki procesora |  8,0 TB |  22 TB |  Dostępne w znaczkach Revision4 |
|---| SAP HANA na platformie Azure S576m<br /> – 12 x Procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora i 576 wątków procesora |  12,0 TB |  28 TB | Dostępne w znaczkach Revision4|
|---| SAP HANA na platformie Azure S768m<br /> – 16 x Procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzenie procesora i 768 wątków procesora |  16,0 TB |  36 TB | Dostępne w znaczkach Revision4|
|---| SAP HANA na platformie Azure S960m<br /> – 20 x Procesor Intel® Xeon® E7-8890 v4<br /> 480 rdzeni procesora i 960 wątków procesora |  20,0 TB |  46 TB | Dostępne w znaczkach Revision4|


W systemie SAP HANA TDIv5 sap umożliwia doprowadzenie do rozmiaru i projekty specyficzne dla klienta, co może prowadzić do konfiguracji serwera, które nie są wymienione jako certyfikowane w:

- [Urządzenia z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Platformy IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

W wielu przypadkach te konfiguracje serwerów specyficzne dla klienta zawierają więcej pamięci niż jednostki serwerowe certyfikowane przez sap. Podczas pracy z sap, klienci mają możliwość uzyskania pomocy technicznej SAP i certyfikacji dla ich konfiguracji serwerów specyficznych dla klienta. Na platformie Azure dostępne są następujące standardowe jednostki SKU dużych instancji HANA oraz w cenniku firmy Microsoft dla takich projektów rozmiaru specyficznych dla klienta TDIv5.

| SKU|Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA na platformie Azure S96<br /> – 2 x Procesor Intel® Xeon® E7-8890 v4<br /> 48 rdzeni procesora i 96 wątków procesora |  768 GB |  3 TB | Dostępne w znaczkach Revision3 i Revision4|


| Oryginalna jednostka SKU, którą można <br /> rozszerzony w pamięci | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| S192m można rozszerzyć na | SAP HANA na platformie Azure S192xm<br /> – 4 x Procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora i 192 wątki procesora |  6,0 TB |  16 TB | Nie są już oferowane |
| S384xm można rozszerzyć na | SAP HANA na platformie Azure S384xxm<br /> – 8 x Procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora i 384 wątki procesora |  12,0 TB |  28 TB | Dostępne w znaczkach Revision4 |
| S576m można rozszerzyć na | SAP HANA na platformie Azure S576xm<br /> – 12 x Procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora i 576 wątków procesora |  18.0 TB |  41 TB | Dostępne w znaczkach Revision4|
| S768m można rozszerzyć na | SAP HANA na platformie Azure S768xm<br /> – 16 x Procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzenie procesora i 768 wątków procesora |  24,0 TB |  56 TB | Dostępne w znaczkach Revision4 |

- Rdzenie procesora = suma nie hiperwątkowardzeniowych rdzeni procesora sumy procesorów jednostki serwerowej.
- Wątki procesora CPU = suma wątków obliczeniowych dostarczanych przez hiperwątkowe rdzenie procesora sumy procesorów jednostki serwerowej. Większość jednostek jest domyślnie skonfigurowana do używania technologii hyper-threading.
- Na podstawie zaleceń dostawców S768m, S768xm i S960m nie są skonfigurowane do używania hyper-threading do uruchamiania SAP HANA.


Wybrane konfiguracje zależą od obciążenia, zasobów procesora i żądanej pamięci. Jest możliwe dla obciążenia OLTP do korzystania z jednostek SKU, które są zoptymalizowane pod kątem obciążenia OLAP. 

Baza sprzętowa dla ofert, z wyjątkiem jednostek dla projektów rozmiarów specyficznych dla klienta, posiada certyfikat SAP HANA TDI. Dwie różne klasy sprzętu dzielą jednostki SKU na:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 i S224m, które są określane jako "klasa typu I" jednostek SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm i S960m, które są określane jako "klasa typu II" jednostek SKU.
- Jeśli jesteś zainteresowany innymi jednostkami S224 oferującymi od 4,5 TB do 9 TB z optane, skontaktuj się z zespołem konta Microsoft, aby uzyskać więcej informacji. 


Kompletny znaczek hana dużych wystąpień nie jest przydzielany wyłącznie dla jednego klienta&#39;s użytkowania. Ten fakt dotyczy stojaków zasobów obliczeniowych i magazynowych połączonych za pośrednictwem sieci szkieletowej wdrożonej również na platformie Azure. Infrastruktura dużych wystąpień HANA, takich &quot;jak&quot; platforma Azure, wdraża różne dzierżawy klientów, które są odizolowane od siebie na następujących trzech poziomach:

- **Sieć:** Izolacja za pośrednictwem sieci wirtualnych w sygnatury dużych wystąpień HANA.
- **Magazyn:** Izolacja za pośrednictwem maszyn wirtualnych magazynu, które mają przypisane woluminy magazynu i izolują woluminy magazynu między dzierżawcami.
- **Obliczenie: Dedykowane**przypisanie jednostek serwera do jednej dzierżawy. Brak twardych lub miękkich partycjonowania jednostek serwerowych. Brak udostępniania pojedynczego serwera lub jednostki hosta między dzierżawcami. 

Wdrożenia jednostek dużych wystąpień HANA między różnymi dzierżawami nie są widoczne dla siebie nawzajem. Jednostki dużych wystąpień HANA wdrożone w różnych dzierżawach nie mogą komunikować się bezpośrednio ze sobą na poziomie stempla dużego wystąpienia HANA. Tylko jednostki dużych wystąpień HANA w ramach jednej dzierżawy mogą komunikować się ze sobą na poziomie stempla dużego wystąpienia HANA.

Wdrożona dzierżawa w sygnatury dużego wystąpienia jest przypisana do jednej subskrypcji platformy Azure na potrzeby rozliczeń. W przypadku sieci można uzyskać do niej dostęp z sieci wirtualnych innych subskrypcji platformy Azure w ramach tej samej rejestracji platformy Azure. Jeśli wdrożysz z inną subskrypcją platformy Azure w tym samym regionie platformy Azure, możesz również poprosić o oddzielną dzierżawę dużych wystąpień HANA.

Istnieją znaczne różnice między uruchamianiem SAP HANA na dużym wystąpieniu HANA i SAP HANA uruchomionym na maszynach wirtualnych wdrożonych na platformie Azure:

- Nie ma warstwy wirtualizacji dla sap HANA na platformie Azure (duże wystąpienia). Otrzymujesz wydajność podstawowego sprzętu bare-metal.
- W przeciwieństwie do platformy Azure serwer SAP HANA na platformie Azure (duże wystąpienia) jest dedykowany określonemu klientowi. Nie ma możliwości, że jednostka serwera lub hosta jest twardy lub miękki partycjonowane. W rezultacie jednostka dużych wystąpień HANA jest używana jako przypisana jako całość do dzierżawy i z tym do Ciebie. Ponowne uruchomienie lub zamknięcie serwera nie prowadzi automatycznie do systemu operacyjnego i sap HANA jest wdrażany na innym serwerze. (Dla jednostek SKU klasy I jedynym wyjątkiem jest sytuacja, w których serwer napotyka problemy i ponowne wdrożenie musi zostać wykonane na innym serwerze).
- W przeciwieństwie do platformy Azure, gdzie typy procesorów hosta są wybierane ze względu na najlepszy stosunek ceny do wydajności, typy procesorów wybrane dla systemu SAP HANA na platformie Azure (duże wystąpienia) są najwyższą wydajnością linii procesorów Intel E7v3 i E7v4.

**Następne kroki**
- Odwołaj się do [rozmiaru HLI](hana-sizing.md)
