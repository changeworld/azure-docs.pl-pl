---
title: "Szczegółowy przewodnik na temat korzystania z usługi Azure Machine Learning danych przygotowania Operationalization | Dokumentacja firmy Microsoft"
description: "Niniejszy dokument zawiera szczegółowe informacje o poprzednio wykonywania przeznaczony pakietów źródeł danych i przygotowania danych"
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparation-operationalization"></a>Operationalization przygotowywania danych 

## <a name="operationalization-scenario"></a>Scenariusz operationalization

Poniżej przedstawiono różne scenariusze Operationalization przygotowywania danych, które może napotkać użytkownik.

### <a name="develop-your-model-based-on-training-data"></a>Tworzenie modelu na podstawie danych szkolenia

Załóżmy, że chcesz utworzyć i wdrożyć w czasie rzeczywistym oceniania usługi interfejsu API. Pierwszym krokiem jest opracowanie modelu wyników na podstawie niektórych zestawu danych szkoleniowych. Przygotowanie bazy danych importuje przykładowych danych szkoleniowych jako nowe źródło danych. Gdy dane są przygotowane, pakiet DataPrep zawiera kroki przygotowania. Przy użyciu konta eksperymenty uczenie maszynowe Azure, użytkownik może przejść modelu uczenia maszynowego generowania etykiety dla każdego danych wejściowych w danych szkoleniowych.

Jako funkcje dane muszą zostać zaktualizowane użytkownik powraca do pakietu DataPrep, aby przygotować dane w nowy sposób i zapisać te kroki. Ten proces iteracji generowania nowych funkcji i dostosowywanie ich modelu uczenia maszynowego będzie nadal występować, dopóki modelu dokładnie wyników ich danych testowych. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Wdróż model do usługi Azure Management modelu

Teraz masz dane klienta, które chcesz wynik w czasie rzeczywistym. Za pomocą usługi Azure Management modelu, należy wdrożyć ten model z poziomu interfejsu wiersza polecenia języka Python uczenie maszynowe Azure jako usługa. Punkt końcowy REST do odbierania danych klienta w czasie rzeczywistym przedstawia wdrożonej usługi i zwracanie odpowiadającego wyjściowych etykiety z trenowanego modelu.

Łatwość użycia, aby uzyskać punkt końcowy modelu akceptuje dane w formacie JSON. Dane wejściowe powinny być reprezentujący 2-wymiarową tablicą danych, które zostaną przekazane transformacji ReadJSONLiteral i przekonwertowane do źródła danych DataPrep ciągu JSON. Pakiet przygotowanie bazy danych utworzone podczas może fazy eksperymenty, a następnie można wykonać w odniesieniu do strumienia danych JSON. Wynikowa dataframe można następnie przekazać do trenowanego modelu wyników.

## <a name="read-json-literal-transformation"></a>Transformacja literałem JSON odczytu

### <a name="description"></a>Opis

Do celów operationalization przygotowanie bazy danych zawiera **ReadJsonLiteral** transformacji do wykonywania działań i generowanie Pandas lub Spark Dataframe. Tej transformacji jednoznacznie przyjmuje jako dane wejściowe istniejących danych przygotowywanie pakietu i źródła danych JSON. Tej transformacji jest dostępne za pośrednictwem interfejsu wiersza polecenia DataPrep języka Python.

### <a name="instructions"></a>Instrukcje

#### <a name="pythoncli"></a>PythonCLI

Z Azure Machine Learning Workbench, Otwórz interfejs wiersza polecenia (Plik > Otwórz wiersz polecenia).

W tym przykładzie TrainingPreparationSteps pakietu przygotowanie bazy danych służy do Przygotowanie danych i dodawana jest funkcja woluminu do poszczególnych danych wejściowych.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` ma następujące parametry opcjonalne
 - `dataflow_idx`: Określ indeks żądanego przepływu danych można wykonać w pakiecie
 - `secrets`: tajne magazynu słownika (klucz: secretId, wartość: hasła przechowywane)
 - `spark`: Podaj sesję spark dla wykonywania spark

#### <a name="input"></a>Dane wejściowe

Wprowadź 2-wymiarową tablicą ("tablicy tablic"). W języku Python dane wejściowe należy uzyskać listę list. Ponieważ JSON nie ma typu natywnego daty, wszystkie obiekty datetime.datetime Python zostaną poddane konwersji na ciągi datę w formacie ISO. Punkty końcowe REST dane wejściowe powinny być literałem JSON reprezentująca tablicę JSON 2-D.

#### <a name="output"></a>Dane wyjściowe

Pandas lub Spark DataFrame. Typ DataFrame jest określany przez platformę wybrane w konfiguracji uruchomienia (`.runconfig`). Wynikowa dataframe mogą być przekazywane jako dane wejściowe uczonego modelu do oceniania.
