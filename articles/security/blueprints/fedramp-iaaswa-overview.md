---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla FedRAMP
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: e1d481c6019feebf3d62f0e23480f5572363869c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946842"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Aplikacja sieci Web IaaS dla FedRAMP

## <a name="overview"></a>Omówienie

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) to programowy rząd USA, który zapewnia ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów i usług w chmurze. Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja zawiera wskazówki dotyczące wdrażania środowiska zgodnego z infrastrukturą FedRAMP jako usługi (IaaS), które jest odpowiednie dla prostej internetowej aplikacji sieci Web. To rozwiązanie automatyzuje wdrażanie i Konfigurowanie zasobów platformy Azure na potrzeby wspólnej architektury referencyjnej, pokazując sposoby, w których klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności, a także jak podstawą dla klientów do kompilowania i Skonfiguruj własne rozwiązania na platformie Azure. Rozwiązanie implementuje podzbiór kontrolek z FedRAMP High Baseline w oparciu o instytut NIST SP 800-53. Aby uzyskać więcej informacji na temat wymagań FedRAMP i tego rozwiązania, zobacz [dokumentację dotyczącą zgodności](#compliance-documentation).
> [!NOTE]
> To rozwiązanie jest wdrażane w Azure Government.

Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja automatycznie wdraża architekturę referencyjną aplikacji sieci Web IaaS ze wstępnie skonfigurowanymi kontrolami zabezpieczeń, aby pomóc klientom w osiągnięciu zgodności z wymaganiami FedRAMP. Rozwiązanie składa się z szablonów Azure Resource Manager i skryptów programu PowerShell, które ułatwiają wdrażanie i Konfigurowanie zasobów.

Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania FedRAMP wysokiego planu bazowego. Pamiętaj o następujących kwestiach:
- Ta architektura udostępnia linię bazową, która ułatwia klientom korzystanie z platformy Azure w sposób zgodny z FedRAMP.
- Klienci są odpowiedzialni za przeprowadzenie odpowiedniej oceny zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

Aby zapoznać się z krótkim omówieniem sposobu działania tego rozwiązania, Obejrzyj ten [film wideo](https://aka.ms/fedrampblueprintvideo) z wyjaśnieniem i demonstrowaniem jego wdrożenia.

Kliknij [tutaj](https://aka.ms/fedrampblueprintrepo) , aby uzyskać instrukcje dotyczące wdrażania.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
To rozwiązanie służy do wdrażania architektury referencyjnej dla aplikacji sieci Web IaaS z zapleczem SQL Server. Architektura obejmuje warstwę sieci Web, warstwę danych, Active Directory infrastrukturę, Application Gateway i Load Balancer. Maszyny wirtualne wdrożone w warstwach sieci Web i danych są skonfigurowane w zestawie dostępności, a wystąpienia SQL Server są konfigurowane w grupie dostępności AlwaysOn w celu zapewnienia wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy Active Directory są używane do wymuszania konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego. Host bastionu zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. **Platforma Azure zaleca skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![Aplikacja sieci Web IaaS dla diagramu architektury referencyjnej FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Aplikacja sieci Web IaaS dla diagramu architektury referencyjnej FedRAMP")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Usługa Azure Virtual Machines
    - (1) Host bastionu (system Windows Server 2016 Datacenter)
    - (2) Active Directory kontroler domeny (Windows Server 2016 Datacenter)
    - (2) SQL Server węzeł klastra (SQL Server 2017 w systemie Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Zestawy dostępności
    - (1) Active Directory kontrolery domeny
    - (1) węzły klastra SQL
    - (1) sieć Web/IIS
- Azure Virtual Network
    - (1)/16 sieci wirtualnych
    - (5)/24 podsieci
    - Ustawienia DNS są ustawiane na obu kontrolerach domeny
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway włączone
        - Tryb zapory: zapobieganie
        - zestaw reguł: OWASP 3.0
        - odbiornik: port 443
- Azure Storage
    - (7) konta magazynu geograficznie nadmiarowego
- Monitor usługi Azure Cloud
- Magazyn usług Recovery Services
- W usłudze Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (dzienniki)

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów deweloperskich i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. SIECIOWEJ grupy zabezpieczeń hosta bastionu zezwala na połączenia tylko na porcie TCP 3389 dla protokołu RDP. Klienci mogą następnie skonfigurować hosta bastionu, aby spełniał wymagania dotyczące ograniczania funkcjonalności systemu organizacji.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie wdraża zasoby w architekturze z oddzielną podsiecią internetową, podsiecią bazy danych, podsiecią Active Directoryową i podsiecią zarządzania w sieci wirtualnej. Podsieci są logicznie oddzielone przez reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch między podsieciami tylko do tych, które są niezbędne dla funkcji systemu i zarządzania.

Zapoznaj się z konfiguracją [sieciowych grup zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożonych z tym rozwiązaniem. Klienci mogą konfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej za pomocą [tej dokumentacji](../../virtual-network/virtual-network-vnet-plan-design-arm.md) jako przewodnika.

Każda podsieć ma dedykowaną sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń):
- 1 sieciowej grupy zabezpieczeń dla Application Gateway (LBNSG)
- 1 sieciowej grupy zabezpieczeń dla hosta bastionu (MGTNSG)
- 1 sieciowej grupy zabezpieczeń dla podstawowych i zapasowych kontrolerów domeny (ADNSG)
- 1 sieciowej grupy zabezpieczeń dla serwerów SQL (SQLNSG)
- 1 sieciowej grupy zabezpieczeń dla warstwy sieci Web (WEBNSG)

**Podsieci**: Każda podsieć jest skojarzona z odpowiadającą jej sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przy użyciu kilku miar szyfrowania.

**Azure Storage**: Aby spełnić wymagania dotyczące szyfrowania danych w czasie spoczynku, wszystkie konta magazynu używają [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server jest skonfigurowany do używania [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), który wykonuje szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych i plików dziennika w celu ochrony informacji przechowywanych w pamięci podręcznej. TDE zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.

Klienci mogą również skonfigurować następujące SQL Server miary zabezpieczeń:
-   [Uwierzytelnianie i autoryzacja w usłudze AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
-   [Always Encrypted kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
-   [SQL Database dynamiczną maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) można wykonać po wdrożeniu architektury referencyjnej. Klienci będą musieli dostosować ustawienia dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

**Azure Disk Encryption**: Azure Disk Encryption jest używany do szyfrowania dysków maszyn wirtualnych z systemem Windows IaaS. [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków systemu operacyjnego i danych. Rozwiązanie jest zintegrowane z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają funkcje zarządzania tożsamościami w środowisku platformy Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft do zarządzania katalogami i tożsamościami opartymi na chmurze.
- Uwierzytelnianie do aplikacji sieci Web wdrożonej przez klienta można wykonać przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).  
- [Access Control oparte na rolach (RBAC) na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem na platformie Azure. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji, a dostęp do zasobów można ograniczyć na podstawie roli użytkownika.
- Wdrożone wystąpienie Active Directory IaaS zapewnia zarządzanie tożsamościami na poziomie systemu operacyjnego dla wdrożonych maszyn wirtualnych IaaS.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie**wpisami tajnymi: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Azure Key Vault ułatwia zarządzanie dyskami maszyn wirtualnych IaaS — kluczami szyfrowania i wpisami tajnymi dla tej architektury referencyjnej.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone przez tę Strategia zabezpieczeń i zgodności z przepisami platformy Azure automatyzację są domyślnie konfigurowane do odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie wdraża również Azure Automation rozwiązanie, za pomocą którego można tworzyć wdrożenia aktualizacji w celu wdrażania poprawek do serwerów z systemem Windows w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) Virtual Machines zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami, gdy znane złośliwe lub niepożądane oprogramowanie próbuje Zainstaluj lub Uruchom na chronionych maszynach wirtualnych.

**Application Gateway**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway z zaporą aplikacji sieci Web (WAF) i włączonym zestawem reguł OWASP. Dodatkowe możliwości obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (Tryb WAF)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%. Rozwiązanie wdraża wszystkie maszyny wirtualne warstwy sieci Web i warstwy danych w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone w wielu izolowanych klastrach sprzętowych w celu zwiększenia dostępności. Ponadto to rozwiązanie wdraża SQL Server maszyny wirtualne w zestawie dostępności jako [zawsze włączonych grup dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Funkcja zawsze włączona Grupa dostępności zapewnia obsługę funkcji wysokiej dostępności i odzyskiwania po awarii.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

**Monitor chmury**: [Monitor w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu failover w systemie Windows Server 2016, który wykorzystuje platformę Azure jako punkt rozstrzygania. Monitor w chmurze, taki jak każdy inny monitor kworum, otrzymuje głos i może uczestniczyć w obliczeniach kworum, ale korzysta ze standardowego publicznie dostępnego Blob Storage platformy Azure. Eliminuje to dodatkowe obciążenie pracą maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

Dzienniki Azure Monitor zapewniają obszerne rejestrowanie aktywności systemu i użytkownika, a także kondycję systemu. [Azure monitor rejestruje](../azure-security-disk-encryption-overview.md) rozwiązanie zbiera i analizuje dane wygenerowane przez zasoby na platformie Azure i w środowiskach lokalnych.

- **Dzienniki aktywności:**  [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne:**  [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) to wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory.
- **Archiwizowanie dzienników:**  Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji. Te dzienniki łączą się z dziennikami Azure Monitor na potrzeby przetwarzania, przechowywania i raportowania pulpitów nawigacyjnych.

Ponadto następujące rozwiązania do monitorowania są instalowane w ramach tej architektury. Należy pamiętać, że klient jest odpowiedzialny za skonfigurowanie tych rozwiązań w celu dostosowania ich do kontroli zabezpieczeń FedRAMP:
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Rozwiązanie chroniące przed złośliwym kodem zgłasza złośliwe oprogramowanie, zagrożenia i stan ochrony.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation rozwiązanie przechowuje elementy Runbook, uruchamia je i zarządza nimi.
-   [Security and Audit](../../security-center/security-center-intro.md): Pulpit nawigacyjny Security and Audit zawiera szczegółowe informacje o stanie zabezpieczeń zasobów dzięki zapewnieniu metryk w domenach zabezpieczeń, istotnych problemach, wykryciach, analizie zagrożeń i typowych zapytaniach zabezpieczeń.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Update Management](../../automation/automation-update-management.md): Rozwiązanie Update Management umożliwia klientom zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, w tym stanem dostępnych aktualizacji i procesem instalowania wymaganych aktualizacji.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Dzienniki aktywności platformy Azure](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.
-   [Change Tracking](../../azure-monitor/platform/collect-activity-logs.md): Rozwiązanie Change Tracking pozwala klientom łatwo identyfikować zmiany w środowisku.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w zasobach platformy Azure klientów.

## <a name="threat-model"></a>Model zagrożeń
Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/fedrampWAdfd) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![IaaS aplikacje sieci Web dla modelu zagrożeń FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS aplikacje sieci Web dla modelu zagrożeń FedRAMP")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności

W [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure FedRAMP o wysokiej odpowiedzialności klienta](https://aka.ms/blueprinthighcrm) znajduje się lista wszystkich kontroli zabezpieczeń wymaganych przez FedRAMP High Baseline. Macierz wskazuje, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta lub współdzielona między nimi.

[Macierz implementacji wysokiej kontroli aplikacji sieci Web strategia zabezpieczeń i zgodności z przepisami platformy Azure FedRAMP IaaS](https://aka.ms/blueprintwacim) zawiera listę wszystkich kontrolek zabezpieczeń wymaganych przez wysoką linię bazową FedRAMP. Macierz zawiera informacje o tym, które kontrolki są objęte architekturą aplikacji sieci Web IaaS, w tym szczegółowy opis sposobu, w jaki implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja składa się z plików konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API Azure Resource Manager do wdrażania zasobów na platformie Azure. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> To rozwiązanie jest wdrażane w Azure Government.

#### <a name="quickstart"></a>Szybki start
1. Sklonuj lub Pobierz [to](https://aka.ms/fedrampblueprintrepo) repozytorium GitHub do lokalnej stacji roboczej.

2. Uruchom skrypt programu PowerShell przed wdrożeniem: Azure-Blueprint/Predeploy/Orchestration_InitialSetup. ps1.

3. Kliknij poniższy przycisk, zaloguj się do Azure Portal, wprowadź wymagane parametry szablonu ARM i kliknij przycisk **Kup**.

    [![Wdrażanie na platformie Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web IaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zastrzeżenie

- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.  
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.  
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.  
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.  
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
