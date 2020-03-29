---
title: Jak zakodować zasób platformy Azure przy użyciu standardu Media Encoder | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać media encoder standard do kodowania zawartości multimedialnej w usłudze Azure Media Services. Przykłady kodu używają interfejsu API REST.
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
ms.openlocfilehash: 6854400f2152a5952a7b24dbd860d7ad4bfc943d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774915"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Jak zakodować zasób przy użyciu standardu Media Encoder
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [Reszta](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Omówienie

Aby dostarczać cyfrowe wideo przez Internet, należy skompresować nośniki. Cyfrowe pliki wideo są duże i mogą być zbyt duże, aby można je było wyświetlać przez Internet lub w urządzeniach klientów, aby mogły je wyświetlać prawidłowo. Kodowanie to proces kompresji wideo i audio, dzięki czemu klienci mogą wyświetlać multimedia.

Zadania kodowania są jedną z najczęstszych operacji przetwarzania w usłudze Azure Media Services. Tworzenie zadań kodowania do konwersji plików multimedialnych z jednego kodowania do drugiego. Podczas kodowania można użyć wbudowanego kodera usługi Media Services (Media Encoder Standard). Można również użyć kodera dostarczonego przez partnera usługi Media Services. Kodery innych firm są dostępne za pośrednictwem portalu Azure Marketplace. Szczegóły zadań kodowania można określić za pomocą wstępnie ustawionych ciągów zdefiniowanych dla kodera lub przy użyciu wstępnie ustawionych plików konfiguracyjnych. Aby wyświetlić typy ustawień predefiniowanych, zobacz [Ustawienia predefiniowane zadań dla standardu kodera multimediów](https://msdn.microsoft.com/library/mt269960).

Każde zadanie może mieć jedno lub więcej zadań w zależności od typu przetwarzania, które chcesz wykonać. Za pośrednictwem interfejsu API REST można tworzyć zadania i związane z nimi zadania na jeden z dwóch sposobów:

* Zadania można zdefiniować w linii za pomocą właściwości Nawigacji zadania w encjach Zadania.
* Użyj przetwarzania wsadowego OData.

Zalecamy, aby zawsze kodować pliki źródłowe do adaptacyjnego zestawu MP4 o szybkości transmisji bitów, a następnie konwertować zestaw do żądanego formatu przy użyciu [dynamicznego pakowania](media-services-dynamic-packaging-overview.md).

Jeśli zasób wyjściowy jest zaszyfrowany magazyn, należy skonfigurować zasady dostarczania zasobów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md).

Przed rozpoczęciem odwoływania się do procesorów multimediów sprawdź, czy masz poprawny identyfikator procesora multimediów. Aby uzyskać więcej informacji, zobacz [Temat Ład o procesorach multimediów](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

## <a name="create-a-job-with-a-single-encoding-task"></a>Tworzenie zadania z jednym zadaniem kodowania

> [!NOTE]
> Podczas pracy z interfejsem API REST usługi Media Services obowiązują następujące zagadnienia:
>
> Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dla programu Media Services REST API development](media-services-rest-how-to-use.md).
>
> W przypadku używania JSON i określania użycia **__metadata** słowa kluczowego w żądaniu (na przykład w celu odwołania się do połączonego obiektu), należy ustawić nagłówek **Akceptuj** na [format JSON Verbose:](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)Accept: application/json;odata=verbose.
>
>

W poniższym przykładzie pokazano, jak utworzyć i opublikować zadanie z jednym zadaniem ustawionym do kodowania wideo w określonej rozdzielczości i jakości. Podczas kodowania za pomocą standardu kodera multimediów można użyć predefiniowanych ustawień konfiguracji zadań określonych [w tym miejscu](https://msdn.microsoft.com/library/mt269960).

Żądanie:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Odpowiedź:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Ustawianie nazwy zasobu wyjściowego
W poniższym przykładzie pokazano, jak ustawić atrybut assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Zagadnienia do rozważenia
* Właściwości TaskBody musi używać dosłownego XML do definiowania liczby danych wejściowych lub zasobów wyjściowych, które są używane przez zadanie. Artykuł o zadaniu zawiera definicję schematu XML dla pliku XML.
* W definicji TaskBody każda `<inputAsset>` wartość `<outputAsset>` wewnętrzna dla i musi być ustawiona jako JobInputAsset(value) lub JobOutputAsset(value).
* Zadanie może mieć wiele zasobów wyjściowych. Jeden zestaw JobOutputAsset(x) może być użyty tylko raz jako dane wyjściowe zadania w zadaniu.
* Zasób wejściowy zadania można określić jako zasób wejściowy zadania.
* Zadania nie mogą tworzyć cyklu.
* Parametr wartości przekazywalny do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu dla środka trwałego. Rzeczywiste zasoby są zdefiniowane w InputMediaAssets i OutputMediaAssets właściwości nawigacji w definicji encji zadania.
* Ponieważ usługi Media Services jest zbudowany na OData v3, poszczególnych zasobów w InputMediaAssets i OutputMediaAssets właściwości nawigacji kolekcji są odwoływane za pośrednictwem "__metadata: uri" nazwa-wartość pary.
* InputMediaAssets jest mapowane do jednego lub więcej zasobów utworzonych w programie Media Services. Zestawy OutputMediaAs są tworzone przez system. Nie odwołują się do istniejącego zasobu.
* Zestawy OutputMediaAssets można nazwać przy użyciu atrybutu assetName. Jeśli ten atrybut nie jest obecny, a następnie nazwa OutputMediaAsset jest `<outputAsset>` niezależnie od wewnętrznej wartości tekstowej elementu jest z sufiksem albo wartość nazwa zadania lub wartość identyfikatora zadania (w przypadku, gdy Name właściwość nie jest zdefiniowana). Na przykład jeśli ustawisz wartość assetName na "Przykład", właściwość OutputMediaAsset Name jest ustawiona na "Przykład". Jeśli jednak nie ustawiono wartości dla assetName, ale ustawiono nazwę zadania na "NewJob", a następnie OutputMediaAsset Nazwa będzie "JobOutputAsset(value)_NewJob."

## <a name="create-a-job-with-chained-tasks"></a>Tworzenie zadania z zadaniami łańcuchowymi
W wielu scenariuszach aplikacji deweloperzy chcą utworzyć serię zadań przetwarzania. W umiarzonych usługach Media Services można utworzyć serię zadań łańcuchowych. Każde zadanie wykonuje różne kroki przetwarzania i może korzystać z różnych procesorów multimediów. Zadania łańcuchowe mogą przekazywać zasób z jednego zadania do drugiego, wykonując liniową sekwencję zadań na zasobie. Jednak zadania wykonywane w zadaniu nie muszą być w sekwencji. Podczas tworzenia zadania łańcuchowego, połączone obiekty **ITask** są tworzone w jednym obiekcie **IJob.**

> [!NOTE]
> Obecnie istnieje limit 30 zadań na zadanie. Jeśli chcesz utworzyć łańcuch więcej niż 30 zadań, utwórz więcej niż jedno zadanie, aby zawierać zadania.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
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
Aby włączyć tworzenie łańcucha zadań:

* Zadanie musi mieć co najmniej dwa zadania.
* Musi istnieć co najmniej jedno zadanie, którego dane wejściowe są dane wyjściowe innego zadania w zadaniu.

## <a name="use-odata-batch-processing"></a>Użyj przetwarzania wsadowego OData
W poniższym przykładzie pokazano, jak używać przetwarzania wsadowego OData do tworzenia zadania i zadań. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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
    x-ms-version: 2.19
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
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Tworzenie zadania przy użyciu tablicy zlecenia
Podczas przetwarzania wielu zasobów przy użyciu wspólnego zestawu zadań, użyj JobTemplate, aby określić domyślne ustawienia predefiniowane zadań lub ustawić kolejność zadań.

W poniższym przykładzie pokazano, jak utworzyć JobTemplate z TaskTemplate, który jest zdefiniowany w wierszu. TaskTemplate używa media encoder standard jako MediaProcessor do kodowania pliku zasobu. Jednak inne MediaProcessors mogą być również używane.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> W przeciwieństwie do innych jednostek usługi Media Services należy zdefiniować nowy identyfikator GUID dla każdego tasktemplate i umieścić go w taskTemplateId i Id właściwości w treści żądania. Schemat identyfikacji zawartości musi być zgodny ze schematem opisanym w obszarze Identyfikowanie jednostek usługi Azure Media Services. Ponadto nie można zaktualizować jobtemplates. Zamiast tego należy utworzyć nowy z zaktualizowanymi zmianami.
>
>

W przypadku powodzenia zwracana jest następująca odpowiedź:

    HTTP/1.1 201 Created

    . . .


W poniższym przykładzie pokazano, jak utworzyć zadanie, które odwołuje się do identyfikatora JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


W przypadku powodzenia zwracana jest następująca odpowiedź:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Zaawansowane funkcje kodowania do zbadania
* [Jak generować miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generowanie miniatur podczas kodowania](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Przycinanie klipów wideo podczas kodowania](media-services-crop-video.md)
* [Dostosowywanie ustawień predefiniowanych kodowania](media-services-custom-mes-presets-with-dotnet.md)
* [Nakładanie lub znak wodny wideo z obrazem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz, jak utworzyć zadanie do kodowania zasobu, zobacz [Jak sprawdzić postęp zadania w programie Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Zobacz też
[Pobierz procesory multimediów](media-services-rest-get-media-processor.md)
