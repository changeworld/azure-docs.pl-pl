---
title: Zabezpieczeń platformy Azure i plan zgodności - analiza GDPR
description: Zabezpieczeń platformy Azure i plan zgodności - analiza GDPR
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 9114dea1f95198b5ff8dc1bf759be5d1179885fd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162183"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Zabezpieczeń platformy Azure i plan zgodności: analiza GDPR

## <a name="overview"></a>Przegląd
Ogólne rozporządzenia ochrony danych (GDPR) zawiera wiele wymagania w zakresie zbierania, przechowywania i przy użyciu informacji osobistych, w tym jak organizacje zidentyfikować i zabezpieczania danych osobowych, uwzględnienia wymogów dotyczących przezroczystości, wykrywania i raportu naruszenia danych osobowych i personel prywatności pociągu i innym pracownikom. GDPR zapewnia większą kontrolę nad jego danych osobowych użytkowników indywidualnych i nakłada wiele nowych obowiązków w organizacji, które zbierania, obsługiwać lub analizowania danych osobowych. Nowe zasady w organizacji, które oferują towarów nakłada GDPR i usług do osób w Unii Europejskiej (UE), lub że zbieranie i analizowanie danych powiązane mieszkańców Unii Europejskiej. Niezależnie od tego, w którym znajduje się organizacja stosuje się GDPR.

