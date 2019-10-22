---
title: Aplikacja sieci Web Strategia zabezpieczeń i zgodności z przepisami platformy Azure IaaS dla Wielkiej Brytanii NHS
description: Aplikacja sieci Web Strategia zabezpieczeń i zgodności z przepisami platformy Azure IaaS dla Wielkiej Brytanii NHS
services: security
author: jomolesk
ms.assetid: 6081bab2-315a-4af4-92a1-7c773f449d66
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 9b0478b3e72a759186d7d18ce6f7a885a1098d4b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71259509"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-uk-nhs"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: aplikacja sieci Web IaaS dla Wielkiej Brytanii NHS

## <a name="overview"></a>Przegląd

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zapewnia architekturę referencyjną i wskazówki dotyczące aplikacji sieci Web infrastruktury jako usługi (IaaS) odpowiedniej do zbierania, przechowywania i pobierania danych o opiece zdrowotnej. W tym rozwiązaniu pokazano, w jaki sposób klienci mogą przestrzegać wskazówek dotyczących usługi [Cloud Security dobry Practice Guide](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) opublikowanej przez [NHS Digital](https://digital.nhs.uk/), partnera działu IT (Zjednoczone Królestwo) ds. zdrowia i opieki społecznej (DHSC). Przewodnik dotyczący zabezpieczeń w chmurze jest oparty na 14 [zasadach zabezpieczeń w chmurze](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) opublikowanych przez narodowe Zjednoczone Królestwo cybernetycznymi Security Centre (NCSC).

Ta architektura referencyjna, przewodnik implementacji i model zagrożeń mają służyć jako podstawa dla klientów, którzy dostosowują się do określonych wymagań i nie powinny być używane w środowisku produkcyjnym bez dodatkowej konfiguracji. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

To rozwiązanie służy do wdrażania architektury referencyjnej dla aplikacji sieci Web IaaS z zapleczem SQL Server. Architektura obejmuje warstwę sieci Web, warstwę danych, Active Directory infrastrukturę, Application Gateway i Load Balancer. Maszyny wirtualne wdrożone w warstwach sieci Web i danych są skonfigurowane w zestawie dostępności, a wystąpienia SQL Server są konfigurowane w grupie dostępności zawsze w celu zapewnienia wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy Active Directory są używane do wymuszania konfiguracji zabezpieczeń i zgodności na poziomie systemu operacyjnego.

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Na platformie Azure są przechowywane trzy kopie danych w wybranym centrum wyznaczonym przez klienta w celu zapewnienia odporności. Geograficznie nadmiarowy magazyn zapewnia, że dane będą replikowane do dodatkowego centrum danych w setkach kilometrów i ponownie przechowywane jako trzy kopie w tym centrum. uniemożliwi to niekorzystne zdarzenie w podstawowym centrum dane klienta, co spowoduje utratę Data.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Azure Active Directory kontroli dostępu opartej na rolach służy do kontrolowania dostępu do wdrożonych zasobów, w tym ich kluczy w Azure Key Vault. Kondycja systemu jest monitorowana za poorednictwem Azure Security Center i Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym. Usługa Azure Application Gateway jest skonfigurowana jako Zapora w trybie zapobiegania i nie zezwala na ruch, który nie jest TLSv 1.2.

Host zarządzania bastionu zapewnia bezpieczne połączenie dla administratorów w celu uzyskania dostępu do wdrożonych zasobów. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub ExpressRoute na potrzeby zarządzania i importowania danych do podsieci architektury referencyjnej.**

![IaaS Web Application for UK NHS diagram architektury referencyjnej](images/uknhs-iaaswa-architecture.png?raw=true "IaaS Web Application for UK NHS diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Azure Virtual Machines
    - (1) Zarządzanie/bastionu (Windows Server 2016 Datacenter)
    - (2) Active Directory kontroler domeny (Windows Server 2016 Datacenter)
    - (2) SQL Server węzeł klastra (SQL Server 2017 w systemie Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Zestawy dostępności
    - (1) Active Directory kontrolery domeny
    - (1) węzły klastra SQL
    - (1) sieć Web/IIS
- Usługa Azure Virtual Network
    - (1)/16 sieci
    - (5)/24 sieci
    - (5) sieciowa Grupa zabezpieczeń
    - Magazyn Recovery Services
- Azure Application Gateway
    - (1) Zapora aplikacji sieci Web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3,0
        - Port odbiornika: 443
- Usługa Active Directory systemu Azure
- Monitor usługi Azure Cloud
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Automation
- Azure Storage
    - (7) konta magazynu geograficznie nadmiarowego

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów w tym środowisku. Host bastionu zapewnia bezpieczne połączenie z wdrożonymi zasobami przez umożliwienie zdalnego ruchu z publicznych adresów IP na bezpiecznej liście. Aby zezwolić na ruch pulpitu zdalnego (RDP), źródło ruchu musi być zdefiniowane w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako przyłączony do domeny Host bastionu z następującymi konfiguracjami:
-   [Rozszerzenie chroniące przed złośliwym kodem](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Diagnostyka Azure rozszerzenie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) przy użyciu Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) w celu ograniczenia użycia zasobów maszyny wirtualnej, gdy nie są używane
-   Włączono funkcję [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) , dzięki czemu poświadczenia i inne wpisy tajne są uruchamiane w chronionym środowisku, które jest odizolowane od działającego systemu operacyjnego

### <a name="virtual-network"></a>Sieć wirtualna

Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: to rozwiązanie służy do wdrażania zasobów w architekturze z oddzielną podsiecią sieci Web, podsiecią bazy danych, podsiecią Active Directory i podsiecią zarządzania wewnątrz sieci wirtualnej. Podsieci są logicznie oddzielone przez reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch między podsieciami tylko do tych, które są niezbędne dla funkcji systemu i zarządzania.

Zapoznaj się z konfiguracją [sieciowych grup zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożonych z tym rozwiązaniem. Organizacje mogą konfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej za pomocą [tej dokumentacji](../../virtual-network/virtual-network-vnet-plan-design-arm.md) jako przewodnika.

Każda podsieć ma dedykowaną sieciową grupę zabezpieczeń:
- 1 sieciowa Grupa zabezpieczeń dla Application Gateway (LBNSG)
- 1 sieciowa Grupa zabezpieczeń dla hosta bastionu (MGTNSG)
- 1 sieciowa Grupa zabezpieczeń dla kontrolerów domeny podstawowej i zapasowej (ADNSG)
- 1 sieciowa Grupa zabezpieczeń dla serwerów SQL i monitor chmury (SQLNSG)
- 1 sieciowa Grupa zabezpieczeń dla warstwy sieci Web (WEBNSG)

### <a name="data-in-transit"></a>Dane przesyłane
Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Ponadto wszystkie transakcje do usługi Azure Storage za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md). Pomaga to w ochronie i ochronie danych w celu obsługi zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez NHS Digital.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) wykorzystuje funkcję BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: w wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:

- [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
- Azure SQL Database jest skonfigurowany do korzystania z funkcji [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), która wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w czasie rzeczywistym. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do użytkowników nieuprzywilejowanych lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to identyfikować i ograniczać dostęp do danych w taki sposób, aby nie zamykał bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają możliwości zarządzania dostępem do danych w środowisku platformy Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa&#39;zarządzania katalogiem i tożsamościami opartymi na chmurze z wieloma dzierżawcami firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do Azure SQL Database.
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pozwala klientom zminimalizować liczbę użytkowników, którzy mają dostęp do określonych informacji. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach,&#39;które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji&#39;i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.


### <a name="security"></a>Zabezpieczenia

**Zarządzanie wpisami tajnymi**: rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić i uzyskiwać dostęp do tych danych:

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza jest sprzętowym modułem zabezpieczeń chronionym 2048-bitowym kluczem RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.
- Rozwiązanie jest zintegrowane z Azure Key Vault zarządzania dyskami maszyny wirtualnej IaaS — kluczami szyfrowania i wpisami tajnymi.

**Zarządzanie poprawkami**: maszyny wirtualne z systemem Windows wdrożone w ramach tej architektury referencyjnej są domyślnie skonfigurowane do odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również usługę [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , za pomocą której można tworzyć zaktualizowane wdrożenia w celu zastosowania poprawek do maszyn wirtualnych w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [firma Microsoft chroniąca](https://docs.microsoft.com/azure/security/fundamentals/antimalware) przed złośliwym kodem Virtual Machines zapewnia ochronę w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie, z konfigurowalnymi alertami w przypadku znanych złośliwych lub niechciane oprogramowanie próbuje zainstalować lub uruchomić na chronionych maszynach wirtualnych.

**Azure Security Center**: w przypadku [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

Ponadto ta architektura referencyjna wykorzystuje [ocenę luk w zabezpieczeniach](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w Azure Security Center. Po skonfigurowaniu Agent partnera (np. Qualys) zgłasza luki w zabezpieczeniach do platformy zarządzania partnera. Z kolei Platforma zarządzania przez partnera zapewnia informacje dotyczące luk i monitorowania kondycji z powrotem do Azure Security Center, dzięki czemu klienci mogą szybko identyfikować zagrożone maszyny wirtualne.

**Azure Application Gateway**: architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu usługi Azure Application Gateway z skonfigurowaną zaporą aplikacji sieci Web, a zestaw reguł OWASP jest włączony. Dodatkowe możliwości obejmują:

- [Kompleksowa — SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](../../application-gateway/create-ssl-portal.md)
- Wyłącz [protokół TLS w wersji 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](../../application-gateway/waf-overview.md) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z zestawem reguł OWASP 3,0
- Włącz [rejestrowanie diagnostyczne](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="business-continuity"></a>Ciągłość działania

**Wysoka dostępność**: rozwiązanie wdraża wszystkie maszyny wirtualne w [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone w wielu izolowanych klastrach sprzętowych w celu zwiększenia dostępności. Co najmniej jedna maszyna wirtualna jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, które spełnia warunki umowy SLA na 99,95%.

**Magazyn Recovery Services**: [Magazyn Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowych i chroni wszystkie konfiguracje Virtual Machines platformy Azure w tej architekturze. W przypadku magazynu Recovery Services klienci mogą przywracać pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, co umożliwia szybsze przywracanie.

**Monitor chmury**: [monitor chmury](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) jest typem monitora kworum klastra trybu failover w systemie Windows Server 2016, który korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, taki jak każdy inny monitor kworum, otrzymuje głos i może uczestniczyć w obliczeniach kworum, ale korzysta ze standardowego publicznie dostępnego Blob Storage platformy Azure. Eliminuje to dodatkowe obciążenie pracą maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](../../azure-monitor/platform/resource-logs-overview.md) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Dzienniki Azure monitor**: te dzienniki są konsolidowane w [dziennikach Azure monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowania pulpitów nawigacyjnych. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych, dzięki czemu wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): rozwiązanie Active Directory Health Check ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): rozwiązanie sprawdzania kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i oferuje klientom z priorytetową listą zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów oraz ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): rozwiązanie Activity Log Analytics pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Azure SQL Database. Rozwiązanie [Change Tracking](../../automation/change-tracking.md) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/uknhs-iaaswa-tm) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![IaaS Web Application for UK NHS model zagrożeń](images/uknhs-iaaswa-threat-model.png?raw=true "IaaS Web Application for UK NHS model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności

W [macierzy strategia zabezpieczeń i zgodności z przepisami platformy Azure – UK NHS — Wielka Brytania](https://aka.ms/uknhs-crm) zawiera wszystkie wymagania NHS. Ta macierz zawiera szczegółowe informacje o tym, czy implementacja każdej z tych zasad jest odpowiedzialna za firmę Microsoft, klienta, czy współdzielona między nimi.

[Macierz implementacji aplikacji sieci Web w strategia zabezpieczeń i zgodności z przepisami platformy Azure Wielkiej Brytanii NHS IaaS](https://aka.ms/uknhs-iaaswa-cim) zawiera informacje o tym, które wymagania NHS z Wielkiej Brytanii są rozwiązywane przez architekturę aplikacji sieci Web IaaS, łącznie z szczegółowymi opisami tego, jak Implementacja spełnia wymagania każdej z tych zasad.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web IaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne &quot;tunnel &quot; informacji w ramach zaszyfrowanego połączenia między&#39;siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta&#39;, dane nie są przesyłane przez Internet i w związku z tym nie są dostępne.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
- Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
- Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
- Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
- Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
- Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
