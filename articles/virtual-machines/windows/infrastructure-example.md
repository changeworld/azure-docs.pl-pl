---
title: Przykład infrastruktury platformy Azure wskazówki | Dokumentacja firmy Microsoft
description: Więcej informacji na temat klucza projekt i implementację wskazówki dotyczące wdrażania infrastruktury przykład na platformie Azure.
documentationcenter: ''
services: virtual-machines-windows
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c532657951d6d0241a5d8d25a56bb237ad481567
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29809873"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Przykład wskazówki infrastruktury platformy Azure dla maszyn wirtualnych systemu Windows
W tym artykule przedstawiono zbudowaniu przykład infrastruktury aplikacji. Firma Microsoft szczegółowo projektowania infrastruktury dla prostego Sklep internetowy zgromadzono wskazówki i decyzji dotyczących konwencji nazewnictwa, zestawów dostępności, sieci wirtualnych i usług równoważenia obciążenia i faktycznie wdrażania maszyn wirtualnych (VM).

## <a name="example-workload"></a>Przykładowe obciążenie
Firma Adventure Works Cycles chce utworzyć aplikację sklepu online na platformie Azure, która składa się z:

* Dwa serwery IIS z frontonu w warstwa sieci web klienta
* Przetwarzanie danych i zamówień w warstwie aplikacji dwóch serwerów usług IIS
* Dwa wystąpienia programu Microsoft SQL Server z grupami dostępności AlwaysOn (dwa serwery SQL i monitora węzła większość) do przechowywania danych produktu i zamówień w warstwy bazy danych
* Dwa kontrolery domeny usługi Active Directory dla konta klienta i dostawców w warstwie uwierzytelniania
* Wszystkie serwery znajdują się w dwóch podsieci:
  * frontonu podsieci dla serwerów sieci web 
  * podsieć wewnętrznych serwerów aplikacji, klaster serwera SQL i kontrolerów domeny

![Diagram różnych warstw dla infrastruktury aplikacji](./media/infrastructure-example/example-tiers.png)

Bezpieczne przychodzącego ruchu w sieci web musi być równoważeniem obciążenia między serwerami sieci web jako klienci Przeglądaj sklepu online. Kolejność przetwarzania ruchu sieciowego w postaci HTTP żąda od serwerów musi uwzględniać między serwerami aplikacji sieci web. Ponadto należy zaprojektować infrastruktury wysokiej dostępności.

Projekt wynikowy musi uwzględniać:

* Subskrypcja platformy Azure i konta
* Pojedyncza grupa zasobów
* Azure Managed Disks
* Sieć wirtualną z dwiema podsieciami
* Zestawy dostępności dla maszyn wirtualnych o podobną rolę
* Maszyny wirtualne

Wszystkie powyższe wykonaj te konwencji nazewnictwa:

* Adventure Works Cycles używa **[obciążenia IT]-[lokalizacja]-[zasobów platformy Azure]** jako prefiksu
  * Na przykład "**azos**" (magazyn Azure Online) jest nazwą obciążenia IT i "**użyj**" (wschodnie stany USA 2) to lokalizacja
* W sieciach wirtualnych za pomocą AZOS-użycie-VN **[numer]**
* Zestawy dostępności używać azos-Użyj — jako-**[rola]**
* Nazwy maszyn wirtualnych użyć azos-Użyj-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Subskrypcje platformy Azure i konta
Firma Adventure Works Cycles używa ich subskrypcji Enterprise, o nazwie Adventure Works Enterprise subskrypcji, zapewnienie rozliczeń dla tej obciążenia IT.

## <a name="storage"></a>Magazyn
Firma Adventure Works Cycles ustalić, czy powinny używać dysków zarządzanych Azure. Podczas tworzenia maszyn wirtualnych, są używane zarówno warstw magazynowania dostępne:

* **Magazynu w warstwie standardowa** dla serwerów sieci web, serwerów aplikacji i kontrolerów domeny i dysków z danymi.
* **Magazyn w warstwie Premium** dla maszyn wirtualnych serwera SQL i dysków z danymi.

## <a name="virtual-network-and-subnets"></a>Sieć wirtualna i podsieci
Ponieważ sieci wirtualnej nie wymaga trwających łączności z siecią lokalną cykle pracy Adventure, zdecydowano, że w sieci wirtualnej tylko w chmurze.

Sieć wirtualna tylko w chmurze one utworzone przy użyciu następujących ustawień przy użyciu portalu Azure:

* Nazwa: AZOS-użycie VN01
* Lokalizacji: Wschodnie stany USA 2
* Przestrzeń adresową sieci wirtualnej: 10.0.0.0/8
* Pierwszej podsieci:
  * Nazwa: frontonu
  * Przestrzeń adresowa: 10.0.1.0/24
* Drugi podsieci:
  * Nazwa: wewnętrznej bazy danych
  * Przestrzeń adresowa: 10.0.2.0/24

## <a name="availability-sets"></a>Zestawy dostępności
Aby zachować wysoką dostępność wszystkie cztery warstwy ich magazynu online, firma Adventure Works Cycles stanowi na cztery zestawy dostępności:

* **azos używany jako web** dla serwerów sieci web
* **azos używany jako aplikacji** dla serwerów aplikacji
* **azos używany jako sql** serwerami SQL
* **azos używany jako dc** dla kontrolerów domeny

## <a name="virtual-machines"></a>Maszyny wirtualne
Firma Adventure Works Cycles decyzję o następujących nazwach na swoich maszynach wirtualnych platformy Azure:

* **azos użycie vm-web01** na pierwszym serwerze sieci web
* **azos użycie vm-web02** drugiego serwera sieci web
* **azos użycie vm-app01** dla pierwszego serwera aplikacji
* **azos użycie vm-app02** dla drugiego serwera aplikacji
* **azos użycie vm-sql01** dla pierwszego serwera SQL Server w klastrze
* **azos użycie vm-sql02** drugiego serwera programu SQL Server w klastrze
* **azos użycie vm-dc01** dla pierwszego kontrolera domeny
* **azos użycie vm-dc02** dla kontrolera domeny

Oto wynikowej konfiguracji.

![Infrastruktura aplikacji końcowego wdrożona na platformie Azure](./media/infrastructure-example/example-config.png)

Ta konfiguracja obejmuje:

* Tylko w chmurze sieci wirtualnej z dwoma podsieciami (frontonu i wewnętrznej bazy danych)
* Dyskach platformy Azure zarządzanego z dyskami w wersjach Standard i Premium
* Cztery zestawy dostępności, jeden dla każdej warstwy sklepu online
* Maszyny wirtualne w czterech warstwach
* Zestaw o zrównoważonym obciążeniu zewnętrznych dla ruchu w sieci web z protokołem HTTPS z Internetu na serwerach sieci web
* Zestaw dla ruchu w sieci web niezaszyfrowane z serwerów sieci web na serwery aplikacji o zrównoważonym obciążeniu wewnętrzny
* Pojedyncza grupa zasobów
