---
title: Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w usłudze Application Insights
titleSuffix: Azure Machine Learning
description: Dodaj rejestrowanie do potoków szkolenia i oceniania partii i wyświetlaj zarejestrowane wyniki w usłudze Application Insights.
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776302"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Debugowanie i rozwiązywanie problemów z potokami uczenia maszynowego w usłudze Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Biblioteka [OpenCensus](https://opencensus.io/quickstart/python/) python może służyć do kierowania dzienników do usługi Application Insights ze skryptów. Agregowanie dzienników z potoku działa w jednym miejscu umożliwia tworzenie zapytań i diagnozowanie problemów. Za pomocą usługi Application Insights pozwoli na śledzenie dzienników w czasie i porównywanie dzienników potoku w różnych uruchomieniach.

Posiadanie dzienników w jednym miejscu zapewni historię wyjątków i komunikatów o błędach. Ponieważ usługa Application Insights integruje się z alertami platformy Azure, można również tworzyć alerty na podstawie zapytań usługi Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki, aby utworzyć obszar roboczy [usługi Azure Machine Learning](./how-to-manage-workspace.md) i utworzyć pierwszy [potok](./how-to-create-your-first-pipeline.md)
* [Skonfiguruj środowisko programistyczne,](./how-to-configure-environment.md) aby zainstalować zestaw SDK usługi Azure Machine Learning.
* Zainstaluj pakiet [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) lokalnie:
  ```python
  pip install opencensus-ext-azure
  ```
* Utwórz [wystąpienie usługi Application Insights](../azure-monitor/app/opencensus-python.md) (ten dokument zawiera również informacje o uzyskaniu ciągu połączenia dla zasobu)

## <a name="getting-started"></a>Wprowadzenie

