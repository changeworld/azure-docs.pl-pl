---
title: Zabezpieczeń platformy Azure i plan zgodności - FFIEC branży usług finansowych podlegającymi ochronie obciążeń
description: Zabezpieczeń platformy Azure i plan zgodności - FFIEC branży usług finansowych podlegającymi ochronie obciążeń
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: frasim
ms.openlocfilehash: 497c5a987753cbbe577c1d042d6bf61be9d905ab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Zabezpieczeń platformy Azure i plan zgodności - FFIEC branży usług finansowych podlegającymi ochronie obciążeń

## <a name="overview"></a>Przegląd

Azure zabezpieczeń i zgodności plan - obciążeń Regulated usług finansowych FFIEC pomaga wdrożyć jako usługa (PaaS) aplikacji sieci web przeznaczone do obsługi danych poufnych w chmurze platformy bezpieczne i zgodne. Plan obejmuje zautomatyzowanych skryptów i wskazówki, które pokazują architektura proste odwołanie i projekt, który ułatwia przyjęcie rozwiązania Microsoft Azure. Ten plan przedstawiono rozwiązanie na trasie do potrzeb organizacji znalezienia sposobów, aby zmniejszyć obciążenie i kosztu wdrożenia w chmurze.

Ten plan zaprojektowano w celu spełnienia wymagań rygorystyczne normy zgodne ustawiony przez Amerykański Instytut z księgowym publicznym takich jak - SOC 1, SOC 2 Radę Payment Card Industry Data Security Standards DSS 3.2 i FFIEC dla Kolekcja, magazynowania i pobierania poufne dane finansowe. Poprzez wdrożenie rozwiązania, zarządzanego dostępu do danych finansowych w środowisku bezpieczne, zgodne i wielowarstwowych go pokazuje prawidłowego obsługi tych danych. Rozwiązanie jest wdrożone jako end-to-end rozwiązania PaaS bazujących na platformie Azure. 

Plan ma służyć jako podstawa do tworzenia i zrozumienie wymagań związanych z zarządzaniem zabezpieczonych danych w chmurze. Rozwiązanie nie stosuje się w środowisku produkcyjnym jako — jest, ale aby zrozumieć, projektowanie i wdrażanie usług platformy Azure; zaprojektowano go jako linii bazowej ułatwiających klientom usługi użyj Microsoft Azure w sposób bezpieczny i być zgodne.

