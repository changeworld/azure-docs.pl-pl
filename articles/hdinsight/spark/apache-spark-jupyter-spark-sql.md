---
title: Tworzenie klastra Apache Spark w usłudze Azure HDInsight | Microsoft Docs
description: Przewodnik Szybki start platformy HDInsight Spark opisujący sposób tworzenia klastra platformy Apache Spark w usłudze HDInsight.
keywords: spark quickstart,interactive spark,interactive query,hdinsight spark,azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 1fdae7d6e47ff70e83e8153cdc22c0b2881d2743
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight

Dowiedz się, jak utworzyć klaster platformy Apache Spark w usłudze Azure HDInsight, a następnie uruchamiać zapytania Spark SQL dla tabel programu Hive. Aby uzyskać informacje na temat klastra Spark w usłudze HDInsight, zobacz [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Tworzenie klastra HDInsight Spark

Utwórz klaster HDInsight Spark przy użyciu [szablonu usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md). Szablon można znaleźć w usłudze [github](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Aby zapoznać się z innymi metodami tworzenia klastra, zobacz temat [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Kliknij poniższy obraz, aby otworzyć szablon w usłudze Azure Portal.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Wprowadź następujące wartości:

    ![Tworzenie klastra HDInsight Spark przy użyciu szablonu usługi Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Tworzenie klastra Spark w usłudze HDInsight przy użyciu szablonu usługi Azure Resource Manager")

    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, za pomocą której utworzono ten klaster.
    * **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą. Grupa zasobów służy do zarządzania zasobami platformy Azure na potrzeby projektów.
    * **Lokalizacja**: Wybierz lokalizację dla grupy zasobów. Szablon używa tej lokalizacji do tworzenia klastra oraz na potrzeby domyślnego magazynu klastra.
    * **Nazwa klastra**: wprowadź nazwę tworzonego klastra usługi HDInsight.
    * **Nazwa logowania i hasło klastra**: domyślna nazwa logowania to admin. Wybierz hasło dla danych logowania klastra.
    * **Nazwa użytkownika i hasło SSH**. Wybierz hasło dla użytkownika SSH.

3. Wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia** i pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij przycisk **Kup**. Zostanie wyświetlony nowy kafelek zatytułowany **Wdrażanie szablonu wdrożenia**. Utworzenie klastra trwa około 20 minut.

Problemy podczas tworzenia klastrów usługi HDInsight mogą świadczyć o braku odpowiednich uprawnień. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> W tym artykule opisano tworzenie klastra Spark korzystającego z [obiektów blob usługi Azure Storage jako magazynu klastra](../hdinsight-hadoop-use-blob-storage.md). Można również utworzyć klaster platformy Spark korzystający z usługi [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) jako magazynu domyślnego. Aby uzyskać instrukcje, zobacz [Create an HDInsight cluster with Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) (Tworzenie klastra HDInsight z usługą Data Lake Store).
>
>

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

[Notes Jupyter](http://jupyter.org) to środowisko notesu interakcyjnego obsługujące różne języki programowania, które umożliwia interakcję z danymi, łączenie kodu z tekstem zawierającym znaczniki i wykonywanie prostych wizualizacji. Platforma Spark w usłudze HDInsight obejmuje również [notes Zeppelin](apache-spark-zeppelin-notebook.md). W tym samouczku jest używany notes Jupyter.

**Aby utworzyć notes Jupyter**

1. Otwórz [portal Azure](https://portal.azure.com/).

2. Otwórz utworzony klaster Spark. Aby uzyskać instrukcje, zobacz [informacje o tworzeniu listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. W portalu kliknij pozycję **Pulpity nawigacyjne klastra**, a następnie pozycję **Notes Jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

   ![Otwieranie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otwieranie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

   > [!NOTE]
   > Dostęp do notesu Jupyter dla klastra można również uzyskać, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Kliknij pozycję **New** (Nowy), a następnie kliknij pozycję **PySpark** w celu utworzenia notesu. Notesy Jupyter w klastrach HDInsight obsługują trzy jądra — **PySpark**, **PySpark3** i **Spark**. W tym samouczku jest używane jądro **PySpark**. Aby uzyskać informacje na temat tych jąder i zalet używania programu **PySpark**, zobacz [Use Jupyter notebooks kernels with Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-kernels.md) (Używanie jąder notesu Jupyter z klastrami Apache Spark w usłudze HDInsight).

   ![Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

4. Jeśli chcesz, kliknij nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    ![Podawanie nazwy dla notesu Jupyter, z poziomu którego będzie uruchamiane interakcyjne zapytanie Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Podawanie nazwy dla notesu Jupyter, z poziomu którego będzie uruchamiane interakcyjne zapytanie Spark")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Uruchamianie instrukcji rozwiązania Spark SQL na tabeli programu Hive

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

Rozwiązanie Spark SQL obsługuje języki wykonywania zapytań SQL i HiveQL. Jego możliwości obejmują obsługę powiązań w językach Python, Scala i Java. Pozwala ono wykonywać zapytania na danych przechowywanych w wielu lokalizacjach, takich jak zewnętrzne bazy danych, pliki danych strukturalnych (na przykład JSON) i tabele programu Hive.

Przykład odczytywania danych z pliku csv zamiast z tabeli programu Hive podano w temacie [Uruchamianie zapytań interakcyjnych w klastrach Spark w usłudze HDInsight](apache-spark-load-data-run-query.md).

**Aby włączyć rozwiązanie Spark SQL**

1. Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Zapytanie programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Zapytanie programu Hive na platformie HDInsight Spark")

2. Po uzyskaniu gotowości przez jądro wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT+ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Gdy używasz notesu Jupyter na potrzeby klastra platformy HDInsight Spark, możesz pobrać ustawienie wstępne `sqlContext` służące do uruchamiania zapytań programu Hive przy użyciu modułu Spark SQL. Wyrażenie `%%sql` informuje notes Jupyter o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Uzyskanie wyników zajmuje około 30 sekund. Dane wyjściowe wyglądają następująco: 

    ![Zapytanie programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Zapytanie programu Hive na platformie HDInsight Spark")

    Aby uzyskać więcej informacji na temat magicznego wyrażenia `%%sql` i wstępnie ustawionych kontekstów, zobacz [Jupyter kernels available for an HDInsight cluster](apache-spark-jupyter-notebook-kernels.md) (Jądra programu Jupyter dostępne dla klastra usługi HDInsight).

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.
    
2. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Wyniki zapytania programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Wyniki zapytania programu Hive na platformie HDInsight Spark")

2. W menu **File** (Plik) w notesie kliknij polecenie **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu spowoduje zwolnienie zasobów klastra.

3. Jeśli planujesz wykonać poniższe kroki w późniejszym czasie, pamiętaj, aby usunąć klaster usługi HDInsight utworzony zgodnie z tym artykułem. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Kolejne kroki 

W tym artykule omówiono sposób tworzenia klastra platformy HDInsight Spark i uruchamiania podstawowego zapytania modułu Spark SQL. Przejdź do następnego artykułu, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra HDInsight Spark.

> [!div class="nextstepaction"]
>[Run interactive queries on an HDInsight Spark cluster (Uruchamianie interakcyjnych zapytań w klastrze HDInsight Spark)](apache-spark-load-data-run-query.md)



