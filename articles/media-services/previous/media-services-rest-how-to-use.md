---
title: Omówienie interfejsu API operacji usługi Media Services REST | Dokumenty firmy Microsoft
description: Interfejs API "Media Services Operations REST" służy do tworzenia zadań, zasobów, kanałów na żywo i innych zasobów na koncie usługi Media Services. Ten artykuł zawiera omówienie interfejsu API rest usługi Azure Media Services w wersji 2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 597839f633ed2b925b86c5f859a0fb2d3b64dd59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773657"
---
# <a name="media-services-operations-rest-api-overview"></a>Omówienie interfejsu API operacji usługi Media Services 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Interfejs API **REST operacji usług multimedialnych** służy do tworzenia zadań, zasobów, kanałów na żywo i innych zasobów na koncie usługi Media Services. Aby uzyskać więcej informacji, zobacz [Odwołanie interfejsu API REST operacji usług media services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Usługa Media Services udostępnia interfejs API REST, który akceptuje zarówno format XML JSON, jak i atom+pub. Interfejs API REST usługi Media Services wymaga określonych nagłówków HTTP, które każdy klient musi wysłać podczas łączenia się z usługą Media Services, a także zestawu opcjonalnych nagłówków. W poniższych sekcjach opisano nagłówki i zlecenia HTTP, których można używać podczas tworzenia żądań i odbierania odpowiedzi z usługi Media Services.

Uwierzytelnianie w interfejsie API REST usługi Media Services odbywa się za pomocą uwierzytelniania usługi Azure Active Directory, które jest opisane w artykule [Korzystanie z uwierzytelniania usługi Azure AD](media-services-rest-connect-with-aad.md) w celu uzyskania dostępu do interfejsu API usługi Azure Media Services za pomocą rest

## <a name="considerations"></a>Zagadnienia do rozważenia

Następujące zagadnienia mają zastosowanie podczas korzystania z REST.

