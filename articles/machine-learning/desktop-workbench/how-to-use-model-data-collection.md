---
title: Użyj funkcji zbierania danych modelu w aplikacji Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o sposobie używania funkcji zbierania danych modelu w aplikacji Azure Machine Learning Workbench
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 5c1a884ebe6216c4e8099f2ada2182ccff68b63e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449785"
---
# <a name="collect-model-data-by-using-data-collection"></a>Zbieranie danych modelu za pomocą kolekcji danych

Funkcji zbierania danych modelu w usłudze Azure Machine Learning służy do archiwizowania danych wejściowych i modelu za pomocą usługi sieci web.

## <a name="install-the-data-collection-package"></a>Zainstaluj pakiet kolekcji danych
Biblioteka zbierania danych można zainstalować natywnie w systemie Linux i Windows.

### <a name="windows"></a>Windows
W Windows należy zainstalować moduł zbierający dane za pomocą następującego polecenia:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
W systemie Linux należy najpierw zainstalować bibliotekę libxml ++. Uruchom następujące polecenie, które musi zostać wystawiony w obszarze "sudo":

    sudo apt-get install libxml++2.6-2v5

Następnie uruchom następujące polecenie:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Zbieranie danych modelu, zależy od dwóch zmiennych środowiskowych. Musi być równa AML_MODEL_DC_STORAGE_ENABLED **true** (tylko małe litery) i AML_MODEL_DC_STORAGE musi być ustawione parametry połączenia dla konta usługi Azure Storage, w którym chcesz przechowywać dane.

Te zmienne środowiskowe już są ustawiane dla Ciebie, kiedy usługa sieci web jest uruchomiona w klastrze na platformie Azure. Podczas uruchamiania lokalnego należy ustawić je samodzielnie. Jeśli używasz platformy Docker, należy użyć parametru -e platformy docker, uruchom polecenie przekazać zmienne środowiskowe.

## <a name="collect-data"></a>Zbieranie danych

Aby użyć zbierania danych modelu, należy wprowadzić następujące zmiany do oceniania pliku:

1. Dodaj następujący kod w górnej części pliku:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

1. Dodaj następujące wiersze kodu w celu `init()` funkcji:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1. Dodaj następujące wiersze kodu w celu `run(input_df)` funkcji:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Upewnij się, że zmienne `input_df` i `pred` (wartość prognozy `model.predict()`) są inicjowane przed wywołaniem `collect()` funkcji na nich.

1. Użyj `az ml service create realtime` polecenia `--collect-model-data true` przełącznik, aby utworzyć usługę internetową czasu rzeczywistego. Ten krok pozwala się upewnić, że dane modelu są zbierane, gdy uruchomiona jest usługa.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
1. Aby przetestować zbierania danych, uruchom `az ml service run realtime` polecenia:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Wyświetlanie zebranych danych
Aby wyświetlić zebrane dane w magazynie obiektów blob:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **wszystkich usług**.
1. W polu wyszukiwania wpisz **kont magazynu** i naciśnij klawisz Enter.
1. Z **kont magazynu** bloku wyszukiwania, wybierz opcję **konta magazynu** zasobów. Aby określić konto magazynu, użyj następujących kroków:

    a. Przejdź do usługi Azure Machine Learning Workbench, wybierz projekt, nad i otwórz wiersz polecenia z **pliku** menu.
    
    b. Wprowadź `az ml env show -v` i sprawdź *storage_account* wartości. Jest to nazwa używanego konta magazynu.

1. Wybierz **kontenery** w zasobie menu bloku i kontener wywołuje **modeldata**. Aby wyświetlić dane, które powinno zacząć się propagowanie do konta magazynu, może być konieczne poczekać maksymalnie 10 minut od pierwszego żądania usługi sieci web. Dane płyną do obiektów blob przy użyciu następującej ścieżki kontenera:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Dane mogą być używane z obiektów blob platformy Azure na różne sposoby, za pomocą oprogramowania firmy Microsoft i narzędzi typu open source. Oto kilka przykładów:
- Usługa Azure Machine Learning Workbench: Otwórz plik CSV w usłudze Azure Machine Learning Workbench, dodając plik CSV jako źródło danych.
- Excel: Otwórz dzienne pliki CSV jako arkusz kalkulacyjny.
- [Usługa Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): twórz wykresy zawierające dane pobierane z danych CSV w obiektach blob.
- [Platforma Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): Utwórz ramkę danych zawierającą dużą część danych CSV.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): Załaduj dane CSV do gałęzi tabeli i wykonaj zapytania SQL bezpośrednio w odniesieniu do obiektu blob.

