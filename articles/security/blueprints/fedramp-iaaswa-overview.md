---
title: Zabezpieczenia platformy Azure i zgodności planu — IaaS w sieci Web aplikacji dla programu FedRAMP
description: Zabezpieczenia platformy Azure i zgodności planu — IaaS w sieci Web aplikacji dla programu FedRAMP
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
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586138"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Zabezpieczenia platformy Azure i zgodności planu: Aplikacja sieci Web IaaS dla FedRAMP

## <a name="overview"></a>Omówienie

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) to program obowiązkowo USA, zapewniający ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów w chmurze i usługi. Ten zabezpieczeń platformy Azure i zgodności planu automatyzacji zawiera wskazówki dotyczące wdrażania zgodnych ze standardami FedRAMP infrastruktury jako usługi (IaaS) środowiska odpowiednie dla prostej aplikacji sieci web dostępnym z Internetu. To rozwiązanie pozwala zautomatyzować wdrażanie i konfigurowanie zasobów platformy Azure dla typowych architektury referencyjnej, demonstrując sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności i służy jako podstawa klienci mogą tworzyć i Skonfiguruj swoich rozwiązań na platformie Azure. Rozwiązanie implementuje podzbiór formantów z linią bazową programowi FedRAMP High, oparte na SP NIST 800-53. Aby uzyskać więcej informacji na temat wymagań FedRAMP i to rozwiązanie, zobacz [dokumentację zgodności](#compliance-documentation).
> [!NOTE]
> Wdraża to rozwiązanie do usługi Azure Government.

Ten zabezpieczeń platformy Azure i zgodności planu automatyzacji automatycznie wdraża architektury referencyjnej IaaS sieci web aplikacji przy użyciu wstępnie skonfigurowane zabezpieczenia, aby ułatwić klientom osiągnąć zgodność z wymaganiami FedRAMP. To rozwiązanie składa się z szablonami usługi Azure Resource Manager i skryptów programu PowerShell, które przewodnik dotyczący wdrażania zasobów i konfiguracji.

Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku, bez żadnych modyfikacji nie wystarcza do całkowicie wymagań programowi FedRAMP High linii bazowej. Pamiętaj o następujących kwestiach:
- Ta architektura zapewnia plan bazowy pomagała klientom platformy Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania rzutem i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić w oparciu szczegółowe informacje na temat implementacji każdego klienta.

Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, Obejrzyj to [wideo](https://aka.ms/fedrampblueprintvideo) objaśnia i demonstruje wprowadzone jej wdrożenia.

Kliknij przycisk [tutaj](https://aka.ms/fedrampblueprintrepo) instrukcje wdrożenia.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
Tego rozwiązania powoduje wdrożenie architektury referencyjnej dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura obejmuje warstwy internetowej, warstwy danych, usługi Active Directory infrastruktury, usługa Application Gateway i modułu równoważenia obciążenia. Maszyny wirtualne wdrożone w warstwach sieć web i danych są konfigurowane w zestawie dostępności, a wystąpienia programu SQL Server powinny być skonfigurowane w celu zapewnienia wysokiej dostępności grupy dostępności AlwaysOn. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego. Host bastionu zapewnia bezpieczne połączenie, Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów. **Azure zaleca się skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Aplikacja internetowa IaaS dla FedRAMP referencyjny diagram architektury](images/fedramp-iaaswa-architecture.png?raw=true "aplikacja internetowa IaaS dla FedRAMP referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegóły architektura wdrożenia znajdują się w [architektura wdrożenia](#deployment-architecture) sekcji.

- Azure Virtual Machines
    - (1) host bastionu (system Windows Server 2016 Datacenter)
    - (2) usługi active Directory kontrolera domeny (Windows Server 2016 Datacenter)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/IIS (system Windows Server 2016 Datacenter)
- Zestawy dostępności
    - (1) kontrolery domeny w usłudze active Directory
    - (1) węzły klastra SQL
    - (1) w sieci web/IIS
- Azure Virtual Network
    - sieci wirtualne /16 (1)
    - podsieci o prefiksie/24 w (5)
    - Ustawienia DNS są ustawione na obu kontrolerach domeny
- Azure Load Balancer
- Azure Application Gateway
    - (1) brama aplikacji zapory aplikacji sieci Web jest włączona
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Odbiornika: port 443
- Azure Storage
    - (7) konta dla magazynu geograficznie nadmiarowego
- Monitor w chmurze platformy Azure
- Magazyn usługi Recovery Services
- W usłudze Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Usługa Azure Monitor (Dzienniki)

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji przedstawiono elementy projektowania i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który zapewnia bezpieczne połączenie dla administratorów, które uzyskują dostęp do wdrożonych zasobów. Bastionu hosta sieciowa grupa zabezpieczeń zezwala na połączenia tylko na porcie TCP 3389 dla protokołu RDP. Klienci mogą skonfigurować dalsze hostem bastionu w celu spełnienia system organizacji w zakresie wzmacniania ochrony.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie służy do wdrażania zasobów w architekturę z podsieci oddzielnych sieci web, bazy danych podsieci, podsieci usługi Active Directory i podsieci zarządzania wewnątrz sieci wirtualnej. Podsieci są logicznie oddzielone reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch pomiędzy podsieciami, aby tylko to niezbędne do systemu oraz funkcji zarządzania.

Można znaleźć konfiguracji [sieciowe grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone za pomocą tego rozwiązania. Klienci mogą skonfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako wskazówki.

Każdej z tych podsieci ma dedykowany sieciową grupę zabezpieczeń (NSG):
- 1 sieciowej grupy zabezpieczeń w usłudze Application Gateway (LBNSG)
- 1 sieciowej grupy zabezpieczeń dla hostem bastionu (MGTNSG)
- 1 sieciowej grupy zabezpieczeń dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 sieciowej grupy zabezpieczeń dla serwerów SQL (SQLNSG)
- 1 sieciowej grupy zabezpieczeń dla warstwy internetowej (WEBNSG)

**Podsieci**: Każda podsieć jest skojarzone z jego odpowiedniego sieciowej grupy zabezpieczeń.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane magazynowane przy użyciu kilku środków szyfrowania.

**Azure Storage**: Aby spełnić wymagania dotyczące szyfrowania danych magazynowanych, wszystkie konta magazynu używają [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**Program SQL Server**: Program SQL Server jest skonfigurowany do używania [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych i plików dziennika do ochrony informacji w stanie spoczynku. Funkcja TDE zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.

Klienci mogą również skonfigurować następujące środki zabezpieczeń programu SQL Server:
-   [Usługi AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą dostęp do danych w postaci zwykłego tekstu.
-   [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) może odbywać się po architektura referencyjna wdraża. Klienci będą musieli dostosować dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

**Usługa Azure Disk Encryption**: Usługa Azure Disk Encryption jest używany do zaszyfrowanych dysków maszyny wirtualnej Windows IaaS. [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowana z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają tożsamość możliwości zarządzania w środowisku platformy Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania.
- Używanie programu Azure AD można wykonać uwierzytelniania do aplikacji sieci web wdrożonych przez klienta. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowane na platformie Azure. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji, a dostęp do zasobów może być ograniczona w zależności od roli użytkownika.
- Wdrożone wystąpienie usługi Active Directory IaaS zapewnia zarządzanie tożsamościami na poziomie systemu operacyjnego dla wdrożonych maszyn wirtualnych IaaS.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Usługa Azure Key Vault pomaga zarządzać kluczami szyfrowania dysków maszyn wirtualnych IaaS i wpisów tajnych dla tej architektury referencyjnej.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne to zabezpieczeń platformy Azure i zgodności planu automatyzacji są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie wdraża też rozwiązania usługi Azure Automation za pomocą których można tworzyć wdrożenia aktualizacji do wdrożenia poprawki na serwerach Windows, gdy potrzebne.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania, można skonfigurować alerty, gdy znany złośliwego lub niechcianego oprogramowania próbuje zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web (WAF) i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure. Rozwiązanie zostanie wdrożone wszystkie warstwy internetowej, a warstwa danych maszyn wirtualnych w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone między wieloma izolowanymi klastrami sprzętowymi Aby zwiększyć dostępność. Ponadto, to rozwiązanie służy do wdrażania maszyn wirtualnych programu SQL Server w zestawie dostępności jako [zawsze włączonej grupy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Zawsze na dostępność grupy funkcja zapewnia funkcje wysokiej dostępności i odzyskiwania po awarii.

**Magazyn usług Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, umożliwiając skraca czas ich przywracania.

**Monitor w chmurze**: [Monitor w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu Failover w systemie Windows Server 2016, która korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, takich jak każdy inny monitor kworum ma głos i można uczestniczyć w obliczeniach kworum, ale używa standardowych publicznej usługi Azure Blob Storage. Pozwala to wyeliminować koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Dzienniki platformy Azure Monitor zapewnia szczegółowe rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. [Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) rozwiązanie zbiera i analizuje dane generowane przez zasoby na platformie Azure i środowiskach lokalnych.

- **Dzienniki aktywności:**  [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne:**  [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory.
- **Archiwizowanie dziennika:**  Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania. Te dzienniki nawiązać dzienniki usługi Azure Monitor do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym.

Ponadto następujące rozwiązania do monitorowania są instalowane w ramach tej architektury. Należy zwrócić uwagę na to, że odpowiada klienta do skonfigurowania tych rozwiązań, aby było zgodne z FedRAMP środki kontroli bezpieczeństwa:
-   [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Ocena ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Rozwiązanie chroniące przed złośliwym kodem raporty dotyczące złośliwego oprogramowania, zagrożeń i ochronę stanu.
-   [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Rozwiązania usługi Azure Automation przechowuje, uruchamia i zarządza elementami runbook.
-   [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Pulpit nawigacyjny zabezpieczenia i inspekcja zawiera ogólne informacje o stanie zabezpieczeń zasobów, zapewniając metryki w przypadku domen zabezpieczeń, problemy godne uwagi, wykrywania, analizy zagrożeń i typowe zapytania dotyczące zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Rozwiązanie Update Management umożliwia zarządzanie klientami z aktualizacjami zabezpieczeń systemu operacyjnego, w tym stan dostępnych aktualizacji i procesu instalacji wymaganych aktualizacji.
-   [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Change Tracking umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Usługa Azure Monitor**
[usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w zasobach platformy Azure dla klientów.

## <a name="threat-model"></a>Model zagrożeń
Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/fedrampWAdfd) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Aplikacja internetowa IaaS modelu zagrożeń FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "aplikacja internetowa IaaS dla FedRAMP model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i plan zgodność — FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez programowi FedRAMP High linii bazowej. Macierz oznacza wykonania każdego formantu odpowiedzialność firmy Microsoft, klienta i czy jest współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i plan zgodność — FedRAMP IaaS sieci Web aplikacji wysokiej implementacji macierzy kontroli](https://aka.ms/blueprintwacim) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez programowi FedRAMP High linii bazowej. Macierz zawiera informacje, na którym formanty są objęte architektury aplikacji sieci web IaaS, w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Ten zabezpieczeń platformy Azure i zgodności planu automatyzacji składa się z pliki konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API usługi Azure Resource Manager do wdrażania zasobów na platformie Azure. Dostępne są instrukcje wdrożenia są szczegółowo [tutaj](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Wdraża to rozwiązanie do usługi Azure Government.

#### <a name="quickstart"></a>Szybki start
1. Klonuj lub Pobierz [to](https://aka.ms/fedrampblueprintrepo) repozytorium GitHub na lokalnej stacji roboczej.

2. Uruchom skrypt programu PowerShell przed wdrożeniem: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Kliknij poniższy przycisk, zaloguj się do witryny Azure portal, podaj wymagane parametry szablonu ARM i kliknij przycisk **zakupu**.

    [![Wdrażanie na platformie Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacja internetowa IaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja-lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.  
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.  
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.  
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
