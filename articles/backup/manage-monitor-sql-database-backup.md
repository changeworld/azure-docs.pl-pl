---
title: Monitorowanie baz danych programu SQL Server na maszynie Wirtualnej platformy Azure, którego kopia zapasowa jest tworzona kopia zapasowa Azure i zarządzanie nim | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób przywracania bazy danych SQL Server, są uruchamiane na Maszynie wirtualnej platformy Azure, które są uwzględnione w przez usługę Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: da4264047830b21b3ac4dae723dd1fd2f9d7a8f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432859"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Zarządzanie i monitorowanie kopii zapasowej bazy danych programu SQL Server 


W tym artykule opisano typowe zadania dla zarządzania i monitorowania baz danych serwera SQL są uruchamiane na maszynie wirtualnej platformy Azure (VM) i który tworzy kopię zapasową do usługi Azure Backup Recovery Services vault przez [kopia zapasowa Azure](backup-overview.md) usługi. Dowiesz się, jak monitorować zadania i alerty, Zatrzymaj i wznowić ochronę bazy danych, uruchamianie zadań tworzenia kopii zapasowej i wyrejestruj Maszynę wirtualną z kopii zapasowych.


> [!NOTE]
> Tworzenie kopii zapasowych baz danych SQL Server, które są uruchomione na Maszynie wirtualnej platformy Azure za pomocą usługi Azure Backup jest obecnie w publicznej wersji zapoznawczej.


Jeśli jeszcze nie skonfigurowano tworzenia kopii zapasowych baz danych programu SQL Server, zobacz [Utwórz kopię zapasową bazy danych SQL Server na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md)

##  <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorowanie ręcznych zadań tworzenia kopii zapasowej w portalu

Usługa Azure Backup pokazuje wszystkie zadania wyzwalanych ręcznie w **zadania tworzenia kopii zapasowej** portalu. Zadania widoczne w tym portalu dołączania bazy danych odnajdywania i rejestrowania i kopii zapasowej i przywracanie operacji.

