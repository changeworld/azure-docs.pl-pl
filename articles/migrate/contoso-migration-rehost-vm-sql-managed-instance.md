---
title: Ponowne hostowanie aplikacji w środowisku lokalnym firmy Contoso, migrując maszyny wirtualne platformy Azure i wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu aplikację w środowisku lokalnym, na maszynach wirtualnych platformy Azure, a za pomocą wystąpienia zarządzanego Azure SQL Database.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 832614c46f0269460245d081f20897b591e31fce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60671303"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migracja Contoso: Ponowne hostowanie aplikacji w środowisku lokalnym na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database

W tym artykule Contoso przeprowadza migrację swoich aplikacji rozwiązania SmartHotel360 maszyny Wirtualnej frontonu na Maszynie wirtualnej platformy Azure przy użyciu usługi Azure Site Recovery. Contoso wykonuje także migrację bazy danych aplikacji do wystąpienia zarządzanego Azure SQL Database.

> [!NOTE]
> Wystąpienie usługi Azure SQL Database Managed jest obecnie w wersji zapoznawczej.

Ten artykuł stanowi jeden w serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje i szereg scenariuszy, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania. Artykuły zostaną dodane do serii wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md) | Contoso uruchamia oceny jego SmartHotel aplikacja dwuwarstwowa lokalnych działających z oprogramowaniem VMware. Contoso ocenia maszyn wirtualnych aplikacji przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi. Contoso ocenia aplikacji bazy danych programu SQL Server przy użyciu [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji SmartHotel w środowisku lokalnym. Contoso aplikacja jest migrowana maszyna wirtualna frontonu za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso przeprowadzanie migracji aplikacji baz danych do wystąpienia usługi Azure SQL Database Managed przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | W tym artykule
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso app jego SmartHotel maszyn wirtualnych jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso kończy lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso przeprowadza migrację swoich aplikacji SmartHotel do aplikacji sieci web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure. | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso przeprowadza migrację swoich aplikacji osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu lokacjach. Aplikacja sieci web jest zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzuj Team Foundation Server w usługach Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migruje jej wdrożenia serwera Team Foundation Server w środowisku lokalnym przy użyciu funkcji migracji do usługi Azure DevOps usług na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure, a następnie rearchitects aplikacji. Contoso rearchitects warstwy sieci web aplikacji jako kontener, Windows i rearchitects bazy danych aplikacji przy użyciu usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services i usługi Azure Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne




Możesz pobrać przykładową aplikację rozwiązania SmartHotel360, która jest używana w tym artykule [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Czynniki biznesowe

Firmy Contoso IT zespół kierowniczy pracował ściśle zintegrowana z partnerami biznesowymi firmy, aby zrozumieć, co firma chce osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie. Co w efekcie zwiększa wykorzystanie w systemach lokalnych i infrastruktury w firmie.
- **Zwiększenie wydajności**: Firma Contoso potrzebuje, usuń zbędne procedur i usprawniać procesy dla jego deweloperów i użytkowników. Potrzeby biznesowe IT do szybkiego i do nie odpadów czas i pieniądze, więc firma może szybciej na wymagania klientów.
- **Zwiększenie elastyczności**:  Contoso IT trzeba zwiększyć szybkość reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany, które występują w witrynie marketplace termin firmy im odnosić sukcesy w globalnej gospodarki reagować. IT w firmie Contoso nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: Jak pomyślnie rozwoju działalności firmy Contoso IT, należy podać systemów, które można rozwijać w tym samym tempie.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury Contoso zidentyfikowała cele tej migracji. Firma używa celów migracji, aby określić najlepszą metodę migracji.

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, które aplikacja ma już dziś w środowisku VMWare lokalne firmy Contoso. Przejście do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Contoso nie chcesz inwestować w aplikacji. Aplikacja jest ochrona kluczowych i ważnych dla firmy, ale firma Contoso chce po prostu przenieść aplikację w obecnej formie do chmury.
- Zadania administracyjne bazy danych należy zminimalizować, po przeprowadzeniu migracji aplikacji.
- Contoso nie chcesz używać usługi Azure SQL Database dla tej aplikacji. Szuka alternatyw.


## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół swoje cele i wymagania, Contoso projektuje i przeglądy rozwiązanie wdrażania rozwiązania i identyfikuje proces migracji, w tym usług platformy Azure, który będzie używany do migracji.

### <a name="current-architecture"></a>Bieżącej architektury 

- Firma Contoso ma jednego głównego centrum danych (**contoso-datacenter**). Centrum danych znajduje się w mieście z nowego Jorku w wschodnich Stanach Zjednoczonych.
- Firma Contoso ma trzy dodatkowe gałęzie lokalne na terenie Stanów Zjednoczonych.
- Główne centrum danych jest połączony z Internetem za pomocą fiber połączenia Metro Ethernet (500 MB/s).
- Każdej gałęzi jest połączony z Internetem lokalnie przy użyciu klasy biznesowej połączeń przy użyciu tuneli IPsec sieci VPN, wróć do głównego centrum danych. Ustawienia umożliwia całej sieci firmy Contoso do podłączenia trwale i optymalizuje łączności z Internetem.
- Główne centrum danych jest w pełni zwirtualizowany z oprogramowaniem VMware. Firma Contoso ma dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez program vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami. Firma Contoso używa serwerów DNS w sieci wewnętrznej.
- Firma Contoso ma lokalnego kontrolera domeny (**contosodc1**).
- Kontrolerach domeny jest uruchomiony na maszynach wirtualnych VMware. Kontrolery domeny w lokalnej gałęzi, uruchom na serwerach fizycznych.
- Aplikacji rozwiązania SmartHotel360 jest warstwowa między dwiema maszynami wirtualnymi (**WEBVM** i **SQLVM**), znajdują się na hoście VMware ESXi wersji 6.5 (**contosohost1.contoso.com**). 
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomiona na maszynie Wirtualnej.

![Bieżący architektura firmy Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>Proponowana architektury

W tym scenariuszu firma Contoso chce migrację swoich aplikacji turystycznej dwuwarstwowej w środowisku lokalnym w następujący sposób:

- Migracja bazy danych aplikacji (**SmartHotelDB**) do wystąpienia zarządzanego bazy danych SQL Azure.
- Migrowanie frontonu **WebVM** na Maszynie wirtualnej platformy Azure.
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>Zagadnienia dotyczące bazy danych

Częścią procesu projektowania rozwiązania firmy Contoso zostały porównanie funkcji usługi Azure SQL Database i wystąpienie zarządzane usługi SQL Server. Następujące zagadnienia dotyczące pomogła ich do określania, do korzystania z wystąpienia zarządzanego.

- Zarządzane wystąpienia ma na celu dostarczać prawie 100% zgodności z najnowszą wersją programu SQL Server w środowisku lokalnym. Firma Microsoft zaleca wystąpienia zarządzanego, klienci korzystający z programu SQL Server w środowisku lokalnym lub na maszynie Wirtualnej IaaS, kto chce migrować swoje aplikacje do w pełni zarządzana usługa, za pomocą zmian projektowych minimalny.
- Firma Contoso jest planowania migracji dużej liczby aplikacji ze środowiska lokalnego do IaaS. Wiele z nich są dostarczane niezależnego dostawcy oprogramowania. Contoso zdaje sobie sprawę, że za pomocą wystąpienia zarządzanego usługi pomagają w zapewnieniu zgodności bazy danych dla tych aplikacji, a nie przy użyciu bazy danych SQL, które mogą nie być obsługiwane.
- Firma Contoso może po prostu przeprowadzić migrację lift-and-shift do wystąpienia zarządzanego przy użyciu w pełni zautomatyzowane Data Migration Service (DMS). W tej usłudze w miejscu Contoso można użyć go ponownie dla migracji bazy danych w przyszłości.
- Wystąpienie zarządzane usługi SQL obsługuje programu SQL Server Agent, który jest ważnym zagadnieniem w przypadku aplikacji rozwiązania SmartHotel360. Firma Contoso potrzebuje tego zgodności, w przeciwnym razie będzie konieczne zmodyfikowanie planów konserwacji, wymagane przez aplikację.
- Z pakietem Software Assurance Contoso mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na wystąpienie zarządzane SQL Database za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server. Dzięki temu firmy Contoso zapisać do 30% na wystąpieniu zarządzanym.
- Wystąpienia zarządzanego w pełni znajduje się w sieci wirtualnej, dzięki czemu zapewnia wysoki poziom izolacji i zabezpieczeń danych firmy Contoso. Contoso można uzyskać z zalet chmury publicznej, przy jednoczesnym zachowaniu środowisko izolowane w publicznym Internecie.
- Zarządzane wystąpienie obsługuje wiele funkcji zabezpieczeń, w tym maskowania danych zawsze zaszyfrowane, dynamiczne zabezpieczenia na poziomie wiersza i wykrywania zagrożeń.


### <a name="solution-review"></a>Przegląd rozwiązania

Contoso ocenia proponowane projektu poprzez umieszczenie razem listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** |  WEBVM zostaną przeniesione na platformę Azure bez konieczności wprowadzania zmian, tworzenie prostego migracji.<br/><br/> Wystąpienie zarządzane usługi SQL obsługuje wymagania techniczne i cele firmy Contoso.<br/><br/> Wystąpienie zarządzane zapewnia 100% zgodności z ich bieżącego wdrożenia podczas przenoszenia ich od programu SQL Server 2008 R2.<br/><br/>  Mogą one używać swoich inwestycji w pakiet Software Assurance i za pomocą korzyści użycia hybrydowego platformy Azure forSQL Server i Windows Server.<br/><br/> Można ponownie użyć Database Migration Service na potrzeby dodatkowych migracji w przyszłości.<br/><br/> Wystąpienie zarządzane usługi SQL ma wbudowane odporności na uszkodzenia, która nie ma konieczności konfiguruje firmy Contoso. Zapewnia to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | WEBVM jest uruchomiony system Windows Server 2008 R2.  Mimo że ten system operacyjny jest obsługiwany przez platformę Azure, nie jest już obsługiwana platforma. [Dowiedz się więcej](https://support.microsoft.com/en-us/help/956893).<br/><br/> Warstwa sieci web pozostanie pojedynczy punkt przejścia w tryb failover przy użyciu tylko WEBVM dostarczanie usług.<br/><br/> Contoso należy kontynuować obsługi warstwy aplikacji sieci web, jako Maszynę wirtualną, zamiast przenosić do zarządzanej usługi, takie jak usługa Azure App Service.<br/><br/> Dla warstwy danych wystąpienia zarządzanego może nie być najlepszym rozwiązaniem Contoso dostosowania systemu operacyjnego lub serwera bazy danych, lub jeśli chcesz uruchamiać aplikacje innych firm, wraz z programu SQL Server. Uruchamianie programu SQL Server na maszynie Wirtualnej IaaS może dostarczyć Ta elastyczność. 

### <a name="migration-process"></a>Proces migracji

Contoso zmigruje warstwy sieci web i danych w swojej aplikacji rozwiązania SmartHotel360 na platformie Azure, wykonując następujące kroki:

1. Firma Contoso ma już swoją infrastrukturę platformy Azure w miejscu, więc po prostu musi dodać kilka określonych składników platformy Azure, w tym scenariuszu.
2. Warstwa danych zostaną poddane migracji za pomocą programu Data Migration Service. Łączy się maszynę Wirtualną w środowisku lokalnym SQL Server z usługi migracji danych przez połączenie VPN lokacja lokacja między centrum danych firmy Contoso i platformą Azure. Następnie Data Migration Service przeprowadza migrację bazy danych.
3. Warstwa sieci web zostanie zmigrowana przy użyciu migracji lift-and-shift przy użyciu usługi Site Recovery. Proces wymaga ich przygotowywanie w lokalnym środowisku VMware, konfigurowanie włączenie replikacji i migracja maszyn wirtualnych przez awaryjną na platformie Azure.

     ![Architekturę migracji](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Usługi platformy Azure

Usługa | Opis | Koszty
--- | --- | ---
[Usługa migracji bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Usługa migracji bazy danych umożliwia bezproblemową migrację z wielu źródłowych baz danych do platformy danych Azure przy minimalnych przestojach. | Dowiedz się więcej o [obsługiwane regiony](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) i [cennik usługi Database Migration Service](https://azure.microsoft.com/pricing/details/database-migration/).
[Wystąpienie zarządzane usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Wystąpienie zarządzane to usługa zarządzana baza danych, która reprezentuje w pełni zarządzane wystąpienia programu SQL Server w chmurze platformy Azure. Używa tego samego kodu jako najnowszą wersję aparatu bazy danych programu SQL Server i zawierają najnowsze funkcje, ulepszeń wydajności i poprawek zabezpieczeń. | Za pomocą wystąpienia zarządzanego SQL Database działających na platformie Azure spowoduje naliczenie opłaty na podstawie pojemności. Dowiedz się więcej o [wystąpieniu zarządzanym kalkulacji cen](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa Site Recovery organizuje i zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i opłaty za operacje po przejściu do trybu failover. Dowiedz się więcej o [Site Recovery, opłaty i ceny](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="prerequisites"></a>Wymagania wstępne

Contoso i inni użytkownicy muszą spełniać następujące wymagania wstępne dotyczące tego scenariusza:

Wymagania | Szczegóły
--- | ---
**Zarejestruj się w wersji zapoznawczej wystąpienia zarządzanego** | Użytkownik musi zostać zarejestrowany w wystąpieniu zarządzanym bazy danych SQL ograniczonej publicznej wersji zapoznawczej. Musisz mieć subskrypcję platformy Azure do [Zarejestruj](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Rejestracja może potrwać kilka dni, aby ukończyć, dlatego upewnij się się zarejestrować, przed rozpoczęciem wdrażania tego scenariusza.
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem subskrypcji, należy skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zobacz [zarządzanie dostępem Site Recovery za pomocą kontroli dostępu opartej na rolach](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Usługa Site Recovery (lokalnego)** | Wystąpienie serwera vCenter w środowisku lokalnym powinna działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Host ESXi w wersji 5.5, 6.0 lub 6.5<br/><br/> Co najmniej jeden maszyn wirtualnych programu VMware działające na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.
**Usługa migracji bazy danych** | Usługa migracji bazy danych należy [zgodne lokalnego urządzenia sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Należy skonfigurować lokalne urządzenie sieci VPN. Musi mieć publiczny adres IPv4 dołączonej do Internetu. Adres nie może znajdować się za urządzeniem translatora adresów Sieciowych.<br/><br/> Upewnij się, że masz dostęp do lokalnej bazy danych programu SQL Server.<br/><br/> Zapora Windows powinien móc dostęp do aparatu bazy danych źródłowych. Dowiedz się, jak [skonfigurować zaporę Windows dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> W przypadku zapory przed komputerze bazy danych, Dodaj reguły, aby zezwolić na dostęp do bazy danych i plików za pośrednictwem portu 445 SMB.<br/><br/> Poświadczenia, które są używane, aby nawiązać połączenie z wystąpieniem programu SQL Server i obiektu docelowego wystąpienia zarządzanego muszą być członkami roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnianie w sieci lokalnej bazy danych usługi migracji bazy danych umożliwia tworzenie kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że konto usługi, uruchamiające źródłowe wystąpienie programu SQL Server ma uprawnienia do zapisu w udziale sieciowym.<br/><br/> Zanotuj Windows użytkownika i hasło, które ma uprawnienia pełnej kontroli w udziale sieciowym. Usługa migracji bazy danych personifikuje te poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage.<br/><br/> Proces instalacji programu SQL Server Express protokół TCP/IP ustawia **wyłączone** domyślnie. Upewnij się, że jest włączone.

## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak firma Contoso zamierza skonfigurowania wdrożenia:

> [!div class="checklist"]
> * **Krok 1. Skonfiguruj wystąpienie zarządzane bazy danych SQL**: Firma Contoso potrzebuje wstępnie utworzone wystąpienie zarządzane do którego zostanie przeprowadzona migracja bazy danych programu SQL Server w środowisku lokalnym.
> * **Krok 2. Przygotowanie Database Migration Service**: Contoso należy zarejestrować dostawcę migracji bazy danych, Utwórz wystąpienie, a następnie utwórz projekt usługi Database Migration Service. Contoso również należy zdefiniować sygnatury dostępu współdzielonego (SAS) identyfikator (URI) dla usługi migracji bazy danych. Identyfikator URI sygnatury dostępu Współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu firmy Contoso, więc Contoso można przyznać dostęp jest ograniczony do magazynu obiektów. Contoso konfiguruje identyfikatora URI połączenia SAS, dzięki czemu usługa migracji bazy danych mogą uzyskiwać dostęp do kontenera konta magazynu, do którego usługa przekazuje te pliki kopii zapasowej programu SQL Server.
> * **Krok 3. Przygotowywanie platformy Azure dla usługi Site Recovery**: Firmy Contoso musi utworzyć konto magazynu do przechowywania replikowanych danych dla usługi Site Recovery. On również utworzyć magazyn usługi Azure Recovery Services.
> * **Krok 4. Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji**: Contoso przygotuje konta dla maszyny Wirtualnej odnajdywanie i zainstalować agenta nawiązać połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 5. Replikowanie maszyn wirtualnych**: Aby skonfigurować replikację, Contoso konfigurowania środowisk źródłowych i docelowych odzyskiwania lokacji, konfiguruje zasady replikacji i rozpoczyna się replikowanie maszyn wirtualnych do usługi Azure Storage.
> * **Krok 6: Migrowanie bazy danych za pomocą Database Migration Service**: Contoso przeprowadza migrację bazy danych.
> * **Krok 7: Migrowanie maszyn wirtualnych przy użyciu usługi Site Recovery**: Contoso uruchamia test trybu failover, aby sprawdzić, czy wszystko działa. Następnie Contoso uruchamia tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Krok 1: Przygotowanie wystąpienie zarządzane bazy danych SQL

Aby skonfigurować bazę danych wystąpienia zarządzanego Azure SQL, firma Contoso potrzebuje podsieci, która spełnia następujące wymagania:

- Podsieć musi być w wersji dedykowanej. Może być pusta i nie może zawierać inne usługi w chmurze. Podsieci nie może być podsieć bramy.
- Po utworzeniu wystąpienia zarządzanego Contoso nie należy dodawać zasoby do podsieci.
- Podsieci nie może mieć skojarzonych z nią sieciową grupę zabezpieczeń.
- Podsieć musi mieć zdefiniowany przez użytkownika (UDR) trasy tabelę routingu. Powinny być tylko trasy przypisane Internetu dla następnego przeskoku 0.0.0.0/0. 
- Opcjonalne niestandardowe DNS: Jeśli niestandardowe DNS jest określony w sieci wirtualnej platformy Azure, adres IP platformy Azure rekursywnego rozpoznawania nazw (na przykład 168.63.129.16) należy dodać do listy. Dowiedz się, jak [Konfigurowanie niestandardowych pozycji DNS dla wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsieci nie może być punkt końcowy usługi (magazyn lub SQL) skojarzonych z nim. Punkty końcowe usługi powinny być wyłączone w sieci wirtualnej.
- Podsieć musi mieć co najmniej 16 adresów IP. Dowiedz się, jak [rozmiar podsieci wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration).
- W środowisku hybrydowym firmy Contoso niestandardowych ustawień DNS są wymagane. Contoso konfiguruje ustawienia DNS, użyj jednego lub kilku serwerów usługi Azure DNS firmy. Dowiedz się więcej o [dostosowywania DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurowanie sieci wirtualnej dla wystąpienia zarządzanego

Administratorzy firmy Contoso Skonfiguruj sieć wirtualną w następujący sposób: 

1. Tworzą nową sieć wirtualną (**EU2-VNET-SQLMI**) w regionie wschodnie stany USA 2 podstawowym. Dodaje sieci wirtualnej, aby **ContosoNetworkingRG** grupy zasobów.
2. Przestrzeń adresowa 10.235.0.0/24 on przypisać. Pozwalają zagwarantować, że zakres nie pokrywa się z innymi sieciami, w swojej organizacji.
3. Dwie podsieci ich dodać do sieci:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Ta podsieć jest używana do dołączenia w katalogu do wystąpienia zarządzanego.

      ![Zarządzane wystąpienia — tworzenie sieci wirtualnej](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. Po wdrożeniu sieci wirtualnej i podsieci są komunikację równorzędną sieci w następujący sposób:

    - Elementy równorzędne **EUS2-VNET-SQLMI** z **VNET-HUB-EUS2** (centralnej sieci wirtualnej dla wschodnie stany USA 2).
    - Elementów równorzędnych **EUS2-VNET-SQLMI** z **VNET-PROD-EUS2** (produkcyjnego).

      ![Komunikacja równorzędna w sieci](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Ustawiają niestandardowych ustawień DNS. DNS wskazuje najpierw kontrolery domeny systemu Azure firmy Contoso. System DNS Azure jest dodatkowej. Kontrolery domeny Contoso Azure znajdują się w następujący sposób:

    - Na terenie **EUS2-PROD-DC** podsieci w sieci produkcyjnej wschodnie stany USA 2 (**VNET-PROD-EUS2**)
    - **CONTOSODC3** adresu: 10.245.42.4
    - **CONTOSODC4** adresu: 10.245.42.5
    - Usługa Azure program rozpoznawania nazw DNS: 168.63.129.16

      ![Serwery DNS w sieci](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Potrzebujesz dodatkowej pomocy?*

- Zapoznaj się z omówieniem programu [wystąpienie zarządzane usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Dowiedz się, jak [tworzenie sieci wirtualnej dla wystąpienia zarządzanego SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration).
- Dowiedz się, jak [skonfigurować komunikację równorzędną](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Dowiedz się, jak [aktualizowanie ustawień DNS usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Konfigurowanie routingu

Wystąpienie zarządzane jest umieszczany w prywatnej sieci wirtualnej. Firma Contoso potrzebuje tabelę tras dla sieci wirtualnej do komunikowania się z usługą zarządzania platformy Azure. Jeśli sieć wirtualna nie może komunikować się z usługą, który zarządza nim, sieć wirtualna stanie się niedostępna.

Contoso uwzględnia następujące czynniki:

- Tabela tras zawiera zestaw reguł (trasy), które określają, jak w sieci wirtualnej, w którym powinny być kierowane pakiety wysyłane z wystąpieniem zarządzanym.
- Tabela tras jest skojarzony z podsieci, w których są wdrożone wystąpienia zarządzanego. Każdy pakiet, który opuszcza podsieć odbywa się na podstawie tabeli tras skojarzone.
- Podsieć może być skojarzona z tabelą tras tylko jeden.
- Nie istnieją żadne dodatkowe opłaty, do tworzenia tabel tras w systemie Microsoft Azure.

  Aby skonfigurować routing Contoso administratorów, wykonaj następujące czynności:

1. Tworzą tabeli trasy zdefiniowanej przez użytkownika (trasy) w **ContosoNetworkingRG** grupy zasobów.

    ![Tabela tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Zgodnie z wymaganiami wystąpienia zarządzanego, pod tabelą tras (**MIRouteTable**) jest wdrażana, Dodaj trasę, która ma prefiks adresu 0.0.0.0/0. **Typu następnego przeskoku** ustawiono opcję **Internet**.

    ![Prefiks tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. skojarzenia tabeli trasy z **SQLMI-DB-EUS2** podsieci (w **EUS2-VNET-SQLMI** sieci). 

    ![Podsieci tabeli tras](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [Konfigurowanie tras dla zarządzanego wystąpienia](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

### <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Teraz firmy Contoso, Administratorzy mogą aprowizować wystąpienie zarządzane bazy danych SQL:

1. Ponieważ wystąpienia zarządzanego służy aplikacja biznesowa, wdrażanych w regionie wschodnie stany USA 2 z głównej firmy wystąpienia zarządzanego. Dodają do wystąpienia zarządzanego **ContosoRG** grupy zasobów.
2. Wybierają cen warstwy, rozmiar obliczeń i magazynu dla tego wystąpienia. Dowiedz się więcej o [wystąpieniu zarządzanym kalkulacji cen](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Po wdrożeniu wystąpienia zarządzanego dwa nowe zasoby są wyświetlane w **ContosoRG** grupy zasobów:

    - Klaster wirtualny w przypadku firmy Contoso ma wiele wystąpień zarządzanych.
    - Wystąpienie zarządzane bazy danych programu SQL Server. 

      ![Wystąpienie zarządzane](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [aprowizowania wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>Krok 2: Przygotowywanie usługi migracji bazy danych

Aby przygotować usługę migracji bazy danych, Administratorzy Contoso należy wykonać kilka czynności:

- Zarejestruj dostawcę usługi Database Migration Service na platformie Azure.
- Udostępnij Database Migration Service dostęp do usługi Azure Storage służącego do przekazywania plików kopii zapasowej, które są używane do migracji bazy danych. Aby zapewnić dostęp do usługi Azure Storage, tworzą kontenera usługi Azure Blob storage. Generują identyfikator URI sygnatury dostępu Współdzielonego dla kontenera magazynu obiektów Blob. 
- Utwórz projekt usługi Database Migration Service.

Następnie należy wykonać następujące czynności:

1. One Zarejestruj dostawcę migracji bazy danych w ramach swojej subskrypcji.
    ![Usługa migracji bazy danych — Zarejestruj się](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Tworzą kontenera magazynu obiektów Blob. Contoso generuje identyfikator URI sygnatury dostępu Współdzielonego, aby usługa migracji bazy danych do niego dostęp.

    ![Usługę migracji bazy danych — Generowanie identyfikatora URI sygnatury dostępu Współdzielonego](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Tworzą wystąpienie usługi Database Migration Service. 

    ![Usługę migracji bazy danych — tworzenie wystąpienia](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Umieść one wystąpienie usługi Database Migration Service w **EUS2-PROD-DC** podsieci **sieć wirtualna-PROD-DC — EUS2** sieci wirtualnej.
    - Usługa migracji bazy danych jest umieszczany w tym miejscu, ponieważ usługa musi znajdować się w sieci wirtualnej, które mogą uzyskiwać dostęp do maszyny Wirtualnej serwera SQL w środowisku lokalnym za pośrednictwem bramy sieci VPN.
    - **VNET-PROD-EUS2** jest połączona z **VNET-HUB-EUS2** i będzie mógł korzystać z bram zdalnych. **Użyj bram zdalnych** opcja gwarantuje, że usługa migracji bazy danych może komunikować się zgodnie z potrzebami.

        ![Usługę migracji bazy danych — Konfigurowanie sieci](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Potrzebujesz dodatkowej pomocy?*

- Dowiedz się, jak [Konfigurowanie Database Migration Service](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Dowiedz się, jak [tworzenie i używanie sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Krok 3: Przygotowywanie platformy Azure dla usługi Site Recovery

Kilka elementów platformy Azure są wymagane dla firmy Contoso skonfigurować Site Recovery do migracji jej maszyna wirtualna w warstwie sieci web (**WEBMV**):

- Sieć wirtualna, w którym znajdują się zasoby w trybie failed-over.
- Konto magazynu do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.

Administratorzy firmy Contoso skonfiguruj Site Recovery w następujący sposób:

1. Ponieważ maszyna wirtualna ma fronton sieci web do aplikacji rozwiązania SmartHotel360, firmy Contoso przechodzi w trybie Failover na maszynie Wirtualnej i jej istniejącej sieci produkcyjnych (**VNET-PROD-EUS2**) i podsiecią (**PROD-FE-EUS2**). Sieci i podsieci znajdują się w regionie wschodnie stany USA 2 podstawowym. Contoso konfigurowania sieci podczas jego [wdrożona infrastruktura platformy Azure](contoso-migration-infrastructure.md).
2. One tworzenie konta magazynu (**contosovmsacc20180528**). Firma Contoso używa konta ogólnego przeznaczenia. Contoso wybiera magazynu w warstwie standardowa i replikacją magazyn lokalnie nadmiarowy.

    ![Site Recovery — Tworzenie konta magazynu](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Za pomocą konta magazynu i sieci w miejscu tworzą magazyn (**ContosoMigrationVault**). Contoso miejsca magazynu w **ContosoFailoverRG** grupę zasobów, w regionie wschodnie stany USA 2 podstawowym.

    ![Recovery Services — utwórz magazyn](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [Konfigurowanie platformy Azure dla usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji

Do przygotowania odzyskiwania lokacji programu VMware, Administratorzy Contoso wykonać te zadania:

- Przygotowywanie konta na vCenter Server wystąpienia lub hosta vSphere ESXi. Konto automatyzuje proces odnajdowania maszyn wirtualnych.
- Przygotowywanie konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware, który firma Contoso chce replikacji.
- Przygotowywanie lokalnych maszyn wirtualnych, połączyć się z maszynami wirtualnymi platformy Azure po ich utworzeniu po włączeniu trybu failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagany jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Firma Contoso potrzebuje konta, które można uruchamiać operacje, takie jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Administratorzy firmy Contoso, skonfigurować konto, wykonując następujące zadania:

1. Tworzy rolę na poziomie vCenter.
2. Przypisuje wymaganych uprawnień do tej roli.

*Potrzebujesz dodatkowej pomocy?*

Dowiedz się, jak [tworzenie i przypisywanie roli do automatycznego odnajdowania](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi mobilności

Usługa Mobility musi być zainstalowana na maszynie Wirtualnej, który firma Contoso chce replikacji. Contoso uwzględnia te czynniki dotyczące usługi mobilności:

- Site Recovery może zrobić wypychania automatycznego instalowania tego składnika, gdy Contoso umożliwia replikację dla maszyny Wirtualnej.
- Instalacja wypychania automatycznego Contoso należy przygotować konta, które używa Usługa Site Recovery, aby dostęp do maszyny Wirtualnej.
- To konto określono podczas replikacji jest skonfigurowany w konsoli platformy Azure.
- Firmy Contoso musi mieć domeny lub lokalnego konta z uprawnieniami do instalowania na maszynie Wirtualnej.

*Potrzebujesz dodatkowej pomocy*

Dowiedz się, jak [Tworzenie konta na potrzeby instalacji wypychanej usługi mobilności](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover na platformie Azure Contoso chce można było połączyć się z replikowanymi maszynami wirtualnymi na platformie Azure. Połączyć się z replikowanymi maszynami wirtualnymi na platformie Azure, Contoso Administratorzy muszą wykonać kilka zadań na lokalną maszynę Wirtualną, przed rozpoczęciem migracji: 

1. Aby uzyskać dostęp za pośrednictwem Internetu umożliwiają one protokołu RDP na maszynie Wirtualnej w środowisku lokalnym przed włączeniem trybu failover. Pozwalają zagwarantować, że reguły TCP i UDP zostały dodane do **publicznych** profilu i że protokołu RDP jest dozwolona w **zapory Windows** > **dozwolone aplikacje** we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN lokacja lokacja firmy Contoso ich Włącz protokół RDP na maszynie lokalnej. Umożliwiają one protokołu RDP w systemie **zapory Windows** > **dozwolone aplikacje i funkcje** dla **domena i prywatne** sieci.
3. Ustawiają zasady sieci SAN systemu operacyjnego na maszynie Wirtualnej w środowisku lokalnym **OnlineAll**.

Administratorzy firmy Contoso konieczne sprawdzenie następujących kwestii, po przejściu w tryb failover:

- Powinno być nie oczekujących aktualizacji Windows na maszynie Wirtualnej po wyzwoleniu przejścia w tryb failover. W przypadku oczekujących aktualizacji Windows Contoso nie logowania do maszyny wirtualnej do momentu ukończenia aktualizacji.
- Po przejściu w tryb failover należy sprawdzić Administratorzy **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie będą mogli zobaczyć diagnostykę rozruchu, należy sprawdzić czy maszyna wirtualna jest uruchomiona, a następnie przejrzyj [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>Krok 5. Replikowanie lokalnych maszyn wirtualnych na platformę Azure

Przed uruchomieniem migracji na platformę Azure, Contoso, Administratorzy muszą skonfigurować i włączyć replikację dla maszyn wirtualnych w środowisku lokalnym.

### <a name="set-a-replication-goal"></a>Ustaw cel replikacji

1. W magazynie w obszarze nazwy magazynu (**ContosoVMVault**), ustawiają cel replikacji (**wprowadzenie** > **Site Recovery**  >   **Przygotowanie infrastruktury**).
2. Określają, czy maszyny są znajdujących się lokalnie, że są one maszyny wirtualne VMware, replikowanych do platformy Azure.

    ![Przygotowanie infrastruktury — cel ochrony](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, Administratorzy firmy Contoso, upewnij się, że ich ukończono Planowanie wdrożenia. Wybierają **tak, ma to zostało zrobione**. W tym wdrożeniu firmy Contoso jest migracja tylko jednej maszyny Wirtualnej, planowania wdrożenia nie jest wymagane.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Teraz Administratorzy Contoso skonfigurować środowisko źródłowe. Aby skonfigurować swoje środowisko źródłowe, mogą pobrać szablon OVF i umożliwiają wdrażanie serwera konfiguracji i powiązanych z nim składników w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Składniki na serwerze obejmują:

- Serwer konfiguracji, który służy do koordynowania komunikacji między infrastrukturą lokalną i platformą Azure. Serwer konfiguracji zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Serwer przetwarzania:
    - Odbiera dane replikacji.
    - Optymalizuje Data replikacji przy użyciu pamięci podręcznej, kompresji i szyfrowania.
    - Wysyła do usługi Azure Storage datę replikacji.
- Serwer przetwarzania instaluje także usługę Mobility na maszynach wirtualnych, które będą replikowane. Serwer przetwarzania przeprowadza automatyczne odnajdywanie lokalnych maszyn wirtualnych programu VMware.
- Po utworzeniu i uruchomieniu maszyny Wirtualnej serwer konfiguracji Contoso rejestruje serwer w magazynie.

Aby skonfigurować źródła administratorów Contoso środowiska, wykonaj następujące czynności:

1. Szablon OVF będą oni mogli pobrać z witryny Azure portal (**Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**).
    
    ![Dodawanie serwera konfiguracji](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. One zaimportuj szablon do programu VMware, aby utworzyć i wdrożyć maszynę Wirtualną.

    ![Wdróż szablon OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Po włączeniu maszyny Wirtualnej po raz pierwszy, rozpoczyna się proces instalacji systemu Windows Server 2016. One Zaakceptuj Umowę licencyjną i wprowadza hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Podczas pierwszego czasu logowania narzędzie konfiguracji usługi Azure Site Recovery uruchamia się automatycznie.
5. W narzędziu konfiguracji odzyskiwania lokacji wejdzie nazwę, aby użyć, aby zarejestrować serwer konfiguracji w magazynie.
6. Narzędzie sprawdza, czy połączenie maszyny Wirtualnej na platformie Azure. Po nawiązaniu połączenia wybierają **Zaloguj** zalogować się do subskrypcji platformy Azure. Poświadczenia muszą mieć dostęp do magazynu, w którym jest zarejestrowana na serwerze konfiguracji. 

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie. Logowania do komputera ponownie. Kreator zarządzania serwerem konfiguracji jest uruchamiany automatycznie.
8. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
9. Wybierają subskrypcji, grupy zasobów i magazyn usługi Recovery Services, w którym można zarejestrować serwer konfiguracji.

    ![Wybierz magazyn usługi Recovery Services](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Ich pobierania i instaluje serwer MySQL i interfejs PowerCLI programu VMWare. Następnie one sprawdza poprawność ustawień serwera.
11. Po zakończeniu walidacji wejdzie nazwy FQDN lub adres IP hosta vSphere lub wystąpienia serwera vCenter. Pozostaw domyślny port i wprowadź nazwę wyświetlaną dla wystąpienia programu vCenter Server na platformie Azure.
12. Określają konta utworzonego wcześniej, dzięki czemu usługa Site Recovery może automatycznie odnajdować maszyny wirtualne VMware, które są dostępne dla replikacji. 
13. Użytkownik podał poświadczeń, dzięki czemu usługa mobilności jest automatycznie instalowany po włączeniu replikacji. W przypadku maszyn Windows konto musi mieć uprawnienia administratora lokalnego na maszynach wirtualnych. 

    ![Konfigurowanie serwera vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Po zakończeniu rejestracji w portalu Azure weryfikują ponownie serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
8. Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Administratorzy Contoso Konfigurowanie środowiska docelowego replikacji:

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto magazynu i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Gdy źródłowy i docelowy są skonfigurowane, Administratorzy Contoso utworzyć zasady replikacji i spowoduje skojarzenie zasad z serwerem konfiguracji:

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą **ContosoMigrationPolicy** zasad.
2. Używają domyślnych ustawień:
   - **Próg celu punktu odzyskiwania**: Domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
   - **Przechowywanie punktów odzyskiwania**: Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
   - **Częstotliwość wykonywania migawek na poziomie aplikacji**: Domyślnej równej 1 godz. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.
 
     ![Zasadami replikacji — tworzenie](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Zasady replikacji - skojarzenia](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Potrzebujesz dodatkowej pomocy?*

- Może odczytywać kompletnym instruktażem następujące kroki w [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Włączanie replikacji

Teraz Administratorzy Contoso rozpocząć replikowanie WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **replikować**, wybierają ustawienia źródła.
2. Wskazują, że chce Włączanie maszyn wirtualnych, wybierz wystąpienie programu vCenter Server i serwer konfiguracji.

    ![Włączanie replikacji — źródło](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Określają ustawienia docelowego, łącznie z grupy zasobów i sieci, w którym maszyna wirtualna platformy Azure zostaną umieszczone po włączeniu trybu failover. Określają konto magazynu, w którym przechowywane będą replikowane dane.

    ![Włączanie replikacji — docelowy](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Wybierają **WebVM** replikacji. Usługa Site Recovery instaluje usługę mobilności na każdej maszynie Wirtualnej, po włączeniu replikacji. 

    ![Włączanie replikacji — wybierz maszynę Wirtualną](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Sprawdź, czy właściwe zasady replikacji jest zaznaczone, a następnie Włącz replikację dla **WEBVM**. Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w witrynie Azure portal można wyświetlić stan maszyn wirtualnych, które jest replikowana do platformy Azure:

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Potrzebujesz dodatkowej pomocy?*

Może odczytywać kompletnym instruktażem następujące kroki w [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database"></a>Krok 6: Migrowanie bazy danych 

Administratorzy firmy Contoso musisz utworzyć projekt usługi Database Migration Service, a następnie przeprowadzić migrację bazy danych.

### <a name="create-a-database-migration-service-project"></a>Utwórz projekt usługi Database Migration Service

1. Mogą utworzyć projekt usługi Database Migration Service. Wybierają **programu SQL Server** typ serwera źródłowego i **wystąpienia zarządzanego Azure SQL Database** jako element docelowy.

     ![Usługa migracji bazy danych — nowy projekt migracji](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Zostanie otwarty Kreator migracji.

### <a name="migrate-the-database"></a>Migrowanie bazy danych 

1. W Kreatorze migracji określają źródłowej maszyny Wirtualnej, na którym znajduje się baza danych w środowisku lokalnym. Użytkownik podał poświadczenia, które mają dostęp do bazy danych.

    ![Usługa migracji bazy danych — Szczegóły źródła](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. wybierają bazę danych do migracji (**SmartHotel.Registration**):

    ![Usługa migracji bazy danych — wybierz źródłowe bazy danych](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Dla elementu docelowego użytkownik podał nazwę wystąpienia zarządzanego na platformie Azure i poświadczenia dostępu.

    ![Usługa migracji bazy danych — szczegóły elementu docelowego](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. W **nowe działanie** > **Uruchamianie migracji**, określają ustawienia, aby uruchomić migrację:
    - Poświadczenia źródłowym i docelowym.
    - Baza danych, aby przeprowadzić migrację.
    - Udział sieciowy utworzony na lokalną maszynę Wirtualną. Usługa migracji bazy danych trwa tworzenie kopii zapasowych źródła do tego udziału. 
        - Konto usługi, który działa źródłowe wystąpienie programu SQL Server musi mieć uprawnienia do zapisu w tym udziale.
        - Należy użyć nazwy FQDN ścieżkę do udziału.
    - Identyfikator URI sygnatury dostępu Współdzielonego, który udostępnia usługę migracji bazy danych dzięki dostępowi do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej do migracji.

        ![Usługę migracji bazy danych — Konfigurowanie ustawień migracji](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Zapisz ustawienia migracji i następnie uruchom migracji.
6. W **Przegląd**, mogą monitorować stan migracji.

    ![Usługi Database Migration Service — monitorowanie](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Po zakończeniu migracji mogą sprawdzić, czy docelowymi bazami danych istnieją w wystąpieniu zarządzanym.

    ![Usługę migracji bazy danych — weryfikowanie migracji bazy danych](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>Krok 7: Migrowanie maszyny Wirtualnej

Administratorzy firmy Contoso, uruchomić szybkie testowanie trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Przed migracją WEBVM, testowy tryb failover pomaga zapewnić, że wszystko działa zgodnie z oczekiwaniami. Administratorzy, wykonaj następujące czynności:

1. Uruchamiają test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**. Po zaznaczeniu tej opcji usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwala przełączenie w tryb failover. Trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 
    1. Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że zostały spełnione wszystkie warunki, które są wymagane do migracji.
    2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli po wybraniu najnowszego punktu odzyskiwania, punkt odzyskiwania jest tworzony z danych.
    3.  Maszyna wirtualna platformy Azure jest tworzona przy użyciu danych przetworzonych w poprzednim kroku.
3. Po zakończeniu pracy w trybie failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Sprawdź, czy wszystko działa prawidłowo: maszyna wirtualna ma prawidłowy rozmiar, jest ona połączona z odpowiedniej sieci i jest uruchomiona. 
4. Po zweryfikowaniu testowy tryb failover, wyczyść przełączenie w tryb failover i zapisz wszelkie obserwacje. 

### <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

1. Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Administratorzy Contoso utworzyć plan odzyskiwania do migracji i Dodaj WEBVM zgodnie z planem:

     ![Tworzenie planu odzyskiwania — wybierz elementy](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Działają one przejścia w tryb failover dla planu, wybierając najnowszego punktu odzyskiwania. Określają, czy usługa Site Recovery należy dążyć do zamykania maszyny Wirtualnej w środowisku lokalnym, zanim wyzwala przełączenie w tryb failover.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Po włączeniu trybu failover ich Sprawdź, czy maszyna wirtualna platformy Azure znajduje się w oczekiwany sposób w witrynie Azure portal.

   ![Szczegóły planu odzyskiwania](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Po zweryfikowaniu, że ich zakończeniu migracji, aby zakończyć proces migracji, zatrzymanie replikacji maszyny Wirtualnej, a następnie Zatrzymaj Site Recovery rozliczeń dla maszyny Wirtualnej.

    ![Tryb failover — Kończenie migracji](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatni krok w procesie migracji Administratorzy Contoso zaktualizować parametry połączenia aplikacji, aby wskazywał zmigrowana baza danych, która jest uruchomiona na wystąpieniu zarządzanym firmy Contoso.

1. W witrynie Azure portal Znajdź są parametry połączenia, wybierając **ustawienia** > **parametry połączenia**.

    ![Parametry połączeń](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Ciąg one aktualizowane przy użyciu nazwy użytkownika i hasło wystąpienia zarządzanego usługi SQL Database.
3. Po skonfigurowaniu ciąg zastępują one bieżących parametrów połączenia w pliku web.config w swojej aplikacji.
4. Po aktualizowania pliku i zapisanie go, ich ponowne uruchomienie usług IIS na WEBVM, uruchamiając `IISRESET /RESTART` w oknie wiersza polecenia.
5. Po ponownym uruchomieniu usług IIS, aplikacja używa bazy danych, która jest uruchomiona na wystąpieniu zarządzanym usługi SQL Database.
6. W tym momencie one można zamknąć środowiska lokalnego na maszynie SQLVM. Migracja została zakończona.

*Potrzebujesz dodatkowej pomocy?*

- Dowiedz się, jak [testować tryb failover](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Dowiedz się, jak [utworzyć plan odzyskiwania](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Dowiedz się, jak [Failover na platformie Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji aplikacji rozwiązania SmartHotel360 jest uruchomiona na Maszynie wirtualnej platformy Azure i rozwiązania SmartHotel360 bazy danych jest dostępna w Azure SQL Database Managed Instance.  

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

- Zespół przegląda sieciowych grup zabezpieczeń, które są używane do kontrolowania dostępu do maszyny Wirtualnej. Zabezpieczenia sieciowe grupy pomagają upewnić się, że tylko ruchu, który jest dozwolony w aplikacji można przekazać.
- Zespół ds. zabezpieczeń firmy Contoso również polega na uwzględnieniu Zabezpieczanie danych na dysku za pomocą usługi Azure Disk Encryption i usługi Azure Key Vault.
- Zespół umożliwia wykrywanie zagrożeń na wystąpieniu zarządzanym. Wykrywanie zagrożeń wysyła alert do firmy Contoso zabezpieczeń zespołu/usługi technicznej systemu, aby otworzyć bilet, jeśli zostanie wykryte zagrożenie. Dowiedz się więcej o [wykrywanie dla zarządzanego wystąpienia zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Zarządzane zabezpieczenia wystąpienia — wykrywanie zagrożeń](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Aby dowiedzieć się więcej na temat praktyk w zakresie zabezpieczeń dla maszyn wirtualnych, zobacz [najlepsze rozwiązania dotyczące obciążeń IaaS na platformie Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms).

### <a name="bcdr"></a>BCDR

Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii (BCDR) Contoso wykonuje następujące akcje:

- Zabezpieczanie danych: Contoso tworzy kopie zapasowe danych na maszynach wirtualnych za pomocą usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Zachowaj aplikacji działanie: Contoso są replikowane maszyny wirtualne na platformie Azure aplikacji przy użyciu Site Recovery w regionie pomocniczym. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- Contoso dowiaduje się więcej na temat zarządzania wystąpienia zarządzanego usługi SQL, w tym [kopie zapasowe bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).


### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Firma Contoso ma istniejących licencji na program WEBVM. Aby skorzystać z ceny dzięki korzyści użycia hybrydowego platformy Azure, firma Contoso konwertuje istniejącej maszyny Wirtualnej platformy Azure.
- Contoso umożliwia usłudze Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. Cost Management to rozwiązanie do zarządzania kosztami wielu chmur, które pomaga Contoso i zarządzać platformy Azure i innych zasobów w chmurze. Dowiedz się więcej o [usługi Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehostowaniu aplikacji rozwiązania SmartHotel360 na platformie Azure przy użyciu funkcji migracji aplikacji frontonu maszyn wirtualnych na platformie Azure przy użyciu usługi Site Recovery. Contoso migruje lokalnej bazy danych do wystąpienia usługi Azure SQL Database zarządzane przy użyciu usługi Azure Database Migration Service.

## <a name="next-steps"></a>Kolejne kroki

W następnym artykule z tej serii Contoso [rehostowaniu aplikacji rozwiązania SmartHotel360 na maszynach wirtualnych Azure](contoso-migration-rehost-vm.md) przy użyciu usługi Azure Site Recovery.

