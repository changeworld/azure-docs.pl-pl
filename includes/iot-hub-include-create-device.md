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
ms.openlocfilehash: e93f78cf07cd4815e5b17ffd3953db121adb6535
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558821"
---
<!-- put the ## header in the file that includes this file -->

W tej sekcji utworzysz tożsamość urządzenia w rejestrze tożsamości w centrum IoT. Urządzenie nie może połączyć się z centrum, chyba że ma wpis w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz sekcję "Rejestr tożsamości" w [przewodniku dewelopera IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

1. W menu nawigacji w usłudze IoT Hub Otwórz pozycję **urządzenia IoT**, a następnie wybierz pozycję **Nowy** , aby dodać urządzenie do centrum IoT Hub.

    ![Tworzenie tożsamości urządzenia w portalu](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. W obszarze **Tworzenie urządzenia**Podaj nazwę nowego urządzenia, na przykład **myDeviceId**, a następnie wybierz pozycję **Zapisz**. Ta akcja tworzy tożsamość urządzenia dla Centrum IoT Hub.

   ![Dodaj nowe urządzenie](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Po utworzeniu urządzenia otwórz je z listy w okienku **urządzenia IoT** . Skopiuj **podstawowe parametry połączenia** w celu późniejszego użycia.

    ![Parametry połączenia urządzenia](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
