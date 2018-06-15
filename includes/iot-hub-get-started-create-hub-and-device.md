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
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371375"
---
## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teraz, po utworzeniu Centrum IoT, zlokalizuj ważne informacje, które są używane do łączenia urządzeń i aplikacji do Centrum IoT. 

W menu nawigacji Centrum IoT, otwórz **zasady dostępu współużytkowanego**.
Wybierz **iothubowner** zasad, a następnie skopiuj **ciąg połączenia---klucz podstawowy** Centrum IoT. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do centrum IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Nie trzeba tego ciągu połączenia iothubowner w tym samouczku konfiguracji. Jednak może być potrzebny dla niektórych samouczki lub różnych scenariuszach IoT po ukończeniu tej konfiguracji.

   ![Pobieranie parametrów połączenia centrum IoT Hub](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Rejestracja urządzenia w centrum IoT Hub dla Twojego urządzenia

1. W menu nawigacji Centrum IoT, otwórz **urządzenia IoT**, następnie kliknij przycisk **Dodaj** zarejestrować urządzenie w Centrum IoT.

   ![Dodaj urządzenie urządzenia IoT z Centrum IoT](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Wprowadź **identyfikator urządzenia** dla nowego urządzenia. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kliknij pozycję **Zapisz**.
5. Po utworzeniu urządzenia, należy otworzyć urządzenia z listy w **urządzenia IoT** okienka.
6. Kopiuj **ciąg połączenia---klucz podstawowy** do użycia w przyszłości.

   ![Pobieranie parametrów połączenia urządzenia](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
