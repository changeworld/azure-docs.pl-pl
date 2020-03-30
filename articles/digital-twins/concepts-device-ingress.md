---
title: Łączność z urządzeniem i dane przychodzące na urządzenia — usługi Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się, jak łączyć się, dołączać i wysyłać dane telemetryczne z urządzenia IoT w usłudze Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862479"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Ruch przychodzący danych telemetrycznych i łączność urządzeń

Dane telemetryczne wysyłane przez urządzenia i czujniki stanowią podstawę dowolnego rozwiązania IoT. Jak reprezentować te różne zasoby i zarządzać nimi w kontekście lokalizacji są główne problemy w rozwoju aplikacji IoT. Usługa Azure Digital Twins upraszcza proces opracowywania rozwiązań IoT poprzez ujednolicenie urządzeń i czujników za pomocą wykresu analizy przestrzennej.

Aby rozpocząć, utwórz zasób usługi Azure IoT Hub w katalogu głównym wykresu przestrzennego. Zasób Usługi IoT Hub umożliwia wszystkim urządzeniom pod miejscem głównym wysyłanie wiadomości. Po utworzeniu centrum IoT Hub rejestruj urządzenia za pomocą czujników w wystąpieniu Digital Twins. Urządzenia mogą wysyłać dane do usługi Digital Twins za pośrednictwem [sdk urządzenia Usługi Azure IoT.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)

Aby zapoznać się z przewodnikiem krok po kroku, jak wprowadzić urządzenia na pokład, przeczytaj [samouczek wdrażania i konfigurowania programów Digital Twins](tutorial-facilities-setup.md). Na pierwszy rzut oka kroki są następujące:

- Wdrażanie wystąpienia cyfrowych bliźniąt z [witryny Azure portal](https://portal.azure.com).
- Tworzenie spacji na wykresie.
- Utwórz zasób Usługi IoT Hub i przypisz go do miejsca na wykresie.
- Utwórz urządzenia i czujniki na wykresie i przypisz je do spacji utworzonych w poprzednich krokach.
- Utwórz matcher do filtrowania wiadomości telemetrycznych na podstawie warunków.
- Utwórz [funkcję zdefiniowaną przez użytkownika](concepts-user-defined-functions.md)i przypisz ją do spacji na wykresie w celu niestandardowego przetwarzania wiadomości telemetrycznych.
- Przypisz rolę, aby umożliwić funkcji zdefiniowanej przez użytkownika dostęp do danych wykresu.
- Pobierz ciąg połączenia urządzenia usługi IoT Hub z interfejsów API zarządzania cyfrowymi bliźniakami.
- Skonfiguruj parametry połączenia urządzenia na urządzeniu za pomocą sdk urządzenia Usługi Azure IoT.

W poniższych sekcjach dowiesz się, jak uzyskać parametry połączenia urządzenia usługi IoT Hub z interfejsu API zarządzania cyfrowymi bliźniakami. Dowiesz się również, jak używać formatu wiadomości telemetrycznych usługi IoT Hub do wysyłania danych telemetrycznych opartych na czujnikach. Cyfrowe bliźniacze wymaga, aby każdy fragment danych telemetrycznych, który otrzymuje, był skojarzony z czujnikiem w wykresie przestrzennym. To wymaganie zapewnia, że dane są przetwarzane i kierowane w odpowiednim kontekście przestrzennym.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Pobierz ciąg połączenia urządzenia usługi IoT Hub z interfejsu API zarządzania

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Wykonaj wywołanie GET w interfejsie API urządzenia z parametrem, `includes=ConnectionString` aby uzyskać parametry połączenia urządzenia usługi IoT Hub. Filtruj według identyfikatora GUID urządzenia lub identyfikatora sprzętu, aby znaleźć dane urządzenie.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_DEVICE_GUID* | Identyfikator urządzenia |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Wartość parametru | Zamień na |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Identyfikator sprzętu urządzenia |

W ładunku odpowiedzi, skopiuj urządzenie **connectionString** właściwości. Używasz go podczas wywoływania zestawu SDK urządzenia Usługi Azure IoT do wysyłania danych do obiektów Digital Twins.

## <a name="device-to-cloud-message"></a>Komunikat między urządzeniami a chmurą

Możesz dostosować format wiadomości i ładunek urządzenia do potrzeb rozwiązania. Użyj dowolnej umowy na dane, która może być serializowana do tablicy bajtowej lub strumienia obsługiwanego przez [klasę komunikatu klienta urządzenia usługi Azure IoT, Message(byte[] byteArray).](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) Wiadomość może być niestandardowy format binarny do wyboru, tak długo, jak dekodować umowy danych w odpowiedniej funkcji zdefiniowanej przez użytkownika. Istnieje tylko jedno wymaganie dla komunikatu urządzenia do chmury. Obsługa zestawu właściwości, aby upewnić się, że wiadomość jest kierowana odpowiednio do aparatu przetwarzania.

### <a name="telemetry-properties"></a>Właściwości telemetrii

 Zawartość ładunku **Message** może być dowolnych danych do 256 KB w rozmiarze. Istnieje kilka wymagań oczekiwanych dla właściwości [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typu. Tabela zawiera wymagane i opcjonalne właściwości obsługiwane przez system.

| Nazwa właściwości | Wartość | Wymagany | Opis |
|---|---|---|---|
| **DigitalTwins-Telemetria** | 1.0 | Tak | Stała wartość, która identyfikuje komunikat do systemu. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Tak | Unikatowy identyfikator czujnika, który wysyła **wiadomość**. Ta wartość musi być zgodna z właściwością **HardwareId** obiektu, aby system ją przetworzył. Na przykład `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nie | Ciąg daty w formacie [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) identyfikujący czas próbkowania ładunku. Na przykład `2018-09-20T07:35:00.8587882-07:00`. |
| **Correlationid** | `string` | Nie | UUID, który jest używany do śledzenia zdarzeń w systemie. Na przykład `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Wyślij wiadomość do Digital Twins

Użyj połączenia DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) lub [SendEventBatchAsync,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) aby wysłać wiadomość do digital twins.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o przetwarzaniu danych w usłudze Azure Digital Twins i funkcjach funkcji zdefiniowanych przez użytkownika, przeczytaj artykuł [Azure Digital Twins processing data processing and user-defined functions](concepts-user-defined-functions.md).
