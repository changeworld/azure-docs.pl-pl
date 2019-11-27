---
title: Zbieranie danych z modeli produkcyjnych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zbierać dane wejściowe modelu usługi Azure Machine Learning w usłudze Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406450"
---
# <a name="collect-data-for-models-in-production"></a>Zbieranie danych dla modeli w środowisku produkcyjnym
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Ten zestaw SDK zostanie wkrótce wycofany. Ten zestaw SDK jest nadal odpowiedni dla deweloperów monitorujących dryfowanie danych w modelach, ale większość deweloperów powinien korzystać z uproszczonego [monitorowania danych z Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

W tym artykule można dowiedzieć się, jak zbierać dane modeli wejściowych z Azure Machine Learning wdrożonych w klastrze usługi Azure Kubernetes (AKS) w usłudze Azure Blob Storage. 

Po włączeniu tych danych, które są zbierane pomoże Ci:
* [Monitoruj dryfy danych](how-to-monitor-data-drift.md) w miarę wprowadzania danych produkcyjnych do modelu

* Podejmowanie lepszych decyzji o tym, kiedy ponowne szkolenie lub optymalizowanie modelu

* Ponowne szkolenie modelu przy użyciu zebranych danych

## <a name="what-is-collected-and-where-does-it-go"></a>Jakie informacje są zbierane i on gdzie?

Mogą być zbierane następujące dane:
* Dane **wejściowe** modelu z usług sieci Web wdrożonych w klastrze Kubernetes Azure (AKS) (głos, obrazy i wideo **nie** są zbierane) 
  
* Prognozowanie modeli przy użyciu danych wejściowych produkcji

> [!Note]
> Wstępnego agregowania lub wstępnego obliczenia na tych danych należy nie są częścią usługi w tej chwili.   

Dane wyjściowe pobiera zapisane w obiekcie Blob platformy Azure. Ponieważ dane są pobierane dodane do obiektu Blob platformy Azure, możesz wybrać ulubionego narzędzia do uruchamiania analizy. 

Ścieżka do danych wyjściowych w obiekcie blob poniżej następującej składni:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> W wersjach zestawu SDK przed `0.1.0a16` argument `designation` miał nazwę `identifier`. Jeśli kod został opracowany przy użyciu wcześniejszej wersji, należy odpowiednio zaktualizować.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj

- Azure Machine Learning obszar roboczy, katalog lokalny zawierający skrypty oraz zestaw Azure Machine Learning SDK dla języka Python. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposobu konfigurowania dokumentu środowiska programistycznego](how-to-configure-environment.md)

- Model uczenia maszynowego uczonego wdrażanych na platformie Azure Kubernetes Service (AKS). Jeśli go nie masz, zapoznaj się z samouczkiem dotyczącym [modelu klasyfikacji obrazów szkolenia](tutorial-train-models-with-aml.md)

- Klaster Azure Kubernetes Service. Aby uzyskać informacje na temat sposobu tworzenia i wdrażania na jednym serwerze, zobacz artykuł [jak wdrożyć i gdzie](how-to-deploy-and-where.md) dokument

