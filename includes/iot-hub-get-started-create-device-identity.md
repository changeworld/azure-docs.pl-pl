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
ms.openlocfilehash: b2bce9788006a564def9bd8c1375a85dc4184b67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814750"
---
## <a name="create-a-device-identity"></a>Tworzenie tożsamości urządzenia

W tej sekcji umożliwia wiersza polecenia platformy Azure tworzenie tożsamości urządzenia w ramach tego samouczka. Wiersza polecenia platformy Azure jest preinstalowany w [usługi Azure Cloud Shell](~/articles/cloud-shell/overview.md), możesz też [instalowanie interfejsu wiersza polecenia Azure lokalnie](/cli/azure/install-azure-cli). W identyfikatorach urządzeń jest uwzględniana wielkość liter.

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
