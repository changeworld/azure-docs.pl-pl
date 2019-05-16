---
title: 'Szybki start: Zapytanie Apache HBase w usłudze Azure HDInsight — powłoki HBase'
description: Dowiedz się, jak korzystanie z powłoki HBase Apache do uruchamiania zapytań bazy danych Apache HBase.
keywords: Usługa hdinsight, hadoop, HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: hrasheed
ms.openlocfilehash: 41b16e63522a02cc16eb4dac2cbcc8e6540aceaf
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65552086"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Szybki start: Zapytanie bazy danych Apache HBase w usłudze Azure HDInsight przy użyciu powłoki HBase

W tym przewodniku Szybki Start dowiesz się, jak korzystanie z powłoki HBase Apache do tworzenia tabel HBase, wstawiania danych i następnie utworzyć zapytanie względem tabeli.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster bazy danych Apache HBase. Zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) do tworzenia klastra usługi HDInsight.  Upewnij się, możesz wybrać **HBase** typ klastra.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Utwórz tabelę i manipulowanie danymi

Dla większości użytkowników dane są wyświetlane w formacie tabelarycznym:

![Dane tabelaryczne usługi HDInsight HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

W bazie danych HBase (implementacja [BigTable chmury](https://cloud.google.com/bigtable/)), te same dane wygląda następująco:

![Dane BigTable usługi HDInsight HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Można połączyć się z klastrami HBase za pomocą protokołu SSH, a następnie korzystanie z powłoki HBase Apache do tworzenia tabel bazy danych HBase, wstawiania danych i zapytania o dane.

1. Użyj `ssh` polecenie, aby nawiązać połączenie z klastrem HBase. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra, a następnie wpisz polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Użyj `hbase shell` polecenia do uruchamiania interaktywnej powłoki HBase. Wprowadź następujące polecenie, w związku z protokołu SSH:

    ```bash
    hbase shell
    ```

3. Użyj `create` polecenie, aby utworzyć tabelę HBase z dwiema rodzinami kolumn. Wprowadź następujące polecenie:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Użyj `list` polecenie, aby wyświetlić wszystkie tabele w bazie danych HBase. Wprowadź następujące polecenie:

    ```hbase
    list
    ```

5. Użyj `put` polecenie, aby wstawić wartości w określonej kolumnie w określonym wierszu określonej tabeli. Wprowadź następujące polecenie:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Użyj `scan` polecenia do skanowania i zwracać `Contacts` tabeli danych. Wprowadź następujące polecenie:

    ```hbase
    scan 'Contacts'
    ```

7. Użyj `get` polecenie, aby pobrać zawartość wiersza. Wprowadź następujące polecenie:

    ```hbase
    get 'Contacts', '1000'
    ```

    Zostaną wyświetlone wyniki podobne, używając `scan` polecenia, ponieważ istnieje tylko jeden wiersz.

8. Użyj `delete` polecenie, aby usunąć wartość komórki w tabeli. Wprowadź następujące polecenie:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Użyj `disable` polecenie, aby wyłączyć w tabeli. Wprowadź następujące polecenie:

    ```hbase
    disable 'Contacts'
    ```

10. Użyj `drop` polecenie, aby usunąć tabelę z bazy danych HBase. Wprowadź następujące polecenie:

    ```hbase
    drop 'Contacts'
    ```

11. Użyj `exit` polecenie, aby zatrzymać interaktywnej powłoki HBase. Wprowadź następujące polecenie:

    ```hbase
    exit
    ```

Aby uzyskać więcej informacji na temat schematu tabeli HBase, zobacz [wprowadzenie do projektu schematu HBase Apache](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Aby uzyskać więcej poleceń HBase, zobacz [podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego przewodnika Szybki Start możesz usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start przedstawiono sposób korzystanie z powłoki HBase Apache do tworzenia tabel HBase, wstawiania danych i następnie utworzyć zapytanie względem tabeli. Aby dowiedzieć się więcej na temat danych przechowywanych w bazie danych HBase, następny artykuł będzie pokazują, jak można wykonać zapytania z platformy Apache Spark.

> [!div class="nextstepaction"]
> [Platforma Apache Spark umożliwia odczytywanie i zapisywanie danych Apache HBase](../hdinsight-using-spark-query-hbase.md)