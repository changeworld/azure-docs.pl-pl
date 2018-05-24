---
title: Jak używać usługi Azure API Management z sieciami wirtualnymi
description: Dowiedz się, jak nawiązanie połączenia z siecią wirtualną w Azure API Management i dostęp do usług sieci web za jego pośrednictwem.
services: api-management
documentationcenter: ''
author: antonba
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 223fa9bc4a19264cc1dcba9830726b30b0f7446c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak używać usługi Azure API Management z sieciami wirtualnymi
Sieci wirtualnych platformy Azure (sieci wirtualne) umożliwiają umieszczać zasobów platformy Azure w kontroli dostępu do sieci routeable z systemem innym niż internet. Te sieci następnie mogą być połączone z sieciami lokalnymi przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej o sieciach wirtualnych platformy Azure Uruchom z informacjami w tym miejscu: [omówienie sieci wirtualnych Azure](../virtual-network/virtual-networks-overview.md).

Zarządzanie interfejsami API Azure można wdrożyć w sieci wirtualnej (VNET), więc można uzyskać dostęp do usług zaplecza w sieci. Portalu dla deweloperów i bramy interfejsu API, można skonfigurować jako dostępny z Internetu lub tylko w ramach sieci wirtualnej.

> [!NOTE]
> Zarządzanie interfejsami API Azure obsługuje sieci wirtualnych Menedżera zasobów klasycznych jak Azure.
>

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musi mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Łączność sieci Wirtualnej jest dostępna w tylko warstwy Premium i deweloperów. Przełącz się do jednego z tych warstw przez zgodnie z instrukcjami w [uaktualnienia i skalować](upgrade-and-scale.md#upgrade-and-scale) tematu.

## <a name="enable-vpn"> </a>Włącz połączenie sieci Wirtualnej

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Łączność sieci Wirtualnej przy użyciu portalu Azure

1. Przejdź do Twojego wystąpienia APIM w [portalu Azure](https://portal.azure.com/).
2. Wybierz **sieci wirtualnej**.
3. Skonfiguruj wystąpienie interfejsu API zarządzania do wdrożenia w ramach sieci wirtualnej.

    ![Zarządzanie interfejsami API menu sieci wirtualnej][api-management-using-vnet-menu]
4. Wybierz typ żądany dostęp:
    
    * **Zewnętrzne**: portal bramy i deweloperów zarządzanie interfejsami API są dostępne z publicznego Internetu za pomocą zewnętrznej usługi równoważenia obciążenia. Bramy mogą uzyskiwać dostęp do zasobów w sieci wirtualnej.
    
    ![Publiczna komunikacja równorzędna][api-management-vnet-public]
    
    * **Wewnętrzny**: portalu bramy i deweloperów interfejsu API zarządzania jest dostępny tylko w obrębie sieci wirtualnej za pomocą wewnętrznego modułu równoważenia obciążenia. Bramy mogą uzyskiwać dostęp do zasobów w sieci wirtualnej.
    
    ![Prywatna komunikacja równorzędna][api-management-vnet-private]`

    Teraz zostanie wyświetlona lista wszystkich regionów, których obsługa została zainicjowana usługi Zarządzanie interfejsami API. Wybierz sieć Wirtualną i podsieć dla każdego regionu. Lista zostanie wypełniona z klasycznym i dostępne w Twojej subskrypcji platformy Azure, które są skonfigurowane w regionie, który jest konfigurowany sieci wirtualnych Menedżera zasobów.
    
    > [!NOTE]
    > **Punkt końcowy usługi** na powyższym diagramie obejmuje bramy/serwera Proxy, portalu Azure, portalu dla deweloperów, usługi GIT i bezpośredniego zarządzania punktu końcowego.
    > **Punkt końcowy zarządzania** na powyższym diagramie jest punktem końcowym hostowanych w usłudze, aby zarządzać konfiguracją za pośrednictwem portalu Azure i programu Powershell.
    > Należy również zauważyć, że mimo, że na diagramie przedstawiono adresów IP dla swoich różnych punktów końcowych usługi Zarządzanie interfejsami API **tylko** reaguje na jego skonfigurowanych nazwy hostów.
    
    > [!IMPORTANT]
    > Podczas wdrażania wystąpienia zarządzanie interfejsami API Azure Resource Manager sieci Wirtualnej, usługa musi być w dedykowanym podsieć, która nie zawiera żadnych innych zasobów, z wyjątkiem wystąpienia usługi Azure API Management. Jeśli próby wdrożenie wystąpienia usługi Azure API Management do podsieci sieci Wirtualnej Resource Manager zawierający inne zasoby, wdrożenie zakończy się niepowodzeniem.
    >

    ![Wybierz sieci VPN][api-management-setup-vpn-select]

5. Kliknij przycisk **zapisać** w górnej części ekranu.

> [!NOTE]
> Adres VIP wystąpienia interfejsu API zarządzania zmieni zawsze sieci Wirtualnej jest włączone lub wyłączone.  
> Po przeniesieniu z interfejsu API zarządzania spowoduje również zmianę adresu VIP **zewnętrznych** do **wewnętrzne** lub na odwrót
>

> [!IMPORTANT]
> Usuń API Management z sieci Wirtualnej lub zmienić ten, który został wdrożony w uprzednio używanych sieci Wirtualnej mogą pozostać zablokowane przez maksymalnie 2 godziny. W tym okresie nie będzie można usunąć sieci Wirtualnej lub wdrożyć nowy zasób.

## <a name="enable-vnet-powershell"> </a>Włącz połączenie sieci Wirtualnej przy użyciu poleceń cmdlet programu PowerShell
Można również włączyć łączność sieci Wirtualnej przy użyciu poleceń cmdlet programu PowerShell

* **Tworzenie usługi Zarządzanie interfejsami API w sieci Wirtualnej**: Użyj polecenia cmdlet [AzureRmApiManagement nowy](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) można utworzyć usługi Azure API Management w sieci Wirtualnej.

* **Wdrażanie istniejącej usługi Zarządzanie interfejsami API w sieci Wirtualnej**: Użyj polecenia cmdlet [AzureRmApiManagementDeployment aktualizacji](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) Aby przenieść istniejącą usługę Azure API Management w sieci wirtualnej.

## <a name="connect-vnet"> </a>Łączenie z usługą sieci web hostowanych w sieci wirtualnej
Po usługi API Management jest podłączony do sieci Wirtualnej, dostęp do usług zaplecza w nim nie różni się od uzyskiwanie dostępu do usług publicznego. Po prostu wpisz lokalny adres IP lub nazwę hosta (Jeśli serwer DNS jest skonfigurowany dla sieci Wirtualnej) usługi sieci web do **adres URL usługi sieci Web** pole podczas tworzenia nowego interfejsu API lub edytowanie istniejących.

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej znajduje się lista typowych problemów z błędem konfiguracji, które mogą wystąpić podczas wdrażania usługi Zarządzanie interfejsami API w sieci wirtualnej.

* **Niestandardowe ustawienia serwera DNS**: Usługa interfejsu API zarządzania zależy od wielu usług Azure. Zarządzanie interfejsami API znajduje się w sieci Wirtualnej przy użyciu niestandardowego serwera DNS, musi rozpoznać nazwy hostów tych usług Azure. Wykonaj [to](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wytyczne dotyczące niestandardowych ustawień DNS. Znajdują się w poniższej tabeli portów i inne wymagania dotyczące sieci dla odwołania.

> [!IMPORTANT]
> Jeśli planujesz używać serwerów DNS niestandardowe sieci wirtualnej, należy skonfigurować ją **przed** wdrażania usługi API Management do niego. W przeciwnym razie należy zaktualizować usługi Zarządzanie interfejsami API każdej zmianie serwerów DNS, uruchamiając [zastosować operacji konfiguracji sieciowej](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty wymagane przez interfejs API zarządzania**: ruchu przychodzącego i wychodzącego do podsieci, w której jest wdrażane zarządzanie interfejsami API można kontrolować przy użyciu [sieciowej grupy zabezpieczeń][Network Security Group]. Jeśli którekolwiek z tych portów są niedostępne, interfejsu API zarządzania może nie działać prawidłowo i może stać się niedostępne. Co najmniej jeden z tych portów zablokowane jest posiadanie innego typowe problemy z błędem konfiguracji podczas korzystania z usługi API Management z sieci Wirtualnej.

Gdy wystąpienie usługi API Management znajduje się w sieci Wirtualnej, są używane porty w poniższej tabeli.

| Źródłowego / docelowego porty | Kierunek | Protokół transportu | Źródłowego / docelowego | Cel (*) | Typ sieci wirtualnej |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Przychodzący |TCP |INTERNET / VIRTUAL_NETWORK|Zarządzanie interfejsami API komunikacji klienta|Zewnętrzne |
| * / 3443 |Przychodzący |TCP |INTERNET / VIRTUAL_NETWORK|Punkt końcowy zarządzania dla portalu Azure i programu Powershell |Wewnętrzny |
| * / 80, 443 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|**Zależność od usługi Azure Storage**, usługi Azure Service Bus i Azure Active Directory (jeśli dotyczy).|Zewnętrzne i wewnętrzne | 
| * / 1433 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|**Dostęp do punktów końcowych Azure SQL** |Zewnętrzne i wewnętrzne |
| * / 5672 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Zależność od dziennika zasad Centrum zdarzeń i agenta monitorowania |Zewnętrzne i wewnętrzne |
| * / 445 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Zależności w udziale plików platformy Azure dla GIT |Zewnętrzne i wewnętrzne |
| * / 1886 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Wymagane do publikowania kondycji zasobów stan kondycji |Zewnętrzne i wewnętrzne |
| * / 25028 |Wychodzący |TCP |VIRTUAL_NETWORK / INTERNET|Połącz z przekazywaniem SMTP do wysyłania wiadomości E-mail |Zewnętrzne i wewnętrzne |
| * / 6381 - 6383 |Dla ruchu przychodzącego i wychodzącego |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Wystąpienia pamięci podręcznej Redis dostępu między RoleInstances |Zewnętrzne i wewnętrzne |
| * / * | Przychodzący |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Moduł równoważenia obciążenia infrastruktury platformy Azure |Zewnętrzne i wewnętrzne |

>[!IMPORTANT]
> Porty, dla którego *celu* jest **bold** są wymagane dla usługi interfejsu API zarządzania zostanie pomyślnie wdrożona. Blokowanie inne porty jednak spowoduje obniżenie w możliwość użycia i uruchomione usługi monitorowania.

* **Funkcje protokołu SSL**: Aby umożliwić tworzenie łańcucha certyfikatów SSL i sprawdzania poprawności zarządzanie interfejsami API usługi musi mieć łączność sieciową wychodzących ocsp.msocsp.com, mscrl.microsoft.com i crl.microsoft.com. Ta zależność nie jest wymagana, jeśli dowolny certyfikat, które zostaną przesłane do interfejsu API zarządzania zawierają pełny łańcuch do głównego urzędu certyfikacji.

* **Dostęp DNS**: wychodzący dostęp przez port 53 jest wymagana do komunikacji przy użyciu serwerów DNS. Jeśli istnieje niestandardowy serwer DNS na drugim końcu bramy sieci VPN, serwer DNS musi być dostępny w podsieci hostingu API Management.

* **Monitorowanie kondycji i metryki**: połączenie sieciowe ruchu wychodzącego Azure punktów końcowych monitorowania, które rozwiązanie w następujących domen: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net, prod.warmpath.msftcloudes.com prod3 black.prod3.metrics.nsatc.net i prod3 red.prod3.metrics.nsatc.net.

* **Trasy Instalacja ekspresowa**: Typowa konfiguracja klienta jest określenie własnych trasa domyślna (0.0.0.0/0), co zmusza wychodzący ruch internetowy, zamiast niego przepływ lokalnymi. Ten przepływ ruchu niezmiennie dzieli łączność z usługą Azure API Management ruch wychodzący zablokowanych lokalnie, ponieważ NAT czy nierozpoznawalną zbiór adresów, które nie będą działać z różnymi punkty końcowe systemu Azure. Rozwiązanie jest określenie jednego (lub więcej) trasy zdefiniowane przez użytkownika ([Udr][UDRs]) w podsieci, która zawiera Azure API Management. PRZEZ definiuje tras specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.
  Jeśli to możliwe zaleca się następującej konfiguracji:
 * Konfiguracji usługi ExpressRoute anonsuje 0.0.0.0/0 i domyślnie życie tuneli wszystkich ruch wychodzący lokalnymi.
 * PRZEZ stosowana do podsieci, zawierający Azure API Management definiuje 0.0.0.0/0 z Internetu Typ następnego przeskoku.
 Łączna tych kroków powoduje, że poziomie podsieci przez ma pierwszeństwo przed ExpressRoute, wymuszone tunelowanie, w związku z tym zapewnienie wychodzący dostęp do Internetu z usługi Azure API Management.

* **Routingu za pośrednictwem sieci wirtualnych urządzeń**: blokuje konfiguracje, które umożliwia przez trasa domyślna (0.0.0.0/0) kierować ruch internetowy przeznaczony z podsieci interfejsu API zarządzania za pośrednictwem sieci urządzenie wirtualne działające na platformie Azure ruch związany z zarządzaniem pochodzące z Internetu do wystąpienia usługi Zarządzanie interfejsami API wdrożone w podsieci sieci wirtualnej. Ta konfiguracja nie jest obsługiwana.

>[!WARNING]  
>Azure API Management nie jest obsługiwany w konfiguracji usługi ExpressRoute który **niepoprawnie cross anonsować tras z publicznej komunikacji równorzędnej ścieżki do ścieżki prywatnej komunikacji równorzędnej**. Konfiguracji usługi ExpressRoute, które mają publicznej komunikacji równorzędnej skonfigurowane, otrzyma anonsów tras firmy Microsoft dla dużych zestawów zakresów adresów IP firmy Microsoft Azure. Jeśli te zakresy adresów są niepoprawnie cross anonsowany w ścieżce prywatnej komunikacji równorzędnej, wynik końcowy są wszystkie pakiety wychodzącego z podsieci wystąpienia usługi Azure API Management niepoprawnie force-tunneled do sieci lokalnej klienta infrastruktura. Ten przepływ sieci dzieli Azure API Management. Rozwiązanie tego problemu jest zatrzymanie tras między reklam z publicznej komunikacji równorzędnej ścieżki do ścieżki prywatnej komunikacji równorzędnej.


## <a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Wstępne instalacji**: po początkowym wdrożeniu usługi API Management do podsieci nie powiedzie się, zaleca się najpierw wdrożyć maszynę wirtualną do tej samej podsieci. Następny pulpitu zdalnego do maszyny wirtualnej i sprawdź, czy jest łączność każdego zasobu poniżej w Twojej subskrypcji platformy azure 
    * Obiekt blob magazynu Azure
    * Azure SQL Database

 > [!IMPORTANT]
 > Po zweryfikowaniu połączenia, upewnij się usunąć wszystkie zasoby, które są wdrożone w podsieci, przed wdrożeniem usługi API Management do podsieci.

* **Aktualizacje przyrostowe**: podczas wprowadzania zmian w sieci, zapoznaj się [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), aby sprawdzić usługi API Management nie utracił dostęp do wszystkich kluczowych zasobów, których ona zależy. Stan powinien być aktualizowany co 15 minut.

* **Linki nawigacji zasobu**: w przypadku wdrażania w podsieci sieci wirtualnej styl Menedżera zasobów, zarządzanie interfejsami API rezerwuje podsieci, tworząc łącza nawigacji zasobu. Jeśli podsieć zawiera już zasób od innego dostawcy, wdrożenie zostanie **niepowodzenie**. Podobnie podczas przenoszenia usługi API Management do innej podsieci lub usuń go, zostanie usunięty tego łącza nawigacji zasobu. 

## <a name="subnet-size"> </a> Wymagany rozmiar podsieci
Azure rezerwuje niektórych adresów IP w każdej podsieci, a nie można użyć tych adresów. Imię i nazwisko adresów IP podsieci są zastrzeżone dla protokołu zgodność, wraz z trzech więcej adresów używanych na potrzeby usług Azure. Aby uzyskać więcej informacji, zobacz [istnieją wszystkie ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresy IP używane przez infrastrukturę sieci Wirtualnej Azure każde wystąpienie interfejsu Api zarządzania w podsieci używa dwóch adresów IP na jednostkę warstwy Premium lub adresów IP dla jednostki SKU Developer. Każde wystąpienie rezerwuje dodatkowe adresu IP zewnętrznej usługi równoważenia obciążenia. W przypadku wdrażania w wewnętrznej sieci wirtualnej, wymaga dodatkowego adresu IP usługi równoważenia obciążenia wewnętrznego.

Biorąc pod uwagę obliczania powyżej minimalny rozmiar podsieci, w którym można wdrożyć zarządzanie interfejsami API jest /29, co daje 3 adresów IP.

## <a name="routing"> </a> Routing
+ Aby zapewnić dostęp do wszystkich punktów końcowych usługi zostanie zarezerwowane ze zrównoważonym obciążeniem publiczny adres IP (VIP).
+ Adres IP z zakresu podsieci IP (DIP) będzie umożliwiać dostęp do zasobów w sieci wirtualnej, a publicznego adresu IP (VIP) będzie używany do dostępu do zasobów poza siecią wirtualną.
+ Publiczny adres IP o zrównoważonym obciążeniu adres znajduje się w bloku Przegląd/Essentials w portalu Azure.

## <a name="limitations"> </a>Ograniczenia
* W podsieci zawierającej wystąpienia interfejsu API zarządzania nie może zawierać innych typów zasobów platformy Azure.
* Podsieć i usługi API Management musi być w tej samej subskrypcji.
* Nie można przenieść w podsieci zawierającej wystąpienia interfejsu API zarządzania różnych subskrypcji.
* W przypadku wdrożeń zarządzanie interfejsami API skonfigurowana w trybie wewnętrznej sieci wirtualnej użytkownicy są odpowiedzialny za zarządzanie Równoważenie obciążenia w wielu regionach, jak są właścicielami routingu.
* Łączność z zasobów w sieci Wirtualnej globalnie peered w innym regionie usługi Zarządzanie interfejsami API w trybie wewnętrznej nie będą działać ze względu na ograniczenia platform. Aby uzyskać więcej informacji, zobacz [zasobów w jednej sieci wirtualnej nie może komunikować się z Azure wewnętrznego modułu równoważenia obciążenia w połączyć za pomocą sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Zawartość pokrewna
* [Połączenie wirtualnej sieci do wewnętrznej bazy danych przy użyciu bramy sieci Vpn](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Łączenie z różne modele wdrażania sieci wirtualnej](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Sposób użycia interfejsu API inspektora śledzenia wywołań w usłudze Azure API Management](api-management-howto-api-inspector.md)
* [Często zadawane pytania dotyczące sieci wirtualnej](../virtual-network/virtual-networks-faq.md)

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
