---
title: Zaporę platformy Azure — często zadawane pytania
description: Często zadawane pytania dotyczące zapory platformy Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 5/3/2019
ms.author: victorh
ms.openlocfilehash: 4c4a6776e3bb56026a48963ec83fe582380c68d0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145960"
---
# <a name="azure-firewall-faq"></a>Zaporę platformy Azure — często zadawane pytania

## <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest w pełni stanowych zapory jako — usługa wbudowaną wysoką dostępność i skalowalność chmury bez ograniczeń. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jakie funkcje są obsługiwane przez zaporę usługi Azure?

* Zapora stanowa jako usługa
* Wbudowana wysoka dostępność z nieograniczoną skalowalnością w chmurze
* Filtrowanie według nazw FQDN
* Tagi w pełni kwalifikowanych nazw domen
* Reguły filtrowania ruchu sieciowego
* Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
* Obsługa technologii DNAT dla ruchu przychodzącego
* Centralnie tworzenie, wymuszanie i dziennika aplikacji i sieci zasad łączności różnych subskrypcji platformy Azure z sieciami wirtualnymi
* Pełna integracja z usługą Azure Monitor na potrzeby rejestrowania i analizy

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co to jest model typowe wdrożenie dla zapory usługi Azure?

Zaporę usługi Azure można wdrożyć w dowolnej sieci wirtualnej, ale klienci zwykle wdrożyć ją w centralnej sieci wirtualnej i nawiązać komunikację równorzędną między innymi sieciami wirtualnymi do niego w modelu koncentrator i klienci. Następnie można ustawić trasy domyślnej w wirtualnych sieciach równorzędnych wskaż tej centralnej zapory sieci wirtualnej. Globalne wirtualne sieci równorzędne są obsługiwane, ale nie jest zalecane z powodu granicę potencjalnej wydajności i opóźnień między regionami. Aby uzyskać najlepszą wydajność należy wdrożyć jedną zaporą na region.

Zaletą tego modelu to możliwość centralnego działania sterowania na wiele sieci wirtualne będące szprychami w różnych subskrypcjach. Istnieją również oszczędności, ponieważ nie trzeba wdrażać oddzielnie zapory w każdej sieci wirtualnej. Oszczędności kosztów powinno być mierzone lub skojarz koszty komunikacji równorzędnej na podstawie wzorców ruchu klientów.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak zainstalować zapory usługi Azure?

Za pomocą witryny Azure portal, programu PowerShell, interfejsu API REST lub przy użyciu szablonów, można skonfigurować zapory usługi Azure. Zobacz [samouczka: Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md) instrukcje krok po kroku.

## <a name="what-are-some-azure-firewall-concepts"></a>Jakie są niektóre pojęcia zapory usługi Azure?

Zapora usługi Azure obsługuje reguł i kolekcji reguł. Kolekcja reguł jest zestaw reguł, które współużytkują ten sam kolejności i priorytet. Kolekcje reguł są wykonywane w kolejności według ich priorytetu. Kolekcje reguł sieci mają wyższy priorytet niż kolekcje reguły aplikacji, a wszystkie reguły są przerywa.

Istnieją trzy typy kolekcji reguł:

* *Zasady aplikacji*: Skonfiguruj w pełni kwalifikowanych nazw domen (FQDN), które są dostępne z podsieci.
* *Reguł sieciowych*: Skonfiguruj reguły, które zawierają adresów źródłowych, protokoły, porty docelowe i docelowe adresy.
* *Reguły translatora adresów Sieciowych*: Skonfiguruj reguły DNAT zezwalać na połączenia przychodzące.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Zaporę usługi Azure obsługuje filtrowanie ruchu przychodzącego?

Zaporę platformy Azure obsługuje filtrowanie przychodzącego i wychodzącego. Ochrona dla ruchu przychodzącego jest protokołów innych niż HTTP/Https. Na przykład protokoły RDP, SSH i FTP.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Jakie usługi rejestrowania i analizy są obsługiwane przez zaporę usługi Azure?

