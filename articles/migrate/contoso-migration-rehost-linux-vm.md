---
title: Dokonaj migracji rehost i rehost aplikację Linux lokalnego na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehost lokalnych aplikacji systemu Linux przy użyciu funkcji migracji do maszyn wirtualnych platformy Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 8f039884ca0ea46c128078984d6cab6fd29ac9af
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220554"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migracja Contoso: Rehost aplikację Linux lokalnego na maszynach wirtualnych platformy Azure

W tym artykule opisano, jak firma Contoso jest rehosting technicznej aplikacji opartych na systemie Linux usługi lokalnej (**osTicket**), aby maszyny wirtualne IaaS platformy Azure.

Ten dokument jest siódmy w szeregu artykuły, które dokumentów, jak fikcyjnej firmy Contoso migruje jej zasobów lokalnych do chmury Microsoft Azure. Serii zawiera informacje i zestaw scenariusze przedstawiono sposób konfigurowania infrastruktury migracji, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększa się złożoność i które będą dodawane dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji frontonu maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi migrację do zarządzanego wystąpienie serwera SQL. | Dostępne
[Artykuł 5: Rehost na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migracji aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Site Recovery ich użyć do migracji aplikacji maszyny wirtualne i usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności programu SQL Server. | Dostępne
Artykuł 7: Rehost aplikacji systemu Linux na maszynach wirtualnych platformy Azure (w tym artykule) | Pokazuje, jak Contoso migruje osService aplikacji systemu Linux przy użyciu usługi Azure Site Recovery.
[Artykuł 8: Rehost aplikację systemu Linux, aby maszyny wirtualne platformy Azure i serwerem MySQL Azure](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso przeprowadzanie migracji aplikacji Linux osService, przy użyciu usługi Site Recovery dla maszyny Wirtualnej migracji i MySQL Workbench do migracji (na wystąpienie serwera Azure MySQL. | Dostępne

W tym artykule Contoso będą migrowane dwie warstwy **osTicket** aplikacja uruchomiona na systemie Linux Apache MySQL PHP (światła) na platformie Azure. Aplikacja maszyn wirtualnych zostanie zmigrowana przy użyciu usługi Azure Site Recovery. Jeśli chcesz korzystać z tej aplikacji open source, możesz pobrać go z [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Czynniki biznesowe

Kadry kierowniczej IT ściśle działał z ich partnerów biznesowych, aby zrozumieć mają osiągnięcie przy tej migracji:

- **Adres rozwoju firmy**: rośnie Contoso i w związku z tym istnieje nacisk na systemów lokalnych i infrastruktury.
- **Ogranicz ryzyko**: usługi technicznej aplikacji jest szczególnie ważne w firmie Contoso. Chcą przenieść ją do platformy Azure z zero ryzyka.
- **Rozszerzanie**: nie chcesz teraz zmienić aplikacji. Chcą po prostu upewnij się, że jest stabilna.


## <a name="migration-goals"></a>Celów migracji

Zespół chmury Contoso ma przypięty dół celów migracji, aby określić najlepszą metodę migracji:

- Po przeprowadzeniu migracji aplikacji na platformie Azure powinien mają te same możliwości wydajności, jak obecnie w środowisku VMWare lokalnymi.  Aplikacja będzie nadal jako krytyczne w chmurze, ponieważ jest on lokalnymi. 
- Contoso nie mają inwestycji w tej aplikacji.  Ważne jest, aby firmy, ale w jego bieżącym formularza po prostu chcą bezpiecznie przenieść go do chmury.
- Contoso nie chce zmienić model ops dla tej aplikacji. Chcą korzystać z niego w chmurze w taki sam sposób jak teraz.
- Contoso nie chce zmienić funkcjonalność aplikacji. Tylko lokalizacji aplikacji zostanie zmieniona.
- Po zakończeniu kilka migracji aplikacji systemu Windows, firma Contoso chce informacje o sposobie korzystania z infrastruktury opartej na systemie Linux na platformie Azure.

## <a name="proposed-architecture"></a>Architektura proponowanych

W tym scenariuszu:

- Aplikacja jest warstwowa w dwóch maszyn wirtualnych (OSTICKETWEB i OSTICKETMYSQL).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Zarządza w środowisku programu VMware vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalnego centrum danych (**centrum danych firmy contoso**), z lokalnym kontrolerem domeny (**contosodc1**).
- Ponieważ aplikacja jest obciążeń produkcyjnych, maszyn wirtualnych na platformie Azure będą znajdować się w grupie zasobów produkcji **ContosoRG**.
- Maszyn wirtualnych będą migrowane do regionu podstawowego (wschodnie stany USA 2) i umieszczony w sieci produkcyjnej (VNET-produkcyjną EUS2):
    - Maszyna wirtualna w sieci web będą znajdować się w podsieci frontonu (produkcyjną-FE-EUS2).
    - Maszyna wirtualna w bazie danych będą znajdować się w podsieci bazy danych (produkcyjną-DB-EUS2).
- Będzie można zlikwidować lokalnymi maszynami wirtualnymi w centrum danych firmy Contoso, po ukończeniu migracji.

![Architektura scenariusza](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>proces migracji

Firma Contoso będzie migracji w następujący sposób:

1. Pierwszym krokiem Contoso konfiguruje platformy Azure i lokalnej infrastruktury wymaganej do wdrożenia usługi Site Recovery.
2. Po przygotowaniu składniki platformy Azure i lokalnego, skonfigurować i włączyć replikację dla maszyn wirtualnych.
3. Po działa replikacja migrowania maszyn wirtualnych przez awaryjne na platformie Azure.

![proces migracji](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwania po awarii dla maszyn wirtualnych platformy Azure i lokalnymi maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure Azure Storage jest obciążany.  Maszyny wirtualne platformy Azure są tworzone i spowodować naliczenie opłat, podczas pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) dotyczące opłat i cenach.

 
## <a name="prerequisites"></a>Wymagania wstępne

Oto (i firmy Contoso) muszą dla tego scenariusza.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcji podczas wcześniejszego artykuły w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, należy do pracy z administratora przypisanie uprawnienia właściciela lub współautora.<br/><br/> Aby uzyskać bardziej szczegółowe uprawnienia, przejrzyj [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktury platformy Azure** | Konfigurowanie infrastruktury platformy Azure, zgodnie z opisem w firmie Contoso [infrastruktury platformy Azure dla migracji](contoso-migration-infrastructure.md).<br/><br/> Dowiedz się więcej na temat konkretnego [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Serwer vCenter lokalnej musi korzystać z wersji 5.5, 6.0 lub 6.5<br/><br/> Hosta ESXi z systemem w wersji 5.5, 6.0 lub 6.5<br/><br/> Jeden lub więcej maszyn wirtualnych VMware uruchomiona na hoście ESXi.
**Lokalne maszyny wirtualne** | [Przejrzyj maszyny z systemem Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) które są obsługiwane w przypadku migracji z usługą Site Recovery.<br/><br/> Sprawdź obsługiwany [systemów plików i magazynowania Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Oto, jak Azure ukończy migracji:

> [!div class="checklist"]
> * **Krok 1: Przygotowanie usługi Azure Site Recovery**: Tworzenie konta magazynu Azure do przechowywania replikowanych danych i Tworzenie magazynu usług odzyskiwania.
> * **Krok 2: Przygotowanie VMware lokalnymi Site Recovery**: przygotowanie konta używanego do maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi Azure po pracy awaryjnej.
> * **Krok 3: Replikacja maszyn wirtualnych**: Konfigurowanie środowiska migracji źródłowym i docelowym, Utwórz zasady replikacji i rozpoczęcia replikacji maszyn wirtualnych z magazynem platformy Azure.
> * **Krok 4: Migrowanie maszyn wirtualnych z usługą Site Recovery**: one testować tryb failover, aby się upewnić, że wszystko działa, a następnie uruchom pełne trybu failover do migracji maszyn wirtualnych na platformie Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowanie Azure do usługi Site Recovery

Contoso wymaga kilku składników usługi Azure Site Recovery:

- Sieci wirtualnej, w którym nie powiodło się za pośrednictwem zasobów (Contoso użyje produkcji już wdrożony w sieci wirtualnej)
- Nowe konto magazynu Azure do przechowywania replikowanych danych. 
- W magazynie usług odzyskiwania Azure.

Contoso utworzone sieci wirtualnej podczas [wdrażania infrastruktury platformy Azure](contoso-migration-infrastructure.md), więc muszą po prostu utworzyć konto magazynu i magazynu.

1. Contoso tworzy konto magazynu platformy Azure (contosovmsacc20180528) w regionie wschodnie stany USA 2.

    - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
    - Używają konta ogólnego przeznaczenia, z magazynu standardowego i replikacji LRS.

    ![Magazyn odzyskiwania lokacji](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Z sieci i magazynu konta w miejscu Contoso Tworzenie magazynu (ContosoMigrationVault) i umieść go w **ContosoFailoverRG** grupy zasobów w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Konfigurowanie usługi Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowanie VMware lokalnymi Site Recovery

Contoso przygotowuje lokalnej infrastruktury programu VMware w następujący sposób:

- Tworzenie konta na vCenter server lub vSphere ESXi hosta, aby zautomatyzować odnajdywania maszyny Wirtualnej.
- Tworzenie konta, które umożliwia automatyczne instalacji usługi mobilności na maszynach wirtualnych VMware, które mają być replikowane.
- Przygotuj lokalną maszyn wirtualnych, tak, aby umożliwić im połączenie z maszyn wirtualnych platformy Azure, gdy są tworzone po migracji.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebujesz konta, które można uruchomić operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso Ustawia konto:

1. Contoso tworzy rolę na poziomie vCenter.
2. Contoso następnie przypisuje wymaganych uprawnień danej roli.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Musi być zainstalowana usługa mobilności na maszynach wirtualnych systemu Linux, który Contoso do migracji:

- Site Recovery może zrobić instalacji wypychanej automatyczne tego składnika, po włączeniu replikacji dla maszyn wirtualnych.
- Dla instalacji wypychanej automatyczne należy przygotować konta, które uzyskują dostęp maszyny wirtualne odzyskiwania lokacji.
- Szczegóły konta są dane wejściowe podczas instalacji replikacji. 
- Konto może być domeny lub lokalnego konta z uprawnieniami do zainstalowania na maszynach wirtualnych.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po w tryb failover na platformie Azure firma Contoso chce mieć możliwość nawiązania replikowanych maszyn wirtualnych na platformie Azure. Aby to zrobić, istnieje kilka rzeczy, które są im potrzebne w celu:

- Aby uzyskać dostęp przez internet, umożliwiają one SSH w systemie Linux lokalnej maszyny Wirtualnej przed migracją.  Dla Ubuntu to można wykonać za pomocą następującego polecenia: **Sudo stanie get ssh zainstalować -y**.
- Po uruchomieniu migracji (failover), należy sprawdzić **diagnostyki rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej.
- Jeśli to nie pomoże, należy sprawdzić czy maszyna wirtualna jest uruchomiona i przejrzeć te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli automatycznego wykrywania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikacja maszyn wirtualnych lokalnie

Przed ich migracji maszyny Wirtualnej w sieci web na platformie Azure, Contoso konfiguruje i pozwala usłudze replikacji.

### <a name="set-a-protection-goal"></a>Ustaw cel ochrony

1. W magazynie, w obszarze Nazwa magazynu (ContosoVMVault) Sami ustawiają celem replikacji (**wprowadzenie** > **usługi Site Recovery** > **przygotowanie infrastruktury**.
2. Określają, czy ich maszyn znajdujących się lokalnie, są one maszyn wirtualnych VMware, a ich chcesz replikować do platformy Azure.
    ![Celem replikacji](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Upewnij się, planowanie wdrożenia

Aby kontynuować, ich potwierdzić, że ich zostało ukończone, planowanie wdrożenia, wybierając **tak, już to zostało zrobione**. Contoso jest tylko Migracja jednej maszyny Wirtualnej w tym scenariuszu i nie jest konieczne Planowanie wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Contoso musi skonfigurować ich środowiska źródłowego. Aby to zrobić, ich Pobierz szablon OVF i użyć go do wdrożenia serwera konfiguracji usługi Site Recovery jako o wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu serwera konfiguracji one Zarejestruj go w magazynie.

Serwer konfiguracji jest uruchamiana liczby składników:

- Składnik serwera konfiguracji, która koordynuje komunikacji między lokalną i platformą Azure i zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Contoso wykonaj następujące kroki w następujący sposób:

1. Będą oni mogli pobrać szablonu OVF **przygotowanie infrastruktury** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Importowanie szablonu VMware do utworzenia maszyny Wirtualnej i wdróż maszynę Wirtualną.

    ![Szablon OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Podczas włączania maszyny Wirtualnej po raz pierwszy, on uruchamiany w procesie instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzia konfiguracji odzyskiwania lokacji Azure domyślnie uruchamia.
5. W narzędziu określają Nazwa do użycia na potrzeby rejestracji w magazynie serwera konfiguracji.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia zalogowaniu do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.

    ![Zarejestruj serwer konfiguracji](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. One ponownie zalogować się w komputerze i Kreatora konfiguracji serwera zarządzania jest uruchamiana automatycznie.
9. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić tego ustawienia, po przeprowadzeniu konfiguracji.
10. Wybierają subskrypcji, grupy zasobów i magazynu, w którym można zarejestrować serwer konfiguracji.

    ![Magazyn](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Ich, a następnie Pobierz i zainstaluj serwer MySQL i VMWare PowerCLI. 
12. Po sprawdzeniu poprawności określają adres FQDN lub adres IP hosta serwera lub vSphere vCenter. Pozostaw domyślny port i określić przyjazną nazwę dla serwera vCenter.
13. Określa konto, utworzony automatycznego wykrywania i poświadczenia, które mają być używane do automatycznego zainstalowania usługi mobilności.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Po zakończeniu rejestracji w portalu Azure Contoso sprawdza, czy serwer konfiguracji i serwera VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może zająć 15 minut lub dłużej. 
15. Odzyskiwanie lokacji łączy się z serwerami VMware i umożliwia odnajdywanie maszyn wirtualnych.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz Contoso umożliwia skonfigurowanie ustawień obiektu docelowego replikacji.

1. W **przygotowanie infrastruktury** > **docelowego**, wybierają ustawienia obiektu docelowego.
2. Usługa Site Recovery sprawdza, czy znajduje się konto magazynu Azure i sieci w określonej lokalizacji docelowej.

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Po źródłowy i docelowy są skonfigurowane, firma Contoso jest gotowy do utworzenia zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawienia replikacji** > **zasad replikacji** >  **tworzenie i Skojarz**, tworzenia zasad **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg RPO**: domyślny 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Przechowywania punktu odzyskiwania**. Domyślna wartość 24 godzin. Ta wartość określa, jak długo trwa okna przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, na którym są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Potrzebujesz dodatkowej pomocy?**

- Pełny przewodnik wszystkie te kroki można znaleźć [ustawienia odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrożenie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) dotyczące agenta gościa Azure dla systemu Linux.

**Potrzebujesz dodatkowej pomocy?**

- Pełny przewodnik wszystkie te kroki można znaleźć [ustawienia odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrożenie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) dotyczące agenta gościa Azure dla systemu Linux.



### <a name="enable-replication-for-osticketweb"></a>Włącz replikację OSTICKETWEB

Teraz Contoso można uruchomić replikacji **OSTICKETWEB** maszyny Wirtualnej.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wybierz one, czy chcą włączyć maszyn wirtualnych, wybierz ustawienia źródła, w tym serwera vCenter i serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Określa ustawień obiektu docelowego, w tym grupa zasobów sieci wirtualnej, w którym maszyny Wirtualnej Azure zostaną umieszczone po pracy awaryjnej i konto magazynu, w którym będą przechowywane replikowanych danych.

     ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Wybiera contoso **OSTICKETWEB** replikacji. 

    - Na tym etapie Contoso wybiera tylko **OSTICKETWEB** ponieważ należy wybrać sieć wirtualną i podsieć, a maszyny wirtualne nie są w tej samej podsieci.
    - Usługa Site Recovery automatycznie instaluje usługi mobilności, po włączeniu replikacji dla maszyny Wirtualnej.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. We właściwościach maszyny Wirtualnej firmy Contoso wybiera konta, na którym jest używany przez serwer przetwarzania, aby automatycznie zainstalować usługi mobilności na maszynie.

     ![Usługa mobilności](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. w **ustawienia replikacji** > **Konfigurowanie ustawień replikacji**, ich Sprawdź, czy zasady replikacji poprawne jest stosowanych i wybierz **włączania replikacji**.
6.  One śledzić postęp replikacji w **zadania**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.



### <a name="enable-replication-for-osticketmysql"></a>Włącz replikację OSTICKETMYSQL

Teraz Contoso można uruchomić replikacji **OSTICKETMYSQL**.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródłowe i docelowe.
2. Wybiera contoso **OSTICKETMYSQL** replikacji i wybiera konto do użycia podczas instalacji usługi mobilności.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Firma Contoso ma zastosowanie same zasady replikacji, które było używane dla OSTICKETWEB i pozwala usłudze replikacji.  

**Potrzebujesz dodatkowej pomocy?**

Pełny przewodnik wszystkie te kroki można znaleźć [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrowanie maszyn wirtualnych 

Contoso szybkiego uruchamiania testowanie trybu failover, a następnie przeprowadzić migrację maszyn wirtualnych.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Uruchomiony test trybu failover pomaga upewnij się, że wszystko działa zgodnie z oczekiwaniami przed migracją. 

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowsze przetworzone**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu próbuje zamknąć źródłowej maszyny Wirtualnej, aby mogło nastąpić wyzwolenie pracy awaryjnej usługi Site Recovery. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Tryb failover uruchomień testów: 
    - Uruchamia się upewnić, że wszystkie warunki wymagane do migracji w miejscu Sprawdzanie wymagań wstępnych.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli jest wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania jest tworzony z danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.
3. Po zakończeniu pracy awaryjnej, replika maszyny Wirtualnej Azure pojawia się w portalu Azure. Sprawdzają, czy maszyna wirtualna jest odpowiedni rozmiar, który został połączony z siecią prawo, i że jest uruchomiona. 
4. Po zweryfikowaniu, ich wyczyścić trybu failover i zarejestrować i zapisać wszelkie obserwacje.

### <a name="create-and-customize-a-recovery-plan"></a>Tworzenie i Dostosowywanie planu odzyskiwania

 Po zweryfikowaniu, że test trybu failover działał zgodnie z oczekiwaniami, Contoso Tworzenie planu odzyskiwania do migracji. 

- Plan odzyskiwania określa kolejność, w których pracy awaryjnej, jak maszyny wirtualne Azure będą włączane na platformie Azure.
- Ponieważ chcą przeprowadzić migrację aplikacji dwuwarstwowej, ich dostosuje planu odzyskiwania, aby dane maszyny Wirtualnej (SQLVM) rozpoczyna się przed frontonu (WEBVM).


1. W **plany odzyskiwania (lokacji odzyskiwania)** > **+ planu odzyskiwania**, tworzenie planu i Dodaj do niej maszyny wirtualne.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Po utworzeniu planu, wybierają go do dostosowania (**plany odzyskiwania** > **OsTicketMigrationPlan** > **Dostosuj**.
3.  Będą oni mogli usunąć **OSTICKETWEB** z **Grupa 1: Start**.  Dzięki temu, że wpływa na pierwszą akcją start **OSTICKETMYSQL** tylko.

    ![Grupa odzyskiwania](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  W **+ grupy** > **Dodaj chronione elementy**, co zwiększa **OSTICKETWEB** do **grupa 2: Start**.  Contoso musi je w dwóch różnych grup.

    ![Grupa odzyskiwania](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrowanie maszyn wirtualnych


Firma Contoso jest gotowe do uruchamiania trybu failover dla planu odzyskiwania, aby przeprowadzić migrację maszyn wirtualnych.

1. One wybierz plan > **pracy awaryjnej**.
2.  Wybierz one, aby przełączyć się do ostatniego punktu odzyskiwania, a następnie określ, czy usługa Site Recovery należy dążyć do zamykania maszyny Wirtualnej lokalnego, aby mogło nastąpić wyzwolenie pracy awaryjnej. Postęp trybu failover może wykonać **zadania** strony.

    ![Tryb failover](./media/contoso-migration-rehost-vm/failover1.png)

3. Podczas pracy awaryjnej vCenter Server wydaje polecenia zatrzymania dwóch maszyn wirtualnych uruchomionych na hoście ESXi.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Po pracy awaryjnej Contoso Sprawdź, czy jest wyświetlany maszyny Wirtualnej Azure, zgodnie z oczekiwaniami w portalu Azure.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Po zweryfikowaniu maszyny Wirtualnej na platformie Azure, ich zakończeniu migracji, w celu zakończenia procesu migracji dla każdej maszyny Wirtualnej. Zatrzymuje replikacji dla maszyny Wirtualnej i zatrzymuje rozliczeń usługi Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Połącz maszynę Wirtualną do bazy danych

Ostatni krok w procesie migracji Contoso zaktualizować parametry połączenia aplikacji do punktu w bazie danych aplikacji uruchomionych na **OSTICKETMYSQL** maszyny Wirtualnej. 

1. Finalizowania połączenie SSH **OSTICKETWEB** maszyny Wirtualnej przy użyciu programu Putty lub innego klienta SSH. Maszyna wirtualna jest prywatny, więc łączą się przy użyciu prywatnego adresu IP.

    ![łączenia z bazą danych](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![łączenia z bazą danych](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Należy upewnić się, że **OSTICKETWEB** maszyna wirtualna może się komunikować z **OSTICKETMYSQL** maszyny Wirtualnej. Konfiguracja jest obecnie zapisane na stałe z adresem IP lokalnymi 172.16.0.43.

    **Przed aktualizacją**
    
    ![Aktualizowanie adresu IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Po aktualizacji**
    
    ![Aktualizowanie adresu IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Ich ponownego uruchomienia usługi z **systemctl Uruchom ponownie apache2**.

    ![Ponowne uruchamianie](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Na koniec zaktualizowanie rekordy DNS dla **OSTICKETWEB** i **OSTICKETMYSQL**, na jednym z kontrolerów domeny Contoso.

    ![Aktualizowanie systemu DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Aktualizowanie systemu DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) uruchomiony test trybu failover. 
- [Dowiedz się](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenie w tryb failover na platformie Azure.

## <a name="clean-up-after-migration"></a>Czyszczenie po migracji

Po zakończeniu migracji osTicket warstwy aplikacji są obecnie uruchomione na maszynach wirtualnych Azure.

Teraz Contoso musi wykonać niektóre Oczyszczanie:  

- Będą oni mogli usunąć lokalnych maszyn wirtualnych ze spisu vCenter.
- Będą oni mogli usunąć lokalnych maszyn wirtualnych z lokalnym zadania tworzenia kopii zapasowej.
- Zaktualizowanie ich wewnętrzny dokumentacji, aby pokazać do nowej lokalizacji, a adresy IP dla OSTICKETWEB i OSTICKETMYSQL.
- Przejrzyj wszystkie zasoby, które współdziałają z maszyn wirtualnych i zaktualizować wszystkie odpowiednie ustawienia lub dokumentacji odzwierciedlą nowej konfiguracji.
- Contoso użycia usługi Azure migracji z mapowaniem zależności w celu oceny maszyn wirtualnych do migracji. One należy usunąć agenta monitorowania firmy Microsoft i agenta zależności są zainstalowane w tym celu z maszyny Wirtualnej.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona Contoso musi pełni operacjonalizacji i zabezpieczyć ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

Zespół ds Contoso przeglądanie OSTICKETWEB OSTICKETMYSQLVMs ustalenie wystąpiły problemy z zabezpieczeniami.

- Przeglądu grup zabezpieczeń sieci (NSG) dla maszyn wirtualnych w celu kontroli dostępu. Grupy NSG są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Rozważanych również Zabezpieczanie danych na dyskach maszyny Wirtualnej przy użyciu szyfrowania dysku i Azure KeyVault.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych

Contoso kopii zapasowej danych na maszynach wirtualnych przy użyciu usługi Kopia zapasowa Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Optymalizacja kosztów i licencjonowania

- Po wdrożeniu zasobów, Contoso przypisuje tagi Azure, zgodnie z definicją w [wdrażania infrastruktury platformy Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Firma Contoso ma licencjonowania problemów ze swoimi serwerami Ubuntu.
- Contoso spowoduje włączenie zarządzania koszt Azure licencjonowane przez Cloudyn, oddziału firmy Microsoft. To rozwiązanie do zarządzania koszt usługi chmury, które pomaga wykorzystywać oraz zarządzania platformy Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o Azure kosztów zarządzania. 


## <a name="next-steps"></a>Kolejne kroki

W tym artykule, które firma Microsoft pokazano, jak migracji Contoso lokalnej usługi technicznej aplikacji warstwowej na dwóch maszyn wirtualnych systemu Linux na maszynach wirtualnych IaaS platformy Azure, za pomocą usługi Azure Site Recovery.

W artykule w serii pokażemy ci jak Contoso migrację z tej samej aplikacji technicznej usługi Azure. Teraz firma Contoso używa usługi Site Recovery do migracji maszyny Wirtualnej serwera sieci Web dla aplikacji, a także migrację bazy danych aplikacji przy użyciu kopii zapasowej i przywrócenie bazy danych platformy Azure dla programu MySQL, za pomocą narzędzia workbench MySQL. [Rozpoczynanie pracy](contoso-migration-rehost-linux-vm-mysql.md).
