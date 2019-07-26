---
title: Apache Sqoop z usługą Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak używać oprogramowania Apache Sqoop do importowania i eksportowania między Apache Hadoop w usłudze HDInsight i Azure SQL Database.
keywords: Hadoop Sqoop, Sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406047"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Za pomocą oprogramowania Apache Sqoop można importować i eksportować dane między Apache Hadoop w usłudze HDInsight i SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać platformy Apache Sqoop do importowania i eksportowania między klastrem Apache Hadoop w usłudze Azure HDInsight i Azure SQL Database lub Microsoft SQL Server bazie danych. Kroki opisane w tym dokumencie używają `sqoop` polecenia bezpośrednio z węzła głównego klastra Hadoop. Używasz protokołu SSH do nawiązywania połączenia z węzłem głównym i uruchamiania poleceń z tego dokumentu. Ten artykuł jest kontynuacją [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [używania platformy Apache Sqoop z usługą Hadoop w usłudze HDInsight](./hdinsight-use-sqoop.md).

* Klient służący do wysyłania zapytań do bazy danych Azure SQL Database. Rozważ użycie [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) lub [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Eksport Sqoop

Z programu Hive do SQL Server.

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Zamień `CLUSTERNAME` na nazwę klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zamień `MYSQLSERVER` na nazwę SQL Server. Aby sprawdzić, czy Sqoop może zobaczyć SQL Database, wprowadź poniższe polecenie w obszarze Otwórz połączenie SSH. Wprowadź hasło dla SQL Server logowania po wyświetleniu monitu. To polecenie zwraca listę baz danych.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Zamień `MYSQLSERVER` na nazwę SQL Server i `MYDATABASE` nazwę bazy danych SQL. Aby wyeksportować dane z tabeli programu `hivesampletable` Hive `mobiledata` do tabeli w SQL Database, wprowadź poniższe polecenie w otwartym połączeniu SSH. Wprowadź hasło dla SQL Server logowania po wyświetleniu monitu

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następujących zapytań z klienta SQL, aby wyświetlić wyeksportowane dane:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop import

Z SQL Server do usługi Azure Storage.

1. Zamień `MYSQLSERVER` na nazwę SQL Server i `MYDATABASE` nazwę bazy danych SQL. Wprowadź poniższe polecenie w otwartym połączeniu SSH, aby zaimportować dane z `mobiledata` tabeli w SQL Database, `wasb:///tutorials/usesqoop/importeddata` do katalogu w usłudze HDInsight. Wprowadź hasło dla SQL Server logowania po wyświetleniu monitu. Pola w danych są oddzielane znakami tabulacji, a wiersze kończą się znakiem nowego wiersza.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Po zakończeniu importowania wprowadź następujące polecenie w otwartym połączeniu SSH, aby wyświetlić listę danych w nowym katalogu:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — za pomocą usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do Microsoft SQL Server lub Azure SQL Database nie obsługuje operacji wstawiania zbiorczego.

* Przetwarzanie wsadowe — za pomocą usługi HDInsight opartej na systemie `-batch` Linux, gdy podczas wykonywania operacji wstawiania jest używany przełącznik, Sqoop tworzy wiele wstawek zamiast wsadowych operacje wstawiania.

## <a name="important-considerations"></a>Ważne zagadnienia

* Zarówno Usługa HDInsight, jak i SQL Server muszą znajdować się na tym samym Virtual Network platformy Azure.

    Aby zapoznać się z przykładem, zobacz temat [łączenie usługi HDInsight z dokumentem w sieci lokalnej](./../connect-on-premises-network.md) .

    Aby uzyskać więcej informacji na temat używania usługi HDInsight z usługą Azure Virtual Network, zobacz dokument o rozszerzonej usłudze [HDInsight z platformą azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) . Aby uzyskać więcej informacji na temat usługi Azure Virtual Network, zobacz dokument [omówienie Virtual Network](../../virtual-network/virtual-networks-overview.md) .

* SQL Server musi być skonfigurowany tak, aby zezwalał na uwierzytelnianie SQL. Aby uzyskać więcej informacji, zapoznaj się z dokumentem [Wybieranie trybu uwierzytelniania](https://msdn.microsoft.com/ms144284.aspx) .

* Może być konieczne skonfigurowanie SQL Server w celu akceptowania połączeń zdalnych. Aby uzyskać więcej informacji, zobacz artykuł [Jak rozwiązywać problemy z nawiązywaniem połączenia z programem SQL Server Database Engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak używać programu Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z platformy Apache Oozie z usługą HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj akcji Sqoop w przepływie pracy Oozie.
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu usługi HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Użyj interakcyjnego zapytania, aby przeanalizować dane dotyczące opóźnień lotów, a następnie użyć Sqoop do eksportowania danych do usługi Azure SQL Database.
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do usługi HDInsight/Azure Blob Storage.
