---
title: Ponowne hostowanie aplikacji w środowisku lokalnym firmy Contoso, migrując maszyny wirtualne platformy Azure i wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu aplikację w środowisku lokalnym, na maszynach wirtualnych platformy Azure, a za pomocą wystąpienia zarządzanego Azure SQL Database.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 3e3f8dffbaa7109423aacdbfbaa658bada8bb84a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215343"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migracja Contoso: ponowne hostowanie aplikacji w środowisku lokalnym na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database

W tym artykule Contoso przeprowadza migrację swoich aplikacji SmartHotel maszyny Wirtualnej frontonu na Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Site Recovery. Contoso wykonuje także migrację bazy danych aplikacji do wystąpienia zarządzanego Azure SQL Database.

> [!NOTE]
> Wystąpienie usługi Azure SQL Database Managed jest obecnie w wersji zapoznawczej.

Ten artykuł stanowi jeden w serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje i szereg scenariuszy, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania. Artykuły zostaną dodane do serii wraz z upływem czasu.


Artykuł | Szczegóły | Stan
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md) | Contoso uruchamia oceny jego SmartHotel aplikacja dwuwarstwowa lokalnych działających z oprogramowaniem VMware. Contoso ocenia maszyn wirtualnych aplikacji przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi. Contoso ocenia aplikacji bazy danych programu SQL Server przy użyciu [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji SmartHotel w środowisku lokalnym. Contoso aplikacja jest migrowana maszyna wirtualna frontonu za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso przeprowadzanie migracji aplikacji baz danych do wystąpienia usługi Azure SQL Database Managed przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | W tym artykule
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso app jego SmartHotel maszyn wirtualnych jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso kończy lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzacja aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso przeprowadza migrację swoich aplikacji SmartHotel do aplikacji sieci web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure. | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso przeprowadza migrację swoich aplikacji osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu lokacjach. Aplikacja sieci web jest zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzacja Team Foundation Server w programie Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso migruje jej wdrożenia serwera Team Foundation Server w środowisku lokalnym przy użyciu funkcji migracji do programu Visual Studio Team Services na platformie Azure. | Dostępne
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure, a następnie rearchitects aplikacji. Contoso rearchitects warstwy sieci web aplikacji jako kontener, Windows i rearchitects bazy danych aplikacji przy użyciu usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompilować aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services i usługi Azure Cosmos DB. | Dostępne

Możesz pobrać przykładową aplikację SmartHotel, która jest używana w tym artykule [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architektura środowiska lokalnego


Ten diagram przedstawia bieżącej infrastruktury lokalnej firmy Contoso:

![Bieżący architektura firmy Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Firma Contoso ma jednego głównego centrum danych. Centrum danych znajduje się w mieście z nowego Jorku w wschodnich Stanach Zjednoczonych.
- Firma Contoso ma trzy dodatkowe gałęzie lokalne na terenie Stanów Zjednoczonych.
- Główne centrum danych jest połączony z Internetem za pomocą fiber połączenia Metro Ethernet (500 MB/s).
- Każdej gałęzi jest połączony z Internetem lokalnie przy użyciu klasy biznesowej połączeń przy użyciu tuneli IPsec sieci VPN, wróć do głównego centrum danych. Ustawienia umożliwia całej sieci firmy Contoso do podłączenia trwale i optymalizuje łączności z Internetem.
- Główne centrum danych jest w pełni zwirtualizowany z oprogramowaniem VMware. Firma Contoso ma dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez program vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami. Firma Contoso używa serwerów DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Kontrolery domeny w lokalnej gałęzi, uruchom na serwerach fizycznych.

## <a name="business-drivers"></a>Czynniki biznesowe

Firmy Contoso IT zespół kierowniczy pracował ściśle zintegrowana z partnerami biznesowymi firmy, aby zrozumieć, co firma chce osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie. Co w efekcie zwiększa wykorzystanie w systemach lokalnych i infrastruktury w firmie.
- **Zwiększenie wydajności**: firma Contoso potrzebuje usunąć niepotrzebne procedur i usprawniać procesy dla jego deweloperów i użytkowników. Potrzeby biznesowe IT do szybkiego i do nie odpadów czas i pieniądze, więc firma może szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT musi być w reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany, które występują w witrynie marketplace termin firmy im odnosić sukcesy w globalnej gospodarki reagować. IT w firmie Contoso nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: jak pomyślnie rozwoju działalności firmy Contoso IT, należy podać systemów, które można rozwijać w tym samym tempie.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury Contoso zidentyfikowała cele tej migracji. Firma używa celów migracji, aby określić najlepszą metodę migracji.

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, które aplikacja ma już dziś w środowisku VMWare lokalne firmy Contoso. Przejście do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Contoso nie chcesz inwestować w aplikacji. Aplikacja jest ochrona kluczowych i ważnych dla firmy, ale firma Contoso chce po prostu przenieść aplikację w obecnej formie do chmury.
- Zadania administracyjne bazy danych należy zminimalizować, po przeprowadzeniu migracji aplikacji.
- Contoso nie chcesz używać usługi Azure SQL Database dla tej aplikacji. Szuka alternatyw.

## <a name="proposed-architecture"></a>Proponowana architektury

W tym scenariuszu:

- Firma Contoso chce migrację swoich aplikacji turystycznej dwuwarstwowej w środowisku lokalnym.
- Aplikacja jest warstwowa między dwiema maszynami wirtualnymi (**WEBVM** i **SQLVM**) i znajduje się na hoście VMware ESXi wersji 6.5 (**contosohost1.contoso.com**). 
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomiona na maszynie Wirtualnej.
- Contoso przeprowadza migrację bazy danych aplikacji (**SmartHotelDB**) do wystąpienia zarządzanego bazy danych SQL Azure.
- Contoso umożliwia migrowanie lokalnych maszyn wirtualnych VMware na Maszynie wirtualnej platformy Azure.
- Firma Contoso ma lokalne centrum danych (**contoso-datacenter**) i kontroler domeny w środowisku lokalnym (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Usługi platformy Azure

Usługa | Opis | Koszty
--- | --- | ---
[Usługa zarządzania bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Usługa zarządzania bazy danych umożliwia bezproblemową migrację z wielu źródłowych baz danych do platformy danych Azure przy minimalnych przestojach. | Dowiedz się więcej o [obsługiwane regiony](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) i [cennika bazy danych usługi zarządzania](https://azure.microsoft.com/pricing/details/database-migration/).
[Wystąpienie zarządzane usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Wystąpienie zarządzane to usługa zarządzana baza danych, która reprezentuje w pełni zarządzane wystąpienia programu SQL Server w chmurze platformy Azure. Używa tego samego kodu jako najnowszą wersję aparatu bazy danych programu SQL Server i zawierają najnowsze funkcje, ulepszeń wydajności i poprawek zabezpieczeń. | Za pomocą wystąpienia zarządzanego SQL Database działających na platformie Azure spowoduje naliczenie opłaty na podstawie pojemności. Dowiedz się więcej o [wystąpieniu zarządzanym kalkulacji cen](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa Site Recovery organizuje i zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i opłaty za operacje po przejściu do trybu failover. Dowiedz się więcej o [Site Recovery, opłaty i ceny](https://azure.microsoft.com/pricing/details/site-recovery/).

 ## <a name="migration-process"></a>Proces migracji

Contoso zmigruje warstwy sieci web i danych w jego SmartHotel aplikacji na platformie Azure, wykonując następujące kroki:

1. Firma Contoso ma już swoją infrastrukturę platformy Azure w miejscu, więc po prostu musi dodać kilka określonych składników platformy Azure, w tym scenariuszu.
2. Warstwa danych zostaną poddane migracji za pomocą programu Data Migration Service. Łączy się maszynę Wirtualną w środowisku lokalnym SQL Server z usługi migracji danych przez połączenie VPN lokacja lokacja między centrum danych firmy Contoso i platformą Azure. Następnie Data Migration Service przeprowadza migrację bazy danych.
3. Warstwa sieci web zostanie zmigrowana przy użyciu migracji lift-and-shift przy użyciu usługi Site Recovery. Proces wymaga ich przygotowywanie w lokalnym środowisku VMware, konfigurowanie włączenie replikacji i migracja maszyn wirtualnych przez awaryjną na platformie Azure.

     ![Architekturę migracji](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>Wymagania wstępne

Contoso i inni użytkownicy muszą spełniać następujące wymagania wstępne dotyczące tego scenariusza:

Wymagania | Szczegóły
--- | ---
**Zarejestruj się w wersji zapoznawczej wystąpienia zarządzanego** | Użytkownik musi zostać zarejestrowany w wystąpieniu zarządzanym bazy danych SQL ograniczonej publicznej wersji zapoznawczej. Musisz mieć subskrypcję platformy Azure do [Zarejestruj](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Rejestracja może potrwać kilka dni, aby ukończyć, dlatego upewnij się się zarejestrować, przed rozpoczęciem wdrażania tego scenariusza.
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem subskrypcji, należy skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zobacz [zarządzanie dostępem Site Recovery za pomocą kontroli dostępu opartej na rolach](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Usługa Site Recovery (lokalnego)** | Wystąpienie serwera vCenter w środowisku lokalnym powinna działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Host ESXi w wersji 5.5, 6.0 lub 6.5<br/><br/> Co najmniej jeden maszyn wirtualnych programu VMware działające na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.
**Usługa zarządzania bazy danych** | Usługa zarządzania bazy danych należy [zgodne lokalnego urządzenia sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Należy skonfigurować lokalne urządzenie sieci VPN. Musi mieć publiczny adres IPv4 dołączonej do Internetu. Adres nie może znajdować się za urządzeniem translatora adresów Sieciowych.<br/><br/> Upewnij się, że masz dostęp do lokalnej bazy danych programu SQL Server.<br/><br/> Zapora Windows powinien móc dostęp do aparatu bazy danych źródłowych. Dowiedz się, jak [skonfigurować zaporę Windows dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> W przypadku zapory przed komputerze bazy danych, Dodaj reguły, aby zezwolić na dostęp do bazy danych i plików za pośrednictwem portu 445 SMB.<br/><br/> Poświadczenia, które są używane, aby nawiązać połączenie z wystąpieniem programu SQL Server i obiektu docelowego wystąpienia zarządzanego muszą być członkami roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnianie w sieci lokalnej bazy danych usługi zarządzania bazy danych można użyć do utworzenia kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że konto usługi, uruchamiające źródłowe wystąpienie programu SQL Server ma uprawnienia do zapisu w udziale sieciowym.<br/><br/> Zanotuj Windows użytkownika i hasło, które ma uprawnienia pełnej kontroli w udziale sieciowym. Usługa bazy danych zarządzania personifikuje te poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage.<br/><br/> Proces instalacji programu SQL Server Express protokół TCP/IP ustawia **wyłączone** domyślnie. Upewnij się, że jest włączone.

## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak firma Contoso zamierza skonfigurowania wdrożenia:

> [!div class="checklist"]
> * **Krok 1: Konfigurowanie wystąpienia zarządzanego SQL Database**: firma Contoso potrzebuje wstępnie utworzone wystąpienie zarządzane do którego zostanie przeprowadzona migracja bazy danych programu SQL Server w środowisku lokalnym.
> * **Krok 2: Przygotowanie usługi zarządzania bazy danych**: Contoso należy zarejestrować dostawcę migracji bazy danych, Utwórz wystąpienie, a następnie utwórz projekt bazy danych zarządzania usługi. Contoso również należy zdefiniować sygnatury dostępu współdzielonego (SAS) identyfikator (URI) dla usługi zarządzania bazy danych. Identyfikator URI sygnatury dostępu Współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu firmy Contoso, więc Contoso można przyznać dostęp jest ograniczony do magazynu obiektów. Contoso konfiguruje identyfikatora URI połączenia SAS, dzięki czemu usługa zarządzania bazy danych można uzyskać dostępu do kontenera konta magazynu, do którego usługa przekazuje te pliki kopii zapasowej programu SQL Server.
> * **Krok 3: Przygotowywanie platformy Azure dla usługi Site Recovery**: Contoso musi utworzyć konto magazynu do przechowywania replikowanych danych dla usługi Site Recovery. On również utworzyć magazyn usługi Azure Recovery Services.
> * **Krok 4: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji**: Contoso będzie przygotowania kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta nawiązać połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 5: Replikowanie maszyn wirtualnych**: Aby skonfigurować replikację, Contoso konfigurowania środowisk źródłowych i docelowych odzyskiwania lokacji, konfiguruje zasady replikacji i rozpoczyna się replikowanie maszyn wirtualnych do usługi Azure Storage.
> * **Krok 6: Migrację bazy danych przy użyciu usługi zarządzania bazy danych**: Contoso przeprowadzanie migracji baz danych.
> * **Krok 7: Migracji maszyn wirtualnych przy użyciu usługi Site Recovery**: Contoso uruchamia test trybu failover, aby sprawdzić, czy wszystko działa. Następnie Contoso uruchamia tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Krok 1: Przygotowanie wystąpienie zarządzane bazy danych SQL

Aby skonfigurować bazę danych wystąpienia zarządzanego Azure SQL, firma Contoso potrzebuje podsieci, która spełnia następujące wymagania:

- Podsieć musi być w wersji dedykowanej. Może być pusta i nie może zawierać inne usługi w chmurze. Podsieci nie może być podsieć bramy.
- Po utworzeniu wystąpienia zarządzanego Contoso nie należy dodawać zasoby do podsieci.
- Podsieci nie może mieć skojarzonych z nią sieciową grupę zabezpieczeń.
- Podsieć musi mieć zdefiniowany przez użytkownika (UDR) trasy tabelę routingu. Powinny być tylko trasy przypisane Internetu dla następnego przeskoku 0.0.0.0/0. 
- Opcjonalne niestandardowe DNS: Jeżeli niestandardowe DNS jest określony w sieci wirtualnej platformy Azure, adres IP platformy Azure rekursywnego rozpoznawania nazw (na przykład 168.63.129.16) musi być dodana do listy. Dowiedz się, jak [Konfigurowanie niestandardowych pozycji DNS dla wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsieci nie może być punkt końcowy usługi (magazyn lub SQL) skojarzonych z nim. Punkty końcowe usługi powinny być wyłączone w sieci wirtualnej.
- Podsieć musi mieć co najmniej 16 adresów IP. Dowiedz się, jak [rozmiar podsieci wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- W środowisku hybrydowym firmy Contoso niestandardowych ustawień DNS są wymagane. Contoso konfiguruje ustawienia DNS, użyj jednego lub kilku serwerów usługi Azure DNS firmy. Dowiedz się więcej o [dostosowywania DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego

Contoso konfiguruje sieci wirtualnej w następujący sposób: 

1. Contoso tworzy nową sieć wirtualną (**EU2-VNET-SQLMI**) w regionie wschodnie stany USA 2 podstawowym. Dodaje sieci wirtualnej, aby **ContosoNetworkingRG** grupy zasobów.
2. Contoso przypisuje 10.235.0.0/24 przestrzeni adresowej. Contoso gwarantuje, że zakres nie pokrywa się z innymi sieciami, w swojej organizacji.
2. Contoso dodaje dwie podsieci do sieci:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Ta podsieć jest używana do dołączenia w katalogu do wystąpienia zarządzanego.

    ![Zarządzane wystąpienia — tworzenie sieci wirtualnej](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Po wdrożeniu sieci wirtualnej i podsieci Contoso prowadzi komunikację równorzędną sieci w następujący sposób:

    - Elementy równorzędne **EUS2-VNET-SQLMI** z **VNET-HUB-EUS2** (centralnej sieci wirtualnej dla wschodnie stany USA 2).
    - Elementów równorzędnych **EUS2-VNET-SQLMI** z **VNET-PROD-EUS2** (produkcyjnego).

    ![Komunikacja równorzędna w sieci](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso ustawia niestandardowych ustawień DNS. DNS wskazuje najpierw kontrolery domeny systemu Azure firmy Contoso. System DNS Azure jest dodatkowej. Kontrolery domeny Contoso Azure znajdują się w następujący sposób:

    - Na terenie **EUS2-PROD-DC** podsieci w sieci produkcyjnej wschodnie stany USA 2 (**VNET-PROD-EUS2**)
    - **CONTOSODC3** adres: 10.245.42.4
    - **CONTOSODC4** adres: 10.245.42.5
    - Program rozpoznawania nazw DNS platformy Azure: 168.63.129.16

     ![Serwery DNS w sieci](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Potrzebujesz dodatkowej pomocy?*

- Zapoznaj się z omówieniem programu [wystąpienie zarządzane usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Dowiedz się, jak [tworzenie sieci wirtualnej dla wystąpienia zarządzanego SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Dowiedz się, jak [skonfigurować komunikację równorzędną](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Dowiedz się, jak [aktualizowanie ustawień DNS usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Konfigurowanie routingu

Wystąpienie zarządzane jest umieszczany w prywatnej sieci wirtualnej. Firma Contoso potrzebuje tabelę tras dla sieci wirtualnej do komunikowania się z usługą zarządzania platformy Azure. Jeśli sieć wirtualna nie może komunikować się z usługą, który zarządza nim, sieć wirtualna stanie się niedostępna.

Contoso uwzględnia następujące czynniki:

- Tabela tras zawiera zestaw reguł (trasy), które określają, jak w sieci wirtualnej, w którym powinny być kierowane pakiety wysyłane z wystąpieniem zarządzanym.
- Tabela tras jest skojarzony z podsieci, w których są wdrożone wystąpienia zarządzanego. Każdy pakiet, który opuszcza podsieć odbywa się na podstawie tabeli tras skojarzone.
- Podsieć może być skojarzona z tabelą tras tylko jeden.
- Nie istnieją żadne dodatkowe opłaty, do tworzenia tabel tras w systemie Microsoft Azure.

 Aby skonfigurować routing:

1. Contoso tworzy tabelę trasy zdefiniowanej przez użytkownika. Contoso tworzy tabelę tras w **ContosoNetworkingRG** grupy zasobów.

    ![Tabela tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Zgodnie z wymaganiami wystąpienia zarządzanego, pod tabelą tras (**MIRouteTable**) jest wdrażana, Contoso dodaje trasę, która ma prefiks adresu 0.0.0.0/0. **Typu następnego przeskoku** ustawiono opcję **Internet**.

    ![Prefiks tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso kojarzy tabelę tras z **SQLMI-DB-EUS2** podsieci (w **EUS2-VNET-SQLMI** sieci). 

    ![Podsieci tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [Konfigurowanie tras dla zarządzanego wystąpienia](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Teraz firma Contoso może aprowizować wystąpienie zarządzane bazy danych SQL:

1. Ponieważ wystąpienia zarządzanego służy aplikacja biznesowa, Contoso wdraża wystąpienia zarządzanego w regionie wschodnie stany USA 2 z głównej firmy. Contoso dodaje wystąpienie zarządzane, aby **ContosoRG** grupy zasobów.
2. Contoso wybiera cen warstwy, rozmiar obliczeń i magazynu dla tego wystąpienia. Dowiedz się więcej o [wystąpieniu zarządzanym kalkulacji cen](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po wdrożeniu wystąpienia zarządzanego dwa nowe zasoby są wyświetlane w **ContosoRG** grupy zasobów:

    - Klaster wirtualny w przypadku firmy Contoso ma wiele wystąpień zarządzanych.
    - Wystąpienie zarządzane bazy danych programu SQL Server. 

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [aprowizowania wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-management-service"></a>Krok 2: Przygotowanie usługi zarządzania bazy danych

Aby przygotować usługę zarządzania bazy danych, Contoso musi wykonać kilka czynności:

- Zarejestruj dostawcę usługi zarządzania bazy danych na platformie Azure.
- Zapewnia usługi zarządzania bazy danych z dostępem do usługi Azure Storage przekazywania plików kopii zapasowej, które są używane do migracji bazy danych. Aby zapewnić dostęp do usługi Azure Storage, Contoso tworzy kontener usługi Azure Blob storage. Contoso generuje identyfikator URI sygnatury dostępu Współdzielonego dla kontenera magazynu obiektów Blob. 
- Utwórz projekt bazy danych usługi zarządzania.

Następnie Contoso wykonuje następujące czynności:

1. Contoso rejestruje dostawcy migracji bazy danych w ramach swojej subskrypcji.
    ![Składnik Usługa zarządzania bazy danych — Zarejestruj się](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Contoso tworzy kontener magazynu obiektów Blob. Contoso generuje identyfikator URI sygnatury dostępu Współdzielonego, aby usługa zarządzania bazy danych do niego dostęp.

    ![Usługę zarządzania bazy danych — Generowanie identyfikatora URI sygnatury dostępu Współdzielonego](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Contoso tworzy wystąpienie bazy danych usługi zarządzania. 

    ![Usługę zarządzania bazy danych — tworzenie wystąpienia](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso umieszcza wystąpienia bazy danych usługi zarządzania w **EUS2-PROD-DC** podsieci **sieć wirtualna-PROD-DC — EUS2** sieci wirtualnej.
    - Contoso umieszcza usługi zarządzania bazy danych istnieje, ponieważ usługa musi znajdować się w sieci wirtualnej, które mogą uzyskiwać dostęp do maszyny Wirtualnej serwera SQL w środowisku lokalnym za pośrednictwem bramy sieci VPN.
    - **VNET-PROD-EUS2** jest połączona z **VNET-HUB-EUS2** i będzie mógł korzystać z bram zdalnych. **Użyj bram zdalnych** opcja gwarantuje, że usługa bazy danych zarządzania może komunikować się zgodnie z potrzebami.

        ![Usługę zarządzania bazy danych — Konfigurowanie sieci](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Potrzebujesz dodatkowej pomocy?*

- Dowiedz się, jak [Konfigurowanie bazy danych usługi zarządzania](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Dowiedz się, jak [tworzenie i używanie sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Przygotowywanie platformy Azure dla usługi Site Recovery

Kilka elementów platformy Azure są wymagane dla firmy Contoso skonfigurować Site Recovery do migracji jej maszyna wirtualna w warstwie sieci web (**WEBMV**):

- Sieć wirtualna, w którym znajdują się zasoby w trybie failed-over.
- Konto magazynu do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.

Contoso konfiguruje Site Recovery w następujący sposób:

1. Ponieważ maszyna wirtualna ma fronton sieci web do aplikacji SmartHotel, firmy Contoso przechodzi w trybie Failover na maszynie Wirtualnej i jej istniejącej sieci produkcyjnych (**VNET-PROD-EUS2**) i podsiecią (**PROD-FE-EUS2**). Sieci i podsieci znajdują się w regionie wschodnie stany USA 2 podstawowym. Contoso konfigurowania sieci podczas jego [wdrożona infrastruktura platformy Azure](contoso-migration-infrastructure.md).
2. Contoso tworzy konto magazynu (**contosovmsacc20180528**). Firma Contoso używa konta ogólnego przeznaczenia. Contoso wybiera magazynu w warstwie standardowa i replikacją magazyn lokalnie nadmiarowy.

    ![Site Recovery — Tworzenie konta magazynu](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Za pomocą konta magazynu i sieci w miejscu Contoso utworzenie magazynu (**ContosoMigrationVault**). Contoso miejsca magazynu w **ContosoFailoverRG** grupę zasobów, w regionie wschodnie stany USA 2 podstawowym.

    ![Recovery Services — utwórz magazyn](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [Konfigurowanie platformy Azure dla usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji

Aby przygotować VMware dla usługi Site Recovery, Contoso, należy wykonać te zadania:

- Przygotowywanie konta na vCenter Server wystąpienia lub hosta vSphere ESXi. Konto automatyzuje proces odnajdowania maszyn wirtualnych.
- Przygotowywanie konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware, który firma Contoso chce replikacji.
- Przygotowywanie lokalnych maszyn wirtualnych, połączyć się z maszynami wirtualnymi platformy Azure po ich utworzeniu po włączeniu trybu failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagany jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Firma Contoso potrzebuje konta, które można uruchamiać operacje, takie jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso konfiguruje konto, wykonując następujące zadania:

1. Tworzy rolę na poziomie vCenter.
2. Przypisuje wymaganych uprawnień do tej roli.

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [tworzenie i przypisywanie roli do automatycznego odnajdowania](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi mobilności

Usługa Mobility musi być zainstalowana na maszynie Wirtualnej, który firma Contoso chce replikacji. Contoso uwzględnia te czynniki dotyczące usługi mobilności:

- Site Recovery może zrobić wypychania automatycznego instalowania tego składnika, gdy Contoso umożliwia replikację dla maszyny Wirtualnej.
- Instalacja wypychania automatycznego Contoso należy przygotować konta, które używa Usługa Site Recovery, aby dostęp do maszyny Wirtualnej.
- Contoso określa to konto, gdy konfiguruje replikacji w konsoli platformy Azure.
- Firmy Contoso musi mieć domeny lub lokalnego konta z uprawnieniami do instalowania na maszynie Wirtualnej.

*Potrzebujesz dodatkowej pomocy*

Dowiedz się, jak [Tworzenie konta na potrzeby instalacji wypychanej usługi mobilności](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover na platformie Azure Contoso chce można było połączyć się z replikowanymi maszynami wirtualnymi na platformie Azure. Połączyć się z replikowanymi maszynami wirtualnymi na platformie Azure, Contoso muszą wykonać kilka zadań na lokalną maszynę Wirtualną, przed rozpoczęciem migracji: 

1. Aby uzyskać dostęp za pośrednictwem Internetu Contoso umożliwia protokołu RDP na maszynie Wirtualnej w środowisku lokalnym przed włączeniem trybu failover. Contoso gwarantuje, że reguły TCP i UDP zostały dodane do **publicznych** profilu i że protokołu RDP jest dozwolona w **zapory Windows** > **dozwolone aplikacje** dla wszystkich profilów .
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN lokacja lokacja firmy Contoso Contoso umożliwia protokołu RDP na maszynie lokalnej. Contoso umożliwia protokołu RDP w systemie **zapory Windows** > **dozwolone aplikacje i funkcje** dla **domena i prywatne** sieci.
3. Contoso ustawia zasady sieci SAN systemu operacyjnego na maszynie Wirtualnej w środowisku lokalnym **OnlineAll**.

Firma Contoso potrzebuje po uruchomieniu trybu failover, sprawdź następujące elementy:

- Powinno być nie oczekujących aktualizacji Windows na maszynie Wirtualnej po wyzwoleniu przejścia w tryb failover. W przypadku oczekujących aktualizacji Windows Contoso nie Zaloguj się do maszyny wirtualnej do momentu ukończenia aktualizacji.
- Po przejściu w tryb failover należy sprawdzić Contoso **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli Contoso nie można wyświetlić diagnostyki rozruchu, należy to sprawdzić czy maszyna wirtualna jest uruchomiona, a następnie przejrzyj [wskazówki dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>Krok 5: Replikowanie lokalnych maszyn wirtualnych na platformę Azure przy użyciu usługi Site Recovery

Przed uruchomieniem migracji na platformę Azure, firma Contoso potrzebuje do konfigurowania replikacji i włączanie replikacji dla maszyny Wirtualnej jej w środowisku lokalnym.

### <a name="set-a-replication-goal"></a>Ustaw cel replikacji

1. W magazynie w obszarze nazwy magazynu (**ContosoVMVault**), Contoso ustawia cel replikacji (**wprowadzenie** > **Site Recovery**  >   **Przygotowanie infrastruktury**).
2. Contoso Określa, czy jego maszyn w środowisku lokalnym, są one maszyny wirtualne VMware, a firma Contoso chce zreplikowana na platformę Azure.

    ![Przygotowanie infrastruktury — cel ochrony](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, firma Contoso potrzebuje upewnić się, że ukończono Planowanie wdrożenia. Aby upewnić się, wybiera Contoso **tak, ma to zostało zrobione**. W tym wdrożeniu firmy Contoso jest migracja tylko jednej maszyny Wirtualnej, więc nie potrzebuje planowania wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Teraz firmy Contoso musi skonfigurować swoje środowisko źródłowe. Aby skonfigurować swoje środowisko źródłowe, Contoso pobiera szablonu OVF. Firma Contoso używa tego szablonu można wdrożyć serwer konfiguracji i powiązanych z nim składników w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Składniki na serwerze obejmują:

- Serwer konfiguracji, który służy do koordynowania komunikacji między infrastrukturą lokalną i platformą Azure. Serwer konfiguracji zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Serwer przetwarzania:
    - Odbiera dane replikacji.
    - Optymalizuje Data replikacji przy użyciu pamięci podręcznej, kompresji i szyfrowania.
    - Wysyła do usługi Azure Storage datę replikacji.
- Serwer przetwarzania instaluje także usługę Mobility na maszynach wirtualnych, które firma Contoso chce replikacji. Serwer przetwarzania przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.
- Po utworzeniu i uruchomieniu maszyny Wirtualnej serwer konfiguracji Contoso rejestruje serwer w magazynie.

Aby skonfigurować środowisko źródłowe:

1. Contoso — pliki do pobrania szablonu pakietu OVF w witrynie Azure portal (**Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**).
    
    ![Dodawanie serwera konfiguracji](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Contoso importuje szablonu do programu VMware, aby utworzyć i wdrożyć maszynę Wirtualną.

    ![Wdróż szablon OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Gdy firmy Contoso przechodzi na maszynie Wirtualnej po raz pierwszy, maszyna wirtualna uruchamia się w środowisko instalacji systemu Windows Server 2016. Contoso akceptuje Umowę licencyjną i wprowadza hasło administratora.
4. Po zakończeniu instalacji firmy Contoso loguje się do maszyny Wirtualnej jako administrator. Przy pierwszym firmy Contoso loguje się do maszyny Wirtualnej, narzędzie konfiguracji usługi Azure Site Recovery uruchamia się automatycznie.
5. W narzędziu konfiguracji odzyskiwania lokacji Contoso wprowadza imię i nazwisko służące do zarejestrowania serwera konfiguracji w magazynie.
6. Narzędzie sprawdza, czy połączenie maszyny Wirtualnej na platformie Azure. Po nawiązaniu połączenia Contoso wybiera **Zaloguj** zalogować się do subskrypcji platformy Azure. Poświadczenia muszą mieć dostęp do magazynu, w którym jest zarejestrowana na serwerze konfiguracji. 

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie. Firmy Contoso loguje się do komputera ponownie. Kreator zarządzania serwerem konfiguracji jest uruchamiany automatycznie.
8. W Kreatorze Contoso wybiera kartę Sieciową do odbierania ruchu związanego z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
9. Contoso wybiera subskrypcji, grupy zasobów i magazyn usługi Recovery Services, w którym można zarejestrować serwer konfiguracji.

    ![Wybierz magazyn usługi Recovery Services](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Contoso pobiera i instaluje serwer MySQL i interfejs PowerCLI programu VMWare. Następnie Contoso sprawdza poprawność ustawień serwera.
11. Po zakończeniu walidacji firmy Contoso przechodzi nazwy FQDN lub adres IP hosta vSphere lub wystąpienia serwera vCenter. Contoso pozostawia domyślny port i wprowadza nazwę wyświetlaną dla wystąpienia programu vCenter Server na platformie Azure.
12. Firmy Contoso, należy określić konto, którego wcześniej utworzona, aby Usługa Site Recovery może automatycznie odnajdować maszyny wirtualne VMware, które są dostępne dla replikacji. 
13. Firmy Contoso przechodzi poświadczeń, dzięki czemu usługa mobilności jest automatycznie instalowany po włączeniu replikacji. W przypadku maszyn Windows konto musi mieć uprawnienia administratora lokalnego na maszynach wirtualnych. 

    ![Konfigurowanie serwera vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po zakończeniu rejestracji w witrynie Azure portal firmy Contoso sprawdza ponownie serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
8. Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu określonych ustawień. Usługa Site Recovery umożliwia odnalezienie maszyn wirtualnych.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz firma Contoso potrzebuje do konfigurowania środowiska docelowego replikacji:

1. W **przygotowanie infrastruktury** > **docelowej**, Contoso wybiera ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto magazynu i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Źródłowy i docelowy są skonfigurowane, jeśli Contoso tworzy zasady replikacji, a spowoduje skojarzenie zasad z serwerem konfiguracji:

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzy Contoso **ContosoMigrationPolicy** zasad.
2. Firma Contoso używa ustawień domyślnych:
    - **Próg celu punktu odzyskiwania**: domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**: domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**: domyślnej równej 1 godz. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.
 
    ![Zasadami replikacji — tworzenie](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Zasady replikacji - skojarzenia](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Potrzebujesz dodatkowej pomocy?*

- Może odczytywać kompletnym instruktażem następujące kroki w [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Włączanie replikacji

Teraz firma Contoso może rozpocząć replikowanie WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **replikować**, Contoso wybiera ustawienia źródła.
2. Contoso wskazuje, że jej chce włączyć maszyn wirtualnych, wybierze wystąpienie programu vCenter Server i ustawia na serwerze konfiguracji.

    ![Włączanie replikacji — źródło](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Contoso określa ustawienia docelowego, łącznie z grupy zasobów i sieci, w którym maszyna wirtualna platformy Azure zostaną umieszczone po włączeniu trybu failover. Contoso Określa konto magazynu, w którym przechowywane będą replikowane dane.

    ![Włączanie replikacji — docelowy](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Wybiera contoso **WebVM** replikacji. Usługa Site Recovery instaluje usługę mobilności na każdej maszynie Wirtualnej, po włączeniu replikacji. 

    ![Włączanie replikacji — wybierz maszynę Wirtualną](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso — sprawdza, czy wybrano właściwe zasady replikacji. Następnie, umożliwia replikację **WEBVM**. Contoso śledzi postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w witrynie Azure portal firmy Contoso można wyświetlić jego strukturę dla maszyn wirtualnych, które są replikowanych do platformy Azure:

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Potrzebujesz dodatkowej pomocy?*

Może odczytywać kompletnym instruktażem następujące kroki w [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-by-using-the-database-management-service"></a>Krok 6: Migrację bazy danych przy użyciu usługi zarządzania bazy danych

Firma Contoso potrzebuje do tworzenia projektu usługi zarządzania bazy danych, a następnie Migruj bazy danych.

### <a name="create-a-database-management-service-project"></a>Utwórz projekt bazy danych usługi zarządzania

1. Contoso utworzy projekt usługi zarządzania bazy danych. Wybiera contoso **programu SQL Server** typ serwera źródłowego. Wybiera contoso **wystąpienia zarządzanego Azure SQL Database** jako element docelowy.

     ![Składnik Usługa zarządzania bazy danych — nowy projekt migracji](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Zostanie otwarty Kreator migracji.

### <a name="migrate-the-database"></a>Migrowanie bazy danych 

1. W Kreatorze migracji Contoso określa źródłowej maszyny Wirtualnej, na którym znajduje się baza danych w środowisku lokalnym. Firmy Contoso przechodzi poświadczenia, które mają dostęp do bazy danych.

    ![Składnik Usługa zarządzania bazy danych — Szczegóły źródła](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Contoso wybiera bazę danych do migracji (**SmartHotel.Registration**):

    ![Składnik Usługa zarządzania bazy danych — wybierz źródłowe bazy danych](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Dla elementu docelowego firmy Contoso przechodzi nazwę wystąpienia zarządzanego na platformie Azure. Contoso wprowadza poświadczenia dostępu do wystąpienia zarządzanego.

    ![Składnik Usługa zarządzania bazy danych — szczegóły elementu docelowego](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. W **nowe działanie** > **Uruchamianie migracji**, Contoso określa ustawienia, aby uruchomić migrację:
    - Poświadczenia źródłowym i docelowym.
    - Baza danych, aby przeprowadzić migrację.
    - Sieć udostępnianie tej firmy Contoso, utworzone w lokalnej maszyny Wirtualnej. Usługa zarządzania bazy danych trwa tworzenie kopii zapasowych źródła do tego udziału. 
        - Konto usługi, który działa źródłowe wystąpienie programu SQL Server musi mieć uprawnienia do zapisu w tym udziale.
        - Należy użyć nazwy FQDN ścieżkę do udziału.
    - Identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej do migracji bazy danych usługi zarządzania.

        ![Usługę zarządzania bazy danych — Konfigurowanie ustawień migracji](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Contoso zapisuje migracji, a następnie uruchomi go.
6. W **Przegląd**, Contoso monitoruje stan migracji.

    ![Składnik Usługa zarządzania bazy danych — Monitor](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po zakończeniu migracji Contoso sprawdza, czy docelowymi bazami danych istnieją w wystąpieniu zarządzanym.

    ![Bazy danych Usługa zarządzania — weryfikowanie migracji bazy danych](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>Krok 7: Migrowanie maszyny Wirtualnej przy użyciu usługi Site Recovery

Contoso uruchamia szybkie testowanie trybu failover, a następnie przeprowadza migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Przed migracją WEBVM, testowy tryb failover pomaga zapewnić, że wszystko działa zgodnie z oczekiwaniami. Contoso wykona następujące czynności:

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybiera contoso **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**. Po zaznaczeniu tej opcji usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwala przełączenie w tryb failover. Trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 
    1. Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że zostały spełnione wszystkie warunki, które są wymagane do migracji.
    2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli po wybraniu najnowszego punktu odzyskiwania, punkt odzyskiwania jest tworzony z danych.
    3.  Maszyna wirtualna platformy Azure jest tworzona przy użyciu danych przetworzonych w poprzednim kroku.
3. Po zakończeniu pracy w trybie failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Contoso — sprawdza, czy wszystko działa prawidłowo: maszyna wirtualna ma prawidłowy rozmiar, jest ona połączona z odpowiedniej sieci i jest uruchomiona. 
4. Po zweryfikowaniu testowy tryb failover, Contoso czyści przełączenie w tryb failover. Następnie rejestruje i zapisuje wszelkie obserwacje. 

### <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

1. Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Contoso tworzy plan odzyskiwania do migracji. Contoso dodaje WEBVM zgodnie z planem:

     ![Tworzenie planu odzyskiwania — wybierz elementy](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Contoso uruchamia przejścia w tryb failover dla planu. Contoso wybiera najnowszego punktu odzyskiwania. Contoso Określa, czy usługa Site Recovery należy dążyć do zamykania maszyny Wirtualnej w środowisku lokalnym, zanim wyzwala przełączenie w tryb failover.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po włączeniu trybu failover Contoso sprawdza, czy maszyny Wirtualnej platformy Azure jest wyświetlana w oczekiwany sposób w witrynie Azure portal.

   ![Szczegóły planu odzyskiwania](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, firma Contoso kończy migracji, aby zakończyć proces migracji, zatrzymanie replikacji maszyny Wirtualnej i zatrzymać rozliczanie Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover — Kończenie migracji](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatni krok w procesie migracji Contoso aktualizuje parametry połączenia aplikacji, aby wskazywał zmigrowana baza danych, która jest uruchomiona na wystąpieniu zarządzanym firmy Contoso.

1. W witrynie Azure portal firmy Contoso umożliwia znalezienie ciągu połączenia, wybierając **ustawienia** > **parametry połączenia**.

    ![Parametry połączeń](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Contoso ciąg zostaje zaktualizowana o nazwę użytkownika i hasło wystąpienia zarządzanego usługi SQL Database.
3. Po skonfigurowaniu ciąg Contoso zastępuje bieżących parametrów połączenia w pliku web.config w swojej aplikacji.
4. Po aktualizacji pliku i zapisanie go, Contoso powoduje ponowne uruchomienie usług IIS na WEBVM. Ponowne uruchomienie usług IIS, uruchamia Contoso `IISRESET /RESTART` w oknie wiersza polecenia.
5. Po ponownym uruchomieniu usług IIS, aplikacja używa bazy danych, która jest uruchomiona na wystąpieniu zarządzanym usługi SQL Database.
6. W tym momencie Contoso można zamknąć środowiska lokalnego na maszynie SQLVM. Migracja została zakończona.

*Potrzebujesz dodatkowej pomocy?*

- Dowiedz się, jak [testować tryb failover](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Dowiedz się, jak [utworzyć plan odzyskiwania](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Dowiedz się, jak [Failover na platformie Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji aplikacji SmartHotel jest uruchomiona na Maszynie wirtualnej platformy Azure i SmartHotel bazy danych jest dostępna w Azure SQL Database Managed Instance.  

Teraz firmy Contoso musi wykonać następujące zadania oczyszczania:  

- Usuń maszynę WEBVM ze spisu serwerów vCenter.
- Usuń maszyny SQLVM ze spisu serwerów vCenter.
- Usuń WEBVM i SQLVM z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizowanie dokumentacji wewnętrzny, aby wyświetlić nową lokalizację i adres IP dla WEBVM.
- Usuń SQLVM wewnętrznego dokumentacji. Alternatywnie Contoso skorygować z dokumentacją, aby pokazać SQLVM jako usunięty i nie jest już na maszynie wirtualnej i spisu.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi. Zaktualizuj wszystkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczania jego nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso przeglądy maszyn wirtualnych platformy Azure i wystąpienie zarządzane usługi SQL Database, aby sprawdzić, czy wystąpiły problemy z zabezpieczeniami z jego implementacją:

- Zespół ds. zabezpieczeń przegląda sieciowych grup zabezpieczeń, które są używane do kontrolowania dostępu do maszyny Wirtualnej. Zabezpieczenia sieciowe grupy pomagają upewnić się, że tylko ruchu, który jest dozwolony w aplikacji można przekazać.
- Zespół ds. zabezpieczeń firmy Contoso również polega na uwzględnieniu Zabezpieczanie danych na dysku za pomocą usługi Azure Disk Encryption i usługi Azure Key Vault.
- Zespół ds. zabezpieczeń umożliwia wykrywanie zagrożeń na wystąpieniu zarządzanym. Wykrywanie zagrożeń wysyła alert do firmy Contoso zabezpieczeń zespołu/usługi technicznej systemu, aby otworzyć bilet, jeśli zostanie wykryte zagrożenie. Dowiedz się więcej o [wykrywanie dla zarządzanego wystąpienia zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Zarządzane zabezpieczenia wystąpienia — wykrywanie zagrożeń](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Aby dowiedzieć się więcej na temat praktyk w zakresie zabezpieczeń dla maszyn wirtualnych, zobacz [najlepsze rozwiązania dotyczące obciążeń IaaS na platformie Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="backups"></a>Tworzenie kopii zapasowych

Contoso tworzy kopie zapasowe danych na WEBVM przy użyciu usługi Azure Backup. Dowiedz się więcej o [kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Firma Contoso ma istniejących licencji na program WEBVM. Aby skorzystać z ceny dzięki korzyści użycia hybrydowego platformy Azure, firma Contoso konwertuje istniejącej maszyny Wirtualnej platformy Azure.
- Contoso umożliwia usłudze Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. Cost Management to rozwiązanie do zarządzania kosztami wielu chmur, które pomaga Contoso i zarządzać platformy Azure i innych zasobów w chmurze. Dowiedz się więcej o [usługi Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehostowaniu aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji aplikacji frontonu maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. Contoso lokalnej bazy danych jest migrowana do wystąpienia usługi Azure SQL Database zarządzane przy użyciu usługi zarządzania bazą danych Azure.

## <a name="next-steps"></a>Kolejne kroki

W następnym artykule z tej serii Contoso [rehostowaniu SmartHotel aplikacji na maszynach wirtualnych Azure](contoso-migration-rehost-vm.md) przy użyciu usługi Azure Site Recovery.

