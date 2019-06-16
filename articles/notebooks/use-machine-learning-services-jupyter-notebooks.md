---
title: Użyj usługi Azure Machine Learning Services w notesy platformy Azure
description: Przegląd notesów próbki dla usług Azure Machine Learning, za pomocą notesów usługi Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2ef327721fd42e5274381834721fd987ec7e9d75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240501"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Usługa Azure Machine Learning w notesie

Notesy platformy Azure ma wstępnie skonfigurowany środowiska niezbędne do pracy z [usługi Azure Machine Learning](/azure/machine-learning/service/). Przykładowy projekt można łatwo sklonować na koncie notesów, aby zapoznać się z różnych scenariuszy usługi Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Klonowanie przykładu do swojego konta

1. Zaloguj się do [notesy platformy Azure](https://notebooks.azure.com/).
1. Wybierz **Moje projekty** można przejść do pulpitu nawigacyjnego projektów.
1. Wybierz **repozytorium GitHub, aby przekazać** (strzałkę w górę) przycisk Otwórz **przekazywanie repozytorium GitHub** okna podręcznego.
1. W okienku wyskakującym wprowadź `Azure/MachineLearningNotebooks` w **repozytorium GitHub**, podaj nazwę dla projektu w **Nazwa projektu** takich jak "Usługa Azure Machine Learning," Podaj identyfikator w **identyfikator projektu** , wyczyść **publicznych** , następnie wybierz opcję **importu**.

    ![Importuj przykład usługi Azure Machine Learning notesu na koncie notesów](media/azureml-import-project.png)

1. Po minucie lub dwóch notesów usługi Azure automatycznie spowoduje przejście do pulpitu nawigacyjnego nowy projekt.

## <a name="run-a-sample-notebook"></a>Uruchamianie notesu próbki

1. Wybierz **00 - configuration.ipynb** uruchomienie sekcji konfiguracji w notesie, a następnie postępuj zgodnie z jego instrukcjami, aby utworzyć obszar roboczy usługi Azure Machine Learning.

    - Ponieważ notesy platformy Azure zawiera niezbędne pakiety języka Python, można po prostu uruchomić fragment kodu w kroku 2 warunków wstępnych, aby sprawdzić wersję zestawu SDK usługi Azure ML.

1. Po zakończeniu konfiguracji wybierz **pracę 01.getting** przejdź do folderu zawierającego trzynaście notesów przykładu, z których każdy jest oczywista.

## <a name="next-steps"></a>Kolejne kroki

Dokumentacja usług Azure Machine Learning zawiera szereg innych zasobów, które pomagają w pracy z usługi Machine Learning w notesach:

- [Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu języka Python](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Samouczek nr 1: Uczenie modelu Klasyfikacja obrazów przy użyciu usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Samouczek 2: Wdróż model klasyfikacji obrazów w wystąpieniu kontenera platformy Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Samouczek: Wytrenuj model klasyfikacji z automatycznych machine learning w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Ponadto można znaleźć w dokumentacji [Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
