---
title: "Użyj funkcji zbierania danych modelu w konsoli usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft"
description: "Ten artykuł zawiera informacje o sposobie używania funkcji zbierania danych modelu w Azure Machine Learning Workbench"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2251f8d241f3ec47d46c04160caf997bcd025124
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Zbieranie danych modelu przy użyciu funkcji zbierania danych

Funkcji zbierania danych modelu w usłudze Azure Machine Learning służy do archiwizowania wejść modelu i prognozowanie z usługi sieci web.

## <a name="install-the-data-collection-package"></a>Zainstaluj pakiet kolekcji danych
Biblioteka zbierania danych można zainstalować natywnie w systemie Linux i Windows.

### <a name="windows"></a>Windows
W systemie Windows należy zainstalować moduł zbierający danych za pomocą następującego polecenia:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
W systemie Linux najpierw zainstaluj biblioteki libxml ++. Uruchom następujące polecenie, które musi zostać wystawiony w obszarze sudo:

    sudo apt-get install libxml++2.6-2v5

Następnie uruchom następujące polecenie:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Ustaw zmienne środowiskowe

Zbieranie danych modelu zależy od dwóch zmiennych środowiskowych. Musi mieć ustawioną AML_MODEL_DC_STORAGE_ENABLED **true** (tylko małe litery) i AML_MODEL_DC_STORAGE musi być ustawione parametry połączenia dla konta usługi Magazyn Azure, w którym chcesz przechowywać dane.

Te zmienne środowiskowe są ustawiane dla Ciebie już, gdy usługa sieci web działa w klastrze na platformie Azure. Podczas uruchamiania lokalnego należy ustawić samodzielnie. Jeśli używasz Docker należy użyć parametru -e docker polecenia do przekazania zmiennych środowiskowych.

## <a name="collect-data"></a>Zbieranie danych

Aby korzystać z funkcji zbierania danych modelu, należy wprowadzić następujące zmiany do pliku wyników:

1. Dodaj następujący kod w górnej części pliku:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Dodaj następujące wiersze kodu do `init()` funkcji:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Dodaj następujące wiersze kodu do `run(input_df)` funkcji:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Upewnij się, że zmienne `input_df` i `pred` (wartość prognozowania z `model.predict()`) są inicjowane przed wywołaniem `collect()` funkcja na nich.

4. Użyj `az ml service create realtime` z `--collect-model-data true` przełącznik, aby utworzyć usługę sieci web czasu rzeczywistego. Ten krok zapewnia, że dane modelu są zbierane, gdy uruchomiona jest usługa.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Aby przetestować zbierania danych, uruchom `az ml service run realtime` polecenia:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Widok zebranych danych
Aby wyświetlić zebrane dane w magazynie obiektów blob:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie usługi**.
3. W polu wyszukiwania wpisz **kont magazynu** i wybierz klawisz Enter.
4. Z **kont magazynu** bloku wyszukiwania wybierz **konta magazynu** zasobów. Aby określić konto magazynu, wykonaj następujące kroki:

    a. Przejdź do usługi Azure Machine Learning Workbench, wybierz projekt pracuje i otwórz wiersz polecenia z **pliku** menu.
    
    b. Wprowadź `az ml env show -v` i sprawdź *storage_account* wartości. Jest to nazwa używanego konta magazynu.

5. Wybierz **kontenery** zasobu o nazwie menu bloku, a następnie kontenera **modeldata**. Aby wyświetlić dane start propagowanie do konta magazynu, może być konieczne odczekanie 10 minut po pierwszym żądanie usługi sieci web. Dane płyną do obiektów blob przy użyciu następującej ścieżki kontenera:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Dane mogą być używane z obiekty BLOB platformy Azure na różne sposoby, za pośrednictwem oprogramowania firmy Microsoft i narzędzi open source. Oto kilka przykładów:
- Azure Machine Learning Workbench: Otwórz plik CSV w Azure Machine Learning Workbench przez dodanie pliku CSV jako źródła danych.
- Excel: Otwórz codzienne pliki CSV jako arkusz kalkulacyjny.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): Tworzenie wykresów z danymi pobierane z danych CSV w obiektach blob.
- [Platforma Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): Utwórz ramkę, dane z dużej części danych CSV.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Gałąź rejestru](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): ładowanie danych CSV do gałęzi tabeli i wykonywania zapytań SQL bezpośrednio w odniesieniu do obiektu blob.

