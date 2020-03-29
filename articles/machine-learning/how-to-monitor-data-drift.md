---
title: Wykrywanie dryfu danych we wdrożeniach usługi AKS
titleSuffix: Azure Machine Learning
description: Wykrywanie dryfu danych (w wersji zapoznawczej) w usłudze Azure Kubernetes wdrożonych modeli w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537008"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Wykrywanie dryfu danych (w wersji zapoznawczej) w modelach wdrożonych w usłudze Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule dowiesz się, jak monitorować dryf danych między zestawem danych szkolenia i danych wnioskowania wdrożonego modelu. W kontekście uczenia maszynowego przeszkolonych modeli uczenia maszynowego może wystąpić obniżona wydajność przewidywania z powodu dryfu. Za pomocą usługi Azure Machine Learning można monitorować dryft danych, a usługa może wysyłać alert e-mail do Ciebie po wykryciu dryfu.

## <a name="what-is-data-drift"></a>Co to jest dryft danych?

W kontekście uczenia maszynowego dryft danych jest zmiana danych wejściowych modelu, która prowadzi do obniżenia wydajności modelu. Jest to jeden z głównych powodów, dla których dokładność modelu pogarsza się w czasie, dzięki czemu monitorowanie dryfu danych pomaga wykryć problemy z wydajnością modelu. 

## <a name="what-can-i-monitor"></a>Co mogę monitorować?

Za pomocą usługi Azure Machine Learning można monitorować dane wejściowe do modelu wdrożonego w usłudze AKS i porównać te dane z zestawem danych szkoleniowych dla modelu. W regularnych odstępach czasu dane wnioskowania są [migawką i profilowane,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)a następnie obliczane względem planu bazowego zestawu danych w celu uzyskania analizy dryfu danych, która: 

+ Mierzy wielkość dryfu danych, zwany współczynnikiem dryfu.
+ Mierzy udział dryfu danych według funkcji, wskazując, które funkcje spowodowały dryf danych.
+ Mierzy metryki odległości. Obecnie wasserstein i odległość energii są obliczane.
+ Mierzy rozkłady funkcji. Obecnie oszacowanie gęstości jądra i histogramy.
+ Wysyłaj alerty do dryfu danych pocztą e-mail.

