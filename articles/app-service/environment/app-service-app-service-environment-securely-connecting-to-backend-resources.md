---
title: Łączenie się z zapleczem v1
description: Dowiedz się, jak bezpiecznie łączyć się z zasobami wewnętrznej bazy danych ze środowiska usługi app service. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687307"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Bezpieczne łączenie się z zasobami zaplecza ze środowiska usługi app service
Ponieważ środowisko usługi aplikacji jest zawsze tworzone **w** sieci wirtualnej usługi Azure Resource Manager **lub** klasycznej [sieci wirtualnej][virtualnetwork]modelu wdrażania, połączenia wychodzące ze środowiska usługi app service do innych zasobów zaplecza mogą przepływać wyłącznie za pośrednictwem sieci wirtualnej.  Po ostatniej zmianie wprowadzonej w czerwcu 2016 r. środowiska ASE można również wdrożyć w sieciach wirtualnych korzystających z zakresów adresów publicznych lub przestrzeni adresowych RFC1918 (tj. adresów prywatnych).  

Na przykład może istnieć sql server uruchomiony na klastrze maszyn wirtualnych z portem 1433 zablokowane.  Punktem końcowym może być ACLd zezwalać tylko na dostęp z innych zasobów w tej samej sieci wirtualnej.  

Innym przykładem poufnych punktów końcowych może działać lokalnie i być połączone z platformą Azure za pośrednictwem [połączenia lokacji do lokacji][SiteToSite] lub [usługi Azure ExpressRoute.][ExpressRoute]  W rezultacie tylko zasoby w sieciach wirtualnych połączonych z tunelami lokacja lokacja lub usługa ExpressRoute będą mogły uzyskiwać dostęp do lokalnych punktów końcowych.

We wszystkich tych scenariuszach aplikacje działające w środowisku usługi app service będą mogły bezpiecznie łączyć się z różnymi serwerami i zasobami.  Ruch wychodzący z aplikacji uruchomionych w środowisku usługi aplikacji do prywatnych punktów końcowych w tej samej sieci wirtualnej (lub połączonych z tą samą siecią wirtualną) będzie przepływał tylko przez sieć wirtualną.  Ruch wychodzący do prywatnych punktów końcowych nie będzie przepływał przez publiczny Internet.

