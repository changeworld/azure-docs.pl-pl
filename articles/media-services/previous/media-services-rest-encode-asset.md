---
title: Jak kodować zasoby platformy Azure za pomocą usługi Media Encoder Standard | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą usługi Media Encoder Standard kodowania zawartości multimedialnej w usłudze Azure Media Services. Przykłady kodu za pomocą interfejsu API REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8db9e60e9ce99eaf2621821825620966b8b8b4ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640082"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Jak kodować zasoby przy użyciu usługi Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Omówienie

Do dostarczania wideo za pośrednictwem Internetu, trzeba skompresować nośniki. Pliki wideo cyfrowe są duże i może być zbyt duża, aby dostarczyć za pośrednictwem Internetu lub dla klientów urządzeń, aby wyświetlać się poprawnie. Kodowanie jest procesem kompresowania audio i wideo, dzięki czemu klienci mogą wyświetlać multimediów.

Zadania kodowania to jedna z najbardziej typowych operacji w usłudze Azure Media Services. Zadania kodowania są tworzone w celu konwertowania plików multimediów z jednego formatu kodowania na inny. Podczas kodowania, możesz użyć kodera wbudowanej usługi Media Services (usługi Media Encoder Standard). Można również użyć koder świadczonych przez partnera usługi Media Services. Kodery innych firm są dostępne za pośrednictwem portalu Azure Marketplace. Można określić szczegóły zadania kodowania, przy użyciu predefiniowanej ciągów zdefiniowane dla usługi encoder lub za pomocą plików konfiguracji ustawienia wstępnego. Aby wyświetlić typy ustawień wstępnych, które są dostępne, zobacz [ustawienia wstępne zadań usługi Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Każde zadanie może mieć jedno lub więcej zadań, w zależności od rodzaju przetwarzania, które chcesz osiągnąć. Za pomocą interfejsu API REST można utworzyć zadań i ich powiązane zadania, w jeden z dwóch sposobów:

* Zadania mogą być zdefiniowano w tekście za pomocą właściwości nawigacji zadania na jednostkach zadania.
* Użyj przetwarzaniem wsadowym OData.

Firma Microsoft zaleca, aby zawsze kodowanie plików źródłowych do adaptacyjną szybkością transmisji bitów zestawu w formacie MP4, a następnie wykonać konwersję zestawu żądany format za pomocą [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

Jeśli element zawartości danych wyjściowych jest szyfrowany w magazynie, należy skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

Przed rozpoczęciem odwołujące się do procesorów multimediów, sprawdź, czy masz właściwe nośniki identyfikator procesora. Aby uzyskać więcej informacji, zobacz [uzyskać procesory multimediów](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Tworzenie zadania z pojedynczym zadaniem kodowania

> [!NOTE]
> Podczas pracy z interfejsem API REST usługi Media Services, obowiązują następujące zastrzeżenia:
>
> Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora do tworzenia aplikacji interfejsu API REST usługi Media Services](media-services-rest-how-to-use.md).
>
> Gdy przy użyciu formatu JSON i określając do użycia **__metadata** — słowo kluczowe w żądaniu (na przykład, aby odwołanie obiektu połączonego), należy ustawić **Akceptuj** nagłówka do [format JSON trybu informacji pełnej](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.
>
>

Poniższy przykład pokazuje, jak utworzyć i opublikuj zadania z jednego zadania zestawu do zakodowania filmu w określonym rozwiązania i jakości. Podczas kodowania za pomocą usługi Media Encoder Standard, możesz użyć ustawienia konfiguracji zadań wstępne określone [tutaj](https://msdn.microsoft.com/library/mt269960).

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
Poniższy przykład pokazuje, jak ustawić atrybutu assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Zagadnienia do rozważenia
* Taskbody — właściwości należy użyć literał XML, aby zdefiniować liczbę dane wejściowe lub wyjściowe zasoby, które są używane przez zadanie. Ten artykuł zadań zawiera definicji schematu XML dla pliku XML.
* W definicji taskbody — wartość każdego wewnętrzny `<inputAsset>` i `<outputAsset>` musi być ustawiona jako JobInputAsset(value) lub JobOutputAsset(value).
* Zadanie może mieć wielu zasobów danych wyjściowych. Jeden JobOutputAsset(x) należy używać tylko jeden raz jako dane wyjściowe zadania w zadaniu.
* Możesz określić JobInputAsset lub JobOutputAsset jako wejściowego elementu zadania.
* Zadania muszą nie tworzą cyklu.
* Parametr wartości, który jest przekazywany do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu dla zasobu. Rzeczywiste zasoby są zdefiniowane w InputMediaAssets i OutputMediaAssets właściwości nawigacji na definicję jednostki zadania.
* Ponieważ usługa Media Services jest oparta na protokole OData v3, poszczególnych zasobów w kolekcji właściwości nawigacji InputMediaAssets i OutputMediaAssets są wywoływane za pośrednictwem "__metadata: identyfikator uri" pary nazwa wartość.
* InputMediaAssets jest mapowany na jeden lub więcej zasobów utworzonych w usłudze Media Services. OutputMediaAssets są tworzone przez system. Nie mogą odwoływać się istniejący zasób.
* Może być nazwany OutputMediaAssets za pomocą atrybutu assetName. Jeśli ten atrybut nie jest obecny, a następnie nazywa OutputMediaAsset niezależnie od tekst wewnętrzny wartość `<outputAsset>` element jest sufiksem Nazwa zadania wartość lub wartość identyfikatora zadania (w przypadku, w którym nie zdefiniowano właściwości Name). Na przykład jeśli ustawisz wartość assetName do "Przykładowy", następnie OutputMediaAsset nazwa właściwości jest równa "Przykładowy". Jednakże jeśli nie była ustawiana wartość assetName, ale ustawiona na nazwę zadania, aby "NewJob", nazwa OutputMediaAsset może być "_NewJob JobOutputAsset (wartość)".

## <a name="create-a-job-with-chained-tasks"></a>Tworzenie zadania za pomocą łańcuchowe zadania podrzędne
W wielu scenariuszach aplikacji deweloperzy chcą utworzyć serię zadań przetwarzania. W usłudze Media Services można utworzyć szereg łańcuchowe zadania podrzędne. Każde zadanie podrzędne wykonuje inne przetwarzanie kroki i mogą używają procesorów różnych mediów. Łańcuchowe zadania podrzędne oddać zasobu z jednego zadania do innego, wykonywanie liniowej sekwencji zadań w elemencie zawartości. Jednak zadania wykonywane w ramach zadania nie muszą znajdować się w sekwencji. Po utworzeniu połączonych zadań łańcuchowych **ITask** obiekty są tworzone w ramach pojedynczej **IJob** obiektu.

> [!NOTE]
> Obecnie istnieje limit 30 zadań na zadanie. Jeśli musisz utworzyć łańcuch ponad 30 zadań, należy utworzyć więcej niż jednego zadania zawiera zadania.
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
* Musi istnieć co najmniej jedno zadanie, których dane wejściowe znajdują się dane wyjściowe innego zadania w ramach zadania.

## <a name="use-odata-batch-processing"></a>Przetwarzanie wsadowe OData
Poniższy przykład pokazuje, jak używać przetwarzaniem wsadowym OData do tworzenia zadań i podzadań. Aby uzyskać informacji na temat przetwarzania wsadowego, zobacz [przetwarzanie wsadowe Open Data Protocol (OData)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

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



## <a name="create-a-job-by-using-a-jobtemplate"></a>Tworzenie zadania przy użyciu obiekt JobTemplate
Przetwarzanie wielu zasobów przy użyciu zestawu typowych zadań, użyj obiekt JobTemplate, aby określić ustawienia domyślne zadań wstępne lub ustawić kolejność zadań.

Poniższy przykład pokazuje, jak utworzyć obiekt JobTemplate za pomocą TaskTemplate, który jest zdefiniowany w tekście. TaskTemplate używa usługi Media Encoder Standard jako MediaProcessor do kodowania pliku elementu zawartości. Jednak inne MediaProcessors może służyć także.

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
> W przeciwieństwie do innych jednostek usługi Media Services możesz zdefiniować nowy identyfikator GUID dla każdego TaskTemplate i umieść go w taskTemplateId i właściwość identyfikatora w swojej treści żądania. Schemat identyfikacji zawartości, należy wykonać schemat opisanego w identyfikacji jednostek usługi multimediów Azure. Ponadto nie można zaktualizować JobTemplates. Zamiast tego należy utworzyć nową grupę za pomocą zaktualizowane zmiany.
>
>

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created

    . . .


Poniższy przykład pokazuje, jak utworzyć zadanie, który odwołuje się do identyfikatora obiekt JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Zaawansowane funkcje kodowania, aby zapoznać się z
* [Generowanie miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Przycinanie wideo podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawienia wstępne kodowania](media-services-custom-mes-presets-with-dotnet.md)
* [Nakładki lub znaku wodnego wideo z obrazem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz gdy wiesz, jak utworzyć zadanie kodowanie elementu zawartości, zobacz [sprawdzanie postępu zadania za pomocą usługi Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Zobacz także
[Pobierz procesory multimediów](media-services-rest-get-media-processor.md)
