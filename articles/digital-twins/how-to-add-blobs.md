---
title: Jak dodać obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Opis sposobu dodawania obiektów blob do obiektów w reprezentacji urządzeń cyfrowych platformy Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688163"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Jak dodać obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure

Obiekty BLOB są bez określonej struktury reprezentacje popularnych typów plików (takich jak dzienniki i obrazów). Obiekty BLOB zachować informacje o rodzaju danych, które reprezentują przy użyciu typu MIME (na przykład: "image/jpeg") i metadanych (nazwa, opis, typ itd.).

Twins cyfrowych platformy Azure obsługuje dołączanie obiektów blob na urządzeniach, miejsca do magazynowania i użytkowników. Obiekty BLOB mogą reprezentować obraz profilu użytkownika, zdjęcia urządzenia, wideo, mapy lub dziennika.

> [!NOTE]
> W tym artykule założono:
> * Czy wystąpienia jest poprawnie skonfigurowany do odbierania żądań interfejsu API zarządzania.
> * Czy zostało prawidłowo uwierzytelnione za pomocą klienta REST wybraną.

## <a name="uploading-blobs-an-overview"></a>Przekazywanie obiektów blob: omówienie

Wieloczęściowy żądania są używane do przekazywania obiektów blob do określonych punktów końcowych i ich odpowiednie funkcje.

> [!IMPORTANT]
> Wieloczęściowy żądania wymagają trzy podstawowe informacje. Bliźniaki cyfrowych platformy Azure:
> * A **Content-Type** nagłówka:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * A **Content-Disposition**: `form-data; name="metadata"`.
> * Zawartość pliku do przekazania.
>
> Dokładnie **Content-Type** i **Content-Disposition** mogą się różnić w zależności od scenariusza użycia.

Każde żądanie wieloczęściowego jest dzielony na wiele fragmentów. Wieloczęściowy żądań cyfrowego Twins zarządzania interfejsów API usługi Azure, są podzielone na **dwóch** (**2**) takich części:

1. Pierwsza część jest wymagana i zawiera metadane obiektu Blob, takich jak skojarzony typ MIME dla **Content-Type** i **Content-Disposition** powyżej.

1. Druga część zawiera rzeczywistą zawartość obiektu Blob (bez określonej struktury zawartość pliku).  

Żadna z dwóch części nie są wymagane dla **PATCH** żądań. Oba są wymagane dla **WPIS** lub operacji tworzenia.

### <a name="blob-metadata"></a>Metadane obiektu blob

Oprócz **Content-Type** i **Content-Disposition**, wieloczęściowego żądań, należy także określić poprawne treść kodu JSON. Treść kodu JSON, który można przesłać zależy od rodzaju wykonywanych operacji żądania HTTP.

Cztery główne schematów JSON używane są następujące:

![Obiekty BLOB miejsca][1]

Te schematy modelu zostały szczegółowo opisane w pełnej w podanej dokumentacji struktury Swagger.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Więcej informacji na temat, zapoznając się przy użyciu podanej dokumentacja [sposób używania struktury Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Przykłady

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Aby żądanie POST, która przekazuje plik tekstowy jako obiekt Blob i kojarzy ją z obszarem:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Wartość parametru | Zamień na |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Nazwa granice zawartości wieloczęściowej wiadomości |

.NET z implementacją tych samych przekazywania obiektów Blob znajduje się poniżej za pomocą klasy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API

Poniżej podano wskazówki podstawowe punkty końcowe i ich określone funkcje.

### <a name="devices"></a>Urządzenia

Obiekty BLOB można dołączyć do urządzeń. Obraz poniżej (przedstawiające strukturę Swagger dokumentacja interfejsów API zarządzania) określa związane z urządzeniami punkty końcowe interfejsu API do użycia obiektów Blob oraz wszelkie parametry ścieżki wymagane do przekazania do nich:

![Obiekty BLOB urządzenia][2]

Na przykład aby zaktualizować lub tworzenie obiektu Blob, a następnie Dołącz obiekt Blob do urządzenia, poprawki wysłaniu żądania do:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu Blob |

Liczba pomyślnych żądań zwróci obiekt DeviceBlob JSON w odpowiedzi. DeviceBlobs spełniać następujące schematu JSON:

| Atrybut | Typ | Opis | Przykłady |
| --- | --- | --- | --- |
| **DeviceBlobType** | Ciąg | Kategorii obiektów Blob, który można dołączyć do urządzenia | `Model` i `Specification` |
| **DeviceBlobSubtype*** | Ciąg | Podkategoria obiektów Blob, który jest bardziej szczegółowe niż DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification`, i `FunctionalSpecification` |

> [!TIP]
> Użyj powyższej tabeli do obsługi danych pomyślnie zwrócone żądanie.

### <a name="spaces"></a>Miejsca do magazynowania

Obiekty BLOB mogą również dołączać do miejsca do magazynowania. Na poniższej ilustracji przedstawiono wszystkie punkty końcowe interfejsu API miejsca odpowiedzialny za obsługę obiektów blob oraz wszelkie parametry ścieżki do przekazania do nich:

![Obiekty BLOB miejsca][3]

Na przykład aby zwrócić obiekt Blob dołączonych do miejsca, żądania GET do:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu Blob |

Wprowadzanie żądanie PATCH tego samego punktu końcowego pozwoli opis metadanych aktualizacji i utworzyć nową wersję obiektu Blob. Żądanie HTTP jest wykonywane przy użyciu metody PATCH oraz wszelkie wymagane metadane i wieloczęściowych danych formularza.

Operacje zakończone powodzeniem, zwróci SpaceBlob, który jest zgodny ze schematem następujące i może służyć do pracy z danymi zwrócone:

| Atrybut | Typ | Opis | Przykłady |
| --- | --- | --- | --- |
| **SpaceBlobType** | Ciąg | Kategoria obiektu Blob, dołączonego do miejsca | `Map` i `Image` |
| **SpaceBlobSubtype** | Ciąg | Podkategoria obiektów Blob, który jest bardziej szczegółowe niż SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap`, i `WayfindingMap` |

### <a name="users"></a>Użytkownicy

Obiekty BLOB mogą być dołączane do modeli użytkownika (powiedzieć, skojarz obraz profilu). Na poniższej ilustracji przedstawiono odpowiednie punkty końcowe interfejsu API użytkowników i wszystkie parametry wymagane ścieżki, takich jak `id`:

![Obiekty BLOB użytkownika][4]

Na przykład aby pobrać obiekt Blob powiązany z użytkownikiem, żądania GET z wszelkie wymagane dane formularza do:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu Blob |

Zwrócone dane JSON (UserBlobs) będzie spełniać następujące modele JSON:

| Atrybut | Typ | Opis | Przykłady |
| --- | --- | --- | --- |
| **UserBlobType** | Ciąg | Kategoria obiektów Blob, który można dołączyć do użytkownika | `Image` i `Video` |
| **UserBlobSubtype** |  Ciąg | Podkategoria obiektów Blob, który jest bardziej szczegółowe niż UserBlobType | `ProfessionalImage`, `VacationImage`, i `CommercialVideo` |

## <a name="common-errors"></a>Typowe błędy

Nie w tym poprawny nagłówek informacje:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat podane Azure cyfrowego Twins Swagger dokumentacji, przeczytaj [sposób używania cyfrowego Swagger Twins](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
