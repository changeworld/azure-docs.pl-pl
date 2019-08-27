---
title: Jak używać usługi Azure API Management z sieciami wirtualnymi
description: Dowiedz się, jak skonfigurować połączenie z siecią wirtualną w usłudze Azure API Management i uzyskać dostęp do usług sieci Web.
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
ms.openlocfilehash: 18bbeb2b4936b1c8a03ffdc7eade8dc92d094fde
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050612"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak używać usługi Azure API Management z sieciami wirtualnymi
Usługi Azure Virtual Networks (sieci wirtualnych) umożliwiają umieszczanie dowolnego z zasobów platformy Azure w sieci niezwiązanej z obsługą Internetu, do której można kontrolować dostęp. Te sieci mogą następnie być połączone z sieciami lokalnymi przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej na temat sieci wirtualnych platformy Azure, Zacznij od informacji poniżej: [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Usługę Azure API Management można wdrożyć w sieci wirtualnej (VNET), dzięki czemu można uzyskać dostęp do usług zaplecza w sieci. Portal dla deweloperów i Brama interfejsu API można skonfigurować tak, aby były dostępne zarówno z Internetu, jak i tylko w obrębie sieci wirtualnej.

> [!NOTE]
> Usługa Azure API Management obsługuje zarówno klasyczny, jak i Azure Resource Manager sieci wirtualnych.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Włącz połączenie sieci wirtualnej

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Włączanie łączności sieci wirtualnej przy użyciu Azure Portal

1. Przejdź do wystąpienia APIM w [Azure Portal](https://portal.azure.com/).
2. Wybierz **Virtual Network**.
3. Skonfiguruj wystąpienie API Management, które ma zostać wdrożone w sieci wirtualnej.

    ![Menu sieci wirtualnej API Management][api-management-using-vnet-menu]
4. Wybierz żądany typ dostępu:

   * **Wyłączone**: jest to ustawienie domyślne. API Management nie jest wdrożona w sieci wirtualnej.

   * **Zewnętrzny**: Brama API Management i Portal dla deweloperów są dostępni z publicznej sieci Internet za pośrednictwem zewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

     ![Publiczna komunikacja równorzędna][api-management-vnet-public]

   * **Wewnętrzne**: Brama API Management i Portal dla deweloperów są dostępni tylko z poziomu sieci wirtualnej za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

     ![Prywatna komunikacja równorzędna][api-management-vnet-private]

     Zostanie wyświetlona lista wszystkich regionów, w których zainicjowano usługę API Management. W każdym regionie wybierz sieć wirtualną i podsiecię. Lista jest wypełniana przy użyciu klasycznych i Menedżer zasobówych sieci wirtualnych dostępnych w ramach subskrypcji platformy Azure, które są skonfigurowane w konfigurowanym regionie.

     > [!IMPORTANT]
     > W przypadku wdrażania wystąpienia usługi Azure API Management w sieci wirtualnej Menedżer zasobów usługa musi znajdować się w dedykowanej podsieci, która nie zawiera żadnych innych zasobów z wyjątkiem wystąpień API Management platformy Azure. Jeśli podjęto próbę wdrożenia wystąpienia usługi Azure API Management w sieci wirtualnej Menedżer zasobów zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem.
     >

     ![Wybieranie sieci VPN][api-management-setup-vpn-select]

5. Kliknij przycisk **Zapisz** na górnym pasku nawigacyjnym.
6. Kliknij przycisk **Zastosuj konfigurację sieci** na górnym pasku nawigacyjnym.

> [!NOTE]
> Adres VIP wystąpienia API Management zmieni się za każdym razem, gdy sieć wirtualna jest włączona lub wyłączona.
> Adres VIP zostanie również zmieniony, gdy API Management zostanie przeniesiony z **zewnątrz** do **wewnętrznego** lub odwrotnie
>

> [!IMPORTANT]
> Jeśli usuniesz API Management z sieci wirtualnej lub zmienisz ją wdrożoną w programie, wcześniej użyta Sieć wirtualna może pozostać zablokowana przez maksymalnie dwie godziny. W tym czasie nie będzie można usunąć sieci wirtualnej ani wdrożyć w niej nowego zasobu.

## <a name="enable-vnet-powershell"> </a>Włączanie połączenia sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell
Połączenie sieci wirtualnej można również włączyć za pomocą poleceń cmdlet programu PowerShell

* **Utwórz usługę API Management wewnątrz sieci wirtualnej**: Użyj polecenia cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) , aby utworzyć usługę Azure API Management w sieci wirtualnej.

* **Wdróż istniejącą usługę API Management w sieci wirtualnej**: Użyj polecenia cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) , aby przenieść istniejącą usługę Azure API Management w ramach Virtual Network.

