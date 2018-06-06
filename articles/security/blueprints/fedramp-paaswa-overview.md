---
title: Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web PaaS dla FedRAMP
description: Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web PaaS dla FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 20aa842fb8168bc28a388c817f4e4eedbdd63ebd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34728027"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Zabezpieczeń platformy Azure i plan zgodności: aplikacja sieci Web PaaS dla FedRAMP

## <a name="overview"></a>Przegląd

[Federalne ryzyka i Program do zarządzania autoryzacji (FedRAMP)](https://www.fedramp.gov/) to program całej dla instytucji rządowych Stanów Zjednoczonych, który zawiera standardowe podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania dla chmury produkty i usługi. Ten zabezpieczeń platformy Azure i plan zgodności zawiera wskazówki dotyczące jak dostarczać platformy Microsoft Azure jako architekturę usługa (PaaS), która pomaga implementuje podzbiór wysokiej FedRAMP kontrolki. To rozwiązanie zawiera wskazówki dotyczące wdrażania i konfigurowania zasobów Azure dla typowych architektura referencyjna prezentacja sposoby, w którym klienci mogą spełniać określone wymagania zabezpieczeń i zgodności i podstawą dla klientów Tworzenie i konfigurowanie ich własnych rozwiązań na platformie Azure.

To architektura referencyjna, przewodniki implementacji skojarzonym formancie i modele zagrożeń mają służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez żadnych modyfikacji jest niewystarczający w celu całkowicie spełniają wymagania wysokiej FedRAMP linii bazowej. Pamiętaj o następujących kwestiach:
- Architektura zapewnia linii bazowej, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia architektura referencyjna dla aplikacji sieci web PaaS z wewnętrznej bazy danych z bazy danych SQL Azure. Aplikacja sieci web znajduje się w izolowanej Azure środowiska usługi aplikacji, która jest prywatny, dedykowanego środowiska w centrum danych Azure. Obciążenia środowiska równoważy ruchu dla aplikacji sieci web na maszynach wirtualnych zarządzanych przez platformę Azure. Taka architektura także grup zabezpieczeń sieci, brama aplikacji w usłudze Azure DNS i usługi równoważenia obciążenia. Ponadto usługi Operations Management Suite udostępnia w czasie rzeczywistym analizy kondycji systemu i zabezpieczeń. **Azure zaleca się Konfigurowanie połączenia sieci VPN i ExpressRoute do importowania danych i zarządzania w podsieci architektura odwołania.**

![Aplikacja sieci Web PaaS dla diagram architektury FedRAMP odwołania](images/fedramp-paaswa-architecture.png?raw=true) "PaaS aplikacji sieci Web diagram architektury odwołania FedRAMP"

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrażania znajdują się w [wdrożenia architecture](#deployment-architecture) sekcji.

- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) zapora aplikacji sieci web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - odbiornik: port 443
- Sieć wirtualna Azure
- Grupy zabezpieczeń sieci
- System DNS platformy Azure
- Azure Storage
- Operations Management Suite
- Azure Monitor
- V2 środowiska usługi aplikacji
- Azure Load Balancer
- Aplikacja sieci Web platformy Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architektura wdrażania
Poniższa sekcja zawiera szczegóły dotyczące wdrażania i wdrażania elementów.

**Usługa Azure Resource Manager**: [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) służy do pracy z zasobami w rozwiązaniu jako grupa. Klientów można wdrożyć, zaktualizować lub usunięcie wszystkich zasobów dla rozwiązania w jednej, skoordynowanej operacji. Klienci Użyj szablonu w celu wdrożenia i tego szablonu można używać w różnych środowiskach testowania, przemieszczania i produkcji. Menedżer zasobów udostępnia zabezpieczeń, inspekcji i tagowania funkcje ułatwiające klientom zarządzanie zasobami po wdrożeniu.

**Środowisko usługi aplikacji w wersji 2**: [środowiska usługi aplikacji Azure (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) to funkcja usługi aplikacji, która udostępnia środowisko pełni izolowanym środowisku, bezpiecznego uruchamiania aplikacji usługi App Service na dużą skalę.

ASEs są izolowane mają zostać wykonane tylko jednego klienta, aplikacji i zawsze są wdrażane w sieci wirtualnej. Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacji może nawiązywać bezpiecznych połączeń o dużej szybkości za pośrednictwem sieci wirtualnej do zasobów firmy lokalnie.

Użyj ASEs dla tej architektury są dozwolone dla formantów/konfiguracje:

- Hostowanie w zabezpieczonej sieci wirtualnej platformy Azure i reguły zabezpieczeń sieci
- Skonfigurowano certyfikatu z podpisem własnym ILB dla komunikacji HTTPS ASE
- [Wewnętrzny tryb równoważenia obciążenia](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Wyłącz [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Zmień [szyfrowania TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Formant [ruchu przychodzącego ruchu N/W portach](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Zapora aplikacji sieci Web — Ogranicz danych](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zezwalaj na [ruchu bazy danych SQL Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

[Wskazówki i zalecenia](#guidance-and-recommendations) sekcja zawiera dodatkowe informacje na temat ASEs.

**Aplikacja sieci Web Azure**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie aplikacji sieci web w języku programowania wybranych przez nich bez zarządzanie infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje zarówno system Windows, jak i system Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Visual Studio Team Services oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Virtual Network
Architektura definiuje prywatnej sieci wirtualnej z 10.200.0.0/16 przestrzeni adresowej.

**Sieciowe grupy zabezpieczeń**: [sieciowej grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które lub odmawiają ruchu w sieci wirtualnej. Grupy NSG mogą służyć do zabezpieczenia ruchu w podsieci lub poziomie maszyny Wirtualnej. Następujące grupy NSG istnieje:
- 1 NSG dla bramy aplikacji
- 1 NSG dla środowiska usługi aplikacji
- 1 NSG dla bazy danych Azure SQL

Każdy z grup NSG mieć określone porty i protokoły umożliwiający rozwiązanie może działać bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej grupy NSG:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i są przechowywane na koncie magazynu
  - Analiza dzienników OMS jest podłączony do [NSG dla diagnostyki](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: każdej podsieci jest skojarzony z jego odpowiedniej grupy NSG.

**Usługa Azure DNS**: System nazw domen lub DNS, jest odpowiedzialny za tłumaczenia (lub rozpoznawania) nazwę witryny sieci Web lub usługi na adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) jest usługą hostingu domen DNS, która udostępnia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Obsługując domen na platformie Azure, użytkownicy mogą zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń jak innymi usługami Azure. Usługa DNS platformy Azure obsługuje również prywatnej domen DNS.

**Moduł równoważenia obciążenia Azure**: [modułu równoważenia obciążenia Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umożliwia klientom skalowania ich aplikacji i utworzyć wysoką dostępność usług. Usługi równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego, jak również wychodzących i zapewnia małe opóźnienia, wysokiej przepływności i skalowanie do milionów przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja z centrach danych platformy Azure i domyślnie. Wystąpić wszystkich transakcji w magazynie Azure za pośrednictwem portalu Azure za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, bazy danych inspekcji i stosowania innych środków.

**Magazyn Azure**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Szyfrowanie dysków Azure**
[szyfrowania dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dysków z danymi. Rozwiązanie integruje się z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

**Baza danych SQL Azure**: wystąpienie bazy danych SQL Azure używa następujących środków zabezpieczeń bazy danych:
-   [AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania konta magazynu platformy Azure.
-   Baza danych SQL Azure jest skonfigurowana do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i Zatrzymaj plików dzienników transakcji do ochrony informacji w.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwić wszystkie dostęp do serwerów baz danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, podając alertów zabezpieczeń bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, ataki i nietypowych bazy danych programu access wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby przerwać przetwarzania.
- [Baza danych SQL dane dynamiczne maskowanie](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) może odbywać się po wdraża architektura referencyjna struktury. Klienci muszą dostosować dane dynamiczne maskowanie ustawienia, aby stosować się do schematu bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają tożsamość możliwości zarządzania w środowisku platformy Azure:
-   [Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkownikom dostęp do bazy danych SQL Azure.
-   Aby aplikacja uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz temat jak [chronią poufne dane w bazie danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji, a dostęp do zasobów można ograniczyć na podstawie roli użytkownika.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji.  Administratorzy mogą używać usługi AAD Privileged Identity Management do odnajdywania, ograniczenia i monitorowanie tożsamości uprzywilejowanych oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, w czasie dostęp administracyjny w razie potrzeby.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzane zdarzenia podjęcie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych** w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom ochrony danych i dostęp do tych danych:
- Zasady dostępu zaawansowane są konfigurowane na potrzeby.
- Zasady dostępu do magazynu kluczy są zdefiniowane z minimalne uprawnienia wymagane do kluczy i kluczy tajnych.
- Wszystkie klucze i kluczy tajnych w magazynie kluczy mają datę wygaśnięcia.
- Wszystkie klucze w magazynie kluczy chronionych przez specjalne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest kluczem RSA HSM chronione 2048-bitowego.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla usługi Key Vault są włączone w okres przechowywania dla co najmniej 365 dni.
- Dozwolonych operacji kryptograficznych dla kluczy są ograniczone do tych wymagane.

**Brama aplikacji w** architekturę zmniejsza ryzyko luk w zabezpieczeniach z zapory aplikacji sieci Web i zestaw reguł OWASP włączona przy użyciu bramy aplikacji. Dodatkowe funkcje obejmują:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z ruleset OWASP 3.0
- Włącz [rejestrowania diagnostyki](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondy kondycji niestandardowych](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnić dodatkową ochronę i powiadomień. Centrum zabezpieczeń Azure zapewnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja
Usługi Operations Management suite udostępnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. Operations Management suite [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji. Dzienniki aktywności mogą ułatwić określenie inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i dzienniki bramy aplikacji dostępu i zapory.
- **Archiwizowanie dziennika**: zapisu wszystkich dzienników diagnostycznych na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji. Przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, aby spełnić wymagania organizacji dotyczące przechowywania. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

Ponadto zawarte w ramach tej architektury są następujące rozwiązania Operations Management suite:
-   [Usługi Active directory do oceny](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i udostępnia listę zalecenia dotyczące infrastruktury serwera wdrożonym z określonymi priorytetami.
-   [Ocena ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raportów dotyczących stanu złośliwego oprogramowania, zagrożeń i ochrony.
-   [Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie do automatyzacji Azure przechowuje, uruchamia i zarządza elementami runbook. W tym rozwiązaniu elementów runbook pomocy, zbierz dzienniki z usługi Application Insights i bazy danych SQL Azure.
-   [Bezpieczeństwo i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów podając metryki na domen zabezpieczeń, godne uwagi problemy wykryć, analizy zagrożeń i typowych kwerend zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożonym opracowuje dla klientów.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie klienta aktualizacje zabezpieczeń systemu operacyjnego, w tym stanu o dostępnych aktualizacjach i proces instalacji wymaganych aktualizacji.
-   [Agent kondycji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): kondycja agenta rozwiązania zgłasza liczbę agentów są wdrażane i ich rozmieszczenie geograficzne, a także liczbę agentów, które odpowiadają i liczby agentów, które są przesyłanie danych operacyjnych.
-   [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązania analizy dziennika aktywności obsługuje Analiza dzienników Azure działania we wszystkich subskrypcji platformy Azure dla danego klienta.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/automation/automation-change-tracking): śledzenie zmian rozwiązania pozwala łatwo zidentyfikować zmiany w środowisku.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, obsługi zabezpieczeń oraz trendów umożliwia organizacjom inspekcji, tworzyć alerty i archiwizowanie danych, w tym śledzenia wywołań interfejsu API w przypadku klientów zasobów platformy Azure.

## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/fedrampPaaSWebAppDFD) lub znajduje się poniżej. Ten model może ułatwić klientom punkty potencjalne ryzyko w infrastrukturze systemu, podczas wprowadzania zmian.

![Aplikacja sieci Web PaaS dla modelu zagrożeń FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "PaaS komputerze sieci Web dla modelu zagrożeń FedRAMP")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i plan zgodności - FedRAMP wysokiej klienta odpowiedzialność macierzy](https://aka.ms/blueprinthighcrm) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Macierzy oznacza wdrożenia każdego formantu odpowiedzialność firmy Microsoft, odbiorcy, czy jest udostępniane między nimi.

[Zabezpieczeń platformy Azure i plan zgodności - FedRAMP PaaS aplikacji sieci Web wysokiej implementacji macierzy kontroli](https://aka.ms/fedrampPaaSWebAppCIM) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Macierz zawiera informacje, na którym formanty są objęte architektury aplikacji sieci web PaaS, w tym szczegółowy opis sposobu implementacji spełnia wymagania dotyczące każdej kontrolki objęte usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieć VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana bezpiecznie nawiązać połączenia z zasobami wdrożone w ramach tej architektury odwołania aplikacji sieci web PaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Implementując bezpieczny tunel sieci VPN platformy Azure, można utworzyć połączenie sieci VPN między siecią lokalną i sieci wirtualnej platformy Azure. To połączenie odbywa się przez Internet i pozwala bezpiecznie "tunnel" informacji wewnątrz zaszyfrowanych łącza między siecią a Azure klienta. Sieci VPN typu lokacja-lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizm szyfrowania.

Ponieważ ruchu w tunelu VPN przechodzą przez Internet, korzystając z sieci VPN lokacja lokacja, firma Microsoft oferuje opcji inny, jeszcze bardziej bezpieczne połączenia. Usługa Azure ExpressRoute jest dedykowanych sieci WAN łącza między Azure i lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Ponadto jest bezpośrednie połączenie klienta telekomunikacyjnych dostawcy, dlatego dane nie są przesyłane w Internecie i w związku z tym nie jest uwidaczniana do niego.

Najlepsze rozwiązania dotyczące wdrażania sieci hybrydowe bezpiecznego, która rozszerza sieci lokalnej na platformie Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
