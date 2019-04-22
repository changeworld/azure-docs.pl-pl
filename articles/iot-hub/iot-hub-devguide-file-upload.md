---
title: Omówienie usługi Azure IoT Hub, przekazywanie pliku | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Użyj funkcji przekazywania plików w usłudze IoT Hub do zarządzania, przekazywanie plików z urządzenia do kontenera obiektów blob usługi Azure storage.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 217d348eacab30b90e06fe805d9cdb0cf32349ac
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050734"
---
# <a name="upload-files-with-iot-hub"></a>Przekazywanie plików za pomocą usługi IoT Hub

Jak wyjaśniono w [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md) artykułu, urządzenie można uruchomić przekazywanie pliku przez wysyłanie powiadomień za pośrednictwem punktu końcowego dostępnego z urządzenia (**/devices/ {deviceId} / pliki**). Gdy urządzenie usługi IoT Hub powiadamia o zakończeniu przekazywania, usługi IoT Hub wysyła komunikat z powiadomieniem przekazywanie plików za pośrednictwem **/messages/servicebound/filenotifications** przeznaczonych dla usług punktu końcowego.

Zamiast pośrednictwa wiadomości za pomocą samej usługi IoT Hub IoT Hub zamiast działa jako wysyłający do skojarzonego konta usługi Azure Storage. Urządzenie wymaga tokenu magazynu z usługi IoT Hub, które są specyficzne dla pliku, który chce przekazać urządzenia. Urządzenie używa identyfikatora URI sygnatury dostępu Współdzielonego można przekazać pliku do magazynu, a po zakończeniu przekazywania urządzenie wysyła powiadomienie o zakończeniu do usługi IoT Hub. Usługa IoT Hub umożliwia sprawdzenie, przekazywanie pliku zostało ukończone, a następnie dodaje komunikat z powiadomieniem przekazywania pliku, do punktu końcowego powiadomienia dla plików przeznaczonych dla usługi.

Zanim przekażesz plik do usługi IoT Hub z urządzenia należy skonfigurować Centrum przez [kojarzenie usługi Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) konta do niego.

