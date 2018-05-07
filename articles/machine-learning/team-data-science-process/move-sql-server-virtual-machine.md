---
title: Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Przenoszenie danych z plików prostych lub z lokalnego programu SQL Server do programu SQL Server na maszynie Wirtualnej Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 56a03347556f9ae3452548e85ce5d46f3961ed93
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure
W tym temacie opisano opcje przenoszenia danych z plików prostych (w formatach CSV lub TSV) lub z lokalnego programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure. Te zadania przenoszenie danych w chmurze są częścią procesu nauki danych zespołu.

Aby temacie, który zawiera opcje przenoszenia danych do bazy danych SQL Azure dla uczenia maszynowego, zobacz [przenieść dane do bazy danych SQL Azure dla usługi Azure Machine Learning](move-sql-azure.md).

**Menu** poniżej łącza do tematów opisujących sposób pozyskiwania danych w innych środowiskach docelowych, gdzie dane mogą być przechowywane i przetwarzane podczas procesu nauki danych zespołu (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

W poniższej tabeli przedstawiono opcje przenoszenia danych do programu SQL Server na maszynie wirtualnej platformy Azure.

| <b>ŹRÓDŁO</b> | <b>Miejsce docelowe: SQL Server na maszynie Wirtualnej platformy Azure</b> |
| --- | --- |
| <b>Plik prosty</b> |1. <a href="#insert-tables-bcp">Narzędzie Kopia wiersza polecenia zbiorczego (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Zapytanie SQL wstawiania zbiorczego </a><br> 3. <a href="#sql-builtin-utilities">Graficznych narzędzi wbudowanych w programie SQL Server</a> |
| <b>Lokalny serwer SQL</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Wdrażanie bazy danych programu SQL Server do kreatora maszyny Wirtualnej programu Microsoft Azure</a><br> 2. <a href="#export-flat-file">Wyeksportuj do pliku prostego </a><br> 3. <a href="#sql-migration">Kreator migracji bazy danych SQL </a> <br> 4. <a href="#sql-backup">Baza danych kopii zapasowej i przywracanie </a><br> |

Pamiętaj, że w tym dokumencie przyjęto założenie, że polecenia SQL są wykonywane z programu SQL Server Management Studio lub Visual Studio Explorer bazy danych.

> [!TIP]
> Alternatywnie, można użyć [fabryki danych Azure](https://azure.microsoft.com/services/data-factory/) do tworzenia i planowania potok, który będzie przenoszenie danych do maszyny Wirtualnej z programu SQL Server na platformie Azure. Aby uzyskać więcej informacji, zobacz [skopiować dane z fabryką danych Azure (działanie kopiowania)](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Wymagania wstępne
Ten samouczek zakłada, że masz:

* **Subskrypcji platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu Azure**. Korzystasz z konta magazynu Azure do przechowywania danych w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artykułu. Po utworzeniu konta magazynu, należy uzyskać klucz konto umożliwia dostęp do magazynu. Zobacz [zarządzanie kluczami dostępu do magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Zainicjowano obsługę administracyjną **programu SQL Server na maszynie Wirtualnej platformy Azure**. Aby uzyskać instrukcje, zobacz [Konfigurowanie maszyny wirtualnej Azure SQL Server jako serwer notesu IPython zaawansowana analityka](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Zainstalowano i skonfigurowano **programu Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Przenoszenie danych ze źródła pliku prostego do programu SQL Server na maszynie Wirtualnej platformy Azure
Jeśli dane pliku prostego (ułożone w formacie wierszy i kolumn), można ją przenosić do maszyny Wirtualnej programu SQL Server na platformie Azure za pomocą następujących metod:

1. [Narzędzie Kopia wiersza polecenia zbiorczego (BCP)](#insert-tables-bcp)
2. [Zapytanie SQL wstawiania zbiorczego ](#insert-tables-bulkquery)
3. [Graficznych narzędzi wbudowanych w programie SQL Server (importu/eksportu, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Narzędzie Kopia wiersza polecenia zbiorczego (BCP)
Narzędzie BCP to narzędzie wiersza polecenia zainstalowane z programem SQL Server i jest najszybszym sposoby przenoszenia danych. Działa ona za pośrednictwem wszystkich trzech wariantów programu SQL Server (lokalnego programu SQL Server, SQL Azure i maszyna wirtualna serwera SQL na platformie Azure).

> [!NOTE]
> **Gdzie powinien mieć BCP danych?**  
> Mimo że nie jest wymagane, o pliki zawierające źródło danych znajdujących się na tym samym komputerze co docelowego programu SQL Server umożliwia szybsze transferu (sieci szybkości vs dysku lokalnym szybkości operacji We/Wy). Można przenieść plików prostych zawierający dane do komputera w przypadku, gdy jest zainstalowany program SQL Server przy użyciu różnych kopiowanie plików narzędzi takich jak [AZCopy](../../storage/common/storage-use-azcopy.md), [Eksploratora usługi Storage Azure](http://storageexplorer.com/) lub windows kopiowania/wklejania za pośrednictwem pulpitu zdalnego Protocol (RDP).
>
>

1. Upewnij się, że baza danych i tabele są tworzone na docelowej bazy danych programu SQL Server. Poniżej przedstawiono przykład sposobu wykonania tego za pomocą `Create Database` i `Create Table` polecenia:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Generowanie pliku formatu, który opisuje schematu dla tabeli, wydając polecenie w wierszu polecenia na komputerze zainstalowanym bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Wstawianie danych do bazy danych przy użyciu polecenia bcp w następujący sposób. Powinno to działać z wiersza polecenia, przy założeniu, że serwer SQL jest zainstalowany na tym samym komputerze:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optymalizacja wstawia BCP** można znaleźć w artykule ["Wytyczne dotyczące importowania zbiorczego optymalizacji"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) w celu zoptymalizowania takich operacji wstawienia.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Parallelizing wstawia dla szybsze przepływu danych
Jeśli dane, które chcesz przenieść jest duży, można przyspieszyć rzeczy, wykonując jednocześnie wiele polecenia BCP równolegle w skrypcie programu PowerShell.

> [!NOTE]
> **Wprowadzanie danych big data** w celu zoptymalizowania danych ładowania dla dużych i bardzo dużych zestawów danych, partycji tabel logicznej i fizycznej bazy danych przy użyciu wielu grup plików i partycji tabeli. Aby uzyskać więcej informacji na temat tworzenia i ładowanie danych do tabel partycji, zobacz [równoległych tabel partycji SQL obciążenia](parallel-load-sql-partitioned-tables.md).
>
>

Poniższy przykładowy skrypt programu PowerShell pokazują równoległych wstawia przy użyciu narzędzia bcp:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
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


### <a name="insert-tables-bulkquery"></a>Zapytanie SQL wstawiania zbiorczego
[Zbiorcze Wstawianie zapytania SQL](https://msdn.microsoft.com/library/ms188365) może służyć do importowania danych do bazy danych z plików na podstawie wiersza/kolumny (obsługiwane typy są objęte[przygotować dane zbiorcze eksportu lub importu (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tematu.

Poniżej przedstawiono niektóre przykładowe polecenia dla Bulk Insert czy, jak pokazano poniżej:  

1. Analizowanie danych i niestandardowych opcji przed zaimportowaniem, aby upewnić się, że bazy danych programu SQL Server zakłada ten sam format pól specjalnych, takich jak daty. Poniżej przedstawiono przykład sposobu ustawiania format daty rok, miesiąc, dzień (Jeśli dane zawierają datę w formacie rok, miesiąc, dzień):

        SET DATEFORMAT ymd;    
2. Importowanie danych przy użyciu zbiorczego instrukcje importu:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Wbudowane narzędzia w programie SQL Server
SQL Server integracji Services (SSIS) służy do importowania danych do maszyny Wirtualnej serwera SQL na platformie Azure z pliku prostego.
SSIS jest dostępny w dwóch środowisk studio. Aby uzyskać więcej informacji, zobacz [Integration Services (SSIS) oraz środowisk Studio](https://technet.microsoft.com/library/ms140028.aspx):

* Szczegółowe informacje dotyczące programu SQL Server Data Tools, zobacz [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Aby uzyskać więcej informacji o Kreatorze importu/eksportu, zobacz [Kreatora programu SQL Server importowania i eksportowania](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Przenoszenie danych z lokalnego programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure
Można także użyć następujących strategii migracji:

1. [Wdrażanie bazy danych programu SQL Server do kreatora maszyny Wirtualnej programu Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Wyeksportuj do pliku prostego](#export-flat-file)
3. [Kreator migracji bazy danych SQL](#sql-migration)
4. [Baza danych kopii zapasowej i przywracanie](#sql-backup)

Opisano każdy z tych poniżej:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Wdrażanie bazy danych programu SQL Server do kreatora maszyny Wirtualnej programu Microsoft Azure
**Wdrażania bazy danych programu SQL Server do kreatora Microsoft Azure VM** jest proste i zalecanym sposobem przenoszenia danych z lokalnego wystąpienia programu SQL Server do programu SQL Server na maszynie Wirtualnej platformy Azure. Aby uzyskać szczegółowy opis kroków, jak również zapoznać się z omówieniem alternatywnych, zobacz [migrację bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Wyeksportuj do pliku prostego
Różne metody może służyć do zbiorczego eksportowania danych z lokalnego programu SQL Server, zgodnie z opisem w [zbiorczego importowanie i eksportowanie danych (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) tematu. Ten dokument obejmują na przykład Program kopiowania zbiorczego (BCP). Gdy dane są eksportowane do pliku prostego, można je zaimportować do innego serwera SQL za pomocą importowania zbiorczego.

1. Eksportowanie danych z lokalnego serwera SQL w pliku za pomocą narzędzia bcp w następujący sposób

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Tworzenie bazy danych i tabeli na maszynie Wirtualnej programu SQL Server przy użyciu usługi Azure `create database` i `create table` eksportowane schemat tabeli w kroku 1.
3. Tworzenie pliku formatu do opisywania tabeli Schemat danych eksportu/importu. Szczegóły pliku formatu są opisane w [Utwórz plik formatu (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Generowanie pliku formatu podczas uruchamiania narzędzia BCP z komputera programu SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Generowanie pliku formatu podczas uruchamiania narzędzia BCP zdalnie na serwerze SQL

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Użyj jednej z metod opisanych w sekcji [przenoszenia danych z pliku źródłowego](#filesource_to_sqlonazurevm) przenoszenia danych w prostych plików do programu SQL Server.

### <a name="sql-migration"></a>Kreator migracji bazy danych SQL
[Kreator migracji bazy danych programu SQL Server](http://sqlazuremw.codeplex.com/) zapewnia przyjazną dla użytkownika sposób przenoszenia danych między dwa wystąpienia programu SQL server. Umożliwia użytkownikowi mapowanie schematu danych między źródłami a tabel docelowych, wybierz typy kolumn i różnych innych funkcji. Używa kopiowania zbiorczego (BCP) w obszarze obejmuje. Poniżej przedstawiono zrzut ekranu przedstawiający ekran powitalny Kreatora migracji bazy danych SQL.  

![Kreator migracji programu SQL Server][2]

### <a name="sql-backup"></a>Baza danych kopii zapasowej i przywracanie
SQL Server obsługuje:

1. [Baza danych kopii zapasowej i przywrócenia jej funkcjonalności](https://msdn.microsoft.com/library/ms187048.aspx) (zarówno do lokalnego pliku lub pliku bacpac Eksport do obiektu blob) i [aplikacji warstwy danych](https://msdn.microsoft.com/library/ee210546.aspx) (przy użyciu pliku bacpac).
2. Możliwość bezpośrednio tworzenia maszyn wirtualnych programu SQL Server na platformie Azure za pomocą skopiowanego bazy danych lub Kopiuj do istniejącej bazy danych SQL Azure. Aby uzyskać więcej informacji, zobacz [za pomocą Kreatora kopiowania bazy danych](https://msdn.microsoft.com/library/ms188664.aspx).

Zrzut ekranu przedstawiający kopii bazy danych w górę/przywracania z programu SQL Server Management Studio opcje są wyświetlane poniżej.

![Narzędzie importu programu SQL Server][1]

## <a name="resources"></a>Zasoby
[Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Omówienie programu SQL Server w usłudze Azure Virtual Machines](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
