---
title: 'Szybki start: tworzenie klastra platformy Spark w usłudze HDInsight przy użyciu witryny Azure portal'
description: Ten przewodnik Szybki start pokazuje, jak używać portalu Azure do tworzenia klastra Platformy Spark apache w usłudze Azure HDInsight i uruchamiania kwerendy SQL platformy Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 5f4d1b8ef742a8dcafa2b8e34a6209f85ae050d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77650652"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki start: tworzenie klastra Platformy Spark apache w usłudze Azure HDInsight przy użyciu witryny Azure portal

W tym przewodniku Szybki start można użyć witryny Azure portal do utworzenia klastra Platformy Spark Apache w usłudze Azure HDInsight. Następnie należy utworzyć notes Jupyter i używać go do uruchamiania zapytań Spark SQL względem tabel gałęzi Apache. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Struktura Apache Spark dla usługi HDInsight umożliwia szybką analizę danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Notebook Jupyter umożliwia interakcję z danymi, łączenie kodu z tekstem znaczników i proste wizualizacje.

Aby uzyskać szczegółowe objaśnienia dostępnych konfiguracji, zobacz [Konfigurowanie klastrów w programie HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji dotyczących używania portalu do tworzenia klastrów, zobacz [Tworzenie klastrów w portalu](../hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]  
> Opłaty za klastry usługi HDInsight są naliczane proporcjonalnie za minutę, niezależnie od ich użycia. Pamiętaj o usunięciu klastra po zakończeniu korzystania z niego. Aby uzyskać więcej informacji, zobacz sekcję [Czyszczenie zasobów](#clean-up-resources) w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Tworzenie klastra platformy Apache Spark w usłudze HDInsight

Za pomocą portalu Azure można utworzyć klaster HDInsight, który używa obiektów blob usługi Azure Storage jako magazynu klastra. Aby uzyskać więcej informacji na temat korzystania z usługi Data Lake Storage 2. generacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Z górnego menu wybierz pozycję **+ Utwórz zasób**.

    ![Portal Azure tworzy zasób](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Tworzenie zasobu w witrynie Azure Portal")

1. Wybierz **Analytics** > **Azure HDInsight,** aby przejść do strony **Tworzenie klastra HDInsight.**

1. Na karcie **Podstawy** podaj następujące informacje:

    |Właściwość  |Opis  |
    |---------|---------|
    |Subskrypcja  | Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra. |
    |Grupa zasobów | Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**.|
    |Nazwa klastra | Podaj globalnie unikatową nazwę.|
    |Region   | Z listy rozwijanej wybierz region, w którym jest tworzony klaster. |
    |Typ klastra| Wybierz wybierz wybierz typ klastra, aby otworzyć listę. Z listy wybierz opcję **Iskra**.|
    |Wersja klastra|To pole zostanie automatycznie wypełnione wersją domyślną po wybraniu typu klastra.|
    |Nazwa użytkownika logowania klastra| Wprowadź nazwę użytkownika logowania klastra.  Domyślną nazwą jest **admin**. To konto służy do logowania się do notesu Jupyter w dalszej części przewodnika Szybki start. |
    |Hasło logowania klastra| Wprowadź hasło logowania klastra. |
    |Nazwa użytkownika protokołu SSH (Secure Shell)| Wprowadź nazwę użytkownika protokołu SSH. Nazwą użytkownika protokołu SSH używaną w tym przewodniku Szybki start jest **sshuser**. Domyślnie to konto współdzieli hasło z kontem *Nazwa użytkownika logowania klastra*. |

    ![Tworzenie podstawowych konfiguracji klastra USŁUGI HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Tworzenie klastra platformy Spark w programie HDInsight w konfiguracjach podstawowych")

    Wybierz **dalej: >>magazynowania,** aby przejść do strony **Magazyn.**

1. W obszarze **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wybierania|Użyj wartości domyślnej **Wybierz z listy**.|
    |Konto magazynu podstawowego|Użyj wartości zapełnianych automatycznie.|
    |Kontener|Użyj wartości zapełnianych automatycznie.|

    ![Tworzenie podstawowych konfiguracji klastra USŁUGI HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Tworzenie klastra platformy Spark w programie HDInsight w konfiguracjach podstawowych")

    Wybierz **pozycję Przejrzyj + utwórz,** aby kontynuować.

1. W obszarze **Recenzja + utwórz**wybierz pozycję **Utwórz**. Utworzenie klastra trwa około 20 minut. Przed przejściem do następnej sesji należy utworzyć klaster.

Jeśli napotkasz problem z tworzeniem klastrów HDInsight, może to oznaczać, że nie masz odpowiednich uprawnień, aby to zrobić. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Jupyter Notebook to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji.

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/jupyter`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

1. Wybierz **pozycję Nowy** > **pyspark,** aby utworzyć notes.

   ![Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Uruchamianie instrukcji APACHE Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Zapytanie gałęzi Apache w programie HDInsight](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Zapytanie gałęzi w programie HDInsight")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe.

1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Korzystając z notesu Jupyter z klastrem HDInsight, `sqlContext` otrzymasz predefiniowane ustawienia, których można użyć do uruchamiania zapytań hive przy użyciu programu Spark SQL. Wyrażenie `%%sql` informuje notes Jupyter o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Uzyskanie wyników zajmuje około 30 sekund. Dane wyjściowe wyglądają następująco:

    ![Zapytanie gałęzi Apache w programie HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Zapytanie gałęzi w programie HDInsight")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Dane wyjściowe zapytania gałęzi w programie HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Dane wyjściowe zapytania gałęzi w programie HDInsight")

1. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu spowoduje zwolnienie zasobów klastra.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usługa HDInsight zapisuje dane w usłudze Azure Storage lub usłudze Azure Data Lake Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z samouczkiem z listy [Następne kroki](#next-steps), warto zachować klaster.

Przejdź z powrotem do witryny Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usługa Azure portal usuwa klaster usługi HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, można usunąć zarówno klaster HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Spark Apache w programie HDInsight i uruchomić podstawową kwerendę SQL platformy Spark. Przejdź do następnego samouczka, aby dowiedzieć się, jak używać klastra HDInsight do uruchamiania zapytań interaktywnych na przykładowych danych.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
