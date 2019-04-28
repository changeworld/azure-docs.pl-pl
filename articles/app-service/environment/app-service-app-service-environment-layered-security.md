---
title: Architektura zabezpieczeń przy użyciu środowisk usługi App Service — platformy Azure z warstwami
description: Implementowanie warstwowej architektury zabezpieczeń przy użyciu środowisk usługi App Service.
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
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130691"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementowanie warstwowej architektury zabezpieczeń przy użyciu środowisk usługi App Service
## <a name="overview"></a>Omówienie
Ponieważ środowisk usługi App Service zapewnia odizolowanym środowisku uruchomieniowym wdrożone w sieci wirtualnej, deweloperzy mogą tworzyć warstwowej architektury zabezpieczeń podając różne poziomy dostępu do sieci dla każdej warstwy aplikacji fizycznych.

Wspólne dążenie jest ukryć interfejsu API zaplecza z ogólnego dostępu do Internetu i zezwolić tylko na interfejsy API ma zostać wywołana przez nadrzędne web apps.  [Sieciowe grupy zabezpieczeń (NSG)] [ NetworkSecurityGroups] może służyć w podsieciach zawierający środowisk usługi App Service do ograniczenia dostępu publicznego do aplikacji interfejsu API.

Poniższy diagram przedstawia architekturę przykładu za pomocą aplikacji WebAPI, na podstawie wdrożone w środowisku usługi App Service.  Trzy wystąpienia aplikacji sieci web w osobnych, wdrożone w trzech oddzielnych środowisk usługi App Service, bezpieczne wywołania serwer zaplecza w tej samej aplikacji WebAPI.

![Architektura koncepcyjna][ConceptualArchitecture] 

Zielony znak plus wskazują, że sieciowej grupy zabezpieczeń w podsieci zawierającej "apiase" umożliwia wywołania z aplikacji sieci web nadrzędnego jako dobrze wywołania od siebie samego.  Jednak tej samej grupy zabezpieczeń sieci jawnie nie zezwala na dostęp do ogólnego ruch przychodzący z Internetu. 

W dalszej części tego artykułu opisano kroki wymagane do skonfigurowania sieciowej grupy zabezpieczeń w podsieci zawierającej "apiase."

## <a name="determining-the-network-behavior"></a>Określanie zachowania sieci
Aby dowiedzieć się, jakie reguły zabezpieczeń sieciowych są wymagane, należy określić, których klienci sieci będą mogli dotrzeć do środowisku usługi App Service zawierający aplikację interfejsu API i klientów, którzy będą blokowane.

Ponieważ [sieciowe grupy zabezpieczeń (NSG)] [ NetworkSecurityGroups] są stosowane do podsieci oraz środowisk usługi App Service są wdrażane w podsieci, reguły zawarte w sieciowej grupie zabezpieczeń mają zastosowanie do **wszystkich** aplikacji uruchomiona w środowisku usługi App Service.  Za pomocą przykładowej architektury na potrzeby tego artykułu, po zastosowaniu sieciową grupę zabezpieczeń do podsieci zawierającej "apiase", wszystkie aplikacje uruchomione na "apiase" App Service Environment będą chronione za pomocą tego samego zestawu reguł zabezpieczeń. 

* **Określ adres IP ruchu wychodzącego nadrzędnych obiektów wywołujących:**  Co to jest adres IP lub adresów nadrzędnych obiektów wywołujących?  Te adresy musi być jawnie dozwolone dostępu w sieciowej grupie zabezpieczeń.  Ponieważ wywołań między środowisk usługi App Service są traktowane jako wywołania "Internet", wychodzący adres IP przypisany do każdego z trzech nadrzędnych środowisk usługi App Service musi mieć dostęp w sieciowej grupie zabezpieczeń dla podsieci "apiase".   Aby uzyskać więcej informacji na temat określania adres IP ruchu wychodzącego dla aplikacji działających w środowisku usługi App Service, zobacz [architektury sieci] [ NetworkArchitecture] artykuł z omówieniem.
* **W aplikacji interfejsu API zaplecza będą wywoływać samego siebie?**  Punkt czasami zapomina i subtelne jest scenariusz, w którym serwer zaplecza w aplikację aby wywołać sam siebie.  Jeśli aplikacja interfejsu API zaplecza w środowisku usługi App Service musi wywoływać samego siebie, również jest traktowana jako wywołanie "Internet".  W przykładowej architektury wymaga zezwolenia na dostęp z wychodzący adres IP "apiase" App Service Environment także.

## <a name="setting-up-the-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń
Gdy zbiór wychodzących adresów IP są znane, następnym krokiem jest do utworzenia sieciowej grupy zabezpieczeń.  Sieciowe grupy zabezpieczeń mogą być tworzone dla obu Menedżera zasobów opartych na sieci wirtualne, a także klasycznych sieci wirtualnych.  Poniższe przykłady pokazują tworzenie i konfigurowanie sieciowej grupy zabezpieczeń na klasyczną siecią wirtualną przy użyciu programu Powershell.

Dla przykładowej architektury środowiska znajdują się w południowo-środkowe stany USA, więc pusty sieciowa grupa zabezpieczeń jest tworzony w tym regionie:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Najpierw jawnie zezwolić na jest dodana reguła dotycząca infrastruktury zarządzania systemu Azure, jak podano w artykule na [ruch przychodzący] [ InboundTraffic] dla środowisk usługi App Service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Następnie dwie reguły są dodawane do umożliwiają wywołania HTTP i HTTPS z pierwszym nadrzędnego usługi App Service Environment ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Przepłukać i powtórz dla drugiego i trzeciego nadrzędnego środowisk usługi App Service ("fe2ase" i "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Na koniec udostępnienia adres IP ruchu wychodzącego środowiska App Service Environment dla interfejsu API zaplecza dzięki czemu można wywołania zwrotnego do niej samej.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nie inne reguły zabezpieczeń sieciowych są wymagane, ponieważ co sieciowa grupa zabezpieczeń zawiera zestaw domyślnych reguł, które blokują dostęp dla ruchu przychodzącego z Internetu, domyślnie.

Poniżej przedstawiono pełną listę reguł w sieciowej grupie zabezpieczeń.  Należy pamiętać o tym, jak ostatni reguły, która jest wyróżniony, bloków dla ruchu przychodzącego dostęp ze wszystkich obiektów wywołujących niż wywołań, w których jawnie przyznano dostęp.

![Konfiguracja sieciowa grupa zabezpieczeń][NSGConfiguration] 

Ostatnim krokiem jest, aby zastosować sieciową grupę zabezpieczeń do podsieci, która zawiera "apiase" App Service Environment.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Za pomocą sieciowej grupy zabezpieczeń do podsieci tylko trzy nadrzędnego środowisk usługi App Service i zawierający zaplecza interfejsu API, usługa App Service Environment są dozwolone do wywołania w środowisku "apiase".

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Informacje o [sieciowe grupy zabezpieczeń](../../virtual-network/security-overview.md).

Opis [wychodzące adresy IP] [ NetworkArchitecture] i środowisk usługi App Service.

[Porty sieciowe] [ InboundTraffic] posługują się środowisk usługi App Service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
