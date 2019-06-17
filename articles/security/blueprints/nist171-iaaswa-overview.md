---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla SP NIST 800-171
description: Zabezpieczenia platformy Azure i zgodności planu - SP NIST aplikacji sieci Web IaaS 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610284"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla SP NIST 800-171

## <a name="overview"></a>Omówienie
[Publikacja specjalne NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony informacji unclassified kontrolowanego (CUI), która znajduje się w organizacji i systemów informacyjnych nonfederal. SP NIST 800-171 ustanawia 14 rodzin wymagań dotyczących ochrony poufności CUI zabezpieczeń.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wytyczne pomagające klientom wdrażanie architektury aplikacji sieci web na platformie Azure, która implementuje podzbiór NIST SP 800-171 kontrolki. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać zabezpieczeń i zgodności z wymaganiami. Służy również jako podstawa do tworzenia i konfigurowania ich własnych aplikacji sieci web na platformie Azure.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do swoich wymagań. Nie należy ich używać jako — w środowisku produkcyjnym. Wdrażania tej architektury bez żadnych modyfikacji jest za mała, aby całkowicie wymagań SP NIST 800-171. Klienci są zobowiązani do przeprowadzania odpowiednie ustawienia zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury. Wymagania mogą się różnić w oparciu o szczegóły implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
Tych samych zabezpieczeń platformy Azure i zgodności planu wdraża architektury referencyjnej dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura obejmuje warstwy internetowej, warstwy danych, usługi Active Directory infrastruktury, Azure Application Gateway i Azure Load Balancer. Wdrożone maszyny wirtualne (VM) do warstwy sieci web i danych są konfigurowane w zestawie dostępności. Wystąpienia programu SQL Server są konfigurowane w zawsze włączonej grupy dostępności wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny. Zasady grupy w usłudze Active Directory Wymuszanie zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego.

Całe rozwiązanie jest oparte na usłudze Azure Storage klienci skonfigurować w witrynie Azure portal. Storage szyfruje wszystkie dane przy użyciu szyfrowania usługi Storage, aby zachować poufność danych magazynowanych. Magazyn geograficznie nadmiarowy gwarantuje, czy zdarzenie niepożądane, u klienta podstawowe centrum danych nie powoduje utraty danych. Druga kopia znajduje się w oddzielnej lokalizacji setki natychmiast mil.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Azure Active Directory (Azure AD) kontroli dostępu opartej na rolach (RBAC) umożliwia kontrolowanie dostępu do wdrożonych zasobów i w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników. Kondycja systemu jest wyświetlany na pojedynczy pulpit nawigacyjny, który jest łatwy w użyciu.

Zarządzanie hostem bastionu zapewnia bezpieczne połączenie, Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów. *Firma Microsoft zaleca, aby skonfigurować połączenie sieci VPN lub usługi Azure ExpressRoute dla importu danych i zarządzania do podsieci architektury odwołanie.*


![Aplikacja internetowa IaaS dla SP NIST 800-171 referencyjny diagram architektury](images/nist171-iaaswa-architecture.png "aplikacja internetowa IaaS dla SP NIST 800-171 referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [architektura wdrożenia](#deployment-architecture) sekcji.

- Usługa Azure Virtual Machines
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
    - (2) usługi active Directory kontrolera domeny (Windows Server 2016 Datacenter)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/IIS (system Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (((1) /16 sieci
    - sieci [5] prefiksie/24
    - (5) grupy zabezpieczeń sieci
- Zestawy dostępności
    - (1) kontrolery domeny w usłudze active Directory
    - (1) węzły klastra SQL
    - (1) w sieci web/IIS
- Azure Application Gateway
    - (1) zapora aplikacji sieci web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Port odbiornika: 443
- Usługa Azure Active Directory
- W usłudze Azure Key Vault
- Azure Load Balancer
- Usługa Azure Monitor (Dzienniki)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Monitor w chmurze
- Magazyn usługi Recovery Services

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, którego użytkownicy mogą uzyskać dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów, zezwalając tylko na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na ruch pulpitu zdalnego, źródła ruchu muszą być zdefiniowane w grupie zabezpieczeń sieci (NSG).

To rozwiązanie umożliwia utworzenie maszyny Wirtualnej jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) za pomocą usługi Key Vault.
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny Wirtualnej nie w użyciu.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) jest włączona, aby uruchomić w środowisku chronionego, która jest odizolowana poświadczeń i innych wpisów tajnych z systemem operacyjnym.

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie służy do wdrażania zasobów w architekturę z oddzielne podsieci dla sieci web, bazy danych, usługi Active Directory i zarządzania w sieci wirtualnej. Podsieci są logicznie oddzielone reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci. Reguły ograniczają ruch pomiędzy podsieciami, aby tylko to niezbędne do systemu oraz funkcji zarządzania.

Zobacz konfigurację [sieciowych grup zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone za pomocą tego rozwiązania. Organizacje można skonfigurować sieciowe grupy zabezpieczeń, edytując poprzedni plik przy użyciu [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako wskazówki.

Każdej z tych podsieci ma dedykowany sieciowej grupy zabezpieczeń:
- Jednej sieciowej grupy zabezpieczeń w usłudze Application Gateway (LBNSG)
- Jednej sieciowej grupy zabezpieczeń dla hostem bastionu (MGTNSG)
- Jednej sieciowej grupy zabezpieczeń dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- Jednej sieciowej grupy zabezpieczeń dla serwerów SQL i monitor w chmurze (SQLNSG)
- Jednej sieciowej grupy zabezpieczeń dla warstwy sieci web (WEBNSG)

### <a name="data-in-transit"></a>Dane przesyłane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Ponadto wszystkie transakcje do magazynu w witrynie Azure portal występować za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą wielu miar. Środki te obejmują szyfrowania i inspekcja bazy danych.

**Azure Storage**: Aby spełnić wymagania dla zaszyfrowanych danych w spoczynku, wszystkie [magazynu](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Ta funkcja pomaga chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez SP NIST 800-171 wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: Szyfrowanie dysków jest używany do zaszyfrowanych dysków maszyn wirtualnych IaaS Windows. [Szyfrowanie dysków](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) używa funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowana z usługą Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Program SQL Server**: Wystąpienie programu SQL Server używa następujących środków bezpieczeństwa bazy danych:
-   [Inspekcja programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) śledzi zdarzenia bazy danych i zapisuje je do dzienników inspekcji.
-   [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, do ochrony informacji w stanie spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywane dane, nie został narażone na nieautoryzowany dostęp.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskać dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanym użytkownikom lub aplikacji. Można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dynamiczne maskowanie danych pomaga ograniczyć dostęp, tak, aby dane poufne nie zakończyć bazy danych za pośrednictwem przed nieautoryzowanym dostępem. *Klienci są zobowiązani do dostosowywania ustawień stosować się do ich schemat bazy danych.*

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:
-   [Usługa Azure AD](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wielodostępne oparte na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze Azure AD i obejmują użytkowników, którzy uzyskują dostęp do wystąpienia programu SQL Server.
-   Do aplikacji uwierzytelniania za pomocą usługi Azure AD. Aby uzyskać więcej informacji, zobacz instrukcje [integrować aplikacje z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Usługa Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) może służyć przez administratorów do definiowania uprawnień dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) może służyć przez klientów, aby zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych zasobów. Administratorzy mogą używać usługi Azure AD Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można również wymusić na żądanie, just-in-time dostęp administracyjny w razie.
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
- Rozwiązanie jest zintegrowana z usługą Key Vault do zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków maszyn wirtualnych IaaS.

**Zarządzanie poprawkami**: Windows maszyn wirtualnych wdrożonych w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą którego można utworzyć wdrożenia zaktualizowane maszynami wirtualnymi w razie zastosowania poprawki.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, ułatwiający identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. Klienci mogą skonfigurować alerty, które generują, gdy znany prób złośliwego lub niechcianego oprogramowania do zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Usługa Azure Security Center**: Za pomocą [usługi Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Usługa Security Center również uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Security Center korzysta z rozmaitych możliwości wykrywania powiadamia klientów o potencjalnych ataków, których platformą docelową ich środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) wygenerowaniu przypadku wystąpienia zagrożenia lub podejrzanej aktywności. Klienci mogą używać [niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) do definiowania nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Usługa Security Center zapewnia zdarzeń i alertów zabezpieczeń uporządkowanych według priorytetu. Usługa Security Center sprawia, że prostsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej zagrożeń. Odpowiedzi na zdarzenia zespołów mogą korzystać z raportów, gdy ich badaniu i usuwaniu zagrożeń.

Ta architektura referencyjna korzysta [oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) możliwości są dostępne w usłudze Security Center. Po skonfigurowaniu, agent partnera (na przykład Qualys) raportów danych luki w zabezpieczeniach do platformy zarządzania partnera. W odpowiedzi platforma zarządzania partnera dostarcza dane monitorowania o lukach w zabezpieczeniach i kondycji do usługi Security Center. Klienci mogą używać tych informacji, aby szybko identyfikować zagrożone maszyny wirtualne.

**Usługa Azure Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web, skonfigurowane i zestaw reguł OWASP, które są włączone. Dodatkowe funkcje obejmują:

- [Koniec na końcu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania).
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) zestaw reguł, OWASP 3.0.
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Niestandardowych sond kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Usługa Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Security Center zapewnia także system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Rozwiązanie zostanie wdrożone na wszystkich maszynach wirtualnych w [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone między wieloma izolowanymi klastrami sprzętowymi Aby zwiększyć dostępność. Co najmniej jedna maszyna wirtualna jest dostępna w przypadku zdarzenia planowaną lub nieplanowaną konserwacją i spełnia 99,95% umowy SLA platformy Azure.

**Magazyn usługi Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej. Ten proces przyspiesza czas ich przywracania.

**Monitor w chmurze**: [Monitor w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu failover w systemie Windows Server 2016, która korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, takich jak każdy inny monitor kworum ma głos i można uczestniczyć w obliczeniach kworum. Użyto publicznie dostępnego magazynu obiektów Blob platformy Azure w warstwie standardowa. Taki układ, który eliminuje koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki magazynu, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Użytkownicy mogą skonfigurować okres przechowywania, nawet do 730 dni, aby spełnić konkretne wymagania.

**Dzienniki platformy Azure Monitor**: Te dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu danych są zorganizowane w w oddzielnych tabelach dla każdego typu danych w obszarach roboczych usługi Log Analytics. W ten sposób wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Usługa Security Center integruje się z dziennikami usługi Azure Monitor. Klienci mogą używać zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu. Daje klientom z priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne. Zgłasza również odpowiadają liczby agentów i liczbę agentów, którzy przesłali danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Automatyzacja](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z programu SQL Server. Klienci mogą korzystać z automatyzacji [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązania, aby łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów. Organizacje mogą używać go do inspekcji, tworzyć alerty i archiwizować dane. Mogą również śledzić wywołań interfejsu API w swoich zasobów platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/nist171-iaaswa-tm) lub można znaleźć tutaj. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu wprowadzasz zmiany.

![Aplikacja internetowa IaaS modelu zagrożeń SP NIST 800-171](images/nist171-iaaswa-threat-model.png "aplikacja internetowa IaaS dla SP NIST 800-171 model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i zgodności planu - SP NIST 800-171 klienta odpowiedzialność macierzy](https://aka.ms/nist171-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez SP NIST 800-171. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - macierzy implementacji kontroli aplikacji sieci Web SP NIST 800-171 IaaS](https://aka.ms/nist171-iaaswa-cim) zawiera informacje, na które SP NIST 800-171 kontrolki są rozwiązywane przez architektury aplikacji sieci web IaaS. Zawiera szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana do bezpieczne nawiązywanie połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web IaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu. Klienci mogą używać tego połączenia o bezpiecznie "tuneli" wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu sieci VPN jest przesyłane przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje inną opcję połączenia jeszcze bardziej bezpieczne. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Połączenia ExpressRoute łączyć się bezpośrednio z dostawcą usług telekomunikacyjnych klienta. W rezultacie dane nie podróżują, przez Internet i nie jest widoczne. Połączenia te oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia. 

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą. 
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań. 
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego. 
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta. 
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
