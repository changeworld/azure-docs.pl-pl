---
title: Jak utworzyć procesor multimediów przy użyciu zestawu SDK usługi Azure Media Services dla platformy .NET| Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć składnik procesora multimediów do kodowania, konwertowania formatu, szyfrowania lub odszyfrowywania zawartości multimedialnej dla usługi Azure Media Services. Przykłady kodu są zapisywane w języku C# i używają zestawu SDK usług multimedialnych dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463850"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak: Pobierz wystąpienie procesora multimediów
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Reszta](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Omówienie
W umywoiń programie Media Services procesor multimediów jest składnikiem obsługującym określone zadanie przetwarzania, takie jak kodowanie, konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. Procesor multimediów jest zazwyczaj tworzęny podczas tworzenia zadania kodowania, szyfrowania lub konwertowania formatu zawartości multimedialnej.

## <a name="azure-media-processors"></a>Procesory multimediów platformy Azure 

Poniższy temat zawiera listy procesorów multimediów:

* [Kodowanie procesorów multimediów](scenarios-and-availability.md#encoding-media-processors)
* [Procesory multimediów usługi analizy](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Pobierz procesor multimediów

Poniższa metoda pokazuje, jak uzyskać wystąpienie procesora multimediów. Przykład kodu zakłada użycie zmiennej na poziomie modułu o nazwie **_context** do odwoływania się do kontekstu serwera, jak opisano w sekcji [Jak: Łączenie się z usługą Media Services programowo](media-services-use-aad-auth-to-access-ams-api.md).

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
Teraz, gdy wiesz, jak uzyskać wystąpienie procesora multimediów, przejdź do tematu [Jak zakodować zasoby,](media-services-dotnet-encode-with-media-encoder-standard.md) który pokaże, jak za pomocą media encoder standard do kodowania zasobu.

