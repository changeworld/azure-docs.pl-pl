---
title: Ponowne hostowanie aplikacji firmy Contoso za pomocą migracji na maszyny wirtualne platformy Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ponowne hostowanie aplikacji w środowisku lokalnym i przy użyciu migracji lift-and-shift na platformę Azure do migracji maszyn lokalnych przy użyciu usługi Azure Site Recovery usługi.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: e152a95a392fd503e027591a4930fbeef744d6f4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009420"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migracja Contoso: ponowne hostowanie aplikacji w środowisku lokalnym na maszynach wirtualnych platformy Azure


W tym artykule przedstawiono, jak Contoso rehostowaniu SmartHotel aplikacji lokalnych na platformie Azure, migrując maszyny wirtualne aplikacji na maszynach wirtualnych platformy Azure.


W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso jest migrowana zasobów n lokalnych do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania, a dodamy dodatkowe artykuły, wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny aplikacji SmartHotel dwuwarstwowej lokalnych działających w oprogramowaniu firmy VMware. Contoso ocenia maszynach wirtualnych usługi app przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak firmy Contoso jest wykonywany migracji lift-and-shift na platformie Azure dla lokalnych aplikacji SmartHotel. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzanego SQL, przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure | Pokazuje, jak Contoso migrację aplikacji SmartHotel maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | W tym artykule.
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i programu SQL Server zawsze włączona grupa dostępności](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do klastra programu SQL Server chronione przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest lift-and-shift migracji aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu funkcji odzyskiwania lokacji i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzacja aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne
[Artykuł 11: Refaktoryzacja TFS w usłudze VSTS](contoso-migration-tfs-vsts.md) | Pokazuje, jak Contoso migruje ich lokalnego wdrożenia Team Foundation Server (TFS), migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure. | Dostępne
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompilować aplikację na platformie Azure](contoso-migration-rebuild.md) | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | Dostępne



