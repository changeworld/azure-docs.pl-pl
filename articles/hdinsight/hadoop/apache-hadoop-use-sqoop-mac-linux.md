---
title: Apache Sqoop z Apache Hadoop - Azure HDInsight
description: Dowiedz się, jak używać Apache Sqoop do importowania i eksportowania między apache Hadoop w programie HDInsight i bazie danych SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769391"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Importowanie i eksportowanie danych między narzędziem Apache Hadoop w usłudze HDInsight i usługą SQL Database przy użyciu narzędzia Apache Sqoop

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak za pomocą apache Sqoop importować i eksportować między klastrem Apache Hadoop w usłudze Azure HDInsight i usłudze Azure SQL Database lub microsoft SQL Server bazy danych. Kroki opisane w tym `sqoop` dokumencie używają polecenia bezpośrednio z węzła główny klastra Hadoop. Za pomocą SSH można połączyć się z węzłem głównym i uruchomić polecenia w tym dokumencie. Ten artykuł jest kontynuacją [Use Apache Sqoop z Hadoop w HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania środowiska testowego](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) z [Użyj Apache Sqoop z Hadoop w HDInsight](./hdinsight-use-sqoop.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Znajomość Sqoop. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Konfiguruj

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby ułatwić obsługę, należy ustawić zmienne. Zastąp `PASSWORD`, `MYSQLSERVER`i `MYDATABASE` odpowiednie wartości, a następnie wprowadź poniższe polecenia:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Eksport sqoop

Od gałęzi do programu SQL Server.

1. Aby sprawdzić, czy Sqoop może zobaczyć bazę danych SQL, wprowadź poniższe polecenie w otwartym połączeniu SSH. To polecenie zwraca listę baz danych.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Wprowadź następujące polecenie, aby wyświetlić listę tabel dla określonej bazy danych:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Aby wyeksportować `hivesampletable` dane `mobiledata` z tabeli Gałąź do tabeli w bazie danych SQL, wprowadź poniższe polecenie w otwartym połączeniu SSH:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Aby sprawdzić, czy dane zostały wyeksportowane, użyj następujących zapytań z połączenia SSH, aby wyświetlić wyeksportowane dane:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Import sqoop

Od programu SQL Server do magazynu platformy Azure.

1. Wprowadź poniższe polecenie w otwartym połączeniu SSH, aby zaimportować dane z `mobiledata` tabeli w bazie danych SQL do `wasbs:///tutorials/usesqoop/importeddata` katalogu w programie HDInsight. Pola w danych są oddzielone znakiem karty, a wiersze są przerywane znakiem nowego wiersza.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Alternatywnie można również określić tabelę hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Po zakończeniu importowania wprowadź następujące polecenie w otwartym połączeniu SSH, aby wyświetlić listę danych w nowym katalogu:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Użyj [beeline,](./apache-hadoop-use-hive-beeline.md) aby sprawdzić, czy tabela została utworzona w obszarze Gałęzi.

    1. Połącz

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Wykonaj każde zapytanie poniżej po jednym na raz i przejrzyj dane wyjściowe:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Wyjdź `!exit`z beeline z .

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — w przypadku usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje wstawiaków zbiorczych.

* Batching — z systemem LINUX HDInsight, Podczas korzystania z `-batch` przełącznika podczas wykonywania wstawia, Sqoop sprawia, że wiele wstawia zamiast przetwarzania wsadowego operacji wstawiania.

## <a name="important-considerations"></a>Istotne zagadnienia

* Zarówno HDInsight i SQL Server musi być w tej samej sieci wirtualnej platformy Azure.

    Na przykład zobacz [Podłącz HDInsight do lokalnego dokumentu sieciowego.](./../connect-on-premises-network.md)

    Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight z siecią wirtualną platformy Azure, zobacz [rozszerzanie usługi HDInsight za pomocą](../hdinsight-plan-virtual-network-deployment.md) dokumentu usługi Azure Virtual Network. Aby uzyskać więcej informacji na temat sieci wirtualnej platformy Azure, zobacz dokument [Omówienie sieci wirtualnej.](../../virtual-network/virtual-networks-overview.md)

* Program SQL Server musi być skonfigurowany tak, aby zezwalał na uwierzytelnianie SQL. Aby uzyskać więcej informacji, zobacz dokument [Wybieranie trybu uwierzytelniania.](https://msdn.microsoft.com/ms144284.aspx)

* Może być trzeba skonfigurować program SQL Server do akceptowania połączeń zdalnych. Aby uzyskać więcej informacji, zobacz [jak rozwiązać problem z łączeniem się z dokumentem aparatu bazy danych programu SQL Server.](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

## <a name="next-steps"></a>Następne kroki

Teraz nauczyłeś się, jak korzystać z Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Użyj Apache Oozie z HDInsight](../hdinsight-use-oozie-linux-mac.md): Użyj sqoop akcji w oozie przepływu pracy.
* [Analizowanie danych opóźnienia lotu przy użyciu funkcji HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Użyj interaktywnej kwerendy do analizowania danych opóźnienia lotu, a następnie użyj Sqoop do eksportowania danych do bazy danych SQL platformy Azure.
* [Przekaż dane do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do magazynu obiektów BLOB USŁUGI HDInsight/Azure.
