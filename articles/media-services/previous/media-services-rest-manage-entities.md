---
title: Zarządzanie jednostkami usługi Media Services za pomocą architektury REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać jednostkami usługi Media Services za pomocą interfejsu API REST.
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
ms.openlocfilehash: ffbf30f2bfdf0a175513a8d2b9182b35c39f6aae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761713"
---
# <a name="managing-media-services-entities-with-rest"></a>Zarządzanie jednostkami usługi Media Services za pomocą architektury REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services jest oparty na protokole REST usługa oparta na protokole OData v3. Można dodać, zapytania, aktualizacji i usuwanie jednostek w taki tak samo, jak w przypadku dowolnej usługi OData. Wyjątki zostaną przestawione, jeśli ma to zastosowanie. Aby uzyskać więcej informacji na temat protokołu OData, zobacz [dokumentacji protokołu Open Data Protocol](https://www.odata.org/documentation/).

W tym temacie dowiesz się, jak zarządzać jednostkami usługi Azure Media Services za pomocą architektury REST.

>[!NOTE]
> Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład 1 kwietnia 2017 r. wszystkie rekordy zadań na Twoim koncie, które są starsze niż 31 grudnia 2016 r. zostaną automatycznie usunięte. Jeśli chcesz zarchiwizować informacje zadania lub zadania podrzędnego, można użyć kodu opisanego w tym temacie.

## <a name="considerations"></a>Zagadnienia do rozważenia  

Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Dodawanie jednostek
Każda jednostka w usłudze Media Services jest dodawany do zbioru jednostek, takich jak zasoby, za pomocą żądania POST HTTP.

Poniższy przykład pokazuje, jak utworzyć AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Podczas badania jednostki
Wykonywanie kwerend oraz wyświetlanie ich listy jednostek jest proste, a tylko obejmuje żądania GET HTTP oraz opcjonalnie operacje usługi OData.
Poniższy przykład pobiera listę wszystkich jednostek MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Możesz również pobrać określonej jednostki lub wszystkie zestawy jednostek skojarzonych określonej jednostki, takie jak w następujących przykładach:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Poniższy przykład zwraca tylko właściwość stan wszystkich zadań.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Poniższy przykład zwraca wszystkie JobTemplates o nazwie "SampleTemplate."

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> $Expand operacja nie jest obsługiwana w usługi Media Services, jak i nieobsługiwane LINQ opisanych metod w zagadnienia dotyczące LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie dużych kolekcjach jednostek
Podczas wykonywania zapytań dotyczących jednostek, istnieje limit 1000 jednostek zwrócona w tym samym czasie, ponieważ publiczny v2 REST ogranicza wyniki zapytania do 1000 wyników. Użyj **pominąć** i **górnej** do sortowania dużych kolekcji jednostek. 

Poniższy przykład pokazuje, jak używać **pominąć** i **górnej** pominąć najpierw 2000 zadań i następnie 1000 wykonywania zadań.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Aktualizowanie jednostek
W zależności od typu jednostki i stan, który znajduje się w można zaktualizować właściwości tej jednostki przy użyciu poprawek żądania PUT lub SCALIĆ HTTP. Aby uzyskać więcej informacji na temat tych operacji, zobacz [poprawki/PUT/SCALANIE](https://msdn.microsoft.com/library/dd541276.aspx).

W poniższym przykładzie kodu pokazano, jak można zaktualizować właściwości Name jednostki zasobów.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Usuwanie jednostek
Za pomocą żądanie DELETE protokołu HTTP w usłudze Media Services można usunąć jednostki. W zależności od jednostki kolejność, w którym usuwanie jednostek może być ważne. Na przykład jednostek, takich jak zasoby wymagają, aby odwołać (lub usuń) wszystkie Lokalizatory odwołujące się do tego określonego zasobu przed usunięciem elementu zawartości.

Poniższy przykład pokazuje, jak usunąć lokalizatora, który został użyty do przekazania pliku do magazynu obiektów blob.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

