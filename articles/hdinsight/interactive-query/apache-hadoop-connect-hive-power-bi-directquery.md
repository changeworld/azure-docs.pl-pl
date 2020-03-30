---
title: Zobacz dane gałęzi interakcyjnej kwerendy w usłudze Power BI w usłudze Azure HDInsight
description: Wizualizacja danych gałęzi gałęzi zapytań interaktywnych za pomocą usługi Microsoft Power BI z usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367996"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Wizualizuj dane gałęzi apache programu Interactive Query za pomocą usługi Microsoft Power BI przy użyciu zapytania bezpośredniego w programie HDInsight

W tym artykule opisano sposób łączenia klastrów zapytań interaktywnych usługi Microsoft Power BI z usługą Azure HDInsight i wizualizowania danych gałęzi Apache przy użyciu kwerendy bezpośredniej. W poniższym przykładzie podano, że dane są ładowane `hivesampletable` z tabeli gałęzi do usługi Power BI. Tabela `hivesampletable` Hive zawiera niektóre dane użycia telefonu komórkowego. Następnie wykreślasz dane użycia na mapie świata:

![HdInsight Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Sterownik [Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) można wykorzystać do importowania za pośrednictwem ogólnego złącza ODBC w programie Power BI Desktop. Jednak nie jest zalecane dla obciążeń bi, biorąc pod uwagę nieinterakcyjny charakter aparatu zapytań hive. [Złącze interaktywne zapytanie HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) i [złącze HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepszym wyborem dla ich wydajności.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem przez ten artykuł, musisz mieć następujące elementy:

* **Klaster HDInsight**. Klaster może być klastrem HDInsight z gałęzią Apache lub nowo wydanym klastrem zapytań interaktywnych. Aby utworzyć klastry, zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Program Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Kopię można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z usługi HDInsight

Tabela `hivesampletable` Hive jest dostępna we wszystkich klastrach HDInsight.

1. Uruchom program Power BI Desktop.

2. Na pasku menu przejdź do **pozycji Strona główna** > **Pobierz dane** > **więcej...**.

    ![HDInsight Power BI Pobierz więcej danych](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. W oknie **Pobierz dane** wprowadź **hdinsight** w polu wyszukiwania.  

4. W wynikach wyszukiwania wybierz pozycję **INTERAKTYWNa kwerenda HDInsight**, a następnie wybierz pozycję **Połącz**.  Jeśli nie widzisz programu **HDInsight Interactive Query,** musisz zaktualizować program Power BI Desktop do najnowszej wersji.

5. Wybierz **pozycję Kontynuuj,** aby zamknąć okno dialogowe **Łączenie z usługą innej firmy.**

6. W oknie **Interaktywna kwerenda HDInsight** wprowadź następujące informacje, a następnie wybierz przycisk **OK:**

    |Właściwość | Wartość |
    |---|---|
    |Serwer |Wprowadź nazwę klastra, na przykład *myiqcluster.azurehdinsight.net*.|
    |baza danych |Wprowadź **wartość domyślną** dla tego artykułu.|
    |Tryb łączności danych |Wybierz **opcję Zapytanie bezpośrednie** dla tego artykułu.|

    ![Interaktywne zapytanie HDInsight, które łączy zapytanie bezpośrednie usługi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Wprowadź poświadczenia HTTP, a następnie wybierz pozycję **Połącz**. Domyślną nazwą użytkownika jest **admin**.

8. W oknie **Nawigator** w lewym okienku wybierz pozycję **hivesampletale**.

9. Wybierz **opcję Wczytaj** z okna głównego.

    ![Interaktywne zapytanie hdinsight Usługi Power BI jest rozjemczne](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Wizualizacja danych na mapie

Kontynuuj od ostatniej procedury.

1. W okienku Wizualizacje wybierz pozycję **Mapa**, ikonę kuli ziemskiej. W oknie głównym pojawi się mapa ogólna.

    ![Usługa HDInsight Power BI dostosowuje raport](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. W okienku Pola wybierz **kraj** i **urządzeniezmieje**. Mapa świata z punktami danych pojawia się w głównym oknie po kilku chwilach.

3. Rozwiń mapę.

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak wizualizować dane z usługi HDInsight przy użyciu usługi Microsoft Power BI.  Aby uzyskać więcej informacji na temat wizualizacji danych, zobacz następujące artykuły:

* [Wizualizuj dane gałęzi Apache za pomocą usługi Microsoft Power BI przy użyciu funkcji ODBC w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Użyj Apache Zeppelin, aby uruchamiać zapytania apache hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Podłącz program Excel do usługi HDInsight za pomocą sterownika ODBC firmy Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z apache Hadoop przy użyciu dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Połącz się z usługą Azure HDInsight i uruchom kwerendy hive Apache przy użyciu narzędzi usługi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Prześlij dane do usługi HDInsight](./../hdinsight-upload-data.md).
