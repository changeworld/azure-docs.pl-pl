---
title: Równoległy zbiorczy import danych w tabelach partycji SQL — proces nauki o danych zespołowych
description: Tworzenie tabel podzielonych na partycje w celu szybkiego równoległego masowego importowania danych do bazy danych programu SQL Server.
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
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721340"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Tworzenie i optymalizowanie tabel pod kątem szybkiego równoległego importu danych do programu SQL Server na maszynie Wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia tabel podzielonych na partycje do szybkiego równoległego zbiorczego importowania danych do bazy danych programu SQL Server. W przypadku ładowania/przesyłania dużych zbiorów danych do bazy danych SQL importowanie danych do bazy danych SQL i kolejnych zapytań można poprawić za pomocą *tabel i widoków podzielonych na partycje.* 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Tworzenie nowej bazy danych i zestawu grup plików
* [Utwórz nową bazę danych](https://technet.microsoft.com/library/ms176061.aspx), jeśli jeszcze nie istnieje.
* Dodaj grupy plików bazy danych do bazy danych, która przechowuje pliki fizyczne na partycje. 
* Można to zrobić za pomocą [create bazy danych,](https://technet.microsoft.com/library/ms176061.aspx) jeśli nowe lub [alter bazy danych,](https://msdn.microsoft.com/library/bb522682.aspx) jeśli baza danych już istnieje.
* Dodaj jeden lub więcej plików (w razie potrzeby) do każdej grupy plików bazy danych.
  
  > [!NOTE]
  > Określ docelową grupę plików, która przechowuje dane dla tej partycji i nazwy plików bazy danych, w których są przechowywane dane grupy plików.
  > 
  > 

Poniższy przykład tworzy nową bazę danych z trzema grupami plików innymi niż grupy podstawowe i dzienniki, zawierającą jeden plik fizyczny w każdej. Pliki bazy danych są tworzone w domyślnym folderze Sql Server Data, skonfigurowanym w wystąpieniu programu SQL Server. Aby uzyskać więcej informacji na temat domyślnych lokalizacji plików, zobacz [Lokalizacje plików dla domyślnych i nazwanych wystąpień programu SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Tworzenie tabeli podzielonej na partycje
Aby utworzyć tabele podzielone na partycje zgodnie ze schematem danych, mapowane na grupy plików bazy danych utworzone w poprzednim kroku, należy najpierw utworzyć funkcję partycji i schemat. Gdy dane są importowane zbiorczo do tabel podzielonych na partycje, rekordy są dystrybuowane między grupy plików zgodnie ze schematem partycji, jak opisano poniżej.

### <a name="1-create-a-partition-function"></a>1. Tworzenie funkcji partycji
[Tworzenie funkcji partycji](https://msdn.microsoft.com/library/ms187802.aspx) Ta funkcja definiuje zakres wartości/granic, które mają być uwzględnione w każdej tabeli poszczególnych partycji, na przykład w celu ograniczenia partycji według miesiąca (niektóre\_pole datetime)\_w roku 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Tworzenie schematu partycji
[Tworzenie schematu partycji](https://msdn.microsoft.com/library/ms179854.aspx). Ten schemat mapuje każdy zakres partycji w funkcji partycji na fizyczną grupę plików, na przykład:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Aby sprawdzić zakresy obowiązujące w każdej partycji zgodnie z funkcją/schematem, uruchom następującą kwerendę:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Tworzenie tabeli partycji
[Utwórz tabelę podzieloną na partycje](https://msdn.microsoft.com/library/ms174979.aspx)zgodnie ze schematem danych i określ schemat partycji i pole ograniczenia używane do partycjonowania tabeli, na przykład:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Aby uzyskać więcej informacji, zobacz [Tworzenie tabel i indeksów podzielonych na partycje](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Zbiorcze importowanie danych dla każdej tabeli poszczególnych partycji

* Można użyć BCP, BULK INSERT lub innych metod, takich jak [Kreator migracji programu SQL Server](https://sqlazuremw.codeplex.com/). Podany przykład używa metody BCP.
* [Zmień bazę danych,](https://msdn.microsoft.com/library/bb522682.aspx) aby zmienić schemat rejestrowania transakcji, aby BULK_LOGGED, aby zminimalizować obciążenie związane z rejestrowaniem, na przykład:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Aby przyspieszyć ładowanie danych, uruchom równolegle operacje importu zbiorczego. Aby uzyskać wskazówki dotyczące przyspieszania masowego importowania dużych zbiorów danych do baz danych programu SQL Server, zobacz [Ładowanie 1 TB w czasie krótszym niż 1 godzina](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Poniższy skrypt programu PowerShell jest przykładem równoległego ładowania danych przy użyciu protokołu BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Tworzenie indeksów w celu optymalizacji sprzężeń i wydajności zapytań
* Jeśli wyodrębnić dane do modelowania z wielu tabel, należy utworzyć indeksy na klucze sprzężenia, aby poprawić wydajność sprzężenia.
* [Utwórz indeksy](https://technet.microsoft.com/library/ms188783.aspx) (klastrowane lub nieklastrowane) przeznaczone dla tej samej grupy plików dla każdej partycji, na przykład:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Lub
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Przed zbiorczym importowaniem danych można utworzyć indeksy. Tworzenie indeksu przed importowaniem zbiorczym spowalnia ładowanie danych.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Zaawansowany proces analityczny i technologia w przykładzie działania
W przykładzie przewodnika end-to-end przy użyciu procesu nauki o danych zespołu z publicznym zestawem danych zobacz [Proces nauki o danych zespołu w działaniu: przy użyciu programu SQL Server](sql-walkthrough.md).

