---
title: 'Szybki Start: Apache Hive w usłudze Azure HDInsight z usługą Apache Zeppelin'
description: W tym przewodniku szybki start dowiesz się, jak uruchamiać Apache Hive zapytania przy użyciu oprogramowania Apache Zeppelin.
keywords: HDInsight, Hadoop, Hive, zapytanie interaktywne, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494286"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Szybki Start: wykonywanie zapytań Apache Hive w usłudze Azure HDInsight przy użyciu platformy Apache Zeppelin

W tym przewodniku szybki start dowiesz się, jak używać oprogramowania Apache Zeppelin do uruchamiania zapytań [Apache Hive](https://hive.apache.org/) w usłudze Azure HDInsight. Interaktywne klastry zapytań usługi HDInsight obejmują notesy [Apache Zeppelin](https://zeppelin.apache.org/) , których można używać do uruchamiania interakcyjnych zapytań Hive.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

HDInsight An interaktywny klaster zapytań. Zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) , aby utworzyć klaster usługi HDInsight.  Upewnij się, że wybrano typ klastra **zapytań interaktywnych** .

## <a name="create-an-apache-zeppelin-note"></a>Tworzenie notatki Apache Zeppelin

1. Zastąp `CLUSTERNAME` nazwą klastra w poniższym adresie URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Następnie wprowadź adres URL w przeglądarce sieci Web.

2. Wprowadź nazwę użytkownika i hasło logowania do klastra. Na stronie Zeppelin można utworzyć nową notatkę lub otworzyć istniejące notatki. **HiveSample** zawiera przykładowe zapytania Hive.  

    ![Zapytanie interaktywne Zeppelin usługi HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Wybierz pozycję **Utwórz nową notatkę**.

4. W oknie dialogowym **Tworzenie nowej notatki** wpisz lub wybierz następujące wartości:

    - Nazwa komentarza: Wprowadź nazwę notatki.
    - Interpreter domyślny: wybierz pozycję **JDBC** z listy rozwijanej.

5. Wybierz pozycję **Utwórz notatkę**.

6. Wprowadź następujące zapytanie Hive w sekcji Code, a następnie naciśnij klawisze **SHIFT + ENTER**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Zapytanie dotyczące uruchamiania interakcyjnych zapytań usługi HDInsight Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Instrukcja **% JDBC (Hive)** w pierwszym wierszu instruuje Notes, aby korzystał z interpretera JDBC programu Hive.

    Zapytanie zwraca jedną tabelę programu Hive o nazwie **hivesampletable**.

    Poniżej znajdują się dwa dodatkowe zapytania Hive, które można uruchomić względem **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Wyniki zapytania są porównywane z tradycyjną gałęzią.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono, jak używać platformy Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight. Aby dowiedzieć się więcej o zapytaniach Hive, w następnym artykule przedstawiono sposób wykonywania zapytań w programie Visual Studio.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
