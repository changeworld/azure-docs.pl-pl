---
title: 'Tworzenie modelu Języka Python: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Tworzenie modelu języka Python w usłudze Azure Machine Learning w celu utworzenia niestandardowego modułu modelowania lub przetwarzania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371821"
---
# <a name="create-python-model-module"></a>Tworzenie modułu modelu języka Python

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Dowiedz się, jak użyć modułu Tworzenie modelu języka Python, aby utworzyć nieprzeszkolony model ze skryptu Języka Python. Model można oprzeć na dowolnym uczniu, który jest zawarty w pakiecie języka Python w środowisku projektanta usługi Azure Machine Learning. 

Po utworzeniu modelu można użyć [modelu pociągu](train-model.md) do uczenia modelu na zestawie danych, jak każdy inny uczeń w usłudze Azure Machine Learning. Przeszkolony model można przekazać do [score model](score-model.md) do prognozowania. Następnie można zapisać przeszkolony model i opublikować przepływ pracy oceniania jako usługę sieci web.

> [!WARNING]
> Obecnie nie jest możliwe przekazanie wyników ocenianego modelu Języka Python w celu [oceny modelu.](evaluate-model.md) Jeśli chcesz ocenić model, możesz napisać niestandardowy skrypt języka Python i uruchomić go przy użyciu modułu [Wykonywanie skryptu Pythona.](execute-python-script.md)  


## <a name="configure-the-module"></a>Konfigurowanie modułu

Korzystanie z tego modułu wymaga pośredniej lub specjalistycznej wiedzy języka Python. Moduł obsługuje korzystanie z dowolnego ucznia, który jest zawarty w pakietach Pythona już zainstalowanych w usłudze Azure Machine Learning. Zobacz listę preinstalowanych pakietów Pythona w [języku Execute Python Script](execute-python-script.md).
  

W tym artykule pokazano, jak używać **tworzenia modelu języka Python** za pomocą prostego potoku. Oto diagram potoku:

![Diagram tworzenia modelu języka Python](./media/module/create-python-model.png)

1. Wybierz **pozycję Utwórz model języka Python**i edytuj skrypt, aby zaimplementować proces modelowania lub zarządzania danymi. Model można oprzeć na dowolnym uczniu, który jest zawarty w pakiecie języka Python w środowisku usługi Azure Machine Learning.

   Poniższy przykładowy kod klasyfikatora Naive Bayes używa popularnego pakietu *sklearn:*

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

1. Połącz moduł **Utwórz model Języka Python,** który został właśnie utworzony w celu **modelu pociągu** i **modelu wyników**.

1. Jeśli chcesz ocenić model, dodaj moduł [Wykonaj skrypt języka Python](execute-python-script.md) i edytuj skrypt Pythona.

   Poniższy skrypt to przykładowy kod oceny:

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

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 