W tym artykule Contoso zostaną zmigrowane Windows dwuwarstwowy. Aplikacja NET SmartHotel działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji jest przewidziana typu open source i można go pobrać z [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso się, a w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Ogranicz ryzyko**: aplikacja SmartHotel jest krytyczne dla działalności firmy Contoso. Firma chce przenieść go do platformy Azure z ryzykiem, zerowego.
- **Rozszerzanie**: Contoso nie chce zmodyfikować aplikację. Firma chce po prostu upewnij się, że jest stabilna.


## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele są używane do określenia najlepszej metody migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku VMware.  Aplikacja pozostanie tak krytyczne w chmurze, ponieważ jest w środowisku lokalnym. 
- Contoso nie chcesz inwestować w tej aplikacji.  Ważne jest, aby firmy, ale w obecnej formie po prostu chcą bezpiecznie przenieść je do chmury.
- Contoso nie chce zmienić model ops dla tej aplikacji. Firma chce korzystać z niego w chmurze w taki sam sposób, jak teraz.
- Contoso nie chce zmienić dowolne funkcje aplikacji. Zmieni się tylko do lokalizacji aplikacji.

## <a name="proposed-architecture"></a>Proponowana architektury

Poniżej przedstawiono bieżące środowisko

- Aplikacja jest warstwowa między dwiema maszynami wirtualnymi (**WEBVM** i **SQLVM**).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.

![Architektura scenariusza](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Proces migracji

Contoso zmigruje fronton aplikacji i bazy danych maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu Site Recovery:

- Pierwszym krokiem one przygotowywanie i konfigurowanie składników platformy Azure dla usługi Site Recovery i przygotowywanie lokalnej infrastruktury VMware.
- Mają swoje [infrastruktury platformy Azure](contoso-migration-infrastructure.md) w miejscu, więc po prostu należy dodać kilka składników platformy Azure specjalnie dla usługi Site Recovery.
- Ze wszystkim, co przygotowany można uruchomić, replikowanie maszyn wirtualnych.
— Po włączeniu replikacji i pracy, migrowania maszyny Wirtualnej przez przechodzenia go w tryb failover na platformie Azure.

![Proces migracji](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure i lokalnych maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure są naliczane opłaty za magazyn Azure.  Maszyny wirtualne platformy Azure są tworzone i naliczenia opłat, po przejściu do trybu failover. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) o Naliczanie opłat i ceny.


## <a name="prerequisites"></a>Wymagania wstępne

Oto, co Ty (i firmy Contoso) potrzebne do uruchomienia tego scenariusza.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcję w okresie wcześniejsze artykuły w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.<br/><br/> Dowiedz się więcej o określonych [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Lokalne serwery vCenter powinna działać w wersji 5.5, 6.0 lub 6.5<br/><br/> Hosty ESXi, należy uruchomić w wersji 5.5, 6.0 lub 6.5<br/><br/> Maszyny wirtualne VMware, powinna być uruchomiona na hoście ESXi.
**Lokalnych maszyn wirtualnych** | Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1: Przygotowanie usługi Azure Site Recovery**: tworzą konta usługi Azure storage do przechowywania replikowanych danych oraz magazyn usługi Recovery Services.
> * **Krok 2: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji**: przygotowywanie konta do maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi platformy Azure po włączeniu trybu failover.
> * **Krok 3: Replikowanie maszyn wirtualnych**: konfigurowania replikacji i rozpocząć replikowanie maszyn wirtualnych do usługi Azure storage.
> * **Krok 4: Migrowanie maszyn wirtualnych z usługą Site Recovery**: one uruchomić testowy tryb failover, aby upewnić się, że wszystko działa, a następnie uruchom tryb failover pełnej migracji maszyn wirtualnych na platformie Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowanie platformy Azure dla usługi Site Recovery

Oto składniki platformy Azure, firma Contoso potrzebuje, aby przeprowadzić migrację maszyn wirtualnych na platformie Azure:

- Sieć wirtualna, w której maszyny wirtualne platformy Azure zostaną umieszczone po ich utworzeniu podczas pracy awaryjnej.
- Konto magazynu platformy Azure do przechowywania replikowanych danych. 
- Magazyn usługi Recovery Services na platformie Azure.

Mogą skonfigurować je w następujący sposób:

1. Contoso już skonfigurowane sieci używają usługi Site Recovery podczas ich [wdrożona infrastruktura platformy Azure](contoso-migration-infrastructure.md)

    - Aplikacja SmartHotel jest aplikacji produkcyjnej, a maszyny wirtualne zostaną zmigrowane do sieci platformy Azure środowiska produkcyjnego (sieć wirtualna-PROD-EUS2) w regionie wschodnie stany USA 2 podstawowym.
    - Obie maszyny wirtualne zostaną umieszczone w grupie zasobów ContosoRG, która będzie używana dla zasobów w środowisku produkcyjnym.
    - Fronton aplikacji maszyny Wirtualnej (WEBVM) zostaną zmigrowane do podsieci frontonu (PROD-FE-EUS2) w sieci produkcyjnej.
    - Baza danych aplikacji maszyny Wirtualnej (SQLVM) zostaną zmigrowane do podsieci (PROD-DB-EUS2), bazy danych w sieci produkcyjnej.

2. Contoso tworzy konto usługi Azure storage (contosovmsacc20180528) w regionie podstawowym.
    - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
    - Używają konto ogólnego przeznaczenia z magazynu w warstwie standardowa i replikacją LRS. 

    ![Site Recovery magazynu](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Za pomocą konta magazynu i sieci w miejscu Contoso teraz utworzenie magazynu usługi Recovery Services (ContosoMigrationVault) i umieszcza je w grupie zasobów ContosoFailoverRG w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-vm/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) konfigurowaniu platformy Azure dla usługi Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowanie lokalnego wdrożenia oprogramowania VMware do odzyskiwania lokacji

Oto, co Contoso przygotowuje lokalnie:

- Konto vCenter server lub hosta vSphere ESXi, do zautomatyzowania odnajdowania maszyn wirtualnych.
- Konta, które umożliwia automatyczną instalację usługi mobilności na maszynach wirtualnych VMware. 
- Lokalne ustawienia maszyny Wirtualnej tak, aby Contoso można połączyć się z replikowanej maszyny wirtualne platformy Azure po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. 
- Organizowanie replikacji, trybu failover i powrotu po awarii dla maszyn wirtualnych.
- Wymagane jest co najmniej konto tylko do odczytu. Konto powinno być możliwe do uruchomienia operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso konfiguruje konto w następujący sposób:

1. Tworzą rolę na poziomie vCenter.
2. Tej roli mogą przypisać wymagane uprawnienia.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie Wirtualnej.

- Site Recovery może zrobić wypychania automatycznego instalowania usługi mobilności, po włączeniu replikacji maszyny Wirtualnej.
- Konto usługi jest wymagane, aby Usługa Site Recovery mogą uzyskiwać dostęp do maszyn wirtualnych przez instalację wypychaną. To konto można określić podczas konfigurowania replikacji.
- Konto może być domeny lub lokalnego z uprawnieniami do zainstalowania na maszynach wirtualnych.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po włączeniu trybu failover firma Contoso chce nawiązać połączenie z maszynami wirtualnymi platformy Azure. Aby to zrobić, ich wykonaj następujące czynności przed migracją:

1. Aby uzyskać dostęp za pośrednictwem Internetu one:

 - Włącz protokół RDP na maszynie Wirtualnej w środowisku lokalnym przed włączeniem trybu failover
 - Upewnij się, że reguły TCP i UDP zostały dodane do **publicznych** profilu.
 - Sprawdź, czy RDP jest dozwolona w **zapory Windows** > **dozwolone aplikacje** we wszystkich profilach.
 
2. Aby uzyskać dostęp za pośrednictwem sieci VPN typu lokacja lokacja są:

 - Włącz protokół RDP na maszynie lokalnej.
 - Zezwalaj na RDP w **zapory Windows** -> **dozwolone aplikacje i funkcje**, aby uzyskać **domena i prywatne** sieci.
 - Ustaw zasady sieci SAN systemu operacyjnego na maszynie Wirtualnej w środowisku lokalnym **OnlineAll**.

Ponadto po uruchomieniu trybu failover muszą sprawdzenie następujących kwestii:

- Powinno być nie oczekujących aktualizacji Windows na maszynie Wirtualnej podczas wyzwalania trybu failover. Jeśli, nie będzie mógł zalogować się do maszyny Wirtualnej do momentu ukończenia aktualizacji.
- Po włączeniu trybu failover, można sprawdzić **diagnostykę rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli to nie rozwiąże problemu, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzyj te [wskazówki dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli do automatycznego odnajdowania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikowanie maszyn wirtualnych w środowisku lokalnym

Przed uruchomieniem przez nich migracji na platformę Azure, firma Contoso potrzebuje do konfigurowania i włączania replikacji.

### <a name="set-a-replication-goal"></a>Ustaw cel replikacji

1. W magazynie w obszarze nazwy magazynu (ContosoVMVault) wybierają cel replikacji (**wprowadzenie** > **Site Recovery** > **Przygotowywanie infrastruktury** .
2. Określają, czy ich maszyn w środowisku lokalnym, działające w programie VMware oraz replikacji do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Aby kontynuować, ich upewnij się, że został ukończony planowania wdrożenia, wybierając **tak, ma to zostało zrobione**. W tym scenariuszu Contoso tylko są migrowane dwie maszyny wirtualne i nie ma potrzeby planowania wdrożenia.


### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Firma Contoso potrzebuje skonfigurować swoje środowisko źródłowe. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery w trybie wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu na serwerze konfiguracji mogą zarejestrować go w magazynie.

Serwer konfiguracji jest uruchamiana jest wiele składników:

- Składnik serwera konfiguracji, który służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.



Contoso wykonuje następujące kroki w następujący sposób:

1. W magazynie, będą oni mogli pobrać szablonu pakietu OVF z **Przygotuj infrastrukturę** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. One zaimportuj szablon do programu VMware, aby utworzyć i wdrożyć maszynę Wirtualną.

    ![Szablon OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Po włączeniu maszyny Wirtualnej po raz pierwszy jej zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, logowania się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzie konfiguracji usługi Azure Site Recovery uruchamia się domyślnie.
5. W narzędziu określają nazwę, aby zarejestrować serwer konfiguracji w magazynie.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia logowania do subskrypcji platformy Azure. Poświadczenia muszą mieć dostęp do magazynu, w którym będzie zarejestrowania serwera konfiguracji.

    ![Rejestrowanie serwera konfiguracji](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Logowania z maszyną ponownie, a następnie automatycznie uruchomiony Kreator zarządzania serwerem konfiguracji.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić to ustawienie, po skonfigurowaniu go.
10. Wybierają subskrypcji, grupy zasobów i Magazyn, w którym można zarejestrować serwer konfiguracji.
        ![Vault](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. One Pobierz i zainstaluj serwer MySQL i interfejs PowerCLI programu VMWare. 
11. Po zakończeniu walidacji określają nazwy FQDN lub adres IP vCenter server lub hoście vSphere. Pozostaw domyślny port i określ przyjazną nazwę serwera na platformie Azure.
12. Określ ich konta, utworzonego do automatycznego odnajdowania i poświadczenia, które są używane do automatycznego instalowania usługi mobilności. W przypadku maszyn Windows konto wymaga uprawnień administratora lokalnego na maszynach wirtualnych.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Po zakończeniu rejestracji w witrynie Azure portal firmy Contoso double sprawdza, czy serwer konfiguracji i serwer VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może potrwać 15 minut lub dłużej. 
8. Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso określa docelowymi ustawieniami replikacji.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierają ustawienia wartości docelowej.
2. Usługa Site Recovery sprawdza, czy istnieje konto usługi Azure storage i sieci w lokalizacji docelowej określonej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Teraz firmy Contoso, można utworzyć zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawień replikacji** > **zasad replikacji** >  **Utwórz i Skojarz**, tworzą zasadę **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg celu punktu odzyskiwania**: domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Czas przechowywania punktu odzyskiwania**. Domyślnej wartości 24 godzin. Ta wartość określa, jak długo trwa okno przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, z jaką są tworzone migawki spójne z aplikacjami.

        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Włączanie replikacji na potrzeby WEBVM

Wszystko w miejscu Contoso można włączyć replikację dla maszyn wirtualnych. Wszystko zaczyna WebVM.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, że chce włączyć maszyn wirtualnych, wybierz serwer vCenter, a serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Wybierają ustawienia docelowego, łącznie z grupy zasobów i sieć platformy Azure i konto magazynu.

     ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Wybiera contoso **WebVM** replikacji, sprawdza, czy zasady replikacji i umożliwia replikację.

    - Na tym etapie Contoso tylko zaznacza WEBVM, ponieważ należy wybrać sieć wirtualną i podsieć, a firmy Contoso jest umieszczenie aplikacji maszyn wirtualnych w różnych podsieciach.
    - Po włączeniu replikacji usługa Site Recovery automatycznie instaluje usługę mobilności na maszynie Wirtualnej.

    ![Włączanie replikacji](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Śledzą postęp replikacji w **zadań**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.
6. W **Essentials** w witrynie Azure portal firmy Contoso można wyświetlić jego strukturę dla maszyn wirtualnych replikowanych do platformy Azure.


### <a name="enable-replication-for-sqlvm"></a>Włączanie replikacji na potrzeby SQLVM

Teraz Contoso rozpocząć replikację na maszynie SQLVM, przy użyciu tego samego procesu jak powyżej.

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


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migracji maszyn wirtualnych 

Contoso uruchamia szybkie testowanie trybu failover, a następnie pełna trybu failover, aby przeprowadzić migrację maszyn wirtualnych.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Testowanie trybu failover pomaga upewnić się, że wszystko działa zgodnie z oczekiwaniami. 

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowszy przetworzony**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu usługa Site Recovery próbuje zamknąć źródłową maszynę Wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Testy trybu failover: 

    - Sprawdzanie wymagań wstępnych, przebiegów, aby upewnić się, że wszystkie warunki wymagane do migracji w miejscu.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.
    
3. Po zakończeniu trybu failover w witrynie Azure portal pojawi się repliki maszyn wirtualnych platformy Azure. Contoso — sprawdza, czy maszyna wirtualna ma prawidłowy rozmiar, połączona z odpowiednią siecią i działa. 
4. Po zweryfikowaniu testowy tryb failover, ich czyszczenie pracy awaryjnej i zarejestrować i zapisać wszelkie obserwacje. 

### <a name="create-and-customize-a-recovery-plan"></a>Twórz i dostosowuj plan odzyskiwania

 Po sprawdzeniu, czy test trybu failover zadziałała zgodnie z oczekiwaniami, Contoso tworzy plan odzyskiwania do migracji. 

- Plan odzyskiwania określa kolejność w pracą w trybie failover następuje i wskazuje, jak maszyny wirtualne platformy Azure będą rozstrzygane online na platformie Azure.
- Ponieważ aplikacja dwuwarstwowa, ich dostosowywać plan odzyskiwania, dzięki czemu dane maszyny Wirtualnej (SQLVM) uruchomi się przed frontonu (WEBVM).

1. W **plany odzyskiwania (Usługa Site Recovery)** > **+ planu odzyskiwania**, Utwórz plan i Dodaj maszyny wirtualne do niego.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Po utworzeniu planu, ich dostosowywanie (**plany odzyskiwania** > **SmartHotelMigrationPlan** > **Dostosuj**).
2.  Usuwają WEBVM z **Grupa 1: Start**.  Dzięki temu pierwszej akcji uruchamiania tylko dotyczy SQLVM.
3.  W **+ grupa** > **Dodaj chronione elementy**, co zwiększa WEBVM grupa 2: Start.  Maszyny wirtualne muszą znajdować się w dwóch różnych grup.


### <a name="migrate-the-vms"></a>Migrowanie maszyn wirtualnych


Teraz Contoso uruchamiać pełne trybu failover, aby ukończyć migrację.

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

Po zakończeniu migracji SmartHotel warstwy aplikacji są teraz uruchomione na maszynach wirtualnych platformy Azure.

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

- Aby kontrolować dostęp, ich Przejrzyj sieciowych grup zabezpieczeń (NSG) dla maszyn wirtualnych. Sieciowe grupy zabezpieczeń są używane do zapewnienia, że tylko ruch do aplikacji można uzyskiwać dostęp go niego.
- One również wziąć pod uwagę zabezpieczania danych na dysku za pomocą usługi Azure Disk Encryption i magazynu kluczy.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych

Firma Contoso zamierza kopii zapasowej danych na maszynach wirtualnych za pomocą usługi Azure Backup. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

1. Firmy Contoso ma istniejącej licencji dla maszyn wirtualnych i będzie korzystać z korzyści użycia hybrydowego platformy Azure.  Przekonwertuje ich istniejących maszyn wirtualnych platformy Azure, aby móc korzystać z tej ceny.
2. Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze. [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso rehosted aplikacji SmartHotel na platformie Azure przy użyciu funkcji migracji aplikacji maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. 


## <a name="next-steps"></a>Kolejne kroki

W [następnego artykułu](contoso-migration-rehost-vm-sql-ag.md) serii, pokażemy ci jak Contoso rehostowaniu frontonu aplikacji SmartHotel maszyny Wirtualnej na Maszynie wirtualnej platformy Azure i wykonuje migrację bazy danych do grupy dostępności SQL Server AlwaysOn na platformie Azure.

