---
title: Rehost aplikacji Contoso, na platformie Azure przy użyciu funkcji migracji do maszyny Wirtualnej platformy Azure i wystąpienia zarządzane SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu aplikacji lokalnej na maszynach wirtualnych platformy Azure i wystąpienia zarządzane SQL Azure
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: c7dc9e8406494739aa5d8f21397a606e0b74a617
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301256"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Migracja Contoso: Rehost aplikacji lokalnej na maszynach wirtualnych platformy Azure i wystąpienia zarządzane SQL Azure

W tym artykule opisano sposób Contoso migruje jego SmartHotel aplikacji frontonu maszyny Wirtualnej na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i bazy danych aplikacji na wystąpienie zarządzane SQL Azure.

> [!NOTE]
> Wystąpienia zarządzane SQL Azure jest obecnie w przeglądzie.

Ten dokument jest czwarty w szeregu artykuły, które dokumentów, jak fikcyjnej firmy Contoso migruje jej zasobów lokalnych do chmury Microsoft Azure. Seria zawiera informacje i szereg scenariuszy, w których pokazano, jak skonfigurować infrastrukturę do migracji, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększa się złożoność i które będą dodawane dodatkowe artykuły wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
Artykuł 4: Rehost na maszynach wirtualnych platformy Azure i zarządzane wystąpienia SQL (w tym artykule) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji frontonu maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi migrację do zarządzanego wystąpienie serwera SQL. | Dostępne
[Artykuł 5: Rehost na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso przeprowadzanie migracji aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Site Recovery ich użyć do migracji aplikacji maszyny wirtualne i usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Rehost aplikacji systemu Linux, aby maszyny wirtualne platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Rehost aplikację systemu Linux, aby maszyny wirtualne platformy Azure i serwerem MySQL Azure](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery i na wystąpienie serwera MySQL Azure przy użyciu narzędzia MySQL Workbench. | Dostępne

Jeśli chcesz użyć przykładowej aplikacji SmartHotel używane w tym artykule, możesz pobrać go z [github](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architektura lokalnej

Poniżej przedstawiono diagram przedstawiający bieżącej infrastruktury lokalnej firmy Contoso.

![Architektura firmy Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Firma Contoso ma jeden głównego centrum danych znajduje się w mieście z nowego Jorku w Wschodniej Stanów Zjednoczonych.
- Mają one trzy dodatkowe gałęzi lokalnej w Stanach Zjednoczonych.
- Głównego centrum danych jest połączony z Internetem za pomocą połączenia ethernet metro Fiber (500 MB/s).
- Każda gałąź jest lokalnie połączony z Internetem przy użyciu połączenia klasy biznesowych, z tuneli protokołu IPSec sieci VPN do głównego centrum danych. Dzięki temu ich całej sieci trwale zostać nawiązane połączenie oraz optymalizuje łączności z Internetem.
- Głównego centrum danych jest w pełni zwirtualizowanych z programu VMware. Mają one dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami i serwery DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Na kontrolerach domeny w lokalnej gałęzi działa na serwerach fizycznych.



## <a name="business-drivers"></a>Czynniki biznesowe

Kadry kierowniczej IT ściśle działał z ich partnerów biznesowych, aby zrozumieć firmy chce osiągnięcie przy tej migracji:

- **Adres rozwoju firmy**: rośnie Contoso i w związku z tym istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawnić procesy ich deweloperzy i użytkownicy.  Potrzeb biznesowych IT szybkie i czas nie odpadkami lub pieniędzy, w związku z tym dostarczania szybciej na wymagania dotyczące klienta.
- **Zwiększyć elastyczność**: Contoso IT musi być bardziej odpowiednie do potrzeb firmy. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby włączyć Powodzenie w globalnej gospodarka reakcji.  Nie może pobrać w taki sposób, lub stają się okienek biznesowych.
- **Skala**: jak pomyślnie rozwojem działalności IT firmy Contoso musi dostarczyć systemów, które są w stanie rośnie w tempie tego samego.

## <a name="migration-goals"></a>Celów migracji

Zespół chmury Contoso ma przypięty dół celów migracji. Te cele są używane do określania najlepszej metody migracji:

- Po przeprowadzeniu migracji aplikacji na platformie Azure powinien mają te same możliwości wydajności, jak obecnie w środowisku VMWare lokalnymi.  Przenoszeniu do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Contoso nie mają inwestycji w tej aplikacji.  Jest ochrona kluczowych i ważnych w firmie, ale w postaci bieżącego ich po prostu chcesz przenieść ten plik jest w chmurze.
- Po przeprowadzeniu migracji aplikacji, należy zminimalizować zadania administracyjne bazy danych.
- Contoso nie chcesz używać bazy danych SQL Azure dla tej aplikacji, a następnie szuka alternatyw.

## <a name="proposed-architecture"></a>Architektura proponowanych

W tym scenariuszu:

- Firma Contoso chce migracji ich lokalne dwuwarstwowej aplikacji podróży.
- Na dwóch maszyn wirtualnych (WEBVM i SQLVM) do warstwy aplikacji i znajduje się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Zarządza w środowisku programu VMware vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Migrowania bazy danych aplikacji (SmartHotelDB) do wystąpienia zarządzane SQL Azure.
- Migrowania maszyn wirtualnych VMware lokalnych na maszynie Wirtualnej platformy Azure.
- Firma Contoso ma lokalnego datacenter (contoso-centrum danych), z lokalnym kontrolerem domeny (**contosodc1**).
- Będzie można zlikwidować lokalnymi maszynami wirtualnymi w centrum danych firmy Contoso, po ukończeniu migracji.

![Architektura scenariusza](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Usługa zarządzania bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Usługa DMS umożliwia bezproblemowe migracji z wielu źródeł bazy danych do platformy Azure danych, z minimalnym czasem przestojów. | Dowiedz się więcej o [obsługiwane regiony](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS i get [szczegóły cennika](https://azure.microsoft.com/pricing/details/database-migration/).
[Wystąpienia zarządzane SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Wystąpienie zarządzanych jest zarządzana usługa bazy danych, który reprezentuje w pełni zarządzane wystąpienia programu SQL Server w chmurze Azure. Dzieli ten sam kod z najnowszą wersję aparatu bazy danych programu SQL Server, a ma najnowszych funkcji, wydajności i poprawki zabezpieczeń. | Za pomocą wystąpień zarządzane bazy danych SQL Azure działa na platformie Azure wiąże się z opłatami oparte na wydajność. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwania po awarii dla maszyn wirtualnych platformy Azure i lokalnymi maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure Azure Storage jest obciążany.  Maszyny wirtualne platformy Azure są tworzone i spowodować naliczenie opłat, podczas pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) dotyczące opłat i cenach.

 

## <a name="migration-process"></a>proces migracji

Contoso będą migrowane warstw zarówno w sieci web, jak i dane aplikacji SmartHotel na platformie Azure.

1. Contoso ma już jego infrastruktury platformy Azure w miejscu, więc potrzebują tylko dodać kilka określone składniki platformy Azure dla tego scenariusza.
2. Warstwa danych zostanie zmigrowana przy użyciu usługi migracji danych (DMS).  DMS łączy do lokalnego serwera SQL z maszyny Wirtualnej przez połączenie VPN typu lokacja lokacja, między centrum danych firmy Contoso i platformą Azure, a następnie wykonuje migrację bazy danych.
3. Warstwa sieci web zostanie zmigrowana przy użyciu migracji przyrostu shift w usłudze Azure Site Recovery. Wiąże się to przygotowywanie środowiska VMware lokalnymi, konfigurowanie włączeniem replikacji i migracji maszyn wirtualnych przez awaryjne na platformie Azure.

     ![Architektura migracji](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Wymagania wstępne

Oto Contoso (a) musi w tym scenariuszu.

**Wymagania** | **Szczegóły**
--- | ---
**Zarejestruj się w wersji zapoznawczej** | Muszą być zarejestrowane w zarządzane wystąpienia ograniczone publicznej wersji zapoznawczej usługi SQL. Potrzebujesz subskrypcji platformy Azure w celu [Zarejestruj](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Tworzenia konta może potrwać kilka dni, dlatego należy się upewnić, czy przed rozpoczęciem wdrażania tego scenariusza.
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcji podczas przeprowadzania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, należy do pracy z administratora przypisanie uprawnienia właściciela lub współautora.<br/><br/> Aby uzyskać bardziej szczegółowe uprawnienia, przejrzyj [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Usługa Site recovery (lokalnego)** | Serwer vCenter lokalnej musi korzystać z wersji 5.5, 6.0 lub 6.5<br/><br/> Hosta ESXi z systemem w wersji 5.5, 6.0 lub 6.5<br/><br/> Jeden lub więcej maszyn wirtualnych VMware uruchomiona na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.
**USŁUGA DMS** | Usługa DMS mają być [urządzenia sieci VPN zgodne lokalnymi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musi być możliwość konfigurowania lokalnego urządzenia sieci VPN. Musi mieć publiczny adres IPv4 połączonej zewnętrznie, a adres nie może znajdować się za urządzeniem NAT.<br/><br/> Upewnij się, że masz dostęp do lokalną bazą danych programu SQL Server.<br/><br/> Zapora systemu Windows powinna mieć możliwość dostępu aparatu bazy danych źródła. [Dowiedz się więcej](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> W przypadku zapory przed komputera bazy danych, Dodaj reguły, aby zezwolić na dostęp do bazy danych i do plików za pośrednictwem portu SMB 445.<br/><br/> Poświadczenia używane do łączenia się ze źródłem programu SQL Server i zarządzane wystąpienia docelowego musi być członkiem roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnionej w programie z lokalną bazą danych, którym DMS umożliwia utworzenie kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że działa wystąpienie serwera SQL źródła konto usługi ma uprawnienia zapisu w udziale sieciowym.<br/><br/> Zwróć uwagę użytkownika systemu Windows (i hasło), który ma uprawnienia pełnej kontroli w udziale sieciowym. Usługa Azure bazy danych migracji personifikuje te poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure.<br/><br/> Proces instalacji programu SQL Server Express ustawia protokołu TCP/IP na **wyłączone** domyślnie. Upewnij się, że jest włączone.


## <a name="scenario-steps"></a>Kroki w scenariuszu

W tym jak Contoso ma ustawienie wdrożenia:

> [!div class="checklist"]
> * **Krok 1: Konfigurowanie wystąpienie serwera SQL Azure zarządzanych**: muszą wstępnie utworzone wystąpienie zarządzanych, do której będą migrowane lokalną bazą danych programu SQL Server.
> * **Krok 2: Przygotowanie DMS**: należy zarejestrować dostawcę migracja bazy danych, Utwórz wystąpienie, a następnie utwórz projekt DMS. Muszą także skonfigurować identyfikator URI SA DMS. Sygnatury dostępu współdzielonego (SAS) identyfikator URI (Uniform Resource) umożliwiają dostęp delegowany do zasobów na koncie magazynu, dzięki czemu można przyznać dostęp jest ograniczony do magazynu obiektów. Identyfikator URI sygnatury dostępu Współdzielonego one skonfigurować tak, aby usługa DMS mogą uzyskiwać dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowych programu SQL Server.
> * **Krok 3: Przygotowanie Azure Site Recovery**: dla usługi Site Recovery muszą utworzyć konto magazynu Azure do przechowywania replikowanych danych i utworzyć magazyn usług odzyskiwania.
> * **Krok 4: Przygotowanie VMware lokalnego do usługi Site Recovery**: Contoso przygotowania kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi Azure po pracy awaryjnej.
> * **Krok 5: Replikowania maszyn wirtualnych**: do skonfigurowania replikacji, ich Konfigurowanie środowiska źródłowego i docelowego odzyskiwania lokacji, skonfiguruj zasady replikacji i rozpoczęcia replikacji maszyn wirtualnych z magazynem platformy Azure.
> * **Krok 6: Migrowanie bazy danych o DMS**: po ich migracji bazy danych.
> * **Krok 7: Migrowanie maszyn wirtualnych z usługą Site Recovery**: one testować tryb failover, aby się upewnić, że wszystko działa, a następnie uruchom pełne trybu failover do migracji maszyn wirtualnych na platformie Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Krok 1: Przygotowanie zarządzanego wystąpienia Azure SQL

Aby skonfigurować wystąpienie zarządzane SQL Azure, Contoso musi podsieci:

- Muszą być dedykowane podsieci. Musi być pusta i nie zawierają inne usługi w chmurze i nie może być podsieć bramy.
- Po utworzeniu wystąpienia zarządzanego nie może dodać zasoby do niego.
- Podsieci nie może być grupy NSG skojarzonej z nim.
- Podsieć musi mieć użytkownika trasy tabeli przez z 0.0.0.0/0 następnego przeskoku Internet jako trasy tylko przypisane do niej. 
- Opcjonalne niestandardowe DNS: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, adres IP platformy Azure cyklicznego rozpoznawania nazw (na przykład 168.63.129.16) należy dodać do listy. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsieci nie może być punkt końcowy usługi (magazynu lub SQL) skojarzonych z nim. Punkty końcowe usługi powinny być wyłączone w sieci wirtualnej.
- Podsieć musi mieć co najmniej 16 adresów IP. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) o zmiany rozmiaru podsieci zarządzanego wystąpienia.
- W środowisku hybrydowym niestandardowych ustawień DNS są potrzebne. Contoso skonfiguruje ustawienia DNS użyć ich serwerów usługi Azure DNS. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) dotyczące dostosowywania DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurowanie sieci wirtualnej dla zarządzanego wystąpienia

Contoso konfiguruje sieci wirtualnej w następujący sposób: 

1. Contoso tworzy nową sieć wirtualną (VNET-SQLMI-EU2) w regionie podstawowym wschodnie stany USA 2 w grupie zasobów ContosoNetworkingRG.
2. Contoso przypisuje przestrzeni adresowej 10.235.0.0/24, zapewniając, że zakres nie pokrywają się z innymi sieciami, w firmie Contoso.
2. Dwie podsieci one dodane do sieci:
    - SQLMI DS EUS2 (10.235.0.0.25)
    - SQLMI-PIŁA EUS2 (10.235.0.128/29). Ta podsieć będzie służyć do podłączenia katalogu do zarządzanego wystąpienia (SQLMI).

    ![Sieci zarządzanej wystąpienia](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Po wdrożeniu sieci wirtualnej i podsieci Contoso równorzędnymi użytkownikami sieci w następujący sposób:

    - Elementy równorzędne sieci Wirtualnej-SQLMI-EUS2 z sieci Wirtualnej Centrum EUS2 (koncentrator sieci wirtualnej dla wschodnie stany USA 2).
    - Elementy równorzędne sieci Wirtualnej-SQLMI-EUS2 z sieci Wirtualnej produkcyjną EUS2 (sieci produkcyjnej).

    ![Sieć równorzędna](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso ustawia niestandardowych ustawień DNS. DNS będzie wskazywać najpierw ich kontrolery domeny z platformy Azure. Usługa DNS platformy Azure będzie dodatkowej. Kontrolerów domeny Contoso Azure znajdują się w następujący sposób:

    - Znajduje się w podsieci produkcyjną-DC-EUS2, w środowisku produkcyjnym wschodnie stany USA 2 sieci (VNET-produkcyjną EUS2).
    - Adres CONTOSODC3: 10.245.42.4
    - Adres CONTOSODC4: 10.245.42.5
    - Program rozpoznawania nazw DNS platformy Azure: 168.63.129.16

     ![Sieci DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Potrzebujesz dodatkowej pomocy?**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) wystąpień zarządzane SQL Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) dotyczące tworzenia sieci wirtualnej dla zarządzanych wystąpienia serwera SQL.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) konfigurowania komunikacji równorzędnej.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) aktualizowanie ustawień DNS platformy Azure AD.



### <a name="set-up-routing"></a>Konfigurowanie routingu

Zarządzane wystąpienia jest umieszczany w prywatnej sieci Wirtualnej, więc Contoso musi tabelę tras dla niego do komunikowania się z usługą zarządzania Azure. Jeśli nie mogą komunikować się z usługą, który zarządza nim, staje się niedostępna.

- Tabela tras zawiera zestaw reguł (trasy), który określa, jak powinny być kierowane pakiety wysyłane z wystąpienia zarządzane w sieci wirtualnej.
- Tabela tras jest skojarzony z podsieci, w których są wdrażane zarządzanych wystąpień. Każdy pakiet wychodzącego z podsieci jest obsługiwane na podstawie tabeli tras skojarzone.
- Podsieć może być skojarzony tylko z jedną tabelą tras.
- Nie ma żadnych dodatkowych opłat do tworzenia tabel tras na platformie Microsoft Azure.

1. Contoso tworzy tabelę tras zdefiniowanych przez użytkownika. Tabela tras jest tworzony w grupie zasobów ContosoNetworkingRG.

    ![Tabela tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Do zapewnienia zgodności z wymaganiami wystąpienia zarządzane, po wdrożeniu tabeli tras (MIRouteTable), Contoso dodać trasę z prefiksem adresu o 0.0.0.0/0, i **następnego przeskoku typu** ustawioną **Internet**.

    ![Prefiks tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso skojarzyć tabelę tras dla podsieci SQLMI-DB-EUS2 (w sieci VNET-SQLMI-EUS2). 

    ![Podsieci tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) Konfigurowanie tras dla zarządzanego wystąpienia.

### <a name="create-a-managed-instance"></a>Utwórz wystąpienie zarządzanych

Teraz Contoso może udostępnić wystąpienia zarządzane bazy danych SQL.

1. Ponieważ wystąpienia zarządzane służy aplikacja biznesowa, Contoso wdrożyć ją w głównej regionie wschodnie stany USA 2, w grupie zasobów ContosoRG 
2. Wybierają cenową warstwy, a rozmiar obliczeniowej i pamięci masowej dla tego wystąpienia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/) o cenach.

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po wdrożeniu wystąpienia zarządzane w grupie zasobów ContosoRG są wyświetlane dwa nowe zasoby:

    - Wirtualny klastra w przypadku, gdy istnieje wiele wystąpień zarządzanych,
    - Program SQL Server zarządzane wystąpienia. 

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) udostępniania wystąpienia programu zarządzane.

## <a name="step-2-prepare-dms"></a>Krok 2: Przygotowanie DMS

Aby przygotować DMS Contoso musi wykonać kilka czynności:

- Rejestrowanie dostawcy DMS na platformie Azure
- Podaj DMS z dostępem do magazynu Azure do przekazywania plików kopii zapasowej migrować bazy danych. To zrobić, tworząc kontenera magazynu obiektów blob i generowanie dostępu sygnatury dostępu Współdzielonego identyfikatora URI. 
- Utwórz projekt DMS.


Wykonaj następujące czynności:

1. Contoso Zarejestruj dostawcę migracja bazy danych w ramach ich subskrypcji.
    ![Usługa DMS rejestru](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Utwórz kontener magazynu obiektów blob i generować identyfikatora URI połączenia SAS, dzięki czemu usługa DMS do niego dostęp.

    ![IDENTYFIKATOR URI SYGNATURY DOSTĘPU WSPÓŁDZIELONEGO](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Na końcu Utwórz wystąpienie DMS. 

    ![Usługa DMS wystąpienia](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso umieszcza wystąpienia DMS produkcyjną-DC-EUS2 podsieci sieci Wirtualnej-produkcyjną-DC-EUS2 sieci wirtualnej.
    - One umieścić ponieważ musi znajdować się w sieci wirtualnej, które mogą uzyskiwać dostęp do maszyny Wirtualnej serwera SQL lokalnej za pośrednictwem bramy sieci VPN.
    - EUS2 produkcyjną sieci Wirtualnej jest połączyć za pomocą do sieci Wirtualnej-Centrum-EUS2 i może używać zdalnego bram.  Dzięki temu, że usługa DMS może komunikować się zgodnie z wymaganiami.

        ![Usługa DMS sieci](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Potrzebujesz dodatkowej pomocy?**
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) Konfigurowanie DMS.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) dotyczących tworzenia i używania sygnatury dostępu Współdzielonego.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Przygotowanie Azure do usługi Site Recovery

Istnieje wiele elementów platformy Azure, które Contoso wymaga, aby skonfigurować usługi Site Recovery dla migracji ich warstwa sieci web maszyny Wirtualnej (WEBMV)

- Awaryjnie sieci wirtualnej, w którym znajdują się zasoby.
- Konto magazynu Azure do przechowywania replikowanych danych. 
- W magazynie usług odzyskiwania Azure.

One skonfigurowane usługi Site Recovery w następujący sposób:

1. Ponieważ maszyna wirtualna jest frontonu sieci web do aplikacji SmartHotel, zakończy się niepowodzeniem przez maszynę Wirtualną do swoich istniejących sieci produkcyjnej (VNET-produkcyjną EUS2) i podsieci (produkcyjną-FE-EUS2) w regionie podstawowym wschodnie stany USA 2. Ustaw one tej sieci się w przypadku ich [wdrożyć infrastrukturę platformy Azure](contoso-migration-infrastructure.md)
2. Tworzenia konta magazynu platformy Azure (contosovmsacc20180528). Używają konta ogólnego przeznaczenia, z magazynu standardowego i replikacji LRS.

    ![Magazyn odzyskiwania lokacji](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Z sieci i magazynu konta w miejscu Contoso można teraz utworzyć magazyn (ContosoMigrationVault) i umieść go w grupie zasobów ContosoFailoverRG w regionie podstawowym wschodnie stany USA 2.

    ![Magazyn usługi Recovery Services](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Konfigurowanie usługi Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Przygotowanie VMware lokalnego do usługi Site Recovery

Do przygotowania odzyskiwania lokacji VMware, Oto, co trzeba zrobić Contoso:

- Przygotuj konto na vCenter server lub vSphere ESXi hosta, aby zautomatyzować odnajdywania maszyny Wirtualnej.
- Przygotowanie konta, które umożliwia automatyczne instalacji usługi mobilności na maszynach wirtualnych VMware, które mają być replikowane.
- Przygotuj lokalnych maszyn wirtualnych, tak, aby umożliwić im połączenie z maszyn wirtualnych platformy Azure, gdy są tworzone po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebujesz konta, które można uruchomić operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso Ustawia konto:

1. Tworzy rolę na poziomie vCenter.
2. Przypisuje wymaganych uprawnień do tej roli.

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli automatycznego wykrywania.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie wirtualnej, która ma być replikowana.

- Site Recovery może zrobić instalacji wypychanej automatyczne tego składnika, po włączeniu replikacji dla maszyny Wirtualnej.
- Dla instalacji wypychanej automatyczne należy przygotować konta, które uzyskują dostęp maszyny Wirtualnej odzyskiwania lokacji.
- To konto można określić po skonfigurowaniu replikacji w konsoli platformy Azure.
- Należy domeny lub lokalnego konta z uprawnieniami do zainstalowania na maszynie Wirtualnej.

**Potrzebujesz dodatkowej pomocy**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po w tryb failover na platformie Azure firma Contoso chce mieć możliwość nawiązania replikowanych maszyn wirtualnych na platformie Azure. Aby to zrobić, istnieje kilka rzeczy, które należy wykonać na lokalnej maszynie Wirtualnej, zanim uruchomią oni migracji: 

1. Dostęp przez internet, Włącz protokół RDP na lokalnej maszynie Wirtualnej przed trybu failover i upewnij się, że reguły TCP i UDP są dodawane do **publicznego** profilu oraz że RDP jest dozwolone w **zapory systemu Windows**  >  **Dozwolone aplikacje** we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem ich połączenia VPN lokacja lokacja, Włącz protokół RDP na maszynie lokalnej i Zezwalaj na RDP w **zapory systemu Windows** -> **dozwolone aplikacje i funkcje** dla **domeny i Prywatne** sieci.
3. One ustawić zasady sieci SAN systemu operacyjnego na lokalnej maszynie Wirtualnej, aby **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdź następujące kwestie:

- Powinien istnieć nie oczekujące aktualizacje systemu Windows na maszynie Wirtualnej, gdy wyzwolenie pracy awaryjnej. Jeśli, nie będzie mógł zalogować się do maszyny wirtualnej do momentu ukończenia aktualizacji.
- Po przejściu w tryb failover, należy sprawdzić **diagnostyki rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie pomoże, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzeć te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikowanie lokalnych maszyn wirtualnych do platformy Azure z usługą Site Recovery

Przed uruchomieniem migracji na platformie Azure, Contoso musi do skonfigurowania replikacji i włączyć replikację dla maszyny Wirtualnej ich lokalnych.

### <a name="set-a-replication-goal"></a>Ustaw celem replikacji

1. W magazynie, w obszarze Nazwa magazynu (ContosoVMVault) Sami ustawiają celem replikacji (**wprowadzenie** > **usługi Site Recovery** > **przygotowanie infrastruktury**.
2. Określają, czy ich maszyn znajdujących się lokalnie, są one maszyn wirtualnych VMware, a ich chcesz replikować do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Upewnij się, planowanie wdrożenia

Aby kontynuować, należy potwierdzić, że ukończył Planowanie wdrożenia, wybierając **tak, już to zostało zrobione**. W tym wdrożeniu Contoso jest tylko Migracja jednej maszyny Wirtualnej, a nie jest konieczne Planowanie wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Teraz należy skonfigurować ich środowiska źródłowego. W tym celu one Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji i jego skojarzone składniki jako o wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Składniki na serwerze obejmują:

- Serwer konfiguracji, który koordynuje komunikacji między lokalną i platformą Azure i zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.
- Po utworzeniu i uruchomieniu maszyny Wirtualnej serwer konfiguracji Contoso można zarejestrować go w magazynie.


Contoso wykonaj następujące kroki w następujący sposób:

1. Będą oni mogli pobrać szablonu OVF z portalu Azure (**przygotowanie infrastruktury** > **źródła** > **serwera konfiguracji**).
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. One zaimportuj szablon do programu VMware do tworzenia i wdrażania maszyny Wirtualnej.

    ![Szablon OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Podczas włączania maszyny Wirtualnej po raz pierwszy, on uruchamiany w procesie instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzia konfiguracji odzyskiwania lokacji Azure domyślnie uruchamia.
5. W narzędziu Określa nazwę do użycia podczas rejestrowania serwera konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierają **Zaloguj**, aby zalogować się do subskrypcji platformy Azure. Poświadczenia musi mieć dostęp do magazynu, w którym będzie można zarejestrować serwera konfiguracji. 

    [Zarejestruj serwer konfiguracji](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie. One ponownie zalogować się w komputerze i Kreatora konfiguracji serwera zarządzania jest uruchamiana automatycznie.
8. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić tego ustawienia, po przeprowadzeniu konfiguracji.
9. Wybierają subskrypcji, grupy zasobów i magazynu, w którym można zarejestrować serwer konfiguracji.
        ![Magazyn](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Ich, a następnie Pobierz i zainstaluj serwer MySQL i VMWare PowerCLI. Następnie w ich Sprawdź poprawność ustawień serwera.
11. Po sprawdzeniu poprawności określają adres FQDN lub adres IP hosta serwera lub vSphere vCenter. Pozostaw domyślny port i określ przyjazną nazwę dla serwera vCenter na platformie Azure.
12. Należy określić konto, które one utworzone wcześniej, dzięki czemu usługa Site Recovery może automatycznie odnajdować maszyn wirtualnych VMware, które są dostępne dla replikacji. 
13. Określają poświadczenia automatyczne instalowanie usługi mobilności, gdy replikacja jest włączona. W przypadku komputerów z systemem Windows konto musi uprawnienia administratora lokalnego na maszynach wirtualnych. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po zakończeniu rejestracji w portalu Azure Contoso podwójne sprawdza, czy serwer konfiguracji i serwera VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może zająć 15 minut lub dłużej. 
8. Usługa Site Recovery następnie łączy się przy użyciu określonych ustawień serwerów VMware i odnajduje maszyn wirtualnych.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso powinien skonfigurować środowiska docelowego replikacji.

1. W **przygotowanie infrastruktury** > **docelowego**, wybierają ustawienia obiektu docelowego.
2. Usługa Site Recovery sprawdza, czy znajduje się konto magazynu Azure i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Po źródłowy i docelowy są skonfigurowane, Contoso jest gotowy do utworzenia zasad replikacji i powiązać ją z serwera konfiguracji.

1. W **przygotowanie infrastruktury** > **ustawienia replikacji** > **zasad replikacji** >  **tworzenie i Skojarz**, tworzenia zasad **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg RPO**: domyślny 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Przechowywania punktu odzyskiwania**. Domyślna wartość 24 godzin. Ta wartość określa, jak długo trwa okna przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, na którym są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Potrzebujesz dodatkowej pomocy?**

- Pełny przewodnik wszystkie te kroki można znaleźć [ustawienia odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrożenie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Włączanie replikacji

Contoso można teraz rozpocząć replikację WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, czy chcą włączyć maszyn wirtualnych, wybierz serwer vCenter i serwer konfiguracji.

 ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Teraz określ ich ustawień obiektu docelowego, w tym nazwa grupy zasobów i sieci, w którym maszyny Wirtualnej Azure zostaną umieszczone po trybu failover oraz konto magazynu, w którym będą przechowywane replikowanych danych.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso wybiera WebVM replikacji. Usługa Site Recovery instaluje usługi mobilności na każdej maszynie Wirtualnej, po włączeniu replikacji dla niego. 

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso sprawdzają, czy wybrano zasady replikacji poprawne i umożliwia replikację WEBVM. One śledzić postęp replikacji w **zadania**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w portalu Azure Contoso można wyświetlić strukturę dla maszyn wirtualnych replikowanych do platformy Azure.

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Potrzebujesz dodatkowej pomocy?**

Pełny przewodnik wszystkie te kroki można znaleźć [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Krok 6: Migrowanie bazy danych o DMS

Contoso musi utworzyć projekt DMS i migrację bazy danych.

### <a name="create-a-dms-project"></a>Tworzenie projektu DMS
1. Tworzenia projektu DMS. Określa typ serwera źródłowego jako serwer SQL i cel jako wystąpienia zarządzane bazy danych SQL Azure.

     ![Usługa DMS projektu](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Po utworzeniu projektu, zostanie otwarty Kreator migracji.

### <a name="migrate-the-database"></a>Migrację bazy danych 

1. W Kreatorze migracji Contoso Określa źródło maszyny Wirtualnej, na którym znajduje się lokalną bazą danych, a poświadczenia dostępu do niego.

    ![Usługa DMS źródła](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Wybierają bazy danych do migracji (SmartHotel.Registration).

    ![Usługa DMS źródłowej bazy danych](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Jako miejsce docelowe określają nazwę wystąpienia zarządzane w Azure i poświadczenia dostępu.

    ![Usługa DMS docelowego](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Następnie w **+ nowe działanie** > **uruchamiania migracji**, określają ustawienia, aby uruchomić migracji:
    - Poświadczenia źródłowe i docelowe.
    - Baza danych, aby przeprowadzić migrację.
    - Udział sieciowy, do utworzenia na lokalnej maszynie Wirtualnej. Usługa DMS przyjmuje kopii zapasowych źródła do tego udziału.
        - Konto usługi uruchamiania źródła programu SQL Server wystąpienie musi mieć uprawnienia do zapisu w tym udziale.
        - Określ ścieżkę FQDN do udziału.
    - Identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazywania plików kopii zapasowej dla migracji DMS.

        ![Usługa DMS ustawienia](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Zapisz migracji i uruchom go.
6. W **omówienie**, ich monitorowania stanu migracji.

    ![Usługa DMS monitora](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po zakończeniu migracji ich Sprawdź, czy z docelowymi bazami danych istnieją w wystąpieniu zarządzanych.

    ![Usługa DMS monitora](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Krok 7: Migrowanie maszyn wirtualnych z usługą Site Recovery

Contoso uruchamia szybkie test trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Przed migracją pomaga testowy tryb failover WEBVM, upewnij się, że wszystko działa zgodnie z oczekiwaniami. 

1. One uruchomić testowy tryb failover do najnowszego dostępnego punktu w czasie (**najnowsze przetworzone**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu próbuje zamknąć źródłowej maszyny Wirtualnej, aby mogło nastąpić wyzwolenie pracy awaryjnej usługi Site Recovery. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Tryb failover uruchomień testów: 

    - Uruchamia się upewnić, że wszystkie warunki wymagane do migracji w miejscu Sprawdzanie wymagań wstępnych.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.
3. Po zakończeniu pracy awaryjnej, replika maszyny Wirtualnej Azure pojawia się w portalu Azure. Sprawdzają, czy wszystko działa poprawnie. Maszyna wirtualna jest odpowiedni rozmiar, jest połączony z siecią prawo i jest uruchomiona. 
4. Po zweryfikowaniu testowania trybu failover, Contoso czyści trybu failover i rekordów i zapisuje wszelkie uwagi. 

### <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

1. Po zweryfikowaniu, że test trybu failover działał zgodnie z oczekiwaniami, Contoso tworzy plan odzyskiwania do migracji. WEBVM ich dodawania do planu.

     ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Następnie uruchom one trybu failover dla planu. Wybierz najnowszy punkt odzyskiwania i określ, czy usługa Site Recovery należy dążyć do zamykania maszyny Wirtualnej lokalnego, aby mogło nastąpić wyzwolenie pracy awaryjnej.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po pracy awaryjnej Contoso Sprawdź, czy jest wyświetlany maszyny Wirtualnej Azure, zgodnie z oczekiwaniami w portalu Azure.

   ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, ich Zakończenie migracji na zakończenie procesu migracji, zatrzymania replikacji dla maszyny Wirtualnej i Zatrzymaj rozliczeń usługi Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatni krok w procesie migracji Contoso aktualizuje parametry połączenia aplikacji do migrowanych baza danych uruchomiony na ich MI SQL.

1. W portalu Azure znajduje się ciąg połączenia, klikając **ustawienia** > **parametry połączenia**.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Zaktualizowanie ciąg przy użyciu nazwy użytkownika i hasła zarządzane wystąpienia SQL.
3. Po skonfigurowaniu ciąg zastępują one bieżących parametrów połączenia w pliku web.config aplikacji.
4. Po aktualizacji plik i zapisać go, ich ponownego uruchomienia usług IIS na WEBVM. Je z **IISRESET /RESTART** z wiersza polecenia.
5. Po ponownym uruchomieniu usług IIS aplikacja będzie używać bazy danych, uruchomione w wystąpieniu programu SQL zarządzane.
6. W tym momencie Contoso można zamknąć SQLVM maszyny lokalnej, a migracja została zakończona.

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) uruchomiony test trybu failover. 
- [Dowiedz się](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenie w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Czyszczenie po migracji

Z po zakończeniu migracji aplikacji SmartHotel działa na maszynie Wirtualnej platformy Azure, a SmartHotel bazy danych jest dostępny w wystąpieniu zarządzane SQL Azure.  

Teraz Contoso musi wykonać niektóre oczyszczania w następujący sposób:  

- Usuń maszynę WEBVM ze spisu vCenter.
- Usuń maszynę SQLVM ze spisu vCenter.
- Usuń WEBVM i SQLVM z lokalnym zadania tworzenia kopii zapasowej.
- Aktualizacja wewnętrznego dokumentacja nowej lokalizacji, adres IP dla WEBVM.
- Usuń SQLVM z dokumentacją. Alternatywnie one można oznaczyć, Pokaż jako usunięte i nie jest już w maszynie Wirtualnej spisu.
- Przejrzyj wszystkie zasoby, które interakcyjnie wycofany z eksploatacji maszyn wirtualnych, a następnie zaktualizować wszystkie odpowiednie ustawienia lub dokumentacji odzwierciedlą nowej konfiguracji.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z zasobów na platformie Azure Contoso musi pełni operacjonalizacji i zabezpieczyć ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Sprawdzenia zespół zabezpieczeń firmy Contoso, maszynach wirtualnych platformy Azure i zarządzane wystąpienia SQL ustalić problemy zabezpieczeń z jej wdrożenia.

- Przeglądu grup zabezpieczeń sieci (NSG) dla maszyny Wirtualnej, w celu kontroli dostępu. Grupy NSG są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Rozważanych również Zabezpieczanie danych na dysku przy użyciu szyfrowania dysków Azure i Azure KeyVault.
- Umożliwiają one wykrywanie zagrożeń na SQL zarządzane wystąpienia (SQLMI). One będzie wysyłać alerty do ich system technicznej zespół/usługi zabezpieczeń można otworzyć bilet, jeśli zostanie wykryte zagrożenie. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Zarządzane zabezpieczeń](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych
Firma Contoso ma kopię zapasową danych na WEBVM przy użyciu usługi Kopia zapasowa Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja kosztów i licencjonowania

1. Contoso ma istniejącej licencji dla WEBVM i będzie korzystać z asysty hybrydowe platformy Azure.  Przekonwertuje one istniejące maszyny Wirtualnej platformy Azure, aby móc korzystać z tej cennik.
2. Contoso spowoduje włączenie zarządzania koszt Azure licencjonowane przez Cloudyn, oddziału firmy Microsoft. To rozwiązanie do zarządzania koszt usługi chmury, które pozwala korzystać z oraz zarządzania platformy Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o Azure kosztów zarządzania. 


## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji frontonu aplikacji maszyny Wirtualnej na platformie Azure przy użyciu usługi Site Recovery. One migracji lokalną bazą danych do usługi Azure SQL zarządzane wystąpienia przy użyciu DMS.

## <a name="next-steps"></a>Kolejne kroki

W artykule w serii zostanie omówiony sposób Contoso rehost aplikacji SmartHotel na maszynach wirtualnych platformy Azure korzystanie z usługi Azure Site Recovery.

