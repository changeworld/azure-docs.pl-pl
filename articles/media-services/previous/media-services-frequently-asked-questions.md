---
title: Często zadawane pytania dotyczące usługi Azure Media Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: bdb5a43ad6669bfcd6e93ef4e3bf1646314e4606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705874"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Media Services w wersji 2

Ten artykuł dotyczy często zadawanych pytań zgłaszanych przez społeczność użytkowników usługi Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Ogólne często zadawane pytania dotyczące ams

P: Jak przesyłać strumieniowo na urządzenia z systemem Apple iOS?

Odp.: dodaj ścieżkę "(format=m3u8-aapl)" do części "/Manifest" adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego o zwrot zawartości HLS do użytku na urządzeniach natywnych apple iOS (szczegółowe informacje można znaleźć w [przypadku dostarczania zawartości),](media-services-deliver-content-overview.md)

P: Jak skalować indeksowanie?

Odp.: Zarezerwowane jednostki są takie same dla zadań kodowania i indeksowania. Postępuj zgodnie z instrukcjami [dotyczącymi skalowania kodowania jednostek zarezerwowanych](media-services-scale-media-processing-overview.md). **Należy zauważyć,** że na wydajność indeksatora nie ma wpływu typ jednostki zarezerwowanej.

P: Przesłałem, zakodowałem i opublikowałem film. Jaki byłby powód, dla którego film nie jest odtwarzany, gdy próbuję go przesyłać strumieniowo?

Odp.: Jednym z najczęstszych powodów jest to, że nie masz punktu końcowego przesyłania strumieniowego, z którego próbujesz odtworzyć w stanie **Uruchamianie.**  

P: Czy mogę zrobić kompozycję na żywo?

Odp.: Tworzenie kompozycji na żywo strumieni nie jest obecnie oferowane w usłudze Azure Media Services, więc należy wstępnie skomponować na komputerze.

Pyt.: Czy mogę używać usługi Azure CDN z przesyłania strumieniowego na żywo?

Odp.: Usługa Media Services obsługuje integrację z usługą Azure CDN (aby uzyskać więcej informacji, zobacz [Jak zarządzać punktami końcowymi przesyłania strumieniowego na koncie usługi Media Services).](media-services-portal-manage-streaming-endpoints.md)  Możesz używać transmisji strumieniowej na żywo z cdn. Usługa Azure Media Services zapewnia płynne przesyłanie strumieniowe, hls i mpeg-dash wyjść. Wszystkie te formaty używają protokołu HTTP do przesyłania danych i uzyskać korzyści z buforowania HTTP. W streamingu na żywo rzeczywiste dane wideo/audio są dzielone na fragmenty i te poszczególne fragmenty są buforowane w sieci CDN. Tylko dane muszą być odświeżane jest dane manifestu. Sieć CDN okresowo odświeża dane manifestu.

Pyt.: Czy usługi Azure Media obsługują przechowywanie obrazów?

Odp.: Jeśli po prostu chcesz przechowywać obrazy JPEG lub PNG, należy zachować te w usłudze Azure Blob Storage. Nie ma żadnych korzyści z umieszczenia ich na koncie usługi Media Services, chyba że chcesz zachować ich powiązanie z zasobami wideo lub audio. Lub jeśli może być konieczne użycie obrazów jako nakładek w koderze wideo. Media Encoder Standard obsługuje nakładanie obrazów na filmy, i to jest to, co wymienia JPEG i PNG jako obsługiwane formaty wejściowe. Aby uzyskać więcej informacji, zobacz [Tworzenie nakładek](media-services-advanced-encoding-with-mes.md#overlay).

Pyt.: Jak skopiować zasoby z jednego konta usługi Media Services do innego?

Odp.: Aby skopiować zasoby z jednego konta usługi Media Services do innego przy użyciu platformy .NET, użyj metody rozszerzenia [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) dostępnej w repozytorium [rozszerzeń zestawu Uszec .NET usługi Media Services.](https://github.com/Azure/azure-sdk-for-media-services-extensions/) Aby uzyskać więcej informacji, zobacz [ten](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) wątek na forum.

Pyt.: Jakie są obsługiwane znaki do nazewnictwa plików podczas pracy z ams?

Odp.: Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL zawartości przesyłanej strumieniowo (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters).) Z tego powodu kodowanie procentowe jest niedozwolone. Wartość **Name** właściwości nie może mieć żadnego z następujących [znaków zarezerwowanych do kodowania procentowego:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;@&=+$,/?%#[]". Ponadto, nie może być tylko jeden "". dla rozszerzenia nazwy pliku.

P: Jak połączyć się za pomocą REST?

Odp.: Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz Dostęp do [interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

P: Jak mogę obrócić wideo podczas procesu kodowania?

O: Standard [emowania multimediów](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje obrót pod kątem 90/180/270. Domyślnym zachowaniem jest "Auto", gdzie próbuje wykryć metadane obrotu w przychodzącym pliku MP4/MOV i skompensować go. Dołącz następujący element **Źródła** do jednego z predefiniowanych ustawień json zdefiniowanych [w tym miejscu:](media-services-mes-presets-overview.md)

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
