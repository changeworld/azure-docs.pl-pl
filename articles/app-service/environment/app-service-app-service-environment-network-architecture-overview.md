---
title: Omówienie architektury sieciowej środowisk usługi App Service
description: Omówienie architektury ofApp topologii sieci środowiska usługi.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: 3362a55524da42914681db06b8d2c0da8df773d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836826"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Omówienie architektury sieciowej środowisk usługi App Service
## <a name="introduction"></a>Wprowadzenie
Środowiska usługi App Service zawsze są tworzone w obrębie podsieci [sieci wirtualnej] [ virtualnetwork] — aplikacje działające w środowisku usługi aplikacji może komunikować się z punktami końcowymi prywatne, znajduje się w tej samej topologii sieci wirtualnej.  Ponieważ klienci mogą zablokować części infrastruktury sieci wirtualnej, należy zrozumieć typy przepływów komunikacji sieci, które występują w przypadku środowiska usługi aplikacji.

## <a name="general-network-flow"></a>Przepływ sieci ogólne
Jeśli środowisko aplikacji (ASE) używa publiczny wirtualny adres IP (VIP) dla aplikacji, cały ruch przychodzący dociera na tym publicznego adresu VIP.  W tym ruchu HTTP i HTTPS dla aplikacji, a także innych ruch FTP, zdalnego debugowania działania i operacji zarządzania platformy Azure.  Pełną listę określonych portów (wymagane i opcjonalne), które są dostępne na publiczny adres VIP w artykule na [kontrolowanie ruchu przychodzącego] [ controllinginboundtraffic] do środowiska usługi aplikacji. 

Środowiska usługi aplikacji obsługuje również uruchomionych aplikacji, które są powiązane tylko z sieci wirtualnej wewnętrzny adres, nazywany również adres ILB (wewnętrzny moduł równoważenia obciążenia).  Na ILB włączone ruchu ASE, HTTP i HTTPS dla aplikacji, a także debugowania wywołań zdalnych przychodzą na adres ILB.  W przypadku najbardziej typowych konfiguracji ILB ASE ruch FTP/FTPS pojawią się również na adres ILB.  Jednak operacje zarządzania Azure nadal będą przepływać w portach 454/455 na publiczny adres VIP o ILB obsługuje ASE.

Na poniższym diagramie przedstawiono omówienie poszczególnych przepływów ruchu przychodzącego i wychodzącego dla środowiska usługi aplikacji, których aplikacje są powiązane z publiczny wirtualny adres IP:

![Ogólne przepływów sieci][GeneralNetworkFlows]

Środowiska usługi aplikacji może komunikować się z różnych punktów końcowych klienta prywatnych.  Na przykład aplikacje działające w środowisku usługi aplikacji może połączyć się z serwerów bazy danych uruchomionych na maszynach wirtualnych IaaS w tej samej topologii sieci wirtualnej.

> [!IMPORTANT]
> Spojrzenie na diagramie sieciowym, "Inne obliczeniowe zasoby" są wdrażane w innej podsieci, ze środowiska usługi aplikacji. Wdrażanie zasobów w tej samej podsieci z ASE zablokuje łączność między ASE tych zasobów (z wyjątkiem określonych ASE wewnątrz routing). Wdróż w innej podsieci z zamiast niego (w tej samej sieci Wirtualnej). Środowiska usługi aplikacji będzie można nawiązać połączenia. Dodatkowa konfiguracja nie jest konieczne.
> 
> 

Środowiska usługi aplikacji również komunikować się z bazą danych Sql i usługi Azure Storage zasoby niezbędne do zarządzania i obsługi środowiska usługi aplikacji.  Niektórych zasobów Sql i magazynu, które środowiska usługi aplikacji komunikuje się z znajdują się w tym samym regionie co środowiska usługi aplikacji, podczas gdy inne znajdują się w zdalnym regiony platformy Azure.  W związku z tym wychodzące połączenie z Internetem jest zawsze wymagany do poprawnego środowiska usługi aplikacji. 

Ponieważ środowisko usługi aplikacji jest wdrażana w podsieci, grup zabezpieczeń sieci można kontrolować ruch przychodzący do podsieci.  Aby uzyskać informacje na temat kontrolowania ruch przychodzący do środowiska usługi aplikacji, zobacz następujące tematy [artykułu][controllinginboundtraffic].

Więcej informacji na temat Zezwalaj wychodzące połączenie internetowe ze środowiska usługi aplikacji, można znaleźć w następującym artykule o pracy z [Express Route][ExpressRoute].  Te same podejście opisaną w artykule zastosowanie podczas pracy z połączenie lokacja-lokacja i przy użyciu wymuszonego tunelowania.

## <a name="outbound-network-addresses"></a>Adresy sieciowe ruchu wychodzącego
Adres IP wywołań wychodzących środowiska usługi aplikacji zawsze jest skojarzony z wywołań wychodzących.  Określony adres IP, który jest używany, zależy od tego, czy punkt końcowy wywoływana znajduje się w topologii sieci wirtualnej, lub na zewnątrz topologii sieci wirtualnej.

