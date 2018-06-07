---
title: Przywracanie kopii zapasowej do wystąpienia zarządzanego usługi Azure SQL Database | Microsoft Docs
description: Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database za pomocą programu SSMS.
keywords: samouczek usługi sql database, wystąpienie zarządzane usługi sql database, przywracanie kopii zapasowych
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "31426206"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database

W tym samouczku przedstawiono sposób przywracania kopii zapasowej bazy danych przechowywanej w usłudze Azure Blob Storage do wystąpienia zarządzanego przy użyciu pliku kopii zapasowej World Wide Importers — Standard. Ta metoda wymaga pewnych przestojów w działaniu. Aby skorzystać z samouczka w zakresie używania usługi Azure Database Migration Service (DMS) do celów migracji, zobacz [Migracja wystąpień zarządzanych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md). Aby uzyskać informacje dotyczące różnych metod migracji, zobacz [SQL Server instance migration to Azure SQL Database Managed Instance (Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database)](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Pobieranie pliku kopii zapasowej Wide World Importers — Standard
> * Tworzenie konta magazynu platformy Azure oraz przekazywanie pliku kopii zapasowej
> * Przywracanie bazy danych Wide World Importers z pliku kopii zapasowej

## <a name="prerequisites"></a>Wymagania wstępne

Jako punkt początkowy ten samouczek używa zasobów utworzonych w tym samouczku: [Create an Azure SQL Database Managed Instance (Tworzenie wystąpienia zarządzanego usługi Azure SQL Database)](sql-database-managed-instance-create-tutorial-portal.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Pobieranie pliku kopii zapasowej Wide World Importers — Standard

Wykonaj następujące kroki, aby pobrać plik kopii zapasowej Wide World Importers — Standard.

W programie Internet Explorer wprowadź ciąg https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak w polu adresu URL, a następnie po wyświetleniu monitu kliknij pozycję **Zapisz**, aby zapisać ten plik w folderze **Pobrane**.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Tworzenie konta magazynu platformy Azure oraz przekazywanie pliku kopii zapasowej

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj **Magazyn** i kliknij opcję **Konto magazynu**, aby otworzyć formularz konta magazynu.

   ![konto magazynu](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Wypełnij formularz konta magazynu, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Model wdrażania**|Model zasobów||
   |**Rodzaj konta**|Blob Storage||
   |**Wydajność**|Standardowa lub Premium|Dyski magnetyczne lub dyski SSD|
   |**Replikacja**|Magazyn lokalnie nadmiarowy||
   |\*\*Warstwa dostępu (domyślna)|Chłodna lub gorąca||
   |**Wymagany bezpieczny transfer**|Disabled (Wyłączony)||
   |**Subskrypcja**|Twoja subskrypcja|Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions).|
   |**Grupa zasobów**|Grupa zasobów, która została utworzona wcześniej|| 
   |**Lokalizacja**|Lokalizacja, która została wybrana wcześniej||
   |**Sieci wirtualne**|Disabled (Wyłączony)||

4. Kliknij przycisk **Utwórz**.

   ![szczegóły konta magazynu](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Po pomyślnym wdrożeniu konta magazynu otwórz nowe konto magazynu.
6. W obszarze **Ustawienia** kliknij opcję **Sygnatura dostępu współdzielonego**, aby otworzyć formularz sygnatury dostępu współdzielonego.

   ![formularz sygnatury dostępu współdzielonego](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. W formularzu sygnatury dostępu współdzielonego zmodyfikuj wartości domyślne wedle potrzeb. Należy pamiętać, że data/godzina wygaśnięcia wynosi domyślnie zaledwie 8 godzin.
8. Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.

   ![wypełniony formularz sygnatury dostępu współdzielonego](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Skopiuj i zapisz **token sygnatury dostępu współdzielonego** oraz **adres URL sygnatury dostępu współdzielonego serwera obiektów blob**.
10. W obszarze **Ustawienia** kliknij przycisk **Kontenery**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Kliknij przycisk **+ Kontener**, aby utworzyć kontener do przechowywania pliku kopii zapasowej.
12. Wypełnij formularz kontenera, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

    | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Poziom dostępu publicznego**|Prywatny (bez dostępu anonimowego)||

    ![szczegóły kontenera](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Kliknij przycisk **OK**.
14. Po utworzeniu kontenera otwórz go.

    ![kontener](./media/sql-database-managed-instance-tutorial/container.png)

15. Kliknij opcję **Właściwości kontenera**, a następnie skopiuj adres URL do kontenera.

    ![adres URL kontenera](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Kliknij pozycję **Przekaż**, aby otworzyć formularz **Przekazywanie obiektu blob**.

    ![przekazywanie](./media/sql-database-managed-instance-tutorial/upload.png)

17. Przejdź do folderu pobierania i wybierz plik **WideWorldIimporters-Standard.bak**.

    ![przekazywanie](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Kliknij pozycję **Przekaż**.
19. Nie kontynuuj pracy, dopóki przekazywanie nie zostanie zakończone.

    ![przekazywanie zakończone](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Przywracanie bazy danych Wide World Importers z pliku kopii zapasowej

W programie SSMS użyj następujących kroków, aby przywrócić bazę danych Wide World Importers do wystąpienia zarządzanego z pliku kopii zapasowej.

1. W programie SSMS otwórz nowe okno zapytania.
2. Użyj następującego skryptu, aby utworzyć poświadczenia sygnatury dostępu współdzielonego — podając adres URL kontenera konta magazynu oraz klucz sygnatury dostępu współdzielonego zgodnie z poleceniem.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![poświadczenia](./media/sql-database-managed-instance-tutorial/credential.png)

3. Użyj następującego skryptu, aby sprawdzić poprawność poświadczenia sygnatury dostępu współdzielonego i kopii zapasowej — podając adres URL kontenera z plikiem kopii zapasowej:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![lista plików](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Użyj następującego skryptu, aby przywrócić bazę danych Adventure Works 2012 z pliku kopii zapasowej — podając adres URL kontenera z plikiem kopii zapasowej:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![wykonywanie przywrócenia](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Aby śledzić stan operacji przywracania, uruchom następujące zapytanie w nowej sesji zapytania:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![procent wykonania przywracania](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Po zakończeniu operacji przywracania wyświetl ją w Eksploratorze obiektów. 

    ![ukończenie przywracania](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Następne kroki

Ten samouczek umożliwił zapoznanie się ze sposobem przywracania kopii zapasowej bazy danych przechowywanej w usłudze Azure Blob Storage do wystąpienia zarządzanego przy użyciu pliku kopii zapasowej World Wide Importers — Standard. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Pobieranie pliku kopii zapasowej Wide World Importers — Standard
> * Tworzenie konta magazynu platformy Azure oraz przekazywanie pliku kopii zapasowej
> * Przywracanie bazy danych Wide World Importers z pliku kopii zapasowej

Przejdź do następnego samouczka, aby dowiedzieć się, jak migrować program SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi DMS.

> [!div class="nextstepaction"]
>[Migracja programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md)