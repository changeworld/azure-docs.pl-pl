---
title: Kontrolować ruch przychodzący do środowiska usługi App Service — platformy Azure
description: Dowiedz się więcej o sposobie konfigurowania reguły zabezpieczeń sieciowych, aby kontrolować ruch przychodzący do środowiska usługi App Service.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130793"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Jak kontrolować ruch przychodzący do środowiska usługi App Service
## <a name="overview"></a>Omówienie
Środowisko usługi App Service mogą być tworzone w **albo** z siecią wirtualną usługi Azure Resource Manager **lub** klasycznego modelu wdrażania [sieci wirtualnej] [ virtualnetwork].  W czasie, utworzonych przez usługę App Service Environment można zdefiniować nową sieć wirtualną i nowej podsieci.  Alternatywnie można utworzyć środowisko App Service w istniejącej sieci wirtualnej i wcześniej istniejącej podsieci.  Za pomocą zmiany wprowadzone w czerwca 2016 roku za pomocą środowisk ASE można także wdrożyć w sieciach wirtualnych, które używają zakresów adresów publicznych lub przestrzeni adresowych RFC1918 (czyli prywatnych adresów).  Aby uzyskać więcej informacji na temat tworzenia środowiska usługi App Service, zobacz [jak utworzyć środowisko App Service Environment][HowToCreateAnAppServiceEnvironment].

Środowiska App Service Environment należy zawsze tworzyć w obrębie podsieci, ponieważ podsieć zapewnia granicę sieci, która może służyć do blokowania ruchu przychodzącego za urządzeniami i usługami w taki sposób, że ruch HTTP i HTTPS jest akceptowany tylko z określonych nadrzędnego Adresy IP.

Dla ruchu przychodzącego i wychodzącego ruchu sieciowego w podsieci jest kontrolowany przy użyciu [sieciowej grupy zabezpieczeń][NetworkSecurityGroups]. Kontrolowanie ruchu przychodzącego nie wymaga tworzenia reguł zabezpieczeń sieciowych w sieciowej grupie zabezpieczeń, a następnie przypisując zabezpieczenia sieci w grupie podsieci zawierającej środowisko App Service Environment.

Po przypisaniu sieciową grupę zabezpieczeń do podsieci, ruch przychodzący do aplikacji w środowisku App Service jest dozwolone/zablokowane oparte na dozwolonych i odmowy reguł zdefiniowanych w sieciowej grupie zabezpieczeń.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Liczba przychodzących porty sieciowe używane w środowisku usługi App Service
Przed blokowania przychodzącego ruchu sieciowego z sieciową grupą zabezpieczeń, to musisz znać zbiór porty wymagane i opcjonalne sieciowe używane przez usługę App Service Environment.  Przypadkowo zamykanie wyłączone na niektóre porty ruchu może spowodować utratę funkcji w środowisku usługi App Service.

Oto lista portów używanych przez usługę App Service Environment. Wszystkie porty są **TCP**, chyba że wyraźnie inaczej:

