---
title: Sterowanie ruchem przychodzącym w wersji 1
description: Dowiedz się, jak kontrolować ruch przychodzący do środowiska usługi app service. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804405"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Jak kontrolować ruch przychodzący do środowiska usługi aplikacji
## <a name="overview"></a>Omówienie
Środowisko usługi aplikacji można utworzyć **w** sieci wirtualnej usługi Azure Resource Manager **lub** w klasycznej [sieci wirtualnej][virtualnetwork]modelu wdrażania.  Nową sieć wirtualną i nową podsieć można zdefiniować w momencie tworzenia środowiska usługi aplikacji.  Alternatywnie środowisko usługi aplikacji można utworzyć w istniejącej sieci wirtualnej i istniejącej podsieci.  Po zmianie wprowadzonej w czerwcu 2016 r. środowiska ASE można również wdrożyć w sieciach wirtualnych korzystających z zakresów adresów publicznych lub przestrzeni adresowych RFC1918 (tj. adresów prywatnych).  Aby uzyskać więcej informacji na temat tworzenia środowiska usługi aplikacji, zobacz [Jak utworzyć środowisko usługi app service][HowToCreateAnAppServiceEnvironment].

Środowisko usługi aplikacji musi być zawsze tworzone w podsieci, ponieważ podsieć zapewnia granicę sieci, która może służyć do blokowania ruchu przychodzącego za urządzeniami i usługami nadrzędnymi, tak aby ruch HTTP i HTTPS był akceptowany tylko z określonych nadrzędnych adresów IP.

Przychodzący i wychodzący ruch sieciowy w podsieci jest kontrolowany za pomocą [sieciowej grupy zabezpieczeń][NetworkSecurityGroups]. Kontrolowanie ruchu przychodzącego wymaga utworzenia reguł zabezpieczeń sieci w sieciowej grupie zabezpieczeń, a następnie przypisania sieciowej grupy zabezpieczeń podsieci zawierającej środowisko usługi app service.

Po przypisaniu sieciowej grupy zabezpieczeń do podsieci ruch przychodzący do aplikacji w środowisku usługi app service jest dozwolony/blokowany na podstawie reguł zezwalania i odrzucania zdefiniowanych w sieciowej grupie zabezpieczeń.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Porty sieci przychodzącej używane w środowisku usługi aplikacji
Przed zablokowaniem przychodzącego ruchu sieciowego z sieciową grupą zabezpieczeń należy znać zestaw wymaganych i opcjonalnych portów sieciowych używanych przez środowisko usługi app service.  Przypadkowe zamknięcie ruchu do niektórych portów może spowodować utratę funkcjonalności w środowisku usługi aplikacji.

Poniżej znajduje się lista portów używanych przez środowisko usługi aplikacji. Wszystkie porty są **TCP**, chyba że wyraźnie zaznaczono inaczej:

* 454: **Wymagany port** używany przez infrastrukturę platformy Azure do zarządzania i obsługi środowisk usługi app service za pośrednictwem protokołu TLS.  Nie blokuj ruchu do tego portu.  Ten port jest zawsze związany z publicznym adresem VIP ase.
* 455: **Wymagany port** używany przez infrastrukturę platformy Azure do zarządzania i obsługi środowisk usługi app service za pośrednictwem protokołu TLS.  Nie blokuj ruchu do tego portu.  Ten port jest zawsze związany z publicznym adresem VIP ase.
* 80: Domyślny port dla przychodzącego ruchu HTTP do aplikacji działających w planach usługi app service w środowisku usługi aplikacji.  W ase z włączoną funkcją ILB ten port jest powiązany z adresem ILB ase.
* 443: Domyślny port dla przychodzącego ruchu TLS do aplikacji działających w planach usługi app service w środowisku usługi aplikacji.  W ase z włączoną funkcją ILB ten port jest powiązany z adresem ILB ase.
* 21: Kanał sterowania dla FTP.  Ten port można bezpiecznie zablokować, jeśli ftp nie jest używany.  W przypadku ase z włączoną funkcją ILB ten port może być powiązany z adresem równoważenia obciążenia sieciowego dla ase.
* 990: Kanał sterowania dla FTPS.  Ten port można bezpiecznie zablokować, jeśli ftps nie jest używany.  W przypadku ase z włączoną funkcją ILB ten port może być powiązany z adresem równoważenia obciążenia sieciowego dla ase.
* 10001-10020: Kanały danych dla FTP.  Podobnie jak w przypadku kanału sterującego, porty te mogą być bezpiecznie blokowane, jeśli ftp nie jest używany.  W ase z włączoną funkcją ILB ten port może być powiązany z adresem równoważenia obciążenia sieciowego ASE.
* 4016: Służy do zdalnego debugowania za pomocą programu Visual Studio 2012.  Ten port można bezpiecznie zablokować, jeśli funkcja nie jest używana.  W ase z włączoną funkcją ILB ten port jest powiązany z adresem ILB ase.
* 4018: Służy do zdalnego debugowania za pomocą programu Visual Studio 2013.  Ten port można bezpiecznie zablokować, jeśli funkcja nie jest używana.  W ase z włączoną funkcją ILB ten port jest powiązany z adresem ILB ase.
* 4020: Służy do zdalnego debugowania za pomocą programu Visual Studio 2015.  Ten port można bezpiecznie zablokować, jeśli funkcja nie jest używana.  W ase z włączoną funkcją ILB ten port jest powiązany z adresem ILB ase.

