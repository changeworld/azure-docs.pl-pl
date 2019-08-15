---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla PCI DSS
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla PCI DSS
services: security
author: meladie
ms.assetid: 5ef64374-7b4e-4176-afe1-0724072f653c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 9b588a536b89c6c3780c36e8b18da375fda811f7
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946604"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-pci-dss"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Aplikacja sieci Web PaaS dla PCI DSS

## <a name="overview"></a>Omówienie

Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja zawiera wskazówki dotyczące wdrożenia w środowisku usługi zgodnych ze standardami płatniczymi branżowymi (PCI DSS 3,2), które są odpowiednie dla kolekcji, magazynu i pobierania danych posiadacza kart. To rozwiązanie automatyzuje wdrażanie i Konfigurowanie zasobów platformy Azure na potrzeby wspólnej architektury referencyjnej, pokazując sposoby, w których klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności, a także jak podstawą dla klientów do kompilowania i Skonfiguruj własne rozwiązania na platformie Azure. Rozwiązanie implementuje podzestaw wymagań z PCI DSS 3,2. Więcej informacji o wymaganiach dotyczących PCI DSS 3,2 i tym rozwiązaniu można znaleźć w sekcji [Dokumentacja dotycząca zgodności](#compliance-documentation) .

Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja automatycznie wdraża architekturę referencyjną aplikacji sieci Web PaaS ze wstępnie skonfigurowanymi kontrolami zabezpieczeń, aby pomóc klientom w osiągnięciu zgodności z wymaganiami PCI DSS 3,2. Rozwiązanie składa się z szablonów Azure Resource Manager i skryptów programu PowerShell, które ułatwiają wdrażanie i Konfigurowanie zasobów.

Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym. Wdrożenie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania PCI DSS 3,2. Pamiętaj o następujących kwestiach:
- Ta architektura udostępnia linię bazową, która ułatwia klientom korzystanie z platformy Azure w PCI DSS 3,2.
- Klienci są odpowiedzialni za przeprowadzenie odpowiedniej oceny zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

Osiągnięcie PCI DSS-zgodność wymaga, aby akredytowany wykwalifikowany podmiot zabezpieczeń (QSA) zaświadczy produkcyjne rozwiązanie dla klientów. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

Kliknij [tutaj](https://aka.ms/pcidss-paaswa-repo) , aby uzyskać instrukcje dotyczące wdrażania.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja służy do wdrażania architektury referencyjnej dla aplikacji sieci Web PaaS z zapleczem Azure SQL Database. Aplikacja sieci Web jest hostowana w izolowanym Azure App Service Environment, czyli prywatnym, dedykowanym środowisku w centrum danych platformy Azure. Obciążenie środowiska równoważenia obciążenia dla aplikacji sieci Web między maszynami wirtualnymi zarządzanymi przez platformę Azure. Ta architektura obejmuje również sieciowe grupy zabezpieczeń, Application Gateway, Azure DNS i Load Balancer.

Aby uzyskać ulepszoną analizę i raportowanie, bazy danych SQL platformy Azure można skonfigurować przy użyciu indeksów magazynu kolumn. Bazy danych Azure SQL Database można skalować w górę lub w dół lub całkowicie wyłączać w odpowiedzi na użycie klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest to, że platforma Azure zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Na platformie Azure są przechowywane trzy kopie danych w wybranym centrach w celu zapewnienia odporności. Geograficznie nadmiarowy magazyn zapewnia, że dane zostaną zreplikowane do dodatkowego centrum danych w setkach kilometrów i zapisane ponownie jako trzy kopie w tym centrum. uniemożliwi to niekorzystne zdarzenie w podstawowym centrum dane klienta, co spowoduje utratę Data.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Azure Active Directory kontroli dostępu opartej na rolach służy do kontrolowania dostępu do wdrożonych zasobów, w tym ich kluczy w Azure Key Vault. Kondycja systemu jest monitorowana za Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym.

Azure SQL Database jest często zarządzany za pośrednictwem SQL Server Management Studio, który działa z komputera lokalnego skonfigurowanego do uzyskiwania dostępu do Azure SQL Database za pośrednictwem bezpiecznego połączenia VPN lub ExpressRoute.

Ponadto Application Insights zapewnia zarządzanie wydajnością aplikacji w czasie rzeczywistym i analizę za ich poorednictwem Azure Monitor dzienników. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![PaaS aplikacji sieci Web dla PCI DSS diagram architektury referencyjnej](images/pcidss-paaswa-architecture.png "PaaS aplikacji sieci Web dla PCI DSS diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- App Service Environment v2
- Application Gateway
  - (1) Zapora aplikacji sieci Web
    - Tryb zapory: zapobieganie
    - Zestaw reguł: OWASP 3.0
    - Port odbiornika: 443
- Application Insights
- Usługa Azure Active Directory
- Azure Automation
- Usługa DNS platformy Azure
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 sieci
    - (4)/24 sieci
    - (4) sieciowe grupy zabezpieczeń
- Aplikacja sieci Web platformy Azure

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia klientom współpracę z zasobami w rozwiązaniu jako Grupa. Klienci mogą wdrażać, aktualizować i usuwać wszystkie zasoby rozwiązania w ramach jednej, skoordynowanej operacji. Klienci używają szablonu do wdrożenia i ten szablon może współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Menedżer zasobów zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające klientom zarządzanie zasobami po wdrożeniu.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane
-   Włączono funkcję [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) , dzięki czemu poświadczenia i inne wpisy tajne są uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego

**App Service Environment v2**: Azure App Service Environment to funkcja App Service, która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji App Service na dużą skalę. Ta funkcja izolacji jest wymagana do spełnienia wymagań dotyczących zgodności ze standardem PCI.

Środowiska App Service są izolowane wyłącznie do uruchamiania aplikacji jednego klienta i są zawsze wdrażane w sieci wirtualnej. Ta funkcja izolacji umożliwia korzystanie z całej izolacji dzierżawy przez architekturę referencyjną, usuwając ją ze środowiska wielodostępnego na platformie Azure, uniemożliwiając tym aplikacjom wiele dzierżawców Wyliczenie wdrożonych App Service Environment zasobów. Klienci mają szczegółową kontrolę nad ruchem przychodzącym i wychodzącym ruchu sieciowego aplikacji, a aplikacje mogą nawiązywać bezpieczne połączenia za pośrednictwem sieci wirtualnych z lokalnymi zasobami firmowymi. Klienci mogą "automatycznie skalować" z App Service Environment na podstawie metryk obciążenia, dostępnego budżetu lub zdefiniowanego harmonogramu.

Użyj środowisk App Service dla następujących kontrolek/konfiguracji:

- Host w ramach zabezpieczonej Virtual Network platformy Azure i reguł zabezpieczeń sieci
- Certyfikat ILB z podpisem własnym na potrzeby komunikacji przy użyciu protokołu HTTPS
- [Tryb wewnętrznego równoważenia obciążenia](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Wyłącz [protokół TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Zmień [szyfr TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Kontrola [portów ruchu przychodzącego (N/s](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md) )
- [Zapora aplikacji sieci Web — ograniczanie danych](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Zezwalaj na [ruch Azure SQL Database](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Aplikacja internetowa platformy Azure**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie aplikacji sieci Web w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Sieć wirtualna

Architektura definiuje prywatny Virtual Network z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy Access Control (ACL), które zezwalają na ruch lub go odmawiają w Virtual Network. Sieciowe grupy zabezpieczeń mogą służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:

- 1 sieciowa Grupa zabezpieczeń dla Application Gateway
- 1 sieciowa Grupa zabezpieczeń dla App Service Environment
- 1 sieciowa Grupa zabezpieczeń dla Azure SQL Database
- 1 sieciowa Grupa zabezpieczeń dla hosta bastionu

Każda z sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdej sieciowej grupy zabezpieczeń są włączone następujące konfiguracje:

- [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
- Dzienniki Azure Monitor są połączone z [diagnostyką sieciowej grupy&#39;zabezpieczeń s](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z odpowiednią sieciową grupą zabezpieczeń.

**Azure DNS**: System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozwiązanie) nazwy witryny sieci Web lub usługi na adres IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure umożliwia użytkownikom Zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. Azure DNS obsługuje również prywatne domeny DNS.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umożliwia klientom skalowanie aplikacji i tworzenie wysokiej dostępności dla usług. Load Balancer obsługuje przychodzące oraz scenariusze wychodzące oraz zapewnia małe opóźnienia i wysoką przepływność oraz skaluje się do milionów przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Dane przesyłane

Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Wszystkie transakcje do usługi Azure Storage za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Pomaga to w ochronie i ochronie danych posiadaczy kart w celu wspierania zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:

- [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
- Azure SQL Database jest skonfigurowany do korzystania z funkcji [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), która wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w czasie rzeczywistym. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do użytkowników nieuprzywilejowanych lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to identyfikować i ograniczać dostęp do danych w taki sposób, aby nie zamykał bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Poniższe technologie zapewniają możliwości zarządzania dostępem do danych posiadaczy kart w środowisku platformy Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa&#39;zarządzania katalogiem i tożsamościami opartymi na chmurze z wieloma dzierżawcami firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do Azure SQL Database.
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych posiadaczy tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) umożliwia klientom zminimalizowanie liczby użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane posiadaczy kart. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach,&#39;które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji&#39;i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo

**Zarządzanie**wpisami tajnymi: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić i uzyskiwać dostęp do tych danych:

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza to chroniony przez moduł HSM 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Azure Security Center**: Dzięki [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

**Application Gateway platformy Azure**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway platformy Azure z skonfigurowaną zaporą aplikacji sieci Web, a zestaw reguł OWASP jest włączony. Dodatkowe możliwości obejmują:

- [Kompleksowa — SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są zorganizowane w oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Azure SQL Database. Rozwiązanie [Change Tracking](../../automation/change-tracking.md) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji dla deweloperów sieci Web na wielu platformach. Application Insights wykrywa anomalie wydajności i klienci mogą używać jej do monitorowania działającej aplikacji sieci Web. Zawiera ona zaawansowane narzędzia analityczne, które ułatwiają klientom diagnozowanie problemów i zrozumienie, jakie użytkownicy faktycznie robią z ich aplikacjami. Jest&#39;ona przeznaczona do ułatwienia klientom ciągłego ulepszania wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/pcidss-paaswa-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![Aplikacja sieci Web PaaS dla modelu zagrożeń PCI DSS](images/pcidss-paaswa-threat-model.png "Aplikacja sieci Web PaaS dla modelu zagrożeń PCI DSS")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności

[Strategia zabezpieczeń i zgodności z przepisami platformy Azure — PCI DSS macierzy odpowiedzialności klienta](https://aka.ms/pcidss-crm) zawiera listę obowiązków dotyczących kontrolerów i procesorów dla wszystkich PCI DSS 3,2 wymagań.

[Macierz implementacji aplikacji sieci web strategia zabezpieczeń i zgodności z przepisami platformy Azure — PCI DSS PaaS](https://aka.ms/pcidss-paaswa-cim) zawiera informacje o tym, które wymagania PCI DSS 3,2 są rozwiązywane przez architekturę aplikacji sieci Web PaaS, w tym szczegółowe opisy tego, jak Implementacja spełnia wymagania poszczególnych artykułów objętych usługą.

## <a name="deploy-this-solution"></a>Wdróż to rozwiązanie
Ta Strategia zabezpieczeń i zgodności z przepisami platformy Azure Automatyzacja składa się z plików konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API Azure Resource Manager do wdrażania zasobów na platformie Azure. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://aka.ms/pcidss-paaswa-repo).

#### <a name="quickstart"></a>Szybki start
1. Sklonuj lub Pobierz [to](https://aka.ms/pcidss-paaswa-repo) repozytorium GitHub do lokalnej stacji roboczej.

2. Przejrzyj 0-Setup-AdministrativeAccountAndPermission.md i uruchom podane polecenia.

3. Wdróż rozwiązanie testowe za pomocą przykładowych danych firmy Contoso lub pilotażowe środowisko produkcyjne.
   - 1A-ContosoWebStoreDemoAzureResources.ps1
     - Ten skrypt wdraża zasoby platformy Azure w celu pokazania sklepu webstore przy użyciu przykładowych danych firmy Contoso.
   - 1-DeployAndConfigureAzureResources.ps1
     - Ten skrypt wdraża zasoby platformy Azure potrzebne do obsługi środowiska produkcyjnego dla aplikacji sieci Web należącej do klienta. To środowisko powinno być bardziej dostosowane przez klienta zgodnie z wymaganiami organizacji.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne &quot;przekazywanie&quot; informacji w ramach zaszyfrowanego połączenia między&#39;siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta&#39;, dane nie są przesyłane przez Internet i w związku z tym nie są dostępne.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zastrzeżenie

- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostępny &quot;w takiej postaci, w jakiej jest.&quot; Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty licencji lub subskrypcji klienta&#39;platformy Azure.
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
