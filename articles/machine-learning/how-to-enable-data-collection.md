---
title: Zbieranie danych na temat modeli produkcyjnych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zbierać dane modelu wejściowego usługi Azure Machine Learning w magazynie obiektów blob platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771397"
---
# <a name="collect-data-for-models-in-production"></a>Zbieranie danych dla modeli w produkcji

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Zestaw SDK do monitorowania usługi Azure Machine Learning zostanie wkrótce wycofany. Zestaw SDK jest nadal odpowiedni dla deweloperów, którzy obecnie używają zestawu SDK do monitorowania dryfu danych w modelach. Jednak dla nowych klientów zalecamy korzystanie z uproszczonego [monitorowania danych za pomocą usługi Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

W tym artykule pokazano, jak zbierać dane modelu wejściowego z usługi Azure Machine Learning. Pokazano również, jak wdrożyć dane wejściowe w klastrze usługi Azure Kubernetes (AKS) i przechowywać dane wyjściowe w magazynie obiektów blob platformy Azure.

Po włączeniu kolekcji zebrane dane pomagają:

* [Monitorowanie dryfów danych](how-to-monitor-data-drift.md) w miarę wprowadzania danych produkcyjnych do modelu.

* Podejmuj lepsze decyzje dotyczące tego, kiedy przeszkolić lub zoptymalizować model.

* Przeszkolić model z zebranych danych.

## <a name="what-is-collected-and-where-it-goes"></a>Co jest zbierane i dokąd zmierza

Można gromadzić następujące dane:

* Modelowanie danych wejściowych z usług sieci web wdrożonych w klastrze AKS. Dźwięk głosowy, obrazy i wideo *nie* są zbierane.
  
* Prognozy modelu przy użyciu danych wejściowych produkcji.

>[!NOTE]
> Preagregacja i wstępne obliczenia tych danych nie są obecnie częścią usługi zbierania.

Dane wyjściowe są zapisywane w magazynie obiektów Blob. Ponieważ dane są dodawane do magazynu obiektów Blob, można wybrać ulubione narzędzie do uruchomienia analizy.

Ścieżka do danych wyjściowych w obiekcie blob jest następująca:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> W wersjach zestawu SDK usługi Azure Machine Learning dla języka Python wcześniejszego `designation` niż `identifier`wersja 0.1.0a16 argument nosi nazwę . Jeśli kod został opracowany z wcześniejszą wersją, należy go odpowiednio zaktualizować.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree) przed rozpoczęciem.

- AzureMachine Learning workspace, katalog lokalny zawierający skrypty i zestaw SDK usługi Azure Machine Learning dla języka Python muszą być zainstalowane. Aby dowiedzieć się, jak je zainstalować, zobacz [Jak skonfigurować środowisko programistyczne](how-to-configure-environment.md).

- Potrzebujesz przeszkolonego modelu uczenia maszynowego, który ma zostać wdrożony w u usługi AKS. Jeśli nie masz modelu, zobacz [Train image klasyfikacji modelu](tutorial-train-models-with-aml.md) samouczka.

- Potrzebny jest klaster AKS. Aby uzyskać informacje na temat tworzenia i wdrażania do niego, zobacz [Jak wdrożyć i gdzie](how-to-deploy-and-where.md).

