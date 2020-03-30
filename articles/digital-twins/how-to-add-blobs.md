---
title: Jak dodawać obiekty blob do obiektów — Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodawać obiekty blob do użytkowników, urządzeń i przestrzeni w usłudze Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75929622"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Dodawanie obiektów blob do obiektów w usłudze Azure Digital Twins

Obiekty BLOB są nieustrukturyzowanymi reprezentacjami typowych typów plików, takich jak obrazy i dzienniki. Obiekty BLOB śledzą, jakiego rodzaju dane reprezentują przy użyciu typu MIME (na przykład: "image/jpeg") i metadanych (nazwa, opis, typ itd.).

Usługa Azure Digital Twins obsługuje dołączanie obiektów blob do urządzeń, spacji i użytkowników. Obiekty BLOB mogą reprezentować zdjęcie profilowe dla użytkownika, zdjęcie urządzenia, film, mapę, kod błyskawiczny oprogramowania układowego, dane JSON, dziennik itp.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Omówienie przekazywania obiektów blob

Wieloczęściowych żądań można przekazać obiekty blob do określonych punktów końcowych i ich odpowiednich funkcji.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadane obiektu blob

Oprócz **zawartości typu** i **dyspozycji zawartości,** azure digital twins żądań wieloczęściowych musi określić poprawną treść JSON. Treści JSON do przesłania zależy od rodzaju operacji żądania HTTP, który jest wykonywany.

Cztery główne schematy JSON to:

