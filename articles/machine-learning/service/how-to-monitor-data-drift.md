---
title: Wykrywać Dryft danych (wersja zapoznawcza) we wdrożeniach usługi AKS
titleSuffix: Azure Machine Learning service
description: Wykrywać Dryft danych w usłudze Azure Kubernetes Service wdrożone modelami w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806025"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Wykrywać Dryft danych (wersja zapoznawcza) w modelach wdrożonych do usługi Azure Kubernetes Service (AKS)

W tym artykule dowiesz się, jak monitorować dryfu danych między dataset szkoleń i wnioskowania danych wdrożony model. W kontekście uczenia maszynowego wytrenowane modele uczenia maszynowego mogą występować prognozowania pogorszenie wydajności z powodu odejściem od tego stanu. Za pomocą usługi Azure Machine Learning można monitorować dryfu danych, a usługa może wysłać wiadomość e-mail z alertem użytkownikom po odejściem od tego stanu wykryciu.

## <a name="what-is-data-drift"></a>Co to jest kilka danych?

Przesunięcie danych występuje, gdy obsługiwany w środowisku produkcyjnym do modelu danych różni się od danych użytych do nauczenia modelu. Jest jednym z głównych powodów gdzie spadku dokładności modelu wraz z upływem czasu, dane monitorowania w związku z tym dryfu pomaga wykrywać problemy z wydajnością w modelu. 

## <a name="what-can-i-monitor"></a>Co można monitorować?

Za pomocą usługi Azure Machine Learning możesz monitorować dane wejściowe do modelu, który został wdrożony w usłudze AKS i porównaj te dane do zestawu danych szkoleniowych dla modelu. W regularnych odstępach czasu, to dane wnioskowania [migawki i profilowane](how-to-explore-prepare-data.md), następnie obliczonego w odniesieniu do zestawu danych linii bazowej do tworzenia analizy dryfu danych który: 

+ Mierzy wielkość dryfu danych, nazywane współczynnik odejściem od tego stanu.
+ Środki danych odstępstw materiałów przekazywanych przez funkcję i informacją o tym, funkcji, które spowodowało dryfu danych.
+ Środki na odległość metryki. Obecnie są obliczane Wasserstein i odległość energii.
+ Mierzy dystrybucje funkcji. Obecnie jądra gęstość szacowania i histogramów.
+ Wysyłanie alertów danych odstępstw za pośrednictwem poczty e-mail.

> [!Note]
> Ta usługa jest w (wersja zapoznawcza) i jest ograniczona w opcji konfiguracji. Zobacz nasze [dokumentacji interfejsu API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) i [wersji](azure-machine-learning-release-notes.md) szczegółowe informacje i aktualizacje. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak dryfu danych jest monitorowany w usłudze Azure Machine Learning

