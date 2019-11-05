---
title: Użyj Azure Machine Learning w Azure Notebooks
description: Omówienie przykładowych notesów dla Azure Machine Learning, których można użyć z Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496638"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Używanie Azure Machine Learning w notesie

Azure Notebooks jest wstępnie skonfigurowany przy użyciu środowiska niezbędnego do pracy z [Azure Machine Learning](/azure/machine-learning/service/). Możesz łatwo sklonować przykładowy projekt do konta notesów, aby poznać różne scenariusze Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Klonowanie przykładu do konta

1. Zaloguj się do [Azure Notebooks](https://notebooks.azure.com/).
1. Wybierz pozycję **Moje projekty** , aby przejść do pulpitu nawigacyjnego projekty.
1. Wybierz przycisk **Przekaż repozytorium GitHub** (Strzałka w górę), aby otworzyć okno podręczne **przekazywanie repozytorium GitHub** .
1. W oknie podręcznym wprowadź `Azure/MachineLearningNotebooks` w **repozytorium GitHub**, podaj nazwę projektu w polu **Nazwa projektu** , na przykład "Azure Machine Learning", podaj identyfikator w polu **Identyfikator projektu**, wyczyść pole wyboru **publiczny** , jeśli chcesz, a następnie wybierz pozycję **Importuj**.

    ![Zaimportuj przykład Azure Machine Learning notesu do konta notesów](media/azureml-import-project.png)

1. Po minucie lub dwóch Azure Notebooks automatycznie przeprowadzisz do pulpitu nawigacyjnego nowego projektu.

## <a name="run-a-sample-notebook"></a>Uruchamianie przykładowego notesu

1. Wybierz **00-Configuration. ipynb** , aby uruchomić sekcję konfiguracyjną notesu, i postępuj zgodnie z instrukcjami, aby utworzyć obszar roboczy usługi Azure Machine Learning.

    - Ponieważ Azure Notebooks zawiera już wymagane pakiety języka Python, można po prostu uruchomić fragment kodu w kroku 2 wymagań wstępnych, aby zweryfikować wersję zestawu SDK usługi Azure ML.

1. Po zakończeniu konfiguracji wybierz pozycję **01. wprowadzenie** do otwarcia folderu zawierającego trzynastie różnych przykładowych notesów, z których każdy nie ma wyjaśnień.

## <a name="next-steps"></a>Następne kroki

Dokumentacja Azure Machine Learning zawiera różne inne zasoby, które przeprowadzą Cię przez proces pracy z Machine Learning w notesach:

- [Szybki Start: wprowadzenie do Azure Machine Learning przy użyciu języka Python](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Samouczek #1: uczenie modelu klasyfikacji obrazów z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Samouczek #2: Wdrażanie modelu klasyfikacji obrazów w usłudze Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Samouczek: uczenie modelu klasyfikacji przy użyciu automatycznego uczenia maszynowego w Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Zapoznaj się również z dokumentacją [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
