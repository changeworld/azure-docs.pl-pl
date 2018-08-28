---
title: Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight
description: Dowiedz się, jak wizualizować dane z interaktywną technologią Hive zapytania przetwarzanych przez usługi Azure HDInsight za pomocą usługi Microsoft Power BI.
keywords: Usługa hdinsight, hadoop, hive, interaktywne zapytania interaktywne usługi hive, LLAP, zapytanie bezpośrednie
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/14/2018
ms.openlocfilehash: 1779151f3768542c08ea62d2c9109d4cd66a1e3f
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041048"
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Wizualizuj dane Interactive Query Hive z usługą Microsoft Power BI za pomocą zapytania bezpośredniego w usłudze Azure HDInsight

Dowiedz się, jak połączyć program Microsoft Power BI z klastrami usługi Azure HDInsight interakcyjnych zapytań i wizualizuj dane programu Hive za pomocą zapytania bezpośredniego. W tym samouczku załadujesz dane z tabeli programu Hive hivesampletable do usługi Power BI. W tej tabeli zawiera pewne dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapę świata:

![HDInsight usługi Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Możesz wykorzystać [sterownika Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) można zaimportować za pomocą ogólnego łącznika ODBC w programie Power BI Desktop. Jednak nie zaleca dla obciążeń analizy Biznesowej ze względu na charakter nieinterakcyjnych aparatu zapytań programu Hive. [Łącznik interaktywnego zapytania HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) i [łącznika usługi HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepsze możliwości ich wydajność.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klaster HDInsight**. Klaster może być klastra usługi HDInsight przy użyciu technologii Hive lub nowo wydanego klastra zapytania interakcyjnego. W celu tworzenia klastrów, zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Istnieje możliwość pobrania kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z HDInsight

W tej tabeli hivesampletable jest powiązana z wszystkimi klastrami HDInsight.

1. Zaloguj się do usługi Power BI Desktop.
2. Kliknij przycisk **Home** kliknij pozycję **Pobierz dane** z **dane zewnętrzne** wstążki, a następnie wybierz **więcej**.

    ![Otwórz dane HDInsight usługi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Z **Pobierz dane** okienko, typ **hdinsight** w polu wyszukiwania. Jeśli nie widzisz **HDInsight Interactive Query (wersja Beta)**, należy zaktualizować usługi Power BI Desktop do najnowszej wersji.
4. Kliknij przycisk **HDInsight Interactive Query (wersja Beta)**, a następnie kliknij przycisk **Connect**.
5. Kliknij przycisk **Kontynuuj** zamknąć **łącznik w wersji zapoznawczej** okno dialogowe z ostrzeżeniem.
6. Z **interaktywnego zapytania HDInsight**wybierz lub wprowadź następujące informacje:

    - **Serwer**: Wprowadź nazwę klastra zapytania interakcyjnego, na przykład *myiqcluster.azurehdinsight.net*.
    - **Baza danych**: W tym samouczku wprowadź **domyślne**.
    - **Tryb łączności danych**: na potrzeby tego samouczka wybierz **zapytania bezpośredniego**.

    ![Łączenie zapytania bezpośredniego HDInsight interakcyjnych zapytań usługi power bi](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Kliknij przycisk **OK**.
8. Wprowadź poświadczenia użytkownika protokołu HTTP, a następnie kliknij przycisk **OK**.  Domyślna nazwa użytkownika to **administratora**
9. W okienku po lewej stronie wybierz **hivesampletale**, a następnie kliknij przycisk **obciążenia**.

    ![HDInsight interakcyjnych zapytań usługi power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Wizualizowanie danych

Kontynuuj w poprzedniej procedurze.

1. W okienku wizualizacje wybierz **mapy**.  Jest ikona globu.

    ![HDInsight usługi Power BI dostosowuje raportu](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. W okienku pola wybierz **kraju** i **devicemake**. Widać danych wykreślić na mapie.
3. Rozwiń mapę.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wizualizuj dane programu Hive z usługą Microsoft Power BI w usłudze Azure HDInsight przy użyciu interfejsu ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z usługą Hadoop przy użyciu dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).
