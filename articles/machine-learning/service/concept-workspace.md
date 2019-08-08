---
title: Co to jest obszar roboczy
titleSuffix: Azure Machine Learning service
description: Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning. Zachowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawek skryptów. Te informacje służą do określenia, które uruchomienie szkoleniowe produkuje najlepszy model
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: cb1fd8e98a5eba350774ff6ccb8f86dcd3e4d734
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856212"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Co to jest obszar roboczy usługi Azure Machine Learning?

Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning, co zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami tworzonymi podczas korzystania z usługi Azure Machine Learning.  Obszar roboczy zachowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawek skryptów. Te informacje służą do określenia, które uruchomienie szkoleniowe produkuje najlepszy model.  

Gdy masz model, możesz zarejestrować go w obszarze roboczym. Następnie można użyć zarejestrowanego modelu i skryptów oceniania w celu wdrożenia do Azure Container Instances, usługi Azure Kubernetes lub do nieprogramowalnej bramy polowej (FPGA) jako punkt końcowy HTTP oparty na protokole REST. Możesz również wdrożyć model na urządzeniu Azure IoT Edge jako moduł.

## <a name="taxonomy"></a>Taksonomii 

Taksonomia obszaru roboczego przedstawiono na poniższym diagramie:

[![Taksonomia obszaru roboczego](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Na diagramie przedstawiono następujące składniki obszaru roboczego:

+ Obszar roboczy może zawierać [maszyny wirtualne notesu](tutorial-1st-experiment-sdk-setup.md), zasoby w chmurze skonfigurowane ze środowiskiem Python niezbędne do uruchamiania Azure Machine Learning.
+ [Role użytkowników](how-to-assign-roles.md) umożliwiają udostępnianie obszaru roboczego innym użytkownikom, zespołom lub projektom.
+ [Cele obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) są używane do uruchamiania eksperymentów.
+ Podczas tworzenia obszaru roboczego są również tworzone [skojarzone zasoby](#resources) .
+ [Eksperymenty](concept-azure-machine-learning-architecture.md#experiments) to przebiegi szkoleniowe, które służą do kompilowania modeli.  Możesz tworzyć i uruchamiać eksperymenty za pomocą
    + [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Sekcja [zautomatyzowane eksperymenty w usłudze Machine Learning (wersja zapoznawcza)](how-to-create-portal-experiments.md) w Azure Portal.
    + [Interfejs wizualny (wersja zapoznawcza)](ui-concept-visual-interface.md).
+ [Potoki](concept-azure-machine-learning-architecture.md#ml-pipelines) to przepływy pracy wielokrotnego użytku na potrzeby szkoleń i ponownego uczenia modelu.
+ [Zestawy](concept-azure-machine-learning-architecture.md#datasets-and-datastores) danych ułatwiają zarządzanie danymi używanymi do szkolenia modelu i tworzenia potoku.
+ Po utworzeniu modelu do wdrożenia należy utworzyć zarejestrowany model.
+ Użyj zarejestrowanego modelu i skryptu oceniania, aby utworzyć [wdrożenie](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Narzędzia do interakcji z obszarem roboczym

Z obszarem roboczym można korzystać w następujący sposób:

+ W sieci Web:
    + [Azure Portal](https://portal.azure.com)
    + [Interfejs wizualny (wersja zapoznawcza)](ui-concept-visual-interface.md)
+ W języku Python przy użyciu [zestawu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ W wierszu polecenia przy użyciu [rozszerzenia CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Uczenie maszynowe za pomocą obszaru roboczego

Zadania uczenia maszynowego odczytują i/lub zapisują artefakty w obszarze roboczym. 

+ Uruchom eksperyment, aby szkolić model — zapisuje wyniki przebiegu eksperymentu w obszarze roboczym.
+ Użyj zautomatyzowanej ML do uczenia modelu — zapisuje wyniki szkolenia do obszaru roboczego.
+ Zarejestruj model w obszarze roboczym.
+ Wdrażanie modelu — program używa zarejestrowanego modelu do utworzenia wdrożenia.
+ Twórz i uruchamiaj przepływy pracy wielokrotnego użytku.
+ Wyświetlanie artefaktów uczenia maszynowego, takich jak eksperymenty, potoki, modele, wdrożenia.
+ Śledź i monitoruj modele.




## <a name="workspace-management"></a>Zarządzanie obszarem roboczym

Można również wykonać następujące zadania zarządzania obszarem roboczym:

| Zadanie zarządzania obszarem roboczym   | Portal              | SDK        | Interfejs wiersza polecenia        |
|---------------------------|------------------|------------|------------|
| Tworzenie obszaru roboczego        | **&check;**     | **&check;** | **&check;** |
| Tworzenie zasobów obliczeniowych i zarządzanie nimi    | **&check;**   | **&check;** |  **&check;**   |
| Zarządzanie dostępem do obszaru roboczego    | **&check;**   | |  **&check;**    |
| Tworzenie maszyny wirtualnej notesu | **&check;**   | |     |

### <a name='create-workspace'></a>Tworzenie obszaru roboczego

Istnieje wiele sposobów tworzenia obszaru roboczego.

* Użyj [Azure Portal](how-to-manage-workspace.md) dla interfejsu typu punkt-i-kliknięcie, aby przeprowadzić Cię przez każdy krok.
* Użyj [zestawu SDK Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) , aby utworzyć obszar roboczy na bieżąco ze skryptów języka Python lub notesów Jupiter
* Użyj [szablonu Azure Resource Manager](how-to-create-workspace-template.md) lub [interfejsu wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md) , gdy zachodzi potrzeba automatyzacji lub dostosowania tworzenia przy użyciu firmowych standardów zabezpieczeń.
* Jeśli pracujesz w Visual Studio Code, użyj [rozszerzenia vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="resources"></a>Skojarzone zasoby

Gdy tworzysz nowy obszar roboczy, automatycznie tworzy kilka zasobów platformy Azure, które są używane przez obszar roboczy:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Rejestruje kontenery platformy Docker, które są używane podczas szkoleń i podczas wdrażania modelu. Aby zminimalizować koszty, ACR jest **załadowane z opóźnieniem** do momentu utworzenia obrazów wdrożenia.
+ [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/): Jest używany jako domyślny magazyn danych dla obszaru roboczego.  Notesy Jupyter używane razem z maszynami wirtualnymi notesu są również przechowywane w tym miejscu.
+ [Application Insights platformy Azure](https://azure.microsoft.com/services/application-insights/): Przechowuje informacje o monitorowaniu dla modeli.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Przechowuje wpisy tajne, które są używane przez cele obliczeniowe i inne poufne informacje, które są niezbędne w obszarze roboczym.

> [!NOTE]
> Oprócz tworzenia nowych wersji, można również korzystać z istniejących usług platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure Machine Learning, zobacz:

+ [Przegląd usługi Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Tworzenie obszaru roboczego](how-to-manage-workspace.md)
+ [Zarządzanie obszarem roboczym](how-to-manage-workspace.md)
+ [Samouczek: trenowanie modelu](tutorial-train-models-with-aml.md)
