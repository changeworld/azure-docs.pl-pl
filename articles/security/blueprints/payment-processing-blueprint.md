---
title: Azure zabezpieczeń i zgodności plan - środowisk standardem PCI DSS przetwarzania płatności
description: Azure zabezpieczeń i zgodności plan - środowisk standardem PCI DSS przetwarzania płatności
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: frasim
ms.openlocfilehash: 5851d5499c61cf99d7f85d07642a292f3b8c19d2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---pci-dss-compliant-payment-processing-environments"></a>Azure zabezpieczeń i zgodności plan - środowisk standardem PCI DSS przetwarzania płatności

## <a name="overview"></a>Przegląd

Przetwarzanie płatności PCI DSS zgodne środowisk zawiera wskazówki dotyczące wdrożenia odpowiednie do obsługi dane kart płatniczych ważne środowiska standardem PCI DSS platformy jako — usługa (PaaS). Ilustrację architektura odwołania, a zaprojektowano w celu uproszczenia wdrażania programu Microsoft Azure. Ten plan przedstawiono rozwiązanie na trasie do potrzeb organizacji znalezienia podejście oparte na chmurze w celu zmniejszenia obciążenia i koszt wdrożenia.

Ten plan ułatwia spełnianie wymagań rygorystyczne Payment Card Industry Data Security Standards (PCI DSS 3.2) do zbierania, przechowywania i pobierania dane kart płatniczych. Pokazuje prawidłowego obsługi danych karty kredytowej (w tym karty numer, wygaśnięcia i weryfikacji danych), w środowisku wielowarstwowych bezpieczne i zgodne wdrożony jako end-to-end rozwiązania PaaS bazujących na platformie Azure. Aby uzyskać więcej informacji na temat wymagań PCI DSS 3.2 i tego rozwiązania, zobacz [PCI DSS wymagania — omówienie wysokiego szczebla](pci-dss-requirements-overview.md).