* 454:  **Wymagany port** używane przez infrastrukturę platformy Azure do zarządzania i konserwacji środowisk usługi App Service za pomocą protokołu SSL.  Nie blokuje ruchu skierowanego do tego portu.  Ten port jest zawsze powiązana z publicznych adresów VIP ASE.
* 455:  **Wymagany port** używane przez infrastrukturę platformy Azure do zarządzania i konserwacji środowisk usługi App Service za pomocą protokołu SSL.  Nie blokuje ruchu skierowanego do tego portu.  Ten port jest zawsze powiązana z publicznych adresów VIP ASE.
* 80:  Domyślny port dla ruchu przychodzącego protokołu HTTP do aplikacji działających w planach usługi App Service w środowisku usługi App Service.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port jest powiązany z adresu wewnętrznego modułu równoważenia obciążenia środowiska ASE.
* 443: Domyślny port dla ruchu przychodzącego protokołu SSL do aplikacji działających w planach usługi App Service w środowisku usługi App Service.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port jest powiązany z adresu wewnętrznego modułu równoważenia obciążenia środowiska ASE.
* 21:  Kanał kontrolny dla połączenia FTP.  Ten port może być bezpiecznie zablokowany, jeśli FTP nie jest używany.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port może być powiązany adres wewnętrznego modułu równoważenia obciążenia za środowisko ASE.
* 990:  Kanał kontrolny dla protokołu FTPS.  Ten port można bezpiecznie zablokowane, jeśli nie jest używany FTPS.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port może być powiązany adres wewnętrznego modułu równoważenia obciążenia za środowisko ASE.
* 10001-10020: Kanały danych dla połączenia FTP.  Podobnie jak w przypadku kanał kontrolny tych portów można bezpiecznie zablokowane, jeśli FTP nie jest używany.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port może być powiązana z adresu wewnętrznego modułu równoważenia obciążenia środowiska ASE.
* 4016: Używany do zdalnego debugowania w programie Visual Studio 2012.  Ten port można bezpiecznie zablokowane, jeśli ta funkcja nie jest używany.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port jest powiązany z adresu wewnętrznego modułu równoważenia obciążenia środowiska ASE.
* 4018: Używany do zdalnego debugowania w programie Visual Studio 2013.  Ten port można bezpiecznie zablokowane, jeśli ta funkcja nie jest używany.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port jest powiązany z adresu wewnętrznego modułu równoważenia obciążenia środowiska ASE.
* 4020: Używany do zdalnego debugowania w programie Visual Studio 2015.  Ten port można bezpiecznie zablokowane, jeśli ta funkcja nie jest używany.  Na środowisko ASE wewnętrznego modułu równoważenia obciążenia, włączone ten port jest powiązany z adresu wewnętrznego modułu równoważenia obciążenia środowiska ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Łączność wychodząca i wymagań dotyczących systemu DNS
App Service Environment działała poprawnie również wymaga wychodzący dostęp do różnych punktów końcowych. Pełną listę zewnętrzne punkty końcowe, używane przez środowisko ASE znajduje się w sekcji "Wymagane łączność sieciową" [konfiguracji sieci dla usługi ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artykułu.

Środowiska usługi App Service wymaga prawidłową infrastruktury DNS skonfigurowany w ramach sieci wirtualnej.  Jeśli z jakiegokolwiek powodu zmiany konfiguracji DNS po utworzeniu środowiska usługi App Service, deweloperzy wymusić środowiska usługi App Service w celu zastosowania nowej konfiguracji DNS.  Wyzwalanie stopniowe ponowny rozruch środowiska za pomocą ikony "Restart" znajdujący się w górnej części bloku zarządzania środowiska App Service Environment w [witryny Azure portal] [ NewPortal] spowoduje, że środowisko w celu odebrania nowych DNS Konfiguracja.

Zalecane jest również, że dowolne niestandardowe serwery DNS w sieci wirtualnej można zainstalować wcześniej przed utworzeniem środowiska usługi App Service.  Zmieniła się konfiguracja DNS sieci wirtualnej podczas tworzenia środowiska App Service Environment, która spowoduje niepowodzenie procesu tworzenia środowiska App Service Environment.  W podobny względzie Jeśli niestandardowego serwera DNS istnieje na drugiej stronie bramy sieci VPN, a serwer DNS jest nieosiągalny lub jest niedostępny, proces tworzenia środowiska App Service Environment również powiedzie się.

## <a name="creating-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Szczegółowe informacje o sposób działania grup zabezpieczeń sieci można znaleźć następujących [informacji][NetworkSecurityGroups].  Zarządzanie usługami platformy Azure, które dotyczą w poniższym przykładzie wyróżniono sieciowych grup zabezpieczeń, ze szczególnym uwzględnieniem Konfigurowanie i stosowanie sieciową grupę zabezpieczeń do podsieci, która zawiera usługę App Service Environment.

**Uwaga:** Sieciowe grupy zabezpieczeń można skonfigurować graficznie przy użyciu [witryny Azure Portal](https://portal.azure.com) lub za pomocą programu Azure PowerShell.

Sieciowe grupy zabezpieczeń są najpierw tworzone jako autonomicznej jednostki skojarzone z subskrypcją. Ponieważ sieciowe grupy zabezpieczeń są tworzone w regionie platformy Azure, upewnij się, czy sieciowa grupa zabezpieczeń został utworzony w tym samym regionie, co środowisko App Service Environment.

Poniżej przedstawiono tworzenie sieciowej grupy zabezpieczeń:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po utworzeniu sieciowej grupy zabezpieczeń, co najmniej jedną regułę zabezpieczeń sieci są dodawane do niego.  Ponieważ zestaw reguł mogą ulec zmianie wraz z upływem czasu, zalecane jest rozdzielenia schematu numerowania, służącą do priorytety reguł ułatwiają wstawić dodatkowe reguły wraz z upływem czasu.

W poniższym przykładzie pokazano reguły, która jawnie udziela dostępu do portów zarządzania wymaganych przez infrastrukturę platformy Azure do zarządzania i obsługa środowiska usługi App Service.  Należy pamiętać, że cały ruch zarządzania przepływu za pośrednictwem protokołu SSL i jest zabezpieczony przez certyfikaty klienta, więc mimo, że są otwarte porty są niedostępne dla dowolnej jednostki inne niż infrastruktury zarządzania systemu Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Zablokowanie dostępu do portu 80 i 443 "hide" App Service Environment za nadrzędnego urządzenia lub usługi, należy znać adres IP nadrzędnego.  Na przykład, jeśli używasz zapory aplikacji sieci web (WAF), zapory aplikacji sieci Web ma swój własny adres IP (lub adresy), których użyto podczas buforowania ruch do podrzędnego środowiska App Service Environment.  Musisz użyć tego adresu IP w *element SourceAddressPrefix* parametru reguły zabezpieczeń sieci.

W poniższym przykładzie ruch przychodzący z konkretnego adresu IP nadrzędnego jest jawnie dozwolone.  Adres *1.2.3.4* służy jako symbol zastępczy adres IP zapory aplikacji sieci Web nadrzędnego.  Zmień wartość na zgodny z adresem używane przez nadrzędne urządzenia lub usługi.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

W razie potrzeby Obsługa FTP następujące reguły może służyć jako szablon, aby przyznać dostęp do danych i port sterowania FTP portów kanału.  Ponieważ stanowego protokołu FTP, nie można kierować ruchem FTP za pośrednictwem tradycyjnych urządzenia zapory lub serwera proxy HTTP/HTTPS.  W takim przypadku należy ustawić *element SourceAddressPrefix* na inną wartość — na przykład adres IP zakresu maszyny dewelopera lub wdrażania na FTP, które są uruchomione klientów. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Uwaga:** zakres portów kanału danych mogą ulec zmianie w okresie wersji zapoznawczej.)

Jeśli zdalnego debugowania programu Visual Studio jest używany, następujące reguły pokazują, jak udzielić dostępu.  Istnieje oddzielny reguły dla każdej obsługiwanej wersji programu Visual Studio, ponieważ każda wersja używa innego portu dla zdalnego debugowania.  Podobnie jak w przypadku dostępu FTP zdalnego debugowania ruch może przepływać poprawnie za pośrednictwem tradycyjnych zapory aplikacji sieci Web lub serwera proxy urządzenia.  *Element SourceAddressPrefix* zamiast tego można ustawić zakresu adresów IP komputery deweloperskie z programem Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Przypisywanie sieciowej grupy zabezpieczeń do podsieci
Sieciowa grupa zabezpieczeń ma domyślną regułę zabezpieczeń, która nie zezwala na dostęp do całego ruchu zewnętrznego.  Wynikiem połączenie opisane powyżej reguły zabezpieczeń sieci z domyślną regułą zabezpieczeń blokuje ruch przychodzący jest czy tylko ruch z źródłowy zakres adresów skojarzony z *Zezwalaj* akcji będą mogli wysyłać ruch do aplikacji działających w środowisku usługi App Service.

Po zapełnieniu reguły zabezpieczeń sieciowej grupy zabezpieczeń, musi być przypisany do podsieci zawierającej środowisko App Service Environment.  Polecenie przypisanie odwołuje się do zarówno nazwę sieci wirtualnej, w którym znajduje się środowisko App Service Environment, a także nazwę podsieci, w której utworzono środowiska App Service Environment.  

W poniższym przykładzie pokazano sieciową grupę zabezpieczeń do podsieci i sieci wirtualnej:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Po pomyślnym przypisanie do grupy zabezpieczeń sieci (przypisanie jest operacji długotrwałych i może potrwać kilka minut), tylko dla ruchu przychodzącego ruchu dopasowania *Zezwalaj* reguły pomyślnie będzie korzystał z aplikacji w usłudze App Service Środowisko.

Aby informacje były kompletne poniższy przykład pokazuje, jak do usunięcia, a zatem dis-skojarzyć sieciową grupę zabezpieczeń z podsieci:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Specjalne uwagi dotyczące jawne połączenie IP SSL
Jeśli aplikacja jest skonfigurowana za pomocą jawnych adresów IP SSL (dotyczy *tylko* do środowiska ASE, które mają publicznych adresów VIP), zamiast korzystać z adresu IP domyślnego środowiska App Service Environment, HTTP i HTTPS ruch jest przenoszony do podsieci za pośrednictwem inny zbiór porty inne niż porty 80 i 443.

Poszczególne pary porty używane przez każdy adres IP SSL można znaleźć w interfejsie użytkownika portalu z bloku UX szczegółów środowiska App Service Environment.  Wybierz pozycję "wszystkie ustawienia"--> "Adresy IP".  Blok "Adresy IP" zawiera spis wszystkich jawnie skonfigurowanych adresów IP protokołu SSL dla usługi App Service Environment, wraz z parą specjalne portu, która jest używana do kierowania ruchu HTTP i HTTPS, które są skojarzone z poszczególnymi adresami IP SSL.  Jest to pary port, który musi być używane dla parametrów DestinationPortRange, podczas konfigurowania reguł w sieciowej grupie zabezpieczeń.

W przypadku aplikacji w środowisku ASE jest skonfigurowany do używania protokołu SSL adresów IP, klientów zewnętrznych nie będą widzieć i nie musisz martwić się o mapowaniu pary port specjalny.  Ruch do aplikacji będzie przepływać zwykle do skonfigurowanego adresu IP SSL.  Tłumaczenie parą port specjalny automatycznie odbywa się wewnętrznie podczas ostatnim etapie routing ruchu w podsieci zawierającej środowisko ASE. 

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [wprowadzenie do środowiska App Service Environment][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje dotyczące aplikacji bezpiecznego nawiązywania połączeń do zasobu zaplecza ze środowiska usługi App Service, zobacz [bezpiecznego nawiązywania połączenia z zasobami zaplecza ze środowiska usługi App Service][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

