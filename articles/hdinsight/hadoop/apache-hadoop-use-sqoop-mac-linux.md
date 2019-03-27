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
ms.date: 02/15/2019
ms.openlocfilehash: 0f96ee3a24a811d7e3ab7e65ba4ff14050541638
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443375"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Przy użyciu narzędzia Apache Sqoop do importowania i eksportowania danych między Apache Hadoop w HDInsight a bazą danych SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak importować i eksportować między klastra Apache Hadoop w usłudze Azure HDInsight a bazą danych Azure SQL Database lub programu Microsoft SQL Server przy użyciu narzędzia Apache Sqoop. Kroki opisane w tej dokumentów użyj `sqoop` polecenia bezpośrednio z węzłem głównym klastra Hadoop. Protokół SSH umożliwia łączenie z węzłem głównym, a następnie uruchom polecenia w tym dokumencie.

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie pracować tylko z klastrami HDInsight, które używają systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

> [!WARNING]  
> Kroki opisane w tym dokumencie przyjęto założenie, że utworzono już o nazwie usługi Azure SQL Database `sqooptest`.
>
> Ten dokument zawiera instrukcje języka T-SQL, które są używane do tworzenia i wysłać zapytanie do tabeli w bazie danych SQL. Istnieje wielu klientów, że za pomocą tych instrukcji SQL Database. Firma Microsoft zaleca następujących klientów:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * [Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) narzędzia

## <a name="create-the-table-in-sql-database"></a>Tworzenie tabeli w bazie danych SQL

> [!IMPORTANT]  
> Jeśli używasz klastra HDInsight i baza danych SQL jest tworzona w [utworzenia klastra i bazy danych SQL](hdinsight-use-sqoop.md), pomiń kroki opisane w tej sekcji. Bazy danych i tabeli, które zostały utworzone w ramach kroków [utworzenia klastra i bazy danych SQL](hdinsight-use-sqoop.md) dokumentu.

Aby nawiązać połączenie za pomocą klienta SQL `sqooptest` bazy danych w usłudze SQL Database. Następnie użyj języka T-SQL, aby utworzyć tabelę o nazwie `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Eksportowanie narzędzia Sqoop

1. Za pomocą protokołu SSH Połącz się z klastrem HDInsight. Na przykład poniższe polecenie nawiązuje połączenie z podstawowym węzłem głównym klastra o nazwie `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby sprawdzić, czy Sqoop widzą bazy danych SQL, użyj następującego polecenia:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Po wyświetleniu monitu wprowadź hasło dla nazwy logowania bazy danych SQL.

    To polecenie zwraca listę baz danych, w tym **sqooptest** wcześniej używane w bazie danych.

3. Aby wyeksportować dane z gałęzi **hivesampletable** do tabeli **mobiledata** tabeli w bazie danych SQL, użyj następującego polecenia:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następującego zapytania z klienta SQL, aby wyświetlić wyeksportowane dane:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    To polecenie wyświetla listę 50 wierszy, które zostały zaimportowane do tabeli.

## <a name="sqoop-import"></a>Sqoop importu

1. Użyj następującego polecenia, aby zaimportować dane z **mobiledata** do tabeli w bazie danych SQL, **wasb: / / / samouczki/usesqoop/importeddata** katalogu HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Pola w danych są oddzielone znak tabulacji, a wiersze kończą się znakiem nowego wiersza.

    > [!IMPORTANT]  
    > `wasb:///` Ścieżki współpracuje z klastrów korzystających z usługi Azure Storage jako domyślnego magazynu klastra. W przypadku klastrów korzystających z usługi Azure Data Lake Storage Gen2 użyj `abfs:///` zamiast tego. W przypadku klastrów korzystających z usługi Azure Data Lake Storage Gen1 użyj `adl:///` zamiast tego.

2. Po zakończeniu importowania należy użyć następujące polecenie, aby wyświetlić listę danych w nowym katalogu:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Za pomocą programu SQL Server

Można również użyć Sqoop do importowania i eksportowania danych z programu SQL Server. Różnice między bazą danych SQL i programu SQL Server są następujące:

* HDInsight i programu SQL Server musi być w tej samej sieci wirtualnej platformy Azure.

    Aby uzyskać przykład, zobacz [Connect HDInsight z siecią lokalną](./../connect-on-premises-network.md) dokumentu.

    Aby uzyskać więcej informacji o używaniu HDInsight z usługą Azure Virtual Network, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu. Aby uzyskać więcej informacji na temat usługi Azure Virtual Network, zobacz [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md) dokumentu.

* Programu SQL Server musi być skonfigurowane i umożliwiają uwierzytelnianie programu SQL. Aby uzyskać więcej informacji, zobacz [wybierz tryb uwierzytelniania](https://msdn.microsoft.com/ms144284.aspx) dokumentu.

* Może być konieczne konfigurowanie programu SQL Server do akceptowania połączeń zdalnych. Aby uzyskać więcej informacji, zobacz [jak rozwiązywanie problemów z połączeniem z aparatem bazy danych programu SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentu.

* Poniższe instrukcje języka Transact-SQL umożliwia utworzenie **mobiledata** tabeli:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Podczas nawiązywania połączenia z programem SQL Server z HDInsight, trzeba użyć adresu IP programu SQL Server. Na przykład:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Ograniczenia

* Zbiorcze export - HDInsight opartych na systemie Linux za pomocą, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje operacji wstawiania zbiorczego.

* Przetwarzanie wsadowe — za pomocą HDInsight opartych na systemie Linux, korzystając z `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop sprawia, że wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="next-steps"></a>Kolejne kroki

Teraz masz pokazaliśmy, jak przy użyciu narzędzia Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Za pomocą usług Apache Oozie HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj narzędzia Sqoop akcji w przepływ pracy programu Oozie.
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Użyj Apache Hive do analizowania danych dotyczących opóźnień lotów, a następnie wyeksportować dane do usługi Azure SQL database przy użyciu narzędzia Sqoop.
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do usługi HDInsight/Azure Blob storage.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
