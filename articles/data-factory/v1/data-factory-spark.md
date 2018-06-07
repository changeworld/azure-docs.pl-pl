---
title: Wywoływanie programów Spark z fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wywołać Spark programy z fabryki danych Azure za pomocą działania MapReduce.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d22829217209b7d0b1b5690d6a864b58bf102e3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622282"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Wywoływanie programów Spark z potoków fabryki danych Azure

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie gałęzi](data-factory-hive-activity.md)
> * [Działanie pig](data-factory-pig-activity.md)
> * [Działania MapReduce](data-factory-map-reduce.md)
> * [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego usługi uczenie maszyny](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizacji szkoleniowego maszyny](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedury składowanej](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL działania](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Przekształcanie danych za pomocą działania Apache Spark w fabryce danych w wersji 2](../transform-data-using-spark.md).

## <a name="introduction"></a>Wprowadzenie
Działanie Spark jest jednym z [działań przekształcania danych](data-factory-data-transformation-activities.md) obsługiwane przez fabryki danych. To działanie uruchamia określony program Spark w klastrze Spark w usłudze Azure HDInsight. 

> [!IMPORTANT]
> - Działanie Spark nie obsługuje klastrów HDInsight Spark, które używają usługi Azure Data Lake Store jako podstawowy magazyn.
> - Działanie Spark obsługuje tylko istniejące (własne) klastry HDInsight Spark. Nie obsługuje usługi HDInsight połączony na żądanie.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Wskazówki: Tworzenie potoku z działaniem Spark
Poniżej przedstawiono typowe etapy, aby utworzyć potok fabryki danych z działaniem Spark: 

* Tworzenie fabryki danych.
* Utwórz połączoną usługą magazynu Azure do łączenia z magazynu, który jest skojarzony z klastrem Spark w usłudze HDInsight z fabryką danych.
* Tworzenie usługi HDInsight połączony do połączenia klastra Spark w usłudze HDInsight z fabryką danych.
* Tworzenie zestawu danych, który odwołuje się do połączonej usługi magazynu. Obecnie należy określić wyjściowy zestaw danych działania nawet jeśli dostępny jest nie wytworzonych. 
* Utworzyć potok z działaniem Spark, odnoszący się do usługi HDInsight połączone, utworzony. Działanie jest skonfigurowany z zestawu danych, utworzony w poprzednim kroku jako wyjściowego zestawu danych. Wyjściowy zestaw danych jest podstawę harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych, nawet jeśli działanie nie naprawdę utworzenie danych wyjściowych.

### <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz konto magazynu ogólnego przeznaczenia, postępując zgodnie z instrukcjami w [Utwórz konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

