---
title: Użyj Azure Machine Learning w Azure Notebooks
description: Omówienie przykładowych notesów dla Azure Machine Learning, których można użyć z Azure Notebooks.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277456"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Używanie Azure Machine Learning w notesie

Azure Notebooks jest wstępnie skonfigurowany przy użyciu środowiska niezbędnego do pracy z [Azure Machine Learning](/azure/machine-learning/service/). Przykładowy projekt można łatwo sklonować na koncie notesów, aby zapoznać się z różnych scenariuszy usługi Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Klonowanie przykładu do swojego konta

1. Zaloguj się do [notesy platformy Azure](https://notebooks.azure.com/).
1. Wybierz pozycję **Moje projekty** , aby przejść do pulpitu nawigacyjnego projekty.
1. Wybierz przycisk **Przekaż repozytorium GitHub** (Strzałka w górę), aby otworzyć okno podręczne **przekazywanie repozytorium GitHub** .
1. W oknie podręcznym wprowadź `Azure/MachineLearningNotebooks` w **repozytorium GitHub**, podaj nazwę projektu w polu **Nazwa projektu** , na przykład "Azure Machine Learning", podaj identyfikator w polu **Identyfikator projektu**, wyczyść pole wyboru **publiczny** , jeśli chcesz, a następnie wybierz pozycję **Importuj**.

    ![Importuj przykład usługi Azure Machine Learning notesu na koncie notesów](media/azureml-import-project.png)

1. Po minucie lub dwóch notesów usługi Azure automatycznie spowoduje przejście do pulpitu nawigacyjnego nowy projekt.

## <a name="run-a-sample-notebook"></a>Uruchamianie notesu próbki

1. Wybierz **00 - configuration.ipynb** uruchomienie sekcji konfiguracji w notesie, a następnie postępuj zgodnie z jego instrukcjami, aby utworzyć obszar roboczy usługi Azure Machine Learning.

    - Ponieważ notesy platformy Azure zawiera niezbędne pakiety języka Python, można po prostu uruchomić fragment kodu w kroku 2 warunków wstępnych, aby sprawdzić wersję zestawu SDK usługi Azure ML.

1. Po zakończeniu konfiguracji wybierz pozycję **01. wprowadzenie** do otwarcia folderu zawierającego trzynastie różnych przykładowych notesów, z których każdy nie ma wyjaśnień.

## <a name="next-steps"></a>Następne kroki

Dokumentacja Azure Machine Learning zawiera różne inne zasoby, które przeprowadzą Cię przez proces pracy z Machine Learning w notesach:

- [Szybki Start: Za pomocą języka Python Rozpoczynanie pracy z usługą Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Samouczek #1: uczenie modelu klasyfikacji obrazów z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Samouczek #2: Wdrażanie model klasyfikacji obrazów w wystąpieniu kontenera platformy Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Samouczek: uczenie modelu klasyfikacji przy użyciu automatycznego uczenia maszynowego w Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Ponadto można znaleźć w dokumentacji [Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
