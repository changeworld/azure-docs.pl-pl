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
ms.openlocfilehash: b2c96509b2b8fd61c1814d5b4a015048eeb1807d
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391788"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Zabezpieczenia platformy Azure i zgodności planu — analiza danych dla SP NIST 800-171

## <a name="overview"></a>Przegląd
[Publikacja specjalne NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony informacji unclassified kontrolowanego (CUI), która znajduje się w organizacji i systemów informacyjnych nonfederal. SP NIST 800-171 ustanawia 14 rodzin wymagań dotyczących ochrony poufności CUI zabezpieczeń.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wytyczne pomagające klientom wdrażanie architektury analizy danych, na platformie Azure, która implementuje podzbiór NIST SP 800-171 kontrolki. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać zabezpieczeń i zgodności z wymaganiami i służy jako podstawa do tworzenia i skonfigurować własne rozwiązania do analizowania danych na platformie Azure.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do ich określonych wymagań i nie powinien być używany jako — w środowisku produkcyjnym. Wdrażania tej architektury bez żadnych modyfikacji jest za mała, aby całkowicie wymagań SP NIST 800-171. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia platforma analityczna, na którym klienci mogą tworzyć swoje własne narzędzia do analizy. Architektura referencyjna przedstawia przypadek użycia ogólny, gdzie klienci wejściowych danych za pośrednictwem importuje dane zbiorcze przez administratora SQL/danych lub aktualizacji danych operacyjnych za pomocą użytkownika usługi Operational. Zarówno workstreams dołączania usługi Azure Functions do importowania danych do usługi Azure SQL Database. Usługa Azure Functions musi zostać skonfigurowane przez klienta za pomocą witryny Azure portal do obsługi zadań importu unikatowe dla zaspokoi analizy.

Platforma Azure oferuje szereg usług raportowania i analizy dla klienta; Jednak to rozwiązanie obejmuje usługi Azure Machine Learning w połączeniu z usługi Azure SQL Database, aby szybko przeglądać dane i że dostarczymy wyniki szybciej za pośrednictwem inteligentniejsze modelowania danych. Usługę Azure Machine Learning jest formą uczenia maszynowego przeznaczone do zwiększenia szybkości zapytań o nowe relacje między zestawami danych odnajdywania. Po danych ma przeszkoleni za pośrednictwem kilku funkcji statystycznych, maksymalnie 7 zapytania dodatkowych pul (8 całkowity, łącznie z serwerem klienta) mogą być synchronizowane z tych samych modeli tabelarycznych rozłożyć obciążenie zapytań i zmniejszyć czas reakcji.

Rozszerzone analizy i raportowania usługi Azure SQL Database można skonfigurować za pomocą indeksy magazynu kolumn. Usługa Azure Machine Learning i Azure SQL Database można skalować w górę i w dół lub całkowicie wyłączony w odpowiedzi do użycia przez klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Gdy dane są przekazywane do usługi Azure SQL Database i odpowiednio przeszkoleni przez usługi Azure Machine Learning, jest rozłożona według użytkownika operacyjną i administratora SQL/danych dzięki usłudze Power BI. Usługa Power BI wyświetla dane intuicyjnie i zbiera informacje o różnych wiele zestawów danych, aby narysować bardziej szczegółowe analizy dotyczące. Jego wysoki stopień zdolności adaptacyjnych i Łatwa integracja z usługą Azure SQL Database gwarantuje, czy klienci mogą skonfigurować go do obsługi szerokiej gamy scenariuszy zgodnie z wymogami swoich potrzeb biznesowych.

Całe rozwiązanie jest oparte na usłudze Azure Storage klienci skonfigurować w witrynie Azure portal. Usługa Azure Storage szyfruje wszystkie dane przy użyciu szyfrowania usługi Storage, aby zachować poufność danych magazynowanych. Geograficzne magazyn nadmiarowy gwarantuje, że zdarzenie niepożądane, u klienta podstawowe centrum danych nie spowoduje utraty danych, zgodnie z drugą kopię będą przechowywane w oddzielnej lokalizacji setki mil natychmiast.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach jest używana do kontrolowania dostępu do wdrożonych zasobów, w tym do kluczy w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Security Center i Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego.

Usługa Azure SQL Database zwykle odbywa się za pomocą programu SQL Server Management Studio, który jest uruchamiany z komputera lokalnego, która jest skonfigurowana do dostępu do bazy danych SQL Azure za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi ExpressRoute. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi ExpressRoute do zarządzania i dane zaimportuj go do grupy zasobów**.

![Analiza danych dla SP NIST 800-171 referencyjny diagram architektury](images/nist171-analytics-architecture.png "analiza danych dla SP NIST 800-171 referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Dalsze szczegółowe informacje na temat [architektura wdrożenia](#deployment-architecture) sekcji.

- Application Insights
- Usługa Azure Active Directory
- Azure Data Catalog
- Usługa Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- W usłudze Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 sieci
    - (2) sieci prefiksie/24
    - (2) grupy zabezpieczeń sieci
- Pulpit nawigacyjny usługi Power BI

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa Azure Event Grid**: [usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) umożliwia klientom łatwe budowanie aplikacji mających architektury oparte na zdarzeniach. Użytkownicy wybierają zasobów platformy Azure, których chcieliby subskrybować, i nadaj program obsługi zdarzeń lub wysłać zdarzenia do punktu końcowego elementu webhook. Klientów można zabezpieczyć elementu webhook punkty końcowe, dodając parametry zapytania do adresu URL elementu webhook, podczas tworzenia subskrypcji zdarzeń. Usługa Azure Event Grid obsługuje tylko punkty końcowe HTTPS elementu webhook. Usługa Azure Event Grid umożliwia klientom kontrolowanie poziomu dostępu do różnych użytkowników może wykonać różne operacje zarządzania, takich jak lista subskrypcji zdarzeń, tworzenie nowych i generowanie kluczy. Kontrola dostępu oparta na rolach na platformie Azure korzysta z usługi Event Grid.

**Usługa Azure Functions**: [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) to usługa obliczeniowa bez serwera, która umożliwia użytkownikom uruchamianie kodu na żądanie bez konieczności jawnego przydzielania infrastruktury ani zarządzania tą infrastrukturą. Za pomocą usługi Azure Functions można uruchamiać skrypty lub fragmenty kodu w reakcji na różne zdarzenia.

**Usługa Azure Machine Learning**: [usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) to technika do nauki o danych, która zezwala na komputerach, aby korzystać z istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów.

**Usługa Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych łatwe do zrozumienia i prostsze do odnalezienia przez użytkowników, którzy zarządzają danymi. Typowe źródła danych można zarejestrowany, oznaczone i wyszukiwane dane. Dane pozostają w istniejącej lokalizacji, ale kopia ich metadanych jest dodawany do usługi Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

### <a name="virtual-network"></a>Sieć wirtualna
Ta architektura referencyjna definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16.

**Sieciowe grupy zabezpieczeń**: [sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie poszczególnych maszyn wirtualnych lub podsieci. Istnieją następujące grupy zabezpieczeń sieci:
  - Sieciową grupę zabezpieczeń usługi Active Directory
  - Sieciową grupę zabezpieczeń dla obciążenia

Każdej z grup zabezpieczeń sieci ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Usługa log Analytics jest połączony z [sieciowe grupy zabezpieczeń, diagnostyki](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: każdej podsieci jest skojarzona z sieciową grupą zabezpieczeń odpowiednich.

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występują za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Usługa Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Pomaga to chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez SP NIST 800-171 wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Usługa Azure SQL Database**: wystąpienie usługi Azure SQL Database używa następujące środki bezpieczeństwa bazy danych:
-   [Usługi AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Usługa Azure SQL Database jest skonfigurowany do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. To pomaga zmniejszyć dostępu w taki sposób, że poufne dane nie istnieje baza danych za pośrednictwem przed nieautoryzowanym dostępem. Klienci są zobowiązani do dostosowywania dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:
-   [Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze Azure Active Directory, w tym użytkowników uzyskujących dostęp do usługi Azure SQL Database.
-   Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane.  Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
-   [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i wsparcia podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomagają chronić dane klientów:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest sprzętu zabezpieczeń modułów chronionych 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Usługa Azure Security Center**: za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania.

**Log Analytics**: tych dzienników i dalszych są skonsolidowane w [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych w obszarach roboczych pakietu Operations Management Suite, który zezwala na wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z usługą Log Analytics, umożliwiając klientom zapytań usługi Log Analytics umożliwia dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące usługi Log Analytics [rozwiązań do zarządzania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check rozwiązanie ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): rozwiązania SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi Azure SQL Database. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Usługa Azure Monitor**: [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w ich systemie Azure zasoby.

**Usługa Application Insights**: [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/) to rozszerzalna Usługa zarządzania wydajnością aplikacji dla deweloperów sieci web na wielu platformach. W nim wykrywa anomalie wydajność oraz zaawansowanych narzędzi analitycznych ułatwia diagnozowanie problemów i aby zrozumieć, jak użytkownicy korzystają z aplikacją. Został zaprojektowany tak, aby pomóc użytkownikom w ciągłym udoskonalaniu wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/nist171-analytics-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Analiza danych dla modelu zagrożeń SP NIST 800-171](images/nist171-analytics-threat-model.png "analizy danych dla modelu zagrożeń SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i zgodności planu - SP NIST 800-171 klienta odpowiedzialność macierzy](https://aka.ms/nist171-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez SP NIST 800-171. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - macierzy implementacji kontroli analizy danych SP NIST 800-171](https://aka.ms/nist171-analytics-cim) zawiera informacje, na które SP NIST 800-171 kontrolki są rozwiązywane przez architektura analizy danych, w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowane do bezpieczne nawiązywanie połączenia z zasobami wdrożonymi w ramach tych danych analizy architektury referencyjnej. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces wyodrębniania, przekształcania, ładowania
[Program PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) można ładowanie danych do usługi Azure SQL Database bez konieczności oddzielnego wyodrębniania, przekształcania, załadowania lub zaimportowania narzędzia. Program PolyBase umożliwia dostęp do danych za pomocą zapytań T-SQL. Przy użyciu technologii PolyBase może służyć przez firmę Microsoft analizy biznesowej i stos analizy, a także narzędzia innych producentów są zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejący serwer Active Directory systemu Windows można zintegrować z usługą Azure Active Directory w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klienci mogą również powiązanie wdrożonej infrastrukturę usługi Active Directory (kontrolery domeny) istniejących usług Azure Active Directory, wprowadzając poddomeny lasu usługi Azure Active Directory wdrożone infrastrukturę usługi Active Directory.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