2. Tworzenie klastra Spark w usłudze HDInsight, postępując zgodnie z instrukcjami w samouczku [Tworzenie klastra Spark w usłudze HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Skojarz konto magazynu, który został utworzony w kroku 1 z tym klastrem.

3. Pobierz i przejrzyj plik skryptu języka Python **test.py** znajdujący się w [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

4. Przekaż **test.py** do **pyFiles** folderu w **adfspark** kontenera w magazynie obiektów blob. Tworzenie kontenera i folderu, jeśli nie istnieją.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Aby utworzyć fabrykę danych, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Nowy** > **Dane i analiza** > **Fabryka danych**.

3. Na **nowa fabryka danych** bloku, w obszarze **nazwa**, wprowadź **SparkDF**.

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych SparkDF nie jest dostępna", należy zmienić nazwy fabryki danych. Na przykład użyć yournameSparkDFdate i ponownie utworzyć fabryki danych. Aby uzyskać więcej informacji na temat reguł nazewnictwa, zobacz [Data Factory: Naming rules (Fabryka danych: reguły nazewnictwa)](data-factory-naming-rules.md).

4. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w ramach której chcesz utworzyć fabrykę danych.

5. Wybierz istniejącą grupę zasobów lub Utwórz grupę zasobów platformy Azure.

6. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego**.

7. Wybierz pozycję **Utwórz**.

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, musisz być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.

8. Fabryka danych możesz sprawdzić, jak jest tworzony na pulpicie nawigacyjnym portalu Azure.

9. Po utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych** z zawartością fabryki danych. Jeśli nie widzisz **fabryki danych** wybierz Kafelek fabrykę danych na pulpicie nawigacyjnym.

    ![Blok Fabryka danych](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwa połączonych usług. Jedna usługa łączy klastra Spark z fabryką danych, i inne usługi łączy magazyn z fabryką danych. 

#### <a name="create-a-storage-linked-service"></a>Tworzenie połączonej usługi Storage
W tym kroku opisano łączenie konta magazynu z fabryką danych. Zestaw danych utworzone w kroku później w tym przewodniku odnosi się do tej połączonej usługi. Usługa HDInsight połączone definiowana w następnym kroku odwołuje się do tej połączonej usługi zbyt. 

1. Na **fabryki danych** bloku, wybierz opcję **tworzenie i wdrażanie**. Zostanie wyświetlony Edytor fabryki danych.

2. Wybierz pozycję **Nowy magazyn danych**, a następnie opcję **Azure Storage**.

   ![Nowy magazyn danych](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

3. Skryptu JSON, który jest używany do utworzenia magazynu połączonej usługi zostanie wyświetlony w edytorze.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Zastąp **nazwa konta** i **klucz konta** z nazwy i klucza dostępu konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zapoznaj się z informacjami na temat sposobów wyświetlania, kopiowania i ponownego generowania kluczy dostępu do magazynu podanymi w sekcji [Zarządzanie kontem magazynu](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

5. Aby wdrożyć połączonej usługi, wybierz **Wdróż** na pasku poleceń. Po pomyślnym wdrożeniu połączonej usługi okno Wersja robocza-1 zniknie. W widoku drzewa po lewej stronie pojawi się wartość **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight
W tym kroku utworzysz usługi HDInsight połączony do połączenia z klastrem Spark w usłudze HDInsight z fabryką danych. Klaster usługi HDInsight służy do uruchomienia programu Spark określony w działaniu Spark potoku, w tym przykładzie. 

1. W edytorze fabryki danych, wybierz **więcej** > **nowych obliczeń** > **klastra usługi HDInsight**.

    ![Tworzenie połączonej usługi HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

2. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. W edytorze JSON wykonaj następujące czynności:

    a. Określ identyfikator URI dla klastra Spark w usłudze HDInsight. Na przykład: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Określ nazwę użytkownika, który ma dostęp do klastra Spark.

    c. Określ hasło dla użytkownika.

    d. Określ połączoną usługą magazynu skojarzonego z klastrem Spark w usłudze HDInsight. W tym przykładzie jest AzureStorageLinkedService.

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
    > - Działanie Spark nie obsługuje klastrów HDInsight Spark, które używają usługi Azure Data Lake Store jako podstawowy magazyn.
    > - Działanie Spark obsługuje tylko istniejące (własne) klastry HDInsight Spark. Nie obsługuje usługi HDInsight połączony na żądanie.

    Aby uzyskać więcej informacji o usłudze HDInsight połączone, zobacz [HDInsight połączona usługa](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

3. Aby wdrożyć połączonej usługi, wybierz **Wdróż** na pasku poleceń. 

### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Wyjściowy zestaw danych jest podstawę harmonogram (co godzinę, codziennie). W związku z tym należy określić wyjściowy zestaw danych działania Spark w potoku, nawet jeśli działanie nie generuje żadnego wyniku. Określenie zestawem danych wejściowych dla działania jest opcjonalne.

1. W Edytorze fabryki danych wybierz pozycję **Więcej** > **Nowy zestaw danych** > **Magazyn obiektów blob Azure**.

2. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Fragment kodu JSON definiuje zestaw danych o nazwie **OutputDataset**. Ponadto określ że wyniki są przechowywane w kontenerze obiektów blob o nazwie **adfspark** i folder o nazwie **pyFiles/wyjścia**. Jak wspomniano wcześniej, ten zestaw danych jest fikcyjny zestawu danych. Program Spark, w tym przykładzie nie generuje żadnego wyniku. **Dostępności** sekcja określa, że wyjściowy zestaw danych jest tworzony codziennie. 

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
3. Aby wdrożyć zestawu danych, wybierz **Wdróż** na pasku poleceń.


### <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku możesz utworzyć potok z działaniem HDInsightSpark. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. W związku z tym nie wejściowy zestaw danych został określony w tym przykładzie.

1. W Edytorze fabryki danych wybierz pozycję **Więcej** > **Nowy potok**.

2. Zastąp skryptu w oknie Projekt 1 następujący skrypt:

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

    b. **Właściwość rootPath** właściwość jest ustawiona na **adfspark\\pyFiles** gdzie adfspark jest kontenera obiektów blob, a pyFiles folderu plików w danym kontenerze. W tym przykładzie magazynu obiektów blob jest skojarzony z klastrem Spark. Można przekazać pliku do innego konta magazynu. Jeśli tak zrobisz, Utwórz połączoną usługą magazynu połączyć konto magazynu z fabryką danych. Następnie określ jako wartość dla nazwy połączonej usługi **sparkJobLinkedService** właściwości. Aby uzyskać więcej informacji na temat tej właściwości oraz inne właściwości obsługiwane przez działanie Spark zobacz [Spark właściwości działania](#spark-activity-properties).

    c. **EntryFilePath** właściwość jest ustawiona na **test.py**, czyli plik Python.

    d. **GetDebugInfo** właściwość jest ustawiona na **zawsze**, co oznacza, że pliki dziennika są zawsze generowany (powodzenie lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, że nie należy ustawiać tej właściwości `Always` w środowisku produkcyjnym, chyba że w przypadku rozwiązywania problemów.

    e. **Generuje** sekcja ma jeden wyjściowy zestaw danych. Należy określić wyjściowy zestaw danych, nawet jeśli Spark program nie generuje żadnego wyniku. Wyjściowy zestaw danych dysków harmonogramu dla potoku (co godzinę, codziennie). 

    Aby uzyskać więcej informacji o obsługiwanych przez działanie Spark właściwości, zobacz sekcję [Spark właściwości działania](#spark-activity-properties).

3. Aby wdrożyć potok, wybierz **Wdróż** na pasku poleceń.

### <a name="monitor-a-pipeline"></a>Monitorowanie potoku
1. Na **fabryki danych** bloku, wybierz opcję **Monitor & Zarządzaj** można uruchomić monitorowania aplikacji w innej karty.

    ![Kafelek Monitorowanie i zarządzanie](media/data-factory-spark/monitor-and-manage-tile.png)

2. Zmień **godzina rozpoczęcia** filtru na górze do **2/1/2017**i wybierz **Zastosuj**.

3. Tylko jedno działanie okna pojawia się, ponieważ istnieje tylko jeden dzień między (2017-02-01) czasu rozpoczęcia i zakończenia (2017-02-02) potoku. Upewnij się, że wycinek danych jest w **gotowe** stanu.

    ![Monitorowanie potoku](media/data-factory-spark/monitor-and-manage-app.png)

4. W **okien działania** listy, wybierz opcję Uruchom, aby wyświetlić szczegółowe informacje o jego działania. Jeśli występuje błąd, zobaczysz szczegółowe informacje o nim w okienku po prawej stronie.

### <a name="verify-the-results"></a>Sprawdź wyniki

1. Uruchom do aplikacji Jupyter Notebook dla klastra Spark w usłudze HDInsight, przechodząc do [tej witryny sieci Web](https://CLUSTERNAME.azurehdinsight.net/jupyter). Można także otworzyć pulpit nawigacyjny klastra Spark w usłudze HDInsight — dla klastra, a następnie uruchom notesu Jupyter.

2. Wybierz **nowy** > **PySpark** uruchomić nowy notes.

    ![Nowego notesu Jupyter](media/data-factory-spark/jupyter-new-book.png)

3. Uruchom następujące polecenie, kopiując i wklejając tekst i naciśnięcie klawiszy Shift + Enter na końcu drugiej instrukcji:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Upewnij się, że zostaną wyświetlone dane z tabeli hvac. 

    ![Wyniki zapytania Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Aby uzyskać szczegółowe instrukcje, zobacz sekcję [uruchamiania zapytań Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Ponieważ ustawioną getDebugInfo **zawsze**, zobacz podfolder dziennika w folderze pyFiles w kontenerze obiektu blob. Dodatkowe informacje znajdują się w pliku dziennika w folderze dziennika. Ten plik dziennika jest szczególnie przydatna w przypadku, gdy występuje błąd. W środowisku produkcyjnym można ustawić ją na **błąd**.

Aby uzyskać dodatkowe informacje o rozwiązywaniu, wykonaj następujące czynności:


1. Przejdź do pozycji `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` (Plik > Nowy > Inny).

    ![Aplikacja YARN interfejsu użytkownika](media/data-factory-spark/yarnui-application.png)

2. Wybierz **dzienniki** jednego uruchomienia prób.

    ![Strona aplikacji](media/data-factory-spark/yarn-applications.png)

3. Pojawić następujące dodatkowe informacje na stronie dziennika:

    ![Błąd dziennika](media/data-factory-spark/yarnui-application-error.png)

Poniższe sekcje zawierają informacje na temat jednostek fabryki danych do używania klastra Spark i działania Spark w fabryce danych.

## <a name="spark-activity-properties"></a>Właściwości działania Spark
Oto przykład definicji JSON potoku z działaniem Spark: 

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

W poniższej tabeli opisano właściwości JSON używane w definicji JSON.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| name | Nazwa działania w potoku. | Yes |
| description | Tekst, który opisuje, co działanie robi. | Nie |
| type | Ta właściwość musi mieć ustawioną HDInsightSpark. | Yes |
| linkedServiceName | Nazwa usługi HDInsight połączone, na którym jest uruchomiony Spark program. | Yes |
| rootPath | Kontener obiektów blob i folder zawierający plik Spark. Nazwa pliku jest uwzględniana wielkość liter. | Yes |
| entryFilePath | Ścieżka względna do folderu głównego Spark kodu/pakietu. | Yes |
| className | Aplikacji Java/Spark klasy głównym. | Nie |
| Argumenty | Lista argumentów wiersza polecenia do programu Spark. | Nie |
| proxyUser | Konto użytkownika do personifikacji do wykonania programu Spark. | Nie |
| sparkConfig | Określ wartości dla właściwości konfiguracji Spark na liście [konfiguracji Spark: właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie |
| getDebugInfo | Określa, kiedy Spark pliki dziennika są kopiowane do magazynu używane przez klaster usługi HDInsight (lub) został określony przez sparkJobLinkedService. Dozwolone wartości to None, zawsze lub niepowodzenie. Wartość domyślna to None. | Nie |
| sparkJobLinkedService | Magazyn połączone usługi, która ma Spark plik zadania, zależności i dzienniki. Jeśli nie określisz wartości dla tej właściwości jest używana magazynem skojarzonym z klastrem usługi HDInsight. | Nie |

## <a name="folder-structure"></a>Struktura folderów
Działanie Spark nie obsługuje wbudowanego skryptu jako Pig i do gałęzi działań. Zadań Spark jest również extensible więcej niż zadań Pig/Hive. W przypadku zadań Spark, możesz podać wiele zależności takich jak jar pakietów (umieszczona w języku java ścieżki klasy), pliki języka Python (dotyczącymi PYTHONPATH) i innych plików.

Utwórz następującą strukturę folderów w magazynie obiektów blob przywoływany przez usługę HDInsight połączone. Natomiast przekazywanie plików zależnych do odpowiednich podfolderów w folderze głównym reprezentowany przez **entryFilePath**. Na przykład przekazywania plików języka Python do podfolderu pyFiles i jar pliki są kopiowane do podfolderu słoików folderu głównego. W czasie wykonywania usługi fabryka danych z oczekuje następującej struktury folderu w magazynie obiektów blob: 

| Ścieżka | Opis | Wymagane | Typ |
| ---- | ----------- | -------- | ---- |
| . | Ścieżka katalogu głównego zadania Spark w usłudze magazynu połączone. | Yes | Folder |
| &lt;Zdefiniowane przez użytkownika &gt; | Ścieżka, który wskazuje plik wpis zadania Spark. | Yes | Plik |
| . / jars | Wszystkie pliki w tym folderze są przekazywane i dotyczącymi klasy Java klastra. | Nie | Folder |
| . / pyFiles | Wszystkie pliki w tym folderze są przekazywane i dotyczącymi PYTHONPATH klastra. | Nie | Folder |
| . / pliki | Wszystkie pliki w tym folderze są przekazywane i umieszczane w katalogu roboczego Moduł wykonujący. | Nie | Folder |
| . / archiwa | Wszystkie pliki w tym folderze są nieskompresowane. | Nie | Folder |
| . / dzienników | Folder, w którym są przechowywane dzienniki w klastrze Spark.| Nie | Folder |

Oto przykład dla magazynu, który zawiera dwa pliki zadania Spark w magazynie obiektów blob przywoływany przez usługę HDInsight połączone:

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