Zaprojektowana Azure z branży środków bezpieczeństwa i zasady zachowania poufności informacji w celu zapewnienia ochrony danych w chmurze, łącznie z kategoriami danych osobowych identyfikowane przez GDPR przez firmę Microsoft. Firmy Microsoft [warunków umowy](http://aka.ms/Online-Services-Terms) zatwierdzić Microsoft wymagania procesorów.

Ten Azure zabezpieczeń i zgodności plan zawiera wskazówki dotyczące wdrażania to architektura analizy danych na platformie Azure, która ułatwia spełnianie wymagań GDPR. To rozwiązanie przedstawia sposób, w którym klienci mogą spełnić zabezpieczeń i wymagań dotyczących zgodności i stanowi podstawę do tworzenia i skonfigurować własne rozwiązania do analizowania danych w usłudze Azure. Klienci może wykorzystywać o architekturze wzorcowej i wykonać firmy Microsoft [czterech kroków procesu](https://aka.ms/gdprebook) w podróży GDPR zgodności:
1. Odnajdywanie: Zidentyfikować istnieje które dane osobowe oraz jej lokalizację.
2. Zarządzanie: Regulują sposób osobistych danych jest używany i dostępny.
3. Ochrona: Ustanowić środki zabezpieczające zapobiec, wykrywanie i reagowanie na luki w zabezpieczeniach i naruszeń danych.
4. Raport: Przechowywania dokumentacji wymagane i zarządzanie żądaniami danych i naruszyć powiadomienia.

To architektura referencyjna, skojarzone z dokumentacją i modelu zagrożeń mają służyć jako podstawę do klientów dostosować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Pamiętaj o następujących kwestiach:
- Architektura zapewnia linii bazowej, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z GDPR.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia analytics platformy, na której klienci mogą tworzyć własne narzędzia do analizy. Architektura referencyjna przedstawiono przypadek użycia ogólny, gdzie klienci wprowadzanie danych za pośrednictwem Importy danych zbiorczego SQL/dane administratora lub aktualizacje danych operacyjnych za pomocą operacyjne użytkownika. Zarówno strumienie pracy dołączyć do nich usługi Azure Functions do importowania danych do bazy danych SQL Azure. Środowisko Azure Functions musi zostać skonfigurowane przez klienta za pośrednictwem portalu Azure w celu obsługi unikatowe dla każdego klienta własnych zadania importu wymagania analytics.

Azure oferuje wiele usług raportowania i analiz dla klienta; Jednak to rozwiązanie obejmuje usługi uczenie maszynowe Azure w połączeniu z bazą danych SQL Azure szybko przeglądanie danych i szybsze dostarczenie wyników za pomocą modelowania inteligentny danych klienta. Azure Machine Learning jest formą uczenia maszynowego przeznaczone do zwiększenia szybkości zapytania odnajdywania nowe relacje między zestawów danych. Po danych po zapoznaniu za pośrednictwem kilku funkcji statystycznych, maksymalnie 7 zapytania dodatkowe pule (całkowity, łącznie z serwera klient 8) mogą być synchronizowane z tej samej modeli tabelarycznych rozkładu obciążenia zapytania i zmniejsza czas odpowiedzi.

Rozszerzone analizy i raportowania można skonfigurować bazy danych SQL Azure z indeksami magazynu kolumn. Zarówno usługi Azure Machine Learning, jak i bazy danych SQL Azure można skalować w górę lub w dół lub całkowicie zamknąć w odpowiedzi do użycia przez klientów. Cały ruch SQL są szyfrowane przy użyciu protokołu SSL przez dodanie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem Azure zalecane jest stosowanie zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Gdy dane są przekazywane do bazy danych SQL Azure i uczenia przez usługi Azure Machine Learning, jest rozłożona operacyjne użytkownika i administratora SQL/danych z usługi Power BI. Power BI intuicyjnie wyświetla dane i pobiera razem informacji między wiele zestawów danych, aby narysować większego wglądu. Jego wysoki stopień zdolności adaptacyjnych i Łatwa integracja z bazy danych SQL Azure zapewnia klientów można skonfigurować go do obsługi szerokiej gamy scenariuszy zgodnie z ich potrzebami.

Całe rozwiązanie jest oparty na usłudze Azure Storage, której klienci skonfigurować w portalu Azure. Magazyn Azure szyfruje wszystkie dane przy użyciu szyfrowania usługi magazynu do zachowania poufności danych w stanie spoczynku. Geograficzne magazyn geograficznie nadmiarowy (GRS) zapewnia czy niekorzystny zdarzeń w centrum danych podstawowych klienta nie spowoduje utratę danych jako drugiej kopii będą przechowywane w innej lokalizacji setki mil optymalizacji.

Aby zwiększyć bezpieczeństwo tej architektury zarządza zasobami za pomocą usługi Azure Active Directory i usługi Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem Operations Management Suite (OMS) i Azure Monitor. Klienci skonfigurować obie te usługi monitorowania do przechwytywania dzienniki i wyświetlania kondycji systemu w jednej, łatwo nawigacji pulpitu nawigacyjnego.

Baza danych SQL Azure zwykle odbywa się za pośrednictwem SQL Server Management Studio (SSMS), w którym jest uruchomiona z komputera lokalnego skonfigurowane do korzystania z bazy danych SQL Azure za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi ExpressRoute. **Azure zaleca konfigurowania połączenia sieci VPN lub usługi ExpressRoute dla zarządzania oraz danych zaimportować do grupy zasobów architektura odwołania**.

![Diagram architektury odwołania analiza GDPR](images/gdpr-analytics-architecture.png?raw=true "analiza GDPR odwołania diagram architektury")

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrożenia są w [wdrożenia Architecture](#deployment-architecture) sekcji.

- Azure Functions
- Azure SQL Database
- Azure Machine Learning
- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Operations Management Suite (OMS)
- Azure Monitor
- Azure Storage
- Pulpit nawigacyjny usługi Power BI
- Azure Data Catalog
- Azure Security Center
- Application Insights
- Azure Event Grid
- (sieciowe grupy zabezpieczeń)

## <a name="deployment-architecture"></a>Architektura wdrażania
Poniższa sekcja zawiera szczegóły dotyczące wdrażania i wdrażania elementów.

**Azure siatki zdarzeń**
[siatki zdarzeń Azure](https://docs.microsoft.com/en-us/azure/event-grid/overview) umożliwia klientom łatwe tworzenie aplikacji za pomocą architektury oparty na zdarzeniach. Użytkownicy wybierają zasobów platformy Azure, które chciałby subskrybować i program obsługi zdarzeń lub wysłać zdarzenia do punktu końcowego elementu webhook. Klientów można zabezpieczyć elementu webhook punkty końcowe, dodając parametry zapytania do adresu URL elementu webhook podczas tworzenia subskrypcji zdarzeń. Siatka zdarzeń platformy Azure obsługuje tylko punktów końcowych HTTPS elementu webhook. Azure siatki zdarzeń umożliwia kontrolę dostępu do różnych użytkowników w różnych operacji zarządzania, takich jak listy zdarzeń subskrypcji, tworzenie nowych i generowania kluczy. Zdarzenie siatki wykorzystuje based kontroli dostępu (RBAC).

**Środowisko Azure Functions**
[usługi Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) jest usługą obliczeniowe bez serwera, która umożliwia użytkownikom na uruchamianie kodu na żądanie bez konieczności jawnego udostępniania lub zarządzanie infrastrukturą. Za pomocą usługi Azure Functions można uruchamiać skrypty lub fragmenty kodu w reakcji na różne zdarzenia.

**Usługa Azure Machine Learning**
[usługi Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/preview/) to technika analizy danych, która umożliwia komputerom używają istniejących danych w celu przewidywania przyszłych zachowań, rezultatów i trendów.

**Wykaz danych Azure**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sprawia, że źródła danych łatwo odnaleźć i zrozumieć przez użytkowników, którzy zarządzają danych. Wspólnych źródeł danych może być zarejestrowane, oznakowane i przeszukiwana danych osobowych. Dane pozostają w istniejącej lokalizacji, ale kopia metadanych jest dodawana do usługi Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

### <a name="virtual-network"></a>Sieć wirtualna
Informacje o architekturze wzorcowej definiuje się na przestrzeń adresową z 10.0.0.0/16 prywatnej sieci wirtualnej.

**Sieciowe grupy zabezpieczeń**: [grup NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu (ACL) akceptujące lub odrzucające ruchu w sieci wirtualnej. Grupy NSG mogą służyć do zabezpieczenia ruchu w podsieci lub poziomie maszyny Wirtualnej. Następujące grupy NSG istnieje:
  - Grupa NSG dla usługi Active Directory
  - Grupa NSG dla obciążeń

Każdy z grup NSG mieć określone porty i protokoły umożliwiający rozwiązanie może działać bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej grupy NSG:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i są przechowywane na koncie magazynu
  - Analiza dzienników OMS jest podłączony do [NSG dla diagnostyki](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: każdej podsieci jest skojarzony z jego odpowiedniej grupy NSG.

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja z centrach danych platformy Azure i domyślnie. Wystąpić wszystkich transakcji w magazynie Azure za pośrednictwem portalu Azure za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez szyfrowanie, bazy danych inspekcji i stosowania innych środków.

**Magazyn Azure** do spełnienia zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych osobowych wesprzeć zobowiązań zabezpieczeń organizacji i zdefiniowanych przez GDPR wymagań dotyczących zgodności.

**Szyfrowanie dysków Azure**
[szyfrowania dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dysków z danymi. Rozwiązanie integruje się z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

**Baza danych SQL Azure**: wystąpienie bazy danych SQL Azure używa następujących środków zabezpieczeń bazy danych:
-   [AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania konta magazynu platformy Azure.
-   Baza danych SQL Azure jest skonfigurowana do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i Zatrzymaj plików dzienników transakcji do ochrony informacji w. Funkcji TDE zawiera gwarantują, że przechowywane dane osobowe nie zostało poddane nieautoryzowanego dostępu.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwić wszystkie dostęp do serwerów baz danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, podając alertów zabezpieczeń bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, ataki i nietypowych bazy danych programu access wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że wrażliwe dane osobowe nigdy nie jest wyświetlana jako zwykłego tekstu w systemie bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) może służyć do przerwanie przetwarzania podmiotów danych, który zezwala użytkownikom na dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczać dane jako "Wycofany", do obsługi aplikacji logiki, aby uniemożliwić przetwarzanie skojarzone dane osobowe.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby przerwać przetwarzania.
- [SQL dynamicznych danych maskowania (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnieniem poufnych danych osobowych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. DDM można automatycznie wykryć potencjalnie poufnych danych i zaproponuje odpowiednie maski ma zostać zastosowany. Dzięki temu identyfikację danych osobowych kwalifikujących się do ochrony GDPR i zmniejszenia dostępu w taki sposób, że nie istnieje w bazie danych za pośrednictwem nieautoryzowanego dostępu. **Uwaga: Użytkownicy będą musieli ustawienia DDM stosować się do schematu bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych osobowych w środowisku platformy Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkownikom dostęp do bazy danych SQL Azure.
-   Aby aplikacja uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi AAD do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz temat jak [chronią poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom definiowanie uprawnień szczegółowych dostępu, aby udzielić tylko takiego dostępu, użytkownicy muszą wykonać swoje zadania. Zamiast nadanie co nieograniczonych uprawnień dla zasobów platformy Azure, Administratorzy mogą umożliwić tylko pewne akcje do uzyskiwania dostępu do danych osobowych. Subskrypcja dostęp jest ograniczony do administratora subskrypcji.
- [Zarządzanie tożsamości usługi AAD uprzywilejowanych (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji takich jak dane osobowe.  Administratorzy mogą używać usługi AAD Privileged Identity Management do odnajdywania, ograniczenia i monitorowanie tożsamości uprzywilejowanych oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, w czasie dostęp administracyjny w razie potrzeby.
-   [Ochronę tożsamości usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzanych zdarzenia podjęcie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych** w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom ochrony danych osobowych i dostęp do tych danych:
- Zasady dostępu zaawansowane są konfigurowane na potrzeby.
- Zasady dostępu do magazynu kluczy są zdefiniowane z minimalne uprawnienia wymagane do kluczy i kluczy tajnych.
- Wszystkie klucze i kluczy tajnych w magazynie kluczy mają datę wygaśnięcia.
- Wszystkie klucze w magazynie kluczy chronionych przez specjalne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest kluczem RSA HSM chronione 2048-bitowego.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień przy użyciu funkcji RBAC.
- Dzienniki diagnostyczne dla usługi Key Vault są włączone w okres przechowywania dla co najmniej 365 dni.
- Dozwolonych operacji kryptograficznych dla kluczy są ograniczone do tych wymagane.

**Alerty zabezpieczeń**: [Centrum zabezpieczeń Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) służy do monitorowania ruchu, zbierz dzienniki i analizować źródeł danych w przypadku zagrożeń. Ponadto Centrum zabezpieczeń Azure uzyskuje dostęp do istniejącej konfiguracji usług platformy Azure, konfiguracji i zalecenia usługi, aby poprawić stan zabezpieczeń i ochrony danych osobowych. Centrum zabezpieczeń Azure obejmuje [raportu analizy zagrożeń](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) każdego wykrytych zagrożeń ułatwiających zespoły odpowiedzi na zdarzenia zbadać i eliminowanie zagrożeń.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. OMS [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji. Dzienniki aktywności mogą ułatwić określenie inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows i magazynu obiektów Blob platformy Azure, tabele i kolejki dzienników.
- **Archiwizowanie dziennika**: wszystkie dzienniki diagnostyczne zapisu na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji okres przechowywania określonych 2 dni. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

Ponadto następujące rozwiązania OMS są dołączone jako część tej architektury:
-   [Ocena AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i udostępnia listę zalecenia dotyczące infrastruktury serwera wdrożonym z określonymi priorytetami.
-   [Ocena ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raportów dotyczących stanu złośliwego oprogramowania, zagrożeń i ochrony.
-   [Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie do automatyzacji Azure przechowuje, uruchamia i zarządza elementami runbook.
-   [Bezpieczeństwo i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów podając metryki na domen zabezpieczeń, godne uwagi problemy wykryć, analizy zagrożeń i typowych kwerend zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożonym opracowuje dla klientów.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie klienta aktualizacje zabezpieczeń systemu operacyjnego, w tym stanu o dostępnych aktualizacjach i proces instalacji wymaganych aktualizacji.
-   [Agent kondycji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): kondycja agenta rozwiązania zgłasza liczbę agentów są wdrażane i ich rozmieszczenie geograficzne, a także liczbę agentów, które odpowiadają i liczby agentów, które są przesyłanie danych operacyjnych.
-   [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązania analizy dziennika aktywności obsługuje Analiza dzienników Azure działania we wszystkich subskrypcji platformy Azure dla danego klienta.
-   [Śledzenie zmian](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking): śledzenie zmian rozwiązania pozwala łatwo zidentyfikować zmiany w środowisku.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) pomaga klientom śledzenia wydajności, obsługi zabezpieczeń i trendów umożliwia organizacjom inspekcji, tworzyć alerty i archiwizowanie danych, w tym śledzenia interfejsu API wywołuje w klientów zasobów platformy Azure.

**Usługa Application Insights**
[usługi Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/) jest rozszerzalną usługę zarządzania wydajności aplikacji (APM) dla deweloperów sieci web na wielu platformach. Używana do monitorowania aplikacji sieci web. Wykryje anomalii wydajności i obejmuje zaawansowane analizy narzędzia ułatwiające diagnozowanie problemów i zrozumieć, co faktycznie zrobić użytkownicy z aplikacją. Został zaprojektowany tak, aby ułatwić użytkownikom stale zwiększyć wydajność i użyteczność.

## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/gdprAnalyticsdfd) lub znajduje się poniżej. Ten model może ułatwić klientom punkty potencjalne ryzyko w infrastrukturze systemu, podczas wprowadzania zmian.

![Analiza GDPR zagrożenia modelu](images/gdpr-analytics-threat-model.png?raw=true "analiza GDPR zagrożenia modelu")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Azure zabezpieczeń i zgodności planu — macierzy odpowiedzialność klienta GDPR](https://aka.ms/gdprCRM) wymieniono obowiązki kontrolera i procesora dla wszystkich artykułów GDPR. Należy pamiętać, że dla usług Azure, klient zazwyczaj jest to kontroler i Microsoft zachowuje się jak procesor.

[Zabezpieczeń platformy Azure i plan zgodności - GDPR danych Analytics implementacji macierzy](https://aka.ms/gdprAnalytics) zawiera informacje, na które GDPR artykuły dotyczą architektury analizy danych, w tym szczegółowe opisy sposób Implementacja spełnia wymagania dotyczące każdego wymienionego artykułu.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieć VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi umożliwiać nawiązać bezpiecznego połączenia z zasobami wdrożenia w ramach tych danych analytics odwołania architektury. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Implementując bezpieczny tunel sieci VPN platformy Azure, można utworzyć wirtualnego prywatnej połączenie między siecią lokalną i sieci wirtualnej platformy Azure. To połączenie odbywa się przez Internet i pozwala bezpiecznie "tunnel" informacji wewnątrz zaszyfrowanych łącza między siecią a Azure klienta. Sieci VPN typu lokacja-lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. [Trybu tunelowania IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizm szyfrowania.

Ponieważ ruchu w tunelu VPN przechodzą przez Internet, korzystając z sieci VPN lokacja lokacja, firma Microsoft oferuje opcji inny, jeszcze bardziej bezpieczne połączenia. Usługa Azure ExpressRoute jest dedykowanych sieci WAN łącza między Azure i lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Ponadto jest bezpośrednie połączenie klienta telekomunikacyjnych dostawcy, dlatego dane nie są przesyłane w Internecie i w związku z tym nie jest uwidaczniana do niego.

Najlepsze rozwiązania dotyczące wdrażania sieci hybrydowe bezpiecznego, która rozszerza sieci lokalnej na platformie Azure są [dostępne](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces wyodrębniania przekształcenia ładowania (ETL)
[Program PolyBase](https://docs.microsoft.com/en-us/sql/relational-databases/polybase/polybase-guide) można załadować dane do bazy danych SQL Azure, bez konieczności oddzielnego ETL lub zaimportować narzędzia. Aparat PolyBase zezwala na dostęp do danych za pomocą zapytania T-SQL. Przy użyciu programu PolyBase może służyć firmy Microsoft analizy biznesowej i stosu analizy, a także narzędzia innych firm zgodne z programem SQL Server.

### <a name="azure-active-directory-setup"></a>Instalacja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) jest niezbędne do zarządzania wdrażania i inicjowania obsługi dostępu do personelu interakcji ze środowiskiem. Istniejący Windows Server Active Directory można zintegrować z usługi AAD w [cztery kliknięć](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Klientów można również powiązanie wdrożonej infrastruktura usługi Active Directory (kontrolery domeny) do istniejącej usługi AAD dokonując wdrożonej infrastruktura usługi Active Directory jest poddomeną las usługi AAD.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.
 - Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.
 - Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
 - Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
