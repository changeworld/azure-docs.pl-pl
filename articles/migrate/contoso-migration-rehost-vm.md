---
title: Ponowne hostowanie aplikacji firmy Contoso za pomocą migracji na maszyny wirtualne platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rehost lokalną aplikację przy użyciu lift-and-shift migrację lokalnych maszyn na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60668409"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migracja Contoso: ponowne hostowanie aplikacji lokalnej na maszynach wirtualnych platformy Azure


W tym artykule przedstawiono, jak Contoso rehostowaniu aplikacji rozwiązania SmartHotel360 lokalnych na platformie Azure, migrując maszyny wirtualne aplikacji na maszynach wirtualnych platformy Azure.


W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso jest migrowana zasobów lokalnych do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | W tym artykule
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zakończeniu migracji lift-and-shift aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, za pomocą usługi Azure Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso aplikacji osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso aplikacji rozwiązania SmartHotel360 jest migrowana do aplikacji sieci Web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzuj TFS na usługom DevOps platformy Azure](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji na kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne


W tym artykule Contoso zostaną zmigrowane Windows dwuwarstwowy. Aplikacja rozwiązania SmartHotel360 NET działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji jest przewidziana typu open source i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie, a w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Ogranicz ryzyko**: Aplikacja rozwiązania SmartHotel360 jest krytyczne dla działalności firmy Contoso. Chce przenieść aplikację na platformę Azure z ryzykiem, zerowego.
- **Rozszerzanie**: Contoso nie chce zmodyfikować aplikację, ale chcesz upewnić się, że jest stabilna.


## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele są używane do określenia najlepszej metody migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku VMware. Aplikacja pozostanie tak krytyczne w chmurze, ponieważ jest w środowisku lokalnym.
- Contoso nie chcesz inwestować w tej aplikacji. Ważne jest, aby firmy, ale w obecnej formie Contoso po prostu chce, aby bezpiecznie przenieść je do chmury.
- Contoso nie chce zmienić model ops dla tej aplikacji. Contoso chce korzystać z niego w chmurze w taki sam sposób, jak teraz.
- Contoso nie chce zmienić dowolne funkcje aplikacji. Zmieni się tylko do lokalizacji aplikacji.


## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół celami i wymaganiami, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, używających Contoso do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- Aplikacja jest warstwowa między dwiema maszynami wirtualnymi (**WEBVM** i **SQLVM**).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).

### <a name="proposed-architecture"></a>Proponowana architektury

- Ponieważ aplikacja jest obciążenie produkcyjne, maszyny wirtualne na platformie Azure aplikacji będą znajdować się w grupie zasobów produkcji ContosoRG.
- Maszyny wirtualne aplikacji będą migrowane do podstawowego regionu platformy Azure (wschodnie stany USA 2) i umieszczane w sieci produkcyjnej (sieć wirtualna-PROD-EUS2).
- Frontony sieci web, maszyn wirtualnych będą znajdować się w podsieci frontend (PROD-FE-EUS2) w sieci produkcyjnej.
- Baza danych maszyny Wirtualnej będą znajdować się w podsieci bazy danych (PROD-DB-EUS2) w sieci produkcyjnej.
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>Zagadnienia dotyczące bazy danych

Częścią procesu projektowania rozwiązania firmy Contoso zostały porównanie funkcji usługi Azure SQL Database i programu SQL Server. Następujące zagadnienia dotyczące przeglądać zdecydować przejść z programu SQL Server uruchomionego na maszynie Wirtualnej IaaS platformy Azure:

- Za pomocą maszynie Wirtualnej platformy Azure z programem SQL Server wydaje się być optymalne rozwiązanie, jeśli firma Contoso potrzebuje dostosować system operacyjny lub serwera bazy danych lub może być kolokacja i uruchomienie aplikacji innych firm na tej samej maszyny Wirtualnej.
- Z pakietem Software Assurance w przyszłości firma Contoso może wymieniać istniejących licencji do korzystania z taryf rabatowych na wystąpienie zarządzane SQL Database za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server. To Oszczędź do 30% na wystąpieniu zarządzanym.



### <a name="solution-review"></a>Przegląd rozwiązania

Contoso ocenia proponowane projektu poprzez umieszczenie razem listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | VMs aplikacji zostaną przeniesione na platformę Azure bez wprowadzania zmian w dokonywania migracji proste.<br/><br/> Ponieważ firma Contoso używa lift-and-shift na obu maszynach wirtualnych usługi app, żadne specjalne narzędzia migracji lub konfiguracji są wymagane dla bazy danych aplikacji.<br/><br/> Contoso mogą korzystać z inwestycji w pakiet Software Assurance, za pomocą korzyści użycia hybrydowego platformy Azure.<br/><br/> Contoso zachowa pełną kontrolę nad aplikację maszyn wirtualnych na platformie Azure.
**Wady** | WEBVM i SQLVM działają Windows Server 2008 R2. System operacyjny jest obsługiwany przez platformę Azure dla określonych ról (lipca 2018 r.). [Dowiedz się więcej](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> Warstwy web i danych w aplikacji będą nadal pojedynczy punkt przejścia w tryb failover.</br><br/> SQLVM jest uruchomiony w programie SQL Server 2008 R2, która nie stanowi wsparcie. Jednak jest obsługiwane w przypadku maszyn wirtualnych platformy Azure (lipca 2018 r.). [Dowiedz się więcej](https://support.microsoft.com/en-us/help/956893).<br/><br/> Contoso należy kontynuować wspieranie aplikacji jako maszyny wirtualne platformy Azure, a nie przechodzenia do zarządzanych usług, takich jak usługa Azure App Service i Azure SQL Database.



### <a name="migration-process"></a>Proces migracji

Contoso zmigruje fronton aplikacji i bazy danych maszyn wirtualnych do maszyn wirtualnych platformy Azure z usługą Site Recovery:

- Pierwszym krokiem Contoso przygotowuje konfiguruje składników platformy Azure dla usługi Site Recovery i przygotowuje lokalnej infrastruktury VMware.
- Mają [infrastruktury platformy Azure](contoso-migration-infrastructure.md) w miejscu, więc Contoso po prostu musi dodać kilka składników platformy Azure specjalnie dla usługi Site Recovery.
- Wszystko, co przygotowany Contoso można rozpocząć replikowanie maszyn wirtualnych.
- Po włączeniu replikacji i pracy, Contoso zostaną zmigrowane maszyny Wirtualnej przez przechodzenia go w tryb failover na platformie Azure.

![Proces migracji](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure. Maszyny wirtualne platformy Azure są tworzone i naliczenia opłat, po przejściu do trybu failover. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) o Naliczanie opłat i ceny.


## <a name="prerequisites"></a>Wymagania wstępne

Oto, czego firma Contoso potrzebuje do uruchomienia tego scenariusza.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Contoso subskrypcje są tworzone we wcześniejszym artykule w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md).
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.<br/><br/> Dowiedz się więcej o określonych [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Lokalne serwery vCenter powinna działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Hosty ESXi, należy uruchomić w wersji 5.5, 6.0 lub 6.5<br/><br/> Maszyny wirtualne VMware, powinna być uruchomiona na hoście ESXi.
**Lokalnych maszyn wirtualnych** | Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono działanie migracji Administratorzy Contoso:

> [!div class="checklist"]
> * **Krok 1. Przygotowywanie platformy Azure dla usługi Site Recovery**: Tworzą konta usługi Azure storage do przechowywania replikowanych danych oraz magazyn usługi Recovery Services.
> * **Krok 2. Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji**: Przygotowywanie konta do maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 3. Replikowanie maszyn wirtualnych**: Konfigurowanie replikacji i rozpocząć replikowanie maszyn wirtualnych do usługi Azure storage.
> * **Krok 4. Migrowanie maszyn wirtualnych z usługą Site Recovery**: One uruchomić testowy tryb failover, aby upewnić się, że wszystko działa, a następnie uruchom tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowywanie platformy Azure dla usługi Site Recovery

Oto składniki platformy Azure, firma Contoso potrzebuje, aby przeprowadzić migrację maszyn wirtualnych na platformie Azure:

- Sieć wirtualna, w której maszyny wirtualne platformy Azure zostaną umieszczone po ich utworzeniu podczas pracy awaryjnej.
- Konto magazynu platformy Azure do przechowywania replikowanych danych.
- Magazyn usługi Recovery Services na platformie Azure.

Mogą skonfigurować je w następujący sposób:

1. Konfigurowanie sieci — Contoso już skonfigurowane sieci, które mogą być dla usługi Site Recovery podczas ich [wdrożona infrastruktura platformy Azure](contoso-migration-infrastructure.md)

    - Aplikacja rozwiązania SmartHotel360 jest aplikacji produkcyjnej i maszyny wirtualne zostaną zmigrowane do sieci platformy Azure środowiska produkcyjnego (sieć wirtualna-PROD-EUS2) w regionie wschodnie stany USA 2 podstawowym.
    - Obie maszyny wirtualne zostaną umieszczone w grupie zasobów ContosoRG, która będzie używana dla zasobów w środowisku produkcyjnym.
    - Fronton aplikacji maszyny Wirtualnej (WEBVM) zostaną zmigrowane do podsieci frontonu (PROD-FE-EUS2) w sieci produkcyjnej.
    - Baza danych aplikacji maszyny Wirtualnej (SQLVM) zostaną zmigrowane do podsieci (PROD-DB-EUS2), bazy danych w sieci produkcyjnej.

2. Konfigurowanie firmy Contoso konta magazynu tworzy konto usługi Azure storage (contosovmsacc20180528) w regionie podstawowym.
   - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
   - Używają konto ogólnego przeznaczenia z magazynu w warstwie standardowa i replikacją LRS.

     ![Site Recovery magazynu](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Tworzenie magazynu z konta magazynu i sieci w miejscu, Contoso teraz tworzy magazyn usługi Recovery Services (ContosoMigrationVault) i umieszcza je w grupie zasobów ContosoFailoverRG w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-vm/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) konfigurowaniu platformy Azure dla usługi Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji

Oto, co Contoso przygotowuje lokalnie:

- Konto vCenter server lub hosta vSphere ESXi, do zautomatyzowania odnajdowania maszyn wirtualnych.
- Konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware.
- Lokalne ustawienia maszyny Wirtualnej tak, aby Contoso można połączyć się z replikowanej maszyny wirtualne platformy Azure po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych.
- Organizowanie replikacji, trybu failover i powrotu po awarii dla maszyn wirtualnych.
- Wymagane jest co najmniej konto tylko do odczytu. Konto powinno być możliwe do uruchomienia operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Administratorzy firmy Contoso, konto jest skonfigurowane w następujący sposób:

1. Tworzą rolę na poziomie vCenter.
2. Tej roli mogą przypisać wymagane uprawnienia.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie Wirtualnej.

- Site Recovery może zrobić wypychania automatycznego instalowania usługi mobilności, po włączeniu replikacji maszyny Wirtualnej.
- Konto usługi jest wymagane, aby Usługa Site Recovery mogą uzyskiwać dostęp do maszyn wirtualnych przez instalację wypychaną. To konto można określić podczas konfigurowania replikacji.
- Konto może być domeny lub lokalnego z uprawnieniami do zainstalowania na maszynach wirtualnych.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po włączeniu trybu failover firma Contoso chce nawiązać połączenie z maszynami wirtualnymi platformy Azure. Aby to zrobić, Administratorzy firmy Contoso, wykonaj następujące czynności przed migracją:

1. Aby uzyskać dostęp za pośrednictwem Internetu one:

   - Włącz protokół RDP na maszynie Wirtualnej w środowisku lokalnym przed włączeniem trybu failover.
   - Upewnij się, że reguły TCP i UDP zostały dodane do **publicznych** profilu.
   - Sprawdź, czy RDP jest dozwolona w **zapory Windows** > **dozwolone aplikacje** we wszystkich profilach.

2. Aby uzyskać dostęp za pośrednictwem sieci VPN typu lokacja lokacja są:

   - Włącz protokół RDP na maszynie lokalnej.
   - Zezwalaj na RDP w **zapory Windows** -> **dozwolone aplikacje i funkcje**, aby uzyskać **domena i prywatne** sieci.
   - Ustaw zasady sieci SAN systemu operacyjnego na maszynie Wirtualnej w środowisku lokalnym **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdzenie następujących kwestii:

- Powinno być nie oczekujących aktualizacji Windows na maszynie Wirtualnej podczas wyzwalania trybu failover. Jeśli, nie będzie mógł zalogować się do maszyny Wirtualnej do momentu ukończenia aktualizacji.
- Po włączeniu trybu failover, można sprawdzić **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie rozwiąże problemu, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli do automatycznego odnajdowania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikowanie lokalnych maszyn wirtualnych

Administratorzy Contoso było uruchomić migrację na platformę Azure, należy skonfigurować i włączyć replikację.

### <a name="set-a-replication-goal"></a>Ustaw cel replikacji

1. W magazynie w obszarze nazwy magazynu (ContosoVMVault) wybierają cel replikacji (**wprowadzenie** > **Site Recovery** > **Przygotowywanie infrastruktury** .
2. Określają, czy ich maszyn w środowisku lokalnym, działające w programie VMware oraz replikacji do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, ich upewnij się, że został ukończony planowania wdrożenia, wybierając **tak, ma to zostało zrobione**. W tym scenariuszu Contoso tylko są migrowane dwie maszyny wirtualne i nie ma potrzeby planowania wdrożenia.


### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Administratorzy firmy Contoso, należy skonfigurować środowisko źródłowe. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu na serwerze konfiguracji mogą zarejestrować go w magazynie.

Serwer konfiguracji jest uruchamiana jest wiele składników:

- Składnik serwera konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.



Administratorzy firmy Contoso wykonaj następujące kroki w następujący sposób:

1. W magazynie, będą oni mogli pobrać szablonu pakietu OVF z **Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. One zaimportuj szablon do programu VMware, aby utworzyć i wdrożyć maszynę Wirtualną.

    ![Szablon OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. Po włączeniu maszyny Wirtualnej po raz pierwszy jej zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, logowania się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzie konfiguracji usługi Azure Site Recovery uruchamia się domyślnie.
5. W narzędziu określają nazwę, aby zarejestrować serwer konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia logowania do subskrypcji platformy Azure. Poświadczenia muszą mieć dostęp do magazynu, w którym będzie zarejestrowania serwera konfiguracji.

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Logowania z maszyną ponownie, a następnie automatycznie uruchomiony Kreator zarządzania serwerem konfiguracji.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
10. Wybierają subskrypcji, grupy zasobów i Magazyn, w którym można zarejestrować serwer konfiguracji.
        ![vault](./media/contoso-migration-rehost-vm/cswiz1.png)

10. One Pobierz i zainstaluj serwer MySQL i interfejs PowerCLI programu VMWare.
11. Po zakończeniu walidacji określają nazwy FQDN lub adres IP vCenter server lub hoście vSphere. Pozostaw domyślny port i określ przyjazną nazwę serwera na platformie Azure.
12. Określ ich konta, utworzonego do automatycznego odnajdowania i poświadczenia, które są używane do automatycznego instalowania usługi mobilności. W przypadku maszyn Windows konto wymaga uprawnień administratora lokalnego na maszynach wirtualnych.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Po zakończeniu rejestracji w witrynie Azure portal, double wydasz czy serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej.
8. Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Administratorzy Contoso określić ustawienia replikacji docelowego.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto usługi Azure storage i sieci w lokalizacji docelowej określonej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Administratorzy firmy Contoso można teraz tworzyć zasady replikacji.

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą zasadę **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg celu punktu odzyskiwania**: Domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**. Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.

        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Włączanie replikacji na potrzeby WEBVM

Wszystko w miejscu Administratorzy firmy Contoso można teraz włączyć replikację dla maszyn wirtualnych. Wszystko zaczyna WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, że chce włączyć maszyn wirtualnych, wybierz serwer vCenter, a serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Wybierają ustawienia docelowego, łącznie z grupy zasobów i sieć platformy Azure i konto magazynu.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Wybierają **WebVM** w przypadku replikacji Sprawdź zasady replikacji, a następnie Włącz replikację.

   - Na tym etapie ich tylko zaznacza WEBVM, ponieważ należy wybrać sieć wirtualną i podsieć, a maszyny wirtualne aplikacji zostaną umieszczone w różnych podsieciach.
   - Po włączeniu replikacji usługa Site Recovery automatycznie instaluje usługę mobilności na maszynie Wirtualnej.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w witrynie Azure portal można zobaczyć strukturę dla maszyn wirtualnych replikowanych do platformy Azure.


### <a name="enable-replication-for-sqlvm"></a>Włączanie replikacji na potrzeby SQLVM

Teraz Administratorzy Contoso rozpocząć replikację na maszynie SQLVM, przy użyciu tego samego procesu jak powyżej.

1. Wybierają ustawienia źródła.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Następnie określają ustawienia wartości docelowej.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Wybierają SQLVM replikacji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Zastosuj te same zasady replikacji, które było używane dla WEBVM i Włącz replikację.

    ![Widok infrastruktury](./media/contoso-migration-rehost-vm/essentials.png)

**Potrzebujesz dodatkowej pomocy?**

- Może odczytywać kompletnym instruktażem wszystkie te kroki [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Możesz dowiedzieć się więcej [włączenie replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrowanie maszyn wirtualnych

Administratorzy firmy Contoso uruchomić szybkie testowanie trybu failover, a następnie pełna trybu failover, aby przeprowadzić migrację maszyn wirtualnych.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Testowanie trybu failover pomaga upewnić się, że wszystko działa zgodnie z oczekiwaniami.

1. Uruchamiają test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się.
3. Testy trybu failover:

    - Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że wszystkie warunki wymagane do migracji w miejscu.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

3. Po zakończeniu trybu failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Sprawdzają, czy maszyna wirtualna ma prawidłowy rozmiar, połączona z odpowiednią siecią i działa.
4. Po zweryfikowaniu testowy tryb failover, ich czyszczenie pracy awaryjnej i zarejestrować i zapisać wszelkie obserwacje.

### <a name="create-and-customize-a-recovery-plan"></a>Twórz i dostosowuj plan odzyskiwania

 Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Administratorzy Contoso utworzyć plan odzyskiwania do migracji.

- Plan odzyskiwania określa kolejność w pracą w trybie failover następuje i wskazuje, jak maszyny wirtualne platformy Azure będą rozstrzygane online na platformie Azure.
- Ponieważ aplikacja dwuwarstwowa, ich dostosowywać plan odzyskiwania, dzięki czemu dane maszyny Wirtualnej (SQLVM) uruchomi się przed frontonu (WEBVM).

1. W **plany odzyskiwania (Usługa Site Recovery)** > **+ planu odzyskiwania**, Utwórz plan i Dodaj maszyny wirtualne do niego.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Po utworzeniu planu, ich dostosowywanie (**plany odzyskiwania** > **SmartHotelMigrationPlan** > **Dostosuj**).
2.  Usuwają WEBVM z **Grupa 1: Rozpocznij**. Dzięki temu pierwszej akcji uruchamiania tylko dotyczy SQLVM.
3.  W **+ grupa** > **Dodaj chronione elementy**, co zwiększa WEBVM grupa 2: Początek. Maszyny wirtualne muszą znajdować się w dwóch różnych grup.


### <a name="migrate-the-vms"></a>Migrowanie maszyn wirtualnych


Teraz Administratorzy Contoso uruchomić pełne trybu failover, aby ukończyć migrację.

1. Wybierają planu odzyskiwania > **trybu Failover**.
2. Należy wybrać, aby przełączyć tryb failover do najnowszego punktu odzyskiwania i że usługa Site Recovery należy dążyć do zamknięcia lokalną maszynę Wirtualną przed wyzwoleniem trybu failover. Mogą śledzić postęp trybu failover **zadań** strony.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover1.png)


3. Po włączeniu trybu failover ich Sprawdź, czy maszyna wirtualna platformy Azure znajduje się w oczekiwany sposób w witrynie Azure portal.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover2.png)

3. Po zakończeniu weryfikacji zakończenia migracji dla każdej maszyny Wirtualnej. Zatrzymanie replikacji maszyny Wirtualnej oraz zatrzymanie naliczania opłat za Site Recovery dla niego.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover3.png)

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) Uruchamianie testu trybu failover.
- [Dowiedz się,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenia w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji warstwy aplikacji rozwiązania SmartHotel360 są teraz uruchomione na maszynach wirtualnych platformy Azure.

Teraz firma Contoso potrzebuje do wykonania tych kroków czyszczenia:

- Usuń maszynę WEBVM z magazynu programu vCenter.
- Usuń maszyny SQLVM z magazynu programu vCenter.
- Usuń WEBVM i SQLVM z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizowanie dokumentacji wewnętrzny, aby wyświetlić nową lokalizację i adresy IP dla maszyn wirtualnych.
- Przejrzyj wszystkie zasoby, które współdziałają z maszyn wirtualnych i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona Contoso teraz musi w pełni operacjonalizacji i zabezpieczyć je na platformie Azure.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso przegląda maszyn wirtualnych platformy Azure, aby określić wszelkie problemy z zabezpieczeniami.

- Aby kontrolować dostęp, zespół przegląda sieciowych grup zabezpieczeń (NSG) dla maszyn wirtualnych. Sieciowe grupy zabezpieczeń są używane do zapewnienia, że tylko ruch do aplikacji można uzyskiwać dostęp go niego.
- Zespół należy również rozważyć zabezpieczenia danych na dysku za pomocą usługi Azure Disk Encryption i magazynu kluczy.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

## <a name="bcdr"></a>BCDR

Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii (BCDR) Contoso wykonuje następujące akcje:

- Zabezpieczanie danych: Contoso tworzy kopie zapasowe danych na maszynach wirtualnych za pomocą usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Zachowaj aplikacji działanie: Contoso są replikowane maszyny wirtualne na platformie Azure aplikacji przy użyciu Site Recovery w regionie pomocniczym. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).



### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

1. Firmy Contoso ma istniejącej licencji dla maszyn wirtualnych i będzie korzystać z korzyści użycia hybrydowego platformy Azure. Contoso przekonwertuje istniejących maszyn wirtualnych platformy Azure, aby móc korzystać z tej ceny.
2. Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze. [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji rozwiązania SmartHotel360 na platformie Azure przy użyciu funkcji migracji aplikacji maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery.


## <a name="next-steps"></a>Kolejne kroki

W [następnego artykułu](contoso-migration-rehost-vm-sql-ag.md) serii, pokażemy ci jak Contoso rehostowaniu frontonu aplikacji rozwiązania SmartHotel360 maszyny Wirtualnej na Maszynie wirtualnej platformy Azure i wykonuje migrację bazy danych do grupy dostępności SQL Server AlwaysOn na platformie Azure.

