---
title: Tworzenie potoków predykcyjnej danych przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć potok predykcyjnej przy użyciu usługi Azure Machine Learning — działanie wykonywania wsadowego w fabryce danych Azure.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 8d87facdf1724ddbcb3bb6d7c189d8ff41d44973
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Tworzenie potoków predykcyjnej przy użyciu usługi Azure Machine Learning i fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Wersja 2 — wersja zapoznawcza](transform-data-using-machine-learning.md)

[Usługa Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umożliwia tworzenie, testowanie i wdrażanie rozwiązań analizy predykcyjnej. Z wysokiego poziomu punktu widzenia można to zrobić w trzy kroki:

1. **Tworzenie eksperymentu uczenia**. Aby wykonać ten krok za pomocą usługi Azure ML Studio. Studio uczenia Maszynowego to środowisko visual programowanie zespołowe używanej do nauczenia i przetestowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go eksperyment predykcyjny**. Po modelu po zapoznaniu z istniejącymi danymi i chcesz używać go do oceniać nowe dane, Przygotuj i usprawnić eksperymentu wyników.
3. **Go wdrożyć jako usługę sieci web**. Możesz opublikować oceniania eksperymentu jako usługi sieci web platformy Azure. Można wysyłać dane do modelu za pomocą tego punktu końcowego usługi sieci web i odbierać prognoz wyniku z modelu.  

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [działanie wykonywania wsadowego Machine Learning w wersji 1](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Fabryki danych i jednocześnie uczenia maszynowego
Fabryka danych Azure umożliwia łatwe tworzenie potoków, które używają opublikowanych [Azure Machine Learning] [-uczenia maszynowego azure-] usługi sieci web analizy predykcyjnej. Przy użyciu **działanie wykonywania wsadowego** w potoku fabryki danych Azure, można wywołać usługi sieci web uczenie Maszynowe Azure w celu prognozowania danych w partii. 

Wraz z upływem czasu modeli predykcyjnych w uczenie Maszynowe Azure oceniania eksperymenty konieczne retrained, przy użyciu nowych baz danych wejściowych. Można ponownie ucz model usługi uczenie Maszynowe Azure z potoku fabryki danych, wykonując następujące czynności:

1. Opublikuj eksperyment uczenia (eksperyment predykcyjny nie) jako usługę sieci web. Należy wykonać ten krok w usłudze Azure ML Studio, jak udostępnić eksperyment predykcyjny jako usługę sieci web w poprzednim scenariuszu.
2. Umożliwia wywoływanie usługi sieci web dla eksperyment uczenia działanie wykonywania wsadowego uczenia Maszynowego Azure. Zasadniczo służy działanie wykonywania wsadowego usługi uczenie Maszynowe Azure do wywołania oceniania usług i usługi sieci web szkolenia.

Po wykonaniu ponownego trenowania zaktualizować oceniania usługi sieci web (eksperyment predykcyjny udostępniony jako usługa sieci web) z nowo trenowanego modelu przy użyciu **działanie aktualizacji zasobu usługi Azure ML**. Zobacz [aktualizowanie modeli przy użyciu działanie aktualizacji zasobu](update-machine-learning-models.md) artykułu, aby uzyskać szczegółowe informacje.

## <a name="azure-machine-learning-linked-service"></a>Usługa Azure Machine Learning połączone

Możesz utworzyć **usługi Azure Machine Learning** połączonej usługi, aby połączyć usługi sieci Web Azure Machine Learning z fabryki danych Azure. Połączona usługa jest używany przez działanie wykonywania wsadowego usługi Azure Machine Learning i [działanie aktualizacji zasobu](update-machine-learning-models.md). 


```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "apiVersion" : "2017-09-01-preview",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu, aby uzyskać informacje dotyczące właściwości w definicji JSON. 

Usługa Azure Machine Learning obsługują klasycznym usługi sieci Web i nowej usługi sieci Web do predykcyjnej eksperymentu. Można wybrać właściwy do użycia z fabryki danych. Aby uzyskać informacje wymagane do utworzenia Azure Machine Learning połączonej usługi, przejdź do https://services.azureml.net, gdzie są wyświetlane wszystkie (nowy) usługi sieci Web i usług sieci Web klasycznego. Kliknij opcję usługi sieci Web, które chcesz uzyskać dostęp, a następnie kliknij przycisk **Consume** strony. Kopiuj **klucza podstawowego** dla **apiKey** właściwości, oraz **żądań wsadowych** dla **mlEndpoint** właściwości. 

![Usługi sieci Web uczenie maszynowe Azure](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Działanie usługi Azure Machine Learning wsadowe

Poniższy fragment kodu JSON definiuje działania usługi Azure Machine Learning wsadowe. Definicja działania zawiera odwołanie do usługi Azure Machine Learning połączone, utworzony wcześniej. 

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
| description       | Tekst opisujący działanie robi.  | Nie       |
| type              | Dla działania Data Lake Analytics U-SQL jest typ działania **AzureMLBatchExecution**. | Yes      |
| linkedServiceName | Połączonej usługi uczenie maszynowe Azure połączonej usługi. Aby dowiedzieć się więcej na temat tej połączonej usługi, zobacz [obliczeniowe połączonych usług](compute-linked-services.md) artykułu. | Yes      |
| webServiceInputs  | Klucz, pary wartości, mapowania nazw danych wejściowych usługi sieci Web Azure Machine Learning. Klucz musi być zgodny parametry wejściowe zdefiniowane w opublikowanej usługi sieci Web uczenie Azure maszyny. Wartość jest pary właściwości połączonej usługi magazynu Azure i FilePath Określanie wejściowych lokalizacji obiektu Blob. | Nie       |
| webServiceOutputs | Klucz, pary wartości, mapowanie nazwy dane wyjściowe usługi sieci Web Azure Machine Learning. Klucz musi być zgodny parametry wyjściowe zdefiniowane w opublikowanej usługi sieci Web uczenie Azure maszyny. Wartość jest połączonej usługi magazynu Azure i ścieżka lokalizacji obiektu Blob pary właściwości określania danych wyjściowych. | Nie       |
| globalParameters  | Pary kluczy, wartość do przekazania do punktu końcowego usługi wykonywania wsadowego uczenia Maszynowego Azure. Klucze musi być zgodne z nazwami parametry usługi sieci web zdefiniowany w opublikowanej usługi uczenie Maszynowe Azure w sieci web. Wartości są przekazywane we właściwości GlobalParameters żądania wykonywania wsadowego uczenie Maszynowe Azure | Nie       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz 1: Eksperymentów przy użyciu sieci Web usługi wejść/wyjść odwołujące się do danych w magazynie obiektów Blob Azure

W tym scenariuszu usługi sieci Web Azure Machine Learning sprawia, że prognoz przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i przechowuje wyniki prognozowania w magazynie obiektów blob. Następujące JSON definiuje potoku fabryki danych do działania AzureMLBatchExecution. Dane wejściowe i wyjściowe w usłudze Azure Storage blogu odwołuje się przy użyciu parę LinkedName FilePath. W przykładzie połączonej usługi z wejściami i wyjściami są różne, można użyć różnych usług połączonych dla każdego użytkownika wejścia/wyjścia dla fabryki danych możliwość Podnieś prawo plików i wysłać do usługi sieci Web uczenie Maszynowe Azure. 

> [!IMPORTANT]
> W eksperymentu uczenia Maszynowego Azure, usługi sieci web w danych wejściowych i wyjściowych portów i parametry globalne mają nazwy domyślnej ("input1", "input2"), które można dostosować. Nazwy używanych w przypadku webServiceInputs, webServiceOutputs i ustawienia globalParameters musi dokładnie odpowiadać nazwy eksperymenty. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego uczenia Maszynowego Azure można zweryfikować oczekiwanego mapowania.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz 2: Eksperymentów przy użyciu modułów odczytywania/zapisywania odwoływać się do danych w różnych miejsc
Inny typowy scenariusz, podczas tworzenia eksperymenty uczenie Maszynowe Azure ma używać modułów importowanie danych i danych wyjściowych. Moduł importowanie danych jest używany do ładowania danych do eksperymentu, a moduł danych wyjściowych jest zapisanie danych z eksperymentów. Aby uzyskać szczegółowe informacje o modułach importowanie danych i danych wyjściowych, zobacz [i zaimportuj dane](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [danych wyjściowych](https://msdn.microsoft.com/library/azure/dn905984.aspx) tematy w bibliotece MSDN.     

Korzystając z modułów importowanie danych i danych wyjściowych, jest dobrym rozwiązaniem, użyj parametru usługi sieci Web dla każdej właściwości w tych modułów. Te parametry sieci web umożliwiają skonfigurowanie wartości w czasie wykonywania. Na przykład można utworzyć eksperyment modułem importowanie danych, który używa bazy danych SQL Azure: XXX.database.windows.net. Po wdrożeniu usługi sieci web ma zostać włączony korzystającym z usług sieci web określić inny serwer SQL Azure o nazwie `YYY.database.windows.net`. Parametr usługi sieci Web służy do zezwalania tę wartość do skonfigurowania.

> [!NOTE]
> Usługi sieci Web w danych wejściowych i wyjściowych różnią się od parametry usługi sieci Web. Pierwszy scenariusz już wspomniano, jak dane wejściowe i wyjściowe można określić dla usługi sieci Web uczenie Maszynowe Azure. W tym scenariuszu należy przekazać parametry usługi sieci Web, które odpowiadają właściwości modułów importu danych/Wyjście danych.
>
> 

Przyjrzyjmy się scenariusz użycia parametry usługi sieci Web. Masz wdrożonej usługi sieci web Azure Machine Learning, używaną do odczytywania danych z jednego ze źródeł danych obsługiwane przez usługi Azure Machine Learning moduł czytnika (na przykład: baza danych SQL Azure). Po wykonaniu wykonywania wsadowego usługi wyniki są napisane przy użyciu modułu zapisywania (baza danych SQL Azure).  Nie sieci web usługi wejściami i wyjściami są definiowane w eksperymenty. W takim przypadku firma Microsoft zaleca, aby skonfigurować parametry usługi sieci web odpowiednich składników zapisywania i odczytywania modułów. Ta konfiguracja pozwala odczytywania/zapisywania modułów można skonfigurować podczas używania działania AzureMLBatchExecution. Określ parametry usługi sieci Web w **globalParameters** sekcji w działaniu JSON w następujący sposób.

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
> Parametry usługi sieci Web jest rozróżniana wielkość liter, dlatego upewnij się, że nazwy, które określisz w działaniu JSON zgodne te udostępniane przez usługę sieci Web.
>

Po wykonaniu ponownego trenowania zaktualizować oceniania usługi sieci web (eksperyment predykcyjny udostępniony jako usługa sieci web) z nowo trenowanego modelu przy użyciu **działanie aktualizacji zasobu usługi Azure ML**. Zobacz [aktualizowanie modeli przy użyciu działanie aktualizacji zasobu](update-machine-learning-models.md) artykułu, aby uzyskać szczegółowe informacje.



## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, które opisują sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie gałęzi](transform-data-using-hadoop-hive.md)
* [Działanie pig](transform-data-using-hadoop-pig.md)
* [Działania MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działaniu przesyłania strumieniowego usługi Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działania procedury składowanej](transform-data-using-stored-procedure.md)