Uznany audytor musi zawierać żadne rozwiązanie do klienta produkcyjnego opartego na ten plan. Rozwiązania może różnić w zależności od szczegóły implementacji i geograficzne każdego klienta.

Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, obejrzyj ten [wideo](https://aka.ms/fsiblueprintvideo) , który objaśnia i przedstawia jego wdrażania.

## <a name="solution-components"></a>Składniki rozwiązania

Architektura składa się z następujących składników i korzysta z funkcji wdrażania z rozwiązania Azure PCI DSS zgodności.

- **Diagram architektury**. Diagram przedstawia architekturę Odwołanie używane dla rozwiązania magazynu sieci Web firmy Contoso.
- **Szablony wdrażania**. W tym wdrożeniu [szablonów usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) są używane do automatycznego wdrożenia składników architektury w Microsoft Azure, określając parametry konfiguracji podczas instalacji.
- **Skrypty wdrażania automatycznego**. Skrypty te pomocy wdrożyć rozwiązanie end-to-end. Skrypty obejmują:
    - Instalacja modułu i [administratora globalnego](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) skrypt instalacyjny jest używane do instalowania i sprawdź, czy wymagane moduły programu PowerShell i ról administratora globalnego są poprawnie skonfigurowane. 
    - Instalacja skryptu PowerShell służy do wdrażania rozwiązania end-to-end, podanego przez plik .zip oraz pliku bacpac, który zawiera pokaz wstępnie zbudowanych aplikacji sieci web z [przykładowej bazy danych SQL](https://github.com/Microsoft/azure-sql-security-sample). zawartość. Kod źródłowy dla tego rozwiązania jest gotowa do przeglądu [repozytorium kodu plan przetwarzania płatności][code-repo]. 

## <a name="architectural-diagram"></a>Diagram architektury

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Scenariusz użytkownika

Następujące adresy planu przypadek użycia poniżej.

> W tym scenariuszu pokazano, jak fikcyjne webstore przenieść się, że poufne dane do PaaS w chmurze rozwiązania opartego na platformie Azure. Rozwiązanie przykład ilustruje zbieranie informacji użytkownika podstawowego i wybrane dane poufne i obsługi. Tę pracę obiektowy zabezpieczeń platformy Azure i plan zgodności - środowisk standardem PCI DSS przetwarzania płatności. Uzyskać więcej informacji na temat rozszerzania na tę pracę ["Przejrzyj i wskazówki dla wdrożenia"](https://aka.ms/pciblueprintprocessingoverview) dokument zawiera przegląd standardem PCI DSS środowisk.

### <a name="use-case"></a>Przypadek użycia
Mała magazynu sieci Web o nazwie *magazynu sieci Web firmy Contoso* jest gotowa do przeniesienia danych finansowych, który zawiera informacje dotyczące płatności klientów do chmury. 

Administrator magazynu sieci Web firmy Contoso jest szuka rozwiązania, które można szybko wdrożyć jego celach. ADAM użyje tego elementu koncepcji (POC) do omówienia z jego uczestników, jak Azure może służyć do zbierania, przechowywania i pobierania danych finansowych, zgodnie z wymaganiami zgodności rygorystyczne.

> Będzie odpowiedzialne za przeprowadzanie odpowiednich ustawień zabezpieczeń i zgodności przeglądami od wszelkich rozwiązań do skompilowanej za pomocą architektury używanych przez ten fazy weryfikacji Koncepcji, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania i geograficzne. 

### <a name="elements-of-the-foundational-architecture"></a>Elementy architektury podstawowych

Architektura podstawowych jest wyposażone w fikcyjne następujące elementy:

Domeny lokacji `contosowebstore.com`

Role użytkowników są wykorzystywane do przedstawienia przypadek użycia i zapewniają wgląd w interfejsie użytkownika.

#### <a name="role-site-and-subscription-admin"></a>Rola: Administrator lokacji i subskrypcji

|Element      |Przykład|
|----------|------|
|Nazwa użytkownika: |`adminXX@contosowebstore.com`|
| Nazwa: |`Global Admin Azure PCI Samples`|
|Typ użytkownika:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- Konto administratora nie można odczytać informacji finansowych pozbawione maskowania. Wszystkie akcje są rejestrowane.
- Konto administratora nie może zarządzać lub zaloguj się do bazy danych SQL.
- Konto administratora mogą zarządzać usługi Active Directory i subskrypcje.

#### <a name="role-sql-administrator"></a>Rola: Administrator SQL

|Element      |Przykład|
|----------|------|
|Nazwa użytkownika: |`sqlAdmin@contosowebstore.com`|
| Nazwa: |`SQLADAdministrator PCI Samples`|
| Imię: |`SQL AD Administrator`|
|Nazwisko: |`PCI Samples`|
|Typ użytkownika:| `Administrator`|

- Konto sqladmin nie można wyświetlić niefiltrowane informacji finansowych. Wszystkie akcje są rejestrowane.
- Konta sqladmin można zarządzać bazą danych SQL.

#### <a name="role-clerk"></a>Rola: pracownik.

|Element      |Przykład|
|----------|------|
|Nazwa użytkownika:| `receptionist_EdnaB@contosowebstore.com`|
| Nazwa: |`Edna Benson`|
| Imię:| `Edna`|
|Nazwisko:| `Benson`|
| Typ użytkownika: |`Member`|

Edna Benson jest Menedżer recepcjonista i biznesowych. Użytkownik jest odpowiedzialny za zapewnienie, że informacje o kliencie są dokładne i rozliczeń zostało zakończone. Edna jest użytkownik zalogowany do interakcji z pokaz magazynu sieci Web firmy Contoso witryny sieci Web. Edna ma następujące uprawnienia: 

- Edna można tworzyć i odczytu informacji o kliencie.
- Edna można zmodyfikować informacje o kliencie.
- Edna można zastąpić informacji finansowych.
- Edna konta nie można wyświetlić niefiltrowane informacji finansowych.



### <a name="contoso-webstore---estimated-pricing"></a>contoso Webstore - szacowane ceny

Tej architektury podstawowych i przykładowej aplikacji sieci web mają strukturę opłaty miesięczne i koszt użycia na godzinę, które należy rozważyć w przypadku sortowania rozwiązania. Koszty te można oszacować, za pomocą [Azure Kalkulator wyceny](https://azure.microsoft.com/pricing/calculator/). Począwszy od września 2017 r. szacowany koszt miesięczne dla tego rozwiązania jest ~ $2500 obejmuje 1000 USD/miesiąc opłat użycia dla ASE v2. Te koszty zależą wielkość użycia i mogą ulec zmianie. Spoczywa na klienta, aby obliczyć ich szacowane koszty miesięczne w czasie wdrażania dokładniejsze oszacowania. 

To rozwiązanie użyć następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrażania znajdują się w [wdrożenia Architecture](#deployment-architecture) sekcji.

>- Application Gateway
>- Usługa Azure Active Directory
>- V2 środowiska usługi aplikacji
>- Log Analytics
>- W usłudze Azure Key Vault
>- Grupy zabezpieczeń sieci
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Aplikacja sieci Web platformy Azure
>- Azure Automation
>- Element Runbook usługi Automatyzacja Azure
>- System DNS platformy Azure
>- Azure Virtual Network
>- Maszyna wirtualna systemu Azure
>- Grupy zasobów platformy Azure i zasady
>- Azure Blob Storage
>- Usługi Azure Active Directory kontroli dostępu opartej na rolach (RBAC)

## <a name="deployment-architecture"></a>Architektura wdrażania

Poniższa sekcja zawiera szczegóły dotyczące projektowania i wdrażania elementów.

### <a name="network-segmentation-and-security"></a>W przypadku segmentacji sieci i zabezpieczeń

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

Architektura podstawowych zmniejsza ryzyko luk w zabezpieczeniach z zapory aplikacji sieci web (WAF) i zestaw reguł OWASP włączona przy użyciu bramy aplikacji. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Odciążanie protokołu SSL](/azure/application-gateway/application-gateway-ssl-portal) włączone
- [Protokołu TLS 1.0 i 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) wyłączone
- [Zapora aplikacji sieci Web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](/azure/application-gateway/application-gateway-web-application-firewall-portal) z ruleset OWASP 3.0
- [Rejestrowanie danych diagnostycznych](/azure/application-gateway/application-gateway-diagnostics) włączone
- [Sondy kondycji niestandardowych](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center) i [Azure Advisor](/azure/advisor/advisor-security-recommendations), które zapewniają dodatkową ochronę i powiadomień. Centrum zabezpieczeń Azure zapewnia również system reputacji.

#### <a name="virtual-network"></a>Sieć wirtualna

Architektura podstawowych definiuje prywatnej sieci wirtualnej z 10.0.0.0/16 przestrzeni adresowej.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń (NSG):

- Grupa zabezpieczeń sieci DMZ dla zapory i zapory aplikacji sieci Web dla bramy aplikacji
- Grupa NSG do zarządzania jumpbox (bastionu hosta)
- Grupa NSG dla środowiska usługi aplikacji

Każdy z grup NSG mieć określone porty i protokoły otwarty do bezpiecznego i prawidłowe funkcjonowanie rozwiązania. 

Ponadto następujące konfiguracje są włączone dla każdej grupy NSG:

- Włączone [dzienników diagnostycznych oraz zdarzenia](/azure/virtual-network/virtual-network-nsg-manage-log) są przechowywane na koncie magazynu 
- Połączenia analizy dzienników do [NSG dla diagnostyki](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
#### <a name="subnets"></a>Podsieci
 Upewnij się, że w każdej podsieci jest skojarzony z jego odpowiedniej grupy NSG.

#### <a name="custom-domain-ssl-certificates"></a>Certyfikaty SSL domeny niestandardowej
 Ruch HTTPS jest włączone, za pomocą certyfikatu SSL domeny niestandardowej.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przy użyciu szyfrowania bazy danych inspekcji i stosowania innych środków.

#### <a name="azure-storage"></a>Azure Storage

Aby spełnić wymagania zaszyfrowanych danych na rest, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Magazyn](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Azure SQL Database

Wystąpienie bazy danych SQL Azure używa następujących środków zabezpieczeń bazy danych:

- [AD uwierzytelniania i autoryzacji](/azure/sql-database/sql-database-aad-authentication)
- [Inspekcja bazy danych SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Przezroczystego szyfrowania danych](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Reguły zapory](/azure/sql-database/sql-database-firewall-configure), dzięki czemu ASE pule procesów roboczych i zarządzanie IP klienta
- [Wykrywanie zagrożeń SQL](/azure/sql-database/sql-database-threat-detection-get-started)
- [Zawsze zaszyfrowanych kolumn](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [Baza danych SQL dane dynamiczne maskowanie](/azure/sql-database/sql-database-dynamic-data-masking-get-started), za pomocą skryptu PowerShell po wdrożeniu

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

[Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics) można udostępnić magazynu sieci Web firmy Contoso szczegółowe rejestrowanie całą aktywność systemu i użytkownika, obejmują rejestrowania danych finansowych. Zmiany można przejrzeć i sprawdzić dokładność. 

- **Dzienniki aktywności.**  [Dzienniki aktywności](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji.
- **Dzienniki diagnostyczne.**  [Dzienniki diagnostyczne](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki magazynu obiektów Blob platformy Azure, tabele i kolejki dzienników.
- **Dzienniki zapory.**  Brama aplikacji w pełnej diagnostyki i uzyskać dostępu do dzienników. Dzienniki zapory są dostępne dla bramy aplikacji zasoby, które mają zapory aplikacji sieci Web jest włączona.
- **Archiwizowanie dziennika.**  Wszystkie dzienniki diagnostyczne są skonfigurowane do zapisywania scentralizowany i zaszyfrowane koncie magazynu Azure dla archiwizacji z okresu przechowywania określonych (2 dni). Dzienniki są następnie połączonych z Analiza dzienników Azure na potrzeby przetwarzania, przechowywania i dashboarding. [Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics) to usługa, która umożliwia zbieranie i analizowanie danych wygenerowanych przez zasobów w chmurze i lokalnych środowiskach.

### <a name="encryption-and-secrets-management"></a>Szyfrowanie i kluczy tajnych zarządzania

Magazynu sieci Web firmy Contoso szyfruje wszystkie dane poufne i używa usługi Azure Key Vault w celu zarządzania kluczami i zapobiec pobierania CHD.

- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pomaga w zabezpieczaniu kluczy kryptograficznych i kluczy tajnych używanych przez usługi i aplikacje w chmurze. 
- [Funkcji SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) służy do szyfrowania wszystkich danych finansowych klientów.
- Dane są przechowywane na dysku za pomocą [szyfrowania dysków Azure](/azure/security/azure-security-disk-encryption) i funkcji BitLocker.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie potwierdzenie tożsamości możliwości zarządzania w środowisku platformy Azure.

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Wszyscy użytkownicy dla rozwiązania zostały utworzone w usłudze Azure Active Directory, w tym użytkownikom dostęp do bazy danych SQL.
- Aby aplikacja uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych również używa usługi Azure AD do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz [zawsze zaszyfrowane: ochrona poufnych danych w bazie danych SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach, które mogą wpłynąć na tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzane zdarzenia i podejmuje odpowiednie działania w celu ich rozwiązywania.
- [Azure opartej na rolach kontroli dostępu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji i usługi Azure Key Vault dostęp jest ograniczony do wszystkich użytkowników.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń bazy danych SQL Azure, zobacz [aplikacja demonstracyjna Clinic Contoso](https://github.com/Microsoft/azure-sql-security-sample) próbki.
   
### <a name="web-and-compute-resources"></a>Zasoby obliczeniowe i sieci Web

#### <a name="app-service-environment"></a>Środowisko usługi App Service

[Usługa aplikacji Azure](/azure/app-service/) jest zarządzaną usługę służącą do wdrażania aplikacji sieci web. Aplikacja Contoso magazynu sieci Web jest wdrażana jako [aplikację usługi sieci Web aplikacji](/azure/app-service-web/app-service-web-overview).

[Środowiska usługi aplikacji Azure (ASE v2)](/azure/app-service/app-service-environment/intro) to funkcja usługi aplikacji, która udostępnia środowisko pełni izolowanym środowisku, aby bezpiecznie pracować aplikacji usługi App Service na dużą skalę. jest używany przez tej architektury podstawowych do zapewnienia zgodności PCI DSS planu usługi Premium.

ASEs izolowane do uruchamiania tylko jednego odbiorcy aplikacji i zawsze są wdrażane w sieci wirtualnej. Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacji może nawiązywać bezpiecznych połączeń o dużej szybkości za pośrednictwem sieci wirtualnej do zasobów firmy lokalnie.

Korzystanie z ASEs dla tej architektury dozwolone dla formantów/konfiguracji.

- Hostowanie w zabezpieczonej sieci wirtualnych i reguły zabezpieczeń sieci
- Skonfigurowano certyfikatu z podpisem własnym ILB dla komunikacji HTTPS ASE
- [Tryb równoważenia obciążenia wewnętrznego](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (tryb 3)
- [Protokołu TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) wyłączone
- [Szyfrowania TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings) zmienione
- Formant [ruchu przychodzącego ruchu N/W portach](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [Zapory aplikacji sieci Web - ograniczanie danych](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Baza danych SQL ruchu](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) dozwolone


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastionu hosta)

Ponieważ środowisko usługi aplikacji jest zabezpieczony i zablokowane, musi istnieć mechanizm umożliwia wersjach opracowywania oprogramowania lub zmiany, które mogą być wymagane, takie jak możliwość monitorowania aplikacji sieci web przy użyciu Kudu. Maszyna wirtualna jest chroniona za NAT modułu równoważenia obciążenia, które umożliwia połączenie z maszyną Wirtualną przez port inny niż 3389 protokołu TCP. 

Maszyna wirtualna została utworzona jako jumpbox (host bastionu) z następujących konfiguracji:

-   [Rozszerzenie ochrony przed złośliwym oprogramowaniem](/azure/security/azure-security-antimalware)
-   [Rozszerzenia analizy dzienników](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie Diagnostyka Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Szyfrowanie dysków Azure](/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault 
-   [Zasad automatyczne zamykanie](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane

### <a name="security-and-malware-protection"></a>Ochrona bezpieczeństwa i złośliwego oprogramowania

[Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) zapewnia scentralizowane widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Jeden rzut oka należy sprawdzić, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowany i można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.  

[Klasyfikator Azure](/azure/advisor/advisor-overview) jest konsultanta spersonalizowane chmury, ułatwiająca należy stosować najlepsze rozwiązania w celu zoptymalizowania wdrożeń platformy Azure. Analizuje konfigurację zasobu i danych telemetrycznych użycia, a następnie zaleca rozwiązania, które mogą pomóc w poprawie opłacalności, wydajność, wysokiej dostępności i zabezpieczeń zasobów platformy Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) dla usług w chmurze Azure i maszyn wirtualnych jest możliwość ochrony w czasie rzeczywistym, która pomaga w identyfikacji i usuwania wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty, gdy znane złośliwe lub niepożądane oprogramowanie próbuje się zainstalować lub uruchomić na komputerach z platformy Azure.

### <a name="operations-management"></a>Zarządzanie operacjami

#### <a name="application-insights"></a>Application Insights

Użyj [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) do uzyskania przydatnych wyników analiz za pośrednictwem aplikacji Zarządzanie wydajnością i analiza błyskawicznych.

#### <a name="log-analytics"></a>Log Analytics

[Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics/) to usługa, która pomaga zbieranie i analizowanie danych wygenerowanych przez zasobów w chmurze i lokalnych środowiskach.

#### <a name="managment-solutions"></a>Rozwiązania do zarządzania

Takie rozwiązania do zarządzania dodatkowe powinien został uznany za i skonfigurowane: 
- [Activity Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Analiza sieci platformy Azure](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Śledzenie zmian](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Analiza usługi Key Vault](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Mapa usługi](/azure/operations-management-suite/operations-management-suite-service-map)
- [Zabezpieczenia i inspekcja](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Oprogramowanie chroniące przed złośliwym kodem](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Zarządzanie aktualizacjami](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Integracja z Centrum zabezpieczeń

Domyślnym wdrożeniu mają na celu dostarczenie linii bazowej zaleceń Centrum zabezpieczeń, które wskazują stan konfiguracji dobrej kondycji i bezpieczne. Można włączyć zbierania danych z Centrum zabezpieczeń Azure. Aby uzyskać więcej informacji, zobacz [Centrum zabezpieczeń Azure — wprowadzenie](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Składniki wdrażania tego rozwiązania są dostępne w [repozytorium kodu planu][code-repo]. Wdrażanie podstawowych architektura wymaga wykonania kilku czynności wykonywane przy użyciu v5 PowerShell firmy Microsoft. Aby połączyć z poziomu witryny sieci Web, podaj niestandardowej nazwy domeny (np. contoso.com). To jest określona za pomocą `-customHostName` przełącznika w kroku 2. Aby uzyskać więcej informacji, zobacz [kupić niestandardowej nazwy domeny dla aplikacji sieci Web Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Niestandardowej nazwy domeny nie jest wymagane, aby pomyślnie wdrożyć i uruchomić rozwiązania, ale będzie mógł podłączyć się do witryny sieci Web w celach demonstracyjnych.

Skrypty dodać użytkowników domeny do dzierżawy usługi Azure AD, który określisz. Firma Microsoft zaleca utworzenie nowej usługi Azure AD dzierżawy do użycia jako test.

Jeśli wystąpią problemy podczas wdrażania, zobacz [— często zadawane pytania i rozwiązywanie problemów z](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

Firma Microsoft zaleca, że czystą instalację programu PowerShell można użyć do wdrożenia rozwiązania. Sprawdź też, że używasz najnowszej modułami wymaganymi do sprawnego wykonywania skryptów instalacji. W tym przykładzie loguje się do jumpbox (host bastionu) i wykonuje następujące polecenia. Należy pamiętać, że dzięki temu polecenie domeny niestandardowej.


1. Zainstaluj wymagane moduły i poprawnie skonfigurowane ról administratora.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Szczegółowe instrukcje dotyczące obsługi, zobacz [instrukcje skryptu — konto administracyjne ustawienia i uprawnienia](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Zainstaluj zarządzania aktualizacjami rozwiązania. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Szczegółowe instrukcje dotyczące obsługi, zobacz [instrukcje skryptu — wdrażanie i konfigurowanie zasobów Azure](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Dziennik analizy rejestrowania i monitorowania. Po wdrożeniu rozwiązania można otworzyć obszaru roboczego analizy dzienników i przykładowe szablony w repozytorium rozwiązania może służyć do zilustrowania konfiguracji monitorowania pulpitu nawigacyjnego. Przykładowe szablony, można znaleźć w temacie [folderu omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Należy pamiętać, że należy zbierać dane w analizy dzienników dla szablonów do wdrożenia poprawnie. To może potrwać godzinę lub dłużej w zależności od działania lokacji.
 
    Podczas konfigurowania sieci rejestrowania analizy dzienników, należy rozważyć umieszczenie tych zasobów:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych (DPD) i przykładowe modelu zagrożeń dla magazynu sieci Web firmy Contoso [modelu zagrożeń planu](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Macierz odpowiedzialność klienta

Klienci są zobowiązani do zachowania kopię [odpowiedzialność podsumowanie macierzy](https://aka.ms/fsiblueprintcrm), który opisano wymagania FFIEC, które są odpowiedzialne za klienta oraz te, które są odpowiedzialne za Microsoft Azure.



## <a name="disclaimer-and-acknowledgments"></a>Zastrzeżenie i potwierdzenia

*2017 września*

- Ten dokument jest tylko do celów informacyjnych. FIRMA MICROSOFT I AVYAN NALEŻY UDZIELANIA ŻADNYCH GWARANCJI, WYRAŻONYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.  
- Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produkt firmy Microsoft lub Avyan lub rozwiązania.  
- Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.  

  > [!NOTE]
  > Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  

- Rozwiązanie w tym dokumencie ma pełnić rolę architektura podstawowych i nie mogą być używane jako — do celów produkcyjnych. Uzyskanie zgodności wymaga, aby klientów należy skontaktować się z ich audytora do sprawdzania poprawności rozwiązań końcowego klienta.  
- Wszystkie nazwy klientów, rekordy transakcji i wszelkie powiązane dane na tej stronie są fikcyjne, utworzone na potrzeby tej architektury podstawowych i do celów ilustracyjnych. Żadne rzeczywiste skojarzenia ani połączenia jest przeznaczony i nie powinno być wnioskowane.  
- To rozwiązanie został opracowany wspólnie przez firmę Microsoft i konsulting Avyan i jest dostępny w obszarze [licencji MIT](https://opensource.org/licenses/MIT).

### <a name="document-authors"></a>Autorzy dokumentu

* *Piotr Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repozytorium kodu"