[![Schematy JSON](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

Metadane obiektów blob JSON są zgodne z następującym modelem:

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

| Atrybut | Typ | Opis |
| --- | --- | --- |
| **Parentid** | Ciąg | Encja nadrzędna do skojarzenia obiektu blob z (spacje, urządzenia lub użytkownicy) |
| **Nazwa** |Ciąg | Przyjazna dla człowieka nazwa obiektu blob |
| **Typu** | Ciąg | Typ obiektu blob - nie można użyć *typu* i *typeId*  |
| **Typeid** | Liczba całkowita | Identyfikator typu obiektu blob — nie można użyć *identyfikatora typu* i *typeId* |
| **Podtypu** | Ciąg | Podtyp obiektu blob — nie można użyć *podtypu* i *podtypu.* |
| **podtypId** | Liczba całkowita | Identyfikator podtypu obiektu blob — nie można użyć *podtypu* i *podtypuId* |
| **Opis** | Ciąg | Dostosowany opis obiektu blob |
| **Udostępnianie** | Ciąg | Czy obiekt blob może być`None`współużytkowana - wyliczenie [ , `Tree`, `Global`] |

Metadane obiektów blob są zawsze dostarczane jako pierwszy `.json` fragment z **typem zawartości** `application/json` lub jako plik. Dane pliku są dostarczane w drugim fragmencie i mogą mieć dowolny obsługiwany typ MIME.

Dokumentacja Swagger opisuje te schematy modelu w szczegółach.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Dowiedz się więcej o korzystaniu z dokumentacji referencyjnej, czytając [jak używać funkcji Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Dane odpowiedzi obiektów blob

Indywidualnie zwracane obiekty blob są zgodne z następującym schematem JSON:

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

| Atrybut | Typ | Opis |
| --- | --- | --- |
| **Identyfikator** | Ciąg | Unikatowy identyfikator obiektu blob |
| **Nazwa** |Ciąg | Przyjazna dla człowieka nazwa obiektu blob |
| **Parentid** | Ciąg | Encja nadrzędna do skojarzenia obiektu blob z (spacje, urządzenia lub użytkownicy) |
| **Typu** | Ciąg | Typ obiektu blob - nie można użyć *typu* i *typeId*  |
| **Typeid** | Liczba całkowita | Identyfikator typu obiektu blob — nie można użyć *identyfikatora typu* i *typeId* |
| **Podtypu** | Ciąg | Podtyp obiektu blob — nie można użyć *podtypu* i *podtypu.* |
| **podtypId** | Liczba całkowita | Identyfikator podtypu obiektu blob — nie można użyć *podtypu* i *podtypuId* |
| **Udostępnianie** | Ciąg | Czy obiekt blob może być`None`współużytkowana - wyliczenie [ , `Tree`, `Global`] |
| **Opis** | Ciąg | Dostosowany opis obiektu blob |
| **contentInfos** | Tablica | Określa nieustrukturyzowane informacje o metadanych, w tym wersję |
| **Fullname** | Ciąg | Pełna nazwa obiektu blob |
| **spacePaths (Ścieżki przestrzeni)** | Ciąg | Ścieżka spacji |

Metadane obiektów blob są zawsze dostarczane jako pierwszy `.json` fragment z **typem zawartości** `application/json` lub jako plik. Dane pliku są dostarczane w drugim fragmencie i mogą mieć dowolny obsługiwany typ MIME.

### <a name="blob-multipart-request-examples"></a>Przykłady żądań wieloczęściowych obiektów blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Aby przekazać plik tekstowy jako obiekt blob i skojarzyć go z spacją, należy wykonać uwierzytelnione żądanie HTTP POST:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Z następującym ciałem:

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
| USER_DEFINED_BOUNDARY | Wieloczęściowa nazwa granicy zawartości |

Poniższy kod jest implementacją platformy .NET tego samego obiektu blob, przy użyciu klasy [MultipartFormDataContent:](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent)

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

Wreszcie użytkownicy [cURL](https://curl.haxx.se/) mogą wprowadzać żądania formularzy wieloczęściowych w ten sam sposób:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOKEN | Twój ważny token OAuth 2.0 |
| YOUR_SPACE_ID | Identyfikator przestrzeni, aby skojarzyć obiekt blob z |
| PATH_TO_FILE | Ścieżka do pliku tekstowego |

[![Przykład cURL](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Pomyślny post zwraca identyfikator nowego obiektu blob.

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API

W poniższych sekcjach opisano podstawowe punkty końcowe interfejsu API związane z obiektami blob i ich funkcje.

### <a name="devices"></a>Urządzenia

Obiekty blob można dołączać do urządzeń. Na poniższej ilustracji przedstawiono dokumentację odwołania Swagger dla interfejsów API zarządzania. Określa punkty końcowe interfejsu API związane z urządzeniem dla zużycia obiektów blob i wszelkie wymagane parametry ścieżki do przekazania do nich.

[![Obiekty blob urządzenia](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Na przykład, aby zaktualizować lub utworzyć obiekt blob i dołączyć obiekt blob do urządzenia, należy wykonać uwierzytelnione żądanie POPRAWKI HTTP do:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu blob |

Pomyślne żądania zwracają obiekt JSON zgodnie [z wcześniejszym opisem](#blobs-response-data).

### <a name="spaces"></a>Spacje

Można również dołączać obiekty blob do spacji. Poniższy obraz zawiera listę wszystkich punktów końcowych interfejsu API miejsca odpowiedzialnych za obsługę obiektów blob. Wyświetla również wszystkie parametry ścieżki do przekazania do tych punktów końcowych.

[![Spacja obiektów blob](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Na przykład, aby zwrócić obiekt blob dołączony do spacji, należy wykonać uwierzytelnione żądanie HTTP GET do:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu blob |

Pomyślne żądania zwracają obiekt JSON zgodnie [z wcześniejszym opisem](#blobs-response-data).

Żądanie PATCH do tego samego punktu końcowego aktualizuje opisy metadanych i tworzy wersje obiektu blob. Żądanie HTTP jest dokonywane za pomocą metody PATCH, wraz z niezbędnymi meta i wieloczęściowymi danymi formularza.

### <a name="users"></a>Użytkownicy

Obiekty blob można dołączać do modeli użytkowników (na przykład w celu skojarzenia zdjęcia profilowego). Na poniższej ilustracji przedstawiono odpowiednie punkty końcowe `id`interfejsu API użytkownika i wszelkie wymagane parametry ścieżki, takie jak:

[![Obiekty BLOB użytkownika](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Na przykład, aby pobrać obiekt blob dołączony do użytkownika, należy wykonać uwierzytelnione żądanie HTTP GET z dowolnymi wymaganymi danymi formularza do:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_BLOB_ID* | Żądany identyfikator obiektu blob |

Pomyślne żądania zwracają obiekt JSON zgodnie [z wcześniejszym opisem](#blobs-response-data).

## <a name="common-errors"></a>Typowe błędy

* Typowy błąd polega na niedoświetlaniu prawidłowych informacji nagłówka:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Aby rozwiązać ten błąd, sprawdź, czy ogólne żądanie ma odpowiedni nagłówek **typu zawartości:**

     * `multipart/mixed`
     * `multipart/form-data`

  Ponadto sprawdź, czy każdy *wieloczęściowy fragment* ma odpowiedni **typ zawartości**.

* Drugi typowy błąd pojawia się, gdy wiele obiektów blob są przypisane do tego samego zasobu na [wykresie analizy przestrzennej:](concepts-objectmodel-spatialgraph.md)

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > Atrybut **wiadomości** będzie się różnić w zależności od zasobu. 

  Tylko jeden obiekt blob (każdego rodzaju) mogą być dołączone do każdego zasobu w ramach wykresu przestrzennego. 

  Aby rozwiązać ten błąd, zaktualizuj istniejący obiekt blob przy użyciu odpowiedniej operacji HTTP PATCH interfejsu API. Spowoduje to zastąpienie istniejących danych obiektów blob żądanymi danymi.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o dokumentacji referencyjnej Swagger dla usługi Azure Digital Twins, przeczytaj [artykuł Korzystanie z usługi Azure Digital Twins Swagger](how-to-use-swagger.md).

- Aby przesłać obiekty BLOB za pośrednictwem listonosza, przeczytaj [artykuł Jak skonfigurować pozycję Listonosz](./how-to-configure-postman.md).