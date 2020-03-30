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
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122591"
---
| Docelowy zasób obliczeniowy | Używana do | Pomoc techniczna: GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Lokalna&nbsp;usługa internetowa](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonych testów i rozwiązywania problemów. Akceleracja sprzętowa zależy od użycia bibliotek w systemie lokalnym.
| [Usługa sieci web&nbsp;&nbsp;wystąpienia obliczeń usługi azure machine learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testowanie/debugowanie | &nbsp; | &nbsp; | Służy do ograniczonych testów i rozwiązywania problemów.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (wdrożenie usługi sieci web) | [Tak](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Służy do wdrożeń produkcyjnych na dużą skalę. Zapewnia szybki czas reakcji i skalowanie automatyczne wdrożonej usługi. Skalowanie automatyczne klastra nie jest obsługiwane za pośrednictwem zestawu SDK usługi Azure Machine Learning. Aby zmienić węzły w klastrze AKS, użyj interfejsu użytkownika dla klastra AKS w witrynie Azure portal. AKS jest jedyną opcją dostępną dla projektanta. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testowanie lub opracowywanie | &nbsp;  | &nbsp; | Służy do obciążeń opartych na procesorze CPU na niskim poziomie, które wymagają mniej niż 48 GB pamięci RAM. |
| [Klaster obliczeniowy usługi Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Wersja zapoznawcza) Wnioskowanie wsadowe&nbsp; | [Tak](../articles/machine-learning/how-to-use-parallel-run-step.md) (potok uczenia maszynowego) | &nbsp;  | Uruchom punktację wsadów na obliczeniach bezserwerowych. Obsługuje maszyny wirtualne o normalnym i niskim priorytecie. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Wersja zapoznawcza) Wnioskowanie w czasie rzeczywistym | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Wersja zapoznawcza) Moduł IoT&nbsp; |  &nbsp; | &nbsp; | Wdrażanie i obsługa modeli uczenia maszynowego na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Przez IoT Edge |  &nbsp; | Tak | Wdrażanie i obsługa modeli uczenia maszynowego na urządzeniach IoT. |

> [!NOTE]
> Chociaż obiekty docelowe obliczeń, takie jak lokalne, wystąpienie obliczeń usługi Azure Machine Learning i klastry obliczeniowe usługi Azure Machine Learning obsługują procesor GPU do szkoleń i eksperymentów, przy użyciu procesora GPU do wnioskowania __po wdrożeniu jako usługa sieci web__ jest obsługiwana tylko w usłudze Azure Kubernetes.
>
> Korzystanie z procesora GPU do wnioskowania __podczas oceniania za pomocą potoku uczenia maszynowego__ jest obsługiwane tylko na platformie Azure Machine Learning Compute.