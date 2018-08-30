---
title: 'Szybki start: tworzenie klastra Spark w usłudze HDInsight przy użyciu szablonu'
description: W tym przewodniku Szybki start pokazano, jak za pomocą szablonu usługi Resource Manager utworzyć klaster Apache Spark w usłudze Azure HDInsight i uruchomić proste zapytanie Spark SQL.
services: azure-hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jasonh
ms.custom: mvc
ms.openlocfilehash: eccef79b53a78abc577ff40d84b615968be3dd0f
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046117"
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-template"></a>Szybki start: tworzenie klastra Spark w usłudze HDInsight przy użyciu szablonu

Dowiedz się, jak utworzyć klaster Apache Spark w usłudze Azure HDInsight, a następnie uruchamiać zapytania Spark SQL dla tabel programu Hive. Platforma Apache Spark umożliwia szybką analizę danych i używanie klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. Aby uzyskać informacje na temat klastra Spark w usłudze HDInsight, zobacz [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md).

W tym przewodniku Szybki start użyjesz szablonu usługi Resource Manager do utworzenia klastra Spark w usłudze HDInsight. Klaster używa usług Azure Storage Blob jako magazynu klastra. Aby uzyskać więcej informacji na temat korzystania z usługi Data Lake Storage 2. generacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]
> Opłaty za klastry usługi HDInsight są naliczane proporcjonalnie za minutę, niezależnie od ich użycia. Pamiętaj o usunięciu klastra po zakończeniu korzystania z niego. Aby uzyskać więcej informacji, zobacz sekcję [Czyszczenie zasobów](#clean-up-resources) w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-hdinsight-spark-cluster"></a>Tworzenie klastra Spark w usłudze HDInsight

Utwórz klaster HDInsight Spark przy użyciu szablonu usługi Azure Resource Manager. Szablon można znaleźć w usłudze [github](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). 

1. Wybierz następujący link, aby otworzyć szablon w witrynie Azure Portal na nowej karcie przeglądarki:         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Wdrażanie na platformie Azure</a>

2. Wprowadź następujące wartości:

    | Właściwość | Wartość |
    |---|---|
    |**Subskrypcja**|Wybierz subskrypcję platformy Azure, za pomocą której utworzono ten klaster. Subskrypcja używana w tym przewodniku Szybki start to **&lt;Nazwa subskrypcji platformy Azure>**. |
    | **Grupa zasobów**|Utwórz grupę zasobów lub wybierz istniejącą grupę. Grupa zasobów służy do zarządzania zasobami platformy Azure na potrzeby projektów. Nową nazwą grupy zasobów używaną w tym przewodniku Szybki start jest **myspark20180403rg**.|
    | **Lokalizacja**|Wybierz lokalizację dla grupy zasobów. Szablon używa tej lokalizacji do tworzenia klastra oraz na potrzeby domyślnego magazynu klastra. Lokalizacja używana w tym przewodniku Szybki start to **Wschodnie stany USA 2**.|
    | **Nazwa klastra**|Wprowadź nazwę tworzonego klastra usługi HDInsight. Nazwą nowego klastra używaną w tym przewodniku Szybki start jest **myspark20180403**.|
    | **Nazwa użytkownika i hasło logowania do klastra**|Domyślna nazwa logowania to admin. Wybierz hasło dla danych logowania klastra. Nazwą logowania używaną w tym przewodniku Szybki start jest **admin**.|
    | **Nazwa użytkownika i hasło SSH**|Wybierz hasło dla użytkownika SSH. Nazwą użytkownika protokołu SSH używaną w tym przewodniku Szybki start jest **sshuser**.|

    ![Tworzenie klastra HDInsight Spark przy użyciu szablonu usługi Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Tworzenie klastra Spark w usłudze HDInsight przy użyciu szablonu usługi Azure Resource Manager")

3. Wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia** i pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz przycisk **Kup**. Zostanie wyświetlony nowy kafelek zatytułowany **Wdrażanie szablonu wdrożenia**. Utworzenie klastra trwa około 20 minut. Przed przejściem do następnej sesji należy utworzyć klaster.

Problemy podczas tworzenia klastrów usługi HDInsight mogą świadczyć o braku odpowiednich uprawnień. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="install-intellijeclipse-for-spark-application"></a>Instalowanie środowiska IntelliJ/Eclipse dla aplikacji Spark
Użyj zestawu narzędzi platformy Azure dla wtyczki IntelliJ/Eclipse, aby utworzyć aplikacje Spark napisane w języku Scala, a następnie prześlij je do klastra Azure HDInsight Spark bezpośrednio ze zintegrowanego środowiska projektowego (IDE) IntelliJ/Eclipse. Aby uzyskać więcej informacji, zobacz tematy [Use IntelliJ to author/submit Spark application](./apache-spark-intellij-tool-plugin.md) (Używanie środowiska IntelliJ do tworzenia/przesyłania aplikacji Spark) i [Use Eclipse to author/submit Spark application](./apache-spark-eclipse-tool-plugin.md) (Używanie środowiska Eclipse do tworzenia/przesyłania aplikacji Spark).

## <a name="install-vscode-for-pysparkhive-applications"></a>Instalowanie programu VSCode dla aplikacji PySpark/Hive
Dowiedz się, jak za pomocą zestawu narzędzi Azure HDInsight Tools for Visual Studio Code (VSCode) tworzyć i przesyłać zadania wsadowe programu Hive, interaktywne zapytania programu Hive, partię PySpark oraz skrypty interaktywne PySpark. Zestaw narzędzi Azure HDInsight Tools można zainstalować na platformach obsługiwanych przez program VSCode. Są to systemy Windows, Linux i macOS. Aby uzyskać więcej informacji, zobacz temat [Use VSCode to author/submit PySpark application](../hdinsight-for-vscode.md) (Używanie programu VSCode do tworzenia/przesyłania aplikacji PySpark).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Jupyter Notebook to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji. 

1. Otwórz [portal Azure](https://portal.azure.com).
2. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz utworzony klaster.

    ![Otwieranie klastra usługi HDInsight w witrynie Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. W portalu wybierz pozycję **Pulpity nawigacyjne klastra**, a następnie pozycję **Notes Jupyter**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

   ![Otwieranie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otwieranie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

4. Aby utworzyć notes, wybierz pozycję **Nowy** > **PySpark**. 

   ![Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interakcyjnego zapytania Spark SQL")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Uruchamianie instrukcji Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Zapytanie programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Zapytanie programu Hive na platformie HDInsight Spark")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe. 
2. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Gdy używasz notesu Jupyter na potrzeby klastra platformy HDInsight Spark, możesz pobrać ustawienie wstępne `sqlContext` służące do uruchamiania zapytań programu Hive przy użyciu modułu Spark SQL. Wyrażenie `%%sql` informuje notes Jupyter o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Uzyskanie wyników zajmuje około 30 sekund. Dane wyjściowe wyglądają następująco: 

    ![Zapytanie programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Zapytanie programu Hive na platformie HDInsight Spark")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.
    
2. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Wyniki zapytania programu Hive na platformie HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Wyniki zapytania programu Hive na platformie HDInsight Spark")

2. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu spowoduje zwolnienie zasobów klastra.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Usługa HDInsight zapisuje Twoje dane w usłudze Azure Storage lub Azure Data Lake Store, więc możesz bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z samouczkiem z listy [Następne kroki](#next-steps), warto zachować klaster.

Przejdź z powrotem do witryny Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Następne kroki 

W tym przewodniku Szybki start omówiono sposób tworzenia klastra Spark w usłudze HDInsight i uruchamiania podstawowego zapytania Spark SQL. Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra Spark w usłudze HDInsight.

> [!div class="nextstepaction"]
>[Uruchamianie interakcyjnych zapytań na platformie Spark](./apache-spark-load-data-run-query.md)