Jedno zastrzeżenie dotyczy ruchu wychodzącego ze środowiska usługi app service do punktów końcowych w sieci wirtualnej.  Środowiska usługi aplikacji nie mogą dotrzeć do punktów końcowych maszyn wirtualnych znajdujących się w **tej samej** podsieci co środowisko usługi aplikacji.  Zwykle nie powinno to być problemem, o ile środowiska usługi app service są wdrażane w podsieci zarezerwowanej do wyłącznego użytku tylko przez środowisko usługi app service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Outbound Connectivity and DNS Requirements (Wymagania dotyczące łączności wychodzącej i systemu DNS)
Aby środowisko usługi aplikacji działało poprawnie, wymaga dostępu wychodzącego do różnych punktów końcowych. Pełna lista zewnętrznych punktów końcowych używanych przez program ASE znajduje się w sekcji "Wymagana łączność sieciowa" [w artykule Konfiguracja sieci dla usługi ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Środowiska usługi app service wymagają prawidłowej infrastruktury DNS skonfigurowanej dla sieci wirtualnej.  Jeśli z jakiegoś powodu konfiguracja DNS zostanie zmieniona po utworzeniu środowiska usługi aplikacji, deweloperzy mogą wymusić na środowisku usługi app service pobranie nowej konfiguracji DNS.  Wyzwalanie ponownego uruchamiania środowiska stopniowego przy użyciu ikony "Uruchom ponownie" znajdującej się w górnej części bloku zarządzania środowiskiem usługi app service w portalu spowoduje, że środowisko odbierze nową konfigurację DNS.

Zaleca się również, aby wszystkie niestandardowe serwery DNS w sieci wirtualnej były instalowane z wyprzedzeniem przed utworzeniem środowiska usługi app service.  Jeśli konfiguracja DNS sieci wirtualnej zostanie zmieniona podczas tworzenia środowiska usługi aplikacji, spowoduje to niepowodzenie procesu tworzenia środowiska usługi app service.  W podobnym duchu, jeśli niestandardowy serwer DNS istnieje na drugim końcu bramy sieci VPN, a serwer DNS jest nieosiągalny lub niedostępny, proces tworzenia środowiska usługi aplikacji również zakończy się niepowodzeniem.

## <a name="connecting-to-a-sql-server"></a>Łączenie się z programem SQL Server
Wspólna konfiguracja programu SQL Server ma punkt końcowy nasłuchiwania na porcie 1433:

![Punkt końcowy programu SQL Server][SqlServerEndpoint]

Istnieją dwa podejścia do ograniczania ruchu do tego punktu końcowego:

* [Listy kontroli dostępu do sieci][NetworkAccessControlLists] (sieci ACL)
* [Sieciowe grupy zabezpieczeń][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Ograniczanie dostępu za pomocą listy ACL sieciowej
Port 1433 można zabezpieczyć za pomocą listy kontroli dostępu do sieci.  W poniższym przykładzie znajdują się adresy klientów pochodzące z sieci wirtualnej i blokuje dostęp do wszystkich innych klientów.

![Przykład listy kontroli dostępu do sieci][NetworkAccessControlListExample]

Wszystkie aplikacje uruchomione w środowisku usługi app service w tej samej sieci wirtualnej co SQL Server będą mogły łączyć się z wystąpieniem programu SQL Server przy użyciu wewnętrznego adresu IP **sieci wirtualnej** dla maszyny wirtualnej programu SQL Server.  

Przykładowy ciąg połączenia poniżej odwołuje się do programu SQL Server przy użyciu jego prywatnego adresu IP.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Mimo że maszyna wirtualna ma również publiczny punkt końcowy, próby połączenia przy użyciu publicznego adresu IP zostaną odrzucone z powodu listy ACL sieciowej. 

## <a name="restricting-access-with-a-network-security-group"></a>Ograniczanie dostępu za pomocą sieciowej grupy zabezpieczeń
Alternatywne podejście do zabezpieczania dostępu jest z sieciową grupą zabezpieczeń.  Sieciowe grupy zabezpieczeń mogą być stosowane do poszczególnych maszyn wirtualnych lub do podsieci zawierającej maszyny wirtualne.

Najpierw należy utworzyć grupę zabezpieczeń sieci:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Ograniczanie dostępu tylko do ruchu wewnętrznego sieci wirtualnej jest bardzo proste w sieci grupy zabezpieczeń.  Reguły domyślne w sieciowej grupie zabezpieczeń zezwalają na dostęp tylko od innych klientów sieci w tej samej sieci wirtualnej.

W rezultacie zablokowanie dostępu do programu SQL Server jest tak proste, jak zastosowanie sieciowej grupy zabezpieczeń z domyślnymi regułami do maszyn wirtualnych z uruchomionym programem SQL Server lub podsieci zawierającej maszyny wirtualne.

Poniższy przykład dotyczy sieciowej grupy zabezpieczeń do podsieci zawierającej:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Wynik końcowy jest zestawem reguł zabezpieczeń, które blokują dostęp zewnętrzny, umożliwiając jednocześnie dostęp wewnętrzny sieci wirtualnej:

![Domyślne reguły zabezpieczeń sieci][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć korzystanie ze środowisk usługi App Service, zobacz [Wprowadzenie do środowiska usługi aplikacji][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje dotyczące kontrolowania ruchu przychodzącego do środowiska usługi App Service, zobacz [Kontrolowanie ruchu przychodzącego do środowiska usługi aplikacji][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
