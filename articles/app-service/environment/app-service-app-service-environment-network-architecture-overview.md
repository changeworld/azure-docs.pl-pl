---
title: Architektura sieci w wersji 1
description: Omówienie architektury topologii sieci środowisk usługi app service. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243850"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Omówienie architektury sieci środowisk usługi app service
Środowiska usługi aplikacji są zawsze tworzone w podsieci [sieci wirtualnej][virtualnetwork] — aplikacje działające w środowisku usługi aplikacji mogą komunikować się z prywatnymi punktami końcowymi znajdującymi się w ramach tej samej topologii sieci wirtualnej.  Ponieważ klienci mogą zablokować części swojej infrastruktury sieci wirtualnej, ważne jest, aby zrozumieć typy przepływów komunikacji sieciowej, które występują w środowisku usługi aplikacji.

## <a name="general-network-flow"></a>Ogólny przepływ sieci
Gdy środowisko usługi aplikacji (ASE) używa publicznego wirtualnego adresu IP (VIP) dla aplikacji, cały ruch przychodzący dociera do tego publicznego adresu VIP.  Obejmuje to ruch HTTP i HTTPS dla aplikacji, a także inny ruch dla protokołu FTP, funkcje zdalnego debugowania i operacje zarządzania platformy Azure.  Aby uzyskać pełną listę określonych portów (zarówno wymagane i opcjonalne), które są dostępne w publicznym programie VIP, zobacz artykuł dotyczący [kontrolowania ruchu przychodzącego][controllinginboundtraffic] do środowiska usługi aplikacji. 

Środowiska usługi app service obsługują również uruchomione aplikacje, które są powiązane tylko z adresem wewnętrznym sieci wirtualnej, zwanym również adresem równoważenia obciążenia (wewnętrznego modułu równoważenia obciążenia).  Na ase ase włączonego przy włączeniu przy wbijania obciążenia sieciowego, ruchu HTTP i HTTPS dla aplikacji, a także zdalnych wywołań debugowania, docieraj na adres równoważenia obciążenia sieciowego.  W przypadku najpopularniejszych konfiguracji ILB-ASE ruch FTP/FTPS zostanie również nadjeżdżany na adres ILB.  Jednak operacje zarządzania platformy Azure nadal będą przepływać do portów 454/455 na publicznym adresie VIP serwera ASE z włączoną funkcją ILB.

Na poniższym diagramie przedstawiono omówienie różnych przepływów sieci przychodzącej i wychodzącej dla środowiska usługi app service, w którym aplikacje są powiązane z publicznym wirtualnym adresem IP:

![Ogólne przepływy sieciowe][GeneralNetworkFlows]

Środowisko usługi aplikacji może komunikować się z różnymi punktami końcowymi klientów prywatnych.  Na przykład aplikacje działające w środowisku usługi app service mogą łączyć się z serwerami bazy danych uruchomionymi na maszynach wirtualnych IaaS w tej samej topologii sieci wirtualnej.

> [!IMPORTANT]
> Patrząc na diagram sieciowy, "Inne zasoby obliczeniowe" są wdrażane w innej podsieci ze środowiska usługi app service. Wdrażanie zasobów w tej samej podsieci za pomocą ase zablokuje łączność z ASE do tych zasobów (z wyjątkiem określonych routingu wewnątrz ASE). Zamiast tego wdrażaj w innej podsieci (w tej samej sieci wirtualnej). Środowisko usługi aplikacji będzie wtedy można nawiązać połączenie. Nie jest konieczna żadna dodatkowa konfiguracja.
> 
> 

Środowiska usługi app service komunikują się również z zasobami usługi Sql DB i usługi Azure Storage niezbędnymi do zarządzania środowiskiem usługi app service i ich obsługi.  Niektóre zasoby sql i magazynu, z którymi komunikuje się środowisko usługi app service, znajdują się w tym samym regionie co środowisko usługi app service, podczas gdy inne znajdują się w zdalnych regionach platformy Azure.  W rezultacie łączność wychodząca z Internetem jest zawsze wymagana do prawidłowego działania środowiska usługi app service. 

Ponieważ środowisko usługi aplikacji jest wdrażane w podsieci, sieciowe grupy zabezpieczeń mogą służyć do kontrolowania ruchu przychodzącego do podsieci.  Aby uzyskać szczegółowe informacje na temat kontrolowania ruchu przychodzącego do środowiska usługi app service, zobacz następujący [artykuł][controllinginboundtraffic].

Aby uzyskać szczegółowe informacje na temat zezwalania na wychodzące połączenie z Internetem ze środowiska usługi app service, zobacz następujący artykuł dotyczący pracy z [trasą ekspresową][ExpressRoute].  To samo podejście opisane w artykule ma zastosowanie podczas pracy z łącznością lokacja lokacja i przy użyciu wymuszonego tunelowania.

## <a name="outbound-network-addresses"></a>Wychodzące adresy sieciowe
Gdy środowisko usługi aplikacji wykonuje połączenia wychodzące, adres IP jest zawsze skojarzony z wywołaniami wychodzącymi.  Określony adres IP, który jest używany zależy od tego, czy punkt końcowy jest wywoływany znajduje się w topologii sieci wirtualnej lub poza topologii sieci wirtualnej.

