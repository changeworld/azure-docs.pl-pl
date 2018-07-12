---
title: Ponowne hostowanie aplikacji firmy Contoso na platformie Azure przy użyciu funkcji migracji do maszyny Wirtualnej platformy Azure, a wystąpienia zarządzanego Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu aplikację w środowisku lokalnym, na maszynach wirtualnych platformy Azure i wystąpienia zarządzanego Azure SQL
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: raynew
ms.openlocfilehash: 99eda135161a228fde139458de30f5120af55153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723945"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Migracja Contoso: ponowne hostowanie aplikacji w środowisku lokalnym na maszynach wirtualnych platformy Azure i wystąpienia zarządzanego Azure SQL

W tym artykule dowiesz się, jak firmy Contoso jest migrowana jego SmartHotel aplikacji frontonu maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i aplikacji bazy danych do wystąpienia zarządzanego usługi Azure SQL.

> [!NOTE]
> Wystąpienie zarządzane SQL Azure jest obecnie w wersji zapoznawczej.

Ten dokument jest czwarta serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje, a także szereg scenariuszy, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania i będziemy dodawać dodatkowe artykuły wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny lokalnej SmartHotel dwuwarstwowej aplikacji działających z oprogramowaniem VMware. Ich oceny maszyn wirtualnych aplikacji, za pomocą [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych usługi Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
Artykuł 4: Ponowne hostowanie dla maszyn wirtualnych platformy Azure i wystąpienia zarządzanego SQL (w tym artykule) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [usługi Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) usługi, aby przeprowadzić migrację do wystąpienia zarządzanego SQL. | Dostępne
[Artykuł 5: Ponowne hostowanie dla maszyn wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso przeprowadza migrację aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu Site Recovery.
[Artykuł 6: Ponowne hostowanie dla maszyn wirtualnych platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Ich Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu Site Recovery i na wystąpienie serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne

Jeśli chcesz używać dla przykładowej aplikacji SmartHotel, który jest używany w tym artykule, możesz ją pobrać z [github](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architektura środowiska lokalnego

Poniżej przedstawiono diagram przedstawiający bieżącej infrastruktury lokalnej firmy Contoso.

![Architektura firmy Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Firma Contoso ma jednego głównego centrum danych znajdujące się w mieście z nowego Jorku w wschodnich Stanach Zjednoczonych.
- Mają trzy dodatkowe gałęzie lokalne na terenie Stanów Zjednoczonych.
- Główne centrum danych jest połączony z Internetem za pomocą połączenia ethernet metro Fiber (500 MB/s).
- Każda gałąź jest lokalnie połączony z Internetem, przy użyciu połączeń klasy biznesowej, przy użyciu tuneli IPSec sieci VPN, wróć do głównego centrum danych. Dzięki temu ich całej sieci, aby trwale połączony oraz optymalizuje łączności z Internetem.
- Główne centrum danych jest w pełni zwirtualizowany z oprogramowaniem VMware. Mają one dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez program vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami i serwery DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Kontrolery domeny w lokalnej gałęzi, uruchom na serwerach fizycznych.



## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, co firma chce osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie i w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla ich deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT musi być w reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: w miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele są używane do określenia najlepszej metody migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku programu VMWare w środowisku lokalnym.  Przejście do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Contoso nie chcesz inwestować w tej aplikacji.  Jest ochrona kluczowych i ważnych dla firmy, ale w obecnej formie one po prostu chcesz ją przenieść się do chmury.
- Zadania administracyjne bazy danych należy zminimalizować, po przeprowadzeniu migracji aplikacji.
- Contoso nie zamierza korzystać z usługi Azure SQL Database dla tej aplikacji, a szuka alternatyw.

## <a name="proposed-architecture"></a>Proponowana architektury

W tym scenariuszu:

- Firma Contoso chce migrację swoich aplikacji turystycznej dwuwarstwowej w środowisku lokalnym.
- Rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM) i aplikacji znajdujących się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Baza danych aplikacji (SmartHotelDB) oni migrować do wystąpienia zarządzanego usługi Azure SQL.
- Migrowania lokalnych maszyn wirtualnych VMware na Maszynie wirtualnej platformy Azure.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Usługa zarządzania bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Usługa DMS umożliwia bezproblemową migrację z wielu źródłowych baz danych na danych na platformie Azure, przy minimalnych przestojach. | Dowiedz się więcej o [obsługiwane regiony](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS i get [cennik](https://azure.microsoft.com/pricing/details/database-migration/).
[Wystąpienie zarządzane SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Wystąpienie zarządzane to usługa zarządzana baza danych, która reprezentuje w pełni zarządzane wystąpienia programu SQL Server w chmurze platformy Azure. On współużytkować ten sam kod z najnowszą wersję aparatu bazy danych programu SQL Server i zawierają najnowsze funkcje, ulepszeń wydajności i poprawek zabezpieczeń. | Za pomocą wystąpieniach zarządzanych bazy danych SQL Azure działać na platformie Azure naliczane opłaty na podstawie pojemności. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i naliczenia opłat, po przejściu do trybu failover. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) o Naliczanie opłat i ceny.

 

## <a name="migration-process"></a>Proces migracji

Contoso zostaną zmigrowane warstwy sieci web i danych aplikacji SmartHotel na platformie Azure.

1. Firmy Contoso jest już jego infrastruktury platformy Azure w miejscu, więc należy po prostu dodać kilka określonych składników platformy Azure, w tym scenariuszu.
2. Warstwa danych zostaną poddane migracji za pomocą Data Migration Service (DMS).  Usługa DMS łączy maszynę Wirtualną w środowisku lokalnym SQL Server przez połączenie VPN lokacja lokacja między centrum danych firmy Contoso i platformą Azure, a następnie przeprowadza migrację bazy danych.
3. Warstwa sieci web zostaną poddane migracji, migracji lift-and-shift przy użyciu usługi Azure Site Recovery. Wiąże się to przygotowanie w lokalnym środowisku VMware, konfigurowanie włączenie replikacji i migracja maszyn wirtualnych przez awaryjną na platformie Azure.

     ![Architekturę migracji](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Wymagania wstępne

Oto Contoso (i) musi w tym scenariuszu.

**Wymagania** | **Szczegóły**
--- | ---
**Zarejestruj się w wersji zapoznawczej** | Muszą być zarejestrowane w SQL zarządzane wystąpienia ograniczonej publicznej wersji zapoznawczej. Musisz mieć subskrypcję platformy Azure w celu [Zarejestruj](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Utwórz konto może potrwać kilka dni, dlatego należy się upewnić, czy przed rozpoczęciem wdrażania tego scenariusza.
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Usługa Site recovery (lokalnego)** | Usługi na lokalnym serwerze vCenter powinny działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Host ESXi w wersji 5.5, 6.0 lub 6.5<br/><br/> Co najmniej jeden maszyn wirtualnych programu VMware działające na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.
**USŁUGA DMS** | Usługa DMS należy [zgodne lokalnego urządzenia sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Należy skonfigurować lokalne urządzenie sieci VPN. Musi mieć dostępny zewnętrznie publiczny adres IPv4, a adres nie może znajdować się za urządzeniem translatora adresów Sieciowych.<br/><br/> Upewnij się, że masz dostęp do lokalnej bazy danych programu SQL Server.<br/><br/> Zapora Windows powinien móc dostęp do aparatu bazy danych źródłowych. [Dowiedz się więcej](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Jeśli występuje zapora, przed komputerze bazy danych, należy dodać reguły zezwalające na dostęp do bazy danych i plików za pośrednictwem portu 445 SMB.<br/><br/> Poświadczenia używane do połączenia ze źródłem programu SQL Server oraz zarządzane wystąpienia docelowego muszą być członkami roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnianie w sieci lokalnej bazy danych usługi DMS umożliwia tworzenie kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że konto usługi, uruchamiające źródłowe wystąpienie programu SQL Server ma uprawnienia do zapisu w udziale sieciowym.<br/><br/> Należy zapamiętać Windows użytkownika (i hasło), który ma uprawnienia do pełnej kontroli w udziale sieciowym. Azure Database Migration Service personifikuje te poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure storage.<br/><br/> Proces instalacji programu SQL Server Express protokół TCP/IP ustawia **wyłączone** domyślnie. Upewnij się, że jest włączone.


## <a name="scenario-steps"></a>Kroki w scenariuszu

Oto jak Contoso, przechodzi do skonfigurowania wdrożenia:

> [!div class="checklist"]
> * **Krok 1: Konfigurowanie wystąpienia zarządzanego Azure SQL**: potrzebują wstępnie utworzone wystąpienie zarządzane, do którego zostanie przeprowadzona migracja bazy danych programu SQL Server w środowisku lokalnym.
> * **Krok 2: Przygotowanie usługi DMS**: należy zarejestrować dostawcę migracji bazy danych, Utwórz wystąpienie, a następnie utwórz projekt usługi DMS. Muszą również skonfigurować SA identyfikator URI usługi DMS. Sygnatury dostępu współdzielonego (SAS) identyfikator (URI) zapewnia delegowany dostęp do zasobów w ramach konta magazynu można przyznać dostęp jest ograniczony do magazynu obiektów. Identyfikator URI sygnatury dostępu Współdzielonego mogą skonfigurować tak, aby usługa DMS mogą uzyskiwać dostęp do kontenera konta magazynu, do którego usługa przekazuje te pliki kopii zapasowych programu SQL Server.
> * **Krok 3: Przygotowywanie platformy Azure dla usługi Site Recovery**: For Site Recovery musi utworzyć konto usługi Azure storage do przechowywania replikowanych danych i utworzyć magazyn usługi Recovery Services.
> * **Krok 4: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji**: Contoso przygotowania kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 5: Replikowanie maszyn wirtualnych**: Aby skonfigurować replikację, mogą skonfigurować środowisko źródłowe i docelowe odzyskiwania lokacji, skonfiguruj zasady replikacji i rozpocząć replikowanie maszyn wirtualnych do usługi Azure storage.
> * **Krok 6: Migrację bazy danych przy użyciu usługi DMS**: teraz migrowania bazy danych.
> * **Krok 7: Migrowanie maszyn wirtualnych z usługą Site Recovery**: one uruchomić testowy tryb failover, aby upewnić się, że wszystko działa, a następnie uruchom tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Krok 1: Przygotowanie wystąpienia zarządzanego usługi Azure SQL

Aby skonfigurować wystąpienia zarządzanego usługi Azure SQL, firma Contoso potrzebuje podsieci:

- Podsieć musi być w wersji dedykowanej. Musi być pusta i nie zawierają inne usługi w chmurze i nie może on być podsieć bramy.
- Po utworzeniu wystąpienia zarządzanego nie może dodać zasoby do niego.
- Podsieci nie może być skojarzone z nią sieciową grupę zabezpieczeń.
- Podsieć musi mieć użytkownika tabeli (UDR) za pomocą internetowej następnego przeskoku 0.0.0.0/0 jako tylko trasy do niej przypisany. 
- Opcjonalne niestandardowe DNS: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, należy dodać adres IP platformy Azure rekursywnego rozpoznawania nazw (na przykład 168.63.129.16) do listy. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsieci nie może być punkt końcowy usługi (magazyn lub SQL) skojarzonych z nim. Punkty końcowe usługi powinny być wyłączone w sieci wirtualnej.
- Podsieć musi mieć co najmniej 16 adresów IP. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) temat dobierania rozmiarów podsieci wystąpienia zarządzanego.
- W ich środowiskach hybrydowych niestandardowych ustawień DNS są potrzebne. Contoso skonfiguruje ustawienia DNS, użyj jednego lub kilku serwerach DNS platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) dotyczące dostosowywania DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego

Contoso konfiguruje sieci wirtualnej w następujący sposób: 

1. Contoso tworzy nową sieć wirtualną (VNET-SQLMI EU2) w regionie wschodnie stany USA 2 podstawowym w grupie zasobów ContosoNetworkingRG.
2. Contoso przypisuje przestrzeni adresowej 10.235.0.0/24, zapewniając, że zakres nie pokrywa się z innymi sieciami, w organizacji Contoso.
2. Dwie podsieci ich dodać do sieci:
    - SQLMI DS EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29). Ta podsieć będzie służyć do dołączenia katalogu do wystąpienia zarządzanego (SQLMI).

    ![Sieci zarządzanej wystąpienia](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Po wdrożeniu sieci wirtualnej i podsieci Contoso prowadzi komunikację równorzędną sieci w następujący sposób:

    - Elementy równorzędne sieci Wirtualnej — SQLMI-EUS2 z sieci Wirtualnej KONCENTRATORA EUS2 (piastą wschodnie stany USA 2).
    - Elementy równorzędne sieci Wirtualnej — SQLMI-EUS2 z sieci Wirtualnej PROD EUS2 (produkcyjnego).

    ![Komunikacja równorzędna w sieci](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso ustawia niestandardowych ustawień DNS. DNS będzie wskazywać najpierw ich kontrolery domeny platformy Azure. Usługa DNS platformy Azure będzie dodatkowej. Kontrolery domeny usługi Azure Contoso znajdują się w następujący sposób:

    - Znajduje się w podsieci EUS2-PROD-kontroler domeny, w środowisku produkcyjnym wschodnie stany USA 2 w sieci (sieć wirtualna-PROD-EUS2).
    - Adres CONTOSODC3: 10.245.42.4
    - Adres CONTOSODC4: 10.245.42.5
    - Program rozpoznawania nazw DNS platformy Azure: 168.63.129.16

     ![Sieci DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Potrzebujesz dodatkowej pomocy?**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) wystąpień zarządzanych SQL Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) dotyczące tworzenia sieci wirtualnej dla wystąpienia zarządzanego SQL.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) konfigurowania komunikacji równorzędnej.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) aktualizowanie ustawień DNS z usługami Azure AD.



### <a name="set-up-routing"></a>Konfigurowanie routingu

Wystąpienie zarządzane jest umieszczany w prywatnej sieci Wirtualnej, więc firma Contoso potrzebuje tabelę tras dla niego nawiązać połączenia z usługą Azure Management Service. Jeśli nie mogą komunikować się z usługą, który zarządza nim, staje się niedostępny.

- Tabela tras zawiera zbiór reguł (trasy), który określa, jak w sieci wirtualnej, w którym powinny być kierowane pakiety wysyłane z wystąpieniem zarządzanym.
- Tabela tras jest skojarzony z podsieci, w których są wdrożone wystąpienia zarządzanego. Każdy pakiet opuszczający podsieć odbywa się na podstawie tabeli tras skojarzone.
- Podsieć może być skojarzony tylko z jedną tabelą tras.
- Nie istnieją żadne dodatkowe opłaty, do tworzenia tabel tras w systemie Microsoft Azure.

1. Contoso tworzy tabelę tras zdefiniowanych przez użytkownika. Tabela tras jest tworzony w tej grupie zasobów ContosoNetworkingRG.

    ![Tabela tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Do wystąpienia zarządzanego wymogów, po wdrożeniu tabeli tras (MIRouteTable), Contoso Dodaj trasę z prefiksem adresu 0.0.0.0/0, i **typu następnego przeskoku** równa **Internet**.

    ![Prefiks tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso Skojarz tabelę tras z podsiecią SQLMI-DB-EUS2 (w sieci VNET-SQLMI-EUS2). 

    ![Podsieci tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) konfigurowania tras dla wystąpienia zarządzanego.

### <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Teraz firma Contoso może aprowizować wystąpienie zarządzane bazy danych SQL.

1. Ponieważ wystąpienia zarządzanego służy aplikacja biznesowa, Contoso wdrożyć ją w głównej regionu wschodnie stany USA 2, w grupie zasobów ContosoRG 
2. Wybierają cen warstwy, a rozmiar obliczeniowe i magazynowe wystąpienia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/) o cenach.

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po wdrożeniu wystąpienia zarządzanego w grupie zasobów ContosoRG są wyświetlane dwa nowe zasoby:

    - Wirtualnej klastra, w przypadku, gdy istnieje wiele wystąpień zarządzanych,
    - Wystąpienie zarządzane SQL Server. 

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) aprowizowania wystąpienia zarządzanego.

## <a name="step-2-prepare-dms"></a>Krok 2: Przygotowanie usługi DMS

Przygotowanie DMS Contoso musi wykonać kilka czynności:

- Zarejestruj dostawcę usługi DMS na platformie Azure
- Podaj DMS z dostępem do usługi Azure storage do przekazywania plików kopii zapasowej, migrować bazy danych. Mogą to zrobić przez utworzenie kontenera magazynu obiektów blob i Generowanie sygnatury dostępu współdzielonego (SAS) identyfikator URI. 
- Utwórz projekt usługi DMS.


Wykonaj następujące czynności:

1. Contoso Zarejestruj dostawcę migracji bazy danych w ramach ich subskrypcji.
    ![Rejestrowanie usługi DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Utwórz kontener obiektów blob storage i wygenerować identyfikator URI sygnatury dostępu Współdzielonego, aby usługa DMS do niego dostęp.

    ![IDENTYFIKATOR URI SYGNATURY DOSTĘPU WSPÓŁDZIELONEGO](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Na koniec Utwórz wystąpienie usługi DMS. 

    ![Wystąpienia usługi DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso umieszcza wystąpienia usługi DMS w podsieci EUS2-PROD-kontroler domeny, sieć wirtualna-PROD-DC — EUS2 sieci wirtualnej.
    - Mogą umieścić tam ponieważ musi znajdować się w sieci wirtualnej, które mogą uzyskiwać dostęp do maszyny Wirtualnej serwera SQL w środowisku lokalnym za pośrednictwem bramy sieci VPN.
    - EUS2 PROD sieci Wirtualnej jest połączona z siecią Wirtualną-HUB-EUS2 i będzie mógł korzystać z bram zdalnych.  Daje to gwarancję, że usługa DMS może komunikować się zgodnie z potrzebami.

        ![Sieć usługi DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Potrzebujesz dodatkowej pomocy?**
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) konfigurowania usługi DMS.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) dotyczących tworzenia i używania sygnatur dostępu Współdzielonego.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Przygotowywanie platformy Azure dla usługi Site Recovery

Istnieje kilka elementów platformy Azure, którą firma Contoso potrzebuje, aby skonfigurować Site Recovery do migracji ich warstwy internetowej maszyny Wirtualnej (WEBMV)

- Sieć wirtualną, w którym przełączone w tryb failover znajdują się zasoby.
- Konto magazynu platformy Azure do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.

One skonfigurowane Site Recovery w następujący sposób:

1. Ponieważ maszyna wirtualna frontonu sieci web do aplikacji SmartHotel, zakończy się niepowodzeniem przenieść maszynę Wirtualną do swoich istniejących sieci produkcyjnej (sieć wirtualna-PROD-EUS2) i podsieci (PROD-FE-EUS2), w regionie wschodnie stany USA 2 podstawowym. Ustawiają tej sieci się w przypadku ich [wdrożona infrastruktura platformy Azure](contoso-migration-infrastructure.md)
2. Tworzą konta usługi Azure storage (contosovmsacc20180528). Konta ogólnego przeznaczenia, używanych z magazynu w warstwie standardowa i replikacją LRS.

    ![Site Recovery magazynu](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Przy użyciu konta magazynu i sieci w miejscu Contoso można teraz utworzyć magazyn (ContosoMigrationVault) i umieść go w grupie zasobów ContosoFailoverRG w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) konfigurowaniu platformy Azure dla usługi Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji

Przygotowywanie wdrożenia oprogramowania VMware do odzyskiwania lokacji, w tym miejscu jest Contoso potrzebuje do wykonywania:

- Przygotowywanie konta na serwerze vCenter lub vSphere ESXi hoście, w celu zautomatyzowania odnajdowania maszyn wirtualnych.
- Przygotowywanie konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware, którą chcesz replikować.
- Przygotowywanie lokalnych maszyn wirtualnych, tak, aby umożliwić im połączenie z maszynami wirtualnymi platformy Azure, po ich utworzeniu po włączeniu trybu failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Wymagane jest konto, które można uruchamiać operacje, takie jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso konfiguruje konto w następujący sposób:

1. Tworzy rolę na poziomie vCenter.
2. Przypisuje wymaganych uprawnień do tej roli.

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli do automatycznego odnajdowania.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie wirtualnej, która ma być replikowana.

- Site Recovery może zrobić wypychania automatycznego instalowania tego składnika, po włączeniu replikacji dla maszyny Wirtualnej.
- Instalacja automatyczna wypychana wymaga przygotowania konta, które usługa Site Recovery będzie uzyskiwać dostęp maszyny Wirtualnej do.
- To konto można określić podczas konfigurowania replikacji w konsoli platformy Azure.
- Potrzebujesz domeny lub lokalnego konta z uprawnieniami do instalowania na maszynie Wirtualnej.

**Potrzebujesz dodatkowej pomocy**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover na platformie Azure Contoso chce można było połączyć się z replikowanymi maszynami wirtualnymi na platformie Azure. Aby to zrobić, istnieje kilka rzeczy, które są im potrzebne w na lokalną maszynę Wirtualną, zanim uruchomią oni migracji: 

1. Na potrzeby dostępu przez internet Włącz protokół RDP na maszynie Wirtualnej w środowisku lokalnym przed włączeniem trybu failover i upewnij się, że reguły TCP i UDP zostały dodane do **publicznych** profilu i że protokołu RDP jest dozwolona w **zapory Windows**  >  **Dozwolone aplikacje** we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem ich połączenia VPN lokacja lokacja, Włącz protokół RDP na maszynie lokalnej i Zezwalaj na RDP w **zapory Windows** -> **dozwolone aplikacje i funkcje** dla **domeny i Prywatne** sieci.
3. Ustawiają zasady sieci SAN systemu operacyjnego na maszynie Wirtualnej w środowisku lokalnym **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdzenie następujących kwestii:

- Powinno być nie oczekujących aktualizacji Windows na maszynie Wirtualnej podczas wyzwalania trybu failover. Jeśli, nie będzie mógł zalogować się do maszyny wirtualnej, do momentu ukończenia aktualizacji.
- Po przejściu w tryb failover należy sprawdzić **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie rozwiąże problemu, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikowanie lokalnych maszyn wirtualnych do platformy Azure z usługą Site Recovery

Przed uruchomieniem migracji na platformę Azure, firma Contoso potrzebuje do konfigurowania replikacji i włączanie replikacji na potrzeby ich lokalnej maszyny Wirtualnej.

### <a name="set-a-replication-goal"></a>Ustaw cel replikacji

1. W magazynie w obszarze nazwy magazynu (ContosoVMVault) ustawiają cel replikacji (**wprowadzenie** > **Site Recovery** > **przygotowanie infrastruktury**.
2. Określają, czy ich maszyn w środowisku lokalnym, że są one i maszyn wirtualnych VMware, czy mają być replikowane na platformie Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, należy potwierdzić, że został ukończony planowania wdrożenia, wybierając **tak, ma to zostało zrobione**. W tym wdrożeniu Contoso są tylko migrowania jednej maszyny Wirtualnej, a nie ma potrzeby planowania wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Teraz należy skonfigurować swoje środowisko źródłowe. W tym celu mogą pobrać szablon OVF i umożliwiają wdrażanie serwera konfiguracji i powiązanych z nim składników w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Składniki na serwerze obejmują:

- Serwer konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.
- Po utworzeniu i uruchomieniu maszyny Wirtualnej serwer konfiguracji Contoso można zarejestrować go w magazynie.


Contoso wykonaj następujące kroki w następujący sposób:

1. Szablon OVF będą oni mogli pobrać z witryny Azure portal (**Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**).
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. One zaimportuj szablon do programu VMware, aby utworzyć i wdrożyć maszynę Wirtualną.

    ![Szablon OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Po włączeniu maszyny Wirtualnej po raz pierwszy jej zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, logowania się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzie konfiguracji usługi Azure Site Recovery uruchamia się domyślnie.
5. W narzędziu określają nazwę do użycia podczas rejestrowania serwera konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierają **Zaloguj**, aby zalogować się do subskrypcji platformy Azure. Poświadczenia muszą mieć dostęp do magazynu, w którym zostanie zarejestrowany na serwerze konfiguracji. 

    [Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie. Logowania z maszyną ponownie, a następnie automatycznie uruchomiony Kreator zarządzania serwerem konfiguracji.
8. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
9. Wybierają subskrypcji, grupy zasobów i Magazyn, w którym można zarejestrować serwer konfiguracji.
        ![Vault](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. One, a następnie Pobierz i zainstaluj serwer MySQL, a program VMWare PowerCLI. Następnie sprawdzają poprawność ustawień serwera.
11. Po zakończeniu walidacji określają nazwy FQDN lub adres IP vCenter server lub hoście vSphere. Pozostaw domyślny port i określ przyjazną nazwę dla serwera vCenter na platformie Azure.
12. Należy określić konto które są utworzone wcześniej, Usługa Site Recovery może automatycznie odnajdować maszyny wirtualne VMware, które są dostępne dla replikacji. 
13. Ich podania poświadczeń dla automatycznego instalowania usługi mobilności, po włączeniu replikacji. W przypadku maszyn Windows konto wymaga uprawnień administratora lokalnego na maszynach wirtualnych. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po zakończeniu rejestracji w witrynie Azure portal firmy Contoso double sprawdza, czy serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
8. Usługa Site Recovery następnie nawiązanie połączenia z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Obecnie firma Contoso potrzebuje do konfigurowania środowiska docelowego replikacji.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto usługi Azure storage i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Po źródłowy i docelowy są skonfigurowane, Contoso jest gotowy do tworzenia zasad replikacji i skojarz go z serwerem konfiguracji.

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą zasadę **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg celu punktu odzyskiwania**: domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**. Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Potrzebujesz dodatkowej pomocy?**

- Może odczytywać kompletnym instruktażem wszystkie te kroki [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Włączanie replikacji

Teraz firma Contoso może rozpocząć replikowanie WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, że chce włączyć maszyn wirtualnych, wybierz serwer vCenter, a serwer konfiguracji.

 ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Teraz określają ustawienia docelowego, łącznie z grupy zasobów i sieci, w którym maszyna wirtualna platformy Azure zostaną umieszczone po włączeniu trybu failover oraz konto magazynu, w którym przechowywane będą replikowane dane.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso zaznacza WebVM replikacji. Usługa Site Recovery instaluje usługę mobilności na każdej maszynie Wirtualnej, po włączeniu replikacji dla niego. 

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso — sprawdza, czy wybrano właściwe zasady replikacji i umożliwia replikację WEBVM. Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w witrynie Azure portal firmy Contoso można wyświetlić jego strukturę dla maszyn wirtualnych replikowanych do platformy Azure.

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Potrzebujesz dodatkowej pomocy?**

Może odczytywać kompletnym instruktażem wszystkie te kroki [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Krok 6: Migrację bazy danych przy użyciu usługi DMS

Firma Contoso potrzebuje do tworzenia projektu usługi DMS i migracji bazy danych.

### <a name="create-a-dms-project"></a>Tworzenie projektu usługi DMS
1. Mogą utworzyć projekt usługi DMS. Określają typ serwera źródłowego, jak program SQL Server i element docelowy jako wystąpienia zarządzanego Azure SQL Database.

     ![Projekt usługi DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Po utworzeniu projektu, zostanie otwarty Kreator migracji.

### <a name="migrate-the-database"></a>Migrowanie bazy danych 

1. W Kreatorze migracji Contoso określa źródłowej maszyny Wirtualnej, na którym znajduje się lokalnej bazy danych, a poświadczenia dostępu do niego.

    ![Źródło usługi DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Użytkownik wybrać bazę danych do migracji (SmartHotel.Registration).

    ![Usługa DMS źródłowej bazy danych](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Jako obiekt docelowy określają nazwę wystąpienia zarządzanego na platformie Azure i poświadczenia dostępu.

    ![Usługa DMS docelowej](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Następnie w **+ nowe działanie** > **Uruchamianie migracji**, określają ustawienia, aby uruchomić migrację:
    - Poświadczenia źródłowym i docelowym.
    - Bazę danych do migracji.
    - Udział sieciowy, utworzonych przez siebie na maszynie Wirtualnej w środowisku lokalnym. Usługa DMS Trwa tworzenie kopii zapasowych źródła do tego udziału.
        - Konto usługi uruchamiające źródła programu SQL Server wystąpienie musi mieć uprawnienia do zapisu w tym udziale.
        - Określ ścieżkę nazwy FQDN do udziału.
    - Identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej do migracji usługi DMS.

        ![Ustawienia usługi DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Zapisz migrację i uruchom go.
6. W **Przegląd**, mogą monitorować stan migracji.

    ![Monitor usługi DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po zakończeniu migracji okaże się, że docelowe bazy danych istnieją w wystąpieniu zarządzanym.

    ![Monitor usługi DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Krok 7: Migrowanie maszyny Wirtualnej z usługą Site Recovery

Contoso uruchamia szybkie testowanie trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Przed migracją testowy tryb failover WEBVM, pomaga upewnić się, że wszystko działa zgodnie z oczekiwaniami. 

1. Uruchamiają testowy tryb failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 

    - Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że wszystkie warunki wymagane do migracji w miejscu.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.
3. Po zakończeniu trybu failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Sprawdzają, czy wszystko działa prawidłowo. Maszyna wirtualna ma prawidłowy rozmiar, jest ona połączona z odpowiednią siecią i działa. 
4. Po zweryfikowaniu testowy tryb failover, Contoso czyści tryb failover i rejestruje i zapisuje wszelkie obserwacje. 

### <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

1. Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Contoso tworzy plan odzyskiwania do migracji. WEBVM ich dodać do planu.

     ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Następnie uruchom one przejścia w tryb failover dla planu. Wybierz najnowszy punkt odzyskiwania i określ, czy usługa Site Recovery należy dążyć do zamknięcia lokalną maszynę Wirtualną przed wyzwoleniem trybu failover.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po włączeniu trybu failover Contoso Sprawdź, czy maszyny Wirtualnej platformy Azure jest wyświetlana w oczekiwany sposób w witrynie Azure portal.

   ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, mogą przeprowadzić migrację do zakończenia procesu migracji, zatrzymanie replikacji maszyny Wirtualnej i zatrzymać rozliczanie Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatni krok w procesie migracji Contoso aktualizuje parametry połączenia aplikacji, aby wskazywał zmigrowana baza danych, uruchomione na ich wystąpienia Zarządzanego SQL.

1. W witrynie Azure portal Znajdź ich parametry połączenia, klikając **ustawienia** > **parametry połączenia**.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Ciąg one aktualizowane przy użyciu nazwy użytkownika i hasło wystąpienia zarządzanego SQL.
3. Po skonfigurowaniu ciąg zastępują one bieżących parametrów połączenia w pliku web.config aplikacji.
4. Po aktualizowania pliku i zapisanie go, ich ponowne uruchomienie usług IIS na WEBVM. Wykonać to za pomocą **IISRESET /RESTART** z poziomu wiersza polecenia.
5. Po ponownym uruchomieniu usług IIS aplikacji będzie używać bazy danych uruchomione na wystąpienie zarządzane SQL.
6. W tym momencie można zamknąć Contoso SQLVM maszyny w środowisku lokalnym, a migracja zostanie zakończona.

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) Uruchamianie testu trybu failover. 
- [Dowiedz się,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenia w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Za pomocą po zakończeniu migracji aplikacji SmartHotel jest uruchomiona na Maszynie wirtualnej platformy Azure, a SmartHotel database jest dostępna na wystąpieniu zarządzanym usługi Azure SQL.  

Teraz firma Contoso potrzebuje należy wyczyścić odpowiednie zasoby, w następujący sposób:  

- Usuń maszynę WEBVM z magazynu programu vCenter.
- Usuń maszyny SQLVM z magazynu programu vCenter.
- Usuń WEBVM i SQLVM z lokalnego zadania tworzenia kopii zapasowej.
- Aktualizacja wewnętrznego dokumentach przedstawiono informacje na nowej lokalizacji, adres IP dla WEBVM.
- Usuń SQLVM w dokumentacji. Alternatywnie można oznaczy Pokaż jako usunięty i nie jest już na maszynie wirtualnej i tworzenie spisu.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso przegląda maszyn wirtualnych platformy Azure i wystąpienia zarządzanego SQL określeniem wystąpiły problemy z zabezpieczeniami z jego implementacją.

- Sieciowe grupy zabezpieczeń (NSG) dla maszyny Wirtualnej, aby kontrolować dostęp do przeglądu. Sieciowe grupy zabezpieczeń są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Są również uwzględnieniu zabezpieczania danych na dysku za pomocą usługi Azure Disk Encryption i usłudze Azure KeyVault.
- Umożliwiają one wykrywanie zagrożeń na SQL zarządzane wystąpienia (SQLMI). One będzie wysyłać alerty do ich system technicznej zespół/usługi zabezpieczeń, aby otworzyć bilet, jeśli zostanie wykryte zagrożenie. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Wystąpienie zarządzane zabezpieczeń](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych
Firma Contoso zamierza utworzyć kopię zapasową danych na WEBVM za pomocą usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

1. Firmy Contoso ma istniejących licencji na program WEBVM i będzie korzystać z korzyści użycia hybrydowego platformy Azure.  Będą one przekonwertować istniejącej maszyny Wirtualnej platformy Azure, aby móc korzystać z tej ceny.
2. Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management. 


## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji aplikacji frontonu maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. One migracji lokalnej bazy danych do wystąpienia zarządzanego Azure SQL przy użyciu usługi DMS.

## <a name="next-steps"></a>Kolejne kroki

W następnym artykule z tej serii pokażemy jak Contoso ponowne hostowanie aplikacji SmartHotel na maszynach wirtualnych platformy Azure przy użyciu tylko za pomocą usługi Azure Site Recovery.