- [Skonfiguruj środowisko](how-to-configure-environment.md) i zainstaluj [zestaw SDK monitorowania](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Włączanie zbierania danych
Zbieranie danych można włączyć niezależnie od modelu wdrażanego za pomocą Azure Machine Learning lub innych narzędzi. 

Aby ją włączyć, należy:

1. Otwórz plik oceniania

1. Dodaj [następujący kod](https://aka.ms/aml-monitoring-sdk) na początku pliku:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Zadeklaruj zmienne kolekcji danych w funkcji `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *Identyfikator korelacji* jest opcjonalnym parametrem, nie trzeba go konfigurować, jeśli model nie jest wymagany. Masz identyfikator korelacji w miejscu pomoc dotyczącą ułatwia mapowanie z innymi danymi. (Przykłady: LoanNumber, CustomerId, itp.)
    
    *Identyfikator* jest później używany do kompilowania struktury folderów w obiekcie blob, może służyć do dzielenia nieprzetworzonych danych na "RAW"

3.  Dodaj następujące wiersze kodu do funkcji `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Podczas wdrażania usługi w AKS **nie** jest automatycznie ustawiana **wartość true (prawda** ), więc należy zaktualizować plik konfiguracji, taki jak: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Usługi Application Insights do monitorowania usługi również może zostać włączona przez zmianę tej konfiguracji:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Aby utworzyć nowy obraz i wdrożyć usługę, zapoznaj się z artykułem [jak wdrożyć i gdzie](how-to-deploy-and-where.md) dokument


Jeśli masz już usługę z zależnościami zainstalowanymi w **pliku środowiska** i **plikiem oceniania**, Włącz zbieranie danych przez:

1. Przejdź do [Azure Machine Learning Studio](https://ml.azure.com)

1. Otwórz obszar roboczy

1. Przejdź do pozycji **wdrożenia** -> **wybierz pozycję Usługa** -> **Edytuj**

   ![Edytowanie usługi](media/how-to-enable-data-collection/EditService.PNG)

1. W obszarze **Ustawienia zaawansowane**wybierz pozycję **Włącz zbieranie danych modelu**

    [![Sprawdź zbieranie danych](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   W tym oknie można także wybrać opcję "Włącz diagnostykę Appinsights", aby śledzić kondycję usługi

1. Wybierz pozycję **Aktualizuj** , aby zastosować zmianę


## <a name="disable-data-collection"></a>Wyłączanie zbierania danych
Możesz zatrzymać zbieranie danych w dowolnym momencie. Użyj kodu Python lub Azure Machine Learning Studio, aby wyłączyć zbieranie danych.

+ Opcja 1 — wyłączanie w programie Azure Machine Learning Studio: 
  1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com)

  1. Otwórz obszar roboczy

  1. Przejdź do pozycji **wdrożenia** -> **wybierz pozycję Usługa** -> **Edytuj**

     [Opcja edycji ![](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. W obszarze **Ustawienia zaawansowane**Usuń zaznaczenie opcji **Włącz zbieranie danych modelu**

     [![usunąć zaznaczenia kolekcji danych](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Wybierz pozycję **Aktualizuj** , aby zastosować zmianę

  Możesz również uzyskać dostęp do tych ustawień w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com).

+ Opcja 2 — za pomocą języka Python wyłączyć zbieranie danych.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Sprawdzanie poprawności danych, a następnie analizowanie ich
Można wybrać dowolne narzędzie swoje preferencje, aby analizować dane zbierane do obiektu Blob platformy Azure.

Aby szybko uzyskać dostęp do danych z obiektu blob:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com)

1. Otwórz obszar roboczy
1. Kliknij pozycję **Magazyn**

    [Magazyn ![](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Wykonaj ścieżkę dane wyjściowe w obiekcie blob przy użyciu następującej składni:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analizowanie danych modelu za pomocą usługi Power BI

1. Pobierz i Otwórz [Power BI Desktop](https://www.powerbi.com)

1. Wybierz pozycję **Pobierz dane** i kliknij pozycję [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [Konfiguracja obiektu BLOB ![PBI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Dodaj nazwę konta magazynu, a następnie wprowadź klucz magazynu. Te informacje można znaleźć w **ustawieniach** obiektu blob, > > klucze dostępu

1. Wybierz kontener **modeldata** , a następnie kliknij pozycję **Edytuj** .

    [![Nawigator PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. W edytorze zapytań kliknij w kolumnie "Name", a następnie dodaj swoje konto magazynu 1. Ścieżka modelu do filtrów. Uwaga: Jeśli chcesz przeglądać tylko do plików z konkretnego roku lub miesiąca, po prostu Rozwiń ścieżkę filtru. Na przykład po prostu zapoznaj się z danymi w marcu:/modeldata/SubscriptionID >/ResourceGroupName >/WorkspaceName >/WebServiceName >/ModelName >/modelversion >/designation >/rok >/3

1. Odfiltruj dane, które są istotne dla Ciebie, na podstawie **nazwy**. Jeśli przechowujesz **prognozy** i **dane wejściowe**, musisz utworzyć zapytanie dla każdej

1. Kliknij podwójną strzałkę w dół w kolumnie **zawartość** , aby połączyć pliki

    [![zawartość PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kliknij przycisk OK, a dane zostaną wstępnie załadowanie

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Możesz teraz kliknąć przycisk **Zamknij i zastosować**

1.  Jeśli dodano dane wejściowe i przewidywania, tabele zostaną automatycznie skorelowane według identyfikator **żądania**

1. Zacznij tworzyć niestandardowe raporty dotyczące danych modelu


### <a name="analyzing-model-data-using-databricks"></a>Analizowanie danych modelu za pomocą usługi Databricks

1. Tworzenie [obszaru roboczego datakosteks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Przejdź do obszaru roboczego datakostki

1. W obszarze roboczym datakostki wybierz pozycję **Przekaż dane**

    [przekazywanie ![DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Tworzenie nowej tabeli i wybieranie **innych źródeł danych** — > Azure Blob Storage — > tworzenia tabeli w notesie

    [tabela ![DB](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Zaktualizuj lokalizację danych. Oto przykład:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DbSetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Postępuj zgodnie z instrukcjami szablonu, aby wyświetlać i analizować dane

## <a name="example-notebook"></a>Przykład notesu

W notesie [How-to-use-azureml/Deployment/Enable-Data-Collection-for-models-in-AKS/Enable-Data-Collection-for-models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) przedstawiono Koncepcje opisane w tym artykule.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
