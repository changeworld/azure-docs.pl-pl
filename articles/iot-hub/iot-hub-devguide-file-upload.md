---
title: Zrozumienie przekazania pliku Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — użycie funkcji przekazywania plików z Centrum IoT do zarządzania przekazywania plików z urządzenia do kontenera obiektów blob magazynu Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: e16d32bdba1374540c03d1034a94192a54e6a109
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634900"
---
# <a name="upload-files-with-iot-hub"></a>Przekazywanie plików z Centrum IoT

Jak wyjaśniono w [punkty końcowe Centrum IoT] [ lnk-endpoints] artykułu, urządzenie może inicjować przekazywania pliku, wysyłając powiadomienia za pośrednictwem punktu końcowego uwzględniającym urządzenia (**/devices/ {deviceId} / pliki**). Gdy urządzenia IoT Hub powiadamia o zakończeniu przekazywania, Centrum IoT wysyła komunikat z powiadomieniem przekazywania plików za pomocą **/messages/servicebound/filenotifications** połączonej usługi punktu końcowego.

Zamiast pośrednictwa komunikaty za pośrednictwem Centrum IoT, samej siebie Centrum IoT zamiast tego działa jako dyspozytora skojarzone konto magazynu Azure. Urządzenie żądania tokenu magazynu z Centrum IoT, która jest specyficzna dla pliku, który chce przekazać urządzenia. Urządzenie korzysta z identyfikatora URI połączenia SAS można przekazać pliku do magazynu, a po zakończeniu przekazywania urządzenie wysyła powiadomienie z informacją o zakończeniu z Centrum IoT. Centrum IoT sprawdza przekazywanie pliku zostało ukończone, a następnie dodaje komunikatu powiadomienia przekazywania pliku do punktu końcowego powiadomienia pliku połączonej usługi.

Przed przekazaniem pliku do Centrum IoT z urządzenia, należy skonfigurować Centrum przez [kojarzenie magazynu Azure] [ lnk-associate-storage] konta do niego.

Urządzenia można następnie [zainicjować przekazanie] [ lnk-initialize] , a następnie [powiadomienia Centrum IoT] [ lnk-notify] po zakończeniu przekazywania. Opcjonalnie, gdy urządzenie Centrum IoT powiadamia o zakończeniu przekazywania, usługa może wygenerować [komunikatu powiadomienia][lnk-service-notification].

### <a name="when-to-use"></a>Kiedy stosować

Przekazywanie pliku używany do wysyłania plików multimedialnych i partie dużych telemetrii przekazany przez sporadycznie połączonych urządzeń lub skompresowane, aby oszczędzić przepustowość.

Zapoznaj się [wskazówki komunikację urządzenia do chmury] [ lnk-d2c-guidance] if wątpliwe między przy użyciu właściwości zgłoszone, wiadomości urządzenia do chmury lub przekazywania pliku.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Skojarz konta usługi Azure Storage z Centrum IoT

Aby korzystać z funkcji przekazywania pliku, możesz połączyć konto usługi Azure Storage w Centrum IoT. To zadanie można wykonać za pośrednictwem [portalu Azure][lnk-management-portal], lub programistycznie za pomocą [interfejsy API REST dostawcy zasobów Centrum IoT] [ lnk-resource-provider-apis]. Po powiązaniu konta usługi Azure Storage z Centrum IoT usługi zwraca identyfikator URI sygnatury dostępu Współdzielonego na urządzeniu, jeśli urządzenie inicjuje żądanie przekazywania plików.

> [!NOTE]
> [Azure IoT SDK] [ lnk-sdks] obsługiwać automatycznie podczas pobierania identyfikatora URI połączenia SAS, przekazywania pliku i powiadamiania IoT Hub przekazywanie zostało ukończone.


## <a name="initialize-a-file-upload"></a>Inicjowanie przekazywania pliku
Centrum IoT ma punkt końcowy specjalnie z myślą o urządzeniom na żądanie identyfikatora URI sygnatury dostępu Współdzielonego dla magazynu do przekazania pliku. Aby zainicjować proces przekazywania plików, urządzenie wysyła żądanie POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files` o następujących treści JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Centrum IoT zwraca następujące dane urządzenie używa można przekazać pliku:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Przestarzałe: zainicjować pobieranie przekazywania pliku

> [!NOTE]
> W tej sekcji opisano funkcje uznane za przestarzałe dla jak uzyskać identyfikator URI SAS z Centrum IoT. Użyj metody POST opisanych powyżej.

Centrum IoT ma dwa punkty końcowe REST do obsługi przekazywania pliku, go w celu uzyskania identyfikatora URI połączenia SAS dla magazynu, a drugą do powiadamiania Centrum IoT z przekazywanie zostało ukończone. Urządzenie inicjuje procesu przekazywania pliku, wysyłając GET do Centrum IoT na `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Zwraca Centrum IoT:

