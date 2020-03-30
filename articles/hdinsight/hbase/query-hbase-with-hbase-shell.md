---
title: 'Szybki start: Apache HBase w usłudze Azure HDInsight — powłoka HBase'
description: W tym przewodniku Szybki start dowiesz się, jak używać powłoki Apache HBase Shell do uruchamiania zapytań Apache HBase.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371073"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Szybki start: Apache HBase w usłudze Azure HDInsight z powłoką HBase

W tym przewodniku Szybki start dowiesz się, jak utworzyć tabelę HBase, wstawić dane, a następnie zbadać tabelę za pomocą aplikacji Apache HBase Shell.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache HBase. Zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md) w celu utworzenia klastra HDInsight.  Upewnij się, że wybierzesz typ klastra **HBase.**

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Tworzenie tabeli i manipulowanie danymi

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

W UBase (implementacja [Cloud BigTable)](https://cloud.google.com/bigtable/)te same dane wyglądają następująco:

![HdInsight Apache HBase BigTable danych](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Za pomocą funkcji SSH można łączyć się z klastrami HBase, a następnie używać powłoki Apache HBase shell do tworzenia tabel HBase, wstawiania danych i danych zapytań.

1. Użyj `ssh` polecenia, aby połączyć się z klastrem HBase. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Użyj `hbase shell` polecenia, aby uruchomić powłokę interaktywną HBase. Wprowadź następujące polecenie w połączeniu SSH:

    ```bash
    hbase shell
    ```

3. Polecenie `create` służy do tworzenia tabeli bazy danych HBase z rodzinami dwukolumnowym. Wprowadź następujące polecenie:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Użyj `list` polecenia, aby wyświetlić listę wszystkich tabel w bazie danych HBase. Wprowadź następujące polecenie:

    ```hbase
    list
    ```

5. Polecenie `put` służy do wstawiania wartości w określonej kolumnie w określonym wierszu w określonej tabeli. Wprowadź następujące polecenie:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Użyj `scan` polecenia, aby `Contacts` zeskanować i zwrócić dane tabeli. Wprowadź następujące polecenie:

    ```hbase
    scan 'Contacts'
    ```

7. Polecenie `get` służy do pobierania zawartości wiersza. Wprowadź następujące polecenie:

    ```hbase
    get 'Contacts', '1000'
    ```

    Widzisz podobne wyniki jako `scan` przy użyciu polecenia, ponieważ istnieje tylko jeden wiersz.

8. Polecenie `delete` służy do usuwania wartości komórki w tabeli. Wprowadź następujące polecenie:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Użyj `disable` polecenia, aby wyłączyć tabelę. Wprowadź następujące polecenie:

    ```hbase
    disable 'Contacts'
    ```

10. Użyj `drop` polecenia, aby upuścić tabelę z bazy danych HBase. Wprowadź następujące polecenie:

    ```hbase
    drop 'Contacts'
    ```

11. Użyj `exit` polecenia, aby zatrzymać powłokę interaktywną HBase. Wprowadź następujące polecenie:

    ```hbase
    exit
    ```

Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [Wprowadzenie do projektu schematu Bazy danych Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Więcej poleceń bazy danych HBase można znaleźć w [Podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób tworzenia tabeli HBase, wstawiania danych, a następnie wykonywania zapytań do tabeli za pomocą aplikacji Apache HBase Shell. Aby dowiedzieć się więcej o danych przechowywanych w bazie danych HBase, w następnym artykule pokazano, jak wykonywać zapytania za pomocą platformy Apache Spark.

> [!div class="nextstepaction"]
> [Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark](../hdinsight-using-spark-query-hbase.md)