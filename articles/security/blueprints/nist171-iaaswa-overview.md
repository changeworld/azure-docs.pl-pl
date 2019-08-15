---
title: Aplikacja sieci Web Strategia zabezpieczeń i zgodności z przepisami platformy Azure IaaS dla programu NIST SP 800-171
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — IaaS Web Application NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 88bca1a799d55ba59c8f5d2263f3219cfb66700e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946720"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Aplikacja sieci Web Strategia zabezpieczeń i zgodności z przepisami platformy Azure IaaS dla programu NIST SP 800-171

## <a name="overview"></a>Omówienie
[Specjalna publikacja NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony kontrolowanych niesklasyfikowanych informacji (CUI), które znajdują się w niefederalnych systemach i organizacjach informacyjnych. NIST SP 800-171 ustanawia 14 rodzin wymagań dotyczących zabezpieczeń, aby chronić poufność CUI.

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki ułatwiające klientom wdrożenie architektury aplikacji sieci Web na platformie Azure, która implementuje podzestaw formantów NIST SP 800-171. W tym rozwiązaniu pokazano, w jaki sposób klienci mogą spełniać określone wymagania w zakresie zabezpieczeń i zgodności. Służy ona również jako podstawa dla klientów do kompilowania i konfigurowania własnych aplikacji sieci Web na platformie Azure.

Ta architektura referencyjna, skojarzony Przewodnik implementacji oraz model zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań. Nie powinny być używane w środowisku produkcyjnym. Wdrożenie tej architektury bez modyfikacji jest niewystarczające, aby całkowicie spełnić wymagania architektury NIST SP 800-171. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury. Wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure wdraża architekturę referencyjną dla aplikacji sieci Web IaaS z zaplecem SQL Server. Architektura obejmuje warstwę sieci Web, warstwę danych, infrastrukturę Active Directory, Application Gateway platformy Azure i Azure Load Balancer. Maszyny wirtualne wdrożone w sieci Web i warstwy danych są konfigurowane w zestawie dostępności. Wystąpienia SQL Server są konfigurowane w zawsze włączone grupy dostępności w celu zapewnienia wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny. Active Directory zasady grupy wymuszają konfiguracje zabezpieczeń i zgodności na poziomie systemu operacyjnego.

Całe rozwiązanie jest oparte na usłudze Azure Storage, którą klienci konfigurują na podstawie Azure Portal. Magazyn szyfruje wszystkie dane za pomocą szyfrowanie usługi Storage, aby zachować poufność danych przechowywanych w spoczynku. Magazyn Geograficznie nadmiarowy zapewnia, że niekorzystne zdarzenie w podstawowym centrum danych klienta nie powoduje utraty danych. Druga kopia jest przechowywana w oddzielnej lokalizacji setki kilometrów.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Usługa Azure Active Directory (Azure AD) Kontrola dostępu oparta na rolach (RBAC) służy do kontrolowania dostępu do wdrożonych zasobów i kluczy w programie Azure Key Vault. Kondycja systemu jest monitorowana za Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników. Kondycja systemu jest wyświetlana na jednym pulpicie nawigacyjnym łatwym w użyciu.

Host zarządzania bastionu zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. *Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi Azure ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.*


![IaaS Web Application for NIST SP 800-171 diagram architektury referencyjnej](images/nist171-iaaswa-architecture.png "IaaS Web Application for NIST SP 800-171 diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Aby uzyskać więcej informacji, zobacz sekcję [architektury wdrożenia](#deployment-architecture) .

- Usługa Azure Virtual Machines
    - (1) Zarządzanie/bastionu (Windows Server 2016 Datacenter)
    - (2) Active Directory kontroler domeny (Windows Server 2016 Datacenter)
    - (2) SQL Server węzeł klastra (SQL Server 2017 w systemie Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 sieci
    - (5)/24 sieci
    - (5) sieciowe grupy zabezpieczeń
- Zestawy dostępności
    - (1) Active Directory kontrolery domeny
    - (1) węzły klastra SQL
    - (1) sieć Web/IIS
- Azure Application Gateway
    - (1) Zapora aplikacji sieci Web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Port odbiornika: 443
- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor (dzienniki)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Monitor w chmurze
- Magazyn usług Recovery Services

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Host bastionu**: Host bastionu to pojedynczy punkt wprowadzania, za pomocą którego użytkownicy mogą uzyskać dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie ze wdrożonymi zasobami, zezwalając na bezpieczną listę tylko ruchu zdalnego z publicznych adresów IP. Aby zezwolić na ruch pulpitu zdalnego, źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń).

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Key Vault.
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyn wirtualnych, gdy nie są używane.
-   [Funkcja Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) jest włączona, aby poświadczenia i inne wpisy tajne były uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie służy do wdrażania zasobów w architekturze z oddzielnymi podsieciami dla sieci Web, baz danych, Active Directory i zarządzania w ramach sieci wirtualnej. Podsieci są logicznie oddzielone regułami sieciowej grupy zabezpieczeń stosowanymi w poszczególnych podsieciach. Reguły ograniczają ruch między podsieciami tylko do tych, które są niezbędne do funkcjonowania systemu i zarządzania.

Zapoznaj się z konfiguracją [sieciowych grup zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożoną w tym rozwiązaniu. Organizacje mogą konfigurować sieciowych grup zabezpieczeń, edytując poprzedni plik za pomocą [tej dokumentacji](../../virtual-network/virtual-network-vnet-plan-design-arm.md) jako przewodnika.

Każda podsieć ma dedykowany sieciowej grupy zabezpieczeń:
- Jeden sieciowej grupy zabezpieczeń dla Application Gateway (LBNSG)
- Jeden sieciowej grupy zabezpieczeń dla hosta bastionu (MGTNSG)
- Jeden sieciowej grupy zabezpieczeń dla podstawowych i zapasowych kontrolerów domeny (ADNSG)
- Jeden sieciowej grupy zabezpieczeń dla serwerów SQL i monitor chmury (SQLNSG)
- Jedna sieciowej grupy zabezpieczeń dla warstwy sieci Web (WEBNSG)

### <a name="data-in-transit"></a>Dane przesyłane
Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Ponadto wszystkie transakcje do magazynowania za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez wiele miar. Te miary obejmują szyfrowanie i inspekcję bazy danych.

**Azure Storage**: Aby spełnić wymagania dotyczące szyfrowanych danych [przechowywanych w magazynie](https://azure.microsoft.com/services/storage/) , wszystkie magazyny są używane [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Ta funkcja pomaga chronić i zabezpieczać dane w celu obsługi zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności określonych przez instytut NIST SP 800-171.

**Azure Disk Encryption**: Szyfrowanie dysków służy do szyfrowania dysków maszyn wirtualnych z systemem Windows IaaS. [Szyfrowanie dysków](../azure-security-disk-encryption-overview.md) używa funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych. Rozwiązanie jest zintegrowane z Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**SQL Server**: W wystąpieniu SQL Server są stosowane następujące miary zabezpieczeń bazy danych:
-   [Inspekcja SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) śledzi zdarzenia bazy danych i zapisuje je w dziennikach inspekcji.
-   [Szyfrowanie danych przezroczystych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) wykonuje szyfrowanie w czasie rzeczywistym oraz odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w celu ochrony informacji przechowywanych w pamięci podręcznej. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) ogranicza narażenie na dane poufne przez maskowanie danych do nieuprzywilejowanych użytkowników lub aplikacji. Może automatycznie odnajdywać potencjalnie wrażliwe dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak aby dane poufne nie wyłączać bazy danych za pośrednictwem nieautoryzowanego dostępu. *Klienci są odpowiedzialni za dostosowanie ustawień, aby były zgodne ze schematem bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości zarządzania dostępem do danych w środowisku platformy Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) to usługa zarządzania katalogiem i tożsamościami opartymi na chmurze firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzeniu w usłudze Azure AD i obejmują użytkowników, którzy uzyskują dostęp do wystąpienia SQL Server.
-   Uwierzytelnianie w aplikacji odbywa się przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz jak [zintegrować aplikacje z usługą Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
-   Administratorzy [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) mogą służyć administratorom do definiowania szczegółowych uprawnień dostępu w celu przyznania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) mogą być używane przez klientów w celu zminimalizowania liczby użytkowników, którzy mają dostęp do określonych zasobów. Administratorzy mogą używać Azure AD Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji. Służy do konfigurowania automatycznych odpowiedzi w celu wykrycia podejrzanych działań związanych z tożsamościami organizacji. Bada także podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie**wpisami tajnymi: Rozwiązanie używa [Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Key Vault pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Następujące funkcje Key Vault pomagają klientom chronić dane:
- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowego sprzętu zabezpieczenia-chroniony przez moduł 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu RBAC.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.
- Rozwiązanie jest zintegrowane z Key Vault do zarządzania dyskami maszyny wirtualnej IaaS — kluczami szyfrowania i wpisami tajnymi.

**Zarządzanie poprawkami**: Maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane do odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia, aby zastosować poprawki do maszyn wirtualnych w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Klienci mogą konfigurować alerty, które generują, kiedy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować lub uruchomić na chronionych maszynach wirtualnych.

**Azure Security Center**: Dzięki [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Security Center również uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków, które są ukierunkowane na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Security Center zawiera zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. Klienci mogą używać [niestandardowych reguł alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w celu definiowania nowych alertów zabezpieczeń opartych na danych, które zostały już zebrane z ich środowiska.

Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia. Security Center ułatwia klientom odnajdywanie potencjalnych problemów z zabezpieczeniami i ich rozwiązywanie. Dla każdego wykrytego zagrożenia jest generowany [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Zespoły reagowania na incydenty mogą korzystać z raportów podczas badania i korygowania zagrożeń.

Ta architektura referencyjna używa funkcji [oceny luk](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w zabezpieczeniach w Security Center. Po jego skonfigurowaniu Agent partnera (na przykład Qualys) zgłasza luki w zabezpieczeniach do platformy zarządzania partnera. W odpowiedzi platforma zarządzania partnera dostarcza dane monitorowania o lukach w zabezpieczeniach i kondycji do usługi Security Center. Klienci mogą używać tych informacji, aby szybko identyfikować podatne na nie maszyny wirtualne.

**Application Gateway platformy Azure**: Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu bramy aplikacji z skonfigurowaną zaporą aplikacji sieci Web i włączonym zestawem reguł OWASP. Dodatkowe możliwości obejmują:

- [Kompleksowy protokół SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md).
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (tryb zapobiegania).
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0.
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md).
- [Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Security Center udostępnia również system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Rozwiązanie wdraża wszystkie maszyny wirtualne w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone w wielu izolowanych klastrach sprzętowych w celu zwiększenia dostępności. Podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna, która spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej. Ten proces przyspiesza czas przywracania.

**Monitor chmury**: [Monitor chmury](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu failover w systemie Windows Server 2016, który korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, taki jak każdy inny monitor kworum, otrzymuje głos i może uczestniczyć w obliczeniach kworum. Używa on standardowego publicznie dostępnego magazynu obiektów blob platformy Azure. To rozwiązanie eliminuje dodatkowe obciążenie pracą maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/diagnostic-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Te dzienniki obejmują Dzienniki systemu Windows, dzienniki magazynu, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania (do 730 dni) w celu spełnienia określonych wymagań.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu danych są one podzielone na oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics. W ten sposób wszystkie dane można analizować razem niezależnie od oryginalnego źródła. Security Center integruje się z dziennikami Azure Monitor. Klienci mogą używać zapytań Kusto, aby uzyskać dostęp do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Ocena Active Directory](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie do sprawdzania kondycji Active Directory ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Ocena SQL](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie sprawdzania kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Zapewnia ona klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną. Raporty te również przedstawiają liczbę agentów, które nie odpowiadają, oraz liczbę agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Usługi Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowują elementy Runbook, uruchamiają je i zarządzają nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z SQL Server. Klienci mogą korzystać z rozwiązania [Change Tracking](../../automation/change-tracking.md) Automation, aby łatwo identyfikować zmiany w środowisku.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów. Organizacje mogą używać go do inspekcji, tworzenia alertów i archiwizowania danych. Mogą również śledzić wywołania interfejsu API w swoich zasobach platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/nist171-iaaswa-tm) lub można go znaleźć tutaj. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![IaaS Web Application for NIST SP 800-171 — model zagrożeń](images/nist171-iaaswa-threat-model.png "IaaS Web Application for NIST SP 800-171 — model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności

W przypadku [macierzy z strategia zabezpieczeń i zgodności z przepisami platformy Azure-NIST SP 800-171 klient](https://aka.ms/nist171-crm) zawiera listę wszystkich kontroli zabezpieczeń wymaganych przez instytut NIST SP 800-171. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja poszczególnych kontroli jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji kontroli aplikacji sieci web strategia zabezpieczeń i zgodności z przepisami platformy Azure-NIST sp 800-171 IaaS](https://aka.ms/nist171-iaaswa-cim) zawiera informacje na temat tego, które kontrolki NIST SP 800-171 są rozwiązywane przez architekturę aplikacji sieci Web IaaS. Zawiera szczegółowy opis sposobu, w jaki implementacja spełnia wymagania poszczególnych formantów objętych usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web IaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu. Klienci mogą używać tego połączenia, aby bezpiecznie "tunelować" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną jeszcze bezpieczną opcję połączenia. ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Połączenia ExpressRoute nawiązują połączenie bezpośrednio z dostawcą telekomunikacyjnym klienta. W związku z tym dane nie podróżują przez Internet i nie są w nim ujawniane. Połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia. 

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zastrzeżenie

- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem. 
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft. 
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych. 
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta. 
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
