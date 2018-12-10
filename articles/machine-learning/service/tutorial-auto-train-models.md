---
title: 'Samouczek nr 2: trenowanie modelu regresji za pomocą zautomatyzowanego uczenia maszynowego — usługa Azure Machine Learning'
description: Dowiedz się, jak wygenerować model uczenia maszynowego przy użyciu zautomatyzowanego uczenia maszynowego.  Korzystając z usługi Azure Machine Learning, można w sposób zautomatyzowany wykonywać wstępne przetwarzanie danych, wybierać algorytmy i hiperparametry. Końcowy model jest następnie wdrażany za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 0c4b9c31c4ae8a6a7a7044887c9af051966c745e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879535"
---
# <a name="tutorial-2-train-a-regression-model-with-automated-machine-learning"></a>Samouczek nr 2: trenowanie modelu regresji za pomocą zautomatyzowanego uczenia maszynowego

Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W poprzednim samouczku [przygotowano dane dotyczące taksówek w Nowym Jorku na potrzeby modelowania regresji](tutorial-data-prep.md).

Teraz możesz rozpocząć tworzenie modelu za pomocą usługi Azure Machine Learning. W tej części samouczka użyjesz przygotowanych danych i automatycznie wygenerujesz modelu regresji do prognozowania cen taryfy taksówek. Korzystając z możliwości automatycznego uczenia maszynowego usługi, zdefiniujesz cele i ograniczenia uczenia maszynowego, uruchomisz zautomatyzowany proces uczenia maszynowego, a następnie zezwolisz na wybór algorytmu i strojenie hiperparametryczne. Technika zautomatyzowanego uczenia maszynowego wykonuje iterację wielu kombinacji algorytmów i hiperparametrów aż do momentu znalezienia najlepszego modelu na podstawie podanego kryterium.

