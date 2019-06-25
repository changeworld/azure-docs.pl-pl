---
title: 'Szybki start: Zapytanie Apache HBase w usłudze Azure HDInsight — rozwiązania Apache Phoenix'
description: W tym przewodniku Szybki Start dowiesz się, jak używać rozwiązania Apache Phoenix w HDInsight. Ponadto Dowiedz się, jak zainstalować i skonfigurować SQLLine na komputerze, aby nawiązać połączenie z klastra usługi HBase na platformie HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137420"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Szybki start: Zapytanie Apache HBase w usłudze Azure HDInsight przy użyciu rozwiązania Apache Phoenix

W tym przewodniku Szybki Start dowiesz się, jak używać rozwiązania Apache Phoenix do uruchamiania kwerend bazy danych HBase w usłudze Azure HDInsight. Apache Phoenix jest aparatem zapytań SQL dla bazy danych Apache HBase. Jest on dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i zarządzanie tabelami HBase przy użyciu języka SQL. [SQLLine](http://sqlline.sourceforge.net/) to narzędzie wiersza polecenia do wykonania SQL.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster bazy danych Apache HBase. Zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) do tworzenia klastra usługi HDInsight.  Upewnij się, możesz wybrać **HBase** typ klastra.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identyfikowania węzłów dozorcy

Po nawiązaniu połączenia z klastrem HBase, należy połączyć się z jednego z węzłów Apache ZooKeeper. Każdy klaster HDInsight ma trzy węzły dozorcy. Curl, można szybko zidentyfikować węzeł usługi ZooKeeper. Edytuj poniższe polecenie programu curl, zastępując `PASSWORD` i `CLUSTERNAME` z odpowiednimi wartościami, a następnie wprowadź polecenie w wierszu polecenia:

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

Zwróć uwagę na wartość `host_name` do późniejszego użycia.

## <a name="create-a-table-and-manipulate-data"></a>Utwórz tabelę i manipulowanie danymi

Można połączyć się z klastrami HBase za pomocą protokołu SSH, a następnie tworzenia tabel bazy danych HBase, wstawiania danych i zapytania o dane za pomocą rozwiązania Apache Phoenix.

1. Użyj `ssh` polecenie, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wpisz polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zmień katalog na klient Phoenix. Wprowadź następujące polecenie:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Uruchom [SQLLine](http://sqlline.sourceforge.net/). Edytuj poniższe polecenie, zastępując `ZOOKEEPER` z węzłem dozorcy wymienionych wcześniej, a następnie wprowadź polecenie:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Tworzenia tabel HBase. Wprowadź następujące polecenie:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Użyj SQLLine `!tables` polecenie, aby wyświetlić wszystkie tabele w bazie danych HBase. Wprowadź następujące polecenie:

    ```sqlline
    !tables
    ```

6. Wprowadź wartości w tabeli. Wprowadź następujące polecenie:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Zapytanie tabeli. Wprowadź następujące polecenie:

    ```sql
    SELECT * FROM Company;
    ```

8. Usuwanie rekordu. Wprowadź następujące polecenie:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Porzucić tę tabelę. Wprowadź następujące polecenie:

    ```hbase
    DROP TABLE Company;
    ```

10. Użyj SQLLine `!quit` polecenie, aby zakończyć działanie SQLLine. Wprowadź następujące polecenie:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego przewodnika Szybki Start możesz usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wiesz, jak uruchamiać zapytania bazy danych HBase w usłudze Azure HDInsight przy użyciu rozwiązania Apache Phoenix. Aby dowiedzieć się więcej na temat rozwiązania Apache Phoenix, następny artykuł zapewni lepszą badania.

> [!div class="nextstepaction"]
> [Oprogramowanie Apache Phoenix w HDInsight](../hdinsight-phoenix-in-hdinsight.md)
