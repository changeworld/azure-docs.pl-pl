---
title: 'Samouczek dotyczący modelu regresji: Zautomatyzowane uczenie maszynowe'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak wygenerować model uczenia maszynowego przy użyciu zautomatyzowanego uczenia maszynowego. Korzystając z usługi Azure Machine Learning, można w sposób zautomatyzowany wykonywać wstępne przetwarzanie danych, wybierać algorytmy i hiperparametry. Końcowy model jest następnie wdrażany za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: 990991eb1ceb5d74c042b42cfa265c75a073e5ef
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670901"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Samouczek: Kompilowanie modelu regresji przy użyciu zautomatyzowanego uczenia maszynowego

Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W poprzednim samouczku [przygotowano dane dotyczące taksówek w Nowym Jorku na potrzeby modelowania regresji](tutorial-data-prep.md).

Teraz możesz rozpocząć tworzenie modelu za pomocą usługi Azure Machine Learning. W tej części samouczka użyjesz przygotowanych danych i automatycznie wygenerujesz model regresji do prognozowania cen taryfy taksówek. Używając funkcji zautomatyzowanego uczenia maszynowego, zdefiniujesz cele i ograniczenia uczenia maszynowego. Uruchomisz proces zautomatyzowanego uczenia maszynowego. Następnie pozwolisz na wybranie algorytmu i dostrojenie hiperparametru za Ciebie. Technika zautomatyzowanego uczenia maszynowego wykonuje iterację wielu kombinacji algorytmów i hiperparametrów aż do momentu znalezienia najlepszego modelu na podstawie podanego kryterium.

![Diagram przepływu](./media/tutorial-auto-train-models/flow2.png)

W tym samouczku nauczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie środowiska Python i importowanie pakietów SDK.
> * Konfigurowanie obszaru roboczego usługi Azure Machine Learning.
> * Automatyczne trenowanie modelu regresji.
> * Lokalne uruchamianie modelu z użyciem niestandardowych parametrów.
> * Eksplorowanie wyników.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu Azure Machine Learning SDK w wersji 1.0.0.

## <a name="prerequisites"></a>Wymagania wstępne

Przejdź do sekcji [Konfigurowanie środowiska projektowego](#start), aby zapoznać się z instrukcjami dotyczącymi notesu, lub skorzystaj z poniższych instrukcji, aby pobrać notes i uruchomić go w usłudze Azure Notebooks lub na swoim serwerze notesów. Do uruchomienia notesu potrzebne są następujące elementy:

* [Ukończony samouczek przygotowywania danych](tutorial-data-prep.md).
* Serwer notesów Python 3.6 z zainstalowanym następującym oprogramowaniem:
    * Zestaw SDK usługi Azure Machine Learning dla języka Python z dodatkami `automl` i `notebooks`
    * `matplotlib`
* Notes samouczka
* Obszar roboczy uczenia maszynowego
* Plik konfiguracji obszaru roboczego w tym samym katalogu co notes

Wszystkie te wymagania wstępne można spełnić, korzystając z jednej z poniższych sekcji.

* Korzystanie z usługi [Azure Notebooks](#azure)
* Korzystanie z [własnego serwera notesów](#server)

### <a name="azure"></a>Korzystanie z usługi Azure Notebooks: bezpłatne notesy Jupyter Notebook w chmurze

Rozpoczęcie pracy z usługą Azure Notebooks jest bardzo proste. [Zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) został już zainstalowany i skonfigurowany w usłudze [Azure Notebooks](https://notebooks.azure.com/). Instalacja i przyszłe aktualizacje są automatycznie zarządzane za pomocą usług platformy Azure.

Po wykonaniu poniższych czynności uruchom notes **tutorials/regression-part2-automated-ml.ipynb** w projekcie **Wprowadzenie**.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Korzystanie z własnego serwera notesów Jupyter Notebook

Wykonaj te kroki, aby utworzyć lokalny serwer notesów Jupyter Notebook na komputerze.  Upewnij się, że instalujesz program `matplotlib` i `automl` i `notebooks` dodatki w danym środowisku.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Po wykonaniu kroków, uruchom notes **tutorials/regresssion-part2-automated-ml.ipynb**.

## <a name="start"></a>Konfigurowanie środowiska projektowego

Cała konfiguracja dla prac programistycznych może zostać wykonana w notesie języka Python. Konfiguracja obejmuje następujące czynności:

* Instalacja zestawu SDK
* Importowanie pakietów języka Python
* Konfigurowanie obszaru roboczego

### <a name="install-and-import-packages"></a>Zainstaluj i zaimportuj pakiety

Jeśli korzystasz z tego samouczka we własnym środowisku języka Python, zainstaluj wymagane pakiety za pomocą następującego polecenia.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Zaimportuj pakiety języka Python, które są potrzebne w tym samouczku:

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. A [obszaru roboczego](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) to klasa, która akceptuje usługi Azure information subskrypcji i zasobów. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu.

`Workspace.from_config()` odczytuje plik **aml_config/config.json** i ładuje szczegóły do obiektu o nazwie `ws`.  Obiekt `ws` jest używany w kodzie w tym samouczku.

Po utworzeniu obiektu obszaru roboczego określ nazwę eksperymentu. Utwórz i zarejestruj katalog lokalny w obszarze roboczym. Historia wszystkich przebiegów jest rejestrowana w ramach określonego eksperymentu oraz w witrynie [Azure Portal](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

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

Użyj obiektu przepływu danych utworzonego w poprzednim samouczku. Podsumowując, w części 1 tego samouczka dane dotyczące nowojorskich taksówek zostały wyczyszczone, aby umożliwić ich użycie w modelu uczenia maszynowego. Teraz użyjemy różnych cech z zestawu danych i za pomocą zautomatyzowanego modelu utworzymy relacje między tymi cechami a kosztem przejazdu taksówką. Otwórz i uruchom przepływ danych, a następnie przejrzyj wyniki:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

package_saved = dprep.Package.open(file_path)
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min.</th>
      <th>Maks.</th>
      <th>Licznik</th>
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
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
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
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
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
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2,90047</td>
      <td>2,69355</td>
      <td>9,72889</td>
      <td>16</td>
      <td>19,3713</td>
      <td>22,6974</td>
      <td>23</td>
      <td>23</td>
      <td>14,2731</td>
      <td>6,59242</td>
      <td>43,46</td>
      <td>-0,693723</td>
      <td>-0,570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4,99701</td>
      <td>4,95833</td>
      <td>14,1528</td>
      <td>29,3832</td>
      <td>44,6825</td>
      <td>56,4444</td>
      <td>58,9909</td>
      <td>59</td>
      <td>29,427</td>
      <td>17,4333</td>
      <td>303,921</td>
      <td>0,0120999</td>
      <td>-1,20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,28131</td>
      <td>5</td>
      <td>14,7832</td>
      <td>29,9293</td>
      <td>44,725</td>
      <td>56,7573</td>
      <td>59</td>
      <td>59</td>
      <td>29,7443</td>
      <td>17,3595</td>
      <td>301,351</td>
      <td>-0,0252399</td>
      <td>-1,19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Piątek</td>
      <td>Środa</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
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
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2,57153</td>
      <td>2</td>
      <td>9,58795</td>
      <td>15,9994</td>
      <td>19,6184</td>
      <td>22,8317</td>
      <td>23</td>
      <td>23</td>
      <td>14,2105</td>
      <td>6,71093</td>
      <td>45,0365</td>
      <td>-0,687292</td>
      <td>-0,61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,44383</td>
      <td>4,84694</td>
      <td>14,1036</td>
      <td>28,8365</td>
      <td>44,3102</td>
      <td>56,6892</td>
      <td>59</td>
      <td>59</td>
      <td>29,2907</td>
      <td>17,4108</td>
      <td>303,136</td>
      <td>0,0222514</td>
      <td>-1,2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,07801</td>
      <td>5</td>
      <td>14,5751</td>
      <td>29,5972</td>
      <td>45,4649</td>
      <td>56,2729</td>
      <td>59</td>
      <td>59</td>
      <td>29,772</td>
      <td>17,5337</td>
      <td>307,429</td>
      <td>-0,0212575</td>
      <td>-1,226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Nie</td>
      <td>Tak</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
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
      <td>-74,0781</td>
      <td>-73,7459</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0578</td>
      <td>-73,9639</td>
      <td>-73,9656</td>
      <td>-73,9508</td>
      <td>-73,9255</td>
      <td>-73,8529</td>
      <td>-73,8302</td>
      <td>-73,8238</td>
      <td>-73,7697</td>
      <td>-73,9123</td>
      <td>0,0503757</td>
      <td>0,00253771</td>
      <td>0,352172</td>
      <td>-0,923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,632</td>
      <td>40,7117</td>
      <td>40,7115</td>
      <td>40,7213</td>
      <td>40,7565</td>
      <td>40,8058</td>
      <td>40,8478</td>
      <td>40.8676</td>
      <td>40,8778</td>
      <td>40,7649</td>
      <td>0,0494674</td>
      <td>0,00244702</td>
      <td>0,205972</td>
      <td>-0,777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0775</td>
      <td>-73,9875</td>
      <td>-73,9882</td>
      <td>-73,9638</td>
      <td>-73,935</td>
      <td>-73,8755</td>
      <td>-73,8125</td>
      <td>-73,7759</td>
      <td>-73,7327</td>
      <td>-73,9202</td>
      <td>0,0584627</td>
      <td>0,00341789</td>
      <td>0,623622</td>
      <td>0,262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,5973</td>
      <td>40,6928</td>
      <td>40,6911</td>
      <td>40,7226</td>
      <td>40,7567</td>
      <td>40,7918</td>
      <td>40,8495</td>
      <td>40.868</td>
      <td>40,8787</td>
      <td>40,7583</td>
      <td>0,0517399</td>
      <td>0,00267701</td>
      <td>0,0390404</td>
      <td>-0,203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
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
      <td>2,39249</td>
      <td>1,83197</td>
      <td>3,3561</td>
      <td>0,763144</td>
      <td>-1,23467</td>
    </tr>
    <tr>
      <th>odległość</th>
      <td>FieldType.DECIMAL</td>
      <td>0,01</td>
      <td>32,34</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0108744</td>
      <td>0,743898</td>
      <td>0,738194</td>
      <td>1,243</td>
      <td>2,40168</td>
      <td>4,74478</td>
      <td>10,5136</td>
      <td>14,9011</td>
      <td>21,8035</td>
      <td>3,5447</td>
      <td>3,2943</td>
      <td>10,8524</td>
      <td>1,91556</td>
      <td>4,99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2,33837</td>
      <td>5,00491</td>
      <td>5</td>
      <td>6,93129</td>
      <td>10,524</td>
      <td>17,4811</td>
      <td>33,2343</td>
      <td>50,0093</td>
      <td>63,1753</td>
      <td>13,6843</td>
      <td>9,66571</td>
      <td>93,426</td>
      <td>1,78518</td>
      <td>4,13972</td>
    </tr>
  </tbody>
</table>

Należy przygotować dane do eksperymentu, dodając kolumny do elementu `dflow_x`, aby stały się funkcjami w procesie tworzenia naszego modelu. Element `dflow_y` należy zdefiniować w taki sposób, aby stał się naszą wartością prognozowania **koszt**:

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Podział danych na zestawy treningowe i testowe

Teraz podzielisz dane na zestawy treningowe i testowe za pomocą funkcji `train_test_split` z biblioteki `sklearn`. Ta funkcja segreguje dane między zestaw danych x **cechy** na potrzeby trenowania modelu i zestaw danych y **wartości do prognozowania** na potrzeby testowania. Parametr `test_size` określa procent danych przydzielanych do testowania. Parametr `random_state` ustawia inicjator na generator liczb losowych, dzięki czemu podział na dane trenowane i testowane jest zawsze deterministyczny:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

Celem tego etapu jest uzyskanie punktów danych do przetestowania ukończono modelu, które nie były używane do jego wytrenowania. Pozwoli to zmierzyć rzeczywistą dokładność. Innymi słowy, dobrze wytrenowany model powinien umożliwiać dokładne prognozowanie na podstawie danych, które jeszcze się nie pojawiły. Teraz masz niezbędne pakiety i dane gotowe do automatycznego szkolenia modelu.

## <a name="automatically-train-a-model"></a>Automatyczne trenowanie modelu

Aby przeprowadzić automatyczne trenowanie modelu, wykonaj następujące czynności:
1. Zdefiniuj ustawienia przebiegu eksperymentu. Dołącz do konfiguracji dane treningowe i zmodyfikuj ustawienia, które sterują procesem treningu.
1. Prześlij eksperyment do strojenia modelu. Po przesłaniu eksperymentu proces wykonuje iterację z użyciem różnych algorytmów uczenia maszynowego i ustawień hiperparametrycznych z uwzględnieniem zdefiniowanych ograniczeń. Na podstawie zoptymalizowanej metryki dokładności jest wybierany model o najlepszym dopasowaniu.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Definiowanie ustawień automatycznego generowania i dostrajania

Zdefiniuj parametr eksperymentu i ustawienia modelu na potrzeby automatycznego generowania i dostrajania. Wyświetl pełną listę [ustawień](how-to-configure-auto-train.md). Przesyłanie eksperymentu z ustawieniami domyślnymi zajmuje około 10–15 minut. Jeśli chcesz skrócić czas wykonywania, zmniejsz właściwość `iterations` lub `iteration_timeout_minutes`.


|Właściwość| Wartość w ramach tego samouczka |Opis|
|----|----|---|
|**iteration_timeout_minutes**|10|Limit czasu w minutach dla każdej iteracji. Zmniejszenie tej wartości powoduje skrócenie całkowitego czasu wykonywania.|
|**iterations**|30|Liczba iteracji. W każdej iteracji przy użyciu danych jest trenowany nowy model uczenia maszynowego. Jest to podstawowa wartość, która ma wpływ na całkowity czas wykonywania.|
|**primary_metric**| spearman_correlation | Metryka, który ma być optymalizowana. Na podstawie tej metryki zostanie wybrany model o najlepszym dopasowaniu.|
|**preprocess**| True | Ustawienie wartości **True** umożliwia wstępne przetworzenie danych wejściowych w eksperymencie (obsługę brakujących danych, przekonwertowanie tekstu na liczby itp.).|
|**verbosity**| logging.INFO | Steruje poziomem rejestrowania.|
|**n_cross_validations**|5|Liczba podziałów krzyżowego sprawdzania poprawności w przypadku nieokreślenia danych weryfikacji.|



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

Użyj zdefiniowanych ustawień treningowych jako parametru obiektu `AutoMLConfig`. Ponadto określ dane treningowe i typ modelu — w tym przypadku `regression`.

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

Uruchom eksperyment lokalnie. Przekaż zdefiniowany obiekt `automated_ml_config` do eksperymentu. Ustaw wartość danych wyjściowych na `True`, aby wyświetlić postęp podczas eksperymentu:


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

Po uruchomieniu eksperymentu dane wyjściowe są aktualizowane na żywo. W każdej iteracji widać typ modelu, czas trwania i dokładność treningu. Pole `BEST` umożliwia śledzenie najlepszego wyniku w uruchomionym treningu na podstawie typu metryki.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Eksplorowanie wyników

Zapoznaj się z wynikami automatycznego trenowania, korzystając z widżetu Jupyter lub sprawdzając historię eksperymentu.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Opcja 1. Dodawanie widżetu Jupyter w celu wyświetlania wyników

Jeśli używasz notesu Jupyter, użyj widżetu notesu Jupyter, aby wyświetlić wykres i tabelę ze wszystkimi wynikami:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Szczegóły przebiegu widżetu Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Wykres widżetu Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Opcja 2: Pobieranie i sprawdzanie wszystkich iteracji przebiegów w środowisku Python

Możesz także pobrać historię poszczególnych eksperymentów i zbadać każdą metrykę pod kątem indywidualnych przebiegów iteracji. Po sprawdzeniu właściwości RMSE (root_mean_squared_error) w poszczególnych przebiegach modelu okazuje się, że większość iteracji przewiduje koszt przejazdu taksówką w rozsądnym zakresie (3–4 USD).

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

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
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>Przyciski ...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,811037</td>
      <td>0,880553</td>
      <td>0,398582</td>
      <td>0,776040</td>
      <td>0,663869</td>
      <td>0,875911</td>
      <td>0,115632</td>
      <td>0,586905</td>
      <td>0,851911</td>
      <td>0,793964</td>
      <td>Przyciski ...</td>
      <td>0,850023</td>
      <td>0,883603</td>
      <td>0,883704</td>
      <td>0,880797</td>
      <td>0,881564</td>
      <td>0,883708</td>
      <td>0,881826</td>
      <td>0,585377</td>
      <td>0,883123</td>
      <td>0,886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2,189444</td>
      <td>1,500412</td>
      <td>5,480531</td>
      <td>2,626316</td>
      <td>2,973026</td>
      <td>1,550199</td>
      <td>6,383868</td>
      <td>4,414241</td>
      <td>1,743328</td>
      <td>2,294601</td>
      <td>Przyciski ...</td>
      <td>1,797402</td>
      <td>1,415815</td>
      <td>1,418167</td>
      <td>1,578617</td>
      <td>1,559427</td>
      <td>1,413042</td>
      <td>1,551698</td>
      <td>4,069196</td>
      <td>1,505795</td>
      <td>1,430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1,438417</td>
      <td>0,850899</td>
      <td>4,579662</td>
      <td>1,765210</td>
      <td>1,594600</td>
      <td>0,869883</td>
      <td>4,266450</td>
      <td>3,627355</td>
      <td>0,954992</td>
      <td>1,361014</td>
      <td>Przyciski ...</td>
      <td>0,973634</td>
      <td>0,774814</td>
      <td>0,797269</td>
      <td>1,147234</td>
      <td>1,116424</td>
      <td>0,783958</td>
      <td>1,098464</td>
      <td>2,709027</td>
      <td>1,003728</td>
      <td>0,851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0,024908</td>
      <td>0,017070</td>
      <td>0,062350</td>
      <td>0,029878</td>
      <td>0,033823</td>
      <td>0,017636</td>
      <td>0,072626</td>
      <td>0,050219</td>
      <td>0,019833</td>
      <td>0,026105</td>
      <td>Przyciski ...</td>
      <td>0,020448</td>
      <td>0,016107</td>
      <td>0,016134</td>
      <td>0,017959</td>
      <td>0,017741</td>
      <td>0,016076</td>
      <td>0,017653</td>
      <td>0,046293</td>
      <td>0,017131</td>
      <td>0,016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,016364</td>
      <td>0,009680</td>
      <td>0,052101</td>
      <td>0,020082</td>
      <td>0,018141</td>
      <td>0,009896</td>
      <td>0,048538</td>
      <td>0,041267</td>
      <td>0,010865</td>
      <td>0,015484</td>
      <td>Przyciski ...</td>
      <td>0,011077</td>
      <td>0,008815</td>
      <td>0,009070</td>
      <td>0,013052</td>
      <td>0,012701</td>
      <td>0,008919</td>
      <td>0,012497</td>
      <td>0,030819</td>
      <td>0,011419</td>
      <td>0,009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0,047968</td>
      <td>0,037882</td>
      <td>0,085572</td>
      <td>0,052282</td>
      <td>0,065809</td>
      <td>0,038664</td>
      <td>0,109401</td>
      <td>0,071104</td>
      <td>0,042294</td>
      <td>0,049967</td>
      <td>Przyciski ...</td>
      <td>0,042565</td>
      <td>0,037685</td>
      <td>0,037557</td>
      <td>0,037643</td>
      <td>0,037513</td>
      <td>0,037560</td>
      <td>0,037465</td>
      <td>0,072077</td>
      <td>0,037249</td>
      <td>0,036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0,055353</td>
      <td>0,045000</td>
      <td>0,110219</td>
      <td>0,065633</td>
      <td>0,063589</td>
      <td>0,044412</td>
      <td>0,123433</td>
      <td>0,092312</td>
      <td>0,046130</td>
      <td>0,055243</td>
      <td>Przyciski ...</td>
      <td>0,046540</td>
      <td>0,041804</td>
      <td>0,041771</td>
      <td>0,045175</td>
      <td>0,044628</td>
      <td>0,041617</td>
      <td>0,044405</td>
      <td>0,079651</td>
      <td>0,042799</td>
      <td>0,041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0,810900</td>
      <td>0,880328</td>
      <td>0,398076</td>
      <td>0,775957</td>
      <td>0,642812</td>
      <td>0,875719</td>
      <td>0,021603</td>
      <td>0,586514</td>
      <td>0,851767</td>
      <td>0,793671</td>
      <td>Przyciski ...</td>
      <td>0,849809</td>
      <td>0,880142</td>
      <td>0,880952</td>
      <td>0,880586</td>
      <td>0,881347</td>
      <td>0,880887</td>
      <td>0,881613</td>
      <td>0,548121</td>
      <td>0,882883</td>
      <td>0,886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4,216362</td>
      <td>3,329810</td>
      <td>7,521765</td>
      <td>4,595604</td>
      <td>5,784601</td>
      <td>3,398540</td>
      <td>9,616354</td>
      <td>6,250011</td>
      <td>3,717661</td>
      <td>4,392072</td>
      <td>Przyciski ...</td>
      <td>3,741447</td>
      <td>3,312533</td>
      <td>3,301242</td>
      <td>3,308795</td>
      <td>3,297389</td>
      <td>3,301485</td>
      <td>3,293182</td>
      <td>6,335581</td>
      <td>3,274209</td>
      <td>3,227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,243184</td>
      <td>0,197702</td>
      <td>0,484227</td>
      <td>0,288349</td>
      <td>0,279367</td>
      <td>0,195116</td>
      <td>0,542281</td>
      <td>0,405559</td>
      <td>0,202666</td>
      <td>0,242702</td>
      <td>Przyciski ...</td>
      <td>0,204464</td>
      <td>0,183658</td>
      <td>0,183514</td>
      <td>0,198468</td>
      <td>0,196067</td>
      <td>0,182836</td>
      <td>0,195087</td>
      <td>0,349935</td>
      <td>0,188031</td>
      <td>0,182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,857965</td>
      <td>0,914703</td>
      <td>0,939846</td>
      <td>0,956159</td>
      <td>0,828187</td>
      <td>0,942069</td>
      <td>0,952581</td>
      <td>0,935477</td>
      <td>Przyciski ...</td>
      <td>0,951287</td>
      <td>0,960335</td>
      <td>0,960195</td>
      <td>0,960279</td>
      <td>0,960288</td>
      <td>0,960323</td>
      <td>0,960161</td>
      <td>0,941254</td>
      <td>0,960293</td>
      <td>0,962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>Przyciski ...</td>
      <td>0,960303</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,962158</td>
    </tr>
  </tbody>
</table>
<p>12 wierszy x 30 kolumn</p>
</div>

## <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu

Wybierz najlepszy potok z iteracji. Metoda `get_output` obiektu `automl_classifier` zwraca najlepszy przebieg i dopasowany model dla ostatniego wywołania dopasowania. Używając przeciążeń metody `get_output`, możesz pobrać najlepszy przebieg i dopasowany model dla dowolnej zarejestrowanej metryki lub konkretnej iteracji:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>Testowanie dokładności najlepszego modelu

Za pomocą najlepszego modelu uruchomionego na zestawie danych testowych spróbuj przewidzieć koszty przejazdu taksówką. Funkcja `predict` używa najlepszego modelu i przewiduje wartość y **koszt podróży** na podstawie zestawu danych `x_test`. Wyświetl pierwsze 10 wartości przewidywanego kosztu z zestawu `y_predict`:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Utwórz wykres punktowy, aby wizualizować przewidywane wartości kosztów w porównaniu z rzeczywistymi wartościami kosztów. Poniższy kod używa funkcji `distance` na osi x i funkcji `cost` podróży na osi y. W celu porównania wariancji przewidywanego kosztu dla poszczególnych wartości odległości w podróży pierwsze 100 przewidywanych i rzeczywistych wartości kosztów tworzy się jako osobną serię. Sprawdzając wykres, zobaczysz, że relacja odległości i kosztu jest prawie liniowa, a wartości przewidywanych kosztów są w większości przypadków bardzo zbliżone do wartości rzeczywistych kosztów dla tej samej odległości w podróży.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Wykres punktowy prognozy](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Oblicz wartość `root mean squared error` dla wyników. Użyj ramki danych `y_test`. Przekształć ją w listę umożliwiającą porównanie z przewidywanymi wartościami. Funkcja `mean_squared_error` pobiera dwie tablice wartości i oblicza średnią wartość błędu kwadratowego między nimi. Wyciągnięcie pierwiastka kwadratowego z wyniku powoduje błąd w tych samych jednostkach co zmienna y **koszt**. W przybliżeniu pokazuje to, jak odległe są prognozy cen przejazdów od rzeczywistych wartości:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Uruchom poniższy kod, aby obliczyć średnią bezwzględną wartość procentową błędu (MAPE) z użyciem pełnych zestawów danych `y_actual` i `y_predict`. Ta metryka oblicza wartości bezwzględne różnic między poszczególnymi wartościami przewidywanymi i rzeczywistymi oraz sumuje wszystkie różnice. Następnie wyraża tę sumę jako procent sumy wartości rzeczywistych:

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
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

Z metryk dokładności prognozowania końcowego widać, że model dość dobrze przewiduje opłaty za przejazd na podstawie cech w zestawie danych, zwykle z dokładnością +/- 3,00 USD. Tradycyjny proces opracowywania modelu uczenia maszynowego intensywnie korzysta z zasobów. Wymaga dużej wiedzy o danej dziedzinie oraz czasu, który trzeba poświęcić na uruchamianie kilkudziesięciu modeli i porównywanie ich wyników. Użycie automatycznego uczenia maszynowego jest doskonałym sposobem na szybkie przetestowanie wielu różnych modeli w danym scenariuszu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zautomatyzowanego uczenia maszynowego wykonano następujące czynności:

> [!div class="checklist"]
> * Skonfigurowano obszar roboczy i przygotowano dane do eksperymentu.
> * Przeprowadzono trenowanie przy użyciu zautomatyzowanego modelu regresji lokalnie z użyciem niestandardowych parametrów.
> * Zbadano i przejrzano wyniki trenowania.

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
