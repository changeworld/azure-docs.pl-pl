---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183330"
---
<!-- put the ## header in the file that includes this file -->

W tej sekcji utworzysz tożsamości urządzenia w rejestrze tożsamości w usłudze IoT hub. Urządzenie nie może połączyć się z centrum IoT, jeśli nie ma wpisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz sekcję "Identity registry" [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. W menu nawigacyjnym Centrum IoT Otwórz **urządzeń IoT**, a następnie wybierz **Dodaj** można zarejestrować nowego urządzenia w usłudze IoT hub.

    ![Tworzenie tożsamości urządzenia w portalu](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Podaj nazwę dla nowego urządzenia, takie jak **myDeviceId**i wybierz **Zapisz**. Ta akcja tworzy nową tożsamość urządzenia w Centrum IoT.

   ![Dodaj nowe urządzenie](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Po utworzeniu urządzenia otwórz je z listy w **urządzeń IoT** okienka. Kopiuj **parametry połączenia---klucza podstawowego** do późniejszego użycia.

    ![Parametry połączenia urządzenia](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
