---
title: 'Utwórz Model języka Python: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć niestandardowy moduł modelowania lub przetwarzania danych przy użyciu modelu utworzyć Model języka Python w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029748"
---
# <a name="create-python-model"></a>Utwórz Model języka Python

W tym artykule opisano sposób używania **utworzyć Model języka Python** moduł służący do tworzenia modelu nieprzeszkolonych pochodzące ze skryptu języka Python. 

Model można oprzeć na dowolnym uczeń, który znajduje się w pakiet języka Python w środowisku usługi Azure Machine Learning. 

Po utworzeniu modelu, można użyć [uczenie modelu](train-model.md) do nauczenia modelu, w zestawie danych, takich jak inne uczeń, Azure Machine Learning. Uczonego modelu mogą być przekazywane do [Score Model](score-model.md) używania tego modelu do przewidywania przyszłych zdarzeń. Można zapisać uczonego modelu i oceniania przepływu pracy mogą być publikowane jako usługi sieci web.

> [!WARNING]
> Obecnie nie jest możliwe do przekazania wyników ocenami to model języka Python do [Evaluate Model](evaluate-model.md). Jeśli zachodzi potrzeba ocenę modelu, można napisać niestandardowy Python skrypt i uruchomić go za pomocą [wykonanie skryptu Python](execute-python-script.md) modułu.  


## <a name="how-to-configure-create-python-model"></a>Jak skonfigurować utworzyć Model języka Python

Korzystanie z tego modułu wymaga pośrednie lub ekspertów znajomości języka Python. Ten moduł umożliwia korzystanie z dowolnej uczeń, który znajduje się w pakiety języka Python zainstalowane w usłudze Azure Machine Learning. Zobacz wstępnie zainstalowanych listy pakietów języka Python w [wykonanie skryptu Python](execute-python-script.md).
  

Tym artykule opisano sposób używania **utworzyć Model języka Python** za pomocą prostego eksperymentu. Poniżej znajduje się wykres eksperymentu.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Kliknij przycisk **utworzyć Model języka Python**, przeprowadź edycję skryptu do wdrożenia usługi modelowania lub przetwarzania danych. Model można oprzeć na dowolnym uczeń, który znajduje się w pakiecie Python w środowisku usługi Azure Machine Learning.


    Oto przykładowy kod dwuklasowych Bayesa firmy klasyfikatora przy użyciu popularnej *skryptu sklearn* pakietu.

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


2. Połącz **utworzyć Model języka Python** moduł został utworzony do **Train Model** i **Score Model**

3. Jeśli zachodzi potrzeba ewaluacji modelu, należy dodać [wykonanie skryptu Python](execute-python-script.md) i edycji skrypt Python w celu wykonania oceny.

Poniżej przedstawiono przykładowy kod oceny.

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
