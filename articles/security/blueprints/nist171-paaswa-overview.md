---
title: Aplikacja sieci Web Strategia zabezpieczeń i zgodności z przepisami platformy Azure PaaS dla programu NIST SP 800-171
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — PaaS Web Application NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 22b18988406beb5bed491d7101f376340d35f623
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259785"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure — aplikacja sieci Web PaaS dla publikacji specjalnej 800-171

## <a name="overview"></a>Przegląd
[Specjalna publikacja NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony kontrolowanych niesklasyfikowanych informacji (CUI), które znajdują się w niefederalnych systemach i organizacjach informacyjnych. NIST SP 800-171 ustanawia 14 rodzin wymagań dotyczących zabezpieczeń, aby chronić poufność CUI.

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki ułatwiające klientom wdrażanie aplikacji sieci Web platformy jako usługi (PaaS) na platformie Azure, która implementuje podzestaw formantów NIST SP 800-171. W tym rozwiązaniu pokazano, w jaki sposób klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności. Służy również jako podstawa dla klientów do kompilowania i konfigurowania własnej aplikacji sieci Web na platformie Azure.

Ta architektura referencyjna, skojarzony Przewodnik implementacji oraz model zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań. Nie powinny być używane w środowisku produkcyjnym. Wdrożenie tej architektury bez modyfikacji jest niewystarczające, aby całkowicie spełnić wymagania architektury NIST SP 800-171. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury. Wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zapewnia architekturę referencyjną dla aplikacji sieci Web PaaS z zapleczem Azure SQL Database. Aplikacja sieci Web jest hostowana w izolowanym środowisku App Service, czyli prywatnym, dedykowanym środowisku w centrum danych platformy Azure. Obciążenie środowiska równoważenia obciążenia dla aplikacji sieci Web między maszynami wirtualnymi zarządzanymi przez platformę Azure. Ta architektura obejmuje także sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń), bramę aplikacji platformy Azure, Azure DNS i Azure Load Balancer.

Aby uzyskać ulepszoną analizę i raportowanie, bazy danych SQL platformy Azure można skonfigurować przy użyciu indeksów magazynu kolumn. Bazy danych Azure SQL Database można skalować w górę lub w dół lub całkowicie wyłączać w odpowiedzi na użycie klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest to, że platforma Azure zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Platforma Azure przechowuje trzy kopie danych w wybranym centrum danych klienta pod kątem odporności. Magazyn Geograficznie nadmiarowy gwarantuje, że dane są replikowane do pomocniczego centrum danych o setki kilometrów i zapisane ponownie jako trzy kopie w tym centrum danych. To rozwiązanie zapobiega niekorzystnemu zdarzeniu w podstawowym centrum danych klienta, co spowodowało utratę danych.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Usługa Azure Active Directory (Azure AD) Kontrola dostępu oparta na rolach (RBAC) służy do kontrolowania dostępu do wdrożonych zasobów. Te zasoby obejmują klucze klienta w Azure Key Vault. Kondycja systemu jest monitorowana za Azure Monitor. Klienci konfigurują tę usługę monitorowania do przechwytywania dzienników. Kondycja systemu jest wyświetlana na jednym pulpicie nawigacyjnym łatwym w użyciu.

SQL Database jest często zarządzany przez SQL Server Management Studio. Jest ona uruchamiana z komputera lokalnego, który jest skonfigurowany do uzyskiwania dostępu do bazy danych SQL za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi Azure ExpressRoute.

Application Insights zapewnia zarządzanie wydajnością aplikacji i analizy w czasie rzeczywistym za pośrednictwem Azure Monitor dzienników *firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub ExpressRoute na potrzeby zarządzania i importowania danych do architektury referencyjnej podsieć.*

