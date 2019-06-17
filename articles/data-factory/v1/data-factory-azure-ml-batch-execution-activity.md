---
title: Tworzenie potoków danych prognostycznych przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia tworzyć potoki predykcyjne przy użyciu usługi Azure Data Factory i Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4093febd19d71512e3c80704e88f9d5cf669d7d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60567421"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i Azure Data Factory

> [!div class="op_single_selector" title1="Działania przekształcania"]
> * [Działanie technologii hive](data-factory-hive-activity.md)
> * [Działania technologii pig](data-factory-pig-activity.md)
> * [Działania technologii MapReduce](data-factory-map-reduce.md)
> * [Działania przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działania platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Wprowadzenie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [przekształcania danych za pomocą usługi machine learning w usłudze Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługa Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) pozwala na tworzenie, testowanie i wdrażanie rozwiązań analizy predykcyjnej. Z wysokiego poziomu punktu widzenia można to zrobić w trzech krokach:

1. **Tworzenie eksperymentu szkolenia**. Ten krok jest wykonywane przy użyciu usługi Azure Machine Learning studio. Usługa Azure Machine Learning studio to środowisko programowanie zespołowe visual używanej do nauczenia i przetestowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go na eksperyment predykcyjny**. Gdy model po zapoznaniu z istniejącymi danymi i wszystko będzie gotowe na potrzeby oceniać nowe dane, Przygotuj i usprawnić eksperymentu do oceniania.
3. **Go wdrożyć jako usługę sieci web**. Możesz opublikować oceniania eksperymentu jako usługi sieci web platformy Azure. Możesz wysyłać dane do modelu przy użyciu tego punktu końcowego usługi sieci web i otrzymywać wyniki prognozy dla modelu.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji **przenoszenia** i **przekształcania** danych. Można tworzyć rozwiązania integracji danych za pomocą usługi Azure Data Factory, które mogą pozyskiwać dane z różnych magazynów danych, przekształcanie i przetwarzanie danych i publikować dane wynikowe w magazynach danych.

Usługa Data Factory pozwala tworzyć potoki danych służące do przenoszenia i przekształcania danych, a następnie uruchamiać te potoki zgodnie z zaplanowanym harmonogramem (co godzinę, codziennie, co tydzień itp.). Usługa ta udostępnia również rozbudowane wizualizacje umożliwiające wyświetlanie elementów powiązanych i zależności między potokami danych oraz monitorowanie wszystkich potoków danych w jednym zintegrowanym widoku, który ułatwia wykrywanie problemów i konfigurowanie alertów monitorowania.

Zobacz [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) i [Tworzenie pierwszego potoku](data-factory-build-your-first-pipeline.md) artykuły, aby szybko rozpocząć pracę z usługą Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Fabryka danych i uczenia maszynowego razem
Usługa Azure Data Factory umożliwia łatwe tworzenie potoki, które używają opublikowanych [usługi Azure Machine Learning] [ azure-machine-learning] sieci web usługi do analizy predykcyjnej. Za pomocą **działanie wykonywania wsadowego** w potoku usługi Azure Data Factory można wywołać usługi sieci web Azure Machine Learning studio, jak tworzyć prognozy na danych w usłudze batch. Zobacz wywoływanie usługi sieci web Azure Machine Learning studio przy użyciu sekcji działanie wykonywania wsadowego, aby uzyskać szczegółowe informacje.

Wraz z upływem czasu modele predykcyjne w programie Azure Machine Learning studio, oceniania eksperymentów konieczne retrained, przy użyciu nowych danych wejściowych zestawów danych. Model usługi Azure Machine Learning studio z potoku usługi fabryka danych mogą przechowywać, wykonując następujące czynności:

1. Publikuj eksperymentu szkolenia (eksperyment nie predykcyjny) jako usługę sieci web. Możesz wykonać ten krok w programie Azure Machine Learning studio, tak jak do udostępnienia eksperyment predykcyjny jako usługę internetową w poprzednim scenariuszu.
2. Użyj usługi Azure Machine Learning studio działanie wykonywania wsadowego do wywołania usługi sieci web dla eksperymentu szkolenia. Po prostu umożliwia studio usługi Azure Machine Learning Batch Execution, działanie Wywołaj usługę sieci web szkolenia i oceniania usługi sieci web.

Po zakończeniu ponownego trenowania, zaktualizować usługi internetowej przyznawania ocen (udostępniane jako usługi sieci web eksperyment predykcyjny) przy użyciu nowo uczonego modelu przy użyciu **usługi Azure Machine Learning studio działanie aktualizacji zasobu**. Zobacz [aktualizacji modeli za pomocą działania aktualizowania zasobów](data-factory-azure-ml-update-resource-activity.md) artykuł, aby uzyskać szczegółowe informacje.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Wywoływanie usługi sieci web przy użyciu działanie wykonywania wsadowego
Azure Data Factory służy do organizowania przenoszenia i przetwarzania danych, a następnie wykonaj wykonywania wsadowego za pomocą usługi Azure Machine Learning. Poniżej przedstawiono kroki najwyższego poziomu:

1. Tworzenie usługi Azure Machine Learning połączone. Potrzebne są następujące wartości:

   1. **Identyfikator URI żądania** wykonywania wsadowego interfejsu API. Identyfikator URI żądania można znaleźć, klikając **wykonywanie WSADOWE** łącze na stronie usługi sieci web.
   2. **Klucz interfejsu API** do opublikowanej usługi Azure Machine Learning usługi sieci web. Klucz interfejsu API można znaleźć, klikając usługę sieci web, które zostały opublikowane.
   3. Użyj **AzureMLBatchExecution** działania.

      ![W usłudze Machine Learning pulpitu nawigacyjnego](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identyfikator URI usługi Batch](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz: Eksperymentów przy użyciu sieci Web usługi danych wejściowych lub wyjściowych, które odwołują się do danych w usłudze Azure Blob Storage
W tym scenariuszu usługi Azure Machine Learning w sieci Web sprawia, że prognozy przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i przechowuje wyniki prognozy w magazynie obiektów blob. Następujący kod JSON definiuje potok usługi Data Factory z działaniem AzureMLBatchExecution. Działanie ma zestaw danych **DecisionTreeInputBlob** jako dane wejściowe i **DecisionTreeResultBlob** jako dane wyjściowe. **DecisionTreeInputBlob** jest przekazywany jako dane wejściowe do usługi sieci web, za pomocą **webServiceInput** właściwość JSON. **DecisionTreeResultBlob** jest przekazywany jako dane wyjściowe do usługi sieci Web, za pomocą **webServiceOutputs** właściwość JSON.

> [!IMPORTANT]
> Jeśli usługa sieci web wymaga wielu danych wejściowych, użyj **webServiceInputs** zamiast przy użyciu właściwości **webServiceInput**. Zobacz [usługi sieci Web wymaga wielu danych wejściowych](#web-service-requires-multiple-inputs) sekcji, na przykład przy użyciu właściwości webServiceInputs.
>
> Zestawy danych, które są przywoływane przez **webServiceInput**/**webServiceInputs** i **webServiceOutputs** właściwości (w  **typeProperties**) muszą także być ujęte w działaniu **dane wejściowe** i **generuje**.
>
> Dane wejściowe usługi sieci web i portów dane wyjściowe i parametry globalne w eksperymencie usługi Azure Machine Learning studio ma domyślne nazwy ("input1", "wejście2"), które można dostosować. Nazwy używanych w przypadku ustawienia globalParameters, webServiceOutputs i webServiceInputs musi dokładnie odpowiadać nazwy w eksperymenty. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego usługi Azure Machine Learning studio zweryfikować oczekiwane mapowania.
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
> Tylko dane wejściowe i wyjściowe aktywności AzureMLBatchExecution mogą być przekazywane jako parametry do usługi sieci Web. Na przykład w powyższym fragmencie kodu JSON DecisionTreeInputBlob jest wartością wejściową działania AzureMLBatchExecution, który jest przekazywany jako dane wejściowe do usługi sieci Web za pomocą parametru webServiceInput.
>
>

### <a name="example"></a>Przykład
W tym przykładzie używa usługi Azure Storage do przechowywania danych wejściowych i wyjściowych.

Zalecamy przeczytanie [Tworzenie pierwszego potoku za pomocą usługi Data Factory] [ adf-build-1st-pipeline] samouczek przed przejściem w tym przykładzie. Edytor usługi Data Factory umożliwia tworzenie artefaktów usługi Data Factory (połączone usługi, zestawy danych, potoku) w tym przykładzie.

1. Tworzenie **połączoną usługę** dla Twojego **usługi Azure Storage**. Jeśli pliki wejściowe i wyjściowe znajdują się w różnych kont magazynu, należy się dwie połączone usługi. Poniżej przedstawiono przykład kodu JSON:

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
2. Tworzenie **wejściowych** usługi Azure Data Factory **zestawu danych**. W odróżnieniu od niektórych innych usługi Data Factory zestawów danych, te zestawy danych mogą zawierać zarówno **folderPath** i **fileName** wartości. Partycjonowanie umożliwia spowodować, że każdy wykonywania wsadowego (każdy wycinek danych) do przetwarzania lub tworzyć unikatowe dane wejściowe i wyjściowe pliki. Konieczne może zawierać pewne działanie w strumieniu przychodzącym do przekształcania danych wejściowych w formacie pliku CSV i umieść go w ramach konta magazynu dla każdego wycinka. W takim przypadku użytkownik nie zawiera **zewnętrznych** i **externalData** ustawienia pokazano w poniższym przykładzie i Twoje DecisionTreeInputBlob będzie wyjściowy zestaw danych z innego działania.

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

    Plik csv wejściowy musi mieć wiersz nagłówka kolumny. Jeśli używasz **działania kopiowania** można utworzyć/przenieść wolumin csv w magazynie obiektów blob, należy ustawić właściwość ujścia **blobWriterAddHeader** do **true**. Na przykład:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Jeśli plik csv nie ma wiersz nagłówka, może zostać wyświetlony następujący błąd: **Błąd w aktywności: Błąd podczas odczytu ciąg. Nieoczekiwany token: StartObject. Ścieżka ", wiersz 1, pozycja 1**.
3. Tworzenie **dane wyjściowe** usługi Azure Data Factory **zestawu danych**. W tym przykładzie użyto partycjonowania, aby utworzyć ścieżkę wyjściową unikatowy dla każdego wykonania wycinka. Bez podziału na partycje, działanie spowodowałoby zastąpienie pliku.

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
4. Tworzenie **połączoną usługę** typu: **Element azuremllinkedservice nie**, zapewniając klucz interfejsu API i model adres URL wykonywania wsadowego.

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
5. Na koniec tworzenie potoku zawierającego **AzureMLBatchExecution** działania. W czasie wykonywania potok wykonuje następujące czynności:

   1. Pobiera lokalizację pliku wejściowego z wejściowych zestawów danych.
   2. Wywołuje interfejs API wykonywania wsadowego usługi Azure Machine Learning
   3. Kopiuje dane wyjściowe wykonywania wsadowego w wyjściowy zestaw danych obiektu blob.

      > [!NOTE]
      > Działanie AzureMLBatchExecution może mieć zero lub więcej danych wejściowych i wyjściowych jednego lub więcej.
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

      Zarówno **start** i **zakończenia** Data/godzina musi należeć do [ISO format](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. **Zakończenia** czasu jest opcjonalne. Jeśli nie określisz wartości dla **zakończenia** właściwości, zostanie ona obliczona jako "**rozpoczęcia + 48 godzin.** " Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

      > [!NOTE]
      > Określenie dane wejściowe na potrzeby AzureMLBatchExecution działania jest opcjonalne.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz: Eksperymentów przy użyciu czytnika/zapisywania modułów do odwoływania się do danych w różnych miejsc
Inny typowy scenariusz, podczas tworzenia usługi Azure Machine Learning studio eksperymentów jest używać modułów czytników i składników zapisywania. Moduł czytnika jest używana do ładowania danych do eksperymentu, a moduł zapisywania jest zapisywane są dane z eksperymentów. Aby uzyskać szczegółowe informacje o modułach czytników i składników zapisywania, zobacz [czytnika](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [moduł zapisujący](https://msdn.microsoft.com/library/azure/dn905984.aspx) tematy w bibliotece MSDN.

Korzystając z modułów czytników i składników zapisywania, jest dobrym rozwiązaniem, użyj parametru usługi sieci Web dla każdej właściwości tych modułów czytnika/składnika zapisywania. Te parametry sieci web umożliwiają skonfigurowanie wartości podczas wykonywania. Na przykład można utworzyć eksperyment za pomocą modułu czytnika, który używa usługi Azure SQL Database: XXX.database.windows.net. Po wdrożeniu usługi sieci web chcesz umożliwić użytkownikom usługi sieci web określić inny serwer SQL platformy Azure o nazwie YYY.database.windows.net. Parametr usługi sieci Web można użyć, aby zezwolić na tę wartość do skonfigurowania.

> [!NOTE]
> Usługa sieci Web w danych wejściowych i wyjściowych różnią się od parametry usługi sieci Web. W przypadku pierwszego scenariusza wiesz, jak dane wejściowe i wyjściowe można określić dla usługi Azure Machine Learning studio usługi sieci Web. W tym scenariuszu przekażesz parametry usługi sieci Web, które odnoszą się do właściwości czytnika/zapisywania modułów.
>
>

Spójrzmy na scenariusz użycia parametry usługi sieci Web. Masz wdrożonej usługi sieci web usługi Azure Machine Learning, który używa modułu reader można odczytać danych z jednego źródła danych obsługiwane przez usługi Azure Machine Learning (na przykład: Azure SQL Database). Wykonywanie wsadowe zostanie wykonane, wyniki są zapisywane z użyciem modułu zapisywania (Azure SQL Database).  Nie sieci web usługi wejściami i wyjściami są definiowane w eksperymenty. W tym przypadku zaleca się skonfigurowanie parametrów usługi web odpowiednie dla modułów czytników i składników zapisywania. Ta konfiguracja pozwala czytnika/zapisywania modułów można skonfigurować podczas używania działania AzureMLBatchExecution. Określ parametry usługi sieci Web w **globalParameters** sekcji w kodzie JSON działania w następujący sposób.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Można również użyć [funkcji usługi fabryka danych](data-factory-functions-variables.md) do przekazywania wartości dla sieci Web usługi parametrów, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry usługi sieci Web jest rozróżniana wielkość liter, dlatego upewnij się, że nazwy, które określisz w działaniu JSON są zgodne z typami udostępnianych przez usługę sieci Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Do odczytywania danych z wielu plików w usłudze Azure Blob przy użyciu modułu czytnika
Potoków danych big data, dzięki takim działaniom jak Pig i Hive można utworzyć jeden lub więcej plików wyjściowych bez rozszerzeń. Na przykład po określeniu tabeli zewnętrznej Hive, dane w tabeli zewnętrznej Hive można przechowywane w usłudze Azure blob storage za pomocą następujących 000000_0 nazwy. Możesz użyć modułu czytnika w eksperymencie można odczytać wielu plików i używać ich na potrzeby prognozy.

Korzystając z pomocy modułu czytnika w eksperymencie usługi Azure Machine Learning, można określić obiektu Blob platformy Azure jako dane wejściowe. Pliki w magazynie obiektów blob platformy Azure mogą być pliki wyjściowe (przykład: 000000_0), są produkowane przez programów Pig i Hive skryptu uruchomionego na HDInsight. Moduł czytnika umożliwia odczytywanie plików (z nie rozszerzenia), konfigurując **ścieżka do kontenera, katalog/obiekt blob**. **Ścieżka do kontenera** punktów do kontenera i **katalogu/obiekt blob** wskazuje folder, który zawiera pliki, jak pokazano na poniższej ilustracji. Gwiazdka oznacza to, \*) **Określa, że wszystkie pliki w folderze/kontenera (czyli danych/aggregateddata/rok = 2014/miesiąca — 6 /\*)** są odczytywane w ramach eksperymentu.

![Właściwości obiektu Blob platformy Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Przykład
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Potok z działaniem AzureMLBatchExecution z parametrów usług sieci Web

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

* Wdrożonej usługi Azure Machine Learning w sieci Web używa modułu zapisywania i odczytywania danych z usługi Azure SQL Database i do odczytu/zapisu. Ta usługa sieci Web udostępnia następujące cztery parametry:  Nazwa serwera bazy danych, nazwę bazy danych, nazwę konta użytkownika serwera i hasło konta użytkownika serwera.
* Zarówno **start** i **zakończenia** Data/godzina musi należeć do [ISO format](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. **Zakończenia** czasu jest opcjonalne. Jeśli nie określisz wartości dla **zakończenia** właściwości, zostanie ona obliczona jako "**rozpoczęcia + 48 godzin.** " Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

### <a name="other-scenarios"></a>Inne scenariusze
#### <a name="web-service-requires-multiple-inputs"></a>Usługa sieci Web wymaga wielu danych wejściowych
Jeśli usługa sieci web wymaga wielu danych wejściowych, użyj **webServiceInputs** zamiast przy użyciu właściwości **webServiceInput**. Zestawy danych, które są przywoływane przez **webServiceInputs** muszą także być ujęte w działaniu **dane wejściowe**.

Dane wejściowe usługi sieci web i portów dane wyjściowe i parametry globalne w eksperymencie usługi Azure Machine Learning studio ma domyślne nazwy ("input1", "wejście2"), które można dostosować. Nazwy używanych w przypadku ustawienia globalParameters, webServiceOutputs i webServiceInputs musi dokładnie odpowiadać nazwy w eksperymenty. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego usługi Azure Machine Learning studio zweryfikować oczekiwane mapowania.

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
Azure usług sieci web wykonywania wsadowego usługi Machine Learning studio może służyć do uruchamiania przepływów pracy, aby uzyskać przykładowe R lub Python skrypty, które nie wymagają żadnych danych wejściowych. Lub doświadczenia mogą być również konfigurowane przy użyciu modułu czytnika nie ujawnia żadnych GlobalParameters. W takim przypadku działanie AzureMLBatchExecution będzie można skonfigurować w następujący sposób:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Usługa sieci Web nie jest wymagane dane wejściowe/wyjściowe
Usługi sieci web Azure Machine Learning studio wsadowe wykonywanie może nie mieć żadnych danych wyjściowych z usługi sieci Web skonfigurowany. W tym przykładzie nie ma usługi sieci Web w danych wejściowych ani danych wyjściowych, ani nie skonfigurowano żadnych GlobalParameters. Nadal jest wyjściem skonfigurować je, ale nie zostanie podany jako webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Czytelnicy używa Usługa sieci Web i autorzy i uruchomienia działania, tylko wtedy, gdy inne działania zakończyły się powodzeniem.
Usługa Azure Machine Learning studio web czytników i składników zapisywania modułów usługi może być skonfigurowany do uruchamiania z lub bez żadnych GlobalParameters. Możesz osadzić wywołań usługi w potoku, który używa zależności zestawu danych, aby wywołać usługę, tylko wtedy, gdy niektóre nadrzędny przetwarzanie zostało zakończone. Po zakończeniu wykonywania wsadowego, przy użyciu tej metody, można również wyzwalać innych działań. W takim przypadku można wyrazić zależności za pomocą działania wejściami i wyjściami, bez określania nazwy któryś z nich jako usługę sieci Web dane wejściowe lub wyjściowe.

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

**Wnioski** są:

* Jeśli dany punkt końcowy eksperyment korzysta webServiceInput: jest reprezentowany przez zestaw danych obiektów blob i jest dostępny w danych wejściowych działania i właściwości webServiceInput. W przeciwnym razie właściwość webServiceInput została pominięta.
* Jeśli dany punkt końcowy eksperyment korzysta webServiceOutput(s): są reprezentowane przez zestawy danych obiektów blob i są uwzględniane w danych wyjściowych działania, a we właściwości webServiceOutputs. Wyjściem działania i webServiceOutputs są mapowane przez nazwę każdego dane wyjściowe w eksperymencie. W przeciwnym razie właściwość webServiceOutputs została pominięta.
* Jeśli dany punkt końcowy eksperyment udostępnia globalParameter(s), otrzymują one we właściwości globalParameters działanie jako pary kluczy, wartość. W przeciwnym razie właściwość globalParameters została pominięta. Kluczy jest rozróżniana wielkość liter. [Funkcje usługi Azure Data Factory](data-factory-functions-variables.md) mogą być używane w wartości.
* Dodatkowe zestawy danych mogą zostać zawarte we właściwościach dane wejściowe i wyjściowe działania, bez odwołuje się typeProperties działania. Te zestawy danych określają sposób wykonania za pomocą właściwości zależności wycinka, ale w przeciwnym razie są ignorowane przez działanie AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizowanie modeli za pomocą działania aktualizowania zasobów
Po zakończeniu ponownego trenowania, zaktualizować usługi internetowej przyznawania ocen (udostępniane jako usługi sieci web eksperyment predykcyjny) przy użyciu nowo uczonego modelu przy użyciu **usługi Azure Machine Learning studio działanie aktualizacji zasobu**. Zobacz [aktualizacji modeli za pomocą działania aktualizowania zasobów](data-factory-azure-ml-update-resource-activity.md) artykuł, aby uzyskać szczegółowe informacje.

### <a name="reader-and-writer-modules"></a>Moduły zapisywania i odczytywania
Typowy scenariusz użycia parametrów usługi sieci Web jest korzystanie z usługi Azure SQL czytników i składników zapisywania. Moduł czytnika jest używana do ładowania danych do eksperymentu z usług zarządzania danymi poza Azure Machine Learning Studio. Moduł zapisywania jest zapisanie danych z eksperymentów w usług zarządzania danymi poza Azure Machine Learning Studio.

Szczegółowe informacje na temat usługi Azure Blob/Azure czytnika/moduł zapisujący programu SQL, zobacz [czytnika](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [moduł zapisujący](https://msdn.microsoft.com/library/azure/dn905984.aspx) tematy w bibliotece MSDN. W przykładzie w poprzedniej sekcji używany składnik zapisywania obiektów Blob platformy Azure i odczytywania obiektów Blob platformy Azure. W tej sekcji omówiono, za pomocą usługi Azure SQL czytników i składników zapisywania usługi Azure SQL.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Pyt.:** Masz wiele plików, które są generowane przez mój potoków danych big Data. Czy można użyć działania AzureMLBatchExecution w celu pracy we wszystkich plikach?

**Odp.:** Tak. Zobacz **można odczytać danych z wielu plików w usłudze Azure Blob przy użyciu modułu Reader** sekcji, aby uzyskać szczegółowe informacje.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Usługa Azure Machine Learning studio działanie programu wsadowego oceniania
Jeśli używasz **AzureMLBatchScoring** działania w celu integracji z usługą Azure Machine Learning, zaleca się, że używasz najnowszej **AzureMLBatchExecution** działania.

Działanie AzureMLBatchExecution został wprowadzony w wersji sierpnia 2015 r., zestaw Azure SDK i programu Azure PowerShell.

Jeśli chcesz nadal korzystać z działania AzureMLBatchScoring kontynuować odczytywanie za pośrednictwem tej sekcji.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Usługa Azure Machine Learning studio wsadowe ocenianie przez działanie za pomocą usługi Azure Storage dla wejścia/wyjścia

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
Aby określić wartości parametrów usługi sieci Web, Dodaj **typeProperties** sekcji **element AzureMLBatchScoringActivity** sekcji w kodzie JSON potoku, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Można również użyć [funkcji usługi fabryka danych](data-factory-functions-variables.md) do przekazywania wartości dla sieci Web usługi parametrów, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry usługi sieci Web jest rozróżniana wielkość liter, dlatego upewnij się, że nazwy, które określisz w działaniu JSON są zgodne z typami udostępnianych przez usługę sieci Web.
>
>

## <a name="see-also"></a>Zobacz też
* [Wpis w blogu platformy Azure: Wprowadzenie do usługi Azure Data Factory i Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
