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
ms.openlocfilehash: 9f7fee71fb2b80be93d978569791dbb57f137949
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346838"
---
## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teraz, po utworzeniu centrum IoT Hub, zlokalizuj ważne informacje używane do łączenia urządzeń i aplikacji z centrum IoT Hub. 

W menu nawigacyjnym Centrum IoT Otwórz **zasady dostępu współdzielonego**. Wybierz **iothubowner** zasad, a następnie skopiuj **parametry połączenia---klucza podstawowego** Centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do centrum IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Nie trzeba parametry połączenia iothubowner na potrzeby tego samouczka konfiguracji. Jednak może być potrzebny w ramach innych samouczków lub inne scenariusze IoT po ukończeniu tej konfiguracji.

   ![Pobieranie parametrów połączenia centrum IoT Hub](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-your-device-in-the-iot-hub"></a>Zarejestruj urządzenie w usłudze IoT hub

1. W menu nawigacyjnym Centrum IoT Otwórz **urządzeń IoT**, następnie kliknij przycisk **Dodaj** do zarejestrowania urządzenia w usłudze IoT hub.

   ![Dodaj urządzenie na urządzeniach IoT z usługi IoT hub](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Wprowadź **identyfikator urządzenia** dla nowego urządzenia. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

3. Kliknij pozycję **Zapisz**.

4. Po utworzeniu urządzenia otwórz je z listy w **urządzeń IoT** okienka.

5. Kopiuj **parametry połączenia---klucza podstawowego** do późniejszego użycia.

   ![Pobieranie parametrów połączenia urządzenia](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