![diagram przepływu](./media/tutorial-auto-train-models/flow2.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska Python i importowanie pakietów SDK
> * Konfigurowanie obszaru roboczego usługi Azure Machine Learning
> * Automatyczne trenowanie modelu regresji
> * Lokalne uruchamianie modelu z użyciem niestandardowych parametrów
> * Eksplorowanie wyników
> * Rejestrowanie najlepszego modelu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu Azure Machine Learning SDK w wersji 1.0.0


## <a name="prerequisites"></a>Wymagania wstępne

> * [Ukończony samouczek przygotowywania danych](tutorial-data-prep.md).
> * Skonfigurowane środowisko automatycznego uczenia maszynowego, na przykład Azure Notebooks, lokalne środowisko Python lub środowisko Data Science Virtual Machine. [Skonfigurowane](samples-notebooks.md) zautomatyzowane uczenie maszynowe.

## <a name="get-the-notebook"></a>Pobieranie notesu

Dla Twojej wygody ten samouczek jest dostępny jako [notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb). Uruchom notes `regression-part2-automated-ml.ipynb` w usłudze Azure Notebooks lub na własnym serwerze notesów Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Importowanie pakietów
Zaimportuj pakiety języka Python, które są potrzebne w tym samouczku.


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging

```

## <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. Element `Workspace` jest klasą, która akceptuje Twoją subskrypcję platformy Azure oraz informacje o zasobach i tworzy zasób w chmurze do monitorowania oraz śledzenia przebiegów modeli. `Workspace.from_config()` odczytuje plik **aml_config/config.json** i ładuje szczegóły do obiektu o nazwie `ws`.  Obiekt `ws` jest używany w kodzie w tym samouczku.

Po utworzeniu obiektu obszaru roboczego określ nazwę eksperymentu i utwórz, a następnie zarejestruj katalog lokalny w obszarze roboczym. Historia wszystkich przebiegów jest rejestrowana w ramach określonego eksperymentu oraz w witrynie [Azure Portal](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

import os

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Eksplorowanie danych

Wykorzystaj obiekt przepływu danych utworzony w poprzednim samouczku. Otwórz i wykonaj przepływ danych, a następnie przejrzyj wyniki.


```python
import azureml.dataprep as dprep
package_saved = dprep.Package.open(".\dflow")
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
      <th>Min.</th>
      <th>Maks.</th>
      <th>Liczba</th>
      <th>Liczba brakujących</th>
      <th>Liczba niebrakujących</th>
      <th>Odsetek brakujących</th>
      <th>Liczba błędów</th>
      <th>Liczba pustych</th>
      <th>Kwantyl 0,1%</th>
      <th>Kwantyl 1%</th>
      <th>Kwantyl 5%</th>
      <th>Kwantyl 25%</th>
      <th>Kwantyl 50%</th>
      <th>Kwantyl 75%</th>
      <th>Kwantyl 95%</th>
      <th>Kwantyl 99%</th>
      <th>Kwantyl 99,9%</th>
      <th>Średnia</th>
      <th>Odchylenie standardowe</th>
      <th>Wariancja</th>
      <th>Skośność</th>
      <th>Kurtoza</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Piątek</td>
      <td>Środa</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.57523</td>
      <td>3</td>
      <td>9,91106</td>
      <td>15.9327</td>
      <td>19</td>
      <td>22.0225</td>
      <td>23</td>
      <td>23</td>
      <td>14.2326</td>
      <td>6.34926</td>
      <td>40.3131</td>
      <td>-0.693335</td>
      <td>-0.459336</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.32313</td>
      <td>4.92308</td>
      <td>14.2214</td>
      <td>29,5244</td>
      <td>44.6436</td>
      <td>56.3767</td>
      <td>58.9798</td>
      <td>59</td>
      <td>29,4635</td>
      <td>17.4396</td>
      <td>304.14</td>
      <td>0.00440324</td>
      <td>-1.20458</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99286</td>
      <td>4.91954</td>
      <td>14.6121</td>
      <td>29,9239</td>
      <td>44.5221</td>
      <td>56.6792</td>
      <td>59</td>
      <td>59</td>
      <td>29,6225</td>
      <td>17.3868</td>
      <td>302.302</td>
      <td>-0.0227466</td>
      <td>-1.19409</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Piątek</td>
      <td>Środa</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.23217</td>
      <td>2.93333</td>
      <td>9,92334</td>
      <td>15.9135</td>
      <td>19</td>
      <td>22.2739</td>
      <td>23</td>
      <td>23</td>
      <td>14.1815</td>
      <td>6.45578</td>
      <td>41.677</td>
      <td>-0.691001</td>
      <td>-0.500215</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.1064</td>
      <td>5</td>
      <td>14.2051</td>
      <td>29,079</td>
      <td>44.2937</td>
      <td>56.6338</td>
      <td>58.9984</td>
      <td>59</td>
      <td>29,353</td>
      <td>17.4241</td>
      <td>303.598</td>
      <td>0.0142562</td>
      <td>-1.21531</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.03373</td>
      <td>5</td>
      <td>14.7471</td>
      <td>29,598</td>
      <td>45.3216</td>
      <td>56.1044</td>
      <td>58.9728</td>
      <td>59</td>
      <td>29,7923</td>
      <td>17.481</td>
      <td>305.585</td>
      <td>-0.0281313</td>
      <td>-1.21965</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Nie</td>
      <td>Tak</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0782</td>
      <td>-73.7365</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0763</td>
      <td>-73.9625</td>
      <td>-73.9629</td>
      <td>-73.949</td>
      <td>-73.9279</td>
      <td>-73.8667</td>
      <td>-73.8304</td>
      <td>-73.8232</td>
      <td>-73.7698</td>
      <td>-73.9139</td>
      <td>0.0487111</td>
      <td>0.00237277</td>
      <td>0.402697</td>
      <td>-0.613516</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.6329</td>
      <td>40.7131</td>
      <td>40.7116</td>
      <td>40.7214</td>
      <td>40.7581</td>
      <td>40.8051</td>
      <td>40.8489</td>
      <td>40.8676</td>
      <td>40.8777</td>
      <td>40.7652</td>
      <td>0.0483485</td>
      <td>0.00233758</td>
      <td>0.228088</td>
      <td>-0.598862</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0788</td>
      <td>-73.9856</td>
      <td>-73.9858</td>
      <td>-73.959</td>
      <td>-73.9367</td>
      <td>-73.8848</td>
      <td>-73.8155</td>
      <td>-73.7767</td>
      <td>-73.7335</td>
      <td>-73.9207</td>
      <td>0.055961</td>
      <td>0.00313163</td>
      <td>0.648649</td>
      <td>0.0229141</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5977</td>
      <td>40.6954</td>
      <td>40.6951</td>
      <td>40.7275</td>
      <td>40.7582</td>
      <td>40.7884</td>
      <td>40.8504</td>
      <td>40.868</td>
      <td>40.8786</td>
      <td>40.7595</td>
      <td>0.0504621</td>
      <td>0.00254642</td>
      <td>0.0484179</td>
      <td>-0.0368799</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.32979</td>
      <td>1.79978</td>
      <td>3.2392</td>
      <td>0.834099</td>
      <td>-1.11111</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>444</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.01808</td>
      <td>3.0125</td>
      <td>5.91545</td>
      <td>9,49055</td>
      <td>16.5816</td>
      <td>33.5638</td>
      <td>51.9924</td>
      <td>81.1368</td>
      <td>12.9112</td>
      <td>11.6447</td>
      <td>135.599</td>
      <td>8.6842</td>
      <td>269,818</td>
    </tr>
  </tbody>
</table>

Należy przygotować dane do eksperymentu, dodając kolumny do elementu `dflow_x`, aby stały się funkcjami w procesie tworzenia naszego modelu. Element `dflow_y` należy zdefiniować w taki sposób, aby stał się naszą wartością prognozowania — cost (koszt).

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday', 'dropoff_latitude', 'dropoff_longitude','pickup_hour','pickup_longitude','pickup_latitude','passengers'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-data-into-train-and-test-sets"></a>Podział danych na zestawy treningowe i testowe

Teraz podzielisz dane na zestawy treningowe i testowe za pomocą funkcji `train_test_split` z biblioteki `sklearn`. Ta funkcja segreguje dane między zestaw danych x (cechy) na potrzeby trenowania modelu i zestaw danych y (wartości do prognozowania) na potrzeby testowania. Parametr `test_size` określa procent danych przydzielanych do testowania. Parametr `random_state` ustawia inicjator na generator liczb losowych, dzięki czemu podział na dane trenowane i testowane jest zawsze deterministyczny.

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=123)
# flatten y_train to 1d array
y_train.values.flatten()
```

Teraz masz niezbędne pakiety i dane gotowe do automatycznego szkolenia modelu.

## <a name="automatically-train-a-model"></a>Automatyczne trenowanie modelu

Aby automatycznie wytrenować model, wykonaj następujące czynności:
1. Zdefiniuj ustawienia przebiegu eksperymentu.
1. Prześlij eksperyment do strojenia modelu.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Definiowanie ustawień automatycznego generowania i dostrajania

Zdefiniuj parametry eksperymentu i ustawienia modelu na potrzeby automatycznego generowania i dostrajania. Wyświetl pełną listę [ustawień](how-to-configure-auto-train.md).


|Właściwość| Wartość w ramach tego samouczka |Opis|
|----|----|---|
|**iteration_timeout_minutes**|10|Limit czasu w minutach dla każdej iteracji|
|**iterations**|30|Liczba iteracji. W przypadku każdej iteracji model jest szkolony za pomocą danych z określonego potoku|
|**primary_metric**| spearman_correlation | Metryka, który ma być optymalizowana.|
|**preprocess**| True | Wartość True umożliwia wykonanie w ramach eksperymentu przetwarzania wstępnego danych wejściowych.|
|**verbosity**| logging.INFO | Steruje poziomem rejestrowania.|
|**n_cross_validationss**|5|Liczba podziałów krzyżowego sprawdzania poprawności



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```


