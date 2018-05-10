---
title: Jak utworzyć procesor multimediów przy użyciu zestawu SDK usługi Azure Media Services dla platformy .NET | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia nośnika składnika procesora do kodowania, przekonwertować format, szyfrowania lub odszyfrowywania zawartości nośnika dla usługi Azure Media Services. Przykłady kodu są napisane w języku C# i używają SDK usługi Media Services dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 60da450c11a2e65d96c15798854adfef371a694f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-get-a-media-processor-instance"></a>Porady: uzyskiwanie wystąpienia procesora nośnika
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Przegląd
W usłudze Media Services, który procesor multimediów jest składnikiem, który obsługuje przetwarzania specyficznego dla zadania, takie jak kodowanie format konwersji, szyfrowania lub odszyfrowywania zawartości nośnika. Podczas tworzenia zadania kodowania, szyfrowania lub przekonwertować format zawartości multimedialnej zwykle utworzyć procesor multimediów.

## <a name="azure-media-processors"></a>Procesory multimediów Azure 

Poniższy temat zawiera listę procesory multimediów:

* [Kodowanie procesory multimediów](scenarios-and-availability.md#encoding-media-processors)
* [Procesory multimediów usługi analiza](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Pobierz procesor multimediów

Następująca metoda pokazano, jak pobrać wystąpienia procesora nośnika. Przykład kodu zakłada użycie zmiennej poziom modułu o nazwie **_kontekstu** do odwołania z kontekstem serwera, zgodnie z opisem w sekcji [porady: nawiązać Media Services programowo](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz, jak uzyskać wystąpienia procesora nośnika, przejdź do [jak kodowanie elementu zawartości](media-services-dotnet-encode-with-media-encoder-standard.md) tematu, w której opisano, jak na potrzeby Media Encoder Standard kodowanie elementu zawartości.

