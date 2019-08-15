---
title: Jak dodać obiekty blob do obiektów w usłudze Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak dodawać obiekty blob do obiektów w usłudze Azure Digital bliźniaczych reprezentacji.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 61c09435606612377781fb382d2d31144e96b07b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965925"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Dodaj obiekty blob do obiektów w usłudze Azure Digital bliźniaczych reprezentacji

Obiekty blob są niestrukturalnymi reprezentacjami wspólnych typów plików, takich jak obrazy i dzienniki. Obiekty blob śledzą rodzaj danych, które reprezentują przy użyciu typu MIME (na przykład: "Image/JPEG") i metadanych (nazwa, opis, typ itd.).

Usługa Azure Digital bliźniaczych reprezentacji obsługuje dołączanie obiektów BLOB do urządzeń, miejsc i użytkowników. Obiekty blob mogą reprezentować obraz profilu dla użytkownika, Zdjęcie urządzenia, wideo, mapę, kod zip oprogramowania układowego, dane JSON, dziennik itp.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Omówienie przekazywania obiektów BLOB

Możesz użyć wieloczęściowych żądań, aby przekazać obiekty blob do określonych punktów końcowych i ich odpowiednich funkcji.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadane obiektu blob

Oprócz tworzenia zawartości i **usuwania zawartości**, żądania wieloczęściowych obiektów BLOB usługi Azure Digital bliźniaczych reprezentacji muszą określać poprawną treść JSON. Która treść JSON do przesłania zależy od rodzaju wykonywanej operacji żądania HTTP.

Cztery główne schematy JSON:

[![Schematy JSON](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

Metadane obiektu BLOB JSON są zgodne z następującym modelem:

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
| **parentId** | String | Jednostka nadrzędna, z którą ma zostać skojarzony obiekt BLOB (miejsca, urządzenia lub Użytkownicy) |
| **name** |String | Przyjazna dla człowieka nazwa obiektu BLOB |
| **type** | String | Typ obiektu BLOB — nie można używać *typu* i elementu *typeId*  |
| **typeId** | Liczba całkowita | Identyfikator typu obiektu BLOB — nie można używać *typu* i elementu *typeId* |
| **Podtyp** | String | Podtyp obiektu BLOB — nie można użyć *podtypu* i elementu subtypeid |
| **subtypeId** | Liczba całkowita | Identyfikator podtypu dla obiektu BLOB — nie można użyć *podtypu* i elementu subtypeid |
| **zharmonizowan** | String | Dostosowany opis obiektu BLOB |
| **sharing** | String | Czy obiekt BLOB może być współużytkowany-enum [`None`, `Tree`, `Global`] |

Metadane obiektu BLOB są zawsze dostarczane jako pierwszy fragment z **typem** `application/json` zawartości lub jako `.json` plik. Dane pliku są dostarczane w drugim fragmencie i mogą być dowolnego obsługiwanego typu MIME.

Dokumentacja struktury Swagger opisuje te schematy modelu w pełnych szczegółach.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Więcej informacji na temat korzystania z dokumentacji referencyjnej można znaleźć w temacie [jak korzystać z struktury Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Dane odpowiedzi obiektów BLOB

Pojedyncze zwrócone obiekty blob są zgodne z następującym schematem JSON:

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
| **id** | String | Unikatowy identyfikator obiektu BLOB |
| **name** |String | Przyjazna dla człowieka nazwa obiektu BLOB |
| **parentId** | String | Jednostka nadrzędna, z którą ma zostać skojarzony obiekt BLOB (miejsca, urządzenia lub Użytkownicy) |
| **type** | String | Typ obiektu BLOB — nie można używać *typu* i elementu *typeId*  |
| **typeId** | Liczba całkowita | Identyfikator typu obiektu BLOB — nie można używać *typu* i elementu *typeId* |
| **Podtyp** | String | Podtyp obiektu BLOB — nie można użyć *podtypu* i elementu subtypeid |
| **subtypeId** | Liczba całkowita | Identyfikator podtypu dla obiektu BLOB — nie można użyć *podtypu* i elementu subtypeid |
| **sharing** | String | Czy obiekt BLOB może być współużytkowany-enum [`None`, `Tree`, `Global`] |
| **zharmonizowan** | String | Dostosowany opis obiektu BLOB |
| **contentInfos** | Array | Określa informacje o metadanych bez struktury, w tym wersję |
| **fullName** | String | Pełna nazwa obiektu BLOB |
| **spacePaths** | String | Ścieżka miejsca |

Metadane obiektu BLOB są zawsze dostarczane jako pierwszy fragment z **typem** `application/json` zawartości lub jako `.json` plik. Dane pliku są dostarczane w drugim fragmencie i mogą być dowolnego obsługiwanego typu MIME.

### <a name="blob-multipart-request-examples"></a>Przykłady żądania wieloczęściowego obiektu BLOB

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Aby przekazać plik tekstowy jako obiekt BLOB i skojarzyć go z miejscem, wykonaj uwierzytelnione żądanie HTTP POST:

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

| Value | Zamień na |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nazwa granicy wieloczęściowej zawartości |

Poniższy kod jest implementacją platformy .NET tego samego przekazywania obiektów BLOB przy użyciu klasy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

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

Wreszcie, [zazwinięcie](https://curl.haxx.se/) użytkownicy mogą wykonywać wieloczęściowe żądania formularzy w taki sam sposób:

[![Obiekty blob urządzenia](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Value | Zamień na |
| --- | --- |
| YOUR_TOKEN | Prawidłowy token uwierzytelniania OAuth 2,0 |
| YOUR_SPACE_ID | Identyfikator przestrzeni, z którą ma zostać skojarzony obiekt BLOB |
| PATH_TO_FILE | Ścieżka do pliku tekstowego |

Pomyślne OPUBLIKOWANie zwraca identyfikator nowego obiektu BLOB (wyróżniony czerwoną literą).

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API

W poniższych sekcjach opisano podstawowe punkty końcowe interfejsu API powiązane z obiektami BLOB i ich funkcje.

### <a name="devices"></a>Urządzenia

Obiekty blob można dołączać do urządzeń. Na poniższej ilustracji przedstawiono dokumentację referencyjną struktury Swagger dla interfejsów API zarządzania. Określa punkty końcowe interfejsu API związane z urządzeniami do użycia obiektów blob i wszystkie wymagane parametry ścieżki do przekazania do nich.

[![Obiekty blob urządzenia](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Na przykład aby zaktualizować lub utworzyć obiekt BLOB i dołączyć obiekt BLOB do urządzenia, wykonaj uwierzytelnione żądanie HTTP PATCH:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu BLOB |

Pomyślne żądania zwracają obiekt JSON zgodnie z [wcześniejszym opisem](#blobModel).

### <a name="spaces"></a>Spacje

Możesz również dołączyć obiekty blob do obszarów. Na poniższej ilustracji przedstawiono punkty końcowe interfejsu API obszaru, które są odpowiedzialne za obsługę obiektów BLOB. Wyświetla również wszystkie parametry ścieżki do przekazania do tych punktów końcowych.

[![Przestrzeń obiektów BLOB](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Aby na przykład zwrócić obiekt BLOB dołączony do spacji, należy wykonać uwierzytelnione żądanie HTTP GET:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu BLOB |

Pomyślne żądania zwracają obiekt JSON zgodnie z [wcześniejszym opisem](#blobModel).

Żądanie poprawki do tego samego punktu końcowego aktualizuje opisy metadanych i tworzy wersje obiektu BLOB. Żądanie HTTP jest nawiązywane za pośrednictwem metody PATCH, wraz z wszelkimi niezbędnymi danymi formularza meta i wieloczęściowych.

### <a name="users"></a>Użytkownicy

Obiekty blob można dołączać do modeli użytkowników (na przykład w celu skojarzenia obrazu profilu). Na poniższej ilustracji przedstawiono odpowiednie punkty końcowe interfejsu API użytkownika i wszystkie wymagane parametry ścieżki `id`, takie jak:

[![Obiekty blob użytkownika](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Na przykład aby pobrać obiekt BLOB dołączony do użytkownika, należy wykonać uwierzytelnione żądanie HTTP GET przy użyciu wymaganych danych formularza, aby:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu BLOB |

Pomyślne żądania zwracają obiekt JSON zgodnie z [wcześniejszym opisem](#blobModel).

## <a name="common-errors"></a>Typowe błędy

Typowy błąd nie obejmuje dostarczania informacji o prawidłowym nagłówku:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Aby rozwiązać ten problem, sprawdź, czy ogólne żądanie ma odpowiedni nagłówek **Content-Type** :

* `multipart/mixed`
* `multipart/form-data`

Upewnij się również, że każdy fragment wieloczęściowy ma odpowiadający mu **Typ zawartości** .

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat dokumentacji struktury Swagger dla usługi Azure Digital bliźniaczych reprezentacji, przeczytaj artykuł [Korzystanie z usługi Azure Digital bliźniaczych reprezentacji Swagger](how-to-use-swagger.md).

- Aby przekazać obiekty blob za poorednictwem programu Poster, przeczytaj artykuł [How to configure](./how-to-configure-postman.md)The Poster.