```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Trenowanie automatycznego modelu regresji

Uruchom eksperyment lokalnie. Przekaż zdefiniowany obiekt `automated_ml_config` do eksperymentu i ustaw dane wyjściowe na wartość `true`, aby wyświetlać postęp podczas eksperymentu.


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

    Parent Run ID: AutoML_83117da4-07e3-473a-b83e-99471bfa9e09
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************
    
     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:21       0.6498    0.6498
             1   MinMaxScaler GradientBoosting                  0:00:22       0.6624    0.6624
             2   StandardScalerWrapper KNN                      0:00:18       0.7267    0.7267
             3   StandardScalerWrapper GradientBoosting         0:00:18       0.5003    0.7267
             4    Ensemble                                      0:00:38       0.6659    0.7267
    

## <a name="explore-the-results"></a>Eksplorowanie wyników

Zapoznaj się z wynikami automatycznego trenowania, korzystając z widżetu Jupyter lub sprawdzając historię eksperymentu.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Opcja 1. Dodawanie widżetu Jupyter w celu wyświetlania wyników

Jeśli używasz notesu Juypter, użyj widżetu notesu Jupyter, aby wyświetlić wykres i tabelę ze wszystkimi wynikami.


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Szczegóły przebiegu w widżecie Jupyter](./media/tutorial-auto-train-models/jup-widget-auto.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Opcja 2. Pobieranie i sprawdzanie wszystkich iteracji przebiegów w środowisku Python

Alternatywnie możesz pobrać historię poszczególnych eksperymentów i zbadać każdą metrykę pod kątem indywidualnych przebiegów iteracji.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```




