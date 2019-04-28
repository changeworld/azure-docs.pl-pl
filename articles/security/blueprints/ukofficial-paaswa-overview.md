---
title: Zabezpieczenia platformy Azure i zgodności planu - hostowania obciążeń oficjalne UK aplikacji sieci Web PaaS
description: Zabezpieczenia platformy Azure i zgodności planu - hostowania obciążeń oficjalne UK aplikacji sieci Web PaaS
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 95e10f4727de239016a2e3c88571e74267e3967b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109331"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Zabezpieczenia platformy Azure i zgodności planu: Hosting w przypadku obciążeń oficjalne UK aplikacji sieci Web PaaS

## <a name="azure-security-and-compliance-blueprints"></a>Plany zabezpieczeń i zgodności platformy Azure

Plany usługi Azure składają się z dokumentów ze wskazówkami dotyczącymi i szablonów do automatyzacji wdrażania architektury oparte na chmurze oferowanie rozwiązań do scenariuszy, które mają akredytacji lub wymagań dotyczących zgodności. Plany usługi Azure są wskazówki i automatyzacji kolekcji szablonów, które umożliwiają klientom przyspieszanie wprowadzania swoich celów biznesowych za pomocą udostępniania architektura foundation, można rozszerzać w celu spełnienia dodatkowych wymogów, Microsoft Azure.

## <a name="overview"></a>Omówienie