Jeśli wywoływany punkt końcowy znajduje się **poza** topologią sieci wirtualnej, adres wychodzący (aka wychodzący adres NAT), który jest używany, jest publicznym adresem VIP środowiska usługi app service.  Ten adres można znaleźć w interfejsie użytkownika portalu dla środowiska usługi aplikacji w właściwości bloku.

![Wychodzący adres IP][OutboundIPAddress]

Ten adres można również określić dla środowisk ASES, które mają tylko publiczny adres VIP, tworząc aplikację w środowisku usługi app service, a następnie wykonując *nslookup* na adres aplikacji. Wynikowy adres IP jest zarówno publiczny adres VIP, jak również wychodzący adres NAT środowiska usługi aplikacji.

Jeśli wywoływany punkt końcowy znajduje się **wewnątrz** topologii sieci wirtualnej, adresem wychodzącym aplikacji wywołującej będzie wewnętrzny adres IP poszczególnych zasobów obliczeniowych z uruchomieniem aplikacji.  Nie istnieje jednak trwałe mapowanie wewnętrznych adresów IP sieci wirtualnej do aplikacji.  Aplikacje mogą poruszać się po różnych zasobach obliczeniowych, a pula dostępnych zasobów obliczeniowych w środowisku usługi aplikacji może ulec zmianie z powodu operacji skalowania.

Jednak ponieważ środowisko usługi app service zawsze znajduje się w podsieci, masz gwarancję, że wewnętrzny adres IP zasobu obliczeniowego z uruchomieniem aplikacji zawsze będzie znajdować się w zakresie CIDR podsieci.  W rezultacie, gdy szczegółowe listy ACL lub sieciowe grupy zabezpieczeń są używane do bezpiecznego dostępu do innych punktów końcowych w sieci wirtualnej, zakres podsieci zawierający środowisko usługi aplikacji musi mieć dostęp.

Na poniższym diagramie przedstawiono te pojęcia bardziej szczegółowo:

![Wychodzące adresy sieciowe][OutboundNetworkAddresses]

Na powyższym diagramie:

* Ponieważ publiczny adres VIP środowiska usługi aplikacji jest 192.23.1.2, jest to wychodzący adres IP używany podczas nawiązywać połączenia z punktami końcowymi "Internet".
* Zakres CIDR podsieci zawierającej środowisko usługi app service wynosi 10.0.1.0/26.  Inne punkty końcowe w ramach tej samej infrastruktury sieci wirtualnej będą widzieć wywołania z aplikacji jako pochodzące z tego zakresu adresów.

## <a name="calls-between-app-service-environments"></a>Połączenia między środowiskami usługi aplikacji
Bardziej złożony scenariusz może wystąpić, jeśli wdrożysz wiele środowisk usługi app service w tej samej sieci wirtualnej i wywołasz wychodzące z jednego środowiska usługi app service do innego środowiska usługi aplikacji.  Te typy wywołań środowiska usługi różnych aplikacji będą również traktowane jako wywołania "Internet".

Na poniższym diagramie przedstawiono przykład architektury warstwowej z aplikacjami w jednym środowisku usługi App Service (np. aplikacje internetowe "Drzwi frontowe") wywołujących aplikacje w drugim środowisku usługi App Service (np. wewnętrzne aplikacje interfejsu API zaplecza, które nie są przeznaczone do dostępu z Internetu). 

![Połączenia między środowiskami usługi aplikacji][CallsBetweenAppServiceEnvironments] 

W powyższym przykładzie środowisko usługi aplikacji "ASE One" ma wychodzący adres IP 192.23.1.2.  Jeśli aplikacja uruchomiona w tym środowisku usługi aplikacji wywołuje wychodzące do aplikacji uruchomionej w drugim środowisku usługi app service ("ASE Two") znajdującej się w tej samej sieci wirtualnej, wywołanie wychodzące będzie traktowane jako wywołanie "Internet".  W rezultacie ruch sieciowy przychodzący w drugim środowisku usługi app service będzie pokazywał jako pochodzący z 192.23.1.2 (tj. nie zakres adresów podsieci pierwszego środowiska usługi aplikacji).

Mimo że wywołania między różnymi środowiskami usługi app service są traktowane jako wywołania "Internet", gdy oba środowiska usługi app service znajdują się w tym samym regionie platformy Azure, ruch sieciowy pozostanie w regionalnej sieci Azure i nie będzie fizycznie przepływać przez publicznego Internetu.  W rezultacie można użyć sieciowej grupy zabezpieczeń w podsieci drugiego środowiska usługi app service, aby zezwolić tylko na połączenia przychodzące z pierwszego środowiska usługi app service (którego wychodzący adres IP wynosi 192.23.1.2), zapewniając w ten sposób bezpieczną komunikację między aplikacją Środowiska usług.

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Szczegółowe informacje na temat portów przychodzących używanych przez środowiska usługi App Service i używania sieciowych grup zabezpieczeń do kontrolowania ruchu przychodzącego są dostępne [tutaj][controllinginboundtraffic].

Szczegółowe informacje na temat używania tras zdefiniowanych przez użytkownika w celu przyznania wychodzącego dostępu do Internetu do środowisk usługi app service są dostępne w tym [artykule][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

