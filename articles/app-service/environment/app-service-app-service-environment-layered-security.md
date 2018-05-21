---
title: Architektura zabezpieczeń warstwowych ze środowiska usługi aplikacji
description: Implementacja architektury zabezpieczeń warstwowych, ze środowiska usługi App Service.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 29c928c7d81eb3a2532f735be9132b49db5da373
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementacja architektury zabezpieczeń warstwowych, ze środowiska usługi aplikacji
## <a name="overview"></a>Przegląd
Ponieważ środowiska usługi App Service udostępniają odizolowanym środowisku uruchomieniowym wdrożony w sieci wirtualnej, deweloperzy mogą tworzyć architektury zabezpieczeń warstwowych, zapewniając różne poziomy dostępu do sieci dla każdej warstwy aplikacji fizycznych.

Wspólne dążenie jest Ukryj zapleczy interfejsu API z dostępem do Internetu ogólne i Zezwalaj tylko na interfejsy API do wywołania przez aplikacje sieci web nadrzędnego.  [Sieciowe grupy zabezpieczeń (NSG)] [ NetworkSecurityGroups] pozwala na podsieci zawierający środowiska usługi App Service ograniczenia publiczny dostęp do aplikacji interfejsu API.

Na poniższym diagramie przedstawiono architekturę przykład z aplikacją WebAPI na podstawie wdrożony w środowisku usługi aplikacji.  Trzy wystąpienia aplikacji sieci web w osobnych, wdrożone na trzy osobne środowiska usługi aplikacji wywołań zaplecza w tej samej aplikacji WebAPI.

![Architektura koncepcyjna][ConceptualArchitecture] 

Zielony znak plus wskazują, że grupy zabezpieczeń sieci w podsieci zawierający "apiase" umożliwia wywołania z aplikacji sieci web nadrzędnego jako dobrze wywołania po sobie samym.  Jednak tej samej grupy zabezpieczeń sieci jawnie nie zezwala na dostęp do ogólnego ruch przychodzący z Internetu. 

W dalszej części tego artykułu, który przeprowadzi Cię przez kroki niezbędne do skonfigurowania sieciowej grupy zabezpieczeń w podsieci zawierający "apiase."

## <a name="determining-the-network-behavior"></a>Określanie zachowania sieci
Aby dowiedzieć się, jakie reguły zabezpieczeń sieciowych są potrzebne, musisz określić klientów sieci będzie mogło nawiązać środowiska usługi aplikacji zawierającej aplikację interfejsu API i których klienci będą blokowane.

Ponieważ [sieciowej grupy zabezpieczeń (NSG)] [ NetworkSecurityGroups] są stosowane do podsieci i środowiska usługi App Service są wdrażane na podsieci, zasad zawartych w grupie NSG są stosowane do **wszystkie** aplikacje działające w środowisku usługi aplikacji.  Przy użyciu architektury próbki tego artykułu, po zastosowaniu sieciową grupę zabezpieczeń do podsieci, zawierający "apiase", wszystkie aplikacje uruchomione na "apiase" środowiska usługi aplikacji będą chronione za pomocą tego samego zestawu reguł zabezpieczeń. 

* **Określić wychodzący adres IP nadrzędne obiekty wywołujące:** co to jest adres IP lub adresy nadrzędne obiekty wywołujące?  Te adresy musi być jawnie dozwolone dostępu w grupie NSG.  Ponieważ wywołań między środowiska usługi App Service są traktowane jako wywołania "Internet", wychodzący adres IP przypisany do każdego z trzech nadrzędnego środowiska usługi aplikacji musi mieć dostęp w grupie NSG dla podsieci "apiase".   Aby uzyskać więcej informacji na temat określania wychodzący adres IP dla aplikacji działających w środowisku usługi aplikacji, zobacz [architektury sieci] [ NetworkArchitecture] artykuł z omówieniem.
* **W aplikacji interfejsu API zaplecza trzeba wywołać się?**  Czasami ich i niewielkie punkt jest scenariusz, w którym aplikacja zaplecza musi wywołać się.  Jeśli aplikacji interfejsu API zaplecza w środowisku usługi aplikacji musi wywoływać samego siebie, również jest traktowane jako wywołanie "Internet".  W architekturze próbki wymaga zezwalania na dostęp z wychodzący adres IP "apiase" środowiska usługi aplikacji oraz.

## <a name="setting-up-the-network-security-group"></a>Konfigurowanie grupy zabezpieczeń sieci
Zestaw wychodzących adresów IP są znane, następnym krokiem po do utworzenia grupy zabezpieczeń sieci.  Można utworzyć grupy zabezpieczeń sieci dla obu Menedżera zasobów opartych na sieci wirtualnych, a także klasycznych sieci wirtualnych.  Poniższe przykłady Pokaż tworzenia i konfigurowania grupy NSG w klasycznym sieci wirtualnej przy użyciu programu Powershell.

Dla architektury próbki tych środowisk znajdują się w południowo-środkowe stany, więc pusta grupa NSG jest tworzona w tym regionie:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Najpierw jawnie zezwolić na reguły jest dodawany do infrastruktury zarządzania platformy Azure, zgodnie z opisem w artykule na [ruch przychodzący] [ InboundTraffic] dla środowiska usługi App Service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Następnie dwie reguły są dodawane do zezwala na wywołania HTTP i HTTPS z pierwszego środowiska usługi aplikacji nadrzędnego ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Przepłukać i powtórz dla drugiego i trzeciego nadrzędnego środowiska usługi App Service ("fe2ase" i "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Ponadto należy udzielić dostępu do środowiska usługi aplikacji interfejsu API zaplecza wychodzących adres IP, dzięki czemu można wywołania zwrotnego do tego samego.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Żadne inne reguły zabezpieczeń sieci są wymagane, ponieważ co grupa NSG zawiera zestaw reguł domyślnych, które blokują dostęp przychodzące z Internetu, domyślnie.

Poniżej przedstawiono pełną listę reguł w grupie zabezpieczeń sieci.  Należy zwrócić uwagę, jak ostatni reguły, która zostanie wyróżniona, bloki ruchu przychodzącego dostęp z wszystkich wywołań, innego niż wywołań, w których jawnie przyznano dostęp.

![Konfiguracja grupy NSG][NSGConfiguration] 

Ostatnim krokiem jest aby zastosować grupy NSG do podsieci, która zawiera "apiase" środowiska usługi aplikacji.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Grupa NSG stosowana do podsieci tylko trzy nadrzędnego środowiska usługi App Service i środowiska usługi aplikacji zawierające zaplecza interfejsu API, są dozwolone do wywołania w środowisku "apiase".

## <a name="additional-links-and-information"></a>Linki do dodatkowych i informacji
Informacje o [sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md).

Opis [wychodzących adresów IP] [ NetworkArchitecture] i środowiska usługi App Service.

[Porty sieciowe] [ InboundTraffic] używane przez środowiska usługi App Service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
