---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla FFIEC
description: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla FFIEC
services: security
author: meladie
ms.assetid: 8577e982-8bc0-4817-a16e-adf2ffefc6f5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 10d13e7dd145feff286b8dd58fa1bc657961e8c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585798"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-ffiec-financial-services"></a>Zabezpieczenia platformy Azure i zgodności planu: Aplikacja sieci Web IaaS dla usług finansowych FFIEC

## <a name="overview"></a>Omówienie

Tych samych zabezpieczeń platformy Azure i zgodności planu znajdują się wskazówki dotyczące wdrażania infrastruktury jako usługi (IaaS) środowiska odpowiednie do zbierania, przechowywania i pobierania danych finansowych prowadzące działalność według federalnych instytucji finansowej Badanie Council (FFIEC).

Ta architektura referencyjna przewodnik wdrażania i model zagrożeń Podaj foundation umożliwiające klientom zachowanie zgodności z wymaganiami FFIEC. To rozwiązanie zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z FFIEC, jednak to rozwiązanie nie jest używane jako — jest w środowisku produkcyjnym, ponieważ wymagana jest dodatkowa konfiguracja.

Osiągnięcie zgodności ze standardem FFIEC wymaga, że kwalifikowaną audytorów certyfikować rozwiązania klienta produkcyjnego. Inspekcje są nadzorowane przez ekspertów z agencji elementu członkowskiego FFIEC firmy, włącznie z Radą agencje federalne rezerwy systemu (FRB), Federal złożenia ubezpieczeniowe Corporation (FDIC), krajowych administracji Credit Union (NCUA), Urząd Comptroller Waluty (OCC) i Biuro finansowych ochrony konsumenta (CFPB). Te ekspertami oświadczam, że inspekcje odbywa się przez ekspertów, którzy obsługują niezależność od instytucji inspekcji. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

Tych samych zabezpieczeń platformy Azure i zgodności planu wdraża architektury referencyjnej dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura obejmuje warstwy internetowej, warstwy danych, usługi Active Directory infrastruktury, usługa Application Gateway i modułu równoważenia obciążenia. Maszyny wirtualne wdrożone w warstwach sieć web i danych są konfigurowane w zestawie dostępności, a wystąpienia programu SQL Server powinny być skonfigurowane w zawsze włączonej grupy dostępności wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego.

Całe rozwiązanie jest oparte na usłudze Azure Storage, którego klienci skonfigurować w witrynie Azure portal. Usługa Azure Storage szyfruje wszystkie dane przy użyciu szyfrowania usługi Storage, aby zachować poufność danych magazynowanych. Geograficzne magazyn nadmiarowy gwarantuje danych replikowana do pomocniczego centrum danych setki mil natychmiast i ponownie przechowywane jako trzy kopie w ramach tego centrum danych, uniemożliwiając zdarzenie niepożądane, u klienta podstawowe centrum danych co spowoduje utratę dane.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach jest używana do kontrolowania dostępu do wdrożonych zasobów, w tym do kluczy w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego.

