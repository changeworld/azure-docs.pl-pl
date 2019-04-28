---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla SP NIST 800-171
description: Zabezpieczenia platformy Azure i zgodności planu - SP NIST aplikacji sieci Web PaaS 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127331"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla publikacji specjalne NIST 800-171

## <a name="overview"></a>Omówienie
[Publikacja specjalne NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony informacji unclassified kontrolowanego (CUI), która znajduje się w organizacji i systemów informacyjnych nonfederal. SP NIST 800-171 ustanawia 14 rodzin wymagań dotyczących ochrony poufności CUI zabezpieczeń.

Tych samych zabezpieczeń platformy Azure i zgodności planu znajdują się wskazówki pomagające klientom skrócić czas wdrażania platformy jako usługi (PaaS) aplikacji sieci web na platformie Azure, która implementuje podzbiór NIST SP 800-171 kontrolki. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać zabezpieczeń i zgodności z wymaganiami. Służy również jako podstawa do tworzenia i konfigurowania aplikacji sieci web na platformie Azure.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do swoich wymagań. Nie należy ich używać jako — w środowisku produkcyjnym. Wdrażania tej architektury bez żadnych modyfikacji jest za mała, aby całkowicie wymagań SP NIST 800-171. Klienci są zobowiązani do przeprowadzania odpowiednie ustawienia zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury. Wymagania mogą się różnić w oparciu o szczegóły implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

Tych samych zabezpieczeń platformy Azure i zgodności planu zapewnia architekturę referencyjną dla aplikacji sieci web PaaS z zapleczem usługi Azure SQL Database. Aplikacja sieci web jest hostowana w izolowanym środowisku usługi App Service, który jest prywatnym, dedykowanym środowisku w centrum danych platformy Azure. Środowisko równoważy obciążenie dla aplikacji sieci web na maszynach wirtualnych (VM) zarządzanych przez platformę Azure. Taka architektura obejmuje również sieciowych grup zabezpieczeń (NSG), bramy aplikacji platformy Azure, usługi Azure DNS i usługi Azure Load Balancer.

Ulepszoną analizę i raportowanie baz danych Azure SQL Database można skonfigurować na indeksach magazynu kolumn. Bazy danych SQL platformy Azure można skalować w górę i w dół lub całkowicie wyłączony w odpowiedzi do użycia przez klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Rozwiązanie używa konta usługi Azure Storage, w których klienci mogą skonfigurować na potrzeby zachowania poufności danych magazynowanych szyfrowanie usługi Storage. Azure przechowuje trzy kopie danych w obrębie centrum danych wybranego klienta pod kątem odporności. Magazyn geograficznie nadmiarowy gwarantuje, że dane są replikowane do dodatkowego centrum danych setki z mil natychmiast i ponownie przechowywane jako trzy kopie w tym centrum danych. To rozwiązanie zapobiega zdarzenie niepożądane, u klienta podstawowe centrum danych co spowoduje utratę danych.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach (Azure AD) (RBAC) służy do kontrolowania dostępu do wdrożonych zasobów. Te zasoby obejmują kluczy klienta w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Monitor. Klienci, skonfigurować niej monitorowania w celu przechwytywania dzienników. Kondycja systemu jest wyświetlany na pojedynczy pulpit nawigacyjny, który jest łatwy w użyciu.

Bazy danych SQL zwykle odbywa się za pomocą programu SQL Server Management Studio. Działa z komputera lokalnego, który jest skonfigurowany do dostępu do bazy danych SQL za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi Azure ExpressRoute.

Usługa Application Insights zapewnia zarządzania wydajnością aplikacji w czasie rzeczywistym i analizy przy użyciu dzienników usługi Azure Monitor *firma Microsoft zaleca, aby skonfigurować połączenie sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania do odwołania architektury podsieci.*

![Aplikacja sieci Web PaaS dla SP NIST 800-171 referencyjny diagram architektury](images/nist171-paaswa-architecture.png "aplikacji sieci Web PaaS dla SP NIST 800-171 referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [architektura wdrożenia](#deployment-architecture) sekcji.

- Azure Virtual Machines
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (((1) /16 sieci
    - (4) prefiksie/24 sieci
    - (4) grupy zabezpieczeń sieci
- Azure Application Gateway
    - Zapora aplikacji internetowej
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP
        - Port odbiornika: 443
- Application Insights
- Usługa Azure Active Directory
- Usługa App Service Environment w wersji 2
- Azure Automation
- System DNS platformy Azure
- W usłudze Azure Key Vault
- Azure Load Balancer
- Usługa Azure Monitor (Dzienniki)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa Azure Resource Manager**: [Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) może służyć przez klientów do pracy z zasobami w rozwiązaniu jako grupa. Klientów można wdrożyć, zaktualizować lub usunąć wszystkie zasoby dla rozwiązania w jednej, skoordynowanej operacji. Klienci używają szablonu wdrożenia. Szablon można następnie używać w różnych środowiskach, takich jak testowania, przemieszczania i produkcji. Menedżer zasobów zapewnia zabezpieczeń, inspekcji i znakowania funkcje, aby pomóc klientom w zarządzaniu zasobami po wdrożeniu.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, którego użytkownicy mogą uzyskać dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów, zezwalając tylko na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na ruch pulpitu zdalnego, źródła ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie umożliwia utworzenie maszyny Wirtualnej jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) za pomocą usługi Key Vault.
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny Wirtualnej nie w użyciu.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) jest włączona, tak aby poświadczeń i innych wpisów tajnych uruchomić w środowisku chronionego, która jest odizolowana od systemem operacyjnym.

**Aplikacje sieci Web**: [Aplikacje sieci Web](https://docs.microsoft.com/azure/app-service/) to funkcja usługi Azure App Service. Klienci służy do tworzenia i hostowania wybranych przez nich aplikacji sieci web w języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoka dostępność. Go obsługuje Windows i Linux oraz umożliwia używanie wdrożeń zautomatyzowanych z usługi GitHub, DevOps platformy Azure lub z dowolnym repozytorium Git.

**Środowisko usługi App Service**: [Środowisko App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) to funkcja usługi App Service. Zapewnia w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji usługi App Service w dużej skali.

Środowisko usługi App Service jest izolowane do uruchamiania tylko jednej aplikacji. Jest ona zawsze wdrażane w sieci wirtualnej. Z powodu funkcja izolacji architektury referencyjnej ma izolacji dzierżawcy ukończone, a zostanie on usunięty z platformy Azure w środowisku wielodostępnym. Klienci mają precyzyjną kontrolę nad ruchem sieciowym w obu aplikacji dla ruchu przychodzącego i wychodzącego. Aplikacje mogą nawiązywać bezpieczne szybkie połączenia za pośrednictwem wirtualnych sieci lokalnych zasobów firmowych. Klienci mogą "automatycznego skalowania" za pomocą środowiska App Service Environment na podstawie metryk obciążenia, dostępnego budżetu lub zdefiniowany harmonogram.

App Service Environment w przypadku tej architektury zapewnia następujące kontrolki i konfiguracje:

- Hostowanie w zabezpieczonej sieci wirtualnej platformy Azure i reguł zabezpieczeń sieciowych.
- Certyfikat modułu równoważenia obciążenia z podpisem własnym wewnętrznego dla komunikacji HTTPS. Najlepszym rozwiązaniem firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.
- [Trybu wewnętrznego równoważenia obciążenia](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (tryb 3).
- Wyłącz [protokołu TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Zmiana [szyfrowania TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Kontrolka [ruchu N/M porty wejściowe](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Zapora aplikacji sieci Web — Ogranicz danych](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Zezwalaj na [ruch usługi Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:
- Jednej sieciowej grupy zabezpieczeń w usłudze Application Gateway
- Jednej sieciowej grupy zabezpieczeń w środowisku usługi App Service
- Jednej sieciowej grupy zabezpieczeń usługi SQL Database
- Jednej sieciowej grupy zabezpieczeń dla hostem bastionu

Każdy z sieciowych grup zabezpieczeń ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu.
  - Dzienniki platformy Azure Monitor jest podłączony do [diagnostyki sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsieci**: Każda podsieć jest skojarzone z jego odpowiedniego sieciowej grupy zabezpieczeń.

**Usługa Azure DNS**: System nazw domen (DNS) jest odpowiedzialny za tłumaczenia (lub rozpoznawanie) nazwę witryny sieci Web lub usługi na jej adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to Usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure, użytkownicy mogą zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co inne usługi platformy Azure. Usługa DNS platformy Azure obsługuje również prywatne domen DNS.

**Usługa Azure Load Balancer**: [Moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) może być używany przez klientów do skalowania swoich aplikacji i zapewniać wysoką dostępność usług. Moduł równoważenia obciążenia obsługuje scenariusze ruchu przychodzącego i wychodzącego. Zapewnia małe opóźnienia i wysoką przepływność i skaluje nawet miliony przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występują za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Azure Storage**: Aby spełnić wymagania dla zaszyfrowanych danych w spoczynku, wszystkie [magazynu](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ta funkcja pomaga chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez SP NIST 800-171 wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [Szyfrowanie dysków](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) używa funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Azure SQL Database**: Wystąpienie bazy danych SQL wykorzystuje następujące środki bezpieczeństwa bazy danych:
-   [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Wykonuje się w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, do ochrony informacji w stanie spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywane dane, nie został narażone na nieautoryzowany dostęp.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania. Dostarcza alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i bazy danych nietypowe wzorce dostępu.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskać dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanym użytkownikom lub aplikacji. Można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak, aby dane poufne nie zakończyć bazy danych za pośrednictwem przed nieautoryzowanym dostępem. *Klienci są zobowiązani do dostosowywania ustawień stosować się do ich schemat bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:
-   [Usługa Azure AD](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wielodostępne oparte na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze Azure AD i są użytkownicy, którzy dostęp do bazy danych SQL.
-   Do aplikacji uwierzytelniania za pomocą usługi Azure AD. Aby uzyskać więcej informacji, zobacz instrukcje [integrować aplikacje z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Szyfrowanie kolumny bazy danych również używa usługi Azure AD można uwierzytelnić aplikację do usługi SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) może służyć przez administratorów do definiowania uprawnień dostępu w zakresie. Dzięki niemu one można udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co dostępu użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do zasobów i danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) może służyć przez klientów, aby zminimalizować liczbę użytkowników, którzy mają dostęp do pewnych informacji. Administratorzy mogą używać usługi Azure AD Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Konfiguruje automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości w organizacji. Bada go również podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Key Vault ułatwia ochronę kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje w chmurze i usług. Następujące funkcje usługi Key Vault pomagają chronić dane klientów:
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

**Usługa Azure Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web, skonfigurowane i zestaw reguł OWASP, które są włączone. Dodatkowe funkcje obejmują:

- [Koniec na końcu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania).
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) zestaw reguł, OWASP 3.0.
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Niestandardowych sond kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Usługa Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Security Center zapewnia także system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki magazynu, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania, nawet do 730 dni, aby spełnić konkretne wymagania.

**Dzienniki platformy Azure Monitor**: Dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu danych, wartość jest zorganizowana w oddzielnych tabelach dla każdego typu danych w obszarach roboczych usługi Log Analytics. W ten sposób wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Usługa Security Center integruje się z dziennikami usługi Azure Monitor. Klienci mogą używać zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Daje klientom z priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne. Zgłasza również odpowiadają liczby agentów i liczbę agentów, którzy przesłali danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Automatyzacja](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi SQL Database. Klienci mogą korzystać z automatyzacji [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązania, aby łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów. Organizacje mogą używać go do inspekcji, tworzyć alerty i archiwizować dane. Mogą również śledzić wywołań interfejsu API w swoich zasobów platformy Azure.

**Usługa Application Insights**: [Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to usługa zarządzania wydajnością aplikacji rozszerzalnej dla deweloperów sieci web na wielu platformach. Usługa Application Insights wykrywa anomalie wydajność. Klienci mogą go używać do monitorowania działającej aplikacji sieci web. Usługi Application Insights zawiera narzędzia zaawansowane funkcje analityczne, aby ułatwić klientom diagnozować problemy i zrozumieć, jak użytkownicy korzystają z aplikacji. Jego&#39;s zaprojektowane, aby pomóc klientom w ciągłym udoskonalaniu wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/nist171-paaswa-tm) lub można znaleźć tutaj. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu wprowadzasz zmiany.

![Aplikacja sieci Web PaaS dla model zagrożeń SP NIST 800-171](images/nist171-paaswa-threat-model.png "aplikacji sieci Web PaaS dla SP NIST 800-171 model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
[Zabezpieczeń platformy Azure i zgodności planu - SP NIST 800-171 klienta odpowiedzialność macierzy](https://aka.ms/nist171-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez SP NIST 800-171. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - macierzy implementacji kontroli aplikacji sieci Web PaaS SP NIST 800-171](https://aka.ms/nist171-paaswa-cim) zawiera informacje, na które SP NIST 800-171 kontrolki są rozwiązywane przez Architektura aplikacji sieci web PaaS. Zawiera szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpieczne nawiązywanie połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web PaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można tworzyć wirtualne prywatne połączenie między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu sieci VPN jest przesyłane przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje inną opcję połączenia jeszcze bardziej bezpieczne. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Połączenia ExpressRoute łączyć się bezpośrednio z dostawcą usług telekomunikacyjnych klienta. W rezultacie dane nie podróżują, przez Internet i nie jest widoczne. Połączenia te oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

 - Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
 - W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
 - Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
 - Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
 - Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
 - Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
