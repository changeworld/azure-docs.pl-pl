---
title: Wdrażanie modelu usługi Azure Machine Learning do urządzenia z usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano, jak modele usługi Azure Machine Learning można wdrożyć na urządzeniach z usługą Azure IoT Edge.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 7322b07740d5dec85b6217e122fb262647527c96
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258412"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Wdrażanie modelu usługi Azure Machine Learning do urządzenia z usługi Azure IoT Edge

Modele usługi Azure Machine Learning można kontenerowych nimi, jako usług sieci web opartych na platformy Docker. Usługa Azure IoT Edge umożliwia wdrażanie kontenerów zdalnie na urządzeniach. Korzystać z tych usług ze sobą na uruchamianie modeli na urządzeniach brzegowych, aby uzyskać krótszy czas reakcji i mniej transferu danych. 

Dodatkowe skrypty oraz instrukcje można znaleźć w [zestaw narzędzi SI dla usługi Azure IoT Edge](https://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operacjonalizowanie modelu

Moduły usługi IoT Edge platformy Azure są oparte na obrazów kontenerów. Aby wdrożyć model uczenia maszynowego na urządzeniu usługi IoT Edge, musisz utworzyć obraz platformy Docker.

Operacjonalizowanie modelu, postępując zgodnie z instrukcjami wyświetlanymi w [wdrożenia usługi internetowej Azure Machine Learning modelu zarządzania](model-management-service-deploy.md) do tworzenia obrazu platformy Docker zawierający model.

## <a name="deploy-to-azure-iot-edge"></a>Wdrażanie usługi Azure IoT Edge

Po utworzeniu obrazu modelu, można wdrożyć dowolne urządzenie z usługi Azure IoT Edge. Wszystkie modele usługi Machine Learning można uruchomić na urządzeniach IoT Edge. 

### <a name="set-up-an-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Dokumentacja usługi Azure IoT Edge umożliwia przygotowanie urządzenie. 

1. [Rejestrowanie urządzenia z usługą Azure IoT Hub](../../iot-edge/how-to-register-device-portal.md). Dane wyjściowe tego procesów są parametry połączenia, które umożliwiają skonfigurowanie urządzenia fizycznego. 
2. Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzenie fizyczne i skonfigurować go przy użyciu parametrów połączenia. Środowisko uruchomieniowe można zainstalować na [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) lub [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) urządzeń.  


### <a name="find-the-machine-learning-container-image-location"></a>Znajdź lokalizację obrazu kontenera usługi Machine Learning
Należy lokalizacji obraz kontenera usługi Machine Learning. Aby znaleźć lokalizację obrazu kontenera:

1. Zaloguj się do witryny [Azure Portal](http://portal.azure.com/).
2. W **usługi Azure Container Registry**, wybierz rejestru chcesz sprawdzić.
3. W rejestrze, kliknij przycisk **repozytoriów** umożliwia wyświetlenie listy wszystkich repozytoriów i obrazów.

Podczas przeglądania usługi container registry w witrynie Azure portal, Pobierz poświadczenia rejestru kontenerów. Te poświadczenia należy nadać urządzenia usługi IoT Edge, aby je ściągnąć obraz z poziomu rejestru prywatnego. 

1. W rejestrze kontenerów, kliknij przycisk **klucze dostępu**. 
2. **Włącz** administratora, jeśli jeszcze tego nie zrobiono. 
3. Zapisz wartości **serwer logowania**, **Username**, i **hasło**. 

### <a name="deploy-the-container-image-to-your-device"></a>Wdrażanie obrazu kontenera do Twojego urządzenia

Korzystając z obrazu kontenera i poświadczenia rejestru kontenerów możesz przystąpić do wdrażania modelu na urządzeniu usługi IoT Edge uczenia maszynowego. 

Postępuj zgodnie z instrukcjami w [modułów wdrożenia usługi IoT Edge w witrynie Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) można uruchomić modelu na urządzeniu usługi IoT Edge. 











