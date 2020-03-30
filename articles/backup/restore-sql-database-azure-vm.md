---
title: Przywracanie baz danych programu SQL Server na maszynie Wirtualnej platformy Azure
description: W tym artykule opisano sposób przywracania baz danych programu SQL Server, które są uruchomione na maszynie Wirtualnej platformy Azure i które są archiwizowane za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252456"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Przywracanie baz danych programu SQL Server na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania bazy danych programu SQL Server uruchomionej na maszynie wirtualnej platformy Azure (VM), na mocy których usługa [Azure Backup](backup-overview.md) wykona kopię zapasową w magazynie usług odzyskiwania kopii zapasowych platformy Azure.

W tym artykule opisano sposób przywracania baz danych programu SQL Server. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Przywracanie do czasu lub punktu odzyskiwania

Usługa Azure Backup może przywrócić bazy danych programu SQL Server, które są uruchomione na maszynach wirtualnych platformy Azure w następujący sposób:

- Przywróć do określonej daty lub godziny (do drugiej) przy użyciu kopii zapasowych dziennika transakcji. Usługa Azure Backup automatycznie określa odpowiednią pełną różnicową kopię zapasową i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia na podstawie wybranego czasu.
- Przywróć określoną pełną lub różnicową kopię zapasową, aby przywrócić do określonego punktu odzyskiwania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przywróceniem bazy danych należy zwrócić uwagę na następujące kwestie:

- Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure.
- Serwer docelowy musi być zarejestrowany w tym samym magazynie co źródło.
- Aby przywrócić zaszyfrowaną bazę danych TDE do innego programu SQL Server, należy najpierw [przywrócić certyfikat na serwerze docelowym](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Przed przywróceniem "głównej" bazy danych uruchom wystąpienie programu SQL Server w trybie jednego użytkownika przy użyciu opcji uruchamiania **-m AzureWorkloadBackup**.
  - Wartość **dla -m** jest nazwą klienta.
  - Tylko określona nazwa klienta może otworzyć połączenie.
- Dla wszystkich baz danych systemu (model, wzorzec, msdb), zatrzymaj usługę SQL Server Agent przed wyzwoleniem przywracania.
- Zamknij wszystkie aplikacje, które mogą próbować nawiązać połączenie z dowolną z tych baz danych.
- Jeśli na serwerze jest uruchomionych wiele wystąpień, wszystkie wystąpienia powinny być uruchomione, w przeciwnym razie serwer nie pojawi się na liście serwerów docelowych, do których można przywrócić bazę danych.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Aby przywrócić, potrzebne są następujące uprawnienia:

- **Uprawnienia operatora kopii zapasowej** w przechowalni, w której wykonujesz przywracanie.
- **Współautor (zapis)** dostęp do źródłowej maszyny Wirtualnej, która jest kopia zapasowa.
- **Dostęp współautora (zapisu)** do docelowej maszyny Wirtualnej:
  - Jeśli przywracasz do tej samej maszyny Wirtualnej, jest to źródłowa maszyna wirtualna.
  - Jeśli przywracasz do lokalizacji alternatywnej, jest to nowa maszyna wirtualna docelowa.

Przywróć w następujący sposób:

1. Otwórz magazyn, w którym jest zarejestrowana maszyna wirtualna programu SQL Server.
2. Na pulpicie nawigacyjnym **przechowalni**w obszarze Użycie wybierz pozycję **Zapasy zapasowe**.
3. W **obszarze Elementy kopii zapasowej**w obszarze Typ **zarządzania kopiami zapasowymi**wybierz pozycję **SQL w usłudze Azure VM**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Wybierz bazę danych do przywrócenia.

    ![Wybieranie bazy danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Przejrzyj menu bazy danych. Zawiera informacje o kopii zapasowej bazy danych, w tym:

    - Najstarsze i najnowsze punkty przywracania.
    - Stan kopii zapasowej dziennika dla ostatnich 24 godzin dla baz danych, które są w trybie odzyskiwania w całości i rejestrowane zbiorczo i które są skonfigurowane dla kopii zapasowych dziennika transakcyjnego.

6. Wybierz pozycję **Przywróć**.

    ![Wybierz pozycję Przywróć](./media/backup-azure-sql-database/restore-db.png)

7. W **obszarze Konfiguraj przywracania**określ, gdzie (lub jak) ma przywrócić dane:
   - **Lokalizacja alternatywna:** Przywróć bazę danych do lokalizacji alternatywnej i zachowaj oryginalną źródłową bazę danych.
   - **Zastąp DB:** Przywróć dane do tego samego wystąpienia programu SQL Server, co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych.

    > [!IMPORTANT]
    > Jeśli wybrana baza danych należy do grupy Dostępności zawsze włączone, program SQL Server nie zezwala na zastępowanie bazy danych. Dostępna jest **tylko lokalizacja alternatywna.**
    >
   - **Przywróć jako pliki**: Zamiast przywracać jako bazę danych, przywróć pliki kopii zapasowej, które można odzyskać jako bazę danych później na dowolnym komputerze, na którym pliki są obecne przy użyciu programu SQL Server Management Studio.
     ![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. W menu **Przywróć konfigurację** w obszarze **Gdzie przywrócić**wybierz pozycję **Lokalizacja alternatywna**.
2. Wybierz nazwę i wystąpienie programu SQL Server, do którego chcesz przywrócić bazę danych.
3. W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.
4. Jeśli ma to zastosowanie, wybierz opcję **Zastąp, jeśli baza danych o tej samej nazwie już istnieje w wybranym wystąpieniu SQL**.
5. Kliknij przycisk **OK**.

    ![Podawanie wartości menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration.png)

6. W **obszarze Wybierz punkt przywracania**wybierz, czy przywrócić do określonego punktu w [czasie,](#restore-to-a-specific-point-in-time) czy [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie w czasie jest dostępne tylko dla kopii zapasowych dziennika dla baz danych, które są w trybie odzyskiwania w całości i rejestrowane zbiorczo.

### <a name="restore-and-overwrite"></a>Przywracanie i zastępowanie

1. W menu **Przywróć konfigurację** w obszarze **Gdzie przywrócić**wybierz pozycję **Zastąpełnij DB** > **OK**.

    ![Wybieranie pozycji Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. W **obszarze Wybierz punkt przywracania**wybierz pozycję **Dzienniki (punkt w czasie),** aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz **opcję Pełny & Różnicowy,** aby przywrócić do [określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie w czasie jest dostępne tylko dla kopii zapasowych dziennika dla baz danych, które są w trybie odzyskiwania w całości i rejestrowane zbiorczo.

### <a name="restore-as-files"></a>Przywracanie jako plików

Aby przywrócić dane kopii zapasowej jako pliki .bak zamiast bazy danych, wybierz pozycję **Przywróć jako pliki**. Gdy pliki są po cenach dumpingowych do określonej ścieżki, można zabrać te pliki do dowolnego komputera, gdzie chcesz przywrócić je jako bazy danych. Dzięki możliwości przenoszenia tych plików na dowolnym komputerze można teraz przywrócić dane w subskrypcjach i regionach.

1. W menu **Przywróć konfigurację** w obszarze **Gdzie przywrócić**wybierz pozycję **Przywróć jako pliki**.
2. Wybierz nazwę programu SQL Server, do której chcesz przywrócić pliki kopii zapasowej.
3. W **ścieżce miejsce docelowej na serwerze** wejściowej ścieżka folderu na serwerze wybrana w kroku 2. Jest to lokalizacja, w której usługa zrzuci wszystkie niezbędne pliki kopii zapasowej. Zazwyczaj ścieżka udziału sieciowego lub ścieżka zainstalowanego udziału plików platformy Azure, gdy jest określona jako ścieżka docelowa, umożliwia łatwiejszy dostęp do tych plików przez inne maszyny w tej samej sieci lub z tym samym udziałem plików platformy Azure zainstalowanym na nich.<BR>

    >Aby przywrócić pliki kopii zapasowej bazy danych na udziale plików azure zainstalowanych na zarejestrowanej maszynie wirtualnej, upewnij się, że nt authority\system ma dostęp do udziału plików. Można wykonać kroki podane poniżej, aby udzielić uprawnień do odczytu/zapisu do afs zamontowany na maszynie wirtualnej:
    >
    >- Uruchom, `PsExec -s cmd` aby wejść do powłoki NT AUTHORITY\SYSTEM
    >   - Wykonaj polecenie `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Weryfikowanie dostępu za pomocą`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Rozpoczynanie przywracania jako plików z `\\<storageacct>.file.core.windows.net\<filesharename>` magazynu kopii zapasowych do ścieżki<BR>
    Możesz pobrać Psexec za pośrednictwem<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Kliknij przycisk **OK**.

    ![Wybierz pozycję Przywróć jako pliki](./media/backup-azure-sql-database/restore-as-files.png)

5. Wybierz **punkt przywracania** odpowiadający, do którego zostaną przywrócone wszystkie dostępne pliki .bak.

    ![Wybieranie punktu przywracania](./media/backup-azure-sql-database/restore-point.png)

6. Wszystkie pliki kopii zapasowej skojarzone z wybranym punktem odzyskiwania są po cenach dumpingowych do ścieżki docelowej. Można przywrócić pliki jako bazę danych na dowolnym komputerze, na których są obecne przy użyciu programu SQL Server Management Studio.

    ![Przywrócone pliki kopii zapasowej w ścieżce docelowej](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli jako typ przywracania **wybrano dzienniki (punkt w czasie),** wykonaj następujące czynności:

1. W obszarze **Przywróć datę/godzinę**otwórz kalendarz. W kalendarzu daty z punktami odzyskiwania są wyświetlane pogrubioną czcionką, a bieżąca data jest wyróżniona.
1. Wybierz datę z punktami odzyskiwania. Nie można wybrać dat, które nie mają punktów odzyskiwania.

    ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania w ciągłym zakresie.
1. Określ czas odzyskiwania na wykresie osi czasu lub wybierz godzinę. Następnie wybierz przycisk **OK**.

    ![Wybieranie czasu przywracania](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. W menu **Konfiguracja zaawansowana,** jeśli chcesz zachować bazę danych bez operacji po przywróceniu, włącz **przywracanie za pomocą norecovery**.
1. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelową.
1. Kliknij przycisk **OK**.

    ![Menu Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
1. Śledź postęp przywracania w obszarze **Powiadomienia** lub śledź go, wybierając **pozycję Przywróć zadania** w menu bazy danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Przywracanie do określonego punktu przywracania

Jeśli jako typ przywracania wybrano opcję **Pełny & różnicowy,** wykonaj następujące czynności:

1. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK**, aby ukończyć procedurę punktu przywracania.

    ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Domyślnie wyświetlane są punkty odzyskiwania z ostatnich 30 dni. Punkty odzyskiwania starsze niż 30 dni można wyświetlić, klikając **pozycję Filtruj** i wybierając niestandardowy zakres.

1. W menu **Konfiguracja zaawansowana,** jeśli chcesz zachować bazę danych bez operacji po przywróceniu, włącz **przywracanie za pomocą norecovery**.
1. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelową.
1. Kliknij przycisk **OK**.

    ![Meny Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
1. Śledź postęp przywracania w obszarze **Powiadomienia** lub śledź go, wybierając **pozycję Przywróć zadania** w menu bazy danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Przywracanie baz danych z dużą liczbą plików

Jeśli całkowity rozmiar ciągu plików w bazie danych jest większy niż [określony limit,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)usługa Azure Backup przechowuje listę plików bazy danych w innym składniku pit, w taki sposób, że nie będzie można ustawić docelowej ścieżki przywracania podczas operacji przywracania. Pliki zostaną przywrócone do domyślnej ścieżki SQL zamiast.

  ![Przywracanie bazy danych z dużym plikiem](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Następne kroki

[Zarządzanie i monitorowanie](manage-monitor-sql-database-backup.md) Bazy danych programu SQL Server, których kopię zapasową jest kopia zapasowa usługi Azure Backup.
