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
ms.openlocfilehash: 05082544830bad543d6743d016a91ef8565767f4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331676"
---
| Obliczeniowego elementu docelowego | Sposób użycia | Obsługa procesora GPU | Obsługa FPGA | Opis |
| ----- | ----- | ----- | ----- | ----- |
| [Lokalne&nbsp;web&nbsp;usługi](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testowanie/debug | Być może | &nbsp; | Dobre dla ograniczonej testowania i rozwiązywania problemów. Przyspieszanie sprzętowe zależy od tego, przy użyciu bibliotek w systemie lokalnym.
| [Usługa Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Wnioskowanie w czasie rzeczywistym |  [Tak](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Tak](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Dobre dla wdrożeń produkcyjnych w dużej skali. Zawiera krótki czas reakcji i automatycznym skalowaniem wdrożonej usługi. Automatyczne skalowanie klastra nie jest obsługiwany przez zestaw SDK usługi Azure Machine Learning. Aby zmienić węzłów w klastrze AKS, należy użyć interfejsu użytkownika dla klastra usługi AKS w witrynie Azure portal. AKS to jedyna opcja dostępna dla interfejsu wizualnego. |
| [Usługa Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testowych lub deweloperskich | &nbsp;  | &nbsp; | Rozwiązanie dobre dla niskiej skalowalności, na podstawie procesora CPU obciążeń wymagających < 48 GB pamięci RAM |
| [Usługi Azure Machine Learning obliczeń](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Wersja zapoznawcza) Batch&nbsp;wnioskowania | tak | &nbsp;  | Uruchom wsadowego oceniania na bezserwerowe środowisko obliczeniowe. Obsługuje maszyny wirtualne normalnych i o niskim priorytecie. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Wersja zapoznawcza) IoT&nbsp;modułu |  &nbsp; | &nbsp; | Wdrażanie i obsługiwać modele uczenia Maszynowego na urządzeniach IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | za pomocą usługi IoT Edge |  &nbsp; | tak | Wdrażanie i obsługiwać modele uczenia Maszynowego na urządzeniach IoT. |
