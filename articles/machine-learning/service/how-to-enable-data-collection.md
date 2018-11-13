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
ms.date: 11/08/2018
ms.openlocfilehash: bb3dca56583296bab42fe9804a32e0690ace5897
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578233"
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
>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK usługi Azure Machine Learning wersji 0.1.74

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Usługi Azure Machine Learning service obszar roboczy, zawierający skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane katalogu lokalnego. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.

- Model uczenia maszynowego uczonego wdrażanych na platformie Azure Kubernetes Service (AKS). Jeśli nie masz, zobacz [szkolenie modeli klasyfikacji obrazów](tutorial-train-models-with-aml.md) samouczka.

- [Klastra AKS](how-to-deploy-to-aks.md).

- [Konfigurowanie środowiska](how-to-configure-environment.md) i zainstaluj [monitorowania SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Włączanie zbierania danych
Niezależnie od tego modelu wdrażana za pośrednictwem usługi Azure Machine Learning lub innych narzędzi można włączyć zbierania danych. 

Aby ją włączyć, należy:

1. Otwórz plik oceniania. 

1. Dodaj [następujący kod](https://aka.ms/aml-monitoring-sdk) w górnej części pliku:

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

   ![Edytowanie usługi](media/how-to-enable-data-collection/EditService.PNG)

1. W **Zaawansowane ustawienia**, usuń zaznaczenie opcji **zbierania danych modelu Włącz**. 

    [![Sprawdź zbierania danych](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   W tym oknie możesz również "Włącz diagnostykę usługi Appinsights" do śledzenia kondycji usługi.  

1. Wybierz **aktualizacji** do zastosowania zmiany.


## <a name="disable-data-collection"></a>Wyłączanie zbierania danych
Możesz zatrzymać zbieranie danych w dowolnym momencie. Użyj kodu w języku Python lub w portalu Azure, aby wyłączyć zbieranie danych.

+ Opcja 1 — Wyłącz w witrynie Azure portal: 
  1. Zaloguj się w [portalu Azure](https://portal.azure.com).

  1. Otwórz obszar roboczy.

  1. Przejdź do **wdrożeń** -> **wybierz usługę** -> **Edytuj**.

    [![Edytowanie usługi](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. W **Zaawansowane ustawienia**, usuń zaznaczenie opcji **zbierania danych modelu Włącz**. 

    [![Usuń zaznaczenie pola wyboru zbierania danych](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Wybierz **aktualizacji** do zastosowania zmiany.

* Opcja 2 — za pomocą języka Python wyłączyć zbieranie danych.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Sprawdzanie poprawności danych, a następnie analizowanie ich
Można wybrać dowolne narzędzie swoje preferencje, aby analizować dane zbierane do obiektu Blob platformy Azure. 

Aby szybko uzyskać dostęp do danych z obiektu blob:
1. Zaloguj się w [portalu Azure](https://portal.azure.com).

1. Otwórz obszar roboczy.
1. Kliknij pozycję **magazynu**.

    [![Magazyn](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Wykonaj ścieżkę dane wyjściowe w obiekcie blob przy użyciu następującej składni:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analizowanie danych modelu za pomocą usługi Power BI

1. Pobierz i Otwórz [pulpit nawigacyjny usługi PowerBi](http://www.powerbi.com)

1. Wybierz **Pobierz dane** i kliknij pozycję [ **usługi Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Instalator usługi PBI obiektów Blob](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Dodaj nazwę konta magazynu, a następnie wprowadź klucz magazynu. Te informacje można znaleźć w swojej blob **ustawienia** >> klucze dostępu. 

1. Wybierz kontener **modeldata** i kliknij pozycję **Edytuj**. 

    [![Nawigator PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. W edytorze zapytań kliknij w kolumnie "Name", a następnie dodaj swoje konto magazynu 1. Ścieżka modelu do filtrów. Uwaga: Jeśli chcesz przeglądać tylko do plików z konkretnego roku lub miesiąca, po prostu Rozwiń ścieżkę filtru. Na przykład, po prostu możliwość przejrzenia danych marca: / modeldata/subscriptionid > / resourcegroupname > / workspacename > / webservicename > / modelname > / modelversion > / Identyfikator > / rok > / 3

1. Filtruj dane, które są istotne dla Ciebie na podstawie **nazwa**. Jeśli przechowujesz **prognozy** i **dane wejściowe** należy utworzyć kwerendę każdego.

1. Kliknij podwójną strzałkę specjalnie **zawartości** kolumny, aby połączyć pliki. 

    [![Zawartość usługi PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kliknij przycisk OK, a dane zostaną wstępnego ładowania.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Możesz teraz kliknąć **Zamknij i Zastosuj** .

1.  Jeśli dodano danych wejściowych i prognozowania tabel będzie automatycznie skorelować przez **RequestId**.

1. Rozpocznij tworzenie niestandardowych raportów na podstawie danych modelu.


### <a name="analyzing-model-data-using-databricks"></a>Analizowanie danych modelu za pomocą usługi Databricks

1. Tworzenie [obszaru roboczego usługi Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Przejdź do obszaru roboczego usługi Databricks. 

1. Wybierz obszar roboczy usługi databricks **przekazywanie danych**.

    [![Przekazywanie bazy danych](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Utwórz nową tabelę i wybierz **innych źródeł danych** -> Azure Blob Storage -> Create Table w notesie.

    [![Tabeli bazy danych](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Zaktualizuj lokalizację danych. Oto przykład:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Program DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Postępuj zgodnie z instrukcjami na tym szablonie, aby można było wyświetlać i analizować dane. 

## <a name="example-notebook"></a>Przykład notesu

[00. Started/12.enable-data-collection-for-models-in-aks.ipynb wprowadzenie](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) koncepcji w tym artykule pokazano, notesu.  

Pobierz ten notes:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
