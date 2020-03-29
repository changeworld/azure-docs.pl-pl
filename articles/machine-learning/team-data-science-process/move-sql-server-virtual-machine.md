---
title: Przenoszenie danych na maszynę wirtualną programu SQL Server — proces nauki o danych zespołowych
description: Przenoszenie danych z plików płaskich lub z lokalnego programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721374"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure

W tym artykule opisano opcje przenoszenia danych z plików płaskich (formaty CSV lub TSV) lub z lokalnego programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure. Te zadania do przenoszenia danych do chmury są częścią procesu nauki o danych zespołu.

W przypadku tematu, który przedstawia opcje przenoszenia danych do bazy danych SQL Azure dla uczenia maszynowego, zobacz [Przenoszenie danych do bazy danych SQL azure dla usługi Azure Machine Learning.](move-sql-azure.md)

W poniższej tabeli podsumowano opcje przenoszenia danych do programu SQL Server na maszynie wirtualnej platformy Azure.

| <b>Źródła</b> | <b>MIEJSCE DOCELOWE: PROGRAM SQL Server na maszynie Wirtualnej platformy Azure</b> |
| --- | --- |
| <b>Plik płaski</b> |1. <a href="#insert-tables-bcp">Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Zbiorcze wstawianie zapytania SQL</a><br> 3. Wbudowane narzędzia <a href="#sql-builtin-utilities">graficzne w programie SQL Server</a> |
| <b>Lokalny program SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Wdrażanie bazy danych programu SQL Server w kreatorze maszyny Wirtualnej platformy Microsoft Azure</a><br> 2. <a href="#export-flat-file">Eksport do płaskiego pliku</a><br> 3. <a href="#sql-migration">Kreator migracji bazy danych SQL</a> <br> 4. <a href="#sql-backup">Tworzenie kopii zapasowych i przywracanie bazy danych</a><br> |

W tym dokumencie przyjęto założenie, że polecenia SQL są wykonywane z programu SQL Server Management Studio lub Eksploratora baz danych programu Visual Studio.

> [!TIP]
> Alternatywnie można użyć [usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) do tworzenia i planowania potoku, który przeniesie dane do maszyny Wirtualnej programu SQL Server na platformie Azure. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych za pomocą usługi Azure Data Factory (działanie kopiowania).](../../data-factory/copy-activity-overview.md)
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Wymagania wstępne
W tym samouczku założono, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu platformy Azure**. Użyjesz konta magazynu platformy Azure do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz artykuł [Tworzenie konta magazynu.](../../storage/common/storage-account-create.md) Po utworzeniu konta magazynu należy uzyskać klucz konta używany do uzyskiwania dostępu do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Aprowerowuj **program SQL Server na maszynie wirtualnej platformy Azure**. Aby uzyskać instrukcje, zobacz [Konfigurowanie maszyny wirtualnej programu Azure SQL Server jako serwera notesu IPython do zaawansowanej analizy.](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)
* Zainstalowano i skonfigurowano **program Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a>Przenoszenie danych z płaskiego źródła plików do programu SQL Server na maszynie Wirtualnej platformy Azure
Jeśli dane są w pliku płaskim (rozmieszczone w formacie wiersza/kolumny), można je przenieść do maszyny Wirtualnej programu SQL Server na platformie Azure za pomocą następujących metod:

