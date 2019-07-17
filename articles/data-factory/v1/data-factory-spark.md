---
title: Wywoływanie programów platformy Spark w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wywoływanie programów platformy Spark z usługi Azure data factory przy użyciu działania technologii MapReduce.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 95c49eec6964984894f75ecd0a9e50c9c947683b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61257646"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Wywoływanie programów platformy Spark z potoków usługi Azure Data Factory

> [!div class="op_single_selector" title1="Działania przekształcania"]
> * [Działanie technologii hive](data-factory-hive-activity.md)
> * [Działania technologii pig](data-factory-pig-activity.md)
> * [Działania technologii MapReduce](data-factory-map-reduce.md)
> * [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działania platformy Spark](data-factory-spark.md)
> * [Machine Learning Batch Execution działania](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update Resource działania](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania usługi Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przekształcania danych za pomocą działania platformy Apache Spark w usłudze Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Wprowadzenie
Działanie platformy Spark jest jednym z [działania przekształcania danych](data-factory-data-transformation-activities.md) obsługiwane przez usługę Data Factory. To działanie uruchamia określony program platformy Spark w klastrze Spark w usłudze Azure HDInsight. 

> [!IMPORTANT]
> - Działanie platformy Spark nie obsługuje klastry HDInsight Spark, korzystających z usługi Azure Data Lake Store jako magazynu głównego.
> - Działanie platformy Spark obsługuje tylko istniejący (własne) klastry HDInsight Spark. Program nie obsługuje usługi połączonej HDInsight na żądanie.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Przewodnik: Tworzenie potoku przy użyciu działania Spark.
Poniżej przedstawiono typowe czynności, aby utworzyć potok usługi data factory za pomocą działania platformy Spark: 

* Tworzenie fabryki danych.
* Tworzenie połączonej usługi Azure Storage w celu połączenia magazynu skojarzonego z klastrem usługi HDInsight Spark w usłudze data factory.
* Tworzenie usługi połączonej HDInsight połączyć klastra Spark w HDInsight z fabryką danych.
* Tworzenie zestawu danych, która odwołuje się do połączonej usługi Storage. Obecnie należy określić wyjściowy zestaw danych dla działania, nawet jeśli dostępny jest Brak danych wyjściowych jest generowany. 
* Tworzenie potoku za pomocą działania platformy Spark, które odwołuje się do usługi połączonej HDInsight utworzony. Działanie jest skonfigurowane z zestawu danych utworzonego w poprzednim kroku jako wyjściowy zestaw danych. Wyjściowy zestaw danych jest elementem wpływającym na harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych, nawet jeśli działanie tak naprawdę nie generuje dane wyjściowe.

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz konto magazynu ogólnego przeznaczenia zgodnie z instrukcjami w [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).

1. Tworzenie klastra Spark w HDInsight, wykonując instrukcje podane w tym samouczku [Tworzenie klastra Spark w HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Skojarz konto magazynu, do którego został utworzony w kroku 1, z tym klastrem.

1. Pobierać i przeglądać plik skryptu języka Python **test.py** znajdującym się w [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Przekaż **test.py** do **pyFiles** folderu w **adfspark** kontenera w usłudze blob storage. Utwórz kontener oraz folder, jeśli nie istnieją.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Aby utworzyć fabrykę danych, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Nowy** > **Dane i analiza** > **Fabryka danych**.

1. Na **nowa fabryka danych** bloku, w obszarze **nazwa**, wprowadź **SparkDF**.

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych SparkDF jest niedostępna", Zmień nazwę fabryki danych. Na przykład użyć yournameSparkDFdate i ponownie utworzyć fabrykę danych. Aby uzyskać więcej informacji na temat reguł nazewnictwa, zobacz [Data Factory: Naming rules (Data Factory: reguły nazewnictwa)](data-factory-naming-rules.md).

1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w ramach której chcesz utworzyć fabrykę danych.

1. Wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów platformy Azure.

1. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego**.

1. Wybierz pozycję **Utwórz**.

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, musisz być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.

1. Fabryki danych jest widoczne, utworzonemu na pulpicie nawigacyjnym witryny Azure Portal.

1. Po utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych** z zawartością fabryki danych. Jeśli nie widzisz **usługi Data factory** wybierz Kafelek fabryki danych na pulpicie nawigacyjnym.

    ![Blok Fabryka danych](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi. Jedna usługa łączy klaster Spark z fabryką danych i innych usług łączy usługi storage z fabryką danych. 

#### <a name="create-a-storage-linked-service"></a>Tworzenie połączonej usługi Storage
W tym kroku opisano łączenie konta magazynu z fabryką danych. Zestaw danych, który zostanie utworzony w kroku w dalszej części tego przewodnika odnosi się do tej połączonej usługi. Z tą usługą połączoną odwołuje się zbyt usługi połączonej HDInsight, która definiuje się w następnym kroku. 

1. Na **usługi Data factory** bloku wybierz **tworzenie i wdrażanie**. Pojawi się Edytor fabryki danych.

1. Wybierz pozycję **Nowy magazyn danych**, a następnie opcję **Azure Storage**.

   ![Nowy magazyn danych](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Skrypt JSON umożliwia tworzenie magazynu połączonej usługi jest wyświetlany w edytorze.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Zastąp **nazwa konta** i **klucz konta** przy użyciu nazwy i klucza dostępu konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zapoznaj się z informacjami na temat sposobów wyświetlania, kopiowania i ponownego generowania kluczy dostępu do magazynu podanymi w sekcji [Zarządzanie kontem magazynu](../../storage/common/storage-account-manage.md#access-keys).

1. Aby wdrożyć połączoną usługę, wybierz **Wdróż** na pasku poleceń. Po pomyślnym wdrożeniu połączonej usługi okno Wersja robocza-1 zniknie. W widoku drzewa po lewej stronie pojawi się wartość **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight
W tym kroku utworzysz usługi połączone HDInsight do połączenia klastra platformy HDInsight Spark w usłudze data factory. Klaster HDInsight służy do uruchomienia określonego w działaniu platformy Spark w potoku, w tym przykładzie program platformy Spark. 

1. W edytorze fabryki danych, wybierz **więcej** > **nowe obliczenie** > **klastra HDInsight**.

    ![Tworzenie połączonej usługi HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. W edytorze JSON wykonaj następujące czynności:

    a. Określ identyfikator URI klastra HDInsight Spark. Na przykład: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Określ nazwę użytkownika, który ma dostęp do klastra Spark.

    c. Określ hasło dla użytkownika.

    d. Określanie połączonej usługi Storage skojarzonym z klastrem usługi HDInsight Spark. W tym przykładzie jest AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Działanie platformy Spark nie obsługuje klastry HDInsight Spark, korzystających z usługi Azure Data Lake Store jako magazynu głównego.
    > - Działanie platformy Spark obsługuje tylko istniejący (własne) klastry HDInsight Spark. Program nie obsługuje usługi połączonej HDInsight na żądanie.

    Aby uzyskać więcej informacji na temat usługi połączonej HDInsight, zobacz [HDInsight połączoną usługę](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Aby wdrożyć połączoną usługę, wybierz **Wdróż** na pasku poleceń. 

### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Wyjściowy zestaw danych jest elementem wpływającym na harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych działania platformy Spark w potoku, nawet jeśli działanie nie generuje żadnych danych wyjściowych. Określenie zestawu danych wejściowych dla działania jest opcjonalne.

1. W Edytorze fabryki danych wybierz pozycję **Więcej** > **Nowy zestaw danych** > **Magazyn obiektów blob Azure**.

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Fragment kodu JSON definiuje zestaw danych o nazwie **OutputDataset**. Ponadto określasz, że wyniki są przechowywane w kontenerze obiektów blob o nazwie **adfspark** oraz folderze o nazwie **pyFiles/output**. Jak wspomniano wcześniej, ten zestaw danych jest fikcyjnego zestawu danych. Program platformy Spark w tym przykładzie nie generuje żadnych danych wyjściowych. **Dostępności** sekcja określa, że wyjściowy zestaw danych jest generowany codziennie. 

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Aby wdrożyć zestaw danych, wybierz **Wdróż** na pasku poleceń.


### <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem HDInsightSpark. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. W związku z tym w tym przykładzie określono żadnego wejściowego zestawu danych.

1. W Edytorze fabryki danych wybierz pozycję **Więcej** > **Nowy potok**.

1. Zastąp skryptu do okna wersja robocza-1 za pomocą następującego skryptu:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Pamiętaj o następujących kwestiach:

    a. **Typu** właściwość jest ustawiona na **HDInsightSpark**.

    b. **RootPath** właściwość jest ustawiona na **adfspark\\pyFiles** gdzie adfspark jest kontener obiektów blob, a pyFiles to folderu plików w kontenerze. W tym przykładzie do magazynu obiektów blob jest ten, który jest skojarzony z klastrem Spark. Możesz przekazać plik do innego konta magazynu. Jeśli tak zrobisz, tworzenia połączonej usługi Storage połączyć tego konta magazynu z fabryką danych. Następnie określ nazwy połączonej usługi, jako wartość **sparkJobLinkedService** właściwości. Aby uzyskać więcej informacji na temat tej właściwości i inne właściwości obsługiwanych przez działanie platformy Spark, zobacz [właściwości działania Spark](#spark-activity-properties).

    c. **Właściwość entryFilePath** właściwość jest ustawiona na **test.py**, czyli pliku Python.

    d. **Getdebuginfo —** właściwość jest ustawiona na **zawsze**, co oznacza, że pliki dziennika są zawsze generowany (powodzenie lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, że ta właściwość nie należy ustawiać na `Always` w środowisku produkcyjnym, chyba że w przypadku rozwiązywania problemów.

    e. **Generuje** sekcja zawiera jeden wyjściowy zestaw danych. Należy określić wyjściowy zestaw danych, nawet jeśli program platformy Spark nie generuje żadnych danych wyjściowych. Wyjściowy zestaw danych kieruje harmonogramem potoku (co godzinę, codziennie). 

    Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez działanie platformy Spark, zobacz sekcję [właściwości działania Spark](#spark-activity-properties).

1. Aby wdrożyć potok, wybierz **Wdróż** na pasku poleceń.

### <a name="monitor-a-pipeline"></a>Monitorowanie potoku
1. Na **usługi Data factory** bloku wybierz **monitorowanie i zarządzanie** można uruchomić monitorowania aplikacji na innej karcie.

    ![Kafelek Monitorowanie i zarządzanie](media/data-factory-spark/monitor-and-manage-tile.png)

1. Zmiana **czas rozpoczęcia** filtru u góry, aby **2/1/2017**i wybierz **Zastosuj**.

1. Tylko jedno działanie okno pojawia się, ponieważ istnieje tylko jeden dzień od rozpoczęcia (2017-02-01) i zakończenia (2017-02-02) potoku. Upewnij się, że wycinek danych znajduje się w **gotowe** stanu.

    ![Monitorowanie potoku](media/data-factory-spark/monitor-and-manage-app.png)

1. W **okien działania** wybierz uruchomienie działania, aby wyświetlić jego szczegóły. Jeśli wystąpi błąd, możesz zobaczyć szczegółowe informacje o nim w okienku po prawej stronie.

### <a name="verify-the-results"></a>Sprawdź wyniki

1. Rozpocznij notesu programu Jupyter dla klastra usługi HDInsight Spark, przechodząc do [tej witryny sieci Web](https://CLUSTERNAME.azurehdinsight.net/jupyter). Można także otworzyć pulpit nawigacyjny klastra usługi HDInsight Spark klastra, a następnie uruchom notesu programu Jupyter.

1. Wybierz **New** > **PySpark** można uruchomić nowy notes.

    ![Nowy notes Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Kopiowanie i wklejanie tekstu i naciskając klawisz Shift + Enter na końcu druga instrukcja, uruchom następujące polecenie:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Upewnij się, że wyświetlane dane z tabeli hvac. 

    ![Wyniki zapytania programu Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Aby uzyskać szczegółowe instrukcje, zobacz sekcję [uruchomienia zapytania Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Ponieważ getdebuginfo — jest ustawiona na **zawsze**, zostanie wyświetlony w podfolderze dziennika w folderze pyFiles w kontenerze obiektów blob. Dodatkowe informacje znajdują się w pliku dziennika w folderze dziennika. Ten plik dziennika jest szczególnie przydatne, gdy występuje błąd. W środowisku produkcyjnym warto ustawić ją na **błąd**.

Aby uzyskać dodatkowe informacje o rozwiązywaniu, wykonaj następujące czynności:


1. Przejdź do pozycji `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` (Plik > Nowy > Inny).

    ![Aplikacja w interfejsie użytkownika YARN](media/data-factory-spark/yarnui-application.png)

1. Wybierz **dzienniki** jeden przebieg prób.

    ![Strona aplikacji](media/data-factory-spark/yarn-applications.png)

1. Zostaną wyświetlone następujące dodatkowe informacje na stronie dziennik:

    ![Błąd dziennika](media/data-factory-spark/yarnui-application-error.png)

Poniższe sekcje zawierają informacje na temat jednostek fabryki danych do użycia z klastra Spark i działania platformy Spark w fabryce danych.

## <a name="spark-activity-properties"></a>Właściwości działania platformy Spark
Oto przykład definicji JSON potok za pomocą działania platformy Spark: 

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

W poniższej tabeli opisano właściwości JSON używanych w definicji JSON.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| name | Nazwa działania w potoku. | Yes |
| description | Tekst, który opisuje, co działanie robi. | Nie |
| type | Ta właściwość musi być równa HDInsightSpark. | Yes |
| linkedServiceName | Nazwa usługi połączonej HDInsight, na którym działa program platformy Spark. | Yes |
| rootPath | Kontener obiektów blob i folder zawierający plik platformy Spark. Nazwa pliku jest uwzględniana wielkość liter. | Yes |
| entryFilePath | Względna ścieżka do folderu głównego kodu lub pakietu platformy Spark. | Yes |
| className | Główna klasa platformy Java lub Spark aplikacji. | Nie |
| arguments | Lista argumentów wiersza polecenia do programu platformy Spark. | Nie |
| proxyUser | Konto użytkownika do personifikacji, aby wykonać program platformy Spark. | Nie |
| sparkConfig | Określ wartości dla właściwości konfiguracji aparatu Spark na liście [Spark konfiguracji: Właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie |
| getDebugInfo | Określa, kiedy Spark pliki dziennika są kopiowane do miejsca używanego przez klaster HDInsight (lub) określony przez sparkJobLinkedService. Dozwolone wartości to None, zawsze lub błąd. Wartość domyślna to Brak. | Nie |
| sparkJobLinkedService | Magazyn połączone usługi, która ma platformy Spark, zależności, dzienniki i plik zadania. Jeśli nie określisz wartości dla tej właściwości jest używany Magazyn skojarzony z klastrem HDInsight. | Nie |

## <a name="folder-structure"></a>Struktura folderów
Działanie platformy Spark nie obsługuje wbudowany skrypt jako Pig i wykonaj działania programu Hive. Zadań platformy Spark są również bardziej extensible niż zadania programów Pig/Hive. W przypadku zadań platformy Spark, możesz podać wiele zależności takich jak jar pakietów (umieszczona w ścieżce klas w języku java), pliki języka Python (umieszczona na PYTHONPATH) i innych plików.

Utwórz następującą strukturę folderów w usłudze blob storage przywoływany przez usługę HDInsight połączone. Następnie przekaż pliki zależne do odpowiednich podfolderów w folderze głównym, reprezentowane przez **właściwość entryFilePath**. Na przykład przekazywania plików języka Python do podfolderu pyFiles i pliki do podfolderu folderu głównego w plikach JAR jar. W czasie wykonywania usługa Data Factory oczekuje następującą strukturę folderów w magazynie obiektów blob: 

| Ścieżka | Opis | Wymagane | Type |
| ---- | ----------- | -------- | ---- |
| . | Ścieżka katalogu głównego zadania Spark w połączonej usługi storage. | Yes | Folder |
| &lt;zdefiniowane przez użytkownika &gt; | Ścieżka, który wskazuje na pliku wejściowego zadania Spark. | Yes | Plik |
| ./jars | Wszystkie pliki w tym folderze są przekazywane i umieszczane w ścieżce klasy Java klastra. | Nie | Folder |
| ./pyFiles | Wszystkie pliki w tym folderze są przekazywane i umieszczane w PYTHONPATH klastra. | Nie | Folder |
| ./files | Wszystkie pliki w tym folderze są przekazywane i umieszczane w katalogu roboczego funkcji wykonawczej. | Nie | Folder |
| ./archives | Wszystkie pliki w tym folderze są bez kompresji. | Nie | Folder |
| ./logs | Folder, w którym są przechowywane dzienniki z klastra Spark.| Nie | Folder |

Oto przykład dla magazynu, która zawiera dwa pliki zadania Spark w usłudze blob storage odwołują się usługi połączonej HDInsight:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
