---
title: Jak używać usługi Azure API Management przy użyciu sieci wirtualnych
description: Dowiedz się, jak skonfigurować połączenie z siecią wirtualną w usłudze Azure API Management i dostęp do usług sieci web za jego pośrednictwem.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: 532c1051522410c496fb3809c06c7e3a74340adb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006050"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak używać usługi Azure API Management przy użyciu sieci wirtualnych
Sieci wirtualne platformy Azure (Vnet) umożliwiają umieszczenie wszystkich zasobów platformy Azure w sieci lecz-internet, która umożliwia kontrolę dostępu do. Te sieci mogą być następnie połączone do sieci w środowisku lokalnym przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej na temat sieci wirtualnych platformy Azure rozpoczyna się od informacji w tym miejscu: [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Usługa Azure API Management można wdrożyć w sieci wirtualnej (VNET), dzięki czemu będzie miał dostęp do usług zaplecza w obrębie sieci. Portal dla deweloperów i API gateway, można skonfigurować dostępne z Internetu lub tylko w obrębie sieci wirtualnej.

> [!NOTE]
> Usługa Azure API Management obsługuje sieci wirtualnych Menedżera zasobów klasycznych i platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie usługi APIM. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Włączanie połączenia sieci Wirtualnej

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Włącz połączenie między SIECIAMI przy użyciu witryny Azure portal

1. Przejdź do swojego wystąpienia usługi APIM w [witryny Azure portal](https://portal.azure.com/).
2. Wybierz **sieci wirtualnej**.
3. Skonfiguruj wystąpienie usługi API Management do wdrożonych w ramach sieci wirtualnej.

    ![Menu usługi API Management w sieci wirtualnej][api-management-using-vnet-menu]
4. Wybierz typ żądanego dostępu:

   * **Zewnętrzne**: portal bramy i deweloper usługi API Management są dostępne z publicznej sieci internet za pośrednictwem modułu równoważenia obciążenia zewnętrznych. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

     ![Publiczna komunikacja równorzędna][api-management-vnet-public]

   * **Wewnętrzny**: usługi API Management bramy i portalu dla deweloperów jest dostępny tylko w obrębie sieci wirtualnej przy użyciu wewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

     ![Prywatna komunikacja równorzędna][api-management-vnet-private]

     Teraz zostanie wyświetlona lista wszystkich regionów, w której zainicjowano usługi API Management. Wybierz sieć Wirtualną i podsieć dla każdego regionu. Lista jest wypełniana przy użyciu wdrożeń klasycznych i sieci wirtualnej usługi Resource Manager dostępne w Twojej subskrypcji platformy Azure, które są skonfigurowane w regionie, który jest konfigurowany.

     > [!NOTE]
     > **Punkt końcowy usługi** na powyższym diagramie zawiera bramy/serwera Proxy, witryny Azure portal, portal dla deweloperów, GIT i bezpośredniego punktu końcowego zarządzania.
     > **Punkt końcowy zarządzania** na powyższym diagramie jest punkt końcowy hostowanej w usłudze, aby zarządzać konfiguracją za pośrednictwem witryny Azure portal i programu Powershell.
     > Należy również zauważyć, że, mimo, że na diagramie przedstawiono adresów IP dla swoich różnych punktów końcowych usługi API Management **tylko** reaguje na jego skonfigurowanych nazw hostów.

     > [!IMPORTANT]
     > W przypadku wdrażania wystąpienia usługi Azure API Management z siecią wirtualną usługi Resource Manager, usługa musi być w dedykowanej podsieci, która nie zawiera żadnych innych zasobów, z wyjątkiem wystąpień usługi Azure API Management. Jeśli zostanie podjęta próba, aby wdrożyć wystąpienie usługi Azure API Management do podsieci sieci Wirtualnej usługi Resource Manager zawierający inne zasoby, wdrożenie zakończy się niepowodzeniem.
     >

     ![Wybierz sieci VPN][api-management-setup-vpn-select]

5. Kliknij przycisk **Zapisz** w górnym pasku nawigacyjnym.
6. Kliknij przycisk **Zastosuj konfigurację sieci** w górnym pasku nawigacyjnym.

> [!NOTE]
> Adres VIP wystąpienia usługi API Management zmieni każdorazowo sieci Wirtualnej jest włączone lub wyłączone.
> Adres VIP zmieni się także wtedy, gdy usługa API Management jest przenoszona z **zewnętrznych** do **wewnętrzne** lub na odwrót
>

> [!IMPORTANT]
> Jeśli usuwanie usługi API Management z sieci Wirtualnej lub zmienić ten, który jest wdrożony w stosowanych wcześniej sieci Wirtualnej mogą pozostać zablokowane przez dwie godziny. W tym okresie nie będzie można usunąć sieci Wirtualnej lub wdrożyć nowy zasób.

## <a name="enable-vnet-powershell"> </a>Włącz połączenia sieci Wirtualnej przy użyciu poleceń cmdlet programu PowerShell
Można również włączyć połączenie między SIECIAMI przy użyciu poleceń cmdlet programu PowerShell

* **Tworzenie usługi API Management w sieci Wirtualnej**: Użyj polecenia cmdlet [New AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) do utworzenia usługi Azure API Management w sieci Wirtualnej.

* **Wdrażanie istniejącej usługi API Management w sieci Wirtualnej**: Użyj polecenia cmdlet [AzApiManagementRegion aktualizacji](/powershell/module/az.apimanagement/update-azapimanagementregion) przenieść istniejącej usługi Azure API Management w sieci wirtualnej.

## <a name="connect-vnet"> </a>Łączenie z usługą sieci web hostowanych w ramach sieci wirtualnej
Usługa API Management jest połączony z siecią wirtualną, uzyskiwanie dostępu do usług zaplecza w nim po nie różni się od uzyskiwania dostępu do usług publicznych. Po prostu wpisz lokalny adres IP lub nazwa hosta (Jeśli serwer DNS jest skonfigurowany do sieci Wirtualnej) usługę sieci web do **adres URL usługi sieci Web** pola podczas tworzenia nowego interfejsu API lub edycji istniejącego.

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej przedstawiono listę typowych problemów z błędną konfiguracją, które mogą wystąpić podczas wdrażania usługi API Management w sieci wirtualnej.

* **Niestandardowe ustawienia serwera DNS**: Usługa API Management jest zależna od kilka usług platformy Azure. Kiedy usługa API Management znajduje się w sieci Wirtualnej za pomocą niestandardowego serwera DNS, należy go rozpoznać nazwy hostów tych usług platformy Azure. Postępuj zgodnie z [to](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wskazówki dotyczące konfiguracją niestandardowego serwera DNS. Zobacz w poniższej tabeli portów i inne wymagania dotyczące sieci dla odwołania.

> [!IMPORTANT]
> Jeśli planujesz użyć niestandardowych serwerów DNS dla sieci Wirtualnej, należy skonfigurować ją **przed** wdrażanie usługi API Management do niego. W przeciwnym razie należy zaktualizować usługę API Management po każdej zmianie serwery DNS, uruchamiając [zastosować operacja konfiguracji sieci](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty wymagane dla usługi API Management**: Przychodzący i wychodzący ruch do podsieci, w której jest wdrażany usługi API Management może być kontrolowana za pomocą [sieciowej grupy zabezpieczeń][Network Security Group]. Jeśli którekolwiek z tych portów są niedostępne, usługa API Management może nie działać prawidłowo i może stać się niedostępny. Co najmniej jeden z tych portów, zablokowane jest innym Typowym problemem błędnej konfiguracji w przypadku korzystania z usługi API Management z sieci Wirtualnej.

<a name="required-ports"> </a> Gdy wystąpienie usługi API Management znajduje się w sieci Wirtualnej, są używane porty w poniższej tabeli.

| Źródło / porty docelowe | Direction          | Protokół transportowy |   [Tagi usługi](../virtual-network/security-overview.md#service-tags) <br> Źródłowy / docelowy   | Cel (*)                                                 | Typ sieci wirtualnej |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Przychodzący            | TCP                | INTERNET / VIRTUAL_NETWORK            | Komunikacja klienta z usługi API Management                      | Zewnętrzna             |
| * / 3443                     | Przychodzący            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Punkt końcowy zarządzania dla witryny Azure portal i programu Powershell         | Zewnętrzne i wewnętrzne  |
| * / 80, 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK / Storage             | **Zależność od usługi Azure Storage**                             | Zewnętrzne i wewnętrzne  |
| * / 80, 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Usługa Azure Active Directory (jeśli dotyczy)                   | Zewnętrzne i wewnętrzne  |
| * / 1433                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / SQL                 | **Dostęp do punktów końcowych usługi Azure SQL**                           | Zewnętrzne i wewnętrzne  |
| * / 5672                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / usługi EventHub            | Zależność dla dziennika do zasad Centrum zdarzeń i agenta monitorowania | Zewnętrzne i wewnętrzne  |
| * / 445                      | Wychodzący           | TCP                | VIRTUAL_NETWORK / Storage             | Zależność od udziału plików platformy Azure dla usługi GIT                      | Zewnętrzne i wewnętrzne  |
| * / 1886                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Niezbędnych do publikowania stan kondycji Resource Health          | Zewnętrzne i wewnętrzne  |
| * / 443                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikowanie diagnostyki dzienników i metryk                        | Zewnętrzne i wewnętrzne  |
| * / 25                       | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Łączenie z usługą przekazywania protokołu SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne i wewnętrzne  |
| * / 587                      | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Łączenie z usługą przekazywania protokołu SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne i wewnętrzne  |
| * / 25028                    | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Łączenie z usługą przekazywania protokołu SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne i wewnętrzne  |
| * / 6381 - 6383              | Dla ruchu przychodzącego i wychodzącego | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Uzyskiwanie dostępu do usługi Azure Cache dla wystąpienia usługi Redis między RoleInstances          | Zewnętrzne i wewnętrzne  |
| * / *                        | Przychodzący            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Moduł równoważenia obciążenia infrastruktury platformy Azure                          | Zewnętrzne i wewnętrzne  |

>[!IMPORTANT]
> Porty, dla którego *przeznaczenia* jest **bold** są wymagane dla usługi API Management została wdrożona pomyślnie. Blokowanie innych portów jednak spowoduje obniżenie wydajności w możliwość korzystania i monitorowania uruchomioną usługę.

+ **Funkcje protokołu SSL**: Aby umożliwić tworzenie łańcucha certyfikatów SSL i sprawdzanie poprawności usługi API Management usługa wymaga połączenia sieciowego ruchu wychodzącego ocsp.msocsp.com oraz mscrl.microsoft.com crl.microsoft.com. Ta zależność nie jest wymagane, jeśli dowolny certyfikat, którego przekazujesz usłudze API Management zawiera pełny łańcuch do głównego urzędu certyfikacji.

+ **Dostęp DNS**: Dostęp ruchu wychodzącego przez port 53 jest wymagany do komunikacji przy użyciu serwerów DNS. Jeśli niestandardowego serwera DNS znajduje się na drugiej stronie bramy sieci VPN, serwer DNS musi być osiągalna z podsieci obsługującej usługi API Management.

+ **Monitorowanie kondycji i metryki**: Połączenia sieciowego ruchu wychodzącego do usługi Azure punktów końcowych monitorowania, które rozwiązania w ramach następujących domen:

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com gdzie `East US 2` jest eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure (Chiny)       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Przekazywania SMTP**: Połączenia sieciowego ruchu wychodzącego do przekazywania SMTP, który jest rozpoznawany jako na hoście `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` i `ies.global.microsoft.com`

+ **Portal dla deweloperów CAPTCHA**: Połączenia sieciowego ruchu wychodzącego dla CAPTCHA portalu dla deweloperów, który jest rozpoznawany jako na hoście `client.hip.live.com`.

+ **Azure portal Diagnostics**: Aby włączyć przepływ dzienników diagnostycznych z witryny Azure portal, korzystając z rozszerzenia usługi API Management z poziomu wewnątrz sieci wirtualnej, a dostęp ruchu wychodzącego do `dc.services.visualstudio.com` na porcie 443 jest wymagany. Pomaga to w rozwiązywaniu problemów, które mogą twarzy w przypadku korzystania z rozszerzenia.

+ **Wymuszanie tunelowania ruchu do zapory środowiska lokalnego przy użyciu Expressroute lub sieci wirtualne urządzenie**: Typowa konfiguracja klienta jest, aby zdefiniować własne trasy domyślnej (0.0.0.0/0), która wymusza cały ruch z usługi API Management delegowane podsieci do usługi flow za pośrednictwem zapory w środowisku lokalnym lub do wirtualnego urządzenia sieciowego. Ten przepływ ruchu niezmiennie przerywa łączność z usługą Azure API Management, ponieważ ruch wychodzący jest zablokowane w środowisku lokalnym lub translatora adresów Sieciowych będzie nierozpoznawalną zbiór adresów, które nie będą działać z różnymi punkty końcowe platformy Azure. To rozwiązanie wymaga, należy wykonać kilka czynności:

  * Włączanie punktów końcowych usługi w podsieci, w której wdrażana jest usługa API Management. [Punkty końcowe usługi] [ ServiceEndpoints] muszą być włączone dla usługi Azure Sql, Azure Storage, Azure Event Hub i Azure, magistrali usług. Włączanie punktów końcowych bezpośrednio z usługi API Management delegowanego podsieci z tymi usługami umożliwia ich do używania sieci szkieletowej platformy Microsoft Azure, zapewniając optymalny routing ruchu usług sieci. Jeśli używasz punktów końcowych usługi przy wymuszonego tunelowania usługa Api Management, powyżej usług Azure, których ruch nie jest wymuszone tunelowanie. Innych API Management który ruch zależności usługi jest wymuszone tunelowanie i nie mogą zostać utracone lub usługa API Management nie będzie działać prawidłowo.
    
  * Wszystkie kontrolki płaszczyzny ruch z Internetu do punktu końcowego zarządzania usługi API Management są przesyłane za pośrednictwem określony zbiór adresów IP dla ruchu przychodzącego, hostowane przez usługę API Management. Ruch jest wymuszonego tunelowania odpowiedzi nie będzie symetrycznie mapować do tych dla ruchu przychodzącego źródłowych adresów IP. Aby wyeliminować to ograniczenie, należy dodać następujące trasy zdefiniowane przez użytkownika ([tras zdefiniowanych przez użytkownika][UDRs]) do kierowania ruchu do platformy Azure, ustawiając miejsce docelowe te trasy hostów do "Internet". Zestaw przychodzące adresy IP dla ruchu płaszczyzna kontroli jest następująca:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * Dla innych zależności usługi API Management, będące wymuszonego tunelowania powinien istnieć sposób, aby rozpoznać nazwę hosta i skontaktowanie się z punktem końcowym. Należą do nich
      - Monitorowanie kondycji i metryki
      - Azure portal Diagnostics
      - Przekazywania protokołu SMTP
      - Portal dla deweloperów CAPTCHA

## <a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Początkowej instalacji**: Po początkowym wdrożeniu usługi API Management w podsieci nie powiedzie się, zaleca się najpierw wdrożyć maszynę wirtualną w tej samej podsieci. Dalej pulpitu zdalnego z maszyną wirtualną i sprawdź, czy jest łączność z jedną z każdego zasobu poniżej w Twojej subskrypcji platformy azure
    * Azure Storage blob
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Po zweryfikowaniu połączenia, upewnij się usunąć wszystkie zasoby, które są wdrożone w tej podsieci, przed wdrożeniem usługi API Management do podsieci.

* **Aktualizacje przyrostowe**: Podczas wprowadzania zmian do sieci, zapoznaj się [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), aby sprawdzić, czy usługa API Management nie utracił dostęp do krytycznych zasobów, których ona zależy. Co 15 minut można zaktualizować stanu łączności.

* **Linki nawigacji zasobu**: Podczas wdrażania do podsieci sieci wirtualnej usługi Resource Manager style, usługa API Management zastrzega sobie podsieci, tworząc Link nawigacji zasobu. Jeśli podsieć zawiera już zasobu z innego dostawcy, wdrożenie będzie **się nie powieść**. Podobnie podczas przenoszenia usługi API Management do innej podsieci lub usuń go, firma Microsoft usunie tego linku nawigacji zasobu.

## <a name="subnet-size"> </a> Wymagany rozmiar podsieci
Platforma Azure rezerwuje pewnych adresów IP w każdej podsieci, a nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane dla zgodności protokołów, oraz trzy dodatkowe adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej platformy Azure każde wystąpienie usługi Api Management w podsieci używa dwóch adresów IP zgodnie z jednostką Premium SKU lub jeden adres IP dla jednostki SKU Developer. Każde wystąpienie rezerwuje dodatkowe adresu IP dla modułu równoważenia obciążenia zewnętrznych. W przypadku wdrażania w wewnętrznej sieci wirtualnej, wymaga dodatkowych adresów IP dla wewnętrznego modułu równoważenia obciążenia.

Biorąc pod uwagę przy obliczaniu powyżej minimalny rozmiar podsieci, w którym można wdrożyć usługi API Management jest rozmiarze/29, co daje trzech adresów IP.

## <a name="routing"> </a> Routing
+ Równoważenia obciążenia publiczny adres IP (VIP) zostanie zarezerwowany zapewniać dostęp do wszystkich punktów końcowych usługi.
+ Adres IP z zakresu adresów IP podsieci (DIP) będzie służyć do dostępu do zasobów w sieci wirtualnej i publicznego adresu IP (VIP) będzie służyć do dostępu do zasobów spoza sieci wirtualnej.
+ Publiczny adres IP ze zrównoważonym obciążeniem adres można znaleźć w bloku Przegląd/Essentials w witrynie Azure portal.

## <a name="limitations"> </a>Ograniczenia
* W podsieci zawierającej wystąpienia usługi API Management nie może zawierać żadnych innych typów zasobów platformy Azure.
* Podsieć i usługa API Management musi być w tej samej subskrypcji.
* W podsieci zawierającej wystąpienia usługi API Management nie można przenosić między subskrypcjami.
* Dla wdrożeń usługi API Management w wielu regionach skonfigurowane w trybie wewnętrzną siecią wirtualną użytkownicy są odpowiedzialni za zarządzanie Równoważenie obciążenia w wielu regionach, jak posiadanych routingu.
* Łączność z zasobu w globalne równorzędne sieci Wirtualnej w innym regionie świadczenia usługi API Management w trybie wewnętrznego nie będzie działać z powodu ograniczenia platform. Aby uzyskać więcej informacji, zobacz [zasobów w jednej sieci wirtualnej nie może komunikować się za pomocą platformy Azure wewnętrznego modułu równoważenia obciążenia w równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Powiązana zawartość
* [Łączenie sieci wirtualnej do wewnętrznej bazy danych przy użyciu bramy sieci Vpn](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Łączenie sieci wirtualnej z różnych modeli wdrażania](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Sposób użycia inspektora interfejsów API do śledzenia wywołań w usłudze Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network Faq](../virtual-network/virtual-networks-faq.md)
* [Tagi usługi](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
