---
title: Wdrażanie usługi Azure Machine Learning model na urządzenia Azure IoT | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano, jak modeli uczenia maszynowego Azure mogą być wdrażane urządzenia Azure IoT krawędzi.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: ceab96b1ef28527c8aa2692b83d3609f133f339c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Wdrażanie usługi Azure Machine Learning model na urządzenia IoT Azure

Wszystkie modele uczenia maszynowego Azure konteneryzowanych jako usługi sieci web opartych na Docker można również uruchomić na urządzenia Azure IoT krawędzi. Dodatkowe skrypty oraz instrukcje można znaleźć w [Toolkit AI Azure IoT Edge](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operacjonalizuj modelu
Operacjonalizować model zgodnie z instrukcjami w [wdrażanie usługi sieci Web zarządzania modelu Azure Machine Learning](model-management-service-deploy.md) do utworzenia obrazu Docker z modelu.

## <a name="deploy-to-azure-iot-edge"></a>Wdrażanie na krawędzi IoT Azure
Azure IoT krawędzi przenosi analizy chmury i niestandardowe reguły biznesowe na urządzeniach. Wszystkie modele uczenia maszynowego można uruchamiać na urządzenia brzegowe IoT. Dokumentacja urządzenia IoT i utworzyć wdrożenie, znajduje się w temacie [aka.ms/azure-iot — krawędzi doc](https://aka.ms/azure-iot-edge-doc).

Poniżej przedstawiono dodatkowe rzeczy do uwzględnienia.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Dodawanie poświadczeń rejestru do środowiska wykonawczego Edge na urządzeniu krawędzi
Na komputerze, na którym uruchomiane krawędzi IoT należy dodać poświadczenia rejestru, aby środowiska uruchomieniowego mogą mieć dostęp do ściągnięcia kontenera.

W systemie Windows uruchom następujące polecenie:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
W systemie Linux uruchom następujące polecenie:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Znajdź lokalizację obrazu kontenera uczenia maszynowego
Należy lokalizacji obrazu kontenera uczenia maszynowego. Aby znaleźć lokalizację kontenera obrazu:

1. Zaloguj się do [portalu Azure](http://portal.azure.com/).
2. W **rejestru kontenera Azure**, wybierz rejestru chcesz sprawdzić.
3. W rejestrze, kliknij przycisk **repozytoria** umożliwia wyświetlenie listy wszystkich repozytoria i obrazów.













