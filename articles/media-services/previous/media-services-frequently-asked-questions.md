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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3164445dc28a2436d8492902d865414da8d75702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463919"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

W tym artykule opisano często zadawane pytania zgłoszone przez społeczność użytkowników usługi Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Często zadawane pytania ogólne usługi AMS

Pyt.: Jak można strumienia do urządzeń z systemem iOS firmy Apple?

Odpowiedź: Dodaj "(format = m3u8-aapl)" Ścieżka "/ Manifest" część adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego do zwrócenia wstecz zawartości HLS do użycia w systemie iOS firmy Apple urządzenia z natywnym (Aby uzyskać więcej informacji, zobacz [dostarczania zawartości](media-services-deliver-content-overview.md)),

Pyt.: Jak można skalować, indeksowanie?

Odp.: Zastrzeżone jednostki są takie same, kodowanie i indeksowanie zadań. Postępuj zgodnie z instrukcjami wyświetlanymi na [sposób skalowania zastrzeżonych jednostek kodowania](media-services-scale-media-processing-overview.md). **Uwaga** czy wydajność indeksatora nie oddziałuje zastrzeżony typ jednostki.

Pyt.: I przekazany, zakodowany i opublikowany film wideo. Jaki byłyby Przyczyna wideo nie jest odtwarzany podczas próby Prześlij go strumieniowo?

Odp.: Jedną z najbardziej typowe przyczyny to Ty nie ma punktu końcowego przesyłania strumieniowego, z którego chcesz odtworzyć **systemem** stanu.  

Pyt.: Możliwości składania na strumień na żywo?

Odp.: Składania strumieni na żywo aktualnie nie jest dostępna w usłudze Azure Media Services, dlatego należy wcześniej została utworzona na tym komputerze.

Pyt.: Czy można używać usługi Azure CDN w transmisji strumieniowej na żywo?

Odp.: Usługa Media Services obsługuje integrację z usługą Azure CDN (Aby uzyskać więcej informacji, zobacz [jak zarządzać punkty końcowe przesyłania strumieniowego w konto usługi Media Services](media-services-portal-manage-streaming-endpoints.md)).  Można użyć na żywo, przesyłanie strumieniowe przy użyciu usługi CDN. Usługa Azure Media Services udostępnia Smooth Streaming, HLS i MPEG-DASH danych wyjściowych. Wszystkie te formaty korzystanie z protokołu HTTP do przesyłania danych i Uzyskaj korzyści o wartości buforowanie HTTP. W transmisji strumieniowej na żywo rzeczywistych danych audio/wideo jest podzielony na fragmenty i tym poszczególne fragmenty Pobieranie pamięci podręcznej w usłudze CDN. Tylko dane muszą być odświeżane są dane manifestu. Usługa CDN okresowo odświeża dane manifestu.

Pyt.: Usługa Azure Media services obsługuje przechowywania obrazów?

Odp.: Jeśli po prostu chcesz przechowywać obrazy JPEG lub PNG, należy zachować w usłudze Azure Blob Storage. Nie ma żadnych korzyści, umieszczając je w konto usługi Media Services, chyba że chcesz zachować skojarzonych zasobów Audio lub wideo. Czy może zajść konieczność stosowania obrazów jako nakładki w koder wideo. Usługi Media Encoder Standard obsługuje nałożenie obrazy na podstawie wideo, a to, co Wyświetla listę JPEG lub PNG jako obsługiwane formaty wejściowych. Aby uzyskać więcej informacji, zobacz [Tworzenie nakładek](media-services-advanced-encoding-with-mes.md#overlay).

Pyt.: Jak skopiować zasobów z jednego konta usługi Media Services do innej?

Odp.: Związane z kopiowaniem zasobów z jednego konta usługi Media Services do innej przy użyciu platformy .NET, użyj [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) metody rozszerzenia są dostępne w [rozszerzenia SDK .NET usługi Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/) repozytorium. Aby uzyskać więcej informacji, zobacz [to](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) wątku forum.

Pyt.: Co to są obsługiwane znaki nazewnictwa plików, pracując przy użyciu usługi AMS?

Odp.: Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent nie jest dozwolone. Wartość **nazwa** właściwość nie może zawierać żadnych z następujących [procent kodowanie — zastrzeżone znaki](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * "();: @& = + $, /? [] % #". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.

Pyt.: Jak połączyć przy użyciu interfejsu REST?

Odp.: Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Pyt.: Jak obrócić wideo podczas kodowania

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
