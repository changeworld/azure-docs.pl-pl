---
title: Bezpieczne łączenie z zasobami zaplecza ze środowiska usługi aplikacji
description: Dowiedz się więcej o tym, jak bezpiecznie łączyć się z zasobami zaplecza ze środowiska usługi aplikacji.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836525"
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Bezpieczne łączenie z zasobami zaplecza ze środowiska usługi aplikacji
## <a name="overview"></a>Omówienie
Od chwili utworzenia środowiska usługi aplikacji jest zawsze w **albo** sieci wirtualnej platformy Azure Resource Manager **lub** klasycznego modelu wdrażania [sieci wirtualnej] [ virtualnetwork], połączenia wychodzące z środowiska usługi aplikacji do innych zasobów w wewnętrznej bazie danych może przepływać wyłącznie za pośrednictwem sieci wirtualnej.  Z ostatnie zmiany wprowadzone w czerwca 2016 ASEs można także wdrożyć w sieci wirtualnych, które używają zakresy publicznych adresów lub RFC1918 przestrzeni adresów (czyli adresy prywatne).  

Na przykład może być programu SQL Server w klastrze maszyn wirtualnych z portu 1433 zablokowana.  Punkt końcowy może być ACLd tylko umożliwiających dostęp do innych zasobów w tej samej sieci wirtualnej.  

Inny przykład poufnych punkty końcowe mogą uruchomić lokalnie i być podłączona do platformy Azure przez albo [lokacja-lokacja] [ SiteToSite] lub [Azure ExpressRoute] [ ExpressRoute] połączenia.  W związku z tym tylko zasoby w sieciach wirtualnych połączony lokacja-lokacja lub ExpressRoute tunele będą możliwość dostępu z lokalnych punktów końcowych.

Wszystkie te scenariusze aplikacje działające w środowisku usługi aplikacji będzie można bezpiecznie łączyć się z różnych serwerów i zasobów z.  Ruch wychodzący z aplikacji działających w środowisku usługi aplikacji do prywatnego punktów końcowych w tej samej sieci wirtualnej (lub podłączone do tej samej sieci wirtualnej), zostanie tylko przepływ za pośrednictwem sieci wirtualnej.  Ruch wychodzący do punktów końcowych prywatnych nie będą przepływać za pośrednictwem publicznej sieci Internet.

Jedno zastrzeżenie: dotyczy ruchu wychodzącego ze środowiska usługi aplikacji do punktów końcowych sieci wirtualnej.  Środowiska usługi App Service można nawiązać połączenia z punktami końcowymi maszyn wirtualnych znajdujących się w **tego samego** podsieci co środowiska usługi aplikacji.  To zwykle nie powinna być problem tak długo, jak środowiska usługi App Service są wdrażane w podsieci zarezerwowane do wyłącznego użytku przez środowisko usługi aplikacji.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Łączność wychodząca i wymagania dotyczące systemu DNS
Dla środowiska usługi aplikacji do poprawnego wymaga wychodzący dostęp do różnych punktów końcowych. Pełna lista zewnętrznych punktów końcowych używanych przez ASE znajduje się w sekcji "Wymagana łączność sieciową" [konfiguracji sieci ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artykułu.

Środowiska usługi aplikacji wymaga prawidłowy infrastruktury DNS skonfigurowane dla sieci wirtualnej.  Jeśli z jakiegokolwiek powodu konfiguracji DNS zostaną zmienione po utworzeniu środowiska usługi aplikacji, deweloperzy można wymusić środowiska usługi aplikacji do pobrania dla nowej konfiguracji DNS.  Wyzwolenie stopniowego ponowny rozruch środowiska przy użyciu ikony "Restart" znajduje się w górnej części bloku zarządzania środowiska usługi aplikacji w portalu spowoduje, że środowisko do pobrania dla nowej konfiguracji DNS.

Zalecane jest również, że żadnych niestandardowych serwerów DNS w sieci wirtualnej, należy skonfigurować wcześniejsze przed utworzeniem środowiska usługi aplikacji.  Konfiguracja DNS sieci wirtualnej zostanie zmieniona, podczas tworzenia środowiska usługi aplikacji, która spowoduje niepowodzenie procesu tworzenia środowiska usługi aplikacji.  W podobny szyjnej Jeśli istnieje niestandardowy serwer DNS na drugim końcu bramy sieci VPN, a serwer DNS jest nieosiągalny lub jest niedostępny, proces tworzenia środowiska usługi aplikacji również powiedzie się.

## <a name="connecting-to-a-sql-server"></a>Łączenie z serwerem SQL
Typowe konfiguracje programu SQL Server ma punktu końcowego nasłuchiwania na porcie 1433:

![Punkt końcowy serwera SQL][SqlServerEndpoint]

Istnieją dwa podejścia do ograniczania ruchu do tego punktu końcowego:

* [Listy kontroli dostępu do sieci] [ NetworkAccessControlLists] (listy ACL sieci)
* [Grupy zabezpieczeń sieci][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Ograniczanie dostępu do sieci listy kontroli dostępu
Port 1433 mogą być chronione przy użyciu listy kontroli dostępu do sieci.  Przykład poniżej klienta whitelists adresów, pochodzących z wewnątrz sieci wirtualnej i blokuje dostęp do wszystkich innych klientów.

![Przykład listy kontroli dostępu do sieci][NetworkAccessControlListExample]

Wszystkie aplikacje uruchomione w środowisku usługi aplikacji w tej samej sieci wirtualnej, ponieważ program SQL Server będzie mogła nawiązać połączenia przy użyciu wystąpienia programu SQL Server **wewnętrznej sieci wirtualnej** adres IP dla maszyny wirtualnej programu SQL Server.  

Przykład parametry połączenia poniżej odwołuje się do serwera SQL przy użyciu prywatnego adresu IP.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Mimo że maszyna wirtualna ma publiczny punkt końcowy również, próby nawiązania połączenia z użyciem publiczny adres IP zostanie odrzucone z powodu listy ACL sieci. 

## <a name="restricting-access-with-a-network-security-group"></a>Ograniczanie dostępu z sieciową grupą zabezpieczeń
Informacje o innym podejściu do zabezpieczania dostępu jest z sieciową grupą zabezpieczeń.  Grupy zabezpieczeń sieci może odnosić się do poszczególnych maszyn wirtualnych lub w podsieci zawierającej maszyny wirtualne.

Najpierw musi zostać utworzona grupa zabezpieczeń sieci:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Ograniczanie dostępu do tylko ruchu w wewnętrznej sieci wirtualnej jest bardzo prosty z sieciową grupą zabezpieczeń.  Reguły domyślne w grupie zabezpieczeń sieci tylko zezwalają na dostęp z innych klientów sieci w tej samej sieci wirtualnej.

W związku z tym zablokowanie dostępu do programu SQL Server jest tak proste, jak stosowanie sieciową grupę zabezpieczeń z jej domyślnych reguł albo programem SQL Server lub podsieci zawierających maszyny wirtualne z maszynami wirtualnymi.

Poniższy przykład dotyczy sieciowej grupy zabezpieczeń zawierające podsieci:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

W rezultacie jest zestaw reguł zabezpieczeń, które blokują dostęp zewnętrznych, umożliwiając dostęp do sieci wirtualnej:

![Reguły zabezpieczeń sieciowych domyślne][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [wprowadzenie do środowiska usługi aplikacji][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje związane sterowanie ruch przychodzący do środowiska usługi aplikacji, zobacz [kontrolowanie ruch przychodzący do środowiska usługi aplikacji][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
