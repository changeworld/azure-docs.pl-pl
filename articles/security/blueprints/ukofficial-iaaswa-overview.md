---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — trzy warstwy wielowarstwowa aplikacja sieci Web IaaS dla URZĘDNIKów BRYTYJSKIch
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — trzy warstwy wielowarstwowa aplikacja sieci Web IaaS dla URZĘDNIKów BRYTYJSKIch
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 2fa9db20554df813e5da94e2bbea122ac6cc9b60
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946542"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure — trzy warstwy wielowarstwowa aplikacja sieci Web IaaS dla URZĘDNIKów BRYTYJSKIch

## <a name="overview"></a>Przegląd

 Ten artykuł zawiera wskazówki i skrypty automatyzacji umożliwiające dostarczenie Microsoft Azure architektury opartej na sieci Web trójwarstwowej, która jest odpowiednia do obsługi wielu obciążeń sklasyfikowanych jako oficjalne w Zjednoczonym Królestwie.

 Przy użyciu infrastruktury jako metody kodu zestaw [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szablonów wdraża środowisko, które jest zgodne z krajowym centrum zabezpieczeń CYBERNETYCZNYMI (NCSC) 14 [zasady zabezpieczeń chmury](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) oraz centrum zabezpieczeń internetowych (CIS). [ Krytyczne kontrole zabezpieczeń](https://www.cisecurity.org/critical-controls.cfm).

 NCSC zalecać klientom korzystanie z zasad zabezpieczeń w chmurze w celu ocenienia właściwości zabezpieczeń usługi i poznania działu odpowiedzialności między klientem i dostawcą. Podano informacje dotyczące każdej z tych zasad, aby ułatwić zrozumienie podziału obowiązków.

 Ta architektura i odpowiednie szablony Azure Resource Manager są obsługiwane przez oficjalny dokument firmy Microsoft, a [14 kontrolki zabezpieczeń w chmurze dla chmury brytyjskiej przy użyciu Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ten katalog dokumentów przedstawia sposób, w jaki usługi platformy Azure są wyrównane do NCSC 14 zasad zabezpieczeń w chmurze, dzięki czemu organizacje mogą szybko śledzić swoją zdolność do zaspokajania swoich obowiązków związanych ze zgodnością przy użyciu usług w chmurze globalnie i w Wielkiej Brytanii na Microsoft Azure chmury.

 Ten szablon służy do wdrażania infrastruktury dla obciążenia. Należy zainstalować i skonfigurować kod aplikacji obsługujący warstwę biznesową i oprogramowanie warstwy danych. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://aka.ms/ukwebappblueprintrepo).

 Jeśli nie masz subskrypcji platformy Azure, możesz szybko i łatwo utworzyć konto — [Rozpocznij pracę z platformą Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

 Szablony platformy Azure dostarczają trzy-warstwową architekturę aplikacji sieci Web w środowisku chmury platformy Azure, które obsługuje oficjalne obciążenia w Wielkiej Brytanii. Architektura zapewnia bezpieczne środowisko hybrydowe, które rozszerza sieć lokalną na platformę Azure, umożliwiając bezpieczny dostęp do obciążeń opartych na sieci Web przez użytkowników w firmie lub z Internetu.

![3-warstwowa aplikacja sieci Web IaaS dla brytyjskiego publicznego diagramu architektury referencyjnej](images/ukofficial-iaaswa-architecture.png?raw=true "3-warstwowa aplikacja sieci Web IaaS dla brytyjskiego publicznego diagramu architektury referencyjnej")

 To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

(1)/16 Virtual Network-operacyjna Sieć wirtualna
- (3)/24 podsieci — 3-warstwowa (Web, BIZ, dane)
- (1)/27 podsieci — dodaje
- (1)/27 podsieć bramy podsieci
- (1)/29 podsieć Application Gateway podsieci
- Używa domyślnej usługi DNS (udostępnionej przez platformę Azure)
- Komunikacja równorzędna włączona z siecią wirtualną zarządzania
- Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) do zarządzania przepływem ruchu

(1)/24 Virtual Network zarządzania siecią wirtualną
- (1)/27 podsieci
- Użycie (2) powoduje dodanie wpisów Azure DNS DNS i (1)
- Komunikacja równorzędna włączona z operacyjną siecią wirtualną
- Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) do zarządzania przepływem ruchu

(1) Application Gateway
- WAF — włączono
- WAF, tryb zapobiegania
- Zestaw reguł: OWASP 3.0
- Odbiornik HTTP na porcie 80
- Łączność/ruch regulowany za pośrednictwem sieciowej grupy zabezpieczeń
- Zdefiniowano punkt końcowy publicznego adresu IP (Azure)

(1) sieć VPN-oparta na trasach — tunel VPN protokołu IPSec w lokacji
- Zdefiniowano punkt końcowy publicznego adresu IP (Azure)
- Łączność/ruch regulowany za pośrednictwem sieciowej grupy zabezpieczeń
- (1) Brama sieci lokalnej (lokalny punkt końcowy)
- (1) Brama sieci platformy Azure (punkt końcowy platformy Azure)

(9) Virtual Machines — wszystkie maszyny wirtualne są wdrażane przy użyciu ustawień DSC usługi Azure IaaS chroniących przed złośliwym kodem

- (2) Active Directory Domain Services kontrolery domeny (Windows Server 2012 R2)
  - (2) role serwera DNS — 1 na maszynę wirtualną
  - (2) karty sieciowe połączone z operacyjną siecią wirtualną — 1 na maszynę wirtualną
  - Oba są przyłączone do domeny w domenie zdefiniowanej w szablonie
    - Domena utworzona jako część wdrożenia


- (1) maszyna wirtualna zarządzania serwera przesiadkowego (bastionu hostem)
  - 1 karta sieciowa w sieci wirtualnej zarządzania z publicznym adresem IP
    - SIECIOWEJ grupy zabezpieczeń służy do ograniczania ruchu (WE/wychodzącym) do określonych źródeł
  - Nie przyłączone do domeny


- (2) maszyny wirtualne warstwy sieci Web
  - (2) role serwera IIS — 1 na maszynę wirtualną
  - (2) karty sieciowe połączone z operacyjną siecią wirtualną — 1 na maszynę wirtualną
  - Nie przyłączone do domeny


- (2) maszyny wirtualne warstwy BIZ
  - (2) karty sieciowe połączone z operacyjną siecią wirtualną — 1 na maszynę wirtualną
  - Nie przyłączone do domeny


- (2) maszyny wirtualne warstwy danych
  - (2) karty sieciowe połączone z operacyjną siecią wirtualną — 1 na maszynę wirtualną
  - Nie przyłączone do domeny

Zestawy dostępności
- (1) domena usługi Active Directory kontroler maszyny wirtualnej z zestawem maszyn wirtualnych 2
- (1) zestaw VM warstwy sieci Web — 2 maszyny wirtualne
- (1) Konfiguracja maszyny wirtualnej w warstwie BIZ — 2 maszyny wirtualne
- (1) Konfiguracja maszyny wirtualnej warstwy danych — 2 maszyny wirtualne

Moduł równoważenia obciążenia
- (1) Load Balancer warstwy sieci Web
- (1) BIZ Load Balancer warstwy
- (1) Load Balancer warstwy danych

Magazyn
- (14) Łączna liczba kont magazynu
  - Zestaw dostępności kontrolera domena usługi Active Directory
    - (2) konta podstawowego magazynu lokalnie nadmiarowego (LRS) — 1 dla każdej maszyny wirtualnej  
    - (1) konto usługi diagnostical nadmiarowe (LRS) do dodania zestawu dostępności
  - Maszyna wirtualna zarządzania serwera przesiadkowego
    - (1) podstawowe konto magazynu lokalnie nadmiarowego (LRS) dla maszyny wirtualnej serwera przesiadkowego
    - (1) LRS konto magazynu na potrzeby diagnostyki lokalnie nadmiarowego () dla maszyny wirtualnej serwera przesiadkowego
  - Maszyny wirtualne warstwy sieci Web
    - (2) konta podstawowego magazynu lokalnie nadmiarowego (LRS) — 1 dla każdej maszyny wirtualnej  
    - (1) konto usługi diagnosticly nadmiarowe (LRS) dla zestawu dostępności warstwy sieci Web
  - Maszyny wirtualne warstwy BIZ
    - (2) konta podstawowego magazynu lokalnie nadmiarowego (LRS) — 1 dla każdej maszyny wirtualnej  
    - (1) LRS konto magazynu na potrzeby diagnostyki lokalnie nadmiarowego () dla zestawu dostępności warstwy BIZ
  - Maszyny wirtualne warstwy danych
    - (2) konta podstawowego magazynu lokalnie nadmiarowego (LRS) — 1 dla każdej maszyny wirtualnej  
    - (1) na potrzeby diagnostyki konta magazynu lokalnie nadmiarowego (LRS) dla zestawu dostępności warstwy danych

### <a name="deployment-architecture"></a>Architektura wdrożenia:

**Sieć lokalna**: Prywatna sieć lokalna zaimplementowana w organizacji.

**Sieć wirtualna w środowisku produkcyjnym**: W środowisku produkcyjnym [Sieć wirtualna](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) jest obsługiwana aplikacja i inne zasoby operacyjne działające na platformie Azure. Każda sieć wirtualna może zawierać kilka podsieci, które są używane do izolowania ruchu sieciowego i zarządzania nim.

**Warstwa sieci Web**: Obsługuje przychodzące żądania HTTP. Odpowiedzi są zwracane przez tę warstwę.

**Warstwa biznesowa**: Implementuje procesy biznesowe i inne logiki funkcjonalne dla systemu.

**Warstwa bazy danych**: Zapewnia trwały magazyn danych przy użyciu [zawsze dostępnych grup dostępności usługi SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) , aby zapewnić wysoką dostępność. Klienci mogą używać [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) jako alternatywy PaaS.

**Brama**: [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) zapewnia łączność między routerami w sieci lokalnej i w środowisku produkcyjnym.

**Brama internetowa i publiczny adres IP**: Brama internetowa udostępnia użytkownikom usługi aplikacji za pomocą Internetu. Ruch uzyskujący dostęp do tych usług jest zabezpieczony przy użyciu [Application Gateway](../../application-gateway/overview.md) oferujący możliwości routingu i równoważenia obciążenia warstwy 7 z ochroną zapory aplikacji sieci Web (WAF).

**Sieć wirtualna zarządzania**: Ta [Sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) zawiera zasoby implementujące funkcje zarządzania i monitorowania dla obciążeń uruchomionych w produkcyjnej sieci wirtualnej.

**Serwera przesiadkowego**: Nazywana również [hostem bastionu](https://en.wikipedia.org/wiki/Bastion_host), który jest bezpieczną maszyną wirtualną w sieci, której administratorzy używają do łączenia się z maszynami wirtualnymi w środowisku produkcyjnym. Rampa zawiera sieciową grupę zabezpieczeń, która zezwala na zdalny ruch z publicznych adresów IP znajdujących się na liście bezpiecznych adresów. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupy zabezpieczeń. Zarządzanie zasobami produkcyjnymi odbywa się za pośrednictwem protokołu RDP przy użyciu zabezpieczonej maszyny wirtualnej serwera przesiadkowego.

**Trasy zdefiniowane przez użytkownika**: [Trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) są używane do definiowania przepływu ruchu IP w ramach usługi Azure sieci wirtualnych.

**Sieci wirtualnych sieciowa komunikacji równorzędnej**: Sieci wirtualnych produkcyjne i zarządzania są połączone za pomocą [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Te sieci wirtualnych są nadal zarządzane jako oddzielne zasoby, ale są wyświetlane jako jeden dla wszystkich celów związanych z łącznością dla tych maszyn wirtualnych. Te sieci komunikują się ze sobą bezpośrednio przy użyciu prywatnych adresów IP. Wirtualne sieci równorzędne podlegają sieci wirtualnych w tym samym regionie świadczenia usługi Azure.

**Sieciowe grupy zabezpieczeń**: [Sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy Access Control, które zezwalają na ruch lub go odmawiają w sieci wirtualnej. Sieciowych grup zabezpieczeń może służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych.

**Active Directory Domain Services (AD DS)** : Ta architektura zapewnia dedykowane wdrożenie [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) .

**Rejestrowanie i inspekcja**: [Dziennik aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md) przechwytuje operacje wykonywane na zasobach w ramach subskrypcji, takich jak osoba, która zainicjowała operację, po wystąpieniu operacji, stanie operacji i wartości innych właściwości, które mogą pomóc w zbadaniu operacji. Dziennik aktywności platformy Azure to usługa platformy Azure, która przechwytuje wszystkie akcje w ramach subskrypcji. Dzienniki można archiwizować lub eksportować w razie potrzeby.

**Monitorowanie sieci i**zgłaszanie alertów: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) to usługa platformy zapewnia przechwytywanie pakietów sieciowych, rejestrowanie przepływów, narzędzia topologii i diagnostykę dla ruchu sieciowego w ramach sieci wirtualnych.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Obciążenia serwera są pogrupowane w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , aby zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Ta konfiguracja pomaga zapewnić, że podczas planowanego lub nieplanowanego zdarzenia konserwacji zostanie udostępniona co najmniej jedna maszyna wirtualna, która będzie spełniać warunki umowy SLA na 99,95%.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja

**Monitorowanie**: [Azure monitor](../../azure-monitor/overview.md) to usługa platformy, która zapewnia jedno źródło do monitorowania dziennika aktywności, metryk i dzienników diagnostycznych wszystkich zasobów platformy Azure. Azure Monitor można skonfigurować w taki sposób, aby wizualizować, wysyłać zapytania, kierować, archiwizować i podejmować działania dotyczące metryk i dzienników pochodzących z zasobów na platformie Azure. Zaleca się, aby do zabezpieczenia dziennika inspekcji był używany Access Control oparty na zasobach w celu zapewnienia, że użytkownicy nie mają możliwości modyfikowania dzienników.

**Dzienniki aktywności**: Skonfiguruj [dzienniki aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md) , aby uzyskać wgląd w operacje wykonywane względem zasobów w ramach subskrypcji.

**Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) to wszystkie dzienniki emitowane przez zasób. Dzienniki te mogą obejmować Dzienniki systemu Windows, obiekty blob, tabele i dzienniki kolejki.

**Dzienniki zapory**: Application Gateway zapewnia pełną diagnostykę i dzienniki dostępu. Dzienniki zapory są dostępne dla zasobów usługi Application Gateway z włączoną zaporą aplikacji sieci Web.

**Archiwizowanie dzienników**: Magazyn danych dziennika można skonfigurować do zapisu na scentralizowanym koncie usługi Azure Storage w celu archiwizacji i zdefiniowanego okresu przechowywania. Dzienniki mogą być przetwarzane przy użyciu dzienników Azure Monitor lub systemów SIEM innych firm.

### <a name="identity"></a>Tożsamość

**Active Directory Domain Services**: Ta architektura zapewnia wdrożenie Active Directory Domain Services na platformie Azure. Aby uzyskać szczegółowe zalecenia dotyczące implementowania usługi Active Directory na platformie Azure, zobacz następujące artykuły:

[Rozszerzanie Active Directory Domain Services (AD DS) na platformę Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

[Wskazówki dotyczące wdrażania Active Directory systemu Windows Server na platformie Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integracja Active Directory**: Jako alternatywę dla dedykowanej architektury AD DS klienci mogą chcieć użyć integracji [Azure Active Directory](/azure/architecture/reference-architectures/identity.md) lub [Active Directory na platformie Azure przyłączonej do lasu lokalnego](/azure/architecture/reference-architectures/identity.md).

### <a name="security"></a>Bezpieczeństwo

**Zabezpieczenia zarządzania**: Ten plan umożliwia administratorom łączenie się z siecią wirtualną zarządzania i serwera przesiadkowego przy użyciu protokołu RDP z zaufanego źródła. Ruch sieciowy dla sieci wirtualnej zarządzania jest kontrolowany przy użyciu sieciowych grup zabezpieczeń. Dostęp do portu 3389 jest ograniczony do ruchu z zaufanego zakresu adresów IP, który może uzyskiwać dostęp do podsieci zawierającej serwera przesiadkowego.

Klienci mogą również rozważyć użycie [rozszerzonego modelu administracyjnego zabezpieczeń](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) , aby zabezpieczyć środowisko podczas łączenia się z siecią wirtualną zarządzania i serwera przesiadkowego. Zalecane jest, aby dla klientów z podwyższonymi zabezpieczeniami używać [stacji roboczej dostępu uprzywilejowanego](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) i konfiguracji RDGateway. Korzystanie z wirtualnych urządzeń sieciowych i publicznych/prywatnych sieci obwodowych oferują dodatkowe ulepszenia zabezpieczeń.

**Zabezpieczanie sieci**: [Sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Sieciowych grup zabezpieczeń) są zalecane dla każdej podsieci, aby zapewnić drugi poziom ochrony przed ruchem przychodzącym, który pomija niepoprawnie skonfigurowaną lub wyłączoną bramę. Przykładowy [szablon Menedżer zasobów do wdrażania sieciowej grupy zabezpieczeń](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Zabezpieczanie publicznych punktów końcowych**: Brama internetowa udostępnia użytkownikom usługi aplikacji za pomocą Internetu. Ruch uzyskujący dostęp do tych usług jest zabezpieczony za pomocą [Application Gateway](../../application-gateway/overview.md), który zapewnia zaporę aplikacji sieci Web i zarządzanie protokołami https.

**Zakresy adresów IP**: Zakresy adresów IP w architekturze są sugerowanymi zakresami. Klienci powinni wziąć pod uwagę swoje własne środowisko i korzystać z odpowiednich zakresów.

**Łączność hybrydowa**: Obciążenia oparte na chmurze są połączone z lokalnym centrum danych za pośrednictwem protokołu IPSEC w sieci VPN przy użyciu usługi Azure VPN Gateway. Klienci powinni upewnić się, że używają odpowiednich VPN Gateway do łączenia się z platformą Azure. Przykład [VPN Gateway szablon Menedżer zasobów](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Klienci korzystający z dużych obciążeń o dużej skali z wymaganiami dotyczącymi danych Big Data mogą chcieć rozważyć architekturę sieci hybrydowej wykorzystującą [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute.md) do łączności z siecią prywatną z usługami w chmurze firmy Microsoft.

**Rozdzielenie obaw**: Ta architektura referencyjna oddziela sieci wirtualnych na potrzeby operacji zarządzania i operacji działania biznesowego. Osobne sieci wirtualnych i podsieci umożliwiają zarządzanie ruchem, w tym ograniczenia ruchu przychodzącego i wychodzącego, przy użyciu sieciowych grup zabezpieczeń między segmentami sieci w ramach [usług w chmurze firmy Microsoft i](/azure/architecture/vdc/networking-virtual-datacenter.md) najlepszych rozwiązań w zakresie zabezpieczeń sieci.

**Zarządzanie zasobami**: Zasoby platformy Azure, takie jak maszyny wirtualne, sieci wirtualnych i moduły równoważenia obciążenia, są zarządzane przez zgrupowanie ich razem z [grupami zasobów platformy Azure](../../azure-resource-manager/resource-group-overview.md). Role Access Control oparte na zasobach można następnie przypisać do poszczególnych grup zasobów, aby ograniczyć dostęp tylko do autoryzowanych użytkowników.

**Ograniczenia Access Control**: Użyj [Access Control opartej na rolach](../../role-based-access-control/role-assignments-portal.md) (RBAC) do zarządzania zasobami w aplikacji przy użyciu [ról niestandardowych](../../role-based-access-control/custom-roles.md) RBAC może służyć do ograniczenia operacji, które DevOps mogą wykonywać w poszczególnych warstwach. Podczas udzielania uprawnień należy stosować [zasadę](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1)najniższych uprawnień. Rejestruj wszystkie operacje administracyjne i przeprowadzaj regularne inspekcje, aby mieć pewność, że wszelkie zmiany konfiguracji są planowane.

**Dostęp do Internetu**: Ta architektura referencyjna korzysta z [usługi Azure Application Gateway](../../application-gateway/overview.md) jako bramy połączonej z Internetem i modułu równoważenia obciążenia. Niektórzy klienci mogą również rozważyć użycie wirtualnych urządzeń sieciowych innych firm, aby uzyskać dodatkowe warstwy zabezpieczeń sieciowych jako alternatywę dla [Application Gateway platformy Azure](../../application-gateway/overview.md).

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) stanowi centralny widok stanu zabezpieczeń zasobów w subskrypcji i zawiera zalecenia, które pomagają zapobiegać zagrożonym zasobom. Można go również użyć, aby włączyć bardziej szczegółowe zasady. Na przykład zasady można zastosować do określonych grup zasobów, co pozwala przedsiębiorstwom dostosować stan do ryzyka. Zaleca się, aby klienci mogli włączyć Azure Security Center w ramach swojej subskrypcji platformy Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentacja dotycząca zgodności z zasadami zabezpieczeń w chmurze NCSC

Komercyjna usługa dla korony (Agencja, która pracuje nad ulepszaniem działalności komercyjnej i z zakresu zakupów przez rząd), odnowić klasyfikację usług Cloud Services w zasięgu firmy Microsoft do G-Cloud w wersji 6, obejmujących wszystkie jej oferty na poziomie Urzędowym. Szczegóły dotyczące platformy Azure i usługi G-Cloud można znaleźć w [podsumowaniu usługi Azure UK Cloud Security](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Ten plan jest zgodny z 14 zasadami zabezpieczeń chmury, które są udokumentowane w [zasadach zabezpieczeń w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) NCSC, aby pomóc w zapewnieniu środowiska, które obsługuje obciążenia sklasyfikowane jako Zjednoczone Królestwo.

[Macierz odpowiedzialności klienta](https://aka.ms/ukofficial-crm) (skoroszyt programu Excel) zawiera listę wszystkich 14 zasad zabezpieczeń w chmurze, a także dla każdej zasady (lub podczęściowej), czy zasady implementacji są odpowiedzialne za firmę Microsoft, klienta lub współdzielona między nimi.

[Macierz implementacji reguły](https://aka.ms/ukofficial-iaaswa-pim) (skoroszyt programu Excel) zawiera listę wszystkich 14 zasad zabezpieczeń w chmurze, a także dla każdej zasady (lub podczęściu), która jest oznaczona jako odpowiedzialność klienta w macierzy odpowiedzialności klienta 1), jeśli Automatyzacja strategii implementuje zasadę i 2) opis sposobu, w jaki wdrożenie jest wyrównane z wymaganymi zasadami.

Ponadto Cloud Security Alliance (CSA) opublikował macierz kontroli chmury w celu obsługi klientów w ocenie dostawców chmury oraz do identyfikowania pytań, na które należy odpowiedzieć przed przejściem do usług w chmurze. W odpowiedzi Microsoft Azure odpowiedzieć na kwestionariusz z inicjatywy CSA z oceną konsensusu ([csa CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), w którym opisano sposób, w jaki firma Microsoft rozwiązuje sugerowane zasady.

## <a name="deploy-the-solution"></a>Wdróż rozwiązanie

Istnieją dwie metody, które użytkownicy wdrożenia mogą wykorzystać do wdrożenia tej automatyzacji. W pierwszej metodzie jest używany skrypt programu PowerShell, natomiast druga metoda wykorzystuje Azure Portal do wdrożenia architektury referencyjnej. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
