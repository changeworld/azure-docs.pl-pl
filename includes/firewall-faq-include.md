---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 10/20/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: e33871f35613fbd5cdc5bf3162855b942056807f
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254508"
---
### <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest w pełni stanowych zapory jako — usługa wbudowaną wysoką dostępność i skalowalność chmury bez ograniczeń. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jakie funkcje są obsługiwane przez zaporę usługi Azure?

* Zapora stanowa jako usługa
* Wbudowana wysoka dostępność z nieograniczoną skalowalnością w chmurze
* Filtrowanie według nazw FQDN
* Tagi w pełni kwalifikowanych nazw domen
* Reguły filtrowania ruchu sieciowego
* Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
* Obsługa technologii DNAT dla ruchu przychodzącego
* Centralnie tworzenie, wymuszanie i dziennika aplikacji i sieci zasad łączności różnych subskrypcji platformy Azure z sieciami wirtualnymi
* Pełna integracja z usługą Azure Monitor na potrzeby rejestrowania i analizy

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co to jest model typowe wdrożenie dla zapory usługi Azure?

Zaporę usługi Azure można wdrożyć w dowolnej sieci wirtualnej, ale klienci zwykle wdrożyć ją w centralnej sieci wirtualnej i nawiązać komunikację równorzędną między innymi sieciami wirtualnymi do niego w modelu koncentrator i klienci. Następnie można ustawić trasy domyślnej w wirtualnych sieciach równorzędnych wskaż tej centralnej zapory sieci wirtualnej.

### <a name="how-can-i-install-the-azure-firewall"></a>Jak zainstalować zapory usługi Azure?

Za pomocą witryny Azure portal, programu PowerShell, interfejsu API REST lub przy użyciu szablonów, można skonfigurować zapory usługi Azure. Zobacz [samouczek: Wdrażanie i konfigurowanie zapory platformy Azure przy użyciu witryny Azure portal](../articles/firewall/tutorial-firewall-deploy-portal.md) instrukcje krok po kroku.

### <a name="what-are-some-azure-firewall-concepts"></a>Jakie są niektóre pojęcia zapory usługi Azure?

Zapora usługi Azure obsługuje reguł i kolekcji reguł. Kolekcja reguł jest zestaw reguł, które współużytkują ten sam kolejności i priorytet. Kolekcje reguł są wykonywane w kolejności według ich priorytetu. Kolekcje reguł sieci mają wyższy priorytet niż kolekcje reguły aplikacji, a wszystkie reguły są przerywa.

Istnieją dwa rodzaje kolekcji reguł:

* *Zasady aplikacji*: umożliwiają skonfigurowanie w pełni kwalifikowanych nazw domen (FQDN), które są dostępne z podsieci.
* *Reguł sieciowych*: umożliwiają skonfigurowanie reguł, które zawiera źródłowe adresy, protokoły, porty docelowe i docelowe adresy.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Zaporę usługi Azure obsługuje filtrowanie ruchu przychodzącego?

Zaporę platformy Azure obsługuje filtrowanie przychodzącego i wychodzącego. Ochrona dla ruchu przychodzącego jest protokołów innych niż HTTP/Https. Na przykład protokoły RDP, SSH i FTP.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Jakie usługi rejestrowania i analizy są obsługiwane przez zaporę usługi Azure?

Zaporę platformy Azure jest zintegrowana z usługą Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do usługi Log Analytics, Azure Storage lub centrów zdarzeń. Mogą być analizowane w usłudze Log Analytics lub przez różnych narzędzi, takich jak program Excel i Power BI. Aby uzyskać więcej informacji, zobacz [samouczek: dzienniki zapory usługi Azure Monitor](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak zapory usługi Azure działa inaczej w istniejących usług, takich jak urządzenia WUS w portalu marketplace?

Zaporę platformy Azure to usługa Zapora podstawowa, która rozwiązywania przy użyciu określonych scenariuszy. Oczekuje się, konieczne może być zarówno urządzeń WUS innych firm i zapory usługi Azure. Współpracując z lepiej jest to główny priorytet.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między brama aplikacji zapory aplikacji internetowych i zapory usługi Azure?

Zapora aplikacji sieci Web (WAF) to funkcja usługi Application Gateway, która zapewnia scentralizowaną ochronę ruchu przychodzącego w aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Zaporę platformy Azure zapewnia ochronę ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład protokołu RDP, SSH, FTP), wychodzące ochrony na poziomie sieci dla wszystkich portów i protokołów i ochrony na poziomie aplikacji dla ruchu wychodzącego HTTP/s

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaka jest różnica między grupami zabezpieczeń sieci (NSG) i zapory usługi Azure?

