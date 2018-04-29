---
title: BI Spark przy użyciu narzędzi do wizualizacji danych w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Użycie narzędzi do wizualizacji danych analytics przy użyciu analizy Biznesowej programu Apache Spark w klastrach HDInsight
keywords: Platforma spark jest Apache spark analizy biznesowej, spark analizy biznesowej, spark wizualizację danych, analiza biznesowa
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 0e728e17a64acd990b301bac8139c7bb395a3098
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI z usługą Azure HDInsight przy użyciu narzędzi do wizualizacji danych

Dowiedz się, jak używać [Microsoft Power BI](http://powerbi.microsoft.com) do wizualizacji danych w klastrze Apache Spark w usłudze Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* **Ukończenie tego artykułu [uruchamianie interakcyjnych zapytań w klastrze Spark w usłudze HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) i [subskrypcji wersji próbnej usługi Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcjonalnie).


## <a name="hivetable"></a>Sprawdź dane

Notesu Jupyter, który został utworzony w [poprzedniego samouczek](apache-spark-load-data-run-query.md) zawiera kod w celu utworzenia `hvac` tabeli. Ta tabela jest oparta na plik CSV dostępne na wszystkich klastrach HDInsight Spark w **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Użyj poniższej procedury, aby sprawdzić dane.

1. Z notesu Jupyter Wklej następujący kod, a następnie naciśnij klawisz **SHIFT + ENTER**. Kod sprawdza, czy istnienie tabel.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Dane wyjściowe wyglądają następująco:

    ![Pokaż tabele w łączniku Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Jeśli został zamknięty przed rozpoczęciem tego samouczka notesu `hvactemptable` jest czyszczona, więc nie znajduje się w danych wyjściowych.
    Tylko Hive tabel, które są przechowywane w potrzeby magazynu metadanych (wskazywanym przez **False** w obszarze **isTemporary** kolumny) można uzyskać z narzędzi do analizy Biznesowej. W tym samouczku, możesz nawiązać **hvac** tabelę, która zostanie utworzona.

2. W pustej komórce Wklej następujący kod, a następnie naciśnij klawisz **SHIFT + ENTER**. Kod sprawdza dane w tabeli.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Dane wyjściowe wyglądają następująco:

    ![Pokaż wiersze z tabeli hvac w łączniku Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. W menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Zamknij aby zwolnić zasoby. 















## <a name="powerbi"></a>Przy użyciu usługi Power BI

W tej sekcji umożliwiają usługi Power BI tworzenie wizualizacji, raporty i pulpity nawigacyjne na podstawie danych klastra Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop
Pierwszym krokiem w pracy z platformy Spark jest Połącz się z klastrem w programie Power BI Desktop, ładowanie danych z klastra i tworzenia podstawowych wizualizacji na podstawie tych danych.

> [!NOTE]
> Łącznik przedstawiona w tym artykule jest obecnie w przeglądzie. Podaj opinię, masz za pośrednictwem [Power BI społeczności](https://community.powerbi.com/) lokacji lub [Power BI pomysły](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Otwórz [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Z **Home** , kliknij pozycję **Pobierz dane**, następnie **więcej**.

    ![Pobieranie danych do programu Power BI Desktop z Apache Spark w usłudze HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "pobieranie danych do usługi Power BI z Apache Spark w usłudze BI")


2. Wprowadź `Spark` w polu wyszukiwania wybierz **Azure HDInsight Spark (Beta)**, a następnie kliknij przycisk **Connect**.

    ![Pobieranie danych do usługi Power BI z Apache Spark w usłudze BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "pobieranie danych do usługi Power BI z Apache Spark w usłudze BI")

3. Podaj adres URL klastra (w postaci `mysparkcluster.azurehdinsight.net`), wybierz pozycję **DirectQuery**, a następnie kliknij przycisk **OK**.

    Umożliwia trybie połączenie danych z platformy Spark. Jeśli używasz zapytania bezpośredniego, zmiany są uwzględniane w raportach bez odświeżania cały zestaw danych. W przypadku importowania danych należy odświeżyć zestawu danych, aby zobaczyć zmiany. Aby uzyskać więcej informacji o tym, jak i kiedy korzystać z zapytania bezpośredniego, zobacz [przy użyciu zapytania bezpośredniego w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Wprowadź dane konta logowania usługi HDInsight, a następnie kliknij przycisk **Connect**. Domyślna nazwa konta jest *admin*.

5. Wybierz `hvac` tabeli, aby wyświetlić podgląd danych, a następnie kliknij **obciążenia**.

    ![Platforma Spark jest klastra nazwę użytkownika i hasło](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark klastra nazwę użytkownika i hasło")

    Power BI Desktop zawiera informacje niezbędne do połączenia się Spark klastra i ładowanie danych z `hvac` tabeli. Tabeli i jej kolumn są wyświetlane w **pola** okienka.  Zobacz poniższy zrzut ekranu:

6. Wizualizuj różnica między temperatury docelowych i rzeczywistego temperatury dla każdego tworzenia: 

    1. W **WIZUALIZACJE** okienku wybierz **obszaru wykresu**. 
    2. Przeciągnij **BuildingID** do **osi**i przeciągnij **ActualTemp** i **TargetTemp** pól do **wartość**.

        ![Tworzenie wizualizacji danych przy użyciu Apache Spark w usłudze BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "wizualizacje danych utworzyć Spark przy użyciu Apache Spark w usłudze BI")

        Diagram wygląda następująco:

        ![Tworzenie wizualizacji danych przy użyciu Apache Spark w usłudze BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "wizualizacje danych utworzyć Spark przy użyciu Apache Spark w usłudze BI")

        Domyślnie wizualizację zawiera sumę dla **ActualTemp** i **TargetTemp**. Kliknij strzałkę w dół obok pozycji **ActualTemp** i **TragetTemp** w okienku wizualizacje widać **suma** jest zaznaczone.

    3. Kliknij strzałkę w dół obok pozycji **ActualTemp** i **TragetTemp** wybierz w okienku wizualizacje **średni** uzyskanie średniej rzeczywistego i temperatury docelowy dla każdego Tworzenie.

        ![Tworzenie wizualizacji danych przy użyciu Apache Spark w usłudze BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "wizualizacje danych utworzyć Spark przy użyciu Apache Spark w usłudze BI")

        Twoje wizualizacji danych jest podobny do przedstawionego na zrzucie ekranu. Przesuń kursor nad wizualizacji uzyskanie etykietki narzędzi z właściwymi danymi.

        ![Tworzenie wizualizacji danych przy użyciu Apache Spark w usłudze BI Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "wizualizacje danych utworzyć Spark przy użyciu Apache Spark w usłudze BI")

7. Kliknij przycisk **pliku** następnie **zapisać**, a następnie wprowadź nazwę `BuildingTemperature.pbix` pliku. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikowanie raportu z usługą Power BI (opcjonalnie)

Usługi Power BI pozwala na udostępnianie raporty i pulpity nawigacyjne w Twojej organizacji. W tej sekcji należy najpierw opublikować zestawu danych i raportu. Następnie można przypiąć raport do pulpitu nawigacyjnego. Pulpity nawigacyjne są zwykle używane do skupić się na podzbiór danych w raporcie; masz tylko jedną wizualizacji w raporcie, ale nadal jest użyteczne kroków.

1. Otwórz program Power BI Desktop.
2. Z **Home** , kliknij pozycję **publikowania**.

    ![Publikowanie z Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "publikowanie z Power BI Desktop")

2. Wybierz obszar roboczy, aby opublikować swojego zestawu danych i raport do, a następnie kliknij przycisk **wybierz**. Na poniższej ilustracji, domyślnie **Mój obszar roboczy** jest zaznaczone.

    ![Wybierz obszar roboczy, aby opublikować zestawu danych i raportów do](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "wybierz obszar roboczy do publikowania zestawu danych i raportów do") 

3. Po publikowanie zakończyło się pomyślnie, kliknij przycisk **Otwórz "BuildingTemperature.pbix" w usłudze Power BI**.

    ![Publikowanie sukces, kliknij, aby wprowadzić poświadczenia](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "opublikować sukces, kliknij, aby wprowadzić poświadczenia") 

4. W usłudze Power BI, kliknij przycisk **wprowadź poświadczenia**.

    ![Wprowadź poświadczenia w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "wprowadzić poświadczenia w usłudze Power BI")

5. Kliknij przycisk **Edycja poświadczeń**.

    ![Edytowanie poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edycja poświadczeń w usłudze Power BI")

6. Wprowadź dane konta logowania usługi HDInsight, a następnie kliknij przycisk **Zaloguj**. Domyślna nazwa konta jest *admin*.

    ![Zaloguj się do klastra Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Zaloguj się do klastra Spark")

7. W okienku po lewej stronie, przejdź do **obszarów roboczych** > **Mój obszar roboczy** > **raporty**, następnie kliknij przycisk **BuildingTemperature**.

    ![Raport na liście raportów w lewym okienku](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "raportu na liście raportów w okienku po lewej stronie")

    Powinno również zostać wyświetlone **BuildingTemperature** kategorii **zestawów danych** w okienku po lewej stronie.

    Element wizualny, który utworzono w programie Power BI Desktop jest teraz dostępna w usłudze Power BI. 

8. Umieść kursor nad wizualizacji, a następnie kliknij ikonę przypinania w prawym górnym rogu.

    ![Raport w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "raportu w usłudze Power BI")

9. Wybierz pozycję "Nowy pulpit nawigacyjny", wprowadź nazwę `Building temperature`, następnie kliknij przycisk **numeru Pin**.

    ![Przypnij do pulpitu nawigacyjnego nowy](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Przypnij do pulpitu nawigacyjnego nowy")

10. W raporcie, kliknij przycisk **przejdź do pulpitu nawigacyjnego**. 

Wizualny jest przypięta do pulpitu nawigacyjnego — możesz dodawać innych elementach wizualnych do raportu i przypiąć je do tej samej pulpitu nawigacyjnego. Aby uzyskać więcej informacji o raporty i pulpity nawigacyjne, zobacz [raporty w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)i [pulpitów nawigacyjnych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>Kolejne kroki

Do tej pory przedstawiono sposób tworzenia klastra, Utwórz Spark ramek danych wykonać zapytania o dane i następnie dostęp do danych z narzędzi do analizy Biznesowej. Teraz można przeglądać instrukcje dotyczące zarządzania zasobami klastra i debugowanie zadań uruchomionych w klastrze Spark w usłudze HDInsight.

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

