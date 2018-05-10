---
title: Jak kodowanie elementu zawartości platformy Azure przy użyciu standardu Media Encoder Standard | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Media Encoder Standard do kodowania zawartości multimedialnej w usłudze Azure Media Services. Przykłady kodu za pomocą interfejsu API REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 78087bbb43d12af65bfbde93f54e2f29309ac093
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Jak kodowanie elementu zawartości przy użyciu standardu Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Przegląd
Aby dostarczyć wideo za pośrednictwem Internetu, trzeba skompresować nośniki. Pliki wideo cyfrowe są duże i może być zbyt duża, aby dostarczyć za pośrednictwem Internetu lub dla klientów urządzeń, aby wyświetlać się poprawnie. Kodowanie jest procesem kompresji audio i wideo, więc klienci mogą wyświetlać multimediów.

Zadania kodowania są jednym z najbardziej typowych operacji w usłudze Azure Media Services. Zadania kodowania są tworzone w celu konwertowania plików multimediów z jednego formatu kodowania na inny. Podczas kodowania, można użyć koder wbudowane usługi Media Services (Media Encoder Standard). Umożliwia także kodera świadczonych przez partnera usługi Media Services. Kodery innych firm są dostępne za pośrednictwem portalu Azure Marketplace. Można określić szczegółów kodowania zadań za pomocą ciągi ustawień wstępnych zdefiniowane dla kodera lub za pomocą plików konfiguracji wstępnie zdefiniowane. Aby wyświetlić typy ustawień, które są dostępne, zobacz [ustawień wstępnych zadań dla standardu Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Każde zadanie może mieć jedno lub więcej zadań, w zależności od typu przetwarzania, które chcesz wykonać. Za pomocą interfejsu API REST można utworzyć zadania i ich zadania pokrewne w jeden z dwóch sposobów:

* Zadania mogą być zdefiniowano w tekście za pośrednictwem właściwości nawigacji zadania na jednostkach zadania.
* Użyj przetwarzanie wsadowe OData.

Firma Microsoft zaleca zawsze kodowanie plików źródłowych do adaptacyjnej szybkości bitowej MP4 zestawu, a następnie wykonać konwersję zestaw pożądany format przy użyciu [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

Jeśli element zawartości danych wyjściowych jest szyfrowany w magazynie, należy skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas uzyskiwania dostępu do obiektów w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

Przed rozpoczęciem odwołujące się do procesory multimediów, sprawdź, czy prawidłowy nośnik identyfikator procesora. Aby uzyskać więcej informacji, zobacz [uzyskać procesory multimediów](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsu API usług AMS, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Utwórz zadanie z jednego zadania kodowania
> [!NOTE]
> Podczas pracy z interfejsu API REST usług Media następujące kwestie:
>
> Podczas uzyskiwania dostępu do obiektów w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usług Media](media-services-rest-how-to-use.md).
>
> Po użyciu JSON i określając do użycia **__metadata** — słowo kluczowe w żądaniu (na przykład do odwołania obiektu połączonego), należy ustawić **Zaakceptuj** nagłówka do [formatu JSON pełne](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Zaakceptuj: application/json; odata = pełne.
>
>

Poniższy przykład przedstawia sposób tworzenia i post zadania z jednego zadania ustawioną kodowanie wideo w określonej rozdzielczości i jakości. Podczas kodowania przy użyciu Media Encoder Standard, można użyć ustawienia konfiguracji zadania określony [tutaj](http://msdn.microsoft.com/library/mt269960).

Żądanie:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Odpowiedź:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Ustaw nazwę elementu zawartości wyjściowej
Poniższy przykład przedstawia sposób ustawiania atrybutu assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Zagadnienia do rozważenia
* Aby określić ilość danych wejściowych lub wyjściowych zasoby, które są używane przez zadanie taskbody — właściwości należy użyć literału XML. Artykuł zadanie zawiera definicję schematu XML dla pliku XML.
* W definicji taskbody — wartość każdego wewnętrzny <inputAsset> i <outputAsset> musi być ustawiona jako JobInputAsset(value) lub JobOutputAsset(value).
* Zadanie może mieć wielu zasobów danych wyjściowych. Jako dane wyjściowe zadania w ramach zadania jeden JobOutputAsset(x) można można użyć tylko raz.
* JobInputAsset lub JobOutputAsset można określić jako zasób wprowadzania zadania.
* Zadania musi nie tworzą cyklu.
* Parametru wartości, który jest przekazywany do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu dla elementu zawartości. Rzeczywiste zasoby są definiowane w InputMediaAssets i OutputMediaAssets właściwości nawigacji w definicji zadania jednostki.
* Ponieważ usługi Media Services jest oparty na OData v3, poszczególne zasoby w kolekcji właściwości nawigacji InputMediaAssets i OutputMediaAssets istnieją odwołania za pośrednictwem "__metadata: identyfikator uri" pary nazwa wartość.
* Mapuje InputMediaAssets na jeden lub więcej zasobów, które zostały utworzone w usłudze Media Services. OutputMediaAssets są tworzone przez system. Nie odwołujących się do istniejącego zasobu.
* Można go nazwać OutputMediaAssets przy użyciu atrybutu assetName. Jeśli ten atrybut nie jest obecny, a następnie nazwę OutputMediaAsset to niezależnie od tekst wewnętrzny wartość <outputAsset> element jest z sufiksem Nazwa zadania wartość lub wartość identyfikatora zadania (w przypadku, gdy nie jest zdefiniowana właściwość Name). Na przykład jeśli ustawisz wartość assetName dotyczące nazwy "Test", następnie OutputMediaAsset nazwa właściwość jest ustawiona na "Test". Jednak jeśli nie zostały ustawione na wartość assetName, ale ustawiona na nazwę zadania na "NewJob", nazwa OutputMediaAsset może być "_NewJob JobOutputAsset (wartość)".

## <a name="create-a-job-with-chained-tasks"></a>Utwórz zadanie z zadaniami łańcuchowa
W wielu scenariuszach aplikacji deweloperzy chcą tworzyć serie przetwarzania zadań. W usłudze Media Services można utworzyć serię zadań łańcuchowych. Każde zadanie wykonuje kroki przetwarzania różnych i używać procesorów innego nośnika. Zadania łańcuchowa oddać zasobów z jednego zadania do innego, wykonywanie liniowej sekwencji zadań na element zawartości. Jednak zadania wykonywane w ramach zadania nie są wymagane w sekwencji. Po utworzeniu zadania łańcuchowa łańcuchowa **ITask** obiekty są tworzone w jednej **IJob** obiektu.

> [!NOTE]
> Obecnie istnieje limit 30 zadań dla zadania. Jeśli potrzebujesz łańcucha ponad 30 zadań, należy utworzyć więcej niż jednego zadania zawiera zadania.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Zagadnienia do rozważenia
Aby włączyć tworzenie łańcuchów zadań:

* Zadanie musi mieć co najmniej dwa zadania.
* Musi istnieć co najmniej jedno zadanie, którego dane wejściowe są dane wyjściowe inne zadanie w zadaniu.

## <a name="use-odata-batch-processing"></a>Przetwarzanie wsadowe OData
Poniższy przykład pokazuje, jak tworzyć zadania i zadania przy użyciu przetwarzania wsadowego OData. Informacje dotyczące przetwarzania wsadowego znajdują się w temacie [przetwarzanie wsadowe Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Utwórz zadanie przy użyciu obiekt JobTemplate
W przypadku przetwarzania wielu zasobów za pomocą wspólnego zestawu zadań, użyj obiekt JobTemplate do określenia ustawień domyślnych zadań lub ustawić kolejność zadań.

Poniższy przykład pokazuje, jak utworzyć obiekt JobTemplate z TaskTemplate, który jest zdefiniowano w tekście. TaskTemplate używa Media Encoder Standard jako MediaProcessor do kodowania pliku zasobów. Jednak inne MediaProcessors może służyć także.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> W przeciwieństwie do innych jednostek usługi Media Services musi Zdefiniuj nowy identyfikator GUID dla każdego TaskTemplate i umieścić w taskTemplateId i właściwość identyfikatora w treści na żądanie. Schemat identyfikacji zawartości, należy wykonać schematu, opisanego w zidentyfikować Azure Media Services jednostek. Ponadto nie można zaktualizować JobTemplates. Zamiast tego należy utworzyć nową zaktualizowanych zmian.
>
>

W przypadku powodzenia następującą odpowiedź jest zwracana:

    HTTP/1.1 201 Created

    . . .


Poniższy przykład przedstawia sposób tworzenia zadania, który odwołuje się do identyfikatora obiekt JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


W przypadku powodzenia następującą odpowiedź jest zwracana:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Funkcje zaawansowane kodowanie do eksplorowania
* [Sposób generowania miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Trwa generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Filmy wideo przycięcia podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawień predefiniowanych](media-services-custom-mes-presets-with-dotnet.md)
* [Nakładki lub znak wodny wideo z obrazem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz, jak utworzyć zadanie kodowanie elementu zawartości, zobacz [sprawdzanie postępu zadania za pomocą usługi Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Zobacz także
[Pobierz procesory multimediów](media-services-rest-get-media-processor.md)
