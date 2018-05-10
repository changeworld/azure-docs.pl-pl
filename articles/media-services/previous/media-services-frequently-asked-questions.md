---
title: Usługa Azure Media Services — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania (FAQ)
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: juliako
ms.openlocfilehash: a47163d06e24814ca5724d1fabea84058f8764cf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

W tym artykule opisano często zadawane pytania zgłoszone przez społeczność użytkowników usługi Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Często zadawane pytania ogólne AMS

Pytanie: jak przesyłania strumieniowego do urządzeń z systemem iOS firmy Apple

A: Dodaj "(format = m3u8-aapl)" Ścieżka do części "/ Manifest" adres URL przesyłania strumieniowego serwerze źródłowym do zwrócenia wstecz zawartości HLS wykorzystania w systemie iOS firmy Apple urządzenia natywnego (Aby uzyskać szczegółowe informacje, zobacz (dostarczania zawartości) [Poinformuj Media usług dostarczyć zawartości overview.md]),

Pytanie: jak można skalować, indeksowania?

Odpowiedź: jednostki zarezerwowanego są takie same dla zadania kodowania i indeksowania. Postępuj zgodnie z instrukcjami wyświetlanymi [jak kodowanie zarezerwowanych jednostek skalowania](media-services-scale-media-processing-overview.md). **Uwaga** czy wydajność indeksatora nie ma wpływu na typ jednostki zarezerwowane.

Pytanie: I przekazany, zakodowany i publikowane wideo. Jakie byłyby Przyczyna wideo nie jest odtwarzany podczas próby strumienia go?

Odpowiedź: jednym z najbardziej typowych przyczyn jest nie ma punktu końcowego przesyłania strumieniowego, z którym próbujesz się odtwarzanie w **systemem** stanu.  

Pytanie: czy można składania na strumień na żywo?

A: składania na strumienie na żywo aktualnie nie jest oferowany usługi Azure Media Services, dlatego należy wcześniej została utworzona na komputerze.

Pytanie: czy za pomocą usługi Azure CDN transmisji strumieniowej na żywo?

Odpowiedź: Media Services obsługuje integrację z usługą Azure CDN (Aby uzyskać więcej informacji, zobacz [jak zarządzać punktami końcowymi przesyłania strumieniowego w konta usługi Media Services](media-services-portal-manage-streaming-endpoints.md)).  Możesz użyć Live streaming sieci CDN. Azure Media Services udostępnia Smooth Streaming, HLS i MPEG-DASH danych wyjściowych. Wszystkie te formaty do przesyłania danych za pomocą protokołu HTTP i uzyskać korzyści buforowania HTTP. W rzeczywistych danych audio/wideo jest podzielona na fragmenty, a to poszczególne fragmenty uzyskać zbuforowana w sieci CDN strumieniowych na żywo. Tylko danych należy odświeżyć to dane manifestu. CDN okresowo odświeża dane manifestu.

Pytanie: jest usługa Azure Media services obsługuje przechowywania obrazów?

Odpowiedź: Jeśli chcesz tylko do przechowywania obrazów JPEG lub PNG, należy przechowywać w magazynie obiektów Blob Azure. Rozwiązanie nie przynosi żadnych do umieszczania ich w konta usługi Media Services, chyba że chcesz zachować skojarzone z wideo lub Audio zasoby. Lub jeśli może zajść potrzeba użycia obrazów jako nakładki na koder wideo. Media Encoder Standard obsługuje nakładanie obrazów na górze wideo i to, co wyświetlane JPEG lub PNG jako obsługiwane formaty wejściowych. Aby uzyskać więcej informacji, zobacz [tworzenie nakładki](media-services-advanced-encoding-with-mes.md#overlay).

Pytanie: jak mogę Kopiowanie zasobów z jednego konta usługi Media Services do innego.

A: Aby kopiowanie zasobów z jednego konta usługi Media Services do innego przy użyciu platformy .NET, użyj [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) — metoda rozszerzenia dostępne w [rozszerzenia SDK .NET usługi Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/) repozytorium. Aby uzyskać więcej informacji, zobacz [to](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) forum wątku.

Pytanie: jakie są obsługiwane znaki nazewnictwa plików podczas pracy z AMS?

Odpowiedź: Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresy URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent jest niedozwolone. Wartość **nazwa** właściwość nie może mieć następujące [procent kodowanie zarezerwowanych znaków](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.

Pytanie: jak połączyć, używając REST?

A: Aby uzyskać informacje na temat nawiązywania połączenia z interfejsu API usług AMS, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Pytanie: jak obrócić wideo podczas kodowania.

Odpowiedź: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje obrotu kątami 90/180/270. Domyślne zachowanie to "Auto", gdy próbuje wykryć metadanych obrotu w pliku MP4/MOV przychodzących, a także kompensuje go. Obejmują **źródeł** elementu predefiniowanych json zdefiniowane [tutaj](media-services-mes-presets-overview.md):

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
