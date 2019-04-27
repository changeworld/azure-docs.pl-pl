---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla Australii CHRONIONA
description: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla Australii CHRONIONA
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: c17f16ce796c9f296facd69c18de4effc7ff5258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610164"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla Australii CHRONIONA

## <a name="overview"></a>Omówienie

Tych samych zabezpieczeń platformy Azure i zgodności planu znajdują się wskazówki dotyczące wdrażania to platforma jako usługa (PaaS) środowiska odpowiednie do zbierania, przechowywania i pobierania danych chronionych Australia dla instytucji rządowych, które są zgodne z celami Australijskich instytucji rządowych informacje zabezpieczeń ręczne (ISM) produkowane przez australijskiego Signals Directorate ASD (). Ten plan przedstawiono typowe architektury referencyjnej i pomaga pokazują prawidłowego przygotowania danych poufnych dla instytucji rządowych w środowisku bezpiecznych, zgodnych i wieloma warstwami.

Tej architektury referencyjnej, przewodnik wdrażania i modelu zagrożeń Podaj podstawa klientom podejmować własnych planowania i system akredytacji procesów, pomagając klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z ASD. Klienci mają możliwość wdrożenia usługi Azure VPN Gateway lub ExpressRoute za pomocą usług federacyjnych oraz integrować ją lokalnych zasobów z zasobami platformy Azure. Klienci, należy wziąć pod uwagę ryzyko związane z korzystania z zasobów lokalnych. Dodatkowa konfiguracja jest wymagane do spełnienia wymagań, ponieważ mogą się różnić zależnie od szczegółowe informacje na temat implementacji każdego klienta.

Osiągnięcie zgodności ASD wymaga, że informacje zabezpieczeń, zarejestrowanego rzeczoznawcę ds inspekcji systemu. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie zapewnia architekturę referencyjną dla aplikacji sieci web PaaS za pomocą wewnętrznej bazy danych Azure SQL Database. Aplikacja sieci web znajduje się w izolowanej Azure App Service Environment, który jest prywatnym, dedykowanym środowisku platformy Azure centra danych. Środowisko równoważy obciążenie dla aplikacji sieci web na maszynach wirtualnych zarządzanych przez platformę Azure. Wszystkie połączenia aplikacji sieci web wymaga protokołu TLS, co najmniej w wersji 1.2. Taka architektura obejmuje również sieciowych grup zabezpieczeń, usługa Application Gateway, usługi Azure DNS i modułu równoważenia obciążenia.

Architektura może dostarczać środowiska bezpieczną hybrydową, która rozszerza sieć lokalną na platformę Azure, umożliwiając obciążeń opartych na sieci web na dostęp do bezpiecznego firmy użytkownicy w organizacji prywatnej sieci lokalnej lub z Internetu. W przypadku rozwiązań lokalnych klient jest odpowiedzialne i odpowiada za wszystkie aspekty zabezpieczeń, działań i zgodności.

