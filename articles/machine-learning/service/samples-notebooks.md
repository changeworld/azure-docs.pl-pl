---
title: Przykładowe notesy Jupyter
titleSuffix: Azure Machine Learning service
description: Znajdź przykładowe notesy Jupyter Notebook i skorzystaj z nich, aby poznać usługę Azure Machine Learning przy użyciu języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035367"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Korzystanie z notesów Jupyter Notebook w celu eksplorowania usługi Azure Machine Learning

Dla Twojej wygody opracowaliśmy szereg notesów Jupyter Notebook w języku Python, których możesz użyć do zapoznania się z usługą Azure Machine Learning. 

Dowiedz się, jak używać usługi z dokumentacji znajdującej się w tej witrynie, oraz jak dostosować notesy do danej sytuacji. 

Aby uruchomić serwer notesów przy użyciu tych przykładowych notesów, użyj jednej z poniższych ścieżek.  Gdy serwer zostanie już uruchomiony, znajdź notesy samouczków w folderze **tutorials** lub odkryj różne funkcje w folderze **how-to-use-azureml**.

## <a name="a-managed-cloud-notebook-server"></a>Serwer notesu zarządzana usługa w chmurze

To ułatwia rozpoczęcie pracy z serwerem oparte na chmurze notesu. Przykładowy Notes i [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) są już zainstalowane i skonfigurowane dla Ciebie, po utworzeniu zasobu w chmurze.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Przykłady są dostępne na stronie sieci Web notesu.

## <a name="a-data-science-virtual-machine-dsvm"></a>Maszyna wirtualna do nauki o danych (DSVM)

W środowisku DSVM jest już zainstalowany i skonfigurowany [zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) oraz serwer notesów. 

Po [utworzeniu maszyny wirtualnej DSVM](how-to-configure-environment.md#dsvm) wykonaj następujące czynności na maszynie wirtualnej DSVM w celu uruchomienia notesów.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>Serwer notesu programu Jupyter

Wykonaj te kroki, aby utworzyć lokalny serwer notesów Jupyter Notebook na komputerze.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Instrukcje dotyczące konfigurowania zainstaluje pakietów, musisz uruchomić notesów Szybki Start i samouczków.  Inne przykładowe notesy mogą wymagać zainstalowania dodatkowych składników.  Aby uzyskać więcej informacji dotyczących tych składników, zobacz [Instalowanie zestawu SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

Notesy próbki i [Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) są już zainstalowane i skonfigurowane na [notesów usługi Azure](https://notebooks.azure.com/). Instalacja i przyszłe aktualizacje są automatycznie zarządzane za pomocą usług platformy Azure.

Użyj [witryny Azure portal](https://portal.azure.com) wprowadzenie notesy platformy Azure.  Otwórz obszar roboczy z **Przegląd** zaznacz **wprowadzenie do platformy Azure, notesy**.

## <a name="next-steps"></a>Kolejne kroki

+ Zapoznaj się z notesów próbki dla usługi Azure Machine Learning, w tym repozytorium GitHub: https://aka.ms/aml-notebooks

Skorzystaj z następujących samouczków:
+ [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

+ [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-data-prep.md)