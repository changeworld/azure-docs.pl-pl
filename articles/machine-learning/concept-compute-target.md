---
title: Co to są cele obliczeniowe
titleSuffix: Azure Machine Learning
description: Zdefiniuj, gdzie chcesz nauczyć lub wdrożyć model przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169815"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Co to są cele obliczeniowe w Azure Machine Learning? 

**Obiekt docelowy obliczeń** to wyznaczono zasób obliczeniowy/środowisko, w którym jest uruchamiany skrypt szkoleniowy lub hostowanie wdrożenia usługi. Ta lokalizacja może być maszyną lokalną lub zasobem obliczeniowym opartym na chmurze. Użycie obiektów docelowych obliczeń ułatwia późniejsze zmiany środowiska obliczeniowego bez konieczności zmiany kodu.  

W typowym cyklu projektowania modelu można:
1. Zacznij od tworzenia i eksperymentowania w niewielkiej ilości danych. Na tym etapie zalecamy środowisko lokalne (komputer lokalny lub maszyna wirtualna oparta na chmurze) jako obiekt docelowy obliczeń. 
2. Skaluj w górę do większej ilości danych lub przekształcenie rozproszone przy użyciu jednego z tych [obiektów docelowych obliczeń szkoleniowych](#train).  
3. Gdy model będzie gotowy, wdróż go w środowisku hostingu internetowego lub na urządzeniu IoT przy użyciu jednego z tych [celów obliczeniowych wdrożenia](#deploy).

Zasoby obliczeniowe używane dla obiektów docelowych obliczeń są dołączone do [obszaru roboczego](concept-workspace.md). Zasoby obliczeniowe inne niż maszyna lokalna są współużytkowane przez użytkowników obszaru roboczego.

## <a name="train"></a>Szkoleniowe cele obliczeniowe

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych.  Możesz również dołączyć własny zasób obliczeniowy, chociaż obsługa różnych scenariuszy może się różnić.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Dowiedz się więcej o [konfigurowaniu elementu docelowego obliczeń i korzystaniu z niego do uczenia modelu](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Cele wdrożenia

Następujące zasoby obliczeniowe mogą służyć do hostowania wdrożenia modelu.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Dowiedz się [, gdzie i jak wdrożyć model w miejscu docelowym obliczeń](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning COMPUTE (zarządzane)

Zarządzany zasób obliczeniowy jest tworzony i zarządzany przez Azure Machine Learning. To obliczenie jest zoptymalizowane pod kątem obciążeń uczenia maszynowego. Azure Machine Learning klastrami obliczeniowymi i [wystąpieniami obliczeniowymi](concept-compute-instance.md) są jedyne zarządzane obliczenia. W przyszłości mogą zostać dodane dodatkowe zarządzane zasoby obliczeniowe.

Możesz tworzyć Azure Machine Learning wystąpienia obliczeniowe (wersja zapoznawcza) lub Klastry obliczeniowe w programie:

| | Studio uczenia maszynowego Azure | Portalu Azure | SDK | Szablon usługi Resource Manager | Interfejs wiersza polecenia |
|---| ----- | ----- | ----- | ----- | ----- |
| Wystąpienie obliczeniowe | tak | tak | tak | tak |  |
| Klaster obliczeniowy | tak | tak | tak | tak | tak |

Po utworzeniu te zasoby obliczeniowe są automatycznie częścią obszaru roboczego w przeciwieństwie do innych rodzajów obiektów docelowych obliczeń.

### <a name="compute-clusters"></a>Klastry obliczeniowe

Azure Machine Learning klastrów obliczeniowych można użyć do szkolenia i usługi Batch inferencing (wersja zapoznawcza).  Ten zasób obliczeniowy posiada następujące:

* Klaster z jednym lub wiele węzłów
* Skalowanie automatyczne przy każdym przesyłaniu przebiegu 
* Automatyczne zarządzanie klastrami i planowanie zadań 
* Obsługa zasobów procesora CPU i procesora GPU



## <a name="unmanaged-compute"></a>Obliczenia niezarządzane

Niezarządzany cel obliczeń *nie* jest zarządzany przez Azure Machine Learning. Ten typ elementu docelowego obliczeń można utworzyć poza Azure Machine Learning, a następnie dołączyć go do obszaru roboczego. Niezarządzane zasoby obliczeniowe mogą wymagać dodatkowych czynności, aby zachować lub zwiększyć wydajność obciążeń uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Instrukcje:
* [Skonfiguruj cel obliczeń do uczenia modelu](how-to-set-up-training-targets.md)
* [Wdrażanie modelu w obiekcie docelowym obliczeń](how-to-deploy-and-where.md)