---
title: 'Tutorial: Analyze Azure HDInsight Apache Spark data with Power BI'
description: Tutorial - Use Microsoft Power BI to visualize Apache Spark data stored HDInsight clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3fd1405d8421d71f52d9cd215dd055ce1595abd0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327278"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Samouczek: analizowanie danych platformy Apache Spark przy użyciu usługi Power BI w usłudze HDInsight

In this tutorial, you learn how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wizualizowanie danych platformy Spark przy użyciu usługi Power BI

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Complete the article [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optional: [Power BI trial subscription](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Weryfikowanie danych

[Notes Jupyter](https://jupyter.org/) utworzony w ramach [poprzedniego samouczka](apache-spark-load-data-run-query.md) zawiera kod do utworzenia tabeli `hvac`. This table is based on the CSV file available on all HDInsight Spark clusters at `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Postępuj zgodnie z następującą procedurą, aby sprawdzić dane.

1. Z notesu Jupyter wklej poniższy kod, a następnie naciśnij klawisze **SHIFT + ENTER**. Kod sprawdza obecność tabel.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Dane wyjściowe wyglądają następująco:

    ![Wyświetlanie tabel na platformie Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Jeśli notes został zamknięty przed rozpoczęciem tego samouczka, tabela `hvactemptable` jest wyczyszczona, więc nie znajduje się w danych wyjściowych.  Z poziomu narzędzi do analizy biznesowej można uzyskać dostęp tylko do tabel Hive przechowywanych w magazynie metadanych (wskazywanych przez wartość **False** w kolumnie **isTemporary**). W tym samouczku nawiążesz połączenie z utworzoną tabelą **hvac**.

2. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**. Kod sprawdza dane w tabeli.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Dane wyjściowe wyglądają następująco:

    ![Pokazywanie wierszy z tabeli hvac platformy Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknij notes, aby zwolnić zasoby.

## <a name="visualize-the-data"></a>Wizualizacja danych

W tej sekcji użyjemy usługi Power BI do utworzenia wizualizacji, raportów i pulpitów nawigacyjnych na podstawie danych klastra platformy Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop

Pierwsze kroki podczas pracy z platformą Spark obejmują połączenie się z klastrem w programie Power BI Desktop, załadowanie danych z klastra i utworzenie podstawowej wizualizacji na podstawie tych danych.

> [!NOTE]  
> Łącznik zaprezentowany w tym artykule jest obecnie w wersji zapoznawczej. Podziel się swoją opinią za pośrednictwem witryny [społeczności usługi Power BI](https://community.powerbi.com/) lub witryny [pomysłów dla usługi Power BI](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Otwórz program Power BI Desktop. Close the start-up splash screen if it opens.

2. From the **Home** tab, navigate to **Get Data** > **More..** .

    ![Get data into Power BI Desktop from HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Get data into Power BI from Apache Spark BI")

3. Enter `Spark` in the search box, select **Azure HDInsight Spark**, and then select **Connect**.

    ![Get data into Power BI from Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Get data into Power BI from Apache Spark BI")

4. Enter your cluster URL (in the form `mysparkcluster.azurehdinsight.net`) in the **Server** text box.

5. Under **Data connectivity mode**, select **DirectQuery**. Następnie wybierz przycisk **OK**.

    Platforma Spark umożliwia wykorzystanie dowolnego trybu łączności danych. Jeśli używasz zapytania bezpośredniego, zmiany są uwzględniane w raportach bez odświeżania całego zestawu danych. W przypadku importowania danych należy odświeżyć zestaw danych, aby zobaczyć zmiany. Aby uzyskać więcej informacji o tym, jak i kiedy korzystać z zapytania bezpośredniego, zobacz [Używanie zapytania bezpośredniego w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Enter the HDInsight login account information, then select **Connect**. Domyślna nazwa konta to *admin*.

7. Select the `hvac` table, wait to see a preview of the data, and then select **Load**.

    ![Spark cluster user name and password](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark cluster user name and password")

    Program Power BI Desktop posiada informacje niezbędne do połączenia się z klastrem Spark i załadowania danych z tabeli `hvac`. Tabela i jej kolumny zostaną wyświetlone w okienku **Pola**.

8. Wizualizuj różnicę między temperaturą docelową i temperaturą rzeczywistą każdego budynku:

    1. W okienku **WIZUALIZACJE** wybierz pozycję **Wykres warstwowy**.

    2. Przeciągnij pole **BuildingID** do obszaru **Oś** i przeciągnij pola **ActualTemp** i **TargetTemp** do obszaru **Wartość**.

        ![add value columns](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "add value columns")

        Diagram wygląda następująco:

        ![area graph sum](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "area graph sum")

        Domyślnie wizualizacja pokazuję sumę wartości **ActualTemp** i **TargetTemp**. Select the down arrow next to **ActualTemp** and **TragetTemp** in the Visualizations pane, you can see **Sum** is selected.

    3. Select the down arrows next to **ActualTemp** and **TragetTemp** in the Visualizations pane, select **Average** to get an average of actual and target temperatures for each building.

        ![average of values](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "average of values")

        Twoja wizualizacja danych będzie podobna do przedstawionej na zrzucie ekranu. Przesuń kursor nad wizualizację, aby wyświetlić etykietki narzędzi z odpowiednimi danymi.

        ![area graph](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "area graph")

9. Navigate to **File** > **Save**, enter the name `BuildingTemperature` for the file, then select **Save**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikowanie raportu w usłudze Power BI (opcjonalnie)

Usługa Power BI umożliwia udostępnianie raportów i pulpitów nawigacyjnych w ramach organizacji. W tej sekcji opublikuj najpierw zestaw danych i raport. Następnie przypnij raport do pulpitu nawigacyjnego. Dashboards are typically used to focus on a subset of data in a report. You have only one visualization in your report, but it's still useful to go through the steps.

1. Otwórz program Power BI Desktop.

1. Na karcie **Narzędzia główne** kliknij pozycję **Publikuj**.

    ![Publikowanie z programu Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikowanie z programu Power BI Desktop")

1. Wybierz obszar roboczy, do którego chcesz opublikować zestaw danych i raport, a następnie kliknij pozycję **Wybierz**. Na poniższej ilustracji domyślnie wybrany jest **Mój obszar roboczy**.

    ![Select workspace to publish dataset and report to](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Select workspace to publish dataset and report to") 

1. Po pomyślnym zakończeniu publikowania kliknij pozycję **Otwórz „BuildingTemperature.pbix” w usłudze Power BI**.

    ![Publish success, click to enter credentials](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publish success, click to enter credentials") 

1. W usłudze Power BI kliknij pozycję **Wprowadź poświadczenia**.

    ![Enter credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Enter credentials in Power BI service")

1. Kliknij pozycję **Edytuj poświadczenia**.

    ![Edit credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edit credentials in Power BI service")

1. Wprowadź dane konta logowania usługi HDInsight, a następnie kliknij przycisk **Zaloguj**. Domyślna nazwa konta to *admin*.

    ![Sign in to Spark cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Sign in to Spark cluster")

1. W okienku po lewej stronie przejdź do pozycji **Obszary robocze** > **Mój obszar roboczy** > **RAPORTY**, a następnie kliknij pozycję **BuildingTemperature** (temperatura budynku).

    ![Report listed under reports in left pane](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report listed under reports in left pane")

    Pozycja **BuildingTemperature** powinna również zostać wyświetlona w obszarze **ZESTAWY DANYCH** w okienku po lewej stronie.

    Wizualizacja utworzona w programie Power BI Desktop jest teraz dostępna w usłudze Power BI. 

1. Umieść kursor na wizualizacji, a następnie kliknij ikonę pinezki w prawym górnym rogu.

    ![Report in the Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report in the Power BI service")

1. Wybierz pozycję „Nowy pulpit nawigacyjny”, wprowadź nazwę `Building temperature`, a następnie kliknij przycisk **Przypnij**.

    ![Pin to new dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Pin to new dashboard")

1. W raporcie kliknij pozycję **Przejdź do pulpitu nawigacyjnego**.

Wizualizacja zostanie przypięta do pulpitu nawigacyjnego — możesz dodać inne wizualizacje do raportu i przypiąć je do tego samego pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat raportów i pulpitów nawigacyjnych, zobacz [Raporty w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) i [Pulpity nawigacyjne w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z samouczka warto usunąć klaster. With HDInsight, your data is stored in Azure Storage, so you can safely delete a cluster when it isn't in use. You're also charged for an HDInsight cluster, even when it isn't in use. Since the charges for the cluster are many times more than the charges for storage, it makes economic sense to delete clusters when they aren't in use.

To delete a cluster, see [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

In this tutorial, you learned how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Advance to the next article to see you can create a machine learning application.

> [!div class="nextstepaction"]
> [Create a machine learning application](./apache-spark-ipython-notebook-machine-learning.md)