Zaporę platformy Azure jest zintegrowana z usługą Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do usługi Log Analytics, Azure Storage lub centrów zdarzeń. Mogą być analizowane w usłudze Log Analytics lub przez różnych narzędzi, takich jak program Excel i Power BI. Aby uzyskać więcej informacji, zobacz [Samouczek: Monitoruj dzienniki zapory usługi Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak zapory usługi Azure działa inaczej w istniejących usług, takich jak urządzenia WUS w portalu marketplace?

Zaporę platformy Azure to usługa Zapora podstawowa, która rozwiązywania przy użyciu określonych scenariuszy. Oczekuje się, konieczne może być zarówno urządzeń WUS innych firm i zapory usługi Azure. Współpracując z lepiej jest to główny priorytet.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między brama aplikacji zapory aplikacji internetowych i zapory usługi Azure?

Zapora aplikacji sieci Web (WAF) to funkcja usługi Application Gateway, która zapewnia scentralizowaną ochronę ruchu przychodzącego w aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Zaporę platformy Azure zapewnia ochronę ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład protokołu RDP, SSH, FTP), wychodzące ochrony na poziomie sieci dla wszystkich portów i protokołów i ochrony na poziomie aplikacji dla ruchu wychodzącego HTTP/s

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaka jest różnica między grupami zabezpieczeń sieci (NSG) i zapory usługi Azure?

Usługa zapory usługi Azure uzupełniają funkcje grupy zabezpieczeń sieci. Razem zapewniają większe bezpieczeństwo sieci "ochronę w głębi". Sieciowe grupy zabezpieczeń zapewniają rozproszonych ruchu warstwy sieci, filtrowanie, aby ograniczyć ruch do zasobów w sieciach wirtualnych w ramach każdej subskrypcji. Zaporę platformy Azure jest pełni stanowe, scentralizowane sieci zapory jako usługa, co zapewnia ochronę na poziomie sieci i aplikacji w różnych subskrypcjach i sieciami wirtualnymi.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak skonfigurować zaporę usługi Azure za pomocą moje punkty końcowe usługi?

Bezpieczny dostęp do usług PaaS zalecamy punktów końcowych usługi. Można włączyć punktów końcowych usługi w podsieci zapory usługi Azure i je wyłączyć w sieciach wirtualnych połączonych szprychy. W ten sposób, możesz korzystać z funkcji — usługi punktu końcowego zabezpieczeń i centralnego rejestrowania dla całego ruchu.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Co to jest cennik dla zapory usługi Azure?

