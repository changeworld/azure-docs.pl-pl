---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a63a12658bd0a4b4d018d51824af9814691a3cbf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "40183907"
---
### <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest w pełni stanowych zapory jako — usługa wbudowaną wysoką dostępność i skalowalność chmury bez ograniczeń. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Zaporę platformy Azure jest obecnie w publicznej wersji zapoznawczej.

### <a name="which-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Które funkcje są obsługiwane w wersji zapoznawczej zapory usługi Azure?  

* Zapora stanowa jako usługa
* Wbudowana wysoka dostępność z nieograniczoną skalowalnością w chmurze
* Filtrowanie według nazw FQDN 
* Reguły filtrowania ruchu sieciowego
* Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
* Centralnie tworzenie, wymuszanie i dziennika aplikacji i sieci zasad łączności różnych subskrypcji platformy Azure i sieci wirtualnych
* Pełna integracja z usługą Azure Monitor, rejestrowania i analizy 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Jak dołączyć Azure zapory publicznej wersji zapoznawczej?

Zaporę platformy Azure jest obecnie zarządzane publicznej wersji zapoznawczej, które możesz dołączyć za pomocą polecenia programu PowerShell element Register-AzureRmProviderFeature. To polecenie zostało wyjaśnione w dokumentacji zapory usługi Azure w publicznej wersji zapoznawczej.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Co to jest cennik dla zapory usługi Azure?

Zaporę platformy Azure jest kosztowne stałe i zmienne. Ceny są następujące i dalszych są obniżone o 50% w publicznej wersji zapoznawczej.

* Stała opłata: $1.25/firewall/hour
* Zmiennej opłaty: 0,03 USD/GB przetworzonych przez zapory (ruch przychodzący lub wychodzący)

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

Zapora publicznej wersji zapoznawczej witryny Azure obsługuje tylko wychodzącego filtrowania. Ogólna dostępność zapory usługi Azure planowana jest wstępnie ochrony dla ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład protokołu RDP, SSH lub FTP)  
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Jakie usługi rejestrowania i analizy są obsługiwane przez zaporę usługi Azure?

Zaporę platformy Azure jest zintegrowana z usługą Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do usługi Log Analytics, Azure Storage lub centrów zdarzeń. Mogą być analizowane w usłudze Log Analytics lub przez różnych narzędzi, takich jak program Excel i Power BI. Aby uzyskać więcej informacji, zobacz [samouczek: dzienniki zapory usługi Azure Monitor](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak zapory usługi Azure działa inaczej w istniejących usług, takich jak urządzenia WUS w portalu marketplace?

Zaporę platformy Azure to usługa Zapora podstawowa, która rozwiązywania przy użyciu określonych scenariuszy. Oczekuje się, konieczne może być zarówno urządzeń WUS innych firm i zapory usługi Azure. Współpracując z lepiej jest to główny priorytet.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między brama aplikacji zapory aplikacji internetowych i zapory usługi Azure?

Zapora aplikacji sieci Web (WAF) to funkcja usługi Application Gateway, która zapewnia scentralizowaną ochronę ruchu przychodzącego w aplikacji sieci web z typowe luki w zabezpieczeniach i luk w zabezpieczeniach. Zaporę platformy Azure zapewnia ochronę ruchu wychodzącego poziomie sieci dla wszystkich portów i protokołów i ochrony na poziomie aplikacji dla ruchu wychodzącego HTTP/s Ogólna dostępność zapory usługi Azure planowana jest wstępnie ochrony dla ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład protokołu RDP, SSH, FTP)

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Jaka jest różnica między grupami zabezpieczeń sieci (NSG) i zapory usługi Azure?

Usługa zapory usługi Azure uzupełniają funkcje grupy zabezpieczeń sieci. Razem zapewniają większe bezpieczeństwo sieci "ochronę w głębi". Sieciowe grupy zabezpieczeń zapewniają rozproszonych ruchu warstwy sieci, filtrowanie, aby ograniczyć ruch do zasobów w sieciach wirtualnych w ramach każdej subskrypcji. Zaporę platformy Azure jest pełni stanowe, scentralizowane sieci zapory jako usługa, co zapewnia ochronę na poziomie sieci i aplikacji w różnych subskrypcjach i sieciami wirtualnymi. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak skonfigurować zaporę usługi Azure za pomocą moje punkty końcowe usługi?

Bezpieczny dostęp do usług PaaS zalecamy punktów końcowych usługi. Można włączyć punktów końcowych usługi w podsieci zapory usługi Azure i je wyłączyć w sieciach wirtualnych połączonych szprychy. W ten sposób, możesz korzystać z funkcji — usługi punktu końcowego zabezpieczeń i centralnego rejestrowania dla całego ruchu.

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

### <a name="what-are-the-known-service-limits"></a>Jakie są limity znane usługi?

* Zapora systemu Azure ma zmienny limit 1000 TB na zapora na miesiąc. 
* Wystąpienia zapory platformy Azure, która działa w centralnej sieci wirtualnej ma wirtualnej sieci równorzędnej ograniczenia z maksymalnie 50 sieci wirtualne będące szprychami.  
* Zaporę platformy Azure nie działa z globalnej komunikacji równorzędnej, więc powinien mieć co najmniej jedno wdrożenie zapory na region.
* Zaporę platformy Azure obsługuje 10 k stosowanie reguł i reguł sieci usługi 10 tys.