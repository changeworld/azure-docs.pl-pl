---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla Australii CHRONIONA
description: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla Australii CHRONIONA
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 3c82a88ea15b52672f9bed428e2e7af40a65309c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610200"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web IaaS dla Australii chronione

## <a name="overview"></a>Omówienie
Tych samych zabezpieczeń platformy Azure i zgodności planu znajdują się wskazówki dotyczące wdrażania infrastruktury jako usługi (IaaS) środowiska odpowiednie do zbierania, przechowywania i pobierania danych chronionych Australia dla instytucji rządowych, które są zgodne z celami australijskich instytucji rządowych informacje zabezpieczeń ręczne (ISM) produkowane przez australijskiego Signals Directorate ASD (). Ten plan przedstawiono typowe architektury referencyjnej i pomaga pokazują prawidłowego przygotowania danych poufnych dla instytucji rządowych w środowisku bezpiecznych, zgodnych i wieloma warstwami.

Tej architektury referencyjnej, przewodnik wdrażania i modelu zagrożeń Podaj podstawa klientom podejmować własnych planowania i system akredytacji procesów, pomagając klientom wdrażanie obciążeń na platformie Azure w sposób zgodny z ASD. Klienci mają możliwość wdrożenia usługi Azure VPN Gateway lub ExpressRoute za pomocą usług federacyjnych oraz integrować ją lokalnych zasobów z zasobami platformy Azure. Klienci, należy wziąć pod uwagę ryzyko związane z korzystania z zasobów lokalnych. Dodatkowa konfiguracja jest wymagane do spełnienia wymagań, ponieważ mogą się różnić zależnie od szczegółowe informacje na temat implementacji każdego klienta.

Osiągnięcie zgodności ASD wymaga, że informacje zabezpieczeń, zarejestrowanego rzeczoznawcę ds inspekcji systemu. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników
Tego rozwiązania powoduje wdrożenie architektury referencyjnej dla aplikacji sieci web IaaS z zapleczem programu SQL Server. Architektura obejmuje warstwy internetowej, warstwy danych, usługi Active Directory infrastruktury, usługa Application Gateway i modułu równoważenia obciążenia. Maszyny wirtualne wdrożone w warstwach sieć web i danych są konfigurowane w zestawie dostępności, a wystąpienia programu SQL Server powinny być skonfigurowane w zawsze włączonej grupy dostępności wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania zabezpieczeń i zgodności konfiguracji na poziomie systemu operacyjnego. Zarządzanie hostem bastionu zapewnia bezpieczne połączenie, Administratorzy mogą uzyskiwać dostęp do wdrożonych zasobów.

Architektura może dostarczać środowiska bezpieczną hybrydową, która rozszerza sieć lokalną na platformę Azure, umożliwiając obciążeń opartych na sieci web na dostęp do bezpiecznego firmy użytkownicy w organizacji prywatnej sieci lokalnej lub z Internetu. W przypadku rozwiązań lokalnych klient jest odpowiedzialne i odpowiada za wszystkie aspekty zabezpieczeń, działań i zgodności.

Zasoby platformy Azure zawartych w tym rozwiązaniu można nawiązać połączenie lokalnej sieci lub centra danych funkcji wspólnej lokalizacji (np. Przechwytywanie zmian danych Canberra) za pośrednictwem protokołu IPSec sieci VPN za pomocą usługi Azure VPN Gateway lub ExpressRoute... Decyzja o korzystanie z sieci VPN powinna być podejmowana z klasyfikacją przesyłanych danych i ścieżka sieciowa, należy pamiętać. Klienci, którzy hostują na dużą skalę, obciążeń o znaczeniu krytycznym, za pomocą wymagających danych big Data, należy rozważyć architektury sieci hybrydowej za pomocą usługi ExpressRoute w sieci prywatnej łączności z usługami platformy Azure. Zapoznaj się z sekcją wskazówki i zalecenia, aby uzyskać więcej szczegółowych informacji dotyczących mechanizmów połączeń opartych na platformie Azure.

Aby umożliwić użytkownikom uwierzytelnianie przy użyciu poświadczeń lokalnych i dostęp do wszystkich zasobów w chmurze przy użyciu lokalnej infrastruktury usług Active Directory Federation Services, stosuje się federacji z usługą Azure Active Directory. Active Directory Federation Services zapewniają uproszczoną, zabezpieczoną tożsamości federacyjnych i sieci web jednej funkcji logowania jednokrotnego dla tego środowiska hybrydowego. Zapoznaj się z sekcją wskazówki i zalecenia dalsze szczegóły dotyczące usługi Azure Active Directory Instalatora.

Rozwiązanie używa konta usługi Azure Storage, w których klienci mogą skonfigurować na potrzeby zachowania poufności danych magazynowanych szyfrowanie usługi Storage. Azure przechowuje trzy kopie danych w wybranym regionie klienta pod kątem odporności. Regiony platformy Azure są wdrażane w parach regionów odporne na błędy i geograficzne magazyn nadmiarowy gwarantuje, czy dane będą replikowane w drugim regionie przy użyciu także trzy kopie. Zapobiega to zdarzenie niekorzystny lokalizacji danych podstawowych klienta, co spowoduje utratę danych.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Kontroli dostępu opartej na rolach w usłudze Azure Active Directory jest używane do kontrolowania dostępu do zasobów i wdrażać w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Security Center i Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego. Usługa Azure Application Gateway jest skonfigurowany jako zapory w trybie zapobiegania i wymaga ruchu TLS co najmniej w wersji 1.2.

![Aplikacja sieci Web IaaS dla architektury referencyjnej chronione AU](images/au-protected-iaaswa-architecture.png?raw=true "aplikacji sieci Web IaaS dla chronionych Australia referencyjny Diagram architektury") 

To rozwiązanie korzysta z poniższych usług platformy Azure. Dalsze szczegółowe informacje na temat [architektura wdrożenia](#deployment-architecture) sekcji.

- Zestawy dostępności
    - (1) węzły klastra SQL
    - (1) w sieci web/IIS
- Usługa Azure Active Directory
- Azure Application Gateway
    - (1) zapora aplikacji sieci web
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP 3.0
        - Port odbiornika: 443
- Monitor w chmurze platformy Azure
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Dzienniki usługi Azure Monitor
- Azure Storage
- Azure Virtual Machines
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
    - (2) węzeł klastra programu SQL Server (SQL Server 2017 w systemie Windows Server 2016)
    - (2) w sieci web/IIS (system Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) grupy zabezpieczeń sieci
    - Azure Network Watcher
- Magazyn usługi Recovery Services

Ten plan zawiera usług platformy Azure, które nie mają certyfikatu do użycia w klasyfikacji chronione przez australijskiego Centrum zabezpieczeń Cybernetycznymi (ACSC). Wszystkie usługi w ramach tej architektury referencyjnej zostały zatwierdzone przez ACSC na poziomie rozpowszechniania ograniczania znaczników (DLM). Firma Microsoft zaleca klientom Przejrzyj opublikowanych zabezpieczenia i raporty z audytów związane z tych usług platformy Azure i użyj ich struktury zarządzania ryzykiem, aby określić, czy usługa Azure nadaje się do ich wewnętrzne akredytacji i używać Klasyfikacja chronionych.

## <a name="deployment-architecture"></a>Architektura wdrożenia
W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Host bastionu**: Host bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane

### <a name="virtual-network"></a>Sieć wirtualna
Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: To rozwiązanie służy do wdrażania zasobów w architekturę z podsieci oddzielnych sieci web, bazy danych podsieci, podsieci usługi Active Directory i podsieci zarządzania wewnątrz sieci wirtualnej. Podsieci są logicznie oddzielone reguły sieciowej grupy zabezpieczeń stosowane do poszczególnych podsieci, aby ograniczyć ruch pomiędzy podsieciami, aby tylko to niezbędne do systemu oraz funkcji zarządzania.

Zobacz konfigurację [sieciowe grupy zabezpieczeń](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone za pomocą tego rozwiązania. Organizacje można skonfigurować sieciowe grupy zabezpieczeń, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako wskazówki.

Każdej z tych podsieci ma dedykowany sieciowej grupy zabezpieczeń:
- 1 sieciowej grupy zabezpieczeń dla bramy aplikacji (LBNSG)
- 1 sieciowej grupy zabezpieczeń dla hostem bastionu (MGTNSG)
- 1 sieciowej grupy zabezpieczeń dla serwerów SQL i monitor w chmurze (SQLNSG)
- 1 sieciowej grupy zabezpieczeń dla warstwy internetowej (WEBNSG)

### <a name="data-in-transit"></a>Przesyłane dane
Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. 

Dane podlegające ochronie w drodze od klienta do sieci architektura używa Internet lub usługi ExpressRoute za pośrednictwem bramy sieci VPN skonfigurowane przy użyciu protokołu IPSEC.

Ponadto wszystkie transakcje na platformie Azure za pośrednictwem portalu zarządzania systemu Azure występować za pośrednictwem protokołu HTTPS przy użyciu protokołu TLS 1.2.

### <a name="data-at-rest"></a>Dane magazynowane
Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych na rzecz organizacji bezpieczeństwa i definicją australijskiej ISM dla instytucji rządowych wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Program SQL Server**: Wystąpienie programu SQL Server używa następujących środków bezpieczeństwa bazy danych:
-   [Inspekcja programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) śledzi zdarzenia bazy danych i zapisuje je do dzienników inspekcji.
-   [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, do ochrony informacji w stanie spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
-   [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
-   [Zaszyfrowane kolumny](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. To pomaga zmniejszyć dostępu w taki sposób, że poufne dane nie istnieje baza danych za pośrednictwem przed nieautoryzowanym dostępem. **Klienci są zobowiązani do dostosowywania dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.**

### <a name="identity-management"></a>Zarządzanie tożsamościami
Klienci mogą używać lokalnych Active Directory Federation Services federowania z [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/), której to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integruje się w katalogach lokalnych z usługą Azure Active Directory. Wszyscy użytkownicy w tym rozwiązaniu wymaga konta usługi Azure Active Directory. Za pomocą logowania federacyjnego użytkownicy mogą zalogować się do usługi Azure Active Directory i uwierzytelniania w zasobach platformy Azure przy użyciu poświadczeń lokalnych.

Ponadto następujące funkcje usługi Azure Active Directory ułatwia zarządzanie dostępem do danych w środowisku platformy Azure:
- Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast wszystkie uprawnienia użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji. Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach wpływających na organizacji&#39;tożsamości s umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z organizacji&#39;tożsamości s i bada podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

**Usługa Azure Multi-Factor Authentication**: Aby chronić tożsamości, powinny zostać wdrożone usługi Multi-Factor authentication. [Usługa Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) to łatwy w użyciu, skalowalne i niezawodne rozwiązanie, które oferuje drugiej metody uwierzytelniania, aby chronić użytkowników. Usługa Azure Multi-Factor Authentication wykorzystuje możliwości chmury i integruje się z usługą Active Directory w środowisku lokalnym i aplikacjami niestandardowymi. Ta ochrona jest rozszerzony do dużej liczby kluczowych scenariuszy.

### <a name="security"></a>Bezpieczeństwo
**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić i uzyskać dostęp do takich danych:

- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest sprzętowego modułu zabezpieczeń chronionej 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.
- Rozwiązanie jest zintegrowana z usługą Azure Key Vault do zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków maszyn wirtualnych IaaS.

**Zarządzanie poprawkami**: Windows wdrożone maszyny wirtualne w ramach tej architektury referencyjnej są domyślnie skonfigurowane, automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To rozwiązanie obejmuje również [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) usługi za pomocą których zaktualizowano wdrożenia mogą być tworzone do poprawki maszyny wirtualne w razie.

**Ochrona przed szkodliwym oprogramowaniem**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania, można skonfigurować alerty, gdy znany złośliwego lub niechcianego oprogramowania próbuje zainstalowania lub uruchomienia na chronionych maszynach wirtualnych.

**Usługa Azure Security Center**: Za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

Ponadto, ta architektura referencyjna korzysta z usługi [oceny luk w zabezpieczeniach](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) w usłudze Azure Security Center. Po skonfigurowaniu agent partnera (np. rozwiązania firmy Qualys) raportuje danych luki w zabezpieczeniach do platformy zarządzania partnera. Z kolei platforma zarządzania partnera zawiera luk w zabezpieczeniach i dane z powrotem do usługi Azure Security Center, monitorowania kondycji co umożliwia klientom szybko zidentyfikować maszyny wirtualne na ataki.

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

**Dzienniki platformy Azure Monitor**: Te dzienniki i dalszych są skonsolidowane w [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych, dzięki czemu wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z dzienników usługi Azure Monitor, dzięki czemu klienci mogą korzystać z zapytania Kusto dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące Azure [rozwiązania do monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Ocena usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Rozwiązanie kondycja Sprawdzanie usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Rozwiązanie SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbierać dzienniki z serwera SQL Azure. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Azure Monitor**: [Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w swoich zasobów platformy Azure.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Azure Network Watcher to usługa, która udostępnia narzędzia umożliwiające monitorowanie, diagnozowanie, wyświetlanie metryk i włączanie lub wyłączanie dzienników zasobów w sieci wirtualnej platformy Azure.  Wspólnota jednostek powinny implementować dzienniki przepływów usługi Network Watcher dla sieciowych grup zabezpieczeń i maszyn wirtualnych. Te dzienniki powinny być przechowywane w konto dedykowanych dla magazynu, które tylko dzienniki zabezpieczeń są przechowywane w i dostęp do konta magazynu powinny być zabezpieczone przy użyciu kontroli dostępu na podstawie ról.

## <a name="threat-model"></a>Model zagrożeń
Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/au-protected-iaaswa-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Aplikacja sieci Web IaaS dla modelu chronione korzystania z jednostki analizy zagrożeń](images/au-protected-iaaswa-threat-model.png?raw=true "aplikacji sieci Web IaaS dla diagramu modelu chronione korzystania z jednostki analizy zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności
Dokumentacja ta zgodność jest generowany przez firmę Microsoft na podstawie platform i usług udostępnianych przez firmę Microsoft. Z powodu różnych wdrożeń klienta ta dokumentacja zawiera podejście uogólnionego rozwiązania tylko hostowane w środowisku platformy Azure. Klienci mogą identyfikować i używanie alternatywnych produktów i usług, w oparciu o własne działających w środowiskach i wyników biznesowych. Klienci wybierający opcję użycia zasobów lokalnych muszą spełnić, zabezpieczenia i operacje dla tych zasobów w środowisku lokalnym. Udokumentowane rozwiązanie można dostosować przez klientów w ich wymagania zabezpieczeń i określonej w środowisku lokalnym.

[Zabezpieczeń platformy Azure i zgodności planu — macierz odpowiedzialności klienta AU-PROTECTED](https://aka.ms/au-protected-crm) Wyświetla wszystkie kontrolki zabezpieczeń wymagane przez chronione korzystania z jednostki analizy. Ta macierz szczegółowe informacje, czy implementacja każdej kontrolki jest odpowiedzialność firmy Microsoft, klientów, lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu — AU-PROTECTED IaaS Web Application implementacji Matrix](https://aka.ms/au-protected-iaaswa-cim) zawiera informacje, na którym chronione Australia kontrolki są rozwiązywane przez IaaS architektury aplikacji sieci web, w tym szczegółowy opis sposobu wdrożenia spełnia wymagania każdego formantu objętego usługą.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia
### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Do informacji klasyfikowanych bezpieczny tunel VPN protokołu IPSec należy skonfigurować nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web IaaS. Konfigurując odpowiednio IPSec sieci VPN, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel IPSec sieci VPN z platformą Azure, można tworzyć wirtualne prywatne połączenie między siecią lokalną a siecią wirtualną platformy Azure. To połączenie może odbywać się za pośrednictwem Internetu i umożliwia klientom bezpieczne "tuneli" informacji wewnątrz zaszyfrowane połączenia między siecią i platformą Azure przez klienta. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. 

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję połączenia prywatnego. Usługa ExpressRoute systemu Azure jest dedykowany łącze między Azure a lokalizacją lokalną lub dostawcy hostingu usług Exchange i jest uznawany za sieci prywatnej. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta, dostawca usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, aby go.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Aby pomóc w ochronie danych niejawnych przeprowadzanych przy użyciu internetowego lub usługi Azure ExpressRoute, klientów należy skonfigurować ich IPSec sieci VPN, stosując następujące ustawienia:

• Inicjator klienta sieci VPN musi być skonfigurowany na okres istnienia skojarzeń zabezpieczeń, nie większym niż 14400 sekund.
• Inicjator klienta sieci VPN, należy wyłączyć tryb agresywne IKEv1.
• Inicjator klienta sieci VPN należy skonfigurować Perfect Forward Secrecy.
• Klienta sieci VPN inicjatora, należy skonfigurować używanie HMAC SHA256 lub nowszej.

Opcje konfiguracji dla urządzenia sieci VPN i protokołu IPSec / parametry protokołu IKE [dostępne](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) do przeglądu.

### <a name="azure-active-directory-setup"></a>Konfiguracja usługi Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest Zarządzanie wdrożeniem i inicjowania obsługi dostępu do personelu wchodzenie w interakcje ze środowiskiem. Istniejący serwer Active Directory systemu Windows można zintegrować z usługą Azure Active Directory w [cztery kliknięcia](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Ponadto [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) pozwala klientom w celu skonfigurowania federacji z lokalną [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) i Azure Active Directory. Logowania federacyjnego klienci mogą włączać użytkownikom na logowanie się do usługi Azure Active Directory za pomocą swoich haseł lokalnych, jak i bez konieczności ponownego wprowadzania haseł znajduje się w sieci firmowej. Przy użyciu opcji federacji z usługami federacyjnymi Active Directory, można wdrożyć nowej instalacji programu Active Directory Federation Services, lub można określić istniejącą instalację w farmie programu Windows Server 2012 R2.

Aby zapobiec sklasyfikowanych danych z synchronizacją z usługą Azure Active Directory, klientów można ograniczyć atrybuty, które są replikowane do usługi Azure Active Directory, stosując następujące ustawienia w usłudze Azure Active Directory Connect:
- [Włącz filtrowanie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Wyłącz synchronizację skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [Wyłącz funkcję zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Wyłącz zapisywanie zwrotne urządzeń](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Pozostaw wartości domyślne dla [Zapobieganie przypadkowemu usuwaniu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) i [automatyczne uaktualnianie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności
- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako-to." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
