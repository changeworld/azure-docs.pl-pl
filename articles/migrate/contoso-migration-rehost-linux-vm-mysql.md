---
title: Ponowne hostowanie aplikacji systemu Linux firmy Contoso usługi technicznej platformy Azure i usługi Azure MySQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu aplikacji systemu Linux w środowisku lokalnym przy użyciu funkcji migracji maszyn wirtualnych platformy Azure i usługi Azure MySQL.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 114f4ccccaa861928263eb59b4e43379989abcca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60670509"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migracja Contoso: Hostuj ponownie lokalną aplikację systemu Linux na maszynach wirtualnych platformy Azure i w usłudze Azure MySQL

W tym artykule pokazano, jak Contoso rehostowaniu jej w środowisku lokalnym dwuwarstwowej Linux technicznej app service (osTicket) przy użyciu funkcji migracji do platformy Azure i usługi Azure MySQL.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują sposób konfigurowania infrastruktury migracji i uruchamiania różnych rodzajów migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne 
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zakończeniu migracji lift-and-shift aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, za pomocą usługi Azure Site Recovery | Dostępne
Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL | Contoso aplikacji osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | W tym artykule
[Artykuł 9: Refaktoryzuj aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso aplikacji rozwiązania SmartHotel360 jest migrowana do aplikacji sieci Web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne 
[Artykuł 11: Refaktoryzuj TFS na usługom DevOps platformy Azure](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji na kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne


W tym artykule Contoso migruje dwuwarstwowej Linux Apache MySQL PHP (LAMP) aplikacji usługi, która działu (osTicket) na platformie Azure. Jeśli chcesz korzystać z tej aplikacji typu open source, możesz ją pobrać z [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć:

- **Adres rozwój**: Rośnie firmy Contoso, a w rezultacie istnieje nacisk na systemach lokalnych i infrastruktury.
- **Ogranicz ryzyko**: Usługi app Service desk jest krytyczny dla działalności. Firma Contoso chce przenieść go do platformy Azure z ryzykiem, zerowego.
- **Rozszerzanie**:  Contoso nie chcesz zmienić aplikację teraz. Po prostu chce zachować stabilnych aplikacji.


## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte w dół do celów migracji, aby określić najlepszą metodę migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku programu VMware w środowisku lokalnym.  Aplikacja pozostanie tak krytyczne w chmurze, ponieważ jest w środowisku lokalnym. 
- Contoso nie chcesz inwestować w tej aplikacji.  Ważne jest, aby firmy, ale w obecnej formie Contoso po prostu chcesz bezpiecznie przenieść je do chmury.
- Po zakończeniu kilka migracji aplikacji Windows, firma Contoso chce dowiedzieć się, jak używać w infrastrukturze opartej na systemie Linux na platformie Azure.
- Firma Contoso chce, aby zminimalizować zadania administratora bazy danych, po przeniesieniu aplikacji w chmurze.

## <a name="proposed-architecture"></a>Proponowana architektury

W tym scenariuszu:

- Aplikacja jest rozmieszczone warstwowo na dwie maszyny wirtualne (OSTICKETWEB i OSTICKETMYSQL).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Aplikacja sieci web warstwy na OSTICKETWEB zostaną zmigrowane do maszyny Wirtualnej IaaS platformy Azure.
- Bazy danych aplikacji zostaną poddane migracji do usługi Azure Database for MySQL PaaS usługi.
- Ponieważ Contoso przeprowadza migrację obciążeń produkcyjnych, zasoby będą znajdować się w grupie zasobów w środowisku produkcyjnym **ContosoRG**.
- Zasoby będą replikowane do regionu podstawowego (wschodnie stany USA 2) i umieszczane w sieci produkcyjnej (sieć wirtualna-PROD-EUS2):
    - Maszyna wirtualna w sieci web będą znajdować się w podsieci frontend (PROD-FE-EUS2).
    - Wystąpienie bazy danych będą znajdować się w podsieci bazy danych (PROD-DB-EUS2).
- Baza danych aplikacji zostaną poddane migracji do usługi Azure MySQL przy użyciu narzędzia MySQL.
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


![Architektura scenariusza](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Proces migracji

Contoso zakończy proces migracji w następujący sposób:

Aby przeprowadzić migrację maszyny Wirtualnej w sieci web:

1. Pierwszym krokiem Contoso konfiguruje platformy Azure i w obrębie lokalnej infrastruktury potrzebne do wdrożenia usługi Site Recovery.
2. Po przygotowaniu składniki platformy Azure i w środowisku lokalnym, Contoso konfiguruje i umożliwia replikację dla maszyny Wirtualnej w sieci web.
3. Jeśli replikacja ma w górę i uruchomiona, Contoso migruje maszyny Wirtualnej przez przechodzenia go w tryb failover na platformie Azure.

Aby przeprowadzić migrację bazy danych:

1. Firmy Contoso inicjuje wystąpienia programu MySQL na platformie Azure.
2. Contoso konfiguruje aplikację MySQL workbench i tworzy kopię zapasową bazy danych lokalnie.
3. Contoso następnie przywróć ją z lokalna kopia zapasowa na platformie Azure.

![Proces migracji](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i naliczenia opłat, po przejściu do trybu failover. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) o Naliczanie opłat i ceny.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Baza danych jest oparta na aparacie serwer MySQL typu open source. Zapewnia społeczności w pełni zarządzane, gotowa do użycia w przedsiębiorstwie w przypadku bazy danych MySQL jako usługi w celu tworzenia i wdrażania aplikacji. 

 
## <a name="prerequisites"></a>Wymagania wstępne

Oto, co firma Contoso potrzebuje dla tego scenariusza.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Contoso utworzone subskrypcje wcześniejszy artykuł. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura platformy Azure** | Konfigurowanie infrastruktury platformy Azure, zgodnie z opisem w contoso [infrastruktury platformy Azure na potrzeby migracji](contoso-migration-infrastructure.md).<br/><br/> Dowiedz się więcej o określonych [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Lokalny serwer vCenter powinny działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Host ESXi w wersji 5.5, 6.0 lub 6.5<br/><br/> Co najmniej jeden maszyn wirtualnych programu VMware działające na hoście ESXi.
**Lokalnych maszyn wirtualnych** | [Przejrzyj wymagania dotyczące maszyny Wirtualnej systemu Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) , są obsługiwane w przypadku migracji z usługą Site Recovery.<br/><br/> Sprawdź obsługiwany [systemów plików i magazynowania Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Contoso zostanie wykonany przez administratorów migracji:

> [!div class="checklist"]
> * **Krok 1. Przygotowywanie platformy Azure dla usługi Site Recovery**: Tworzą konta usługi Azure storage do przechowywania zreplikowane dane i utworzyć magazyn usługi Recovery Services.
> * **Krok 2. Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji**: Przygotowywanie konta do maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
>   * **Krok 3. Aprowizowanie bazy danych]**: Na platformie Azure udostępnia wystąpienie bazy danych Azure MySQL.
> * **Krok 4. Replikowanie maszyn wirtualnych**: Ich Konfigurowanie środowiska źródłowego i docelowego Site Recovery, skonfiguruj zasady replikacji i rozpocząć replikowanie maszyn wirtualnych do usługi Azure storage.
> * **Krok 5. Migrowanie bazy danych**: Konfigurowania migracji za pomocą narzędzi MySQL.
> * **Krok 6: Migrowanie maszyn wirtualnych z usługą Site Recovery**: Na koniec one uruchomić testowy tryb failover, aby upewnić się, że wszystko działa, a następnie uruchom tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowywanie platformy Azure dla usługi Site Recovery

Firma Contoso potrzebuje kilka składników platformy Azure dla usługi Site Recovery:

- Sieć wirtualną, w którym przełączone w tryb failover znajdują się zasoby. Contoso utworzonej sieci wirtualnej podczas [wdrażania infrastruktury platformy Azure](contoso-migration-infrastructure.md)
- Nowe konto usługi Azure storage do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.

Administratorzy firmy Contoso Tworzenie konta magazynu i Magazyn w następujący sposób:

1. One tworzenie konta magazynu (**contosovmsacc20180528**) w regionie wschodnie stany USA 2.

   - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
   - Używają konta ogólnego przeznaczenia z magazynu w warstwie standardowa i replikacją LRS.

     ![Site Recovery magazynu](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Za pomocą konta magazynu i sieci w miejscu utworzyć magazyn (ContosoMigrationVault) i umieść go w **ContosoFailoverRG** grupę zasobów, w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) konfigurowaniu platformy Azure dla usługi Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowywanie lokalnych zasobów programu VMware do odzyskiwania lokacji

Administratorzy firmy Contoso Przygotuj lokalną infrastrukturę VMware w następujący sposób:

- Na serwerze vCenter w celu zautomatyzowania odnajdowania maszyn wirtualnych mogą utworzyć konto usługi.
- Tworzą konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware, która będzie replikowana.
- Lokalnych maszyn wirtualnych, ich przygotowywanie tak, aby umożliwić im połączenie z maszynami wirtualnymi platformy Azure, po ich utworzeniu po zakończeniu migracji.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Wymagane jest konto, które można uruchamiać operacje, takie jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Administratorzy firmy Contoso, konto jest skonfigurowane w następujący sposób:

1. Tworzą rolę na poziomie vCenter.
2. Są następnie przypisać tę rolę wymaganych uprawnień.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie Wirtualnej, która Contoso chce, aby przeprowadzić migrację.

- Site Recovery może zrobić wypychania automatycznego instalowania tego składnika, po włączeniu replikacji dla maszyn wirtualnych.
- Instalacja automatyczna. Usługa Site Recovery wymaga konta z uprawnieniami do dostęp do maszyny Wirtualnej. 
- Szczegóły konta są dane wejściowe podczas konfigurowania replikacji. 
- To konto może być domeny lub lokalnego konta, tak długo, jak długo ma uprawnienia do instalacji.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover na platformie Azure Contoso chce można było połączyć się z maszynami wirtualnymi platformy Azure. Aby to zrobić, Administratorzy Contoso muszą wykonać następujące czynności:

- Aby uzyskać dostęp za pośrednictwem Internetu, umożliwiają one SSH w systemie Linux w środowisku lokalnym maszyny Wirtualnej przed migracją.  Dla systemu Ubuntu można to zrobić za pomocą następującego polecenia: **"Sudo" polecenia apt-get ssh zainstalować -y**.
- Po włączeniu trybu failover, należy sprawdzić **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej.
- Jeśli to nie rozwiąże problemu, należy sprawdzić, czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli do automatycznego odnajdowania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-provision-azure-database-for-mysql"></a>Krok 3: Inicjowanie obsługi administracyjnej Azure Database dla MySQL

Administratorzy firmy Contoso aprowizować wystąpienie bazy danych MySQL w regionie wschodnie stany USA 2 podstawowym.

1. W witrynie Azure portal tworzą usługi Azure Database for MySQL zasobów. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Dodaj ich nazwę **contosoosticket** dla bazy danych platformy Azure. Mogą dodać bazę danych do grupy zasobów w środowisku produkcyjnym **ContosoRG**, a następnie określ poświadczenia dla niego.
3. Lokalna baza danych MySQL jest wersji 5.7, więc wybierają tej wersji do zgodności. Używają domyślnym rozmiarze, odpowiadających ich wymagania dotyczące bazy danych.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Aby uzyskać **Opcje nadmiarowości kopii zapasowej**, należy wybrać, aby użyć **magazynu geograficznie nadmiarowego**. Ta opcja służy do przywracania bazy danych w regionie środkowe stany USA, ich pomocniczym, jeśli wystąpi awaria. One można skonfigurować tylko tę opcję, gdy ich aprowizować bazę danych.

     ![Nadmiarowość](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. W **VNET-PROD-EUS2** sieci > **punkty końcowe usługi**, mogą dodać punkt końcowy usługi (podsiecią bazy danych) dla usługi programu SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Po dodaniu do podsieci, tworzą regułę sieci wirtualnej, która umożliwia dostęp z podsieci bazy danych w sieci produkcyjnej.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Krok 4: Replikowanie lokalnych maszyn wirtualnych

Przed ich migracji maszyn wirtualnych w sieci web na platformie Azure, Administratorzy Contoso ustawienie i Włącz replikację.

### <a name="set-a-protection-goal"></a>Ustaw cel ochrony

1. W magazynie w obszarze nazwy magazynu (ContosoVMVault) ustawiają cel replikacji (**wprowadzenie** > **Site Recovery** > **przygotowanie infrastruktury**.
2. Określają, czy ich maszyn w środowisku lokalnym, że są one i maszyn wirtualnych VMware, czy mają być replikowane na platformie Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, ich upewnij się, że ich ukończeniu planowania wdrożenia, wybierając **tak, ma to zostało zrobione**. Contoso są tylko migrowania jednej maszyny Wirtualnej, w tym scenariuszu i nie ma potrzeby planowania wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Administratorzy firmy Contoso teraz skonfigurować środowisko źródłowe. Aby to zrobić, za pomocą szablonu pakietu OVF, ich wdrażanie serwera konfiguracji usługi Site Recovery w trybie wysokiej dostępności, lokalnych maszyn wirtualnych programu VMware. Po skonfigurowaniu i uruchomieniu na serwerze konfiguracji mogą zarejestrować go w magazynie.

Serwer konfiguracji jest uruchamiana jest wiele składników:

- Składnik serwera konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Administratorzy firmy Contoso to zrobić w następujący sposób:


1. Będą oni mogli pobrać szablonu pakietu OVF z **Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Zaimportuj szablon do programu VMware do utworzenia maszyny Wirtualnej i wdrażanie maszyny Wirtualnej.

    ![Szablon OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Po włączeniu maszyny Wirtualnej po raz pierwszy jej zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, logowania się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzie konfiguracji usługi Azure Site Recovery uruchamia się domyślnie.
5. W narzędziu określają nazwy do rejestrowania serwera konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure.
7. Po nawiązaniu połączenia logowania do subskrypcji platformy Azure. Poświadczenia muszą mieć dostęp do magazynu, w którym będzie zarejestrowania serwera konfiguracji.

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
9. Logowania z maszyną ponownie, a następnie automatycznie uruchomiony Kreator zarządzania serwerem konfiguracji.
10. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
11. Wybierają subskrypcji, grupy zasobów i Magazyn, w którym można zarejestrować serwer konfiguracji.

    ![Vault](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Teraz Pobierz i zainstaluj serwer MySQL i interfejs PowerCLI programu VMWare. 
13. Po zakończeniu walidacji określają nazwy FQDN lub adres IP vCenter server lub hoście vSphere. Pozostaw domyślny port i określ przyjazną nazwę dla serwera vCenter.
14. Wprowadź ich konta, utworzonego do automatycznego odnajdowania i poświadczenia, których usługa Site Recovery będzie używać do automatycznego instalowania usługi mobilności. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Po zakończeniu rejestracji w witrynie Azure portal, sprawdzają, czy serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
15. Wszystko w miejscu Usługa Site Recovery nawiąże połączenie z serwerami VMware i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Administratorzy firmy Contoso, wprowadź ustawienia replikacji obiektu docelowego.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto usługi Azure storage i sieci w określonej lokalizacji docelowej.


### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Za pomocą źródłowego i docelowego, konfigurowanie Administratorzy Contoso przystąpić do tworzenia zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą zasadę **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg celu punktu odzyskiwania**: Domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**. Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Potrzebujesz dodatkowej pomocy?**

- Może odczytywać kompletnym instruktażem wszystkie te kroki [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych VMware w środowisku lokalnym](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrażanie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o agenta gościa platformy Azure dla systemu Linux.

### <a name="enable-replication-for-the-web-vm"></a>Włącz replikację dla maszyny Wirtualnej w sieci Web

Teraz firmy Contoso, Administratorzy mogą rozpocząć replikowanie **OSTICKETWEB** maszyny Wirtualnej.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, że chce włączyć maszyn wirtualnych, a następnie wybierz pozycję Ustawienia źródła, w tym serwera vCenter, a serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Teraz określić ustawienia wartości docelowej. Należą do grupy zasobów i sieci, w którym maszyna wirtualna platformy Azure zostaną umieszczone po włączeniu trybu failover oraz konto magazynu, w którym przechowywane będą replikowane dane. 

     ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Wybierają **OSTICKETWEB** replikacji. 

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. We właściwościach maszyny Wirtualnej wybierają konta, które mają być używane do automatycznego instalowania usługi mobilności na maszynie Wirtualnej.

     ![Usługa mobilności](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. w **ustawień replikacji** > **Konfigurowanie ustawień replikacji**, mogą sprawdzić, czy właściwe zasady replikacji zastosowane i wybierz pozycję **włączania replikacji**. Usługa Mobility zostanie zainstalowana automatycznie.
6.  Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.


**Potrzebujesz dodatkowej pomocy?**

Może odczytywać kompletnym instruktażem wszystkie te kroki [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Krok 5. Migrowanie bazy danych

Administratorzy firmy Contoso migracji bazy danych przy użyciu kopii zapasowej i przywracania, przy użyciu narzędzia MySQL. Ich instalacja aplikacji MySQL Workbench, wykonaj kopię zapasową bazy danych z OSTICKETMYSQL, a następnie przywrócić do usługi Azure Database dla serwera MySQL.

### <a name="install-mysql-workbench"></a>Instalacja aplikacji MySQL Workbench

1. Wydasz [wymagań wstępnych oraz pliki do pobrania aplikacji MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Podczas instalacji aplikacji MySQL Workbench for Windows zgodnie z [instrukcje dotyczące instalacji](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. W aplikacji MySQL Workbench tworzą połączenia MySQL OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Ich eksportowania bazy danych jako **osticket**, do lokalnego pliku niezależna.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Po bazy danych ma lokalnie kopii zapasowej, tworzą połączenie usługi Azure Database for MySQL — wystąpienia.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Teraz ich można zaimportować plik (przywracanie) bazy danych w wystąpieniu usługi Azure MySQL z niezależna. Nowy schemat (osticket) jest tworzony dla tego wystąpienia.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Krok 6: Migrowanie maszyn wirtualnych z usługą Site Recovery

Na koniec Administratorzy Contoso uruchomić szybkie testowanie trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Uruchamianie testu trybu failover pomaga sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, przed rozpoczęciem migracji. 

1. Uruchamiają test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 

    - Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że wszystkie warunki wymagane do migracji w miejscu.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

3. Po zakończeniu trybu failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Sprawdzają, czy maszyna wirtualna jest odpowiedni rozmiar, który jest połączony z odpowiednią siecią i czy działa. 
4. Po zweryfikowaniu, ich czyszczenie pracy awaryjnej i zarejestrować i zapisać wszelkie obserwacje.

### <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

Aby przeprowadzić migrację maszyny Wirtualnej, Administratorzy Contoso tworzy plan odzyskiwania, który zawiera maszynę Wirtualną i tryb failover planu na platformie Azure.

1. Tworzenie planu i Dodaj **OSTICKETWEB** do niego.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Działają one przejścia w tryb failover dla planu. Wybierz najnowszy punkt odzyskiwania i określ, czy usługa Site Recovery należy dążyć do zamknięcia lokalną maszynę Wirtualną przed wyzwoleniem trybu failover. Mogą śledzić postęp trybu failover **zadań** strony.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Podczas pracy awaryjnej programu vCenter Server wydaje polecenia, aby zatrzymać dwóch maszyn wirtualnych uruchomionych na hoście ESXi.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Po włączeniu trybu failover ich Sprawdź, czy maszyna wirtualna platformy Azure znajduje się w oczekiwany sposób w witrynie Azure portal.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Po sprawdzeniu maszyny Wirtualnej, ich ukończenia migracji. Zatrzymanie replikacji maszyny Wirtualnej oraz zatrzymanie naliczania opłat za Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) Uruchamianie testu trybu failover. 
- [Dowiedz się,](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenia w tryb failover na platformie Azure.


### <a name="connect-the-vm-to-the-database"></a>Połącz maszynę Wirtualną do bazy danych

Ostatni krok w procesie migracji Administratorzy Contoso zaktualizować parametry połączenia aplikacji do punktu do usługi Azure Database for MySQL. 

1. One Utwórz połączenie SSH z maszyną wirtualną OSTICKETWEB przy użyciu programu Putty lub innego klienta SSH. Maszyna wirtualna jest prywatny, dzięki czemu łączą się przy użyciu prywatnego adresu IP.

    ![nawiązać połączenie z bazą danych](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![nawiązać połączenie z bazą danych](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Zaktualizowanie ustawień, aby **OSTICKETWEB** maszyny Wirtualnej mogą komunikować się z **OSTICKETMYSQL** bazy danych. Obecnie konfiguracja zostanie zapisane na stałe o adresie IP 172.16.0.43 w środowisku lokalnym.

    **Przed aktualizacją**
    
    ![Aktualizowanie adresów IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Po aktualizacji**
    
    ![Aktualizowanie adresów IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Aktualizowanie adresów IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Ich ponowne uruchomienie usługi przy użyciu **systemctl ponowne uruchomienie oprogramowania apache2**.

    ![Ponowne uruchamianie](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Na koniec zaktualizuj one rekordy DNS dla **OSTICKETWEB**, na jednym z kontrolerów domeny Contoso.

    ![Aktualizowanie systemu DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji osTicket warstwy aplikacji są uruchomione na maszynach wirtualnych platformy Azure.

Teraz Contoso musi wykonać następujące czynności: 
- Usuń maszyny wirtualne programu VMware z magazynu programu vCenter
- Usuń lokalne maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Aktualizacja wewnętrznego dokumentach przedstawiono informacje na nowym lokalizacjom i adresów IP. 
- Przejrzyj wszystkie zasoby, które współdziałają z lokalnych maszyn wirtualnych i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.
- Contoso użycia usługi Azure Migrate z mapowaniem zależności w celu oceny **OSTICKETWEB** maszyny Wirtualnej na potrzeby migracji. Powinny one teraz usunąć agentów (Microsoft Monitoring Agent/Dependency Agent), są zainstalowane w tym celu z maszyny Wirtualnej.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona Contoso muszą w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds. zabezpieczeń Contoso Przejrzyj maszyny Wirtualnej i bazy danych, aby określić wszelkie problemy z zabezpieczeniami.

- Sieciowe grupy zabezpieczeń (NSG) dla maszyny Wirtualnej, aby kontrolować dostęp do przeglądu. Sieciowe grupy zabezpieczeń są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Rozważa zabezpieczania danych na dyskach maszyn wirtualnych przy użyciu szyfrowania dysku i usłudze Azure KeyVault.
- Komunikacja między wystąpienia maszyn wirtualnych i bazy danych nie jest skonfigurowana do obsługi protokołu SSL. Należy to zrobić, aby upewnić się, że ruchu bazy danych nie może stać się celem ataku.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="bcdr"></a>BCDR

Ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii Contoso wykonuje następujące akcje:

- **Zabezpieczanie danych**: Contoso tworzy kopie zapasowe danych w aplikacji maszyny Wirtualnej przy użyciu usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nie muszą skonfigurować kopię zapasową bazy danych. Usługa Azure Database for MySQL — automatycznie tworzy i przechowuje kopie zapasowe serwera. Została wybrana opcja używania nadmiarowości geograficznej bazy danych, więc odpornej na błędy i gotowe do produkcji.
- **Zachowaj aplikacji działanie**: Contoso są replikowane maszyny wirtualne na platformie Azure aplikacji przy użyciu Site Recovery w regionie pomocniczym. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu zasobów, Contoso przypisuje tagi platformy Azure, zgodnie z ich decyzje podczas [infrastruktury platformy Azure](contoso-migration-infrastructure.md#set-up-tagging) wdrożenia.
- Nie są problemy licencjonowania dla serwerów z systemem Ubuntu firmy Contoso.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.


## <a name="next-steps"></a>Kolejne kroki

W tym scenariuszu pokazaliśmy scenariuszy rehost końcowej. Contoso migracji frontonu VM aplikacji osTicket systemu Linux w środowisku lokalnym na Maszynie wirtualnej platformy Azure i migrować bazy danych aplikacji na wystąpienie usługi Azure MySQL.

W następnym zestawem samouczkach z tej serii migracji zamierzamy dowiesz się, jak Contoso wykonywane bardziej złożonego zestawu na potrzeby migracji, obejmujące refaktoryzacji aplikacji, zamiast prostego migracje lift-and-shift.
