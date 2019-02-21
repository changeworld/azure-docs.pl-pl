---
title: Przywracanie kopii zapasowej bazy danych programu SQL Server na Maszynie wirtualnej platformy Azure z usługą Azure Backup | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przywracania baz danych SQL Server uruchomiony na Maszynie wirtualnej platformy Azure kopie zapasowe są tworzone za pomocą usługi Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445444"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Przywracanie bazy danych SQL Server na maszynach wirtualnych platformy Azure 


W tym artykule opisano sposób przywracania bazy danych SQL Server uruchomiony na maszynie Wirtualnej platformy Azure, która została utworzona kopia zapasowa Magazyn usługi Azure Backup Recovery Services za pomocą [kopia zapasowa Azure](backup-overview.md) usługi.


> [!NOTE]
> Kopia zapasowa bazy danych SQl Server uruchomiony na Maszynie wirtualnej platformy Azure z usługą Azure Backup jest obecnie w publicznej wersji zapoznawczej.


W tym artykule opisano sposób przywracania bazy danych programu SQL Server. Jeśli nie skonfigurowano kopii zapasowej dla bazy danych, postępuj zgodnie z instrukcjami [w tym artykule](backup-azure-sql-database.md)



## <a name="about-restoring-databases"></a>Przywracanie baz danych — informacje

Usługa Azure Backup można przywrócić bazy danych programu SQL Server uruchomiony na maszynach wirtualnych platformy Azure w następujący sposób:

- Przywróć do określonej daty lub czasu (sekundy), przy użyciu kopii zapasowej dziennika transakcji. Usługa Azure Backup automatycznie ustala odpowiedni pełnej różnicowej kopii zapasowej i łańcuch kopii zapasowych dziennika, które są wymagane do przywracania na podstawie wybranego czasu.
- Przywróć określonej pełna lub różnicowa kopii zapasowej do przywrócenia określony punkt odzyskiwania, a nie w określonym czasie.


### <a name="prerequisites"></a>Wymagania wstępne

Przed przywróceniem bazy danych Pamiętaj o następujących kwestiach:

- Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure.
- Serwer docelowy musi być zarejestrowany w tym samym magazynie jako źródło.
- Aby przywrócić bazę danych zaszyfrowanych TDE inny program SQL Server, trzeba wcześniej [przywrócić certyfikat na serwerze docelowym](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Przed wyzwoleniem Przywracanie bazy danych "master" Uruchom wystąpienie programu SQL Server w trybie jednego użytkownika z opcją uruchamiania **-m AzureWorkloadBackup**.
    - Wartość **-m** jest nazwa klienta.
    - Tylko nazwę określonego klienta może otworzyć połączenia.
- W przypadku wszystkich systemowych baz danych (model, master, msdb) należy zatrzymać usługę agenta SQL przed wyzwoleniem przywracania.
- Zamknij wszystkie aplikacje, które mogą spróbować wykraść połączenie z dowolną z tych baz danych.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Aby przywrócić potrzebne są następujące uprawnienia:

* **Wykonaj kopię zapasową Operator** uprawnienia w magazynie która operacji przywracania.
* **Współautor (zapis)** dostępu do źródła maszynę Wirtualną, która jest wykonywana kopia zapasowa.
* **Współautor (zapis)** dostęp do docelowej maszyny Wirtualnej:
    - Jeśli są przywracane do tej samej maszyny Wirtualnej będzie źródłowej maszyny Wirtualnej.
    - Jeśli są przywracane do alternatywnej lokalizacji będzie nowy obiekt docelowy maszyny Wirtualnej. 

Przywróć w następujący sposób:
1. Otwórz magazyn, w którym jest zarejestrowana maszynę Wirtualną programu SQL Server.
2. Na pulpicie nawigacyjnym magazynu w ramach **użycia**, wybierz opcję **elementy kopii zapasowej**.

    ![Otwieranie menu Elementy kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. W **elementy kopii zapasowej**w obszarze **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Wybierz bazę danych do przywrócenia.

    ![Wybieranie bazy danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Zapoznaj się z menu bazy danych. Zawiera informacje na temat kopii zapasowej bazy danych, w tym: 

    * Najstarsze i najnowsze punkty przywracania.
    * Rejestrowanie stanu kopii zapasowej w ciągu ostatnich 24 godzin dla baz danych w trybie odzyskiwania pełnego i z niepełnym dziennikiem, jeśli skonfigurowane dla kopii zapasowych dziennika transakcji.

6. Kliknij przycisk **Restore DB**. 

    ![Wybieranie pozycji Przywróć bazę danych](./media/backup-azure-sql-database/restore-db-button.png)

7. W **przywracania konfiguracji**, określ, gdzie chcesz przywrócić dane do:
    - **Lokalizacja alternatywna**: przywróć bazę danych do lokalizacji alternatywnej i zachowaj oryginalną źródłową bazę danych.
    - **Zastąp bazę danych**: przywróć dane do tego samego wystąpienia programu SQL Server co oryginalne źródło. Ta opcja powoduje zastąpienie oryginalnej bazy danych.

    > [!Important]
    > Jeśli wybrana baza danych należy do zawsze włączonej grupy dostępności, program SQL Server nie zezwala na zastąpienie bazy danych. Tylko **lokalizacji alternatywnej** jest dostępna.
    >

    ![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. W **przywracania konfiguracji** menu, w obszarze **gdzie chcesz przywrócić**, wybierz opcję **lokalizacji alternatywnej**.
2. Wybierz nazwę serwera SQL Server i wystąpienia, dla których chcesz wykonać przywrócenie bazy danych.
3. W **przywrócić Nazwa bazy danych** wprowadź nazwę docelowej bazy danych.
4. w razie potrzeby wybierz **zastąpić, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu SQL**.
5. Kliknij przycisk **OK**.

    ![Podawanie wartości menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. W **wybierz punkt przywracania**, wybierz opcję [Przywracanie do określonego punktu w czasie](#restore-to-a-specific-point-in-time), lub przywracania [określony punkt odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie punktu w czasie jest dostępna tylko w przypadku kopii zapasowych dzienników dla baz danych w trybie odzyskiwania pełnego i z niepełnym dziennikiem. 


### <a name="restore-and-overwrite"></a>Przywracanie i zastępowania

1. W **przywracania konfiguracji** menu, w obszarze **gdzie chcesz przywrócić**, wybierz opcję **zastąpić DB** > **OK**.

    ![Wybieranie pozycji Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. W **wybierz punkt przywracania**, wybierz pozycję ** Dzienniki (punkt w czasie), aby [Przywracanie do określonego punktu w czasie](#restore-to-a-specific-point-in-time), lub **pełna i różnicowa** do przywrócenia [określonych punkt odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie do punktu w czasie jest dostępne tylko w przypadku kopii zapasowych dzienników dla baz danych z modelem odzyskiwania pełnego i z niepełnym dziennikiem. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli wybrano **dzienniki (punkt w czasie)** jako typ przywracania, wykonaj następujące czynności:

1.  W obszarze **Data/Godzina przywracania**, wybierz mini kalendarz. Na **kalendarza**pogrubione daty mają punkty odzyskiwania i zostanie wyróżniona bieżącą datę.
2. Wybierz datę z punktami odzyskiwania. Nie można wybrać dat bez punktów odzyskiwania.

    ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania w ciągłym zakresie.
4. Określ czas odzyskiwania, za pomocą wykres osi czasu, lub wybierz godzinę. Następnie kliknij przycisk **OK**.

    ![Otwieranie kalendarza](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. Na **Advanced Configuration** menu, aby ochronić bazę danych nie działa po przywróceniu, Włącz **Przywróć z opcją NORECOVERY**.
5. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelowego.
6. Kliknij przycisk **OK**.

    ![Menu Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
8. Śledź postęp przywracania w **powiadomienia** obszaru, lub wybierz **przywrócić zadania** menu bazy danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Przywracanie do punktu przywracania określonego

Jeśli wybrano **pełna i różnicowa** jako typ przywracania, wykonaj następujące czynności:


1. Wybierz punkt odzyskiwania z listy, a następnie kliknij przycisk **OK** do wykonania procedury punktu przywracania.

    ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. Na **Advanced Configuration** menu, aby ochronić bazę danych nie działa po przywróceniu, Włącz **Przywróć z opcją NORECOVERY**.
3. Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nową ścieżkę docelowego. 
4. Kliknij przycisk **OK**.

    ![Meny Konfiguracja zaawansowana](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.
8. Śledź postęp przywracania w **powiadomienia** obszaru, lub wybierz **przywrócić zadania** menu bazy danych.

    ![Postęp zadania przywracania](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie i zarządzanie nim](manage-monitor-sql-database-backup.md) kopie zapasowe baz danych programu SQL Server przez usługę Azure Backup.
