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
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: a74d91ad986b606a36a8040ac849e7fcbec03f16
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093196"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak używać usługi Azure API Management przy użyciu sieci wirtualnych
Sieci wirtualne platformy Azure (Vnet) umożliwiają umieszczenie wszystkich zasobów platformy Azure w sieci lecz-internet, która umożliwia kontrolę dostępu do. Te sieci mogą być następnie połączone do sieci w środowisku lokalnym przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej o usłudze Azure Virtual Networks start z informacjami w tym miejscu: [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Usługa Azure API Management można wdrożyć w sieci wirtualnej (VNET), dzięki czemu będzie miał dostęp do usług zaplecza w obrębie sieci. Portal dla deweloperów i API gateway, można skonfigurować dostępne z Internetu lub tylko w obrębie sieci wirtualnej.

> [!NOTE]
> Usługa Azure API Management obsługuje sieci wirtualnych Menedżera zasobów klasycznych i platformy Azure.
>

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie usługi APIM. Aby uzyskać więcej informacji, zobacz [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).
+ Łączność z siecią Wirtualną jest dostępna w tylko warstw Premium i deweloperów. Przejdź do jednej z tych warstw, postępując zgodnie z instrukcjami w [uaktualnianie i skalowanie](upgrade-and-scale.md#upgrade-and-scale) tematu.

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

    ![Prywatna komunikacja równorzędna][api-management-vnet-private]`

    Teraz zostanie wyświetlona lista wszystkich regionów, w której zainicjowano usługi API Management. Wybierz sieć Wirtualną i podsieć dla każdego regionu. Lista jest wypełniana przy użyciu wdrożeń klasycznych i sieci wirtualnej usługi Resource Manager dostępne w Twojej subskrypcji platformy Azure, które są skonfigurowane w regionie, który jest konfigurowany.

    > [!NOTE]
    > **Punkt końcowy usługi** na powyższym diagramie zawiera bramy/serwera Proxy, witryny Azure portal, portal dla deweloperów, GIT i bezpośredniego punktu końcowego zarządzania.
    > **Punkt końcowy zarządzania** na powyższym diagramie jest punkt końcowy hostowanej w usłudze, aby zarządzać konfiguracją za pośrednictwem witryny Azure portal i programu Powershell.
    > Należy również zauważyć, że, mimo, że na diagramie przedstawiono adresów IP dla swoich różnych punktów końcowych usługi API Management **tylko** reaguje na jego skonfigurowanych nazw hostów.

    > [!IMPORTANT]
    > W przypadku wdrażania wystąpienia usługi Azure API Management z siecią wirtualną usługi Resource Manager, usługa musi być w dedykowanej podsieci, która nie zawiera żadnych innych zasobów, z wyjątkiem wystąpień usługi Azure API Management. Jeśli zostanie podjęta próba, aby wdrożyć wystąpienie usługi Azure API Management do podsieci sieci Wirtualnej usługi Resource Manager zawierający inne zasoby, wdrożenie zakończy się niepowodzeniem.
    >

    ![Wybierz sieci VPN][api-management-setup-vpn-select]

5. Kliknij przycisk **Zapisz** w górnej części ekranu.

> [!NOTE]
> Adres VIP wystąpienia usługi API Management zmieni każdorazowo sieci Wirtualnej jest włączone lub wyłączone.
> Adres VIP zmieni się także wtedy, gdy usługa API Management jest przenoszona z **zewnętrznych** do **wewnętrzne** lub na odwrót
>

> [!IMPORTANT]
> Jeśli usuwanie usługi API Management z sieci Wirtualnej lub zmienić ten, który jest wdrożony w stosowanych wcześniej sieci Wirtualnej mogą pozostać zablokowane przez dwie godziny. W tym okresie nie będzie można usunąć sieci Wirtualnej lub wdrożyć nowy zasób.

## <a name="enable-vnet-powershell"> </a>Włącz połączenia sieci Wirtualnej przy użyciu poleceń cmdlet programu PowerShell
Można również włączyć połączenie między SIECIAMI przy użyciu poleceń cmdlet programu PowerShell

* **Tworzenie usługi API Management w sieci Wirtualnej**: Użyj polecenia cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) do utworzenia usługi Azure API Management w sieci Wirtualnej.

* **Wdrażanie istniejącej usługi API Management w sieci Wirtualnej**: Użyj polecenia cmdlet [AzureRmApiManagementDeployment aktualizacji](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) przenieść istniejącej usługi Azure API Management w sieci wirtualnej.

## <a name="connect-vnet"> </a>Łączenie z usługą sieci web hostowanych w ramach sieci wirtualnej
Usługa API Management jest połączony z siecią wirtualną, uzyskiwanie dostępu do usług zaplecza w nim po nie różni się od uzyskiwania dostępu do usług publicznych. Po prostu wpisz lokalny adres IP lub nazwa hosta (Jeśli serwer DNS jest skonfigurowany do sieci Wirtualnej) usługę sieci web do **adres URL usługi sieci Web** pola podczas tworzenia nowego interfejsu API lub edycji istniejącego.

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej przedstawiono listę typowych problemów z błędną konfiguracją, które mogą wystąpić podczas wdrażania usługi API Management w sieci wirtualnej.

* **Niestandardowe ustawienia serwera DNS**: Usługa API Management jest zależna od kilka usług platformy Azure. Kiedy usługa API Management znajduje się w sieci Wirtualnej za pomocą niestandardowego serwera DNS, należy go rozpoznać nazwy hostów tych usług platformy Azure. Postępuj zgodnie z [to](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wskazówki dotyczące konfiguracją niestandardowego serwera DNS. Zobacz w poniższej tabeli portów i inne wymagania dotyczące sieci dla odwołania.

> [!IMPORTANT]
> Jeśli planujesz użyć niestandardowych serwerów DNS dla sieci Wirtualnej, należy skonfigurować ją **przed** wdrażanie usługi API Management do niego. W przeciwnym razie należy zaktualizować usługę API Management po każdej zmianie serwery DNS, uruchamiając [zastosować operacja konfiguracji sieci](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty wymagane dla usługi API Management**: ruchu przychodzącego i wychodzącego do podsieci, w której jest wdrażany usługi API Management może być kontrolowana za pomocą [sieciowej grupy zabezpieczeń][Network Security Group]. Jeśli którekolwiek z tych portów są niedostępne, usługa API Management może nie działać prawidłowo i może stać się niedostępny. Co najmniej jeden z tych portów, zablokowane jest innym Typowym problemem błędnej konfiguracji w przypadku korzystania z usługi API Management z sieci Wirtualnej.

Gdy wystąpienie usługi API Management znajduje się w sieci Wirtualnej, są używane porty w poniższej tabeli.

| Źródło / porty docelowe | Kierunek | Protokół transportowy | Źródłowy / docelowy | Cel (*) | Typ sieci wirtualnej |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Przychodzący |TCP |INTERNET / VIRTUAL_NETWORK|Komunikacja klienta z usługi API Management|Zewnętrzne |
| * / 3443 |Przychodzący |TCP |INTERNET / VIRTUAL_NETWORK|Punkt końcowy zarządzania dla witryny Azure portal i programu Powershell |Zewnętrzne i wewnętrzne |
| * / 80, 443 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|**Zależność od usługi Azure Storage**, usługi Azure Service Bus i Azure Active Directory (jeśli dotyczy).|Zewnętrzne i wewnętrzne |
| * / 1433 |Wychodzący |TCP |VIRTUAL_NETWORK / SQL|**Dostęp do punktów końcowych usługi Azure SQL** |Zewnętrzne i wewnętrzne |
| * / 5672 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Zależność dla dziennika do zasad Centrum zdarzeń i agenta monitorowania |Zewnętrzne i wewnętrzne |
| * / 445 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Zależność od udziału plików platformy Azure dla usługi GIT |Zewnętrzne i wewnętrzne |
| * / 1886 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Niezbędnych do publikowania stan kondycji Resource Health |Zewnętrzne i wewnętrzne |
| * / 25028 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Łączenie do przekazywania SMTP do wysyłania wiadomości E-mail |Zewnętrzne i wewnętrzne |
| * / 6381 - 6383 |Dla ruchu przychodzącego i wychodzącego |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Dostęp do wystąpień usługi Redis Cache między RoleInstances |Zewnętrzne i wewnętrzne |
| * / * | Przychodzący |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Moduł równoważenia obciążenia infrastruktury platformy Azure |Zewnętrzne i wewnętrzne |

>[!IMPORTANT]
> Porty, dla którego *przeznaczenia* jest **bold** są wymagane dla usługi API Management została wdrożona pomyślnie. Blokowanie innych portów jednak spowoduje obniżenie wydajności w możliwość korzystania i monitorowania uruchomioną usługę.

* **Funkcje protokołu SSL**: Aby umożliwić tworzenie łańcucha certyfikatów SSL i sprawdzanie poprawności usługi API Management usługa wymaga połączenia sieciowego ruchu wychodzącego ocsp.msocsp.com oraz mscrl.microsoft.com crl.microsoft.com. Ta zależność nie jest wymagane, jeśli dowolny certyfikat, którego przekazujesz usłudze API Management zawiera pełny łańcuch do głównego urzędu certyfikacji.

* **Dostęp DNS**: dostępu wychodzącego na port 53 jest wymagany do komunikacji przy użyciu serwerów DNS. Jeśli niestandardowego serwera DNS znajduje się na drugiej stronie bramy sieci VPN, serwer DNS musi być osiągalna z podsieci obsługującej usługi API Management.

* **Monitorowanie kondycji i metryki**: połączenia sieciowego ruchu wychodzącego do usługi Azure punktów końcowych monitorowania, które rozwiązania w ramach następujących domen: 

    | Środowisko platformy Azure | Punkty końcowe |
    | --- | --- |
    | Azure Public | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li><li>prod3 black.prod3.metrics.nsatc.net</li><li>prod3 red.prod3.metrics.nsatc.net</li></ul> |
    | Azure Government | <ul><li>fairfax.warmpath.usgovcloudapi.NET</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li></ul> |
    | Azure (Chiny) | <ul><li>mooncake.warmpath.chinacloudapi.CN</li><li>shoebox2.Metrics.nsatc.NET</li><li>prod3.Metrics.nsatc.NET</li></ul> |

* **Trasy Instalacja ekspresowa**: Typowa konfiguracja klienta jest określenie własnych trasy domyślnej (0.0.0.0/0), co zmusza wychodzącego ruchu internetowego, aby zamiast tego przepływu w środowisku lokalnym. Ten przepływ ruchu niezmiennie przerywa łączność z usługą Azure API Management, ponieważ ruch wychodzący jest zablokowane w środowisku lokalnym lub translatora adresów Sieciowych będzie nierozpoznawalną zbiór adresów, które nie będą działać z różnymi punkty końcowe platformy Azure. Rozwiązanie polega na zdefiniowaniu jedną (lub więcej) trasy zdefiniowane przez użytkownika ([tras zdefiniowanych przez użytkownika][UDRs]) w tej podsieci, który zawiera usługi Azure API Management. Trasa zdefiniowana przez użytkownika definiuje trasy specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.
  Jeśli to możliwe zaleca się użyć następującej konfiguracji:
 * Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie życie tuneli całego ruchu wychodzącego środowiska lokalnego.
 * Trasa zdefiniowana przez użytkownika zastosowane do podsieci zawierającej usługi Azure API Management definiuje 0.0.0.0/0 z typem następnego przeskoku Internet.
 Połączony wpływ tych kroków jest, czy poziomu podsieci trasy zdefiniowanej przez użytkownika ma pierwszeństwo przed ExpressRoute wymuszonego tunelowania, co pozwala na zapewnienie ruch wychodzący do Internetu z usługi Azure API Management.

* **Routing za pośrednictwem wirtualnych urządzeń sieciowych**: zablokuje konfiguracje używających trasy zdefiniowanej przez użytkownika trasa domyślna (0.0.0.0/0) do kierowania ruchu internetowego skierowany do podsieci usługi API Management za pośrednictwem wirtualnego urządzenia sieciowego działających na platformie Azure ruch zarządzania, pochodzące z Internetu do wystąpienia usługi API Management, które są wdrażane w podsieci sieci wirtualnej. Ta konfiguracja nie jest obsługiwana.

>[!WARNING]
>Usługa Azure API Management nie jest obsługiwana w przypadku konfiguracji usługi ExpressRoute, **niepoprawnie cross anonsować tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej**. Konfiguracje usługi ExpressRoute, które mają skonfigurowaną, publiczną komunikacją równorzędną otrzymają anonsy tras od firmy Microsoft na duży zestaw zakresów adresów IP platformy Microsoft Azure. Jeśli te zakresy adresów są niepoprawnie anonsowania krzyżowego ścieżką prywatnej sieci równorzędnej, efekt jest, czy wszystkie pakiety sieciowe wychodzące z podsieci z wystąpieniem usługi Azure API Management są niepoprawnie sposób wymuszony tunelowany do sieci lokalnej klienta infrastruktura. Ten przepływ sieciowy przerywa usługi Azure API Management. Rozwiązanie tego problemu jest zatrzymanie anonsowania krzyżowego tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.


## <a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Początkowej instalacji**: po początkowym wdrożeniu usługi API Management w podsieci nie powiedzie się, zaleca się najpierw wdrożyć maszynę wirtualną w tej samej podsieci. Dalej pulpitu zdalnego z maszyną wirtualną i sprawdź, czy jest łączność z jedną z każdego zasobu poniżej w Twojej subskrypcji platformy azure
    * Usługi Azure blob Storage
    * Azure SQL Database
    * Tabela magazynu Azure

 > [!IMPORTANT]
 > Po zweryfikowaniu połączenia, upewnij się usunąć wszystkie zasoby, które są wdrożone w tej podsieci, przed wdrożeniem usługi API Management do podsieci.

* **Aktualizacje przyrostowe**: podczas wprowadzania zmian do sieci, zapoznaj się [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), aby sprawdzić, czy usługa API Management nie utracił dostęp do krytycznych zasobów, których ona zależy. Co 15 minut można zaktualizować stanu łączności.

* **Linki nawigacji zasobu**: podczas wdrażania do podsieci sieci wirtualnej usługi Resource Manager style, usługa API Management rezerwuje podsieci, tworząc Link nawigacji zasobu. Jeśli podsieć zawiera już zasobu z innego dostawcy, wdrożenie będzie **się nie powieść**. Podobnie podczas przenoszenia usługi API Management do innej podsieci lub usuń go, firma Microsoft usunie tego linku nawigacji zasobu.

* **Testowanie interfejsu API w witrynie Azure portal**: podczas testowania interfejsu API z poziomu witryny Azure portal i wystąpienie usługi API Management jest zintegrowany z wewnętrznej sieci wirtualnej, serwery DNS skonfigurowany w sieci wirtualnej będzie służyć do rozpoznawania nazw. Jeśli otrzymasz odpowiedź 404 podczas testowania w witrynie Azure portal, upewnij się, że serwery DNS dla sieci wirtualnej może poprawnie rozpoznać nazwę hosta wystąpienia usługi API Management. 

## <a name="subnet-size"> </a> Wymagany rozmiar podsieci
Platforma Azure rezerwuje pewnych adresów IP w każdej podsieci, a nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane dla zgodności protokołów, oraz trzy dodatkowe adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej platformy Azure każde wystąpienie usługi Api Management w podsieci używa dwóch adresów IP zgodnie z jednostką Premium SKU lub jeden adres IP dla jednostki SKU Developer. Każde wystąpienie rezerwuje dodatkowe adresu IP dla modułu równoważenia obciążenia zewnętrznych. W przypadku wdrażania w wewnętrznej sieci wirtualnej, wymaga dodatkowych adresów IP dla wewnętrznego modułu równoważenia obciążenia.

Biorąc pod uwagę przy obliczaniu powyżej minimalny rozmiar podsieci, w którym można wdrożyć usługi API Management jest rozmiarze/29, co daje 3 adresów IP.

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
* [Sieć wirtualna — często zadawane pytania](../virtual-network/virtual-networks-faq.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
