---
title: Co to są obiekty docelowe obliczeń
titleSuffix: Azure Machine Learning
description: Zdefiniuj miejsce, w którym chcesz trenować lub wdrażać model za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: f9ca75943eaec2ae018b54145d872fc09294035e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398180"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Co to są obiekty docelowe obliczeń w usłudze Azure Machine Learning? 

**Obiekt docelowy obliczeń** to wyznaczony zasób/środowisko obliczeniowe, w którym uruchamiasz skrypt szkoleniowy lub hostujesz wdrożenie usługi. Ta lokalizacja może być komputer lokalny lub zasób obliczeniowy oparty na chmurze. Za pomocą celów obliczeniowych ułatwić później zmienić środowisko obliczeniowe bez konieczności zmiany kodu.  

W typowym cyklu życia rozwoju modelu można:
1. Zacznij od opracowania i eksperymentowania na niewielkiej ilości danych. Na tym etapie zaleca się środowisko lokalne (komputer lokalny lub maszyna wirtualna oparta na chmurze) jako miejsce docelowe obliczeń. 
2. Skaluj do większych danych lub wykonuj szkolenia rozproszone przy użyciu jednego z tych [celów obliczeniowych szkoleniowych.](#train)  
3. Gdy model będzie gotowy, wdrożyć go w środowisku hostingu sieci Web lub urządzeniu IoT z jednym z tych [celów obliczeniowych wdrożenia.](#deploy)

Zasoby obliczeniowe używane dla celów obliczeniowych są dołączone do [obszaru roboczego](concept-workspace.md). Zasoby obliczeniowe inne niż komputer lokalny są współużytkowane przez użytkowników obszaru roboczego.

## <a name="training-compute-targets"></a><a name="train"></a>Cele obliczeń szkoleniowych

Usługa Azure Machine Learning ma różną obsługę w różnych zasobach obliczeniowych.  Można również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Dowiedz się więcej o [konfigurowaniu i używaniu celu obliczeniowego do szkolenia modelu](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Cele wdrożenia

Następujące zasoby obliczeniowe mogą służyć do hostowania wdrożenia modelu.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Dowiedz [się, gdzie i jak wdrożyć model w celu obliczeniowym.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Obliczenia usługi Azure Machine Learning (zarządzane)

Zarządzany zasób obliczeniowy jest tworzony i zarządzany przez usługę Azure Machine Learning. To obliczenie jest zoptymalizowane pod kątem obciążeń uczenia maszynowego. Klastry obliczeniowe i [wystąpienia obliczeniowe](concept-compute-instance.md) usługi Azure Machine Learning są jedynymi zarządzanymi obliczeniami. Dodatkowe zarządzane zasoby obliczeniowe mogą zostać dodane w przyszłości.

Można utworzyć wystąpienia obliczeniowe usługi Azure Machine Learning (wersja zapoznawcza) lub klastry obliczeniowe z:
* Studio uczenia maszynowego Azure
* Azure Portal
* Klasy [obliczeń](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) SDK języka Python i [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)
* [Zestaw SDK języka R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Szablon usługi Resource Manager

Można również tworzyć klastry obliczeniowe przy użyciu [rozszerzenia uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure.](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training)

Po utworzeniu te zasoby obliczeniowe są automatycznie częścią obszaru roboczego w przeciwieństwie do innych rodzajów celów obliczeniowych.

### <a name="compute-clusters"></a>Klastry obliczeniowe

Klastrów obliczeniowych usługi Azure Machine Learning można używać do szkolenia i wnioskowania wsadowego (wersja zapoznawcza).  Dzięki temu zasobowi obliczeniowego masz:

* Klaster jedno- lub wielowęzłowy
* Automatyczne skalowanie przy każdym przesłaniu biegu 
* Automatyczne zarządzanie klastrami i planowanie zadań 
* Obsługa zasobów procesora CPU i GPU



## <a name="unmanaged-compute"></a>Obliczenia niezarządzane

Niezarządzany obiekt docelowy obliczeń *nie* jest zarządzany przez usługę Azure Machine Learning. Ten typ obiektu docelowego obliczeń jest utworzony poza usługą Azure Machine Learning, a następnie dołącza go do obszaru roboczego. Niezarządzane zasoby obliczeniowe mogą wymagać dodatkowych kroków w celu utrzymania lub zwiększenia wydajności obciążeń uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Instrukcje:
* [Konfigurowanie celu obliczeniowego w celu szkolenia modelu](how-to-set-up-training-targets.md)
* [Wdrażanie modelu w celu osiągnięcia celu obliczeniowego](how-to-deploy-and-where.md)