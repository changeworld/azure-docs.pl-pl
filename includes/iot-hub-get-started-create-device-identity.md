---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78893097"
---
W tej sekcji używasz interfejsu wiersza polecenia platformy Azure, aby utworzyć tożsamość urządzenia dla tego artykułu. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. Otwórz usługę [Azure Cloud Shell](https://shell.azure.com/).

1. W usłudze Azure Cloud Shell uruchom następujące polecenie, aby zainstalować rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Utwórz nową tożsamość urządzenia wywołaną `myDeviceId` i pobierz ciąg połączenia urządzenia za pomocą następujących poleceń:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Zanotuj ciąg połączenia urządzenia z wyniku. Ten ciąg połączenia urządzenia jest używany przez aplikację urządzenia do łączenia się z centrum IoT Hub jako urządzenie.

<!-- images and links -->
