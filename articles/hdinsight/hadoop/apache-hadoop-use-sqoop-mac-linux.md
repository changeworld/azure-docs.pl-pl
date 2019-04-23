---
title: Apache Sqoop przy użyciu technologii Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak do importowania i eksportowania między Apache Hadoop w HDInsight a bazą danych Azure SQL Database przy użyciu narzędzia Apache Sqoop.
keywords: hadoop, sqoop, sqoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 75a77843bd7634e8a3fe7a6b46177efe54878682
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148887"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Przy użyciu narzędzia Apache Sqoop do importowania i eksportowania danych między Apache Hadoop w HDInsight a bazą danych SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak importować i eksportować między klastra Apache Hadoop w usłudze Azure HDInsight a bazą danych Azure SQL Database lub programu Microsoft SQL Server przy użyciu narzędzia Apache Sqoop. Kroki opisane w tej dokumentów użyj `sqoop` polecenia bezpośrednio z węzłem głównym klastra Hadoop. Protokół SSH umożliwia łączenie z węzłem głównym, a następnie uruchom polecenia w tym dokumencie. Ten artykuł jest kontynuacją [Użyj Apache Sqoop z usługą Hadoop w HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [Konfiguracja środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [Użyj Apache Sqoop z usługą Hadoop w HDInsight](./hdinsight-use-sqoop.md).

* Klient do wykonywania zapytań w bazie danych Azure SQL. Należy rozważyć użycie [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) lub [programu Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Eksportowanie narzędzia Sqoop

Z programu Hive do programu SQL Server.

1. Za pomocą protokołu SSH Połącz się z klastrem HDInsight. Zastąp `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zastąp `MYSQLSERVER` nazwą swojego serwera SQL Server. Aby sprawdzić, czy Sqoop widzą bazy danych SQL, wprowadź poniższe polecenie w Otwórz połączenie SSH. Wprowadź hasło dla nazwy logowania programu SQL Server, po wyświetleniu monitu. To polecenie zwraca listę baz danych.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Zastąp `MYSQLSERVER` nazwą swojego serwera SQL Server i `MYDATABASE` o nazwie bazy danych SQL. Aby wyeksportować dane z gałęzi `hivesampletable` do tabeli `mobiledata` tabeli w bazie danych SQL, wprowadź poniższe polecenie w Otwórz połączenie SSH. Wprowadź hasło dla nazwy logowania programu SQL Server, po wyświetleniu monitu

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Aby sprawdzić, czy dane zostały wyeksportowane, należy użyć następujących zapytań z klienta SQL w celu wyświetlenia wyeksportowane dane:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop importu

Z programu SQL Server do usługi Azure storage.

1. Zastąp `MYSQLSERVER` nazwą swojego serwera SQL Server i `MYDATABASE` o nazwie bazy danych SQL. Wprowadź poniższe polecenie w Otwieranie połączenia SSH, aby zaimportować dane z `mobiledata` do tabeli w bazie danych SQL, `wasb:///tutorials/usesqoop/importeddata` katalogu HDInsight. Wprowadź hasło dla nazwy logowania programu SQL Server, po wyświetleniu monitu. Pola w danych są oddzielone znak tabulacji, a wiersze kończą się znakiem nowego wiersza.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Po zakończeniu importowania, wprowadź następujące polecenie w Otwórz połączenie SSH, aby wyświetlić listę danych w nowym katalogu:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Ograniczenia

* Zbiorcze export - HDInsight opartych na systemie Linux za pomocą, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje operacji wstawiania zbiorczego.

* Przetwarzanie wsadowe — za pomocą HDInsight opartych na systemie Linux, korzystając z `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop sprawia, że wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="important-considerations"></a>Istotne zagadnienia

* HDInsight i programu SQL Server musi być w tej samej sieci wirtualnej platformy Azure.

    Aby uzyskać przykład, zobacz [Connect HDInsight z siecią lokalną](./../connect-on-premises-network.md) dokumentu.

    Aby uzyskać więcej informacji o używaniu HDInsight z usługą Azure Virtual Network, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu. Aby uzyskać więcej informacji na temat usługi Azure Virtual Network, zobacz [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md) dokumentu.

* Programu SQL Server musi być skonfigurowane i umożliwiają uwierzytelnianie programu SQL. Aby uzyskać więcej informacji, zobacz [wybierz tryb uwierzytelniania](https://msdn.microsoft.com/ms144284.aspx) dokumentu.

* Może być konieczne konfigurowanie programu SQL Server do akceptowania połączeń zdalnych. Aby uzyskać więcej informacji, zobacz [jak rozwiązywanie problemów z połączeniem z aparatem bazy danych programu SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentu.

## <a name="next-steps"></a>Kolejne kroki

Teraz masz pokazaliśmy, jak przy użyciu narzędzia Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Za pomocą usług Apache Oozie HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj narzędzia Sqoop akcji w przepływ pracy programu Oozie.
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Użyj Apache Hive do analizowania danych dotyczących opóźnień lotów, a następnie wyeksportować dane do usługi Azure SQL database przy użyciu narzędzia Sqoop.
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do usługi HDInsight/Azure Blob storage.
