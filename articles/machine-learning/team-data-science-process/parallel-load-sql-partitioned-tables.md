---
title: Tworzenie i zoptymalizować tabel fast równoległych importowania danych do programu SQL Server na maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Równoległy zbiorczy import danych przy użyciu tabeli partycji SQL
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 2de926746a5e6b94a458dbc1a126ab5bc86b12fe
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838538"
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Równoległy zbiorczy import danych przy użyciu tabeli partycji SQL
Ten dokument zawiera opis sposobu tworzenia partycjonowane tabele fast równoległych zbiorczego importowania danych do bazy danych programu SQL Server. Ładowanie danych big data/transferu do bazy danych SQL, importowanie danych do bazy danych SQL i kolejne zapytania można zwiększyć za pomocą *partycjonowane tabele i widoki*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Utwórz nową bazę danych i zestaw grup plików
* [Utwórz nową bazę danych](https://technet.microsoft.com/library/ms176061.aspx), jeśli jest ona już nie istnieje.
* Dodawanie grup plików bazy danych do bazy danych, która przechowuje pliki fizyczne podzielonym na partycje. 
* Można to zrobić z [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) w przypadku nowego lub [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) Jeśli baza danych już istnieje.
* Dodaj jeden lub więcej plików (w razie potrzeby) do każdej grupy plików bazy danych.
  
  > [!NOTE]
  > Określ grupę plików docelowych, którym są przechowywane dane dla tej partycji i nazwy plików fizycznej bazy danych, których są przechowywane dane grupy plików.
  > 
  > 

Poniższy przykład tworzy nową bazę danych z trzech grup plików innych niż podstawowy i grupy dzienników, zawierających jeden plik fizyczny w każdym. Pliki bazy danych są tworzone w domyślnym folderem danych programu SQL Server, zgodnie z konfiguracją w wystąpieniu programu SQL Server. Aby uzyskać więcej informacji na temat domyślne lokalizacje plików, zobacz [lokalizacje domyślne i nazwane wystąpienia programu SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

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
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Tworzenie tabeli partycjonowanej
Aby utworzyć partycjonowanych tabel schemat danych, mapowane do grup plików bazy danych utworzone w poprzednim kroku, należy najpierw utworzyć funkcji partycji i schematu. Jeśli dane są zbiorczego importowania do tabel podzielonym na partycje, rekordy są rozłożone grup zgodnie ze schematem partycji, zgodnie z poniższym opisem.

### <a name="1-create-a-partition-function"></a>1. Utwórz funkcję partycji
[Utwórz funkcję partycji](https://msdn.microsoft.com/library/ms187802.aspx) tej funkcji określa zakres wartości/granice do uwzględnienia w każdej tabeli poszczególnych partycji, na przykład, aby ograniczyć partycje według miesięcy (niektóre\_datetime\_pola) w roku 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Tworzenie schematu partycji
[Tworzenie schematu partycji](https://msdn.microsoft.com/library/ms179854.aspx). Ten schemat mapuje każdego zakresu partycji w funkcji partycji fizycznej grupy plików, na przykład:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Aby sprawdzić zakresy obowiązywać w każdej partycji zgodnie z funkcji/schemat, uruchom następujące zapytanie:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Tworzenie tabeli partycji
[Tworzenie tabeli partycjonowanej](https://msdn.microsoft.com/library/ms174979.aspx)(s) zgodnie z schemat danych, a następnie określ pola schemat i ograniczenie partycji użyty do partycjonowania tabeli, na przykład:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Aby uzyskać więcej informacji, zobacz [tworzenia tabel na partycje i indeksów](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Zbiorcze importowanie danych dla każdej tabeli poszczególnych partycji

* Możesz użyć narzędzia BCP, BULK INSERT lub innych metod takich jak [Kreator migracji programu SQL Server](http://sqlazuremw.codeplex.com/). Podany przykład używa metody BCP.
* [Zmiany bazy danych](https://msdn.microsoft.com/library/bb522682.aspx) zmianę schematu rejestrowanie transakcji na BULK_LOGGED, aby zminimalizować obciążenie rejestrowania, na przykład:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Aby przyspieszyć ładowania danych, uruchom operacji importowania zbiorczego równolegle. Aby uzyskać wskazówki dotyczące usprawnienia zbiorczego importowanie danych big data do baz danych programu SQL Server, zobacz [załadować 1TB w mniej niż 1 godzina](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Poniższy skrypt programu PowerShell jest przykładem równoległego ładowania, przy użyciu narzędzia BCP danych.

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


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Tworzenie indeksów, aby zoptymalizować wydajność zapytań i sprzężenia
* Jeśli wyodrębniania danych do modelowania z wielu tabel, Utwórz indeksy kluczy sprzężenia, aby poprawić wydajność sprzężenia.
* [Tworzenie indeksów](https://technet.microsoft.com/library/ms188783.aspx) (klastrowanych lub nieklastrowanych) platformą docelową tej samej grupie plików, dla każdej partycji, na przykład:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Lub,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Możesz utworzyć indeksy przed zbiorczego importowania danych. Tworzenie indeksów, przed zaimportowaniem zbiorczego spowalnia ładowania danych.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Proces zaawansowane metody analizy i technologii w przykładzie akcji
Przykład wskazówki na trasie przy użyciu procesu nauki zespołu danych z publicznego zestawu danych, zobacz [proces nauki danych zespołu w działaniu: przy użyciu programu SQL Server](sql-walkthrough.md).

