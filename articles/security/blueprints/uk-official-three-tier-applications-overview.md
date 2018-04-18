---
title: Zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web trójwarstwowa UK oficjalne
description: Zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web trójwarstwowa UK oficjalne
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: bb0a667c28e4ed0be3e67a7d89f10903be2c9d2a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---uk-offical-three-tier-web-applications-automation"></a>Zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web oficjalnego UK trójwarstwowej

## <a name="overview"></a>Przegląd

 Ten artykuł zawiera wskazówki i automatyzacji skrypty w celu dostarczenia architektury trójwarstwowej aplikacja sieci web Microsoft Azure odpowiednie do obsługi wielu obciążeń sklasyfikowane jako URZĘDNIKA Zjednoczone Królestwo.

 Przy użyciu infrastruktury jako kod podejścia zbiór [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szablony wdrożyć środowisko zawierające wyrównuje na Brytyjski National przez zabezpieczeń Centrum (NCSC) 14 [zasad zabezpieczeń chmury](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) Centrum zabezpieczeń Internetu (ci) i [formanty krytycznych](https://www.cisecurity.org/critical-controls.cfm).

 NCSC zaleca się ich zasad zabezpieczeń chmury można używać przez klientów, można oszacować właściwości zabezpieczeń usługi, a także aby lepiej poznać podział obowiązków między klientem a dostawcy. Przygotowaliśmy informacji w odniesieniu do każdego z tych zasad, aby lepiej zrozumieć podział obowiązków.

 Taka architektura i szablony usługi Azure Resource Manager są obsługiwane przez w dokumencie Microsoft [14 kontroli bezpieczeństwa chmury na Brytyjski w chmurze przy użyciu usługi Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). W tym dokumencie katalogi usługami Azure jak dostosować UK NCSC 14 chmurze zasad bezpieczeństwa, umożliwiając firmom szybkie możliwość zobowiązań ich zgodność przy użyciu usług w chmurze globalnie i w UK w systemie Microsoft Azure Chmura.

 Ten szablon wdraża infrastrukturę dla obciążenia. Kod aplikacji i obsłudze warstwą biznesową a oprogramowanie warstwy danych musi być zainstalowana i skonfigurowana. Szczegółowe instrukcje są dostępne [tutaj](https://aka.ms/ukwebappblueprintrepo).

 Jeśli nie masz subskrypcji platformy Azure, a następnie możesz utworzyć konto szybko i łatwo - [Rozpoczynanie pracy z platformą Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

 Szablony usługi Azure dostarczania architektura trójwarstwowa sieci web w środowisku chmury Azure, które obsługuje oficjalne UK obciążeń. Architektura zapewnia środowisku hybrydowym bezpieczny, rozszerzający sieci lokalnej do platformy Azure możliwość obciążenia opartego na sieci web można uzyskać dostępu do bezpiecznego użytkowników firmowej lub z Internetu.

![tekst alternatywny](images/diagram.png?raw=true "Azure oficjalnego UK trzy warstwy architektury")

 To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrażania znajdują się w [wdrożenia architecture](#deployment-architecture) sekcji.

((1) / 16 do / sieci wirtualnej - operacyjnych sieci wirtualnej
- podsieci (3) prefiksie/24 - warstwy 3 (danych sieci Web, Biz)
- (1) /27 DODAJE podsieci -
- ((1) /27 podsieci - podsieci bramy
- ((1) /29 podsieci - podsieci bramy aplikacji
- Używa domyślnej (DNS platformy Azure)
- Komunikacja równorzędna włączone do zarządzania w sieci wirtualnej
- Grupy zabezpieczeń sieci (NSG) do zarządzania przepływem ruchu

((1) prefiksie/24 sieć wirtualna — Zarządzanie sieciami wirtualnymi
- ((1) /27 podsieci
- DNS DODAJE używa (2) i (1) wpisy DNS platformy Azure
- Komunikacja równorzędna włączone do operacyjnej sieci wirtualnej
- Grupy zabezpieczeń sieci (NSG) do zarządzania przepływem ruchu

(1) brama aplikacji
- Zapory aplikacji sieci Web — włączone
- Tryb zapory aplikacji sieci Web — zapobiegania
- Zestaw reguł: OWASP 3.0
- Odbiornik HTTP na porcie 80
- Łączność/ruch regulowane przez NSG
- Publiczny punkt końcowy adres IP zdefiniowane (Azure)

(1) sieci VPN — oparte na trasach tunelu VPN IPSec lokacji 2
- Publiczny punkt końcowy adres IP zdefiniowane (Azure)
- Łączność/ruch regulowane przez NSG
- (1) brama sieci lokalnej (lokalnego punktu końcowego)
- (1) brama sieci platformy azure (punktu końcowego platformy Azure)

[9] maszyny wirtualne — wszystkie maszyny wirtualne są wdrażane z ustawieniami usługi Konfiguracja DSC Azure IaaS ochrony przed złośliwym oprogramowaniem

- (2) active Directory Domain Services kontrolery domeny (z systemem Windows Server 2012 R2)
  - (2) role serwera DNS - 1 dla maszyny Wirtualnej
  - (2) karty sieciowe podłączone do sieci wirtualnej operacyjne - 1 dla maszyny Wirtualnej
  - Oba są przyłączone do domeny w domenie zdefiniowaną w szablonie
    - Domena utworzona w ramach wdrożenia


- (1) Jumpbox (Host bastionu) zarządzania maszyny Wirtualnej
  - 1 karta sieciowa w sieci wirtualnej zarządzania z publicznego adresu IP
    - Grupa NSG jest używany do ograniczania ruchu (We/Wy) do konkretnych źródeł
  - Nie przyłączonych do domeny


- (2) maszyny wirtualne warstwy sieci web
  - (2) role serwera usług IIS - 1 dla maszyny Wirtualnej
  - (2) karty sieciowe podłączone do sieci wirtualnej operacyjne - 1 dla maszyny Wirtualnej
  - Nie przyłączonych do domeny


- (2) maszyny wirtualne warstwy biz
  - (2) karty sieciowe podłączone do sieci wirtualnej operacyjne - 1 dla maszyny Wirtualnej
  - Nie przyłączonych do domeny


- (2) maszyny wirtualne warstwy danych
  - (2) karty sieciowe podłączone do sieci wirtualnej operacyjne - 1 dla maszyny Wirtualnej
  - Nie przyłączonych do domeny

Zestawy dostępności
- (1) usługi active Directory kontrolera domeny maszyny Wirtualnej ustawić — 2 maszyny wirtualne
- (1) warstwa sieci web maszyna wirtualna ustawić — 2 maszyny wirtualne
- (1) biz warstwy maszyny Wirtualnej ustawić — 2 maszyny wirtualne
- (1) ustawianie warstwy danych maszyna wirtualna — 2 maszyny wirtualne

Moduł równoważenia obciążenia
- (1) moduł równoważenia obciążenia warstwy sieci web
- (1) moduł równoważenia obciążenia warstwy biz
- (1) moduł równoważenia obciążenia warstwy danych

Magazyn
- (14) konta magazynu łączna
  - Zestaw dostępności kontrolera domeny usługi Active Directory
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) Diagnostyka konto magazyn lokalnie nadmiarowy (LRS) DODAJE zestawu dostępności
  - Maszyna wirtualna Jumpbox zarządzania
    - (1) podstawowy magazyn lokalnie nadmiarowy (LRS) konta dla maszyny Wirtualnej Jumpbox
    - (1) Diagnostyka konto magazyn lokalnie nadmiarowy (LRS) dla maszyny Wirtualnej Jumpbox
  - Maszyny wirtualne warstwy sieci Web
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) Diagnostyka konto magazyn lokalnie nadmiarowy (LRS) dla zestawu dostępności warstwy sieci Web
  - Maszyny wirtualne warstwy Biz
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) Diagnostyka konto magazyn lokalnie nadmiarowy (LRS) dla zestawu dostępności warstwy Biz
  - Maszyny wirtualne warstwy danych
    - (2) podstawowego konta magazyn lokalnie nadmiarowy (LRS) - 1 dla każdej maszyny Wirtualnej  
    - (1) Diagnostyka konto magazyn lokalnie nadmiarowy (LRS) dla zestawu dostępności warstwy danych

### <a name="deployment-architecture"></a>Architektura wdrażania:

**Sieć lokalną**: prywatnej sieci lokalnej w organizacji.

**Sieć wirtualna produkcji**: produkcji [sieci wirtualnej](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (sieci wirtualnej) obsługuje aplikacji i innych zasobów operacyjnych działających na platformie Azure. Każdej sieci wirtualnej może zawierać kilka podsieci, które są używane do izolowania i zarządzaniem ruchem sieciowym.

**Sieci Web warstwy**: obsługi przychodzących żądań HTTP. Odpowiedzi są zwracane przez tę warstwę.

**Warstwa biznesowa**: implementuje procesy biznesowe i inne logiki funkcjonalności dla systemu.

**Baza danych warstwy**: umożliwia przechowywanie danych przy użyciu [programu SQL Server zawsze włączonych grup dostępności](https://msdn.microsoft.com/library/hh510230.aspx) wysokiej dostępności. Klienci mogą używać [bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) jako alternatywne PaaS.

**Brama**: [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) zapewnia łączność między routerami w sieci lokalnej i produkcji sieci wirtualnej.

**Bramą internetową i publiczny adres IP**: bramą internetową udostępnia usługi aplikacji użytkownikom za pośrednictwem Internetu. Dostęp do tych usług ruchu jest zabezpieczone przy użyciu [brama aplikacji w](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferty warstwy 7 routingu i możliwości z ochroną zaporą (WAF) aplikacji sieci web równoważenia obciążenia.

**Zarządzanie sieciami wirtualnymi**: to [sieci wirtualnej](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) zawiera zasoby, które implementuje zarządzania i monitorowania możliwości dla obciążeń działających w środowisku produkcyjnym sieci wirtualnej.

**Jumpbox**: nazywane również [hosta bastionu](https://en.wikipedia.org/wiki/Bastion_host), która jest bezpiecznej maszyny Wirtualnej w sieci, w której administratorzy mogą używać do nawiązania połączenia maszyn wirtualnych w środowisku produkcyjnym sieci wirtualnej. Rampa zawiera sieciową grupę zabezpieczeń, która zezwala na zdalny ruch z publicznych adresów IP znajdujących się na liście bezpiecznych adresów. Aby zezwolić na zdalny pulpitu ruch (RDP), źródło ruchu musi być zdefiniowana w grupie NSG. Zarządzanie zasobami produkcji jest za pomocą protokołu RDP przy użyciu zabezpieczonych Jumpbox maszyny Wirtualnej.

**Trasy zdefiniowane przez użytkownika**: [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) są używane do definiowania przepływu ruchu IP w sieci wirtualnych platformy Azure.

**Połączyć za pomocą sieci wirtualnych sieci**: produkcyjnego i zarządzanie sieciami wirtualnymi są połączone za pomocą [sieci wirtualnej komunikacji równorzędnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Te sieci wirtualne są nadal zarządzane jako oddzielne zasoby, ale pojawia się na potrzeby łączności wszystkich tych maszyn wirtualnych. Te sieci komunikują się ze sobą za pomocą prywatnych adresów IP. Sieci wirtualnej komunikacji równorzędnej podlega sieci wirtualnych, w tym samym regionie Azure.

**Sieciowe grupy zabezpieczeń**: [grup NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, akceptujące lub odrzucające ruchu w sieci wirtualnej. Grupy NSG mogą służyć do zabezpieczenia ruchu w podsieci lub poziomie maszyny Wirtualnej.

**Usług domenowych w usłudze Active Directory (AD DS)**: tej architektury zawiera dedykowany [usług domenowych w usłudze Active Directory](https://technet.microsoft.com/library/hh831484.aspx) wdrożenia.

**Rejestrowanie i inspekcja**: [dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) przechwytuje operacji wykonywanych na zasobów w ramach subskrypcji, takie jak działania, kto zainicjował podczas operacji wystąpił stan operacji i wartości inne właściwości, które mogą ułatwić badania wykonać operację. Dziennik aktywności platformy Azure jest usługą platformy Azure, która przechwytuje wszystkie akcje w ramach subskrypcji. Dzienniki można zarchiwizować lub wyeksportowane, jeśli jest to wymagane.

**Monitorowanie i alerty sieci**: [obserwatora sieciowego Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) jest usługą platformy dostarcza przechwytywania pakietów sieciowych, rejestrowania przepływu, narzędzia topologii i diagnostyki dla sieci traffics w Twojej sieci wirtualnych.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="business-continuity"></a>Ciągłość działania

**Wysoka dostępność**: obciążenia serwera są pogrupowane w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomaga zapewnić wysoką dostępność maszyn wirtualnych na platformie Azure. Taka konfiguracja pozwala, upewnij się, że podczas planowanego lub nieplanowanego zdarzenia konserwacji co najmniej jednej maszyny wirtualnej będzie dostępny i oferując dostępność przez 99,95% umowy SLA platformy Azure.

### <a name="logging-and-audit"></a>Rejestrowanie i inspekcja

**Monitorowanie**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) to usługa platformy, która zapewnia jednego źródła do monitorowania dziennika aktywności, metryki i dzienników diagnostycznych wszystkich zasobów platformy Azure. Azure Monitor można skonfigurować do wizualizacji, zapytania trasy, archiwizacji i działają na metryki i dzienników pochodzących z zasobami na platformie Azure. Zaleca się, że kontroli dostępu na podstawie zasobów służy do zabezpieczania dziennik inspekcji pomaga zapewnić, że użytkownicy nie mają uprawnienia do modyfikowania dzienniki.

**Dzienniki aktywności**: Konfigurowanie [Dzienniki aktywności Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) do zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji.

**Dzienniki diagnostyczne**: [dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez zasób. Dzienniki te mogą obejmować dzienniki systemu zdarzeń systemu Windows, obiektów blob, tabel i dzienniki kolejki.

**Zapory Dzienniki**: Application Gateway udostępnia pełnych dzienników diagnostyki i dostępu. Dzienniki zapory są dostępne dla zasobów usługi Application Gateway z włączoną zaporą aplikacji sieci Web.

**Zaloguj się Archiwizacja**: Magazyn danych można skonfigurować, aby zapisać scentralizowany magazyn Azure dziennika konta dla archiwizacji i okres przechowywania zdefiniowany. Dzienniki mogą być przetwarzane przy użyciu usługi Analiza dzienników Azure lub innej systemów SIEM.

### <a name="identity"></a>Tożsamość

**Usługi domenowe Active Directory**: tej architektury zapewnia wdrażania usług domenowych w usłudze Active Directory na platformie Azure. Aby uzyskać szczegółowe zalecenia dotyczące implementowania usługi Active Directory na platformie Azure, zobacz następujące artykuły:

[Rozszerzenie usług domenowych Active Directory (AD DS) na platformie Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Wskazówki dotyczące wdrażania systemu Windows serwer usługi Active Directory na maszynach wirtualnych Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integracja usługi Active Directory**: zamiast dedykowanych architektury usług AD DS, klienci mogą chcieć użyć [usługi Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) integracji lub [do lokalnego przyłączonych do usługi Active Directory na platformie Azure las](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Bezpieczeństwo

**Zabezpieczenia Management**: ten plan umożliwia administratorom połączenia do zarządzania sieciami wirtualnymi i Jumpbox za pomocą protokołu RDP z zaufanego źródła. Ruch sieciowy za zarządzanie sieciami wirtualnymi steruje się za pomocą grup NSG. Dostęp do portu 3389 jest ograniczone do ruchu z zaufanych zakres IP, dostępnym podsieć zawierająca Jumpbox.

Klienci mogą również wziąć pod uwagę przy użyciu [modelu administracyjnego zwiększone zabezpieczenia](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) do zabezpieczania środowiska podczas łączenia się Zarządzanie sieciami wirtualnymi i Jumpbox. Zalecane jest, aby zwiększyć bezpieczeństwo klienci powinni korzystać [uprzywilejowanego dostępu do stacji roboczej](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) i RDGateway konfiguracji. Korzystanie z wirtualnych urządzeń sieciowych i publicznych/prywatnych sieci obwodowych oferują dodatkowe ulepszenia zabezpieczeń.

**Zabezpieczanie sieci**: [grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) są zalecane dla każdej podsieci w celu zapewnienia drugiego poziomu ochrony przed ruch przychodzący, pomijanie bramę niepoprawnie skonfigurowany lub wyłączone. Przykład — [szablonu usługi Resource Manager do wdrażania grupy NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Zabezpieczanie punktów końcowych publicznego**: bramą internetową udostępnia usługi aplikacji użytkownikom za pośrednictwem Internetu. Dostęp do tych usług ruchu jest zabezpieczone przy użyciu [brama aplikacji w](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), zapewniające zarządzania protokołu zapory aplikacji sieci Web i HTTPS.

**Zakresy adresów IP**: zakresy IP architektury są sugerowane zakresów. Zalecana klientów należy wziąć pod uwagę ich środowiska i użycie odpowiednich zakresach.

**Połączenia hybrydowe**: obciążeń w chmurze są połączone z lokalnego centrum danych za pośrednictwem protokołu IPSEC sieci VPN przy użyciu bramy sieci VPN platformy Azure. Klienci powinien upewnić się, że używają odpowiednie bramy sieci VPN z usługą Azure. Przykład — [szablonu usługi Resource Manager bramy sieci VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Klienci korzystający z na dużą skalę, obciążenia o znaczeniu krytycznym wymagania danych big data mogą chcieć należy rozważyć użycie architektury sieci hybrydowe [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) sieci prywatnej łączności do firmy Microsoft usługi w chmurze.

**Separacji**: informacje o architekturze wzorcowej oddziela sieci wirtualne dla operacji zarządzania i operacje biznesowe. Oddzielne sieci wirtualnych i podsieci umożliwia zarządzanie ruchu, w tym ograniczenia przychodzące i wychodzące ruchu za pomocą grup NSG między segmentami sieci po [bezpieczeństwa usług i sieci chmury Microsoft](https://docs.microsoft.com/azure/best-practices-network-security) najlepsze rozwiązania.

**Zarządzanie zasobami**: zasobów platformy Azure, takich jak maszyn wirtualnych, sieci wirtualnych i usług równoważenia obciążenia są zarządzane przez ich grupowanie razem w [grup zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). Następnie można przypisać ról kontroli dostępu na podstawie zasobów do każdej grupy zasobów można ograniczyć dostęp tylko do autoryzowanych użytkowników.

**Ograniczeń kontroli dostępu**: Użyj [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) do zarządzania zasobami w aplikacji za pomocą [role niestandardowe](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC może być używany do ograniczania operacje który DevOps można wykonywać w każdej warstwie. Podczas udzielania uprawnień, użyj [zasadą najniższych uprawnień](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Rejestruj wszystkie operacje administracyjne i przeprowadzaj regularne inspekcje, aby mieć pewność, że wszelkie zmiany konfiguracji są planowane.

**Dostęp do Internetu**: wykorzystuje informacje o architekturze wzorcowej [brama aplikacji w usłudze Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) jako internetowy równoważenia bramy i obciążenia. Niektórzy klienci mogą również wziąć pod uwagę przy użyciu innej sieci wirtualnych urządzeń dla dodatkowych warstw zabezpieczeń, zamiast sieci [brama aplikacji w usłudze Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Centrum zabezpieczeń Azure**: [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) centralnej Wyświetla stan zabezpieczeń zasobów w subskrypcji i zawiera zalecenia, które ułatwiają ochronę zasobów ze złamanymi zabezpieczeniami. Można go również służyć do włączyć bardziej szczegółowe zasady. Na przykład zasad może odnosić się do określonych grup zasobów, co pozwala dostosować jego stan ryzyko przedsiębiorstwa. Zaleca się klientom włączenie Centrum zabezpieczeń Azure w swoich subskrypcji Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC chmury zabezpieczeń zasad zgodności dokumentacji

Usługa komercyjnych wierzchołek (Agencja działający zwiększające handlowych i nabywania działania przez Rząd) odnowić klasyfikacji Microsoft przedsiębiorstwa w zakresie usług w chmurze do chmury G IPv6, obejmujące wszystkie jego oferty na poziomie oficjalnego. Szczegóły Azure i w chmurze G znajdują się w [podsumowanie oceny zabezpieczeń G-chmury Azure UK](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Ten plan jest wyrównywany do zasad zabezpieczeń 14 chmury, które są udokumentowane w NCSC [zasad zabezpieczeń chmury](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) pomaga zapewnić środowisko, które obsługuje obciążeń sklasyfikowane jako oficjalne UK.

[Macierzy odpowiedzialność klienta](https://aka.ms/blueprintuk-gcrm) (skoroszyt programu Excel) zawiera listę wszystkich zasad zabezpieczeń 14 chmury, a macierz wskazuje, dla każdej zasady (lub podsekcja zasady), czy zasada wdrażania jest odpowiedzialny za Microsoft, klienta lub udostępnionego między nimi.

[Macierzy implementacji zasady](https://aka.ms/ukwebappblueprintpim) oznacza wszystkie zasady zabezpieczeń 14 chmury, a macierz, list (skoroszyt programu Excel), dla każdej zasady (lub zasadą podsekcja) wyznaczony odpowiedzialność klienta w klienta Obowiązki macierzy, 1) Jeśli automatyzacji planu implementuje zasady i 2) opis sposobu implementacji wyrównana z zdefiniowanymi zasady. Ta zawartość jest również dostępna [tutaj](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Ponadto Alliance zabezpieczeń chmury (CSA) opublikowane macierzy kontroli chmury, aby obsługiwać klientów podczas obliczania dostawców w chmurze oraz zidentyfikować pytania, na które należy odpowiedzieć na przed przejściem do usług w chmurze. W odpowiedzi, Microsoft Azure odbierane kwestionariusz inicjatywy oceny konsensu CSA ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), która opisuje, jak Microsoft adresów sugerowane zasad.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Istnieją dwie metody, których użytkownicy wdrożenia może użyć do wdrożenia automatyzacji tego planu. Pierwsza metoda używa skrypt programu PowerShell, podczas gdy druga metoda wykorzystuje portalu Azure, aby wdrożyć architektura referencyjna struktury. Szczegółowe instrukcje są dostępne [tutaj](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
