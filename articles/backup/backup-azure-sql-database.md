---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: W tym samouczku opisano sposób tworzenia kopii zapasowych programu SQL Server na platformie Azure. W niniejszym artykule wyjaśniono również odzyskiwanie programu SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: c3321fb64c423b1b3c80f48fb97a70cc7dbc83f9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433569"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure

Bazy danych programu SQL Server są krytycznych obciążeń, które wymagają cel punktu odzyskiwania niski (RPO) i długoterminowego przechowywania. Usługa Azure Backup udostępnia rozwiązania tworzenia kopii zapasowych programu SQL Server, która wymaga zero infrastruktury: nie złożonych, Utwórz kopię zapasową serwera, agent zarządzania nie i bez magazynu kopii zapasowych do zarządzania. Usługa Azure Backup umożliwia scentralizowane zarządzanie dla kopii zapasowych, na wszystkich serwerach, które korzystają z programu SQL Server lub nawet różnych obciążeń.

W tym artykule dowiesz się:

> [!div class="checklist"]
> * Wymagania wstępne, aby utworzyć kopię zapasową wystąpienia programu SQL Server na platformie Azure.
> * Jak utworzyć i korzystać z magazynu usługi Recovery Services.
> * Jak skonfigurować kopie zapasowe bazy danych programu SQL Server.
> * Jak ustawić zasady tworzenia kopii zapasowej (lub przechowywania) dla punktów odzyskiwania.
> * Jak przywrócić bazę danych.

