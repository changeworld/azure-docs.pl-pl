---
title: Usługi Azure Machine Learning wdrażanie usługi sieci Web zarządzania modelu | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano kroki związane z wdrażania modelu uczenia maszynowego, za pomocą usługi Azure Machine Learning model zarządzania.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: f6bb6f3fbafe9b529d483af8edd55e16a35e703a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647664"
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Wdrażanie modelu uczenia maszynowego jako usługi sieci web

Zarządzanie modelami usługi Azure Machine Learning udostępnia interfejsy, które umożliwia wdrażanie modeli jako kontenerowych nimi usług internetowych opartych na platformie Docker. Można wdrażać modele utworzone za pomocą platform, takich jak Spark, Microsoft Cognitive Toolkit (CNTK), Keras, Tensorflow i Python. 

W tym dokumencie opisano kroki w celu wdrażania modeli jako usług sieci web przy użyciu interfejsu wiersza polecenia (CLI) Zarządzanie modelami w usłudze Azure Machine Learning.

## <a name="what-you-need-to-get-started"></a>Wymagane do rozpoczęcia pracy

Aby maksymalnie wykorzystać możliwości tego przewodnika, należy uprawień dostęp do subskrypcji platformy Azure lub grupy zasobów, którą można wdrożyć swoje modele, aby.
Interfejs wiersza polecenia jest wstępnie zainstalowany w aplikacji Azure Machine Learning Workbench i w [maszyny Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).  Może on też instalowany jako autonomiczny pakiet.

Ponadto środowisko modelu zarządzania kontem i wdrażania musi już być skonfigurowana.  Aby uzyskać więcej informacji na temat konfigurowania Twojego konta zarządzania modelami i środowisko lokalne i wdrożenie klastra, zobacz [konfiguracji zarządzania modelami](deployment-setup-configuration.md).

## <a name="deploying-web-services"></a>Wdrażanie usług sieci web
Korzystając z interfejsów wiersza polecenia, można wdrożyć usługi sieci web do uruchomienia na komputerze lokalnym lub w klastrze.

Firma Microsoft zaleca, począwszy od lokalnego wdrożenia. Weryfikowanie, model i kod pracować, to wdrażanie usługi sieci web do klastra do użytku w skali produkcyjnej.

Poniżej przedstawiono kroki wdrażania:
1. Użyj modelu uczenia maszynowego zapisane, przeszkolonych,
2. Utwórz schemat dla danych wejściowych usługi sieci web i danych wyjściowych
3. Tworzenie obrazu kontenera na platformie docker
4. Tworzenie i wdrażanie usługi sieci web

### <a name="1-save-your-model"></a>1. Zapisz model
Uruchom plik zapisane trenowanego modelu, na przykład mymodel.pkl. Rozszerzenie pliku zależy od platformy, używanej do nauczenia i zapisania modelu. 

Na przykład przy użyciu biblioteki Pickle języka Python i zapisania trenowanego modelu do pliku. Oto [przykład](http://scikit-learn.org/stable/modules/model_persistence.html) przy użyciu zestawu danych Iris:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Utwórz plik schema.json

Podczas generowania schematu jest opcjonalne, zaleca do definiowania żądania i dane wejściowe format zmiennych lepszą obsługę.

Utwórz schemat do automatycznego sprawdzania poprawności danych wejściowych i wyjściowych usługi sieci web. Interfejsów wiersza polecenia również używać schematu, aby utworzyć dokument struktury Swagger dla usługi sieci web.

Aby utworzyć schemat, należy zaimportować następujące biblioteki:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Następnie zdefiniuj zmienne wejściowe, takie jak Spark dataframe, Pandas dataframe lub NumPy tablicy. W poniższym przykładzie użyto tablicy Numpy:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
W poniższym przykładzie użyto Spark dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

W poniższym przykładzie użyto PANDAS dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

W poniższym przykładzie użyto ogólny format JSON:

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Utwórz plik score.py
Możesz podać w pliku score.py, który ładuje modelu i zwraca wyniki prognozowania, przy użyciu modelu.

Plik musi zawierać dwie funkcje: init i uruchom.

Dodaj następujący kod w górnej części pliku score.py, aby włączyć funkcję zbierania danych, która ułatwia zbieranie danych wejściowych i prognozowania modelu

```python
from azureml.datacollector import ModelDataCollector
```

Sprawdź [zbierania danych modelu](how-to-use-model-data-collection.md) sekcji, aby uzyskać więcej informacji na temat korzystania z tej funkcji.

#### <a name="init-function"></a>Init — funkcja
Aby załadować zapisany model, należy użyć funkcji init.

Przykład funkcji init prostego ładowania modelu:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Uruchamianie funkcji
Wykonywania funkcja korzysta z modelu i dane wejściowe, aby powrócić do prognozowania.

Przykład prostej uruchamianie funkcji przetwarzania danych wejściowych i zwrócenia wyniku prognozowania:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Zarejestruj model
Następujące polecenie służy do rejestrowania modelu utworzonego w kroku 1 powyżej

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Tworzenie manifestu
Następujące polecenie ułatwia tworzenie manifestu dla modelu

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
Wcześniej zarejestrowanego model można dodać do manifestu, za pomocą argumentu `--model-id` lub `-i` w poleceniu powyżej. Można określić wiele modeli z dodatkowymi -i argumentów.

### <a name="6-create-an-image"></a>6. Tworzenie obrazu 
Obraz można utworzyć za pomocą opcji Jeśli utworzono jego manifestu przed. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>Umożliwia także jedno polecenie do wykonania rejestracji, manifest i modelu tworzenia modelu. Użyj -h z usługą Utwórz polecenie, aby uzyskać więcej informacji.

Jako alternatywę, jest jednego polecenia, aby zarejestrować model oraz tworzenie manifestu i utworzyć obraz (ale nie utworzyć i wdrożyć usługę sieci web jeszcze) jako jeden krok w następujący sposób.

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Aby uzyskać szczegółowe informacje na temat parametrów polecenia wpisz -h na końcu polecenia, na przykład az ml obraz Utwórz -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Tworzenie i wdrażanie usługi sieci web
Wdróż usługę za pomocą następującego polecenia:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Aby wykonać wszystkie poprzednie kroki 4 umożliwia także jednego polecenia. Użyj -h z usługą Utwórz polecenie, aby uzyskać więcej informacji.

Jako alternatywę Brak jednego polecenia, aby zarejestrować model, utworzyć manifest, utworzyć obraz, także, tworzenie i wdrażanie usługi sieci Web, jako jeden krok w następujący sposób.

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8. Testowanie usługi
Aby uzyskać informacje na temat wywołania tej usługi, użyj następującego polecenia:

```
az ml service usage realtime -i <service id>
```

Wywołanie usługi przy użyciu funkcji wykonywania w wierszu polecenia:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

Poniższy przykład wywołuje próbkę irysów usługi sieci web:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy zostały przetestowane usługi sieci web, aby uruchomić lokalnie, można wdrożyć klaster do użycia na dużą skalę. Aby uzyskać więcej informacji na temat konfigurowania klastra dla wdrożenia usługi internetowej, zobacz [konfiguracji zarządzania w modelu](deployment-setup-configuration.md). 
