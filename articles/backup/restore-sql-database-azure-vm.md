---
title: Usługa Azure Backup umożliwia przywracanie bazy danych SQL Server kopii zapasowej na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przywracania bazy danych SQL Server, które są uruchomione na Maszynie wirtualnej platformy Azure i że kopie zapasowe są tworzone za pomocą usługi Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: 856f45f448aa843e9dc04ec3b6a60841cfe33227
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704861"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Przywracanie bazy danych SQL Server na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania bazy danych SQL Server, który działa na maszynie wirtualnej platformy Azure (VM), [kopia zapasowa Azure](backup-overview.md) usługi ma kopii zapasowej do magazynu usługi Azure Backup Recovery Services.

W tym artykule opisano sposób przywracania bazy danych programu SQL Server. Aby uzyskać więcej informacji, zobacz [Utwórz kopię zapasową bazy danych SQL Server na maszynach wirtualnych Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Przywracanie do czasu lub punktu odzyskiwania

Usługa Azure Backup można przywrócić bazy danych programu SQL Server, które są uruchomione na maszynach wirtualnych platformy Azure w następujący sposób:

- Przywróć do określonej daty lub czasu (sekundy) przy użyciu kopii zapasowej dziennika transakcji. Usługa Azure Backup automatycznie ustala odpowiedni pełnej różnicowej kopii zapasowej i łańcuch kopii zapasowych dzienników, które są wymagane do przywracania na podstawie wybranego czasu.
- Przywróć określonej pełna lub różnicowa kopii zapasowej do przywrócenia określony punkt odzyskiwania.


## <a name="prerequisites"></a>Wymagania wstępne

Przed przywróceniem bazy danych, pamiętaj o następujących kwestiach:

- Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure.
- Serwer docelowy musi być zarejestrowany w tym samym magazynie jako źródło.
- Aby przywrócić bazy danych zaszyfrowane przez funkcję TDE inny program SQL Server, trzeba wcześniej [przywrócić certyfikat na serwerze docelowym](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Przed przywróceniem bazy danych "master" Uruchom wystąpienie programu SQL Server w trybie jednego użytkownika przy użyciu opcji uruchamiania **-m AzureWorkloadBackup**.
    - Wartość **-m** jest nazwa klienta.
    - Tylko nazwę określonego klienta można otworzyć połączenia.
- Dla wszystkich systemowych baz danych (master, msdb modelu) należy zatrzymać usługi programu SQL Server Agent, zanim wyzwolenia przywracania.
- Zamknij wszelkie aplikacje, które może próbować wykonać połączenie do dowolnego z tych baz danych.
- W przypadku wielu wystąpieniach na serwerze, wszystkich wystąpień powinna być w górę i w przeciwnym razie uruchomienie serwera nie było wyświetlane na liście serwerów docelowych w celu przywrócenia bazy danych.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Aby przywrócić, potrzebne są następujące uprawnienia:

* **Wykonaj kopię zapasową Operator** uprawnienia w magazynie, na którym wykonujesz przywracania.
* **Współautor (zapis)** dostępu do źródła maszynę Wirtualną, która jest wykonywana kopia zapasowa.
* **Współautor (zapis)** dostęp do docelowej maszyny Wirtualnej:
    - W przypadku przywracania tej samej maszyny Wirtualnej, to źródłowej maszyny Wirtualnej.
    - Jeśli przywracania alternatywnej lokalizacji, jest to nowy obiekt docelowy maszyny Wirtualnej.

Przywróć w następujący sposób:
1. Otwórz magazyn, w którym jest zarejestrowana maszynę Wirtualną programu SQL Server.
2. Na pulpicie nawigacyjnym magazynu w ramach **użycia**, wybierz opcję **elementy kopii zapasowej**.
3. W **elementy kopii zapasowej**w obszarze **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Wybierz bazę danych do przywrócenia.

    ![Wybieranie bazy danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Zapoznaj się z menu bazy danych. Zawiera informacje na temat kopii zapasowej bazy danych, w tym:

    * Najstarsze i najnowsze punkty przywracania.
    * Stan kopii zapasowej dziennika, w ciągu ostatnich 24 godzin dla baz danych, które są w trybie odzyskiwania pełnego i z niepełnym dziennikiem, które są skonfigurowane dla kopii zapasowych dziennika transakcji.

6. Wybierz **Restore DB**.

    ![Wybieranie pozycji Przywróć bazę danych](./media/backup-azure-sql-database/restore-db-button.png)

7. W **przywracania konfiguracji**, określ lokalizację do przywracania danych:
   - **Lokalizacja alternatywna**: Przywróć bazę danych do lokalizacji alternatywnej i zachować oryginalne źródłowej bazy danych.
   - **Zastąp bazę danych**: przywróć dane do tego samego wystąpienia programu SQL Server co oryginalne źródło. Ta opcja spowoduje zastąpienie oryginalnej bazy danych.

     > [!Important]
     > Jeśli wybrana baza danych, należy do grupy dostępności Always On programu SQL Server nie zezwala na bazy danych zostaną zastąpione. Tylko **lokalizacji alternatywnej** jest dostępna.
     >

     ![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. W **przywracania konfiguracji** menu, w obszarze **gdzie chcesz przywrócić**, wybierz opcję **lokalizacji alternatywnej**.
2. Wybierz nazwę serwera SQL Server i wystąpienia, dla których chcesz wykonać przywrócenie bazy danych.
3. W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.
4. w razie potrzeby wybierz **zastąpić, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu SQL**.
5. Kliknij przycisk **OK**.

    ![Podawanie wartości menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. W **wybierz punkt przywracania**, wybierz opcję [Przywracanie do określonego punktu w czasie](#restore-to-a-specific-point-in-time) lub [Przywracanie do punktu odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie punktu w czasie jest dostępna tylko w przypadku kopii zapasowych dzienników dla baz danych, które znajdują się w trybie odzyskiwania pełnego i z niepełnym dziennikiem.

### <a name="restore-and-overwrite"></a>Przywracanie i zastępowania

1. W **przywracania konfiguracji** menu, w obszarze **gdzie chcesz przywrócić**, wybierz opcję **zastąpić DB** > **OK**.

    ![Wybieranie pozycji Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. W **wybierz punkt przywracania**, wybierz opcję **dzienniki (punkt w czasie)** do [Przywracanie do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz **pełna i różnicowa** do przywrócenia [określony punkt odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie punktu w czasie jest dostępna tylko w przypadku kopii zapasowych dzienników dla baz danych, które znajdują się w trybie odzyskiwania pełnego i z niepełnym dziennikiem.

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli wybrano **dzienniki (punkt w czasie)** jako typ przywracania, wykonaj następujące czynności:

1.  W obszarze **Data/Godzina przywracania**, otwórz kalendarz. W kalendarzu dat, które mają punkty odzyskiwania są wyświetlane pogrubioną czcionką, a bieżąca data jest wyróżniona.
1. Wybierz datę, która ma punkty odzyskiwania. Nie można wybrać daty, które mają żadne punkty odzyskiwania.

    ![Otwórz kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania w ciągłym zakresie.
1. Określ czas odzyskiwania na wykresie osi czasu, lub wybierz godzinę. Następnie wybierz przycisk **OK**.

    ![Wybierz czas przywracania](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Na **Advanced Configuration** menu, aby ochronić bazę danych niesprawny po przywróceniu, Włącz **Przywróć z opcją NORECOVERY**.
1. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelowego.
1. Kliknij przycisk **OK**.

    ![Menu Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
1. Śledź postęp przywracania w **powiadomienia** obszaru, lub śledzić go, wybierając **przywrócić zadania** menu bazy danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Przywracanie do punktu przywracania określonego

Jeśli wybrano **pełna i różnicowa** jako typ przywracania, wykonaj następujące czynności:

1. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK**, aby ukończyć procedurę punktu przywracania.

    ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Na **Advanced Configuration** menu, aby ochronić bazę danych niesprawny po przywróceniu, Włącz **Przywróć z opcją NORECOVERY**.
1. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelowego.
1. Kliknij przycisk **OK**.

    ![Meny Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
1. Śledź postęp przywracania w **powiadomienia** obszaru, lub śledzić go, wybierając **przywrócić zadania** menu bazy danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Przywracanie baz danych z dużą liczbą plików

Jeśli rozmiar ciągu łączna liczba plików w bazie danych jest większa niż [określonego limitu](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), kopia zapasowa Azure przechowuje listę plików bazy danych w składniku pit różnych w taki sposób, że nie można ustawić ścieżki docelowej przywracania podczas przywracania Operacja. Zamiast tego pliki zostaną przywrócone do ścieżki domyślnej SQL.

  ![Przywracanie bazy danych przy użyciu dużego pliku](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie i zarządzanie nim](manage-monitor-sql-database-backup.md) baz danych programu SQL Server, które tworzy kopię zapasową za pomocą usługi Azure Backup.
