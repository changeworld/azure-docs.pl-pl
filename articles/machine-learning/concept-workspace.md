---
title: Co to jest obszar roboczy
titleSuffix: Azure Machine Learning
description: Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning. Przechowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawki skryptów. Te informacje są używane do określenia, który przebieg szkolenia tworzy najlepszy model
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505581"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co to jest obszar roboczy usługi Azure Machine Learning?

Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning, zapewniając scentralizowane miejsce do pracy ze wszystkimi artefaktami, które można utworzyć podczas korzystania z usługi Azure Machine Learning.  Obszar roboczy przechowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawki skryptów. Te informacje są używane do określenia, który przebieg szkolenia tworzy najlepszy model.  

Gdy masz model, który Ci się podoba, zarejestruj go w obszarze roboczym. Następnie można użyć zarejestrowanego modelu i oceniania skryptów do wdrożenia w wystąpieniach kontenera platformy Azure, usłudze Azure Kubernetes lub w tablicy bramy programowalnej w polu (FPGA) jako punkt końcowy HTTP oparty na rest. Można również wdrożyć model na urządzeniu usługi Azure IoT Edge jako moduł.

Ceny i dostępne funkcje zależą od tego, czy dla obszaru roboczego wybrano [wersję Podstawową czy Enterprise.](overview-what-is-azure-ml.md#sku) Wersję należy wybrać podczas [tworzenia obszaru roboczego](#create-workspace).  Można również [uaktualnić wersję](#upgrade) z wersji Basic do Enterprise.

## <a name="taxonomy"></a>Taksonomii 

Taksonomia obszaru roboczego jest zilustrowana na poniższym diagramie:

[![Taksonomia obszaru roboczego](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Na diagramie przedstawiono następujące składniki obszaru roboczego:

+ Obszar roboczy może zawierać [wystąpienia obliczeniowe usługi Azure Machine Learning](concept-compute-instance.md), zasoby w chmurze skonfigurowane ze środowiskiem Języka Python niezbędne do uruchomienia usługi Azure Machine Learning.

+ [Role użytkowników](how-to-assign-roles.md) umożliwiają udostępnianie obszaru roboczego innym użytkownikom, zespołom lub projektom.
+ [Obiekty docelowe obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) są używane do uruchamiania eksperymentów.
+ Podczas tworzenia obszaru roboczego są również tworzone [skojarzone zasoby.](#resources)
+ [Eksperymenty](concept-azure-machine-learning-architecture.md#experiments) to przebiegi szkoleniowe używane do tworzenia modeli.  
+ [Potoki](concept-azure-machine-learning-architecture.md#ml-pipelines) są wielokrotnego użytku przepływów pracy do szkolenia i przekwalifikowania modelu.
+ [Zestawy danych](concept-azure-machine-learning-architecture.md#datasets-and-datastores) pomagają w zarządzaniu danymi używanymi do szkolenia modelu i tworzenia potoku.
+ Po uzyskaniu modelu, który chcesz wdrożyć, należy utworzyć zarejestrowany model.
+ Użyj zarejestrowanego modelu i skryptu oceniania, aby utworzyć [punkt końcowy wdrożenia](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Narzędzia do interakcji z obszarem roboczym

Możesz wchodzić w interakcje z obszarem roboczym w następujący sposób:

+ W internecie:
    + [Studio usługi Azure Machine Learning](https://ml.azure.com) 
    + [Projektant usługi Azure Machine Learning (wersja zapoznawcza)](concept-designer.md) — jest dostępna tylko w obszarach roboczych [w wersji Enterprise.](overview-what-is-azure-ml.md#sku)
+ W dowolnym środowisku języka Python z [zestawem SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ W dowolnym środowisku języka R z [zestawem SDK usługi Azure Machine Learning dla języka R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ W wierszu polecenia przy użyciu [rozszerzenia interfejsu wiersza polecenia](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) usługi Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Uczenie maszynowe z obszarem roboczym

Zadania uczenia maszynowego odczytu i/lub zapisu artefaktów w obszarze roboczym.

+ Uruchom eksperyment, aby wyszkolić model - zapisuje wyniki uruchamiania eksperymentu w obszarze roboczym.
+ Użyj automatycznego uczenia ml, aby trenować model - zapisuje wyniki szkolenia w obszarze roboczym.
+ Zarejestruj model w obszarze roboczym.
+ Wdrażanie modelu — używa zarejestrowanego modelu do utworzenia wdrożenia.
+ Tworzenie i uruchamianie przepływów pracy wielokrotnegoużytnia.
+ Wyświetlanie artefaktów uczenia maszynowego, takich jak eksperymenty, potoki, modele, wdrożenia.
+ Śledzenie i monitorowanie modeli.

## <a name="workspace-management"></a>Zarządzanie obszarem roboczym

Można również wykonywać następujące zadania zarządzania obszarami roboczymi:

| Zadanie zarządzania obszarem roboczym   | Portal              | Studio | SDK Pythona / R SDK       | Interfejs wiersza polecenia        |
|---------------------------|---------|---------|------------|------------|
| Tworzenie obszaru roboczego        | **&check;**     | | **&check;** | **&check;** |
| Zarządzanie dostępem do obszaru roboczego    | **&check;**   || |  **&check;**    |
| Uaktualnienie do wersji Enterprise    | **&check;** | **&check;**  | |     |
| Tworzenie zasobów obliczeniowych i zarządzanie nimi    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Tworzenie maszyny Wirtualnej notesu |   | **&check;** | |     |

> [!WARNING]
> Przenoszenie obszaru roboczego usługi Azure Machine Learning do innej subskrypcji lub przenoszenie subskrypcji właścicielskiej do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Tworzenie obszaru roboczego

Podczas tworzenia obszaru roboczego należy zdecydować, czy ma być on utworzony w [wersji Podstawowej, czy Enterprise.](overview-what-is-azure-ml.md#sku) Wersja określa funkcje dostępne w obszarze roboczym. Między innymi wersja Enterprise zapewnia dostęp do [projektanta usługi Azure Machine Learning](concept-designer.md) i studyjnej wersji tworzenia [zautomatyzowanych eksperymentów uczenia maszynowego.](tutorial-first-experiment-automated-ml.md)  Aby uzyskać więcej informacji i informacji o cenach, zobacz [Ceny usługi Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Istnieje wiele sposobów tworzenia obszaru roboczego:  

* Użyj [witryny Azure portal](how-to-manage-workspace.md) dla interfejsu wskaż i kliknij, aby przejść przez każdy krok.
* Użyj zestawu [SDK usługi Azure Machine Learning dla języka Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) aby utworzyć obszar roboczy na bieżąco ze skryptów języka Python lub notesów Jupiter
* Użyj [szablonu usługi Azure Resource Manager](how-to-create-workspace-template.md) lub interfejsu [wiersza polecenia usługi Azure Machine Learning,](reference-azure-machine-learning-cli.md) gdy musisz zautomatyzować lub dostosować tworzenie za pomocą firmowych standardów zabezpieczeń.
* Jeśli pracujesz w programie Visual Studio Code, użyj [rozszerzenia vs kod](tutorial-setup-vscode-extension.md).

> [!NOTE]
> Nazwa obszaru roboczego jest niewrażliwa na wielkości liter.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Uaktualnienie do wersji Enterprise

Obszar [roboczy można uaktualnić z wersji Basic do Enterprise](how-to-manage-workspace.md#upgrade) za pomocą witryny Azure Portal. Nie można obniżyć obszaru roboczego wersji Enterprise do obszaru roboczego wersji podstawowej. 

## <a name="associated-resources"></a><a name="resources"></a>Skojarzone zasoby

Podczas tworzenia nowego obszaru roboczego automatycznie tworzy kilka zasobów platformy Azure, które są używane przez obszar roboczy:

+ [Usługa Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)Rejestruje kontenery dokowane używane podczas szkolenia i wdrażania modelu. Aby zminimalizować koszty, ACR jest **ładowany z opóźnieniem** do momentu utworzenia obrazów wdrażania.
+ [Konto usługi Azure Storage:](https://azure.microsoft.com/services/storage/)jest używany jako domyślny magazyn danych dla obszaru roboczego.  W tym miejscu przechowywane są również notesy jupyter, które są używane z wystąpieniami obliczeniowymi usługi Azure Machine Learning.
+ [Usługa Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)przechowuje informacje o monitorowaniu modeli.
+ [Usługa Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)przechowuje wpisy tajne, które są używane przez obiekty docelowe obliczeń i inne poufne informacje, które są potrzebne w obszarze roboczym.

> [!NOTE]
> Oprócz tworzenia nowych wersji można również korzystać z istniejących usług platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi Azure Machine Learning, zobacz:

+ [Omówienie usługi Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Tworzenie obszaru roboczego](how-to-manage-workspace.md)
+ [Zarządzanie obszarem roboczym](how-to-manage-workspace.md)
+ [Samouczek: Pierwsze rozpoczęcie tworzenia pierwszego eksperymentu uczenia maszynowego za pomocą zestawu SDK języka Python](tutorial-1st-experiment-sdk-setup.md)
+ [Samouczek: Wprowadzenie do usługi Azure Machine Learning za pomocą zestawu R SDK](tutorial-1st-r-experiment.md)
+ [Samouczek: Tworzenie pierwszego modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego](tutorial-first-experiment-automated-ml.md) (dostępne tylko w obszarach roboczych [w wersji Enterprise)](overview-what-is-azure-ml.md#sku)
+ [Samouczek: Przewidywanie ceny samochodu za pomocą projektanta](tutorial-designer-automobile-price-train-score.md) (dostępne tylko w przestrzeniach roboczych [w wersji Enterprise)](overview-what-is-azure-ml.md#sku)
