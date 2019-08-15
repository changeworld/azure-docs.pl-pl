---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla FedRAMP
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: b74373201db26405653584fc4000993b9d6fdf43
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946829"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Aplikacja sieci Web PaaS dla FedRAMP

## <a name="overview"></a>Omówienie

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) to Stany Zjednoczone program dla instytucji rządowych, który zapewnia ustandaryzowane podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów i usług w chmurze. Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące sposobu dostarczania architektury Microsoft Azure platformy jako usługi (PaaS), która pomaga zaimplementować podzestaw FedRAMP wysokiej kontroli. To rozwiązanie zapewnia wskazówki dotyczące wdrażania i konfigurowania zasobów platformy Azure na potrzeby wspólnej architektury referencyjnej, pokazując sposoby, w których klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności, a także służy jako podstawa dla klientów Twórz i Konfiguruj własne rozwiązania na platformie Azure.

Ta architektura referencyjna, przewodniki związane z implementacją kontroli i modele zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań i nie powinny być używane w środowisku produkcyjnym. Wdrożenie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania FedRAMP wysokiego planu bazowego. Pamiętaj o następujących kwestiach:
- Architektura udostępnia linię bazową, która ułatwia klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
To rozwiązanie zapewnia architekturę referencyjną dla aplikacji sieci Web PaaS z zapleczem Azure SQL Database. Aplikacja sieci Web jest hostowana w izolowanym Azure App Service Environment, czyli prywatnym, dedykowanym środowisku w centrum danych platformy Azure. Obciążenie środowiskowe równoważy ruch dla aplikacji sieci Web na maszynach wirtualnych zarządzanych przez platformę Azure. Ta architektura obejmuje również sieciowe grupy zabezpieczeń, Application Gateway, Azure DNS i Load Balancer. Ponadto Azure Monitor zapewnia analizę kondycji systemu w czasie rzeczywistym. **Platforma Azure zaleca skonfigurowanie połączenia sieci VPN lub ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![Aplikacja sieci Web PaaS dla diagramu architektury referencyjnej FedRAMP](images/fedramp-paaswa-architecture.png?raw=true "Aplikacja sieci Web PaaS dla diagramu architektury referencyjnej FedRAMP")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Zapora aplikacji sieci Web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Odbiornik: port 443
- Sieć wirtualna platformy Azure
- Grupy zabezpieczeń sieci
- Usługa DNS platformy Azure
- Azure Storage
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Aplikacja sieci Web platformy Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia klientom współpracę z zasobami w rozwiązaniu jako Grupa. Klienci mogą wdrażać, aktualizować i usuwać wszystkie zasoby rozwiązania w ramach jednej, skoordynowanej operacji. Klienci używają szablonu do wdrożenia i ten szablon może współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Menedżer zasobów zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające klientom zarządzanie zasobami po wdrożeniu.

