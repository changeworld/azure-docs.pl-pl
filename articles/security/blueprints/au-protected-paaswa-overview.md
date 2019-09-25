---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla Australii
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla Australii
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 6d1b84894439010e5297ce010b2ece6dea8f3e56
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257602"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla Australii

## <a name="overview"></a>Przegląd

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące wdrażania środowiska platformy jako usługi (PaaS) odpowiednie do zbierania, przechowywania i pobierania danych administracji publicznej, które są zgodne z celami Australijskie informacje o bezpieczeństwie instytucji rządowych (ISM), które zostały opracowane przez australijską organizację sygnałów (ASD). Ten plan przedstawia wspólną architekturę referencyjną i pomaga w zapewnieniu właściwej obsługi poufnych danych rządowych w bezpiecznym, zgodnym środowisku wielowarstwowym.

Ta architektura referencyjna, przewodnik implementacji i model zagrożeń stanowią podstawę dla klientów, którzy podejmują własne procesy planowania i akredytacji systemu, ułatwiając klientom wdrażanie obciążeń na platformie Azure w sposób zgodny ze standardem ASD. Klienci mogą zdecydować się na wdrożenie usługi Azure VPN Gateway lub ExpressRoute do korzystania z usług federacyjnych oraz do integrowania zasobów lokalnych z zasobami platformy Azure. Klienci muszą rozważyć implikacje zabezpieczeń dotyczące korzystania z zasobów lokalnych. Dodatkowa konfiguracja jest wymagana w celu spełnienia wszystkich wymagań, ponieważ mogą się one różnić w zależności od implementacji poszczególnych klientów.

Osiągnięcie zgodności ze standardem ASD wymaga, aby administrator zarejestrowanych informacji na temat zabezpieczeń był przeprowadzany inspekcją systemu. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
To rozwiązanie zapewnia architekturę referencyjną dla aplikacji sieci Web PaaS z zapleczem Azure SQL Database. Aplikacja sieci Web jest hostowana w izolowanym Azure App Service Environment, czyli prywatnym, dedykowanym środowisku w centrum Datacentre. Obciążenie środowiska równoważenia obciążenia dla aplikacji sieci Web między maszynami wirtualnymi zarządzanymi przez platformę Azure. Wszystkie połączenia aplikacji sieci Web wymagają protokołu TLS o minimalnej wersji 1,2. Ta architektura obejmuje również sieciowe grupy zabezpieczeń, Application Gateway, Azure DNS i Load Balancer.

Architektura może zapewnić bezpieczne środowisko hybrydowe, które rozszerza sieć lokalną na platformę Azure, umożliwiając bezpieczny dostęp do obciążeń opartych na sieci Web przez użytkowników w prywatnej sieci lokalnej organizacji lub z Internetu. W przypadku rozwiązań lokalnych klient jest odpowiedzialny za wszystkie aspekty zabezpieczeń, operacji i zgodności.

