---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla rozporządzenia RODO
description: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla rozporządzenia RODO
services: security
author: jomolesk
ms.assetid: 229759a1-f984-4985-a3fd-3719a7d1c8ff
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 805c983c0c2db4256dade641f32363ca6514023c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600815"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-gdpr"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla rozporządzenia RODO

## <a name="overview"></a>Przegląd
Ogólne rozporządzenie o ochronie danych (RODO) zawiera wiele wymagań dotyczących zbierania, przechowywania i przy użyciu informacji osobistych, w tym jak organizacjom identyfikowanie i zabezpieczanie danych osobowych, uwzględnić wymagania dotyczące przezroczystości, wykrywania i raportu naruszenia danych osobowych i szkolenie zachowania personel i innych pracowników. RODO zapewnia większą kontrolę nad ich danych osobowych użytkowników indywidualnych i nakłada wiele nowych zobowiązań w organizacji, które zbierania, obsługiwać lub analizować dane osobowe. Rozporządzenie GDPR nakłada nowe zasady dotyczące organizacji oferujących towary i usługi osobom w Unii Europejskiej (UE) lub że zbierać i analizować dane związane z osobami mieszkającymi w UE. Niezależnie od tego, w którym znajduje się organizacja stosuje się GDPR.

Firma Microsoft zaprojektowała platformy Azure za pomocą wiodących w branży środki bezpieczeństwa i zasady zachowania poufności informacji w celu ochrony danych w chmurze, w tym kategorie danych osobowych identyfikowane przez rozporządzenie GDPR. Firmy Microsoft [warunków umowy](https://aka.ms/Online-Services-Terms) zatwierdzić Microsoft wymagania procesorów.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wskazówki dotyczące wdrażania platformy jako usługi (PaaS) środowiska odpowiednie dla prostej aplikacji sieci web dostępnym z Internetu. To rozwiązanie przedstawia sposób, w którym klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności rozporządzenia rodo i służy jako podstawa do tworzenia i konfigurowanie własnych rozwiązań aplikacji sieci web PaaS na platformie Azure. Klienci mogą korzystać z tej architektury referencyjnej i postępuj zgodnie z firmy Microsoft [czterech kroków procesu](https://aka.ms/gdprebook) w podróży do zgodności z rozporządzeniem GDPR:
1. Odnajdywanie: Zidentyfikować dane osobowe, które istnieje, oraz jej lokalizację.
2. Zarządzanie: Zarządzanie danymi osobowymi jak jest używane i dostępne.
3. Ochrona: Należy ustanowić kontrole bezpieczeństwa, aby zapobiec, wykrywanie ich i odpowiadanie na luki w zabezpieczeniach i naruszeń danych.
4. Raport: Wymagane dokumentacji i zarządzanie żądaniami danych i naruszenia powiadomienia.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Pamiętaj o następujących kwestiach:
- Architektura zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z RODO.
- Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia architekturę referencyjną dla aplikacji sieci web PaaS za pomocą wewnętrznej bazy danych Azure SQL Database. Aplikacja sieci web znajduje się w izolowanej Azure App Service Environment, który jest prywatnym, dedykowanym środowisku w centrum danych platformy Azure. Środowisko równoważy obciążenie dla aplikacji sieci web na maszynach wirtualnych zarządzanych przez platformę Azure. Taka architektura obejmuje również sieciowych grup zabezpieczeń, usługa Application Gateway, usługi Azure DNS i modułu równoważenia obciążenia. Ponadto usługa Azure Monitor udostępnia analizy w czasie rzeczywistym o kondycji systemu. **Azure zaleca się skonfigurowanie połączenia sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![PaaS Web Distributed dla rozporządzenia RODO referencyjny diagram architektury](images/gdpr-paaswa-architecture.png?raw=true "PaaS Web Distributed dla rozporządzenia RODO referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegóły architektura wdrożenia znajdują się w [architektura wdrożenia](#deployment-architecture) sekcji.

- Usługa Azure Active Directory (AAD)
- W usłudze Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) brama aplikacji zapory aplikacji sieci Web jest włączona
        - Tryb zapory: Zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Odbiornika: port 443
- Sieć wirtualna Azure
- (sieciowe grupy zabezpieczeń)
- System DNS platformy Azure
- Azure Storage
- Azure Monitor
- Application Insights
- Azure Security Center
- Usługa App Service Environment w wersji 2
- Azure Load Balancer
- Aplikacja sieci Web platformy Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa Azure Resource Manager**: [Usługa Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia klientom pracę z zasobami w rozwiązaniu jako grupa. Klientów można wdrożyć, zaktualizować lub usunąć wszystkie zasoby dla rozwiązania w jednej, skoordynowanej operacji. Klienci dla wdrożenia wykonuje się przy użyciu szablonu, którego można używać w różnych środowiskach, takich jak testowanie, etap przejściowy i produkcja. Menedżer zasobów zapewnia zabezpieczeń, inspekcji i znakowania funkcje, aby pomóc klientom w zarządzaniu zasobami po wdrożeniu.

**Usługa App Service Environment w wersji 2**: [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) to funkcja usługi App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji usługi App Service w dużej skali.

Za pomocą środowisk ASE są izolowane do uruchamiania tylko jednego klienta aplikacji i są zawsze wdrażane w sieci wirtualnej. Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacje mogą nawiązywać bezpieczne szybkie połączenia za pośrednictwem wirtualnych sieci lokalnych zasobów firmowych.

Użycie środowiska ASE w tej architekturze są dozwolone w przypadku kontrolek/konfiguracje:

- Hostowanie w zabezpieczonej sieci wirtualnej platformy Azure i reguł zabezpieczeń sieciowych
- Środowisko ASE jest skonfigurowane przy użyciu certyfikatu wewnętrznego modułu równoważenia obciążenia z podpisem własnym dla komunikacji HTTPS
- [Tryb równoważenia obciążenia wewnętrznego](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (tryb 3)
- Wyłącz [protokołu TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Zmiana [szyfrowania TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrolka [ruchu N/M porty wejściowe](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Zapora aplikacji sieci Web — ograniczanie danych](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zezwalaj na [ruch usługi Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Aplikacja sieci Web platformy Azure**: [Usługa Azure App Service](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie wybranych przez nich aplikacji sieci web w języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Virtual Network
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają list kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:
- 1 sieciowej grupy zabezpieczeń w usłudze Application Gateway
- 1 sieciowej grupy zabezpieczeń dla środowiska App Service
- 1 sieciowej grupy zabezpieczeń dla usługi Azure SQL Database

Sieciowe grupy zabezpieczeń mają określonych portów i protokołów, otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Usługa log Analytics jest połączony z [diagnostyki sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzone z jego odpowiedniego sieciowej grupy zabezpieczeń.

**Usługa Azure DNS**: System nazw domen lub DNS, odpowiada za tłumaczenia (lub rozpoznawanie) nazwę witryny sieci Web lub usługi na jej adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to Usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure, użytkownicy mogą zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co inne usługi platformy Azure. Usługa DNS platformy Azure obsługuje również prywatne domen DNS.

**Usługa Azure Load Balancer**: [Usługa Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pozwala klientom na skalowanie swoich aplikacji i zapewniać wysoką dostępność usług. Modułu równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego, a także ruchu wychodzącego i zapewnia małe opóźnienia i wysoką przepływność oraz skaluje nawet miliony przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występują za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Usługa Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych osobowych w odniesieniu do bezpieczeństwa organizacji i zdefiniowane przez rozporządzenie GDPR wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**
[usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Usługa Azure SQL Database**: Wystąpienie usługi Azure SQL Database wykorzystuje następujące środki bezpieczeństwa bazy danych:
-   [Usługi AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Usługa Azure SQL Database jest skonfigurowany do używania [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Funkcja TDE zapewnia pewność, że przechowywane dane osobowe nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że wrażliwe dane osobowe nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) może służyć do zaprzestać przetwarzania, których dane dotyczą, ponieważ pozwala ono użytkownikom dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczania tagami danych jako "Wycofany", do obsługi logiki aplikacji, aby uniemożliwić przetwarzanie skojarzone dane osobowe.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby zaprzestać przetwarzania.
- [SQL Database dynamicznych danych maskowania (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych osobowych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. DDM może automatycznie odnajdywać potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dzięki temu identyfikację danych osobowych kwalifikujących się GDPR ochrony oraz do zmniejszenia dostępu w taki sposób, że nie istnieje w bazie danych za pośrednictwem przed nieautoryzowanym dostępem. **Uwaga: Klienci będą musieli dostosować ustawienia DDM stosować się do ich schemat bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych osobowych w środowisku platformy Azure:
-   [Usługa Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkowników uzyskujących dostęp do usługi Azure SQL Database.
-   Do aplikacji uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi AAD do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych osobowych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługi AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane osobowe.  Administratorzy mogą używać usługi AAD Privileged Identity Management, aby odkryć, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Ochrona tożsamości usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i bada podejrzanych zdarzenia, które można podjąć odpowiednie działania, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi** rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić dane osobowe i dostęp do tych danych:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest HSM chronione 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień, korzystając z modelu RBAC.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Alerty zabezpieczeń**: [Usługa Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) służy do monitorowania ruchu, zbieranie dzienników i analizowanie źródeł danych pod kątem zagrożeń. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejącej konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych osobowych. Usługa Azure Security Center obejmuje [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia badaniu i usuwaniu zagrożeń.

**Usługa Application Gateway** architektury zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci Web (WAF) i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia także system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługa Azure Monitor zapewnia szczegółowe rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. Zbiera i analizuje dane generowane przez zasoby na platformie Azure i środowiskach lokalnych.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory.
- **Archiwizowanie dziennika**: Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania. Te dzienniki połączyć z usługą Azure Log Analytics do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym.

Ponadto następujące rozwiązania do monitorowania, są uwzględnione w ramach tej architektury:
-   [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Ocena ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Rozwiązanie chroniące przed złośliwym kodem raporty dotyczące złośliwego oprogramowania, zagrożeń i ochronę stanu.
-   [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Rozwiązania usługi Azure Automation przechowuje, uruchamia i zarządza elementami runbook. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi Application Insights i Azure SQL Database.
-   [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Pulpit nawigacyjny zabezpieczenia i inspekcja zawiera ogólne informacje o stanie zabezpieczeń zasobów, zapewniając metryki w przypadku domen zabezpieczeń, problemy godne uwagi, wykrywania, analizy zagrożeń i typowe zapytania dotyczące zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Rozwiązanie Update Management umożliwia zarządzanie klientami z aktualizacjami zabezpieczeń systemu operacyjnego, w tym stan dostępnych aktualizacji i procesu instalacji wymaganych aktualizacji.
-   [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/automation/automation-change-tracking): Rozwiązanie Change Tracking umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Usługa Azure Monitor**
[usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w zasobach platformy Azure dla klientów.

**Usługa Application Insights**
[usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna Usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci web na wielu platformach. Usługa Application Insights wykrywa anomalie wydajność i klienci mogą używać go do monitorowania aplikacji sieci web. Obejmuje ona zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów z klientami i aby zrozumieć, jak użytkownicy w rzeczywistości korzystają z aplikacji. Został zaprojektowany tak, aby pomóc klientom w ciągłym udoskonalaniu wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/gdprPaaSdfd) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![PaaS Web Distributed modelu zagrożeń RODO](images/gdpr-paaswa-threat-model.png?raw=true "PaaS Web Distributed modelu zagrożeń RODO")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i zgodności planu — RODO klienta odpowiedzialność macierzy](https://aka.ms/gdprCRM) Wyświetla obowiązki kontrolera i procesora dla wszystkich artykułów RODO. Należy pamiętać, usług systemu Azure, klient jest zazwyczaj kontrolera i Microsoft działa jako procesor.

[Zabezpieczeń platformy Azure i zgodności planu — RODO PaaS Web Application implementacji Matrix](https://aka.ms/gdprPaaSWeb) zawiera informacje, na które RODO artykuły są rozwiązywane przez architektury aplikacji sieci web PaaS, w tym szczegółowe opisy jak wdrożenia spełnia wymagania części każdego artykułu objętego usługą.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web PaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

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
