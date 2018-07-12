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
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724922"
---
## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teraz, po utworzeniu centrum IoT Hub, zlokalizuj ważne informacje używane do łączenia urządzeń i aplikacji z centrum IoT Hub. 

W menu nawigacyjnym Centrum IoT Otwórz **zasady dostępu współdzielonego**.
Wybierz **iothubowner** zasad, a następnie skopiuj **parametry połączenia---klucza podstawowego** Centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do centrum IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Nie trzeba te parametry połączenia iothubowner na potrzeby tego samouczka konfiguracji. Jednak może być potrzebny w ramach innych samouczków lub inne scenariusze IoT po ukończeniu tej konfiguracji.

   ![Pobieranie parametrów połączenia centrum IoT Hub](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Rejestracja urządzenia w centrum IoT Hub dla Twojego urządzenia

1. W menu nawigacyjnym Centrum IoT Otwórz **urządzeń IoT**, następnie kliknij przycisk **Dodaj** do zarejestrowania urządzenia w usłudze IoT hub.

   ![Dodaj urządzenie na urządzeniach IoT z usługi IoT hub](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Wprowadź **identyfikator urządzenia** dla nowego urządzenia. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kliknij pozycję **Zapisz**.
5. Po utworzeniu urządzenia otwórz je z listy w **urządzeń IoT** okienka.
6. Kopiuj **parametry połączenia---klucza podstawowego** do późniejszego użycia.

   ![Pobieranie parametrów połączenia urządzenia](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