## <a name="connect-vnet"> </a>Nawiązywanie połączenia z usługą sieci Web hostowaną w sieci wirtualnej
Gdy usługa API Management jest połączona z siecią wirtualną, dostęp do usług zaplecza nie różni się od uzyskiwania dostępu do usług publicznych. Po prostu wpisz adres IP lub nazwę hosta (Jeśli serwer DNS jest skonfigurowany dla sieci wirtualnej) w polu **adres URL usługi** sieci Web podczas tworzenia nowego interfejsu API lub edytowania istniejącego.

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej znajduje się lista typowych problemów z błędami konfiguracji, które mogą wystąpić podczas wdrażania usługi API Management w Virtual Network.

* **Konfiguracja niestandardowego serwera DNS**: Usługa API Management zależy od kilku usług platformy Azure. Gdy API Management jest hostowana w sieci wirtualnej z niestandardowym serwerem DNS, musi rozpoznać nazwy hostów tych usług platformy Azure. Postępuj zgodnie z [tymi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wskazówkami dotyczącymi NIESTANDARDOWEJ konfiguracji DNS. Zapoznaj się z poniższą tabelą porty i innymi wymaganiami dotyczącymi sieci.

> [!IMPORTANT]
> Jeśli planujesz używać niestandardowych serwerów DNS dla sieci wirtualnej, skonfiguruj ją **przed** wdrożeniem usługi API Management. W przeciwnym razie należy zaktualizować usługę API Management przy każdej zmianie serwerów DNS przez uruchomienie [operacji Zastosuj konfigurację sieci](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty wymagane dla API Management**: Ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management można kontrolować przy użyciu [sieciowej grupy zabezpieczeń][Network Security Group]. Jeśli którykolwiek z tych portów jest niedostępny, API Management może nie działać prawidłowo i może stać się niedostępna. Jeśli co najmniej jeden z tych portów jest zablokowany, jest to inny typowy problem z konfiguracją podczas korzystania z API Management z siecią wirtualną.

<a name="required-ports"></a> Gdy wystąpienie usługi API Management jest hostowane w sieci wirtualnej, używane są porty w poniższej tabeli.

| Porty źródłowe/docelowe | Direction          | Protokół transportowy |   [Tagi usług](../virtual-network/security-overview.md#service-tags) <br> Źródło/miejsce docelowe   | Cel (*)                                                 | Typ Virtual Network |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Przychodzący            | TCP                | INTERNET/VIRTUAL_NETWORK            | Komunikacja z klientem do API Management                      | Zewnętrzne             |
| * / 3443                     | Przychodzący            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Punkt końcowy zarządzania dla Azure Portal i programu PowerShell         | Wewnętrzna & zewnętrzna  |
| * / 80, 443                  | Wychodzące           | TCP                | VIRTUAL_NETWORK/magazyn             | **Zależność od usługi Azure Storage**                             | Wewnętrzna & zewnętrzna  |
| * / 80, 443                  | Wychodzące           | TCP                | VIRTUAL_NETWORK/usługi azureactivedirectory | Azure Active Directory (jeśli dotyczy)                   | Wewnętrzna & zewnętrzna  |
| * / 1433                     | Wychodzące           | TCP                | VIRTUAL_NETWORK/SQL                 | **Dostęp do punktów końcowych usługi Azure SQL**                           | Wewnętrzna & zewnętrzna  |
| * / 5672                     | Wychodzące           | TCP                | VIRTUAL_NETWORK/EventHub            | Zależność dla dziennika do zasad usługi Event Hub i agenta monitorowania | Wewnętrzna & zewnętrzna  |
| * / 445                      | Wychodzące           | TCP                | VIRTUAL_NETWORK/magazyn             | Zależność od udziału plików platformy Azure dla usługi GIT                      | Wewnętrzna & zewnętrzna  |
| * / 1886                     | Wychodzące           | TCP                | VIRTUAL_NETWORK/INTERNET            | Potrzeba opublikowania stanu kondycji w celu Resource Health          | Wewnętrzna & zewnętrzna  |
| * / 443                     | Wychodzące           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikowanie dzienników diagnostycznych i metryk                        | Wewnętrzna & zewnętrzna  |
| * / 25                       | Wychodzące           | TCP                | VIRTUAL_NETWORK/INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Wewnętrzna & zewnętrzna  |
| */587                      | Wychodzące           | TCP                | VIRTUAL_NETWORK/INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Wewnętrzna & zewnętrzna  |
| * / 25028                    | Wychodzące           | TCP                | VIRTUAL_NETWORK/INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Wewnętrzna & zewnętrzna  |
| * / 6381 - 6383              | Przychodzące & wychodzące | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Dostęp do pamięci podręcznej platformy Azure dla wystąpień Redis między RoleInstances          | Wewnętrzna & zewnętrzna  |
| * / *                        | Przychodzący            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Load Balancer infrastruktury platformy Azure                          | Wewnętrzna & zewnętrzna  |

>[!IMPORTANT]
> Porty, dla których *przeznaczenie* jest pogrubienie, są wymagane do pomyślnego wdrożenia usługi API Management. Zablokowanie innych portów spowoduje jednak obniżenie wydajności i monitorowanie uruchomionej usługi.

+ **Funkcja SSL**: Aby włączyć tworzenie i sprawdzanie poprawności łańcucha certyfikatu SSL, usługa API Management wymaga łączności sieciowej wychodzącej z ocsp.msocsp.com, mscrl.microsoft.com i crl.microsoft.com. Ta zależność nie jest wymagana, jeśli dowolny certyfikat przekazywany do API Management zawiera pełny łańcuch do katalogu głównego urzędu certyfikacji.

+ **Dostęp do usługi DNS**: Dostęp wychodzący na porcie 53 jest wymagany do komunikacji z serwerami DNS. Jeśli na drugim końcu bramy sieci VPN istnieje niestandardowy serwer DNS, serwer DNS musi być osiągalny z API Management hostowania podsieci.

+ **Metryki i monitorowanie kondycji**: Wychodzące połączenia sieciowe z punktami końcowymi monitorowania platformy Azure, które są rozwiązywane w następujących domenach:

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`Warm.ingestion.msftcloudes.com gdzie `East US 2` to eastus2.Warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure — Chiny       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Przekaźnik SMTP**: Wychodząca łączność sieciowa dla przekaźnika SMTP, która jest rozpoznawana `smtpi-ch1.msn.com`pod kątem `smtpi-sin.msn.com` hosta `smtpi-co1.msn.com`,, `smtpi-db3.msn.com`i`ies.global.microsoft.com`

+ **CAPTCHA portalu deweloperów**: Wychodząca łączność sieciowa dla CAPTCHA portalu deweloperów, która jest rozpoznawana w `client.hip.live.com` obszarze `partner.hip.live.com`hosty i.

+ **Diagnostyka Azure Portal**: Aby umożliwić przepływ dzienników diagnostycznych z Azure Portal przy użyciu rozszerzenia API Management z wewnątrz Virtual Network, wymagany jest dostęp wychodzący `dc.services.visualstudio.com` do portu 443. Pomaga to w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z rozszerzenia.

+ **Wymuś tunelowanie ruchu w zaporze Premium za pomocą usługi Express Route lub sieciowego urządzenia wirtualnego**: Typową konfiguracją klienta jest definiowanie własnej trasy domyślnej (0.0.0.0/0), która wymusza cały ruch z API Management delegowanej podsieci do przepływu przez zaporę lokalną lub do sieciowego urządzenia wirtualnego. Ten przepływ ruchu niezmiennie zrywa łączność z platformą Azure API Management, ponieważ ruch wychodzący jest blokowany lokalnie lub NAT na nierozpoznawalny zbiór adresów, które nie działają już z różnymi punktami końcowymi platformy Azure. Rozwiązanie wymaga wykonania kilku czynności:

  * Włącz punkty końcowe usługi w podsieci, w której wdrożono usługę API Management. [Punkty końcowe usługi][ServiceEndpoints] muszą być włączone dla usług Azure SQL, Azure Storage, Azure EventHub i Azure ServiceBus. Włączenie punktów końcowych bezpośrednio z API Management delegowanej podsieci do tych usług pozwala im korzystać z sieci szkieletowej Microsoft Azure zapewniającej optymalny Routing ruchu usług. W przypadku korzystania z punktów końcowych usługi przy użyciu wymuszonego tunelowego zarządzania interfejsem API powyższy ruch usług platformy Azure nie jest wymuszany. Inne API Management ruchu zależności usługi są wymuszane tunelowanie i nie można go utracić lub usługa API Management nie działa prawidłowo.
    
  * Cały ruch płaszczyzny kontroli z Internetu do punktu końcowego zarządzania usługi API Management jest kierowany przez określony zestaw przychodzących adresów IP hostowanych przez API Management. Gdy ruch wymuszony jest tunelowany, odpowiedzi nie będą symetryczne mapowanie z powrotem na te adresy IP źródła przychodzącego. Aby przezwyciężyć ograniczenie, musimy dodać następujące trasy zdefiniowane przez użytkownika ([UDR][UDRs]) w celu kierowania ruchu z powrotem do platformy Azure przez ustawienie miejsca docelowego tych tras hosta na wartość "Internet". Zestaw przychodzących adresów IP dla ruchu płaszczyzny kontroli jest następujący:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * W przypadku innych zależności usługi API Management, które są wymuszane tunelowanie, powinien istnieć sposób, aby rozpoznać nazwę hosta i skontaktować się z punktem końcowym. Obejmują one
      - Metryki i monitorowanie kondycji
      - Diagnostyka Azure Portal
      - Przekaźnik SMTP
      - Portal dla deweloperów CAPTCHA

## <a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Początkowa konfiguracja**: Gdy początkowe wdrożenie usługi API Management w podsieci nie powiedzie się, zaleca się najpierw wdrożyć maszynę wirtualną w tej samej podsieci. Następne pulpit zdalny do maszyny wirtualnej i sprawdza, czy istnieje połączenie z jednym z poniższych zasobów w ramach subskrypcji platformy Azure
    * Obiekt BLOB usługi Azure Storage
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Po zweryfikowaniu łączności upewnij się, że usunięto wszystkie zasoby wdrożone w podsieci przed wdrożeniem API Management w podsieci.

* **Aktualizacje przyrostowe**: Podczas wprowadzania zmian w sieci należy zapoznać się z [interfejsem API NetworkStatus](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus), aby sprawdzić, czy usługa API Management nie utraciła dostępu do żadnych krytycznych zasobów, od których zależy. Stan łączności powinien być aktualizowany co 15 minut.

* **Linki nawigacji zasobów**: W przypadku wdrażania do Menedżer zasobów stylu sieci wirtualnej API Management rezerwuje podsieć przez utworzenie linku nawigacji zasobów. Jeśli podsieć zawiera już zasób od innego dostawcy, wdrożenie zakończy **się niepowodzeniem**. Podobnie po przeniesieniu usługi API Management do innej podsieci lub usunięciu jej zostanie usunięte łącze nawigacyjne tego zasobu.

## <a name="subnet-size"></a> Wymagania dotyczące rozmiaru podsieci
Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure, każde wystąpienie usługi API Management w podsieci używa dwóch adresów IP na jednostkę SKU Premium lub jeden adres IP dla jednostki SKU dewelopera. Każde wystąpienie rezerwuje dodatkowy adres IP dla zewnętrznego modułu równoważenia obciążenia. Podczas wdrażania w wewnętrznej sieci wirtualnej wymaga dodatkowego adresu IP dla wewnętrznego modułu równoważenia obciążenia.

W przypadku obliczenia przekraczającego minimalny rozmiar podsieci, w której można wdrożyć API Management to/29, które daje trzy adresy IP.

## <a name="routing"></a> Routing
+ Publiczny adres IP ze zrównoważonym obciążeniem (VIP) zostanie zarezerwowany w celu zapewnienia dostępu do wszystkich punktów końcowych usługi.
+ Adres IP z zakresu adresów IP podsieci (DIP) będzie używany do uzyskiwania dostępu do zasobów w sieci wirtualnej, a publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów poza siecią wirtualną.
+ Publiczny adres IP ze zrównoważonym obciążeniem można znaleźć w bloku przegląd/podstawy w Azure Portal.

## <a name="limitations"> </a>Ograniczenia
* Podsieć zawierająca wystąpienia API Management nie może zawierać żadnych innych typów zasobów platformy Azure.
* Podsieć i usługa API Management muszą znajdować się w tej samej subskrypcji.
* Podsieć zawierająca wystąpienia API Management nie może zostać przeniesiona między subskrypcjami.
* W przypadku wdrożeń wieloregionowych API Management skonfigurowanych w trybie wewnętrznej sieci wirtualnej użytkownicy są odpowiedzialni za Zarządzanie równoważeniem obciążenia w wielu regionach, które są właścicielami routingu.
* Połączenie z zasobu w sieci wirtualnej sieci równorzędnej w innym regionie w celu API Management usługi w trybie wewnętrznym nie będzie działało z powodu ograniczenia platformy. Aby uzyskać więcej informacji, zobacz [zasoby w jednej sieci wirtualnej nie mogą komunikować się z wewnętrznym modułem równoważenia obciążenia platformy Azure w równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Powiązana zawartość
* [Łączenie Virtual Network z zapleczem przy użyciu usługi VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Łączenie Virtual Network z różnych modeli wdrażania](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak śledzić wywołania na platformie API Management Azure za pomocą Inspektora interfejsu API](api-management-howto-api-inspector.md)
* [Virtual Network często zadawane pytania](../virtual-network/virtual-networks-faq.md)
* [Tagi usług](../virtual-network/security-overview.md#service-tags)

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
