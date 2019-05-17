---
title: Wizualizuj dane Interactive Query Hive z usługą Power BI w usłudze Azure HDInsight
description: Microsoft Power BI umożliwia wizualizowanie danych usługi Azure HDInsight Interactive Query Hive
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: fb4e16c8be5344c5b9947758b6a09845b470196d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801005"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Wizualizuj dane zapytanie interakcyjne Apache Hive z usługą Microsoft Power BI za pomocą zapytania bezpośredniego w usłudze Azure HDInsight

W tym artykule opisano sposób połączenia usługi Microsoft Power BI z klastrami usługi Azure HDInsight interakcyjnych zapytań i wizualizowanie danych Apache Hive, za pomocą zapytania bezpośredniego. Podany przykład służy do ładowania danych z `hivesampletable` tabelę programu Hive w usłudze Power BI. `hivesampletable` Tabelę programu Hive zawiera pewne dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapę świata:

![HDInsight usługi Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Możesz wykorzystać [sterownika ODBC programu Hive Apache](../hadoop/apache-hadoop-connect-hive-power-bi.md) można zaimportować za pomocą ogólnego łącznika ODBC w programie Power BI Desktop. Jednak nie zaleca dla obciążeń analizy Biznesowej ze względu na charakter nieinterakcyjnych aparatu zapytań programu Hive. [Łącznik interaktywnego zapytania HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) i [łącznika usługi HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepsze możliwości ich wydajność.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klaster HDInsight**. Klaster może być w klastrze usługi HDInsight przy użyciu Apache Hive lub nowo wydanego klastra zapytania interakcyjnego. W celu tworzenia klastrów, zobacz [Utwórz klaster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Istnieje możliwość pobrania kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z HDInsight

`hivesampletable` Tabelę programu Hive, który jest dostarczany z wszystkimi klastrami HDInsight.

1. Uruchom program Power BI Desktop.

2. Na pasku menu, przejdź do **Home** > **Pobierz dane** > **więcej...** .

    ![Otwórz dane HDInsight usługi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Z **Pobierz dane** oknie wprowadź **hdinsight** w polu wyszukiwania.  

4. W wynikach wyszukiwania wybierz **interaktywnego zapytania HDInsight**, a następnie wybierz pozycję **Connect**.  Jeśli nie widzisz **interaktywnego zapytania HDInsight**, należy zaktualizować usługi Power BI Desktop do najnowszej wersji.

5. Wybierz **Kontynuuj** zamknąć **łączenia się z usługą innej firmy** okna dialogowego.

6. W **interaktywnego zapytania HDInsight** okna, wprowadź następujące informacje, a następnie wybierz pozycję **OK**:

    |Właściwość | Wartość |
    |---|---|
    |Serwer |Wprowadź nazwę klastra, na przykład *myiqcluster.azurehdinsight.net*.|
    |Database (Baza danych) |Wprowadź **domyślne** na potrzeby tego artykułu.|
    |Tryb łączności danych |Wybierz **zapytania bezpośredniego** na potrzeby tego artykułu.|

    ![Connect HDInsight interakcyjnych zapytań DirectQuery usługi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Wprowadź poświadczenia protokołu HTTP, a następnie wybierz pozycję **Connect**. Domyślna nazwa użytkownika jest **administratora**.

8. Z **Nawigator** oknie w lewym okienku wybierz **hivesampletale**.

9. Wybierz **obciążenia** w głównym oknie.

    ![HDInsight interakcyjnych zapytań usługi Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Wizualizowanie danych na mapie

Kontynuuj w poprzedniej procedurze.

1. W okienku wizualizacje wybierz **mapy**, ikona globu. Ogólna mapa pojawi się w głównym oknie.

    ![HDInsight usługi Power BI dostosowuje raportu](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. W okienku pola wybierz **kraju** i **devicemake**. Po kilku chwilach mapę świata z punktami danych pojawia się w głównym oknie.

3. Rozwiń mapę.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu usługi Microsoft Power BI.  Aby uzyskać więcej informacji na temat wizualizacji danych zobacz następujące artykuły:

* [Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight przy użyciu interfejsu ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).
