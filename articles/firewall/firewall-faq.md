---
title: Zapora platformy Azure – często zadawane pytania
description: Często zadawane pytania dotyczące zapory platformy Azure. Zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 5ddbb58837fbda0f14a07186d5a3053055954454
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677451"
---
# <a name="azure-firewall-faq"></a>Zapora platformy Azure – często zadawane pytania

## <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jakie możliwości są obsługiwane w Zaporze platformy Azure?

* Stanowa zapora jako usługa
* Wbudowana wysoka dostępność z nieograniczoną skalowalnością chmury
* Filtrowanie według nazw FQDN
* Tagi w pełni kwalifikowanych nazw domen
* Reguły filtrowania ruchu sieciowego
* Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
* Obsługa technologii DNAT dla ruchu przychodzącego
* Centralne tworzenie, wymuszanie i rejestrowanie zasad łączności aplikacji i sieci w ramach subskrypcji platformy Azure i sieci wirtualnych
* Pełna integracja z usługą Azure Monitor do rejestrowania i analizy

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Co to jest typowy model wdrażania zapory platformy Azure?

Zaporę platformy Azure można wdrożyć w dowolnej sieci wirtualnej, ale klienci zazwyczaj wdrażają ją w centralnej sieci wirtualnej i równorzędnie innych sieci wirtualnych do niej w modelu koncentratora i szprychy. Następnie można ustawić domyślną trasę z sieci wirtualnych równorzędnych, aby wskazywała tę centralną sieć wirtualną zapory. Globalna komunikacja równorzędna sieci wirtualnej jest obsługiwana, ale nie jest zalecane ze względu na potencjalne problemy z wydajnością i opóźnieniami w różnych regionach. Aby uzyskać najlepszą wydajność, należy wdrożyć jedną zaporę na region.

Zaletą tego modelu jest możliwość centralnego wywierania kontroli na wielu szprychowych vnets w różnych subskrypcjach. Istnieją również oszczędności kosztów, ponieważ nie trzeba wdrażać zapory w każdej sieci wirtualnej oddzielnie. Oszczędności kosztów należy mierzyć w porównaniu z kosztem komunikacji równorzędnej na podstawie wzorców ruchu klientów.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak zainstalować Zaporę platformy Azure?

Zaporę platformy Azure można skonfigurować przy użyciu witryny Azure portal, powershell, rest api lub przy użyciu szablonów. Zobacz [samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md) dla instrukcji krok po kroku.

## <a name="what-are-some-azure-firewall-concepts"></a>Jakie są koncepcje zapory platformy Azure?

Zapora azure obsługuje reguły i kolekcje reguł. Kolekcja reguł to zestaw reguł, które mają tę samą kolejność i priorytet. Kolekcje reguł są wykonywane w kolejności ich priorytetu. Kolekcje reguł sieciowych mają wyższy priorytet niż kolekcje reguł aplikacji, a wszystkie reguły kończą się.

Istnieją trzy typy kolekcji reguł:

* *Reguły aplikacji:* Konfigurowanie w pełni kwalifikowanych nazw domen (FQDN), do których można uzyskać dostęp z podsieci.
* *Reguły sieciowe:* Konfigurowanie reguł zawierających adresy źródłowe, protokoły, porty docelowe i adresy docelowe.
* *Reguły TRANSLATORA*: Skonfiguruj reguły DNAT, aby zezwolić na przychodzące połączenia internetowe.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Czy Zapora platformy Azure obsługuje filtrowanie ruchu przychodzącego?

