---
title: Przegląd interfejsu API REST operacji usługi Media Services | Dokumentacja firmy Microsoft
description: Przegląd interfejsu API REST usługi multimediów
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;johndeu
ms.openlocfilehash: 472408f1c367984d5f4e0e435366c4a0af2e5b34
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="media-services-operations-rest-api-overview"></a>Przegląd interfejsu API REST operacji usługi Media Services
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

**Media Services operacji REST** interfejsu API jest używany do tworzenia zadań, zasobów, kanałów na żywo i innych zasobów w ramach konta usługi Media Services. Aby uzyskać więcej informacji, zobacz [nośnika usług operacji REST API reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Usługa Media Services udostępnia interfejs API REST, który akceptuje JSON lub atom + pub XML format. Interfejs API REST usługi Media wymaga określonych nagłówków HTTP, które jest wysłanie każdego klienta podczas nawiązywania połączenia usługi Media Services, a także zestaw opcjonalne nagłówki. W poniższych sekcjach opisano nagłówki i zleceń HTTP, można użyć podczas tworzenia żądania odbierania odpowiedzi z usługi Media Services.

Uwierzytelnianie w interfejsie API REST Serivces nośnika odbywa się za pośrednictwem uwierzytelniania usługi Azure Active Directory, które jest opisane w artykule [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

Następujące kwestie przy użyciu REST.

* Podczas wykonywania zapytania jednostek, istnieje limit 1000 jednostek zwracane w tym samym czasie, ponieważ publiczny v2 REST ogranicza wyniki zapytania do 1000 wyników. Należy użyć **Pomiń** i **zająć** (.NET) / **górnej** (REST) zgodnie z opisem w [w tym przykładzie .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [w tym przykładzie interfejsu API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Po użyciu JSON i określając do użycia **__metadata** — słowo kluczowe w żądaniu (na przykład do odwołania obiektu połączonego) należy ustawić **Zaakceptuj** nagłówka do [formatu JSON pełne](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(zobacz poniższy przykład). OData nie rozpoznaje **__metadata** właściwości w żądaniu, chyba że zostanie ustawiona pełnego.  
  
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

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardowych nagłówków żądania HTTP obsługiwane przez usługę Media Services
Dla każdego wywołania wprowadzone do usługi Media Services ustawiono wymagane nagłówki, które należy uwzględnić w żądaniu, a także zestaw opcjonalne nagłówki można dołączyć. W poniższej tabeli przedstawiono wymagane nagłówki:

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Autoryzacja |Elementu nośnego |Elementu nośnego to mechanizm tylko zaakceptowane autoryzacji. Wartość musi również zawierać token dostępu udostępniony przez usługę Azure Active Directory. |
| x-ms-version |Decimal |2.17 (lub najbardziej aktualne wersje)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Ponieważ usługi Media Services używa OData do udostępnienia jej interfejsów API REST, nagłówkach DataServiceVersion i MaxDataServiceVersion powinny być uwzględnione w wszystkie żądania; Jednak jeśli nie są one następnie obecnie Media Services przyjęto założenie, że wartość DataServiceVersion używany jest 3.0.
> 
> 

Oto zestaw opcjonalne nagłówki:

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Date |RFC 1123 daty |Sygnatura czasowa żądania |
| Zaakceptuj |Typ zawartości |Żądany typ zawartości odpowiedzi, takie jak następujące:<p> -application/json; odata = verbose<p> -application/atom + xml<p> Odpowiedzi może być innego typu zawartości, takich jak pobieranie obiektu blob, gdy odpowiedź oznaczająca Powodzenie zawiera strumienia obiektu blob jako ładunek. |
| Zaakceptuj kodowania |Gzip, deflate |GZIP i DEFLATE kodowania, jeśli jest to wymagane. Uwaga: W przypadku dużych zasobów usługi Media Services może zignorować ten nagłówek i zwrócić nieskompresowanych danych. |
| Zaakceptuj języka |"en", "es" i tak dalej. |Określa preferowany język dla odpowiedzi. |
| Accept-Charset |Typ zestawu znaków, takich jak "UTF-8" |Domyślne to UTF-8. |
| X-HTTP-Method |Metoda HTTP |Umożliwia klientom lub zapory, które nie obsługują metody HTTP, takie jak PUT lub Usuń, aby użyć tych metod, tunneled za pośrednictwem wywołania GET. |
| Typ zawartości |Typ zawartości |Żądania typu zawartości w treści żądania PUT lub POST. |
| Client-request-id |Ciąg |Wartość zdefiniowane przez obiekt wywołujący, która identyfikuje danego żądania. Jeśli jest określony, ta wartość będą uwzględniane w komunikacie odpowiedzi jako mapują żądania. <p><p>**Ważne**<p>Wartości powinny być ograniczone do 2096b (2 KB). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standardowych nagłówków odpowiedzi HTTP obsługiwane przez usługę Media Services
Oto zestaw nagłówki, które mogą być zwracane w zależności od zasobów, które zostały żądania i akcji, którą zamierzasz wykonać.

| Nagłówek | Typ | Wartość |
| --- | --- | --- |
| Identyfikator żądania |Ciąg |Unikatowy identyfikator dla bieżącej operacji usługi wygenerowany. |
| Client-request-id |Ciąg |Identyfikator określony przez obiekt wywołujący w oryginalnego żądania, jeśli jest obecny. |
| Date |RFC 1123 daty |Data/czas przetwarzania żądania. |
| Typ zawartości |Zmienia się |Typ zawartości w treści odpowiedzi. |
| Kodowanie zawartości |Zmienia się |Gzip i deflate, gdzie to właściwe. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardowa zleceń HTTP obsługiwane przez usługę Media Services
Oto pełną listę zleceń HTTP, które mogą być używane podczas wprowadzania HTTP żądania:

| Zlecenie | Opis |
| --- | --- |
| POBIERZ |Zwraca bieżącą wartość obiektu. |
| POST |Tworzy obiekt na podstawie danych przedstawionych lub przesyła polecenia. |
| UMIEŚĆ |Zastępuje obiekt lub tworzy nazwanego obiektu (jeśli jest to wymagane). |
| USUŃ |Usuwa obiekt. |
| SCALANIA |Aktualizuje istniejący obiekt nazwane właściwości zostanie zmieniona. |
| HEAD |Zwraca metadane obiektu dla odpowiedzi GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Odnajdywanie i Przeglądaj modelu jednostki usługi Media Services
Aby jednostek usługi Media Services mogą szybciej odnajdywać, można użyć operacji $metadata. Umożliwia pobranie wszystkich typów jednostek prawidłowe, właściwości jednostki, skojarzenia, funkcji, działania i tak dalej. Dodając $metadata operacji na końcu punkt końcowy interfejsu API REST usług Media, uzyskać dostęp do tej usługi odnajdywania.

 /API/$ metadanych.

Należy dołączyć "? api version=2.x" na końcu identyfikatora URI, jeśli chcesz wyświetlić w przeglądarce metadanych lub nie ma nagłówka x-ms-version w żądaniu.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Uwierzytelniania w usłudze Media Services REST przy użyciu usługi Azure Active Directory

Uwierzytelnianie przy użyciu interfejsu API REST odbywa się za pośrednictwem usługi Azure Active Directory(AAD).
Aby uzyskać więcej informacji na temat uzyskać szczegółowe informacje wymagane uwierzytelnianie dla konta usługi Media Services z portalu Azure, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Aby uzyskać więcej informacji na temat pisania kodu, który łączy się z interfejsu API REST przy użyciu uwierzytelniania usługi Azure AD, zobacz artykuł [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z POZOSTAŁĄ](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak korzystać z interfejsu API REST usług Media uwierzytelniania usługi Azure AD, zobacz [uwierzytelnianie usługi Azure AD na dostęp do interfejsu API usługi multimediów Azure z POZOSTAŁĄ](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