1. [Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)](#insert-tables-bcp)
2. [Zbiorcze wstawianie kwerendy SQL](#insert-tables-bulkquery)
3. [Wbudowane narzędzia graficzne w programie SQL Server (import/eksport, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)
BCP jest narzędziem wiersza polecenia zainstalowanym z PROGRAMEM SQL Server i jest jednym z najszybszych sposobów przenoszenia danych. Działa we wszystkich trzech wariantach programu SQL Server (lokalna maszyna wirtualna SQL Server, SQL Azure i SQL Server na platformie Azure).

> [!NOTE]
> **Gdzie powinny być moje dane dla BCP?**  
> Chociaż nie jest to wymagane, posiadanie plików zawierających dane źródłowe znajdujących się na tym samym komputerze co docelowy program SQL Server pozwala na szybsze transfery (szybkość sieci a szybkość we/wy dysku lokalnego). Można przenieść pliki płaskie zawierające dane na komputer, na którym jest zainstalowany program SQL Server, przy użyciu różnych narzędzi do kopiowania plików, takich jak [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](https://storageexplorer.com/) lub windows copy/paste za pośrednictwem protokołu RDP (Remote Desktop Protocol).
>
>

1. Upewnij się, że baza danych i tabele są tworzone w docelowej bazie danych programu SQL Server. Oto przykład, jak to zrobić `Create Database` za `Create Table` pomocą i poleceń:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Wygeneruj plik formatu opisujący schemat tabeli, wydając następujące polecenie z wiersza polecenia komputera, na którym jest zainstalowany bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Włóż dane do bazy danych za pomocą polecenia bcp, które powinno działać z wiersza polecenia, gdy program SQL Server jest zainstalowany na tym samym komputerze:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optymalizacja wstawień BCP** Aby zoptymalizować takie wstawki, zapoznaj się z poniższym artykułem ["Wytyczne dotyczące optymalizacji importu zbiorczego".](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx)
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Równoległe wstawienia dla szybszego przenoszenia danych
Jeśli przewuszujące dane są duże, można przyspieszyć, jednocześnie wykonując wiele poleceń BCP równolegle w skrypcie programu PowerShell.

> [!NOTE]
> **Duże zbiory danych Połknienia** Aby zoptymalizować ładowanie danych dla dużych i bardzo dużych zestawów danych, partycjonuj logiczne i fizyczne tabele baz danych przy użyciu wielu grup plików i tabel partycji. Aby uzyskać więcej informacji na temat tworzenia i ładowania danych do tabel partycji, zobacz [Równoległe ładowanie tabel partycji SQL](parallel-load-sql-partitioned-tables.md).
>
>

Poniższy przykładowy skrypt programu PowerShell demonstruje równoległe wstawia przy użyciu bcp:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Zbiorcze wstawianie kwerendy SQL
[Bulk Insert SQL Query](https://msdn.microsoft.com/library/ms188365) może służyć do importowania danych do bazy danych z plików opartych na wierszach/kolumnach (obsługiwane typy są opisane w temacie[Przygotowywanie danych do eksportu zbiorczego lub importu (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Oto kilka przykładowych poleceń dla wstawania zbiorczego są jak poniżej:  

1. Analizuj dane i ustaw opcje niestandardowe przed zaimportowaniem, aby upewnić się, że baza danych programu SQL Server przyjmuje ten sam format dla wszystkich specjalnych pól, takich jak daty. Oto przykład ustawiania formatu daty jako rok-miesiąc-dzień (jeśli dane zawierają datę w formacie rok-miesiąc-dzień):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importowanie danych przy użyciu zbiorczych instrukcji importu:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Wbudowane narzędzia w programie SQL Server
Usługi integracji programu SQL Server (SSIS) można użyć do zaimportowania danych do maszyny wirtualnej programu SQL Server na platformie Azure z pliku płaskiego.
SSIS jest dostępny w dwóch środowiskach studyjnych. Aby uzyskać szczegółowe informacje, zobacz [Integration Services (SSIS) i Studio Environments:](https://technet.microsoft.com/library/ms140028.aspx)

* Aby uzyskać szczegółowe informacje na temat narzędzi SQL Server Data Tools, zobacz [Narzędzia danych programu Microsoft SQL Server](https://msdn.microsoft.com/data/tools.aspx)  
* Aby uzyskać szczegółowe informacje na temat Kreatora importu/eksportu, zobacz [Kreator importu i eksportowania programu SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Przenoszenie danych z lokalnego programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure
Można również użyć następujących strategii migracji:

1. [Wdrażanie bazy danych programu SQL Server w kreatorze maszyn wirtualnych platformy Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Eksportowanie do pliku płaskiego](#export-flat-file)
3. [Kreator migracji bazy danych SQL](#sql-migration)
4. [Tworzenie kopii zapasowych i przywracanie bazy danych](#sql-backup)

Opisujemy każdą z poniższych opcji:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Wdrażanie bazy danych programu SQL Server w kreatorze maszyn wirtualnych platformy Microsoft Azure
**Wdrażanie bazy danych programu SQL Server w kreatorze maszyn wirtualnych platformy Microsoft Azure** to prosty i zalecany sposób przenoszenia danych z lokalnego wystąpienia programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure. Aby uzyskać szczegółowe kroki, a także omówienie innych alternatyw, zobacz [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure.](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Eksportowanie do pliku płaskiego
Różne metody mogą służyć do zbiorczego eksportowania danych z lokalnego programu SQL Server zgodnie z dokumentami w temacie [Importowanie zbiorcze i eksportowanie danych (SQL Server).](https://msdn.microsoft.com/library/ms175937.aspx) Ten dokument będzie obejmować program kopiowania zbiorczego (BCP) jako przykład. Po wyeksportowaniu danych do pliku płaskiego można je zaimportować do innego serwera SQL przy użyciu importu zbiorczego.

1. Eksportowanie danych z lokalnego programu SQL Server do pliku przy użyciu narzędzia bcp w następujący sposób

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Utwórz bazę danych i tabelę na `create database` maszynie Wirtualnej programu SQL Server na platformie Azure przy użyciu i `create table` dla schematu tabeli wyeksportowanych w kroku 1.
3. Utwórz plik formatu opisujący schemat tabeli eksportowanych/importowanych danych. Szczegóły pliku formatu są opisane w [polu Utwórz plik formatu (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatowanie generowania plików podczas uruchamiania protokołu BCP z komputera programu SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formatowanie generowania plików podczas zdalnego uruchamiania protokołu BCP względem programu SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Użyj dowolnej z metod opisanych w sekcji [Przenoszenie danych ze źródła plików,](#filesource_to_sqlonazurevm) aby przenieść dane w plikach płaskich do programu SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Kreator migracji bazy danych SQL
[Kreator migracji bazy danych programu SQL Server](https://sqlazuremw.codeplex.com/) zapewnia przyjazny dla użytkownika sposób przenoszenia danych między dwoma wystąpieniami serwera SQL. Umożliwia użytkownikowi mapowanie schematu danych między źródłami i tabelami docelowymi, wybieranie typów kolumn i różnych innych funkcji. Używa kopii zbiorczej (BCP) pod przykryciem. Poniżej przedstawiono zrzut ekranu powitalnego kreatora migracji bazy danych SQL.  

![Kreator migracji programu SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Tworzenie kopii zapasowych i przywracanie bazy danych
Program SQL Server obsługuje:

1. [Tworzenie kopii zapasowych i przywracanie bazy danych (zarówno](https://msdn.microsoft.com/library/ms187048.aspx) do pliku lokalnego, jak i eksportu bacpac do obiektu blob) i [aplikacji warstwy danych](https://msdn.microsoft.com/library/ee210546.aspx) (przy użyciu bacpac).
2. Możliwość bezpośredniego tworzenia maszyn wirtualnych programu SQL Server na platformie Azure za pomocą skopiowanej bazy danych lub kopiowania do istniejącej bazy danych sql azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z Kreatora kopiowania bazy danych](https://msdn.microsoft.com/library/ms188664.aspx).

Poniżej przedstawiono zrzut ekranu przedstawiający opcje kopii zapasowej/przywracania bazy danych z programu SQL Server Management Studio.

![Narzędzie importowania programu SQL Server][1]

## <a name="resources"></a>Resources
[Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Omówienie programu SQL Server w usłudze Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
