---
title: 'Samouczek: analizowanie danych platformy Apache Spark przy użyciu usługi Power BI w usłudze Azure HDInsight '
description: Używanie usługi Microsoft Power BI do wizualizacji danych platformy Apache Spark przechowywanych w klastrach usługi HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: ed7beeadc0a550a28d1f936702aabeb45823b677
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699609"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Samouczek: analizowanie danych platformy Apache Spark przy użyciu usługi Power BI w usłudze HDInsight 

Dowiedz się, jak używać usługi [Microsoft Power BI](https://powerbi.microsoft.com/) do wizualizacji danych w klastrze platformy [Apache Spark](https://spark.apache.org/) w usłudze [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wizualizowanie danych platformy Spark przy użyciu usługi Power BI

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* **Ukończenie artykułu [Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [program Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) i [subskrypcja wersji próbnej usługi Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcjonalnie).


## <a name="verify-the-data"></a>Weryfikowanie danych

[Notes Jupyter](https://jupyter.org/) utworzony w ramach [poprzedniego samouczka](apache-spark-load-data-run-query.md) zawiera kod do utworzenia tabeli `hvac`. Ta tabela jest oparta na pliku CSV dostępnym we wszystkich klastrach HDInsight Spark w lokalizacji **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Postępuj zgodnie z następującą procedurą, aby sprawdzić dane.

1. Z notesu Jupyter wklej poniższy kod, a następnie naciśnij klawisze **SHIFT + ENTER**. Kod sprawdza obecność tabel.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Dane wyjściowe wyglądają następująco:

    ![Wyświetlanie tabel na platformie Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Jeśli notes został zamknięty przed rozpoczęciem tego samouczka, tabela `hvactemptable` jest wyczyszczona, więc nie znajduje się w danych wyjściowych.  Z poziomu narzędzi do analizy biznesowej można uzyskać dostęp tylko do tabel Hive przechowywanych w magazynie metadanych (wskazywanych przez wartość **False** w kolumnie **isTemporary**). W tym samouczku nawiążesz połączenie z utworzoną tabelą **hvac**.

2. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**. Kod sprawdza dane w tabeli.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Dane wyjściowe wyglądają następująco:

    ![Pokazywanie wierszy z tabeli hvac platformy Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. W menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Zamknij notes, aby zwolnić zasoby. 

## <a name="visualize-the-data"></a>Wizualizacja danych

W tej sekcji użyjemy usługi Power BI do utworzenia wizualizacji, raportów i pulpitów nawigacyjnych na podstawie danych klastra platformy Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop
Pierwsze kroki podczas pracy z platformą Spark obejmują połączenie się z klastrem w programie Power BI Desktop, załadowanie danych z klastra i utworzenie podstawowej wizualizacji na podstawie tych danych.

> [!NOTE]  
> Łącznik zaprezentowany w tym artykule jest obecnie w wersji zapoznawczej. Podziel się swoją opinią za pośrednictwem witryny [społeczności usługi Power BI](https://community.powerbi.com/) lub witryny [pomysłów dla usługi Power BI](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Otwórz program [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Na karcie **Narzędzia główne** kliknij pozycję **Pobierz dane**, a następnie pozycję **Więcej**.

    ![Pobieranie danych do programu Power BI Desktop z usługi HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Pobieranie danych do usługi Power BI z usługi HDInsight Apache Spark")


2. Wprowadź `Spark` w polu wyszukiwania, wybierz pozycję **Azure HDInsight Spark**, a następnie kliknij przycisk **Połącz**.

    ![Pobieranie danych do usługi Power BI z usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Pobieranie danych do usługi Power BI z usługi Apache Spark BI")

3. Wprowadź adres URL klastra (w formacie `mysparkcluster.azurehdinsight.net`), wybierz pozycję **Zapytanie bezpośrednie**, a następnie kliknij przycisk **OK**.

    Platforma Spark umożliwia wykorzystanie dowolnego trybu łączności danych. Jeśli używasz zapytania bezpośredniego, zmiany są uwzględniane w raportach bez odświeżania całego zestawu danych. W przypadku importowania danych należy odświeżyć zestaw danych, aby zobaczyć zmiany. Aby uzyskać więcej informacji o tym, jak i kiedy korzystać z zapytania bezpośredniego, zobacz [Używanie zapytania bezpośredniego w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Wprowadź dane konta logowania usługi HDInsight, a następnie kliknij przycisk **Połącz**. Domyślna nazwa konta to *admin*.

5. Wybierz tabelę `hvac`, aby wyświetlić podgląd danych, a następnie kliknij pozycję **Załaduj**.

    ![Nazwa użytkownika i hasło klastra Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nazwa użytkownika i hasło klastra Spark")

    Program Power BI Desktop posiada informacje niezbędne do połączenia się z klastrem Spark i załadowania danych z tabeli `hvac`. Tabela i jej kolumny zostaną wyświetlone w okienku **Pola**.  Zobacz poniższy zrzut ekranu:

6. Wizualizuj różnicę między temperaturą docelową i temperaturą rzeczywistą każdego budynku: 

    1. W okienku **WIZUALIZACJE** wybierz pozycję **Wykres warstwowy**. 
    2. Przeciągnij pole **BuildingID** do obszaru **Oś** i przeciągnij pola **ActualTemp** i **TargetTemp** do obszaru **Wartość**.

        ![Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI")

        Diagram wygląda następująco:

        ![Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI")

        Domyślnie wizualizacja pokazuję sumę wartości **ActualTemp** i **TargetTemp**. Klikając strzałkę w dół obok pozycji **ActualTemp** i **TragetTemp** w okienku Wizualizacje, zobaczysz zaznaczoną pozycję **Suma**.

    3. Kliknij strzałki w dół obok pozycji **ActualTemp** i **TragetTemp** w okienku Wizualizacje, wybierz pozycję **Średnia**, aby uzyskać średnią rzeczywistej i docelowej temperatury dla każdego budynku.

        ![Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI")

        Twoja wizualizacja danych będzie podobna do przedstawionej na zrzucie ekranu. Przesuń kursor nad wizualizację, aby wyświetlić etykietki narzędzi z odpowiednimi danymi.

        ![Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Tworzenie wizualizacji danych platformy Spark przy użyciu usługi Apache Spark BI")

7. Kliknij pozycję **Plik**, następnie pozycję **Zapisz** i wpisz nazwę `BuildingTemperature.pbix` dla pliku. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikowanie raportu w usłudze Power BI (opcjonalnie)

Usługa Power BI umożliwia udostępnianie raportów i pulpitów nawigacyjnych w ramach organizacji. W tej sekcji opublikuj najpierw zestaw danych i raport. Następnie przypnij raport do pulpitu nawigacyjnego. Pulpity nawigacyjne pozwalają zwykle skupić się na podzbiorze danych w raporcie; Twój raport zawiera tylko jedną wizualizację, ale nadal możesz wykonać poszczególne kroki.

1. Otwórz program Power BI Desktop.
2. Na karcie **Narzędzia główne** kliknij pozycję **Publikuj**.

    ![Publikowanie z programu Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikowanie z programu Power BI Desktop")

2. Wybierz obszar roboczy, do którego chcesz opublikować zestaw danych i raport, a następnie kliknij pozycję **Wybierz**. Na poniższej ilustracji domyślnie wybrany jest **Mój obszar roboczy**.

    ![Wybieranie obszaru roboczego, do którego chcesz opublikować zestaw danych i raport](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Wybieranie obszaru roboczego, do którego chcesz opublikować zestaw danych i raport") 

3. Po pomyślnym zakończeniu publikowania kliknij pozycję **Otwórz „BuildingTemperature.pbix” w usłudze Power BI**.

    ![Publikowanie zakończone pomyślnie, kliknij, aby wprowadzić poświadczenia](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publikowanie zakończone pomyślnie, kliknij, aby wprowadzić poświadczenia") 

4. W usłudze Power BI kliknij pozycję **Wprowadź poświadczenia**.

    ![Wprowadzanie poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Wprowadzanie poświadczeń w usłudze Power BI")

5. Kliknij pozycję **Edytuj poświadczenia**.

    ![Edycja poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edycja poświadczeń w usłudze Power BI")

6. Wprowadź dane konta logowania usługi HDInsight, a następnie kliknij przycisk **Zaloguj**. Domyślna nazwa konta to *admin*.

    ![Logowanie do klastra Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Logowanie do klastra Spark")

7. W okienku po lewej stronie przejdź do pozycji **Obszary robocze** > **Mój obszar roboczy** > **RAPORTY**, a następnie kliknij pozycję **BuildingTemperature** (temperatura budynku).

    ![Raport na liście raportów w okienku po lewej stronie](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Raport na liście raportów w okienku po lewej stronie")

    Pozycja **BuildingTemperature** powinna również zostać wyświetlona w obszarze **ZESTAWY DANYCH** w okienku po lewej stronie.

    Wizualizacja utworzona w programie Power BI Desktop jest teraz dostępna w usłudze Power BI. 

8. Umieść kursor na wizualizacji, a następnie kliknij ikonę pinezki w prawym górnym rogu.

    ![Raport w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Raport w usłudze Power BI")

9. Wybierz pozycję „Nowy pulpit nawigacyjny”, wprowadź nazwę `Building temperature`, a następnie kliknij przycisk **Przypnij**.

    ![Przypinanie do nowego pulpitu nawigacyjnego](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Przypinanie do nowego pulpitu nawigacyjnego")

10. W raporcie kliknij pozycję **Przejdź do pulpitu nawigacyjnego**. 

Wizualizacja zostanie przypięta do pulpitu nawigacyjnego — możesz dodać inne wizualizacje do raportu i przypiąć je do tego samego pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat raportów i pulpitów nawigacyjnych, zobacz [Raporty w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) i [Pulpity nawigacyjne w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](https://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](https://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](https://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
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

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

- Wizualizowanie danych platformy Apache Spark przy użyciu usługi Power BI.

Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI. 
> [!div class="nextstepaction"]
> [Uruchamianie zadania przesyłania strumieniowego na platformie Apache Spark](apache-spark-eventhub-streaming.md)

