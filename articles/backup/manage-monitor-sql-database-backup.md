---
title: Zarządzanie i monitorowanie bazy danych SQL Server na maszynie Wirtualnej platformy Azure z kopii zapasowej przez usługę Azure Backup | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przywracania baz danych SQL Server uruchomiony na Maszynie wirtualnej platformy Azure kopie zapasowe są tworzone za pomocą usługi Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430801"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Zarządzanie i monitorowanie kopii zapasowej bazy danych programu SQL Server 


W tym artykule opisano typowe zadania dla zarządzania i monitorowania baz danych SQL Server uruchomiony na Maszynie wirtualnej platformy Azure, które tworzy kopię zapasową do usługi Azure Backup Recovery Services vault przez [kopia zapasowa Azure](backup-overview.md) usługi. Zadania, w tym monitorowania zadania i alerty, zatrzymywanie i wznowienie ochrony bazy danych, uruchomione zadania tworzenia kopii zapasowej i wyrejestrowywania Maszynę wirtualną z kopii zapasowej.


> [!NOTE]
> Kopia zapasowa bazy danych SQl Server uruchomiony na Maszynie wirtualnej platformy Azure z usługą Azure Backup jest obecnie w publicznej wersji zapoznawczej.


Nie zostały jeszcze skonfigurowane kopie zapasowe baz danych programu SQL Server, postępuj zgodnie z instrukcjami TIf [w tym artykule](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs"></a>Monitorowanie zadań kopii zapasowej

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>Monitorowanie zadań ad hoc w portalu

Usługa Azure Backup pokazuje wszystkie zadania wyzwalanych ręcznie w **zadania tworzenia kopii zapasowej** portalu, w tym odnajdywanie i rejestrowanie baz danych i operacje tworzenia kopii zapasowych i przywracania.

![Portal zadań kopii zapasowej](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Zaplanowanych zadań kopii zapasowej nie są wyświetlane w **zadania tworzenia kopii zapasowej** portalu. Do monitorowania zaplanowanych zadań kopii zapasowych należy użyć programu SQL Server Management Studio zgodnie z opisem w następnej sekcji.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Monitorowanie zadań tworzenia kopii zapasowych w programie SQL Server Management Studio 

Usługa Azure Backup używa natywnych interfejsów API SQL na potrzeby wszystkich operacji dotyczących kopii zapasowych.

Natywnych interfejsów API należy użyć do pobrania wszystkich informacji o zadaniu z [tabeli zestawu kopii zapasowych SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) w bazie danych msdb.

Poniższy przykład to zapytanie, które pobiera wszystkie zadania kopii zapasowej dla bazy danych o nazwie **DB1**. Dostosowywanie zapytania na potrzeby monitorowania zaawansowanego.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Ponieważ kopie zapasowe dziennika występuje co 15 minut, monitorowanie zadań tworzenia kopii zapasowej może być żmudne. Usługa Azure Backup ułatwia monitorowanie za pomocą wiadomości e-mail dla alertów.

- Alerty są wyzwalane dla wszystkich błędów kopii zapasowych.
- Alerty są konsolidowane na poziomie bazy danych według kodu błędu.
- Alerty e-mail są wysyłane tylko w przypadku pierwszego niepowodzenia tworzenia kopii zapasowej bazy danych. 

Aby monitorować alerty dotyczące kopii zapasowej:

1. Zaloguj się do subskrypcji platformy Azure w [witryny Azure portal](https://portal.azure.com) monitorować alerty bazy danych.

2. Na pulpicie nawigacyjnym magazynu wybierz **alerty i zdarzenia**.

   ![Wybieranie pozycji Alerty i zdarzenia](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. W **alerty i zdarzenia**, wybierz opcję **alerty kopii zapasowej**.

   ![Wybieranie pozycji Alerty kopii zapasowej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymywanie ochrony bazy danych programu SQL Server

Aby zatrzymać, tworzenie kopii zapasowej bazy danych programu SQL Server na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej i Usuń wszystkie punkty odzyskiwania.
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej, ale pozostawić bez zmian do punktów odzyskiwania.

Należy pamiętać, że:

Pozostawienie punktów odzyskiwania, punkty zostaną wyczyszczone zgodnie z zasadami tworzenia kopii zapasowej. Zostaną naliczone opłaty za chronione wystąpienie i użytego miejsca do magazynowania, dopóki wszystkie punkty odzyskiwania są czyszczone. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup/) o cenach.
- Gdy punkty odzyskiwania pozostanie bez zmian, mimo że wygasają zgodnie z zasadami przechowywania, kopia zapasowa Azure zawsze przechowuje jeden ostatniego punktu odzyskiwania aż jawnie usuniesz dane kopii zapasowej.
- Jeśli usuniesz źródła danych bez zatrzymywania tworzenia kopii zapasowych, nowych kopii zapasowych będzie kończyć się niepowodzeniem. Ponownie wygaśnie stare punkty odzyskiwania zgodnie z zasadami, ale jeden z ostatniego punktu odzyskiwania zawsze zostaną zachowane, dopóki Zatrzymaj kopię zapasową i Usuń dane.
- Nie można zatrzymać tworzenie kopii zapasowej dla bazy danych włączony do automatycznej ochrony, dopóki automatycznej ochrony jest wyłączone.

Aby zatrzymać ochronę bazy danych:

1. Na pulpicie nawigacyjnym magazynu w ramach **użycia**, wybierz opcję **elementy kopii zapasowej**.

    ![Otwieranie menu Elementy kopii zapasowej](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. W **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Wybierz bazę danych, dla którego chcesz zatrzymać ochronę.

    ![Wybieranie bazy danych w celu zatrzymania ochrony](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. W menu bazy danych wybierz **Zatrzymaj kopię zapasową**.

    ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-db-button.png)


6. W **Zatrzymaj tworzenie kopii zapasowej** menu, wybierz, czy chcesz zachować lub usunąć dane. Opcjonalnie podaj przyczynę i komentarz.

    ![Menu Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-backup-button.png)

7. Kliknij przycisk **Zatrzymaj kopię zapasową** .

  

### <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Jeśli opcja **Zachowaj dane kopii zapasowej** została wybrana po zatrzymaniu ochrony bazy danych SQL, można wznowić ochronę. Jeśli dane kopii zapasowej nie zostały zachowane, nie można wznowić ochrony.

1. Aby wznowić ochronę bazy danych SQL, otwórz element kopii zapasowej, a następnie wybierz pozycję **Wznów tworzenie kopii zapasowej**.

    ![Wybieranie pozycji Wznów tworzenie kopii zapasowej w celu wznowienia ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

2. W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie

Można uruchomić poszczególnych typów kopii zapasowych na żądanie:

* Pełna kopia zapasowa
* Pełna kopia zapasowa tylko do kopiowania
* Różnicowa kopia zapasowa
* Kopia zapasowa dziennika

[Dowiedz się więcej](backup-architecture.md#sql-server-backup-types) o programie SQL Server należy utworzyć kopię zapasową typów.

## <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Po po wyłączeniu ochrony, ale przed usunięciem magazynu, należy wyrejestrować wystąpienia programu SQL Server:

1. Na pulpicie nawigacyjnym magazynu w ramach **Zarządzaj**, wybierz opcję **infrastruktura zapasowa**.  

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. W obszarze **Serwery zarządzania** wybierz pozycję **Serwery chronione**.

   ![Wybieranie pozycji Serwery chronione](./media/backup-azure-sql-database/protected-servers.png)


3. W **serwerów chronionych**, wybierz serwer, które chcesz wyrejestrować. Aby usunąć magazyn, musisz wyrejestrować wszystkie serwery.

4. Kliknij prawym przyciskiem myszy serwer chroniony > **Usuń**.

   ![Wybieranie pozycji Usuń](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Kolejne kroki

[Przegląd](backup-sql-server-azure-troubleshoot.md) informacje dla kopii zapasowej bazy danych programu SQL Server dotyczące rozwiązywania problemów.
