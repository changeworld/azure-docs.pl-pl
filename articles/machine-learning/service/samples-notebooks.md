---
title: Przykładowe notesy Jupyter
titleSuffix: Azure Machine Learning
description: Znajdź i użyj przykładowych notesów Jupyter, aby poznać Azure Machine Learning Python for SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: dd93e4e621a4fd474283f788e2cf67b305a1be3d
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997059"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Eksplorowanie Azure Machine Learning z notesami Jupyter

[Przykładowe repozytorium Azure Machine Learning notesów](https://github.com/azure/machinelearningnotebooks) zawiera najnowsze Azure Machine Learning przykłady zestawu SDK dla języka Python. Te notesy Juypter zostały zaprojektowane, aby ułatwić zapoznanie się z zestawem SDK i służyć jako modele dla własnych projektów uczenia maszynowego.

W tym artykule pokazano, jak uzyskać dostęp do repozytorium z następujących środowisk:

- [Maszyna wirtualna w Azure Machine Learning notesu](#notebookvm)
- [Przenoszenie własnego serwera notesu](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Po sklonowaniu repozytorium znajdziesz notesy samouczków w folderze **samouczków** i w notesach specyficznych dla funkcji w folderze **How to-use-Azure** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Pobierz przykłady na Azure Machine Learning notesu maszyn wirtualnych

Najprostszym sposobem na rozpoczęcie pracy z przykładami jest ukończenie tego [samouczka: Zainstaluj środowisko i obszar](tutorial-1st-experiment-sdk-setup.md)roboczy. Po zakończeniu będziesz mieć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium. Nie trzeba pobierać ani zainstalacji.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Pobierz przykłady na serwerze notesu

Jeśli chcesz przenieść własny serwer notesu na potrzeby lokalnego tworzenia, wykonaj następujące kroki:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Te instrukcje instalują podstawowe pakiety SDK niezbędne do obsługi notesów Szybki Start i samouczków. Inne przykładowe notesy mogą wymagać zainstalowania dodatkowych składników. Aby uzyskać więcej informacji, zobacz [Instalowanie zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Pobierz przykłady w witrynie DSVM

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej zbudowany specjalnie na potrzeby analizy danych. Jeśli [UTWORZYSZ DSVM](how-to-configure-environment.md#dsvm), zestaw SDK i serwer notesu zostaną zainstalowane i skonfigurowane. Jednak nadal trzeba utworzyć obszar roboczy i sklonować przykładowe repozytorium.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [przykładowymi notesami](https://aka.ms/aml-notebooks) , aby dowiedzieć się, jakie Azure Machine Learning można wykonać, lub wypróbuj te samouczki:

- [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

- [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-auto-train-models.md)
