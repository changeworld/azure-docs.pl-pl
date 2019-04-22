---
title: Migracji rehost i ponowne hostowanie aplikacji systemu Linux w środowisku lokalnym do maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehost aplikacji systemu Linux w środowisku lokalnym przy użyciu funkcji migracji do maszyn wirtualnych platformy Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 200f9c5df0d4165341e38ca9d4dd85ad75c8403c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59010366"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migracja Contoso: Ponowne hostowanie aplikacji systemu Linux w środowisku lokalnym, na maszynach wirtualnych platformy Azure

W tym artykule pokazano, jak firmy Contoso jest rehosting aplikacji działu usług opartych na systemie Linux w środowisku lokalnym (**osTicket**), do maszyn wirtualnych IaaS platformy Azure.

W tym dokumencie jest jednym z serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje i zestaw scenariuszy, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne 
Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure | Contoso zakończeniu migracji lift-and-shift aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, za pomocą usługi Azure Site Recovery | W tym artykule
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso aplikacji osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso aplikacji rozwiązania SmartHotel360 jest migrowana do aplikacji sieci Web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne 
[Artykuł 11: Refaktoryzuj TFS na usługom DevOps platformy Azure](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji na kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne



W tym artykule Contoso zostanie przeprowadzona migracja dwuwarstwowej **osTicket** aplikacji działających w systemie Linux Apache MySQL PHP (LAMP) na platformę Azure. Maszyny wirtualne aplikacji zostaną poddane migracji za pomocą usługi Azure Site Recovery. Jeśli chcesz korzystać z tej aplikacji typu open source, możesz ją pobrać z [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Rośnie firmy Contoso, a w rezultacie istnieje nacisk na systemach lokalnych i infrastruktury.
- **Ogranicz ryzyko**: Usługi aplikacji działu jest krytyczne dla działalności firmy Contoso. Firma Contoso chce przenieść go do platformy Azure z ryzykiem, zerowego.
- **Rozszerzanie**:  Contoso nie chcesz zmienić aplikację teraz. Po prostu chce upewnij się, że aplikacja jest stabilna.


## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji, aby określić najlepszą metodę migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku programu VMWare w środowisku lokalnym.  Aplikacja pozostanie tak krytyczne w chmurze, ponieważ jest w środowisku lokalnym. 
- Contoso nie chcesz inwestować w tej aplikacji.  Ważne jest, aby firmy, ale w obecnej formie Contoso po prostu chce, aby bezpiecznie przenieść je do chmury.
- Contoso nie chce zmienić model ops dla tej aplikacji. Chce korzystać z aplikacji w chmurze w taki sam sposób, jak teraz.
- Contoso nie chce zmienić funkcje aplikacji. Zmieni się tylko do lokalizacji aplikacji.
- Po zakończeniu kilka migracji aplikacji Windows, firma Contoso chce dowiedzieć się, jak używać w infrastrukturze opartej na systemie Linux na platformie Azure.


## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół celami i wymaganiami, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, używających Contoso do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- Aplikacja OSTicket jest warstwowa między dwiema maszynami wirtualnymi (**OSTICKETWEB** i **OSTICKETMYSQL**).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (**contoso-datacenter**), za pomocą lokalnego kontrolera domeny (**contosodc1**)

### <a name="proposed-architecture"></a>Proponowana architektury

- Ponieważ aplikacja jest obciążenie produkcyjne, maszyn wirtualnych na platformie Azure będą znajdować się w grupie zasobów w środowisku produkcyjnym **ContosoRG**.
- Maszyny wirtualne zostaną zmigrowane do regionu podstawowego (wschodnie stany USA 2) i umieszczane w sieci produkcyjnej (sieć wirtualna-PROD-EUS2):
    - Maszyna wirtualna w sieci web będą znajdować się w podsieci frontend (PROD-FE-EUS2).
    - Baza danych maszyny Wirtualnej będą znajdować się w podsieci bazy danych (PROD-DB-EUS2).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>Przegląd rozwiązania

Contoso ocenia proponowane projektu poprzez umieszczenie razem listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | VMs aplikacji zostaną przeniesione na platformę Azure bez wprowadzania zmian w dokonywania migracji proste.<br/><br/> Ponieważ firma Contoso używa lift-and-shift na obu maszynach wirtualnych usługi app, żadne specjalne narzędzia migracji lub konfiguracji są wymagane dla bazy danych aplikacji.<br/><br/> Contoso zachowa pełną kontrolę nad aplikację maszyn wirtualnych na platformie Azure. </br>/br > app maszyny wirtualne są uruchomione systemu Ubuntu 16.04-TLS, który jest zalecane dla dystrybucji systemu Linux. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).
**Wady** | Warstwy sieć web i danych aplikacji pozostanie pojedynczy punkt przejścia w tryb failover. <br/><br/> Firma Contoso będzie musiał kontynuować wspieranie aplikacji jako maszyny wirtualne platformy Azure, a nie przechodzenia do zarządzanych usług, takich jak usługa Azure App Service i Azure Database for MySQL.<br/><br/> Firma Contoso ma pamiętać, że przez prostotę rzeczy za pomocą migracji maszyny Wirtualnej lift-and-shift, mogą one nie pełne czerpanie korzyści funkcji oferowanych przez [— Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) (wbudowaną wysoką dostępność, przewidywalna wydajność proste Skalowanie automatyczne tworzenie kopii zapasowych i wbudowane zabezpieczenia).

### <a name="migration-process"></a>Proces migracji

Firma Contoso będzie migracji w następujący sposób:

1. Pierwszym krokiem Contoso konfiguruje platformy Azure i w obrębie lokalnej infrastruktury potrzebne do wdrożenia usługi Site Recovery.
2. Po przygotowaniu składniki platformy Azure i w środowisku lokalnym, Contoso konfiguruje i umożliwia replikację dla maszyn wirtualnych.
3. Po działa replikacja Contoso umożliwia migrowanie maszyn wirtualnych przez awaryjną na platformie Azure.

![Proces migracji](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i naliczenia opłat, po przejściu do trybu failover. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) o Naliczanie opłat i ceny.

 
## <a name="prerequisites"></a>Wymagania wstępne

Oto, co firma Contoso potrzebuje dla tego scenariusza.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Contoso utworzyć subskrypcji w wczesnych artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura platformy Azure** | Konfigurowanie infrastruktury platformy Azure, zgodnie z opisem w contoso [infrastruktury platformy Azure na potrzeby migracji](contoso-migration-infrastructure.md).<br/><br/> Dowiedz się więcej o określonych [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Lokalny serwer vCenter powinny działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Host ESXi w wersji 5.5, 6.0 lub 6.5<br/><br/> Co najmniej jeden maszyn wirtualnych programu VMware działające na hoście ESXi.
**Lokalnych maszyn wirtualnych** | [Przejrzyj maszyn z systemem Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , są obsługiwane w przypadku migracji z usługą Site Recovery.<br/><br/> Sprawdź obsługiwany [systemów plików i magazynowania Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak przeprowadzić migrację przez firmy Contoso:

> [!div class="checklist"]
> * **Krok 1. Przygotowywanie platformy Azure dla usługi Site Recovery**: Contoso tworzy konto usługi Azure storage do przechowywania replikowanych danych oraz magazyn usługi Recovery Services.
> * **Krok 2. Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji**: Contoso przygotowuje kont do użycia dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowuje się nawiązać połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 3. Replikowanie maszyn wirtualnych**: Contoso konfiguruje migracji środowiska źródłowego i docelowego, tworzy zasady replikacji, a następnie rozpoczyna się replikowanie maszyn wirtualnych do usługi Azure storage.
> * **Krok 4. Migrowanie maszyn wirtualnych z usługą Site Recovery**: Contoso uruchamia test trybu failover, aby upewnić się, że wszystko działa, a następnie uruchomić tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowywanie platformy Azure dla usługi Site Recovery

Firma Contoso potrzebuje kilka składników platformy Azure dla usługi Site Recovery:

- Nowe konto usługi Azure storage do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.
- Sieć wirtualną, w którym przełączone w tryb failover znajdują się zasoby. Contoso utworzonej sieci wirtualnej podczas [wdrażania infrastruktury platformy Azure](contoso-migration-infrastructure.md), więc muszą po prostu utworzyć konto magazynu i magazynu.

1. Administratorzy firmy Contoso tworzyć konta usługi Azure storage (contosovmsacc20180528) w regionie wschodnie stany USA 2.

    - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
    - Używają konta ogólnego przeznaczenia z magazynu w warstwie standardowa i replikacją LRS.

      ![Site Recovery magazynu](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Za pomocą konta magazynu i sieci w miejscu utworzyć magazyn (ContosoMigrationVault) i umieść go w **ContosoFailoverRG** grupę zasobów, w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) konfigurowaniu platformy Azure dla usługi Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji

Administratorzy firmy Contoso Przygotuj lokalną infrastrukturę VMware w następujący sposób:

- Na serwerze vCenter lub vSphere ESXi hoście, w celu zautomatyzowania odnajdowania maszyn wirtualnych mogą utworzyć konto usługi.
- Tworzą konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware, którą chcesz replikować.
- Lokalnych maszyn wirtualnych, ich przygotowywanie tak, aby umożliwić im połączenie z maszynami wirtualnymi platformy Azure, po ich utworzeniu po migracji.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Wymagane jest konto, które można uruchamiać operacje, takie jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Administratorzy firmy Contoso, konto jest skonfigurowane w następujący sposób:

1. Tworzą rolę na poziomie vCenter.
2. Tej roli mogą przypisać wymagane uprawnienia.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Musi być zainstalowana usługa Mobility na maszynach wirtualnych systemu Linux, która będzie migrowana.

- Site Recovery może zrobić wypychania automatycznego instalowania tego składnika, po włączeniu replikacji dla maszyn wirtualnych.
- Dotyczące instalacji wypychanej automatyczne muszą przygotować konto, którego usługa Site Recovery będzie uzyskiwać dostęp maszyny wirtualne do.
- Szczegóły konta są dane wejściowe podczas konfigurowania replikacji. 
- Konto może być domeny lub lokalnego konta z uprawnieniami do zainstalowania na maszynach wirtualnych.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover na platformie Azure Contoso chce można było połączyć się z replikowanymi maszynami wirtualnymi na platformie Azure. Aby to zrobić, istnieje kilka rzeczy, które należy wykonać administratorzy Contoso:

- Dostęp do maszyn wirtualnych platformy Azure za pośrednictwem Internetu, umożliwiają one SSH w systemie Linux w środowisku lokalnym maszyny Wirtualnej przed migracją.  Dla systemu Ubuntu można to zrobić za pomocą następującego polecenia: **"Sudo" polecenia apt-get ssh zainstalować -y**.
- Po uruchomieniu migracji (tryb failover), można sprawdzić **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej.
- Jeśli to nie rozwiąże problemu, należy sprawdzić, czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli do automatycznego odnajdowania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikowanie lokalnych maszyn wirtualnych

Przed ich migracji maszyn wirtualnych w sieci web na platformie Azure, Administratorzy Contoso ustawienie i Włącz replikację.

### <a name="set-a-protection-goal"></a>Ustaw cel ochrony

1. W magazynie w obszarze nazwy magazynu (ContosoVMVault) ustawiają cel replikacji (**wprowadzenie** > **Site Recovery** > **przygotowanie infrastruktury**.
2. Określają, czy ich maszyn w środowisku lokalnym, że są one i maszyn wirtualnych VMware, czy mają być replikowane na platformie Azure.
    ![Cel replikacji](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, ich upewnij się, że ich ukończeniu planowania wdrożenia, wybierając **tak, ma to zostało zrobione**. Contoso są tylko migrowania jednej maszyny Wirtualnej, w tym scenariuszu i nie ma potrzeby planowania wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Administratorzy firmy Contoso teraz należy skonfigurować środowisko źródłowe. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu na serwerze konfiguracji mogą zarejestrować go w magazynie.

Serwer konfiguracji jest uruchamiana jest wiele składników:

- Składnik serwera konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Administratorzy firmy Contoso wykonaj następujące kroki w następujący sposób:

1. Będą oni mogli pobrać szablonu pakietu OVF z **Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Zaimportuj szablon do programu VMware do utworzenia maszyny Wirtualnej i wdrażanie maszyny Wirtualnej.

    ![Szablon OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Po włączeniu maszyny Wirtualnej po raz pierwszy jej zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzie konfiguracji usługi Azure Site Recovery uruchamia się domyślnie.
5. W narzędziu określają nazwy do rejestrowania serwera konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia logowania do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Logowania z maszyną ponownie, a następnie automatycznie uruchomiony Kreator zarządzania serwerem konfiguracji.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
10. Wybierają subskrypcji, grupy zasobów i Magazyn, w którym można zarejestrować serwer konfiguracji.

    ![Vault](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. One, a następnie Pobierz i zainstaluj serwer MySQL, a program VMWare PowerCLI. 
12. Po zakończeniu walidacji określają nazwy FQDN lub adres IP vCenter server lub hoście vSphere. Pozostaw domyślny port i określ przyjazną nazwę dla serwera vCenter.
13. Określ ich konta, utworzonego do automatycznego odnajdowania i poświadczenia, które powinny być używane do automatycznego instalowania usługi mobilności.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Po zakończeniu rejestracji w witrynie Azure portal, sprawdzają, czy serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
15. Usługa Site Recovery nawiąże połączenie z serwerami VMware i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso administratorom konfigurowanie ustawień replikacji docelowego.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto usługi Azure storage i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Po źródłowy i docelowy są skonfigurowane, są one gotowe do tworzenia zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą zasadę **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg celu punktu odzyskiwania**: Domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**. Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Potrzebujesz dodatkowej pomocy?**

- Może odczytywać kompletnym instruktażem wszystkie te kroki [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o agenta gościa platformy Azure dla systemu Linux.

**Potrzebujesz dodatkowej pomocy?**

- Może odczytywać kompletnym instruktażem wszystkie te kroki [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o agenta gościa platformy Azure dla systemu Linux.



### <a name="enable-replication-for-osticketweb"></a>Włączanie replikacji na potrzeby OSTICKETWEB

Teraz firmy Contoso, Administratorzy mogą rozpocząć replikowanie **OSTICKETWEB** maszyny Wirtualnej.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wybierz one, że chce włączyć maszyn wirtualnych, wybierz pozycję Ustawienia źródła, w tym serwera vCenter, a serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Określają ustawienia docelowego, łącznie z grupy zasobów i sieci wirtualnej, w którym maszyna wirtualna platformy Azure zostaną umieszczone po włączeniu trybu failover oraz konto magazynu, w którym przechowywane będą replikowane dane.

     ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Wybierają **OSTICKETWEB** maszyny Wirtualnej na potrzeby replikacji. 

   - Na tym etapie wybierają **OSTICKETWEB** tylko, ponieważ sieć wirtualną i podsieć zarówno należy wybrać, a maszyny wirtualne nie są w tej samej podsieci.
   - Usługa Site Recovery automatycznie instaluje usługę Mobility po włączeniu replikacji dla maszyny Wirtualnej.

     ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. We właściwościach maszyny Wirtualnej są wybierz konto które jest używane przez serwer przetwarzania do automatycznego instalowania usługi mobilności na maszynie.

     ![Usługa mobilności](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. w **ustawień replikacji** > **Konfigurowanie ustawień replikacji**, mogą sprawdzić, czy właściwe zasady replikacji zastosowane i wybierz pozycję **włączania replikacji**.
6. Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.



### <a name="enable-replication-for-osticketmysql"></a>Włączanie replikacji na potrzeby OSTICKETMYSQL

Teraz firmy Contoso, Administratorzy mogą rozpocząć replikowanie **OSTICKETMYSQL**.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródłowe i docelowe.
2. Wybierają **OSTICKETMYSQL** maszyny Wirtualnej na potrzeby replikacji, a następnie wybierz konto do użycia dla instalacji usługi mobilności.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Zastosuj te same zasady replikacji, które było używane dla OSTICKETWEB i Włącz replikację.  

**Potrzebujesz dodatkowej pomocy?**

Może odczytywać kompletnym instruktażem wszystkie te kroki [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrowanie maszyn wirtualnych 

Administratorzy firmy Contoso, uruchomić szybkie testowanie trybu failover, a następnie przeprowadzić migrację maszyn wirtualnych.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Uruchamianie testu trybu failover pomaga zapewnić, że wszystko działa zgodnie z oczekiwaniami przed migracją. 

1. Uruchamiają test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 
    - Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że wszystkie warunki wymagane do migracji w miejscu.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli po wybraniu najnowszego punktu odzyskiwania, punkt odzyskiwania jest tworzony z danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.
3. Po zakończeniu trybu failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Sprawdzają, czy maszyna wirtualna jest odpowiedni rozmiar, który jest połączony z odpowiednią siecią i czy działa. 
4. Po zweryfikowaniu, ich czyszczenie pracy awaryjnej i zarejestrować i zapisać wszelkie obserwacje.

### <a name="create-and-customize-a-recovery-plan"></a>Twórz i dostosowuj plan odzyskiwania

 Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Administratorzy Contoso utworzyć plan odzyskiwania do migracji. 

- Plan odzyskiwania określa kolejność, w której awaryjnej, jak maszyny wirtualne platformy Azure zostanie wznowione na platformie Azure.
- Ponieważ chcą migrować aplikacja dwuwarstwowa, będą one dostosowywać plan odzyskiwania, tak, aby dane maszyny Wirtualnej (SQLVM) uruchomi się przed frontonu (WEBVM).


1. W **plany odzyskiwania (Usługa Site Recovery)** > **+ planu odzyskiwania**, Utwórz plan i Dodaj maszyny wirtualne do niego.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Po utworzeniu planu, mogą wybrać go do dostosowywania (**plany odzyskiwania** > **OsTicketMigrationPlan** > **Dostosuj**.
3.  Usuwają **OSTICKETWEB** z **Grupa 1: Rozpocznij**.  Daje to gwarancję, że pierwszy akcji uruchamiania ma wpływ na **OSTICKETMYSQL** tylko.

    ![Grupa odzyskiwania](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  W **+ grupa** > **Dodaj chronione elementy**, co zwiększa **OSTICKETWEB** do **grupa 2: Rozpocznij**.  Muszą one w dwóch różnych grup.

    ![Grupa odzyskiwania](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrowanie maszyn wirtualnych


Administratorzy firmy Contoso są teraz gotowe do uruchomienia przejścia w tryb failover w planie odzyskiwania, aby przeprowadzić migrację maszyn wirtualnych.

1. Wybierają plan > **trybu Failover**.
2.  Należy wybrać, aby przełączyć tryb failover do najnowszego punktu odzyskiwania, a następnie określ, czy usługa Site Recovery należy dążyć do zamknięcia lokalną maszynę Wirtualną przed wyzwoleniem trybu failover. Mogą śledzić postęp trybu failover **zadań** strony.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover1.png)

3. Podczas pracy awaryjnej programu vCenter Server wydaje polecenia, aby zatrzymać dwóch maszyn wirtualnych uruchomionych na hoście ESXi.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Po włączeniu trybu failover ich Sprawdź, czy maszyna wirtualna platformy Azure znajduje się w oczekiwany sposób w witrynie Azure portal.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, mogą przeprowadzić migrację do zakończenia procesu migracji dla każdej maszyny Wirtualnej. Zatrzymanie replikacji maszyny Wirtualnej oraz zatrzymanie naliczania opłat za Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Połącz maszynę Wirtualną do bazy danych

Ostatni krok w procesie migracji Administratorzy Contoso zaktualizować parametry połączenia aplikacji, aby wskazywał bazie aplikacji uruchomionych na **OSTICKETMYSQL** maszyny Wirtualnej. 

1. Dokonają połączenia SSH **OSTICKETWEB** maszyny Wirtualnej przy użyciu programu Putty lub innego klienta SSH. Maszyna wirtualna jest prywatny, dzięki czemu łączą się przy użyciu prywatnego adresu IP.

    ![nawiązać połączenie z bazą danych](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![nawiązać połączenie z bazą danych](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Upewnij się, że muszą **OSTICKETWEB** maszyny Wirtualnej mogą komunikować się z **OSTICKETMYSQL** maszyny Wirtualnej. Obecnie konfiguracja zostanie zapisane na stałe o adresie IP 172.16.0.43 w środowisku lokalnym.

    **Przed aktualizacją**
    
    ![Aktualizowanie adresów IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Po aktualizacji**
    
    ![Aktualizowanie adresów IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Ich ponowne uruchomienie usługi przy użyciu **systemctl ponowne uruchomienie oprogramowania apache2**.

    ![Ponowne uruchamianie](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Na koniec zaktualizuj one rekordy DNS dla **OSTICKETWEB** i **OSTICKETMYSQL**, na jednym z kontrolerów domeny Contoso.

    ![Aktualizowanie systemu DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Aktualizowanie systemu DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) Uruchamianie testu trybu failover. 
- [Dowiedz się,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenia w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji osTicket warstwy aplikacji są teraz uruchomione na maszynach wirtualnych platformy Azure.

Teraz firma Contoso potrzebuje do oczyszczenia w następujący sposób: 

- Usuń lokalne maszyny wirtualne z magazynu programu vCenter.
- Usuń lokalne maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizowanie ich wewnętrzne dokumentacji, aby wyświetlić nową lokalizację i adresy IP dla OSTICKETWEB i OSTICKETMYSQL.
- Przejrzyj wszystkie zasoby, które współdziałają z maszyn wirtualnych i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.
- Contoso użycia usługi Azure Migrate z mapowaniem zależności w celu oceny maszyn wirtualnych do migracji. Administratorzy, należy usunąć program Microsoft Monitoring Agent i agenta zależności są zainstalowane w tym celu z maszyny Wirtualnej.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso przeglądanie OSTICKETWEB OSTICKETMYSQLVMs do określenia wszelkich problemów z zabezpieczeniami.

- Zespół przegląda sieciowych grup zabezpieczeń (NSG) dla maszyn wirtualnych w celu kontrolowania dostępu. Sieciowe grupy zabezpieczeń są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Zespół uwzględnia również Zabezpieczanie danych na dyskach maszyn wirtualnych przy użyciu szyfrowania dysku i usłudze Azure KeyVault.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="bcdr"></a>BCDR

Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii Contoso wykonuje następujące akcje:

- **Zabezpieczanie danych**: Contoso tworzy kopie zapasowe danych na maszynach wirtualnych za pomocą usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Zachowaj aplikacji działanie**: Contoso są replikowane maszyny wirtualne na platformie Azure aplikacji przy użyciu Site Recovery w regionie pomocniczym. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu zasobów, Contoso przypisuje tagi platformy Azure, zgodnie z definicją w [wdrażania infrastruktury platformy Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Firma Contoso ma żadnych licencjonowania problemów z serwerami systemu Ubuntu.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management. 


## <a name="next-steps"></a>Kolejne kroki

W tym artykule, który pokazaliśmy, jak migrować Contoso aplikację w środowisku lokalnym service desk warstwowego na dwie maszyny wirtualne systemu Linux na maszynach wirtualnych IaaS platformy Azure, za pomocą usługi Azure Site Recovery.

W następnym artykule z tej serii pokażemy ci jak Contoso jest migrowana z tej samej aplikacji działu usług na platformie Azure. Ten czas, firma Contoso używa Usługa Site Recovery do migrowania maszyny Wirtualnej serwera sieci Web dla aplikacji i przeprowadzanie migracji bazy danych aplikację za pomocą utworzyć kopię zapasową i przywrócić do usługi Azure Database for MySQL przy użyciu narzędzia MySQL workbench. [Rozpoczynanie pracy](contoso-migration-rehost-linux-vm-mysql.md).