* Podczas wykonywania zapytań jednostek istnieje limit 1000 jednostek zwracanych jednocześnie, ponieważ publiczna rest w wersji 2 ogranicza wyniki kwerendy do 1000 wyników. Musisz użyć **Skip** and **Take** (.NET)/ **top** (REST), jak opisano w [tym przykładzie .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [tym przykładzie interfejsu API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Podczas korzystania z JSON i określania do **używania __metadata** słowa kluczowego w żądaniu (na przykład, aby odwołać się do obiektu połączonego) musi ustawić **Accept** nagłówka do [formatu JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (patrz poniższy przykład). Odata nie rozumie **właściwości __metadata** w żądaniu, chyba że ustawisz ją na pełne.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardowe nagłówki żądań HTTP obsługiwane przez usługi Media Services
Dla każdego wywołania wprowadzonego do usługi Media Services istnieje zestaw wymaganych nagłówków, które należy uwzględnić w żądaniu, a także zestaw opcjonalnych nagłówków, które można uwzględnić. W poniższej tabeli wymieniono wymagane nagłówki:

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Autoryzacja |Bearer |Okaziciela jest jedynym akceptowanym mechanizmem autoryzacji. Wartość musi również zawierać token dostępu dostarczony przez usługę Azure Active Directory. |
| wersja x-ms |Wartość dziesiętna |2.17 (lub najnowsza wersja)|
| DataServiceVersion (Wersja danych) |Wartość dziesiętna |3.0 |
| MaxDataServiceVersion (Wersja maksymalna) |Wartość dziesiętna |3.0 |

> [!NOTE]
> Ponieważ usługa Media Services używa OData do udostępnienia jego interfejsów API REST, Nagłówki DataServiceVersion i MaxDataServiceVersion powinny być uwzględnione we wszystkich żądaniach; jednak jeśli nie są one, a następnie obecnie Media Services zakłada DataServiceVersion wartość w użyciu jest 3.0.
> 
> 

Poniżej znajduje się zestaw opcjonalnych nagłówków:

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Data |RFC 1123 data |Sygnatura czasowa żądania |
| Zaakceptuj |Typ zawartości |Żądany typ zawartości dla odpowiedzi, taki jak:<p> -application/json;odata=pełne<p> - aplikacja/atom+xml<p> Odpowiedzi mogą mieć inny typ zawartości, takich jak pobieranie obiektu blob, gdzie pomyślna odpowiedź zawiera strumień obiektów blob jako ładunek. |
| Akceptowanie kodowania |Gzip, opróżnianie |Kodowanie GZIP i DEFLATE, w stosownych przypadkach. Uwaga: W przypadku dużych zasobów usługa Media Services może zignorować ten nagłówek i zwrócić niezamkowane dane. |
| Accept-Language |"en", "es" i tak dalej. |Określa preferowany język odpowiedzi. |
| Zestaw znaków akceptowania |Typ charset jak "UTF-8" |Wartość domyślna to UTF-8. |
| Metoda X-HTTP |Metoda HTTP |Umożliwia klientom lub zapory, które nie obsługują metod HTTP, takich jak PUT lub DELETE, aby korzystać z tych metod, tunelowane za pośrednictwem wywołania GET. |
| Content-Type |Typ zawartości |Typ zawartości treści żądania w żądaniach PUT lub POST. |
| identyfikator żądania klienta |Ciąg |Wartość zdefiniowana przez wywołującego, która identyfikuje dane żądanie. Jeśli zostanie określona, ta wartość zostanie uwzględniona w komunikacie odpowiedzi jako sposób mapowania żądania. <p><p>**Ważne**<p>Wartości powinny być ograniczone do 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standardowe nagłówki odpowiedzi HTTP obsługiwane przez usługi Media Services
Poniżej znajduje się zestaw nagłówków, które mogą być zwracane do Ciebie w zależności od żądanego zasobu i akcji, które zamierzasz wykonać.

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| identyfikator żądania |Ciąg |Unikatowy identyfikator bieżącej operacji, wygenerowanej usługi. |
| identyfikator żądania klienta |Ciąg |Identyfikator określony przez obiekt wywołujący w oryginalnym żądaniu, jeśli jest obecny. |
| Data |RFC 1123 data |Data/godzina przetworzenia żądania. |
| Content-Type |Różna |Typ zawartości treści odpowiedzi. |
| Content-Encoding |Różna |Gzip lub deflate, stosownie do przypadku. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardowe zlecenia HTTP obsługiwane przez media services
Poniżej znajduje się pełna lista zleceń HTTP, które mogą być używane podczas wysyłania żądań HTTP:

| Czasownik | Opis |
| --- | --- |
| GET |Zwraca bieżącą wartość obiektu. |
| POST |Tworzy obiekt na podstawie dostarczonych danych lub przesyła polecenie. |
| PUT |Zastępuje obiekt lub tworzy nazwany obiekt (w stosownych przypadkach). |
| DELETE |Usuwa obiekt. |
| Scalania |Aktualizuje istniejący obiekt o nazwie zmiany właściwości. |
| HEAD |Zwraca metadane obiektu dla odpowiedzi GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Odnajdowanie i przeglądanie modelu encji usługi Media Services
Aby jednostki usługi Media Services były bardziej wykrywalne, można użyć operacji $metadata. Umożliwia pobieranie wszystkich prawidłowych typów encji, właściwości encji, skojarzeń, funkcji, akcji itd. Dodając operację $metadata na końcu punktu końcowego interfejsu API rest usługi Media Services, można uzyskać dostęp do tej usługi odnajdywania.

 /api/$metadata.

Należy dołączyć "?api-version=2.x" na końcu identyfikatora URI, jeśli chcesz wyświetlić metadane w przeglądarce lub nie zawierają nagłówka x-ms-version w żądaniu.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Uwierzytelnij się za pomocą usługi Media Services REST przy użyciu usługi Azure Active Directory

Uwierzytelnianie w interfejsie API REST odbywa się za pośrednictwem usługi Azure Active Directory(AAD).
Aby uzyskać szczegółowe informacje na temat uzyskiwania wymaganych szczegółów uwierzytelniania dla konta usługi Media Services z witryny Azure Portal, zobacz [Uzyskiwanie dostępu do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md)

Aby uzyskać szczegółowe informacje na temat pisania kodu łączącego się z interfejsem API REST przy użyciu uwierzytelniania usługi Azure AD, zobacz artykuł [Korzystanie z uwierzytelniania usługi Azure AD w celu uzyskania dostępu do interfejsu API usługi Azure Media Services za pomocą REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak używać uwierzytelniania usługi Azure AD za pomocą interfejsu MEDIA Services REST API, zobacz [Korzystanie z uwierzytelniania usługi Azure AD](media-services-rest-connect-with-aad.md)w celu uzyskania dostępu do interfejsu API usługi Azure Media Services za pomocą rest .

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

