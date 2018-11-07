---
title: Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight
description: Microsoft Power BI umożliwia wizualizowanie danych usługi Azure HDInsight Interactive Query Hive
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 46b5b3436a33c3f83d85cfb02028759d53d214af
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245277"
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Wizualizuj dane Interactive Query Hive z usługą Microsoft Power BI za pomocą zapytania bezpośredniego w usłudze Azure HDInsight

W tym artykule opisano sposób połączenia usługi Microsoft Power BI z klastrami usługi Azure HDInsight interakcyjnych zapytań i wizualizowanie danych Apache Hive, za pomocą zapytania bezpośredniego. Podany przykład załaduje dane z tabeli programu Hive hivesampletable do usługi Power BI. W tej tabeli hivesampletable zawiera pewne dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapę świata:

![HDInsight usługi Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Możesz wykorzystać [sterownika Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) można zaimportować za pomocą ogólnego łącznika ODBC w programie Power BI Desktop. Jednak nie zaleca dla obciążeń analizy Biznesowej ze względu na charakter nieinterakcyjnych aparatu zapytań programu Hive. [Łącznik interaktywnego zapytania HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) i [łącznika usługi HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepsze możliwości ich wydajność.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klaster HDInsight**. Klaster może być klastra usługi HDInsight przy użyciu technologii Hive lub nowo wydanego klastra zapytania interakcyjnego. W celu tworzenia klastrów, zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Istnieje możliwość pobrania kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z HDInsight

W tej tabeli hivesampletable jest powiązana z wszystkimi klastrami HDInsight.

1. Zaloguj się do usługi Power BI Desktop.

2. Kliknij przycisk **Home** kliknij pozycję **Pobierz dane** z **dane zewnętrzne** wstążki, a następnie wybierz **więcej...** .

    ![Otwórz dane HDInsight usługi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
    
3. Z **Pobierz dane** okienko, typ **hdinsight** w polu wyszukiwania. Jeśli nie widzisz **HDInsight Interactive Query (wersja Beta)**, należy zaktualizować usługi Power BI Desktop do najnowszej wersji.

4. Wybierz **HDInsight Interactive Query (wersja Beta)**, a następnie wybierz pozycję **Connect**.

5. Wybierz **Kontynuuj** zamknąć **łącznik w wersji zapoznawczej** okno dialogowe z ostrzeżeniem.

6. Z **interaktywnego zapytania HDInsight**wybierz lub wprowadź następujące informacje:

    - **Serwer**: Wprowadź nazwę klastra zapytania interakcyjnego, na przykład *myiqcluster.azurehdinsight.net*.

    - **Baza danych**: W tym samouczku wprowadź **domyślne**.
    
    - **Tryb łączności danych**: na potrzeby tego samouczka wybierz **zapytania bezpośredniego**.

    ![Connect HDInsight interakcyjnych zapytań DirectQuery usługi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Kliknij przycisk **OK**.

8. Wprowadź poświadczenia użytkownika protokołu HTTP, a następnie kliknij przycisk **OK**. Domyślna nazwa użytkownika to **administratora**

9. W okienku po lewej stronie wybierz **hivesampletale**, a następnie kliknij przycisk **obciążenia**.

    ![HDInsight interakcyjnych zapytań usługi Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Wizualizowanie danych na mapie

Kontynuuj w poprzedniej procedurze.

1. W okienku wizualizacje wybierz **mapy**.  Jest ikona globu.

    ![HDInsight usługi Power BI dostosowuje raportu](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
    
2. W okienku pola wybierz **kraju** i **devicemake**. Widać danych wykreślić na mapie.

3. Rozwiń mapę.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu usługi Power BI.  Aby uzyskać więcej informacji na temat wizualizacji danych zobacz następujące artykuły:

* [Wizualizuj dane programu Hive z usługą Microsoft Power BI w usłudze Azure HDInsight przy użyciu interfejsu ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z usługą Hadoop przy użyciu dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).
