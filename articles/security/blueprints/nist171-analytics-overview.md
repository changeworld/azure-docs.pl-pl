---
title: Zabezpieczenia platformy Azure i zgodności planu — analiza danych dla SP NIST 800-171
description: Zabezpieczenia platformy Azure i zgodności planu — analiza danych dla SP NIST 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f79ba9ae60454d4e73c914fc1c8af675a6d07d5d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60608870"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Zabezpieczenia platformy Azure i zgodności planu — analiza danych dla SP NIST 800-171

## <a name="overview"></a>Omówienie
[Publikacja specjalne NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony informacji unclassified kontrolowanego (CUI), która znajduje się w organizacji i systemów informacyjnych nonfederal. SP NIST 800-171 ustanawia 14 rodzin wymagań dotyczących ochrony poufności CUI zabezpieczeń.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wytyczne pomagające klientom wdrażanie architektury analizy danych, na platformie Azure, która implementuje podzbiór NIST SP 800-171 kontrolki. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać zabezpieczeń i zgodności z wymaganiami. Służy również jako podstawa do tworzenia i skonfigurować własne rozwiązania do analizowania danych na platformie Azure.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do swoich wymagań. Nie należy ich używać jako — w środowisku produkcyjnym. Wdrażania tej architektury bez żadnych modyfikacji jest za mała, aby całkowicie wymagań SP NIST 800-171. Klienci są zobowiązani do przeprowadzania rzutem i oceny zgodności od wszelkich rozwiązań do wbudowanych, który używa tej architektury. Wymagania mogą się różnić w oparciu o szczegóły implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia platforma analityczna, na którym klienci mogą tworzyć swoje własne narzędzia do analizy. Architektura referencyjna przedstawia przypadek użycia ogólnego. Klienci mogą używać go do wprowadzania danych za pomocą zbiorczego Importy danych przez administratora SQL/Data. Mogą również służy do wprowadzania danych za pomocą aktualizacji danych operacyjnych za pomocą użytkownika usługi operational. Zarówno workstreams dołączania usługi Azure Functions do importowania danych do usługi Azure SQL Database. Usługa Azure Functions musi zostać skonfigurowane przez klienta za pomocą witryny Azure portal do obsługi zadań importu unikatowe dla zaspokoi analizy.

Platforma Azure oferuje szereg usług raportowania i analizy dla klienta. To rozwiązanie wymaga usług Azure Machine Learning i usługi SQL Database, aby szybko przeglądać dane i że dostarczymy wyniki szybciej za pośrednictwem inteligentniejsze modelowania danych. Uczenie maszynowe jest przeznaczona do zwiększenia szybkości zapytań przez odnajdywanie nowe relacje między zestawami danych. Początkowo danych jest uczony przy użyciu kilku funkcji statystycznych. Później maksymalnie siedem zapytania dodatkowe pule mogą być synchronizowane za pomocą tych samych modeli tabelarycznych rozłożyć obciążenie zapytań i zmniejszyć czas reakcji. Serwera klienta ośmiu udostępnia łączna liczba pul zapytania.

Rozszerzone analiza i raportowanie bazy danych SQL można skonfigurować za pomocą indeksy magazynu kolumn. Uczenie maszynowe i bazy danych SQL można skalować w górę i w dół lub całkowicie wyłączony w odpowiedzi do użycia przez klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Po przekazane do bazy danych SQL i skonfigurowanych pod kątem przez uczenie maszynowe danych jest rozłożona się przez użytkownika operacyjną i administratora SQL/danych dzięki usłudze Power BI. Usługa Power BI wyświetla dane intuicyjnie i zbiera informacje o różnych wiele zestawów danych, aby narysować bardziej szczegółowe analizy dotyczące. Usługa Power BI ma wysoki stopień zdolności adaptacyjnych i łatwo zintegrować z usługą SQL Database. Klienci mogą skonfigurować go do obsługi szerokiej gamy scenariuszy, które są wymagane przez swoich potrzeb biznesowych.

Całe rozwiązanie jest oparte na usłudze Azure Storage klienci skonfigurować w witrynie Azure portal. Storage szyfruje wszystkie dane przy użyciu szyfrowania usługi Storage, aby zachować poufność danych magazynowanych. Magazyn geograficznie nadmiarowy gwarantuje, czy zdarzenie niepożądane, u klienta podstawowe centrum danych nie powoduje utraty danych. Druga kopia znajduje się w oddzielnej lokalizacji setki natychmiast mil.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach (Azure AD) (RBAC) służy do kontrolowania dostępu do wdrożonych zasobów. Te zasoby obejmują kluczy klienta w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Security Center i Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników. Kondycja systemu jest wyświetlany na pojedynczy pulpit nawigacyjny, który jest łatwy w użyciu.

Bazy danych SQL zwykle odbywa się za pomocą programu SQL Server Management Studio. Działa z maszyny lokalnej skonfigurowane do korzystania z bazy danych SQL za pośrednictwem bezpiecznego połączenia VPN lub usługi Azure ExpressRoute. *Firma Microsoft zaleca, aby skonfigurować połączenie sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania w grupie zasobów*.

![Analiza danych dla SP NIST 800-171 referencyjny diagram architektury](images/nist171-analytics-architecture.png "analiza danych dla SP NIST 800-171 referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [architektura wdrożenia](#deployment-architecture) sekcji.

- Application Insights
- Usługa Azure Active Directory
- Azure Data Catalog
- Usługa Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- W usłudze Azure Key Vault
- Azure Machine Learning
- Usługa Azure Monitor (Dzienniki)
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1) /16 Network
    - (2) /24 Networks
    - (2) grupy zabezpieczeń sieci
- Pulpit nawigacyjny usługi Power BI

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługi Azure Event Grid**: Za pomocą [usługi Event Grid](https://docs.microsoft.com/azure/event-grid/overview), klienci mogą łatwo tworzyć aplikacji za pomocą architektury oparte na zdarzeniach. Użytkownicy wybierają zasobów platformy Azure, jaki mu pasuje, aby subskrybować. Następnie zapewniają program obsługi zdarzeń lub wysłać zdarzenia do punktu końcowego elementu webhook. Klientów można zabezpieczyć elementu webhook punkty końcowe, dodając parametry zapytania do adresu URL elementu webhook, podczas tworzenia subskrypcji zdarzeń. Usługa Event Grid obsługuje tylko element webhook punktów końcowych HTTPS. Dzięki usłudze Event Grid klientów można kontrolować poziom dostępu określonych do różnych użytkowników, aby wykonywać różne operacje zarządzania. Użytkownicy mogą listy subskrypcji zdarzeń, tworzenie nowych i wygenerować klucze. Usługa Event Grid korzysta z funkcji RBAC platformy Azure.

**Usługa Azure Functions**: [Usługa Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) to bezserwerowa usługa obliczeniowa, która uruchamia kod na żądanie. Nie trzeba jawnego przydzielania infrastruktury ani zarządzania tą infrastrukturą. Za pomocą usługi Azure Functions można uruchamiać skrypty lub fragmenty kodu w reakcji na różne zdarzenia.

**Usługa Azure Machine Learning**: [Uczenie maszynowe](https://docs.microsoft.com/azure/machine-learning/service/) to technika do nauki o danych, która zezwala na komputerach, aby korzystać z istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów.

**Azure Data Catalog**: [Wykaz danych](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych jest łatwe odnajdywanie i zrozumienie przez użytkowników, którzy zarządzają danymi. Typowe źródła danych można zarejestrowany, oznaczone i wyszukiwane dane. Dane pozostają w istniejącej lokalizacji, ale kopia ich metadanych jest dodawany do usługi Data Catalog. Odwołanie do lokalizacji źródła danych jest dołączony. Metadane są indeksowane, aby ułatwić odnajdywanie za pomocą wyszukiwania dla każdego źródła danych. Indeksowanie sprawia, że jej zrozumiałe dla użytkowników, którzy je odnajdą.

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie poszczególnych maszyn wirtualnych lub podsieci. Istnieją następujące sieciowe grupy zabezpieczeń:
  - Sieciowa grupa zabezpieczeń usługi Active Directory
  - Sieciowa grupa zabezpieczeń dla obciążenia

Każdy z sieciowych grup zabezpieczeń ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Dzienniki platformy Azure Monitor jest podłączony do [diagnostyki sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzone z jego odpowiedniego sieciowej grupy zabezpieczeń.

### <a name="data-in-transit"></a>Dane przesyłane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Wszystkie do magazynu w witrynie Azure portal wykonywane transakcje za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Azure Storage**: Aby spełnić wymagania dla zaszyfrowanych danych w spoczynku, wszystkie [magazynu](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ta funkcja pomaga chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez SP NIST 800-171 wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [Szyfrowanie dysków](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) używa funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Azure SQL Database**: Wystąpienie bazy danych SQL wykorzystuje następujące środki bezpieczeństwa bazy danych:
-   [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Wykonuje się w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, do ochrony informacji w stanie spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywane dane, nie został narażone na nieautoryzowany dostęp.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania. Dostarcza alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i bazy danych nietypowe wzorce dostępu.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskać dostęp do danych w postaci zwykłego tekstu.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanym użytkownikom lub aplikacji. Można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak, aby dane poufne nie zakończyć bazy danych za pośrednictwem przed nieautoryzowanym dostępem. *Klienci są zobowiązani do dostosowywania ustawień stosować się do ich schemat bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:
-   [Usługa Azure AD](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze zarządzania katalogami i tożsamościami usługę zarządzania Microsoft. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze Azure AD i obejmują użytkowników, którzy uzyskują dostęp bazy danych SQL.
-   Do aplikacji uwierzytelniania za pomocą usługi Azure AD. Aby uzyskać więcej informacji, zobacz instrukcje [integrować aplikacje z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Szyfrowanie kolumny bazy danych również używa usługi Azure AD można uwierzytelnić aplikację do usługi SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) może służyć przez administratorów do definiowania uprawnień dostępu w zakresie. Dzięki niemu one można udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) może służyć przez klientów, aby zminimalizować liczbę użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane. Administratorzy mogą używać usługi Azure AD Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, just-in-time dostęp administracyjny w razie.
-   [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Konfiguruje automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości w organizacji. Bada go również podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Key Vault ułatwia ochronę kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje w chmurze i usług. Następujące funkcje usługi Key Vault pomagają chronić dane klientów:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest sprzętu chronionych modułu zabezpieczeń 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień przy użyciu RBAC.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Usługa Azure Security Center**: Za pomocą [usługi Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Usługa Security Center również uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

 Usługa Security Center korzysta z rozmaitych możliwości wykrywania powiadamia klientów o potencjalnych ataków, których platformą docelową ich środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , są wyzwalane, gdy zagrożenia lub podejrzanej aktywności ma miejsce. Klienci mogą używać [niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) do definiowania nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

 Usługa Security Center zapewnia zdarzeń i alertów zabezpieczeń uporządkowanych według priorytetu. Usługa Security Center sprawia, że prostsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej zagrożeń. Odpowiedzi na zdarzenia zespołów mogą korzystać z raportów, gdy ich badaniu i usuwaniu zagrożeń.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki magazynu, dzienników inspekcji usługi Key Vault i Dzienniki dostępu i zapory usługi Azure Application Gateway. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania, nawet do 730 dni, aby spełnić konkretne wymagania.

**Dzienniki platformy Azure Monitor**: Dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu danych są zorganizowane w w oddzielnych tabelach dla każdego typu danych w obszarach roboczych usługi Log Analytics. W ten sposób wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Usługa Security Center integruje się z dziennikami usługi Azure Monitor. Klienci mogą używać zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Daje klientom z priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne. Ponadto raporty odpowiadają liczby agentów i liczbę agentów, którzy przesłali danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Automatyzacja](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi SQL Database. Klienci mogą korzystać z automatyzacji [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązania, aby łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów. Organizacje mogą używać go do inspekcji, tworzyć alerty i archiwizować dane. Mogą również śledzić wywołań interfejsu API w swoich zasobów platformy Azure.

**Usługa Application Insights**: [Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/) to rozszerzalna Usługa zarządzania wydajnością aplikacji dla deweloperów sieci web na wielu platformach. W nim wykrywa anomalie wydajność oraz zaawansowanych narzędzi analitycznych. Narzędzia ułatwiają diagnozowanie problemów klientów i pomaganie im zrozumieć, jak użytkownicy korzystają z aplikacją. Został zaprojektowany tak, aby pomóc użytkownikom w ciągłym udoskonalaniu wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/nist171-analytics-tm) lub można znaleźć tutaj. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu wprowadzasz zmiany.

![Analiza danych dla modelu zagrożeń SP NIST 800-171](images/nist171-analytics-threat-model.png "analizy danych dla modelu zagrożeń SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i zgodności planu - SP NIST 800-171 klienta odpowiedzialność macierzy](https://aka.ms/nist171-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez SP NIST 800-171. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - macierzy implementacji kontroli analizy danych SP NIST 800-171](https://aka.ms/nist171-analytics-cim) zawiera informacje, na które SP NIST 800-171 kontrolki są rozwiązywane przez architektura analizy danych. Zawiera szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpieczne nawiązywanie połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej analizy danych. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można tworzyć wirtualne prywatne połączenie między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu. Klienci mogą używać połączenia o bezpiecznie "tuneli" wewnątrz zaszyfrowanych powiązanie ich z siecią i platformą Azure. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu sieci VPN jest przesyłane przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje inną opcję połączenia jeszcze bardziej bezpieczne. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Połączenia ExpressRoute łączyć się bezpośrednio z dostawcą usług telekomunikacyjnych klienta. W rezultacie dane nie podróżują, przez Internet i nie jest widoczne. Połączenia te oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia. 

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces wyodrębniania, przekształcania, ładowania
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można załadować dane do bazy danych SQL Database bez konieczności stosowania osobnych extract-transform-load lub zaimportować narzędzia. Program PolyBase umożliwia dostęp do danych za pomocą zapytań T-SQL. Stos analizy i analizy biznesowej firmy Microsoft oraz narzędzi innych firm, które są zgodne z programem SQL Server może służyć przy użyciu technologii PolyBase.

### <a name="azure-ad-setup"></a>Konfiguracja usługi Azure AD
[Usługa Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do pracowników, którzy współpracują ze środowiskiem. Lokalne usługi Active Directory można zintegrować z usługą Azure AD w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klientów może także powiązać wdrożonej infrastruktura usługi Active Directory (kontrolery domeny) do usługi Azure AD. Aby to zrobić, należy wdrożonej infrastrukturę usługi Active Directory poddomeny las usługi AD systemu Azure.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
