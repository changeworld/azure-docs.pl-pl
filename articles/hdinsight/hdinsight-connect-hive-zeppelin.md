---
title: Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Zeppelin do uruchamiania zapytań Hive.
keywords: hdinsight, hadoop, hive, interakcyjne zapytania, LLAP
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: c8fe65d2eadaede1d99befbf76c4d06fab9598fc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin 

Klastry HDInsight interakcyjne zapytania obejmują notesów Zeppelin, które służy do wykonywania interakcyjnych zapytań Hive. W tym artykule Dowiedz się jak używać Zeppelin do uruchamiania zapytań Hive w usłudze Azure HDInsight. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Klaster HDInsight interakcyjne zapytania**. Zobacz [Utwórz klaster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) do tworzenia klastra usługi HDInsight.  Upewnij się wybrać typ zapytania interaktywnego. 

## <a name="create-a-zeppelin-note"></a>Tworzenie notatki Zeppelin

1. Przejdź do następującego adresu URL:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Zastąp ciąg **CLUSTERNAME** nazwą klastra.

2. Wprowadź nazwę użytkownika Hadoop i hasło. Na stronie Zeppelin możesz utworzyć nową notatkę lub otworzyć istniejące notatki. HiveSample zawiera kilka przykładowych zapytań Hive.  

    ![Zeppelin HDInsight interakcyjne zapytania](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Kliknij przycisk **Tworzenie nowej notatki**.
4. Wpisz lub wybierz poniższe wartości:

    - Nazwa Uwaga: Wprowadź nazwę uwagi.
    - Interpreter domyślne: Wybierz **JDBC**.

5. Kliknij przycisk **Tworzenie notatki**.
6. Uruchom następujące zapytanie Hive:

        %jdbc(hive)
        show tables

    ![HDInsight interakcyjne zeppelin uruchamia zapytania](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    **%Jdbc(hive)** instrukcji w pierwszym wierszu informuje, aby użyć interpreter Hive JDBC.

    Zapytanie zwraca jedną tabelę programu Hive o nazwie *hivesampletable*.

    Poniżej przedstawiono dwa więcej uruchamiane przed hivesampletable zapytania Hive. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Porównywanie z tradycyjnego Hive, wyniki zapytania wróć musi szybciej.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizuj dane z usługi HDInsight przy użyciu usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizacja gałęzi przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane interakcyjne zapytań Hive z usługą Power BI w usłudze Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Łączenie programu Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Hadoop za pomocą dodatku Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Łączenie do usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./hdinsight-upload-data.md).