Usługa zapory usługi Azure uzupełniają funkcje grupy zabezpieczeń sieci. Razem zapewniają większe bezpieczeństwo sieci "ochronę w głębi". Sieciowe grupy zabezpieczeń zapewniają rozproszonych ruchu warstwy sieci, filtrowanie, aby ograniczyć ruch do zasobów w sieciach wirtualnych w ramach każdej subskrypcji. Zaporę platformy Azure jest pełni stanowe, scentralizowane sieci zapory jako usługa, co zapewnia ochronę na poziomie sieci i aplikacji w różnych subskrypcjach i sieciami wirtualnymi.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak skonfigurować zaporę usługi Azure za pomocą moje punkty końcowe usługi?

Bezpieczny dostęp do usług PaaS zalecamy punktów końcowych usługi. Można włączyć punktów końcowych usługi w podsieci zapory usługi Azure i je wyłączyć w sieciach wirtualnych połączonych szprychy. W ten sposób, możesz korzystać z funkcji — usługi punktu końcowego zabezpieczeń i centralnego rejestrowania dla całego ruchu.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Co to jest cennik dla zapory usługi Azure?

Zaporę platformy Azure ma koszt stały + koszty zmiennej:

* Stała opłata: $1.25/firewall/hour
* Zmiennej opłaty: 0,03 USD/GB przetworzonych przez zapory (ruch przychodzący lub wychodzący)

Brak żadnymi kosztami zapory przydział zostanie cofnięty.

Aby uzyskać więcej informacji, zobacz [cennik usługi Azure zapory](https://azure.microsoft.com/pricing/details/azure-firewall/).

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak zatrzymać i uruchomić Zaporę usługi Azure?

Można użyć programu Azure PowerShell *deallocate* i *przydzielić* metody.

Na przykład:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Należy zmienić alokację zapory i publiczny adres IP do oryginalnej grupy zasobów i subskrypcji.

### <a name="what-are-the-known-service-limits"></a>Jakie są limity znane usługi?

Limity usługi zapory usługi Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Można zapory usługi Azure w sieci wirtualnej koncentratora i do przodu i filtrowanie ruchu sieciowego między dwiema sieciami wirtualnymi szprychy?

Tak, można użyć zapory usługi Azure, w centralnej sieci wirtualnej trasy i filtrowanie ruchu między siecią wirtualną dwóch szprychy. Podsieci w każdej sieci wirtualne będące szprychami muszą mieć Routing zdefiniowany przez użytkownika wskazuje zapory platformy Azure jako brama domyślna, w tym scenariuszu do poprawnego działania.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network"></a>Do przodu może zapory usługi Azure i filtrowanie ruchu sieciowego między podsieciami w tej samej sieci wirtualnej?

Ruch między podsieciami w tej samej sieci wirtualnej lub w wirtualnej sieci równorzędnej bezpośrednio jest kierowany bezpośrednio nawet wtedy, gdy trasa zdefiniowana przez użytkownika wskazuje zapory platformy Azure jako brama domyślna. Zalecaną metodą segmentacji sieci wewnętrznej jest przy użyciu grup zabezpieczeń sieci. Aby wysyłać ruch podsieci do sieci do zapory, w tym scenariuszu, trasa zdefiniowana przez użytkownika musi zawierać prefiks docelowy podsieci sieci jawnie na obie podsieci.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Czy istnieją wszystkie zapory ograniczenia grup zasobów?

Tak. Zapora, podsieci, sieci wirtualnej i publicznego adresu IP musi być w tej samej grupie zasobów.
