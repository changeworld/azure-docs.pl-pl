---
title: Kopiowanie danych między Data Lake Store i bazy danych Azure SQL przy użyciu Sqoop | Dokumentacja firmy Microsoft
description: Sqoop umożliwia kopiowanie danych między bazą danych SQL Azure i usługi Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 4f0c20b7d18d9b03ae7127e6cdcdc269a3c4b404
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624169"
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Kopiowanie danych między Data Lake Store i za pomocą Sqoop bazy danych Azure SQL
Dowiedz się, jak używać narzędzia Apache Sqoop umożliwia importowanie i eksportowanie danych między bazą danych SQL Azure i usługi Data Lake Store.

## <a name="what-is-sqoop"></a>Co to jest Sqoop?
Aplikacje danych big data nadają się naturalnie do przetwarzania nieustrukturyzowanych i częściowo ustrukturyzowanych danych, takich jak dzienniki i pliki. Jednak może również być potrzebne do przetwarzania danych strukturalnych, który jest przechowywany w relacyjnych baz danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) to narzędzie przeznaczone do transferowania danych relacyjnych baz danych i repozytorium danych big data, takich jak Data Lake Store. Można go użyć do importowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS) takie jak bazy danych SQL Azure do usługi Data Lake Store. Możesz przekształcić i analizować dane przy użyciu danych big data obciążeń i następnie wyeksportować dane do RDBMS. W tym samouczku użycia bazy danych SQL Azure jako relacyjnej bazy danych do importu/eksportu z.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Klaster HDInsight Azure** z dostępem do konta usługi Data Lake Store. Zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). W tym artykule przyjęto założenie, że masz klaster usługi HDInsight Linux z dostępem do usługi Data Lake Store.
* **Usługa Azure SQL Database**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [tworzenie bazy danych Azure SQL](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Czy dzięki filmom szybko się uczysz?
[Obejrzyj ten film](https://mix.office.com/watch/1butcdjxmu114) na kopiowanie danych między obiektach blob magazynu Azure i usługi Data Lake Store za pomocą narzędzia DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Tworzenie przykładowych tabel w bazie danych SQL Azure
1. Do uruchomienia z Utwórz dwa przykładowe tabele w bazie danych SQL Azure. Użyj [programu SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby nawiązać połączenie z bazą danych SQL Azure, a następnie uruchom następujące kwerendy.

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
2. W **Table1**, Dodawanie przykładowych danych. Pozostaw **Table2** puste. Firma Microsoft będzie importowania danych z **Tabela1** do usługi Data Lake Store. Następnie, firma Microsoft będzie eksportować dane z usługi Data Lake Store w **Table2**. Uruchom poniższy fragment kodu.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Użyj Sqoop z klastra usługi HDInsight z dostępem do usługi Data Lake Store
Klaster usługi HDInsight jest już dostępne pakiety Sqoop. Jeśli zostały skonfigurowane do używania usługi Data Lake Store jako dodatkowego magazynu klastra usługi HDInsight, umożliwia Sqoop (bez żadnych zmian konfiguracji) importu/eksportu danych między relacyjnej bazy danych (w tym przykładzie baza danych SQL Azure) i konto usługi Data Lake Store.

1. W tym samouczku przyjęto założenie, że utworzono klaster systemu Linux, SSH ma używać do łączenia się z klastrem. Zobacz [Połącz z klastrem usługi HDInsight opartej na systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Sprawdź, czy masz dostęp do konta usługi Data Lake Store z klastra. Uruchom następujące polecenie w wierszu SSH:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    To powinien zawierać listę plików/folderów w ramach konta usługi Data Lake Store.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importowanie danych z bazy danych SQL Azure do usługi Data Lake Store
1. Przejdź do katalogu, w którym dostępne są pakiety Sqoop. Zazwyczaj są to w `/usr/hdp/<version>/sqoop/bin`.
2. Importuj dane z **Tabela1** do konta usługi Data Lake Store. Należy użyć następującej składni:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Należy pamiętać, że **— Nazwa bazy danych sql-server** symbol zastępczy reprezentuje nazwę serwera, na którym działa baza danych Azure SQL. **Nazwa bazy danych SQL** symbol zastępczy reprezentuje nazwę rzeczywistej bazy danych.

    Na przykład:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Upewnij się, że dane zostały przeniesione do konta usługi Data Lake Store. Uruchom następujące polecenie:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Powinny zostać wyświetlone następujące dane wyjściowe.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Każdy **części-m -*** pliku odpowiada wiersz w tabeli źródłowej **Table1**. Można wyświetlić zawartość części - m-* plików, aby sprawdzić.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Eksportowanie danych z usługi Data Lake Store w bazie danych SQL Azure
1. Eksportowanie danych z konta usługi Data Lake Store, aby pusta tabela **Table2**, w bazie danych SQL Azure. Należy użyć następującej składni.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Na przykład:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Sprawdź, czy dane został przesłany do tabeli bazy danych SQL. Użyj [programu SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) lub Visual Studio, aby nawiązać połączenie z bazą danych SQL Azure, a następnie uruchom następujące zapytanie.

        SELECT * FROM TABLE2

    Ma to następujące dane wyjściowe.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Zagadnienia dotyczące wydajności przy użyciu Sqoop

Wydajność dostrajanie zadania Sqoop skopiować dane do usługi Data Lake Store, zobacz [Sqoop wydajności dokumentu](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Zobacz także
* [Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
