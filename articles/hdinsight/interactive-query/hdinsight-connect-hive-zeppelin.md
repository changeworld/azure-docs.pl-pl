---
title: 'Szybki start: Wykonywanie zapytania usługi Apache Hive w usłudze Azure HDInsight — Apache Zeppelin'
description: Dowiedz się, jak uruchamiać zapytania usługi Apache Hive za pomocą rozwiązania Zeppelin Apache.
keywords: Usługa hdinsight, hadoop, hive, interakcyjnych zapytań i funkcji LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: f4b8495646e83005dc48e8a729a0e5987b832721
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801039"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Szybki start: Wykonywanie zapytania usługi Apache Hive w usłudze Azure HDInsight przy użyciu rozwiązania Apache Zeppelin

W tym przewodniku Szybki Start dowiesz się, jak uruchomić za pomocą rozwiązania Zeppelin Apache [Apache Hive](https://hive.apache.org/) zapytania w usłudze Azure HDInsight. Klastry HDInsight interakcyjnego zapytania obejmują [Apache Zeppelin](https://zeppelin.apache.org/) notesów, do których można użyć do uruchamiania interakcyjnych zapytań Hive.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Klaster usługi HDInsight interakcyjnych zapytań. Zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) do tworzenia klastra usługi HDInsight.  Upewnij się wybrać **Interactive Query** typ klastra.

## <a name="create-an-apache-zeppelin-note"></a>Tworzenie notatki Zeppelin Apache

1. Zastąp `CLUSTERNAME` nazwą klastra w następujący adres URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Następnie wprowadź adres URL w przeglądarce sieci web.

2. Wprowadź nazwę użytkownika logowania klastra i hasło. Ze strony Zeppelin można utworzyć nowej notatki lub Otwórz istniejące informacje o. **HiveSample** zawiera kilka przykładowych zapytań Hive.  

    ![Zapytanie interakcyjne HDInsight zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Wybierz **Tworzenie nowej notatki**.

4. Z **Tworzenie nowej notatki** okno dialogowe, wpisz lub wybierz następujące wartości:

    - Nazwa Uwaga: Wprowadź nazwę uwagi.
    - Interpreter domyślne: Wybierz **jdbc** z listy rozwijanej.

5. Wybierz **Tworzenie notatki**.

6. Wprowadź następujące zapytanie programu Hive w sekcji kodu, a następnie naciśnij klawisz **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Zeppelin interaktywnego zapytania HDInsight uruchamia zapytania](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **%Jdbc(hive)** instrukcji w pierwszym wierszu informuje Notes, aby użyć interpreter JDBC technologii Hive.

    Zapytanie zwraca jedną tabelę programu Hive o nazwie **hivesampletable**.

    Poniżej przedstawiono dwa dodatkowe zapytań programu Hive, które mogą być uruchamiane względem **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Porównanie z tradycyjnych technologii Hive, wyniki zapytania wrócić musi szybciej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego przewodnika Szybki Start możesz usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usunąć klaster usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start przedstawiono sposób uruchamiania zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache. Aby dowiedzieć się więcej na temat zapytania programu Hive, następny artykuł będzie pokazują, jak do wykonywania zapytań w programie Visual Studio.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="see-also"></a>Zobacz także

* [Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane zapytanie interakcyjne Apache Hive z usługą Power BI w usłudze Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](../hdinsight-upload-data.md).
