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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "34371205"
---
## <a name="create-a-device-identity"></a>Tworzenie tożsamości urządzenia

W tej sekcji użyjesz [portalu Azure] [ lnk-azure-portal] do tworzenia tożsamości urządzenia w rejestrze tożsamości w Centrum IoT. Urządzenie nie może połączyć się z centrum IoT, jeśli nie ma wpisu w rejestrze tożsamości. Więcej informacji znajduje się w sekcji „Identity registry” (Rejestr tożsamości) artykułu [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera). Użyj **urządzenia IoT** panelu w portalu, aby wygenerować unikatowy identyfikator urządzenia i klucz dla urządzenia w celu używania w celu zidentyfikowania się Centrum IoT. W identyfikatorach urządzeń jest uwzględniana wielkość liter.

1. Zaloguj się w witrynie [Azure Portal][lnk-azure-portal].

1. Wybierz **wszystkie zasoby** i Znajdź zasobu Centrum IoT.

1. Po otwarciu zasobu Centrum IoT kliknij **urządzenia IoT** narzędzia, a następnie kliknij przycisk **Dodaj** u góry. 

    ![Tworzenie tożsamości urządzenia w portalu][img-add-device]

1. Podaj nazwę dla nowego urządzenia, takie jak **myDeviceId**i kliknij przycisk **zapisać**. Ta akcja tworzy nową tożsamość urządzenia Centrum IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Dodaj nowe urządzenie][img-create-device]

1. Na liście urządzeń kliknij nowo utworzonego urządzenia i skopiuj **ciąg połączenia---klucz podstawowy** do użycia w przyszłości.

    ![Ciąg połączenia urządzenia][img-connection-string]

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. Przechowuje identyfikatory urządzeń i klucze, które będą używane jako poświadczenia zabezpieczeń, oraz flagę włączone/wyłączone, która umożliwia wyłączenie dostępu do poszczególnych urządzeń. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera).

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

