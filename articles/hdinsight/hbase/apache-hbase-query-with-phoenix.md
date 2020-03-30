---
title: 'Szybki start: Apache HBase & Apache Phoenix - Azure HDInsight'
description: W tym przewodniku Szybki start dowiesz się, jak korzystać z apache phoenix w funkcji HDInsight. Dowiedz się również, jak zainstalować i skonfigurować program SQLLine na komputerze w celu połączenia się z klastrem HBase w programie HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370325"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Szybki start: Apache HBase w usłudze Azure HDInsight z Apache Phoenix

W tym przewodniku Szybki start dowiesz się, jak używać Apache Phoenix do uruchamiania zapytań HBase w usłudze Azure HDInsight. Apache Phoenix jest aparatem zapytań SQL dla Apache HBase. Jest on dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i zarządzanie tabelami HBase przy użyciu języka SQL. [SQLLine](http://sqlline.sourceforge.net/) jest narzędziem wiersza polecenia do wykonywania SQL.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache HBase. Zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md) w celu utworzenia klastra HDInsight.  Upewnij się, że wybierzesz typ klastra **HBase.**

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identyfikowanie węzła ZooKeeper

Po nawiązaniu połączenia z klastrem HBase należy połączyć się z jednym z węzłów Apache ZooKeeper. Każdy klaster HDInsight ma trzy węzły ZooKeeper. Curl może służyć do szybkiego identyfikowania węzła ZooKeeper. Edytuj polecenie curl poniżej, zastępując `PASSWORD` i `CLUSTERNAME` odpowiednimi wartościami, a następnie wprowadź polecenie w wierszu polecenia:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Część danych wyjściowych będzie wyglądać podobnie do:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Zanotuj wartość `host_name` do późniejszego użycia.

## <a name="create-a-table-and-manipulate-data"></a>Tworzenie tabeli i manipulowanie danymi

Za pomocą funkcji SSH można łączyć się z klastrami HBase, a następnie używać apache phoenix do tworzenia tabel HBase, wstawiania danych i danych zapytań.

1. Użyj `ssh` polecenia, aby połączyć się z klastrem HBase. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zmień katalog na klienta Phoenix. Wprowadź następujące polecenie:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Uruchom [program SQLLine](http://sqlline.sourceforge.net/). Edytuj poniższe polecenie, zastępując `ZOOKEEPER` węzłem ZooKeeper zidentyfikowanym wcześniej, a następnie wprowadź polecenie:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Utwórz tabelę bazy danych HBase. Wprowadź następujące polecenie:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Polecenie SQLLine `!tables` służy do listy wszystkich tabel w bazie danych HBase. Wprowadź następujące polecenie:

    ```sqlline
    !tables
    ```

6. Wstaw wartości w tabeli. Wprowadź następujące polecenie:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Kwerenda tabeli. Wprowadź następujące polecenie:

    ```sql
    SELECT * FROM Company;
    ```

8. Usuwanie rekordu. Wprowadź następujące polecenie:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Upuść stół. Wprowadź następujące polecenie:

    ```hbase
    DROP TABLE Company;
    ```

10. Użyj polecenia `!quit` SQLLine, aby zamknąć program SQLLine. Wprowadź następujące polecenie:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak używać Apache Phoenix do uruchamiania zapytań HBase w usłudze Azure HDInsight. Aby dowiedzieć się więcej o Apache Phoenix, następny artykuł zapewni głębszą analizę.

> [!div class="nextstepaction"]
> [Oprogramowanie Apache Phoenix w usłudze HDInsight](../hdinsight-phoenix-in-hdinsight.md)
