---
title: Wizualizuj dane interakcyjne zapytań Hive z usługą Power BI w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Microsoft Power BI do wizualizacji danych interakcyjne zapytania Hive przetwarzanych przez usługi Azure HDInsight.
keywords: hdinsight, hadoop, hive, interakcyjne zapytanie directquery interakcyjne hive, LLAP,
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
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: b8da1f17b9e477caf9031cf94ee14f3a181e247e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Wizualizuj dane interakcyjne zapytań Hive z usługi Microsoft Power BI przy użyciu zapytania bezpośredniego w usłudze Azure HDInsight

Dowiedz się, jak Microsoft Power BI nawiązać połączenia z klastrów Azure HDInsight interakcyjne zapytania i wizualizacji danych Hive za pomocą zapytania bezpośredniego. W tym samouczku można załadować danych z tabeli programu Hive hivesampletable do usługi Power BI. Tabela gałęzi zawiera niektóre dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapie world:

![HDInsight Power BI raportu mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Można wykorzystać [sterownika ODBC programu Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) do zaimportowania za pośrednictwem łącznika ODBC ogólnego w programie Power BI Desktop. Jednak nie zaleca dla obciążeń BI nieinterakcyjnym charakter aparatu zapytań Hive. [Łącznik zapytania interakcyjne HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) i [łącznika Spark w usłudze HDInsight](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepsze możliwości ich wydajność.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Klaster usługi HDInsight**. Klaster może być klastra usługi HDInsight przy użyciu Hive lub nowo wydanego klastra interakcyjne zapytania. Do tworzenia klastrów, zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Istnieje możliwość pobrania kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z usługi HDInsight

W tabeli Hive hivesampletable zawiera wszystkie klastry usługi HDInsight.

1. Zaloguj się do Power BI Desktop.
2. Kliknij przycisk **Home** , kliknij pozycję **Pobierz dane** z **danych zewnętrznych** wstążki, a następnie wybierz **więcej**.

    ![Otwórz dane HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Z **Pobierz dane** okienka, typ **hdinsight** w polu wyszukiwania. Jeśli nie widzisz **HDInsight zapytania interakcyjne (Beta)**, musisz zaktualizować do najnowszej wersji programu Power BI Desktop.
4. Kliknij przycisk **HDInsight zapytania interakcyjne (Beta)**, a następnie kliknij przycisk **Connect**.
5. Kliknij przycisk **Kontynuuj** zamknąć **łącznika Podgląd** okno dialogowe z ostrzeżeniem.
6. Z **zapytania interakcyjne HDInsight**, wybierz lub wprowadź następujące informacje:

    - **Serwer**: Wprowadź nazwę klastra interakcyjne kwerendy, na przykład *myiqcluster.azurehdinsight.net*.
    - **Baza danych**: W tym samouczku, wprowadź **domyślne**.
    - **Tryb łączności danych**: W tym samouczku, wybierz **DirectQuery**.

    ![Połącz zapytania bezpośredniego HDInsight interakcyjne zapytania zasilania analizy biznesowej](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Kliknij przycisk **OK**.
8. Wprowadź poświadczenia użytkownika HTTP, a następnie kliknij przycisk **OK**.  Domyślna nazwa użytkownika to **administratora**
9. W okienku po lewej stronie wybierz **hivesampletale**, a następnie kliknij przycisk **obciążenia**.

    ![HDInsight interakcyjne zapytania zasilania analizy biznesowej hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Wizualizuj dane

Kontynuuj w poprzedniej procedurze.

1. W okienku wizualizacje wybierz **mapy**.  Jest ikona globu.

    ![HDInsight Power BI dostosowuje raportu](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. W okienku pola wybierz **kraju** i **devicemake**. Widać danych wykreślić na mapie.
3. Rozwiń węzeł mapy.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizuj dane z usługi HDInsight przy użyciu usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj dane gałąź z usługi Microsoft Power BI przy użyciu interfejsu ODBC w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Hadoop za pomocą dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Łączenie do usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).
