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
ms.openlocfilehash: f19478b29fd96cd2bd3ed5e0e994595d7b00edf3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391984"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla SP NIST 800-171

## <a name="overview"></a>Przegląd
[Publikacja specjalne NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) zawiera wytyczne dotyczące ochrony informacji unclassified kontrolowanego (CUI), która znajduje się w organizacji i systemów informacyjnych nonfederal. SP NIST 800-171 ustanawia 14 rodzin wymagań dotyczących ochrony poufności CUI zabezpieczeń.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wytyczne pomagające klientom wdrażanie architektury aplikacji sieci web na platformie Azure, która implementuje podzbiór NIST SP 800-171 kontrolki. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać zabezpieczeń i zgodności z wymaganiami i służy jako podstawa do tworzenia i konfigurowania ich własnych aplikacji sieci web na platformie Azure.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do ich określonych wymagań i nie powinien być używany jako — w środowisku produkcyjnym. Wdrażania tej architektury bez żadnych modyfikacji jest za mała, aby całkowicie wymagań SP NIST 800-171. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
Tych samych zabezpieczeń platformy Azure i zgodności planu wdraża architektury referencyjnej dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura obejmuje warstwy internetowej, warstwy danych, usługi Active Directory infrastruktury, usługa Application Gateway i modułu równoważenia obciążenia. Maszyny wirtualne wdrożone w warstwach sieć web i danych są konfigurowane w zestawie dostępności, a wystąpienia programu SQL Server powinny być skonfigurowane w zawsze włączonej grupy dostępności wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego.

Całe rozwiązanie jest oparte na usłudze Azure Storage, którego klienci skonfigurować w witrynie Azure portal. Usługa Azure Storage szyfruje wszystkie dane przy użyciu szyfrowania usługi Storage, aby zachować poufność danych magazynowanych. Geograficzne magazyn nadmiarowy gwarantuje, że zdarzenie niepożądane, u klienta podstawowe centrum danych nie spowoduje utraty danych, zgodnie z drugą kopię będą przechowywane w oddzielnej lokalizacji setki mil natychmiast.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Kontroli dostępu opartej na rolach w usłudze Azure Active Directory jest używane do kontrolowania dostępu do zasobów i wdrażać w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego.

Zarządzanie hostem bastionu zapewnia bezpieczne połączenie, Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**


![Aplikacja internetowa IaaS dla SP NIST 800-171 referencyjny diagram architektury](images/nist171-iaaswa-architecture.png "aplikacja internetowa IaaS dla SP NIST 800-171 referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Dalsze szczegółowe informacje na temat [architektura wdrożenia](#deployment-architecture) sekcji.

- Azure Virtual Machines
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
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Monitor w chmurze
- Magazyn usługi Recovery Services

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Host bastionu**: hostem bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od system operacyjny

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: to rozwiązanie służy do wdrażania zasobów w architekturę z podsieci oddzielnych sieci web, bazy danych podsieci, podsieci usługi Active Directory i podsieci zarządzania wewnątrz sieci wirtualnej. Podsieci są logicznie oddzielone reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch pomiędzy podsieciami, aby tylko to niezbędne do systemu oraz funkcji zarządzania.

Zobacz konfigurację [sieciowe grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone za pomocą tego rozwiązania. Organizacje można skonfigurować grupy zabezpieczeń sieci, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako wskazówki.

Każdej z tych podsieci ma dedykowany sieciowej grupy zabezpieczeń:
- 1 sieciowej grupy zabezpieczeń dla bramy aplikacji (LBNSG)
- 1 sieciowej grupy zabezpieczeń dla hostem bastionu (MGTNSG)
- 1 sieciowej grupy zabezpieczeń dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 sieciowej grupy zabezpieczeń dla serwerów SQL i monitor w chmurze (SQLNSG)
- 1 sieciowej grupy zabezpieczeń dla warstwy internetowej (WEBNSG)

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Ponadto wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występować za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą wielu środków, takimi jak szyfrowanie i inspekcja bazy danych.

**Usługa Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez SP NIST 800-171 wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: Usługa Azure Disk Encryption jest używany do zaszyfrowanych dysków maszyny wirtualnej Windows IaaS. [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowana z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Program SQL Server**: wystąpienie programu SQL Server używa następujące środki bezpieczeństwa bazy danych:
-   [Inspekcja programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) śledzi zdarzenia bazy danych i zapisuje je do dzienników inspekcji.
-   [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, do ochrony informacji w stanie spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. To pomaga zmniejszyć dostępu w taki sposób, że poufne dane nie istnieje baza danych za pośrednictwem przed nieautoryzowanym dostępem. **Klienci są zobowiązani do dostosowywania dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:
-   [Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze Azure Active Directory, w tym użytkowników uzyskujących dostęp do programu SQL Server.
-   Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych zasobów.  Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i wsparcia podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomagają chronić dane klientów:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń typu klucza jest sprzętowego modułu zabezpieczeń chronionego 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.
- Rozwiązanie jest zintegrowana z usługą Azure Key Vault do zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków maszyn wirtualnych IaaS.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, ułatwiający identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania za pomocą konfigurowalnych alertów gdy znany złośliwego lub niechcianego oprogramowania podejmuje próbę zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Usługa Azure Security Center**: za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

Ponadto, ta architektura referencyjna korzysta z usługi [oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w usłudze Azure Security Center. Po skonfigurowaniu agent partnera (np. rozwiązania firmy Qualys) raportuje danych luki w zabezpieczeniach do platformy zarządzania partnera. Z kolei platforma zarządzania partnera zawiera luk w zabezpieczeniach i dane z powrotem do usługi Azure Security Center, monitorowania kondycji co umożliwia klientom szybko zidentyfikować maszyny wirtualne na ataki.

**Usługa Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web, skonfigurowane i włączone zestaw reguł OWASP. Dodatkowe funkcje obejmują:

- [Koniec na końcu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia także system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: rozwiązanie zostanie wdrożone wszystkich maszyn wirtualnych w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone między wieloma izolowanymi klastrami sprzętowymi Aby zwiększyć dostępność. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usługi Recovery Services**: [magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, umożliwiając skraca czas ich przywracania.

**Monitor w chmurze**: [monitora w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu Failover w systemie Windows Server 2016, która korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, takich jak każdy inny monitor kworum ma głos i można uczestniczyć w obliczeniach kworum, ale używa standardowych publicznej usługi Azure Blob Storage. Pozwala to wyeliminować koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania.

**Log Analytics**: tych dzienników i dalszych są skonsolidowane w [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych w obszarach roboczych pakietu Operations Management Suite, który zezwala na wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z usługą Log Analytics, umożliwiając klientom zapytań usługi Log Analytics umożliwia dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące usługi Log Analytics [rozwiązań do zarządzania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check rozwiązanie ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): rozwiązania SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbierać dzienniki z serwera SQL Azure. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Usługa Azure Monitor**: [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w ich systemie Azure zasoby.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/nist171-iaaswa-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Aplikacja internetowa IaaS modelu zagrożeń SP NIST 800-171](images/nist171-iaaswa-threat-model.png "aplikacja internetowa IaaS dla SP NIST 800-171 model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i zgodności planu - SP NIST 800-171 klienta odpowiedzialność macierzy](https://aka.ms/nist171-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez SP NIST 800-171. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu - macierzy implementacji kontroli aplikacji sieci Web SP NIST 800-171 IaaS](https://aka.ms/nist171-iaaswa-cim) zawiera informacje, na które SP NIST 800-171 kontrolki są rozwiązywane przez architektury aplikacji sieci web dla IaaS w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacja internetowa IaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.  
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.  
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.  
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
