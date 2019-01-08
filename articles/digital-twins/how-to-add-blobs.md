---
title: Jak dodać obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 9abf1eebe8174160bd671d83086ed641708b98eb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073955"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Dodaj obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure

Obiekty BLOB są bez określonej struktury reprezentacje popularnych typów plików, takich jak dzienniki i obrazy. Obiekty BLOB zachować informacje o jakiego rodzaju dane reprezentują przy użyciu typu MIME (na przykład: "image/jpeg") i metadanych (nazwa, opis, typ i tak dalej).

Platforma Azure obsługuje cyfrowego Twins dołączanie obiektów blob na urządzeniach, miejsca do magazynowania i użytkowników. Obiekty BLOB mogą reprezentować obraz profilu użytkownika, zdjęcia urządzenia, wideo, mapy, zip oprogramowania układowego, dane JSON, do dziennika itp.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Przekazywanie obiektów blob: omówienie

Wieloczęściowy żądań służy do przekazywania obiektów blob do określonych punktów końcowych i ich odpowiednie funkcje.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadane obiektu blob

Oprócz **Content-Type** i **Content-Disposition**, żądania wieloczęściowego obiektu blob Azure cyfrowego bliźniaczych reprezentacji należy określić prawidłowe treść kodu JSON. Treść kodu JSON, który można przesłać zależy od rodzaju operacji żądania HTTP, która jest wykonywana.

Są cztery główne schematów JSON:

![Schematów JSON][1]

Dokumentacja programu Swagger w tym artykule opisano te schematy modelu w szczegółach.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Dowiedz się więcej o używaniu dokumentację referencyjną, zapoznając się [sposób używania struktury Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Przykłady

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Przekaż plik tekstowy jako obiekt blob i skojarzyć ją z obszarem, należy uwierzytelnionego żądania HTTP POST do:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Następujące jednostki:

```plaintext
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

| Wartość | Zamień na |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nazwa granice zawartości wieloczęściowej wiadomości |

Poniższy kod jest implementacją .NET tego samego przekazywania obiektów blob, za pomocą klasy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

W obu przykładach:

1. Sprawdź, czy nagłówki obejmują: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Sprawdź, czy treści wieloczęściowej wiadomości:

   - Pierwsza część zawiera metadane wymaganego obiektu blob.
   - Druga część zawiera plik tekstowy.

1. Sprawdź, czy plik jest dostarczany jako `Content-Type: text/plain`.

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API

W poniższych sekcjach opisano, punkty końcowe interfejsu API związanych z obiektami blob podstawowych i ich funkcje.

### <a name="devices"></a>Urządzenia

Obiekty BLOB można dołączyć do urządzenia. Na poniższej ilustracji przedstawiono strukturę Swagger dokumentacja interfejsów API zarządzania. Określa związane z urządzeniami punkty końcowe interfejsu API do użycia obiektów blob oraz wszelkie parametry ścieżki wymagane do przekazania do nich.

![Obiekty BLOB urządzenia][2]

Na przykład aby zaktualizować lub tworzenie obiektu blob, a następnie Dołącz obiekt blob do urządzenia, należy uwierzytelnionego żądania HTTP PATCH, aby:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Identyfikator żądanego obiektu blob |

Liczba pomyślnych żądań zwracają **DeviceBlob** obiekt JSON w odpowiedzi. **DeviceBlob** obiekty są zgodne z następującego schematu JSON:

| Atrybut | Typ | Opis | Przykłady |
| --- | --- | --- | --- |
| **DeviceBlobType** | Ciąg | Kategorii obiektów blob, który można dołączyć do urządzenia | `Model` i `Specification` |
| **DeviceBlobSubtype** | Ciąg | Podkategoria obiektów blob, który jest bardziej szczegółowe niż **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification`, i `FunctionalSpecification` |

> [!TIP]
> Użyj tej tabeli do obsługi danych pomyślnie zwrócone żądanie.

### <a name="spaces"></a>Spacje

Obiekty BLOB można także dołączyć do miejsca do magazynowania. Poniższa ilustracja zawiera listę wszystkich miejsca na punkty końcowe interfejsu API odpowiedzialny za obsługę obiektów blob. Wyświetla ona wszystkie parametry ścieżki do przekazania do tych punktów końcowych.

![Obiekty BLOB miejsca][3]

Na przykład aby zwrócić obiekt blob dołączonych do miejsca, należy uwierzytelnionego żądania HTTP GET do:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Identyfikator żądanego obiektu blob |

POPRAWKI zażadaj tego samego punktu końcowego opisów metadanych aktualizacji i tworzy nowe wersje obiektu blob. Żądanie HTTP jest wysyłane za pośrednictwem metody PATCH, oraz wszelkie niezbędne meta i wieloczęściowych danych formularza.

Operacje zakończone powodzeniem zwracają **SpaceBlob** obiekt, który jest zgodny ze schematem następujące. Służy do pracy z danymi zwrócone.

| Atrybut | Typ | Opis | Przykłady |
| --- | --- | --- | --- |
| **SpaceBlobType** | Ciąg | Kategoria obiektu blob, dołączonego do miejsca | `Map` i `Image` |
| **SpaceBlobSubtype** | Ciąg | Podkategoria obiektów blob, który jest bardziej szczegółowe niż **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap`, i `WayfindingMap` |

### <a name="users"></a>Użytkownicy

Obiekty BLOB można dołączyć do modeli użytkownika (na przykład, aby skojarzyć obraz profilu). Na poniższej ilustracji przedstawiono końcowych użytkownika odpowiedniego interfejsu API oraz wszelkie parametry wymagane ścieżki, takich jak `id`:

![Obiekty BLOB użytkownika][4]

Na przykład można pobrać obiektu blob dołączonych do użytkownika, należy utworzyć uwierzytelnionego żądania HTTP GET z wszelkimi danymi formularzu wymaganych do:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Identyfikator żądanego obiektu blob |

Zwrócone dane JSON (**UserBlob** obiektów) jest zgodny z następujących modeli JSON:

| Atrybut | Typ | Opis | Przykłady |
| --- | --- | --- | --- |
| **UserBlobType** | Ciąg | Kategoria obiektów blob, który można dołączyć do użytkownika | `Image` i `Video` |
| **UserBlobSubtype** |  Ciąg | Podkategoria obiektów blob, który jest bardziej szczegółowe niż **UserBlobType** | `ProfessionalImage`, `VacationImage`, i `CommercialVideo` |

## <a name="common-errors"></a>Typowe błędy

Typowym błędem jest nie zawierać prawidłowy nagłówek informacje:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat dokumentacji struktury Swagger bliźniaki cyfrowych platformy Azure, przeczytaj [użycia Azure cyfrowego Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
