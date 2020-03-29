---
title: Kopiowanie danych między magazynem danych Gen1 a platformą Azure SQL — Sqoop | Dokumenty firmy Microsoft
description: Kopiowanie danych między usługą Azure SQL Database database i azure data lake storage Gen1 za pomocą funkcji Sqoop
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839057"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopiowanie danych między magazynem danych Gen1 a usługą Azure SQL Database przy użyciu funkcji Sqoop

Dowiedz się, jak za pomocą apache Sqoop importować i eksportować dane między usługą Azure SQL Database i Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Co to jest Sqoop?

Aplikacje big data są naturalnym wyborem do przetwarzania nieustrukturyzowanych i częściowo ustrukturyzowanych danych, takich jak dzienniki i pliki. Jednak może być również konieczne przetwarzanie danych strukturalnych, które są przechowywane w relacyjnych baz danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) to narzędzie przeznaczone do przesyłania danych między relacyjnych baz danych i repozytorium dużych zbiorów danych, takich jak Data Lake Storage Gen1. Można go używać do importowania danych z relacyjnego systemu zarządzania bazami danych (RDBMS), takiego jak Azure SQL Database do bazy danych Data Lake Storage Gen1. Następnie można przekształcić i analizować dane przy użyciu obciążeń dużych zbiorów danych, a następnie wyeksportować dane z powrotem do rdbms. W tym artykule używasz bazy danych SQL platformy Azure jako relacyjnej bazy danych do importowania/eksportowania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy mieć następujące właściwości:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia konta, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Usługi Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z pamięcią Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). W tym artykule założono, że masz klaster SYSTEMU LINUX HDInsight z dostępem do magazynu danych Gen1.
* **Baza danych SQL platformy Azure**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz [Tworzenie bazy danych SQL platformy Azure](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Tworzenie przykładowych tabel w bazie danych SQL platformy Azure

1. Aby rozpocząć, należy utworzyć dwie przykładowe tabele w bazie danych SQL platformy Azure. Użyj [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby połączyć się z bazą danych, a następnie uruchomić następujące kwerendy.

    **Utwórz tabelę1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **Utwórz tabelę2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Uruchom następujące polecenie, aby dodać przykładowe dane do **tabeli Table1**. Pozostaw **Table2** pustą. Później zaimportujesz dane z **tabeli1** do usługi Data Lake Storage Gen1. Następnie wyeksportujesz dane z usługi Data Lake Storage Gen1 do **tabeli2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Korzystanie z funkcji Sqoop z klastra HDInsight z dostępem do pamięci masowej w uł.1

Klaster HDInsight ma już dostępne pakiety Sqoop. Jeśli klaster HDInsight został skonfigurowany do używania magazynu usługi Data Lake Gen1 jako dodatkowego magazynu, można użyć sqoop (bez żadnych zmian konfiguracji) do importowania/eksportowania danych między relacyjnej bazy danych, takiej jak usługa Azure SQL Database, a kontem Gen1 magazynu usługi Data Lake.

1. W tym artykule zakładamy, że utworzono klaster systemu Linux, więc należy użyć SSH, aby połączyć się z klastrem. Zobacz [Łączenie się z klastrem HDInsight opartym na systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Sprawdź, czy można uzyskać dostęp do konta Data Lake Storage Gen1 z klastra. Uruchom następujące polecenie z wiersza SSH:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   To polecenie zawiera listę plików/folderów na koncie Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importowanie danych z bazy danych SQL usługi Azure do usługi Data Lake Storage Gen1

1. Przejdź do katalogu, w którym dostępne są pakiety Sqoop. Zazwyczaj ta lokalizacja `/usr/hdp/<version>/sqoop/bin`jest .

1. Zaimportuj dane z **tabeli1** do konta Gen1 magazynu usługi Data Lake. Użyj następującej składni:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Symbol zastępczy **nazwy serwera bazy danych sql** reprezentuje nazwę serwera, na którym jest uruchomiona baza danych SQL platformy Azure. symbol **zastępczy nazwy bazy danych sql** reprezentuje rzeczywistą nazwę bazy danych.

   Na przykład:

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Sprawdź, czy dane zostały przeniesione na konto Data Lake Storage Gen1. Uruchom następujące polecenie:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Powinny zostać wyświetlone następujące dane wyjściowe.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Każdy plik **part-m-*** odpowiada wierszowi w tabeli źródłowej **Table1**. Można wyświetlić zawartość plików part-m-* do sprawdzenia.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Eksportowanie danych z usługi Data Lake Storage Gen1 do bazy danych SQL usługi Azure

1. Eksportuj dane z konta Data Lake Storage Gen1 do pustej tabeli **Table2**w bazie danych SQL Azure. Użyj następującej składni.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Na przykład:

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Sprawdź, czy dane zostały przekazane do tabeli Bazy danych SQL. Użyj [sql server management studio](../sql-database/sql-database-connect-query-ssms.md) lub visual studio, aby połączyć się z bazą danych SQL Azure, a następnie uruchomić następującą kwerendę.

       SELECT * FROM TABLE2

   To polecenie powinno mieć następujące dane wyjściowe.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Zagadnienia dotyczące wydajności podczas korzystania z sqoop

Aby uzyskać informacje na temat dostrajania wydajności zadania Sqoop w celu skopiowania danych do usługi Data Lake Storage Gen1, zobacz [wpis w blogu o wydajności Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="next-steps"></a>Następne kroki

* [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