* Identyfikatora URI połączenia SAS określonego pliku do przekazania.
* Identyfikator korelacji ma być używany po zakończeniu przekazywania.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Powiadom Centrum IoT przekazywania pliku ukończone

Urządzenie jest odpowiedzialny za przekazywania pliku do magazynu przy użyciu zestawów SDK magazynu Azure. Po zakończeniu przekazywania urządzenie wysyła żądanie POST `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` o następujących treści JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Wartość `isSuccess` reprezentująca wartość logiczna, czy plik został załadowany pomyślnie. Kod stanu `statusCode` jest stan przekazywania plików do magazynu i `statusDescription` odpowiada `statusCode`.

## <a name="reference-topics"></a>Tematy odwołań:

Następujące tematy dokumentacji dostarczają więcej informacji na temat przekazywania plików z urządzenia.

## <a name="file-upload-notifications"></a>Powiadomienia o przekazywania plików

Opcjonalnie gdy urządzenia IoT Hub powiadamia o zakończeniu przekazywania, Centrum IoT generuje komunikat, który zawiera nazwę i magazynu lokalizację pliku.

Zgodnie z objaśnieniem w [punkty końcowe][lnk-endpoints], Centrum IoT zapewnia powiadomienia o przekazywania plików za pośrednictwem punktu końcowego usługi połączonej (**/messages/servicebound/fileuploadnotifications**) jako wiadomości. Semantyka odbierania powiadomień przekazywania plików są takie same jak w przypadku wiadomości chmury do urządzenia i tej samej [cykl życia komunikatów][lnk-lifecycle]. Każdy komunikat pobierane z punktu końcowego powiadomienia przekazywania plików jest rekordem JSON z następującymi właściwościami:

| Właściwość | Opis |
| --- | --- |
| EnqueuedTimeUtc |Sygnatura czasowa wskazująca utworzenia powiadomienia. |
| DeviceId |**DeviceId** urządzenia, które przekazać plik. |
| BlobUri |Identyfikator URI przekazanego pliku. |
| BlobName |Nazwa przekazanego pliku. |
| LastUpdatedTime |Sygnatura czasowa wskazująca, kiedy plik ostatniej aktualizacji. |
| BlobSizeInBytes |Rozmiar przekazanego pliku. |

**Przykład**. Ten przykład przedstawia treści pliku przekazać komunikat powiadomienia.

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

Każdy Centrum IoT udostępnia następujące opcje konfiguracji w pliku przekazywania powiadomień:

| Właściwość | Opis | Zakres i domyślne |
| --- | --- | --- |
| **enableFileUploadNotifications** |Określa, czy powiadomienia o przekazywania plików są zapisywane w pliku punktu końcowego powiadomienia. |Bool. Domyślnie: True. |
| **fileNotifications.ttlAsIso8601** |Domyślny czas wygaśnięcia pliku wysyłania powiadomień. |ISO_8601 interwał maksymalnie 48 H (minimalna 1 minuta). Wartość domyślna: 1 godzina. |
| **fileNotifications.lockDuration** |Czas trwania blokady do kolejki powiadomień przekazywania plików. |5 a 300 sekund (minimalna 5 sekund). Domyślnie: 60 sekund. |
| **fileNotifications.maxDeliveryCount** |Liczba maksymalna dostarczania dla pliku przekazać kolejka powiadomień. |1 do 100. Domyślnie: 100. |

## <a name="additional-reference-material"></a>Odwołanie dodatkowe materiały

Inne tematy referencyjne w Podręczniku dewelopera Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT] [ lnk-endpoints] opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji zarządzania i środowiska wykonawczego.
* [Ograniczenia przepustowości i przydziały] [ lnk-quotas] opisano przydziały i ograniczenia przepustowości zachowania, które dotyczą usługi Centrum IoT.
* [Zestawy Azure IoT urządzenia i usługi SDK] [ lnk-sdks] wymieniono języka różnych zestawów SDK, można użyć podczas opracowywania aplikacji usług i urządzeń, które współdziałają z Centrum IoT.
* [Język zapytań Centrum IoT] [ lnk-query] opisuje język kwerendy można pobrać z Centrum IoT informacji o twins urządzenia i zadania.
* [Obsługa MQTT Centrum IoT] [ lnk-devguide-mqtt] zapewnia więcej informacji na temat Centrum IoT obsługi protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się przekazywania plików z urządzeń przy użyciu Centrum IoT, mogą być zainteresowane w następujących tematach przewodnik dewelopera Centrum IoT:

* [Zarządzanie tożsamościami urządzenie w Centrum IoT][lnk-devguide-identities]
* [Kontrola dostępu do Centrum IoT][lnk-devguide-security]
* [Użyj twins urządzenia, aby zsynchronizować stanu i konfiguracji][lnk-devguide-device-twins]
* [Wywoływanie metody bezpośrednio na urządzeniu][lnk-devguide-directmethods]
* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczek Centrum IoT:

* [Sposób przekazywania plików z urządzenia do chmury z Centrum IoT][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
