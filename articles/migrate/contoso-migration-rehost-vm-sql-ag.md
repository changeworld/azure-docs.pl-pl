---
title: Rehost aplikacji Contoso za pomocą migracji na maszynach wirtualnych platformy Azure i SQL Server AlwaysOn grupy dostępności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehost aplikacji lokalnych przy użyciu funkcji migracji na maszynach wirtualnych platformy Azure i SQL Server AlwaysOn grupy dostępności
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 03e3aaad810f6ccd5fb376765ddbada072dedb06
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301307"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-sql-server-alwayson-availability-group"></a>Migracja Contoso: Rehost aplikację lokalnie na maszynach wirtualnych platformy Azure i SQL Server AlwaysOn grupy dostępności

W tym artykule przedstawiono sposób Contoso rehost ich SmartHotel aplikacji na platformie Azure. Migrowania frontonu aplikacji maszyny Wirtualnej na maszynie Wirtualnej platformy Azure i aplikacji bazy danych do programu SQL Server maszyny Wirtualnej platformy Azure, uruchomionych w klastrze pracy awaryjnej systemu Windows Server z grupami dostępności AlwaysOn programu SQL Server.

Ten dokument jest jedną z szeregu artykuły, które pokazują, jak fikcyjnej firmy Contoso migruje ich zasobów lokalnych do chmury Microsoft Azure. Seria zawiera informacje i scenariusze, które przedstawiają Konfigurowanie infrastruktury migracji, oceny zasobów lokalnych do migracji i z różnymi typami migracji. Scenariusze zwiększa się złożoność i dodamy dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używany dla wszystkich artykułów migracji. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje sposób Contoso uruchamiania ocenę lokalnymi dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Contoso ocenia maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost aplikację na maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje sposób Contoso uruchamiania migracji przyrostu shift Azure dla aplikacji SmartHotel. Contoso przeprowadzanie migracji aplikacji frontonu maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzane SQL, za pomocą [usługi migracji bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
[Artykuł 5: Rehost aplikację na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migracji aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu usługi Site Recovery.
Artykuł 6: Rehost aplikację na maszynach wirtualnych platformy Azure i SQL Server zawsze włączonej grupy dostępności (w tym artykule) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa usługi Site Recovery do migracji aplikacji, maszyny wirtualne i usługi migracja bazy danych do migracji do klastra programu SQL Server chronione przez zawsze włączoną grupę dostępności bazy danych aplikacji. | Dostępne
[Artykuł 7: Rehost aplikacji systemu Linux, aby maszyny wirtualne platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso jest migracji przyrostu shift aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu usługi Site Recovery | Planowany
[Artykuł 8: Rehost aplikację systemu Linux, aby maszyny wirtualne platformy Azure i serwerem MySQL Azure](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery i przeprowadzanie migracji aplikacji bazy danych do wystąpienia serwera usługi MySQL Azure przy użyciu MySQL Workbench. | Dostępne



W tym artykule Contoso migracji dwuwarstwowa systemu Windows. Aplikacja NET SmartHotel działające na maszynach wirtualnych VMware do platformy Azure. Jeśli chcesz korzystać z tej aplikacji, jest podana jako typu open source i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Kadry kierowniczej IT ściśle działał z ich partnerów biznesowych, aby zrozumieć mają osiągnięcie przy tej migracji:

- **Adres rozwoju firmy**: rośnie Contoso i w związku z tym istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawnić procesy dla deweloperów i użytkowników.  Potrzeb biznesowych IT szybkie i czas nie odpadkami lub pieniędzy, w związku z tym dostarczania szybciej na wymagania dotyczące klienta.
- **Zwiększyć elastyczność**: Contoso IT musi być bardziej odpowiednie do potrzeb firmy. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby włączyć Powodzenie w globalnej gospodarka reakcji.  Nie może pobrać w taki sposób, lub stają się okienek biznesowych.
- **Skala**: jak pomyślnie rozwojem działalności IT firmy Contoso musi dostarczyć systemów, które są w stanie rośnie w tempie tego samego.

## <a name="migration-goals"></a>Celów migracji

Zespół chmury Contoso ma przypięty dół celów migracji. Tych celów były używane w celu ustalenia najlepszej metody migracji:

- Po migracji aplikacji na platformie Azure powinien mieć te same możliwości wydajności, jak obecnie w środowisku programu VMWare.  Aplikacja będzie nadal jako krytyczne w chmurze, ponieważ jest on lokalnymi.
- Contoso nie mają inwestycji w tej aplikacji.  Ważne jest, aby firmy, ale w jego bieżącym formularza po prostu chcą bezpiecznie przenieść go do chmury.
- Lokalną bazą danych aplikacji ma problemów dotyczących dostępności. Chce zobaczyć ona wdrożona na platformie Azure jako klaster wysokiej dostępności, możliwości trybu failover.
- Firma Contoso chce uaktualnić ich bieżącej platformy SQL Server 2008 R2 do 2017 serwera SQL.
- Contoso nie chcesz używać bazy danych SQL Azure dla tej aplikacji, a następnie szuka alternatyw.

## <a name="proposed-architecture"></a>Architektura proponowanych

W tym scenariuszu:

- Aplikacja jest warstwowa w dwóch maszyn wirtualnych (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Zarządza w środowisku programu VMware vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalnego datacenter (contoso-centrum danych), z lokalnym kontrolerem domeny (**contosodc1**).
- Będzie można zlikwidować lokalnymi maszynami wirtualnymi w centrum danych firmy Contoso, po ukończeniu migracji.

![Architektura scenariusza](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Usługa zarządzania bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso przeprowadzona migracja aplikacji warstwy danych przy użyciu DMS. Usługa DMS łączenie się na lokalnej maszynie SQLVM przez sieć VPN lokacja lokacja i Migrowanie DMS umożliwia bezproblemowe migracji z wielu źródeł bazy danych do platformy Azure danych, z minimalnym czasem przestojów. | Dowiedz się więcej o [obsługiwane regiony](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) DMS i get [szczegóły cennika](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso będzie używać usługi Site Recovery migracji przyrostu shift frontonu aplikacji maszyny Wirtualnej. Usługa Site Recovery organizuje zarządza migracji i odzyskiwania po awarii dla maszyn wirtualnych platformy Azure i lokalnymi maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure Azure Storage jest obciążany.  Maszyny wirtualne platformy Azure są tworzone i spowodować naliczenie opłat, podczas pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) dotyczące opłat i cenach.

 

## <a name="migration-process"></a>proces migracji

- Contoso przeprowadzona migracja aplikacji maszyn wirtualnych na platformie Azure.
- Maszyna wirtualna serwera sieci Web będzie migracją do maszyny Wirtualnej platformy Azure przy użyciu usługi Site Recovery:
    - Pierwszym krokiem one będzie przygotowanie i konfigurowanie składników platformy Azure i przygotowanie infrastruktury lokalnej programu VMware.
    - Z całą zawartością przygotowany mogą rozpocząć replikację maszyny Wirtualnej.
    - Po włączeniu replikacji i pracy, migrowania maszyny Wirtualnej przez awaryjne na platformie Azure.
- Będą one migrację bazy danych do klastra programu SQL Server na platformie Azure przy użyciu usługi migracji danych (DMS).
    - Pierwszym krokiem potrzebnych będzie do obsługi administracyjnej maszyn wirtualnych serwera SQL na platformie Azure, konfigurowanie klastra i wewnętrzny moduł równoważenia obciążenia i konfigurowanie grup dostępności AlwaysOn.
    - Z tym w miejscu można migrować bazy danych
- Po migracji będzie umożliwiają one ochronę AlwaysOn dla bazy danych.

![proces migracji](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Wymagania wstępne

Oto, co użytkownik (i firmy Contoso) muszą działać w tym scenariuszu:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcji podczas przeprowadzania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, należy do pracy z administratora przypisanie uprawnienia właściciela lub współautora.<br/><br/> Aby uzyskać bardziej szczegółowe uprawnienia, przejrzyj [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktury platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.<br/><br/> Dowiedz się więcej na temat konkretnego [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Usługa Site recovery (lokalnego)** | Serwer vCenter lokalnej musi korzystać z wersji 5.5, 6.0 lub 6.5<br/><br/> Hosta ESXi z systemem w wersji 5.5, 6.0 lub 6.5<br/><br/> Jeden lub więcej maszyn wirtualnych VMware uruchomiona na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.<br/><br/> Chcesz replikować maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**USŁUGA DMS** | Usługa DMS mają być [urządzenia sieci VPN zgodne lokalnymi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musi być możliwość konfigurowania lokalnego urządzenia sieci VPN. Musi mieć publiczny adres IPv4 połączonej zewnętrznie, a adres nie może znajdować się za urządzeniem NAT.<br/><br/> Upewnij się, że masz dostęp do lokalną bazą danych programu SQL Server.<br/><br/> Zapora systemu Windows powinna mieć możliwość dostępu aparatu bazy danych źródła. [Dowiedz się więcej](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> W przypadku zapory przed komputera bazy danych, Dodaj reguły, aby zezwolić na dostęp do bazy danych i do plików za pośrednictwem portu SMB 445.<br/><br/> Poświadczenia używane do łączenia się ze źródłem programu SQL Server i zarządzane wystąpienia docelowego musi być członkiem roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnionej w programie z lokalną bazą danych, którym DMS umożliwia utworzenie kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że działa wystąpienie serwera SQL źródła konto usługi ma uprawnienia zapisu w udziale sieciowym.<br/><br/> Zwróć uwagę użytkownika systemu Windows (i hasło), który ma uprawnienia pełnej kontroli w udziale sieciowym. Usługa Azure bazy danych migracji personifikuje te poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure.<br/><br/> Proces instalacji programu SQL Server Express ustawia protokołu TCP/IP na **wyłączone** domyślnie. Upewnij się, że jest włączone.


## <a name="scenario-steps"></a>Kroki w scenariuszu

Oto, jak Contoso będzie uruchamiane migracji:

> [!div class="checklist"]
> * **Krok 1: Tworzenie maszyn wirtualnych serwera SQL na platformie Azure**: wysokiej dostępności, Contoso mają zostać wdrożone klastrowanej bazy danych na platformie Azure. Wdrażaniu przez nich dwóch maszyn wirtualnych serwera SQL i Azure wewnętrznego modułu równoważenia obciążenia.
> * **Krok 2: Wdrażanie klastra**: po wdrożeniu maszynach wirtualnych serwera SQL, ich przygotowywanie klastra serwerów SQL Azure.  W tej wstępnie utworzonej klaster będzie migrowania ich bazy danych.
> * **Krok 3: Przygotowanie DMS**: Aby przygotować DMS zarejestrowanie dostawcy migracja bazy danych, Utwórz wystąpienie DMS i projektu. Konfigurowania sygnatury dostępu współdzielonego (SAS) identyfikator URI (Uniform Resource). Usługa DMS używa identyfikatora URI SA do uzyskiwania dostępu kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowych programu SQL Server.
> * **Krok 4: Przygotowanie usługi Azure Site Recovery**: tworzenia konta magazynu Azure do przechowywania replikowanych danych i magazynu usług odzyskiwania.
> * **Krok 5: Przygotowanie VMware lokalnego do usługi Site Recovery**: przygotowanie kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowania lokalnych maszyn wirtualnych, aby umożliwić im połączenie z maszynami wirtualnymi Azure po pracy awaryjnej.
> * **Krok 6: Replikowania maszyn wirtualnych**: Konfigurowanie ustawień replikacji i włączyć replikację maszyny Wirtualnej.
> * **Krok 7: Migrowanie bazy danych o DMS**: ich migrację bazy danych.
> * **Krok 8: Migrowanie maszyn wirtualnych z usługą Site Recovery**: najpierw uruchomić testowy tryb failover, aby się upewnić, że wszystko działa, następuje pełna pracy awaryjnej, aby przeprowadzić migrację maszyny Wirtualnej.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Krok 1: Przygotowanie klastra grupy dostępności AlwaysOn programu SQL Server

Firma Contoso chce planować z wyprzedzeniem, przez wdrożenie bazy danych w klastrze na platformie Azure. Tego klastra mogą korzystać z innych baz danych. 

- Zostanie ona wdrożona maszynach wirtualnych serwera SQL w grupie zasobów ContosoRG (używanych do produkcji zasobów).
- Oprócz unikatowe nazwy obie maszyny wirtualne używały tych samych ustawień.
- Wewnętrzny moduł równoważenia obciążenia będzie można wdrożyć w ContosoNetworkingRG (używanych do zasobów sieciowych).
- Maszyny wirtualne zostanie uruchomiony system Windows Server 2016 SQL Server 2017 Enterprise Edition. Contoso nie ma licencji dla tego systemu operacyjnego, dlatego będzie przez nich używana obrazu w portalu Azure Marketplace, zapewniająca licencji jako opłata do ich zobowiązań Azure EA.

Poniżej przedstawiono sposób konfigurowania klastra Contoso:

1. Po wybraniu obrazu programu SQL Server 2017 przedsiębiorstwa systemu Windows Server 2016 w portalu Azure Marketplace tworzenia dwóch maszyn wirtualnych programu SQL Server. 

    ![JEDNOSTKA SKU MASZYNY WIRTUALNEJ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. W **tworzenia maszyny wirtualnej kreatora** > **podstawy**, skonfigurować ich:

    - Nazwy maszyn wirtualnych: **SQLAOG1** i **SQLAOG2**.
    - Ponieważ maszyny biznesowych o znaczeniu krytycznym, umożliwiają one SSD dla typu dysku maszyny Wirtualnej.
    - Określa poświadczenia maszyny.
    - Ich wdrażanie maszyn wirtualnych w głównej wschodnie stany USA 2 regionu, w grupie zasobów ContosoRG.

3. W **rozmiar**, zaczynają D2s_V3 jednostki SKU dla obu maszyn wirtualnych. Będzie skalować później należy.
4. W **ustawienia**, ich wykonaj następujące czynności:

    - Ponieważ te maszyny wirtualne są krytyczne bazy danych dla aplikacji, będzie używać dysków zarządzanych.
    - One umieszczać maszyny w sieci produkcyjnej wschodnie stany USA 2 głównej region (**EUS2-VNET-produkcyjną**), w podsieci bazy danych (**produkcyjną-DB-EUS2**).
    - One tworzenie nowego zestawu dostępności: **SQLAOGAVSET**, z dwóch domen błędów i pięć domen aktualizacji.

    ![MASZYNY WIRTUALNEJ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. W **ustawienia programu SQL Server**, łączność z serwerem SQL do sieci wirtualnej (prywatny), ich limit domyślnego portu 1433. Do uwierzytelniania używają tych samych poświadczeń ponieważ używają one siedzibie (**administrator_contoso**).

    ![MASZYNY WIRTUALNEJ SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Potrzebujesz dodatkowej pomocy?**

- [Uzyskaj pomoc dotyczącą](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) inicjowania obsługi maszyny Wirtualnej programu SQL Server.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) Konfigurowanie maszyn wirtualnych dla różnych wersji serwera SQL.

## <a name="step-2-deploy-the-failover-cluster"></a>Krok 2: Wdrażanie klastra trybu failover

Poniżej przedstawiono sposób konfigurowania klastra Contoso:

1. Do działania jako monitor chmury one skonfigurowane konto magazynu platformy Azure.
2. Do domeny usługi Active Directory w lokalnym centrum danych firmy Contoso, co zwiększa maszynach wirtualnych serwera SQL.
3. Tworzenia klastra w systemie Azure.
4. Skonfigurowano monitora chmury.
5. Ponadto umożliwiają one SQL zawsze włączonych grup dostępności.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Konfigurowanie konta magazynu jako monitor chmury

Aby skonfigurować Monitor chmury, Contoso musi konta usługi Azure Storage, w którym będą przechowywane w pliku obiektu blob, używanego do rozstrzygnięcia klastra. To samo konto magazynu, można skonfigurować Monitor chmury dla wielu klastrów. 

Contoso tworzy konto magazynu w następujący sposób:

1. Określają one rozpoznawalną nazwę konta (**contosocloudwitness**).
2. Ogólne uniwersalny przy użyciu adresu e-mail, LRS ich wdrażania.
3. Konto one umieścić w regionie trzeci - południowo-środkowe Stany. One umieścić poza region podstawowych i pomocniczych tak, aby pozostaje dostępna w przypadku regionalnej awarii.
4. One umieścić go w ich grupy zasobów, który zawiera zasoby infrastruktury - **ContosoInfraRG**.

    ![Monitor chmury](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Podczas tworzenia konta magazynu, podstawowy i pomocniczy dostęp klucze są generowane dla niego. Potrzebna jest podstawowy klucz dostępu do utworzenia monitora chmury. Klucz jest wyświetlany w obszarze nazwy konta magazynu > **klucze dostępu**.

    ![Klucz dostępu](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Dodawanie maszyn wirtualnych programu SQL Server do domeny firmy Contoso

1. Contoso dodaje SQLAOG1 i SQLAOG2 do domeny contoso.com.
2. Następnie na każdej maszynie Wirtualnej instalacji narzędzia i funkcji klastra pracy awaryjnej systemu Windows.

## <a name="set-up-the-cluster"></a>Konfigurowanie klastra

Przed rozpoczęciem konfigurowania klastra, Contoso tworzy migawkę dysk systemu operacyjnego na każdym komputerze.

![Migawki](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Następnie ich uruchamiania skryptu została utworzona, są ze sobą w celu utworzenia klastra trybu Failover systemu Windows.

    ![Tworzenie klastra](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. Po utworzeniu klastra, okaże się, że maszyny wirtualne są wyświetlane jako węzły klastra.

     ![Tworzenie klastra](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Konfigurowanie monitora chmury

1. Contoso konfigurowania monitora chmury, za pomocą **Kreatora konfiguracji kworum** w Menedżerze klastra trybu Failover.
2. W Kreatorze one wybierz, aby utworzyć Monitor chmury z kontem magazynu.
3. Po skonfigurowaniu monitora chmury w pojawia się w przystawce Menedżer klastra trybu Failover.

    ![Monitor chmury](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>Włącz program SQL Server zawsze włączone grupy dostępności

Contoso można teraz włączyć zawsze włączone:

1. W programie SQL Server Configuration Manager umożliwiają one **zawsze włączonych grup dostępności** dla **programu SQL Server (MSSQLSERVER)** usługi.

    ![Włącz funkcję AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Ich ponownego uruchomienia usługi, aby zmiany zaczęły obowiązywać.

Z włączone (AlwaysOn) Contoso można skonfigurować grupy dostępności funkcji AlwaysOn, które będzie chronione SmartHotel bazy danych.

**Potrzebujesz dodatkowej pomocy?**

- [Przeczytaj informacje o](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) monitora i konfigurowania konta magazynu dla niego w chmurze.
- [Pobierz instrukcje](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) dotyczące konfigurowania klastra i utworzenia grupy dostępności.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Krok 3: Wdrażanie usługi równoważenia obciążenia Azure

Contoso można teraz wdrożyć wewnętrznego modułu równoważenia obciążenia, który znajduje się przed węzłów klastra. Moduł równoważenia obciążenia nasłuchuje ruchu i kieruje je do odpowiednich węzła.

![Równoważenie obciążenia](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Tworzenia usługi równoważenia obciążenia w następujący sposób:

1. W portalu Azure > **sieci** > **moduł równoważenia obciążenia**, konfigurowania nowego modułu równoważenia obciążenia wewnętrznego: **ILB-produkcyjną-DB-EUS2-SQLAOG**.
2. Składania usługi równoważenia obciążenia w sieci produkcyjnej **EUS2-VNET-produkcyjną**, w podsieci bazy danych **produkcyjną-DB-EUS2**.
3. Ich przypisać statyczny adres IP: 10.245.40.100.
4. Jako element sieci wdrażaniu przez nich usługi równoważenia obciążenia w grupie zasobów sieciowych **ContosoNetworkingRG**.

    ![Równoważenie obciążenia](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Po wdrożeniu usługi równoważenia obciążenia wewnętrznego Contoso należy go skonfigurować. Ich tworzenia puli adresów zaplecza, ustawienie sondy kondycji i skonfigurować regułę równoważenia obciążenia.

### <a name="add-a-backend-pool"></a>Dodaj pulę zaplecza

Aby rozpocząć dystrybucję ruchu do maszyn wirtualnych w klastrze, Contoso Konfigurowanie puli adresów zaplecza, który zawiera adresy IP używane dla maszyn wirtualnych, które będzie odbierać ruch sieciowy z modułu równoważenia obciążenia.

1. W ustawieniach usługi równoważenia obciążenia w portalu firmy Contoso Dodaj pulę zaplecza: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Pula skojarzenia z zestawu dostępności SQLAOGAVSET. Maszyny wirtualne w zestawie (**SQLAOG1** i **SQLAOG2**) są dodawane do puli.

    ![Puli wewnętrznej bazy danych](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Contoso tworzy badanie kondycji, aby usługa równoważenia obciążenia można monitorować kondycję aplikacji. Sonda dynamicznie dodaje lub usuwa maszyn wirtualnych z obrotu usługi równoważenia obciążenia, oparte na jak one odpowiadać na sprawdzenie kondycji.

Sonda ich utworzyć w następujący sposób: 

1. W ustawieniach usługi równoważenia obciążenia w portalu firmy Contoso tworzy badanie kondycji: **SQLAlwaysOnEndPointProbe**.
2. Ustaw one sondy do monitorowania maszyn wirtualnych na porcie TCP 59999.
3. One ustawić interwał 5 sekund między sond i próg 2. W przypadku awarii dwóch sond, maszyna wirtualna zostanie uwzględniony w złej kondycji.

    ![Sondy](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurowanie równoważenia obciążenia do odbierania ruchu


Teraz Contoso potrzeb reguły modułu równoważenia obciążenia do defins rozkład ruchu do maszyn wirtualnych.

- Adres IP frontonu obsługuje ruch przychodzący.
- Puli adresów IP zaplecza odbiera ruch.

Reguły tworzenia w następujący sposób:

1. W ustawieniach usługi równoważenia obciążenia w portalu, co zwiększa nowej reguły równoważenia obciążenia: **SQLAlwaysOnEndPointListener**.
2. Contoso ustawia frontonu odbiornika odbierania ruchu przychodzącego klienta SQL na porcie TCP 1433.
3. Określa pulę zaplecza, jaki ruch, zostaną przesłane i port, na którym maszyn wirtualnych nasłuchiwać ruchu.
4. Contoso umożliwia pływającego adresu IP (bezpośredni zwrot serwera). To jest zawsze wymagane dla funkcji SQL AlwaysOn.

    ![Sondy](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Potrzebujesz dodatkowej pomocy?**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) usługi równoważenia obciążenia Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) tworzenia modułu równoważenia obciążenia.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Krok 4: Przygotowanie Azure do usługi Site Recovery

Poniżej przedstawiono składniki Azure Contoso wymaga wdrożenia usługi Site Recovery:

- Sieci wirtualnej, w którym zostaną umieszczone podczas ich tworzenia podczas pracy awaryjnej.
- Konto magazynu Azure do przechowywania replikowanych danych. 
- W magazynie usług odzyskiwania Azure.

One skonfigurowane je w następujący sposób:

1.  Contoso już utworzona sieć/podsieć, można użyć do odzyskiwania lokacji podczas ich [wdrożyć infrastrukturę platformy Azure](contoso-migration-rehost-vm-sql-ag.md).

    - Aplikacja SmartHotel jest aplikacji produkcyjnej, a WEBVM zostaną zmigrowane do środowiska produkcyjnego Azure (VNET-produkcyjną EUS2) w regionie wschodnie US2 podstawowym.
    - WEBVM zostanie umieszczona w grupie zasobów ContosoRG jest używana do zasobów w środowisku produkcyjnym, a w środowisku produkcyjnym podsieci (produkcyjną-FE-EUS2).

2. Contoso tworzy konto magazynu platformy Azure (contosovmsacc20180528) w regionie podstawowym.

    - Korzystają z konta ogólnego przeznaczenia, z magazynu standardowego i replikacji LRS.
    - Konto musi znajdować się w tym samym regionie co magazyn.

    ![Magazyn odzyskiwania lokacji](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Z sieci i magazynu konta w miejscu magazynu usług odzyskiwania teraz utworzyć (**ContosoMigrationVault**) i umieść go w **ContosoFailoverRG** grupy zasobów w regionie podstawowym wschodnie stany USA 2 .

    ![Magazyn usługi Recovery Services](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Konfigurowanie usługi Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Krok 4: Przygotowanie VMware lokalnego do usługi Site Recovery

Oto co Contoso przygotowuje lokalnie:

- Konto vCenter server lub vSphere ESXi hosta, aby zautomatyzować odnajdywania maszyny Wirtualnej.
- Konta, które umożliwia automatyczne instalacji usługi mobilności na maszynach wirtualnych VMware, które mają być replikowane.
- Lokalnymi ustawienia maszyny Wirtualnej, dzięki czemu Contoso mogą łączyć się zreplikowanej maszyny Wirtualnej Azure po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. 
- Organizowanie replikacji, trybu failover i powrotu po awarii.
- Wymagane jest co najmniej konto tylko do odczytu. Potrzebujesz konta, które można uruchomić operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso Ustawia konto:

1. Contoso tworzy rolę na poziomie vCenter.
2. Contoso następnie przypisuje wymaganych uprawnień danej roli.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Musi być zainstalowana usługa mobilności na każdej maszynie Wirtualnej.

- Site Recovery może zrobić instalacji wypychanej automatyczne tego składnika, po włączeniu replikacji dla maszyny Wirtualnej.
- Potrzebujesz konta usługi Site Recovery umożliwia dostęp do maszyny Wirtualnej dla instalacji wypychanej. To konto można określić po skonfigurowaniu replikacji w konsoli platformy Azure.
- Konto może być domeny lub lokalnych z uprawnienia do instalacji na maszynie Wirtualnej.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover firma Contoso chce nawiązać połączenia z maszynami wirtualnymi platformy Azure. Aby to zrobić, ich wykonaj następujące czynności przed migracją:

1. Aby uzyskać dostęp za pośrednictwem Internetu one:

 - Włącz protokół RDP na lokalnej maszynie Wirtualnej przed trybu failover
 - Upewnij się, że reguły TCP i UDP są dodawane do **publicznego** profilu.
 - Sprawdź, czy RDP jest dozwolony w **zapory systemu Windows** > **dozwolone aplikacje** we wszystkich profilach.
 
2. Aby uzyskać dostęp za pośrednictwem sieci VPN typu lokacja lokacja ich:

 - Włącz protokół RDP na maszynie lokalnej.
 - Zezwalaj na RDP w **zapory systemu Windows** -> **dozwolone aplikacje i funkcje**, dla **domeny i prywatnej** sieci.
 - Ustaw zasady sieci SAN systemu operacyjnego na lokalnej maszynie Wirtualnej, aby **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdź następujące kwestie:

- Powinien istnieć nie oczekujące aktualizacje systemu Windows na maszynie Wirtualnej, gdy wyzwolenie pracy awaryjnej. Jeśli, nie będzie mógł zalogować się do maszyny Wirtualnej do momentu ukończenia aktualizacji.
- Po przejściu w tryb failover, można sprawdzić **diagnostyki rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie pomoże, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzeć te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli automatycznego wykrywania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Krok 5: Replikowanie lokalnych maszyn wirtualnych do platformy Azure z usługą Site Recovery

Przed uruchomieniem migracji na platformie Azure, Contoso należy skonfigurować i włączyć replikację.

### <a name="set-a-replication-goal"></a>Ustaw celem replikacji

1. W magazynie, w obszarze Nazwa magazynu (ContosoVMVault) wybierają celem replikacji (**wprowadzenie** > **usługi Site Recovery** > **przygotowanie infrastruktury** .
2. Określają, czy ich maszyn znajdujących się lokalnie, systemem VMware i replikację do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Upewnij się, planowanie wdrożenia

Aby kontynuować, należy potwierdzić, że ukończył Planowanie wdrożenia, wybierając **tak, już to zostało zrobione**. W tym scenariuszu firma Contoso jest tylko migracja maszyny Wirtualnej, a nie jest konieczne Planowanie wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Contoso musi skonfigurować ich środowiska źródłowego. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery jako o wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu serwera konfiguracji one Zarejestruj go w magazynie.

Serwer konfiguracji jest uruchamiana liczby składników:

- Składnik serwera konfiguracji, która koordynuje komunikacji między lokalną i platformą Azure i zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Contoso wykonaj następujące kroki w następujący sposób:


1. W magazynie, będą oni mogli pobrać szablonu OVF **przygotowanie infrastruktury** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. One zaimportuj szablon do programu VMware do tworzenia i wdrażania maszyny Wirtualnej.

    ![Szablon OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Podczas włączania maszyny Wirtualnej po raz pierwszy, on uruchamiany w procesie instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzia konfiguracji odzyskiwania lokacji Azure domyślnie uruchamia.
5. W narzędziu określają Nazwa do użycia na potrzeby rejestracji w magazynie serwera konfiguracji.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia zalogowaniu do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.

    ![Zarejestruj serwer konfiguracji](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. One ponownie zalogować się w komputerze i Kreatora konfiguracji serwera zarządzania jest uruchamiana automatycznie.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić tego ustawienia, po przeprowadzeniu konfiguracji.
10. Wybierają subskrypcji, grupy zasobów i magazynu, w którym można zarejestrować serwer konfiguracji.
        ![Magazyn](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Ich, a następnie Pobierz i zainstaluj serwer MySQL i VMWare PowerCLI. 
11. Po sprawdzeniu poprawności określają adres FQDN lub adres IP hosta serwera lub vSphere vCenter. Pozostaw domyślny port i określić przyjazną nazwę dla serwera vCenter.
12. Określa konto, utworzony automatycznego wykrywania i poświadczenia, które są używane do automatycznego zainstalowania usługi mobilności. W przypadku komputerów z systemem Windows konto musi uprawnienia administratora lokalnego na maszynach wirtualnych.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Po zakończeniu rejestracji w portalu Azure Contoso podwójne sprawdza, czy serwer konfiguracji i serwera VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może zająć 15 minut lub dłużej. 
8. Odzyskiwanie lokacji łączy się przy użyciu określonych ustawień serwerów VMware i umożliwia odnajdywanie maszyn wirtualnych.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso określa ustawienia replikacji obiektu docelowego.

1. W **przygotowanie infrastruktury** > **docelowego**, wybierają ustawienia obiektu docelowego.
2. Usługa Site Recovery sprawdza, czy znajduje się konto magazynu Azure i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Nie, firma Contoso można utworzyć zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawienia replikacji** > **zasad replikacji** >  **tworzenie i Skojarz**, tworzenia zasad **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg RPO**: domyślny 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Przechowywania punktu odzyskiwania**. Domyślna wartość 24 godzin. Ta wartość określa, jak długo trwa okna przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, na którym są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Włączanie replikacji

Contoso można teraz rozpocząć replikację WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, czy chcą włączyć maszyn wirtualnych, wybierz serwer vCenter i serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Teraz określ ich ustawień obiektu docelowego, łącznie z grupy zasobów i sieć wirtualna i konta magazynu, w którym będą przechowywane replikowanych danych.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso wybiera WebVM replikacji, sprawdza zasady replikacji i pozwala usłudze replikacji. Usługa Site Recovery instaluje usługi mobilności na maszynie Wirtualnej, po włączeniu replikacji.
 
    ![Włączanie replikacji](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. One śledzić postęp replikacji w **zadania**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
5. W **Essentials** w portalu Azure Contoso można wyświetlić strukturę dla maszyn wirtualnych replikowanych do platformy Azure.

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Potrzebujesz dodatkowej pomocy?**

- Pełny przewodnik wszystkie te kroki można znaleźć [ustawienia odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrożenie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Użytkownik może dowiedzieć się więcej o [włączenia replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>Krok 5: Zainstaluj Asystenta migracji bazy danych (DMA)

Contoso spowoduje zmigrowanie maszyny Wirtualnej platformy Azure bazy danych SmartHotel **SQLAOG1** przy użyciu DMA. One skonfigurowane DMA w następujący sposób:

1. Będą oni mogli pobrać narzędzie z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) do lokalnego serwera SQL z maszyny Wirtualnej (**SQLVM**).
2. One uruchom Instalator (DownloadMigrationAssistant.msi) na Maszynie wirtualnej.
3. Na **Zakończ** strony, wybierają **uruchomić Asystenta firmy Microsoft danych migracji** przed zakończeniem pracy kreatora.

## <a name="step-6-migrate-the-database-with-dma"></a>Krok 6: Migrowanie bazy danych o DMA

1. W DMA działają nowych migracji - **SmartHotel**.
2. Wybierają **typ serwer docelowy** jako **programu SQL Server na maszynach wirtualnych Azure**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Informacje dotyczące migracji, co zwiększa **SQLVM** co serwer źródłowy i **SQLAOG1** jako element docelowy. Określa poświadczenia dla każdego komputera.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Tworzenia lokalnego udziału informacji bazy danych i konfiguracji. Musi być on dostępny do zapisu dla konta usługi SQL na SQLVM i SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso wybiera logowania, które mają zostać poddane migracji i rozpoczyna migrację. Po zakończeniu DMA pokazuje migracji pomyślne.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Sprawdź, czy baza danych jest uruchomiona na **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS łączy do lokalnego serwera SQL z maszyny Wirtualnej przez połączenie VPN typu lokacja lokacja, między centrum danych firmy Contoso i platformą Azure, a następnie wykonuje migrację bazy danych.

## <a name="step-7-protect-the-database"></a>Krok 7: Ochrona bazy danych

Z bazy danych aplikacji uruchomionej na **SQLAOG1**, Contoso mogą teraz chronić go przy użyciu zawsze włączonych grup dostępności. One skonfigurować przy użyciu programu SQL Management Studio (AlwaysOn), a następnie przypisz odbiornik przy użyciu klastra systemu Windows. 

### <a name="create-an-alwayson-availability-group"></a>Tworzenie grupy dostępności AlwaysOn

1. W programie SQL Management Studio, kliknij prawym przyciskiem **zawsze na wysoką dostępność** uruchomić **Kreatora nowej grupy dostępności**.
2. W **Określ opcje**, ich nazwy grupy dostępności **SHAOG**. W **wybierz baz danych**, wybierają SmartHotel bazy danych.

    ![Zawsze włączone grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. W **Określanie replik**, dodaj dwa węzły SQL jako repliki dostępności i skonfigurować je, aby zapewnić automatyczną pracę awaryjną zatwierdzania synchronicznego.

     ![Zawsze włączone grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Skonfigurowano odbiornika dla grupy (**SHAOG**) i portu. Adres IP wewnętrznego modułu równoważenia obciążenia jest dodawana jako statyczny adres IP (10.245.40.100).

    ![Zawsze włączone grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. W **wybierz synchronizację danych**, umożliwiają automatyczne wstępne wypełnianie. Po wybraniu tej opcji programu SQL Server automatycznie tworzy repliki pomocniczej dla każdej bazy danych w grupie dzięki Contoso nie trzeba ręcznie utworzyć kopię zapasową i przywrócić je. Po sprawdzeniu poprawności zostanie utworzona grupa dostępności.

    ![Zawsze włączone grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso wystąpił problem podczas tworzenia grupy. Nieużywane Active Directory zintegrowanych zabezpieczeń systemu Windows i w związku z tym należy przyznać uprawnienia do logowania SQL, aby utworzyć role klastra pracy awaryjnej systemu Windows.

    ![Zawsze włączone grupy dostępności](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Po utworzeniu grupy Contoso można sprawdzić w programie SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Skonfiguruj odbiornik w klastrze

Jako ostatni etap w ramach konfigurowania wdrożenia programu SQL firmy Contoso konfiguruje wewnętrznego modułu równoważenia obciążenia jako odbiornik w klastrze i oferuje odbiornika w trybie online. W tym ich za pomocą skryptu.

![Odbiornik klastra](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Sprawdź, czy konfiguracja

W przypadku wszystko Konfigurowanie Contoso teraz mają grupy dostępności funkcjonalności w systemie Azure korzystającej z migrowanych bazy danych. Są to sprawdzić, nawiązując połączenie z usługą równoważenia obciążenia wewnętrznego w programie SQL Management Studio.

![Połącz ILB](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Potrzebujesz dodatkowej pomocy?**
- Więcej informacji o tworzeniu [grupy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) i [odbiornika](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Ręcznie [klaster skonfigurowany do używania adresu IP usługi równoważenia obciążenia](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) dotyczących tworzenia i używania sygnatury dostępu Współdzielonego.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Krok 8: Migrowanie maszyn wirtualnych z usługą Site Recovery

Contoso szybkiego uruchamiania testowanie trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Uruchomiony test trybu failover pomaga upewnij się, że wszystko działa zgodnie z oczekiwaniami przed migracją. 

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowsze przetworzone**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu próbuje zamknąć źródłowej maszyny Wirtualnej, aby mogło nastąpić wyzwolenie pracy awaryjnej usługi Site Recovery. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Tryb failover uruchomień testów: 

    - Uruchamia się upewnić, że wszystkie warunki wymagane do migracji w miejscu Sprawdzanie wymagań wstępnych.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

3. Po zakończeniu pracy awaryjnej, replika maszyny Wirtualnej Azure pojawia się w portalu Azure. Contoso sprawdza, czy maszyna wirtualna jest odpowiedni rozmiar, który został połączony z siecią prawo, i że jest uruchomiona. 
4. Po zweryfikowaniu, Contoso wyczyścić trybu failover i zarejestrować i zapisać wszelkie obserwacje. 

### <a name="run-a-failover"></a>Uruchamianie trybu failover

1. Po zweryfikowaniu, że test trybu failover działał zgodnie z oczekiwaniami, Contoso utworzyć plan odzyskiwania do migracji i Dodaj WEBVM do planu.

     ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Tryb failover są uruchamiane w planie. Wybierz najnowszy punkt odzyskiwania i określ, czy usługa Site Recovery należy dążyć do zamykania maszyny Wirtualnej lokalnego, aby mogło nastąpić wyzwolenie pracy awaryjnej.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Po pracy awaryjnej ich Sprawdź, czy maszyny Wirtualnej Azure pojawia się zgodnie z oczekiwaniami w portalu Azure.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, ich Zakończenie migracji na zakończenie procesu migracji, zatrzymania replikacji dla maszyny Wirtualnej i Zatrzymaj rozliczeń usługi Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatni krok w procesie migracji Contoso zaktualizować parametry połączenia aplikacji, aby wskazywał migrowanych systemem odbiornika SHAOG bazy danych. Ta konfiguracja zostanie zmieniony na WEBVM teraz działają na platformie Azure.  Tej konfiguracji znajduje się w pliku web.config aplikacji ASP. 

1. Zlokalizuj plik na C:\inetpub\SmartHotelWeb\web.config.  Zmień nazwę serwera w celu odzwierciedlenia nazwy FQDN AOG: shaog.contoso.com.

    ![Tryb failover](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Po aktualizacji plik i zapisać go, ich ponownego uruchomienia usług IIS na WEBVM. One to robić przy użyciu /RESTART IISRESET z wiersza polecenia.
2. Po ponownym uruchomieniu usług IIS aplikacji jest teraz, używając systemem SQL MI bazy danych.


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) uruchomiony test trybu failover. 
- [Dowiedz się](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenie w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Czyszczenie po migracji

Po przeprowadzeniu migracji aplikacji SmartHotel działa na maszynie Wirtualnej platformy Azure, a SmartHotel baza danych znajduje się w klastrze programu SQL Azure.

Contoso musi teraz wykonać te kroki oczyszczania:  

- Usuń lokalnych maszyn wirtualnych ze spisu vCenter.
- Usuń maszyny wirtualne z lokalnym zadania tworzenia kopii zapasowej.
- Aktualizuj wewnętrzne dokumentacji do wyświetlenia nowych lokalizacji i adresów IP dla maszyn wirtualnych.
- Przejrzyj wszystkie zasoby, które interakcyjnie wycofany z eksploatacji maszyn wirtualnych, a następnie zaktualizować wszystkie odpowiednie ustawienia lub dokumentacji odzwierciedlą nowej konfiguracji.
- Dodaj dwa nowe maszyny wirtualne (SQLAOG1 i SQLAOG2) powinien zostać dodany do monitorowania systemów produkcyjnych.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z zasobów na platformie Azure Contoso musi pełni operacjonalizacji i zabezpieczyć ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół zabezpieczeń firmy Contoso przegląda WEBVM maszyn wirtualnych Azure, SQLAOG1 i SQLAOG2 określenia wszelkich problemów z bezpieczeństwem. 

- Przeglądu grup zabezpieczeń sieci (NSG) dla maszyny Wirtualnej w celu kontroli dostępu. Grupy NSG są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Rozważanych Zabezpieczanie danych na dysku przy użyciu szyfrowania dysków Azure i KeyVault.
- One powinny ocenić przezroczystego szyfrowania danych (funkcji TDE), a następnie włącz go w bazie danych SmartHotel uruchomionych na nowe AOG SQL. [Dowiedz się więcej](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych

Firma Contoso ma kopię zapasową danych na WEBVM, SQLAOG1 i SQLAOG2 przy użyciu usługi Kopia zapasowa Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja kosztów i licencjonowania

1. Contoso ma istniejącej licencji dla ich WEBVM i będzie korzystać z asysty hybrydowe platformy Azure.  Będzie konwertują istniejących maszyn wirtualnych platformy Azure, aby móc korzystać z tej cennik.
2. Contoso spowoduje włączenie zarządzania koszt Azure licencjonowane przez Cloudyn, oddziału firmy Microsoft. To rozwiązanie do zarządzania koszt usługi chmury, które pomaga wykorzystywać oraz zarządzania platformy Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o Azure kosztów zarządzania. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji frontonu aplikacji maszyny Wirtualnej na platformie Azure przy użyciu usługi Site Recovery. Migrację bazy danych aplikacji do klastra programu SQL Server udostępnione na platformie Azure i chroniony w grupie dostępności AlwaysOn programu SQL Server.

## <a name="next-steps"></a>Kolejne kroki

W artykule w serii możemy opisano, jak Contoso rehost ich usługi technicznej osTicket aplikacji uruchomionej w systemie Linux i wdrażane z bazy danych MySQL.


