---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla rozporządzenia RODO
description: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla rozporządzenia RODO
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: f5f04fdaa12c716b21bafcc0abccc719428fe791
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242079"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla rozporządzenia RODO

## <a name="overview"></a>Przegląd
Ogólne rozporządzenie o ochronie danych (RODO) zawiera wiele wymagań dotyczących zbierania, przechowywania i przy użyciu informacji osobistych, w tym jak organizacjom identyfikowanie i zabezpieczanie danych osobowych, uwzględnić wymagania dotyczące przezroczystości, wykrywania i raportu naruszenia danych osobowych i szkolenie zachowania personel i innych pracowników. RODO zapewnia większą kontrolę nad ich danych osobowych użytkowników indywidualnych i nakłada wiele nowych zobowiązań w organizacji, które zbierania, obsługiwać lub analizować dane osobowe. Rozporządzenie GDPR nakłada nowe zasady dotyczące organizacji oferujących towary i usługi osobom w Unii Europejskiej (UE) lub że zbierać i analizować dane związane z osobami mieszkającymi w UE. Niezależnie od tego, w którym znajduje się organizacja stosuje się GDPR.

Firma Microsoft zaprojektowała platformy Azure za pomocą wiodących w branży środki bezpieczeństwa i zasady zachowania poufności informacji w celu ochrony danych w chmurze, w tym kategorie danych osobowych identyfikowane przez rozporządzenie GDPR. Firmy Microsoft [warunków umowy](https://aka.ms/Online-Services-Terms) zatwierdzić Microsoft wymagania procesorów.

Tych samych zabezpieczeń platformy Azure i zgodności plan zawiera wskazówki dotyczące wdrażania infrastruktury jako usługi (IaaS) środowiska odpowiednie dla prostej aplikacji sieci web dostępnym z Internetu. To rozwiązanie przedstawia sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności rozporządzenia rodo i służy jako podstawa do tworzenia i konfigurowania ich własnych rozwiązań aplikacji sieci web IaaS na platformie Azure. Klienci mogą korzystać z tej architektury referencyjnej i postępuj zgodnie z firmy Microsoft [czterech kroków procesu](https://aka.ms/gdprebook) w podróży do zgodności z rozporządzeniem GDPR:
1. Odnajdywanie: Zidentyfikować dane osobowe, które istnieje, oraz jej lokalizację.
2. Zarządzanie: Określają jak osobistych danych jest używane i dostępne.
3. Ochrona: Ustanowić kontrole bezpieczeństwa, aby zapobiec, wykrywanie ich i odpowiadanie na luki w zabezpieczeniach i naruszeń danych.
4. Raport: Przechowywania dokumentacji wymagane i zarządzanie żądaniami danych i naruszenia powiadomienia.

Ta architektura referencyjna skojarzonego wdrożenia przewodnik i model zagrożeń są przeznaczone do służyć jako podstawa dla klientów dostosować je do ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Pamiętaj o następujących kwestiach:
- Architektura zapewnia plan bazowy, aby ułatwić klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z RODO.
- Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
Tego rozwiązania powoduje wdrożenie architektury referencyjnej dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura obejmuje warstwy internetowej, warstwy danych, usługi Active Directory infrastruktury, usługa Application Gateway i modułu równoważenia obciążenia. Maszyny wirtualne wdrożone w warstwach sieć web i danych są konfigurowane w zestawie dostępności, a wystąpienia programu SQL Server powinny być skonfigurowane w celu zapewnienia wysokiej dostępności grupy dostępności AlwaysOn. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego. Zarządzanie hostem bastionu zapewnia bezpieczne połączenie, Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów. **Azure zaleca się skonfigurowanie połączenia sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Distributed Web IaaS do RODO referencyjny diagram architektury](images/gdpr-iaaswa-architecture.png?raw=true "IaaS Web Distributed dla rozporządzenia RODO referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegóły architektura wdrożenia znajdują się w [architektura wdrożenia](#deployment-architecture) sekcji.

- Azure Virtual Machines
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
    - (2) usługi active Directory kontrolera domeny (Windows Server 2016 Datacenter)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/IIS (system Windows Server 2016 Datacenter)
- Zestawy dostępności
    - (1) kontrolery domeny w usłudze active Directory
    - (1) węzły klastra SQL
    - (1) w sieci web/IIS
- Azure Virtual Network
    - sieci wirtualne /16 (1)
    - podsieci o prefiksie/24 w (5)
    - Ustawienia DNS są ustawione na obu kontrolerach domeny
- Azure Load Balancer
- Azure Application Gateway
    - (1) brama aplikacji zapory aplikacji sieci Web jest włączona
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Odbiornika: port 443
- Azure Storage
    - (7) konta dla magazynu geograficznie nadmiarowego
- Monitor w chmurze
- Magazyn usługi Recovery Services
- W usłudze Azure Key Vault
- Usługa Azure Active Directory (AAD)
- Azure Resource Manager
- Log Analytics
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Host bastionu**: hostem bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Aby zezwolić na ruchu (RDP) pulpitu zdalnego, źródło ruchu musi można zdefiniować w grupie zabezpieczeń sieci (NSG).

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie analizy dzienników](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od system operacyjny

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: to rozwiązanie służy do wdrażania zasobów w architekturę z podsieci oddzielnych sieci web, bazy danych podsieci, podsieci usługi Active Directory i podsieci zarządzania wewnątrz sieci wirtualnej. Podsieci są logicznie oddzielone reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch pomiędzy podsieciami, aby tylko to niezbędne do systemu oraz funkcji zarządzania.

Zobacz konfigurację [sieciowe grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone za pomocą tego rozwiązania. Organizacje można skonfigurować grupy zabezpieczeń sieci, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako wskazówki.

Każdej z tych podsieci ma dedykowany sieciową grupę zabezpieczeń (NSG):
- 1 sieciowej grupy zabezpieczeń w usłudze Application Gateway (LBNSG)
- 1 sieciowej grupy zabezpieczeń dla hostem bastionu (MGTNSG)
- 1 sieciowej grupy zabezpieczeń dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 sieciowej grupy zabezpieczeń dla serwerów SQL i monitor w chmurze (SQLNSG)
- 1 sieciowej grupy zabezpieczeń dla warstwy internetowej (WEBNSG)

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Ponadto wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występować za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą wielu środków, takimi jak szyfrowanie i inspekcja bazy danych.

**Usługa Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych osobowych w odniesieniu do bezpieczeństwa organizacji i zdefiniowane przez rozporządzenie GDPR wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: Usługa Azure Disk Encryption jest używany do zaszyfrowanych dysków maszyny wirtualnej Windows IaaS. [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowana z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Program SQL Server**: wystąpienie programu SQL Server używa następujące środki bezpieczeństwa bazy danych:
-   [Usługi AD uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
-   [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
-   Baza danych SQL jest skonfigurowana do używania [przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Funkcja TDE zapewnia pewność, że przechowywane dane osobowe nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
-   [Zawsze zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że wrażliwe dane osobowe nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkcja może służyć do zaprzestać przetwarzania, których dane dotyczą, ponieważ pozwala ono użytkownikom dodawanie właściwości niestandardowych do obiektów bazy danych i oznaczania tagami danych jako "Wycofany" na potrzeby obsługi aplikacji logiki, aby zapobiec Przetwarzanie skojarzone dane osobowe.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pozwala użytkownikom na definiowanie zasad, aby ograniczyć dostęp do danych, aby zaprzestać przetwarzania.
- [SQL Database dynamicznych danych maskowania (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych osobowych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. DDM może automatycznie odnajdywać potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Dzięki temu identyfikację danych osobowych kwalifikujących się GDPR ochrony oraz do zmniejszenia dostępu w taki sposób, że nie istnieje w bazie danych za pośrednictwem przed nieautoryzowanym dostępem. **Uwaga: Klienci będą musieli dostosować ustawienia DDM stosować się do ich schemat bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych osobowych w środowisku platformy Azure:
-   [Usługa Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usłudze AAD, w tym użytkowników uzyskujących dostęp do programu SQL Server.
-   Do aplikacji uwierzytelniania przy użyciu usługi AAD. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi AAD do uwierzytelniania aplikacji do programu SQL Server. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Usługa Azure opartej na rolach kontrola dostępu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast co nieograniczone uprawnienia dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych osobowych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługi AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych zasobów.  Administratorzy mogą używać usługi AAD Privileged Identity Management, aby odkryć, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Ochrona tożsamości usługi AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykryje potencjalne luki mające wpływ na tożsamości w organizacji, umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z tożsamości organizacji i bada podejrzanych zdarzenia, które można podjąć odpowiednie działania, aby je rozwiązać.
- Wdrożone wystąpienie usługi Active Directory IaaS zapewnia zarządzanie tożsamościami na poziomie systemu operacyjnego dla wdrożonych maszyn wirtualnych IaaS.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić dane osobowe i dostęp do tych danych:
- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń (HSM). Typ klucza jest HSM chronione 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień, korzystając z modelu RBAC.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.
- Rozwiązanie jest zintegrowana z usługą Azure Key Vault do zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków maszyn wirtualnych IaaS.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, ułatwiający identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania za pomocą konfigurowalnych alertów gdy znany złośliwego lub niechcianego oprogramowania podejmuje próbę zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Alerty zabezpieczeń**: [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) służy do monitorowania ruchu, zbieranie dzienników i analizowanie źródeł danych pod kątem zagrożeń. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejącej konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych osobowych. Usługa Azure Security Center obejmuje [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia badaniu i usuwaniu zagrożeń.

**Usługa Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji przy użyciu zapory aplikacji sieci web (WAF), a zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia także system reputacji.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Wysoka dostępność**: rozwiązanie zostanie wdrożone wszystkich maszyn wirtualnych w [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności zapewniają, że maszyny wirtualne są rozproszone między wieloma izolowanymi klastrami sprzętowymi Aby zwiększyć dostępność. Co najmniej jednej maszyny wirtualnej są dostępne podczas planowanych lub nieplanowanych zdarzeń związanych z konserwacją spotkania 99,95% umowy SLA platformy Azure.

**Magazyn usługi Recovery Services**: [magazyn usługi Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) przechowuje dane kopii zapasowej i chroni wszystkie konfiguracje maszyn wirtualnych Azure w ramach tej architektury. Przy użyciu magazynu usługi Recovery Services klienci mogą przywracać pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, umożliwiając skraca czas ich przywracania.

**Monitor w chmurze**: [monitora w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) to typ monitora kworum klastra trybu Failover w systemie Windows Server 2016, która korzysta z platformy Azure jako punktu rozstrzygania. Monitor w chmurze, takich jak każdy inny monitor kworum ma głos i można uczestniczyć w obliczeniach kworum, ale używa standardowych publicznej usługi Azure Blob Storage. Pozwala to wyeliminować koszty obsługi dodatkowych maszyn wirtualnych hostowanych w chmurze publicznej.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługa log Analytics oferuje szczegółowe rejestrowanie aktywności systemu i użytkownika, a także kondycji systemu. [Usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/) rozwiązanie zbiera i analizuje dane generowane przez zasoby na platformie Azure i środowiskach lokalnych.
- **Dzienniki aktywności**: [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory.
- **Archiwizowanie dziennika**: wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania. Te dzienniki połączyć z usługą Azure Log Analytics do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym.

Ponadto następujące rozwiązania do monitorowania, są uwzględnione w ramach tej architektury:
-   [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check rozwiązanie ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Ocena ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): rozwiązanie chroniące przed złośliwym kodem raporty dotyczące złośliwego oprogramowania, zagrożeń i ochronę stanu.
-   [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): rozwiązanie usługi Azure Automation przechowuje, uruchamia i zarządza elementami runbook.
-   [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpieczenia i inspekcja pulpit nawigacyjny zawiera ogólne informacje o stanie zabezpieczeń zasobów, zapewniając metryki w przypadku domen zabezpieczeń, problemy godne uwagi, wykrywania, analizy zagrożeń i typowe zapytania dotyczące zabezpieczeń.
-   [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): rozwiązania SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
-   [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): rozwiązanie do zarządzania aktualizacjami umożliwia zarządzanie klientami z aktualizacjami zabezpieczeń systemu operacyjnego, w tym stan dostępnych aktualizacji i procesu instalacji wymaganych aktualizacji.
-   [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.
-   [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie do śledzenia zmian umożliwia klientom łatwo identyfikować zmiany w środowisku.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych (DPD) dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/gdprIaaSdfd) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![IaaS Web Distributed modelu zagrożeń RODO](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS Web Distributed modelu zagrożeń RODO")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i zgodności planu — RODO klienta odpowiedzialność macierzy](https://aka.ms/gdprCRM) Wyświetla obowiązki kontrolera i procesora dla wszystkich artykułów RODO. Należy pamiętać, usług systemu Azure, klient jest zazwyczaj kontrolera i Microsoft działa jako procesor.

[Zabezpieczeń platformy Azure i zgodności planu — RODO IaaS Web Application implementacji Matrix](https://aka.ms/gdprIaaSweb) zawiera informacje, na które RODO artykuły są rozwiązywane przez architektury aplikacja internetowa IaaS, w tym szczegółowe opisy jak wdrożenia spełnia wymagania części każdego artykułu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacja internetowa IaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja-lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.  
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.  
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.  
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