Zasoby platformy Azure zawarte w tym rozwiązaniu mogą łączyć się z siecią lokalną lub obiektem kolokacji centrów danych (np. Funkcja przechwytywania zmian w Canberra) za pośrednictwem sieci VPN IPSec przy użyciu VPN Gateway i ExpressRoute. Należy podjąć decyzję o użyciu sieci VPN z klasyfikacją przesyłanych danych i ścieżką sieciową. Klienci korzystający z dużej skali obciążeń o krytycznym znaczeniu z wymaganiami dotyczącymi danych Big Data powinni rozważyć architekturę sieci hybrydowej wykorzystującą ExpressRoute do łączności sieci prywatnej z usługami platformy Azure. Więcej informacji na temat mechanizmów połączeń z platformą Azure można znaleźć w sekcji [wskazówki i zalecenia](#guidance-and-recommendations) .

Federacji z Azure Active Directory należy użyć, aby umożliwić użytkownikom uwierzytelnianie przy użyciu poświadczeń lokalnych i dostęp do wszystkich zasobów w chmurze przy użyciu lokalnej infrastruktury Active Directory Federation Servicesowej. Active Directory Federation Services może zapewnić uproszczoną, zabezpieczoną Federację tożsamości i logowanie jednokrotne w sieci Web dla tego środowiska hybrydowego. Więcej informacji Azure Active Directory konfiguracji można znaleźć w sekcji [wskazówki i zalecenia](#guidance-and-recommendations) .

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Platforma Azure przechowuje trzy kopie danych w wybranym regionie klienta pod kątem odporności. Regiony platformy Azure są wdrażane w odpornych parach regionów, a geograficznie nadmiarowy magazyn gwarantuje, że dane zostaną zreplikowane do drugiego regionu z trzema kopiami. Zapobiega to niekorzystnemu zdarzeniu w podstawowej lokalizacji danych klienta, co spowodowało utratę danych.

W celu zwiększenia bezpieczeństwa wszystkie zasoby platformy Azure w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Azure Active Directory kontroli dostępu opartej na rolach służy do kontrolowania dostępu do wdrożonych zasobów i kluczy w Azure Key Vault. Kondycja systemu jest monitorowana za poorednictwem Azure Security Center i Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym. Usługa Azure Application Gateway jest skonfigurowana jako Zapora w trybie zapobiegania i nie zezwala na ruch, który nie jest zgodny z protokołem TLS w wersji 1.2 lub nowszej. Rozwiązanie korzysta z środowiska Azure Application Service Environment w wersji 2 w celu odizolowania warstwy sieci Web w środowisku innym niż wiele dzierżawców.

![Aplikacja sieci Web PaaS dla architektury referencyjnej chronionej przez aktualizacje automatyczne](images/au-protected-paaswa-architecture.png?raw=true "Aplikacja sieci Web PaaS dla diagramu architektury referencyjnej chronionej za pomocą funkcji au")

To rozwiązanie używa następujących usług platformy Azure. Dalsze szczegóły znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Application Gateway
    - Zapora aplikacji internetowej
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP
        - Port odbiornika: 443
- Application Insights
- Usługa Azure Active Directory
- Środowisko usługi aplikacji platformy Azure w wersji 2
- Azure Automation
- System DNS platformy Azure
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Dzienniki usługi Azure Monitor
- Azure Virtual Network
    - (1)/16 sieci
    - (4)/24 sieci
    - Grupy zabezpieczeń sieci
- Grupy zabezpieczeń sieci
- Magazyn usługi Recovery Services
- Aplikacja sieci Web platformy Azure

Ten plan zawiera usługi platformy Azure, które nie zostały certyfikowane do użycia w chronionej klasyfikacji przez australijskie Centrum zabezpieczeń cybernetycznymi (ACSC). Firma Microsoft zaleca, aby klienci przeglądali opublikowane raporty dotyczące zabezpieczeń i inspekcji związane z tymi usługami platformy Azure oraz korzystać z ich struktury zarządzania ryzykiem w celu określenia, czy usługa platformy Azure jest odpowiednia do celów wewnętrznych akredytacji i używania w Klasyfikacja chroniona.

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia klientom współpracę z zasobami w rozwiązaniu jako Grupa. Klienci mogą wdrażać, aktualizować i usuwać wszystkie zasoby rozwiązania w ramach jednej, skoordynowanej operacji. Klienci używają szablonu do wdrożenia i ten szablon może współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Menedżer zasobów zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające klientom zarządzanie zasobami po wdrożeniu.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane

**App Service Environment v2**: [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) to funkcja App Service, która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji App Service na dużą skalę.

Środowiska App Service są izolowane wyłącznie do uruchamiania aplikacji jednego klienta i są zawsze wdrażane w sieci wirtualnej. Klienci mają szczegółową kontrolę nad ruchem przychodzącym i wychodzącym ruchu sieciowego aplikacji, a aplikacje mogą nawiązywać bezpieczne połączenia za pośrednictwem sieci wirtualnych z lokalnymi zasobami firmowymi.

Użycie środowisk App Service dla tej architektury pozwala na następujące kontrolki/konfiguracje:

- Środowiska App Service muszą być skonfigurowane tak, aby korzystały z odizolowanego planu usługi
    - Skonfiguruj różne środowiska App Service dla aplikacji pod różnymi klasyfikacjami
- Host w ramach zabezpieczonej sieci wirtualnej platformy Azure i reguł zabezpieczeń sieciowych
- Środowiska App Service skonfigurowane z certyfikatem wewnętrznego modułu równoważenia obciążenia z podpisem własnym na potrzeby komunikacji przy użyciu protokołu HTTPS. Najlepszym rozwiązaniem jest to, że firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.
- [Tryb wewnętrznego równoważenia obciążenia](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (tryb 3)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Zmień [szyfr TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Kontrola [portów ruchu przychodzącego (N/s](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md) )
- [Zapora aplikacji sieci Web — ograniczanie danych](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Zezwalaj na [ruch Azure SQL Database](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Aplikacja internetowa platformy Azure**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie aplikacji sieci Web w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps Services oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy kontroli dostępu, które zezwalają na ruch lub go odmawiają w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń mogą służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:
- 1 sieciowa Grupa zabezpieczeń dla Application Gateway
- 1 sieciowa Grupa zabezpieczeń dla App Service Environment
- 1 sieciowa Grupa zabezpieczeń dla Azure SQL Database
- 1 sieciowa Grupa zabezpieczeń dla hosta bastionu

Każda z sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdej sieciowej grupy zabezpieczeń są włączone następujące konfiguracje:

  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Dzienniki Azure Monitor są połączone z [diagnostyką sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z odpowiednią sieciową grupą zabezpieczeń.

**Azure DNS**: System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozwiązanie) nazwy witryny sieci Web lub usługi na adres IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure umożliwia użytkownikom Zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. Azure DNS obsługuje również prywatne domeny DNS.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umożliwia klientom skalowanie aplikacji i tworzenie wysokiej dostępności dla usług. Load Balancer obsługuje przychodzące oraz scenariusze wychodzące oraz zapewnia małe opóźnienia i wysoką przepływność oraz skaluje się do milionów przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Dane przesyłane
Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. 

W przypadku chronionych danych przesyłanych z sieci należących do klienta architektura korzysta z usługi Azure Internet lub ExpressRoute z VPN Gateway skonfigurowanym przy użyciu protokołu IPSEC.

Ponadto wszystkie transakcje na platformie Azure za pośrednictwem portalu zarządzania systemu Azure odbywają się za pośrednictwem protokołu HTTPS wykorzystującego protokół TLS w wersji 1.2.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Pomaga to w ochronie i ochronie danych w celu wspierania zobowiązań w zakresie bezpieczeństwa organizacji i wymagań dotyczących zgodności określonych przez australijski rząd ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:
-   [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   Azure SQL Database jest skonfigurowany do korzystania z funkcji [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), która wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w czasie rzeczywistym. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych. Wykrywanie zagrożeń SQL integruje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecanej akcji na sposobach badania i łagodzenia zagrożeń.
-   [Always Encrypted kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do użytkowników nieuprzywilejowanych lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pozwala to ograniczyć dostęp do tego, że dane poufne nie opuszczają bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci będą musieli dostosować ustawienia dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Klienci mogą korzystać z usług federacyjnych Active Directory lokalnych, aby sfederować z usługą [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), która to usługa oparta na chmurze usługi zarządzania tożsamościami w ramach wielu dzierżawców firmy Microsoft. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) integruje katalogi lokalne z Azure Active Directory. Wszyscy użytkownicy w tym rozwiązaniu wymagają kont Azure Active Directory, w tym użytkowników uzyskujących dostęp do Azure SQL Database. Logowanie federacyjne pozwala użytkownikom na logowanie się do Azure Active Directory i uwierzytelnianie w zasobach platformy Azure przy użyciu poświadczeń lokalnych.

Ponadto następujące funkcje Azure Active Directory ułatwiają zarządzanie dostępem do danych w środowisku platformy Azure:
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pozwala klientom zminimalizować liczbę użytkowników, którzy mają dostęp do określonych informacji. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

**Multi-Factor Authentication platformy Azure**: Aby chronić tożsamości, należy zaimplementować uwierzytelnianie wieloskładnikowe. [Usługa Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) to łatwe w użyciu, skalowalne i niezawodne rozwiązanie, które zapewnia drugą metodę uwierzytelniania w celu ochrony użytkowników. Platforma Azure Multi-Factor Authentication używa możliwości chmury i integruje się z lokalnymi Active Directory i aplikacjami niestandardowymi. Ta ochrona jest rozszerzona o duże ilościowe scenariusze o kluczowym znaczeniu.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić dane:
- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowym modułem zabezpieczeń chronionym 2048-bitowym kluczem RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Azure Security Center**: Dzięki [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

**Application Gateway**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway z zaporą aplikacji sieci Web, a włączono zestaw reguł OWASP. Dodatkowe możliwości obejmują:

- [Kompleksowa — SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/resource-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych, dzięki czemu wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Azure SQL Database. Rozwiązanie [Change Tracking](../../automation/change-tracking.md) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

Azure Network Watcher: [Usługa Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) udostępnia narzędzia do monitorowania, diagnozowania, wyświetlania metryk i włączania i wyłączania dzienników dla zasobów w sieci wirtualnej platformy Azure.  Jednostki Wspólnoty powinny implementować Network Watcher dzienników przepływów dla sieciowych grup zabezpieczeń i Virtual Machines. Dzienniki te powinny być przechowywane na dedykowanym koncie magazynu, w którym są przechowywane tylko dzienniki zabezpieczeń, a dostęp do konta magazynu powinien być zabezpieczony przy użyciu kontroli dostępu opartej na rolach.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/au-protected-paaswa-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![Aplikacja sieci Web PaaS dla modelu zagrożeń chronionych przez au](images/au-protected-paaswa-threat-model.png?raw=true "Aplikacja sieci Web PaaS dla diagramu modelu zagrożeń chronionych przez aktualizacje automatyczne")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
Ta dokumentacja dotycząca zgodności jest generowana przez firmę Microsoft w oparciu o platformy i usługi dostępne w firmie Microsoft. Z powodu szerokiej gamy wdrożeń klientów Ta dokumentacja zawiera uogólnione podejście do rozwiązania tylko hostowanego w środowisku platformy Azure. Klienci mogą identyfikować i korzystać z alternatywnych produktów i usług na podstawie własnych środowisk operacyjnych i wyników firmy. Klienci korzystający z zasobów lokalnych muszą zająć się zabezpieczeniami i operacjami dotyczącymi zasobów lokalnych. Udokumentowane rozwiązanie może być dostosowywane przez klientów w celu rozwiązania określonych wymagań dotyczących lokalnego i bezpieczeństwa.

W [macierzy odpowiedzialności klienta w strategia zabezpieczeń i zgodności z przepisami platformy Azure-au —](https://aka.ms/au-protected-crm) lista wszystkich kontroli zabezpieczeń wymaganych przez program prot. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji aplikacji sieci Web w strategia zabezpieczeń i zgodności z przepisami platformy Azure PaaS-au-Protected](https://aka.ms/au-protected-paaswa-cim) zawiera informacje o tym, które kontrolki chronione za pomocą funkcji au są rozwiązywane przez architekturę aplikacji sieci Web PaaS, w tym szczegółowe opisy jak implementacja spełnia wymagania poszczególnych formantów objętych usługą.

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
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) ma zasadnicze znaczenie dla zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Istniejące Active Directory systemu Windows Server można zintegrować z Azure Active Directory za pomocą [czterech kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md).

Ponadto [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) umożliwia klientom Konfigurowanie Federacji przy użyciu lokalnych [Active Directory Federation Services]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) i Azure Active Directory. Za pomocą logowania federacyjnego klienci mogą umożliwić użytkownikom logowanie się do usług opartych na Azure Active Directory przy użyciu haseł lokalnych i bez konieczności ponownego wprowadzania haseł w sieci firmowej. Za pomocą opcji federacyjnej z Active Directory Federation Services można wdrożyć nową instalację Active Directory Federation Services lub można określić istniejącą instalację w farmie systemu Windows Server 2012 R2.

Aby zapobiec synchronizacji danych sklasyfikowanych do Azure Active Directory, klienci mogą ograniczyć atrybuty, które są replikowane do Azure Active Directory, stosując następujące ustawienia w Azure Active Directory Connect:

- [Włącz filtrowanie](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Wyłącz synchronizację skrótów haseł](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
-   [Wyłącz zapisywanie zwrotne haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Wyłącz zapisywanie zwrotne urządzeń](../../active-directory/hybrid/how-to-connect-device-writeback.md)
-   Pozostaw ustawienia domyślne, aby [zapobiec przypadkowemu usuwaniu](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) i [automatycznym uaktualnianiu](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md)


## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
