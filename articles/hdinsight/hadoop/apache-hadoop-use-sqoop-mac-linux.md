---
title: Apache Sqoop z platformą Hadoop — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzia Apache Sqoop do importowania i eksportowania między Hadoop w usłudze HDInsight i bazy danych SQL Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 38b1c57b9ac666bc908df69b29f72fbd5aea0495
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403913"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop umożliwia importowanie i eksportowanie danych między Hadoop w usłudze HDInsight i bazy danych SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać narzędzia Apache Sqoop importowania i eksportowania między klastrem usługi Hadoop w usłudze Azure HDInsight i bazy danych Azure SQL Database lub programu Microsoft SQL Server. Użyj dokumentów z krokami w tym `sqoop` polecenia bezpośrednio z headnode klastra usługi Hadoop. SSH umożliwia łączenie z węzłem głównym, a następnie uruchom polecenia w tym dokumencie.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracować tylko z klastrami usługi HDInsight, które używają systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

> [!WARNING]
> Kroki opisane w tym dokumencie założono, że utworzono już bazy danych SQL Azure o nazwie `sqooptest`.
>
> Ten dokument zawiera instrukcje T-SQL, które są używane do tworzenia i zapytania dotyczącego tabeli w bazie danych SQL. Istnieje wielu klientów, że można używać tych instrukcji z bazy danych SQL. Firma Microsoft zaleca następujących klientów:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * [Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) narzędzia

## <a name="create-the-table-in-sql-database"></a>Tworzenie tabeli w bazie danych SQL

> [!IMPORTANT]
> Jeśli korzystasz z klastrem usługi HDInsight i tworzenie bazy danych SQL w [Tworzenie klastra i bazy danych SQL](hdinsight-use-sqoop.md), pomiń kroki opisane w tej sekcji. Bazy danych i tabeli zostały utworzone jako część kroki [Tworzenie klastra i bazy danych SQL](hdinsight-use-sqoop.md) dokumentu.

Aby nawiązać połączenie za pomocą klienta programu SQL `sqooptest` bazy danych w bazie danych SQL. Następnie użyj T-SQL, aby utworzyć tabeli o nazwie `mobiledata`:

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

## <a name="sqoop-export"></a>Sqoop eksportu

1. Używanie protokołu SSH, aby połączyć się z klastrem usługi HDInsight. Na przykład następujące polecenie nawiązuje połączenie z podstawowym headnode klastra o nazwie `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Aby sprawdzić, czy Sqoop widzą bazy danych SQL, użyj następującego polecenia:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Po wyświetleniu monitu wprowadź hasło dla nazwy logowania bazy danych SQL.

    To polecenie zwraca listę baz danych, w tym **sqooptest** baza danych używana wcześniej.

3. Aby wyeksportować dane z gałęzi **hivesampletable** do tabeli **mobiledata** tabeli w bazie danych SQL, użyj następującego polecenia:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następującego zapytania z klienta programu SQL, aby wyświetlić wyeksportowane dane:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    To polecenie wyświetla listę 50 wierszy, które zostały zaimportowane do tabeli.

## <a name="sqoop-import"></a>Importuj Sqoop

1. Użyj następującego polecenia do importowania danych z **mobiledata** do tabeli w bazie danych SQL, **wasb: / / / samouczki/usesqoop/importeddata** katalogu w usłudze HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Pola są oddzielone znak tabulacji, a wiersze kończą się znakiem nowego wiersza.

    > [!IMPORTANT]
    > `wasb:///` Ścieżka współpracuje z klastrów korzystających z usługi Magazyn Azure jako domyślnego magazynu klastra. W przypadku klastrów korzystających z usługi Azure Data Lake Store, użyj `adl:///` zamiast tego.

2. Po zakończeniu importowania, użyj następującego polecenia do listy limit danych w nowym katalogu:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Za pomocą programu SQL Server

Umożliwia także Sqoop umożliwia importowanie i eksportowanie danych z programu SQL Server. Różnice między przy użyciu bazy danych SQL i programu SQL Server są następujące:

* Zarówno HDInsight, jak i SQL Server musi być w tej samej sieci wirtualnej platformy Azure.

    Na przykład zobacz [HDInsight połączyć się z siecią lokalną](./../connect-on-premises-network.md) dokumentu.

    Aby uzyskać więcej informacji o korzystaniu z sieci wirtualnej platformy Azure HDInsight, zobacz [rozszerzenie usługi HDInsight za pomocą usługi Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu. Aby uzyskać więcej informacji w sieci wirtualnej Azure, zobacz [omówienie sieci wirtualnych](../../virtual-network/virtual-networks-overview.md) dokumentu.

* Program SQL Server musi być skonfigurowane i umożliwiają uwierzytelnianie SQL. Aby uzyskać więcej informacji, zobacz [wybierz tryb uwierzytelniania](https://msdn.microsoft.com/ms144284.aspx) dokumentu.

* Może być konieczne skonfigurowanie serwera SQL na akceptowanie połączeń zdalnych. Aby uzyskać więcej informacji, zobacz [jak rozwiązywać problemy z nawiązywania połączenia z aparatem bazy danych programu SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentu.

* Użyj następujących instrukcji języka Transact-SQL, aby utworzyć **mobiledata** tabeli:

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

* Podczas nawiązywania połączenia z programem SQL Server z usługi HDInsight, należy użyć adresu IP programu SQL Server. Na przykład:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Ograniczenia

* Zbiorcze export - opartych na systemie Linux z usługi HDInsight, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub bazy danych SQL Azure nie obsługuje operacji wstawiania zbiorczego.

* Przetwarzanie wsadowe — z opartą na systemie Linux usługą HDInsight przy użyciu `-batch` przełączyć podczas wykonywania operacji wstawienia, Sqoop sprawia, że wiele operacji wstawienia zamiast przetwarzanie wsadowe operacji insert.

## <a name="next-steps"></a>Kolejne kroki

Teraz ma przedstawiono sposób używania Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z usługą HDInsight Oozie](../hdinsight-use-oozie.md): Użyj Sqoop działań w przepływie pracy Oozie.
* [Analizowanie danych opóźnienie transmitowane przy użyciu usługi HDInsight](../hdinsight-analyze-flight-delay-data.md): Użyj gałąź rejestru, aby transmitowane analizować opóźnienie danych, a następnie użyj Sqoop eksportować dane do bazy danych Azure SQL.
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md): znajdowanie innych metod do przekazywania danych do magazynu obiektów Blob HDInsight/Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
