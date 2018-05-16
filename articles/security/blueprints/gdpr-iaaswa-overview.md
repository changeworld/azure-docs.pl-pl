---
title: Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web IaaS dla GDPR
description: Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web IaaS dla GDPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Zabezpieczeń platformy Azure i plan zgodności — aplikacji sieci Web IaaS dla GDPR

## <a name="overview"></a>Przegląd
Ogólne rozporządzenia ochrony danych (GDPR) zawiera wiele wymagania w zakresie zbierania, przechowywania i przy użyciu informacji osobistych, w tym jak organizacje zidentyfikować i zabezpieczania danych osobowych, uwzględnienia wymogów dotyczących przezroczystości, wykrywania i raportu naruszenia danych osobowych i personel prywatności pociągu i innym pracownikom. GDPR zapewnia większą kontrolę nad jego danych osobowych użytkowników indywidualnych i nakłada wiele nowych obowiązków w organizacji, które zbierania, obsługiwać lub analizowania danych osobowych. Nowe zasady w organizacji, które oferują towarów nakłada GDPR i usług do osób w Unii Europejskiej (UE), lub że zbieranie i analizowanie danych powiązane mieszkańców Unii Europejskiej. Niezależnie od tego, w którym znajduje się organizacja stosuje się GDPR.

Zaprojektowana Azure z branży środków bezpieczeństwa i zasady zachowania poufności informacji w celu zapewnienia ochrony danych w chmurze, łącznie z kategoriami danych osobowych identyfikowane przez GDPR przez firmę Microsoft. Firmy Microsoft [warunków umowy](http://aka.ms/Online-Services-Terms) zatwierdzić Microsoft wymagania procesorów.

Ten Azure zabezpieczeń i zgodności plan zawiera wskazówki dotyczące wdrażania infrastruktury jako usługi (IaaS) środowiska odpowiednie dla prostej aplikacji sieci web skierowane do Internetu. To rozwiązanie przedstawia sposób, w którym klienci mogą spełnić szczególne wymagania zabezpieczeń i zgodności GDPR i stanowi podstawę do tworzenia i konfigurowania ich własnych rozwiązań aplikacji sieci web IaaS na platformie Azure. Klienci może wykorzystywać o architekturze wzorcowej i wykonać firmy Microsoft [czterech kroków procesu](https://aka.ms/gdprebook) w podróży GDPR zgodności:
1. Odnajdywanie: Zidentyfikować istnieje które dane osobowe oraz jej lokalizację.
2. Zarządzanie: Regulują sposób osobistych danych jest używany i dostępny.
3. Ochrona: Ustanowić środki zabezpieczające zapobiec, wykrywanie i reagowanie na luki w zabezpieczeniach i naruszeń danych.
4. Raport: Przechowywania dokumentacji wymagane i zarządzanie żądaniami danych i naruszyć powiadomienia.

To architektura referencyjna, skojarzone z dokumentacją i modelu zagrożeń mają służyć jako podstawę do klientów dostosować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Pamiętaj o następujących kwestiach:
- Architektura zapewnia linii bazowej, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z GDPR.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
To rozwiązanie wdraża architektura referencyjna dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura zawiera warstwa sieci web, warstwy danych, usługi Active Directory infrastruktury, bramy aplikacji oraz usługi równoważenia obciążenia. Maszyny wirtualne wdrażane warstwy web i danych są skonfigurowane w zestawie dostępności i wystąpień programu SQL Server są konfigurowane w zawsze włączonej grupy dostępności, wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania konfiguracji zgodności i zabezpieczeń na poziomie systemu operacyjnego. Host bastionu zarządzania zapewnia bezpieczne połączenie, Administratorzy mogą wdrożyć dostęp do zasobów. **Azure zaleca się Konfigurowanie połączenia sieci VPN i ExpressRoute do importowania danych i zarządzania w podsieci architektura odwołania.**

![IaaS komputerze sieci Web dla diagram architektury odwołanie GDPR](images/gdpr-iaaswa-architecture.png?raw=true "IaaS komputerze sieci Web dla diagram architektury GDPR odwołania")

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrażania znajdują się w [wdrożenia architecture](#deployment-architecture) sekcji.

- Azure Virtual Machines
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
    - (2) usługi active Directory kontrolera domeny (z systemem Windows Server Datacenter 2016)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/usług IIS (Windows Server 2016 Datacenter)
- Zestawy dostępności
    - (1) kontrolery domeny active Directory
    - (1) węzły klastra SQL
    - (1) w sieci web/usług IIS
- Azure Virtual Network
    - ((1) / 16 do / sieci wirtualnych
    - podsieci o prefiksie/24 (5)
    - Ustawienia DNS są skonfigurowane do obu kontrolerów domeny
- Azure Load Balancer
- Azure Application Gateway
    - (1) brama aplikacji zapory aplikacji sieci Web jest włączona
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - odbiornik: port 443
- Azure Storage
    - (7) konta magazynu geograficznie nadmiarowego
- Monitor chmury
- Magazyn usługi Recovery Services
- W usłudze Azure Key Vault
- Usługa Azure Active Directory (AAD)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura wdrażania
Poniższa sekcja zawiera szczegóły dotyczące wdrażania i wdrażania elementów.

**Host bastionu**: host bastionu jest pojedynczy punkt wejścia, który umożliwia użytkownikom dostęp do wdrożonych zasobów, w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów, zezwalając tylko ruchu zdalnego z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na zdalny pulpitu ruch (RDP), źródło ruchu musi być zdefiniowana w grupie zabezpieczeń sieci (NSG).

To rozwiązanie tworzy maszynę wirtualną jako hosta domeny bastionu z następujących konfiguracji:
-   [Rozszerzenie ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie pakietu OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie Diagnostyka Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasad automatyczne zamykanie](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender poświadczeń Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączane poświadczeń i innych informacji poufnych uruchamiane w chronionym środowisku, która jest odizolowana od systemem operacyjnym

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej z 10.200.0.0/16 przestrzeni adresowej.

**Sieciowe grupy zabezpieczeń**: to rozwiązanie wdraża zasobów w architekturze podsieci WWW, podsieci bazy danych, podsieci usługi Active Directory i zarządzania podsieci w sieci wirtualnej. Podsieci są logicznie oddzielone zastosować do poszczególnych podsieci, aby ograniczyć ruch między podsieciami, aby tylko to konieczne do systemu oraz funkcji zarządzania reguły grupy zabezpieczeń sieci.

Zobacz konfiguracji [sieciowej grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone w tym rozwiązaniu. Organizacje można skonfigurować grupy zabezpieczeń sieci, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako przewodnika.

Każdą z tych podsieci ma dedykowany sieciowej grupy zabezpieczeń (NSG):
- 1 NSG dla bramy aplikacji (LBNSG)
- 1 NSG dla hosta bastionu (MGTNSG)
- 1 NSG dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 NSG serwerów SQL i monitora chmury (SQLNSG)
- 1 NSG dla warstwy sieci web (WEBNSG)

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja z centrach danych platformy Azure i domyślnie. Ponadto wszystkie transakcje do magazynu Azure za pośrednictwem portalu Azure występować za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane przechowywane przez kilka miar, takimi jak szyfrowanie i inspekcja bazy danych.

**Magazyn Azure**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych osobowych wesprzeć zobowiązań zabezpieczeń organizacji i zdefiniowanych przez GDPR wymagań dotyczących zgodności.

**Szyfrowanie dysków Azure**: szyfrowania dysków Azure umożliwia szyfrowane dyski maszyn wirtualnych IaaS systemu Windows. [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

**SQL Server**: wystąpienie programu SQL Server używa następujących środków zabezpieczeń bazy danych:
-   [AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania konta magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i Zatrzymaj plików dzienników transakcji do ochrony informacji w. Funkcji TDE zawiera gwarantują, że przechowywane dane osobowe nie zostało poddane nieautoryzowanego dostępu.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwić wszystkie dostęp do serwerów baz danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, podając alertów zabezpieczeń bazy danych podejrzanych działań, potencjalnych luk w zabezpieczeniach, ataki i nietypowych bazy danych programu access wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że wrażliwe dane osobowe nigdy nie jest wyświetlana jako zwykłego tekstu w systemie bazy danych. Po włączeniu szyfrowania danych, tylko aplikacje klienckie lub serwery aplikacji dostęp do kluczy można uzyskać dostępu do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkcja może być używana zaprzestania przetwarzania podmiotów danych, który zezwala użytkownikom na dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczać dane jako "Wycofany", do obsługi aplikacji logiki, aby zapobiec Przetwarzanie skojarzone dane osobiste.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby przerwać przetwarzania.
- [SQL bazy danych dynamicznych danych maskowania (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnieniem poufnych danych osobowych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. DDM można automatycznie wykryć potencjalnie poufnych danych i zaproponuje odpowiednie maski ma zostać zastosowany. Dzięki temu identyfikację danych osobowych kwalifikujących się do ochrony GDPR i zmniejszenia dostępu w taki sposób, że nie istnieje w bazie danych za pośrednictwem nieautoryzowanego dostępu. **Uwaga: Użytkownicy będą musieli ustawienia DDM stosować się do schematu bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych osobowych w środowisku platformy Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkownicy uzyskujący dostęp do programu SQL Server.
-   Aby aplikacja uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi AAD do uwierzytelniania aplikacji z programem SQL Server. Aby uzyskać więcej informacji, zobacz temat jak [chronią poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom definiowanie uprawnień szczegółowych dostępu, aby udzielić tylko takiego dostępu, użytkownicy muszą wykonać swoje zadania. Zamiast nadanie co nieograniczonych uprawnień dla zasobów platformy Azure, Administratorzy mogą umożliwić tylko pewne akcje do uzyskiwania dostępu do danych osobowych. Subskrypcja dostęp jest ograniczony do administratora subskrypcji.
- [Zarządzanie tożsamościami uprzywilejowanymi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych zasobów.  Administratorzy mogą używać usługi AAD Privileged Identity Management do odnajdywania, ograniczenia i monitorowanie tożsamości uprzywilejowanych oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, w czasie dostęp administracyjny w razie potrzeby.
- [Ochronę tożsamości usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki tożsamości organizacji, konfiguruje automatyczne odpowiedzi wykryte podejrzane działania związane z tożsamości organizacji i sprawdza podejrzanych zdarzenia podjęcie odpowiednich działań, aby je rozwiązać.
- Wdrożone wystąpienie IaaS usługi Active Directory umożliwia zarządzanie tożsamości na poziomie systemu operacyjnego dla wdrożonych maszyn wirtualnych IaaS.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie kluczy tajnych**: w tym rozwiązaniu zastosowano [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczy i kluczy tajnych. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom ochrony danych osobowych i dostęp do tych danych:
- Zasady dostępu zaawansowane są konfigurowane na potrzeby.
- Zasady dostępu do magazynu kluczy są zdefiniowane z minimalne uprawnienia wymagane do kluczy i kluczy tajnych.
- Wszystkie klucze i kluczy tajnych w magazynie kluczy mają datę wygaśnięcia.
- Wszystkie klucze w magazynie kluczy chronionych przez specjalne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest kluczem RSA HSM chronione 2048-bitowego.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień przy użyciu funkcji RBAC.
- Dzienniki diagnostyczne dla usługi Key Vault są włączone w okres przechowywania dla co najmniej 365 dni.
- Dozwolonych operacji kryptograficznych dla kluczy są ograniczone do tych wymagane.
- Rozwiązanie jest zintegrowany z usługą Azure Key Vault, aby zarządzać kluczami szyfrowania dysków maszyn wirtualnych IaaS i kluczy tajnych.

**Zarządzanie poprawkami**: maszyny wirtualne systemu Windows wdrożone w ramach tej architektury odwołania są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie obejmuje również OMS [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których można utworzyć wdrożenia zaktualizowane maszynom wirtualnym poprawki w razie potrzeby.

**Ochrona przed złośliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, że pomaga zidentyfikować i usunąć wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty Jeśli znane złośliwego lub niechcianego oprogramowania próbuje zainstalować lub uruchomić na chronionych maszyn wirtualnych.

**Alerty zabezpieczeń**: [Centrum zabezpieczeń Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) służy do monitorowania ruchu, zbierz dzienniki i analizować źródeł danych w przypadku zagrożeń. Ponadto Centrum zabezpieczeń Azure uzyskuje dostęp do istniejącej konfiguracji usług platformy Azure, konfiguracji i zalecenia usługi, aby poprawić stan zabezpieczeń i ochrony danych osobowych. Centrum zabezpieczeń Azure obejmuje [raportu analizy zagrożeń](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) każdego wykrytych zagrożeń ułatwiających zespoły odpowiedzi na zdarzenia zbadać i eliminowanie zagrożeń.

**Brama aplikacji w**: Architektura zmniejsza ryzyko luk w zabezpieczeniach z zapory aplikacji sieci web (WAF) przy użyciu bramy aplikacji i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z ruleset OWASP 3.0
- Włącz [rejestrowania diagnostyki](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Sondy kondycji niestandardowych](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center) i [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) zapewnić dodatkową ochronę i powiadomień. Centrum zabezpieczeń Azure zapewnia również system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: rozwiązanie wdraża wszystkich maszyn wirtualnych w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności upewnij się, że maszyny wirtualne rozproszone na wielu klastrów izolowanego sprzętu, aby zwiększyć dostępność. Co najmniej jednej maszyny wirtualnej jest dostępna podczas planowanego lub nieplanowanego zdarzenia konserwacji, spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usług odzyskiwania**: [magazyn usług odzyskiwania](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. W magazynie usług odzyskiwania klientów można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, umożliwiające szybsze przywracania.

**Chmury monitora**: [monitora chmury](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) typ monitora kworum klastra trybu Failover w systemie Windows Server 2016 wykorzystującego Azure jako punktu rozstrzygnięcia. Monitor chmury, takich jak inne monitora kworum, pobiera głos i mogą uczestniczyć w obliczeniach kworum, ale używa standardowych publicznie dostępny magazyn obiektów Blob Azure. Eliminuje koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

OMS zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. OMS [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasobami na platformie Azure i lokalnego środowiska.
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacji wykonywanych na zasobów w ramach subskrypcji. Dzienniki aktywności mogą ułatwić określenie inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i dzienniki bramy aplikacji dostępu i zapory.
- **Archiwizowanie dziennika**: zapisu wszystkich dzienników diagnostycznych na konto magazynu Azure scentralizowane i zaszyfrowanego dla archiwizacji. Przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, aby spełnić wymagania organizacji dotyczące przechowywania. Te dzienniki nawiązać Analiza dzienników Azure do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego.

Ponadto następujące rozwiązania OMS są dołączone jako część tej architektury:
-   [Ocena AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i udostępnia listę zalecenia dotyczące infrastruktury serwera wdrożonym z określonymi priorytetami.
-   [Ocena ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raportów dotyczących stanu złośliwego oprogramowania, zagrożeń i ochrony.
-   [Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie do automatyzacji Azure przechowuje, uruchamia i zarządza elementami runbook.
-   [Bezpieczeństwo i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcji pulpit nawigacyjny zapewnia wysokiego poziomu wgląd w stan zabezpieczeń zasobów podając metryki na domen zabezpieczeń, godne uwagi problemy wykryć, analizy zagrożeń i typowych kwerend zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL kondycji Sprawdź rozwiązanie ocenia ryzyko i kondycji serwera środowisk w regularnych odstępach czasu i priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożonym opracowuje dla klientów.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacji umożliwia zarządzanie klienta aktualizacje zabezpieczeń systemu operacyjnego, w tym stanu o dostępnych aktualizacjach i proces instalacji wymaganych aktualizacji.
-   [Agent kondycji](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): kondycja agenta rozwiązania zgłasza liczbę agentów są wdrażane i ich rozmieszczenie geograficzne, a także liczbę agentów, które odpowiadają i liczby agentów, które są przesyłanie danych operacyjnych.
-   [Azure Dzienniki aktywności](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązania analizy dziennika aktywności obsługuje Analiza dzienników Azure działania we wszystkich subskrypcji platformy Azure dla danego klienta.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): śledzenie zmian rozwiązania pozwala łatwo zidentyfikować zmiany w środowisku.

## <a name="threat-model"></a>Modelu zagrożeń

Diagram przepływu danych (DPD) dla tej architektury dokumentacja jest dostępna dla [Pobierz](https://aka.ms/gdprIaaSdfd) lub znajduje się poniżej. Ten model może ułatwić klientom punkty potencjalne ryzyko w infrastrukturze systemu, podczas wprowadzania zmian.

![IaaS komputerze sieci Web dla modelu zagrożeń GDPR](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS komputerze sieci Web dla modelu zagrożeń GDPR")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i plan zgodności - GDPR klienta odpowiedzialność macierzy](https://aka.ms/gdprCRM) wymieniono obowiązki kontrolera i procesora dla wszystkich artykułów GDPR. Należy pamiętać, że dla usług Azure, klient zazwyczaj jest to kontroler i Microsoft zachowuje się jak procesor.

[Zabezpieczeń platformy Azure i plan zgodności - GDPR IaaS sieci Web aplikacji implementacji macierzy](https://aka.ms/gdprIaaSweb) zawiera informacje, na które GDPR artykuły dotyczą architektury IaaS aplikacji sieci Web, w tym szczegółowe opisy sposób wykonania spełnia wymagania dotyczące każdego wymienionego artykułu.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieć VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpiecznego nawiązać połączenie z zasoby wdrożone w ramach tej architektury odwołanie IaaS aplikacji sieci Web. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Implementując bezpieczny tunel sieci VPN platformy Azure, można utworzyć wirtualnego prywatnej połączenie między siecią lokalną i sieci wirtualnej platformy Azure. To połączenie odbywa się przez Internet i pozwala bezpiecznie "tunnel" informacji wewnątrz zaszyfrowanych łącza między siecią a Azure klienta. Sieci VPN typu lokacja-lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. [Trybu tunelowania IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizm szyfrowania.

Ponieważ ruchu w tunelu VPN przechodzą przez Internet, korzystając z sieci VPN lokacja lokacja, firma Microsoft oferuje opcji inny, jeszcze bardziej bezpieczne połączenia. Usługa Azure ExpressRoute jest dedykowanych sieci WAN łącza między Azure i lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Jako połączeń ExpressRoute nie przechodzą w Internecie, te połączenia oferują więcej niezawodności, szybkości szybsze niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu. Ponadto jest bezpośrednie połączenie klienta telekomunikacyjnych dostawcy, dlatego dane nie są przesyłane w Internecie i w związku z tym nie jest uwidaczniana do niego.

Najlepsze rozwiązania dotyczące wdrażania sieci hybrydowe bezpiecznego, która rozszerza sieci lokalnej na platformie Azure są [dostępne](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.  
- Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.  
- Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.  
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  
- Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
- Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