![Portal zadania kopii zapasowej](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **Zadania tworzenia kopii zapasowej** portal nie wyświetla zaplanowanych zadań kopii zapasowej. Do monitorowania zaplanowanych zadań kopii zapasowych należy użyć programu SQL Server Management Studio zgodnie z opisem w następnej sekcji.
>

## <a name="monitor-scheduled-backup-jobs-in-sql-server-management-studio"></a>Monitorowanie zaplanowanych zadań kopii zapasowej w programie SQL Server Management Studio 

Usługa Azure Backup używa natywnych interfejsów API SQL na potrzeby wszystkich operacji dotyczących kopii zapasowych. Natywnych interfejsów API należy użyć do pobrania wszystkich informacji o zadaniu z [tabeli zestawu kopii zapasowych SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) w bazie danych msdb.

Poniższy przykład to zapytanie, które pobiera wszystkie zadania tworzenia kopii zapasowej bazy danych, który nosi nazwę **DB1**. Dostosowywanie zapytania na potrzeby monitorowania zaawansowanego.

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

Ponieważ kopie zapasowe dziennika występuje co 15 minut, monitorowanie zadań tworzenia kopii zapasowej może być żmudne. Usługa Azure Backup ułatwia monitorowania przez wysłanie wiadomości e-mail dla alertów. Alerty poczty e-mail są:

- Wywołany przez wszystkie błędy tworzenia kopii zapasowej.
- Skonsolidowane na poziomie bazy danych przez kod błędu.
- Wysyłane tylko w przypadku pierwszego niepowodzenia wykonywania kopii zapasowej bazy danych. 

Aby monitorować alerty kopii zapasowej bazy danych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na pulpicie nawigacyjnym magazynu wybierz **alerty i zdarzenia**.

   ![Wybieranie pozycji Alerty i zdarzenia](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

1. W **alerty i zdarzenia**, wybierz opcję **alerty kopii zapasowej**.

   ![Wybieranie pozycji Alerty kopii zapasowej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymywanie ochrony bazy danych programu SQL Server

Aby zatrzymać, tworzenie kopii zapasowej bazy danych programu SQL Server na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej i Usuń wszystkie punkty odzyskiwania.
* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i punktów odzyskiwania należy pozostawić bez zmian.

Jeśli zdecydujesz się pozostawienie punktów odzyskiwania, pamiętać o tych szczegółów:

* Zostaną wyczyszczone wszystkie punkty odzyskiwania, które pozostaną zgodnie z zasadami tworzenia kopii zapasowej. 
* Dopóki wszystkie punkty odzyskiwania są czyszczone, Opłata zostanie naliczona za chronione wystąpienie i użytego miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Kopia zapasowa Azure zawsze przechowuje jeden ostatniego punktu odzyskiwania, dopóki nie usuniesz dane kopii zapasowej. 
* Jeśli usuniesz źródła danych bez konieczności zatrzymywania tworzenia kopii zapasowych nowych kopii zapasowych zakończy się niepowodzeniem. 
* Jeśli baza danych jest włączone dla autoprotection, nie można zatrzymać kopie zapasowe, o ile nie wyłączysz autoprotection.

Aby zatrzymać ochronę bazy danych:

1. Na pulpicie nawigacyjnym magazynu w ramach **użycia**, wybierz opcję **elementy kopii zapasowej**.

1. W obszarze **typ zarządzania kopiami zapasowymi**, wybierz opcję **SQL na maszynie Wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)


1. Wybierz bazę danych, dla którego chcesz zatrzymać ochronę.

    ![Wybieranie bazy danych w celu zatrzymania ochrony](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


1. W menu bazy danych wybierz **Zatrzymaj kopię zapasową**.

    ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-db-button.png)


1. Na **Zatrzymaj tworzenie kopii zapasowej** menu, wybierz, czy chcesz zachować lub usunąć dane. Jeśli chcesz, podaj przyczynę i komentarz.

    ![Zachować lub usunąć dane w menu Zatrzymaj kopię zapasową](./media/backup-azure-sql-database/stop-backup-button.png)

1. Wybierz **Zatrzymaj kopię zapasową**.

  

## <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Po zatrzymaniu ochrony usługi SQL database, jeśli zostanie wybrana **Zachowaj dane kopii zapasowej** opcji później można wznowić ochronę. Jeśli nie Zachowaj dane kopii zapasowej, nie można wznowić ochronę.

Aby wznowić ochronę bazy danych SQL:

1. Otwórz element kopii zapasowej i wybrać **Wznów tworzenie kopii zapasowej**.

    ![Wybieranie pozycji Wznów tworzenie kopii zapasowej w celu wznowienia ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

2. W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

## <a name="run-an-on-demand-backup"></a>Uruchomienie w kopii zapasowej na żądanie

Można uruchomić poszczególnych typów kopii zapasowych na żądanie:

* Pełna kopia zapasowa
* Pełna kopia zapasowa tylko do kopiowania
* Różnicowa kopia zapasowa
* Kopia zapasowa dziennika

Aby uzyskać więcej informacji, zobacz [typy kopii zapasowych programu SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Po wyłączeniu ochrony, ale przed usunięciem magazynu, należy wyrejestrować wystąpienia programu SQL Server:

1. Na pulpicie nawigacyjnym magazynu w ramach **Zarządzaj**, wybierz opcję **infrastruktura zapasowa**.  

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. W obszarze **Serwery zarządzania** wybierz pozycję **Serwery chronione**.

   ![Wybieranie pozycji Serwery chronione](./media/backup-azure-sql-database/protected-servers.png)


3. W **serwerów chronionych**, wybierz serwer, które chcesz wyrejestrować. Aby usunąć magazyn, musisz wyrejestrować wszystkie serwery.

4. Kliknij prawym przyciskiem myszy serwer chroniony, a następnie wybierz pozycję **Usuń**.

   ![Wybieranie pozycji Usuń](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z kopiami zapasowymi w bazie danych programu SQL Server](backup-sql-server-azure-troubleshoot.md).
