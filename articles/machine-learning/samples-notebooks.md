---
title: Przykładowe notesy Jupyter
titleSuffix: Azure Machine Learning
description: Znajdź przykładowe notesy Jupyter do eksplorowania usługi Azure Machine Learning Python dla SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 7242b82ee5c43878a33731bd1f02b685020f22b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78673617"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Poznaj usługę Azure Machine Learning za pomocą notesów Jupyter

[Przykładowe repozytorium notesów usługi Azure Machine Learning](https://github.com/azure/machinelearningnotebooks) zawiera najnowsze przykłady zestawów SDK języka Python usługi Azure Machine Learning. Te notesy Juypter zostały zaprojektowane, aby ułatwić eksplorowanie sdk i służyć jako modele dla własnych projektów uczenia maszynowego.

W tym artykule pokazano, jak uzyskać dostęp do repozytorium z następujących środowisk:

- [Wystąpienie obliczeń usługi Azure Machine Learning](#notebookvm)
- [Przynieś własny serwer notebooka](#byo)
- [Maszyna wirtualna do analizy danych](#dsvm)

> [!NOTE]
> Po sklonowaniu repozytorium w folderze samouczków znajdują się notesy **samouczków** i notesy specyficzne dla funkcji w folderze **how-to-use-azureml.**

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Pobieranie próbek z wystąpienia obliczeniowego usługi Azure Machine Learning

Najprostszym sposobem rozpoczęcia pracy z próbkami jest ukończenie [samouczka: Środowisko instalacji i obszar roboczy](tutorial-1st-experiment-sdk-setup.md). Po zakończeniu będziesz mieć serwer dedykowany notebooka wstępnie załadowany z zestawem SDK i przykładowym repozytorium. Nie jest wymagane pobieranie ani instalacja.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Pobieranie próbek na serwerze notesu

Jeśli chcesz zabrać ze sobą własny serwer notebooków do lokalnego rozwoju, wykonaj następujące kroki:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

W tych instrukcjach instaluje się podstawowe pakiety zestawu SDK niezbędne do obsługi notesów szybki start i samouczków. Inne przykładowe notesy mogą wymagać zainstalowania dodatkowych składników. Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Pobieranie próbek na dsvm

Maszyna wirtualna do nauki o danych (DSVM) to dostosowany obraz maszyny Wirtualnej stworzony specjalnie do nauki o danych. W przypadku [utworzenia systemu DSVM](how-to-configure-environment.md#dsvm)pakiet SDK i serwer notebooka zostaną zainstalowane i skonfigurowane. Jednak nadal trzeba będzie utworzyć obszar roboczy i sklonować repozytorium przykład.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [przykładowymi notesami,](https://aka.ms/aml-notebooks) aby dowiedzieć się, co może zrobić usługa Azure Machine Learning, lub wypróbuj te samouczki:

- [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

- [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-auto-train-models.md)
