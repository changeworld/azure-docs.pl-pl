---
title: Co to jest obszar roboczy
titleSuffix: Azure Machine Learning service
description: Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning. Zapewnia historię wszystkich przebiegów szkoleniowych, w tym dzienniki, metryki, dane wyjściowe i migawkę skryptów. Dzięki tym informacjom można ustalić, które Uruchom szkolenia tworzy najlepszy model
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 912c064fb5ca4e7ca311f60ed04a0122809cb0ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442370"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Co to jest obszar roboczy usługi Azure Machine Learning?

Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning, zapewniając scentralizowanym miejscem do pracy z wszystkich artefaktów, które możesz utworzyć, korzystając z usługi Azure Machine Learning.  Obszar roboczy przechowuje historię wszystkich przebiegów szkoleniowych, w tym dzienniki, metryki, dane wyjściowe i migawkę skryptów. Użyjesz tych informacji do określenia, które Uruchom szkolenia tworzy najlepszy model.  

Gdy model, który chcesz zarejestrować z obszarem roboczym. Następnie używasz zarejestrowanego modelu i oceniania skryptów do wdrażania w usłudze Azure Container Instances, Azure Kubernetes Service lub tablicą programowalny bramy (FPGA) jako punkt końcowy oparty na protokole REST protokołu HTTP. Można także wdrożyć model do urządzenia z usługi Azure IoT Edge jako moduł.

## <a name="taxonomy"></a>Taksonomia 

Taksonomia obszaru roboczego zostało zilustrowane na poniższym diagramie:

[![Taksonomia obszaru roboczego](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Na diagramie przedstawiono następujące składniki obszaru roboczego:

+ Obszar roboczy może zawierać [maszyn wirtualnych z notesu](quickstart-run-cloud-notebook.md), zasobów skonfigurowano środowisko Python niezbędne do uruchomienia usługi Azure Machine Learning w chmurze.
+ [Role użytkowników](how-to-assign-roles.md) umożliwiają udostępnianie obszaru roboczego innym użytkownikom, zespoły lub projekty.
+ [Celów obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) są używane do uruchamiania eksperymentów.
+ Po utworzeniu obszaru roboczego [zasoby skojarzone z](#resources) są również tworzone dla Ciebie.
+ [Eksperymenty](concept-azure-machine-learning-architecture.md#experiments) są przebiegów szkoleniowych, które umożliwia tworzenie modeli.  Możesz tworzyć i uruchamiać eksperymenty z
    + [Usługi Azure Machine Learning zestawu SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + [Zautomatyzowane eksperymenty uczenia maszynowego (wersja zapoznawcza)](how-to-create-portal-experiments.md) sekcji w witrynie Azure portal.
    + [Interfejs graficzny (wersja zapoznawcza)](ui-concept-visual-interface.md).
+ [Potoki](concept-azure-machine-learning-architecture.md#ml-pipelines) są przepływów danych wielokrotnego użytku celów szkoleniowych i ponownego szkolenia modelu.
+ [Zestawy danych](concept-azure-machine-learning-architecture.md#datasets-and-datastores) pomoc w zarządzaniu danych używasz do tworzenia potoku i szkolenie modelu.
+ Po utworzeniu modelu, który chcesz wdrożyć, utworzysz zarejestrowanego modelu.
+ Umożliwia tworzenie zarejestrowanego modelu i skrypt oceniania [wdrożenia](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Narzędzia do interakcji z obszaru roboczego

Możesz korzystać z obszarem roboczym, w następujący sposób:

+ W sieci web:
    + [Azure Portal](https://portal.azure.com)
    + [Interfejs graficzny (wersja zapoznawcza)](ui-concept-visual-interface.md)
+ W języku Python za pomocą usługi Azure Machine Learning [zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ W wierszu polecenia za pomocą usługi Azure Machine Learning [rozszerzenie interfejsu wiersza polecenia](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Uczenie maszynowe przy użyciu obszaru roboczego

Zadania uczenia maszynowego odczytu lub zapisu artefaktów do swojego obszaru roboczego. 

+ Uruchamianie eksperymentu do nauczenia modelu — operacje zapisu eksperymentów w Studio wyniki przebiegu do obszaru roboczego.
+ Użyj zautomatyzowanej uczenia Maszynowego w celu nauczenia modelu — zapisuje wyniki szkoleń do obszaru roboczego.
+ W obszarze roboczym, należy zarejestrować model.
+ Wdrażanie modelu — używa zarejestrowanego modelu w celu utworzenia wdrożenia.
+ Tworzenie i uruchamianie przepływów danych wielokrotnego użytku.
+ Wyświetl usługi machine learning artefaktów, takich jak eksperymentów, potoki, modele wdrożeń.
+ Śledzenie i monitorowanie modeli.

## <a name="workspace-management"></a>Zarządzanie obszarem roboczym

Można również wykonać następujące zadania zarządzania obszaru roboczego:

| Obszar roboczy zadania zarządzania   | Portal              | SDK        | Interfejs wiersza polecenia        |
|---------------------------|------------------|------------|------------|
| Tworzenie obszaru roboczego        | **&check;**     | **&check;** | **&check;** |
| Tworzenie i zarządzanie zasobami obliczeniowymi    | **&check;**   | **&check;** |  **&check;**   |
| Zarządzanie dostępem do obszaru roboczego    | **&check;**   | |  **&check;**    |
| Tworzenie notesu maszyny Wirtualnej | **&check;**   | |     |

Rozpoczynanie pracy z usługą przez [tworzenia obszaru roboczego](setup-create-workspace.md).

## <a name="resources"></a> Skojarzone zasoby

Gdy tworzysz nowy obszar roboczy, automatycznie tworzy kilka zasobów platformy Azure, które są używane przez obszar roboczy:

+ [Usługa Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Rejestry kontenerów platformy docker, używane podczas uczenia i wdrażania modelu. Aby zminimalizować koszty, rejestru Azure container Registry jest **załadowane z opóźnieniem** dopóki nie zostaną utworzone obrazy wdrożeń.
+ [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/): Jest używana jako magazyn danych domyślny dla obszaru roboczego.  Notesy Jupyter notebook, które są używane z maszynami wirtualnymi notesu tutaj są przechowywane także.
+ [Usługi Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Monitorowanie informacji o modelach magazynów.
+ [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Magazyny kluczy tajnych, które są używane przez obliczeniowych elementów docelowych i inne poufne informacje, które jest potrzebne przez obszar roboczy.

> [!NOTE]
> Oprócz tworzenia nowych wersji, można również użyć istniejące usługi platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz:

+ [Omówienie usługi Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Tworzenie obszaru roboczego](setup-create-workspace.md)
+ [Zarządzanie obszarem roboczym](how-to-manage-workspace.md)
+ [Samouczek: trenowanie modelu](tutorial-train-models-with-aml.md)
