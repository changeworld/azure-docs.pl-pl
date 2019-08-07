---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla Australii
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla Australii
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 1cceecba59b4cd1a70fc6f152020757e137f4d45
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778977"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web IaaS dla Australii

## <a name="overview"></a>Przegląd
Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące wdrażania środowiska infrastruktury jako usługi (IaaS) odpowiednie do zbierania, przechowywania i pobierania danych administracji publicznej chronionych przez usługę AU, które są zgodne z celami Australijskie informacje o bezpieczeństwie instytucji rządowych (ISM), które zostały opracowane przez australijską organizację sygnałów (ASD). Ten plan przedstawia wspólną architekturę referencyjną i pomaga w zapewnieniu właściwej obsługi poufnych danych rządowych w bezpiecznym, zgodnym środowisku wielowarstwowym.

Ta architektura referencyjna, przewodnik implementacji i model zagrożeń stanowią podstawę dla klientów, którzy podejmują własne procesy planowania i akredytacji systemu, ułatwiając klientom wdrażanie obciążeń na platformie Azure w sposób zgodny ze standardem ASD. Klienci mogą zdecydować się na wdrożenie usługi Azure VPN Gateway lub ExpressRoute do korzystania z usług federacyjnych oraz do integrowania zasobów lokalnych z zasobami platformy Azure. Klienci muszą rozważyć implikacje zabezpieczeń dotyczące korzystania z zasobów lokalnych. Dodatkowa konfiguracja jest wymagana w celu spełnienia wszystkich wymagań, ponieważ mogą się one różnić w zależności od implementacji poszczególnych klientów.

Osiągnięcie zgodności ze standardem ASD wymaga, aby administrator zarejestrowanych informacji na temat zabezpieczeń był przeprowadzany inspekcją systemu. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
To rozwiązanie służy do wdrażania architektury referencyjnej dla aplikacji sieci Web IaaS z zapleczem SQL Server. Architektura obejmuje warstwę sieci Web, warstwę danych, Active Directory infrastrukturę, Application Gateway i Load Balancer. Maszyny wirtualne wdrożone w warstwach sieci Web i danych są skonfigurowane w zestawie dostępności, a wystąpienia SQL Server są konfigurowane w grupie dostępności zawsze w celu zapewnienia wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy Active Directory są używane do wymuszania konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego. Host zarządzania bastionu zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów.

Architektura może zapewnić bezpieczne środowisko hybrydowe, które rozszerza sieć lokalną na platformę Azure, umożliwiając bezpieczny dostęp do obciążeń opartych na sieci Web przez użytkowników w prywatnej sieci lokalnej organizacji lub z Internetu. W przypadku rozwiązań lokalnych klient jest odpowiedzialny za wszystkie aspekty zabezpieczeń, operacji i zgodności.

Zasoby platformy Azure zawarte w tym rozwiązaniu mogą łączyć się z siecią lokalną lub z usługą wspólnej lokalizacji centrów danych (np. Funkcja przechwytywania w Canberra) za pośrednictwem protokołu IPSec w sieci VPN przy użyciu usługi Azure VPN Gateway lub za pośrednictwem ExpressRoute. Należy podjąć decyzję o użyciu sieci VPN z klasyfikacją przesyłanych danych i ścieżką sieciową. Klienci korzystający z dużej skali obciążeń o krytycznym znaczeniu z wymaganiami dotyczącymi danych Big Data powinni rozważyć architekturę sieci hybrydowej wykorzystującą ExpressRoute do łączności sieci prywatnej z usługami platformy Azure. Więcej informacji na temat mechanizmów połączeń z platformą Azure można znaleźć w sekcji wskazówki i zalecenia.

Federacji z Azure Active Directory należy użyć, aby umożliwić użytkownikom uwierzytelnianie przy użyciu poświadczeń lokalnych i dostęp do wszystkich zasobów w chmurze przy użyciu lokalnej infrastruktury Active Directory Federation Servicesowej. Active Directory Federation Services może zapewnić uproszczoną, zabezpieczoną Federację tożsamości i logowanie jednokrotne w sieci Web dla tego środowiska hybrydowego. Więcej informacji Azure Active Directory konfiguracji można znaleźć w sekcji wskazówki i zalecenia.

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Platforma Azure przechowuje trzy kopie danych w wybranym regionie klienta pod kątem odporności. Regiony platformy Azure są wdrażane w odpornych parach regionów, a geograficznie nadmiarowy magazyn gwarantuje, że dane zostaną zreplikowane do drugiego regionu z trzema kopiami. Zapobiega to niekorzystnemu zdarzeniu w podstawowej lokalizacji danych klienta, co spowodowało utratę danych.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Azure Active Directory kontroli dostępu opartej na rolach służy do kontrolowania dostępu do wdrożonych zasobów i kluczy w Azure Key Vault. Kondycja systemu jest monitorowana za poorednictwem Azure Security Center i Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym. Usługa Azure Application Gateway jest skonfigurowana jako Zapora w trybie zapobiegania i wymaga ruchu TLS z minimalną wersją 1,2.

