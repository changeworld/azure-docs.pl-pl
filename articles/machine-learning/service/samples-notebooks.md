---
title: Przykładowe notesy Jupyter
titleSuffix: Azure Machine Learning service
description: Znajdź i użyj przykładowych notesów Jupyter, aby poznać zestaw SDK języka Python dla usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 7fdf9c52df502bc94519ab6c65be2e9fb546ce48
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699135"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>Eksplorowanie usługi Azure Machine Learning przy użyciu notesów Jupyter

[Przykładowe repozytorium Azure Machine Learning notesów](https://github.com/azure/machinelearningnotebooks) zawiera najnowsze Azure Machine Learning przykłady zestawu SDK dla języka Python. Te notesy Juypter zostały zaprojektowane, aby ułatwić zapoznanie się z zestawem SDK i służyć jako modele dla własnych projektów uczenia maszynowego.

W tym artykule pokazano, jak uzyskać dostęp do repozytorium z następujących środowisk:

- [Maszyna wirtualna w Azure Machine Learning notesu](#notebookvm)
- [Przenoszenie własnego serwera notesu](#byo)
- [Data Science Virtual Machine](#dsvm)
- [Azure Notebooks](#aznb)

> [!NOTE]
> Po sklonowaniu repozytorium znajdziesz notesy samouczków w folderze samouczków i w notesach specyficznych dla funkcji w folderze **How to-use-Azure** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Pobierz przykłady na Azure Machine Learning notesu maszyn wirtualnych

Najprostszym sposobem na rozpoczęcie pracy z przykładami jest ukończenie samouczka [szybkiego startu w chmurze](quickstart-run-cloud-notebook.md). Po zakończeniu będziesz mieć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium. Nie trzeba pobierać ani zainstalacji.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Pobierz przykłady na serwerze notesu

Jeśli chcesz przenieść własny serwer notesu na potrzeby lokalnego tworzenia, wykonaj następujące kroki:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Te instrukcje instalują podstawowe pakiety SDK niezbędne do obsługi notesów Szybki Start i samouczków. Inne przykładowe notesy mogą wymagać zainstalowania dodatkowych składników. Aby uzyskać więcej informacji, zobacz [Instalowanie zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Pobierz przykłady w witrynie DSVM

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej zbudowany specjalnie na potrzeby analizy danych. Jeśli [UTWORZYSZ DSVM](how-to-configure-environment.md#dsvm), zestaw SDK i serwer notesu zostaną zainstalowane i skonfigurowane. Jednak nadal trzeba utworzyć obszar roboczy i sklonować przykładowe repozytorium.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

<a name="aznb"></a>
## <a name="get-samples-on-azure-notebooks"></a>Pobierz przykłady na Azure Notebooks

Na [Azure Notebooks](https://notebooks.azure.com/)zestaw SDK i serwer notesu są zainstalowane i skonfigurowane. Azure Notebooks udostępnia w pełni zarządzane, uproszczone środowisko dla notesu do eksplorowania.

Aby uzyskać dostęp do przykładowego repozytorium na Azure Notebooks, przejdź do obszaru roboczego Azure Machine Learning za pomocą [Azure Portal](https://portal.azure.com). W sekcji **Przegląd** wybierz pozycję wprowadzenie **w Azure Notebooks**.

## <a name="next-steps"></a>Następne kroki

Zapoznaj [](https://aka.ms/aml-notebooks) się z przykładowymi notesami, aby dowiedzieć się, co może zrobić usługa Azure Machine Learning lub wypróbuj te samouczki:

- [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

- [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-data-prep.md)