Tych samych zabezpieczeń platformy Azure i zgodności planu zapewnia wskazówki i automatyzacji skrypty dopasowane do dostarczania Microsoft Azure [platforma jako usługa (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) hostowane odpowiednie do obsługi obciążeń sklasyfikowane Architektura aplikacji sieci web jako [UK-OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Ta klasyfikacja zabezpieczeń obejmuje większość informacji utworzone lub przetworzone przez usługę z sektora publicznego. Obejmuje to operacje biznesowe procedur i usług, które if utracone, skradzione lub opublikowane na nośniku, z których część może mieć konsekwencje szkodliwe. Profil typowych zagrożeń oficjalne klasyfikacji jest bardzo podobne do firm prywatnych, który udostępnia cenne informacje i usługi. OFICJALNE UK oszacowano, trzeba chronić dane Rządu Zjednoczonego Królestwa lub usług zagrożenie lub naruszenia przez osoby atakujące przy użyciu ograniczonych możliwości i zasobów takich jak (ale nie jest ograniczona do) hactivists single-issue grup wykorzystanie dochodzeniowych dziennikarzy właściwe poszczególnych przed hakerami i większość osób karnego i grup.

Ten plan został przejrzany, Zjednoczone Królestwo National Cybernetycznymi zabezpieczeń Centrum (NCSC) i zgodne z zasadami zabezpieczeń NCSC 14 chmury.

Architektura korzysta z platformy Azure [platforma jako usługa](https://azure.microsoft.com/overview/what-is-paas/) składniki, aby dostarczać środowisko, które pozwala uniknąć kosztownego i skomplikowanego kupowania licencji na oprogramowanie, zarządzanie podstawowej infrastruktury aplikacji i oprogramowania pośredniczącego lub narzędzi deweloperskich i innych zasobów. Klienci zarządzać aplikacjami i usługami, które opracowują, skupiając się na dostarczaniu wartości biznesowej, podczas gdy Microsoft Azure zarządza innych zasobów platformy Azure, takie jak maszyny wirtualne, Magazyn i sieć, umieszczając jedną z [dzielenia odpowiedzialność za](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) dla zarządzanie infrastrukturą na platformie Azure. [Usługi Azure App Services](https://azure.microsoft.com/services/app-service/) oferuje automatyczne skalowanie, wysoka dostępność, obsługuje Windows i Linux oraz umożliwia używanie wdrożeń zautomatyzowanych z usługi GitHub, DevOps platformy Azure lub dowolnego repozytorium Git jako usług domyślnych. Za pomocą usług aplikacji, deweloperów może skupić się na dostarczaniu wartości biznesowej bez konieczności zarządzania infrastrukturą. Można tworzyć nowe aplikacje sieci web Java, PHP, Node.js, Python, HTML lub C# od podstaw lub migrację istniejącej chmurze lub lokalnych aplikacji sieci web do usług Azure App Services (mimo że dokładnego aby upewnić się, wydajność jest wymagany w należytą starannością i testowania).

Ten plan koncentruje się na aprowizacji od bezpiecznej podstawy [platforma jako usługa](https://azure.microsoft.com/overview/what-is-paas/) interfejs internetowy dla publicznego, a także użytkownikom zaplecza. Takiego scenariusza projektowego planu uważa, że korzystanie z platformy Azure, obsługiwana usług sieci web gdzie publiczny użytkownika mogą bezpiecznie przesyłać, wyświetlić oraz zarządzać poufnych danych; również czy wsteczny operator pakietu office lub dla instytucji rządowych bezpiecznie może przetwarzać dane poufne, które zostało przesłane przez użytkownika publicznego. Przypadki użycia, w tym scenariuszu mogą obejmować:

- Przesyłanie zwracania podatku, za pomocą operatora dla instytucji rządowych, przetwarzanie przesłanych informacji, użytkownik
- Użytkownika żądającego usługi za pośrednictwem aplikacji opartych na sieci web, z użytkownikiem zaplecza sprawdzania poprawności, a potem dostarczając usługi; lub
- Wyszukiwanie użytkowników i wyświetlania w domenie publicznej informacje dotyczące usługi dla instytucji rządowych.

Za pomocą [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szablonów i skryptów interfejsu wiersza polecenia platformy Azure, planu wdrażania środowiska pasującą do Wielkiej Brytanii National Cybernetycznymi zabezpieczeń Centrum (NCSC) 14 [zasad bezpieczeństwa w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) Centrum zabezpieczeń internetowych (CIS) i [środki kontroli bezpieczeństwa krytycznych](https://www.cisecurity.org/critical-controls.cfm). NCSC zaleca ich zasad bezpieczeństwa w chmurze użyte przez klientów do oceny właściwości zabezpieczeń usługi i lepiej zrozumieć podziału wspólnej odpowiedzialności klienta i dostawcy. Firma Microsoft udostępnia informacje w odniesieniu do każdego z tych zasad, aby pomóc lepiej zrozumieć podziału obowiązków. Ta architektura i odpowiednie szablony usługi Azure Resource Manager są obsługiwane przez ten oficjalny dokument firmy Microsoft, [14 kontroli zabezpieczeń Cloud w Wielkiej Brytanii w chmurze przy użyciu platformy Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ta architektura zostało przejrzane przez NCSC i zgodne z Wielkiej Brytanii NCSC 14 chmury zasad bezpieczeństwa, umożliwiając w ten sposób automatyki możliwość zobowiązań zgodności przy użyciu usług w chmurze globalnie i w Wielkiej Brytanii w organizacji z sektora publicznego Microsoft Azure w chmurze. Ten szablon umożliwia wdrożenie infrastruktury dla obciążenia. Kod aplikacji i obsługa warstwą biznesową a oprogramowanie warstwy danych musi być zainstalowane i skonfigurowane przez klientów. Dostępne są instrukcje wdrożenia są szczegółowo [tutaj](https://aka.ms/ukofficial-paaswa-repo/).

Ten plan jest architektura foundation. Naszym klientom za pomocą tego planu jako podstawa potrzeby ich obciążeń opartych na sieci web oficjalne klasyfikacji i rozwiń węzeł Szablony i zasoby z obowiązujących ich wymagań. Ten plan jest oparta na zasadach [planu aplikacji sieci Web w Wielkiej Brytanii urzędowy IaaS Three-Tier](https://aka.ms/ukofficial-iaaswa) zaoferować naszym klientom [infrastruktura jako usługa (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) i opcji wdrożenia PaaS do obsługi obciążeń opartych na sieci web.

Aby wdrożyć ten plan, subskrypcję platformy Azure jest wymagana. Jeśli nie masz subskrypcji platformy Azure, możesz zarejestrować się szybko i łatwo bez dodatkowych opłat: Rozpocznij pracę z platformą Azure. Kliknij przycisk [tutaj](https://aka.ms/ukofficial-paaswa-repo/) instrukcje wdrożenia.

## <a name="architecture-and-components"></a>Architektura i składniki

Ten plan zapewnia rozwiązanie w środowisku chmury platformy Azure, które obsługuje obciążenia oficjalne UK do hostowania aplikacji sieci web. Architektura zapewnia bezpieczne środowisko, które korzysta z platformy Azure jako możliwości usługi. W tym środowisku dwie aplikacje usługi App Service web apps są wdrożone (jeden dla użytkowników publicznych) i jeden dla użytkowników zaplecza, z warstwą aplikacji interfejsu API do świadczenia usług biznesowych dla sieci web frontonu. Usługi Azure SQL Database jest wdrażany jako zarządzanego relacyjnego magazynu danych dla aplikacji. Łączność z tych składników z poza platformę i między tymi składnikami jest szyfrowany za pomocą protokołu TLS 1.2 w celu zapewnienia danych w transportu poufność informacji użytkowników, dostęp uwierzytelniony przez usługę Azure Active Directory.

![PaaS hostowania aplikacji internetowych za dla obciążeń oficjalne UK referencyjny diagram architektury](images/ukofficial-paaswa-architecture.png?raw=true "PaaS hostowania aplikacji internetowych za dla obciążeń oficjalne UK referencyjny diagram architektury")

Architektura wdrożenia, aprowizowanie bezpiecznego magazynu, monitorowania & rejestrowania, ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami i zarządzania w ramach funkcji są też wdrażane, aby upewnić się, że klienci mają wszystkie narzędzia, które są wymagane do zabezpieczenia i monitorowania ich środowisko, w tym rozwiązaniu.

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegółowe informacje na temat architektury wdrożenia [architektura wdrożenia](#deployment-architecture) sekcji.

- Usługa Azure Active Directory
- App Service
- Aplikacja internetowa
- Aplikacja interfejsu API
- System DNS platformy Azure
- Usługa Key Vault
- Usługa Azure Monitor (Dzienniki)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

### <a name="security"></a>Bezpieczeństwo

#### <a name="identity-and-authentication"></a>Uwierzytelnianie i tożsamość

Ten plan zapewnia, że dostęp do zasobów są chronione przy użyciu usług zarządzania katalogami i tożsamościami. Ta architektura umożliwia pełne wykorzystanie [tożsamość jako zabezpieczeń obwodowych](https://docs.microsoft.com/azure/security/security-paas-deployments). 

Następujące technologie zapewniają tożsamość możliwości zarządzania w środowisku platformy Azure:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy dotyczące rozwiązania zostały utworzone w usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do bazy danych SQL.
- Operator połączonego z dostępu do administrowania zasobami platformy Azure i aplikacji sieci web uwierzytelniania za pomocą usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Szyfrowanie kolumny bazy danych używa usługi Azure AD można uwierzytelnić aplikację do usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Always Encrypted: Ochrona poufnych danych w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Dla obywateli połączonego z aplikacji sieci web jest skonfigurowana dla dostępu publicznego. Aby umożliwić tworzenie konta i uwierzytelniania za pomocą usługi active directory lub społecznościowych sieci dostawcy tożsamości [usługi Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) można zintegrować, jeśli jest to wymagane.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa ryzykowne kont i potencjalnych luk w zabezpieczeniach przedstawiono zalecenia, aby zwiększyć poziom zabezpieczeń tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości w organizacji i bada podejrzanych zdarzeń i przyjmuje odpowiednią akcję, aby je rozwiązać.
- [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowane na platformie Azure. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji i usługi Azure Key Vault dostęp jest ograniczony tylko do użytkowników, którzy wymagają dostępu do zarządzania kluczami.
- Za pomocą wykorzystaniu [usługi Azure Active Directory dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) klienci mogą zostać wymuszone dodatkowych opcji zabezpieczeń dostępu do aplikacji lub użytkowników w swoich środowiskach, na podstawie określonych warunków, takich jak lokalizacji, urządzeń, stanu i logowanie ryzyko.
- [Usługa Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) w połączeniu z najlepsze rozwiązania dotyczące projektowania aplikacji, zapewnia ochronę przed atakami DDoS, przy użyciu zawsze włączone monitorowanie ruchu i w czasie rzeczywistym zapobieganie typowych ataków na poziomie sieci. Dzięki architekturze PaaS platformy poziom ochrony przed atakami DDoS jest niewidoczne dla klienta i zarejestrowanych w platformę, ale jest należy pamiętać, odpowiedzialność projektowania zabezpieczeń aplikacji znajduje się u klienta.

#### <a name="data-in-transit"></a>Przesyłane dane

Dane są przesyłane z zewnątrz i między składnikami platformy Azure jest chroniony za pomocą [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), który używa do szyfrowania komunikacji w miarę ich Kryptografia symetryczna w oparciu o wspólny klucz tajny przesyłane za pośrednictwem sieci. Domyślnie ruch sieciowy jest zabezpieczony za pomocą protokołu TLS 1.2.

#### <a name="security-and-malware-protection"></a>Ochrona zabezpieczeniami i złośliwym oprogramowaniem

[Usługa Azure Security Center](https://azure.microsoft.com/services/security-center/) zapewnia scentralizowany widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure. Na pierwszy rzut oka może Sprawdź, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowane i można szybko zidentyfikować wszelkie zasoby, które wymagają uwagi.

[Usługa Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) to spersonalizowany konsultant ds. chmury, który ułatwia stosowanie najlepszych rozwiązań do optymalizacji wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) funkcja ochrony w czasie rzeczywistym, który ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. To domyślnie jest zainstalowany na podstawowej infrastruktury PaaS w maszynie wirtualnej i jest zarządzana przez sieci szkieletowej Azure w sposób niewidoczny dla użytkownika do klienta.

### <a name="paas-services-in-this-blueprint"></a>Usługi PaaS w tym planie

#### <a name="azure-app-service"></a>Azure App Service

Usługa Azure App Service oferuje w pełni zarządzane w Internecie środowiska dla aplikacji sieci web w języku Java, PHP, Node.js, Python, HTML i C# bez konieczności zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoka dostępność, obsługuje zarówno Windows, jak i Linux i umożliwia automatyczne wdrożeń z [DevOps platformy Azure](https://azure.microsoft.com/services/visual-studio-team-services/) lub dowolnym repozytorium opartych o Git.

App Service to [ISO, SOC i PCI](https://www.microsoft.com/TrustCenter/) i mogą uwierzytelniać użytkowników za pomocą [usługi Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) lub logowania z serwisów społecznościowych ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [wserwisieFacebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter), i [uwierzytelnianie firmy Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft).

Basic, Standard i plany Premium są przeznaczone dla obciążeń produkcyjnych i działają na dedykowanych wystąpieniach maszyn wirtualnych. Każde wystąpienie może obsługiwać wiele aplikacji i domen. Usługi App services również obsługę [ograniczenia adresów IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) zabezpieczyć ruch do zaufanych adresów IP, jeśli jest to wymagane, a także [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) dla bezpiecznego połączenia do innych usług PaaS takie jak [magazyn kluczy](https://azure.microsoft.com/services/key-vault/) i [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/). W przypadku, gdy są wymagane dodatkowe zabezpieczenia naszego planu izolowanego hostowanie aplikacji w prywatnym, dedykowanym środowisku platformy Azure i jest idealne dla aplikacji, które wymagają bezpiecznego połączenia z Twojej sieci lokalnej lub dodatkowej wydajności i skali.

Ten szablon wdraża następujące funkcje usługi App Service:

- [Standardowa](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) warstwę planu usługi App Service
- Wielu usług aplikacji [miejsc wdrożenia](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Deweloperów, w wersji zapoznawczej, odpowiedzi na pytania, przeprowadzających testowanie Akceptacyjne i oczywiście produkcyjnych (domyślne miejsce).
- [Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) nawiązać [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (to może również służyć do zapewnienia dostępu do [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integracja z usługą [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) do monitorowania wydajności
- [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- Metryka [alertów](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

Usługa SQL Database jest zarządzaną usługą relacyjnej bazy danych ogólnego przeznaczenia na platformie Microsoft Azure, obsługującą struktury takie jak dane relacyjne, JSON, dane przestrzenne i XML. SQL Database oferuje zarządzane pojedynczej bazy danych SQL, zarządzane bazy danych SQL w [puli elastycznej](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool), SQL i [wystąpienia zarządzane przez usługę](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (w publicznej wersji zapoznawczej). Zapewnia [dynamicznie skalowalną wydajność](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) i udostępnia opcje, takie jak [indeksy magazynu kolumn](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview), używane w skomplikowanych analizach i raportowaniu, oraz [przetwarzanie OLTP danych w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) na potrzeby ekstremalnego przetwarzania transakcyjnego. Firma Microsoft bezproblemowo obsługuje wprowadzanie poprawek i aktualizowanie bazy kodu SQL i ukrywa procesy zarządzania podstawową infrastrukturą.

Usługa Azure SQL Database w tym planie

Wystąpienie usługi Azure SQL Database wykorzystuje następujące środki bezpieczeństwa bazy danych:

- [Reguły zapory na poziomie serwera i na poziomie bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), lub za pomocą [punkty końcowe usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) przy użyciu [reguł sieci wirtualnej](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) pomaga chronić bazy danych Azure SQL Database i Azure Data Warehouse przed złośliwymi działaniami. Wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji.
- [Uwierzytelnianie usługi Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), można centralnie zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.
- Korzystanie z usługi Azure Active Directory do administrowania bazy danych
- [Dzienniki inspekcji](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) do kont magazynu
- Metryka [alerty](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) dla nie powiodło się: połączenia bazy danych
- [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Szyfrowanie kolumn](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [usługi Azure Storage](https://azure.microsoft.com/services/storage/) to usługa firmy Microsoft, zarządzane usługi w chmurze, która zapewnia miejsca do magazynowania o wysokiej dostępne, bezpieczne, niezawodne, skalowalne i nadmiarowe. Usługa Azure Storage składa się z następujących usług: Blob Storage, File Storage i Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Usługa Azure Storage, w tym planie

Ten szablon używa następujących składników usługi Azure Storage:

- [Szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Zezwalaj tylko na połączenia HTTPS

#### <a name="data-at-rest"></a>Dane magazynowane

Za pomocą [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) wszystkie dane zapisane w usłudze Azure Storage są szyfrowane za pomocą 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych. Za pomocą kluczy szyfrowania zarządzanego przez firmę Microsoft za pomocą funkcji SSE lub możesz użyć [kluczy szyfrowania](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Kont magazynu mogą być chronione za pomocą [punkty końcowe usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) przy użyciu [reguł sieci wirtualnej](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Szczegółowe informacje dotyczące zabezpieczania usługi Azure Storage można znaleźć w [Przewodnik po zabezpieczeniach](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Zarządzanie wpisami tajnymi

#### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

[Usługa Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) używany do zabezpieczania aplikacji kluczy i wpisów tajnych, aby upewnić się, że nie są dostępne przez osoby trzecie. Usługa Key Vault nie jest przeznaczona do przechowywania haseł użytkowników. Umożliwia utworzenie wielu zabezpieczonych kontenerów nazywanych magazynami. Te magazyny są wspierane przez sprzętowe moduły zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny usługi Key Vault umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów protokołu TLS (Transport Layer Security), udostępniając funkcje wymagane przez niezawodne rozwiązania do zarządzania cyklem życia certyfikatu.

#### <a name="azure-key-vault-in-this-blueprint"></a>Usługa Azure Key Vault, w tym planie

- Przechowuje klucz dostępu do magazynu z dostępem do odczytu przyznane [tożsamości zarządzanej](https://docs.microsoft.com/azure/app-service/overview-managed-identity) klienta aplikacji sieci web umożliwiający dostęp do Internetu
- Przechowuje hasła DBA serwera SQL (w osobnym magazynie)
- Rejestrowanie diagnostyczne

### <a name="monitoring-logging-and-audit"></a>Monitorowanie, rejestrowanie i inspekcja

#### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki platformy Azure Monitor](https://azure.microsoft.com/services/log-analytics/) to usługa platformy Azure, która ułatwia zbieranie oraz analizowanie danych generowanych przez zasoby w środowisku chmurowym i lokalnym.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Usługa Azure Monitor loguje się tego planu

- Ocena serwera SQL
- Diagnostyka usługi Key Vault
- Połączenie z usługą Application Insights
- Dziennik aktywności platformy Azure

#### <a name="application-insights"></a>Application Insights

[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna Usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci web na wielu platformach. Używane do monitorowania aplikacji sieci web go będzie automatycznie wykrywać anomalie wydajności, analizy wydajności, diagnozowanie problemów i aby zrozumieć sposób korzystania przez użytkowników z aplikacją. Usługa Application Insights można wdrożyć na platformach, łącznie z platformą .NET, Node.js i Java EE hostowanych lokalnie lub w chmurze. Integruje się ona z procesem DevOps i ma punkty połączenia z szeroką gamą narzędzi programistycznych.

#### <a name="application-insights-in-this-blueprint"></a>Usługa Application Insights, w tym planie

Ten szablon używa następujących składników usługi Application Insights:

- Pulpit nawigacyjny szczegółowych informacji w aplikacji na witrynę (Operator, klientów i interfejsu API)

#### <a name="azure-activity-logs"></a>Dzienniki aktywności platformy Azure

[Dziennik aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) inspekcji zdarzeń płaszczyznę kontroli dla subskrypcji. Przy użyciu dziennika aktywności, można określić "co, kto i kiedy" dla dowolnego zapisu (PUT, POST, DELETE) wykonywanych na zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości.

#### <a name="azure-monitor"></a>Azure Monitor

[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) zapewnia podstawowe funkcje monitorowania usług systemu Azure, umożliwiając zbierania metryk, dzienników aktywności i dzienników diagnostycznych. Usługa Azure Monitor oferuje metryki infrastruktury na poziomie podstawowym oraz dzienniki dla większości usług platformy Microsoft Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/ukofficial-paaswa-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![PaaS hostowania aplikacji internetowych za dla obciążeń oficjalne UK model zagrożeń](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS hostowania aplikacji internetowych za dla obciążeń oficjalne UK model zagrożeń")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentacja zgodności zasad bezpieczeństwa w chmurze NCSC

Usługa komercyjnych korony (Agencja, który pracuje, aby poprawić działanie komercyjne i zamówienia przez Rząd) odnowić klasyfikacji Microsoft przedsiębiorstwa w zakresie usług cloud services G-Cloud w wersji 6, obejmujące wszystkie swoje oferty na poziomie OFFICIAL. Można znaleźć szczegółowe informacje o platformie Azure i G-Cloud w [podsumowanie oceny zabezpieczeń usługi Azure UK G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Ten plan zgodnie z zasadami zabezpieczeń 14 chmury, które są udokumentowane w NCSC [zasad bezpieczeństwa w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) aby zapewnić środowisko, które obsługuje obciążenia sklasyfikowane jako oficjalne UK.

[Zabezpieczeń platformy Azure i plan zgodność — UK oficjalne klienta odpowiedzialność macierzy](https://aka.ms/ukofficial-crm) (skoroszyt programu Excel) zawiera listę wszystkich zasad bezpieczeństwa w chmurze 14 i oznacza dla każdej zasady (lub zasadą podsekcja), czy zasady Implementacja jest odpowiedzialność firmy Microsoft, klient lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - aplikacji sieci Web PaaS, Zjednoczone Królestwo oficjalne zasady wykonania macierzy](https://aka.ms/ukofficial-paaswa-pim) (skoroszyt programu Excel) zawiera listę wszystkich zasad bezpieczeństwa w chmurze 14 i oznacza dla każdej zasady (lub zasadą podsekcja) który wyznaczono odpowiedzialność klienta w macierzy obowiązki klienta, 1) Jeśli planu implementuje zasady i (2) opis sposobu implementacji wyrównuje zasady spełnienia następujących wymagań.  

Ponadto Cloud Security Alliance (CSA) opublikowane macierzy kontroli chmury, aby wspierać klientów w wersji ewaluacyjnej programu dostawców rozwiązań w chmurze i zidentyfikować pytania, na które należy odpowiedzieć na przed przejściem do usług w chmurze. W odpowiedzi, Microsoft Azure odpowiedzi na kwestionariusz inicjatywy oceny Consensus CSA ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), która opisuje, jak Microsoft adresy sugerowane zasad.

## <a name="third-party-assessment"></a>Ocena innych firm

Ten plan został przejrzany, Zjednoczone Królestwo National Cybernetycznymi zabezpieczeń Centrum (NCSC) i zgodne z zasadami zabezpieczeń NCSC 14 chmury

Szablony usługi automation zostały przetestowane przez zespół sukcesu klientów Zjednoczone Królestwo jednostki Architekt rozwiązań chmury platformy Azure i naszym partnerem firmy Microsoft [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Ten zabezpieczeń platformy Azure i zgodności planu automatyzacji składa się z pliki konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API usługi Azure Resource Manager do wdrażania zasobów na platformie Azure. Dostępne są instrukcje wdrożenia są szczegółowo [tutaj](https://aka.ms/ukofficial-paaswa-repo).

Trzy metody zostały przewidziane w wdrożenia; Proste "express" [2 interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) odpowiednie do szybkiego tworzenia testu środowiska; sparametryzowane [2 interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) podejście, zapewniając większą konfiguracji dla środowisk obciążenia; i witryny Azure portal na podstawie wdrożenia, w której operator może określić parametrów wdrożenia przy użyciu witryny Azure portal. 

1.  Klonuj lub Pobierz [to](https://aka.ms/ukofficial-paaswa-repo) repozytorium GitHub na lokalnej stacji roboczej.
2.  Przegląd [metoda 1: Azure CLI 2 (wersja Express)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) i wykonaj polecenia podane.
3.  Przegląd [metoda 1a: Usługa Azure 2 interfejsu wiersza polecenia (Konfigurowanie wdrożenia za pośrednictwem argumenty skryptu)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) i wykonaj polecenia podane
4.  Przegląd [metoda 2: Proces wdrażania w portalu Azure](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) i wykonywania listy poleceń

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="api-management"></a>API Management

[Usługa Azure API Management](https://azure.microsoft.com/services/api-management/) może służyć przed interfejsu API usługi App Service w celu zapewnienia dodatkowych warstw zabezpieczeń, ograniczania i kontroli w celu udostępnienia serwera proxy i ochrona interfejsów API.

### <a name="azure-b2c"></a>Azure B2C

[Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) można zaimplementować jako formant, aby umożliwić użytkownikom rejestrowanie, Utwórz tożsamość i Włącz autoryzację i kontrolę dostępu dla aplikacji sieci web publicznych.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
