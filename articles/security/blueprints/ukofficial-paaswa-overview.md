---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure-PaaS hosting aplikacji sieci Web dla OFICJALNych obciążeń BRYTYJSKIch
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure-PaaS hosting aplikacji sieci Web dla OFICJALNych obciążeń BRYTYJSKIch
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: c0163b5280de942491f2174aa371fa7cc83d5984
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946516"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: PaaS hosting aplikacji sieci Web dla OFICJALNych obciążeń BRYTYJSKIch

## <a name="azure-security-and-compliance-blueprints"></a>Plany zabezpieczeń i zgodności platformy Azure

Plany platformy Azure składają się z wskazówek dotyczących dokumentów i szablonów automatyzacji, które wdrażają architektury oparte na chmurze w celu oferowania rozwiązań do scenariuszy, które mają akredytacji lub wymagania dotyczące zgodności. Plany platformy Azure to wskazówki i kolekcje szablonów automatyzacji, dzięki którym Microsoft Azure klienci mogą przyspieszyć dostarczanie ich celów firmy poprzez zainicjowanie architektury podstawy, która może zostać rozszerzona w celu spełnienia wszelkich dalszych wymagań.

## <a name="overview"></a>Przegląd

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki i skrypty automatyzacji umożliwiające dostarczenie architektury aplikacji sieci Web hostowanej na [platformie Microsoft Azure jako usługi (PaaS),](https://azure.microsoft.com/overview/what-is-paas/) która jest odpowiednia do obsługi obciążeń sklasyfikowanych jako [Oficjalna w Zjednoczonym Królestwie ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Ta klasyfikacja zabezpieczeń obejmuje większość informacji utworzonych lub przetwarzanych przez sektor publiczny. Obejmuje to rutynowe operacje i usługi biznesowe, które w przypadku zgubienia, kradzieży lub opublikowania na nośniku, z których niektóre mogły mieć szkodliwy wpływ. Typowy profil zagrożenia dla oficjalnej klasyfikacji jest taki sam jak prywatny podmiot, który zapewnia cenne informacje i usługi. OFICJALNE Królestwo w Wielkiej Brytanii przewiduje konieczność obrony danych i usług rządowych Zjednoczonego Królestwa przed zagrożeniem lub naruszeniem bezpieczeństwa przez osoby atakujące z ograniczonymi możliwościami i zasobami, takimi jak (ale nie są ograniczone do) hactivists, pojedynczej grupy nacisków, badaczy, kompetentni indywidualni hakerzy oraz większość osób i grup kryminalnych.

Ten plan został poddany przeglądowi przez narodowe Zjednoczone cybernetycznymi Security Centre (NCSC) i wyrównany do zasad zabezpieczeń w chmurze NCSC 14.

Architektura korzysta z [platformy Azure jako składników usługi](https://azure.microsoft.com/overview/what-is-paas/) , aby dostarczać środowisko, które umożliwia klientom uniknięcie wydatków i złożoności kupowania licencji na oprogramowanie, zarządzania podstawową infrastrukturą aplikacji i oprogramowania pośredniczącego lub Narzędzia programistyczne i inne zasoby. Klienci zarządzają opracowywanymi przez siebie aplikacjami i usługami, koncentrując się na dostarczaniu wartości biznesowej, podczas gdy Microsoft Azure zarządza innymi zasobami platformy Azure, takimi jak maszyny wirtualne, magazyn i sieć, co zwiększa możliwości [dzielenia odpowiedzialność](../fundamentals/paas-deployments.md) za zarządzanie infrastrukturą na platformie Azure. [Usługa azure App Services](https://azure.microsoft.com/services/app-service/) oferuje automatyczne skalowanie, wysoką dostępność, obsługuje systemy Windows i Linux oraz włącza automatyczne wdrożenia z usługi GitHub, platformy Azure DevOps lub dowolnego repozytorium git jako usług domyślnych. Korzystając z App Services, deweloperzy mogą skoncentrować się na dostarczaniu wartości biznesowej bez nakładów związanych z zarządzaniem infrastrukturą. Istnieje możliwość kompilowania Greenfield nowych aplikacji Java, PHP, Node. js, Python, HTML lub C# sieci Web, a także do migrowania istniejącej chmury lub lokalnych aplikacji sieci Web do usługi Azure App Services (chociaż gruntownie sprawdzają się dokładne i testy w celu potwierdzenia wydajności). wymagane).

Ten plan koncentruje się na zainicjowaniu obsługi platformy Secure Foundation [jako](https://azure.microsoft.com/overview/what-is-paas/) interfejsu opartego na sieci Web dla publicznego, a także dla użytkowników w biurze zaplecza. W tym scenariuszu projektowania strategii rozważa się użycie usług opartych na sieci Web hostowanych na platformie Azure, w których użytkownik publiczny może bezpiecznie przesyłać i wyświetlać poufne dane oraz zarządzać nimi. Ponadto Urząd biurowy lub podmiot administracji publicznej może bezpiecznie przetwarzać dane poufne przesłane przez użytkownika publicznego. Przypadki użycia w tym scenariuszu mogą obejmować:

- Użytkownik przesyłający dane podatkowe przy użyciu operatora rządowego, który przetwarza zgłoszenie;
- Użytkownik żądający usługi za pośrednictwem aplikacji opartej na sieci Web, a użytkownik zaplecza — sprawdza i dostarcza usługę; oraz
- Użytkownik poszukujący i wyświetla informacje pomocy dotyczącej domeny publicznej dotyczącej usługi rządowej.

Korzystając z szablonów [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) i skryptów interfejsu wiersza polecenia platformy Azure, plan wdraża środowisko, które jest zgodne z krajowym centrum zabezpieczeń cybernetycznymi w Wielkiej Brytanii (NCSC) 14 [zasady zabezpieczeń chmury](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) oraz centrum dla Internetu. Zabezpieczenia (CIS) [krytyczne funkcje zabezpieczeń](https://www.cisecurity.org/critical-controls.cfm). NCSC zaleca klientom korzystanie z zasad zabezpieczeń chmury w celu ocenienia właściwości zabezpieczeń usługi i poznania działu odpowiedzialności między klientem i dostawcą. Firma Microsoft udostępniła informacje dotyczące każdej z tych zasad, aby lepiej zrozumieć podział obowiązków. Ta architektura i odpowiednie szablony Azure Resource Manager są obsługiwane przez oficjalny dokument firmy Microsoft, a [14 kontrolki zabezpieczeń w chmurze dla chmury brytyjskiej przy użyciu Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Ta architektura została poddana przeglądowi przez NCSC i jest zgodna z zasadami bezpieczeństwa w chmurze w Zjednoczonym Królestwie NCSC 14, dzięki czemu organizacje sektora publicznego mogą szybko śledzić swoją możliwość spełnienia obowiązków związanych ze zgodnością za pomocą usług w chmurze globalnie i w Wielkiej Brytanii na Microsoft Azure Cloud. Ten szablon służy do wdrażania infrastruktury dla obciążenia. Klienci mogą instalować i konfigurować kod aplikacji obsługujący warstwę biznesową i oprogramowanie warstwy danych. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://aka.ms/ukofficial-paaswa-repo/).

Ten plan jest architekturą podstawy. Nasi klienci mogą używać tego planu jako podstawy do ich OFICJALNEgo klasyfikowania obciążeń opartych na sieci Web i rozwijać szablony i zasoby z ich własnymi wymaganiami. Ten plan jest oparty na zasadach z planu [aplikacji sieci Web w Wielkiej OFFICAL (IaaS](https://aka.ms/ukofficial-iaaswa) ) w celu zaoferowania naszym klientom rozwiązań [w zakresie infrastruktury jako usługi (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) i implementacji PaaS do hostowania obciążeń opartych na sieci Web.

Aby wdrożyć ten plan, wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z możliwości szybkiego i łatwego rejestrowania: Rozpocznij pracę z platformą Azure. Kliknij [tutaj](https://aka.ms/ukofficial-paaswa-repo/) , aby uzyskać instrukcje dotyczące wdrażania.

## <a name="architecture-and-components"></a>Architektura i składniki

Ten plan dostarcza rozwiązanie hostingu aplikacji sieci Web w środowisku chmury platformy Azure, które obsługuje oficjalne obciążenia w Wielkiej Brytanii. Architektura zapewnia bezpieczne środowisko, które wykorzystuje platformę Azure jako możliwości usługi. W środowisku programu są wdrażane dwie App Service aplikacje sieci Web (po jednym dla użytkowników publicznych i jeden dla użytkowników zaplecza w biurze), dzięki warstwie aplikacji interfejsu API w celu zapewnienia usług firmy dla frontonu sieci Web. Azure SQL Database jest wdrażana jako zarządzany magazyn danych relacyjnych dla aplikacji. Łączność z tymi składnikami spoza platformy i między wszystkimi tymi składnikami jest szyfrowana za pośrednictwem protokołu TLS 1,2 w celu zapewnienia prywatności danych w transporcie, z dostępem uwierzytelnionym przez Azure Active Directory.

![PaaS hosting aplikacji sieci Web na potrzeby oficjalnych obciążeń dla Wielkiej Brytanii](images/ukofficial-paaswa-architecture.png?raw=true "PaaS hosting aplikacji sieci Web na potrzeby oficjalnych obciążeń dla Wielkiej Brytanii")

W ramach architektury wdrażania bezpieczne Inicjowanie obsługi magazynu, monitorowanie & rejestrowanie, ujednolicone Zarządzanie zabezpieczeniami & zaawansowanej ochrony przed zagrożeniami oraz funkcje zarządzania są również wdrażane w celu zapewnienia, że klienci mają wszystkie narzędzia wymagane do Zabezpiecz i monitoruj swoje środowisko dla tego rozwiązania.

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Usługa Azure Active Directory
- App Service
- Aplikacja sieci Web
- Aplikacja interfejsu API
- Usługa DNS platformy Azure
- Usługa Key Vault
- Azure Monitor (dzienniki)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

### <a name="security"></a>Bezpieczeństwo

#### <a name="identity-and-authentication"></a>Tożsamość i uwierzytelnianie

Ten plan zapewnia ochronę dostępu do zasobów za poorednictwem usług katalogowych i zarządzania tożsamościami. Ta architektura umożliwia pełne wykorzystanie [tożsamości jako obwodu zabezpieczeń](../fundamentals/paas-deployments.md). 

Następujące technologie zapewniają funkcje zarządzania tożsamościami w środowisku platformy Azure:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft do zarządzania katalogami i tożsamościami opartymi na chmurze. Wszyscy użytkownicy rozwiązania zostały utworzeni w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do SQL Database.
- Uwierzytelnianie do aplikacji sieci Web w ramach operatora i dostęp do administrowania zasobami platformy Azure odbywa się za pomocą usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- Szyfrowanie kolumn bazy danych używa usługi Azure AD do uwierzytelniania aplikacji do Azure SQL Database. Aby uzyskać więcej informacji, [Zobacz Always Encrypted: Ochrona danych poufnych w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Aplikacja sieci Web mająca dostęp do obywateli jest skonfigurowana na potrzeby dostępu publicznego. Aby umożliwić tworzenie i uwierzytelnianie kont za poorednictwem dostawców tożsamości usługi Active Directory lub sieci społecznościowej, [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) mogą być zintegrowane w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach i ryzykowne konta zawierają zalecenia zwiększające bezpieczeństwo stan tożsamości w organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami organizacji i badania podejrzanych zdarzeń oraz podejmowanie odpowiednich działań w celu ich rozwiązania.
- [Access Control oparte na rolach (RBAC) na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem na platformie Azure. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji, a Azure Key Vault dostęp jest ograniczony tylko do użytkowników, którzy wymagają dostępu do zarządzania kluczami.
- Korzystając z [Azure Active Directory klienci dostępu warunkowego](../../active-directory/active-directory-conditional-access-azure-portal.md) mogą wymusić dodatkowe środki kontroli zabezpieczeń dotyczące dostępu do aplikacji lub użytkowników w ich środowisku na podstawie określonych warunków, takich jak lokalizacja, urządzenie, stan i ryzyko związane z logowaniem.
- [Azure DDoS Protection](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, zapewnia ochronę przed atakami DDoS, z zawsze włączonym monitorowaniem ruchu i ograniczeniami w czasie rzeczywistym na podstawie typowych ataków na poziomie sieci. Dzięki architekturze PaaS ochrona DDoS na poziomie platformy jest niewidoczna dla klienta i wbudowana w platformę, ale ważne jest, aby pamiętać, że odpowiedzialność za projektowanie zabezpieczeń aplikacji zależy od klienta.

#### <a name="data-in-transit"></a>Dane przesyłane

Dane są przesyłane z zewnątrz i między składnikami platformy Azure są chronione za pomocą [Transport Layer Security/SSL (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), które używają kryptografii symetrycznej opartej na wspólnym kluczu tajnym do szyfrowania komunikacji przesyłanej przez sieć. Domyślnie ruch sieciowy jest zabezpieczony przy użyciu protokołu TLS 1,2.

#### <a name="security-and-malware-protection"></a>Bezpieczeństwo i ochrona przed złośliwym oprogramowaniem

[Azure Security Center](https://azure.microsoft.com/services/security-center/) stanowi scentralizowany widok stanu zabezpieczeń wszystkich zasobów platformy Azure. W skrócie możesz sprawdzić, czy odpowiednie mechanizmy kontroli zabezpieczeń są poprawnie skonfigurowane, i czy można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) to spersonalizowany konsultant ds. usług w chmurze, który pomaga stosować najlepsze rozwiązania w celu optymalizacji wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

[Firma Microsoft chroniąca przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware) to funkcja ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Ta wartość jest domyślnie instalowana w źródłowej infrastrukturze maszyn wirtualnych PaaS i jest ona zarządzana przez sieć szkieletową platformy Azure w sposób niewidoczny dla klienta.

### <a name="paas-services-in-this-blueprint"></a>Usługi PaaS w tym planie

#### <a name="azure-app-service"></a>Usługa Azure App Service

Azure App Service udostępnia w pełni zarządzane środowisko hostingu w sieci Web dla aplikacji sieci Web opracowane w języku Java, PHP, Node. js C# Python, HTML i bez konieczności zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje zarówno system Windows, jak i Linux, a także włącza automatyczne wdrożenia z [usługi Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) lub dowolnego repozytorium opartego na usłudze git.

App Service są [zgodne ze standardami ISO, SOC i PCI](https://www.microsoft.com/TrustCenter/) oraz mogą uwierzytelniać użytkowników za pomocą [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) lub z logowaniem do sieci społecznościowej ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)i [Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)).

Plany w warstwach Podstawowa, standardowa i Premium są przeznaczone dla obciążeń produkcyjnych i działają na dedykowanych wystąpieniach maszyn wirtualnych. Każde wystąpienie może obsługiwać wiele aplikacji i domen. Usługi App Services obsługują także [Ograniczenia adresów IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) w celu zabezpieczania ruchu do zaufanych adresów IP, jeśli jest to wymagane, a także do [zarządzania tożsamościami dla zasobów platformy Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) w celu bezpiecznego połączenia z innymi usługami PaaS, takimi jak [Key Vault](https://azure.microsoft.com/services/key-vault/) i [Azure SQL Baza danych](https://azure.microsoft.com/services/sql-database/)programu. Jeśli jest wymagany dodatkowy poziom zabezpieczeń, nasz plan izolowany obsługuje aplikacje w prywatnym, dedykowanym środowisku platformy Azure i jest idealnym rozwiązaniem w przypadku aplikacji wymagających bezpiecznych połączeń z siecią lokalną lub dodatkowej wydajności i skalowania.

Ten szablon wdraża następujące funkcje App Service:

- [Standard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service warstwa planu
- Wiele App Service [miejsc wdrożenia](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Tworzenie, Podgląd, pytania i odpowiedzi, przeprowadzających i kurs (gniazdo domyślne).
- [Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) w celu nawiązania połączenia z [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (może to również służyć do zapewnienia dostępu do [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integracja z [usługą Azure Application Insights](../../azure-monitor/app/azure-web-apps.md) w celu monitorowania wydajności
- [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) 
- [Alerty](../../azure-monitor/app/alerts.md) metryk 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

Usługa SQL Database jest zarządzaną usługą relacyjnej bazy danych ogólnego przeznaczenia na platformie Microsoft Azure, obsługującą struktury takie jak dane relacyjne, JSON, dane przestrzenne i XML. SQL Database oferuje zarządzane pojedyncze bazy danych SQL, zarządzane bazy danych SQL w [puli elastycznej](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)oraz [wystąpienia zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) SQL (w publicznej wersji zapoznawczej). Zapewnia [dynamicznie skalowalną wydajność](../../sql-database/sql-database-purchase-models.md) i udostępnia opcje, takie jak [indeksy magazynu kolumn](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview), używane w skomplikowanych analizach i raportowaniu, oraz [przetwarzanie OLTP danych w pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) na potrzeby ekstremalnego przetwarzania transakcyjnego. Firma Microsoft bezproblemowo obsługuje wprowadzanie poprawek i aktualizowanie bazy kodu SQL i ukrywa procesy zarządzania podstawową infrastrukturą.

Azure SQL Database w tym planie

W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:

- [Reguły zapory na poziomie serwera i na poziomie bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)lub za pośrednictwem [punktów końcowych usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) przy użyciu [reguł sieci wirtualnej](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) pomaga chronić Azure SQL Database i usługi Azure Data Warehouse przed zagrożeniem złośliwych działań. Wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w czasie rzeczywistym bez konieczności wprowadzania zmian w aplikacji.
- [Uwierzytelnianie w usłudze Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie IDENTYFIKATORami zapewnia pojedyncze miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.
- Używanie Azure Active Directory do administrowania bazą danych
- [Inspekcja dzienników](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) na kontach magazynu
- [Alerty](../../azure-monitor/app/alerts.md) metryk dla niezakończonych połączeń bazy danych
- [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) to zarządzana przez firmę Microsoft usługa w chmurze, która zapewnia wysoką dostępność, bezpieczny, trwały, skalowalny i nadmiarowy magazyn. Usługa Azure Storage składa się z następujących usług: Blob Storage, File Storage i Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Usługa Azure Storage w tym planie

Ten szablon używa następujących składników usługi Azure Storage:

- [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Zezwalaj tylko na połączenia HTTPS

#### <a name="data-at-rest"></a>Dane magazynowane

Za pomocą [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) wszystkie dane zapisywane w usłudze Azure Storage są szyfrowane za pomocą 256-BITOWEGO szyfrowania AES, jednego z najsilniejszych szyfrów blokowych. Klucze szyfrowania zarządzane przez firmę Microsoft można używać z funkcją SSE lub z użyciem [własnych kluczy szyfrowania](../../storage/common/storage-encryption-keys-portal.md).

Konta magazynu można zabezpieczyć za pośrednictwem [punktów końcowych usługi Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) przy użyciu [reguł sieci wirtualnej](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Szczegółowe informacje na temat zabezpieczania usługi Azure Storage można znaleźć w [przewodniku po zabezpieczeniach](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Zarządzanie kluczami tajnymi

#### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) służy do zabezpieczania kluczy i wpisów tajnych aplikacji w celu zapewnienia, że nie są one dostępne dla stron trzecich. Usługa Key Vault nie jest przeznaczona do przechowywania haseł użytkowników. Umożliwia tworzenie wielu bezpiecznych kontenerów o nazwie magazyny. Te magazyny są wspierane przez sprzętowe moduły zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny usługi Key Vault umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów protokołu TLS (Transport Layer Security), udostępniając funkcje wymagane przez niezawodne rozwiązania do zarządzania cyklem życia certyfikatu.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault w tym planie

- Przechowuje klucz dostępu do magazynu z dostępem do odczytu przyznanym do [zarządzanej tożsamości](https://docs.microsoft.com/azure/app-service/overview-managed-identity) aplikacji sieci Web klienta.
- Przechowuje hasło SQL Server DBA (w osobnym magazynie)
- Rejestrowanie diagnostyczne

### <a name="monitoring-logging-and-audit"></a>Monitorowanie, rejestrowanie i inspekcja

#### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Azure monitor Logs](https://azure.microsoft.com/services/log-analytics/) to usługa platformy Azure, która ułatwia zbieranie i analizowanie danych generowanych przez zasoby w środowiskach w chmurze i lokalnych.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Dzienniki Azure Monitor w tym planie

- Ocena SQL
- Diagnostyka Key Vault
- Połączenie Application Insights
- Dziennik aktywności platformy Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Służy do monitorowania dynamicznych aplikacji sieci Web, które automatycznie wykrywają anomalie wydajności, analizują wydajność, diagnozować problemy i wiedzą, jak użytkownicy będą korzystać z aplikacji. Application Insights można wdrożyć na platformach, w tym .NET, Node. js i Java EE, hostowanych lokalnie lub w chmurze. Integruje się ona z procesem DevOps i ma punkty połączenia z szeroką gamą narzędzi programistycznych.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights w tym planie

Ten szablon używa następujących składników Application Insights:

- Pulpit nawigacyjny Application Insights na witrynę (operator, klient i interfejs API)

#### <a name="azure-activity-logs"></a>Dzienniki aktywności platformy Azure

[Dziennik aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) przeprowadza inspekcję zdarzeń płaszczyzny dla subskrypcji. Za pomocą dziennika aktywności można określić, kto i kiedy "w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych dla zasobów w subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) umożliwia podstawowe monitorowanie usług platformy Azure, umożliwiając zbieranie metryk, dzienników aktywności i dzienników diagnostycznych. Usługa Azure Monitor oferuje metryki infrastruktury na poziomie podstawowym oraz dzienniki dla większości usług platformy Microsoft Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/ukofficial-paaswa-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![PaaS hosting aplikacji sieci Web na potrzeby oficjalnego modelu zagrożeń dla brytyjskich obciążeń](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS hosting aplikacji sieci Web na potrzeby oficjalnego modelu zagrożeń dla brytyjskich obciążeń")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentacja dotycząca zgodności z zasadami zabezpieczeń w chmurze NCSC

Komercyjna usługa dla korony (Agencja, która pracuje nad ulepszaniem działalności komercyjnej i z zakresu zakupów przez rząd), odnowić klasyfikację usług Cloud Services w zasięgu firmy Microsoft do G-Cloud w wersji 6, obejmujących wszystkie jej oferty na poziomie Urzędowym. Szczegóły dotyczące platformy Azure i usługi G-Cloud można znaleźć w [podsumowaniu usługi Azure UK Cloud Security](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Ten plan jest zgodny z 14 zasadami zabezpieczeń w chmurze, które zostały udokumentowane w [zasadach zabezpieczeń w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) NCSC, aby pomóc w zapewnieniu środowiska, które obsługuje obciążenia sklasyfikowane jako urzędnik brytyjski.

W Strategia zabezpieczeń i zgodności z przepisami platformy Azureej z BRYTYJSKIej noty [odpowiedzialnej za klienta](https://aka.ms/ukofficial-crm) (skoroszyt programu Excel) są wyświetlane wszystkie 14 zasad zabezpieczeń chmury i określenia dla każdej zasady (lub podczęściu), niezależnie od tego, czy zasady implementacji są odpowiedzialność firmy Microsoft, klienta lub współdzielenia między nimi.

[Aplikacja sieci Web strategia zabezpieczeń i zgodności z przepisami platformy Azure-PaaS dla brytyjskiej oficjalnej implementacji reguły](https://aka.ms/ukofficial-paaswa-pim) (skoroszyt programu Excel) zawiera wszystkie 14 zasad zabezpieczeń chmury i wskazuje, dla każdej zasady (lub podczęściu), która jest oznaczona Odpowiedzialność klienta w macierzy odpowiedzialności klienta 1), jeśli plan implementuje zasadę i 2) opis sposobu, w jaki wdrożenie jest wyrównane z wymaganiami zasad.  

Ponadto Cloud Security Alliance (CSA) opublikował macierz kontroli chmury w celu obsługi klientów w ocenie dostawców chmury oraz do identyfikowania pytań, na które należy odpowiedzieć przed przejściem do usług w chmurze. W odpowiedzi Microsoft Azure odpowiedzieć na kwestionariusz z inicjatywy CSA z oceną konsensusu ([csa CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), w którym opisano sposób, w jaki firma Microsoft rozwiązuje sugerowane zasady.

## <a name="third-party-assessment"></a>Ocena innych firm

Ten plan został poddany przeglądowi przez narodowe Zjednoczone cybernetycznymi Security Centre (NCSC) i wyrównany do zasad zabezpieczeń w chmurze NCSC 14

Szablony automatyzacji zostały przetestowane przez zainstalowaną w Wielkiej Brytanii jednostkę architekta rozwiązań w chmurze platformy Azure i nasz partner firmy Microsoft [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja składa się z plików konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API Azure Resource Manager do wdrażania zasobów na platformie Azure. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://aka.ms/ukofficial-paaswa-repo).

Do wdrożenia udostępniono trzy podejścia; Prosty "Express" [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , który jest przystosowany do szybkiego tworzenia środowiska testowego; sparametryzowane podejście [interfejsu wiersza polecenia platformy Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) zapewniające większą konfigurację dla środowisk obciążeń; i wdrożenie oparte na Azure Portal, w którym operator może określić parametry wdrożenia za pośrednictwem Azure Portal. 

1.  Sklonuj lub Pobierz [to](https://aka.ms/ukofficial-paaswa-repo) repozytorium GitHub do lokalnej stacji roboczej.
2.  Zapoznaj [się z metodą 1: Interfejs wiersza polecenia platformy Azure 2 (](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) wersja Express) i wykonywanie podanych poleceń.
3.  Przegląd [metody 1a: Interfejs wiersza polecenia platformy Azure 2 (Konfigurowanie wdrożenia za pośrednictwem argumentów skryptu)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) i wykonywanie podanych poleceń
4.  Przegląd [metody 2: Azure Portal proces](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) wdrażania i wykonywanie wymienionych poleceń

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="api-management"></a>API Management

[Usługi Azure API Management](https://azure.microsoft.com/services/api-management/) można używać przed App Service interfejsu API w celu zapewnienia dodatkowych warstw zabezpieczeń, ograniczania i kontroli w celu udostępniania, proxy i ochrony interfejsów API.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) można zaimplementować jako kontrolkę umożliwiającą użytkownikom rejestrację, utworzenie tożsamości i włączenie autoryzacji i kontroli dostępu do publicznej aplikacji sieci Web.

## <a name="disclaimer"></a>Zastrzeżenie

- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
