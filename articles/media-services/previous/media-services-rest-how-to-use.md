---
title: Omówienie interfejsu API REST usługi Media Services operacje | Dokumentacja firmy Microsoft
description: Omówienie interfejsu API REST usługi Media Services
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
ms.author: juliako;johndeu
ms.openlocfilehash: 549554521570d1d2f27b2da2b36ca1dfde25562f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762532"
---
# <a name="media-services-operations-rest-api-overview"></a>Omówienie interfejsu API REST usługi Media Services operacji 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

**Media Services operacji REST** interfejsu API są używane do tworzenia zadań, zasobów, kanały na żywo i innych zasobów w ramach konta usługi Media Services. Aby uzyskać więcej informacji, zobacz [odwołanie do interfejsu API usług Media Services operacji REST](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Usługa Media Services udostępnia interfejs API REST, który akceptuje JSON lub atom + pub XML format. Interfejsu API REST usługi Media Services wymaga określonych nagłówki HTTP, które każdy klient musi wysłać podczas nawiązywania połączenia z usługi Media Services, a także zestaw opcjonalne nagłówki. W poniższych sekcjach opisano nagłówki i zleceń HTTP, można użyć podczas tworzenia żądań i odbierania odpowiedzi z usługi Media Services.

Uwierzytelnianie interfejsu API REST usługi Media Services odbywa się za pośrednictwem uwierzytelniania usługi Azure Active Directory, który jest opisany w artykule [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów Azure z użyciem usług REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia, gdy przy użyciu interfejsu REST.

* Podczas wykonywania zapytań dotyczących jednostek, istnieje limit 1000 jednostek zwrócona w tym samym czasie, ponieważ publiczny v2 REST ogranicza wyniki zapytania do 1000 wyników. Należy użyć **Pomiń** i **zająć** (.NET) / **górnej** (REST), zgodnie z opisem w [w tym przykładzie .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [tego interfejsu API REST przykład](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Gdy przy użyciu formatu JSON i określając do użycia **__metadata** — słowo kluczowe w żądaniu (na przykład, aby odwołanie obiektu połączonego) należy ustawić **Akceptuj** nagłówka do [format JSON trybu informacji pełnej](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(zobacz poniższy przykład). OData nie rozpoznaje **__metadata** właściwości w żądaniu, chyba że zostanie ustawiona do pełnego.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardowa nagłówków żądań HTTP obsługiwane przez usługę Media Services
Za każde wywołanie, wprowadzone do usługi Media Services ustawiono wymagane nagłówki, które należy uwzględnić w żądaniu, a także zestaw opcjonalne nagłówki warto uwzględnić. W poniższej tabeli przedstawiono wymagane nagłówki:

| Nagłówek | Type | Wartość |
| --- | --- | --- |
| Autoryzacja |Elementu nośnego |Elementu nośnego jest mechanizm autoryzacji akceptowane tylko. Wartość musi również zawierać token dostępu udostępniony przez usługę Azure Active Directory. |
| x-ms-version |Decimal |2.17 (lub najnowsza wersja)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Ponieważ usługa Media Services korzysta z protokołu OData do udostępnienia jej interfejsów API REST, nagłówki DataServiceVersion i MaxDataServiceVersion powinny być uwzględnione w wszystkie żądania; Jednak jeśli nie są one następnie obecnie usługi Media Services przyjęto założenie, że wartość DataServiceVersion używana jest 3.0.
> 
> 

Oto zbiór opcjonalne nagłówki:

| Nagłówek | Type | Wartość |
| --- | --- | --- |
| Date |RFC 1123 daty |Sygnatura czasowa żądania |
| Zaakceptuj |Typ zawartości |Żądany typ zawartości odpowiedzi podobny do następującego:<p> -application/json;odata=verbose<p> -application/atom + xml<p> Odpowiedzi mogą mieć inny typ zawartości, takie jak pobieranie obiektów blob, gdy odpowiedź oznaczająca Powodzenie zawiera strumienia obiektu blob jako ładunek. |
| Accept-Encoding |Gzip, deflate |GZIP i DEFLATE, kodowanie, jeśli ma to zastosowanie. Uwaga: W przypadku dużych zasobów usługi Media Services może zignorować ten nagłówek i zwróć nieskompresowanych danych. |
| Zaakceptuj języka |"en", "es" i tak dalej. |Określa preferowany język dla odpowiedzi. |
| Accept-Charset |Typ zestaw znaków, takich jak "UTF-8" |Domyślna to UTF-8. |
| X-HTTP-Method. |Metoda HTTP |Umożliwia klientom lub zapory, które nie obsługują metod HTTP, takich jak PUT lub DELETE w celu użycia tych metod tunelowania przez wywołanie GET. |
| Content-Type |Typ zawartości |Typ zawartości treści żądania PUT lub WPIS żądań. |
| client-request-id |String |Wartość zdefiniowana przez obiekt wywołujący, która identyfikuje danego żądania. Jeśli zostanie określony, ta wartość będzie zawarty w komunikacie odpowiedzi jako sposób mapowania żądania. <p><p>**Ważne**<p>Wartości powinny być ograniczone do 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standardowych nagłówków odpowiedzi HTTP obsługiwane przez usługę Media Services
Poniżej znajduje się zestaw nagłówków, które mogą być zwrócone do użytkownika w zależności od tego, czy zasób, do którego zostały żądania i akcję, którą zamierzasz wykonać.

| Nagłówek | Type | Wartość |
| --- | --- | --- |
| Identyfikator żądania |String |Unikatowy identyfikator dla bieżącej operacji usługi wygenerowany. |
| client-request-id |String |Identyfikator określony przez obiekt wywołujący w oryginalne żądanie, jeśli jest obecny. |
| Date |RFC 1123 daty |Data/czas przetwarzania żądania. |
| Content-Type |Różna |Typ zawartości treści odpowiedzi. |
| Content-Encoding |Różna |Gzip lub deflate, odpowiednio. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardowa zleceń HTTP obsługiwane przez usługę Media Services
Poniżej przedstawiono pełną listę zleceń HTTP, które mogą być używane podczas wprowadzania HTTP żądania:

| zlecenia | Opis |
| --- | --- |
| GET |Zwraca bieżącą wartość obiektu. |
| POST |Tworzy obiekt na podstawie danych, pod warunkiem lub przesyła polecenia. |
| PUT |Zastępuje obiekt lub tworzy nazwany obiekt (jeśli ma zastosowanie). |
| DELETE |Usuwa obiekt. |
| SCALANIE |Aktualizuje istniejący obiekt ze zmianami właściwości o nazwie. |
| HEAD |Zwraca metadane obiektu dla odpowiedzi GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Odnajdywanie i Przeglądaj modelu jednostki usługi Media Services
Aby jednostek usługi Media Services mogą szybciej odnajdywać, służy operacja $metadata. Umożliwia pobranie wszystkich typów jednostek prawidłowe, właściwości jednostki, skojarzeń, funkcji, działania i tak dalej. Dodanie operacji $metadata-to-end punktu końcowego interfejsu API REST usługi Media Services, dostęp do tej usługi odnajdywania.

 metadane$ /API/.

Należy dołączyć "? api version=2.x" na końcu identyfikatora URI, jeśli chcesz wyświetlić metadane w przeglądarce lub w żądaniu nie dołączaj nagłówka x-ms-version.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Uwierzytelnianie za pomocą nośnika usługi REST za pomocą usługi Azure Active Directory

Uwierzytelnianie interfejsu API REST odbywa się za pośrednictwem usługi Azure Active o.
Aby uzyskać więcej informacji o tym, jak można pobrać szczegółów uwierzytelniania dla konta usługi Media Services w witrynie Azure Portal, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Aby uzyskać szczegółowe informacje na temat pisania kodu, który nawiązuje połączenie z interfejsu API REST przy użyciu uwierzytelniania usługi Azure AD, zobacz artykuł [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów Azure z użyciem usług REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak używać uwierzytelniania usługi Azure AD za pomocą interfejsu API REST usługi Media Services, zobacz [uwierzytelnianie usługi Azure AD dostęp do interfejsu API usługi multimediów Azure z użyciem usług REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

