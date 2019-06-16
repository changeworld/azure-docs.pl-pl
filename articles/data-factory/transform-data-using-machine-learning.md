---
title: Tworzenie potoków danych prognostycznych przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć potok predykcyjne za pomocą usługi Azure Machine Learning — działanie wykonywania wsadowego w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: aaf1d72a0c9c56e7d140fb615caf014507ebf263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60928080"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Bieżąca wersja](transform-data-using-machine-learning.md)

[Usługa Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) pozwala na tworzenie, testowanie i wdrażanie rozwiązań analizy predykcyjnej. Z wysokiego poziomu punktu widzenia można to zrobić w trzech krokach:

1. **Tworzenie eksperymentu szkolenia**. Ten krok jest wykonywane przy użyciu usługi Azure Machine Learning studio. Usługa Azure Machine Learning studio to środowisko programowanie zespołowe visual używanej do nauczenia i przetestowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go na eksperyment predykcyjny**. Gdy model po zapoznaniu z istniejącymi danymi i wszystko będzie gotowe na potrzeby oceniać nowe dane, Przygotuj i usprawnić eksperymentu do oceniania.
3. **Go wdrożyć jako usługę sieci web**. Możesz opublikować oceniania eksperymentu jako usługi sieci web platformy Azure. Można wysyłać dane do modelu przy użyciu tego punktu końcowego usługi sieci web i otrzymywać wyników przewidywań modelu.

### <a name="data-factory-and-machine-learning-together"></a>Fabryka danych i uczenia maszynowego razem
Usługa Azure Data Factory umożliwia łatwe tworzenie potoki, które używają opublikowanych [usługi Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) sieci web usługi do analizy predykcyjnej. Za pomocą **działanie wykonywania wsadowego** w potoku usługi Azure Data Factory można wywołać usługi sieci web Azure Machine Learning studio, jak tworzyć prognozy na danych w usłudze batch.

Wraz z upływem czasu modele predykcyjne w programie Azure Machine Learning studio, oceniania eksperymentów konieczne retrained, przy użyciu nowych danych wejściowych zestawów danych. Model z potoku usługi fabryka danych mogą przechowywać, wykonując następujące czynności:

1. Publikuj eksperymentu szkolenia (eksperyment nie predykcyjny) jako usługę sieci web. Możesz wykonać ten krok w programie Azure Machine Learning studio, tak jak do udostępnienia eksperyment predykcyjny jako usługę internetową w poprzednim scenariuszu.
2. Użyj usługi Azure Machine Learning studio działanie wykonywania wsadowego do wywołania usługi sieci web dla eksperymentu szkolenia. Po prostu umożliwia studio usługi Azure Machine Learning Batch Execution, działanie Wywołaj usługę sieci web szkolenia i oceniania usługi sieci web.

Po zakończeniu ponownego trenowania, zaktualizować usługi internetowej przyznawania ocen (udostępniane jako usługi sieci web eksperyment predykcyjny) przy użyciu nowo uczonego modelu przy użyciu **usługi Azure Machine Learning studio działanie aktualizacji zasobu**. Zobacz [aktualizacji modeli za pomocą działania aktualizowania zasobów](update-machine-learning-models.md) artykuł, aby uzyskać szczegółowe informacje.

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone

Możesz utworzyć **usługi Azure Machine Learning** połączoną usługę służącą do łączenia usługi sieci Web Azure Machine Learning do usługi Azure data factory. Połączona usługa jest używana przez działanie wykonywania wsadowego dla Machine Learning, Azure i [działanie aktualizacji zasobu](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykuł dotyczący opisy właściwości w definicji JSON.

Usługa Azure Machine Learning obsługuje zarówno w przypadku klasycznych usług sieci Web, jak i nowych usług sieci Web do swojego eksperymentu predykcyjnego. Możesz wybrać właściwy do użycia z fabryką danych. Aby uzyskać informacje wymagane do utworzenia usługi połączonej usługi Azure Machine Learning, przejdź do https://services.azureml.net, gdzie są wymienione wszystkie (nowy) usług sieci Web i klasycznych usług sieci Web. Kliknij opcję usługi sieci Web, które chcesz uzyskać dostęp, a następnie kliknij przycisk **zużywania** strony. Kopiuj **klucza podstawowego** dla **apiKey** właściwości i **żądań wsadowych** dla **mlEndpoint** właściwości.

![Usługi sieci Web Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Usługa Azure Machine Learning Batch Execution działania

Poniższy fragment kodu JSON definiuje działania usługi Azure Machine Learning Batch Execution. Definicja aktywności zawiera odwołanie do usługi Azure Machine Learning, połączone, która została utworzona wcześniej.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nazwa działania w potoku     | Yes      |
| description       | Tekst opisujący, co działanie robi.  | Nie       |
| type              | Działanie U-SQL usługi Data Lake Analytics jest typ działania **AzureMLBatchExecution**. | Yes      |
| linkedServiceName | Połączone usługi do usługi Azure Machine Learning połączoną usługę. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [usługi połączone usługi Compute](compute-linked-services.md) artykułu. | Tak      |
| webServiceInputs  | Klucz, wartość pary mapowania nazw danych wejściowych usługi sieci Web Azure Machine Learning. Klucz musi być zgodny parametry wejściowe zdefiniowane w opublikowanej Azure usługi internetowej Machine Learning. Wartość jest połączonych usług magazynu Azure FilePath właściwości parę i określanie wejściowych lokalizacji obiektu Blob. | Nie       |
| webServiceOutputs | Wartość pary kluczy, mapowanie nazw usługi Azure Machine Learning sieci Web usługi wyników. Klucz musi być zgodny parametry wyjściowe zdefiniowane w opublikowanej Azure usługi internetowej Machine Learning. Wartość jest połączonych usług magazynu platformy Azure i FilePath parze właściwości, określając dane wyjściowe obiektu Blob w lokalizacji. | Nie       |
| globalParameters  | Pary wartości klucza, które zostaną przekazane do punktu końcowego usługi Azure Machine Learning studio usługę wykonywania wsadowego. Klucze musi być zgodne z nazwami parametry usługi sieci web zdefiniowany w opublikowanej usługi sieci web Azure Machine Learning studio. Wartości są przekazywane we właściwości GlobalParameters żądania wykonania partii Azure Machine Learning studio | Nie       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz 1: Eksperymentów przy użyciu sieci Web usługi danych wejściowych lub wyjściowych, które odwołują się do danych w usłudze Azure Blob Storage

W tym scenariuszu usługi Azure Machine Learning w sieci Web sprawia, że prognozy przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i przechowuje wyniki prognozy w magazynie obiektów blob. Następujący kod JSON definiuje potok usługi Data Factory z działaniem AzureMLBatchExecution. Dane wejściowe i wyjściowe w usłudze Azure Blog Storage odwołuje się za pomocą pary LinkedName i ścieżka pliku. W przykładzie połączonej usługi dane wejściowe i wyjściowe są różne, różnych połączonych usług dla każdej z danych wejściowych/wyjściowych dla usługi Data Factory umożliwia można wczytać odpowiednie pliki i wysyłanie do usługi Azure Machine Learning studio usługi sieci Web.

> [!IMPORTANT]
> W usłudze Azure Machine Learning studio eksperymentu, dane wejściowe usługi sieci web i danych wyjściowych portów i parametrów globalnych mają nazwy domyślnej ("input1", "wejście2"), które można dostosować. Nazwy używanych w przypadku ustawienia globalParameters, webServiceOutputs i webServiceInputs musi dokładnie odpowiadać nazwy w eksperymenty. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego usługi Azure Machine Learning studio zweryfikować oczekiwane mapowania.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz 2: Eksperymentów przy użyciu czytnika/zapisywania modułów do odwoływania się do danych w różnych miejsc
Inny typowy scenariusz, podczas tworzenia usługi Azure Machine Learning studio eksperymentów jest używać modułów importu i dane wyjściowe. Modułu importu danych jest używana do ładowania danych do eksperymentu, a moduł danych wyjściowych jest zapisywane są dane z eksperymentów. Aby uzyskać szczegółowe informacje dotyczące modułów importu i dane wyjściowe, zobacz [importu danych](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [dane wyjściowe](https://msdn.microsoft.com/library/azure/dn905984.aspx) tematy w bibliotece MSDN.

Korzystając z modułów importu i dane wyjściowe, jest dobrym rozwiązaniem, użyj parametru usługi sieci Web dla każdej właściwości tych modułów. Te parametry sieci web umożliwiają skonfigurowanie wartości podczas wykonywania. Na przykład można utworzyć eksperyment za pomocą modułu importu danych, która używa usługi Azure SQL Database: XXX.database.windows.net. Po wdrożeniu usługi sieci web, aby włączyć konsumentów usługi sieci web określić inny serwer SQL platformy Azure o nazwie `YYY.database.windows.net`. Parametr usługi sieci Web można użyć, aby zezwolić na tę wartość do skonfigurowania.

> [!NOTE]
> Usługa sieci Web w danych wejściowych i wyjściowych różnią się od parametry usługi sieci Web. W przypadku pierwszego scenariusza wiesz, jak dane wejściowe i wyjściowe można określić dla usługi Azure Machine Learning studio usługi sieci Web. W tym scenariuszu przekażesz parametry usługi sieci Web, które odnoszą się do właściwości modułów importu danych/Wyjście danych.
>
>

Spójrzmy na scenariusz użycia parametry usługi sieci Web. Masz wdrożonej usługi sieci web usługi Azure Machine Learning, który używa modułu reader można odczytać danych z jednego źródła danych obsługiwane przez usługi Azure Machine Learning (na przykład: Azure SQL Database). Wykonywanie wsadowe zostanie wykonane, wyniki są zapisywane z użyciem modułu zapisywania (Azure SQL Database).  Nie sieci web usługi wejściami i wyjściami są definiowane w eksperymenty. W tym przypadku zaleca się skonfigurowanie parametrów usługi web odpowiednie dla modułów czytników i składników zapisywania. Ta konfiguracja pozwala czytnika/zapisywania modułów można skonfigurować podczas używania działania AzureMLBatchExecution. Określ parametry usługi sieci Web w **globalParameters** sekcji w kodzie JSON działania w następujący sposób.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Parametry usługi sieci Web jest rozróżniana wielkość liter, dlatego upewnij się, że nazwy, które określisz w działaniu JSON są zgodne z typami udostępnianych przez usługę sieci Web.
>

Po zakończeniu ponownego trenowania, zaktualizować usługi internetowej przyznawania ocen (udostępniane jako usługi sieci web eksperyment predykcyjny) przy użyciu nowo uczonego modelu przy użyciu **usługi Azure Machine Learning studio działanie aktualizacji zasobu**. Zobacz [aktualizacji modeli za pomocą działania aktualizowania zasobów](update-machine-learning-models.md) artykuł, aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie technologii hive](transform-data-using-hadoop-hive.md)
* [Działania technologii pig](transform-data-using-hadoop-pig.md)
* [Działania technologii MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działania przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działania platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działania procedur składowanych](transform-data-using-stored-procedure.md)
