---
title: Tworzenie potoków predykcyjnej danych przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia tworzenie potoków predykcyjnej przy użyciu fabryki danych Azure i usługi Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 62ee13c4e756e41558adcb5b98081e24d683e01a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620378"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Tworzenie potoków predykcyjnej przy użyciu usługi Azure Machine Learning i fabryki danych Azure

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Działanie gałęzi](data-factory-hive-activity.md) 
> * [Działanie pig](data-factory-pig-activity.md)
> * [Działania MapReduce](data-factory-map-reduce.md)
> * [Działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działania aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Działania niestandardowe .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Wprowadzenie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [Przekształcanie danych za pomocą uczenia maszynowego w fabryce danych w wersji 2](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Usługa Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umożliwia tworzenie, testowanie i wdrażanie rozwiązań analizy predykcyjnej. Z wysokiego poziomu punktu widzenia można to zrobić w trzy kroki:

1. **Tworzenie eksperymentu uczenia**. Aby wykonać ten krok za pomocą usługi Azure ML Studio. Studio uczenia Maszynowego to środowisko visual programowanie zespołowe używanej do nauczenia i przetestowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go eksperyment predykcyjny**. Po modelu po zapoznaniu z istniejącymi danymi i chcesz używać go do oceniać nowe dane, Przygotuj i usprawnić eksperymentu wyników.
3. **Go wdrożyć jako usługę sieci web**. Możesz opublikować oceniania eksperymentu jako usługi sieci web platformy Azure. Można wysyłać dane do modelu za pomocą tego punktu końcowego usługi sieci web i odbierać prognoz wyniku z modelu.  

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji **przenoszenia** i **przekształcania** danych. Można utworzyć integracji danych rozwiązania przy użyciu fabryki danych Azure, które można pozyskiwania danych z różnych baz danych, proces/transformacji danych i publikować dane wynikowe do magazynów danych.

Usługa Data Factory pozwala tworzyć potoki danych służące do przenoszenia i przekształcania danych, a następnie uruchamiać te potoki zgodnie z zaplanowanym harmonogramem (co godzinę, codziennie, co tydzień itp.). Usługa ta udostępnia również rozbudowane wizualizacje umożliwiające wyświetlanie elementów powiązanych i zależności między potokami danych oraz monitorowanie wszystkich potoków danych w jednym zintegrowanym widoku, który ułatwia wykrywanie problemów i konfigurowanie alertów monitorowania.

Zobacz [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) i [kompilacji swój pierwszy potok](data-factory-build-your-first-pipeline.md) artykuły, aby szybko rozpocząć pracę z usługą fabryki danych Azure.

### <a name="data-factory-and-machine-learning-together"></a>Fabryki danych i jednocześnie uczenia maszynowego
Fabryka danych Azure umożliwia łatwe tworzenie potoków, które używają opublikowanych [usługi Azure Machine Learning] [ azure-machine-learning] usługi analizy predykcyjnej sieci web. Przy użyciu **działanie wykonywania wsadowego** w potoku fabryki danych Azure, można wywołać usługi sieci web uczenie Maszynowe Azure w celu prognozowania danych w partii. Zobacz [wywoływania usługi sieci web uczenie Maszynowe Azure przy użyciu działanie wykonywania wsadowego](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) sekcji, aby uzyskać szczegółowe informacje.

Wraz z upływem czasu modeli predykcyjnych w uczenie Maszynowe Azure oceniania eksperymenty konieczne retrained, przy użyciu nowych baz danych wejściowych. Można ponownie ucz model usługi uczenie Maszynowe Azure z potoku fabryki danych, wykonując następujące czynności:

1. Opublikuj eksperyment uczenia (eksperyment predykcyjny nie) jako usługę sieci web. Należy wykonać ten krok w usłudze Azure ML Studio, jak udostępnić eksperyment predykcyjny jako usługę sieci web w poprzednim scenariuszu.
2. Umożliwia wywoływanie usługi sieci web dla eksperyment uczenia działanie wykonywania wsadowego uczenia Maszynowego Azure. Zasadniczo służy działanie wykonywania wsadowego usługi uczenie Maszynowe Azure do wywołania oceniania usług i usługi sieci web szkolenia.

Po wykonaniu ponownego trenowania zaktualizować oceniania usługi sieci web (eksperyment predykcyjny udostępniony jako usługa sieci web) z nowo trenowanego modelu przy użyciu **działanie aktualizacji zasobu usługi Azure ML**. Zobacz [aktualizowanie modeli przy użyciu działanie aktualizacji zasobu](data-factory-azure-ml-update-resource-activity.md) artykułu, aby uzyskać szczegółowe informacje.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Wywoływanie usługi sieci web przy użyciu działanie wykonywania wsadowego
Użyj usługi fabryka danych Azure do organizowania przenoszenia danych i przetwarzania, a następnie wykonaj wykonywania wsadowego usługi za pomocą usługi Azure Machine Learning. Poniżej przedstawiono kroki najwyższego poziomu:

1. Tworzenie usługi Azure Machine Learning połączone. Potrzebne są następujące wartości:

   1. **Identyfikator URI żądania** dla wykonywania wsadowego usługi interfejsu API. Identyfikator URI żądania można znaleźć, klikając **BATCH EXECUTION** łącze na stronie usługi sieci web.
   2. **Klucz interfejsu API** dla opublikowanych usługi Azure Machine Learning usługi sieci web. Klucz interfejsu API można znaleźć, klikając usługę sieci web, które zostały opublikowane.
   3. Użyj **AzureMLBatchExecution** działania.

      ![Pulpit nawigacyjny uczenia maszynowego](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identyfikator URI partii](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz: Eksperymentów przy użyciu sieci Web usługi wejść/wyjść odwołujące się do danych w magazynie obiektów Blob Azure
W tym scenariuszu usługi sieci Web Azure Machine Learning sprawia, że prognoz przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i przechowuje wyniki prognozowania w magazynie obiektów blob. Następujące JSON definiuje potoku fabryki danych do działania AzureMLBatchExecution. Działanie ma zestaw danych **DecisionTreeInputBlob** jako dane wejściowe i **DecisionTreeResultBlob** jako dane wyjściowe. **DecisionTreeInputBlob** przekazany jako dane wejściowe z usługą sieci web przez przy użyciu **WebServiceInputActivity** właściwości JSON. **DecisionTreeResultBlob** jest przekazywany jako dane wyjściowe z usługą sieci Web przez przy użyciu **webServiceOutputs** właściwości JSON.  

> [!IMPORTANT]
> Jeśli usługa sieci web ma wielu danych wejściowych, użyj **webServiceInputs** zamiast za pomocą właściwości **WebServiceInputActivity**. Zobacz [usługi sieci Web wymaga wielu nakładów](#web-service-requires-multiple-inputs) sekcji, na przykład za pomocą właściwości webServiceInputs.
>
> Zestawy danych, które odwołują się **WebServiceInputActivity**/**webServiceInputs** i **webServiceOutputs** właściwości (w **typeProperties**) muszą także być ujęte w działaniu **dane wejściowe** i **generuje**.
>
> W eksperymencie uczenie Maszynowe Azure wprowadzania usługi sieci web i porty wyjścia i parametry globalne mają nazwy domyślnej ("input1", "input2"), które można dostosować. Nazwy używanych w przypadku webServiceInputs, webServiceOutputs i ustawienia globalParameters musi dokładnie odpowiadać nazwy eksperymenty. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego uczenia Maszynowego Azure można zweryfikować oczekiwanego mapowania.
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
> Tylko danych wejściowych i wyjściowych działania AzureMLBatchExecution mogą być przekazywane jako parametry z usługą sieci Web. Na przykład w powyższym fragment kodu JSON DecisionTreeInputBlob jest wartością wejściową działania AzureMLBatchExecution, który jest przekazywany jako dane wejściowe do usługi sieci Web za pomocą parametru WebServiceInputActivity.   
>
>

### <a name="example"></a>Przykład
W tym przykładzie używa usługi Azure Storage do przechowywania danych wejściowych i wyjściowych.

Firma Microsoft zaleca zapoznanie [kompilacji swój pierwszy potok z fabryką danych] [ adf-build-1st-pipeline] samouczka przed rozpoczęciem tego przykładu. Edytor fabryki danych można utworzyć fabryki danych artefakty (połączone usługi, zestawy danych, potoku) w tym przykładzie.   

1. Utwórz **połączona usługa** dla Twojego **usługi Azure Storage**. Pliki wejściowe i wyjściowe są w różnych kont magazynu, należy najpierw dwóch połączonych usług. Oto przykład JSON:

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
2. Utwórz **wejściowych** fabryki danych Azure **zestawu danych**. W odróżnieniu od niektórych innych fabryki danych zestawów danych, te zestawy danych musi zawierać zarówno **folderPath** i **fileName** wartości. Partycjonowanie umożliwia spowodować wykonanie każdej partii (każdy wycinek danych) do przetwarzania lub utworzyć unikatowe dane wejściowe i plików wyjściowych. Konieczne może być obejmują niektóre działania nadrzędnego do przekształcania danych wejściowych w formacie pliku CSV i umieść go w ramach konta magazynu dla każdego wycinka. W takim przypadku użytkownik nie obejmuje **zewnętrznych** i **externalData** ustawienia pokazano w poniższym przykładzie i DecisionTreeInputBlob Twojego mogą być wyjściowy zestaw danych z innego działania.

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

    Plik wejściowy csv musi mieć wiersz nagłówka kolumny. Jeśli używasz **działanie kopiowania** Utwórz/przenoszenia plików csv do magazynu obiektów blob, należy ustawić właściwość zbiornika **blobWriterAddHeader** do **true**. Na przykład:

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    Jeśli plik csv nie ma wiersz nagłówka, może zostać wyświetlony następujący błąd: **błąd w działaniu: Wystąpił błąd podczas odczytywania ciągu. Nieoczekiwany token: metodzie StartObject. Ścieżka ', wiersz 1, umieść 1**.
3. Utwórz **dane wyjściowe** fabryki danych Azure **zestawu danych**. W tym przykładzie użyto do utworzenia ścieżkę wyjściową unikatowy dla każdego wykonywania wycinka partycjonowania. Bez podziału, działanie spowoduje zastąpienie pliku.

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
4. Utwórz **połączona usługa** typu: **AzureMLLinkedService**, zapewniając klucz interfejsu API i adres URL wykonywania wsadowego modelu.

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
5. Na koniec tworzenie potoku nadrzędnym **AzureMLBatchExecution** działania. W czasie wykonywania potoku wykonuje następujące czynności:

   1. Pobiera lokalizację pliku wejściowego z zestawów danych wejściowych.
   2. Wywołuje wykonywania wsadowego usługi uczenie maszynowe Azure interfejsu API
   3. Kopiuje dane wyjściowe wykonania partii do obiektu blob, podany w zestawie danych wyjściowych.

      > [!NOTE]
      > Działanie AzureMLBatchExecution może mieć zero lub więcej wejściami i wyjściami jeden lub więcej.
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

      Zarówno **start** i **zakończenia** dat i godzin musi być w [formacie ISO](http://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. **Zakończenia** czasu jest opcjonalna. Jeśli nie określisz wartości **zakończenia** właściwości, jest on obliczany jako "**start + 48 godzin.**" Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

      > [!NOTE]
      > Określenie wartości wejściowe AzureMLBatchExecution działania jest opcjonalne.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz: Eksperymentów przy użyciu modułów odczytywania/zapisywania odwoływać się do danych w różnych miejsc
Inny typowy scenariusz, podczas tworzenia eksperymenty uczenie Maszynowe Azure ma używać składników zapisywania i odczytywania modułów. Moduł czytnika jest używany do ładowania danych do eksperymentu, a moduł zapisywania jest zapisanie danych z eksperymentów. Szczegółowe informacje na temat składników zapisywania i odczytywania modułów, zobacz [czytnika](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [zapisywania](https://msdn.microsoft.com/library/azure/dn905984.aspx) tematy w bibliotece MSDN.     

Podczas korzystania ze składników zapisywania i odczytywania modułów, jest dobrym rozwiązaniem, użyj parametru usługi sieci Web dla każdej właściwości w tych modułów odczytywania/zapisywania. Te parametry sieci web umożliwiają skonfigurowanie wartości w czasie wykonywania. Na przykład można utworzyć eksperyment przy użyciu modułu reader używa usługi Azure SQL Database: XXX.database.windows.net. Po wdrożeniu usługi sieci web chcesz włączyć korzystającym z usług sieci web określić inny serwer SQL Azure o nazwie YYY.database.windows.net. Parametr usługi sieci Web służy do zezwalania tę wartość do skonfigurowania.

> [!NOTE]
> Usługi sieci Web w danych wejściowych i wyjściowych różnią się od parametry usługi sieci Web. Pierwszy scenariusz już wspomniano, jak dane wejściowe i wyjściowe można określić dla usługi sieci Web uczenie Maszynowe Azure. W tym scenariuszu należy przekazać parametry usługi sieci Web, które odpowiadają właściwości odczytywania/zapisywania modułów.
>
>

Przyjrzyjmy się scenariusz użycia parametry usługi sieci Web. Masz wdrożonej usługi sieci web Azure Machine Learning, używaną do odczytywania danych z jednego ze źródeł danych obsługiwane przez usługi Azure Machine Learning moduł czytnika (na przykład: baza danych SQL Azure). Po wykonaniu wykonywania wsadowego usługi wyniki są napisane przy użyciu modułu zapisywania (baza danych SQL Azure).  Nie sieci web usługi wejściami i wyjściami są definiowane w eksperymenty. W takim przypadku firma Microsoft zaleca, aby skonfigurować parametry usługi sieci web odpowiednich składników zapisywania i odczytywania modułów. Ta konfiguracja pozwala odczytywania/zapisywania modułów można skonfigurować podczas używania działania AzureMLBatchExecution. Określ parametry usługi sieci Web w **globalParameters** sekcji w działaniu JSON w następujący sposób.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Można również użyć [funkcji fabryki danych](data-factory-functions-variables.md) w przekazywanie wartości dla sieci Web usługi parametrów, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry usługi sieci Web jest rozróżniana wielkość liter, dlatego upewnij się, że nazwy, które określisz w działaniu JSON zgodne te udostępniane przez usługę sieci Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Za pomocą modułu Reader można odczytać danych z wielu plików w obiekcie Blob Azure
Dane big data potoków z działania, takie jak Pig i Hive można utworzyć jeden lub więcej plików wyjściowych bez rozszerzeń. Na przykład po określeniu tabelę zewnętrzną Hive danych dla tabeli zewnętrznej Hive można przechowywane w magazynu obiektów blob platformy Azure z następujących 000000_0 nazwy. Użyj moduł czytnika w eksperymencie można odczytać wielu plików i używać ich na potrzeby prognoz.

Korzystając z czytnika modułu w eksperymencie usługi Azure Machine Learning, obiektów Blob platformy Azure można określić jako danych wejściowych. Pliki w magazynie obiektów blob platformy Azure mogą być pliki wyjściowe (przykład: 000000_0) który są produkowane przez Pig i Hive skryptu uruchomionego w usłudze HDInsight. Moduł czytnika umożliwia odczytanie plików (nie rozszerzenia), konfigurując **ścieżki do kontenera, katalog/obiektów blob**. **Ścieżki do kontenera** punktów do kontenera i **katalogu/obiektów blob** wskazuje folder, który zawiera pliki, jak pokazano na poniższej ilustracji. Gwiazdka oznacza to, \*) **Określa, że wszystkie pliki w folderze/kontenera (oznacza to, aggregateddata/rocznie danych = miesiąc-2014-6 /\*)** są odczytywane w ramach eksperymentu.

![Właściwości obiektów Blob platformy Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Przykład
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>W potoku z działania AzureMLBatchExecution o parametry usługi sieci Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
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

* Wdrożonej usługi sieci Web Azure Machine Learning używa czytnika i modułu zapisywania danych bazy danych SQL Azure z/do odczytu/zapisu. Ta usługa sieci Web udostępnia następujące cztery parametry: Nazwa serwera, nazwa bazy danych, nazwę konta użytkownika serwera i hasło konta użytkownika serwera bazy danych.  
* Zarówno **start** i **zakończenia** dat i godzin musi być w [formacie ISO](http://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. **Zakończenia** czasu jest opcjonalna. Jeśli nie określisz wartości **zakończenia** właściwości, jest on obliczany jako "**start + 48 godzin.**" Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

### <a name="other-scenarios"></a>Inne scenariusze
#### <a name="web-service-requires-multiple-inputs"></a>Usługa sieci Web wymaga wielu danych wejściowych
Jeśli usługa sieci web ma wielu danych wejściowych, użyj **webServiceInputs** zamiast za pomocą właściwości **WebServiceInputActivity**. Zestawy danych, które odwołują się **webServiceInputs** muszą także być ujęte w działaniu **dane wejściowe**.

W eksperymencie uczenie Maszynowe Azure wprowadzania usługi sieci web i porty wyjścia i parametry globalne mają nazwy domyślnej ("input1", "input2"), które można dostosować. Nazwy używanych w przypadku webServiceInputs, webServiceOutputs i ustawienia globalParameters musi dokładnie odpowiadać nazwy eksperymenty. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego uczenia Maszynowego Azure można zweryfikować oczekiwanego mapowania.

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
Usługi sieci web wykonywania wsadowego Azure ML może służyć do uruchamiania przepływów pracy, skryptów przykład R lub Python, które nie wymagają żadnych danych wejściowych. Lub eksperyment mogą być skonfigurowane przy użyciu modułu nie ujawnia żadnych GlobalParameters czytniku. W takim przypadku działanie AzureMLBatchExecution będzie można skonfigurować w następujący sposób:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Usługa sieci Web nie wymaga danych wejściowych/wyjściowych
Usługa sieci web uzyskiwania informacji na temat wykonywania wsadowego uczenie Maszynowe Azure może nie mieć żadnych danych wyjściowych z usługą sieci Web skonfigurowana. W tym przykładzie nie istnieje usługi sieci Web w danych wejściowych lub wyjściowych, ani nie skonfigurowano żadnych GlobalParameters. Nadal jest wyjściem skonfigurowane w działaniu sam, ale nie zostanie podany jako webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Czytniki używa usługi sieci Web i zapisywania i uruchomień działania tylko wtedy, gdy inne działania zakończyło się pomyślnie
Uczenie Maszynowe Azure w sieci web usługi składników zapisywania i odczytywania modułów mogą być skonfigurowane do korzystania z lub bez żadnych GlobalParameters. Jednak możesz osadzić wywołania usług w potoku, który używa zależności zestawu danych do wywołania usługi tylko wtedy, gdy niektóre nadrzędnego przetwarzanie zostało zakończone. Po zakończeniu wykonywania wsadowego usługi przy użyciu tej metody, można również uruchomić innych działań. W takim przypadku można wyrazić za pomocą działania wejściach i wyjściach, bez nazw ich jako usługi sieci Web wejściowych i wyjściowych zależności.

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

**Takeaways** są:

* Jeśli punkt końcowy eksperyment używa WebServiceInputActivity: jest reprezentowana przez zestaw danych obiektów blob i jest dostępny w danych wejściowych działania i WebServiceInputActivity właściwość. W przeciwnym razie właściwość WebServiceInputActivity została pominięta.
* Jeśli punkt końcowy eksperyment używa webServiceOutput(s): są reprezentowane przez zestawy danych obiektów blob i zawarte w danych wyjściowych działania oraz we właściwości webServiceOutputs. Wyświetla działania i webServiceOutputs są mapowane przez nazwę każdego dane wyjściowe w eksperymencie. W przeciwnym razie wartość właściwości webServiceOutputs zostanie pominięty.
* Jeśli punkt końcowy eksperyment przedstawia globalParameter(s), otrzymują one we właściwości globalParameters działania jako pary kluczy, wartość. W przeciwnym razie wartość właściwości globalParameters zostanie pominięty. Kluczy jest rozróżniana wielkość liter. [Funkcje fabryki danych Azure](data-factory-functions-variables.md) mogą być używane w wartości.
* Dodatkowe zestawy danych może być zawarta w właściwości danych wejściowych i wyjściowych działania, bez przywoływane w typeProperties działania. Te zestawy danych będą zarządzały sposobem wykonania przy użyciu zależności wycinka, ale w przeciwnym razie są ignorowane przez działanie AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizowanie modeli przy użyciu działanie aktualizacji zasobu
Po wykonaniu ponownego trenowania zaktualizować oceniania usługi sieci web (eksperyment predykcyjny udostępniony jako usługa sieci web) z nowo trenowanego modelu przy użyciu **działanie aktualizacji zasobu usługi Azure ML**. Zobacz [aktualizowanie modeli przy użyciu działanie aktualizacji zasobu](data-factory-azure-ml-update-resource-activity.md) artykułu, aby uzyskać szczegółowe informacje.

### <a name="reader-and-writer-modules"></a>Czytnik i modułów zapisywania
Typowy scenariusz użycia parametrów usługi sieci Web jest używana czytników SQL Azure i modułów zapisujących. Moduł czytnika jest używana do ładowania danych do eksperymentów z usług zarządzania danych poza Azure Machine Learning Studio. Moduł zapisywania jest zapisanie danych z eksperymentów w danych zarządzania usługi poza Azure Machine Learning Studio.  

Aby uzyskać więcej informacji o SQL Azure/obiektów Blob Azure odczytywania/zapisywania, zobacz [czytnika](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [zapisywania](https://msdn.microsoft.com/library/azure/dn905984.aspx) tematy w bibliotece MSDN. Przykład w poprzedniej sekcji używane zapisywania obiektów Blob platformy Azure i odczytywania obiektów Blob platformy Azure. W tej sekcji omówiono przy użyciu czytnik Azure SQL i składnika zapisywania usługi Azure SQL.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Pytanie:** mam wiele plików, które są generowane przez mój potoki danych big data. Działanie AzureMLBatchExecution można służy do pracy we wszystkich plikach?

**Odpowiedź:** tak. Zobacz **można odczytać danych z wielu plików w obiekcie Blob Azure za pomocą modułu Reader** sekcji, aby uzyskać szczegółowe informacje.

## <a name="azure-ml-batch-scoring-activity"></a>Działanie oceniania partii zadań Azure ML
Jeśli używasz **AzureMLBatchScoring** działania integracji z usługi Azure Machine Learning, zaleca się, że używasz najnowszej **AzureMLBatchExecution** działania.

Wprowadzono działania AzureMLBatchExecution w wydaniu sierpnia 2015 zestawu Azure SDK i programu Azure PowerShell.

Jeśli chcesz kontynuować korzystanie z działania AzureMLBatchScoring, nadal odczytu za pomocą tej sekcji.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Wsadowe ocenianie przez ML działanie Azure przy użyciu usługi Azure Storage dla wejścia/wyjścia

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
Aby określić wartości parametrów usługi sieci Web, Dodaj **typeProperties** sekcji do **AzureMLBatchScoringActivty** sekcji w potoku JSON, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Można również użyć [funkcji fabryki danych](data-factory-functions-variables.md) w przekazywanie wartości dla sieci Web usługi parametrów, jak pokazano w poniższym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Parametry usługi sieci Web jest rozróżniana wielkość liter, dlatego upewnij się, że nazwy, które określisz w działaniu JSON zgodne te udostępniane przez usługę sieci Web.
>
>

## <a name="see-also"></a>Zobacz też
* [Azure wpis w blogu: wprowadzenie do fabryki danych Azure i usługi Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