<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.113810</td>
      <td>0.093514</td>
      <td>-0.010248</td>
      <td>0.005867</td>
      <td>0.108187</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>7.004893</td>
      <td>6.348354</td>
      <td>6.493000</td>
      <td>7.045597</td>
      <td>6.646850</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>4.834063</td>
      <td>3.503244</td>
      <td>3.321553</td>
      <td>4.349547</td>
      <td>4.389995</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.077832</td>
      <td>0.070537</td>
      <td>0.072144</td>
      <td>0.078284</td>
      <td>0.073854</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.053712</td>
      <td>0.038925</td>
      <td>0.036906</td>
      <td>0.048328</td>
      <td>0.048778</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.117819</td>
      <td>0.120518</td>
      <td>0.126141</td>
      <td>0.124289</td>
      <td>0.118340</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.177689</td>
      <td>0.163360</td>
      <td>0.168101</td>
      <td>0.178250</td>
      <td>0.168685</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.104661</td>
      <td>0.064075</td>
      <td>-0.036158</td>
      <td>-0.004403</td>
      <td>0.096976</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>10.603744</td>
      <td>10.846632</td>
      <td>11.352731</td>
      <td>11.185972</td>
      <td>10.650593</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.801531</td>
      <td>0.736896</td>
      <td>0.758279</td>
      <td>0.804062</td>
      <td>0.760913</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.549825</td>
      <td>0.562435</td>
      <td>0.526702</td>
      <td>0.500302</td>
      <td>0.565857</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.549825</td>
      <td>0.562435</td>
      <td>0.562435</td>
      <td>0.562435</td>
      <td>0.565857</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu

Wybierz najlepszy potok z iteracji. Metoda `get_output` obiektu `automl_classifier` zwraca najlepszy przebieg i dopasowany model dla ostatniego wywołania dopasowania. Istnieją przeciążenia metody `get_output` umożliwiające pobranie najlepszego przebiegu i dopasowanego modelu dla dowolnej zarejestrowanej metryki lub konkretnej iteracji.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="register-the-model"></a>Rejestrowanie modelu

Zarejestruj model w obszarze roboczym usługi Azure Machine Learning.


```python
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model-accuracy"></a>Testowanie dokładności najlepszego modelu

Użyj najlepszego modelu, aby uruchomić przewidywania dla zestawu danych testowych. Funkcja `predict` używa najlepszego modelu i przewiduje wartość y (koszt podróży) na podstawie zestawu danych `x_test`. Wyświetl pierwsze 10 wartości przewidywanego kosztu z zestawu `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Porównaj przewidywane wartości kosztu z rzeczywistymi wartościami kosztu. Użyj ramki danych `y_test` i przekształć ją w listę umożliwiającą porównanie z przewidywanymi wartościami. Funkcja `mean_absolute_error` pobiera dwie tablice wartości i oblicza średnią wartość bezwzględną błędu między nimi. W tym przykładzie średni bezwzględny błąd o wartości 3,5 oznacza, że średnio model przewiduje koszt w zakresie od plus 3,5 do minus 3,5 od wartości rzeczywistej.

```python
from sklearn.metrics import mean_absolute_error

y_actual = y_test.values.flatten().tolist()
mean_absolute_error(y_actual, y_predict)
```

    [ 3.16213051 ]

Uruchom poniższy kod, aby obliczyć MAPE (średnią bezwzględną wartość procentową błędu) z użyciem pełnych zestawów danych `y_actual` i `y_predict`. Ta metryka oblicza bezwzględną różnicę między każdą przewidywaną i rzeczywistą wartością, sumuje wszystkie różnice, a następnie przedstawia tę sumę jako procent sumy wartości rzeczywistych.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.22424976634422172

    Model Accuracy:
    0.7757502336557782

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanego uczenia maszynowego wykonano następujące czynności:

> [!div class="checklist"]
> * Skonfigurowano obszar roboczy i przygotowano dane do eksperymentu.
> * Przeprowadzono trenowanie przy użyciu zautomatyzowanego modelu regresji lokalnie z użyciem niestandardowych parametrów.
> * Zbadano i przejrzano wyniki trenowania.
> * Zarejestrowano najlepszy model

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