Zobacz [zaporę platformy Azure, cennik](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak zatrzymać i uruchomić Zaporę usługi Azure?

Można użyć programu Azure PowerShell *deallocate* i *przydzielić* metody.

Na przykład:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Należy zmienić alokację zapory i publiczny adres IP do oryginalnej grupy zasobów i subskrypcji.

## <a name="what-are-the-known-service-limits"></a>Jakie są limity znane usługi?

Limity usługi zapory usługi Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Można zapory usługi Azure w sieci wirtualnej koncentratora i do przodu i filtrowanie ruchu sieciowego między dwiema sieciami wirtualnymi szprychy?

Tak, można użyć zapory usługi Azure, w centralnej sieci wirtualnej trasy i filtrowanie ruchu między siecią wirtualną dwóch szprychy. Podsieci w każdej sieci wirtualne będące szprychami muszą mieć Routing zdefiniowany przez użytkownika wskazuje zapory platformy Azure jako brama domyślna, w tym scenariuszu do poprawnego działania.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Do przodu może zapory usługi Azure i filtrowanie ruchu sieciowego między podsieciami w tej samej sieci wirtualnej lub równorzędnej sieci wirtualnych?

Tak. Konfigurowanie tras zdefiniowanych przez użytkownika, aby przekierować ruch pomiędzy podsieciami w tej samej sieci Wirtualnej wymaga jednak wymagają dodatkowej uwagi. Podczas korzystania z zakres adresów sieci Wirtualnej, ponieważ prefiks docelowy dla zdefiniowanej przez użytkownika jest wystarczająca, również są kierowane cały ruch z jednego komputera do innej maszyny w tej samej podsieci za pośrednictwem wystąpienia zapory usługi Azure. Aby tego uniknąć, objęte tras dla podsieci zdefiniowanej przez użytkownika z typem następnego przeskoku dla **sieci Wirtualnej**. Zarządzanie te trasy może być uciążliwe i podatne na błędy. Zalecaną metodą segmentacji sieci wewnętrznej jest używać sieciowych grup zabezpieczeń, które nie wymagają tras zdefiniowanych przez użytkownika.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Jest wymuszone tunelowanie łańcucha do wirtualnego urządzenia sieciowego obsługiwane?

Wymuszone tunelowanie nie jest obsługiwany przez domyślną, ale można ją włączyć za pomocą pomocy od działu pomocy technicznej.

Zaporę platformy Azure musi mieć bezpośrednie połączenie z Internetem. Jeśli Twoje AzureFirewallSubnet uczy się trasę domyślną elementom sieci lokalnej za pośrednictwem protokołu BGP, konieczne jest przesłonięcie to za pomocą 0.0.0.0/0 trasy zdefiniowanej przez użytkownika za pomocą **Typ następnego przeskoku** wartość ustawiona jako **Internet** utrzymanie bezpośredniej Łączność z Internetem. Domyślnie Zapora usługi Azure nie obsługuje wymuszonego tunelowania do sieci lokalnej.

Jednak jeśli konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej, Microsoft będzie obsługiwać go na podstawie przypadku. Się z pomocą techniczną, dzięki czemu możesz przejrzeć tej sprawy. Jeśli zaakceptowane, utworzymy dozwolonych subskrypcji i upewnij się, że połączenie z Internetem wymagany zapory są obsługiwane.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Czy istnieją wszystkie zapory ograniczenia grup zasobów?

Tak. Zapora, podsieci, sieci wirtualnej i publicznego adresu IP musi być w tej samej grupie zasobów.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Podczas konfigurowania DNAT dla przychodzącego ruchu sieciowego, również należy skonfigurować odpowiednią regułę sieci, aby zezwolić na ruch?

Nie. Reguły translatora adresów Sieciowych niejawnie Dodaj odpowiednie sieci regułę zezwalającą na ruch tłumaczenia. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak działają symboli wieloznacznych w aplikacji obiekt docelowy reguły pełni kwalifikowaną nazwę domeny?

Jeśli skonfigurujesz ***. contoso.com**, umożliwia ona *anyvalue*. contoso.com, ale nie contoso.com (wierzchołki domeny). Jeśli chcesz zezwolić na wierzchołku domeny jawnie należy go skonfigurować jako docelowa nazwa FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Jak działa *stan inicjowania obsługi: Nie powiodło się* oznacza?

Zawsze, gdy po zastosowaniu zmiany konfiguracji, zapory usługi Azure próbuje zaktualizować jego podstawowego wystąpienia wewnętrznej bazy danych. W rzadkich przypadkach jednego z tych wystąpień zaplecza może nie można zaktualizować przy użyciu nowej konfiguracji, a następnie zatrzyma proces aktualizacji nie powiodło się stan aprowizacji. Zapora usługi Azure jest nadal działa, ale zastosowane konfiguracji może być w stanie niespójnym, gdzie niektóre wystąpienia mają poprzednią konfigurację, gdy inne osoby mają zaktualizowanego zestawu reguł. Jeśli tak się stanie, spróbuj zaktualizować konfigurację jednego więcej czasu, aż operacja powiedzie się, a Zapora w *Powodzenie* stan inicjowania obsługi.
