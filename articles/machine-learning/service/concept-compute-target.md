---
title: Celów obliczeń
titleSuffix: Azure Machine Learning service
description: Cel obliczenia umożliwia określenie zasobu obliczeniowego, w którym jest uruchamiane hosta lub skrypt szkoleniowy wdrożenia swojej usługi. Ta lokalizacja może być komputer lokalny lub zasób mocą obliczeniową opartą na chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755353"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Co to jest obliczeniowego elementu docelowego w usłudze Azure Machine Learning? 

Cel obliczenia umożliwia określenie zasobu obliczeniowego, w którym jest uruchamiane hosta lub skrypt szkoleniowy wdrożenia swojej usługi. Ta lokalizacja może być komputer lokalny lub zasób mocą obliczeniową opartą na chmurze.

Obliczeniowych elementów docelowych ułatwiają Zmień środowisko obliczeniowe bez wprowadzania zmian w kodzie.  Cykl życia typowego modelu rozwoju:

* Zacznij od dev/eksperymentów na niewielkiej ilości danych. Na tym etapie firma Microsoft zaleca używanie środowiska lokalnego. Na przykład komputera lokalnego lub maszyny Wirtualnej opartej na chmurze.
* Skalowanie w górę szkolenia na większych zestawów danych lub rozproszonego szkolenia przy użyciu jednej z [cele szkoleniowe](#train).  
* Wdrażanie wielu środowisk hostingu w sieci Web lub na urządzeniach IoT przy użyciu jednej z [celów wdrażania](#deploy).

Zasoby obliczeniowe używane dla obliczeniowych elementów docelowych są dołączone do [obszaru roboczego](concept-workspace.md). Obliczeniowe zasobów innych niż komputer lokalny są udostępniane przez użytkowników obszaru roboczego.

## <a name="train"></a> Cele szkoleniowe

Usługa Azure Machine Learning obsługuje różne w różnych zasobów obliczeniowych.  Można również dołączyć własnych zasobów obliczeniowych, mimo że obsługi różnych scenariuszy mogą się różnić zależnie z poniższym opisem:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Cele wdrożenia

Następujące zasoby obliczeniowe może służyć do obsługi wdrożenia modelu.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Zarządzane obliczenia

Zasób obliczeniowy zarządzanych jest tworzony i zarządzany przez usługę Azure Machine Learning. Ta obliczeń jest zoptymalizowany pod kątem obciążeń learning maszyny. Usługa Azure obliczeniowego usługi Machine Learning jest tylko zarządzane zasoby obliczeniowe od 30 maja 2019 r. Dodatkowe zarządzane zasoby obliczeniowe mogą być dodawane w przyszłości.

### <a name="amlcompute"></a> Usługi Azure Machine Learning obliczeń

Szkolenia oraz wnioskowania usługi batch (wersja zapoznawcza) można użyć obliczeniowego usługi Azure Machine Learning.  Z tego zasobu obliczeniowego masz:

* Klaster z jednym lub wielu node
* Skalowania każdej próbie przesłania przebiegu 
* Zarządzanie klastrem automatyczne i planowania zadań 
* Obsługa zasobów procesora CPU i procesora GPU

Można utworzyć wystąpienia obliczeniowego usługi Azure Machine Learning z dowolnymi z następujących czynności:

* Witryna Azure Portal
* Azure Machine Learning zestawu SDK
* Interfejs wiersza polecenia platformy Azure

Wszystkie zasoby obliczeniowe muszą być tworzone spoza obszaru roboczego i następnie jest podłączone do niego.

## <a name="unmanaged-compute"></a>Niezarządzane obliczeń

Zasób obliczeniowy niezarządzanego jest *nie* zarządzane przez usługę Azure Machine Learning. Możesz utworzyć ten typ obliczeń spoza usługi Azure Machine Learning, a następnie dołączyć go do obszaru roboczego. Zasoby obliczeniowe niezarządzanych może wymagać dodatkowych kroków służących do obsługi lub w celu poprawy wydajności dla obciążeń learning maszyn.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie celów obliczeń do trenowania modelu](how-to-set-up-training-targets.md)
* [Wdrażaj modele za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md)