---
title: Bezpieczne łączenie kopii zakończenia zasoby ze środowiska usługi App Service — platformy Azure
description: Dowiedz się więcej o tym, jak bezpiecznie łączyć się z zasobami zaplecza ze środowiska usługi App Service.
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
ms.custom: seodec18
ms.openlocfilehash: aea51234d26e5dbaef836419c2a13a12f8083e6f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130708"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Bezpieczne łączenie kopii zakończenia zasoby ze środowiska usługi App Service
## <a name="overview"></a>Omówienie
Ponieważ środowisko App Service zawsze jest tworzony w **albo** z siecią wirtualną usługi Azure Resource Manager **lub** klasycznego modelu wdrażania [sieci wirtualnej] [ virtualnetwork], połączenia wychodzące z środowiska usługi App Service do innych zasobów wewnętrznej bazy danych może przepływać wyłącznie za pośrednictwem sieci wirtualnej.  Z ostatnich zmian wprowadzonych w czerwca 2016 roku za pomocą środowisk ASE można także wdrożyć w sieciach wirtualnych, które używają zakresów adresów publicznych lub przestrzeni adresowych RFC1918 (czyli prywatnych adresów).  

Na przykład może być program SQL Server uruchomiony w klastrze maszyn wirtualnych przy użyciu portu 1433 zablokowany.  Punkt końcowy może być ACLd, aby zezwolić tylko na dostęp z innych zasobów w tej samej sieci wirtualnej.  

Inny przykład poufnych punkty końcowe mogą być uruchamiane w środowisku lokalnym i podłączenia do platformy Azure za pośrednictwem albo [Site-to-Site] [ SiteToSite] lub [usługi Azure ExpressRoute] [ ExpressRoute] połączeń.  W rezultacie tylko zasoby w sieciach wirtualnych, nawiązanie połączenia lokacja-lokacja lub ExpressRoute tuneli będzie można dostępu lokalne punkty końcowe.

Wszystkie te scenariusze aplikacje działające w środowisku usługi App Service będzie nawiązania bezpiecznego połączenia z różnymi usługami i zasobami.  Ruch wychodzący z aplikacji działających w środowisku usługi App Service do prywatnych punktów końcowych w tej samej sieci wirtualnej (lub podłączone do tej samej sieci wirtualnej), będą tylko przepływu za pośrednictwem sieci wirtualnej.  Ruch wychodzący do prywatnych punktów końcowych nie będą przepływać za pośrednictwem publicznej sieci Internet.