![PaaS Web Application for NIST SP 800-171 diagram architektury referencyjnej](images/nist171-paaswa-architecture.png "PaaS Web Application for NIST SP 800-171 diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Aby uzyskać więcej informacji, zobacz sekcję [architektury wdrożenia](#deployment-architecture) .

- Usługa Azure Virtual Machines
    - (1) Zarządzanie/bastionu (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 sieci
    - (4)/24 sieci
    - (4) sieciowe grupy zabezpieczeń
- Azure Application Gateway
    - Zapora aplikacji internetowej
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP
        - Port odbiornika: 443
- Application Insights
- Usługa Azure Active Directory
- App Service Environment v2
- Azure Automation
- System DNS platformy Azure
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor (dzienniki)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Aplikacje internetowe paltformy Azure

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Azure Resource Manager**: [Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mogą być używane przez klientów do pracy z zasobami w rozwiązaniu jako Grupa. Klienci mogą wdrażać, aktualizować i usuwać wszystkie zasoby rozwiązania w ramach jednej, skoordynowanej operacji. Klienci używają szablonu do wdrożenia. Szablon może współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Menedżer zasobów zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające klientom zarządzanie zasobami po wdrożeniu.

**Host bastionu**: Host bastionu to pojedynczy punkt wprowadzania, za pomocą którego użytkownicy mogą uzyskać dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie ze wdrożonymi zasobami, zezwalając na bezpieczną listę tylko ruchu zdalnego z publicznych adresów IP. Aby zezwolić na ruch pulpitu zdalnego, źródło ruchu musi być zdefiniowane w sieciowej grupy zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Key Vault.
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyn wirtualnych, gdy nie są używane.
-   [Funkcja Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) jest włączona, aby poświadczenia i inne wpisy tajne były uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego.

**Web Apps**: [Web Apps](https://docs.microsoft.com/azure/app-service/) jest funkcją Azure App Service. Klienci mogą używać go do kompilowania i hostowania aplikacji sieci Web w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność. Obsługuje ona systemy Windows i Linux oraz włącza automatyczne wdrożenia z usług GitHub, Azure DevOps lub dowolnego repozytorium git.

**App Service Environment**: [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) jest funkcją App Service. Zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji App Service na dużą skalę.

Środowisko App Service jest izolowane do uruchamiania tylko jednej aplikacji. Jest on zawsze wdrażany w sieci wirtualnej. Ze względu na funkcję izolacji architektura referencyjna ma kompletną izolację dzierżawy i jest usuwana ze środowiska wielodostępnego platformy Azure. Klienci mają szczegółową kontrolę nad ruchem sieciowym aplikacji przychodzących i wychodzących. Aplikacje mogą nawiązywać bezpieczne połączenia za pośrednictwem sieci wirtualnych z lokalnymi zasobami firmy. Klienci mogą "automatycznie skalować" z App Service Environment na podstawie metryk obciążenia, dostępnego budżetu lub zdefiniowanego harmonogramu.

Korzystanie z App Service Environment dla tej architektury zapewnia następujące kontrolki i konfiguracje:

- Host w ramach zabezpieczonej sieci wirtualnej platformy Azure i reguł zabezpieczeń sieciowych.
- Certyfikat wewnętrznego modułu równoważenia obciążenia z podpisem własnym na potrzeby komunikacji przy użyciu protokołu HTTPS. Najlepszym rozwiązaniem jest to, że firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.
- [Tryb wewnętrznego równoważenia obciążenia](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (tryb 3).
- Wyłącz [protokół TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Zmień [szyfr TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Steruj [ruchem przychodzącym N/h portów](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).
- [Zapora aplikacji sieci Web — ograniczanie danych](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md).
- Zezwalaj na [ruch Azure SQL Database](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md).

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy kontroli dostępu, które zezwalają na ruch w sieci wirtualnej lub odmawiają go. Sieciowych grup zabezpieczeń może służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Następująca sieciowych grup zabezpieczeń istnieje:
- Jeden sieciowej grupy zabezpieczeń do Application Gateway
- Jeden sieciowej grupy zabezpieczeń do App Service Environment
- Jeden sieciowej grupy zabezpieczeń do SQL Database
- Jeden sieciowej grupy zabezpieczeń dla hosta bastionu

Każdy z sieciowych grup zabezpieczeń ma określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdego sieciowej grupy ZABEZPIECZEŃu są włączone następujące konfiguracje:
  - [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu.
  - Dzienniki Azure Monitor są połączone z [diagnostyką sieciowej grupy zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsieci**: Każda podsieć jest skojarzona z odpowiadającą jej sieciowej grupy zabezpieczeń.

**Azure DNS**: System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozpoznanie) nazwy witryny sieci Web lub usługi na adres IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure umożliwia użytkownikom Zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. Azure DNS obsługuje również prywatne domeny DNS.

**Azure Load Balancer**: [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) mogą być używane przez klientów do skalowania aplikacji i tworzenia wysokiej dostępności dla usług. Load Balancer obsługuje scenariusze ruchu przychodzącego i wychodzącego. Zapewnia małe opóźnienia i wysoką przepływność oraz skaluje się do milionów przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Dane przesyłane
Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Wszystkie transakcje do usługi Azure Storage za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby spełnić wymagania dotyczące szyfrowanych danych przechowywanych w magazynie, wszystkie [magazyny](https://azure.microsoft.com/services/storage/) są używane [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Ta funkcja pomaga chronić i zabezpieczać dane w celu obsługi zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez instytut NIST SP 800-171.

**Azure Disk Encryption**: [Szyfrowanie dysków](../azure-security-disk-encryption-overview.md) używa funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu SQL Database są stosowane następujące miary zabezpieczeń bazy danych:
-   [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
-   SQL Database jest skonfigurowany do używania [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Wykonuje szyfrowanie w czasie rzeczywistym oraz odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w pamięci podręcznej. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania. Zapewnia alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalnych luk w zabezpieczeniach, ataków iniekcji SQL i nietypowych wzorców dostępu do bazy danych.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez maskowanie danych do nieuprzywilejowanych użytkowników lub aplikacji. Może automatycznie odnajdywać potencjalnie wrażliwe dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak aby dane poufne nie wyłączać bazy danych za pośrednictwem nieautoryzowanego dostępu. *Klienci są odpowiedzialni za dostosowanie ustawień, aby były zgodne ze schematem bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości zarządzania dostępem do danych w środowisku platformy Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) to usługa zarządzania katalogiem i tożsamościami opartymi na chmurze firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzeniu w usłudze Azure AD i obejmują użytkowników, którzy uzyskują dostęp do bazy danych SQL.
-   Uwierzytelnianie w aplikacji odbywa się przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz jak [zintegrować aplikacje z usługą Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Szyfrowanie kolumny bazy danych używa również usługi Azure AD do uwierzytelniania aplikacji w SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Administratorzy [systemu Azure](../../role-based-access-control/role-assignments-portal.md) mogą używać administratorów do definiowania szczegółowych uprawnień dostępu. Z działem IT mogą przyznawać tylko dostęp, który użytkownicy potrzebują do wykonywania swoich zadań. Zamiast zapewniać wszystkim użytkownikom nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do zasobów i danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) mogą być używane przez klientów w celu zminimalizowania liczby użytkowników, którzy mają dostęp do określonych informacji. Administratorzy mogą używać Azure AD Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji. Służy do konfigurowania automatycznych odpowiedzi w celu wykrycia podejrzanych działań związanych z tożsamościami organizacji. Bada także podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Key Vault pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Następujące funkcje Key Vault pomagają klientom chronić dane:
- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowego sprzętu zabezpieczenia-chroniony przez moduł 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu RBAC.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Azure Security Center**: Dzięki [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Security Center również uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków, które są ukierunkowane na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Security Center zawiera zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , które są wyzwalane w przypadku wystąpienia zagrożeń lub podejrzanych działań. Klienci mogą używać [niestandardowych reguł alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w celu definiowania nowych alertów zabezpieczeń opartych na danych, które zostały już zebrane z ich środowiska.

Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia. Security Center ułatwia klientom odnajdywanie potencjalnych problemów z zabezpieczeniami i ich rozwiązywanie. Dla każdego wykrytego zagrożenia jest generowany [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Zespoły reagowania na incydenty mogą korzystać z raportów podczas badania i korygowania zagrożeń.

**Application Gateway platformy Azure**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu bramy aplikacji z skonfigurowaną zaporą aplikacji sieci Web i włączonym zestawem reguł OWASP. Dodatkowe możliwości obejmują:

- [Kompleksowy protokół SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md).
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (tryb zapobiegania).
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0.
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md).
- [Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Security Center udostępnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/resource-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Te dzienniki obejmują Dzienniki systemu Windows, dzienniki magazynu, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania (do 730 dni) w celu spełnienia określonych wymagań.

**Azure monitor dzienników**: Dzienniki są konsolidowane w [dziennikach Azure monitor](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu danych są one podzielone na oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics. W ten sposób wszystkie dane można analizować razem niezależnie od oryginalnego źródła. Security Center integruje się z dziennikami Azure Monitor. Klienci mogą używać zapytań Kusto, aby uzyskać dostęp do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Ocena Active Directory](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie do sprawdzania kondycji Active Directory ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Ocena SQL](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie sprawdzania kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Zapewnia ona klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną. Raporty te również przedstawiają liczbę agentów, które nie odpowiadają, oraz liczbę agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Usługi Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowują elementy Runbook, uruchamiają je i zarządzają nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z SQL Database. Klienci mogą korzystać z rozwiązania [Change Tracking](../../automation/change-tracking.md) Automation, aby łatwo identyfikować zmiany w środowisku.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów. Organizacje mogą używać go do inspekcji, tworzenia alertów i archiwizowania danych. Mogą również śledzić wywołania interfejsu API w swoich zasobach platformy Azure.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji dla deweloperów sieci Web na wielu platformach. Application Insights wykrywa anomalie wydajności. Klienci mogą używać go do monitorowania działającej aplikacji sieci Web. Application Insights obejmuje zaawansowane narzędzia analityczne pomagające klientom zdiagnozować problemy i zrozumieć, co użytkownicy robią z aplikacją. Jest&#39;ona przeznaczona do ułatwienia klientom ciągłego ulepszania wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/nist171-paaswa-tm) lub można go znaleźć tutaj. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![PaaS Web Application for NIST SP 800-171 — model zagrożeń](images/nist171-paaswa-threat-model.png "PaaS Web Application for NIST SP 800-171 — model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
W przypadku [macierzy z strategia zabezpieczeń i zgodności z przepisami platformy Azure-NIST SP 800-171 klient](https://aka.ms/nist171-crm) zawiera listę wszystkich kontroli zabezpieczeń wymaganych przez instytut NIST SP 800-171. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji kontroli aplikacji sieci web strategia zabezpieczeń i zgodności z przepisami platformy Azure-NIST sp 800-171 PaaS](https://aka.ms/nist171-paaswa-cim) zawiera informacje na temat tego, które kontrolki NIST SP 800-171 są rozwiązywane przez architekturę aplikacji sieci Web PaaS. Zawiera szczegółowy opis sposobu, w jaki implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną jeszcze bezpieczną opcję połączenia. ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Połączenia ExpressRoute nawiązują połączenie bezpośrednio z dostawcą telekomunikacyjnym klienta. W związku z tym dane nie podróżują przez Internet i nie są w nim ujawniane. Połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