Zarządzanie hostem bastionu zapewnia bezpieczne połączenie, Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Aplikacja internetowa IaaS dla FFIEC referencyjny diagram architektury](images/ffiec-iaaswa-architecture.png "aplikacja internetowa IaaS dla FFIEC referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegóły architektura wdrożenia znajdują się w [architektura wdrożenia](#deployment-architecture) sekcji.

- Zestawy dostępności
    - (1) kontrolery domeny w usłudze active Directory
    - (1) węzły klastra SQL
    - (1) w sieci web/IIS
- Usługa Azure Active Directory
- Azure Application Gateway
    - (1) zapora aplikacji sieci web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Port odbiornika: 443
- W usłudze Azure Key Vault
- Azure Load Balancer
- Usługa Azure Monitor (Dzienniki)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
    - (7) konta dla magazynu geograficznie nadmiarowego
- Azure Virtual Machines
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
    - (2) usługi active Directory kontrolera domeny (Windows Server 2016 Datacenter)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/IIS (system Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 Network
    - (5) /24 Networks
    - (5) grupy zabezpieczeń sieci
- Monitor w chmurze
- Magazyn usługi Recovery Services

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od system operacyjny

### <a name="virtual-network"></a>Sieć wirtualna

Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie służy do wdrażania zasobów w architekturę z podsieci oddzielnych sieci web, bazy danych podsieci, podsieci usługi Active Directory i podsieci zarządzania wewnątrz sieci wirtualnej. Podsieci są logicznie oddzielone reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch pomiędzy podsieciami, aby tylko to niezbędne do systemu oraz funkcji zarządzania.

Zobacz konfigurację [sieciowe grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone za pomocą tego rozwiązania. Organizacje można skonfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako wskazówki.

Każdej z tych podsieci ma dedykowanej grupy zabezpieczeń sieci:
- 1 sieciowe grupy zabezpieczeń dla bramy aplikacji (LBNSG)
- 1 sieciowe grupy zabezpieczeń dla hostem bastionu (MGTNSG)
- 1 sieciowe grupy zabezpieczeń dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 sieciowe grupy zabezpieczeń dla serwerów SQL i monitor w chmurze (SQLNSG)
- 1 sieciowe grupy zabezpieczeń dla warstwy sieci web (WEBNSG)

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Ponadto wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występować za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez FFIEC wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Azure SQL Database**: Wystąpienie usługi Azure SQL Database wykorzystuje następujące środki bezpieczeństwa bazy danych:

- [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
- Usługa Azure SQL Database jest skonfigurowany do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Pomaga to identyfikować i Ogranicz dostęp do danych w taki sposób, że nie istnieje w bazie danych za pośrednictwem przed nieautoryzowanym dostępem. Klienci są zobowiązani do dostosowywania dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:

- [Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsoft&#39;s wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami usługi zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do usługi Azure SQL Database.
- Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast wszystkie uprawnienia użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji. Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach wpływających na organizacji&#39;tożsamości s umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z organizacji&#39;tożsamości s i bada podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.


### <a name="security"></a>Bezpieczeństwo

**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić i uzyskać dostęp do takich danych:

- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest HSM chronione 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.
- Rozwiązanie jest zintegrowana z usługą Azure Key Vault do zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków maszyn wirtualnych IaaS.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania, można skonfigurować alerty, gdy znany złośliwego lub niechcianego oprogramowania próbuje zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Usługa Azure Security Center**: Za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

**Usługa Azure Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu usługi Azure Application Gateway przy użyciu zapory aplikacji sieci web, skonfigurowane i włączone zestaw reguł OWASP. Dodatkowe funkcje obejmują:

- [Koniec na końcu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia także system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: Wszystkie maszyny wirtualne na wdrożeniu rozwiązania [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone między wieloma izolowanymi klastrami sprzętowymi Aby zwiększyć dostępność. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usług Recovery Services**: [Magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klientów można przywrócić pliki i foldery z maszyny wirtualnej IaaS bez przywracania całej maszyny wirtualnej, umożliwiając skraca czas ich przywracania.

**Monitor w chmurze**: [Monitor w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu Failover w systemie Windows Server 2016, która korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, takich jak każdy inny monitor kworum ma głos i można uczestniczyć w obliczeniach kworum, ale używa standardowych publicznej usługi Azure Blob Storage. Pozwala to wyeliminować koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania.

**Dzienniki platformy Azure Monitor**: Te dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych w obszarach roboczych usługi Log Analytics, która zezwala na wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z dzienników usługi Azure Monitor, dzięki czemu klienci mogą korzystać z zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Ocena usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi Azure SQL Database. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w swoich zasobów platformy Azure.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/ffiec-iaaswa-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Aplikacja internetowa IaaS modelu zagrożeń FFIEC](images/ffiec-iaaswa-threat-model.png "aplikacja internetowa IaaS dla FFIEC model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i zgodności planu — macierz odpowiedzialności klienta FFIEC](https://aka.ms/ffiec-crm) Wyświetla listę wszystkich celów zabezpieczeń wymagane przez FFIEC. Ta macierz szczegółowe informacje, czy odpowiedzialność firmy Microsoft, klienta i wykonania każdego celu lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu — FFIEC IaaS Web Application implementacji Matrix](https://aka.ms/ffiec-iaaswa-cim) udostępnia informacje, na które FFIEC wymagania są rozwiązywane przez IaaS architektury aplikacji sieci web, w tym szczegółowe opisy jak wdrożenia spełnia wymagania każdego celu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web IaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się przez Internet oraz umożliwia klientom bezpieczne &quot;tunelu&quot; informacji o wewnątrz zaszyfrowanych powiązanie klienta&#39;s siecią i platformą Azure. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta&#39;s dostawcy usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, do niego.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
