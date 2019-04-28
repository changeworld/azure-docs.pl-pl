---
title: Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache
description: Dowiedz się, jak uruchamiać zapytania usługi Apache Hive za pomocą rozwiązania Zeppelin Apache.
keywords: Usługa hdinsight, hadoop, hive, interakcyjnych zapytań i funkcji LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 417e9f8ae78889374983bf77900ee00fa7fc6338
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098764"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache 

Klastry HDInsight interakcyjnego zapytania obejmują [Apache Zeppelin](https://zeppelin.apache.org/) notesów, do których można użyć do uruchamiania interakcyjnych zapytań Hive. W tym artykule opisano sposób uruchamiania za pomocą rozwiązania Zeppelin Apache [Apache Hive](https://hive.apache.org/) zapytania w usłudze Azure HDInsight. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klastra zapytania interakcyjnego HDInsight**. Zobacz [Utwórz klaster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) do utworzenia klastra HDInsight.  Upewnij się wybrać typ zapytania interaktywnego. 

## <a name="create-an-apache-zeppelin-note"></a>Tworzenie notatki Zeppelin Apache

1. Przejdź do następującego adresu URL:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Zastąp ciąg **CLUSTERNAME** nazwą klastra.

2. Wprowadź nazwę użytkownika Hadoop i hasło. Ze strony Zeppelin można utworzyć nowej notatki lub Otwórz istniejące informacje o. HiveSample zawiera kilka przykładowych zapytań Hive.  

    ![Zapytanie interakcyjne HDInsight zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Kliknij przycisk **Tworzenie nowej notatki**.
4. Wpisz lub wybierz poniższe wartości:

    - Nazwa Uwaga: Wprowadź nazwę uwagi.
    - Interpreter domyślne: Wybierz **JDBC**.

5. Kliknij przycisk **Tworzenie notatki**.
6. Uruchom następujące zapytanie programu Hive:

        %jdbc(hive)
        show tables

    ![Zeppelin interaktywnego zapytania HDInsight uruchamia zapytania](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **%Jdbc(hive)** instrukcji w pierwszym wierszu informuje Notes, aby użyć interpreter JDBC technologii Hive.

    Zapytanie zwraca jedną tabelę programu Hive o nazwie *hivesampletable*.

    Poniżej przedstawiono dwa więcej zapytań programu Hive, które mogą być uruchamiane względem tabeli hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Porównanie z tradycyjnych technologii Hive, wyniki zapytania wrócić musi szybciej.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu [Microsoft Power BI](https://powerbi.microsoft.com/).  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane zapytanie interakcyjne Apache Hive z usługą Power BI w usłudze Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./hdinsight-upload-data.md).