Jedno zastrzeżenie: ma zastosowanie do ruchu wychodzącego ze środowiska usługi App Service do punktów końcowych w sieci wirtualnej.  Środowiska usługi App Service nie może połączyć się punkty końcowe maszyn wirtualnych znajdujących się w **tego samego** podsieci środowiska App Service Environment.  To zwykle nie powinna być problemem tak długo, jak środowisk usługi App Service są wdrażane w podsieci, zarezerwowane do wyłącznego użytku przez tylko usługi App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Łączność wychodząca i wymagań dotyczących systemu DNS
App Service Environment działała poprawnie wymaga wychodzący dostęp do różnych punktów końcowych. Pełną listę zewnętrzne punkty końcowe, używane przez środowisko ASE znajduje się w sekcji "Wymagane łączność sieciową" [konfiguracji sieci dla usługi ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artykułu.

Środowiska usługi App Service wymaga prawidłową infrastruktury DNS skonfigurowany w ramach sieci wirtualnej.  Jeśli z jakiegokolwiek powodu zmiany konfiguracji DNS po utworzeniu środowiska usługi App Service, deweloperzy wymusić środowiska usługi App Service w celu zastosowania nowej konfiguracji DNS.  Wyzwalanie stopniowe ponowny rozruch środowiska za pomocą ikony "Restart" znajdujący się w górnej części bloku zarządzania środowiska App Service Environment w portalu spowoduje, że do środowiska, aby wczytać nowej konfiguracji DNS.

Zalecane jest również, że dowolne niestandardowe serwery DNS w sieci wirtualnej można zainstalować wcześniej przed utworzeniem środowiska usługi App Service.  Zmieniła się konfiguracja DNS sieci wirtualnej podczas tworzenia środowiska App Service Environment, która spowoduje niepowodzenie procesu tworzenia środowiska App Service Environment.  W podobny względzie Jeśli niestandardowego serwera DNS istnieje na drugiej stronie bramy sieci VPN, a serwer DNS jest nieosiągalny lub jest niedostępny, proces tworzenia środowiska App Service Environment również powiedzie się.

## <a name="connecting-to-a-sql-server"></a>Nawiązywanie połączenia z programem SQL Server
Typowa konfiguracja programu SQL Server ma punkt końcowy nasłuchuje na porcie 1433:

![Punkt końcowy serwera SQL][SqlServerEndpoint]

Istnieją dwa podejścia do ograniczania ruchu do tego punktu końcowego:

* [Listy kontroli dostępu do sieci] [ NetworkAccessControlLists] (sieci listy ACL)
* [Sieciowe grupy zabezpieczeń][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Ograniczanie dostępu przy użyciu listy ACL sieci
Port 1433 mogą być chronione przy użyciu listy kontroli dostępu do sieci.  Przykład poniżej klienta dozwolonych adresów, pochodzących z wewnątrz sieci wirtualnej i blokuje dostęp do wszystkich pozostałych klientów.

![Przykład listy kontroli dostępu do sieci][NetworkAccessControlListExample]

Wszystkie aplikacje uruchomione w środowisku App Service w tej samej sieci wirtualnej, ponieważ program SQL Server będą mogli nawiązać połączenie za pomocą wystąpienia programu SQL Server **wewnętrznej sieci wirtualnej** adres IP dla maszyny wirtualnej programu SQL Server.  

Poniższe przykładowe parametry połączenia odwołuje się do programu SQL Server przy użyciu jego prywatny adres IP.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Mimo, że maszyna wirtualna ma publiczny punkt końcowy także, próby nawiązania połączenia przy użyciu publicznego adresu IP zostanie odrzucone z powodu listy ACL sieci. 

## <a name="restricting-access-with-a-network-security-group"></a>Ograniczanie dostępu przy użyciu sieciowej grupy zabezpieczeń
Alternatywnym podejściem do zabezpieczania dostępu jest z sieciową grupą zabezpieczeń.  Sieciowe grupy zabezpieczeń można stosować do poszczególnych maszyn wirtualnych lub podsieci zawierającej maszynę wirtualną.

Najpierw należy utworzyć grupę zabezpieczeń sieci:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Ograniczanie dostępu do tylko ruch wewnętrzny sieci wirtualnej jest bardzo proste z sieciową grupą zabezpieczeń.  Reguły domyślne w sieciowej grupie zabezpieczeń zezwalanie na dostęp tylko z innych klientów sieci w tej samej sieci wirtualnej.

W rezultacie takie zablokowanie dostępu do programu SQL Server jest proste i polega na zastosowaniu sieciowej grupy zabezpieczeń z jej domyślnych reguł do obu maszyn wirtualnych z programu SQL Server lub podsieci zawierającej maszyny wirtualne.

Poniższy przykład dotyczy sieciową grupę zabezpieczeń zawierającą podsieci:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Wynik końcowy to zestaw reguł zabezpieczeń, które blokują dostęp zewnętrzny, zezwalając na dostęp do sieci wirtualnej:

![Domyślnych reguł zabezpieczeń sieciowych][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [wprowadzenie do środowiska App Service Environment][IntroToAppServiceEnvironment]

Szczegóły wokół kontrolowanie ruchu przychodzącego do środowiska App Service Environment można znaleźć [kontrolowanie ruchu przychodzącego do środowiska usługi App Service][ControlInboundASE]

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
