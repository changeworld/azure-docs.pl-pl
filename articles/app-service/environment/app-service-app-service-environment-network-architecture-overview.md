---
title: Omówienie architektury sieciowej środowisk usługi App Service, Azure
description: Omówienie architektury ofApp topologii sieci środowiska usług.
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
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60765315"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Omówienie architektury sieciowej środowisk usługi App Service
## <a name="introduction"></a>Wprowadzenie
Środowiska usługi App Service są zawsze tworzone w podsieci [sieci wirtualnej] [ virtualnetwork] — aplikacje działające w środowisku usługi App Service może komunikować się z punktami końcowymi prywatnych, znajdującymi się w tym samym wirtualnej Topologia sieci.  Ponieważ klienci mogą zablokować części infrastruktury sieci wirtualnej, należy zapoznać się z typami przepływów komunikacji sieci, które występują w środowisku usługi App Service.

## <a name="general-network-flow"></a>Przepływ sieciowy ogólne
Gdy App Service Environment (ASE) używa publiczny wirtualny adres IP (VIP) dla aplikacji, cały ruch przychodzący dociera przy użyciu tego publicznego adresu VIP.  W tym ruchu HTTP i HTTPS dla aplikacji, a także pozostałe rodzaje ruchu dla protokołu FTP, funkcji debugowania zdalnego i operacje zarządzania platformy Azure.  Aby uzyskać pełną listę określonych portów (wymagane i opcjonalne), które są dostępne w publicznych adresów VIP zobacz artykuł [kontrolowanie ruchu przychodzącego] [ controllinginboundtraffic] do środowiska usługi App Service. 

Środowiska App Service Environment obsługują również uruchamiania aplikacji, które są powiązane tylko z siecią wirtualną wewnętrzny adres, określane również jako adresu wewnętrznego modułu równoważenia obciążenia (wewnętrzny moduł równoważenia obciążenia).  W wewnętrznym modułem równoważenia obciążenia włączone ruchu środowiska ASE, HTTP i HTTPS dla aplikacji, a także wywołania zdalne debugowanie, pojawić się na adres wewnętrznego modułu równoważenia obciążenia.  W przypadku najbardziej typowych konfiguracji środowiska ASE wewnętrznego modułu równoważenia obciążenia ruchu protokołu FTP/FTPS również zostaną dostarczone adresu wewnętrznego modułu równoważenia obciążenia.  Jednak operacje zarządzania platformą Azure nadal będą przepływać portach 454/455 w publicznych adresów VIP z wewnętrznym modułem równoważenia obciążenia włączone środowiska ASE.

Na poniższym diagramie przedstawiono omówienie poszczególnych przepływów ruchu przychodzącego i wychodzącego dla środowiska App Service Environment, na których aplikacje są powiązane z publiczny wirtualny adres IP:

![Ogólne przepływów sieci][GeneralNetworkFlows]

Środowisko usługi App Service może komunikować się z różnych punktów końcowych klienta prywatnych.  Na przykład aplikacje działające w środowisku App Service można nawiązać połączenia serwerach baz danych uruchamianych na maszynach wirtualnych IaaS w tej samej topologii sieci wirtualnej.

> [!IMPORTANT]
> Patrząc na diagramie sieciowym, "Inne obliczenia zasoby" są wdrażane w innej podsieci z usługi App Service Environment. Wdrażanie zasobów w tej samej podsieci za pomocą środowiska ASE zablokuje łączności z ASE do tych zasobów (z wyjątkiem określonych wewnątrz środowiska ASE routingu). Wdróż do innej podsieci z zamiast (w tej samej sieci Wirtualnej). Usługa App Service Environment będą w stanie połączyć. Dodatkowa konfiguracja nie jest konieczne.
> 
> 

Środowiska usługi App Service również komunikować się z bazą danych Sql i Azure Storage zasoby niezbędne do działania w środowisku usługi App Service i zarządzanie nimi.  Niektóre zasoby Sql i Storage, które komunikuje się środowisko App Service znajdują się w tym samym regionie co usługa App Service Environment, podczas gdy inne znajdują się w odległych regionów platformy Azure.  W wyniku połączenia wychodzącego do Internetu jest zawsze wymagany dla środowiska usługi App Service do poprawnego działania. 

Ponieważ środowisko App Service jest wdrażana w podsieci, sieciowe grupy zabezpieczeń można kontrolować ruch przychodzący do podsieci.  Aby uzyskać szczegółowe informacje o tym, jak kontrolować ruch przychodzący do środowiska usługi App Service, zapoznaj się z poniższymi [artykułu][controllinginboundtraffic].

Szczegółowe informacje na temat sposobu zezwalania wychodzące połączenie z Internetem w środowisku usługi App Service, zobacz następujący artykuł o pracy z [Express Route][ExpressRoute].  Tej samej metody opisanej w artykule stosuje się podczas pracy z połączeniem lokacja-lokacja i przy użyciu wymuszonego tunelowania.

## <a name="outbound-network-addresses"></a>Adresy sieciowe dotyczące połączeń wychodzących
Po wywołań ruchu wychodzącego środowiska App Service Environment adres IP jest zawsze skojarzone z połączenia wychodzące.  Określony adres IP, która jest używana, zależy od tego, czy punktu końcowego wywołanego znajduje się w topologii sieci wirtualnej lub poza topologii sieci wirtualnej.

