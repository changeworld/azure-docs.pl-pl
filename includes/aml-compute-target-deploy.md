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
ms.date: 05/30/2019
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753114"
---
| Obliczeniowego elementu docelowego | Sposób użycia | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Usługa sieci web w lokalnych](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testowanie/debug | Być może | &nbsp; | Dobre dla ograniczonej testowania i rozwiązywania problemów. Przyspieszanie sprzętowe zależy od tego, przy użyciu bibliotek w systemie lokalnym.
| [Usługa Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Tak](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Dobre dla wdrożeń produkcyjnych w dużej skali. Oferuje automatyczne skalowanie i krótszych czasów reakcji.  AKS to jedyna opcja dostępna dla interfejsu wizualnego. |
| [Usługa Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testowych lub deweloperskich | &nbsp;  | &nbsp; | Rozwiązanie dobre dla niskiej skalowalności, na podstawie procesora CPU obciążeń wymagających < 48 GB pamięci RAM |
| [Usługi Azure Machine Learning obliczeń](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Wersja zapoznawcza) Wnioskowanie usługi Batch | tak | &nbsp;  | Uruchom wsadowego oceniania na bezserwerowe środowisko obliczeniowe. Obsługuje maszyny wirtualne normalnych i o niskim priorytecie. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Wersja zapoznawcza) Moduł IoT |  &nbsp; | &nbsp; | Wdrażanie i obsługiwać modele uczenia Maszynowego na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | za pomocą usługi IoT Edge |  &nbsp; | tak | Wdrażanie i obsługiwać modele uczenia Maszynowego na urządzeniach IoT. |