Zasoby platformy Azure zawartych w tym rozwiązaniu mogą łączyć się z lokalną sieć lub centra danych funkcji wspólnej lokalizacji (np. Przechwytywanie zmian danych Canberra) za pośrednictwem protokołu IPSec VPN przy użyciu bramy sieci VPN i za pośrednictwem usługi ExpressRoute. Decyzja o korzystanie z sieci VPN powinna być podejmowana z klasyfikacją przesyłanych danych i ścieżka sieciowa, należy pamiętać. Klienci, którzy hostują na dużą skalę, obciążeń o znaczeniu krytycznym, za pomocą wymagających danych big Data, należy rozważyć architektury sieci hybrydowej za pomocą usługi ExpressRoute w sieci prywatnej łączności z usługami platformy Azure. Zapoznaj się [wskazówki i zalecenia](#guidance-and-recommendations) sekcji, aby uzyskać więcej szczegółowych informacji dotyczących mechanizmów połączeń opartych na platformie Azure.

Aby umożliwić użytkownikom uwierzytelnianie przy użyciu poświadczeń lokalnych i dostęp do wszystkich zasobów w chmurze przy użyciu lokalnej infrastruktury usług Active Directory Federation Services, stosuje się federacji z usługą Azure Active Directory. Active Directory Federation Services zapewniają uproszczoną, zabezpieczoną tożsamości federacyjnych i sieci web jednej funkcji logowania jednokrotnego dla tego środowiska hybrydowego. Zapoznaj się [wskazówki i zalecenia](#guidance-and-recommendations) sekcji dalsze szczegóły dotyczące usługi Azure Active Directory Instalatora.

Rozwiązanie używa konta usługi Azure Storage, w których klienci mogą skonfigurować na potrzeby zachowania poufności danych magazynowanych szyfrowanie usługi Storage. Azure przechowuje trzy kopie danych w wybranym regionie klienta pod kątem odporności. Regiony platformy Azure są wdrażane w parach regionów odporne na błędy i geograficzne magazyn nadmiarowy gwarantuje, czy dane będą replikowane w drugim regionie przy użyciu także trzy kopie. Zapobiega to zdarzenie niekorzystny lokalizacji danych podstawowych klienta, co spowoduje utratę danych.

Aby zwiększyć bezpieczeństwo wszystkich zasobów platformy Azure, w tym rozwiązaniu są zarządzane jako grupy zasobów za pomocą usługi Azure Resource Manager. Kontroli dostępu opartej na rolach w usłudze Azure Active Directory jest używane do kontrolowania dostępu do zasobów i wdrażać w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Security Center i Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego. Usługa Azure Application Gateway jest skonfigurowany jako zapory w trybie zapobiegania i nie zezwalają na ruch, który nie jest protokół TLS 1.2 lub nowszej. Środowiska usługi aplikacji Azure w wersji 2 do izolowania warstwa sieci web w środowisku niż dzierżawcy multi korzysta z rozwiązania.

![Aplikacja sieci Web PaaS dla architektury referencyjnej chronione AU](images/au-protected-paaswa-architecture.png?raw=true "aplikacji sieci Web PaaS dla chronionych Australia referencyjny Diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Dalsze szczegółowe informacje na temat [architektura wdrożenia](#deployment-architecture) sekcji.

- Application Gateway
    - Zapora aplikacji internetowej
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP
        - Port odbiornika: 443
- Application Insights
- Usługa Azure Active Directory
- Środowiska usługi aplikacji Azure w wersji 2
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
    - (1) /16 Network
    - (4) /24 Networks
    - Grupy zabezpieczeń sieci
- Grupy zabezpieczeń sieci
- Magazyn usługi Recovery Services
- Aplikacja sieci Web platformy Azure

Ten plan zawiera usług platformy Azure, które nie mają certyfikatu do użycia w klasyfikacji chronione przez australijskiego Centrum zabezpieczeń Cybernetycznymi (ACSC). Firma Microsoft zaleca klientom Przejrzyj opublikowanych zabezpieczenia i raporty z audytów związane z tych usług platformy Azure i użyj ich struktury zarządzania ryzykiem, aby określić, czy usługa Azure nadaje się do ich wewnętrzne akredytacji i używać Klasyfikacja chronionych.

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa Azure Resource Manager**: [Usługa Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia klientom pracę z zasobami w rozwiązaniu jako grupa. Klientów można wdrożyć, zaktualizować lub usunąć wszystkie zasoby dla rozwiązania w jednej, skoordynowanej operacji. Klienci dla wdrożenia wykonuje się przy użyciu szablonu, którego można używać w różnych środowiskach, takich jak testowanie, etap przejściowy i produkcja. Menedżer zasobów zapewnia zabezpieczeń, inspekcji i znakowania funkcje, aby pomóc klientom w zarządzaniu zasobami po wdrożeniu.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane

**Usługa App Service Environment w wersji 2**: [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) to funkcja usługi App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji usługi App Service w dużej skali.

Środowiska usługi App Service są izolowane do uruchamiania tylko jednego klienta aplikacji i są zawsze wdrażane w sieci wirtualnej. Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacje mogą nawiązywać bezpieczne szybkie połączenia za pośrednictwem wirtualnych sieci lokalnych zasobów firmowych.

Korzystanie z środowisk usługi App Service dla tej architektury zezwalanie na następujące kontrolki/konfiguracje:

- Środowiska usługi App Service musi być skonfigurowany do używania plan usług typu izolowany
    - Konfigurowanie różnych środowisk usługi App Service dla aplikacji na różne klasyfikacje
- Hostowanie w zabezpieczonej sieci wirtualnej platformy Azure i reguł zabezpieczeń sieciowych
- Środowiska usługi App Service jest skonfigurowany przy użyciu certyfikatu modułu równoważenia obciążenia z podpisem własnym wewnętrznego dla komunikacji HTTPS. Najlepszym rozwiązaniem firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.
- [Trybu wewnętrznego równoważenia obciążenia](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (tryb 3)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Zmiana [szyfrowania TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrolka [ruchu N/M porty wejściowe](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Zapora aplikacji sieci Web — ograniczanie danych](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zezwalaj na [ruch usługi Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Aplikacja sieci Web platformy Azure**: [Usługa Azure App Service](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie wybranych przez nich aplikacji sieci web w języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps Services oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Virtual Network
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie poszczególnych maszyn wirtualnych lub podsieci. Istnieją następujące grupy zabezpieczeń sieci:
- 1 sieciowej grupy zabezpieczeń w usłudze Application Gateway
- 1 sieciowej grupy zabezpieczeń dla środowiska App Service Environment
- 1 sieciowej grupy zabezpieczeń usługi Azure SQL Database
- 1 sieciowej grupy zabezpieczeń dla hostem bastionu

Każdej z grup zabezpieczeń sieci ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:

  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
  - Dzienniki platformy Azure Monitor jest podłączony do [sieciowe grupy zabezpieczeń, diagnostyki](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z sieciową grupą zabezpieczeń odpowiednich.

**Usługa Azure DNS**: System nazw domen lub DNS, odpowiada za tłumaczenia (lub rozpoznawanie) nazwę witryny sieci Web lub usługi na jej adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to Usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure, użytkownicy mogą zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co inne usługi platformy Azure. Usługa DNS platformy Azure obsługuje również prywatne domen DNS.

**Usługa Azure Load Balancer**: [Usługa Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pozwala klientom na skalowanie swoich aplikacji i zapewniać wysoką dostępność usług. Modułu równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego, a także ruchu wychodzącego i zapewnia małe opóźnienia i wysoką przepływność oraz skaluje nawet miliony przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. 

W przypadku chronionych danych w drodze od klienta do sieci architektura używa platformy Azure Internet lub usługi ExpressRoute za pośrednictwem bramy sieci VPN skonfigurowane przy użyciu protokołu IPSEC.

Ponadto wszystkie transakcje na platformie Azure za pośrednictwem portalu zarządzania systemu Azure występować za pośrednictwem protokołu HTTPS przy użyciu protokołu TLS 1.2.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych na rzecz organizacji bezpieczeństwa i definicją australijskiej ISM dla instytucji rządowych wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Azure SQL Database**: Wystąpienie usługi Azure SQL Database wykorzystuje następujące środki bezpieczeństwa bazy danych:
-   [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Usługa Azure SQL Database jest skonfigurowany do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce. Funkcja wykrywania zagrożeń SQL integruje alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), który zawiera szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. To pomaga zmniejszyć dostępu w taki sposób, że poufne dane nie istnieje baza danych za pośrednictwem przed nieautoryzowanym dostępem. Klienci będą musieli dostosować dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Klienci mogą używać lokalnych Active Directory Federation Services federowania z [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/), której to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integruje się w katalogach lokalnych z usługą Azure Active Directory. Wszyscy użytkownicy w tym rozwiązaniu wymaga kont usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do usługi Azure SQL Database. Za pomocą logowania federacyjnego użytkownicy mogą zalogować się do usługi Azure Active Directory i uwierzytelniania w zasobach platformy Azure przy użyciu poświadczeń lokalnych.

Ponadto następujące funkcje usługi Azure Active Directory ułatwia zarządzanie dostępem do danych w środowisku platformy Azure:
- Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast wszystkie uprawnienia użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji. Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i wsparcia podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

**Usługa Azure Multi-Factor Authentication**: Aby chronić tożsamości, powinny zostać wdrożone usługi Multi-Factor authentication. [Usługa Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) to łatwy w użyciu, skalowalne i niezawodne rozwiązanie, które oferuje drugiej metody uwierzytelniania, aby chronić użytkowników. Usługa Azure Multi-Factor Authentication wykorzystuje możliwości chmury i integruje się z usługą Active Directory w środowisku lokalnym i aplikacjami niestandardowymi. Ta ochrona jest rozszerzony do dużej liczby kluczowych scenariuszy.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomagają chronić dane klientów:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest sprzętowego modułu zabezpieczeń chronionej 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Usługa Azure Security Center**: Za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

**Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [Koniec na końcu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia także system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania.

**Dzienniki platformy Azure Monitor**: Te dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych, dzięki czemu wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z dzienników usługi Azure Monitor, dzięki czemu klienci mogą korzystać z zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Ocena usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi Azure SQL Database. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w swoich zasobów platformy Azure.

Azure Network Watcher: [Usługa Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) udostępnia narzędzia umożliwiające monitorowanie, diagnozowanie, wyświetlaj metryki oraz włączyć lub wyłączyć dzienników dla zasobów w sieci wirtualnej platformy Azure.  Wspólnota jednostek powinny implementować dzienniki przepływów usługi Network Watcher dla sieciowych grup zabezpieczeń i maszyn wirtualnych. Te dzienniki powinny być przechowywane w konto dedykowanych dla magazynu, które tylko dzienniki zabezpieczeń są przechowywane w i dostęp do konta magazynu powinny być zabezpieczone przy użyciu kontroli dostępu na podstawie ról.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/au-protected-paaswa-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Aplikacja sieci Web PaaS dla Model zagrożeń AU-PROTECTED](images/au-protected-paaswa-threat-model.png?raw=true "aplikacji sieci Web PaaS dla diagramu modelu chronione korzystania z jednostki analizy zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
Dokumentacja ta zgodność jest generowany przez firmę Microsoft na podstawie platform i usług udostępnianych przez firmę Microsoft. Z powodu różnych wdrożeń klienta ta dokumentacja zawiera podejście uogólnionego rozwiązania tylko hostowane w środowisku platformy Azure. Klienci mogą identyfikować i używanie alternatywnych produktów i usług, w oparciu o własne działających w środowiskach i wyników biznesowych. Klienci wybierający opcję użycia zasobów lokalnych muszą spełnić, zabezpieczenia i operacje dla tych zasobów w środowisku lokalnym. Udokumentowane rozwiązanie można dostosować przez klientów w ich wymagania zabezpieczeń i określonej w środowisku lokalnym.

[Zabezpieczeń platformy Azure i zgodności planu — macierz odpowiedzialności klienta AU-PROTECTED](https://aka.ms/au-protected-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez Prot. (Australia) Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - AU-PROTECTED PaaS Web Application implementacji Matrix](https://aka.ms/au-protected-paaswa-cim) zawiera informacje, na którym chronione Australia kontrolki są rozwiązywane przez architektury aplikacji sieci web PaaS, w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Do informacji klasyfikowanych bezpieczny tunel VPN protokołu IPSec należy skonfigurować nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web IaaS. Konfigurując odpowiednio IPSec sieci VPN, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel IPSec sieci VPN z platformą Azure, można tworzyć wirtualne prywatne połączenie między siecią lokalną a siecią wirtualną platformy Azure. To połączenie może odbywać się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. 

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję połączenia prywatnego. Usługa ExpressRoute systemu Azure jest dedykowany łącze między Azure a lokalizacją lokalną lub dostawcy hostingu usług Exchange i jest uznawany za sieci prywatnej. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Aby pomóc w ochronie danych niejawnych przeprowadzanych przy użyciu internetowego lub usługi Azure ExpressRoute, klientów należy skonfigurować ich IPSec sieci VPN, stosując następujące ustawienia:

• Inicjator klienta sieci VPN musi być skonfigurowany na okres istnienia skojarzeń zabezpieczeń, nie większym niż 14400 sekund.
• Inicjator klienta sieci VPN, należy wyłączyć tryb agresywne IKEv1.
• Inicjator klienta sieci VPN należy skonfigurować Perfect Forward Secrecy.
• Klienta sieci VPN inicjatora, należy skonfigurować używanie HMAC SHA256 lub nowszej.

Opcje konfiguracji dla urządzenia sieci VPN i protokołu IPSec / parametry protokołu IKE [dostępne](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) do przeglądu.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejący serwer Active Directory systemu Windows można zintegrować z usługą Azure Active Directory w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Ponadto [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) pozwala klientom w celu skonfigurowania federacji z lokalną [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) i Azure Active Directory. Logowania federacyjnego klienci mogą włączać użytkownikom na logowanie się do usługi Azure Active Directory za pomocą swoich haseł lokalnych, jak i bez konieczności ponownego wprowadzania haseł znajduje się w sieci firmowej. Przy użyciu opcji federacji z usługami federacyjnymi Active Directory, można wdrożyć nowej instalacji programu Active Directory Federation Services, lub można określić istniejącą instalację w farmie programu Windows Server 2012 R2.

Aby zapobiec sklasyfikowanych danych z synchronizacją z usługą Azure Active Directory, klientów można ograniczyć atrybuty, które są replikowane do usługi Azure Active Directory, stosując następujące ustawienia w usłudze Azure Active Directory Connect:

- [Włącz filtrowanie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Wyłącz synchronizację skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Wyłącz funkcję zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Wyłącz zapisywanie zwrotne urządzeń](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Pozostaw wartości domyślne dla [Zapobieganie przypadkowemu usuwaniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) i [automatyczne uaktualnianie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
