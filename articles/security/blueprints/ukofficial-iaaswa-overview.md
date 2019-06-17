---
title: Zabezpieczenia platformy Azure i zgodności planu — IaaS trójwarstwowa aplikacja sieci Web dla UK-OFFICIAL
description: Zabezpieczenia platformy Azure i zgodności planu — IaaS trójwarstwowa aplikacja sieci Web dla UK-OFFICIAL
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 13ea2b68027c81bca7b43cef62cf7039aa0ea8dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609494"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Zabezpieczenia platformy Azure i zgodności planu — IaaS trójwarstwowa aplikacja sieci Web dla UK-OFFICIAL

## <a name="overview"></a>Omówienie

 Ten artykuł zawiera wskazówki i automatyzacji skryptów do świadczenia odpowiednich do obsługi wielu obciążeń sklasyfikowane jako OFFICIAL będzie przydatna w Zjednoczonym Królestwie architektura trójwarstwowa aplikacja sieci web Microsoft Azure.

 Przy użyciu infrastruktury jako kodu podejście zbiór [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szablony wdrażają środowiska pasującą do Wielkiej Brytanii National Cybernetycznymi zabezpieczeń Centrum (NCSC) 14 [zasad bezpieczeństwa w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) Centrum zabezpieczeń internetowych (CIS) i [środki kontroli bezpieczeństwa krytycznych](https://www.cisecurity.org/critical-controls.cfm).

 NCSC zaleca się ich zasad bezpieczeństwa w chmurze jest używany przez klientów, można obliczyć wartości właściwości zabezpieczeń usługi i lepiej zrozumieć podziału wspólnej odpowiedzialności klienta i dostawcy. Udostępniliśmy informacji w odniesieniu do każdego z tych zasad, które pomagają zrozumieć podziału obowiązków.

 Ta architektura i odpowiednie szablony usługi Azure Resource Manager są obsługiwane przez ten oficjalny dokument firmy Microsoft, [14 kontroli zabezpieczeń Cloud w Wielkiej Brytanii w chmurze przy użyciu platformy Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). W tym dokumencie katalogi jak usługi platformy Azure wyrównane z Wielkiej Brytanii NCSC 14 chmury zasad bezpieczeństwa, umożliwiając organizacjom szybkie możliwość zobowiązań ich zgodność przy użyciu usług w chmurze globalnie i w Wielkiej Brytanii na Microsoft Azure w chmurze.

 Ten szablon umożliwia wdrożenie infrastruktury dla obciążenia. Kod aplikacji i obsługa warstwą biznesową a oprogramowanie warstwy danych musi być zainstalowane i skonfigurowane. Dostępne są instrukcje wdrożenia są szczegółowo [tutaj](https://aka.ms/ukwebappblueprintrepo).

 Jeśli nie masz subskrypcji platformy Azure, a następnie możesz zarejestrować się szybko i łatwo - [Rozpoczynanie pracy z usługą Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

 Szablony platformy Azure zapewniają architektury aplikacji internetowych, w środowisku chmury platformy Azure, które obsługuje obciążenia oficjalne UK. Architektura zapewnia środowisko bezpieczną hybrydową, która rozszerza sieć lokalną do obciążeń platformy Azure umożliwiająca oparty na sieci web można mieć bezpieczny dostęp do firmowych użytkowników lub z Internetu.

![Trójwarstwowa aplikacja internetowa IaaS dla oficjalne UK referencyjny diagram architektury](images/ukofficial-iaaswa-architecture.png?raw=true "trójwarstwowa aplikacja internetowa IaaS dla oficjalne UK referencyjny diagram architektury")

 To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegóły architektura wdrożenia znajdują się w [architektura wdrożenia](#deployment-architecture) sekcji.

(((1) /16 sieci — operacyjne
- podsieci (3) prefiksie/24 - 3-warstwowej (sieci Web, Biz, dane)
- (1) wartość/27 DODAJE podsieć —
- podsieć (1) wartość/27 — podsieć bramy
- podsieć (1) rozmiarze/29 — podsieci bramy aplikacji
- Używa domyślnej (DNS platformy Azure)
- Komunikacja równorzędna włączone zarządzanie siecią wirtualną
- Grupy zabezpieczeń sieci (NSG) do zarządzania przepływem ruchu

(((1) prefiksie/24 sieci — zarządzanie
- (((1) wartość/27 podsieci
- (2) DODAJE DNS i (1) wpisy DNS platformy Azure
- Komunikacja równorzędna enabled operacyjnej sieci wirtualnej
- Grupy zabezpieczeń sieci (NSG) do zarządzania przepływem ruchu

(1) usługa application Gateway
- Zapora aplikacji sieci Web — włączone
- Tryb zapory aplikacji sieci Web — zapobieganie
- Zestaw reguł: OWASP 3.0
- Odbiornik HTTP na porcie 80
- Łączność/ruch prowadzące działalność za pomocą sieciowej grupy zabezpieczeń
- Punktu końcowego publicznego adresu IP zdefiniowane (Azure)

(1) sieci VPN — oparte na trasach tunel VPN protokołu IPSec lokacja 2
- Punktu końcowego publicznego adresu IP zdefiniowane (Azure)
- Łączność/ruch prowadzące działalność za pomocą sieciowej grupy zabezpieczeń
- (1) brama sieci lokalnej (punkt końcowy lokalnych)
- (1) bramy sieci platformy azure (punkt końcowy platformy Azure)

(9) maszyn wirtualnych — wszystkie maszyny wirtualne są wdrażane przy użyciu ustawienia DSC ochrony przed złośliwym oprogramowaniem IaaS platformy Azure

- (2) active Directory Domain Services kontrolerów domeny (system Windows Server 2012 R2)
  - (2) role serwera DNS - 1 dla maszyny Wirtualnej
  - (2) karty sieciowe połączone z siecią wirtualną Operational - 1 dla maszyny Wirtualnej
  - Jedne i drugie są przyłączone do domeny, zdefiniowane w szablonie
    - Domena utworzona w ramach wdrożenia


- (1) serwer Przesiadkowy (hostem bastionu) zarządzania maszyny Wirtualnej
  - 1 karta sieciowa w sieci wirtualnej zarządzania za pomocą publicznego adresu IP
    - Sieciowa grupa zabezpieczeń jest używany do ograniczania ruchu (We/Wy) do określonego źródła
  - Nie przyłączonych do domeny


- (2) maszyny wirtualne warstwy sieci web
  - (2) role serwera usług IIS - 1 dla maszyny Wirtualnej
  - (2) karty sieciowe połączone z siecią wirtualną Operational - 1 dla maszyny Wirtualnej
  - Nie przyłączonych do domeny


- (2) maszyny wirtualne warstwy biz
  - (2) karty sieciowe połączone z siecią wirtualną Operational - 1 dla maszyny Wirtualnej
  - Nie przyłączonych do domeny


- (2) maszyny wirtualne warstwy danych
  - (2) karty sieciowe połączone z siecią wirtualną Operational - 1 dla maszyny Wirtualnej
  - Nie przyłączonych do domeny

Zestawy dostępności
- (1) usługi active Directory kontrolera domeny maszyny Wirtualnej są ustawione — 2 maszyny wirtualne
- (1) maszyna wirtualna w warstwie sieci web są ustawione — 2 maszyny wirtualne
- (1) maszyna wirtualna w warstwie biz są ustawione — 2 maszyny wirtualne
- (1) maszyna wirtualna w warstwie danych są ustawione — 2 maszyny wirtualne

Moduł równoważenia obciążenia
- (1) moduł równoważenia obciążenia warstwy sieci web
- (1) moduł równoważenia obciążenia warstwy biz
- (1) moduł równoważenia obciążenia warstwy danych

Magazyn
- (14) konta magazynu łączna liczba
  - Zestaw dostępności kontrolera domeny usługi Active Directory
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) diagnostyczne konto magazynu lokalnie nadmiarowego (LRS) DODAJE zestawu dostępności
  - Maszyna wirtualna serwera Przesiadkowego zarządzania
    - (1) podstawowy magazyn lokalnie nadmiarowy (LRS) konta dla maszyny Wirtualnej serwera Przesiadkowego
    - (1) diagnostyczne konto magazynu lokalnie nadmiarowego (LRS) dla maszyny Wirtualnej serwera Przesiadkowego
  - Maszyny wirtualne warstwy sieci Web
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) diagnostyczne konto magazynu lokalnie nadmiarowego (LRS) dla zestawu dostępności warstwy sieci Web
  - Maszyny wirtualne warstwy Biz
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) diagnostyczne konto magazynu lokalnie nadmiarowego (LRS) dla zestawu dostępności warstwy Biz
  - Maszyny wirtualne warstwy danych
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) diagnostyczne konto magazynu lokalnie nadmiarowego (LRS) dla zestawu dostępności warstwy danych

### <a name="deployment-architecture"></a>Architektura wdrożenia:

**Sieci lokalnej**: Prywatna sieć lokalna zaimplementowana w organizacji.

**Sieć wirtualna produkcji**: Produkcyjne [sieci wirtualnej](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) hostuje aplikację i inne zasoby operacyjnych działających na platformie Azure. Każda sieć wirtualna może zawierać kilka podsieci, które są używane do izolowania i zarządzaniem ruchem sieciowym.

**Warstwy Web**: Obsługuje przychodzące żądania HTTP. Odpowiedzi są zwracane przez tę warstwę.

**Warstwa biznesowa**: Implementuje procesów biznesowych i innych logik funkcjonalności systemu.

**Bazy danych warstwy**: Udostępnia trwałego magazynu danych, za pomocą [programu SQL Server zawsze włączonych grup dostępności](https://msdn.microsoft.com/library/hh510230.aspx) wysokiej dostępności. Klienci mogą korzystać z [usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) jako alternatywa PaaS.

**Gateway**: [Bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) zapewnia łączność między routerami w sieci lokalnej i sieci wirtualnej w środowisku produkcyjnym.

**Internet, bramy i publiczny adres IP**: Bramy internetowej udostępnia usługi aplikacji użytkownikom za pośrednictwem Internetu. Ruch dostępu do tych usług jest zabezpieczone przy użyciu [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferty routingu warstwy 7 i możliwości ochroną umożliwiającymi zainstalowanie zapory aplikacji sieci web równoważenia obciążenia.

**Zarządzanie siecią wirtualną**: To [sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) zawiera zasoby, które implementują, zarządzanie i monitorowanie ich możliwości dla obciążeń działających w sieci wirtualnej w środowisku produkcyjnym.

**Serwer Przesiadkowy**: Nazywane również [hostem bastionu](https://en.wikipedia.org/wiki/Bastion_host), która jest bezpieczna maszyna wirtualna w sieci, w której administratorzy używają do połączonych z maszynami wirtualnymi w sieci wirtualnej w środowisku produkcyjnym. Rampa zawiera sieciową grupę zabezpieczeń, która zezwala na zdalny ruch z publicznych adresów IP znajdujących się na liście bezpiecznych adresów. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń. Zarządzanie zasobami w środowisku produkcyjnym jest za pośrednictwem protokołu RDP, za pomocą zabezpieczonej maszyny Wirtualnej serwera Przesiadkowego.

**Trasy definiowane przez użytkownika**: [Trasy definiowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) są używane do definiowania przepływu ruchu IP w obrębie sieci wirtualnych platformy Azure.

**Sieci wirtualne sieci równorzędne**: Produkcyjne i zarządzanie sieciami wirtualnymi są połączeni przy użyciu [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Te sieci wirtualne są nadal zarządzane jako oddzielne zasoby, ale są traktowane jako jedna do wszystkich celów związanych z łącznością dla tych maszyn wirtualnych. Te sieci komunikują się ze sobą bezpośrednio przy użyciu prywatnych adresów IP. Komunikacja równorzędna sieci wirtualnych podlega sieci wirtualne znajdujące się w tym samym regionie platformy Azure.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch sieciowy w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych.

**Active Directory Domain Services (AD DS)** : Ta architektura zapewnia dedykowany [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) wdrożenia.

**Rejestrowanie i inspekcja**: [Dziennik aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) przechwytywania operacji wykonywanych na zasoby w ramach subskrypcji, takie jak którzy zainicjował operację, gdy operacja Wystąpił stan operacji i wartości innych właściwości, które mogą pomóc badań Operacja. Dziennik aktywności platformy Azure jest usługą platformy Azure, która przechwytuje wszystkie akcje w ramach subskrypcji. Dzienniki można zarchiwizować lub wyeksportować, jeśli jest to wymagane.

**Sieć, monitorowania i zgłaszania alertów**: [Usługa Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) jest to usługa platformy przewiduje Przechwytywanie pakietów sieciowych, rejestrowanie przepływu, narzędzia topologii i Diagnostyka traffics sieci w ramach sieci wirtualnych.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Obciążenia serwera są zgrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do pomagają zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Ta konfiguracja wpiera, upewnij się, że podczas planowanego lub nieplanowanego zdarzenia konserwacji co najmniej jedna maszyna wirtualna zostanie dostępne i spełniać 99,95% umowy SLA platformy Azure.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja

**Monitorowanie**: [Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) to usługa platformy, która zapewnia jedno źródło monitorowania dziennika aktywności, metryki i dzienniki diagnostyczne Twoich zasobów platformy Azure. Usługa Azure Monitor można skonfigurować do wizualizacji, zapytania, trasy, archiwizować i działania dotyczące metryk i dzienników pochodzących z zasobów na platformie Azure. Zaleca się, że kontroli dostępu na podstawie zasobów jest wykorzystywany do zabezpieczenia dziennika inspekcji, aby mieć pewność, że użytkownicy nie mają możliwość modyfikowania dzienniki.

**Dzienniki aktywności**: Konfigurowanie [dzienników aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniające wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji.

**Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez zasób. Dzienniki te mogą obejmować dzienniki systemu zdarzeń Windows, obiektów blob, tabel i Dzienniki kolejek.

**Dzienniki zapory**: Usługa Application Gateway zapewnia pełne dzienniki dostępu i diagnostyki. Dzienniki zapory są dostępne dla zasobów usługi Application Gateway z włączoną zaporą aplikacji sieci Web.

**Archiwizowanie dziennika**: Przechowywanie danych dziennika można skonfigurować do zapisu do konta scentralizowany magazyn platformy Azure w celu archiwizacji i okres przechowywania zdefiniowany. Dzienniki mogą być przetwarzane przy użyciu dzienników usługi Azure Monitor lub systemów SIEM innych firm.

### <a name="identity"></a>Tożsamość

**Active Directory Domain Services**: Ta architektura zapewnia wdrożenia usługi Active Directory Domain Services na platformie Azure. Aby uzyskać szczegółowe zalecenia dotyczące implementowania usługi Active Directory na platformie Azure, zobacz następujące artykuły:

[Rozszerzanie usługi Active Directory Domain Services (AD DS) na platformę Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Wytyczne dotyczące wdrażania Active Directory systemu Windows Server na maszynach wirtualnych Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integracja usługi Active Directory**: Jako alternatywę do dedykowanych architektury usług AD DS, klienci mogą chcieć skorzystać [usługi Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) integracji lub [usługi Active Directory na platformie Azure przyłączone do lasu lokalnego](https://docs.microsoft.com/azure/guidance/guidance-ra-identity).

### <a name="security"></a>Bezpieczeństwo

**Zarządzanie zabezpieczeniami**: Ten plan umożliwia administratorom nawiązywanie zarządzania w sieci wirtualnej i serwera Przesiadkowego, przy użyciu protokołu RDP z zaufanego źródła. Ruch sieciowy do zarządzania siecią wirtualną jest kontrolowany przy użyciu sieciowych grup zabezpieczeń. Dostęp do portu 3389 jest ograniczony do ruch z zaufanych zakresu adresów IP, który mogą uzyskiwać dostęp do podsieci zawierającej serwer Przesiadkowy.

Klienci mogą również wziąć pod uwagę przy użyciu [modelu administracyjnego zwiększone zabezpieczenia](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) do bezpiecznego środowiska, podczas nawiązywania połączenia z zarządzania, siecią wirtualną i serwera Przesiadkowego. Zalecane jest, aby zwiększyć bezpieczeństwo aby klienci używali [stacji roboczej z dostępem uprzywilejowanym](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) i RDGateway konfiguracji. Korzystanie z wirtualnych urządzeń sieciowych i publicznych/prywatnych sieci obwodowych oferują dodatkowe ulepszenia zabezpieczeń.

**Zabezpieczanie sieci**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) są zalecane dla każdej podsieci zapewnić drugi poziom ochrony przed ruch przychodzący nieprawidłowo skonfigurowanych lub wyłączonych bramy z pominięciem. Przykład — [szablonu usługi Resource Manager do wdrażania sieciowej grupy zabezpieczeń](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Zabezpieczanie publiczne punkty końcowe**: Bramy internetowej udostępnia usługi aplikacji użytkownikom za pośrednictwem Internetu. Ruch dostępu do tych usług jest zabezpieczone przy użyciu [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), zapewniającą zarządzania protokołu zapory aplikacji sieci Web i protokołu HTTPS.

**Zakresy adresów IP**: Zakresy adresów IP w architekturze są sugerowane zakresów. Należy wziąć pod uwagę swoje własne środowisko i używać odpowiednie zakresy doradza się klientów.

**Łączność hybrydowa**: Obciążenia oparte na chmurze są połączone z lokalnym centrum danych za pośrednictwem protokołu IPSEC sieci VPN za pomocą usługi Azure VPN Gateway. Klienci należy upewnić się, czy używają odpowiedniej bramy sieci VPN do połączenia z platformą Azure. Przykład — [szablonu Menedżera zasobów bramy sieci VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Klienci, którzy hostują na dużą skalę, obciążeń o znaczeniu krytycznym, za pomocą wymagających danych big Data mogą chcieć należy rozważyć użycie architektury sieci hybrydowej [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) dla połączenia sieci prywatnej z usługą Microsoft cloud services.

**Separacji**: Ta architektura referencyjna oddziela sieci wirtualne dla operacji zarządzania i operacji biznesowych. Oddzielne sieci wirtualnych i podsieci umożliwia zarządzanie ruchem, ograniczenia ruchu przychodzącego i wychodzącego ruchu sieciowego, w tym za pomocą sieciowych grup zabezpieczeń między segmentami sieci po [usługa Microsoft cloud services i network security](https://docs.microsoft.com/azure/best-practices-network-security) najlepszych rozwiązań.

**Zarządzanie zasobami**: Zasoby platformy Azure, takie jak maszyny wirtualne, sieci wirtualne i usługi równoważenia obciążenia są zarządzane przez grupując je razem w [grup zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Role Based Access Control zasobów można przypisać do każdej grupy zasobów, aby ograniczyć dostęp tylko uprawnionym użytkownikom.

**Ograniczenia kontroli dostępu**: Użyj [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) do zarządzania zasobami w swojej aplikacji za pomocą [ról niestandardowych](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC można ograniczyć możliwość wykonywania operacji, które rola DevOps może wykonywać w każdej warstwie. Podczas nadawania uprawnień użyj [zasadę najmniejszych uprawnień](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Rejestruj wszystkie operacje administracyjne i przeprowadzaj regularne inspekcje, aby mieć pewność, że wszelkie zmiany konfiguracji są planowane.

**Dostęp do Internetu**: Ta architektura referencyjna korzysta z [usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) jako bramy i obciążenia równoważenia połączonego z Internetem. Niektórzy klienci mogą również wziąć pod uwagę przy użyciu dodatkowych warstw zabezpieczeń jako alternatywę dla sieci wirtualnych urządzeń sieciowych innych firm [usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Usługa Azure Security Center**: [Usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) oferuje centralny widok stanu zabezpieczeń zasobów w subskrypcji i zapewnia zaleceń, które pomagają zapobiegać zaatakowanych zasobów. Może również służyć można włączyć bardziej szczegółowe zasady. Na przykład zasady można stosować do określonych grup zasobów, co umożliwia przedsiębiorstwa dostosować jego poziom ryzyka. Zaleca się, że klienci włączą usługi Azure Security Center w ramach swojej subskrypcji platformy Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentacja usługi NCSC Cloud Security zasad zgodności

Usługa komercyjnych korony (Agencja, który pracuje, aby poprawić działanie komercyjne i zamówienia przez Rząd) odnowić klasyfikacji Microsoft przedsiębiorstwa w zakresie usług cloud services G-Cloud w wersji 6, obejmujące wszystkie swoje oferty na poziomie OFFICIAL. Można znaleźć szczegółowe informacje o platformie Azure i G-Cloud w [podsumowanie oceny zabezpieczeń usługi Azure UK G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Ten plan zgodnie z zasadami zabezpieczeń 14 chmury, które są udokumentowane w NCSC [zasad bezpieczeństwa w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) aby zapewnić środowisko, które obsługuje obciążenia sklasyfikowane jako UK-OFFICIAL.

[Macierzy odpowiedzialności klienta](https://aka.ms/ukofficial-crm) (skoroszyt programu Excel) zawiera listę wszystkich zasad bezpieczeństwa w chmurze 14 i macierzy wskazuje, dla każdej zasady (lub zasadą podsekcja), czy odpowiedzialność za implementację zasady Microsoft, klienta lub współdzielonej między nimi.

[Macierzy implementacji zasady](https://aka.ms/ukofficial-iaaswa-pim) oznacza wszystkie zasady zabezpieczeń chmury 14 i macierzy, list (skoroszyt programu Excel), dla każdej zasady (lub zasadą podsekcja), wyznaczony odpowiedzialność klientów w klienta Obowiązki macierzy, 1) Jeśli automatyzacji planu implementuje zasady i (2) opis sposobu implementacji wyrównuje zasady spełnienia następujących wymagań.

Ponadto Cloud Security Alliance (CSA) opublikowane macierzy kontroli chmury, aby wspierać klientów w wersji ewaluacyjnej programu dostawców rozwiązań w chmurze i zidentyfikować pytania, na które należy odpowiedzieć na przed przejściem do usług w chmurze. W odpowiedzi, Microsoft Azure odpowiedzi na kwestionariusz inicjatywy oceny Consensus CSA ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), która opisuje, jak Microsoft adresy sugerowane zasad.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Istnieją dwie metody, których użytkownicy wdrożenia może użyć do wdrożenia tego planu usługi automation. Pierwsza metoda używa skryptu programu PowerShell, natomiast druga metoda korzysta z portalu Azure, aby wdrożyć tę architekturę referencyjną. Dostępne są instrukcje wdrożenia są szczegółowo [tutaj](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