Jeśli punkt końcowy, wywoływana jest **poza** topologii sieci wirtualnej, następnie adres ruchu wychodzącego (alias wychodzący adres translatora adresów Sieciowych), który jest używany jest publicznych adresów VIP dla usługi App Service Environment.  Ten adres znajdują się w interfejsie użytkownika portalu usługi App Service Environment w bloku właściwości.

![Wychodzący adres IP][OutboundIPAddress]

Ten adres można również określić dla środowiska ASE, którzy mają tylko publicznych adresów VIP przez tworzenie aplikacji w środowisku App Service, a następnie wykonuje *nslookup* adresu aplikacji. Wynikowe adres IP jest zarówno publicznych adresów VIP, a także adres translatora adresów Sieciowych z ruchu wychodzącego środowiska App Service Environment.

Jeśli punkt końcowy, wywoływana jest **wewnątrz** topologii sieci wirtualnej adres ruchu wychodzącego wywoływania aplikacji będzie wewnętrzny adres IP zasobów obliczeniowych poszczególnych, uruchamiając aplikację.  Jednak nie ma trwały mapowanie sieci wirtualnych adresów IP do aplikacji.  Aplikacje można zmieniają położenie w różnych zasobów obliczeniowych i puli dostępnych obliczeń, które zasoby w środowisku usługi App Service można zmienić z powodu operacji skalowania.

Jednak ponieważ środowisko App Service zawsze znajduje się w podsieci, ma gwarancji, że wewnętrzny adres IP zasobu obliczeniowego, uruchomienie aplikacji, zawsze będzie znajdować się w obrębie zakres CIDR podsieci.  Co w efekcie gdy list ACL identyfikatorach lub sieciowe grupy zabezpieczeń służą do bezpiecznego dostępu do innych punktów końcowych w sieci wirtualnej, zakres podsieci zawierających środowiska App Service Environment musi otrzymać dostęp.

Na poniższym diagramie przedstawiono te pojęcia bardziej szczegółowo:

![Adresy sieciowe dotyczące połączeń wychodzących][OutboundNetworkAddresses]

Na powyższym diagramie:

* Ponieważ publicznych adresów VIP dla usługi App Service Environment jest 192.23.1.2, który jest wychodzący adres IP używany podczas nawiązywania połączeń z punktami końcowymi "Internet".
* Zakres CIDR podsieci zawierającej App Service Environment jest 10.0.1.0/26.  Inne punkty końcowe w ramach tej samej infrastrukturze sieci wirtualnej, zostaną wyświetlone wywołań z aplikacji jako pochodzący z gdzieś w ramach tego zakresu adresów.

## <a name="calls-between-app-service-environments"></a>Wywołania między środowisk usługi App Service
Bardziej złożone scenariusz może wystąpić, jeśli możesz wdrożyć wiele środowisk App Service Environment w tej samej sieci wirtualnej i wprowadzić połączenia wychodzące z jednego środowiska usługi App Service do innego środowiska App Service Environment.  Tego rodzaju obejmujące wiele wywołań również będą traktowane jako "Internet" wywołania środowiska App Service Environment.

Na poniższym diagramie przedstawiono przykład architektury warstwowej, za pomocą aplikacji w jednym środowisku usługi App Service (np. Aplikacje sieci web "Drzwi wejściowe") podczas wywoływania aplikacji w drugim środowisku App Service Environment (np. wewnętrznych aplikacji interfejsu API zaplecza nie mają być dostępne z Internetu). 

![Wywołania między środowisk usługi App Service][CallsBetweenAppServiceEnvironments] 

W powyższym przykładzie środowiska App Service Environment "ASE jeden" ma adres IP ruchu wychodzącego 192.23.1.2.  Jeśli aplikacji uruchomionej na tym środowisku usługi App Service będzie sprawia, że wywołanie ruchu wychodzącego do aplikacji uruchomionej na drugie środowisko usługi App Service ("ASE dwie") znajduje się w tej samej sieci wirtualnej wychodzące wywołanie jest traktowane jako wywołanie "Internet".  W rezultacie ruch sieciowy przychodzący drugiego środowiska App Service Environment będą wyświetlane jako pochodzący z 192.23.1.2 (czyli nie zakresu adresów podsieci z pierwszej usługi App Service Environment).

Mimo że wywołań między różnych środowisk usługi App Service są traktowane jako wywołania "Internet", gdy zarówno środowisk usługi App Service znajdują się w tym samym regionie platformy Azure ruch sieciowy będzie pozostawać na regionalnej sieci platformy Azure, a nie fizycznie przepływu za pośrednictwem publicznej sieci Internet.  W rezultacie można użyć sieciowej grupy zabezpieczeń w podsieci drugim środowisku usługi App Service umożliwia tylko wywołań przychodzących od pierwszej usługi App Service Environment (których wychodzący adres IP jest 192.23.1.2), co pozwala na zapewnienie bezpiecznej komunikacji między aplikacji Środowiska usługi.

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Szczegółowe informacje dotyczące portów używanych przez środowisk usługi App Service dla ruchu przychodzącego i kontrolować ruch przychodzący przy użyciu sieciowych grup zabezpieczeń jest dostępne [tutaj][controllinginboundtraffic].

Szczegółowe informacje na temat korzystania z użytkowników zdefiniowanych tras do udzielania wychodzącego dostępu do Internetu dla środowisk usługi App Service jest dostępna w tym [artykułu][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