Urządzenia można następnie [zainicjować przekazywania](iot-hub-devguide-file-upload.md#initialize-a-file-upload) i następnie [powiadomić usługi IoT hub](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) po zakończeniu przekazywania. Opcjonalnie, gdy urządzenia usługi IoT Hub powiadamia o zakończeniu przekazywania, usługa może wygenerować [wiadomość z powiadomieniem](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Kiedy stosować

Umożliwia wysyłanie plików multimedialnych i telemetrii dużych partii przekazany przez urządzenia połączone sporadycznie lub skompresować, aby oszczędzić przepustowość przekazywania plików.

Zapoznaj się [wskazówki dotyczące komunikacji urządzenia do chmury](iot-hub-devguide-d2c-guidance.md) if w stanie wątpliwości między za pomocą zgłoszonych właściwości, komunikaty z urządzenia do chmury lub przekazywanie pliku.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Skojarz konto usługi Azure Storage z usługą IoT Hub

Przy użyciu funkcji przekazywania plików, należy najpierw połączyć konto usługi Azure Storage do usługi IoT Hub. Możesz wykonać tego zadania za pomocą witryny Azure portal lub programowo za pośrednictwem [dostawcy zasobów usługi IoT Hub interfejsów API REST](/rest/api/iothub/iothubresource). Po konta usługi Azure Storage skojarzonych z Twoim Centrum IoT Hub, usługa zwraca identyfikator URI sygnatury dostępu Współdzielonego na urządzeniu, gdy urządzenie zostanie uruchomione żądanie przekazania pliku.

[Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub](iot-hub-csharp-csharp-file-upload.md) przewodniki z instrukcjami zapewniają szczegółowy przewodnik proces przekazywania plików. Te przewodniki z instrukcjami dowiesz się, jak skojarzyć konto usługi storage z usługą IoT hub za pomocą witryny Azure portal.

> [!NOTE]
> [Azure IoT SDKs](iot-hub-devguide-sdks.md) automatycznie obsługiwać podczas pobierania identyfikatora URI połączenia SAS, przekazanie pliku i powiadamianie IoT Hub przekazywanie zostało ukończone.

## <a name="initialize-a-file-upload"></a>Inicjowanie przekazywania pliku
Usługa IoT Hub ma punkt końcowy specjalnie dla żądanie identyfikatora URI sygnatury dostępu Współdzielonego dla magazynu do przekazania pliku na urządzeniach. Aby rozpocząć proces przekazywania plików, urządzenie wysyła żądanie POST `{iot hub}.azure-devices.net/devices/{deviceId}/files` za pomocą następujących treść kodu JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Usługa IoT Hub zwróci następujące dane, który jest używany do przekazania pliku:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Przestarzałe: inicjowanie przekazywania pliku pobieranie

> [!NOTE]
> W tej sekcji opisano przestarzałe funkcje dotyczące uzyskania identyfikatora URI sygnatury dostępu Współdzielonego z usługi IoT Hub. Użyj metody POST, które opisano wcześniej.

Usługa IoT Hub ma dwa punkty końcowe REST, aby obsługiwać przekazywanie pliku, je, aby uzyskać identyfikatora URI połączenia SAS dla magazynu, a druga do powiadamiania IoT hub przekazywanie zostało ukończone. Urządzenie zostanie uruchomione proces przekazywania pliku, wysyłając GET do usługi IoT hub w `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Zwraca usługę IoT hub:

* Identyfikatora URI połączenia SAS określonych w pliku do przekazania.

* Identyfikator korelacji ma być używany, gdy przekazywanie zostanie zakończone.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Powiadomienie usługi IoT Hub przekazywania pliku ukończone

Urządzenie przekazuje plik do magazynu przy użyciu zestawów SDK usługi Azure Storage. Po zakończeniu przekazywania urządzenie wysyła żądanie POST `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` za pomocą następujących treść kodu JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Wartość `isSuccess` jest wartością logiczną, wskazującą, czy plik został pomyślnie przekazany. Kod stanu `statusCode` jest jego stan w celu przekazania pliku do magazynu, a `statusDescription` odpowiada `statusCode`.

## <a name="reference-topics"></a>Tematy referencyjne:

W poniższych tematach odwołania udostępnić więcej informacji na temat przekazywania plików z urządzenia.

## <a name="file-upload-notifications"></a>Powiadomienia o przekazywania plików

Opcjonalnie gdy urządzenie usługi IoT Hub powiadamia o zakończeniu przekazywania, usługi IoT Hub generuje komunikat z powiadomieniem. Ten komunikat zawiera nazwy i magazynu lokalizację pliku.

Jak wyjaśniono w [punktów końcowych](iot-hub-devguide-endpoints.md), IoT Hub dostarczy powiadomienia o przekazywanie plików za pośrednictwem punktu końcowego usługi skierowaną (**/messages/servicebound/fileuploadnotifications**) jako komunikaty. Semantyka odbierania powiadomień przekazywania pliku są takie same jak w przypadku komunikatów z chmury do urządzeń i mają taką samą [cykl życia komunikatów](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Każdy komunikat pobierane z punktu końcowego powiadomienia przekazywania plików jest rekordem JSON z następującymi właściwościami:

| Właściwość | Opis |
| --- | --- |
| EnqueuedTimeUtc |Sygnatura czasowa wskazująca, kiedy utworzono powiadomienie. |
| DeviceId |**DeviceId** urządzenia, do którego przekazano plik. |
| BlobUri |Identyfikator URI przekazanego pliku. |
| BlobName |Nazwa przekazanego pliku. |
| LastUpdatedTime |Sygnatura czasowa wskazująca, kiedy plik ostatniej aktualizacji. |
| BlobSizeInBytes |Rozmiar przekazanego pliku. |

**Przykład**. Ten przykład przedstawia, treść pliku przekazywania wiadomości z powiadomieniem.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opcje konfiguracji powiadomień przekazywania pliku

Każde Centrum IoT hub ma następujące opcje konfiguracji dla pliku przekazywania powiadomień:

| Właściwość | Opis | Zakres i domyślne |
| --- | --- | --- |
| **enableFileUploadNotifications** |Określa, czy powiadomienia o przekazywania plików są zapisywane w pliku punktu końcowego powiadomienia. |Bool. Domyślne: True. |
| **fileNotifications.ttlAsIso8601** |Domyślny czas wygaśnięcia pliku przekazywania powiadomień. |ISO_8601 interwał maksymalnie 48 godz. (minimalna 1 minuta). Domyślne: 1 godzinę. |
| **fileNotifications.lockDuration** |Czas trwania blokady kolejki powiadomień przekazywania plików. |5-300 sekund (minimalna 5 sekund). Domyślne: 60 sekund. |
| **fileNotifications.maxDeliveryCount** |Maksymalna liczba prób dostarczenia pliku przekazać kolejka powiadomień. |1 do 100. Domyślne: 100. |

## <a name="additional-reference-material"></a>Dodatkowe materiały

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) w tym artykule opisano różne punkty końcowe Centrum IoT dla operacji zarządzania i środowiska wykonawczego.

* [Przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md) opisano limity przydziału i ograniczanie zachowań, które są stosowane do usługi IoT Hub.

* [Usługa Azure IoT usługi zestawy SDK urządzeń i](iot-hub-devguide-sdks.md) Wyświetla język różnych zestawów SDK, można użyć podczas tworzenia aplikacji usług i urządzeń, które współdziałają z usługą IoT Hub.

* [Język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md) opisuje język zapytań, można użyć, aby pobrać informacje z usługi IoT Hub o bliźniaczych reprezentacji urządzeń i zadań.

* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi usługi IoT Hub dla protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Teraz wyjaśniono sposób przekazywania plików z urządzeń przy użyciu usługi IoT Hub, mogą być zainteresowane w następujących tematach przewodnik dla deweloperów usługi IoT Hub:

* [Zarządzanie tożsamościami urządzeń w usłudze IoT Hub](iot-hub-devguide-identity-registry.md)

* [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md)

* [Przy użyciu bliźniaczych reprezentacji urządzeń do stanu i konfiguracji](iot-hub-devguide-device-twins.md)

* [Wywoływanie metody bezpośredniej o urządzeniu](iot-hub-devguide-direct-methods.md)

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki usługi IoT Hub:

* [Sposób przekazywania plików z urządzeń do chmury za pomocą usługi IoT Hub](iot-hub-csharp-csharp-file-upload.md)