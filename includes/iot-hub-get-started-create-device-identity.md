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
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346197"
---
## <a name="create-a-device-identity"></a>Tworzenie tożsamości urządzenia

W tej sekcji umożliwia wiersza polecenia platformy Azure tworzenie tożsamości urządzenia w ramach tego samouczka. Interfejs wiersza polecenia platformy Azure jest preinstalowany w [usługi Azure Cloud Shell](~/articles/cloud-shell/overview.md), możesz też [zainstalować je lokalnie](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. Uruchom następujące polecenie w środowisku wiersza polecenia, w którym używasz interfejsu wiersza polecenia platformy Azure można zainstalować rozszerzenia IoT:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, użyj następującego polecenia, aby zalogować się do konta platformy Azure (Jeśli używasz powłoki Cloud, użytkownik jest zalogowany automatycznie i nie jest potrzebny do uruchomienia tego polecenia):

    ```cmd/sh
    az login
    ```

1. Na koniec Utwórz nową tożsamość urządzenia o nazwie `myDeviceId` i pobieranie parametrów połączenia urządzenia przy użyciu następujących poleceń:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Zanotuj parametry połączenia urządzenia z wyników. Te parametry połączenia urządzenia jest używany przez aplikację urządzenia połączyć się z Centrum IoT Hub jako urządzenie.

<!-- images and links -->
