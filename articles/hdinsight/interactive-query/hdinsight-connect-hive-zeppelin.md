---
title: 'Szybki start: Gałąź Apache w usłudze Azure HDInsight z Apache Zeppelin'
description: W tym przewodniku Szybki start dowiesz się, jak używać apache zeppelin do uruchamiania zapytań Apache Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79367928"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Szybki start: wykonywanie zapytań hive apache w usłudze Azure HDInsight za pomocą apache Zeppelin

W tym przewodniku Szybki start dowiesz się, jak używać apache zeppelin do uruchamiania zapytań [hive Apache](https://hive.apache.org/) w usłudze Azure HDInsight. Klastry interaktywnych zapytań HDInsight obejmują notesy [Apache Zeppelin,](https://zeppelin.apache.org/) których można używać do uruchamiania interaktywnych zapytań hive.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Interaktywny klaster zapytań USŁUGI HDInsight. Zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md) w celu utworzenia klastra HDInsight.  Upewnij się, że wybierzesz typ klastra **kwerendinterakcyjnych.**

## <a name="create-an-apache-zeppelin-note"></a>Tworzenie notatki Apache Zeppelin

1. Zamień `CLUSTERNAME` na nazwę klastra `https://CLUSTERNAME.azurehdinsight.net/zeppelin`w następującym adresie URL . Następnie wprowadź adres URL w przeglądarce internetowej.

2. Wprowadź nazwę użytkownika i hasło logowania do klastra. Na stronie Zeppelin możesz utworzyć nową notatkę lub otworzyć istniejące notatki. **HiveSample** zawiera kilka przykładowych zapytań hive.  

    ![Interaktywne zapytanie HDInsight zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Wybierz **pozycję Utwórz nową notatkę**.

4. W oknie dialogowym **Tworzenie nowej notatki** wpisz lub wybierz następujące wartości:

    - Uwaga Nazwa: Wprowadź nazwę notatki.
    - Domyślny interpreter: Wybierz **jdbc** z listy rozwijanej.

5. Wybierz **pozycję Utwórz notatkę**.

6. Wprowadź następującą kwerendę hive w sekcji kodu, a następnie naciśnij **klawisze Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Interaktywne zapytanie HDInsight zeppelin uruchamia kwerendę](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Instrukcja **%jdbc(hive)** w pierwszym wierszu informuje notes o użyciu interpretera JDBC hive.

    Kwerenda zwraca jedną tabelę hive o nazwie **hivesampletable**.

    Oto dwa dodatkowe zapytania hive, które można uruchomić względem **pouci:**

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    W porównaniu do tradycyjnej gałęzi, wyniki kwerendy wrócić musi szybciej.

### <a name="additional-examples"></a>Więcej przykładów

1. Utwórz tabelę. Wykonaj poniższy kod w notesie Zeppelin:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Załaduj dane do nowej tabeli. Wykonaj poniższy kod w notesie Zeppelin:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Wstawianie pojedynczego rekordu. Wykonaj poniższy kod w notesie Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Zapoznaj się z [podręcznikiem języka gałęzi, aby](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) uzyskać dodatkową składnię.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak używać apache zeppelin do uruchamiania zapytań hive Apache w usłudze Azure HDInsight. Aby dowiedzieć się więcej o zapytaniach hive, w następnym artykule pokaże, jak wykonać kwerendy z visual studio.

> [!div class="nextstepaction"]
> [Łączenie się z usługą Azure HDInsight i uruchamianie zapytań gałęzi Apache przy użyciu narzędzi usługi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