Jeśli punkt końcowy wywoływany jest **poza** następnie topologii sieci wirtualnej adres wychodzącego (alias wychodzący adres NAT), który jest używany jest publiczny adres VIP środowiska usługi aplikacji.  Ten adres znajduje się w interfejsie użytkownika portalu dla środowiska usługi aplikacji w bloku właściwości.

![Adres IP ruchu wychodzącego][OutboundIPAddress]

Ten adres także można określić dla ASEs tylko mających publicznego firmy przez tworzenie aplikacji w środowisku usługi aplikacji, a następnie wykonuje *nslookup* adresu aplikacji. Wynikowe adres IP jest zarówno publiczny adres VIP, a także adres NAT ruchu wychodzącego środowiska usługi aplikacji.

Jeśli punkt końcowy wywoływany jest **wewnątrz** topologii sieci wirtualnej wychodzący adres wywoływania aplikacji będzie wewnętrzny adres IP zasób obliczeniowy poszczególnych aplikację.  Jednak nie ma trwałego mapowanie sieci wirtualnych adresów IP do aplikacji.  Aplikacje można przenieść między zasoby obliczeniowe różnych i puli obliczeniowej dostępnych zasobów w środowisku usługi aplikacji można zmienić z powodu operacji skalowania.

Jednak ponieważ środowiska usługi aplikacji zawsze znajduje się w podsieci, ma się gwarancji, że wewnętrznego adresu IP zasobu obliczeniowego uruchamiania aplikacji zawsze będzie znajdować się w obrębie zakres CIDR podsieci.  W związku z tym gdy szczegółowych list ACL lub grup zabezpieczeń sieci są używane do zabezpieczania dostępu do innych punktów końcowych w sieci wirtualnej, zakresu podsieci zawierającego środowiska usługi aplikacji musi otrzymać dostęp.

Na poniższym diagramie przedstawiono te pojęcia bardziej szczegółowo:

![Adresy sieciowe ruchu wychodzącego][OutboundNetworkAddresses]

Na powyższym diagramie:

* Ponieważ publiczny adres VIP środowiska usługi aplikacji jest 192.23.1.2, który jest wychodzący adres IP używany podczas połączenia z punktami końcowymi "Internet".
* Zakres CIDR zawierający podsieci dla środowiska usługi aplikacji jest 10.0.1.0/26.  Inne punkty końcowe w ramach tej samej infrastrukturze sieci wirtualne będą widzieć wywołania z aplikacji jako pochodzący z gdzieś w ramach tego zakresu adresów.

## <a name="calls-between-app-service-environments"></a>Wywołania między środowiska usługi aplikacji
Bardziej złożone scenariusz może wystąpić, jeśli wdrożyć wiele środowisk usługi aplikacji w tej samej sieci wirtualnej, a nawiązywać wychodzące połączenia z jednego środowiska usługi aplikacji do innego środowiska usługi aplikacji.  Tego rodzaju wielu wywołań również będą traktowane jako wywołania "Internet" środowiska usługi aplikacji.

Na poniższym diagramie przedstawiono przykład architektury warstwowej z aplikacjami na jednym środowiska usługi aplikacji (np. "Drzwi wejściowe" web apps) wywołanie aplikacji w drugim środowiska usługi aplikacji (np. wewnętrznych aplikacji interfejsu API zaplecza nie mają być dostępne z Internetu). 

![Wywołania między środowiska usługi aplikacji][CallsBetweenAppServiceEnvironments] 

W powyższym przykładzie środowiska usługi aplikacji "ASE jeden" ma adres IP wychodzących 192.23.1.2.  Jeśli aplikacja uruchomiona na tym będzie sprawia, że wywołanie wychodzące do aplikacji uruchomionej na drugi środowiska usługi aplikacji ("ASE dwóch") znajduje się w tej samej sieci wirtualnej wywołań wychodzących środowiska usługi aplikacji są traktowane jako wywołanie "Internet".  W rezultacie ruch sieciowy odbieranych na sekundę środowiska usługi aplikacji będą wyświetlane jako pochodzący z 192.23.1.2 (tj. nie zakres adresów podsieci pierwszy środowiska usługi aplikacji).

Mimo że wywołań między różne środowiska usługi aplikacji są traktowane jako wywołania "Internet", gdy oba środowiska usługi aplikacji znajdują się w tym samym regionie Azure ruch sieciowy pozostanie na regionalną sieć platformy Azure i nie będzie fizycznie możliwy przepływ za pośrednictwem publicznej sieci Internet.  W związku z tym służy sieciowej grupy zabezpieczeń w podsieci drugi środowiska usługi aplikacji umożliwiają tylko wywołania z pierwszego środowiska usługi aplikacji (których wychodzący adres IP jest 192.23.1.2), w związku z tym zapewnienia bezpiecznej komunikacji między środowiska usługi App Service.

## <a name="additional-links-and-information"></a>Linki do dodatkowych i informacji
Szczegóły ruchu przychodzącego porty używane przez środowiska usługi App Service i kontrolować ruch przychodzący za pomocą grup zabezpieczeń sieci jest dostępna [tutaj][controllinginboundtraffic].

Szczegółowe informacje o wykorzystaniu użytkownika zdefiniowanych tras udzielenia wychodzący dostęp do Internetu dla środowiska usługi aplikacji jest dostępna w tym [artykułu][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

