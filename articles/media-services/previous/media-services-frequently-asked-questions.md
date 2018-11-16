---
title: Usługa Azure Media Services — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania (FAQ)
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bf5bc66ce55b0b9d6095cd395a11f68b40af1639
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685722"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

W tym artykule opisano często zadawane pytania zgłoszone przez społeczność użytkowników usługi Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Często zadawane pytania ogólne usługi AMS

P: jak należy strumienia do urządzeń z systemem iOS firmy Apple

Odpowiedź: Dodaj "(format = m3u8-aapl)" Ścieżka "/ Manifest" część adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego do zwrócenia wstecz zawartości HLS do użycia w systemie iOS firmy Apple urządzenia z natywnym (Aby uzyskać więcej informacji, zobacz [dostarczania zawartości](media-services-deliver-content-overview.md)),

P: jak można skalować, indeksowanie?

Odpowiedź zastrzeżone jednostki są takie same, kodowanie i indeksowanie zadań. Postępuj zgodnie z instrukcjami wyświetlanymi na [sposób skalowania zastrzeżonych jednostek kodowania](media-services-scale-media-processing-overview.md). **Uwaga** czy wydajność indeksatora nie oddziałuje zastrzeżony typ jednostki.

P: czy mogę przekazany, zakodowany i opublikowany film wideo. Jaki byłyby Przyczyna wideo nie jest odtwarzany podczas próby Prześlij go strumieniowo?

Odp.: jednym z najbardziej typowych przyczyn jest bez punktu końcowego przesyłania strumieniowego, z którego próbujesz odtwarzania w **systemem** stanu.  

P: czy można zrobić składania na strumień na żywo?

A: składania strumieni na żywo aktualnie nie jest dostępna w usłudze Azure Media Services, dlatego należy wcześniej została utworzona na tym komputerze.

P: czy mogę używać usługi Azure CDN transmisji strumieniowej na żywo?

Odp.: usługa Media Services obsługuje integrację z usługą Azure CDN (Aby uzyskać więcej informacji, zobacz [jak zarządzać punkty końcowe przesyłania strumieniowego w konto usługi Media Services](media-services-portal-manage-streaming-endpoints.md)).  Można użyć na żywo, przesyłanie strumieniowe przy użyciu usługi CDN. Usługa Azure Media Services udostępnia Smooth Streaming, HLS i MPEG-DASH danych wyjściowych. Wszystkie te formaty korzystanie z protokołu HTTP do przesyłania danych i Uzyskaj korzyści o wartości buforowanie HTTP. W transmisji strumieniowej na żywo rzeczywistych danych audio/wideo jest podzielony na fragmenty i tym poszczególne fragmenty Pobieranie pamięci podręcznej w usłudze CDN. Tylko dane muszą być odświeżane są dane manifestu. Usługa CDN okresowo odświeża dane manifestu.

P: czy jest usługi Azure Media services obsługuje przechowywania obrazów?

Odp.: Jeśli po prostu chcesz przechowywać obrazy JPEG lub PNG, należy zachować w usłudze Azure Blob Storage. Nie ma żadnych korzyści, umieszczając je w konto usługi Media Services, chyba że chcesz zachować skojarzonych zasobów Audio lub wideo. Czy może zajść konieczność stosowania obrazów jako nakładki w koder wideo. Usługi Media Encoder Standard obsługuje nałożenie obrazy na podstawie wideo, a to, co Wyświetla listę JPEG lub PNG jako obsługiwane formaty wejściowych. Aby uzyskać więcej informacji, zobacz [Tworzenie nakładek](media-services-advanced-encoding-with-mes.md#overlay).

P: jak mogę skopiować zasoby z jednego konta usługi Media Services do innego.

Odp.: związane z kopiowaniem zasobów z jednego konta usługi Media Services do innej przy użyciu platformy .NET, użyj [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) metody rozszerzenia są dostępne w [rozszerzenia SDK .NET usługi Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/) repozytorium. Aby uzyskać więcej informacji, zobacz [to](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) wątku forum.

Pytanie: jakie są obsługiwane znaki nazewnictwa plików, pracując przy użyciu usługi AMS?

Odp.: usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent nie jest dozwolone. Wartość **nazwa** właściwość nie może zawierać żadnych z następujących [procent kodowanie — zastrzeżone znaki](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * "();: @& = + $, /? [] % #". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.

Pytanie: jak połączyć przy użyciu interfejsu REST?

Odp.: Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

P: jak obrócić wideo podczas procesu kodowania.

Odp.: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje obrotu kątami 90/180/270. Domyślne zachowanie to "Auto", gdzie próbuje wykryć metadanych obrotu w pliku w formacie MP4/MOV przychodzących, a także kompensuje ona. Obejmują następujące elementy **źródeł** elementu wstępne json, zdefiniowana [tutaj](media-services-mes-presets-overview.md):

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
