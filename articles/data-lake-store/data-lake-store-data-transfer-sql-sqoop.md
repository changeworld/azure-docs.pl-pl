---
title: Kopiowanie danych między Azure Data Lake Storage Gen1 i Azure SQL database przy użyciu narzędzia Sqoop | Dokumentacja firmy Microsoft
description: Kopiowanie danych między środowiskiem usługi Azure SQL Database i Azure Data Lake Storage Gen1 przy użyciu narzędzia Sqoop
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878535"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopiowanie danych między Azure Data Lake Storage Gen1 i Azure SQL database przy użyciu narzędzia Sqoop
Dowiedz się, jak do importowania i eksportowania danych między Azure SQL Database i Azure Data Lake Storage Gen1 przy użyciu narzędzia Apache Sqoop.

## <a name="what-is-sqoop"></a>Co to jest Sqoop?
Aplikacje danych big data są naturalnym wyborem do przetwarzania danych bez struktury i o połowicznej strukturze, takich jak dzienniki i pliki. Jednak mogą istnieć potrzeba przetwarzania danych strukturalnych, które są przechowywane w relacyjnych baz danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) to narzędzie do transferu danych między relacyjnych baz danych i repozytorium danych big data, takie jak Data Lake Storage Gen1. Służy do importowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS) takich jak Azure SQL Database do Data Lake Storage Gen1. Można, a następnie Przekształcanie i analizowanie danych przy użyciu obciążeń dużymi ilościami danych i następnie wyeksportować dane z powrotem w systemie RDBMS. W tym samouczku użycia usługi Azure SQL Database jako Twoja relacyjna baza danych do importu/eksportu z.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). W tym artykule przyjęto, że istnieje klastra usługi HDInsight w systemie Linux przy użyciu Data Lake Storage Gen1 dostępu.
* **Usługa Azure SQL Database**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [utworzyć bazę danych Azure SQL](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Czy dzięki filmom szybko się uczysz?
[Obejrzyj ten film wideo](https://mix.office.com/watch/1butcdjxmu114) dotyczącym kopiowania danych między Azure Storage Blobs a Data Lake Storage Gen1 korzystanie z narzędzia DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Utwórz przykładowe tabele w bazie danych SQL Azure
1. Na początek z Utwórz dwa przykładowe tabele w bazie danych SQL Azure. Użyj [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby nawiązać połączenie z bazą danych SQL Azure, a następnie uruchom następujące zapytania.

    **Utwórz Tabela1**

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

    **Utwórz Table2**

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
2. W **Tabela1**, Dodawanie przykładowych danych. Pozostaw **Table2** puste. Zaimportujemy dane z **Tabela1** do Data Lake Storage Gen1. Następnie możemy eksportowanie danych z Data Lake Storage Gen1 do **Table2**. Uruchom poniższy fragment kodu.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Przy użyciu narzędzia Sqoop z klastra usługi HDInsight z dostępem do programu Data Lake Storage Gen1
Klaster usługi HDInsight jest już dostępne pakiety narzędzia Sqoop. Jeśli skonfigurowano klaster HDInsight służące Data Lake Storage Gen1 jako dodatkowego magazynu umożliwia Sqoop (bez wprowadzania żadnych zmian konfiguracji) importu/eksportu danych między relacyjnej bazy danych (w tym przykładzie usługi Azure SQL Database) i usługi Data Lake Konto magazynu, Gen1.

1. W tym samouczku przyjęto założenie, że utworzono klastra systemu Linux, więc zaleca się użycie protokołu SSH do łączenia z klastrem. Zobacz [łączenie z klastrem HDInsight opartych na systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Sprawdź, czy można uzyskać dostępu do konta Data Lake Storage Gen1 z klastra. Uruchom następujące polecenie w wierszu polecenia SSH:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    To powinien podać listę plików/folderów, w ramach konta Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importowanie danych z usługi Azure SQL Database do Data Lake Storage Gen1
1. Przejdź do katalogu, w którym dostępne są pakiety narzędzia Sqoop. Zazwyczaj są to na `/usr/hdp/<version>/sqoop/bin`.
2. Importuj dane z **Tabela1** na konto usługi Data Lake Storage Gen1. Należy użyć następującej składni:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Należy pamiętać, że **— Nazwa bazy danych sql-server** symbol zastępczy reprezentuje nazwę serwera, na którym działa baza danych Azure SQL. **Nazwa bazy danych SQL** symbol zastępczy reprezentuje nazwę bazy danych.

    Na przykład:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Upewnij się, że dane zostały przeniesione do konta Data Lake Storage Gen1. Uruchom następujące polecenie:

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Następujące dane wyjściowe powinny być widoczne.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Każdy **część-m -*** odnosi się do wiersza w tabeli źródłowej **Tabela1**. Można wyświetlić zawartość części - m-* plików, aby sprawdzić.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Eksportowanie danych z programu Data Lake Storage Gen1 do usługi Azure SQL Database
1. Eksportowanie danych z konta Data Lake Storage Gen1 do pustej tabeli **Table2**, w usłudze Azure SQL Database. Należy użyć następującej składni.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Na przykład:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Sprawdź, czy danych został przekazany do tabeli bazy danych SQL. Użyj [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby nawiązać połączenie z bazą danych SQL Azure, a następnie uruchom następujące zapytanie.

        SELECT * FROM TABLE2

    Powinno to mieć następujące dane wyjściowe.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Zagadnienia dotyczące wydajności podczas korzystania z narzędzia Sqoop

Aby dostrajania wydajności oprogramowania Sqoop zadania umożliwia kopiowanie danych do programu Data Lake Storage Gen1, zobacz [Sqoop wydajności dokumentu](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Zobacz także
* [Kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
