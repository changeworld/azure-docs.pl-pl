---
title: Przykładowe notesy Jupyter
titleSuffix: Azure Machine Learning service
description: Znajdź i eksplorowanie usługi Azure Machine Learning zestawu SDK języka Python za pomocą notesów programu Jupyter w przykładzie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391785"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Korzystanie z notesów Jupyter Notebook w celu eksplorowania usługi Azure Machine Learning

[Repozytorium usługi Azure Machine Learning notesów](https://github.com/azure/machinelearningnotebooks) obejmuje najnowsze przykłady zestawu SDK usługi Azure Machine Learning języka Python. Te Juypter notebooków zapoznaj się z zestawu SDK i służyć jako modele własne projekty uczenia maszynowego.

W tym artykule pokazano, jak uzyskać dostęp do repozytorium z następujących środowiskach:

- [Notes usługi Azure Machine Learning maszyny Wirtualnej](#azure-machine-learning-notebook-vm)
- [Przenieś serwer notesu](#bring-your-own-jupyter-notebook-server)
- [Maszyna wirtualna do nauki o danych](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Gdy sklonowane repozytorium, można znaleźć samouczek notesy w **samouczki** folder i notesy specyficzne dla funkcji w **jak-to-użyj-usługi Azure ml** folderu.

## <a name="azure-machine-learning-notebook-vm"></a>Notes usługi Azure Machine Learning maszyny Wirtualnej

Najprostszym sposobem, aby rozpocząć pracę z przykładów jest realizacja [Szybki Start oparte na chmurze notesu](quickstart-run-cloud-notebook.md). Po zakończeniu będziesz mieć dedykowany serwer wstępnie załadowane z zestawu SDK i przykładowe repozytorium. Brak plików do pobrania i instalacji konieczne.

## <a name="bring-your-own-jupyter-notebook-server"></a>Przenieś serwer notesu programu Jupyter

Jeśli chcesz przenieść serwer notesu dla rozwoju lokalnych, wykonaj następujące kroki:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Te instrukcje instalacji podstawowej pakiety zestawu SDK, niezbędne do notesów Szybki Start i samouczków. Inne przykładowe notesów może wymagać zainstalowania dodatkowych składników. Aby uzyskać więcej informacji, zobacz [zainstalować zestaw SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Maszyna wirtualna do analizy danych

Maszyna wirtualna do nauki o danych (DSVM) jest dostosowany obraz maszyny Wirtualnej, przeznaczony do nauki o danych. Jeśli użytkownik [tworzenie maszyny wirtualnej DSVM](how-to-configure-environment.md#dsvm), zestaw SDK i notesu serwer są zainstalowane i skonfigurowane. Jednak nadal musisz utworzyć obszar roboczy i sklonowania przykładowego repozytorium.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

Na [notesów usługi Azure](https://notebooks.azure.com/), zestaw SDK i notesu serwer są zainstalowane i skonfigurowane. Notesy platformy Azure udostępnia środowisko notesu w pełni zarządzane, proste do eksplorowania.

Aby uzyskać dostęp do repozytorium przykładów na notesy platformy Azure, przejdź do obszaru roboczego usługi Azure Machine Learning, za pośrednictwem [witryny Azure portal](https://portal.azure.com). Z **Przegląd** zaznacz **wprowadzenie do platformy Azure, notesy**.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z [przykładowy notesów](https://aka.ms/aml-notebooks) Aby dowiedzieć się, jakie usługi Azure Machine Learning usługa może zrobić lub wypróbuj następujące samouczki:

- [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

- [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-data-prep.md)