Ten plan ma służyć jako podstawę do klientów lepiej zrozumieć szczególne wymagania i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez żadnych modyfikacji nie jest wystarczające całkowicie wymogów standardem PCI DSS rozwiązania dla niestandardowego rozwiązania. Pamiętaj o następujących kwestiach:
- Ten plan zawiera linii bazowej, aby ułatwić klientom korzystać w sposób zgodny z PCI DSS Microsoft Azure.
- Osiągnięcia zgodności ze standardem PCI DSS wymaga czy akredytowanego kwalifikowaną zabezpieczeń doświadczeniem (QSA) certyfikować rozwiązaniem klienta produkcyjnego.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności przeglądy dowolnego rozwiązania utworzone przy użyciu tej architektury podstawowych, ponieważ wymagania mogą się różnić oparte na szczegóły implementacji i geograficzne każdego klienta.  

Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, obejrzyj ten [wideo](https://aka.ms/pciblueprintvideo) wyjaśniający i prezentowania jego wdrażania.

## <a name="solution-components"></a>Składniki rozwiązania

Architektura podstawowych składa się z następujących składników:

- **Diagram architektury**. Diagram przedstawia architekturę Odwołanie używane dla rozwiązania magazynu sieci Web firmy Contoso.
- **Szablony wdrażania**. W tym wdrożeniu [szablonów usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) są używane do automatycznego wdrożenia składników architektury w Microsoft Azure, określając parametry konfiguracji podczas instalacji.
- **Skrypty wdrażania automatycznego**. Skrypty te pomocy wdrożyć rozwiązanie end-to-end. Skrypty obejmują:
    - Instalacja modułu i [administratora globalnego](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) skrypt instalacyjny jest używane do instalowania i sprawdź, czy wymagane moduły programu PowerShell i ról administratora globalnego są poprawnie skonfigurowane.
    - Instalacja skryptu PowerShell służy do wdrażania rozwiązania end-to-end, podanego przez plik .zip oraz pliku bacpac, który zawiera pokaz wstępnie zbudowanych aplikacji sieci web z [przykładowej bazy danych SQL](https://github.com/Microsoft/azure-sql-security-sample). zawartość. Kod źródłowy dla tego rozwiązania jest gotowa do przeglądu [ repozytorium kodu planu][code-repo]. 

## <a name="architectural-diagram"></a>Diagram architektury

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Scenariusz użytkownika

Plan adresów poniżej przypadek użycia.

> W tym scenariuszu pokazano, jak fikcyjne webstore przenieść jej przetwarzania do rozwiązania na podstawie Azure PaaS karta płatności. Rozwiązanie obsługuje zbieranie informacji użytkownika podstawowego, w tym dane o płatności. Rozwiązanie nie może przetwarzać płatności przy użyciu tych danych posiadacza; gdy dane są zbierane, klienci są zobowiązani do inicjowania i uzupełniania transakcji z procesorem płatności. Aby uzyskać więcej informacji, zobacz ["Przeglądu i wskazówki dla wdrożenia"](https://aka.ms/pciblueprintprocessingoverview).

### <a name="use-case"></a>Przypadek użycia
Mała magazynu sieci Web o nazwie *magazynu sieci Web firmy Contoso* jest gotowa do przejścia ich system płatności do chmury. Został wybrany Microsoft Azure do hostowania proces zakupu i umożliwia pracownik do zbierania płatności kartą kredytową z klientów.

Administrator szuka rozwiązania, które można szybko wdrożyć celach jego w rozwiązania urodzony chmury. ADAM użyje tego elementu koncepcji (POC) do omówienia z jego uczestników, jak Azure może służyć do zbierania, przechowywania i pobrać dane kart płatniczych podczas rygorystycznych wymagań Payment Card Industry Data Security Standard (PCI DSS).

> Będzie odpowiedzialne za przeprowadzanie odpowiednich ustawień zabezpieczeń i zgodności przeglądami od wszelkich rozwiązań do skompilowanej za pomocą architektury używanych przez ten fazy weryfikacji Koncepcji, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania i geograficzne. PCI DSS wymaga pracy bezpośrednio z akredytowanego doświadczeniem kwalifikowaną zabezpieczeń do certyfikowania rozwiązania gotowe do produkcji.

### <a name="elements-of-the-foundational-architecture"></a>Elementy architektury podstawowych

Architektura podstawowych jest wyposażone w fikcyjne następujące elementy:

Domeny lokacji `contosowebstore.com`

Role użytkowników używane do zilustrowania przypadek użycia i zapewniają wgląd w interfejsie użytkownika.

#### <a name="role-site-and-subscription-admin"></a>Rola: Administrator lokacji i subskrypcji

|Element      |Przykład|
|----------|------|
|Nazwa użytkownika: |`adminXX@contosowebstore.com`|
| Nazwa: |`Global Admin Azure PCI Samples`|
|Typ użytkownika:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- Konto administratora nie można odczytać informacji o karcie kredytowej pozbawione maskowania. Wszystkie akcje są rejestrowane.
- Konto administratora nie może zarządzać lub zaloguj się do bazy danych SQL.
- Konto administratora mogą zarządzać usługi Active Directory i subskrypcji.

#### <a name="role-sql-administrator"></a>Rola: Administrator SQL

|Element      |Przykład|
|----------|------|
|Nazwa użytkownika: |`sqlAdmin@contosowebstore.com`|
| Nazwa: |`SQLADAdministrator PCI Samples`|
| Imię: |`SQL AD Administrator`|
|Nazwisko: |`PCI Samples`|
|Typ użytkownika:| `Administrator`|

- Konto sqladmin nie można wyświetlić informacje o karcie kredytowej niefiltrowane. Wszystkie akcje są rejestrowane.
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

- Edna można tworzyć i przeczytaj informacje o kliencie
- Edna można zmodyfikować informacje o kliencie.
- Edna można zastąpić, lub Zastąp numer karty kredytowej, wygaśnięcia i CVV informacji.

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
>- Kontrola dostępu oparta na rolach w usłudze Azure Active Directory (RBAC)

## <a name="deployment-architecture"></a>Architektura wdrażania

Poniższa sekcja zawiera szczegóły dotyczące projektowania i wdrażania elementów.

### <a name="network-segmentation-and-security"></a>W przypadku segmentacji sieci i zabezpieczeń

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

Architektura podstawowych zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji z zapory aplikacji sieci web (WAF) i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [TLS 1.0 i 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](/azure/application-gateway/application-gateway-web-application-firewall-portal) z ruleset OWASP 3.0
- Włącz [rejestrowania diagnostyki](/azure/application-gateway/application-gateway-diagnostics)
- [Sondy kondycji niestandardowych](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center) i [Azure Advisor](/azure/advisor/advisor-security-recommendations) zapewnić dodatkową ochronę i powiadomień. Centrum zabezpieczeń Azure zapewnia również system reputacji.

#### <a name="virtual-network"></a>Sieć wirtualna

Architektura podstawowych definiuje prywatnej sieci wirtualnej z 10.0.0.0/16 przestrzeni adresowej.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Każdy z warstw sieci ma dedykowany sieciowej grupy zabezpieczeń (NSG):
- Grupa zabezpieczeń sieci obwodowej dla zapory i zapory aplikacji sieci Web dla bramy aplikacji
- Grupa NSG do zarządzania jumpbox (bastionu hosta)
- Grupa NSG dla środowiska usługi aplikacji

Każdy z grup NSG mieć określone porty i protokoły otwarty do bezpiecznego i prawidłowe funkcjonowanie rozwiązania. Aby uzyskać więcej informacji, zobacz [wskazówki PCI - grupy zabezpieczeń sieci](#network-security-groups).

Każdy z grup NSG mieć określone porty i protokoły otworzyć bezpiecznego i poprawne funkcjonowania rozwiązania. Ponadto następujące konfiguracje są włączone dla każdej grupy NSG:
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

[Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics) można udostępnić magazynu sieci Web firmy Contoso szczegółowe rejestrowanie całą aktywność systemu i użytkownika, obejmują posiadacza rejestrowanie danych. Zmiany można przejrzeć i sprawdzić dokładność. 

- **Dzienniki aktywności:**[Dzienniki aktywności](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji.
- **Dzienniki diagnostyczne:**[dzienniki diagnostyczne](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, magazynu obiektów Blob platformy Azure, tabele i kolejki dzienników.
- **Dzienniki zapory:** Application Gateway udostępnia pełnej diagnostyki i dostępu do dzienników. Dzienniki zapory są dostępne dla bramy aplikacji zasoby, które mają zapory aplikacji sieci Web jest włączona.
- **Archiwizacja dziennika:** wszystkich dzienników diagnostycznych są skonfigurowane do zapisu konto magazynu Azure scentralizowany i zaszyfrowane dla archiwizacji z okresu przechowywania określonych (2 dni). Dzienniki są następnie połączonych z Analiza dzienników Azure na potrzeby przetwarzania, przechowywania i dashboarding. [Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics) to usługa, która umożliwia zbieranie i analizowanie danych wygenerowanych przez zasobów w chmurze i lokalnych środowiskach.

### <a name="encryption-and-secrets-management"></a>Szyfrowanie i kluczy tajnych zarządzania

Magazynu sieci Web firmy Contoso szyfruje wszystkie dane karty kredytowej, a następnie używa usługi Azure Key Vault zarządzać kluczami, co uniemożliwia pobieranie CHD.

- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pomaga w zabezpieczaniu kluczy kryptograficznych i kluczy tajnych używanych przez usługi i aplikacje w chmurze. 
- [Funkcji SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) służy do szyfrowania wszystkich danych posiadacza klienta, datę wygaśnięcia i CVV.
- Dane są przechowywane na dysku za pomocą [szyfrowania dysków Azure](/azure/security/azure-security-disk-encryption) i funkcji BitLocker.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie potwierdzenie tożsamości możliwości zarządzania w środowisku platformy Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) jest firmy Microsoft wielodostępne oparte na chmurze katalogami i tożsamościami usługą zarządzania. Wszyscy użytkownicy dla rozwiązania zostały utworzone w usłudze Azure Active Directory, w tym użytkownikom dostęp do bazy danych SQL.
- Aby aplikacja uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych również używa usługi Azure AD do uwierzytelniania aplikacji z bazą danych SQL Azure. Aby uzyskać więcej informacji, zobacz [zawsze zaszyfrowane: ochrona poufnych danych w bazie danych SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzane zdarzenia i podejmuje odpowiednie działania w celu ich rozwiązywania.
- [Azure opartej na rolach kontroli dostępu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji i usługi Azure Key Vault dostęp jest ograniczony do wszystkich użytkowników.

Aby dowiedzieć się więcej o korzystaniu z funkcji zabezpieczeń bazy danych SQL Azure, zobacz [aplikacja demonstracyjna Clinic Contoso](https://github.com/Microsoft/azure-sql-security-sample) próbki.
   
### <a name="web-and-compute-resources"></a>Zasoby obliczeniowe i sieci Web

#### <a name="app-service-environment"></a>Środowisko usługi App Service

[Usługa aplikacji Azure](/azure/app-service/) jest zarządzaną usługę służącą do wdrażania aplikacji sieci web. Aplikacja Contoso magazynu sieci Web jest wdrażana jako [aplikację usługi sieci Web aplikacji](/azure/app-service-web/app-service-web-overview).

[Środowiska usługi aplikacji Azure (ASE v2)](/azure/app-service/app-service-environment/intro) to funkcja usługi aplikacji, która udostępnia środowisko pełni izolowanym środowisku, aby bezpiecznie pracować aplikacji usługi App Service na dużą skalę. jest używany przez tej architektury podstawowych do zapewnienia zgodności PCI DSS planu usługi Premium.

ASEs izolowane do uruchamiania tylko jednego odbiorcy aplikacji i zawsze są wdrażane w sieci wirtualnej. Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacji może nawiązywać bezpiecznych połączeń o dużej szybkości za pośrednictwem sieci wirtualnej do zasobów firmy lokalnie.

Korzystanie z ASEs dla tej architektury dozwolone dla formantów/konfiguracji.

- Host wewnątrz zabezpieczonej sieci wirtualnej i reguły zabezpieczeń
- Konfigurowane z certyfikatem ILB z podpisem własnym dla komunikacji HTTPS ASE
- [Tryb równoważenia obciążenia wewnętrznego](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (tryb 3)
- Wyłącz [protokołu TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) — protokół TLS, który jest przestarzały z punktu widzenia PCI DSS
- Zmień [szyfrowania TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Formant [ruchu przychodzącego ruchu N/W portach](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [Zapory aplikacji sieci Web — ograniczanie danych](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zezwalaj na [ruchu bazy danych SQL](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastionu hosta)

Środowiska usługi aplikacji jest zabezpieczona i zablokowane, musi istnieć mechanizm umożliwia wersjach opracowywania oprogramowania lub zmiany, które mogą być wymagane, takie jak możliwość monitorowania aplikacji sieci web przy użyciu Kudu. Maszyna wirtualna jest chroniona za NAT modułu równoważenia obciążenia, dzięki czemu można połączyć maszyny Wirtualnej na porcie innego niż 3389 protokołu TCP. 

Maszyna wirtualna została utworzona jako jumpbox (host bastionu) z następujących konfiguracji:

-   [Rozszerzenie ochrony przed złośliwym oprogramowaniem](/azure/security/azure-security-antimalware)
-   [Rozszerzenie pakietu OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie Diagnostyka Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Szyfrowanie dysków Azure](/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault (szanuje Azure dla instytucji rządowych, PCI DSS, HIPAA i inne wymagania).
-   [Zasad automatyczne zamykanie](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane.

### <a name="security-and-malware-protection"></a>Ochrona bezpieczeństwa i złośliwego oprogramowania

[Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) zapewnia scentralizowane widok stanu zabezpieczeń wszystkich zasobów platformy Azure. Jeden rzut oka należy sprawdzić, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowany i można szybko zidentyfikować wszystkie zasoby, które wymagają uwagi.  

[Klasyfikator Azure](/azure/advisor/advisor-overview) jest konsultanta spersonalizowane chmury, ułatwiająca należy stosować najlepsze rozwiązania w celu zoptymalizowania wdrożeń platformy Azure. Analizuje konfigurację zasobów i dane telemetryczne dotyczące użycia, a następnie zaleca rozwiązania, które mogą pomóc w zapewnieniu wysokiej dostępności, bezpieczeństwa, wydajności i efektywności kosztowej zasobów platformy Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) dla usług Azure Cloud Services i maszyn wirtualnych jest możliwość ochrony w czasie rzeczywistym, która pomaga w identyfikacji i usuwania wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty, gdy znane złośliwe lub niepożądane oprogramowanie próbuje się zainstalować lub uruchomić w systemie Azure.

### <a name="operations-management"></a>Zarządzanie operacjami

#### <a name="application-insights"></a>Application Insights

Użyj [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) do uzyskania przydatnych wyników analiz za pośrednictwem aplikacji Zarządzanie wydajnością i analiza błyskawicznych.

#### <a name="log-analytics"></a>Log Analytics

[Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics/) jest usługą platformy Azure, która ułatwia zbieranie i analizowanie danych wygenerowanych przez zasobów w chmurze i lokalnych środowiskach.

#### <a name="management-solutions"></a>Rozwiązania do zarządzania

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

Domyślnym wdrożeniu mają na celu dostarczenie linii bazowej zaleceń Centrum zabezpieczeń, wskazujący stan konfiguracji dobrej kondycji i bezpieczne. Można włączyć zbierania danych z Centrum zabezpieczeń Azure. Aby uzyskać więcej informacji, zobacz [Centrum zabezpieczeń Azure — wprowadzenie](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Składniki wdrażania tego rozwiązania są dostępne w [repozytorium kodu planu PCI][code-repo]. Wdrażanie podstawowych architektura wymaga wykonania kilku czynności wykonywane przy użyciu v5 PowerShell firmy Microsoft. Aby połączyć z poziomu witryny sieci Web, podaj niestandardowej nazwy domeny (np. contoso.com). To jest określona za pomocą `-customHostName` przełącznika w kroku 2. Aby uzyskać więcej informacji, zobacz [kupić niestandardowej nazwy domeny dla aplikacji sieci Web Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Niestandardowej nazwy domeny nie jest wymagane, aby pomyślnie wdrożyć i uruchomić rozwiązania, ale będzie mógł podłączyć się do witryny sieci Web w celach demonstracyjnych.

Skrypty dodać użytkowników domeny do dzierżawy usługi Azure AD, który określisz. Zaleca się utworzenie nowej dzierżawy usługi Azure AD ma być używana jako testu.

Jeśli wystąpią problemy podczas wdrażania, zobacz [— często zadawane pytania i rozwiązywanie problemów z](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

Zdecydowanie zaleca się, że czystą instalację programu PowerShell można użyć do wdrożenia rozwiązania. Sprawdź też, że używasz najnowszej modułami wymaganymi do sprawnego wykonywania skryptów instalacji. W tym przykładzie firma Microsoft Zaloguj się do jumpbox (host bastionu) i uruchom następujące polecenia. Należy pamiętać, że dzięki temu polecenie domeny niestandardowej.


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
    
2. Zainstaluj zarządzania aktualizacjami rozwiązania 
 
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
    
3. Monitorowanie i rejestrowanie. Po wdrożeniu rozwiązania można otworzyć obszaru roboczego analizy dzienników i przykładowe szablony w repozytorium rozwiązania może służyć do zilustrowania konfiguracji monitorowania pulpitu nawigacyjnego. Przykładowych szablonów można znaleźć w publikacji [folderu omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Należy pamiętać, że należy zbierać dane w analizy dzienników dla szablonów do wdrożenia poprawnie. To może potrwać godzinę lub dłużej w zależności od działania lokacji.
 
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

Klienci są zobowiązani do zachowania kopię [odpowiedzialność podsumowanie macierzy](https://aka.ms/pciblueprintcrm32), który opisano wymagania PCI DSS, które są odpowiedzialne za klienta, jak i tych, które są odpowiedzialne za Microsoft Azure.

## <a name="pci-compliance-review"></a>Przegląd zgodności PCI 

Rozwiązanie zostało sprawdzone przez Coalfire systems, Inc. (PCI-DSS kwalifikowaną oceniających zabezpieczeń). [Wskazówki dotyczące wdrażania i przejrzyj zgodności PCI](https://aka.ms/pciblueprintprocessingoverview) zawiera przegląd audytora rozwiązanie i uwagi dotyczące Przekształcanie plan wdrażania gotowe do produkcji.

## <a name="disclaimer-and-acknowledgements"></a>Zastrzeżenie i potwierdzeń

*2017 września*

- Ten dokument jest tylko do celów informacyjnych. FIRMA MICROSOFT I AVYAN NALEŻY UDZIELANIA ŻADNYCH GWARANCJI, WYRAŻONYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.  
- Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produkt firmy Microsoft lub Avyan lub rozwiązania.  
- Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.  

  > [!NOTE]
  > Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  

- Rozwiązanie w tym dokumencie ma pełnić rolę architektura podstawowych i nie mogą być używane jako — do celów produkcyjnych. Uzyskanie PCI zgodności wymaga, że klienci należy skontaktować się z ich kwalifikowaną doświadczeniem zabezpieczeń.  
- Wszystkie nazwy klientów, rekordy transakcji i wszelkie powiązane dane na tej stronie są fikcyjne, utworzone na potrzeby tej architektury podstawowych i do celów ilustracyjnych. Żadne rzeczywiste skojarzenia ani połączenia jest przeznaczony i nie powinno być wnioskowane.  
- To rozwiązanie został opracowany wspólnie przez firmę Microsoft i konsulting Avyan i jest dostępny w obszarze [licencji MIT](https://opensource.org/licenses/MIT).
- To rozwiązanie zostało zbadane przez Coalfire, Audytor PCI-DSS firmy Microsoft. [Przejrzyj zgodności PCI](https://aka.ms/pciblueprintcrm32) zapewnia niezależne, innych firm przeglądu rozwiązania i składników, które należy rozważyć. 

### <a name="document-authors"></a>Autorzy dokumentu

- *Piotr Simorjay (Microsoft)*  
- *Gururaj Pandurangi (Avyan konsultacji)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repozytorium kodu"
