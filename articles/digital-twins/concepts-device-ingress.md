---
title: Urządzenie łączności i dane telemetryczne transferu danych przychodzących za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie dołączania urządzeń za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324194"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Ruch przychodzący łączności i telemetrię urządzenia

Dane telemetryczne wysyłane przez urządzenia i czujniki formularza sieci szkieletowej każde rozwiązanie IoT. W efekcie reprezentujący tych różnych zasobów i zarządzanie nimi w ramach lokalizacji jest główny problem podczas tworzenia aplikacji IoT. Azure Twins cyfrowego upraszcza tworzenie rozwiązań IoT poprzez łączenie urządzeń i czujników z wykresem analizy przestrzennej.

Aby rozpocząć pracę, `IoTHub` zasobu powinny być tworzone w katalogu głównym wykres przestrzennych, dzięki czemu wszystkie urządzenia znajdujące się poniżej obszaru głównego do wysyłania wiadomości. Po utworzeniu Centrum IoT i urządzeń za pomocą czujników zostały zarejestrowane w ramach wystąpienia cyfrowego bliźniaczych reprezentacji, urządzenia mogą zaczęła wysyłać dane do usługi cyfrowego Twins, za pomocą [zestawu SDK urządzenia IoT Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Przewodnik krok po kroku dla dołączania urządzeń można znaleźć w [samouczka, aby wdrożyć i skonfigurować cyfrowego Twins](tutorial-facilities-setup.md). Na pierwszy rzut oka dostępne są następujące czynności:

- Wdróż wystąpienie Twins cyfrowych platformy Azure z [witryny Azure portal](https://portal.azure.com)
- Tworzenie miejsca do magazynowania na wykresie
- Utwórz `IoTHub` zasobów i przypisać je do obszaru na wykresie
- Tworzenie urządzenia i czujniki w grafie i przypisać je do miejsca do magazynowania utworzone w powyższych krokach
- Utwórz dopasowywania komunikaty telemetryczne w oparciu o kryteria filtrowania
- Tworzenie [ **funkcji zdefiniowanej przez użytkownika** ](concepts-user-defined-functions.md) i przypisz go do obszaru wykresu dla niestandardowego przetwarzania komunikatów danych telemetrycznych
- Przypisz rolę, aby umożliwić funkcji zdefiniowanej przez użytkownika uzyskać dostęp do danych programu graph
- Pobierz parametry połączenia urządzenia usługi IoT Hub z cyfrowego interfejsów API zarządzania bliźniaczych reprezentacji
- Konfigurowanie parametrów połączenia urządzenia na urządzeniu przy użyciu zestawu SDK urządzenia IoT platformy Azure

Poniżej dowiesz się, jak pobrać parametry połączenia urządzenia usługi IoT Hub z cyfrowego interfejsu API zarządzania Twins oraz jak wysyłać dane telemetryczne oparte na czujników przy użyciu formatu wiadomości telemetrii usługi IoT Hub. Cyfrowy Twins wymaga każdego elementu telemetrii otrzymywanych ma zostać skojarzony z czujników w ramach przestrzenne programu graph do zapewnienia bezpieczeństwa danych jest przetwarzany i przesyłane w ramach odpowiedniego kontekstu przestrzennych.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Pobierz parametry połączenia urządzenia usługi IoT Hub z interfejsu API zarządzania

Czy wywołanie GET interfejsu API urządzenia za pomocą `includes=ConnectionString` parametru, aby pobrać parametry połączenia urządzenia usługi IoT Hub. Można filtrować według Identyfikatora GUID lub sprzęt urządzenia można znaleźć dla danego urządzenia:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| `yourManagementApiUrl` | Pełna ścieżka adresu URL dla interfejsu API zarządzania |
| `yourDeviceGuid` | Identyfikator urządzenia |
| `yourDeviceHardwareId` | Identyfikator urządzenia |

W ładunku odpowiedzi skopiuj urządzenia `connectionString` właściwość, która będzie używana podczas wywoływania zestawu SDK urządzenia IoT Azure, które znajdują się na wysyłanie danych do reprezentacji urządzeń cyfrowych platformy Azure.

## <a name="device-to-cloud-message"></a>Urządzenia do chmury wiadomości

Można dostosować format wiadomości na urządzeniu oraz ładunek do potrzeb swojego rozwiązania. Możesz użyć dowolnego kontraktu danych, który może być serializowany do tablicy bajtów lub strumień, który jest obsługiwany przez [klasy programu usługi Azure IoT Device Message klienta, komunikat (byteArray byte [])](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Może on niestandardowy format binarny, co pozwala tak długo, jak dekodowanie kontraktu danych w odpowiedniej funkcji zdefiniowane przez użytkownika. Jedynym wymaganiem dla komunikatu urządzenia do chmury jest utrzymane zbiór właściwości (patrz poniżej), upewnij się, że jest właściwy aparatu przetwarzania wiadomości.

### <a name="telemetry-properties"></a>Właściwości danych telemetrycznych

Podczas gdy zawartość ładunku `Message` mogą zawierać dowolne dane, się do 256 kb, rozmiar, istnieje kilka wymagań na oczekiwany [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet). Kroki opisane poniżej odzwierciedla właściwości wymaganych i opcjonalnych, obsługiwane przez system:

| Nazwa właściwości | Wartość | Wymagane | Opis |
|---|---|---|---|
| Dane telemetryczne DigitalTwins | 1.0 | tak | Stała wartość, która identyfikuje wiadomość do systemu |
| DigitalTwins SensorHardwareId | `string(72)` | tak | Unikatowy identyfikator wysyłania czujnika `Message`. Ta wartość musi odpowiadać obiektu `HardwareId` właściwości systemu do ich przetworzenia. Na przykład: `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | nie | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) sformatowany ciąg daty identyfikowanie podczas próbkowania w ładunku. Na przykład: `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | nie | A `uuid` który może służyć do śledzenia zdarzeń w systemie. Na przykład: `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Wysyłanie komunikatu do cyfrowego bliźniaczych reprezentacji

Użyj DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) lub [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) wywołania do wysyłania komunikatu do usługi cyfrowego bliźniaczych reprezentacji.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się o Azure cyfrowego bliźniaczych reprezentacji przetwarzania danych i możliwości funkcje zdefiniowane przez użytkownika, przeczytaj [przetwarzania danych Twins cyfrowych platformy Azure i funkcji zdefiniowanych przez użytkownika](concepts-user-defined-functions.md).

