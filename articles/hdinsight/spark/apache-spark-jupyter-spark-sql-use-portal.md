---
title: 'Szybki Start: Tworzenie klastra Spark w usłudze HDInsight przy użyciu Azure Portal'
description: W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć klaster Apache Spark w usłudze Azure HDInsight i uruchomić program Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: a4c7fe0d01bc9e5045cfe585c3f235636aa3dd22
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676989"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki Start: Tworzenie klastra Apache Spark w usłudze Azure HDInsight przy użyciu Azure Portal

Dowiedz się, jak utworzyć klaster Apache Spark w usłudze Azure HDInsight oraz jak uruchamiać zapytania Spark SQL względem tabel programu Hive. Apache Spark umożliwia szybkie analizowanie danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Aby uzyskać informacje na temat platformy Spark w usłudze HDInsight, zobacz [Omówienie: Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md).

W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć klaster usługi HDInsight Spark. Klaster używa obiektów BLOB usługi Azure Storage jako magazynu klastra. Aby uzyskać więcej informacji na temat korzystania z Data Lake Storage Gen2, zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> Opłaty za klastry usługi HDInsight są naliczane proporcjonalnie za minutę, niezależnie od tego, czy są one używane. Pamiętaj o usunięciu klastra po zakończeniu korzystania z niego. Aby uzyskać więcej informacji, zobacz sekcję [czyszczenie zasobów](#clean-up-resources) w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-an-hdinsight-spark-cluster"></a>Tworzenie klastra usługi HDInsight Spark

1. W Azure Portal wybierz pozycję **Utwórz zasób** > **Analiza** > **HDInsight**.

    ![Azure Portal utworzyć]usługi HDInsight(./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "dla zasobu na Azure Portal")

1. W obszarze **podstawy**podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Ramach  | Z listy rozwijanej wybierz subskrypcję platformy Azure używaną dla tego klastra. Subskrypcja użyta w tym przewodniku szybki start to **Azure**. |
    |Grupa zasobów | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Nazwa grupy zasobów używana w tym przewodniku szybki start to grupa **zasobów**. |
    |Nazwa klastra | Nadaj nazwę klastrowi usługi HDInsight Spark. Nazwa klastra używana w tym przewodniku szybki start to **myspark2019**.|
    |Lokalizacja   | Wybierz lokalizację grupy zasobów. Szablon używa tej lokalizacji do tworzenia klastra oraz domyślnego magazynu klastra. Lokalizacja używana w tym przewodniku szybki start to **Wschodnie stany USA**. |
    |Typ klastra| Wybierz pozycję **Spark** jako typ klastra.|
    |Wersja klastra|Po wybraniu typu klastra pole zostanie automatycznie wypełnione przy użyciu domyślnej wersji.|
    |Nazwa użytkownika logowania klastra| Wprowadź nazwę użytkownika logowania klastra.  Nazwa domyślna to *admin*. To konto jest używane do logowania się do notesu Jupyter w dalszej części przewodnika Szybki Start. |
    |Hasło logowania klastra| Wprowadź hasło logowania klastra. |
    |Nazwa użytkownika Secure Shell (SSH)| Wprowadź nazwę użytkownika SSH. Nazwa użytkownika SSH użyta w tym przewodniku szybki start to **sshuser**. Domyślnie to konto ma to samo hasło co konto logowania do *nazwy użytkownika* . |

    ![Tworzenie podstawowego konfigurowania klastra usługi HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Tworzenie klastra Spark w usłudze HDInsight konfiguracje podstawowe")

    Wybierz pozycję **Dalej: > magazynu >** , aby przejść do strony **Magazyn** .

1. W obszarze **Magazyn**podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wyboru|Użyj wartości domyślnej **Wybierz z listy**.|
    |Podstawowe konto magazynu|Użyj automatycznie wypełnionej wartości.|
    |Wbudowane|Użyj automatycznie wypełnionej wartości.|

    ![Tworzenie podstawowego konfigurowania klastra usługi HDInsight Spark](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage-spark.png "Tworzenie klastra Spark w usłudze HDInsight konfiguracje podstawowe")

    Wybierz pozycję **Recenzja + Utwórz** , aby kontynuować.

1. W obszarze **Recenzja + tworzenie**wybierz pozycję **Utwórz**. Utworzenie klastra trwa około 20 minut. Przed przejściem do następnej sesji należy utworzyć klaster.

Jeśli wystąpi problem z tworzeniem klastrów usługi HDInsight, może to oznaczać, że nie masz odpowiednich uprawnień, aby to zrobić. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Jupyter Notebook to interaktywne środowisko notesu obsługujące różne języki programowania. Notes pozwala na posługiwanie się danymi, łączenie kodu z tekstem z promocji i wykonywanie prostych wizualizacji.

1. Otwórz [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz utworzony klaster.

    ![Otwórz klaster usługi HDInsight w Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. W portalu wybierz pozycję **pulpity nawigacyjne klastra**, a następnie wybierz pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania klastra dla klastra.

   ![Otwórz Jupyter Notebook, aby uruchomić interakcyjne zapytanie Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter notebook do uruchamiania interakcyjnego zapytania Spark SQL")

1. Wybierz pozycję **nowy** > **PySpark** , aby utworzyć Notes.

   ![Utwórz Jupyter notebook do uruchamiania interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie Jupyter notebook do uruchamiania interakcyjnego zapytania Spark SQL")

   Nowy Notes zostanie utworzony i otwarty z nazwą bez tytułu (bez tytułu. pynb).

## <a name="run-spark-sql-statements"></a>Uruchamianie instrukcji platformy Spark SQL

SQL (Structured Query Language) to najpopularniejszy i szeroko używany język służący do wykonywania zapytań i definiowania danych. Platforma Spark SQL działa jako rozszerzenie do Apache Spark przetwarzania danych strukturalnych przy użyciu znanej składni SQL.

1. Sprawdź, czy jądro jest gotowe. Jądro jest gotowe, gdy zobaczysz pusty okrąg obok nazwy jądra w notesie. Pełny okrąg oznacza, że jądro jest zajęte.

    Zapytanie ![Apache Hive w](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "kwerendzie Hive") usługi HDInsight Spark1 w usłudze HDInsight Spark1

    Po pierwszym uruchomieniu notesu jądro wykonuje pewne zadania w tle. Zaczekaj na gotowość jądra.

1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER** , aby uruchomić kod. Polecenie wyświetla listę tabel programu Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Jeśli używasz Jupyter Notebook z klastrem usługi HDInsight Spark, uzyskasz wstępnie ustawioną `sqlContext`, której można użyć do uruchamiania zapytań Hive przy użyciu platformy Spark SQL. `%%sql` informuje Jupyter Notebook o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera 10 pierwszych wierszy z tabeli programu Hive (**hivesampletable**), które są domyślnie dołączone do wszystkich klastrów usługi HDInsight. Uzyskanie wyników trwa około 30 sekund. Dane wyjściowe wyglądają następująco:

    Zapytanie ![Apache Hive w](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "kwerendzie Hive") usługi HDInsight Spark2 w usłudze HDInsight Spark2

    Za każdym razem, gdy uruchamiasz zapytanie w Jupyter, tytuł okna przeglądarki sieci Web ukazuje stan **(zajęty)** wraz z tytułem notesu. Zobaczysz również pełny okrąg obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom kolejne zapytanie, aby wyświetlić dane w `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia danych wyjściowych zapytania.

    ![Dane wyjściowe zapytania programu Hive w usłudze HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "zapytania dotyczące danych wyjściowych w usłudze HDInsight Spark")

1. W menu **plik** w notesie wybierz pozycję **Zamknij i zatrzymywanie**. Zamykanie notesu zwalnia zasoby klastra.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usługa HDInsight zapisuje dane w usłudze Azure Storage lub Azure Data Lake Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty są naliczone również za klaster usługi HDInsight, nawet wtedy, gdy nie jest on używany. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane. Jeśli planujesz prace nad samouczkiem wymienionym w [następnych krokach](#next-steps) , możesz chcieć zachować klaster.

Przełącz się z powrotem do Azure Portal i wybierz pozycję **Usuń**.

![Azure Portal usunąć klastra usługi HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "usuwanie klastra usługi HDInsight")

Możesz również wybrać nazwę grupy zasobów, aby otworzyć stronę Grupa zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, można usunąć zarówno klaster usługi HDInsight Spark, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra usługi HDInsight Spark i uruchamiania podstawowego zapytania Spark SQL. Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra usługi HDInsight Spark.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na Apache Spark](./apache-spark-load-data-run-query.md)
