---
title: Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web IaaS dla FedRAMP
description: Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web IaaS dla FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 27346d8e2133ea61056817fb48050a4e5c8c3c97
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726486"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Zabezpieczeń platformy Azure i plan zgodności: aplikacja sieci Web IaaS dla FedRAMP

## <a name="overview"></a>Przegląd

[Federalne ryzyka i Program do zarządzania autoryzacji (FedRAMP)](https://www.fedramp.gov) to program całej dla instytucji rządowych USA, który zawiera standardowe podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów chmury i usługi. Ten Azure zabezpieczeń i zgodności plan automatyzacji zawiera wskazówki dotyczące wdrożenia FedRAMP zgodności infrastrukturę jako usługę (IaaS) środowiska odpowiednie dla prostej aplikacji sieci web skierowane do Internetu. To rozwiązanie pozwala zautomatyzować, wdrażanie i konfigurowanie zasobów Azure dla typowych architektura referencyjna prezentacja sposoby, w którym klienci mogą spełnić szczególne wymagania dotyczące zabezpieczeń i zgodności i służy jako podstawę do klientów do tworzenia i Skonfiguruj swoje własne rozwiązania na platformie Azure. Rozwiązanie implementuje podzbiór formantów z linii bazowej wysokiej FedRAMP, oparte na SP NIST 800-53. Aby uzyskać więcej informacji na temat wymagań FedRAMP i tego rozwiązania, zobacz [dokumentacji zgodności](#compliance-documentation).
> [!NOTE]
> To rozwiązanie wdraża Azure dla instytucji rządowych.

Ten Azure zabezpieczeń i zgodności plan automatyzacji automatycznie wdraża IaaS architekturę odwołania aplikacji sieci web z wstępnie skonfigurowane zabezpieczenia, aby ułatwić klientom zapewnienia zgodności z wymaganiami FedRAMP. Rozwiązania składa się z szablonów usługi Azure Resource Manager i skryptów programu PowerShell, które przewodnik dotyczący wdrażania zasobów i konfiguracji.

Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez żadnych modyfikacji nie wystarcza do całkowicie spełniają wymagania wysokiej FedRAMP linii bazowej. Pamiętaj o następujących kwestiach:
- Taka architektura umożliwia linii bazowej, aby ułatwić klientom używać platformy Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, obejrzyj ten [wideo](https://aka.ms/fedrampblueprintvideo) wyjaśniający i prezentowania jego wdrażania.

Kliknij przycisk [tutaj](https://aka.ms/fedrampblueprintrepo) instrukcje wdrożenia.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie wdraża architektura referencyjna dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura zawiera warstwa sieci web, warstwy danych, usługi Active Directory infrastruktury, bramy aplikacji oraz usługi równoważenia obciążenia. Maszyny wirtualne wdrażane warstwy web i danych są skonfigurowane w zestawie dostępności i wystąpień programu SQL Server są konfigurowane w zawsze włączonej grupy dostępności, wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania konfiguracji zgodności i zabezpieczeń na poziomie systemu operacyjnego. Host bastionu zapewnia bezpieczne połączenie, Administratorzy mogą wdrożyć dostęp do zasobów. **Azure zaleca się Konfigurowanie połączenia sieci VPN lub rozwiązania Azure ExpressRoute do importowania danych i zarządzania w podsieci architektura odwołania.**

![IaaS aplikacji sieci Web dla diagram architektury odwołanie FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "IaaS aplikacji sieci Web dla diagram architektury FedRAMP odwołania")

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrażania znajdują się w [wdrożenia architecture](#deployment-architecture) sekcji.

- Azure Virtual Machines
    - (1) host bastionu (system Windows Server Datacenter 2016)
    - (2) usługi active Directory kontrolera domeny (z systemem Windows Server Datacenter 2016)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/usług IIS (Windows Server 2016 Datacenter)
- Zestawy dostępności
    - (1) kontrolery domeny active Directory
    - (1) węzły klastra SQL
    - (1) w sieci web/usług IIS
- Azure Virtual Network
    - ((1) / 16 do / sieci wirtualnych
    - podsieci o prefiksie/24 (5)
    - Ustawienia DNS są skonfigurowane do obu kontrolerów domeny
- Azure Load Balancer
- Azure Application Gateway
    - (1) brama aplikacji zapory aplikacji sieci Web jest włączona
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - odbiornik: port 443
- Azure Storage
    - (7) konta magazynu geograficznie nadmiarowego
- Monitor chmury Azure
- Magazyn usługi Recovery Services
- W usłudze Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Azure Monitor

## <a name="deployment-architecture"></a>Architektura wdrażania

Poniższa sekcja zawiera szczegóły dotyczące projektowania i wdrażania elementów.

**Host bastionu**: host bastionu jest pojedynczy punkt wejścia, który zapewnia bezpieczne połączenie dla administratorów, aby wdrożyć dostęp do zasobów. Bastionu NSG hosta zezwala na połączenia tylko na portu 3389 protokołu TCP dla protokołu RDP. Klientów można dodatkowo skonfigurować hosta bastionu, aby spełniać ograniczenie funkcjonalności wymagania systemu organizacji.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej z 10.200.0.0/16 przestrzeni adresowej.

**Sieciowe grupy zabezpieczeń**: to rozwiązanie wdraża zasobów w architekturze podsieci WWW, podsieci bazy danych, podsieci usługi Active Directory i zarządzania podsieci w sieci wirtualnej. Podsieci są logicznie oddzielone zastosować do poszczególnych podsieci, aby ograniczyć ruch między podsieciami, aby tylko to konieczne do systemu oraz funkcji zarządzania reguły grupy zabezpieczeń sieci.

Sprawdź konfigurację [sieciowej grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone w tym rozwiązaniu. Klientów można skonfigurować grupy zabezpieczeń sieci, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako przewodnika.

Każdą z tych podsieci ma dedykowany sieciowej grupy zabezpieczeń (NSG):
- 1 NSG dla bramy aplikacji (LBNSG)
- 1 NSG dla hosta bastionu (MGTNSG)
- 1 NSG dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 NSG dla serwerów SQL (SQLNSG)
- 1 NSG dla warstwy sieci Web (WEBNSG)

**Podsieci**: każdej podsieci jest skojarzony z jego odpowiedniej grupy NSG.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przy użyciu kilku środków szyfrowania.

**Usługa Azure Storage**: Aby spełnić wymagania dotyczące szyfrowania danych na rest, użyj wszystkich kont magazynu [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server jest skonfigurowany do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), który wykonuje w czasie rzeczywistym szyfrowania i odszyfrowywania danych i plików dziennika do ochrony informacji w stanie spoczynku. Funkcji TDE zawiera gwarantują, że przechowywanych danych nie zostało poddane nieautoryzowanego dostępu.

Klienci mogą również skonfigurować następujące środki bezpieczeństwa serwera SQL:
-   [AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania konta magazynu platformy Azure.
-   [Reguły zapory](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) uniemożliwić wszystkie dostęp do serwerów baz danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, podając alertów zabezpieczeń bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, ataki i nietypowych bazy danych programu access wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu.
-   [Baza danych SQL dane dynamiczne maskowanie](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) może odbywać się po wdraża architektura referencyjna struktury. Klienci muszą dostosować dane dynamiczne maskowanie ustawienia, aby stosować się do schematu bazy danych.

**Szyfrowanie dysków Azure**: szyfrowania dysków Azure umożliwia szyfrowane dyski maszyn wirtualnych IaaS systemu Windows. [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają tożsamość możliwości zarządzania w środowisku platformy Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft.
- Używanie programu Azure AD można wykonać uwierzytelniania do aplikacji sieci web wdrożonych przez klienta. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji, a dostęp do zasobów można ograniczyć na podstawie roli użytkownika.
- Wdrożone wystąpienie IaaS usługi Active Directory umożliwia zarządzanie tożsamości na poziomie systemu operacyjnego dla wdrożonych maszyn wirtualnych IaaS.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych**: w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Usługa Azure Key Vault ułatwia zarządzanie klucze szyfrowania dysków maszyn wirtualnych IaaS i klucze tajne dla tej architektury odwołania.

**Zarządzanie poprawkami**: maszyny wirtualne systemu Windows wdrożone przez Azure zabezpieczeń i zgodności plan automatyzacji są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie również wdraża rozwiązanie Automatyzacja Azure za pomocą których można utworzyć wdrożenia aktualizacji do wdrożenia poprawki na serwerach z systemem Windows w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, że pomaga zidentyfikować i usunąć wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty Jeśli znane złośliwego lub niechcianego oprogramowania próbuje zainstalować lub uruchomić na chronionych maszyn wirtualnych.

**Brama aplikacji w**: Architektura zmniejsza ryzyko luk w zabezpieczeniach z zapory aplikacji sieci web (WAF) przy użyciu bramy aplikacji i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z ruleset OWASP 3.0

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: co najmniej jednej maszyny wirtualnej jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, spotkania 99,95% umowy SLA platformy Azure. Rozwiązanie umożliwia wdrażanie wszystkich warstwa sieci web i maszyn wirtualnych w warstwie danych [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności upewnij się, że maszyny wirtualne rozproszone na wielu klastrów izolowanego sprzętu, aby zwiększyć dostępność. Ponadto to rozwiązanie wdraża maszyn wirtualnych programu SQL Server w zestawie dostępności jako [grupy dostępności funkcji AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Zawsze włączone dostępności grupy funkcja zapewnia możliwości wysokiej dostępności i odzyskiwania po awarii.

**Magazyn usług odzyskiwania**: [magazyn usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. W magazynie usług odzyskiwania klientów można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, umożliwiające szybsze przywracania.

**Chmury monitora**: [monitora chmury](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) typ monitora kworum klastra trybu Failover w systemie Windows Server 2016 wykorzystującego Azure jako punktu rozstrzygnięcia. Monitor chmury, takich jak inne monitora kworum, pobiera głos i mogą uczestniczyć w obliczeniach kworum, ale używa standardowych publicznie dostępny magazyn obiektów Blob Azure. Eliminuje koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

OMS zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. OMS [analizy dzienników](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.

- **Dzienniki aktywności:**[Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji. Dzienniki aktywności mogą ułatwić określenie inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne:**[dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki usługi Azure storage, dzienników inspekcji usługi Key Vault i dzienniki bramy aplikacji dostępu i zapory.
- **Archiwizowanie dziennika:** zapisu wszystkich dzienników diagnostycznych na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji. Przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, aby spełnić wymagania organizacji dotyczące przechowywania. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

Ponadto następujące rozwiązania OMS są instalowane w ramach tej architektury. Należy pamiętać, że odpowiedzialność klienta do konfigurowania tych rozwiązań, aby były wyrównane z formantami zabezpieczeń FedRAMP:
-   [Ocena AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i udostępnia listę zalecenia dotyczące infrastruktury serwera wdrożonym z określonymi priorytetami.
-   [Ocena ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raportów dotyczących stanu złośliwego oprogramowania, zagrożeń i ochrony.
-   [Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie do automatyzacji Azure przechowuje, uruchamia i zarządza elementami runbook.
-   [Bezpieczeństwo i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów podając metryki na domen zabezpieczeń, godne uwagi problemy wykryć, analizy zagrożeń i typowych kwerend zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożonym opracowuje dla klientów.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie klienta aktualizacje zabezpieczeń systemu operacyjnego, w tym stanu o dostępnych aktualizacjach i proces instalacji wymaganych aktualizacji.
-   [Agent kondycji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): kondycja agenta rozwiązania zgłasza liczbę agentów są wdrażane i ich rozmieszczenie geograficzne, a także liczbę agentów, które odpowiadają i liczby agentów, które są przesyłanie danych operacyjnych.
-   [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązania analizy dziennika aktywności obsługuje Analiza dzienników Azure działania we wszystkich subskrypcji platformy Azure dla danego klienta.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): śledzenie zmian rozwiązania pozwala łatwo zidentyfikować zmiany w środowisku.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, obsługi zabezpieczeń oraz trendów umożliwia organizacjom inspekcji, tworzyć alerty i archiwizowanie danych, w tym śledzenia wywołań interfejsu API w przypadku klientów zasobów platformy Azure.

## <a name="threat-model"></a>Modelu zagrożeń
Diagram przepływu danych dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/fedrampWAdfd) lub znajduje się poniżej. Ten model może ułatwić klientom punkty potencjalne ryzyko w infrastrukturze systemu, podczas wprowadzania zmian.

![Aplikacja sieci Web IaaS dla modelu zagrożeń FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS komputerze sieci Web dla modelu zagrożeń FedRAMP")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i plan zgodności - FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Macierzy oznacza wdrożenia każdego formantu odpowiedzialność firmy Microsoft, odbiorcy, czy jest udostępniane między nimi.

[Zabezpieczeń platformy Azure i plan zgodności - FedRAMP IaaS sieci Web aplikacji wysokiej implementacji macierzy kontroli](https://aka.ms/blueprintwacim) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Macierz zawiera informacje, na którym formanty są objęte architektury aplikacji sieci web IaaS, w tym szczegółowy opis sposobu implementacji spełnia wymagania dotyczące każdej kontrolki objęte usługą.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

To Azure zabezpieczeń i zgodności plan automatyzacji składa się z pliki konfiguracji w formacie JSON i skryptów programu PowerShell, które są obsługiwane przez usługi interfejsu API usługi Azure Resource Manager do wdrażania zasobów w systemie Azure. Szczegółowe instrukcje są dostępne [tutaj](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> To rozwiązanie wdraża Azure dla instytucji rządowych.

#### <a name="quickstart"></a>Szybki start
1. Klonuj lub pobrać [to](https://aka.ms/fedrampblueprintrepo) repozytorium GitHub, aby na lokalnej stacji roboczej.

2. Uruchom skrypt programu PowerShell przed wdrożeniem: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Kliknij przycisk poniżej, zaloguj się do portalu Azure, wprowadź wymagane parametry szablonu ARM i kliknij przycisk **zakupu**.

    [![Wdrażanie na platformie Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieć VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpiecznego nawiązać połączenie z zasoby wdrożone w ramach tej architektury odwołanie IaaS aplikacji sieci Web. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Implementując bezpieczny tunel sieci VPN platformy Azure, można utworzyć wirtualnego prywatnej połączenie między siecią lokalną i sieci wirtualnej platformy Azure. To połączenie odbywa się przez Internet i pozwala bezpiecznie "tunnel" informacji wewnątrz zaszyfrowanych łącza między siecią a Azure klienta. Sieci VPN typu lokacja-lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. [Trybu tunelowania IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizm szyfrowania.

Ponieważ ruchu w tunelu VPN przechodzą przez Internet, korzystając z sieci VPN lokacja lokacja, firma Microsoft oferuje opcji inny, jeszcze bardziej bezpieczne połączenia. Usługa Azure ExpressRoute jest dedykowanych sieci WAN łącza między Azure i lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Ponadto jest bezpośrednie połączenie klienta telekomunikacyjnych dostawcy, dlatego dane nie są przesyłane w Internecie i w związku z tym nie jest uwidaczniana do niego.

Najlepsze rozwiązania dotyczące wdrażania sieci hybrydowe bezpiecznego, która rozszerza sieci lokalnej na platformie Azure są [dostępne](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.  
- Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.  
- Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.  
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  
- Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
- Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
