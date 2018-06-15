---
title: Jak kontrolować ruch przychodzący do środowiska usługi aplikacji
description: Więcej informacji na temat sposobu konfigurowania reguły zabezpieczeń sieciowych, aby kontrolować ruch przychodzący do środowiska usługi aplikacji.
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
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836924"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Jak kontrolować ruch przychodzący do środowiska usługi aplikacji
## <a name="overview"></a>Omówienie
Środowiska usługi aplikacji mogą być tworzone w **albo** sieci wirtualnej platformy Azure Resource Manager **lub** klasycznego modelu wdrażania [sieci wirtualnej][virtualnetwork].  Podczas tworzenia środowiska usługi aplikacji można zdefiniować nowej sieci wirtualnej i nową podsieć.  Alternatywnie można utworzyć środowiska usługi aplikacji w istniejącej sieci wirtualnej i wstępnie istniejącą podsieć.  Wraz ze zmianą w czerwca 2016 ASEs można także wdrożyć w sieci wirtualnych, które używają zakresy publicznych adresów lub RFC1918 przestrzeni adresów (czyli adresy prywatne).  Więcej informacji na temat tworzenia środowiska usługi aplikacji można znaleźć [tworzenie środowiska usługi aplikacji][HowToCreateAnAppServiceEnvironment].

Środowiska usługi aplikacji musi zawsze można utworzyć w podsieci, ponieważ podsieć zawiera granic sieci, która może służyć do blokowania przychodzący ruch związany z nadrzędnego urządzeń i usług w taki sposób, że ruch HTTP i HTTPS jest dopuszczalne tylko z określonych adresów IP nadrzędnego.

Przychodzący i wychodzący ruch sieciowy w podsieci jest kontrolowany przy użyciu [sieciowej grupy zabezpieczeń][NetworkSecurityGroups]. Kontrolowanie ruchu przychodzącego wymaga tworzenia reguły zabezpieczeń sieciowych w grupie zabezpieczeń sieci, a następnie przypisanie zabezpieczenia sieci grupy podsieć zawierająca środowiska usługi aplikacji.

Po grupy zabezpieczeń sieci jest przypisany do podsieci, ruch przychodzący do aplikacji w środowisku usługi aplikacji jest dozwolone/blokowane oparte na Zezwalaj i Odmów reguł zdefiniowanych w grupie zabezpieczeń sieci.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Przychodzący porty używane w środowisku usługi aplikacji
Przed blokowania przychodzącego ruchu sieciowego z sieciową grupą zabezpieczeń, należy znać zbiór wymaganych i opcjonalnych porty używane przez środowisko usługi aplikacji.  Zamykanie przypadkowo poza ruch do Niektóre porty może spowodować utratę funkcjonalności w środowisku usługi aplikacji.

Poniżej znajduje się lista portów używanych przez środowisko usługi aplikacji. Wszystkie porty są **TCP**, chyba że wyraźnie inaczej:

