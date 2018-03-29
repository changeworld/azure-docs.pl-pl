---
title: Omówienie wewnętrzny moduł równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Jak wewnętrznego modułu równoważenia obciążenia działa w Azure i scenariuszy dotyczących konfigurowania wewnętrznych punktów końcowych.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Omówienie usługi Azure wewnętrznego modułu równoważenia obciążenia



Azure usługi równoważenia obciążenia wewnętrznego (ILB) tylko kieruje ruch do zasobów, które znajdują się wewnątrz usługi w chmurze lub z sieci VPN uzyskiwać dostęp do infrastruktury platformy Azure. W związku z tym ILB różni się od modułu równoważenia obciążenia skierowane do Internetu. Infrastruktury platformy Azure ogranicza dostęp do równoważenia obciążenia wirtualne adresy IP (VIP) usługi w chmurze lub sieci wirtualnej. Adresy VIP i sieci wirtualne są nigdy nie bezpośrednio widoczne dla punktu końcowego internet. Wewnętrznych aplikacji biznesowych — Uruchom na platformie Azure i są dostępne z platformy Azure lub z lokalnymi zasobami.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Dlaczego może być konieczne wewnętrznego modułu równoważenia obciążenia

Wewnętrzny moduł równoważenia obciążenia zapewnia zrównoważenia obciążenia między maszynach wirtualnych (VM), które znajdują się wewnątrz usługi w chmurze lub sieci wirtualnej z zakresu regionalnego. Aby uzyskać informacje o sieciach wirtualnych z zakresu regionalnego, zobacz [regionalnych sieci wirtualnych](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) w blogu platformy Azure. Istniejące sieci wirtualne, które są skonfigurowane do grupy koligacji nie można użyć ILB.

ILB umożliwia następujące typy równoważenia obciążenia:

* W ramach usługi w chmurze: równoważenie z maszyn wirtualnych z zestawem maszyn wirtualnych znajdujących się w tej samej usługi w chmurze obciążenia. Zobacz to <a href="#figure1">przykład</a>.
* W ramach sieci wirtualnej: równoważenia obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych znajdujących się w tej samej usługi w chmurze sieci wirtualnej. Zobacz to <a href="#figure2">przykład</a>.
* Dla sieci wirtualnej między lokalizacjami: załadować równoważenia z komputerów lokalnych do wielu maszyn wirtualnych znajdujących się w tej samej usługi w chmurze sieci wirtualnej. Zobacz to <a href="#figure3">przykład</a>.
* Dla aplikacji wielowarstwowych: równoważenia obciążenia dla aplikacji wielowarstwowych internetowy gdy warstw zaplecza nie są skierowane do Internetu. Poziomy zaplecza wymagają równoważenia z warstwy internetowy obciążenia ruchu.
* W przypadku aplikacji biznesowych z: równoważenia obciążenia dla aplikacji — biznesowych, które są obsługiwane na platformie Azure, bez dodatkowego obciążenia równoważenia sprzętu lub oprogramowania. Taki scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest równoważeniem obciążenia.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Równoważenie obciążenia dla aplikacji wielowarstwowych połączonych z Internetem

Warstwa sieci web ma punkty końcowe skierowane do Internetu dla klientów internetowych i jest częścią zestawu o zrównoważonym obciążeniu. ILB dystrybuuje ruch przychodzący z klientów w sieci web dla portu TCP 443 (HTTPS) na serwerach sieci web.

Serwery bazy danych są za punktem końcowym ILB, używanego do przechowywania serwerów sieci web. Punktem końcowym ILB jest punkt końcowy równoważenia obciążenia usługi bazy danych. Ruch jest równoważeniem obciążenia na serwerach bazy danych w zestawie ILB.

Na poniższej ilustracji przedstawiono wewnętrzne Równoważenie obciążenia dla aplikacji wielowarstwowych internetowy w ramach tej samej usługi w chmurze.

<a name="figure1"></a>
![Internetowy wielowarstwową aplikację](./media/load-balancer-internal-overview/IC736321.png)

Inny scenariusz jest dostępna dla aplikacji wielowarstwowych. Moduł równoważenia obciążenia jest wdrażana na innej usługi chmury niż ten, który wykorzystuje usługę ILB.

Usługi w chmurze, które używają tej samej sieci wirtualnej można uzyskać dostępu do końcowym ILB. Na poniższej ilustracji przedstawiono serwerów frontonu sieci web, które znajdują się w innej usługi chmury z bazy danych zaplecza. Serwery frontonu stanowić zaplecza końcowym ILB w tej samej sieci wirtualnej.

<a name="figure2"></a>
![Serwery frontonu w innej usługi chmury](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Równoważenie obciążenia dla aplikacji biznesowych z sieci intranet

Ruch z klientów w sieci lokalnej jest równoważeniem obciążenia przez zestaw serwerów z biznesowych, które używają połączenia sieci VPN do sieci platformy Azure.

Komputer kliencki może dostęp do adresu IP usługi sieci VPN platformy Azure przy użyciu sieci VPN punkt lokacja. Może być hostowana aplikacji biznesowych z za punktem końcowym ILB.

<a name="figure3"></a>
![Aplikacji biznesowych z hostowanej za punktem końcowym ILB](./media/load-balancer-internal-overview/IC744148.png)

Inny scenariusz dla aplikacji biznesowych z jest siecią wirtualną, której skonfigurowano końcowym ILB sieci VPN lokacja lokacja. Ruch w sieci lokalnej jest kierowany do końcowym ILB.

<a name="figure4"></a>
![Ruch kierowany do punktem końcowym ILB sieci lokalnej](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Ograniczenia

Wewnętrzny moduł równoważenia obciążenia konfiguracje nie obsługują SNAT. W tym artykule SNAT odwołuje się do scenariusze obejmujące maskaradę portu translatora adresów sieciowych źródła. Adres IP frontonu modułu równoważenia obciążenia wewnętrznego odpowiednich maszyn wirtualnych w puli usługi równoważenia obciążenia muszą być dostarczone. Po przepływ równoważeniem obciążenia do maszyny Wirtualnej, które powstały przepływ wystąpią jakieś awarie połączenia. Te scenariusze nie są obsługiwane dla ILB. Zamiast tego należy użyć modułu równoważenia obciążenia serwera proxy stylu.

## <a name="next-steps"></a>Kolejne kroki

* [Pomocy technicznej platformy Azure Resource Manager dla usługi równoważenia obciążenia Azure](load-balancer-arm.md)
* [Wprowadzenie do konfigurowania usługi równoważenia obciążenia połączonych z Internetem](load-balancer-get-started-internet-arm-ps.md)
* [Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia](load-balancer-get-started-ilb-arm-ps.md)
* [Configure load balancer distribution mode (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)](load-balancer-distribution-mode.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
