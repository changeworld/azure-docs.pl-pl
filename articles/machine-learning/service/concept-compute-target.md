---
title: 'Celów obliczeń: gdzie uczenie i wdrażanie modeli'
titleSuffix: Azure Machine Learning service
description: Zdefiniuj, które chcesz uczenia lub wdrażania modelu za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806047"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Jakie są cele obliczeniowych w usłudze Azure Machine Learning? 

A **obliczeniowego elementu docelowego** to zasobów/środowisko obliczeniowe wyznaczonym, w której uruchomiono skrypt szkolenia lub host wdrożenia swojej usługi. Ta lokalizacja może być komputer lokalny lub zasób mocą obliczeniową opartą na chmurze. Przy użyciu obliczeniowych elementów docelowych ułatwiają można później zmienić środowisko obliczeniowe bez konieczności zmian w kodzie.  

W cykl życia typowego modelu rozwoju można:
1. Rozpocznij od tworzenia i eksperymentowania na niewielką ilość danych. Na tym etapie firma Microsoft zaleca lokalnym środowisku (komputer lokalny lub maszyny Wirtualnej w chmurze) co obliczeniowego elementu docelowego. 
2. Skalowanie w górę do większej danych lub rozproszonego szkolenia przy użyciu jednej z tych [celów obliczeń szkolenia](#train).  
3. Gdy model jest gotowy, wdrożyć ją na hosta środowiska lub urządzeń IoT za pomocą jednego z tych sieci web [celów obliczeń wdrożenia](#deploy).

Zasoby obliczeniowe używane dla obliczeniowych elementów docelowych są dołączone do [obszaru roboczego](concept-workspace.md). Obliczeniowe zasobów innych niż komputer lokalny są udostępniane przez użytkowników obszaru roboczego.

## <a name="train"></a> Szkolenie obliczeniowych elementów docelowych

Usługa Azure Machine Learning obsługuje różne w różnych zasobów obliczeniowych.  Można również dołączyć własnych zasobów obliczeniowych, mimo że obsługa mogą się różnić w różnych scenariuszach.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Dowiedz się więcej o [Konfigurowanie i używanie obliczeniowego elementu docelowego do trenowania modelu](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Cele wdrożenia

Następujące zasoby obliczeniowe może służyć do obsługi wdrożenia modelu.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Dowiedz się, [gdzie i jak wdrożyć model do obliczeniowego elementu docelowego](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Obliczeniowe platformy Azure Machine Learning (zarządzana)

Zasób obliczeniowy zarządzanych jest tworzony i zarządzany przez usługę Azure Machine Learning. Ta obliczeń jest zoptymalizowany pod kątem obciążeń learning maszyny. Usługa Azure obliczeniowego usługi Machine Learning jest tylko zarządzane zasoby obliczeniowe od 30 maja 2019 r. Dodatkowe zarządzane zasoby obliczeniowe mogą być dodawane w przyszłości.

Szkolenia oraz wnioskowania usługi batch (wersja zapoznawcza) można użyć obliczeniowego usługi Azure Machine Learning.  Z tego zasobu obliczeniowego masz:

* Klaster z jednym lub wielu node
* Skalowania każdej próbie przesłania przebiegu 
* Zarządzanie klastrem automatyczne i planowania zadań 
* Obsługa zasobów procesora CPU i procesora GPU

Można utworzyć wystąpienia obliczeniowego usługi Azure Machine Learning w witrynie Azure portal, za pomocą zestawu SDK lub przy użyciu interfejsu wiersza polecenia. Podczas tworzenia go automatycznie jest częścią obszaru roboczego w odróżnieniu od innych rodzajów obliczeniowych elementów docelowych.

## <a name="unmanaged-compute"></a>Niezarządzane obliczeń

Niezarządzane obliczeniowego elementu docelowego jest *nie* zarządzane przez usługę Azure Machine Learning. Można utworzyć tego rodzaju obliczeniowego elementu docelowego spoza usługi Azure Machine Learning, a następnie dołączyć go do obszaru roboczego. Zasoby obliczeniowe niezarządzanych może wymagać dodatkowych kroków służących do obsługi lub w celu poprawy wydajności dla obciążeń learning maszyn.

## <a name="next-steps"></a>Kolejne kroki

Instrukcje:
* [Skonfiguruj cel obliczenia na podstawie uczyć modele](how-to-set-up-training-targets.md)
* [Model jest wdrażany obliczeniowego elementu docelowego](how-to-deploy-and-where.md)