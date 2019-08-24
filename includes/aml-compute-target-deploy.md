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
ms.openlocfilehash: e402989c6bdbbc90ae10047fcf19f4f2d74328a6
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014795"
---
| Obliczeniowego elementu docelowego | Użycie | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Lokalna&nbsp;usługa&nbsp;sieci Web](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testowanie/debugowanie | &nbsp; | &nbsp; | Dobre dla ograniczonego testowania i rozwiązywania problemów. Przyspieszenie sprzętowe zależy od używania bibliotek w systemie lokalnym.
| [Usługa sieci&nbsp;Web&nbsp;notesu maszyny wirtualnej](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testowanie/debugowanie | &nbsp; | &nbsp; | Dobre dla ograniczonego testowania i rozwiązywania problemów. 
| [Usługa Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Wnioskowanie w czasie rzeczywistym |  [opcję](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [opcję](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Dobre dla wdrożeń produkcyjnych w dużej skali. Zapewnia krótki czas odpowiedzi i automatyczne skalowanie wdrożonej usługi. Automatyczne skalowanie klastra nie jest obsługiwane za pomocą zestawu SDK Azure Machine Learning. Aby zmienić węzły w klastrze AKS, użyj interfejsu użytkownika klastra AKS w Azure Portal. AKS jest jedyną opcją dostępną dla interfejsu wizualizacji. |
| [Usługa Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testowanie lub programowanie | &nbsp;  | &nbsp; | Dobre w przypadku niskich skalowania, obciążeń opartych na PROCESORAch, które wymagają < 48 GB pamięci RAM |
| [Usługi Azure Machine Learning obliczeń](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Przeglądania Wnioskowanie partii&nbsp; | &nbsp; | &nbsp;  | Uruchom ocenianie wsadowe w przypadku obliczeń bezserwerowych. Obsługuje maszyny wirtualne o normalnym i niskim priorytecie. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Przeglądania Moduł&nbsp;IoT |  &nbsp; | &nbsp; | Wdrażaj & obsłużyć modele ML na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | za pośrednictwem IoT Edge |  &nbsp; | tak | Wdrażaj & obsłużyć modele ML na urządzeniach IoT. |

> [!NOTE]
> Podczas gdy elementy docelowe obliczeń, takie jak Local, Notes VM i Azure Machine Learning, obsługują procesor GPU na potrzeby szkoleń i eksperymentowania, użycie procesora GPU jest obsługiwane tylko w usłudze Azure Kubernetes.