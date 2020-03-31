---
title: Wywoływanie programów Spark z usługi Azure Data Factory
description: Dowiedz się, jak wywołać programy Platformy Spark z fabryki danych platformy Azure przy użyciu działania MapReduce.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: ce5fb014c7d954b3e8430a86430c6a666adff204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969246"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Wywoływanie programów Spark z potoków usługi Azure Data Factory

> [!div class="op_single_selector" title1="Działania związane z transformacją"]
> * [Aktywność gałęzi](data-factory-hive-activity.md)
> * [Aktywność świń](data-factory-pig-activity.md)
> * [Działanie mapreduce](data-factory-map-reduce.md)
> * [Aktywność w serwisie Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Aktywność iskierki](data-factory-spark.md)
> * [Działanie wsadowe uczenia maszynowego](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie zasobów aktualizacji usługi Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działanie procedury składowanej](data-factory-stored-proc-activity.md)
> * [Działanie U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształcanie danych przy użyciu działania Apache Spark w fabryce danych](../transform-data-using-spark.md).

## <a name="introduction"></a>Wprowadzenie
Spark działania jest jednym z [działań transformacji danych obsługiwanych](data-factory-data-transformation-activities.md) przez fabrykę danych. To działanie uruchamia określony program Spark w klastrze platformy Spark w usłudze Azure HDInsight.

> [!IMPORTANT]
> - Działanie platformy Spark nie obsługuje klastrów platformy Spark usługi HDInsight, które używają usługi Azure Data Lake Store jako magazynu podstawowego.
> - Działanie Platformy Spark obsługuje tylko istniejące (własne) klastry platformy SPARK hdinsight. Nie obsługuje usługi połączonej HDInsight na żądanie.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Instruktaż: Tworzenie potoku z działaniem platformy Spark
Oto typowe kroki tworzenia potoku fabryki danych z działaniem platformy Spark:

* Tworzenie fabryki danych.
* Utwórz usługę połączone usługi Azure Storage, aby połączyć magazyn skojarzony z klastrem platformy SPARK usługi HDInsight z fabryką danych.
* Utwórz usługę połączona HDInsight, aby połączyć klaster Platformy Spark w usłudze HDInsight z fabryką danych.
* Utwórz zestaw danych, który odwołuje się do usługi połączonej magazyn. Obecnie należy określić wyjściowy zestaw danych dla działania, nawet jeśli nie jest produkowanych danych wyjściowych.
* Utwórz potok z działaniem platformy Spark, który odwołuje się do utworzonej usługi połączonej HDInsight. Działanie jest konfigurowane z zestawem danych utworzonym w poprzednim kroku jako wyjściowy zestaw danych. Wyjściowy zestaw danych jest tym, co napędza harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych, nawet jeśli działanie tak naprawdę nie generuje dane wyjściowe.

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz konto magazynu ogólnego przeznaczenia, postępując zgodnie z instrukcjami w [grze Utwórz konto magazynu](../../storage/common/storage-account-create.md).

