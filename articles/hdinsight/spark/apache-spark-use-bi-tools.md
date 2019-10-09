---
title: 'Samouczek: analizowanie danych platformy Apache Spark przy użyciu usługi Power BI w usłudze Azure HDInsight '
description: Samouczek — używanie Power BI firmy Microsoft do wizualizacji Apache Spark przechowywanych klastrów usługi HDInsight danych
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: fe7560f68690233d7a333724274a259d693123d0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035692"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Samouczek: analizowanie danych platformy Apache Spark przy użyciu usługi Power BI w usłudze HDInsight

W tym samouczku dowiesz się, jak za pomocą [programu Microsoft Power BI](https://powerbi.microsoft.com/) wizualizować dane w klastrze Apache Spark w [usłudze Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wizualizowanie danych platformy Spark przy użyciu usługi Power BI

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj instrukcje w [samouczku: ładowanie danych i uruchamianie zapytań w klastrze Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcjonalnie: [Power BI subskrypcję wersji próbnej](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Weryfikowanie danych

[Notes Jupyter](https://jupyter.org/) utworzony w ramach [poprzedniego samouczka](apache-spark-load-data-run-query.md) zawiera kod do utworzenia tabeli `hvac`. Ta tabela jest oparta na pliku CSV, który jest dostępny we wszystkich klastrach usługi HDInsight Spark w `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Postępuj zgodnie z następującą procedurą, aby sprawdzić dane.

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

1. Otwórz program Power BI Desktop. Zamknij ekran powitalny uruchamiania, jeśli zostanie otwarty.

2. Na karcie **Narzędzia główne** przejdź do okna **pobieranie danych** >  **.** ..

    ![Pobieranie danych do programu Power BI Desktop z usługi HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Pobieranie danych do usługi Power BI z usługi HDInsight Apache Spark")

3. Wprowadź `Spark` w polu wyszukiwania wybierz pozycję **Azure HDInsight Spark**, a następnie wybierz pozycję **Połącz**.

    ![Pobieranie danych do usługi Power BI z usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Pobieranie danych do usługi Power BI z usługi Apache Spark BI")

4. Wprowadź adres URL klastra (w formacie `mysparkcluster.azurehdinsight.net`) w polu tekstowym **serwer** .

5. W obszarze **tryb łączności danych**wybierz pozycję **zapytanie**bezpośrednie. Następnie wybierz przycisk **OK**.

    Platforma Spark umożliwia wykorzystanie dowolnego trybu łączności danych. Jeśli używasz zapytania bezpośredniego, zmiany są uwzględniane w raportach bez odświeżania całego zestawu danych. W przypadku importowania danych należy odświeżyć zestaw danych, aby zobaczyć zmiany. Aby uzyskać więcej informacji o tym, jak i kiedy korzystać z zapytania bezpośredniego, zobacz [Używanie zapytania bezpośredniego w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Wprowadź informacje o koncie logowania usługi HDInsight, a następnie wybierz pozycję **Połącz**. Domyślna nazwa konta to *admin*.

7. Wybierz tabelę `hvac`, zaczekaj na wyświetlenie podglądu danych, a następnie wybierz pozycję **Załaduj**.

    ![Nazwa użytkownika i hasło klastra Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nazwa użytkownika i hasło klastra Spark")

    Program Power BI Desktop posiada informacje niezbędne do połączenia się z klastrem Spark i załadowania danych z tabeli `hvac`. Tabela i jej kolumny zostaną wyświetlone w okienku **Pola**.

8. Wizualizuj różnicę między temperaturą docelową i temperaturą rzeczywistą każdego budynku:

    1. W okienku **WIZUALIZACJE** wybierz pozycję **Wykres warstwowy**.

    2. Przeciągnij pole **BuildingID** do obszaru **Oś** i przeciągnij pola **ActualTemp** i **TargetTemp** do obszaru **Wartość**.

        ![Dodawanie kolumn wartości]—(./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Dodaj kolumny wartości")

        Diagram wygląda następująco:

        ![Wykres obszaru]Sum —(./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Wykres obszaru")

        Domyślnie wizualizacja pokazuję sumę wartości **ActualTemp** i **TargetTemp**. Wybierz strzałkę w dół obok pozycji **ActualTemp** i **TragetTemp** w okienku wizualizacje, aby zobaczyć pozycję **Suma** jest zaznaczona.

    3. Wybierz strzałkę w dół obok pozycji **ActualTemp** i **TragetTemp** w okienku wizualizacje, a następnie wybierz pozycję **średnia** , aby uzyskać średnią rzeczywistą i docelową temperaturę dla każdego budynku.

        ![średnia wartość](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "średnia") wartości

        Twoja wizualizacja danych będzie podobna do przedstawionej na zrzucie ekranu. Przesuń kursor nad wizualizację, aby wyświetlić etykietki narzędzi z odpowiednimi danymi.

        (./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Wykres obszaru") ![wykresu warstwowego]

9. Przejdź do **pliku** > **Zapisz**, wprowadź nazwę `BuildingTemperature` dla pliku, a następnie wybierz pozycję **Zapisz**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikowanie raportu w usłudze Power BI (opcjonalnie)

Usługa Power BI umożliwia udostępnianie raportów i pulpitów nawigacyjnych w ramach organizacji. W tej sekcji opublikuj najpierw zestaw danych i raport. Następnie przypnij raport do pulpitu nawigacyjnego. Pulpity nawigacyjne są zwykle używane do skoncentrowania się na podzbiorze danych w raporcie. W raporcie znajduje się tylko jedna Wizualizacja, ale nadal warto wykonać kroki opisane w sekcji.

1. Otwórz program Power BI Desktop.

1. Na karcie **Narzędzia główne** kliknij pozycję **Publikuj**.

    ![Publikowanie z programu Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikowanie z programu Power BI Desktop")

1. Wybierz obszar roboczy, do którego chcesz opublikować zestaw danych i raport, a następnie kliknij pozycję **Wybierz**. Na poniższej ilustracji domyślnie wybrany jest **Mój obszar roboczy**.

    ![Wybieranie obszaru roboczego, do którego chcesz opublikować zestaw danych i raport](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Wybieranie obszaru roboczego, do którego chcesz opublikować zestaw danych i raport") 

1. Po pomyślnym zakończeniu publikowania kliknij pozycję **Otwórz „BuildingTemperature.pbix” w usłudze Power BI**.

    ![Publikowanie zakończone pomyślnie, kliknij, aby wprowadzić poświadczenia](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publikowanie zakończone pomyślnie, kliknij, aby wprowadzić poświadczenia") 

1. W usłudze Power BI kliknij pozycję **Wprowadź poświadczenia**.

    ![Wprowadzanie poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Wprowadzanie poświadczeń w usłudze Power BI")

1. Kliknij pozycję **Edytuj poświadczenia**.

    ![Edycja poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edycja poświadczeń w usłudze Power BI")

1. Wprowadź dane konta logowania usługi HDInsight, a następnie kliknij przycisk **Zaloguj**. Domyślna nazwa konta to *admin*.

    ![Logowanie do klastra Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Logowanie do klastra Spark")

1. W okienku po lewej stronie przejdź do pozycji **Obszary robocze** > **Mój obszar roboczy** > **RAPORTY**, a następnie kliknij pozycję **BuildingTemperature** (temperatura budynku).

    ![Raport na liście raportów w okienku po lewej stronie](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Raport na liście raportów w okienku po lewej stronie")

    Pozycja **BuildingTemperature** powinna również zostać wyświetlona w obszarze **ZESTAWY DANYCH** w okienku po lewej stronie.

    Wizualizacja utworzona w programie Power BI Desktop jest teraz dostępna w usłudze Power BI. 

1. Umieść kursor na wizualizacji, a następnie kliknij ikonę pinezki w prawym górnym rogu.

    ![Raport w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Raport w usłudze Power BI")

1. Wybierz pozycję „Nowy pulpit nawigacyjny”, wprowadź nazwę `Building temperature`, a następnie kliknij przycisk **Przypnij**.

    ![Przypinanie do nowego pulpitu nawigacyjnego](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Przypinanie do nowego pulpitu nawigacyjnego")

1. W raporcie kliknij pozycję **Przejdź do pulpitu nawigacyjnego**.

Wizualizacja zostanie przypięta do pulpitu nawigacyjnego — możesz dodać inne wizualizacje do raportu i przypiąć je do tego samego pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat raportów i pulpitów nawigacyjnych, zobacz [Raporty w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) i [Pulpity nawigacyjne w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z samouczka warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z [Power BI firmy Microsoft](https://powerbi.microsoft.com/) w celu wizualizacji danych w klastrze Apache Spark w [usłudze Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI.

> [!div class="nextstepaction"]
> [Uruchamianie zadania przesyłania strumieniowego na platformie Apache Spark](apache-spark-eventhub-streaming.md)