- [Skonfiguruj swoje środowisko](how-to-configure-environment.md) i zainstaluj [zestaw SDK do monitorowania usługi Azure Machine Learning](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Włączanie zbierania danych

Zbieranie danych można włączyć niezależnie od modelu wdrażanego za pośrednictwem usługi Azure Machine Learning lub innych narzędzi.

Aby włączyć zbieranie danych, należy:

1. Otwórz plik punktacji.

1. Dodaj [następujący kod](https://aka.ms/aml-monitoring-sdk) u góry pliku:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Zadeklaruj zmienne `init` zbierania danych w swojej funkcji:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* jest parametrem opcjonalnym. Nie trzeba go używać, jeśli model nie wymaga. Korzystanie z *CorrelationId* ułatwia mapowanie z innymi danymi, takimi jak *LoanNumber* lub *CustomerId*.
    
    Parametr *Identyfikator* jest później używany do tworzenia struktury folderów w obiekcie blob. Można go użyć do odróżnienia nieprzetworzonych danych od przetworzonych danych.

1. Dodaj następujące wiersze kodu `run(input_df)` do funkcji:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Zbieranie danych *nie* jest automatycznie ustawiona na **true** podczas wdrażania usługi w usłudze AKS. Zaktualizuj plik konfiguracyjny, tak jak w poniższym przykładzie:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Można również włączyć usługę Application Insights do monitorowania usługi, zmieniając tę konfigurację:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Aby utworzyć nowy obraz i wdrożyć model uczenia maszynowego, zobacz [Jak wdrożyć i gdzie](how-to-deploy-and-where.md).

Jeśli masz już usługę z zależnościami zainstalowanymi w pliku środowiska i pliku oceniania, włącz zbieranie danych, wykonując następujące kroki:

1. Przejdź do [usługi Azure Machine Learning](https://ml.azure.com).

1. Otwórz obszar roboczy.

1. Wybierz **wdrożeń** > **Wybierz usługę** > **Edytuj**.

   ![Edytowanie usługi](././media/how-to-enable-data-collection/EditService.PNG)

1. W **obszarze Ustawienia zaawansowane**wybierz pozycję Włącz zbieranie danych **modelu**.

    [![Wybieranie zbierania danych](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Można również wybrać **Włącz diagnostykę AppInsights,** aby śledzić kondycję usługi.

1. Wybierz **pozycję Aktualizuj,** aby zastosować zmiany.

## <a name="disable-data-collection"></a>Wyłączanie zbierania danych

Możesz przestać zbierać dane w dowolnym momencie. Użyj kodu języka Python lub usługi Azure Machine Learning, aby wyłączyć zbieranie danych.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opcja 1 — wyłącz zbieranie danych w usłudze Azure Machine Learning

1. Zaloguj się do [usługi Azure Machine Learning](https://ml.azure.com).

1. Otwórz obszar roboczy.

1. Wybierz **wdrożeń** > **Wybierz usługę** > **Edytuj**.

   [![Wybierz opcję Edytuj](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. W **obszarze Ustawienia zaawansowane**wyczyść pozycję Włącz zbieranie danych **modelu**.

    [![Wyczyść pole wyboru zbierania danych](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Wybierz **pozycję Aktualizuj,** aby zastosować zmianę.

Dostęp do tych ustawień można również uzyskać w obszarze roboczym w [usłudze Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Opcja 2 - Wyłącz zbieranie danych za pomocą języka Python

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Sprawdzanie poprawności i analizowanie danych

Można wybrać narzędzie preferowane do analizowania danych zebranych w magazynie obiektów Blob.

### <a name="quickly-access-your-blob-data"></a>Szybki dostęp do danych obiektu blob

1. Zaloguj się do [usługi Azure Machine Learning](https://ml.azure.com).

1. Otwórz obszar roboczy.

1. Wybierz **opcję Magazyn**.

    [![Wybierz opcję Magazyn](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Postępuj zgodnie ze ścieżką do danych wyjściowych obiektu blob z tą składnią:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analizowanie danych modelu przy użyciu usługi Power BI

1. Pobierz i otwórz [program Power BI Desktop](https://www.powerbi.com).

1. Wybierz **pozycję Pobierz dane** i wybierz pozycję Usługa Azure [**Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Konfiguracja obiektów blob usługi Power BI](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Dodaj nazwę konta magazynu i wprowadź klucz magazynu. Informacje te można znaleźć, wybierając**klawisze dostęp** **do ustawień** > w obiekcie blob.

1. Wybierz kontener **danych modelu** i wybierz pozycję **Edytuj**.

    [![Nawigator usługi Power BI](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. W edytorze zapytań kliknij kolumnę **Nazwa** i dodaj konto magazynu.

1. Wprowadź ścieżkę modelu do filtru. Jeśli chcesz przeglądać tylko pliki z określonego roku lub miesiąca, po prostu rozwiń ścieżkę filtru. Na przykład, aby zajrzeć tylko do danych z marca, użyj tej ścieżki filtru:

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<oznaczenie>/\<rok>/3

1. Filtruj dane, które są istotne dla Ciebie na podstawie wartości **nazwy.** Jeśli przechowywane prognoz i danych wejściowych, należy utworzyć kwerendę dla każdego.

1. Wybierz podwójne strzałki w dół obok nagłówka kolumny **Zawartość,** aby połączyć pliki.

    [![Zawartość usługi Power BI](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Kliknij przycisk **OK**. Dane są wstępnie ładowane.

    [![Łączenie plików w usłudze Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Wybierz **pozycję Zamknij i zastosuj**.

1. Jeśli dodano dane wejściowe i prognozowania, tabele są automatycznie uporządkowane przez **RequestId** wartości.

1. Zacznij tworzyć niestandardowe raporty na danych modelu.

### <a name="analyze-model-data-using-azure-databricks"></a>Analizowanie danych modelu przy użyciu usługi Azure Databricks

1. Utwórz [obszar roboczy usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Przejdź do obszaru roboczego Databricks.

1. W obszarze roboczym Databricks wybierz pozycję **Przekaż dane**.

    [![Wybieranie opcji Przekazywanie danych przez czajki danych](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Wybierz **pozycję Utwórz nową tabelę** i wybierz pozycję **Inne źródła** > danych**Azure Blob Storage** > **Create Table w notesie**.

    [![Tworzenie tabeli Databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Zaktualizuj lokalizację danych. Oto przykład:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Konfiguracja databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Postępuj zgodnie z instrukcjami na szablonie, aby wyświetlić i przeanalizować dane.
