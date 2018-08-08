---
title: Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin
description: Dowiedz się, jak uruchamiać zapytania Hive za pomocą rozwiązania Zeppelin.
keywords: Usługa hdinsight, hadoop, hive, interakcyjnych zapytań i funkcji LLAP
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: d4767c4d86d03827b0c055af41638988afd632a1
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595937"
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin 

Klastry HDInsight interakcyjnego zapytania obejmują z notesów Zeppelin używanych do uruchamiania interakcyjnych zapytań Hive. W tym artykule dowiesz się, jak uruchamiać zapytania Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klastra zapytania interakcyjnego HDInsight**. Zobacz [Utwórz klaster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) do utworzenia klastra HDInsight.  Upewnij się wybrać typ zapytania interaktywnego. 

## <a name="create-a-zeppelin-note"></a>Utwórz notatkę Zeppelin

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
W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj dane programu Hive z usługą Microsoft Power BI w usłudze Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z usługą Hadoop przy użyciu dodatku Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./hdinsight-upload-data.md).