Ta sekcja jest wprowadzenie specyficzne dla korzystania z OpenCensus z potoku usługi Azure Machine Learning. Aby uzyskać szczegółowy samouczek, zobacz [OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Dodaj ciąg PythonScriptStep do potoku usługi Azure ML. Skonfiguruj [konfigurację run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) z zależnością od opencensus-ext-azure. Skonfiguruj zmienną środowiskową. `APPLICATIONINSIGHTS_CONNECTION_STRING`

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Utwórz plik o nazwie `sample_step.py`. Zaimportuj klasę AzureLogHandler, aby rozsyłać dzienniki do usługi Application Insights. Należy również zaimportować bibliotekę rejestrowania języka Python.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Następnie dodaj AzureLogHandler do rejestratora python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Rejestrowanie za pomocą wymiarów niestandardowych
 
Domyślnie dzienniki przekazywane do usługi Application Insights nie będą miały wystarczającego kontekstu, aby prześledzić wstecz do uruchomienia lub eksperymentu. Aby dzienniki były możliwe do zasłaniania problemów, potrzebne są dodatkowe pola. 

Aby dodać te pola, można dodać wymiary niestandardowe, aby zapewnić kontekst do komunikatu dziennika. Jednym z przykładów jest, gdy ktoś chce wyświetlić dzienniki w wielu krokach w tym samym przebiegu potoku.

Wymiary niestandardowe tworzą słownik par klucz-wartość (przechowywany jako ciąg, ciąg). Słownik jest następnie wysyłany do usługi Application Insights i wyświetlany jako kolumna w wynikach kwerendy. Jego poszczególne wymiary mogą być używane jako [parametry zapytania](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Pomocny kontekst, aby uwzględnić

| Pole                          | Rozumowanie/przykład                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Może wysyłać zapytania do dzienników o tej samej parent_run_id, aby z czasem wyświetlać dzienniki dla wszystkich kroków, zamiast nurkować w poszczególnych krokach                                        |
| step_id                        | Można kwerendy dzienniki dla tych z tym samym step_id, aby zobaczyć, gdzie wystąpił problem z wąskim zakresem tylko do pojedynczego kroku                                                        |
| step_name                      | Można kwerendy dzienników, aby zobaczyć wydajność kroku w czasie. Pomaga również znaleźć step_id dla ostatnich biegów bez nurkowania w interfejsie portalu                                          |
| experiment_name                | Można kwerendy w dziennikach, aby zobaczyć wydajność eksperymentu w czasie. Pomaga również znaleźć parent_run_id lub step_id dla ostatnich biegów bez nurkowania w interfejsie portalu                   |
| run_url                 | Może podać link bezpośrednio z powrotem do biegu do badania. |

**Inne pomocne pola**

Te pola mogą wymagać dodatkowej instrumentacji kodu i nie są dostarczane przez kontekst uruchamiania.

| Pole                   | Rozumowanie/przykład                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Jeśli do wdrożenia jest używany dysk CIĄGŁEJ/CD, to pole może skorelować dzienniki z wersją kodu, która dostarczyła logikę kroku i potoku. To łącze może dodatkowo pomóc w diagnozowaniu problemów lub identyfikowaniu modeli o określonych cechach (wartości dziennika/metryki) |
| run_type                       | Można rozróżniać różne typy modeli lub szkolenia vs punktacji przebiegów |

### <a name="creating-a-custom-dimensions-dictionary"></a>Tworzenie słownika wymiarów niestandardowych

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>Zagadnienia dotyczące rejestrowania języka OpenCensus Python

Usługa OpenCensus AzureLogHandler służy do kierowania dzienników języka Python do usługi Application Insights. W rezultacie należy rozważyć niuanse rejestrowania Języka Python. Po utworzeniu rejestratora ma domyślny poziom dziennika i pokaże dzienniki większe lub równe do tego poziomu. Dobrym punktem odniesienia dla korzystania z funkcji rejestrowania Pythona jest [książka kucharska rejestrowania.](https://docs.python.org/3/howto/logging-cookbook.html)

Zmienna środowiskowa `APPLICATIONINSIGHTS_CONNECTION_STRING` jest potrzebna dla biblioteki OpenCensus. Zaleca się ustawienie tej zmiennej środowiskowej zamiast przekazywania go jako parametr potoku, aby uniknąć przekazywania wokół ciągów połączeń zwykłego tekstu.

## <a name="querying-logs-in-application-insights"></a>Wykonywanie zapytań o dzienniki w aplikacji Usługi Insights

Dzienniki kierowane do usługi Application Insights będą wyświetlane w obszarze "ślady" lub "wyjątki". Pamiętaj, aby dostosować przedział czasu, aby uwzględnić przebieg potoku.

![Wynik kwerendy aplikacji w aplikacji Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Wynik w usłudze Application Insights wyświetli komunikat dziennika i poziom, ścieżkę pliku i numer wiersza kodu. Będzie również wyświetlać wszystkie wymiary niestandardowe zawarte. Na tym obrazie słownik customDimensions pokazuje pary klucz/wartość z poprzedniego [przykładu kodu](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Dodatkowe przydatne zapytania

Niektóre z poniższych zapytań używają "customDimensions.Level". Te poziomy ważności odpowiadają poziomowi, z na jaki pierwotnie został wysłany dziennik języka Python. Aby uzyskać dodatkowe informacje na temat kwerend, zobacz [Kwerendy dziennika monitora platformy Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

| Przypadek użycia                                                               | Zapytanie                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Rejestrowanie wyników dla określonego wymiaru niestandardowego, na przykład "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Rejestrowanie wyników wszystkich przebiegów treningowych w ciągu ostatnich 7 dni                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Rejestrowanie wyników z ważnościPostępze z ostatnich 7 dni              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Liczba wyników dziennika z ważnościpostępem poziomu w ciągu ostatnich 7 dni     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Następne kroki

Po zalogowaniu w wystąpieniu usługi Application Insights można ich używać do ustawiania [alertów usługi Azure Monitor](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) na podstawie wyników kwerend.

Można również dodać wyniki z zapytań do [pulpitu nawigacyjnego platformy Azure,](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) aby uzyskać dodatkowe informacje.