Przed rozpoczęciem procedury przedstawione w tym artykule, należy mieć wystąpienia programu SQL Server, który działa w systemie Azure. [Usługa SQL Marketplace virtual machines umożliwia szybkie tworzenie wystąpienia programu SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Następujące elementy są znane ograniczenia dotyczące publicznej wersji zapoznawczej:

- Maszyna wirtualna SQL (VM) wymaga łączności z Internetem na dostęp do platformy Azure z publicznymi adresami IP. Aby uzyskać więcej informacji, zobacz [ustanowienia połączenia z siecią](backup-azure-sql-database.md#establish-network-connectivity).
- Chronić maksymalnie 2000 baz danych SQL w jednym magazynie usług Recovery Services. Dodatkowe bazy danych SQL powinny być przechowywane w osobnym magazynie usługi Recovery Services.
- [Kopie zapasowe grup dostępności rozproszonych](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) podlegają ograniczeniom.
- SQL Server zawsze na wystąpienia klastra trybu Failover (występowanie) nie są obsługiwane.
- Umożliwia skonfigurowanie usługi Azure Backup, aby chronić bazy danych programu SQL Server w witrynie Azure portal. Program Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsów API REST nie są obecnie obsługiwane.

Zapoznaj się [sekcji często zadawane pytania](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) więcej informacji na temat pomocy technicznej/nie obsługiwane scenariusze.

## <a name="support-for-azure-geos"></a>Obsługa regionach geograficznych systemu Azure

Usługa Azure Backup jest obsługiwane w przypadku następujących obszarach geograficznych:

- Australia Południowo-Wschodnia (ASE) 
- Brazylia Południowa (BRS)
- Kanada Środkowa (CNC)
- Kanada Wschodnia (CE)
- Środkowe stany USA (CUS)
- Azja Wschodnia (EA)
- Australia Wschodnia (AE) 
- Wschodnie stany USA (EUS)
- Wschodnie stany USA 2 (EUS2)
- Indie Środkowe (INC) 
- Indie Południowe (INS)
- Japonia Wschodnia (JPE)
- Japonia Zachodnia (JPW)
- Korea Środkowa (KRC)
- Korea Południowa (KRS)
- Północno-środkowe stany USA (NCUS) 
- Europa Północna (NE) 
- Południowo-środkowe stany USA (SCUS) 
- Azja Południowo-Wschodnia (SEA)
- Południowe Zjednoczone Królestwo (UKS) 
- Zachodnie Zjednoczone Królestwo (UKW) 
- Zachodnio-środkowe stany USA (WCUS)
- Europa Zachodnia (WE) 
- Zachodnie stany USA (WUS)
- Zachodnie stany USA 2 (WUS 2) 

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Obsługa systemów operacyjnych i wersji programu SQL Server

W tej sekcji opisano obsługę usługi Azure Backup dla systemów operacyjnych i wersji programu SQL Server. Maszyny wirtualne SQL Marketplace Azure i maszyn wirtualnych spoza witryny Marketplace (SQL Server ręcznie zainstalowanym) są obsługiwane.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Obecnie nie jest obsługiwanym w systemie Linux.

### <a name="supported-sql-server-versions-and-editions"></a>Obsługiwane wersje i wersji programu SQL Server

- SQL Server 2012 Enterprise, Standard, Web i deweloper Express
- SQL Server 2014 Enterprise, Standard, Web i deweloper Express
- SQL Server 2016 Enterprise, Standard, Web i deweloper Express
- Express programu SQL Server 2017 Enterprise, Standard, Web i dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

Przed tworzysz kopię zapasową bazy danych programu SQL Server, sprawdź następujące warunki:

- Identyfikowanie lub [Utwórz magazyn usługi Recovery Services](backup-azure-sql-database.md#create-a-recovery-services-vault) w tym samym regionie lub ustawień regionalnych jako maszyny wirtualnej, który jest hostem wystąpienia programu SQL Server.
- [Sprawdzenie uprawnień na maszynie wirtualnej](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) wymagane do utworzenia kopii zapasowej bazy danych SQL.
- Upewnij się, że [maszyna wirtualna SQL ma łączność sieciową](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Może mieć tylko jedno rozwiązanie tworzenia kopii zapasowych w czasie, aby utworzyć kopię zapasową bazy danych programu SQL Server. Wyłącz wszystkie pozostałe kopie zapasowe SQL przed użyciem tej funkcji; w przeciwnym razie kopie zapasowe będą kolidować i zakończyć się niepowodzeniem. Można włączyć usługi Azure Backup dla maszyn wirtualnych IaaS wraz z kopii zapasowej SQL bez konfliktów.
>

Jeśli te warunki w danym środowisku, w dalszym ciągu [konfigurowania kopii zapasowej baz danych programu SQL Server](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Jeśli dowolny z wymagań wstępnych nie istnieje, Czytaj dalej.


## <a name="establish-network-connectivity"></a>Ustanowienie połączenia z siecią

Dla wszystkich operacji maszyny wirtualnej SQL musi mieć łączność platformy Azure z publicznymi adresami IP. Operacje maszyny wirtualnej SQL (takich jak bazy danych, odnajdowanie, konfigurowanie kopii zapasowych, Planowanie tworzenia kopii zapasowych, przywracania punktów odzyskiwania i tak dalej) bez łączności z publicznych adresów IP. Umożliwia jedną z następujących opcji zapewni przejrzystą ścieżkę ruchu kopii zapasowej:

- Lista dozwolonych adresów IP centrum danych platformy Azure zakresów: do listy dozwolonych zakresów adresów IP centrum danych platformy Azure, użyj [strony Centrum pobierania, aby uzyskać szczegółowe informacje na temat zakresów adresów IP i instrukcje](https://www.microsoft.com/download/details.aspx?id=41653). 
- Wdrażanie serwera proxy HTTP na potrzeby kierowania ruchu: podczas wykonywania kopii zapasowej bazy danych SQL na maszynie wirtualnej, rozszerzenie kopii zapasowej na maszynie Wirtualnej używa interfejsów API protokołu HTTPS do wysyłania poleceń zarządzania do usługi Azure Backup i danych do usługi Azure Storage. Zapasowy numer wewnętrzny również używa usługi Azure Active Directory (Azure AD) do uwierzytelniania. Kierować ruchem zapasowy numer wewnętrzny dla tych trzech usług za pośrednictwem serwera proxy HTTP. Rozszerzenie firmy jedyny składnik, który jest skonfigurowany do uzyskiwania dostępu do publicznej sieci internet.

Kompromis między opcje są możliwości zarządzania, kontrolę i kosztów.

> [!NOTE]
> Tagi usługi dla usługi Azure Backup powinna być dostępna przez ogólnie dostępne.
>

| Opcja | Zalety | Wady |
| ------ | ---------- | ------------- |
| Lista dozwolonych adresów IP, zakresy | Bez dodatkowych kosztów. <br/> Aby uzyskać dostęp do otwarcia w sieciowej grupie zabezpieczeń, użyj **AzureNetworkSecurityRule zestaw** polecenia cmdlet. | Złożone do zarządzania, ponieważ dotyczy zakresów adresów IP zmienia się wraz z upływem czasu. <br/>Zapewnia dostęp do całego systemu Azure, nie tylko usługi Azure Storage.|
| Użyj serwera proxy HTTP   | Zapewnia szczegółową kontrolę na serwerze proxy magazynu dozwolone adresy URL. <br/>Pojedynczy punkt internet dostępu do maszyn wirtualnych. <br/> Nie może ulec zmianie adresów IP platformy Azure. | Dodatkowe koszty, aby uruchomić Maszynę wirtualną z oprogramowaniem serwera proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ustawianie uprawnień dla innych niż - portalu Marketplace maszyn wirtualnych SQL

Aby utworzyć kopię zapasową maszyny wirtualnej, wymaga usługi Azure Backup **AzureBackupWindowsWorkload** rozszerzenia do zainstalowania. Jeśli korzystasz z maszynami wirtualnymi portalu Azure Marketplace, w dalszym ciągu [baz danych serwera SQL odnajdywanie](backup-azure-sql-database.md#discover-sql-server-databases). Jeśli maszyny wirtualnej, który jest hostem bazy danych SQL nie jest tworzony w portalu Azure Marketplace, należy wykonać następującą procedurę, aby zainstalować rozszerzenie i ustawić odpowiednie uprawnienia. Oprócz **AzureBackupWindowsWorkload** rozszerzenia usługi Azure Backup wymaga uprawnień administratora systemu SQL do ochrony baz danych SQL. Aby odnaleźć bazy danych na maszynie wirtualnej, usługa Azure Backup tworzy konto **NT Service\AzureWLBackupPluginSvc**. Aby usługa Azure Backup do odnajdywania baz danych SQL **NT Service\AzureWLBackupPluginSvc** konto musi mieć SQL i SQL uprawnienia administratora systemu. Poniższa procedura wyjaśnia, jak zapewnić te uprawnienia.

Aby skonfigurować uprawnienia:

1. W [witryny Azure portal](https://portal.azure.com), Otwórz magazyn usługi Recovery Services, która umożliwia ochronę baz danych SQL.

2. Na **magazyn usługi Recovery Services** pulpitu nawigacyjnego, wybierz opcję **kopii zapasowej**. **Cel kopii zapasowej** zostanie otwarte menu.

   ![Wybieranie kopii zapasowej, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cel kopii zapasowej** ustawić menu **gdzie jest uruchomione Twoje obciążenie** domyślne: **Azure**.

4. Rozwiń **jakich elementów chcesz utworzyć kopię zapasową** pole listy rozwijanej i wybierz pozycję **programu SQL Server na maszynie Wirtualnej platformy Azure**.

    ![Wybierz program SQL Server w maszynie Wirtualnej platformy Azure dla kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cel kopii zapasowej** menu zostaną wyświetlone dwa kroki: **odnajdowanie baz danych na maszynach wirtualnych** i **konfigurowania kopii zapasowej**. **Odnajdowanie baz danych na maszynach wirtualnych** kroku rozpocząć wyszukiwania dla maszyn wirtualnych platformy Azure.

    ![Przejrzyj dwa kroki cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. W obszarze **odnajdowanie baz danych na maszynach wirtualnych**, wybierz opcję **Uruchom odnajdowanie** wyszukiwania niechronionych maszyn wirtualnych w subskrypcji. Może upłynąć trochę czasu, aby wyszukać wszystkie maszyny wirtualne. Czas wyszukiwania zależy od liczby niechronionych maszyn wirtualnych w subskrypcji.

    ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania dla baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po odnalezieniu niechronione maszyny wirtualnej pojawi się na liście. Niechronione maszyny wirtualne są wyświetlane według ich maszynę wirtualną nazwą i grupą zasobów. Istnieje możliwość dla wielu maszyn wirtualnych mieć taką samą nazwę. Jednak maszyn wirtualnych o takiej samej nazwie należą do różnych grup zasobów. Jeśli oczekiwane maszyny wirtualnej nie ma na liście, zobacz, czy maszyna wirtualna jest już chroniony w magazynie.

6. Z listy maszyn wirtualnych wybierz maszynę Wirtualną, która ma SQL bazy danych do utworzenia kopii zapasowej, a następnie wybierz **odnajdowanie baz danych**. 

    Proces odnajdywania instaluje **AzureBackupWindowsWorkload** rozszerzenia na maszynie wirtualnej. Rozszerzenie umożliwia usłudze Azure Backup do komunikacji z maszyną wirtualną, aby go utworzyć kopię zapasową bazy danych SQL. Po zainstalowaniu rozszerzenia usługi Azure Backup tworzy konto usługi wirtualnej Windows **NT Service\AzureWLBackupPluginSvc** na maszynie wirtualnej. Wirtualnego konta usługi wymaga uprawnień administratora systemu SQL. W procesie usługa wirtualnego konta instalacji, jeśli wystąpi błąd `UserErrorSQLNoSysadminMembership`, zobacz [uprawnienia administratora systemu SQL naprawić](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    **Powiadomienia** obszaru przedstawia postęp odnajdywania bazy danych. Może upłynąć trochę czasu na ukończenie zadania. Godzina zadania zależy od liczby baz danych na maszynie wirtualnej. Po odnalezieniu wybranych baz danych, zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Po skojarzeniu bazy danych z magazynu usługi Recovery Services, następnym krokiem jest [skonfigurować zadanie tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Napraw uprawnienia administratora systemu SQL

Podczas procesu instalacji, jeśli wystąpi błąd `UserErrorSQLNoSysadminMembership`, użyj konta z uprawnieniami administratora systemu SQL Server, aby zalogować się do programu SQL Server Management Studio (SSMS). Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelniania Windows powinny działać.

1. Na serwerze SQL otwórz folder zabezpieczeń/nazwy logowania.

    ![Otwórz folder zabezpieczeń/logowania do konta](./media/backup-azure-sql-database/security-login-list.png)

2. Kliknij prawym przyciskiem myszy folder logowania, a następnie wybierz pozycję **nowy identyfikator logowania**. W **identyfikator logowania — nowy** okno dialogowe, wybierz opcję **wyszukiwania**.

    ![Podczas logowania — nowe okno dialogowe, wybierz opcję wyszukiwanie](./media/backup-azure-sql-database/new-login-search.png)

3. Konto usługi Windows wirtualnego **NT Service\AzureWLBackupPluginSvc** został utworzony podczas rejestracji maszyny wirtualnej i faza odnajdywania SQL. Wprowadź nazwę konta, jak pokazano na **wprowadź nazwę obiektu do wybrania** pole. Wybierz **Sprawdź nazwy** do rozpoznania nazwy. 

    ![Wybierz pozycję Sprawdź nazwy, aby rozpoznać nazwę usługi nieznany](./media/backup-azure-sql-database/check-name.png)

4. Wybierz **OK** aby zamknąć okno dialogowe.

5. W **ról serwera** upewnij się, że **sysadmin** zaznaczona została rola. Wybierz **OK** aby zamknąć okno dialogowe.

    ![Upewnij się, że wybrano roli serwera sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Teraz powinny istnieć wymagane uprawnienia.

6. Naprawiono błąd uprawnień, ale trzeba skojarzyć bazy danych z magazynu usługi Recovery Services. W witrynie Azure portal w **serwerów chronionych** listy, kliknij prawym przyciskiem myszy serwera, który jest błąd, stanu i wybierz pozycję **Rediscover baz danych**.

    ![Sprawdź, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

    **Powiadomienia** obszaru przedstawia postęp odnajdywania bazy danych. Może upłynąć trochę czasu na ukończenie zadania. Godzina zadania zależy od liczby baz danych na maszynie wirtualnej. Gdy zostaną odnalezione wybranych baz danych, zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

Po skojarzeniu bazy danych z magazynu usługi Recovery Services, następnym krokiem jest [skonfigurować zadanie tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Usługa Azure Backup umożliwia odnalezienie wszystkich baz danych w wystąpieniu programu SQL Server. Może chronić baz danych, zgodnie z wymogami dotyczącymi kopii zapasowej. Poniższa procedura umożliwia zidentyfikowanie maszyny wirtualnej, który jest hostem bazy danych SQL. Po stwierdzeniu maszyny wirtualnej usługi Azure Backup instaluje rozszerzeniem uproszczone, aby odnaleźć bazy danych programu SQL Server.

1. Zaloguj się do Twojej subskrypcji w [witryny Azure portal](https://portal.azure.com/).

2. W menu po lewej stronie wybierz **wszystkich usług**.

    ![Wybierz wszystkie usługi](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. W **wszystkich usług** okna dialogowego wprowadź **usługi Recovery Services**. Podczas wpisywania, dane wejściowe filtrowanie listy zasobów. Wybierz **Magazyny usługi Recovery Services** na liście.

    ![Wprowadź i wybierz pozycję magazynów usługi Recovery Services](./media/backup-azure-sql-database/all-services.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji. 

4. Na liście magazynów usługi Recovery Services wybierz magazyn na potrzeby ochrony baz danych SQL.

5. Na **magazyn usługi Recovery Services** pulpitu nawigacyjnego, wybierz opcję **kopii zapasowej**. **Cel kopii zapasowej** zostanie otwarte menu.

   ![Wybieranie kopii zapasowej, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

6. Na **cel kopii zapasowej** ustawić menu **gdzie jest uruchomione Twoje obciążenie** domyślne: **Azure**.

7. Rozwiń **co chcesz utworzyć kopię zapasową** pole listy rozwijanej i wybierz pozycję **programu SQL Server na maszynie Wirtualnej platformy Azure**.

    ![Wybierz program SQL Server w maszynie Wirtualnej platformy Azure dla kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cel kopii zapasowej** menu zostaną wyświetlone dwa kroki: **odnajdowanie baz danych na maszynach wirtualnych** i **konfigurowania kopii zapasowej**.
    
    ![Przejrzyj dwa kroki cel kopii zapasowej](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. W obszarze **odnajdowanie baz danych na maszynach wirtualnych**, wybierz opcję **Uruchom odnajdowanie** wyszukiwania niechronionych maszyn wirtualnych w subskrypcji. Przeszukiwanie wszystkich maszyn wirtualnych może potrwać. Czas wyszukiwania zależy od liczby niechronionych maszyn wirtualnych w subskrypcji.

    ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania dla baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Po odnalezieniu niechronione maszyny wirtualnej pojawi się na liście. Wiele maszyn wirtualnych może mieć takiej samej nazwie. Jednak maszyn wirtualnych o takiej samej nazwie należą do różnych grup zasobów. Niechronione maszyny wirtualne są wyświetlane według ich maszynę wirtualną nazwą i grupą zasobów. Jeśli oczekiwane maszyny wirtualnej nie ma na liście, zobacz, czy ta maszyna jest już chroniony w magazynie.

9. Z listy maszyn wirtualnych wybierz maszynę Wirtualną, która ma SQL bazy danych do utworzenia kopii zapasowej, a następnie wybierz **odnajdowanie baz danych**.

    Usługa Azure Backup umożliwia odnalezienie wszystkich baz danych SQL na maszynie wirtualnej. Aby dowiedzieć się, jak wykonywanego w fazie odnajdywania bazy danych, zobacz [operacji w tle](backup-azure-sql-database.md#background-operations). Po odnalezieniu baz danych SQL, możesz rozpocząć [skonfigurować zadanie tworzenia kopii zapasowej](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Operacje w tle

Kiedy używasz **odnajdowanie baz danych** narzędzia, usługi Azure Backup wykonuje następujące operacje w tle:

- Zarejestruj maszynę wirtualną przy użyciu magazynu usługi Recovery Services do tworzenia kopii zapasowych obciążeń. Wszystkie bazy danych na maszynie wirtualnej zarejestrowanej utworzeniem kopii zapasowej, w tym magazynie usługi Recovery Services. 

- Zainstaluj **AzureBackupWindowsWorkload** rozszerzenia na maszynie wirtualnej. Tworzenie kopii zapasowych programu SQL Server, bazy danych to rozwiązanie bez wykorzystania agentów. To rozszerzenie jest zainstalowane na maszynie wirtualnej, a nie agent zainstalowany na bazie danych SQL.

- Tworzenie konta usługi **NT Service\AzureWLBackupPluginSvc** na maszynie wirtualnej. Wszystkie operacje tworzenia kopii zapasowych i przywracania, użyj konta usługi. **NT Service\AzureWLBackupPluginSvc** musi mieć uprawnienia administratora systemu SQL. Wszystkie maszyny wirtualne SQL Marketplace wyposażony **SqlIaaSExtension** zainstalowane. **AzureBackupWindowsWorkload** używa rozszerzenia **SQLIaaSExtension** automatycznie uzyskać wymaganych uprawnień. Jeśli maszyna wirtualna nie ma **SqlIaaSExtension** zainstalowany, odnajdywanie bazy danych kończy się niepowodzeniem z komunikatem o błędzie `UserErrorSQLNoSysAdminMembership`. Aby dodać uprawnienia administratora systemu dla kopii zapasowej, postępuj zgodnie z instrukcjami [skonfigurować uprawnienia usługi Azure Backup dla nie - portalu Marketplace maszyn wirtualnych SQL](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Wybierz maszynę Wirtualną i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Skonfiguruj kopię zapasową baz danych programu SQL Server

Usługa Azure Backup udostępnia usługi zarządzania do ochrony baz danych programu SQL Server i zarządzanie zadaniami tworzenia kopii zapasowej. Z funkcji monitorowania i zarządzania zależą od magazynu usługi Recovery Services. 

> [!NOTE]
> Może mieć tylko jedno rozwiązanie tworzenia kopii zapasowych w czasie, aby utworzyć kopię zapasową bazy danych programu SQL Server. Wyłącz wszystkie pozostałe kopie zapasowe SQL przed użyciem tej funkcji; w przeciwnym razie kopie zapasowe będą kolidować i zakończyć się niepowodzeniem. Można włączyć usługi Azure Backup dla maszyn wirtualnych IaaS wraz z kopii zapasowej SQL bez konfliktów.
>

Aby skonfigurować ochronę dla bazy danych SQL:

1. Otwórz magazyn usługi Recovery Services, która jest zarejestrowana z maszyną wirtualną programu SQL.

2. Na **magazyn usługi Recovery Services** pulpitu nawigacyjnego, wybierz opcję **kopii zapasowej**. **Cel kopii zapasowej** zostanie otwarte menu.

   ![Wybieranie kopii zapasowej, aby otworzyć menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **cel kopii zapasowej** ustawić menu **gdzie jest uruchomione Twoje obciążenie** domyślne: **Azure**.

4. Rozwiń **co chcesz utworzyć kopię zapasową** pole listy rozwijanej i wybierz pozycję **programu SQL Server na maszynie Wirtualnej platformy Azure**.

    ![Wybierz program SQL Server w maszynie Wirtualnej platformy Azure dla kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    **Cel kopii zapasowej** menu zostaną wyświetlone dwa kroki: **odnajdowanie baz danych na maszynach wirtualnych** i **konfigurowania kopii zapasowej**.
    
    Jeśli zostanie wykonane czynności opisane w tym artykule w kolejności, znasz niechronionych maszyn wirtualnych i tego magazynu jest zarejestrowana z maszyną wirtualną. Teraz możesz przystąpić do konfigurowania ochrony dla baz danych SQL.
    
5. Na **cel kopii zapasowej** menu, wybierz opcję **konfigurowania kopii zapasowej**.

    ![Wybierz opcję skonfigurowania kopii zapasowej](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Usługa Azure Backup Wyświetla wszystkie wystąpienia programu SQL Server za pomocą autonomicznych baz danych i grup dostępności programu SQL Server Always On. Aby wyświetlić autonomicznych baz danych w wystąpieniu programu SQL Server, wybierz podwójną strzałkę w lewo nazwę wystąpienia. Na poniższych ilustracjach przedstawiono przykłady wystąpienia autonomicznego i zawsze włączonej grupy dostępności.

    > [!NOTE]
    > W grupie dostępności programu SQL Server Always On preferencję tworzenia kopii zapasowych SQL zostanie uznane. Jednak ze względu na ograniczenia platform SQL, pełne i różnicowe kopie zapasowe muszą mieć miejsce z węzła podstawowego. Dziennik z powrotem nawet odbywa się zgodnie z preferencjami kopii zapasowej. Ze względu na to ograniczenie węzła podstawowego zawsze musi być zarejestrowany dla grup dostępności.
    >

    ![Lista baz danych w wystąpieniu programu SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Wybierz strzałkę po lewej stronie zawsze włączonej grupy dostępności, aby wyświetlić listę baz danych.

    ![Lista baz danych w zawsze włączonej grupy dostępności](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Na liście baz danych, wybierz wszystkie bazy danych do ochrony, a następnie wybierz **OK**.

    ![Wybierz wiele baz danych do ochrony](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Wybierz maksymalnie 50 baz danych w danym momencie. Aby chronić więcej niż 50 baz danych, należy kilku przebiegów. Po włączeniu ochrony 50 pierwszych baz danych, powtórz ten krok, aby chronić następny zestaw baz danych.

    > [!Note] 
    > W celu zoptymalizowania obciążeń kopii zapasowych, kopia zapasowa Azure dzieli duże zadania tworzenia kopii zapasowej na wiele partii. Maksymalna liczba baz danych w ramach jednego zadania tworzenia kopii zapasowej jest 50.
    >
    >

7. Utwórz lub wybierz zasady kopii zapasowych na **kopii zapasowej** menu, wybierz opcję **zasady tworzenia kopii zapasowej**. **Zasady tworzenia kopii zapasowej** zostanie otwarte menu.

    ![Wybierz zasady tworzenia kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

8. W **wybierz zasady tworzenia kopii zapasowej** pole listy rozwijanej wybierz zasady tworzenia kopii zapasowej, a następnie wybierz **OK**. Aby uzyskać informacje na temat tworzenia zasad tworzenia kopii zapasowej, zobacz [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > W trakcie okresu Zapoznawczego nie możesz edytować zasady tworzenia kopii zapasowych. Jeśli chcesz, aby inne zasady niż ten dostępny na liście, należy utworzyć te zasady. Aby uzyskać informacje na temat tworzenia nowych zasad tworzenia kopii zapasowej, zobacz sekcję [Definiowanie zasad tworzenia kopii zapasowej](backup-azure-sql-database.md#define-a-backup-policy).

    ![Wybierz zasady tworzenia kopii zapasowych z listy](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    Na **zasady tworzenia kopii zapasowej** menu w **wybierz zasady tworzenia kopii zapasowej** pole listy rozwijanej możesz: 
    - Wybierz zasady domyślne: **HourlyLogBackup**.
    - Wybierz istniejące zasady tworzenia kopii zapasowej utworzone wcześniej do języka SQL.
    - [Definiowanie nowych zasad](backup-azure-sql-database.md#define-a-backup-policy) oparte na swój zakres od celu punktu odzyskiwania i przechowywania. 

    > [!Note]
    > Usługa Azure Backup obsługuje długoterminowego przechowywania danych, który jest oparty na schemacie tworzenia kopii zapasowej dziadek ojciec syn. Schemat optymalizuje wykorzystanie magazynu zaplecza podczas potrzebami w zakresie zgodności spotkania.
    >

9. Po wybraniu zasad kopii zapasowych na **menu kopia zapasowa**, wybierz opcję **Włącz kopię zapasową**.

    ![Włącz wybrane zasady kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

    Śledź postęp konfiguracji w **powiadomienia** obszaru portalu.

    ![Obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definiowanie zasad tworzenia kopii zapasowych

Zasady tworzenia kopii zapasowych określają macierz z informacjami podczas tworzenia kopii zapasowych są pobierane i jak długo są przechowywane. Użyj usługi Azure Backup można zaplanować trzy typy kopii zapasowych baz danych SQL:

* Pełna kopia zapasowa: pełną kopię zapasową bazy tworzy kopię zapasową całej bazy danych. Pełna kopia zapasowa zawiera wszystkie dane w konkretnej bazy danych lub zestaw grup plików lub plików oraz wystarczającą ilość dzienniki, aby odzyskać usuniętych danych. Co najwyżej użytkownik zainicjuje jedną pełną kopię zapasową dziennie. Istnieje możliwość wykonania pełnej kopii zapasowej na interwał codziennie lub co tydzień. 
* Różnicowa kopia zapasowa: różnicowej kopii zapasowej jest oparta na najnowszych, poprzedniego pełnych danych kopii zapasowej. Różnicowa kopia zapasowa przechwytuje dane, które uległy zmianie od czasu pełnej kopii zapasowej. Co najwyżej możesz wyzwolić jednym różnicowa kopia zapasowa każdego dnia. Nie można skonfigurować pełnej kopii zapasowej i różnicowej kopii zapasowej tego samego dnia.
* Kopia zapasowa dziennika transakcji: kopii zapasowej dziennika umożliwia w momencie przywracania do określonego sekundy. Maksymalnie można skonfigurować, kopii zapasowych dziennika transakcji co 15 minut.

Zasady tworzone w magazynie usługi Recovery Services poziomu. Wiele magazynów można używać tych samych zasad tworzenia kopii zapasowej, ale należy zastosować zasady kopii zapasowych do każdego magazynu. Po utworzeniu zasad tworzenia kopii zapasowej dzienny pełnej kopii zapasowej jest ustawieniem domyślnym. Można dodać różnicowej kopii zapasowej, ale tylko wtedy, jeśli skonfigurujesz pełnych kopie zapasowych co tydzień. Poniższa procedura wyjaśnia sposób tworzenia zasad kopii zapasowych dla wystąpienia programu SQL Server w maszynie wirtualnej platformy Azure. 

> [!NOTE]
> W wersji zapoznawczej nie można edytować zasad tworzenia kopii zapasowej. Zamiast tego należy utworzyć nowe zasady przy użyciu żądanego szczegółów.  
 
Aby utworzyć zasady kopii zapasowych:

1. W magazynie usługi Recovery Services, która chroni bazy danych SQL, kliknij przycisk **zasady tworzenia kopii zapasowych**, a następnie kliknij przycisk **Dodaj**. 

   ![Otwórz okno dialogowe Tworzenie nowych zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/new-policy-workflow.png)

   **Dodaj** zostanie wyświetlone menu.

2. W **Dodaj** menu, kliknij przycisk **programu SQL Server na maszynie Wirtualnej platformy Azure**.

   ![Wybierz typ zasad dla nowych zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/policy-type-details.png)

   Wybranie programu SQL Server w maszynie Wirtualnej platformy Azure definiuje typ zasad, a zostanie otwarte menu zasady tworzenia kopii zapasowych. **Zasady tworzenia kopii zapasowej** menu zawiera pola, które są niezbędne do nowych zasad tworzenia kopii zapasowych programu SQL Server.

3. W **nazwa_zasad**, wprowadź nazwę nowych zasad.

4. Pełna kopia zapasowa jest obowiązkowa. Nie można wyłączyć **pełnej kopii zapasowej** opcji. Kliknij przycisk **pełnej kopii zapasowej** możesz wyświetlać i edytować zasady. Nawet jeśli nie zmienisz zasad tworzenia kopii zapasowej, należy wyświetlić szczegóły zasad.

    ![nowe pola zasad tworzenia kopii zapasowej](./media/backup-azure-sql-database/full-backup-policy.png)

    W **zasad pełnej kopii zapasowej** menu dla **częstotliwość wykonywania kopii zapasowych**, wybierz **codzienne** lub **tygodniowy**. Aby uzyskać **codzienne**, wybierz godzinę i strefę czasową, po rozpoczęciu zadania tworzenia kopii zapasowej. Nie można utworzyć różnicowe kopie zapasowe codziennego wykonywania pełnych kopii zapasowych.

   ![codzienne ustawienia interwału](./media/backup-azure-sql-database/daily-interval.png)

    Aby uzyskać **tygodniowy**, wybierz dzień tygodnia, godzinę i strefę czasową, po rozpoczęciu zadania tworzenia kopii zapasowej.

   ![ustawienia interwału co tydzień](./media/backup-azure-sql-database/weekly-interval.png)

5. Domyślnie wszystkie **zakres przechowywania** są zaznaczone opcje: dzienne, tygodniowe, miesięczne i roczne. Usuń zaznaczenie jakiekolwiek ograniczenia zakresu przechowywania niepożądane. Ustalonych odstępach czasu do użycia. W **zasad pełnej kopii zapasowej** menu, wybierz opcję **OK** aby zaakceptować ustawienia.

   ![Ustawienia interwału zakres przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

    Punkty odzyskiwania są oznaczone do przechowywania danych na podstawie ich zakresu przechowywania. Na przykład jeśli wybierzesz codzienne pełnej kopii zapasowej, tylko jeden pełnej kopii zapasowej jest wyzwalany, każdego dnia. Tworzenie kopii zapasowej dla określonego dnia są oznaczone i przechowywane na podstawie co tydzień zakres przechowywania oraz ustawienia przechowywania co tydzień. Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

6. Aby dodać różnicowe zasady tworzenia kopii zapasowych, wybierz **różnicowej kopii zapasowej**. **Różnicowej kopii zapasowej zasad** zostanie otwarte menu. 

   ![Otwórz menu różnicowe zasady tworzenia kopii zapasowej](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    Na **różnicowej kopii zapasowej zasad** menu, wybierz opcję **Włącz** do otwierania kontrolki częstotliwość i przechowywania. Co najwyżej możesz wyzwolić jednym różnicowa kopia zapasowa każdego dnia.
    
    > [!Important] 
    > Różnicowe kopie zapasowe mogą być zachowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższy okres przechowywania, należy użyć pełnych kopii zapasowych.
    >

   ![Edytuj różnicowe zasady tworzenia kopii zapasowej](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Wybierz **OK** do zapisania zasad i wróć do strony głównej **zasady tworzenia kopii zapasowej** menu.

7. Aby dodać zasady kopii zapasowych dziennika transakcji, wybierz **kopii zapasowej dziennika**. **Kopii zapasowej dziennika** zostanie otwarte menu.

    W **kopii zapasowej dziennika** menu, wybierz opcję **Włącz**, a następnie ustaw częstotliwość i przechowywanie kontrolek. Kopie zapasowe dziennika może wystąpić nawet co 15 minut, a także mogą być zachowywane przez maksymalnie 35 dni. Wybierz **OK** do zapisania zasad i wróć do strony głównej **zasady tworzenia kopii zapasowej** menu.

   ![Edytuj zasady tworzenia kopii zapasowych dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. Na **zasady tworzenia kopii zapasowej** menu, wybierz, czy włączyć **kompresja kopii zapasowej SQL**. Kompresja jest domyślnie wyłączona.

    Na zapleczu usługa Azure Backup używa natywnego kompresja kopii zapasowej SQL.

9. Po zakończeniu zmiany w zasadach kopii zapasowych, wybierz **OK**. 

   ![Zaakceptować nowe zasady kopii zapasowych](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Przywracanie bazy danych SQL
Usługa Azure Backup oferuje funkcje, aby przywrócić pojedyncze bazy danych do określonej daty lub czasu (sekundy) przy użyciu kopii zapasowej dziennika transakcji. Usługa Azure Backup automatycznie określa odpowiednie różnicowej pełnego i łańcuch kopii zapasowych dzienników, które są wymagane do przywrócenia danych na podstawie Twojej czasów przywracania.

Możesz również wybrać określonej pełna lub różnicowa kopii zapasowej do przywrócenia określony punkt odzyskiwania, a nie w określonym czasie.

### <a name="pre-requisite-before-triggering-a-restore"></a>Warunek wstępny przed wyzwoleniem przywracania

1. Możesz przywrócić bazy danych do wystąpienia programu SQL Server, w tym samym regionie platformy Azure. Serwer docelowy musi być zarejestrowany w tym samym magazynie usługi Recovery Services jako źródło.  
2. Aby przywrócić bazę danych zaszyfrowanych TDE inny program SQL Server, należy najpierw przywrócić certyfikat na serwerze docelowym wykonując kroki opisane [tutaj](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Przed wyzwoleniem Przywracanie bazy danych "master" Uruchom wystąpienie programu SQL Server w trybie jednego użytkownika z opcją uruchamiania `-m AzureWorkloadBackup`. Argument `-m` opcja jest nazwa klienta. Tylko ten klient może otworzyć połączenia. Dla wszystkich systemowych baz danych (master, msdb modelu) należy zatrzymać usługę agenta SQL przed wyzwolenia przywracania. Zamknij wszelkie aplikacje, które mogą próbować wykraść połączenia do dowolnego z tych baz danych.

### <a name="steps-to-restore-a-database"></a>Kroki, aby przywrócić bazę danych:

1. Otwórz magazyn usługi Recovery Services, która jest zarejestrowana z maszyną wirtualną programu SQL.

2. Na **magazyn usługi Recovery Services** pulpit nawigacyjny w obszarze **użycia**, wybierz opcję **elementy kopii zapasowej** otworzyć **elementy kopii zapasowej** menu.

    ![Otworzyć menu elementy kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Na **elementy kopii zapasowej** menu, w obszarze **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**. 

    ![Wybierz SQL w maszynie Wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **Elementy kopii zapasowej** menu pokazuje listę baz danych SQL. 

4. Na liście baz danych SQL wybierz bazę danych do przywrócenia.

    ![Wybierz bazę danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych, zostanie otwarte menu. Menu udostępnia szczegóły kopii zapasowej bazy danych, w tym:

    * Najstarszej i najnowszej punktów przywracania.
    * Stan kopii zapasowej dziennika w ciągu ostatnich 24 godzin (w przypadku baz danych w modelu odzyskiwania pełnego i z niepełnym dziennikiem, jeśli skonfigurowane dla kopii zapasowych dziennika transakcji).

5. W menu dla wybranej bazy danych, wybierz **przywracania bazy danych**. **Przywrócić** zostanie otwarte menu.

    ![Wybierz opcję przywracania bazy danych](./media/backup-azure-sql-database/restore-db-button.png)

    Gdy **przywrócić** zostanie otwarte menu **przywracania konfiguracji** również zostanie otwarte menu. **Przywracania konfiguracji** menu jest pierwszym krokiem do skonfigurowania przywracania. Użyj tego menu, aby wybrać miejsce do przywracania danych. Dostępne opcje to:
    - **Lokalizacja alternatywna**: Przywróć bazę danych do lokalizacji alternatywnej i zachować oryginalne źródłowej bazy danych.
    - **Zastąp bazę danych**: przywraca dane do tego samego wystąpienia programu SQL Server jako oryginalnego źródła. Ta opcja powoduje zastąpienie oryginalnej bazy danych.

    > [!Important]
    > Jeśli wybrana baza danych, należy do grupy dostępności Always On programu SQL Server nie zezwala na bazy danych zostaną zastąpione. W tym przypadku tylko **lokalizacji alternatywnej** opcja jest włączona.
    >

    ![Przywracanie menu konfiguracji](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywróć do lokalizacji alternatywnej

Ta procedura przeprowadzi Cię przez przywrócenie danych do alternatywnej lokalizacji. Aby zastąpić bazy danych podczas przywracania, w dalszym ciągu [przywrócić i zastąpienia bazy danych](backup-azure-sql-database.md#restore-and-overwrite-the-database). Na tym etapie magazynu usługi Recovery Services jest otwarty i **przywracania konfiguracji** menu jest widoczne. Jeśli komputer nie znajduje się na tym etapie, Rozpocznij od [przywracania bazy danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Możesz przywrócić bazy danych do wystąpienia programu SQL Server, w tym samym regionie platformy Azure. Serwer docelowy musi być zarejestrowany w magazynie usługi Recovery Services. 
>

Na **przywracania konfiguracji** menu **serwera** pole listy rozwijanej pokazuje tylko wystąpienia programu SQL Server, które są zarejestrowane w magazynie usługi Recovery Services. Jeśli serwer, który ma nie ma na liście, zobacz [baz danych serwera SQL odnajdywanie](backup-azure-sql-database.md#discover-sql-server-databases) można znaleźć serwera. Podczas procesu odnajdywania nowe serwery są rejestrowane w magazynie usługi Recovery Services.

1. W **przywracania konfiguracji** menu:

    * W obszarze **gdzie chcesz przywrócić**, wybierz opcję **lokalizacji alternatywnej**.
    * Otwórz **serwera** listy rozwijanej pole, a następnie wybierz wystąpienie programu SQL Server, aby przywrócić bazę danych.
    * Otwórz **wystąpienia** listy rozwijanej pole, a następnie wybierz wystąpienie programu SQL Server.
    * W **przywrócić Nazwa bazy danych** wprowadź nazwę docelowej bazy danych.
    * Jeśli ma to zastosowanie, wybierz opcję **zastąpić, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu SQL**.
    * Wybierz **OK** UK ończyć miejsca docelowego i Kontynuuj, aby wybrać punkt przywracania.

    ![Podanie wartości menu Przywracanie konfiguracji](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Na **wybierz punkt przywracania** menu, wybierz **dzienniki (punkt w czasie)** lub **pełna i różnicowa** punkt przywracania. Aby przywrócić określonego dziennika w momencie, przejdź do tego kroku. Aby przywrócić punkt przywracania pełnych i różnicowych, przejdź do kroku 3.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Przywracanie punktu w czasie jest dostępna tylko w przypadku kopii zapasowych dzienników dla baz danych przy użyciu modelu odzyskiwania pełnego i z niepełnym dziennikiem. Aby przywrócić do określonego punktu w czasie:

    1. Wybierz **dzienniki (punkt w czasie)** jako typ przywracania.

        ![Wybierz typ punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **Data/Godzina przywracania**, wybierz kalendarz mini, aby otworzyć **kalendarza**. Na **kalendarza**pogrubione daty mają punkty odzyskiwania i zostanie wyróżniona bieżącą datę. Wybierz datę z punktami odzyskiwania. Nie można wybierać dat bez punktów odzyskiwania.

        ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu daty, wykres osi czasu wyświetla dostępne punkty odzyskiwania ciągłego zakresu.

    3. Za pomocą osi czasu wykresu lub **czasu** okno dialogowe, aby określić godzinę dla punktu odzyskiwania. Wybierz **OK** aby ukończyć etap tworzenia punktu przywracania.
    
       ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Wybierz punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Na **Advanced Configuration** menu:

        * Aby ochronić niedziałającą bazę danych po przywróceniu, należy ustawić **Przywróć z opcją NORECOVERY** do **włączone**.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w **docelowej** kolumny.
        * Wybierz **OK** zatwierdzić ustawienia. Zamknij **Advanced Configuration** menu.

    5. Na **przywrócić** menu, wybierz opcję **przywrócić** można uruchomić zadania przywracania. Śledzenie postępu przywracania w **powiadomienia** obszaru, lub wybierz **przywrócić zadania** menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

3. Na **wybierz punkt przywracania** menu, wybierz **dzienniki (punkt w czasie)** lub **pełna i różnicowa** punkt przywracania. Aby przywrócić dziennika w momencie, wróć do kroku 2. Ten krok spowoduje przywrócenie punktu określonego pełna lub różnicowa przywracania. Możesz zobaczyć wszystkich punktów odzyskiwania pełnych i różnicowych, w ciągu ostatnich 30 dni. Aby wyświetlić punkty odzyskiwania starsze niż 30 dni, wybierz **filtru** otworzyć **punktów przywracania filtru** menu. Dla punktu odzyskiwania różnicowa kopia zapasowa Azure najpierw przywraca punkt odpowiednie pełnego odzyskiwania, a następnie stosuje wybrany różnicowej punkt odzyskiwania.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W **wybierz punkt przywracania** menu, wybierz **pełna i różnicowa**.

       ![Wybierz pełne i różnicowe](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        W menu pojawia się lista dostępnych punktów odzyskiwania.

    2. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK** do wykonania procedury punktu przywracania. 

        ![Wybierz punkt pełnego odzyskiwania](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Punktu przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

        ![Menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Na **Advanced Configuration** menu:

        * Aby ochronić niedziałającą bazę danych po przywróceniu, należy ustawić **Przywróć z opcją NORECOVERY** do **włączone**. **Przywróć z opcją NORECOVERY** jest domyślnie wyłączona.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w **docelowej** kolumny.
        * Wybierz **OK** zatwierdzić ustawienia. Zamknij **Advanced Configuration** menu.

    4. Na **przywrócić** menu, wybierz opcję **przywrócić** można uruchomić zadania przywracania. Śledzenie postępu przywracania w **powiadomienia** obszaru, lub wybierz **przywrócić zadania** menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Przywracanie i zastąpienia bazy danych

Ta procedura zawiera Cię przez proces przywracania danych i zastępowanie bazy danych. Aby przywrócić do innej lokalizacji, w dalszym ciągu [Przywróć do lokalizacji alternatywnej](backup-azure-sql-database.md#restore-to-an-alternate-location). Na tym etapie magazynu usługi Recovery Services jest otwarty i **przywracania konfiguracji** menu jest widoczny (zobacz poniższy obraz). Jeśli komputer nie znajduje się na tym etapie, Rozpocznij od [przywracania bazy danych SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Przywracanie menu konfiguracji](./media/backup-azure-sql-database/restore-overwrite-db.png)

Na **przywracania konfiguracji** menu **serwera** pole listy rozwijanej pokazuje tylko wystąpienia programu SQL Server, które są zarejestrowane w magazynie usługi Recovery Services. Jeśli serwer, który ma nie ma na liście, zobacz [baz danych serwera SQL odnajdywanie](backup-azure-sql-database.md#discover-sql-server-databases) można znaleźć serwera. Podczas procesu odnajdywania nowe serwery są rejestrowane w magazynie usługi Recovery Services.

1. W **przywracania konfiguracji** menu, wybierz opcję **zastąpienia bazy danych**, a następnie wybierz pozycję **OK** UK ończyć miejsca docelowego. 

   ![Wybierz opcję Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    **Serwera**, **wystąpienia**, i **przywrócić Nazwa bazy danych** ustawienia nie są konieczne.

2. Na **wybierz punkt przywracania** menu, wybierz **dzienniki (punkt w czasie)** lub **pełna i różnicowa** punkt przywracania. Aby przywrócić określonego dziennika w momencie, przejdź do tego kroku. Aby przywrócić punkt przywracania pełnych i różnicowych, przejdź do kroku 3.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    Przywracanie punktu w czasie jest dostępna tylko w przypadku kopii zapasowych dzienników dla baz danych przy użyciu modelu odzyskiwania pełnego i z niepełnym dziennikiem. Aby przywrócić do określonych sekundy:

    1. Wybierz **dzienniki (punkt w czasie)** punkt przywracania.

        ![Wybierz typ punktu przywracania](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. W obszarze **Data/Godzina przywracania**, wybierz kalendarz mini, aby otworzyć **kalendarza**. Na **kalendarza**pogrubione daty mają punkty odzyskiwania i zostanie wyróżniona bieżącą datę. Wybierz datę z punktami odzyskiwania. Nie można wybierać dat bez punktów odzyskiwania.

        ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Po wybraniu daty, wykres osi czasu wyświetla dostępne punkty odzyskiwania.

    3. Za pomocą osi czasu wykresu lub **czasu** okno dialogowe, aby określić godzinę dla punktu odzyskiwania. Wybierz **OK** aby ukończyć etap tworzenia punktu przywracania.
    
       ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        **Wybierz punkt przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

       ![menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Na **Advanced Configuration** menu:

        * Aby ochronić niedziałającą bazę danych po przywróceniu, należy ustawić **Przywróć z opcją NORECOVERY** do **włączone**.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w **docelowej** kolumny.
        * Wybierz **OK** zatwierdzić ustawienia. Zamknij **Advanced Configuration** menu.

    5. Na **przywrócić** menu, wybierz opcję **przywrócić** można uruchomić zadania przywracania. Śledzenie postępu przywracania w **powiadomienia** obszaru, lub wybierz **przywrócić zadania** menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

3. Na **wybierz punkt przywracania** menu, wybierz **dzienniki (punkt w czasie)** lub **pełna i różnicowa** punkt przywracania. Aby przywrócić dziennika w momencie, wróć do kroku 2. Ten krok spowoduje przywrócenie punktu określonego pełna lub różnicowa przywracania. Możesz zobaczyć wszystkich punktów odzyskiwania pełnych i różnicowych, w ciągu ostatnich 30 dni. Aby wyświetlić punkty odzyskiwania starsze niż 30 dni, wybierz **filtru** otworzyć **punktów przywracania filtru** menu. Dla punktu odzyskiwania różnicowa kopia zapasowa Azure najpierw przywraca punkt odpowiednie pełnego odzyskiwania, a następnie stosuje wybrany różnicowej punkt odzyskiwania.

    ![Wybierz menu punkt przywracania](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. W **wybierz punkt przywracania** menu, wybierz **pełna i różnicowa**.

       ![Wybierz pełne i różnicowe](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        W menu pojawia się lista dostępnych punktów odzyskiwania.

    2. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK** do wykonania procedury punktu przywracania. 

        ![Wybierz punkt pełnego odzyskiwania](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        **Punktu przywracania** zamknięciu menu i **Advanced Configuration** zostanie otwarte menu.

        ![Menu konfiguracji zaawansowanej](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Na **Advanced Configuration** menu:

        * Aby ochronić niedziałającą bazę danych po przywróceniu, należy ustawić **Przywróć z opcją NORECOVERY** do **włączone**. **Przywróć z opcją NORECOVERY** jest domyślnie wyłączona.
        * Aby zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę w **docelowej** kolumny.
        * Wybierz **OK** zatwierdzić ustawienia. Zamknij **Advanced Configuration** menu.

    4. Na **przywrócić** menu, wybierz opcję **przywrócić** można uruchomić zadania przywracania. Śledź postęp przywracania w **powiadomienia** obszaru lub wybierając **przywrócić zadania** menu bazy danych.

       ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Zarządzanie operacjami usługi Azure Backup dla programu SQL na maszynach wirtualnych platformy Azure

Ta sekcja zawiera informacje na temat różnych operacji zarządzania usługi Azure Backup, które są dostępne dla programu SQL na maszynach wirtualnych platformy Azure. Istnieją następujące operacje wysokiego poziomu:

* Monitorowanie zadań
* Alerty kopii zapasowej
* Zatrzymanie ochrony bazy danych SQL
* Wznawianie ochrony bazy danych SQL
* Wyzwalanie zadania tworzenia kopii zapasowej ad hoc
* Wyrejestruj serwer, na którym działa program SQL Server

### <a name="monitor-backup-jobs"></a>Monitorowanie zadań tworzenia kopii zapasowej
Usługa Azure Backup jest klasy przedsiębiorstwa, który zapewnia zaawansowane alerty kopii zapasowej i powiadomienia dotyczące błędów. (Zobacz [wyświetlać alerty kopii zapasowej](backup-azure-sql-database.md#view-backup-alerts).) Aby monitorowanie określonych zadań, można użyć dowolnego z poniższych opcji, zgodnie z wymaganiami.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Użyj witryny Azure portal dla operacji ad hoc
Usługa Azure Backup zawiera wyzwalanych wszystkie ręcznie lub ad hoc, zadania w **zadania tworzenia kopii zapasowej** portalu. Zadania, które są dostępne w **zadania tworzenia kopii zapasowej** portal obejmują:
- Skonfiguruj wszystkie operacje tworzenia kopii zapasowej.
- Ręcznie wyzwolone operacje tworzenia kopii zapasowej.
- Operacji przywracania.
- Rejestracja i odnajdywanie operacji bazy danych.
- Zatrzymanie operacji tworzenia kopii zapasowej. 

![Portal zadania tworzenia kopii zapasowej](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Wszystkie zaplanowane zadania tworzenia kopii zapasowej, łącznie z pełnej, różnicowej i kopii zapasowej dziennika, nie są wyświetlane w **zadania tworzenia kopii zapasowej** portalu. Do monitorowania zaplanowanych zadań kopii zapasowej, należy użyć programu SQL Server Management Studio, zgodnie z opisem w następnej sekcji.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Użyj programu SQL Server Management Studio zadań tworzenia kopii zapasowej
Usługa Azure Backup używa natywnych interfejsów API SQL dla wszystkich operacji tworzenia kopii zapasowej. Przy użyciu natywnych interfejsów API do pobierania wszystkich informacji zadania z [tabeli zestawu kopii zapasowych SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) w bazie danych msdb.

Poniższy przykład to zapytanie, które pobiera wszystkie zadania tworzenia kopii zapasowej bazy danych o nazwie **DB1**. Dostosowywanie zapytania w przypadku zaawansowanego monitorowania.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="view-backup-alerts"></a>Wyświetl alerty kopii zapasowej

Ponieważ kopie zapasowe dziennika występuje co 15 minut od czasu do czasu monitorowania zadań tworzenia kopii zapasowej może być żmudne. Usługa Azure Backup udostępnia pomoc w takiej sytuacji. Wiadomości e-mail dla alertów są wyzwalane dla wszystkich błędów kopii zapasowych. Alerty są konsolidowane na poziomie bazy danych przez kod błędu. Alerty w wiadomościach e-mail są wysyłane tylko w przypadku pierwszego niepowodzenia wykonywania kopii zapasowej dla bazy danych. Zaloguj się do witryny Azure portal do monitorowania wszystkich błędów dla bazy danych. 

Aby monitorować alerty kopii zapasowej:

1. Zaloguj się do subskrypcji platformy Azure w [witryny Azure portal](https://portal.azure.com).

2. Otwórz magazyn usługi Recovery Services, która jest zarejestrowana z maszyną wirtualną programu SQL.

3. Na **magazyn usługi Recovery Services** pulpitu nawigacyjnego, wybierz opcję **alerty i zdarzenia**. 

   ![Wybierz alerty i zdarzenia](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Na **alerty i zdarzenia** menu, wybierz opcję **alerty kopii zapasowej** do wyświetlania na liście alertów.

   ![Wybierz alerty kopii zapasowej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymanie ochrony bazy danych programu SQL Server

Po zatrzymaniu ochrony bazy danych programu SQL Server, usługi Azure Backup żądań opcję przechowywania punktów odzyskiwania. Istnieją dwa sposoby na zatrzymanie ochrony bazy danych SQL:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej i Usuń wszystkie punkty odzyskiwania.
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej, ale pozostawienie punktów odzyskiwania.

Nic nie kosztuje pozostawienie punktów odzyskiwania. Punkty odzyskiwania dla programu SQL pociągnąć za sobą chronionego wystąpienia SQL cen opłaty, a także zajmowanego w magazynie. Aby uzyskać więcej informacji na temat cen usługi Azure Backup SQL, zobacz [stronę z cennikiem usługi Kopia zapasowa Azure](https://azure.microsoft.com/pricing/details/backup/). 

Aby zatrzymać ochronę dla bazy danych:

1. Otwórz magazyn usługi Recovery Services, która jest zarejestrowana z maszyną wirtualną programu SQL.

2. Na **magazyn usługi Recovery Services** pulpit nawigacyjny w obszarze **użycia**, wybierz opcję **elementy kopii zapasowej** otworzyć **elementy kopii zapasowej** menu.

    ![Otworzyć menu elementy kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Na **elementy kopii zapasowej** menu, w obszarze **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**. 

    ![Wybierz SQL w maszynie Wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    **Elementy kopii zapasowej** menu pokazuje listę baz danych SQL. 

4. Na liście baz danych SQL wybierz bazę danych, aby zatrzymać ochronę.

    ![Wybierz bazę danych, aby zatrzymać ochronę](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Po wybraniu bazy danych, zostanie otwarte menu.

5. W menu dla wybranej bazy danych, wybierz **Zatrzymaj kopię zapasową**. 

    ![Wybierz opcję Zatrzymaj kopię zapasową](./media/backup-azure-sql-database/stop-db-button.png)

    **Zatrzymaj tworzenie kopii zapasowej** zostanie otwarte menu.

6. Na **Zatrzymaj tworzenie kopii zapasowej** menu wybrać **Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Opcjonalnie podaj przyczynę zatrzymanie ochrony i komentarz.

    ![Zatrzymaj menu kopia zapasowa](./media/backup-azure-sql-database/stop-backup-button.png)

7. Wybierz **Zatrzymaj kopię zapasową** zatrzymanie ochrony bazy danych. 

### <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Jeśli **Zachowaj dane kopii zapasowej** wybrano opcję, jeśli zatrzymano ochronę bazy danych SQL, możesz wznowić ochronę. Jeśli dane kopii zapasowej nie została zachowana, nie można wznowić ochronę. 

1. Aby wznowić ochronę bazy danych SQL, otwórz element kopii zapasowej, a następnie wybierz **Wznów tworzenie kopii zapasowej**.

    ![Wybierz pozycję Wznów tworzenie kopii zapasowej, aby wznowić ochronę bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

   **Zasady tworzenia kopii zapasowej** zostanie otwarte menu.

2. Na **zasady tworzenia kopii zapasowej** menu, wybierz zasady, a następnie wybierz **Zapisz**.

### <a name="trigger-an-adhoc-backup"></a>Wyzwalanie tworzenia kopii zapasowej ad hoc

Wyzwalanie tworzenia kopii zapasowych ad hoc, zgodnie z potrzebami. Istnieją cztery typy kopii zapasowych ad hoc:

* Pełna kopia zapasowa
* Tylko do kopiowania pełnej kopii zapasowej
* Różnicowa kopia zapasowa
* Kopia zapasowa dziennika

Aby uzyskać szczegółowe informacje dotyczące każdego typu, zobacz [kopii zapasowych typów SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Aby wyrejestrować wystąpienia programu SQL Server, po usunięciu ochrony, ale przed usunięciem magazynu:

1. Otwórz magazyn usługi Recovery Services, która jest zarejestrowana z maszyną wirtualną programu SQL.

2. Na **magazyn usługi Recovery Services** pulpit nawigacyjny w obszarze **Zarządzaj**, wybierz opcję **infrastruktura zapasowa**.  

   ![Wybierz infrastruktury kopii zapasowych](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. W obszarze **serwerów zarządzania**, wybierz opcję **serwerów chronionych**.

   ![Wybierz serwery chronione](./media/backup-azure-sql-database/protected-servers.png)

    **Serwerów chronionych** zostanie otwarte menu. 

4. Na **serwerów chronionych** menu, wybierz serwer, które chcesz wyrejestrować. Aby usunąć magazynu, musisz wyrejestrować wszystkie serwery.

5. Na **serwerów chronionych** menu, kliknij prawym przyciskiem myszy serwer chroniony, a następnie wybierz pozycję **Usuń**. 

   ![Wybierz opcję Usuń](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>Często zadawane pytania

W poniższej sekcji przedstawiono dodatkowe informacje na temat kopii zapasowej bazy danych SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>Czy mogę ograniczyć szybkość zasad kopii zapasowych programu SQL Server

Tak. Możesz ograniczyć szybkość jaką wykonuje zasad tworzenia kopii zapasowej, aby zminimalizować wpływ na wystąpieniu programu SQL Server.

Aby zmienić ustawienia:

1. W wystąpieniu programu SQL Server, w folderze C:\Program Files\Azure obciążenia Backup\bin Otwórz **TaskThrottlerSettings.json** pliku.

2. W pliku TaskThrottlerSettings.json zmienić **DefaultBackupTasksThreshold** ustawienie niższej wartości (na przykład 5).

3. Zapisz zmiany. Zamknij plik.

4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie **obciążenie kopii zapasowej platformy Azure, usługa koordynatora**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełnej kopii zapasowej z repliki pomocniczej

Nie. Ta funkcja nie jest obsługiwana.

### <a name="do-successful-backup-jobs-create-alerts"></a>Zadania tworzenia kopii zapasowej zakończone pomyślnie, należy tworzyć alerty?

Nie. Pomyślne zadania tworzenia kopii zapasowej nie generują alerty. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powieść.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Może wyświetlać szczegóły dla zaplanowanych zadań kopii zapasowej w menu zadania?

Nie. **Zadania tworzenia kopii zapasowej** menu pokazuje szczegóły zadania ad hoc, ale nie zaplanowanych zadań kopii zapasowej. Jeśli wszelkich zaplanowanych zadań kopii zapasowej nie powiedzie się, są one dostępne w alertach zadania zakończonego niepowodzeniem. Aby monitorować wszystkie zaplanowane i zadania tworzenia kopii zapasowej ad hoc, użyj [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Po wybraniu pozycji wystąpienia programu SQL Server są przyszłych baz danych, które automatycznie dodaje?

Nie. Jeśli wybierzesz opcję poziomu serwera można skonfigurować ochrony dla wystąpienia programu SQL Server, dodawane są wszystkie bazy danych. Po dodaniu bazy danych do wystąpienia programu SQL Server, po skonfigurowaniu ochrony, należy ręcznie dodać nowych baz danych, aby chronić je. Bazy danych nie są automatycznie umieszczane w skonfigurowanym ochrony.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Zmiana modelu odzyskiwania, jak ponownie rozpocząć ochrony?

Wyzwól tworzenie pełnej kopii zapasowej. Kopie zapasowe dziennika rozpocząć zgodnie z oczekiwaniami.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Czy można chronić SQL zawsze włączonych grup dostępności gdzie znajduje się replika podstawowa jest w środowisku lokalnym

Nie. Usługa Azure Backup chroni serwery SQL działającej na platformie Azure. Jeśli grupy dostępności (AG) jest rozłożona się między maszynami platformy Azure i w środowisku lokalnym, AG mogą być chronione, tylko wtedy, gdy replika podstawowa jest uruchomiona na platformie Azure. Ponadto usługa Azure Backup chroni tylko węzły, w tym samym regionie platformy Azure co magazyn usługi Recovery Services.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o usłudze Azure Backup, zobacz przykładowy skrypt programu PowerShell platformy Azure do tworzenia kopii zapasowych zaszyfrowanych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Tworzenie kopii zapasowej zaszyfrowanej maszyny Wirtualnej](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
