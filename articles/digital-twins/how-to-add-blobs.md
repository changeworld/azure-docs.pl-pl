---
title: Jak dodać obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: b77960961a7c032faad7000f7a2ce297802a1497
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967050"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Dodaj obiekty BLOB do obiektów w reprezentacji urządzeń cyfrowych platformy Azure

Obiekty BLOB są bez określonej struktury reprezentacje popularnych typów plików, takich jak dzienniki i obrazy. Śledzenie obiektów blob, jakiego rodzaju dane reprezentują przy użyciu typu MIME (na przykład: "image/jpeg") i metadanych (nazwa, opis, typ i tak dalej).

Platforma Azure obsługuje cyfrowego Twins dołączanie obiektów blob na urządzeniach, miejsca do magazynowania i użytkowników. Obiekty BLOB mogą reprezentować obraz profilu użytkownika, zdjęcia urządzenia, wideo, mapy, zip oprogramowania układowego, dane JSON, do dziennika itp.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Przekazywanie obiektów blob — omówienie

Wieloczęściowy żądań służy do przekazywania obiektów blob do określonych punktów końcowych i ich odpowiednie funkcje.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadane obiektu blob

Oprócz **Content-Type** i **Content-Disposition**, żądania wieloczęściowego obiektu blob Azure cyfrowego bliźniaczych reprezentacji należy określić prawidłowe treść kodu JSON. Treść kodu JSON, który można przesłać zależy od rodzaju operacji żądania HTTP, która jest wykonywana.

Są cztery główne schematów JSON:

![Schematów JSON][1]

Metadane obiektu blob JSON jest zgodny z modelem następujące:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Atrybut | Type | Opis |
| --- | --- | --- |
| **parentId** | String | Jednostka nadrzędna do skojarzenia z obiektu blob z (miejsca do magazynowania, urządzeń lub użytkowników) |
| **name** |String | Przyjazne dla człowieka nazwę obiektu blob |
| **type** | String | Nie można użyć typu obiektu blob - *typu* i *typeId*  |
| **typeId** | Integer | Identyfikator typu obiektu blob — nie można użyć *typu* i *typeId* |
| **Podtyp** | String | Podtyp obiektów blob — nie można użyć *podtypu* i *subtypeId* |
| **subtypeId** | Integer | Identyfikator podtypu dla obiektów blob — nie można użyć *podtypu* i *subtypeId* |
| **description** | String | Opis dostosowanego obiektu blob |
| **sharing** | String | Czy obiekt blob może być współużytkowany — wyliczenie [`None`, `Tree`, `Global`] |

Metadane obiektu blob zawsze jest dostarczany jako pierwszy fragment z **Content-Type** `application/json` lub jako `.json` pliku. Plik danych jest dostarczany w drugim fragmentów i może być dowolnego typu MIME obsługiwane.

Dokumentacja programu Swagger w tym artykule opisano te schematy modelu w szczegółach.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Dowiedz się więcej o używaniu dokumentację referencyjną, zapoznając się [sposób używania struktury Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Dane odpowiedzi obiektów blob

Indywidualnie zwróconych obiektów blob są zgodne z następującego schematu JSON:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Atrybut | Type | Opis |
| --- | --- | --- |
| **id** | String | Unikatowy identyfikator dla obiektu blob |
| **name** |String | Przyjazne dla człowieka nazwę obiektu blob |
| **parentId** | String | Jednostka nadrzędna do skojarzenia z obiektu blob z (miejsca do magazynowania, urządzeń lub użytkowników) |
| **type** | String | Nie można użyć typu obiektu blob - *typu* i *typeId*  |
| **typeId** | Integer | Identyfikator typu obiektu blob — nie można użyć *typu* i *typeId* |
| **subtype** | String | Podtyp obiektów blob — nie można użyć *podtypu* i *subtypeId* |
| **subtypeId** | Integer | Identyfikator podtypu dla obiektów blob — nie można użyć *podtypu* i *subtypeId* |
| **sharing** | String | Czy obiekt blob może być współużytkowany — wyliczenie [`None`, `Tree`, `Global`] |
| **description** | String | Opis dostosowanego obiektu blob |
| **contentInfos** | Tablica | Określa informacje metadanych bez struktury, w tym wersja |
| **fullName** | String | Pełna nazwa obiektu blob |
| **spacePaths** | String | Ścieżka miejsca |

Metadane obiektu blob zawsze jest dostarczany jako pierwszy fragment z **Content-Type** `application/json` lub jako `.json` pliku. Plik danych jest dostarczany w drugim fragmentów i może być dowolnego typu MIME obsługiwane.

### <a name="blob-multipart-request-examples"></a>Przykłady wieloczęściowego żądania obiektu blob

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

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Na koniec [cURL](https://curl.haxx.se/) użytkownicy mogą wprowadzać żądań wieloczęściowego formularza w taki sam sposób:

![Obiekty BLOB urządzenia][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOKEN | Nieprawidłowy token OAuth 2.0 |
| YOUR_SPACE_ID | Identyfikator obszaru do skojarzenia obiekt blob z |
| ŚCIEŻKA_DO_PLIKU | Ścieżka do pliku tekstowego |

Po pomyślnym zwraca identyfikator nowego obiektu blob (wyróżniony czerwonym kolorem wcześniej).

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

Liczba pomyślnych żądań zwraca obiekt JSON jako [opisanej wcześniej](#blobModel).

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

Liczba pomyślnych żądań zwraca obiekt JSON jako [opisanej wcześniej](#blobModel).

POPRAWKI zażadaj tego samego punktu końcowego opisów metadanych aktualizacji i tworzy wersji obiektu blob. Żądanie HTTP jest wysyłane za pośrednictwem metody PATCH, oraz wszelkie niezbędne meta i wieloczęściowych danych formularza.

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

Liczba pomyślnych żądań zwraca obiekt JSON jako [opisanej wcześniej](#blobModel).

## <a name="common-errors"></a>Typowe błędy

Typowym błędem obejmuje, nie zawierającą informacje nagłówka poprawny:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Aby rozwiązać ten problem, należy sprawdzić, czy żądania ogólnego jest preferowany **Content-Type** nagłówka:

* `multipart/mixed`
* `multipart/form-data`

Ponadto upewnij się, że każdy fragment wieloczęściowego ma odpowiadające mu **Content-Type** zgodnie z potrzebami.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat dokumentacji struktury Swagger bliźniaki cyfrowych platformy Azure, przeczytaj [użycia Azure cyfrowego Twins Swagger](how-to-use-swagger.md).

- Aby przekazać obiekty BLOB za pomocą narzędzia Postman, przeczytaj [Konfigurowanie narzędzia Postman](./how-to-configure-postman.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG
