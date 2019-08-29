---
title: 'Utwórz model języka Python: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modelu modelu języka Python w usłudze Azure Machine Learning, aby utworzyć niestandardowy moduł modelowania lub przetwarzania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: c6d7aabd41e9d0e872926adbbcb2d18332cb7d5e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128919"
---
# <a name="create-python-model"></a>Tworzenie modelu języka Python

W tym artykule opisano sposób użycia modułu **tworzenia modelu języka Python** w celu utworzenia nieszkolenia modelu z poziomu skryptu języka Python. 

Model można utworzyć na podstawie dowolnych informacji, które są zawarte w pakiecie Python w środowisku Azure Machine Learning. 

Po utworzeniu modelu można użyć [modelu uczenia](train-model.md) do uczenia modelu w zestawie danych, podobnie jak w przypadku innych dowolnych informacji w Azure Machine Learning. Przeszkolony model można przesłać do [modelu oceny](score-model.md) , aby użyć modelu do prognozowania. Można następnie zapisać przeszkolony model, a przepływ pracy oceniania może być opublikowany jako usługa sieci Web.

> [!WARNING]
> Obecnie nie można przekazać wyników oceny modelu języka Python do [oceny modelu](evaluate-model.md). Jeśli chcesz oszacować model, możesz napisać niestandardowy skrypt w języku Python i uruchomić go przy użyciu modułu [uruchamiania skryptu języka Python](execute-python-script.md) .  


## <a name="how-to-configure-create-python-model"></a>Jak skonfigurować model tworzenia modelu języka Python

Korzystanie z tego modułu wymaga pośredniej lub specjalistycznej wiedzy o języku Python. Moduł obsługuje korzystanie z dowolnych informacji, które znajdują się w pakietach języka Python zainstalowanych już w Azure Machine Learning. Zobacz wstępnie zainstalowaną listę pakietów języka Python w [skrypcie wykonywania skryptu języka Python](execute-python-script.md).
  

W tym artykule przedstawiono sposób korzystania z **modelu języka Python** z prostym eksperymentem. Poniżej znajduje się wykres eksperymentu.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Kliknij pozycję **Utwórz model języka Python**, Edytuj skrypt, aby zaimplementować proces modelowania lub zarządzania danymi. Model można utworzyć na podstawie dowolnych informacji, które są zawarte w pakiecie języka Python w środowisku Azure Machine Learning.


    Poniżej znajduje się przykładowy kod dwóch klas klasyfikatora algorytm Bayesa Bayesa przy użyciu popularnego pakietu *skryptu sklearn* .

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Połącz właśnie utworzony moduł **tworzenia modelu języka Python** z modelem **uczenia** i **modelem oceny**

3. Jeśli musisz ocenić model, Dodaj [skrypt środowiska Python](execute-python-script.md) i Edytuj skrypt języka Python w celu zaimplementowania oceny.

Poniżej znajduje się przykładowy kod ewaluacyjny.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
