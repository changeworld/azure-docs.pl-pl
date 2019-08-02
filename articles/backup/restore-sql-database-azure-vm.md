---
title: Używanie Azure Backup do przywracania kopii zapasowych SQL Server baz danych na maszynie wirtualnej platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób przywracania SQL Server baz danych, które są uruchomione na maszynie wirtualnej platformy Azure i których kopia zapasowa została utworzona przy użyciu Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dacurwin
ms.openlocfilehash: 71867e520d9c98b4af4d4f18f3d08c9e8cc4a8c4
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639549"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Przywracanie SQL Server baz danych na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure (VM), w której utworzono kopię zapasową usługi [Azure Backup](backup-overview.md) do magazynu Azure Backup Recovery Services.

W tym artykule opisano sposób przywracania SQL Server baz danych. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych baz danych SQL Server na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Przywracanie do czasu lub punktu odzyskiwania

Azure Backup można przywrócić SQL Server baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

- Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika transakcji. Azure Backup automatycznie określa odpowiednią pełną, różnicową kopię zapasową i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.
- Przywróć określoną pełną lub różnicową kopię zapasową w celu przywrócenia do określonego punktu odzyskiwania.


## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do przywracania bazy danych należy zwrócić uwagę na następujące kwestie:

- Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure.
- Serwer docelowy musi być zarejestrowany w tym samym magazynie co źródło.
- Aby przywrócić bazę danych zaszyfrowaną TDE na inny SQL Server, należy najpierw [przywrócić certyfikat na serwerze docelowym](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Przed przystąpieniem do przywracania bazy danych "Master" uruchom wystąpienie SQL Server w trybie jednego użytkownika przy użyciu opcji Start **-m AzureWorkloadBackup**.
    - Wartość parametru **-m** to nazwa klienta.
    - Połączenie może otworzyć tylko określona nazwa klienta.
- Dla wszystkich systemowych baz danych (model, Master, msdb) Zatrzymaj usługę agenta SQL Server przed wywołaniem przywracania.
- Zamknij wszystkie aplikacje, które mogą próbować nawiązać połączenie z dowolną z tych baz danych.
- Jeśli na serwerze jest uruchomionych wiele wystąpień, wszystkie wystąpienia powinny działać, a w przeciwnym razie serwer nie zostanie wyświetlony na liście serwerów docelowych, do których ma zostać przywrócona baza danych.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Do przywrócenia wymagane są następujące uprawnienia:

* Uprawnienia **operatora kopii zapasowych** w magazynie, w którym wykonujesz przywracanie.
* Dostęp współautora **(zapisu)** do ŹRÓDŁOWEJ maszyny wirtualnej, której kopia zapasowa została utworzona.
* Dostęp współautora **(zapis)** do docelowej maszyny wirtualnej:
    - Jeśli przywracasz do tej samej maszyny wirtualnej, jest to źródłowa maszyna wirtualna.
    - Jeśli przywracasz do lokalizacji alternatywnej, jest to nowa docelowa maszyna wirtualna.

Przywróć w następujący sposób:
1. Otwórz magazyn, w którym zarejestrowano SQL Server maszynę wirtualną.
2. Na pulpicie nawigacyjnym magazynu w obszarze **użycie**wybierz pozycję **elementy kopii zapasowej**.
3. W obszarze **elementy kopii zapasowej**w obszarze **Typ zarządzania kopiami zapasowymi**wybierz pozycję **SQL na maszynie wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Wybierz bazę danych do przywrócenia.

    ![Wybieranie bazy danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Przejrzyj menu Baza danych. Zawiera informacje o kopii zapasowej bazy danych, w tym:

    * Najstarsze i najnowsze punkty przywracania.
    * Stan kopii zapasowej dziennika dla ostatnich 24 godzin dla baz danych, które są w pełni i w trybie odzyskiwania zbiorczego i są skonfigurowane do tworzenia kopii zapasowych dzienników transakcyjnych.

6. Wybierz pozycję **Przywróć bazę danych**.

    ![Wybieranie pozycji Przywróć bazę danych](./media/backup-azure-sql-database/restore-db-button.png)

7. W obszarze **Konfiguracja przywracania**określ miejsce przywracania danych:
   - **Lokalizacja alternatywna**: Przywróć bazę danych do alternatywnej lokalizacji i Zachowaj oryginalną źródłową bazę danych.
   - **Zastąp bazę danych**: przywróć dane do tego samego wystąpienia programu SQL Server co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych.

     > [!Important]
     > Jeśli wybrana baza danych należy do grupy dostępności Always On, SQL Server nie zezwala na zastąpienie bazy danych. Dostępna jest tylko **Lokalizacja alternatywna** .
     >

     ![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. W menu **Przywróć konfigurację** w obszarze **gdzie należy przywrócić**wybierz pozycję **Lokalizacja alternatywna**.
2. Wybierz nazwę SQL Server i wystąpienie, do którego chcesz przywrócić bazę danych.
3. W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.
4. Jeśli ma to zastosowanie, wybierz opcję Zastąp, **Jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu programu SQL**.
5. Kliknij przycisk **OK**.

    ![Podawanie wartości menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. W obszarze **Wybierz punkt przywracania**wybierz, czy chcesz [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time) , czy [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie do punktu w czasie jest dostępne tylko dla kopii zapasowych dzienników dla baz danych, które są w trybie odzyskiwania pełnym i zbiorczym.

### <a name="restore-and-overwrite"></a>Przywróć i Zastąp

1. W menu **Przywróć konfigurację** w obszarze **gdzie można przywrócić**wybierz pozycję Zastąp **bazę danych** > **OK**.

    ![Wybieranie pozycji Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. W obszarze **Wybierz punkt przywracania**wybierz pozycję **dzienniki (punkt w czasie)** , aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz opcję **pełna & różnicowa** , aby przywrócić do [określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie do punktu w czasie jest dostępne tylko dla kopii zapasowych dzienników dla baz danych, które są w trybie odzyskiwania pełnym i zbiorczym.

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli wybrano opcję **dzienniki (punkt w czasie)** jako typ przywracania, wykonaj następujące czynności:

1.  W obszarze **Przywróć datę/godzinę**Otwórz Kalendarz. W kalendarzu daty z punktami odzyskiwania są wyświetlane pogrubioną czcionką, a bieżąca data jest wyróżniona.
1. Wybierz datę mającą punkty odzyskiwania. Nie można wybrać dat, które nie mają punktów odzyskiwania.

    ![Otwórz Kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania w ciągłym zakresie.
1. Określ czas odzyskiwania na wykresie osi czasu lub wybierz godzinę. Następnie wybierz przycisk **OK**.

    ![Wybierz godzinę przywracania](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Jeśli po przywróceniu ma pozostać niedziałający baza danych w menu **Konfiguracja** zaawansowana, Włącz opcję **Przywróć z opcją NORECOVERY**.
1. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelową.
1. Kliknij przycisk **OK**.

    ![Menu Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
1. Śledź postęp przywracania w obszarze **powiadomień** lub śledź go, wybierając pozycję **Przywróć zadania** w menu Baza danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Przywróć do określonego punktu przywracania

Jeśli wybrano opcję **pełny & różnicowa** jako typ przywracania, wykonaj następujące czynności:

1. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK**, aby ukończyć procedurę punktu przywracania.

    ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Jeśli po przywróceniu ma pozostać niedziałający baza danych w menu **Konfiguracja** zaawansowana, Włącz opcję **Przywróć z opcją NORECOVERY**.
1. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelową.
1. Kliknij przycisk **OK**.

    ![Meny Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
1. Śledź postęp przywracania w obszarze **powiadomień** lub śledź go, wybierając pozycję **Przywróć zadania** w menu Baza danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Przywracanie baz danych o dużej liczbie plików

Jeśli całkowity rozmiar ciągu plików w bazie danych jest większy niż [określony limit](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Azure Backup przechowuje listę plików bazy danych w innym składniku Pit, w taki sposób, że nie będzie można ustawić docelowej ścieżki przywracania podczas operacji przywracania. Pliki zostaną przywrócone do domyślnej ścieżki SQL.

  ![Przywracanie bazy danych z dużym plikiem](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie i monitorowanie](manage-monitor-sql-database-backup.md) SQL Server baz danych, których kopię zapasową utworzono przy użyciu Azure Backup.
