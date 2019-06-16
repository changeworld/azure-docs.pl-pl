---
title: Urządzenie łączności i dane telemetryczne transferu danych przychodzących za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie sposobu urządzenie dołączyć za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 35d12d0114f9677905c85a9df94ecd074e5f8f75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60926082"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Ruch przychodzący danych telemetrycznych i łączność urządzeń

Dane telemetryczne wysyłane przez urządzenia i czujniki formularza sieci szkieletowej każde rozwiązanie IoT. Jak reprezentują tych różnych zasobów i zarządzania nimi w ramach lokalizacji są głównymi problemy podczas tworzenia aplikacji IoT. Azure cyfrowego bliźniaczych reprezentacji upraszcza tworzenie rozwiązań IoT przez łączenie urządzeń i czujników z wykresem analizy przestrzennej.

Aby rozpocząć pracę, należy utworzyć zasób usługi Azure IoT Hub w katalogu głównym przestrzenne programu graph. Zasób usługi IoT Hub umożliwia urządzeniom poniżej obszaru głównego do wysyłania wiadomości. Po utworzeniu Centrum IoT Hub rejestrować urządzenia z czujników w ramach wystąpienia Twins cyfrowych. Urządzenia mogą wysyłać dane do usługi cyfrowego Twins, za pomocą [zestaw SDK urządzeń Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Przewodnik krok po kroku dotyczące sposobu przynoszą Dołączanie urządzenia w temacie [samouczka, aby wdrożyć i skonfigurować cyfrowego Twins](tutorial-facilities-setup.md). Na pierwszy rzut oka dostępne są następujące czynności:

- Wdróż wystąpienie cyfrowego bliźniaczych elementów z [witryny Azure portal](https://portal.azure.com).
- Tworzenie miejsca do magazynowania w grafie.
- Utwórz zasób usługi IoT Hub, a następnie przypisać go do obszaru na wykresie.
- Tworzenie urządzenia i czujniki w grafie i przypisać je do miejsca do magazynowania utworzone w poprzednich krokach.
- Utwórz dopasowywania komunikaty telemetryczne w oparciu o kryteria filtrowania.
- Tworzenie [funkcji zdefiniowanej przez użytkownika](concepts-user-defined-functions.md)i przypisz go do obszaru wykresu dla niestandardowego przetwarzania komunikatów danych telemetrycznych.
- Przypisz rolę, aby umożliwić funkcji zdefiniowanej przez użytkownika uzyskać dostęp do danych programu graph.
- Pobierz parametry połączenia urządzenia usługi IoT Hub z cyfrowego interfejsów API zarządzania bliźniaczych reprezentacji.
- Konfigurowanie parametrów połączenia urządzenia na urządzeniu przy użyciu zestawu SDK urządzeń Azure IoT.

W poniższych sekcjach dowiesz się, jak pobrać parametry połączenia urządzenia usługi IoT Hub z cyfrowego interfejsu API zarządzania bliźniaczych reprezentacji. Możesz także dowiedzieć się, jak wysyłać dane telemetryczne oparte na czujników przy użyciu formatu wiadomości telemetrii usługi IoT Hub. Cyfrowy Twins wymaga każdego elementu telemetrii, że odbiera ma zostać skojarzony z czujników w ramach przestrzenne programu graph. Wymaganie to zapewnia, że dane są przetwarzane i przesyłane w ramach odpowiedniego kontekstu przestrzennych.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Pobierz parametry połączenia urządzenia usługi IoT Hub z interfejsu API zarządzania

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Czy wywołanie GET interfejsu API urządzenia za pomocą `includes=ConnectionString` parametru, aby pobrać parametry połączenia urządzenia usługi IoT Hub. Filtruj według urządzenia, GUID lub jego identyfikator sprzętu, aby znaleźć dla danego urządzenia.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_DEVICE_GUID* | Identyfikator urządzenia |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Wartość parametru | Zamień na |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Identyfikator urządzenia |

W ładunku odpowiedzi skopiuj urządzenia **connectionString** właściwości. Możesz użyć podczas wywoływania zestawu SDK w celu wysyłania danych do cyfrowego bliźniaczych reprezentacji urządzeń Azure IoT.

## <a name="device-to-cloud-message"></a>Komunikat z urządzenia do chmury

Można dostosować format wiadomości na urządzeniu oraz ładunek do potrzeb swojego rozwiązania. Użyj dowolnej kontraktu danych, który może być serializowany do tablicy bajtów lub strumień, który jest obsługiwany przez [klasy programu usługi Azure IoT Device Message klienta, komunikat (byteArray byte [])](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Może on niestandardowy format binarny, co pozwala tak długo, jak dekodowanie kontraktu danych w odpowiedniej funkcji zdefiniowanej przez użytkownika. Istnieje tylko jedno wymaganie dotyczące komunikatu urządzenia do chmury. Należy zachować zbiór właściwości, aby upewnij się, że jest właściwy aparatu przetwarzania wiadomości.

### <a name="telemetry-properties"></a>Właściwości danych telemetrycznych

 Ładunek zawartości **komunikat** mogą zawierać dowolne dane o rozmiarze do 256 KB. Istnieje kilka wymagań oczekiwany dla właściwości [ `Message.Properties` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typu. W tabeli przedstawiono właściwości wymaganych i opcjonalnych, obsługiwane przez system.

| Nazwa właściwości | Wartość | Wymagane | Opis |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Tak | Stała wartość, która identyfikuje wiadomość do systemu. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Tak | Unikatowy identyfikator czujnik, który wysyła **komunikat**. Ta wartość musi odpowiadać obiektu **HardwareId** właściwości systemu do ich przetworzenia. Na przykład `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nie | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) sformatowany ciąg daty, który identyfikuje podczas próbkowania w ładunku. Na przykład `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Nie | Identyfikator UUID, który został użyty do śledzenia zdarzeń w systemie. Na przykład `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Wysłać wiadomość do cyfrowego bliźniaczych reprezentacji

Użyj DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) lub [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) wywołania do wysyłania wiadomości do reprezentacji urządzeń cyfrowych.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o przetwarzaniu danych Twins cyfrowych platformy Azure i możliwości funkcji zdefiniowanych przez użytkownika, przeczytaj [przetwarzania danych Twins cyfrowych platformy Azure i funkcji zdefiniowanych przez użytkownika](concepts-user-defined-functions.md).