Za pomocą usługi Azure Machine Learning, dryfu danych jest monitorowany za pośrednictwem zestawów danych lub wdrożeń. Aby monitorować dryfu danych, według planu bazowego zestawu danych — zwykle szkolenia zestawu danych dla modelu — jest określona. Drugi zestaw danych — zwykle modelu danych wejściowych zebrane z wdrożenia — są testowane w odniesieniu do bazowego zestawu danych. Oba zestawy danych są [profilowane](how-to-explore-prepare-data.md#explore-with-summary-statistics) i wprowadzania danych odstępstw usługi monitorowania. Model uczenia maszynowego jest uczony w celu wykrywania różnic między dwoma zestawami danych. Model wydajności jest konwertowana na współczynnik dryfu, czyli miary wielkości odchylenie między dwoma zestawami danych. Za pomocą [modelowania współdziałania](machine-learning-interpretability-explainability.md), są obliczane funkcje, które przyczyniają się do współczynnika odejściem od tego stanu. Z profilu zestaw danych jest śledzona informacje statystyczne na temat każdej funkcji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, przed rozpoczęciem należy utworzyć bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

- Obszar roboczy usługi Azure Machine Learning i Azure Machine Learning SDK dla język Python jest zainstalowany. Postępuj zgodnie z instrukcjami w [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md#sdk) wykonać następujące czynności:

    - Utwórz środowisko Miniconda
    - Zainstaluj aplikację Azure Machine Learning zestawu SDK dla języka Python
    - Tworzenie obszaru roboczego
    - Wpisywanie danych do pliku konfiguracji obszaru roboczego (aml_config/config.json).

- Zainstaluj dryfu danych zestawu SDK przy użyciu następującego polecenia:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Tworzenie [dataset](how-to-create-register-datasets.md) z modelu danych szkoleniowych.

- Określ zestaw danych szkoleniowych podczas [rejestrowanie](concept-model-management-and-deployment.md) modelu. Poniższy przykład demonstruje użycie `datasets` parametru, aby określić zestaw danych szkoleniowych:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Włącz zbieranie danych modelu](how-to-enable-data-collection.md) do zbierania danych z modelu wdrożenia usługi AKS i upewnij się, dane są zbierane w `modeldata` kontenera obiektów blob.

## <a name="configure-data-drift"></a>Konfigurowanie danych odejściem od tego stanu
Aby skonfigurować kilka dane do eksperymentu, zaimportuj zależności, jak pokazano w poniższym przykładzie w języku Python. 

W tym przykładzie przedstawiono konfigurowanie [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) obiektu:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Prześlij Uruchom DataDriftDetector

Za pomocą `DataDriftDetector` obiektu skonfigurowane, możesz przesłać [dryfu danych uruchom](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) w danym dniu dla modelu. W ramach przebiegu, Włącz alerty DataDriftDetector, ustawiając `drift_threshold` parametru. Jeśli [datadrift_coefficient](#metrics) powyżej znajduje się dany `drift_threshold`, zostanie wysłana wiadomość e-mail.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Wizualizuj metryki odejściem od tego stanu

<a name="metrics"></a>

Po przesłaniu swoje DataDriftDetector Uruchom jesteś w stanie wyświetlić metryki odejściem od tego stanu, które są zapisywane w każdej iteracji wykonywania zadania dryfu danych:


|Metryka|Opis|
--|--|
wasserstein_distance|Odległość statystyczne zdefiniowane dla jednowymiarowa dystrybucji wartości liczbowych.|
energy_distance|Odległość statystyczne zdefiniowane dla jednowymiarowa dystrybucji wartości liczbowych.|
datadrift_coefficient|Podobnie jak współczynnik korelacji firmy Matthew obliczane, ale te dane wyjściowe są liczba rzeczywista z zakresu od 0 do 1. W kontekście dryfu 0 oznacza nie dryfu i wartość 1 oznacza maksymalną odejściem od tego stanu.|
datadrift_contribution|Funkcja ważność przyczyniające się do funkcji.|

Istnieje wiele sposobów, aby wyświetlić metryki odejściem od tego stanu:

* Za pomocą widżetu Jupyter.
* Użyj [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funkcji na dowolnym `datadrift` przebiegu.
* Wyświetlać metryki w witrynie Azure portal w modelu

W poniższym przykładzie Python pokazuje, jak do wykreślenia metryki dryfu odpowiednie dane. Zwracane metryki służy do tworzenia niestandardowych wizualizacji:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zobacz dryfu danych wykryte przez usługi Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Harmonogram danych odejściem od tego stanu skanowania 

Po włączeniu wykrywanie dryfu danych DataDriftDetector jest uruchamiany z częstotliwością określony, według harmonogramu. Jeśli osiągnie datadrift_coefficient danego `drift_threshold`, zostanie wysłana wiadomość e-mail z każdego zaplanowanego uruchomienia. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurację wykrywacz dryfu danych są widoczne na stronie szczegółów modelu w witrynie Azure portal.

![Dane Dryft konfiguracji witryny Azure portal](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Wyświetl wyniki w interfejsie użytkownika z obszaru roboczego uczenia Maszynowego Azure

Aby wyświetlić wyniki w Interfejsie użytkownika usługi Azure ML obszaru roboczego, przejdź do strony modelu. Na karcie Szczegóły modelu danych Dryft konfiguracji jest wyświetlany. Na karcie "Dryfu danych (wersja zapoznawcza)" jest teraz dostępna, wizualizowanie metryk dryfu danych. 

![Przesunięcie danych o witrynie Azure portal](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Otrzymywanie alertów odejściem od tego stanu

Przez ustawienie współczynnika dryfu próg alertów i podając adres e-mail [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) alert e-mail są wysyłane automatycznie zawsze wtedy, gdy współczynnik dryfu przekracza wartość progową. 

Aby, można skonfigurować niestandardowe alerty i działania, wszystkie metryki dryfu dane są przechowywane w [usługi Application Insights](how-to-enable-app-insights.md) zasobów, która została utworzona wraz z obszaru roboczego usługi Azure Machine Learning. Skorzystaj z łącza w alercie e-mail, do zapytania usługi Application Insights.

![Alert E-mail dryfu danych](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Ponowne szkolenie modelu po odejściem od tego stanu

Gdy dryfu danych negatywnie wpływa na wydajność wdrożonego modelu, nadszedł czas na ponowne szkolenie modelu. Następujące [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) metoda daje poczucie początkowej co zmieniło się między zestawami danych szkoleniowych stare i nowe. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

Oparte na danych wyjściowych poprzedniego kodu, możesz chcieć Ponowne szkolenie modelu. Aby to zrobić, wykonaj następujące kroki.

* Zbadaj zebranych danych i przygotowania danych do nauczenia się nowego modelu.
* Podziel go na szkolenie i testowanie danych.
* Uczenie modelu ponownie przy użyciu nowych danych.
* Ocena wydajności modelu nowo wygenerowane.
* Jeśli wydajność jest lepsze niż model produkcji, należy wdrożyć nowy model.

## <a name="next-steps"></a>Następne kroki

* Pełny przykład przy użyciu danych odejściem od tego stanu można wyświetlić [danych usługi Azure ML odstępstw notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Ten notes Jupyter, który demonstruje sposób użycia [Otwórz zestaw danych usługi Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) do nauczenia modelu do prognozowania pogody, wdrażanie w usłudze AKS i monitorować dane odejściem od tego stanu. 

* Znacznie prosimy o pytania, komentarze lub sugestie przemieszcza się dryfu danych kierunku ogólnej dostępności. Użyj przycisku opinii produktu poniżej! 
