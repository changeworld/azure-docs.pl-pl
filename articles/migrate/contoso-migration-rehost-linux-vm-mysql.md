---
title: Rehost Contoso Linux usługi technicznej aplikacji na platformie Azure oraz Azure MySQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu lokalnych aplikacji systemu Linux przy użyciu funkcji migracji na maszynach wirtualnych platformy Azure i MySQL na platformie Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 4367bf7cb02bb6a1e343dc3fb171be731e15c32b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300715"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migracja Contoso: Rehost aplikację Linux lokalnego na maszynach wirtualnych platformy Azure i MySQL na platformie Azure

W tym artykule opisano, jak Contoso są rehosting swoich lokalnych dwuwarstwowa Linux usługi technicznej aplikacji (osTicket), przy użyciu funkcji migracji do platformy Azure i MySQL na platformie Azure.

Ta okazana jest ósmego w szeregu artykuły, które pokazują, jak fikcyjnej firmy Contoso migruje jej zasobów lokalnych do chmury Microsoft Azure. Seria zawiera informacje i scenariuszy, w których pokazano, jak skonfigurować infrastrukturę do migracji, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększa się złożoność i które będą dodawane dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji sieci web maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi migrację do zarządzanego wystąpienie serwera SQL. | Dostępne
[Artykuł 5: Rehost na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migracji ich SmartHotel do maszyn wirtualnych IaaS platformy Azure, przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Site Recovery ich użyć do migracji aplikacji maszyny wirtualne i usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Rehost aplikacji systemu Linux, aby maszyny wirtualne platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso migruje aplikacji Linux osTicket do maszyn wirtualnych IaaS platformy Azure przy użyciu usługi Azure Site Recovery.
Artykuł 8: Rehost aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer MySQL Azure (w tym artykule) | Pokazuje, jak Contoso migruje osTicket aplikacji systemu Linux. Używają migrację do wystąpienia serwera MySQL Azure Site Recovery dla maszyny Wirtualnej migracji i narzędzia MySQL Workbench. | Dostępne

W tym artykule Contoso migruje dwuwarstwowa Linux Apache MySQL PHP (światła) usługi technicznej aplikacji (osTicket) na platformie Azure. Jeśli chcesz korzystać z tej aplikacji open source, możesz pobrać go z [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Czynniki biznesowe

Kadry kierowniczej IT ściśle działał z ich partnerów biznesowych, aby zrozumieć, jakie chcą, aby osiągnąć:

- **Adres rozwoju firmy**: rośnie Contoso i w związku z tym istnieje nacisk na systemów lokalnych i infrastruktury.
- **Ogranicz ryzyko**: usługi technicznej aplikacji jest szczególnie ważne w firmie Contoso. Chcą przenieść ją do platformy Azure z zero ryzyka.
- **Rozszerzanie**: Contoso nie chcesz teraz zmienić aplikacji. Po prostu one powinny być stabilna.


## <a name="migration-goals"></a>Celów migracji

Zespołu chmury Contoso ma przypięty dół celów migracji, aby było możliwe określenie najlepszej metody migracji:

- Po przeprowadzeniu migracji aplikacji na platformie Azure powinien mają te same możliwości wydajności, jak obecnie w środowisku VMWare lokalnymi.  Aplikacja będzie nadal jako krytyczne w chmurze, ponieważ jest on lokalnymi. 
- Contoso nie mają inwestycji w tej aplikacji.  Ważne jest, aby firmy, ale w jego bieżącym formularza po prostu chcą bezpiecznie przenieść go do chmury.
- Po zakończeniu kilka migracji aplikacji systemu Windows, firma Contoso chce informacje o sposobie korzystania z infrastruktury opartej na systemie Linux na platformie Azure.
- Firma Contoso chce zminimalizować zadania administratora bazy danych, gdy aplikacja zostanie przeniesiona do chmury.

## <a name="proposed-architecture"></a>Architektura proponowanych

W tym scenariuszu:

- Aplikacja jest warstwowa w dwóch maszyn wirtualnych (OSTICKETWEB i OSTICKETMYSQL).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5).
- Zarządza w środowisku programu VMware vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalnego datacenter (contoso-centrum danych), z lokalnym kontrolerem domeny (**contosodc1**).
- Warstwy aplikacji sieci web na OSTICKETWEB zostaną zmigrowane do maszyny Wirtualnej platformy Azure IaaS.
- Bazy danych aplikacji zostaną zmigrowane do bazy danych Azure dla usługi MySQL PaaS.
- Ponieważ są one migracji obciążeń produkcyjnych, zasoby będą znajdować się w grupie zasobów produkcji **ContosoRG**.
- Zasoby będą replikowane do regionu podstawowego (wschodnie stany USA 2) i umieszczony w sieci produkcyjnej (VNET-produkcyjną EUS2):
    - Maszyna wirtualna w sieci web będą znajdować się w podsieci frontonu (produkcyjną-FE-EUS2).
    - Wystąpienie bazy danych będą znajdować się w podsieci bazy danych (produkcyjną-DB-EUS2).
- Bazy danych aplikacji zostaną zmigrowane do MySQL Azure za pomocą narzędzi MySQL.
- Będzie można zlikwidować lokalnymi maszynami wirtualnymi w centrum danych firmy Contoso, po ukończeniu migracji.


![Architektura scenariusza](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>proces migracji

Contoso zakończy proces migracji w następujący sposób:

Aby przeprowadzić migrację maszyny Wirtualnej w sieci web:

1. Pierwszym krokiem Contoso konfiguruje platformy Azure i lokalnej infrastruktury wymaganej do wdrożenia usługi Site Recovery.
2. Po przygotowaniu składniki platformy Azure i lokalnego, skonfigurować i włączyć replikację dla maszyny Wirtualnej w sieci web.
3. Po replikacji jest w górę i uruchomiona, migrowania maszyny Wirtualnej przez awaryjne na platformie Azure.

Aby przeprowadzić migrację bazy danych:

1. Contoso inicjuje wystąpienia programu MySQL na platformie Azure.
2. Konfigurowanie MySQL workbench i utworzyć kopię zapasową bazy danych lokalnie.
3. One następnie przywrócić bazę danych z lokalna kopia zapasowa Azure.

![proces migracji](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwania po awarii dla maszyn wirtualnych platformy Azure i lokalnymi maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure Azure Storage jest obciążany.  Maszyny wirtualne platformy Azure są tworzone i spowodować naliczenie opłat, podczas pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) dotyczące opłat i cenach.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Bazy danych jest oparty na aparacie serwer MySQL open source. Zapewnia społeczności w pełni zarządzaną, gotowe enterprise baza danych MySQL, jako usługa dla aplikacji opracowywania i wdrażania. 

 
## <a name="prerequisites"></a>Wymagania wstępne

Jeśli użytkownik (i firmy Contoso) chcesz uruchomić ten scenariusz, w tym miejscu jest powinny mieć.

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Należy utworzono już subskrypcji podczas wcześniejszego artykuły w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, należy do pracy z administratora przypisanie uprawnienia właściciela lub współautora.<br/><br/> Aby uzyskać bardziej szczegółowe uprawnienia, przejrzyj [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastruktury platformy Azure** | Konfigurowanie infrastruktury platformy Azure, zgodnie z opisem w firmie Contoso [infrastruktury platformy Azure dla migracji](contoso-migration-infrastructure.md).<br/><br/> Dowiedz się więcej na temat konkretnego [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) wymagania dotyczące usługi Site Recovery.
**Serwery lokalne** | Serwer vCenter lokalnej musi korzystać z wersji 5.5, 6.0 lub 6.5<br/><br/> Hosta ESXi z systemem w wersji 5.5, 6.0 lub 6.5<br/><br/> Jeden lub więcej maszyn wirtualnych VMware uruchomiona na hoście ESXi.
**Lokalne maszyny wirtualne** | [Przegląd wymagań maszyny Wirtualnej systemu Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) które są obsługiwane w przypadku migracji z usługą Site Recovery.<br/><br/> Sprawdź obsługiwany [systemów plików i magazynowania Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroki w scenariuszu

Oto, jak Azure ukończy migracji:

> [!div class="checklist"]
> * **Krok 1: Przygotowanie usługi Azure Site Recovery**: Tworzenie konta magazynu Azure do przechowywania replikowanych danych i Tworzenie magazynu usług odzyskiwania.
> * **Krok 2: Przygotowanie VMware lokalnymi Site Recovery**: przygotowanie kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi Azure po pracy awaryjnej.
 * **Krok 3: udostępniania bazy danych]**: na platformie Azure, należy udostępnić wystąpienie bazy danych MySQL na platformie Azure.
> * **Krok 4: Replikowania maszyn wirtualnych**: Konfigurowanie środowiska źródłowego i docelowego odzyskiwania lokacji, skonfiguruj zasady replikacji i rozpoczęcia replikacji maszyn wirtualnych z magazynem platformy Azure.
> * **Krok 5: Migrowanie bazy danych**: Konfigurowanie migracji za pomocą narzędzia MySQL.
> * **Krok 6: Migrowanie maszyn wirtualnych z usługą Site Recovery**: Uruchom test trybu failover, aby się upewnić, że wszystko działa, a następnie uruchom pełne trybu failover do migracji maszyn wirtualnych na platformie Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Przygotowanie Azure do usługi Site Recovery

Contoso wymaga kilku składników usługi Azure Site Recovery:

- Sieci wirtualnej, w którym nie powiodło się za pośrednictwem zasobów (Contoso użyje produkcji już wdrożony w sieci wirtualnej).
- Nowe konto magazynu Azure do przechowywania replikowanych danych. 
- W magazynie usług odzyskiwania Azure.

Contoso utworzone sieci wirtualnej podczas [wdrażania infrastruktury platformy Azure](contoso-migration-infrastructure.md), więc muszą po prostu utworzyć konto magazynu i magazynu.


1. Contoso tworzy konto magazynu platformy Azure (**contosovmsacc20180528**) w regionie wschodnie stany USA 2.

    - Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.
    - Firma Contoso używa konta ogólnego przeznaczenia, z magazynu standardowego i replikacji LRS.

    ![Magazyn odzyskiwania lokacji](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Przy użyciu sieci i magazynu konta w miejscu, tworzy magazynu (ContosoMigrationVault) firmy Contoso i umieszcza je w **ContosoFailoverRG** grupy zasobów w regionie wschodnie stany USA 2 podstawowym.

    ![Magazyn usługi Recovery Services](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Potrzebujesz dodatkowej pomocy?**

[Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) Konfigurowanie usługi Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Przygotowanie VMware lokalnymi Site Recovery

Contoso przygotowuje lokalnej infrastruktury programu VMware w następujący sposób:

- Tworzy konto vCenter Server, aby zautomatyzować odnajdywania maszyny Wirtualnej.
- Tworzy konto, który umożliwia automatycznej instalacji usługi mobilności na maszynach wirtualnych VMware, które mają być replikowane.
- Przygotowuje lokalnych maszyn wirtualnych, tak aby umożliwić im połączenie z maszyn wirtualnych platformy Azure, gdy są tworzone po migracji.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebujesz konta, które można uruchomić operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Contoso Ustawia konto:

1. Contoso tworzy rolę na poziomie vCenter.
2. Contoso następnie przypisuje wymaganych uprawnień danej roli.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Musi być zainstalowana usługa mobilności na każdej maszynie Wirtualnej, który firma Contoso chce migracji.

- Site Recovery może zrobić instalacji wypychanej automatyczne tego składnika, po włączeniu replikacji dla maszyn wirtualnych.
- Automatyczną instalację. Usługa Site Recovery wymaga konta z uprawnieniami do maszyny Wirtualnej. 
- Szczegóły konta są dane wejściowe podczas instalacji replikacji. 
- Konto może być domeny lub konta lokalnego, jak długo ma uprawnienia do instalacji.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po w tryb failover na platformie Azure firma Contoso chce mieć możliwość nawiązania połączenia z maszynami wirtualnymi Azure. Aby to zrobić, muszą wykonać kilka czynności: 

- Aby uzyskać dostęp przez internet, umożliwiają one SSH w systemie Linux lokalnej maszyny Wirtualnej przed migracją.  Dla Ubuntu to można wykonać za pomocą następującego polecenia: **Sudo stanie get ssh zainstalować -y**.
- Po przejściu w tryb failover, należy sprawdzić **diagnostyki rozruchu** Aby wyświetlić zrzut ekranu maszyny wirtualnej.
- Jeśli to nie pomoże, należy sprawdzić, czy maszyna wirtualna jest uruchomiona i przejrzeć te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) tworzenie i przypisywanie roli automatycznego wykrywania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) tworzenia konta dla instalacji wypychanej usługi mobilności.


## <a name="step-3-provision-azure-database-for-mysql"></a>Krok 3: Udostępniania bazy danych platformy Azure dla programu MySQL

Contoso przepisy MySQL bazy danych wystąpienia w regionie wschodnie stany USA 2 podstawowym.

1. W portalu Azure tworzenia bazy danych Azure dla zasobu MySQL. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Dodaj ich nazwę **contosoosticket** dla bazy danych Azure. Ich dodać bazy danych do grupy zasobów w środowisku produkcyjnym **ContosoRG**i określić poświadczenia dla niego.
3. Lokalną bazą danych MySQL jest w wersji 5.7, więc ich wybrana ta wersja zgodności. Używają rozmiary domyślne, które odpowiadają ich wymagania dotyczące bazy danych.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Dla **opcje kopii zapasowej nadmiarowość**, Contoso wybiera do użycia **geograficznie nadmiarowego**. Ta opcja umożliwia je, aby przywrócić bazę danych w ich region pomocniczy środkowe stany USA, w przypadku wystąpienia awarii. Ich można skonfigurować tylko ta opcja udostępnia bazy danych.

     ![Nadmiarowość](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. W **EUS2-VNET-produkcyjną** sieci > **punkty końcowe usługi**, co zwiększa punktu końcowego (podsieci bazy danych) dla usługi SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Po dodaniu do podsieci, tworzenia sieci wirtualnej regułę, która zezwala na dostęp z podsieci bazy danych w sieci produkcyjnej.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Krok 4: Replikowanie lokalnych maszyn wirtualnych

Przed ich migracji maszyny Wirtualnej w sieci web na platformie Azure, Contoso konfiguruje i pozwala usłudze replikacji.

### <a name="set-a-protection-goal"></a>Ustaw cel ochrony

1. W magazynie, w obszarze Nazwa magazynu (ContosoVMVault) Sami ustawiają celem replikacji (**wprowadzenie** > **usługi Site Recovery** > **przygotowanie infrastruktury**.
2. Określają, czy ich maszyn znajdujących się lokalnie, są one maszyn wirtualnych VMware, a ich chcesz replikować do platformy Azure.

    ![Cel replikacji](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Upewnij się, planowanie wdrożenia

Aby kontynuować, ich potwierdzić, że ich zostało ukończone, planowanie wdrożenia, wybierając **tak, już to zostało zrobione**. Contoso jest tylko Migracja jednej maszyny Wirtualnej w tym scenariuszu i nie jest konieczne Planowanie wdrożenia.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Contoso musi skonfigurować ich środowiska źródłowego. Aby to zrobić, za pomocą szablonu pakietu OVF ich wdrożenie serwera konfiguracji usługi Site Recovery jako o wysokiej dostępności, lokalnej maszyny Wirtualnej VMware. Po skonfigurowaniu i uruchomieniu serwera konfiguracji one Zarejestruj go w magazynie.

Serwer konfiguracji jest uruchamiana liczby składników:

- Składnik serwera konfiguracji, która koordynuje komunikacji między lokalną i platformą Azure i zarządza replikacji danych.
- Serwer przetwarzania, który działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.

Contoso wykonaj następujące kroki w następujący sposób:


1. Będą oni mogli pobrać szablonu OVF **przygotowanie infrastruktury** > **źródła** > **serwera konfiguracji**.
    
    ![Pobieranie pakietu OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importowanie szablonu VMware do utworzenia maszyny Wirtualnej i wdróż maszynę Wirtualną.

    ![Szablon OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Podczas włączania maszyny Wirtualnej po raz pierwszy, on uruchamiany w procesie instalacji systemu Windows Server 2016. Zaakceptuj Umowę licencyjną i wprowadź hasło administratora.
4. Po zakończeniu instalacji, ich Zaloguj się do maszyny Wirtualnej jako administrator. Przy pierwszym logowaniu narzędzia konfiguracji odzyskiwania lokacji Azure domyślnie uruchamia.
5. W narzędziu określają Nazwa do użycia na potrzeby rejestracji w magazynie serwera konfiguracji.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure.
7. Po nawiązaniu połączenia zalogowaniu do subskrypcji platformy Azure. Poświadczenia musi mieć dostęp do magazynu, w którym będzie Zarejestruj serwer konfiguracji.

    ![Zarejestruj serwer konfiguracji](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
9. One ponownie zalogować się w komputerze i Kreatora konfiguracji serwera zarządzania jest uruchamiana automatycznie.
10. W Kreatorze one wybierz kartę Sieciową, aby odbierać ruch związany z replikacją. Nie można zmienić tego ustawienia, po przeprowadzeniu konfiguracji.
11. Wybierają subskrypcji, grupy zasobów i magazynu, w którym można zarejestrować serwer konfiguracji.

    ![Magazyn](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Teraz Pobierz i zainstaluj serwer MySQL i VMWare PowerCLI. 
13. Po sprawdzeniu poprawności określają adres FQDN lub adres IP hosta serwera lub vSphere vCenter. Pozostaw domyślny port i określić przyjazną nazwę dla serwera vCenter.
14. Wprowadź one konta, utworzony automatycznego wykrywania i poświadczeń używanych Site Recovery automatycznie zainstalować usługi mobilności. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Po zakończeniu rejestracji w portalu Azure Contoso sprawdza, czy serwer konfiguracji i serwera VMware są wyświetlane na **źródła** strony w magazynie. Odnajdywanie może zająć 15 minut lub dłużej. 
15. Wszystko w miejscu odzyskiwania lokacji łączy się z serwerami VMware i umożliwia odnajdywanie maszyn wirtualnych.

### <a name="set-up-the-target"></a>Konfigurowanie obiektu docelowego

Teraz wejść Contoso docelowych ustawień replikacji.

1. W **przygotowanie infrastruktury** > **docelowego**, wybierają ustawienia obiektu docelowego.
2. Usługa Site Recovery sprawdza, czy znajduje się konto magazynu Azure i sieci w określonej lokalizacji docelowej.


### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

Z źródłowych i docelowych, konfigurowanie firma Contoso jest gotowy do utworzenia zasad replikacji.

1. W **przygotowanie infrastruktury** > **ustawienia replikacji** > **zasad replikacji** >  **tworzenie i Skojarz**, tworzenia zasad **ContosoMigrationPolicy**.
2. Używają domyślnych ustawień:
    - **Próg RPO**: domyślny 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
    - **Przechowywania punktu odzyskiwania**. Domyślna wartość 24 godzin. Ta wartość określa, jak długo trwa okna przechowywania dla każdego punktu odzyskiwania. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
    - **Częstotliwość migawek spójności aplikacji**. Domyślnie co godzinę. Ta wartość określa częstotliwość, na którym są tworzone migawki spójne z aplikacjami.
 
        ![Tworzenie zasad replikacji](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Potrzebujesz dodatkowej pomocy?**

- Pełny przewodnik wszystkie te kroki można znaleźć [ustawienia odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Szczegółowe instrukcje są dostępne ułatwić [Konfigurowanie środowiska źródłowego](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [wdrożenie serwera konfiguracji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), i [Konfigurowanie ustawień replikacji](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) dotyczące agenta gościa Azure dla systemu Linux.

### <a name="enable-replication-for-the-web-vm"></a>Włącz replikację dla maszyny Wirtualnej w sieci Web

Teraz Contoso można uruchomić replikacji **OSTICKETWEB** maszyny Wirtualnej.

1. W **Replikowanie aplikacji** > **źródła** > **+ Replikuj** wybierają ustawienia źródła.
2. Wskazują, że chcą włączyć maszyny wirtualnej, a następnie wybierz ustawienia źródła, w tym serwera vCenter i serwer konfiguracji.

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Teraz określić ustawienia obiektu docelowego. Należą do grupy zasobów i sieci, w którym maszyny Wirtualnej Azure zostaną umieszczone po trybu failover oraz konto magazynu, w którym będą przechowywane replikowanych danych. 

     ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Wybiera contoso **OSTICKETWEB** replikacji. 

    ![Włączanie replikacji](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. We właściwościach maszyny Wirtualnej wybierają konta, które mają być używane do automatycznego zainstalowania usługi mobilności na maszynie Wirtualnej.

     ![Usługa mobilności](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. w **ustawienia replikacji** > **Konfigurowanie ustawień replikacji**, ich Sprawdź, czy zasady replikacji poprawne jest stosowanych i wybierz **włączania replikacji**. Usługa mobilności zostanie automatycznie zainstalowana.
6.  One śledzić postęp replikacji w **zadania**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.


**Potrzebujesz dodatkowej pomocy?**

Pełny przewodnik wszystkie te kroki można znaleźć [włączyć replikację](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Krok 5: Migrowanie bazy danych

Contoso będzie migrację bazy danych przy użyciu kopii zapasowej i przywracania, narzędziami MySQL. One Zainstaluj MySQL Workbench, wykonaj kopię zapasową bazy danych z OSTICKETMYSQL, a następnie przywróć bazę danych Azure, dla serwera MySQL.

### <a name="install-mysql-workbench"></a>Instalacja aplikacji MySQL Workbench

1. Sprawdzanie contoso [wymagania wstępne i pliki do pobrania MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Instalacji MySQL Workbench dla systemu Windows przy użyciu [instrukcje dotyczące instalacji](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. W MySQL Workbench tworzenia połączenia MySQL OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. One wyeksportować bazy danych jako **osticket**, do lokalnego pliku niezależne.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Po bazy danych ma lokalnie kopii zapasowej, tworzenia połączenia z bazą danych Azure dla wystąpienia MySQL.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Teraz Contoso można zaimportować plik (przywracanie) bazy danych w wystąpieniu Azure MySQL z autonomiczną. Nowego schematu (osticket) jest tworzony dla tego wystąpienia.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Krok 6: Migrowanie maszyn wirtualnych z usługą Site Recovery

Contoso szybkiego uruchamiania testowanie trybu failover, a następnie przeprowadzić migrację maszyny Wirtualnej.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Uruchomiony test trybu failover pomaga sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, przed migracją. 

1. Contoso uruchamia test trybu failover do najnowszego dostępnego punktu w czasie (**najnowsze przetworzone**).
2. Wybierają **Zamknij maszynę przed rozpoczęciem pracy awaryjnej**, dzięki czemu próbuje zamknąć źródłowej maszyny Wirtualnej, aby mogło nastąpić wyzwolenie pracy awaryjnej usługi Site Recovery. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
3. Tryb failover uruchomień testów: 

    - Uruchamia się upewnić, że wszystkie warunki wymagane do migracji w miejscu Sprawdzanie wymagań wstępnych.
    - Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
    - Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

3. Po zakończeniu pracy awaryjnej, replika maszyny Wirtualnej Azure pojawia się w portalu Azure. Contoso sprawdza, czy maszyna wirtualna jest odpowiedni rozmiar, który został połączony z siecią prawo, i że jest uruchomiona. 
4. Po zweryfikowaniu, ich wyczyścić trybu failover i zarejestrować i zapisać wszelkie obserwacje.

### <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

Aby dokonać migracji maszyny Wirtualnej, Contoso tworzy plan odzyskiwania, który zawiera maszyny Wirtualnej i zakończyć się niepowodzeniem planu za pośrednictwem usługi Azure.

1. Contoso tworzy plan i dodaje **OSTICKETWEB** do niego.

    ![Plan odzyskiwania](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Tryb failover są uruchamiane w planie. Wybierz najnowszy punkt odzyskiwania i określ, czy usługa Site Recovery należy dążyć do zamykania maszyny Wirtualnej lokalnego, aby mogło nastąpić wyzwolenie pracy awaryjnej. Postęp trybu failover może wykonać **zadania** strony.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Podczas pracy awaryjnej vCenter Server wydaje polecenia zatrzymania dwóch maszyn wirtualnych uruchomionych na hoście ESXi.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Po pracy awaryjnej Contoso sprawdza, czy jest wyświetlany maszyny Wirtualnej Azure, zgodnie z oczekiwaniami w portalu Azure.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Po sprawdzeniu maszyny Wirtualnej, ich zakończyć migrację. Zatrzymuje replikacji dla maszyny Wirtualnej i zatrzymuje rozliczeń usługi Site Recovery dla maszyny Wirtualnej.

    ![Tryb failover](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Potrzebujesz dodatkowej pomocy?**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) uruchomiony test trybu failover. 
- [Dowiedz się](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) Tworzenie planu odzyskiwania.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) przechodzenie w tryb failover na platformie Azure.


### <a name="connect-the-vm-to-the-database"></a>Połącz maszynę Wirtualną do bazy danych

Ostatni krok w procesie migracji Contoso zaktualizować parametry połączenia aplikacji do punktu w bazie danych Azure dla programu MySQL. 

1. Finalizowania połączenia SSH z maszyną wirtualną OSTICKETWEB przy użyciu programu Putty lub innego klienta SSH. Maszyna wirtualna jest prywatny, więc łączą się przy użyciu prywatnego adresu IP.

    ![łączenia z bazą danych](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![łączenia z bazą danych](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Zaktualizowanie ustawień, aby **OSTICKETWEB** maszyna wirtualna może się komunikować z **OSTICKETMYSQL** bazy danych. Konfiguracja jest obecnie zapisane na stałe z adresem IP lokalnymi 172.16.0.43.

    **Przed aktualizacją**
    
    ![Aktualizowanie adresu IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Po aktualizacji**
    
    ![Aktualizowanie adresu IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Aktualizowanie adresu IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Ich ponownego uruchomienia usługi z **systemctl Uruchom ponownie apache2**.

    ![Ponowne uruchamianie](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Na koniec zaktualizowanie rekordy DNS dla **OSTICKETWEB**, na jednym z kontrolerów domeny Contoso.

    ![Aktualizowanie systemu DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Czyszczenie po migracji

Po zakończeniu migracji osTicket warstwy aplikacji są uruchomione na maszynach wirtualnych Azure.

Teraz Contoso musi wykonać następujące czynności: 
- Usuń maszyny wirtualne VMware z magazynu vCenter
- Usuń lokalnych maszyn wirtualnych z lokalnym zadania tworzenia kopii zapasowej.
- Aktualizacja wewnętrznego dokumentacja nowych lokalizacji i adresów IP. 
- Przejrzyj wszystkie zasoby, które interakcji z lokalnymi maszynami wirtualnymi, a następnie zaktualizować wszystkie odpowiednie ustawienia lub dokumentacji odzwierciedlą nowej konfiguracji.
- Contoso użycia usługi Azure migracji z mapowaniem zależności w celu oceny **OSTICKETWEB** maszyny Wirtualnej do migracji. Teraz należy one usunąć agentów (Microsoft Monitoring Agent/Dependency Agent) są zainstalowane w tym celu z maszyny Wirtualnej.

## <a name="review-the-deployment"></a>Przegląd wdrożenia

Gdy aplikacja jest teraz uruchomiona Contoso muszą w pełni operacjonalizacji i zabezpieczenia infrastruktury nowego.

### <a name="security"></a>Bezpieczeństwo

Zespół zabezpieczeń firmy Contoso Przejrzyj maszyny Wirtualnej i bazy danych do określenia wszelkich problemów z bezpieczeństwem.

- Przeglądu grup zabezpieczeń sieci (NSG) dla maszyny Wirtualnej, w celu kontroli dostępu. Grupy NSG są używane do zapewnienia, że można przekazać tylko ruch do aplikacji.
- Uznają Zabezpieczanie danych na dyskach maszyny Wirtualnej przy użyciu szyfrowania dysku i Azure KeyVault.
- Komunikacja między wystąpienie maszyny Wirtualnej i bazy danych nie jest skonfigurowana do używania protokołu SSL. Należy to zrobić, aby upewnić się, że nie może być zaatakowane ruchu bazy danych.

[Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o rozwiązania w zakresie zabezpieczeń dla maszyn wirtualnych.

### <a name="backups"></a>Tworzenie kopii zapasowych

- Contoso wykona kopię zapasową danych na Maszynie wirtualnej za pomocą usługi Kopia zapasowa Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Nie należy skonfigurować tworzenie kopii zapasowych bazy danych. Bazy danych platformy Azure dla programu MySQL automatycznego tworzenia kopii zapasowych serwera i przechowuje. One wybrany do użycia nadmiarowość geograficzna dla bazy danych, tak aby zawierała odporne i gotowe do produkcji.

### <a name="licensing-and-cost-optimization"></a>Optymalizacja kosztów i licencjonowania

- Po wdrożeniu zasobów, Contoso przypisuje tagów Azure, zgodnie z ich decyzje podczas [infrastruktury platformy Azure](contoso-migration-infrastructure.md#set-up-tagging) wdrożenia.
- Nie ma żadnych licencjonowania problemów w przypadku serwerów Contoso Ubuntu.
- Contoso spowoduje włączenie zarządzania koszt Azure licencjonowane przez Cloudyn, oddziału firmy Microsoft. To rozwiązanie do zarządzania koszt usługi chmury, które pomaga wykorzystywać oraz zarządzania platformy Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o Azure kosztów zarządzania.


## <a name="next-steps"></a>Kolejne kroki

W tym scenariuszu firma Microsoft wykazało scenariuszy rehost końcowego czy wypróbowane Contoso. Migracji wirtualna aplikacji osTicket Linux lokalnego serwera sieci Web do maszyny Wirtualnej platformy Azure i migracji aplikacji bazy danych do wystąpienia MySQL na platformie Azure.

W zestawie dalej samouczkach z tej serii migracji zamierzamy Pokaż Contoso realizację bardziej złożonego zestawu elementów migracji, obejmujące refaktoryzacji aplikacji, zamiast prostego migracje przyrostu shift.
