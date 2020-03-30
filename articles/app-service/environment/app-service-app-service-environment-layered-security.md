---
title: Zabezpieczenia warstwowe w wersji 1
description: Dowiedz się, jak zaimplementować architekturę zabezpieczeń warstwowych w środowisku usługi app service. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688797"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementowanie warstwowej architektury zabezpieczeń ze środowiskami usługi app service
Ponieważ środowiska usługi app service zapewniają izolowane środowisko uruchomieniowe wdrożone w sieci wirtualnej, deweloperzy mogą utworzyć architekturę zabezpieczeń warstwowych, zapewniając różne poziomy dostępu do sieci dla każdej warstwy aplikacji fizycznych.

Typowym pragnieniem jest ukrycie zaplecza interfejsu API przed ogólnym dostępem do Internetu i zezwalanie tylko na wywoływanie interfejsów API przez nadrzędne aplikacje sieci web.  [Sieciowe grupy zabezpieczeń (NSG)][NetworkSecurityGroups] mogą być używane w podsieciach zawierających środowiska usługi app service w celu ograniczenia publicznego dostępu do aplikacji interfejsu API.

Na poniższym diagramie przedstawiono przykładową architekturę z aplikacją opartą na webapi wdrożoną w środowisku usługi app service.  Trzy oddzielne wystąpienia aplikacji sieci web, wdrożone w trzech oddzielnych środowiskach usługi app service, nawiązują wywołania zaplecza do tej samej aplikacji WebAPI.

![Architektura konceptualna][ConceptualArchitecture] 

Zielone znaki plus wskazują, że grupa zabezpieczeń sieci w podsieci zawierającej "apiase" umożliwia przychodzące wywołania z nadrzędnych aplikacji sieci web, a także wywołania od siebie.  Jednak ta sama grupa zabezpieczeń sieci jawnie odmawia dostępu do ogólnego ruchu przychodzącego z Internetu. 

Poniżej dalsza część artykułu Poniżej dalsza część artykułu Czynności niezbędne do skonfigurowania sieciowej grupy zabezpieczeń w podsieci zawierającej "apiase".

## <a name="determining-the-network-behavior"></a>Określanie zachowania sieciowego
Aby wiedzieć, jakie reguły zabezpieczeń sieciowych są potrzebne, należy określić, którzy klienci sieciowi będą mogli dotrzeć do środowiska usługi app service zawierającego aplikację interfejsu API i którzy klienci będą blokowani.

Ponieważ [sieciowe grupy zabezpieczeń (NSG)][NetworkSecurityGroups] są stosowane do podsieci, a środowiska usługi app service są wdrażane w podsieciach, reguły zawarte w sieciowej grupy zabezpieczeń mają zastosowanie do **wszystkich** aplikacji uruchomionych w środowisku usługi aplikacji.  Korzystając z przykładowej architektury dla tego artykułu, po zastosowaniu sieciowej grupy zabezpieczeń do podsieci zawierającej "apiase", wszystkie aplikacje uruchomione w środowisku usługi app service "apiase" będą chronione przez ten sam zestaw reguł zabezpieczeń. 

* **Określ wychodzący adres IP wywołań nadrzędnych:**  Jaki jest adres IP lub adresy nadrzędnych rozmówców?  Adresy te muszą być jawnie dozwolone dostęp w nsg.  Ponieważ wywołania między środowiskami usługi app service są uważane za wywołania "Internet", wychodzący adres IP przypisany do każdego z trzech środowisk usługi aplikacji nadrzędnych musi mieć dostęp w sieciowej strefy sieciowej dla podsieci "apiase".   Aby uzyskać więcej informacji na temat określania wychodzącego adresu IP dla aplikacji działających w środowisku usługi aplikacji, zobacz omówienie [architektury sieci.][NetworkArchitecture]
* **Czy aplikacja interfejsu API zaplecza musi wywołać siebie?**  Czasami pomijane i subtelny punkt jest scenariusz, w którym aplikacja zaplecza musi wywołać siebie.  Jeśli aplikacja interfejsu API zaplecza w środowisku usługi aplikacji musi wywołać siebie, jest również traktowana jako wywołanie "Internet".  W przykładowej architekturze wymaga to zezwolenia na dostęp z wychodzącego adresu IP środowiska usługi app service "apiase".

## <a name="setting-up-the-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń
Po znaniu zestawu wychodzących adresów IP następnym krokiem jest utworzenie sieciowej grupy zabezpieczeń.  Sieciowe grupy zabezpieczeń można tworzyć zarówno dla sieci wirtualnych opartych na Menedżerze zasobów, jak i klasycznych sieci wirtualnych.  Poniższe przykłady pokazują tworzenie i konfigurowanie sieciowej sieciowej sieciowej w klasycznej sieci wirtualnej przy użyciu programu Powershell.

Dla architektury przykładu środowiska znajdują się w południowo-środkowej części USA, więc pusta grupa NSG jest tworzony w tym regionie:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Najpierw jawna reguła zezwalania jest dodawana dla infrastruktury zarządzania platformy Azure, jak wspomniano w artykule na [temat ruchu przychodzącego][InboundTraffic] dla środowisk usługi app service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Następnie zostaną dodane dwie reguły zezwalają na wywołania HTTP i HTTPS z pierwszego środowiska usługi aplikacji nadrzędnej ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Spłukać i powtórzyć dla drugiego i trzeciego środowiska usługi aplikacji upstream ("fe2ase" i "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Na koniec udzielić dostępu do wychodzącego adresu IP środowiska usługi app service interfejsu u zaplecza, dzięki czemu można wywołać z powrotem do siebie.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Żadne inne reguły zabezpieczeń sieciowych nie są wymagane, ponieważ każda grupa zabezpieczeń sieciowa ma zestaw reguł domyślnych, które domyślnie blokują dostęp przychodzący z Internetu.

Pełna lista reguł w sieciowej grupie zabezpieczeń przedstawiona jest poniżej.  Należy zauważyć, jak ostatnia reguła, która jest wyróżniona, blokuje dostęp przychodzący od wszystkich wywołujących, innych niż wywołujących, które zostały jawnie przyznane dostęp.

![Konfiguracja sieciowej grupy zabezpieczeń][NSGConfiguration] 

Ostatnim krokiem jest zastosowanie sieciowej sieciowej grupki sieciowej do podsieci, która zawiera środowisko usługi aplikacji "apiase".

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Z sieciowej sieciowej stosowane do podsieci, tylko trzy środowiska usługi aplikacji nadrzędnego i środowiska usługi aplikacji zawierające zaplecza interfejsu API, mogą wywoływać w środowisku "apiase".

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Informacje o [sieciowych grupach zabezpieczeń](../../virtual-network/security-overview.md).

Opis [wychodzących adresów IP][NetworkArchitecture] i środowisk usługi app service.

[Porty sieciowe][InboundTraffic] używane przez środowiska usługi app service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
