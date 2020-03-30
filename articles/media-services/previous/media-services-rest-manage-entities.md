---
title: Zarządzanie jednostkami usługi Media Services za pomocą REST | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak zarządzać encjami usługi Media Services za pomocą interfejsu API REST.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a03bc24b689df342be40536c26149a7611fc5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283318"
---
# <a name="managing-media-services-entities-with-rest"></a>Zarządzanie jednostkami usługi Media Services za pomocą REST  

> [!div class="op_single_selector"]
> * [Reszta](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services to usługa oparta na restunie, oparta na ul. Można dodawać, kwerendy, aktualizować i usuwać jednostki w taki sam sposób, jak w każdej innej usłudze OData. Wyjątki będą wywoływane w stosownych przypadkach. Aby uzyskać więcej informacji na temat OData, zobacz [Dokumentacja protokołu Open Data Protocol](https://www.odata.org/documentation/).

W tym temacie pokazano, jak zarządzać jednostkami usługi Azure Media Services za pomocą rest.

>[!NOTE]
> Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład 1 kwietnia 2017 r. każdy rekord zadania na koncie starszym niż 31 grudnia 2016 r. zostanie automatycznie usunięty. Jeśli chcesz zarchiwizować informacje o zadaniu/zadaniu, możesz użyć kodu opisanego w tym temacie.

## <a name="considerations"></a>Zagadnienia do rozważenia  

Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

## <a name="adding-entities"></a>Dodawanie encji
Każda encja w umiań Programu Media Services jest dodawana do zestawu jednostek, takich jak Zasoby, za pośrednictwem żądania HTTP POST.

W poniższym przykładzie pokazano, jak utworzyć AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Wykonywanie zapytań o jednostki
Wykonywanie zapytań i wystawianie danych na listę jednostek jest proste i obejmuje tylko żądanie GET HTTP i opcjonalne operacje OData.
Poniższy przykład pobiera listę wszystkich jednostek MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Można również pobrać określoną encję lub wszystkie zestawy jednostek skojarzone z określoną encją, na przykład w poniższych przykładach:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Poniższy przykład zwraca tylko State właściwości wszystkich zadań.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Poniższy przykład zwraca wszystkie jobtemplates o nazwie "SampleTemplate."

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> Operacja $expand nie jest obsługiwana w programie Media Services, a także nieobsługiwanych metod LINQ opisanych w linq considerations (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie dużych zbiorów jednostek
Podczas wykonywania zapytań jednostek istnieje limit 1000 jednostek zwracanych jednocześnie, ponieważ publiczna rest w wersji 2 ogranicza wyniki kwerendy do 1000 wyników. Użyj **pomiń** i **góry,** aby wyliczyć za pośrednictwem dużej kolekcji jednostek. 

W poniższym przykładzie pokazano, jak użyć **skip** i **top,** aby pominąć pierwsze 2000 zadań i uzyskać następne 1000 zadań.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Aktualizowanie encji
W zależności od typu jednostki i stanu, w jakim się znajduje, można zaktualizować właściwości tej jednostki za pomocą żądań HTTP PATCH, PUT lub MERGE. Aby uzyskać więcej informacji na temat tych operacji, zobacz [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

W poniższym przykładzie kodu pokazano, jak zaktualizować Name właściwości w encji zasobów.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Usuwanie encji
Jednostki można usunąć w programie Media Services przy użyciu żądania DELETE HTTP. W zależności od jednostki kolejność usuwania jednostek może być ważna. Na przykład jednostki, takie jak Zasoby, wymagają odwołania (lub usunięcia) wszystkich lokalizatorów, które odwołują się do określonego zasobu przed usunięciem środka trwałego.

W poniższym przykładzie pokazano, jak usunąć lokalizator, który został użyty do przekazania pliku do magazynu obiektów blob.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

