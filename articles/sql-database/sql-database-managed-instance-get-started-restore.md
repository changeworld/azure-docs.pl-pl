---
title: Przywracanie kopii zapasowej do wystąpienia zarządzanego usługi Azure SQL Database | Microsoft Docs
description: Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database za pomocą programu SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6cad73c8b8fa6a2fa95a6ea0c1fdb5d4114ffd41
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180103"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database

W tym przewodniku Szybki start przedstawiono sposób przywracania kopii zapasowej bazy danych przechowywanej w usłudze Azure Blob Storage do wystąpienia zarządzanego przy użyciu pliku kopii zapasowej World Wide Importers — Standard. Ta metoda wymaga pewnych przestojów w działaniu. 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

Aby skorzystać z samouczka w zakresie używania usługi Azure Database Migration Service (DMS) do celów migracji, zobacz [Migracja wystąpień zarządzanych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md). Aby uzyskać informacje dotyczące różnych metod migracji, zobacz artykuł [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:
- Wykorzystuje początkowe zasoby utworzone w przewodniku Szybki start: [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Wymaga najnowszej wersji programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) na komputerze klienckim w środowisku lokalnym
- Wymaga połączenia z wystąpieniem zarządzanym przy użyciu programu SQL Server Management Studio. Zobacz te przewodniki Szybki Start dla opcji łączności:
  - [Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL Database z maszyny wirtualnej platformy Azure](sql-database-managed-instance-configure-vm.md)
  - [Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL Database ze środowiska lokalnego za pomocą połączenia punkt-lokacja](sql-database-managed-instance-configure-p2s.md).
- Korzysta z wstępnie skonfigurowanego konta usługi Azure Blob Storage zawierającego plik kopii zapasowej Wide World Importers — Standard (pobrany z https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak).

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania bazy danych programu SQL Server przy użyciu usługi Azure Blob Storage i sygnatury dostępu współdzielonego (SAS), zobacz artykuł [Kopia zapasowa programu SQL Server do adresu URL](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Przywracanie bazy danych Wide World Importers z pliku kopii zapasowej

W programie SSMS użyj następujących kroków, aby przywrócić bazę danych Wide World Importers do wystąpienia zarządzanego z pliku kopii zapasowej.

1. Otwórz program SQL Server Management Studio (SSMS) i połącz się z wystąpieniem zarządzanym.
2. W programie SSMS otwórz nowe okno zapytania.
3. Użyj poniższego skryptu w celu utworzenia poświadczenia w wystąpieniu zarządzanym przy użyciu wstępnie skonfigurowanego konta magazynu i klucza sygnatury dostępu współdzielonego.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![tworzenie poświadczenia](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > Zawsze usuwaj wiodące znaki **?** z wygenerowanego klucza sygnatury dostępu współdzielonego.
  
3. Użyj następującego skryptu, aby sprawdzić poprawność poświadczenia sygnatury dostępu współdzielonego i kopii zapasowej — podając adres URL kontenera z plikiem kopii zapasowej:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lista plików](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. Użyj następującego skryptu, aby przywrócić bazę danych Wide World Importers z pliku kopii zapasowej — podając adres URL kontenera z plikiem kopii zapasowej:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Przywracanie](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. Aby śledzić stan operacji przywracania, uruchom następujące zapytanie w nowej sesji zapytania:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. Po zakończeniu operacji przywracania wyświetl ją w Eksploratorze obiektów. 

## <a name="next-steps"></a>Następne kroki

- W celu rozwiązywania problemów dotyczących tworzenia kopii zapasowej do adresu URL, zobacz artykuł [SQL Server Backup to URL Best Practices and Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting) (Kopia zapasowa programu SQL Server do adresu URL — najlepsze rozwiązania i rozwiązywanie problemów).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Connect your applications to Managed Instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym).
- Aby wykonywać zapytania przy użyciu ulubionych narzędzi lub języków, zobacz [nawiązywanie połączeń i wykonywanie zapytań](sql-database-connect-query.md).
