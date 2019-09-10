---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 29da4ab949cf38419bc7a4d0a3b5da6669441887
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847720"
---
| Obliczeniowego elementu docelowego | Używana do | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Lokalna&nbsp;usługa&nbsp;sieci Web](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów. Przyspieszenie sprzętowe zależy od użycia bibliotek w systemie lokalnym.
| [Usługa sieci&nbsp;Web&nbsp;notesu maszyny wirtualnej](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonego testowania i rozwiązywania problemów.
| [Usługa Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (wdrożenie usługi sieci Web) | [Tak](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Używany w przypadku wdrożeń produkcyjnych o dużej skali. Zapewnia krótki czas odpowiedzi i automatyczne skalowanie wdrożonej usługi. Skalowanie automatyczne klastra nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby zmienić węzły w klastrze AKS, użyj interfejsu użytkownika klastra AKS w Azure Portal. AKS jest jedyną opcją dostępną dla interfejsu wizualizacji. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testowanie lub programowanie | &nbsp;  | &nbsp; | Używany w przypadku obciążeń opartych na PROCESORAch o niskiej skali, które wymagają mniej niż 48 GB pamięci RAM. |
| [Usługi Azure Machine Learning obliczeń](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Przeglądania Wnioskowanie partii&nbsp; | [Tak](../articles/machine-learning/service/how-to-run-batch-predictions.md) (potok uczenia maszynowego) | &nbsp;  | Uruchom ocenianie wsadowe w przypadku obliczeń bezserwerowych. Obsługuje maszyny wirtualne o normalnym i niskim priorytecie. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Przeglądania Moduł&nbsp;IoT |  &nbsp; | &nbsp; | Wdrażanie i obsługiwanie modeli ML na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Za pośrednictwem IoT Edge |  &nbsp; | Tak | Wdrażanie i obsługiwanie modeli ML na urządzeniach IoT. |

> [!NOTE]
> Chociaż obiekty docelowe obliczeń, takie jak Local, Notes VM i Azure Machine Learning, obsługują procesor GPU do szkoleń i eksperymentów, użycie procesora GPU do wnioskowania __podczas wdrażania jako usługi sieci Web__ jest obsługiwane tylko w usłudze Azure Kubernetes.
>
> Użycie procesora GPU do wnioskowania, __gdy ocenianie z potokiem uczenia maszynowego__ jest obsługiwane tylko na Azure Machine Learning obliczeń.