* 454: **wymagany port** używanych przez infrastrukturę platformy Azure do zarządzania i obsługi środowiska usługi App Service za pomocą protokołu SSL.  Nie blokuje ruchu skierowanego do tego portu.  Port ten jest zawsze powiązana do publicznego adresu IP z ASE.
* 455: **wymagany port** używanych przez infrastrukturę platformy Azure do zarządzania i obsługi środowiska usługi App Service za pomocą protokołu SSL.  Nie blokuje ruchu skierowanego do tego portu.  Port ten jest zawsze powiązana do publicznego adresu IP z ASE.
* 80: domyślny port dla ruchu przychodzącego protokołu HTTP do aplikacji działających w plany usługi App Service w środowisku usługi aplikacji.  W elemencie ASE włączone ILB ten port jest powiązany adres ILB ASE.
* 443: domyślny port dla ruchu przychodzącego protokołu SSL do aplikacji działających w plany usługi App Service w środowisku usługi aplikacji.  W elemencie ASE włączone ILB ten port jest powiązany adres ILB ASE.
* 21: kanału kontroli w przypadku usługi FTP.  Ten port można bezpiecznie zablokowane, jeśli nie jest używany FTP.  W elemencie ASE włączone ILB ten port może być powiązana z adresem ILB dla ASE.
* 990: kanał kontrolny dla FTPS.  Ten port można bezpiecznie zablokowane, jeśli nie jest używany FTPS.  W elemencie ASE włączone ILB ten port może być powiązana z adresem ILB dla ASE.
* 10001 10020: kanały danych w przypadku usługi FTP.  Podobnie jak w przypadku kanał kontrolny tych portów można bezpiecznie zablokowane, jeśli FTP nie jest używany.  W elemencie ASE włączone ILB ten port może być powiązana ASE ILB adres.
* 4016: używanej do zdalnego debugowania w programie Visual Studio 2012.  Ten port można bezpiecznie zablokowane, jeśli funkcja nie jest używany.  W elemencie ASE włączone ILB ten port jest powiązany adres ILB ASE.
* 4018: używany do zdalnego debugowania za pomocą programu Visual Studio 2013.  Ten port można bezpiecznie zablokowane, jeśli funkcja nie jest używany.  W elemencie ASE włączone ILB ten port jest powiązany adres ILB ASE.
* 4020: używany do zdalnego debugowania za pomocą programu Visual Studio 2015.  Ten port można bezpiecznie zablokowane, jeśli funkcja nie jest używany.  W elemencie ASE włączone ILB ten port jest powiązany adres ILB ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Łączność wychodząca i wymagania dotyczące systemu DNS
Dla środowiska usługi aplikacji działać poprawnie wymagany jest również wychodzący dostęp do różnych punktów końcowych. Pełna lista zewnętrznych punktów końcowych używanych przez ASE znajduje się w sekcji "Wymagana łączność sieciową" [konfiguracji sieci ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artykułu.

Środowiska usługi aplikacji wymaga prawidłowy infrastruktury DNS skonfigurowane dla sieci wirtualnej.  Jeśli z jakiegokolwiek powodu konfiguracji DNS zostaną zmienione po utworzeniu środowiska usługi aplikacji, deweloperzy można wymusić środowiska usługi aplikacji do pobrania dla nowej konfiguracji DNS.  Wyzwolenie stopniowego ponowny rozruch środowiska przy użyciu ikony "Restart" znajduje się w górnej części bloku zarządzania środowiska usługi aplikacji w [portalu Azure] [ NewPortal] spowoduje, że środowisko do pobrania dla nowej konfiguracji DNS.

Zalecane jest również, że żadnych niestandardowych serwerów DNS w sieci wirtualnej, należy skonfigurować wcześniejsze przed utworzeniem środowiska usługi aplikacji.  Konfiguracja DNS sieci wirtualnej zostanie zmieniona, podczas tworzenia środowiska usługi aplikacji, która spowoduje niepowodzenie procesu tworzenia środowiska usługi aplikacji.  W podobny szyjnej Jeśli istnieje niestandardowy serwer DNS na drugim końcu bramy sieci VPN, a serwer DNS jest nieosiągalny lub jest niedostępny, proces tworzenia środowiska usługi aplikacji również powiedzie się.

## <a name="creating-a-network-security-group"></a>Tworzenie grupy zabezpieczeń sieci
Aby uzyskać szczegółowe informacje o sposobie działania grup zabezpieczeń sieci, zobacz następujące [informacji][NetworkSecurityGroups].  Zarządzanie usługami Azure, które w poniższym przykładzie dotyka na prezentuje grup zabezpieczeń sieci, nacisk na konfigurowanie i stosowanie sieciową grupę zabezpieczeń do podsieci, która zawiera środowiska usługi aplikacji.

**Uwaga:** grup zabezpieczeń sieci można skonfigurować, używając graficznie [Azure Portal](https://portal.azure.com) lub za pomocą programu Azure PowerShell.

Grupy zabezpieczeń sieci najpierw są tworzone jako element autonomiczny skojarzone z subskrypcją. Ponieważ grupy zabezpieczeń sieci są tworzone w regionie Azure, upewnij się, że w tym samym regionie co środowiska usługi aplikacji zostanie utworzona grupa zabezpieczeń sieci.

Poniżej przedstawiono tworzenie grupy zabezpieczeń sieci:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Po utworzeniu grupy zabezpieczeń sieci, co najmniej jednej reguły zabezpieczeń sieci są dodawane do niego.  Ponieważ zestaw reguł, mogą ulec zmianie w czasie, zalecane jest Rozmieść schematu numerowania służącą do priorytety reguł ułatwiają wstawić dodatkowych reguł wraz z upływem czasu.

W poniższym przykładzie pokazano regułę, która jawnie udziela dostępu do portów zarządzania wymagane przez infrastrukturę platformy Azure do zarządzania i obsługa środowiska usługi aplikacji.  Należy pamiętać, że cały ruch zarządzania przepływa za pośrednictwem protokołu SSL i jest zabezpieczone certyfikaty klienta, nawet jeśli są otwarte porty są niedostępne dla dowolnej jednostki inne niż infrastruktura zarządzania platformy Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Zablokowanie dostępu do portu 80 i 443 "ukryć" środowiska usługi aplikacji za nadrzędnego urządzenia lub usługi, należy znać adres IP nadrzędnego.  Na przykład, jeśli używasz zapory aplikacji sieci web (WAF) zapory aplikacji sieci Web będzie mieć własny adres IP (lub adresy), których używa podczas buforowania ruchu podrzędne środowiska usługi aplikacji.  Musisz użyć tego adresu IP w *SourceAddressPrefix* parametru reguły zabezpieczeń sieci.

W poniższym przykładzie ruch przychodzący z określonego adresu IP nadrzędnego jest jawnie dozwolone.  Adres *1.2.3.4* jest używany jako symbol zastępczy dla adresu IP nadrzędnego zapory aplikacji sieci Web.  Zmień wartość do dopasowania adres używany przez urządzenie nadrzędnego lub usługi.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

W razie potrzeby Obsługa FTP następujące reguły mogą służyć jako szablon, można przyznać dostęp do danych i port kontroli FTP portów kanału.  Ponieważ FTP jest protokołem stanowe, nie można kierować ruchem FTP za pomocą tradycyjnych urządzenia zapory lub serwera proxy HTTP/HTTPS.  W takim przypadku należy ustawić *SourceAddressPrefix* innej wartości — na przykład adres IP zakresu maszyn deweloperem lub wdrażania na FTP, które są uruchomione klientów. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Uwaga:** zakres portów kanału danych mogą ulec zmianie w okresie używania wersji zapoznawczej.)

Jeśli używana jest debugowanie zdalne z programem Visual Studio, następujące reguły pokazują, jak można udzielić dostępu.  Istnieje reguła osobne dla każdej obsługiwanej wersji programu Visual Studio, ponieważ każda wersja używa innego portu do zdalnego debugowania.  Podobnie jak w przypadku dostępu FTP zdalnego debugowania ruchu może nie odzwierciedlać prawidłowo za pośrednictwem tradycyjnych zapory aplikacji sieci Web lub serwera proxy urządzenia.  *SourceAddressPrefix* zamiast tego można ustawić zakresu adresów IP maszyn developer działanie programu Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Przypisywanie sieciowej grupy zabezpieczeń do podsieci
Grupa zabezpieczeń sieci ma domyślną regułę zabezpieczeń, która nie zezwala na dostęp do całego zewnętrznego ruchu.  Wynik z połączenia sieci zasad zabezpieczeń opisanych powyżej i domyślną regułę zabezpieczeń blokuje ruch przychodzący, jest tylko ruch z zakresów adresów źródłowy skojarzony z *Zezwalaj* akcji będą mogli przesyłać dane do aplikacji działających w środowisku usługi aplikacji.

Po sieciowej grupy zabezpieczeń jest wypełniane przy użyciu reguły zabezpieczeń, musi zostać przypisane do podsieci, zawierającej środowiska usługi aplikacji.  Polecenie przypisania odwołuje się zarówno nazwę sieci wirtualnej, w którym znajduje się środowiska usługi aplikacji, jak i nazwę podsieci, w której został utworzony środowiska usługi aplikacji.  

W poniższym przykładzie pokazano sieciową grupę zabezpieczeń do podsieci i sieci wirtualnej:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Po pomyślnym przypisanie do grupy zabezpieczeń sieci (przypisanie jest długotrwałych operacji i może zająć kilka minut), tylko ruchu przychodzącego ruchu dopasowania *Zezwalaj* reguły pomyślnie osiągną aplikacji w środowisku usługi aplikacji.

Aby informacje były kompletne poniższym przykładzie pokazano, jak usunąć i w związku z tym dis-skojarzenia sieciowej grupy zabezpieczeń z podsieci:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Uwagi dotyczące protokołu SSL jawnego adresu IP
Jeśli aplikacja jest konfigurowana jawne adresów IP protokołu SSL (dotyczy *tylko* do ASEs mających publicznego adresu VIP), zamiast używać domyślnego adresu IP środowiska usługi aplikacji, HTTP i HTTPS ruchu, jest przenoszony do podsieci za pośrednictwem innego zestawu porty inne niż porty 80 i 443.

Poszczególne pary porty używane przez każdy adres IP SSL można znaleźć w interfejsie użytkownika portalu z bloku UX szczegóły środowiska usługi aplikacji.  Wybierz opcję "wszystkie ustawienia"--> "Adresów IP".  Blok "Adresów IP" zawiera spis wszystkich jawnie skonfigurowanych adresów IP protokołu SSL dla środowiska usługi aplikacji oraz pary port specjalny, który jest używany do kierowania ruchu HTTP i HTTPS, które są skojarzone z poszczególnych adresów IP protokołu SSL.  Jest to pary port, który musi zostać użyte dla parametrów DestinationPortRange podczas konfigurowania reguły do grupy zabezpieczeń sieci.

Po skonfigurowaniu aplikacji w elemencie ASE do używania protokołu SSL IP klientów zewnętrznych nie będą widzieć i nie trzeba martwić mapowanie pary specjalne portu.  Ruch do aplikacji będzie zwykle przepływać do skonfigurowanych adresów IP protokołu SSL.  Tłumaczenie parę port specjalny automatycznie odbywa się wewnętrznie podczas końcowej gałęzi routingu ruchu w podsieci, zawierającej ASE. 

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [wprowadzenie do środowiska usługi aplikacji][IntroToAppServiceEnvironment]

Aby uzyskać szczegółowe informacje dotyczące aplikacji bezpiecznego połączenia do wewnętrznej bazy danych zasobu ze środowiska usługi aplikacji, zobacz [bezpiecznego łączenia z zasobami zaplecza ze środowiska usługi aplikacji][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

