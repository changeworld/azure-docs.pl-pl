---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 4809881276da752ac6eb08773fb8be145dc5f4c7
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49367038"
---
## <a name="create-a-device-identity"></a>Tworzenie tożsamości urządzenia

W tej sekcji użyjesz [witryny Azure portal](https://portal.azure.com) do tworzenie tożsamości urządzenia w rejestrze tożsamości w usłudze IoT hub. Urządzenie nie może połączyć się z centrum IoT, jeśli nie ma wpisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz sekcję "Identity registry" [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md) użyj **urządzeń IoT** panelu w portalu, aby wygenerować unikatowy identyfikator urządzenia i klucz urządzenia w celu zidentyfikowania się do usługi IoT Hub. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz **wszystkie zasoby** i Znajdź zasób usługi IoT hub.

1. Po otwarciu zasobu usługi IoT hub kliknij **urządzeń IoT** narzędzia, a następnie kliknij przycisk **Dodaj** u góry. 

    ![Tworzenie tożsamości urządzenia w portalu](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. Podaj nazwę dla nowego urządzenia, takie jak **myDeviceId**i kliknij przycisk **Zapisz**. Ta akcja tworzy nową tożsamość urządzenia w Centrum IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Dodaj nowe urządzenie](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

1. Na liście urządzeń kliknij nowo utworzoną urządzenia i skopiować **parametry połączenia---klucza podstawowego** do późniejszego użycia.

    ![Parametry połączenia urządzenia](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
