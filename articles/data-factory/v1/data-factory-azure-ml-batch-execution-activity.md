---
title: Tworzenie potoków danych predykcyjnych przy użyciu usługi Azure Data Factory
description: W tym artykule opisano sposób tworzenia potoków predykcyjnych przy użyciu usługi Azure Data Factory i usługi Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: eba5df587d6bd6dda6083314cfb94836c6669393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73683143"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Tworzenie potoków predykcyjnych przy użyciu usług Azure Machine Learning i Azure Data Factory

> [!div class="op_single_selector" title1="Działania związane z transformacją"]
> * [Aktywność gałęzi](data-factory-hive-activity.md)
> * [Aktywność świń](data-factory-pig-activity.md)
> * [Działanie mapreduce](data-factory-map-reduce.md)
> * [Aktywność strumieniowania Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktywność iskra](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działanie niestandardowe platformy .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Wprowadzenie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [przekształcanie danych przy użyciu uczenia maszynowego w fabryce danych](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługa Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umożliwia tworzenie, testowanie i wdrażanie rozwiązań analizy predykcyjnej. Z wysokiego poziomu jest to wykonywane w trzech krokach:

1. **Utwórz eksperyment szkoleniowy**. Ten krok należy wykonać przy użyciu studia usługi Azure Machine Learning. Studio usługi Azure Machine Learning to wspólne środowisko programistyczne, którego używasz do uczenia i testowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go na eksperyment predykcyjny**. Gdy model został przeszkolony z istniejącymi danymi i możesz go użyć do uzyskania nowych danych, przygotowujesz i usprawnisz eksperyment do oceniania.
3. **Wdrażanie go jako usługi sieci web**. Eksperyment oceniania można opublikować jako usługę sieci Web platformy Azure. Można wysyłać dane do modelu za pośrednictwem tego punktu końcowego usługi sieci web i odbierać prognozy wyników z modelu.

### <a name="azure-data-factory"></a>Azure Data Factory
Fabryka danych to usługa integracji danych oparta na chmurze, która organizuje i automatyzuje **przenoszenie** i **przekształcanie** danych. Można tworzyć rozwiązania integracji danych przy użyciu usługi Azure Data Factory, które można pozyskiwania danych z różnych magazynów danych, przekształcać/przetwarzać dane i publikować dane wynikowe do magazynów danych.

Usługa Data Factory pozwala tworzyć potoki danych służące do przenoszenia i przekształcania danych, a następnie uruchamiać te potoki zgodnie z zaplanowanym harmonogramem (co godzinę, codziennie, co tydzień itp.). Usługa ta udostępnia również rozbudowane wizualizacje umożliwiające wyświetlanie elementów powiązanych i zależności między potokami danych oraz monitorowanie wszystkich potoków danych w jednym zintegrowanym widoku, który ułatwia wykrywanie problemów i konfigurowanie alertów monitorowania.

Zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i tworzenie pierwszych artykułów [potoku,](data-factory-build-your-first-pipeline.md) aby szybko rozpocząć pracę z usługą Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Fabryka danych i uczenie maszynowe razem
Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web [usługi Azure Machine Learning][azure-machine-learning] do analizy predykcyjnej. Za pomocą **działania wykonywania wsadowego** w potoku usługi Azure Data Factory, można wywołać usługi sieci web studio usługi Azure Machine Learning, aby prognozować dane w partii. Zobacz wywoływanie usługi sieci web studio usługi Azure Machine Learning przy użyciu działania wykonywania wsadowego sekcji, aby uzyskać szczegółowe informacje.

Z biegiem czasu modele predykcyjne w studiu azure machine learning oceniania eksperymentów muszą być przeszkolone przy użyciu nowych wejściowych zestawów danych. Można przeszkolić model studio usługi Azure Machine Learning z potoku usługi Data Factory, wykonując następujące kroki:

1. Opublikuj eksperyment szkoleniowy (nie eksperyment predykcyjny) jako usługę sieci web. Ten krok należy wykonać w studio usługi Azure Machine Learning, tak jak w celu udostępnienia eksperymentu predykcyjnego jako usługi sieci web w poprzednim scenariuszu.
2. Użyj działania wsadowego studia usługi Azure Machine Learning, aby wywołać usługę sieci web dla eksperymentu szkoleniowego. Zasadniczo można użyć działania azure machine learning studio wsadowe wykonanie do wywoływania zarówno szkolenia usługi sieci web i oceniania usługi sieci web.

Po zakończeniu przekwalifikowania zaktualizuj usługę sieci web oceniania (eksperyment predykcyjny ujawniony jako usługa sieci web) przy użyciu nowo przeszkolonego modelu przy użyciu **działania zasobów usługi Azure Machine Learning studio Update Resource.** Aby uzyskać szczegółowe informacje, zobacz [Aktualizowanie modeli przy użyciu](data-factory-azure-ml-update-resource-activity.md) artykułu Aktualizuj działanie zasobów.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Wywoływanie usługi sieci web przy użyciu działania wykonywania wsadowego
Usługa Azure Data Factory służy do organizowania przenoszenia i przetwarzania danych, a następnie wykonywania wsadowego przy użyciu usługi Azure Machine Learning. Oto kroki najwyższego poziomu:

1. Tworzenie połączonej usługi usługi Azure Machine Learning. Potrzebne są następujące wartości:

   1. **Żądanie identyfikatora URI** dla interfejsu API wykonywania wsadowego. Identyfikator URI żądania można znaleźć, klikając łącze **WYKONYWANIE USŁUGI WSADOWE** na stronie usług sieci web.
   2. **Klucz interfejsu API** dla opublikowanej usługi sieci web usługi Azure Machine Learning. Klucz interfejsu API można znaleźć, klikając opublikowaną usługę sieci web.
   3. Użyj **działania AzureMLBatchExecution.**

      ![Pulpit nawigacyjny uczenia maszynowego](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identyfikator URI partii](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz: eksperymenty przy użyciu danych wejściowych/wyjściowych usługi sieci Web, które odwołują się do danych w magazynie obiektów blob platformy Azure
W tym scenariuszu usługa azure machine learning sieci Web sprawia, że prognoz przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i przechowuje wyniki prognozowania w magazynie obiektów blob. Następujący JSON definiuje potok fabryki danych z działaniem AzureMLBatchExecution. Działanie ma zestaw danych **DecisionTreeInputBlob** jako dane wejściowe i **DecisionTreeResultBlob** jako dane wyjściowe. **DecisionTreeInputBlob** jest przekazywana jako dane wejściowe do usługi sieci web przy użyciu **właściwości webServiceInput** JSON. **DecisionTreeResultBlob** jest przekazywana jako dane wyjściowe do usługi sieci Web przy użyciu **właściwości JSON webServiceOutputs.**

> [!IMPORTANT]
> Jeśli usługa sieci web pobiera wiele danych wejściowych, należy użyć **właściwości webServiceInputs** zamiast używać **webServiceInput**. Zobacz [usługi sieci Web wymaga wielu danych wejściowych](#web-service-requires-multiple-inputs) sekcji na przykład przy użyciu webServiceInputs właściwości.
>
> Zestawy danych, do których odwołują się **webServiceInput**/**webServiceInput i** **webServiceOutputs** właściwości (w **typeProperties)** muszą być również zawarte w **danych wejściowych** działania i **wyjść**.
>
> W eksperymencie studio usługi Azure Machine Learning porty wejściowe i wyjściowe usługi sieci web oraz parametry globalne mają nazwy domyślne ("input1", "input2"), które można dostosować. Nazwy używane dla webServiceInputs, webServiceOutputs i globalParameters ustawienia muszą dokładnie odpowiadać nazwy w eksperymentach. Można wyświetlić ładunek żądania przykładu na stronie Pomocy wykonywania wsadu dla punktu końcowego studio usługi Azure Machine Learning, aby zweryfikować oczekiwane mapowanie.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Tylko dane wejściowe i wyjściowe działania AzureMLBatchExecution mogą być przekazywane jako parametry do usługi sieci Web. Na przykład w powyższym fragmentie kodu JSON DecisionTreeInputBlob jest dane wejściowe do działania AzureMLBatchExecution, który jest przekazywany jako dane wejściowe do usługi sieci Web za pośrednictwem webServiceInput parametru.
>
>

### <a name="example"></a>Przykład
W tym przykładzie używa usługi Azure Storage do przechowywania danych wejściowych i wyjściowych.

Przed przejściem przez ten przykład należy przejść przez samouczek [Skompiluj swój pierwszy potok za pomocą][adf-build-1st-pipeline] usługi Data Factory. Edytor fabryki danych służy do tworzenia artefaktów fabryki danych (połączone usługi, zestawy danych, potok) w tym przykładzie.

1. Utwórz **połączony serwis** dla usługi **Azure Storage**. Jeśli pliki wejściowe i wyjściowe znajdują się na różnych kontach magazynu, potrzebne są dwie połączone usługi. Oto przykład JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Utwórz **wejściowy** **zestaw danych**usługi Azure Data Factory . W przeciwieństwie do innych zestawów danych fabryki danych te zestawy danych muszą zawierać wartości **folderPath** i **fileName.** Partycjonowanie służy do tworzenia każdej wsadowej (każdy plasterek danych) do przetwarzania lub tworzenia unikatowych plików wejściowych i wyjściowych. Może być konieczne uwzględnienie niektórych działań nadrzędnych, aby przekształcić dane wejściowe w format pliku CSV i umieścić je na koncie magazynu dla każdego plasterka. W takim przypadku nie będzie zawierać **ustawienia zewnętrzne** i **externalData** pokazano w poniższym przykładzie, a DecisionTreeInputBlob będzie wyjściowy zestaw danych innego działania.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    Plik wejściowy csv musi mieć wiersz nagłówka kolumny. Jeśli używasz **działania kopiowania** do tworzenia/przenoszenia pliku csv do magazynu obiektów blob, należy ustawić właściwość ujścia **blobWriterAddHeader** na **true**. Przykład:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Jeśli plik csv nie ma wiersza nagłówka, może zostać wyświetlony następujący błąd: **Błąd w działaniu: Ciąg odczytu błędu. Nieoczekiwany token: StartObject. Ścieżka '', linia 1, pozycja 1**.
3. Utwórz **wyjściowy** **zestaw danych**usługi Azure Data Factory . W tym przykładzie użyto partycjonowania, aby utworzyć unikatową ścieżkę wyjściową dla każdego wykonania plasterka. Bez partycjonowania działanie zastąpiłoby plik.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Utwórz **połączone usługi** typu: **AzureMLLinkedService**, zapewniając klucz interfejsu API i adres URL wykonywania partii modelu.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Na koniec autor potoku zawierającego **działanie AzureMLBatchExecution.** W czasie wykonywania potok wykonuje następujące kroki:

   1. Pobiera lokalizację pliku wejściowego z wejściowych zestawów danych.
   2. Wywołuje interfejs API wykonywania wsadowego usługi Azure Machine Learning
   3. Kopiuje dane wyjściowe wykonania partii do obiektu blob podane w zestawie danych wyjściowych.

      > [!NOTE]
      > Działanie AzureMLBatchExecution może mieć zero lub więcej danych wejściowych i co najmniej jeden wynik wyjściowy.
      >
      >

      ```JSON
      {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      Zarówno **data rozpoczęcia,** jak i **koniec** datowania musi być w [formacie ISO.](https://en.wikipedia.org/wiki/ISO_8601) Na przykład: 2014-10-14T16:32:41Z. Godzina **zakończenia** jest opcjonalna. Jeśli nie określisz wartości właściwości **końcowej,** zostanie ona obliczona jako "**start + 48 godzin.**" Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

      > [!NOTE]
      > Określanie danych wejściowych dla działania AzureMLBatchExecution jest opcjonalne.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz: Eksperymenty przy użyciu modułów czytnika/modułów modułów modułów modułów w celu odwoływania się do danych w różnych magazynach
Innym typowym scenariuszem podczas tworzenia eksperymentów studio usługi Azure Machine Learning jest użycie modułów programu Reader i Writer. Moduł czytnika służy do ładowania danych do eksperymentu, a moduł modułu modułu modułu zapisującego jest zapisywanie danych z eksperymentów. Aby uzyskać szczegółowe informacje na temat modułów czytnika i modułu zapisującego, zobacz [Tematy programu Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) w bibliotece MSDN.

Podczas korzystania z modułów czytnika i modułu modułu zapisującego, dobrą praktyką jest użycie parametru usługi sieci Web dla każdej właściwości tych modułów czytnika/modułu modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów modułów Te parametry sieci web umożliwiają skonfigurowanie wartości w czasie wykonywania. Na przykład można utworzyć eksperyment z modułem czytnika, który używa usługi Azure SQL Database: XXX.database.windows.net. Po wdrożeniu usługi sieci web chcesz włączyć konsumentów usługi sieci web, aby określić inny program Azure SQL Server o nazwie YYY.database.windows.net. Można użyć parametru usługi sieci Web, aby zezwolić na skonfigurowanie tej wartości.

> [!NOTE]
> Dane wejściowe i wyjściowe usługi sieci Web różnią się od parametrów usługi sieci Web. W pierwszym scenariuszu można zobaczyć, jak dane wejściowe i wyjściowe można określić dla usługi Azure Machine Learning studio usługi sieci Web. W tym scenariuszu należy przekazać parametry dla usługi sieci Web, które odpowiadają właściwości modułów czytnika/modułu modułów modułów modułów.
>
>

Przyjrzyjmy się scenariuszowi korzystania z parametrów usługi sieci Web. Masz wdrożoną usługę sieci web usługi Azure Machine Learning, która używa modułu czytnika do odczytu danych z jednego ze źródeł danych obsługiwanych przez usługę Azure Machine Learning (na przykład: Usługa Azure SQL Database). Po wykonaniu wsadowego wyniki są zapisywane przy użyciu modułu modułu writer (azure sql database).  W eksperymentach nie zdefiniowano żadnych wejść i wyjść usługi sieci web. W takim przypadku zaleca się skonfigurowanie odpowiednich parametrów usługi sieci web dla modułów czytnika i modułów modułów modułów modułów modułów modułów modułów. Ta konfiguracja umożliwia moduły czytnika/modułu zapisującego, które mają być skonfigurowane podczas korzystania z działania AzureMLBatchExecution. Parametry usługi sieci Web należy określić w sekcji **globalParameters** w działaniu JSON w następujący sposób.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Można również użyć [funkcji fabryki danych](data-factory-functions-variables.md) podczas przekazywania wartości parametrów usługi sieci Web, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> W parametrach usługi sieci Web rozróżniana jest wielkość liter, dlatego należy upewnić się, że nazwy określone w usłudze JSON są zgodne z nazwami ujawnionymi przez usługę sieci Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Używanie modułu reader do odczytywania danych z wielu plików w obiekcie Blob platformy Azure
Potoki dużych zbiorów danych z działań, takich jak Świnia i Hive można utworzyć jeden lub więcej plików wyjściowych bez rozszerzeń. Na przykład po określeniu zewnętrznej tabeli gałęzi dane dla zewnętrznej tabeli gałęzi mogą być przechowywane w magazynie obiektów blob platformy Azure o następującej nazwie 000000_0. Moduł czytnika w eksperymencie służy do odczytu wielu plików i używania ich do prognozowania.

Korzystając z modułu czytnika w eksperymencie usługi Azure Machine Learning, można określić obiekt blob platformy Azure jako dane wejściowe. Pliki w magazynie obiektów blob platformy Azure mogą być plikami wyjściowymi (przykład: 000000_0), które są produkowane przez skrypt Pig i hive uruchomiony w programie HDInsight. Moduł czytnika umożliwia odczytywanie plików (bez rozszerzeń) przez skonfigurowanie **path to container, directory/blob**. **Ścieżka do kontenera** wskazuje kontener i **katalog/obiekt blob** wskazuje na folder, który zawiera pliki, jak pokazano na poniższej ilustracji. Gwiazdka, która jest \*) **określa, że wszystkie pliki w kontenerze/folderze (czyli data/aggregateddata/year=2014/month-6/\*)** są odczytywane w ramach eksperymentu.

![Właściwości obiektów blob platformy Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Przykład
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Potok z działaniem Usługi AzureMLBatchExecution z parametrami usługi sieci Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

W powyższym przykładzie JSON:

* Wdrożona usługa azure machine learning sieci Web używa modułu czytnika i modułu modułu modułu modułu zapisu do odczytu/zapisu danych z/do bazy danych SQL azure. Ta usługa sieci Web udostępnia następujące cztery parametry: nazwa serwera bazy danych, nazwa bazy danych, nazwa konta użytkownika serwera i hasło konta użytkownika serwera.
* Zarówno **data rozpoczęcia,** jak i **koniec** datowania musi być w [formacie ISO.](https://en.wikipedia.org/wiki/ISO_8601) Na przykład: 2014-10-14T16:32:41Z. Godzina **zakończenia** jest opcjonalna. Jeśli nie określisz wartości właściwości **końcowej,** zostanie ona obliczona jako "**start + 48 godzin.**" Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

### <a name="other-scenarios"></a>Inne scenariusze
#### <a name="web-service-requires-multiple-inputs"></a>Usługa sieci Web wymaga wielu danych wejściowych
Jeśli usługa sieci web pobiera wiele danych wejściowych, należy użyć **właściwości webServiceInputs** zamiast używać **webServiceInput**. Zestawy danych, do których odwołują się **webServiceInputs,** muszą również być uwzględnione w **danych wejściowych**działania .

W eksperymencie studio usługi Azure Machine Learning porty wejściowe i wyjściowe usługi sieci web oraz parametry globalne mają nazwy domyślne ("input1", "input2"), które można dostosować. Nazwy używane dla webServiceInputs, webServiceOutputs i globalParameters ustawienia muszą dokładnie odpowiadać nazwy w eksperymentach. Można wyświetlić ładunek żądania przykładu na stronie Pomocy wykonywania wsadu dla punktu końcowego studio usługi Azure Machine Learning, aby zweryfikować oczekiwane mapowanie.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Usługa sieci Web nie wymaga danych wejściowych
Usługi sieci web wsadowe usługi azure machine learning studio mogą być używane do uruchamiania wszelkich przepływów pracy, na przykład skryptów języka R lub Python, które nie mogą wymagać żadnych danych wejściowych. Lub eksperyment może być skonfigurowany z modułem Reader, który nie udostępnia żadnych GlobalParameters. W takim przypadku działanie AzureMLBatchExecution zostanie skonfigurowane w następujący sposób:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Usługa sieci Web nie wymaga wejścia/wyjścia
Usługa sieci web wykonywania wsadowego studia usługi Azure Machine Learning może nie mieć skonfigurowanych żadnych danych wyjściowych usługi sieci Web. W tym przykładzie nie ma żadnych danych wejściowych lub wyjściowych usługi sieci Web ani nie są skonfigurowane żadne globalparametry. Nadal istnieje dane wyjściowe skonfigurowane na samym działaniu, ale nie jest podane jako webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Usługa sieci Web używa czytników i modułów zapisu, a działanie jest uruchamiane tylko wtedy, gdy inne działania zakończyły się pomyślnie
Moduły czytnika usług internetowych i modułów modułów usługi sieciowej usługi studyjnej usługi azure machine learning mogą być skonfigurowane do uruchamiania z lub bez żadnych GlobalParameters. Jednak można osadzić wywołania usługi w potoku, który używa zależności zestawu danych do wywołania usługi tylko wtedy, gdy niektóre przetwarzanie nadrzędnego zostało zakończone. Można również wyzwolić inne działania po wykonaniu partii została ukończona przy użyciu tej metody. W takim przypadku można wyrazić zależności przy użyciu danych wejściowych i wyjściowych działania, bez nazywania któregokolwiek z nich jako danych wejściowych lub wyjściowych usługi sieci Web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Na **wynos** są:

* Jeśli punkt końcowy eksperymentu używa webServiceInput: jest reprezentowany przez zestaw danych obiektu blob i jest uwzględniony w danych wejściowych działania i webServiceInput właściwości. W przeciwnym razie właściwość webServiceInput zostanie pominięta.
* Jeśli punkt końcowy eksperymentu używa webServiceOutput(s): są one reprezentowane przez zestawy danych obiektów blob i są uwzględniane w wynikach działania i we właściwości webServiceOutputs. Dane wyjściowe działania i webServiceOutputs są mapowane według nazwy każdego danych wyjściowych w eksperymencie. W przeciwnym razie właściwość webServiceOutputs zostanie pominięta.
* Jeśli punkt końcowy eksperymentu udostępnia globalParameter(y), są one podane we właściwości globalParameters działania jako klucz, pary wartości. W przeciwnym razie globalParameters właściwość zostanie pominięta. W klawiszach rozróżniana jest wielkość liter. [Funkcje usługi Azure Data Factory](data-factory-functions-variables.md) mogą być używane w wartościach.
* Dodatkowe zestawy danych mogą być zawarte w działania danych wejściowych i wyjść właściwości, bez odwoływania się w activity typeProperties. Te zestawy danych regulują wykonywanie przy użyciu zależności plasterka, ale w przeciwnym razie są ignorowane przez działanie AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizowanie modeli przy użyciu działania aktualizującego zasoby
Po zakończeniu przekwalifikowania zaktualizuj usługę sieci web oceniania (eksperyment predykcyjny ujawniony jako usługa sieci web) przy użyciu nowo przeszkolonego modelu przy użyciu **działania zasobów usługi Azure Machine Learning studio Update Resource.** Aby uzyskać szczegółowe informacje, zobacz [Aktualizowanie modeli przy użyciu](data-factory-azure-ml-update-resource-activity.md) artykułu Aktualizuj działanie zasobów.

### <a name="reader-and-writer-modules"></a>Moduły czytnika i modułu zapisującego
Typowym scenariuszem korzystania z parametrów usługi sieci Web jest użycie czytników i modułów zapisu sql platformy Azure. Moduł czytnika służy do ładowania danych do eksperymentu z usług zarządzania danymi poza usługą Azure Machine Learning Studio. Moduł modułu modułu modułu zapisującego jest zapisywanie danych z eksperymentów w usługach zarządzania danymi poza usługą Azure Machine Learning Studio.

Aby uzyskać szczegółowe informacje na temat czytnika/modułu zapisującego sql usługi Azure/Azure, zobacz tematy [programu Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) w bibliotece MSDN. W przykładzie w poprzedniej sekcji użyto czytnika obiektów blob platformy Azure i modułu zapisującego obiektów blob platformy Azure. W tej sekcji omówiono przy użyciu czytnika SQL platformy Azure i modułu zapisującego sql platformy Azure.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**P.** Mam wiele plików, które są generowane przez moje potoki dużych zbiorów danych. Czy mogę używać działania AzureMLBatchExecution do pracy nad wszystkimi plikami?

**Odpowiedź:** tak. Zobacz **za pomocą modułu czytnika do odczytu danych z wielu plików w usłudze Azure Blob** sekcji, aby uzyskać szczegółowe informacje.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Działanie oceniania wsadowego studia usługi Azure Machine Learning
Jeśli używasz **działania AzureMLBatchScoring** do integracji z usługą Azure Machine Learning, zaleca się użycie najnowszego działania **AzureMLBatchExecution.**

Działanie AzureMLBatchExecution został wprowadzony w sierpniu 2015 wydanie azure SDK i Azure PowerShell.

Jeśli chcesz kontynuować korzystanie z azuremlbatchscoring działania, kontynuuj czytanie w tej sekcji.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Działanie oceniania wsadowego studia usługi Azure Machine Learning przy użyciu usługi Azure Storage dla danych wejściowych/wyjściowych

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parametry usługi sieci Web
Aby określić wartości parametrów usługi sieci Web, dodaj sekcję **typeProperties** do sekcji **AzureMLBatchScoringActivity** w potoku JSON, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Można również użyć [funkcji fabryki danych](data-factory-functions-variables.md) podczas przekazywania wartości parametrów usługi sieci Web, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> W parametrach usługi sieci Web rozróżniana jest wielkość liter, dlatego należy upewnić się, że nazwy określone w usłudze JSON są zgodne z nazwami ujawnionymi przez usługę sieci Web.
>
>

## <a name="see-also"></a>Zobacz też
* [Wpis w blogu platformy Azure: Wprowadzenie do usługi Azure Data Factory i usługi Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