> [!Note]
> Ta usługa jest w (wersja zapoznawcza) i ograniczona w opcjach konfiguracji. Szczegółowe informacje i aktualizacje można znaleźć w dokumentacji [interfejsu API](https://docs.microsoft.com/python/api/azureml-datadrift/) i [informacjach o wersji.](azure-machine-learning-release-notes.md) 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Jak dryf danych jest monitorowany w usłudze Azure Machine Learning

Za pomocą usługi Azure Machine Learning dryf danych jest monitorowany za pośrednictwem zestawów danych lub wdrożeń. Aby monitorować dryft danych, określa się zestaw danych linii bazowej — zwykle zestaw danych szkoleniowych dla modelu. Drugi zestaw danych — zwykle model danych wejściowych zebranych z wdrożenia — jest testowany na podstawie zestawu danych linii bazowej. Oba zestawy danych są profilowane i wprowadzane do usługi monitorowania dryfu danych. Model uczenia maszynowego jest szkolony do wykrywania różnic między dwoma zestawami danych. Wydajność modelu jest konwertowana na współczynnik dryfu, który mierzy wielkość dryfu między dwoma zestawami danych. Za pomocą [interpretowalności modelu](how-to-machine-learning-interpretability.md)obliczane są operacje, które przyczyniają się do współczynnika dryfu. Z profilu zestawu danych są śledzone informacje statystyczne o każdej funkcji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, utwórz bezpłatne konto przed rozpoczęciem. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- Zainstalowano zestaw SDK usługi Azure Machine Learning dla języka Python. Skorzystaj z instrukcji w [usłudze Azure Machine Learning SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) aby wykonać następujące czynności:

    - Tworzenie środowiska Miniconda
    - Instalowanie pakietu SDK usługi Azure Machine Learning dla języka Python

- [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

- [Plik konfiguracji](how-to-configure-environment.md#workspace)obszaru roboczego .

- Zainstaluj zestaw SDK dryfującej danych za pomocą następującego polecenia:

    ```shell
    pip install azureml-datadrift
    ```

- Utwórz [zestaw danych](how-to-create-register-datasets.md) na podstawie danych szkoleniowych modelu.

- Określ zestaw danych szkolenia podczas [rejestrowania](concept-model-management-and-deployment.md) modelu. Poniższy przykład pokazuje `datasets` przy użyciu parametru, aby określić zestaw danych szkolenia:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Włącz zbieranie danych modelu](how-to-enable-data-collection.md) do zbierania danych z wdrożenia usługi AKS `modeldata` modelu i potwierdzić, że dane są zbierane w kontenerze obiektu blob.

## <a name="configure-data-drift"></a>Konfigurowanie dryfu danych
Aby skonfigurować dryft danych dla eksperymentu, zaimportuj zależności, jak pokazano w poniższym przykładzie języka Python. 

W tym przykładzie [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) pokazano konfigurowanie obiektu:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Przesyłanie uruchomienia DataDriftDetector

Po `DataDriftDetector` skonfigurowaniu obiektu można przesłać [przebieg dryfu danych](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) w określonym dniu dla modelu. W ramach uruchomienia włącz alerty DataDriftDetector, `drift_threshold` ustawiając parametr. Jeśli [datadrift_coefficient](#visualize-drift-metrics) jest powyżej podanej, `drift_threshold`wysyłany jest e-mail.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Wizualizuj metryki dryfu

<a name="metrics"></a>

Po przesłaniu uruchomienia DataDriftDetector, można zobaczyć metryki dryfu, które są zapisywane w każdej iteracji uruchamiania dla zadania dryfu danych:


|Metryka|Opis|
--|--|
wasserstein_distance|Statystyczna odległość zdefiniowana dla jednowymiarowego rozkładu liczbowego.|
energy_distance|Statystyczna odległość zdefiniowana dla jednowymiarowego rozkładu liczbowego.|
datadrift_coefficient|Obliczony podobnie jako współczynnik korelacji Mateusza, ale to wyjście jest rzeczywistą liczbą od 0 do 1. W kontekście dryfu, 0 oznacza brak dryfu, a 1 oznacza maksymalny dryf.|
datadrift_contribution|Znaczenie funkcji przyczyniających się do dryfowania.|

Istnieje wiele sposobów wyświetlania metryk dryfu:

* Użyj `RunDetails` [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Użyj [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funkcji na `datadrift` dowolnym obiekcie uruchamiania.
* Wyświetl metryki z sekcji **Modele** obszaru roboczego w [studiu usługi Azure Machine Learning.](https://ml.azure.com)

W poniższym przykładzie języka Python pokazano, jak wykreślić odpowiednie dane dryf metryki. Zwrócone metryki można użyć do tworzenia wizualizacji niestandardowych:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zobacz dryft danych wykryty przez usługę Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Planowanie skanowania dryfu danych 

Po włączeniu wykrywania dryfu danych datadriftDetector jest uruchamiany z określoną, zaplanowaną częstotliwością. Jeśli datadrift_coefficient osiągnie dany `drift_threshold`adres e-mail, z każdym zaplanowanym przebiegiem zostanie wysłana wiadomość e-mail. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurację detektora dryfu danych można zobaczyć w obszarze **Modele** na karcie **Szczegóły** w obszarze roboczym w [studiu usługi Azure Machine Learning.](https://ml.azure.com)

[![Dryf danych w studio usługi Azure Machine Learning](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Wyświetlanie wyników w studio usługi Azure Machine Learning

Aby wyświetlić wyniki w obszarze roboczym w [usłudze Azure Machine Learning studio,](https://ml.azure.com)przejdź do strony modelu. Na karcie szczegóły modelu wyświetlana jest konfiguracja dryfu danych. Karta **Dryf danych** jest teraz dostępna wizualizująca metryki dryfu danych. 

[![Dryf danych w studio usługi Azure Machine Learning](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Otrzymywanie alertów o dryfowaniu

Ustawiając próg alertów współczynnika dryfu i podając adres e-mail, alert e-mail [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) jest automatycznie wysyłany, gdy współczynnik dryfu przekracza próg. 

Aby skonfigurować niestandardowe alerty i akcje, wszystkie metryki dryfu danych są przechowywane w zasobie [usługi Application Insights,](how-to-enable-app-insights.md) który został utworzony wraz z obszarem roboczym usługi Azure Machine Learning. Możesz kierować łącze w alert e-mail do kwerendy usługi Application Insights.

![Alert e-mail driftu danych](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Przekwalifikowanie modelu po dryfowaniu

Gdy dryf danych negatywnie wpływa na wydajność wdrożonego modelu, nadszedł czas, aby przekwalifikować model. Aby to zrobić, wykonaj następujące czynności.

* Zbadaj zebrane dane i przygotuj dane, aby wyszkolić nowy model.
* Podziel go na dane pociągu/testu.
* Trenuj model ponownie przy użyciu nowych danych.
* Oceń wydajność nowo wygenerowanego modelu.
* Wdrażanie nowego modelu, jeśli wydajność jest lepsza niż model produkcyjny.

## <a name="next-steps"></a>Następne kroki

* Pełny przykład użycia dryfu danych można znaleźć w [notesie dryfu danych usługi Azure ML.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb) Ten notes Jupyter pokazuje przy użyciu [zestawu danych Azure Open](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) do szkolenia modelu do przewidywania pogody, wdrożyć go w usłudze AKS i monitorować dryf danych. 

* Wykrywanie dryfu danych za pomocą [monitorów zestawu danych](how-to-monitor-datasets.md).

* Będziemy bardzo wdzięczni za twoje pytania, komentarze lub sugestie, ponieważ dryf danych przesuwa się w kierunku ogólnej dostępności. Użyj przycisku opinii o produkcie poniżej! 