1. Utwórz klaster platformy Spark w udziale w udziale w udziale w udziale w udziale [Tworzenie klastra platformy Spark w pliku HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Skojarz konto magazynu utworzone w kroku 1 z tym klastrem.

1. Pobierz i przejrzyj plik skryptu **Pythona test.py** znajdujący się pod adresem [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Przekaż **test.py** do folderu **pyFiles** w kontenerze **adfspark** w magazynie obiektów blob. Utwórz kontener i folder, jeśli nie istnieją.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Aby utworzyć fabrykę danych, wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Wybierz **nowe** > **dane + fabryka** > **danych**analizy .

1. Na bloku **Nowa fabryka danych** w obszarze **Nazwa**wprowadź **sparkdf**.

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych SparkDF nie jest dostępna", zmień nazwę fabryki danych. Na przykład użyj yournameSparkDFdate i ponownie utwórz fabrykę danych. Aby uzyskać więcej informacji na temat reguł nazewnictwa, zobacz [Data Factory: Naming rules (Fabryka danych: reguły nazewnictwa)](data-factory-naming-rules.md).

1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w ramach której chcesz utworzyć fabrykę danych.

1. Wybierz istniejącą grupę zasobów lub utwórz grupę zasobów platformy Azure.

1. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego**.

1. Wybierz **pozycję Utwórz**.

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, musisz być członkiem roli [współautora fabryki danych](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.

1. Zobaczysz fabrykę danych, jak jest tworzony na pulpicie nawigacyjnym witryny Azure portal.

1. Po utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych** z zawartością fabryki danych. Jeśli nie widzisz strony **Fabryka danych,** wybierz kafelek dla fabryki danych na pulpicie nawigacyjnym.

    ![Blok Fabryka danych](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi. Jedna usługa łączy klaster Platformy Spark z fabryką danych, a druga łączy magazyn z fabryką danych.

#### <a name="create-a-storage-linked-service"></a>Tworzenie połączonej usługi Storage
W tym kroku opisano łączenie konta magazynu z fabryką danych. Zestaw danych utworzony w kroku w dalszej części tego przewodnika odnosi się do tej połączonej usługi. Usługa połączona HDInsight zdefiniowana w następnym kroku odnosi się również do tej połączonej usługi.

1. W bloku **Fabryka danych** wybierz pozycję **Autor i wdruzuj**. Pojawi się Edytor fabryki danych.

1. Wybierz pozycję **Nowy magazyn danych**, a następnie opcję **Azure Storage**.

   ![Nowy magazyn danych](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Skrypt JSON używany do tworzenia usługi połączonej magazyn jest wyświetlany w edytorze.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Zastąp **nazwę konta** i **klucz konta** nazwą i kluczem dostępu konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

1. Aby wdrożyć usługę połączeniową, wybierz pozycję **Wdrażanie** na pasku poleceń. Po pomyślnym wdrożeniu połączonej usługi okno Wersja robocza-1 zniknie. W widoku drzewa po lewej stronie pojawi się wartość **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight
W tym kroku utworzysz usługę połączona HDInsight, aby połączyć klaster platformy SPARK usługi HDInsight z fabryką danych. Klaster HDInsight jest używany do uruchamiania programu Spark określonego w spark działania potoku w tym przykładzie.

1. W Edytorze fabryk danych wybierz pozycję **Więcej** > **nowych obliczeń** > **klastra HDInsight**.

    ![Tworzenie połączonej usługi HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. W edytorze JSON należy wykonać następujące kroki:

    a. Określ identyfikator URI dla klastra platformy Spark usługi HDInsight. Na przykład: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Określ nazwę użytkownika, który ma dostęp do klastra Platformy Spark.

    d. Podaj hasło użytkownika.

    d. Określ usługę połączone magazynu, która jest skojarzona z klastrem PLATFORMY SPARK usługi HDInsight. W tym przykładzie jest to AzureStorageLinkedService.

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
    > - Działanie platformy Spark nie obsługuje klastrów platformy Spark usługi HDInsight, które używają usługi Azure Data Lake Store jako magazynu podstawowego.
    > - Działanie Platformy Spark obsługuje tylko istniejące (własne) klastry platformy SPARK hdinsight. Nie obsługuje usługi połączonej HDInsight na żądanie.

    Aby uzyskać więcej informacji na temat usługi połączonej HDInsight, zobacz [usługa połączona HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Aby wdrożyć usługę połączeniową, wybierz pozycję **Wdrażanie** na pasku poleceń.

### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Wyjściowy zestaw danych jest tym, co napędza harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych dla działania platformy Spark w potoku, nawet jeśli działanie nie generuje żadnych danych wyjściowych. Określenie wejściowego zestawu danych dla działania jest opcjonalne.

1. W Edytorze fabryki danych wybierz pozycję **Więcej** > **nowego zestawu** > danych**usługi Azure Blob storage**.

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Fragment kodu JSON definiuje zestaw danych o nazwie **OutputDataset**. Ponadto należy określić, że wyniki są przechowywane w kontenerze obiektów blob o nazwie **adfspark** i folder o nazwie **pyFiles/output**. Jak wspomniano wcześniej, ten zestaw danych jest fikcyjnym zestawem danych. Program Spark w tym przykładzie nie generuje żadnych danych wyjściowych. Sekcja **dostępności** określa, że wyjściowy zestaw danych jest produkowany codziennie.

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
1. Aby wdrożyć zestaw danych, wybierz pozycję **Wdrażanie** na pasku poleceń.


### <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem HDInsightSpark. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. W związku z tym nie zestaw danych wejściowych jest określony w tym przykładzie.

1. W Edytorze fabryki danych wybierz pozycję **Więcej** > **nowego potoku**.

1. Zastąp skrypt w oknie Wersja robocza-1 następującym skryptem:

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

    a. Właściwość **type** jest ustawiona na **HDInsightSpark**.

    b. Właściwość **rootPath** jest **ustawiona\\na adfspark pyFiles,** gdzie adfspark jest kontenerem obiektów blob, a pyFiles jest folderem plików w tym kontenerze. W tym przykładzie magazynu obiektów blob jest ten, który jest skojarzony z klastrem Platformy Spark. Możesz przekazać plik na inne konto magazynu. Jeśli to zrobisz, utwórz usługę połączony magazyn, aby połączyć to konto magazynu z fabryką danych. Następnie należy określić nazwę połączonej usługi jako wartość właściwości **sparkJobLinkedService.** Aby uzyskać więcej informacji na temat tej właściwości i innych właściwości obsługiwanych przez działanie Platformy Spark, zobacz [właściwości działania platformy Spark.](#spark-activity-properties)

    d. Właściwość **entryFilePath** jest ustawiona na **test.py**, który jest plikiem Pythona.

    d. Właściwość **getDebugInfo** jest ustawiona na **Zawsze**, co oznacza, że pliki dziennika są zawsze generowane (powodzenie lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, aby nie ustawiać `Always` tej właściwości w środowisku produkcyjnym, chyba że rozwiązujesz problem.

    e. Sekcja wyjścia ma jeden **wyjściowy** zestaw danych. Należy określić wyjściowy zestaw danych, nawet jeśli program Spark nie generuje żadnych danych wyjściowych. Wyjściowy zestaw danych napędza harmonogram potoku (co godzinę, codziennie).

    Aby uzyskać więcej informacji na temat właściwości obsługiwanych przez działanie Spark, zobacz sekcję [Właściwości działania Platformy Spark](#spark-activity-properties).

1. Aby wdrożyć potok, wybierz **pozycję Deploy** na pasku poleceń.

### <a name="monitor-a-pipeline"></a>Monitorowanie potoku
1. W bloku **Fabryka danych** wybierz **pozycję Monitoruj & Zarządzaj,** aby uruchomić aplikację monitorowania na innej karcie.

    ![Kafelek Monitorowanie i zarządzanie](media/data-factory-spark/monitor-and-manage-tile.png)

1. Zmień filtr **Czas rozpoczęcia** u góry na **2/1/2017**i wybierz pozycję **Zastosuj**.

1. Tylko jedno okno działania jest wyświetlany, ponieważ istnieje tylko jeden dzień między rozpoczęciem (2017-02-01) i godzin zakończenia (2017-02-02) potoku. Upewnij się, że plasterek danych jest w stanie **Gotowy.**

    ![Monitorowanie potoku](media/data-factory-spark/monitor-and-manage-app.png)

1. Na liście **Okna działania** wybierz działanie uruchomione, aby wyświetlić szczegółowe informacje na jej temat. Jeśli występuje błąd, szczegóły na ten temat są widoczne w prawym okienku.

### <a name="verify-the-results"></a>Weryfikowanie wyników

1. Uruchom notebook Jupyter dla klastra HDInsight Spark, przechodząc do [tej witryny](https://CLUSTERNAME.azurehdinsight.net/jupyter). Można również otworzyć pulpit nawigacyjny klastra dla klastra PLATFORMY HDInsight Spark, a następnie uruchomić notes Jupyter.

1. Wybierz **pozycję Nowy** > **pyspark,** aby uruchomić nowy notes.

    ![Jupyter nowy notatnik](media/data-factory-spark/jupyter-new-book.png)

1. Uruchom następujące polecenie, kopiując i wklejając tekst i naciskając klawisze Shift+Enter na końcu drugiej instrukcji:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Upewnij się, że widzisz dane z tabeli hvac.

    ![Wyniki kwerendy jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Aby uzyskać szczegółowe instrukcje, zobacz [sekcję Uruchamianie kwerendy SQL platformy Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Ponieważ getDebugInfo jest **zawsze,** w folderze pyFiles w kontenerze obiektów blob zostanie wyświetlony podfolder dziennika. Plik dziennika w folderze dziennika zawiera dodatkowe informacje. Ten plik dziennika jest szczególnie przydatny w przypadku wystąpienia błędu. W środowisku produkcyjnym można ustawić go na **Błąd**.

Aby rozwiązać dalsze problemy, należy wykonać następujące kroki:


1. Przejdź do pozycji `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` (Plik > Nowy > Inny).

    ![Aplikacja interfejsu użytkownika YARN](media/data-factory-spark/yarnui-application.png)

1. Wybierz **dzienniki** dla jednej z prób uruchomienia.

    ![Strona aplikacji](media/data-factory-spark/yarn-applications.png)

1. Na stronie dziennika są widoczne następujące dodatkowe informacje o błędzie:

    ![Błąd dziennika](media/data-factory-spark/yarnui-application-error.png)

Poniższe sekcje zawierają informacje o jednostkach fabryki danych do używania aktywności klastra platformy Spark i platformy Spark w fabryce danych.

## <a name="spark-activity-properties"></a>Właściwości aktywności iskrowej
Oto przykładowa definicja JSON potoku z spark działania:

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
                    },
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

W poniższej tabeli opisano właściwości JSON używane w definicji JSON.

| Właściwość | Opis | Wymagany |
| -------- | ----------- | -------- |
| name | Nazwa działania w potoku. | Tak |
| description | Tekst opisujący działanie. | Nie |
| type | Ta właściwość musi być ustawiona na HDInsightSpark. | Tak |
| linkedServiceName | Nazwa połączonej usługi HDInsight, na której działa program Spark. | Tak |
| ścieżka rootPath | Kontener obiektu blob i folder zawierający plik Spark. W nazwie pliku rozróżniana jest wielkość liter. | Tak |
| wpisFilePath | Ścieżka względna do folderu głównego kodu/pakietu Platformy Spark. | Tak |
| Classname | Klasy głównej Java/Spark aplikacji. | Nie |
| Argumenty | Lista argumentów wiersza polecenia programu Spark. | Nie |
| proxyUżytnik | Konto użytkownika do personifikacji w celu wykonania programu Spark. | Nie |
| sparkConfig (niem. | Określ wartości właściwości konfiguracji platformy Spark wymienione w [obszarze Konfiguracja platformy Spark: Właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie |
| getDebugInfo | Określa, kiedy pliki dziennika platformy Spark są kopiowane do magazynu używanego przez klaster HDInsight (lub) określone przez sparkJobLinkedService. Dozwolone wartości to Brak, Zawsze lub Błąd. Wartość domyślna to Brak. | Nie |
| sparkJobLinkedService (SparkJobLinkedService) | Usługa połączona magazyn, która przechowuje plik zadania platformy Spark, zależności i dzienniki. Jeśli nie określisz wartości dla tej właściwości, używany jest magazyn skojarzony z klastrem HDInsight. | Nie |

## <a name="folder-structure"></a>Struktura folderów
Spark działania nie obsługuje skryptu wbudowanego, jak świąd i hive działania zrobić. Zadania spark są również bardziej rozszerzalne niż zadania Pig/Hive. W przypadku zadań platformy Spark można podać wiele zależności, takich jak pakiety jar (umieszczone w java CLASSPATH), pliki Pythona (umieszczone na PYTHONPATH) i inne pliki.

Utwórz następującą strukturę folderów w magazynie obiektów blob, do którego odwołuje się usługa połączona HDInsight. Następnie należy przesłać pliki zależne do odpowiednich podfolderów w folderze głównym reprezentowanym przez **entryFilePath**. Na przykład przekaż pliki Języka Python do podfolderu pyFiles i plików jar do podfolderu słoików folderu głównego. W czasie wykonywania usługa Data Factory oczekuje następującej struktury folderów w magazynie obiektów blob:

| Ścieżka | Opis | Wymagany | Typ |
| ---- | ----------- | -------- | ---- |
| . | Ścieżka główna zadania platformy Spark w połączonej usłudze magazynu. | Tak | Folder |
| &lt;zdefiniowane przez użytkownika&gt; | Ścieżka, która wskazuje plik wejścia zadania Spark. | Tak | Plik |
| ./słoiki | Wszystkie pliki w tym folderze są przekazywane i umieszczane na ścieżce klasy Java klastra. | Nie | Folder |
| ./pyFiles | Wszystkie pliki w tym folderze są przesyłane i umieszczane na PYTHONPATH klastra. | Nie | Folder |
| ./pliki | Wszystkie pliki w tym folderze są przekazywane i umieszczane w katalogu roboczym wykonawcy. | Nie | Folder |
| ./archiwum | Wszystkie pliki w tym folderze są nieskompresowane. | Nie | Folder |
| ./dzienniki | Folder, w którym przechowywane są dzienniki z klastra Platformy Spark.| Nie | Folder |

Oto przykład magazynu, który zawiera dwa pliki zadań platformy Spark w magazynie obiektów blob, do którego odwołuje się usługa połączona HDInsight:

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