**App Service Environment v2**: [Azure App Service Environment (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) to funkcja App Service, która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji App Service na dużą skalę.

Środowisk ASE są izolowane tylko do uruchamiania aplikacji jednego klienta i są zawsze wdrażane w sieci wirtualnej. Klienci mają szczegółową kontrolę nad ruchem przychodzącym i wychodzącym ruchu sieciowego aplikacji, a aplikacje mogą nawiązywać bezpieczne połączenia za pośrednictwem sieci wirtualnych z lokalnymi zasobami firmowymi.

Użycie środowisk ASE dla tej architektury jest dozwolone dla następujących kontrolek/konfiguracji:

- Host w ramach zabezpieczonej Virtual Network platformy Azure i reguł zabezpieczeń sieci
- Środowisko ASE skonfigurowane z certyfikatem ILB z podpisem własnym na potrzeby komunikacji przy użyciu protokołu HTTPS
- [Tryb wewnętrznego równoważenia obciążenia](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Wyłącz [protokół TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Zmień [szyfr TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Kontrola [portów ruchu przychodzącego (N/s](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md) )
- [Zapora aplikacji sieci Web — ograniczanie danych](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Zezwalaj na [ruch Azure SQL Database](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

Sekcja [wskazówki i zalecenia](#guidance-and-recommendations) zawiera dodatkowe informacje na temat środowisk ASE.

**Aplikacja internetowa platformy Azure**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie aplikacji sieci Web w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy kontroli dostępu, które zezwalają na ruch w sieci wirtualnej lub odmawiają go. Sieciowych grup zabezpieczeń może służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Następująca sieciowych grup zabezpieczeń istnieje:
- 1 sieciowej grupy zabezpieczeń dla Application Gateway
- 1 sieciowej grupy zabezpieczeń dla App Service Environment
- 1 sieciowej grupy zabezpieczeń dla Azure SQL Database

Każdy sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdego sieciowej grupy ZABEZPIECZEŃu są włączone następujące konfiguracje:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Dzienniki Azure Monitor są połączone z [diagnostyką sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z odpowiadającą jej sieciowej grupy zabezpieczeń.

**Azure DNS**: System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozwiązanie) nazwy witryny sieci Web lub usługi na adres IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure umożliwia użytkownikom Zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. Azure DNS obsługuje również prywatne domeny DNS.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umożliwia klientom skalowanie aplikacji i tworzenie wysokiej dostępności dla usług. Load Balancer obsługuje przychodzące oraz scenariusze wychodzące oraz zapewnia małe opóźnienia i wysoką przepływność oraz skaluje się do milionów przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Dane przesyłane
Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Wszystkie transakcje do usługi Azure Storage za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) wykorzystuje funkcję BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:
-   [Uwierzytelnianie i autoryzacja w usłudze AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   Azure SQL Database jest skonfigurowany do używania [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje szyfrowanie w czasie rzeczywistym oraz odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane na bieżąco.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
-   [Always Encrypted kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umożliwiają użytkownikom Definiowanie zasad ograniczających dostęp do danych w celu ich dalszego przetwarzania.
- [SQL Database dynamiczną maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) można wykonać po wdrożeniu architektury referencyjnej. Klienci będą musieli dostosować ustawienia dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają funkcje zarządzania tożsamościami w środowisku platformy Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to oparta na chmurze usługa zarządzania tożsamościami i tożsamość firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w usłudze AAD, w tym użytkownicy uzyskujący dostęp do Azure SQL Database.
-   Uwierzytelnianie w aplikacji odbywa się przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia precyzyjne zarządzanie dostępem na platformie Azure. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji, a dostęp do zasobów można ograniczyć na podstawie roli użytkownika.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pozwala klientom zminimalizować liczbę użytkowników, którzy mają dostęp do określonych informacji.  Administratorzy mogą używać usługi AAD Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczami tajnymi** Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić dane i uzyskiwać dostęp do takich danych:
- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Typ klucza to chroniony przez moduł HSM 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Application Gateway** Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway z zaporą aplikacji sieci Web, a włączono zestaw reguł OWASP. Dodatkowe możliwości obejmują:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja
Azure Monitor zapewnia obszerne rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. Zbiera i analizuje dane generowane przez zasoby na platformie Azure i w środowiskach lokalnych.
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory.
- **Archiwizowanie dzienników**: Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji. Te dzienniki łączą się z dziennikami Azure Monitor na potrzeby przetwarzania, przechowywania i raportowania pulpitów nawigacyjnych.

Ponadto w ramach tej architektury są dołączone następujące rozwiązania do monitorowania:
-   [Ocena usługi Active Directory](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Rozwiązanie chroniące przed złośliwym kodem zgłasza złośliwe oprogramowanie, zagrożenia i stan ochrony.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation rozwiązanie przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Application Insights i Azure SQL Database.
-   [Security and Audit](../../security-center/security-center-intro.md): Pulpit nawigacyjny Security and Audit zawiera szczegółowe informacje o stanie zabezpieczeń zasobów dzięki zapewnieniu metryk w domenach zabezpieczeń, istotnych problemach, wykryciach, analizie zagrożeń i typowych zapytaniach zabezpieczeń.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
-   [Update Management](../../automation/automation-update-management.md): Rozwiązanie Update Management umożliwia klientom zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, w tym stanem dostępnych aktualizacji i procesem instalowania wymaganych aktualizacji.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Dzienniki aktywności platformy Azure](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.
-   [Change Tracking](../../automation/change-tracking.md): Rozwiązanie Change Tracking pozwala klientom łatwo identyfikować zmiany w środowisku.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w zasobach platformy Azure klientów.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/fedrampPaaSWebAppDFD) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![PaaS aplikacje sieci Web dla modelu zagrożeń FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "PaaS aplikacje sieci Web dla modelu zagrożeń FedRAMP")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
W [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure FedRAMP o wysokiej odpowiedzialności klienta](https://aka.ms/blueprinthighcrm) znajduje się lista wszystkich kontroli zabezpieczeń wymaganych przez FedRAMP High Baseline. Macierz wskazuje, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta lub współdzielona między nimi.

[Macierz strategia zabezpieczeń i zgodności z przepisami platformy Azure-FedRAMP PaaS webapp wysoka kontrola](https://aka.ms/fedrampPaaSWebAppCIM) zawiera wszystkie kontrolki zabezpieczeń wymagane przez FedRAMP High Baseline. Macierz zawiera informacje o tym, które kontrolki są objęte architekturą aplikacji sieci Web PaaS, w tym szczegółowy opis sposobu, w jaki implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć połączenie sieci VPN między siecią lokalną i Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