Zapora azure obsługuje filtrowanie przychodzące i wychodzące. Ochrona ruchu przychodzącego jest zwykle używana dla protokołów innych niż HTTP/S. Na przykład protokoły RDP, SSH i FTP. Aby uzyskać najlepszą przychodzącą ochronę HTTP/S, użyj zapory aplikacji sieci Web, takiej jak [Zapora aplikacji sieci Web azure (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Które usługi rejestrowania i analizy są obsługiwane przez zaporę platformy Azure?

Zapora azure jest zintegrowana z usługą Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do usługi Log Analytics, usługi Azure Storage lub Centrum zdarzeń. Można je analizować w usłudze Log Analytics lub za pomocą różnych narzędzi, takich jak Excel i Power BI. Aby uzyskać więcej informacji, zobacz [Samouczek: Monitorowanie dzienników zapory platformy Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak zapora azure działa inaczej niż istniejące usługi, takie jak usługi w portalu marketplace?

Zapora azure to podstawowa usługa zapory, która może rozwiązać niektóre scenariusze klientów. Oczekuje się, że będziesz mieć kombinację usług NVA innych firm i zaporą platformy Azure. Lepsza współpraca jest głównym priorytetem.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między usługą WAF bramy aplikacji a zaporą platformy Azure?

Zapora aplikacji sieci Web (WAF) jest funkcją bramy aplikacji, która zapewnia scentralizowaną ochronę przychodzącą aplikacji sieci web przed typowymi exploitami i lukami w zabezpieczeniach. Zapora azure zapewnia ochronę ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład RDP, SSH, FTP), wychodzącą ochronę na poziomie sieci dla wszystkich portów i protokołów oraz ochronę na poziomie aplikacji dla wychodzącego protokołu HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaka jest różnica między sieciowymi grupami zabezpieczeń (NSG) a zaporą platformy Azure?

Usługa Zapora azure uzupełnia funkcje sieciowej grupy zabezpieczeń. Razem zapewniają one lepsze zabezpieczenia sieci "obrony w głębi". Sieciowe grupy zabezpieczeń zapewniają filtrowanie ruchu rozproszonej warstwy sieciowej w celu ograniczenia ruchu do zasobów w sieciach wirtualnych w każdej subskrypcji. Zapora azure to w pełni stanowa, scentralizowana zapora sieciowa jako usługa, która zapewnia ochronę na poziomie sieci i aplikacji w różnych subskrypcjach i sieciach wirtualnych.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Czy sieciowe grupy zabezpieczeń są obsługiwane w podsieci Zapory platformy Azure?

Zapora azure to usługa zarządzana z wieloma warstwami ochrony, w tym ochroną platformy z sieciami sieciowymi na poziomie sieci nych (nie widocznymi).  Sieciowe sieci zabezpieczeń na poziomie podsieci nie są wymagane w podsieci Zapory platformy Azure i są wyłączone, aby zapewnić brak przerw w świadczeniu usług.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak skonfigurować zaporę platformy Azure z punktami końcowymi usługi?

Aby zapewnić bezpieczny dostęp do usług PaaS, zalecamy punkty końcowe usługi. Można włączyć punkty końcowe usługi w podsieci Zapory platformy Azure i wyłączyć je w połączonych sieciach wirtualnych szprychy. W ten sposób można korzystać z obu funkcji: zabezpieczenia punktu końcowego usługi i centralnego rejestrowania dla całego ruchu.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Jakie są ceny zapory platformy Azure?

Zobacz [Cennik zapory platformy Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak mogę zatrzymać i uruchomić Zaporę platformy Azure?

Można użyć narzędzia Azure PowerShell *deallocate* i *przydzielić* metody.

Przykład:

```azurepowershell
# Stop an existing firewall

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
> Należy ponownie przydzielić zaporę i publiczny adres IP do oryginalnej grupy zasobów i subskrypcji.

## <a name="what-are-the-known-service-limits"></a>Jakie są znane limity usług?

W przypadku limitów usług Zapory platformy Azure zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Czy Zapora azure w sieci wirtualnej centrum do przodu i filtrować ruch sieciowy między dwiema sieciami wirtualnymi szprychy?

Tak, za pomocą zapory azure w sieci wirtualnej centrum do kierowania i filtrowania ruchu między dwiema sieciami wirtualnymi szprychy. Podsieci w każdej sieci wirtualnej szprychy musi mieć UDR wskazując na Zaporę platformy Azure jako bramę domyślną dla tego scenariusza do poprawnego działania.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Czy zapora azure może przesyłać dalej i filtrować ruch sieciowy między podsieciami w tej samej sieci wirtualnej lub w sieciach wirtualnych równorzędnych?

Tak. Jednak konfigurowanie UDR do przekierowywania ruchu między podsieciami w tej samej sieci wirtualnej wymaga dodatkowej uwagi. Podczas korzystania z zakresu adresów sieci wirtualnej jako prefiks docelowy dla UDR jest wystarczająca, to również trasy całego ruchu z jednego komputera na inny komputer w tej samej podsieci za pośrednictwem wystąpienia zapory platformy Azure. Aby tego uniknąć, należy uwzględnić trasę dla podsieci w UDR z następnym typem przeskoku **sieci wirtualnej**. Zarządzanie tymi trasami może być uciążliwe i podatne na błędy. Zalecaną metodą wewnętrznej segmentacji sieci jest użycie grup zabezpieczeń sieciowych, które nie wymagają UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Czy usługa Azure Firewall wychodzących SNAT między sieciami prywatnymi?

Zapora azure nie SNAT, gdy docelowy adres IP jest prywatny zakres IP na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Jeśli twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, usługa Azure Firewall sNATs ruchu do jednego z zapory prywatnych adresów IP w usłudze AzureFirewallSubnet. Zaporę platformy Azure można skonfigurować tak, aby **nie** była dostępna w zakresie publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Zakresy prywatnych adresów IP zapory azure SNAT](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Czy wymuszone tunelowanie/tworzenie łańcucha do sieciowego urządzenia wirtualnego jest obsługiwane?

Wymuszone tunelowanie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [Tunelowanie wymuszone zaporą platformy Azure (wersja zapoznawcza)](forced-tunneling.md). 

Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli azurefirewallsubnet uczy się domyślnej trasy do sieci lokalnej za pośrednictwem protokołu BGP, należy zastąpić to z 0.0.0.0/0 UDR z **NextHopType** wartość ustawiona jako **Internet,** aby utrzymać bezpośrednią łączność z Internetem.

Jeśli konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej i można określić docelowe prefiksy IP dla miejsc docelowych Internetu, można skonfigurować te zakresy za pomocą sieci lokalnej jako następnego przeskoku za pośrednictwem trasy zdefiniowanej przez użytkownika w usłudze AzureFirewallSubnet. Można też użyć protokołu BGP do zdefiniowania tych tras.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Czy istnieją ograniczenia grupy zasobów zapory?

Tak. Zapora, sieć wirtualna i publiczny adres IP muszą znajdować się w tej samej grupie zasobów.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Czy podczas konfigurowania DNAT dla przychodzącego ruchu sieciowego muszę również skonfigurować odpowiednią regułę sieci, aby zezwolić na ten ruch?

Nie. Reguły TRANSLATORa niejawnie dodają odpowiednią regułę sieciową, aby zezwolić na przetłumaczony ruch. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak działają symbole wieloznaczne w docelowej znaczeń FQDN reguły aplikacji?

Jeśli skonfigurujesz ***.contoso.com**, umożliwia *anyvalue*.contoso.com, ale nie contoso.com (wierzchołek domeny). Jeśli chcesz zezwolić na apex domeny, należy jawnie skonfigurować go jako docelową nazwę FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Co oznacza *stan inicjowania obsługi administracyjnej: Niepowodzenie?*

Za każdym razem, gdy zostanie zastosowana zmiana konfiguracji, Zapora platformy Azure próbuje zaktualizować wszystkie jej podstawowe wystąpienia zaplecza. W rzadkich przypadkach jedno z tych wystąpień wewnętrznej bazy danych może nie zostać zaktualizowane przy nowej konfiguracji, a proces aktualizacji zostanie zatrzymany ze stanem inicjowania obsługi administracyjnej, który nie powiódł się. Zapora azure jest nadal działa, ale zastosowana konfiguracja może być w niespójnym stanie, gdzie niektóre wystąpienia mają poprzednią konfigurację, gdzie inne mają zaktualizowany zestaw reguł. Jeśli tak się stanie, spróbuj zaktualizować konfigurację jeszcze raz, aż operacja zakończy się pomyślnie, a zapora jest w stanie *pomyślnego* inicjowania obsługi administracyjnej.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>W jaki sposób Zapora platformy Azure obsługuje planowaną konserwację i nieplanowane awarie?
Zapora azure składa się z kilku węzłów wewnętrznej bazy danych w konfiguracji aktywny-aktywny.  W przypadku każdej planowanej konserwacji mamy logikę opróżniania połączeń, aby bezpiecznie aktualizować węzły.  Aktualizacje są planowane w godzinach pracy dla każdego z regionów platformy Azure, aby dodatkowo ograniczyć ryzyko zakłóceń.  W przypadku nieplanowanych problemów wystąpienia nowego węzła, aby zastąpić węzeł, który uległ awarii.  Łączność z nowym węzłem jest zazwyczaj przywracana w ciągu 10 sekund od czasu awarii.

## <a name="how-does-connection-draining-work"></a>Jak działa opróżnianie połączeń?

W przypadku każdej planowanej konserwacji logika opróżniania połączeń bezpiecznie aktualizuje węzły wewnętrznej bazy danych. Zapora azure czeka 90 sekund na zamknięcie istniejących połączeń. W razie potrzeby klienci mogą automatycznie przywrócić łączność z innym węzłem wewnętrznej bazy danych.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Czy istnieje limit znaków dla nazwy zapory?

Tak. Istnieje limit 50 znaków dla nazwy zapory.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Dlaczego Zapora platformy Azure potrzebuje rozmiaru podsieci /26?

Zapora azure musi aprowizować więcej wystąpień maszyny wirtualnej w miarę skalowania. Przestrzeń adresowa /26 zapewnia, że zapora ma wystarczającą liczbę adresów IP, aby pomieścić skalowanie.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Czy rozmiar podsieci zapory musi ulec zmianie w miarę skalowania usługi?

Nie. Zapora platformy Azure nie potrzebuje podsieci większej niż /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Jak zwiększyć przepustowość zapory?

Początkowa przepustowość zapory platformy Azure wynosi 2,5 - 3 Gb/s i jest skalowana do 30 Gb/s. Skaluje się w poziomie na podstawie użycia procesora CPU i przepływności. Skontaktuj się z pomocą techniczną, aby zwiększyć przepustowość zapory.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Jak długo trwa skalowanie w poziomie zapory platformy Azure?

Skalowanie w poziomie w ciągu pięciu do siedmiu minut w przypadku zapory platformy Azure. Skontaktuj się z pomocą techniczną, aby zwiększyć początkową przepustowość zapory, jeśli masz wybuchy, które wymagają szybszej skali automatycznej.

Podczas testowania automatycznej skali zapory należy wziąć pod uwagę następujące kwestie:

- Wydajność pojedynczego przepływu TCP jest ograniczona do 1,4 Gb/s. Dlatego test wydajności musi ustanowić wiele przepływów TCP.
- Narzędzia wydajności muszą stale ustanawiać nowe połączenia, aby można je było połączyć ze skalowanymi wystąpieniami zapory wewnętrznej bazy danych. Jeśli test ustanawia połączenia raz na początku, te będą łączyć się tylko z początkowych wystąpień wewnętrznej bazy danych. Mimo że zapora jest skalowana w górę, nie zobaczysz większej wydajności, ponieważ połączenia są skojarzone z początkowymi wystąpieniami.


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Czy Zapora platformy Azure domyślnie zezwala na dostęp do usługi Active Directory?

Nie. Zapora platformy Azure domyślnie blokuje dostęp do usługi Active Directory. Aby zezwolić na dostęp, skonfiguruj tag usługi AzureActiveDirectory. Aby uzyskać więcej informacji, zobacz [Tagi usługi Zapory platformy Azure](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Czy można wykluczyć numer FQDN lub adres IP z filtrowania opartego na analizie zagrożeń zapory platformy Azure?

Tak, można użyć programu Azure PowerShell, aby to zrobić:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Dlaczego ping TCP i podobne narzędzia mogą pomyślnie połączyć się z docelową siecią FQDN, nawet jeśli żadna reguła w zaporze platformy Azure nie zezwala na ten ruch?

Ping TCP nie łączy się z docelową siecią FQDN. Dzieje się tak, ponieważ przezroczysty serwer proxy usługi Azure Firewall nasłuchuje na porcie 80/443 dla ruchu wychodzącego. Ping TCP ustanawia połączenie z zaporą, która następnie porzuca pakiet i rejestruje połączenie. To zachowanie nie ma żadnego wpływu na bezpieczeństwo. Aby jednak uniknąć nieporozumień, badamy potencjalne zmiany w tym zachowaniu.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Czy istnieją limity liczby adresów IP obsługiwanych przez grupy adresów IP?

Tak. Aby uzyskać więcej informacji, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)