![Aplikacja sieci Web IaaS dla architektury referencyjnej chronionej przez aktualizacje automatyczne](images/au-protected-iaaswa-architecture.png?raw=true "Aplikacja sieci Web IaaS dla diagramu architektury referencyjnej chronionej za pomocą funkcji au") 

To rozwiązanie używa następujących usług platformy Azure. Dalsze szczegóły znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Zestawy dostępności
    - (1) węzły klastra SQL
    - (1) sieć Web/IIS
- Usługa Azure Active Directory
- Azure Application Gateway
    - (1) Zapora aplikacji sieci Web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Port odbiornika: 443
- Monitor usługi Azure Cloud
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Dzienniki usługi Azure Monitor
- Azure Storage
- Usługa Azure Virtual Machines
    - (1) Zarządzanie/bastionu (Windows Server 2016 Datacenter)
    - (2) SQL Server węzeł klastra (SQL Server 2017 w systemie Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) sieciowe grupy zabezpieczeń
    - Azure Network Watcher
- Magazyn Recovery Services

Ten plan zawiera usługi platformy Azure, które nie zostały certyfikowane do użycia w chronionej klasyfikacji przez australijskie Centrum zabezpieczeń cybernetycznymi (ACSC). Wszystkie usługi uwzględnione w tej architekturze referencyjnej zostały certyfikowane przez ACSC na poziomie "DLM". Firma Microsoft zaleca, aby klienci przeglądali opublikowane raporty dotyczące zabezpieczeń i inspekcji związane z tymi usługami platformy Azure oraz korzystać z ich struktury zarządzania ryzykiem w celu określenia, czy usługa platformy Azure jest odpowiednia do celów wewnętrznych akredytacji i używania w Klasyfikacja chroniona.

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Diagnostyka Azure rozszerzenie](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie wdraża zasoby w architekturze z oddzielną podsiecią internetową, podsiecią bazy danych, podsiecią Active Directoryową i podsiecią zarządzania w sieci wirtualnej. Podsieci są logicznie oddzielone przez reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch między podsieciami tylko do tych, które są niezbędne dla funkcji systemu i zarządzania.

Zapoznaj się z konfiguracją [sieciowych grup zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożonych z tym rozwiązaniem. Organizacje mogą konfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej za pomocą [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako przewodnika.

Każda podsieć ma dedykowaną sieciową grupę zabezpieczeń:
- 1 sieciowa Grupa zabezpieczeń dla Application Gateway (LBNSG)
- 1 sieciowa Grupa zabezpieczeń dla hosta bastionu (MGTNSG)
- 1 sieciowa Grupa zabezpieczeń dla serwerów SQL i monitor chmury (SQLNSG)
- 1 sieciowa Grupa zabezpieczeń dla warstwy sieci Web (WEBNSG)

### <a name="data-in-transit"></a>Dane przesyłane
Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. 

W przypadku chronionych danych przesyłanych z sieci należących do klienta architektura korzysta z Internetu lub ExpressRoute z VPN Gateway skonfigurowany przy użyciu protokołu IPSEC.

Ponadto wszystkie transakcje na platformie Azure za pośrednictwem portalu zarządzania systemu Azure odbywają się za pośrednictwem protokołu HTTPS wykorzystującego protokół TLS 1,2.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to w ochronie i ochronie danych w celu zapewnienia obsługi zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności określonych przez australijski rząd ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**SQL Server**: W wystąpieniu SQL Server są stosowane następujące miary zabezpieczeń bazy danych:
-   [Inspekcja SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) śledzi zdarzenia bazy danych i zapisuje je w dziennikach inspekcji.
-   [Szyfrowanie danych przezroczystych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) wykonuje szyfrowanie w czasie rzeczywistym oraz odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w celu ochrony informacji przechowywanych w pamięci podręcznej. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) ogranicza narażenie na dane poufne przez maskowanie danych do użytkowników, którzy nie mają uprawnień lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pozwala to ograniczyć dostęp do tego, że dane poufne nie opuszczają bazy danych za pośrednictwem nieautoryzowanego dostępu. **Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Klienci mogą korzystać z usług federacyjnych Active Directory lokalnych, aby sfederować z usługą [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), która to usługa oparta na chmurze usługi zarządzania tożsamościami w ramach wielu dzierżawców firmy Microsoft. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integruje katalogi lokalne z Azure Active Directory. Wszyscy użytkownicy w tym rozwiązaniu wymagają kont Azure Active Directory. Logowanie federacyjne pozwala użytkownikom na logowanie się do Azure Active Directory i uwierzytelnianie w zasobach platformy Azure przy użyciu poświadczeń lokalnych.

Ponadto następujące funkcje Azure Active Directory ułatwiają zarządzanie dostępem do danych w środowisku platformy Azure:
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pozwala klientom zminimalizować liczbę użytkowników, którzy mają dostęp do określonych informacji. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalne luki w zabezpieczeniach,&#39;które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji&#39;i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

**Uwierzytelnianie wieloskładnikowe systemu Azure**: Aby chronić tożsamości, należy zaimplementować uwierzytelnianie wieloskładnikowe. [Uwierzytelnianie wieloskładnikowe systemu Azure](https://azure.microsoft.com/services/multi-factor-authentication/) to łatwe w użyciu, skalowalne i niezawodne rozwiązanie, które zapewnia drugą metodę uwierzytelniania w celu ochrony użytkowników. Uwierzytelnianie wieloskładnikowe systemu Azure używa możliwości chmury i integruje się z lokalnymi Active Directory i aplikacjami niestandardowymi. Ta ochrona jest rozszerzona o duże ilościowe scenariusze o kluczowym znaczeniu.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie**wpisami tajnymi: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić i uzyskiwać dostęp do tych danych:

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowym modułem zabezpieczeń chronionym 2048-bitowym kluczem RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.
- Rozwiązanie jest zintegrowane z Azure Key Vault zarządzania dyskami maszyny wirtualnej IaaS — kluczami szyfrowania i wpisami tajnymi.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia w celu zastosowania poprawek do maszyn wirtualnych w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) Virtual Machines zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami, gdy znane złośliwe lub niepożądane oprogramowanie próbuje Zainstaluj lub Uruchom na chronionych maszynach wirtualnych.

**Azure Security Center**: Dzięki [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

Ponadto ta architektura referencyjna wykorzystuje [ocenę luk](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w zabezpieczeniach w Azure Security Center. Po skonfigurowaniu Agent partnera (np. Qualys) zgłasza luki w zabezpieczeniach do platformy zarządzania partnera. Z kolei Platforma zarządzania przez partnera zapewnia informacje dotyczące luk i monitorowania kondycji z powrotem do Azure Security Center, dzięki czemu klienci mogą szybko identyfikować zagrożone maszyny wirtualne.

**Application Gateway platformy Azure**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway platformy Azure z skonfigurowaną zaporą aplikacji sieci Web, a zestaw reguł OWASP jest włączony. Dodatkowe możliwości obejmują:

- [Kompleksowa — SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
**Wysoka dostępność**: Rozwiązanie wdraża wszystkie maszyny wirtualne w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone w wielu izolowanych klastrach sprzętowych w celu zwiększenia dostępności. Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

**Monitor chmury**: [Monitor w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu failover w systemie Windows Server 2016, który wykorzystuje platformę Azure jako punkt rozstrzygania. Monitor w chmurze, taki jak każdy inny monitor kworum, otrzymuje głos i może uczestniczyć w obliczeniach kworum, ale korzysta ze standardowego publicznie dostępnego Blob Storage platformy Azure. Eliminuje to dodatkowe obciążenie pracą maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja
Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych, dzięki czemu wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z usługi Azure SQL Server. Rozwiązanie [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Azure Network Watcher to usługa, która udostępnia narzędzia umożliwiające monitorowanie, diagnozowanie, wyświetlanie metryk i włączanie lub wyłączanie dzienników zasobów w sieci wirtualnej platformy Azure.  Jednostki Wspólnoty powinny implementować Network Watcher dzienników przepływów dla sieciowych grup zabezpieczeń i Virtual Machines. Dzienniki te powinny być przechowywane na dedykowanym koncie magazynu, w którym są przechowywane tylko dzienniki zabezpieczeń, a dostęp do konta magazynu powinien być zabezpieczony przy użyciu kontroli dostępu opartej na rolach.

## <a name="threat-model"></a>Model zagrożeń
Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/au-protected-iaaswa-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![Aplikacja sieci Web IaaS dla modelu zagrożeń chronionych przez au](images/au-protected-iaaswa-threat-model.png?raw=true "Aplikacja sieci Web IaaS dla diagramu modelu zagrożeń chronionych przez aktualizacje automatyczne")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
Ta dokumentacja dotycząca zgodności jest generowana przez firmę Microsoft w oparciu o platformy i usługi dostępne w firmie Microsoft. Z powodu szerokiej gamy wdrożeń klientów Ta dokumentacja zawiera uogólnione podejście do rozwiązania tylko hostowanego w środowisku platformy Azure. Klienci mogą identyfikować i korzystać z alternatywnych produktów i usług na podstawie własnych środowisk operacyjnych i wyników firmy. Klienci korzystający z zasobów lokalnych muszą zająć się zabezpieczeniami i operacjami dotyczącymi zasobów lokalnych. Udokumentowane rozwiązanie może być dostosowywane przez klientów w celu rozwiązania określonych wymagań dotyczących lokalnego i bezpieczeństwa.

[Macierz odpowiedzialności klienta strategia zabezpieczeń i zgodności z przepisami platformy Azure — chroniona za pomocą funkcji aktualizacji automatycznych](https://aka.ms/au-protected-crm) zawiera listę wszystkich kontroli zabezpieczeń wymaganych przez aktualizacje automatyczne. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji aplikacji sieci Web IaaS — chronionej strategia zabezpieczeń i zgodności z przepisami platformy Azure za pomocą funkcji au](https://aka.ms/au-protected-iaaswa-cim) — jest dostępna w oparciu o architekturę aplikacji sieci Web IaaS, w tym szczegółowe opisy jak implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
W przypadku informacji klasyfikowanych bezpieczny tunel VPN protokołu IPSec musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web IaaS. Przez odpowiednie skonfigurowanie sieci VPN IPSec, klienci mogą dodać warstwę ochrony danych do przetworzenia.

Przez implementację bezpiecznego tunelu sieci VPN IPSec z platformą Azure można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie może odbywać się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. 

Ponieważ ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje opcję połączenia prywatnego. Usługa Azure ExpressRoute to dedykowany link między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange, który jest traktowany jako sieć prywatna. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure. 

Aby chronić dane klasyfikowane przy użyciu Internetu lub usługi Azure ExpressRoute, klienci muszą skonfigurować sieci VPN IPSec, stosując następujące ustawienia:

• Inicjator sieci VPN klienta musi być skonfigurowany dla okresu istnienia skojarzenia zabezpieczeń, który nie jest większy niż 14400 sekund.
• Inicjator sieci VPN klienta musi wyłączyć tryb agresywny protokołu IKEv1.
• Inicjator sieci VPN klienta musi skonfigurować doskonałe utajnienie przekazywania dalej.
• Inicjator sieci VPN klienta musi skonfigurować użycie algorytmu HMAC-SHA256 lub nowszego.

Opcje konfiguracji dla urządzeń sieci VPN i parametrów protokołu IPSec/IKE są [dostępne](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) do przeglądu.

### <a name="azure-active-directory-setup"></a>Konfiguracja Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ma zasadnicze znaczenie dla zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Istniejące Active Directory systemu Windows Server można zintegrować z Azure Active Directory za pomocą [czterech kliknięć](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Ponadto [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) umożliwia klientom Konfigurowanie Federacji przy użyciu lokalnych [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) i Azure Active Directory. Za pomocą logowania federacyjnego klienci mogą umożliwić użytkownikom logowanie się do usług opartych na Azure Active Directory przy użyciu haseł lokalnych i bez konieczności ponownego wprowadzania haseł w sieci firmowej. Za pomocą opcji federacyjnej z Active Directory Federation Services można wdrożyć nową instalację Active Directory Federation Services lub można określić istniejącą instalację w farmie systemu Windows Server 2012 R2.

Aby zapobiec synchronizacji danych sklasyfikowanych do Azure Active Directory, klienci mogą ograniczyć atrybuty, które są replikowane do Azure Active Directory, stosując następujące ustawienia w Azure Active Directory Connect:
- [Włącz filtrowanie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Wyłącz synchronizację skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [Wyłącz zapisywanie zwrotne haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Wyłącz zapisywanie zwrotne urządzeń](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Pozostaw ustawienia domyślne, aby [zapobiec przypadkowemu usuwaniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) i [automatycznym uaktualnianiu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)

## <a name="disclaimer"></a>Zastrzeżenie
- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
