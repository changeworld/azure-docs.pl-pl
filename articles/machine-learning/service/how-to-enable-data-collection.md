---
title: Włącz zbieranie danych dla modeli w środowisku produkcyjnym — usługa Azure Machine Learning
description: Dowiedz się, jak zbierać dane wejściowe modelu usługi Azure Machine Learning w usłudze Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 61776c5122abb73dd34ef2ad11f6a44ab22f6a6b
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238842"
---
# <a name="collect-data-for-models-in-production"></a>Zbieranie danych dla modeli w środowisku produkcyjnym

W tym artykule możesz dowiedzieć się, jak można zbierać dane w modelu wejściowym z usługi Azure Machine Learning, który został wdrożony do klastra Kubernetes usługi Azure (AKS) do usługi Azure Blob storage. 

Po włączeniu tych danych, które są zbierane pomoże Ci:
* Monitorowanie danych drifts zgodnie z danymi produkcyjnymi wprowadza modelu

* Podejmowanie lepszych decyzji o tym, kiedy ponowne szkolenie lub optymalizowanie modelu

* Ponowne szkolenie modelu przy użyciu zebranych danych

## <a name="what-is-collected-and-where-does-it-go"></a>Jakie informacje są zbierane i on gdzie?

Mogą być zbierane następujące dane:
* Model **wejściowych** danych z usług sieci web wdrożonych w klastrze Kubernetes usługi Azure (AKS) (głosowej, obrazów i filmów wideo są **nie** zbierane) 
  
* Model prognozy, przy użyciu danych wejściowych w środowisku produkcyjnym.

> [!Note]
> Wstępnego agregowania lub wstępnego obliczenia na tych danych należy nie są częścią usługi w tej chwili.   

Dane wyjściowe pobiera zapisane w obiekcie Blob platformy Azure. Ponieważ dane są pobierane dodane do obiektu Blob platformy Azure, możesz wybrać ulubionego narzędzia do uruchamiania analizy. 

Ścieżka do danych wyjściowych w obiekcie blob poniżej następującej składni:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Usługi Azure Machine Learning service obszar roboczy, zawierający skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane katalogu lokalnego. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.

- Model uczenia maszynowego uczonego wdrażanych na platformie Azure Kubernetes Service (AKS). Jeśli nie masz, zobacz [szkolenie modeli klasyfikacji obrazów](tutorial-train-models-with-aml.md) samouczka.

- [Klastra AKS](how-to-deploy-to-aks.md).

- Następujące zależności i zainstalowany moduł [w danym środowisku](how-to-configure-environment.md):
  + W systemie Linux:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + W systemie Windows:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Włączanie zbierania danych
Niezależnie od tego modelu wdrażana za pośrednictwem usługi Azure Machine Learning lub innych narzędzi można włączyć zbierania danych. 

Aby ją włączyć, należy:

1. Otwórz plik oceniania. 

1. Dodaj następujący kod w górnej części pliku:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Zadeklaruj swoje zmienne kolekcji danych w Twojej `init()` funkcji:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *Identyfikator korelacji* jest parametrem opcjonalnym, nie trzeba skonfigurować, jeśli model nie wymagają tego. Masz identyfikator korelacji w miejscu pomoc dotyczącą ułatwia mapowanie z innymi danymi. (Przykłady: LoanNumber, CustomerId, itp.)
    
    *Identyfikator* jest późniejsza używany do tworzenia struktury folderów w obiekt Blob, może służyć do podzielenia danych "pierwotne" i "przetworzona".

3.  Dodaj następujące wiersze kodu w celu `run(input_df)` funkcji:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Zbieranie danych jest **nie** automatycznie ustawienie **true** podczas wdrażania usługi w usłudze AKS, więc należy zaktualizować plik konfiguracji takich jak: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Usługi Application Insights do monitorowania usługi również może zostać włączona przez zmianę tej konfiguracji:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [Utwórz nowy obraz i wdróż usługę.](how-to-deploy-to-aks.md) 


Jeśli masz już usługę z zależnościami, zainstalowane w Twojej **plikiem środowiska** i **plik oceniania**, Włącz zbieranie danych przez:

1. Przejdź do [witryny Azure Portal](https://portal.azure.com).

1. Otwórz obszar roboczy.

1. Przejdź do **wdrożeń** -> **wybierz usługę** -> **Edytuj**.

   ![Edytowanie usługi](media/how-to-enable-data-collection/EditService.png)

1. W **Zaawansowane ustawienia**, usuń zaznaczenie opcji **zbierania danych modelu Włącz**. 

   ![Usuń zaznaczenie pola wyboru zbierania danych](media/how-to-enable-data-collection/CheckDataCollection.png)

   W tym oknie możesz również "Włącz diagnostykę usługi Appinsights" do śledzenia kondycji usługi.  

1. Wybierz **aktualizacji** do zastosowania zmiany.


## <a name="disable-data-collection"></a>Wyłączanie zbierania danych
Możesz zatrzymać zbieranie danych w dowolnym momencie. Użyj kodu w języku Python lub w portalu Azure, aby wyłączyć zbieranie danych.

+ Opcja 1 — Wyłącz w witrynie Azure portal: 
  1. Zaloguj się w [portalu Azure](https://portal.azure.com).

  1. Otwórz obszar roboczy.

  1. Przejdź do **wdrożeń** -> **wybierz usługę** -> **Edytuj**.

     ![Edytowanie usługi](media/how-to-enable-data-collection/EditService.png)

  1. W **Zaawansowane ustawienia**, usuń zaznaczenie opcji **zbierania danych modelu Włącz**. 

     ![Usuń zaznaczenie pola wyboru zbierania danych](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Wybierz **aktualizacji** do zastosowania zmiany.

* Opcja 2 — za pomocą języka Python wyłączyć zbieranie danych.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Przykład notesu

`00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb` Koncepcji w tym artykule pokazano, notesu.  

Pobierz ten notes:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]