## <a name="outbound-connectivity-and-dns-requirements"></a>Outbound Connectivity and DNS Requirements (Wymagania dotyczące łączności wychodzącej i systemu DNS)
Dla środowiska usługi aplikacji do poprawnego działania, wymaga również dostępu wychodzącego do różnych punktów końcowych. Pełna lista zewnętrznych punktów końcowych używanych przez program ASE znajduje się w sekcji "Wymagana łączność sieciowa" [w artykule Konfiguracja sieci dla usługi ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Środowiska usługi app service wymagają prawidłowej infrastruktury DNS skonfigurowanej dla sieci wirtualnej.  Jeśli z jakiegoś powodu konfiguracja DNS zostanie zmieniona po utworzeniu środowiska usługi aplikacji, deweloperzy mogą wymusić na środowisku usługi app service pobranie nowej konfiguracji DNS.  Wyzwalanie ponownego uruchamiania środowiska stopniowego przy użyciu ikony "Uruchom ponownie" znajdującej się w górnej części bloku zarządzania środowiskiem usługi App Service w [portalu Azure][NewPortal] spowoduje, że środowisko odbierze nową konfigurację DNS.

Zaleca się również, aby wszystkie niestandardowe serwery DNS w sieci wirtualnej były instalowane z wyprzedzeniem przed utworzeniem środowiska usługi app service.  Jeśli konfiguracja DNS sieci wirtualnej zostanie zmieniona podczas tworzenia środowiska usługi aplikacji, spowoduje to niepowodzenie procesu tworzenia środowiska usługi app service.  W podobnym duchu, jeśli niestandardowy serwer DNS istnieje na drugim końcu bramy sieci VPN, a serwer DNS jest nieosiągalny lub niedostępny, proces tworzenia środowiska usługi aplikacji również zakończy się niepowodzeniem.

## <a name="creating-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Aby uzyskać szczegółowe informacje na temat działania grup zabezpieczeń sieciowych, zobacz następujące [informacje][NetworkSecurityGroups].  Przykład usługi Azure Service Management poniżej dotyczy wskazówek dotyczących grup zabezpieczeń sieciowych, ze szczególnym uwzględnieniem konfigurowania i stosowania sieciowej grupy zabezpieczeń do podsieci zawierającej środowisko usługi aplikacji.

**Uwaga:** Sieciowe grupy zabezpieczeń można skonfigurować graficznie przy użyciu [witryny Azure Portal](https://portal.azure.com) lub za pośrednictwem programu Azure PowerShell.

Sieciowe grupy zabezpieczeń są najpierw tworzone jako samodzielna jednostka skojarzona z subskrypcją. Ponieważ sieciowe grupy zabezpieczeń są tworzone w regionie platformy Azure, upewnij się, że grupa zabezpieczeń sieci jest tworzona w tym samym regionie co środowisko usługi aplikacji.

Poniżej przedstawiono tworzenie sieciowej grupy zabezpieczeń:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po utworzeniu sieciowej grupy zabezpieczeń dodawana jest do niej co najmniej jedna reguła zabezpieczeń sieciowych.  Ponieważ zestaw reguł może się zmieniać w czasie, zaleca się rozmieszczenie schematu numeracji używanego dla priorytetów reguł, aby ułatwić wstawianie dodatkowych reguł w czasie.

W poniższym przykładzie przedstawiono regułę, która jawnie udziela dostępu do portów zarządzania potrzebnych przez infrastrukturę platformy Azure do zarządzania i obsługi środowiska usługi app service.  Należy zauważyć, że wszystkie przepływy ruchu zarządzania za pomocą protokołu TLS i jest zabezpieczony przez certyfikaty klientów, więc nawet jeśli porty są otwarte są niedostępne przez dowolną jednostkę inną niż infrastruktura zarządzania platformy Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Podczas blokowania dostępu do portu 80 i 443 do "ukrycia" środowiska usługi aplikacji za urządzeniami lub usługami nadrzędnymi należy znać nadrzędny adres IP.  Na przykład jeśli używasz zapory aplikacji sieci web (WAF), WAF będzie miał swój własny adres IP (lub adresy), który używa podczas proxy ruchu do środowiska usługi aplikacji podrzędnego.  Ten adres IP należy użyć w parametrze *SourceAddressPrefix* reguły zabezpieczeń sieci.

W poniższym przykładzie ruch przychodzący z określonego nadrzędnego adresu IP jest jawnie dozwolony.  Adres *1.2.3.4* jest używany jako symbol zastępczy dla adresu IP nadrzędnego WAF.  Zmień wartość, aby dopasować adres używany przez urządzenie lub usługę nadrzędną.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Jeśli wymagana jest obsługa protokołu FTP, następujące reguły mogą służyć jako szablon do udzielania dostępu do portu sterowania FTP i portów kanałów danych.  Ponieważ protokół FTP jest protokołem stanowym, kierowanie ruchu FTP przez tradycyjną zaporę HTTP/HTTPS lub urządzenie proxy może być niemożliwe.  W takim przypadku należy ustawić *SourceAddressPrefix* na inną wartość — na przykład zakres adresów IP deweloperów lub komputerów wdrożeniowych, na których są uruchomione klienci FTP. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

**(Uwaga:** zakres portów kanału danych może ulec zmianie w okresie podglądu).

Jeśli używane jest zdalne debugowanie za pomocą programu Visual Studio, poniższe reguły pokazują, jak udzielić dostępu.  Istnieje osobna reguła dla każdej obsługiwanej wersji programu Visual Studio, ponieważ każda wersja używa innego portu do zdalnego debugowania.  Podobnie jak w przypadku dostępu FTP, zdalny ruch debugowania może nie przepływać poprawnie za pośrednictwem tradycyjnego urządzenia WAF lub serwera proxy.  *SourceAddressPrefix* zamiast tego można ustawić na zakres adresów IP maszyn deweloperskich z systemem Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Przypisywanie sieciowej grupy zabezpieczeń do podsieci
Sieciowa grupa zabezpieczeń ma domyślną regułę zabezpieczeń, która odmawia dostępu do całego ruchu zewnętrznego.  Wynikiem połączenia opisanych powyżej reguł zabezpieczeń sieciowych i domyślnej reguły zabezpieczeń blokującej ruch przychodzący jest to, że tylko ruch z zakresów adresów źródłowych skojarzonych z akcją *Zezwalaj* będzie mógł wysyłać ruch do aplikacji działających w środowisku usługi aplikacji.

Po wypełnieniu sieciowej grupy zabezpieczeń regułami zabezpieczeń musi ona zostać przypisana do podsieci zawierającej środowisko usługi app service.  Polecenie przypisania odwołuje się zarówno do nazwy sieci wirtualnej, w której znajduje się środowisko usługi app service, jak i do nazwy podsieci, w której utworzono środowisko usługi aplikacji.  

W poniższym przykładzie przedstawiono sieciową grupę zabezpieczeń przypisaną do podsieci i sieci wirtualnej:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Gdy przypisanie sieciowej grupy zabezpieczeń powiedzie się (przypisanie jest długotrwałą operacją i może potrwać kilka minut), tylko dopasowanie ruchu przychodzącego *Zezwalaj* reguły pomyślnie dotrą do aplikacji w środowisku usługi aplikacji.

W celu uzupełnienia w poniższym przykładzie pokazano, jak usunąć i w ten sposób usunąć grupę zabezpieczeń sieci z podsieci:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Uwagi specjalne dotyczące jawnego protokołu IP-SSL
Jeśli aplikacja jest skonfigurowana z jawnym adresem IP-SSL (dotyczy *tylko* środowisk ASE, które mają publiczny adres VIP), zamiast używać domyślnego adresu IP środowiska usługi app service, ruch HTTP i HTTPS przepływa do podsieci za pośrednictwem innego zestawu portów innych niż porty 80 i 443.

Poszczególne pary portów używanych przez każdy adres IP-SSL można znaleźć w interfejsie użytkownika portalu z bloku UX szczegółowe środowisko usługi app service.  Wybierz opcję "Wszystkie ustawienia" --> "adresy IP".  W bloku "Adresy IP" jest wyświetlana tabela wszystkich jawnie skonfigurowanych adresów IP-SSL dla środowiska usługi app service, wraz ze specjalną parą portów używaną do kierowania ruchu HTTP i HTTPS skojarzonego z każdym adresem IP-SSL.  Jest to ta para portów, która musi być używana dla parametrów DestinationPortRange podczas konfigurowania reguł w sieciowej grupie zabezpieczeń.

Gdy aplikacja na ASE jest skonfigurowana do używania protokołu IP-SSL, klienci zewnętrzni nie będą widzieć i nie muszą się martwić o mapowanie pary portów specjalnych.  Ruch do aplikacji będzie płynąć normalnie do skonfigurowany adres IP-SSL.  Tłumaczenie na specjalną parę portów odbywa się automatycznie podczas ostatniego etapu ruchu routingowego do podsieci zawierającej ASE. 

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć korzystanie ze środowisk usługi App Service, zobacz [Wprowadzenie do środowiska usługi aplikacji][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje na temat aplikacji bezpiecznie łączących się z zasobem wewnętrznej bazy danych ze środowiska usługi app service, zobacz [Bezpieczne łączenie się z zasobami zaplecza ze środowiska usługi app service][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

