---
title: Tworzenie zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie tworzenia zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: 0ade52d3ae9714f2b370308253e455bcde7ac7a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825136"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Tworzenie zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy  
## <a name="overview"></a>Omówienie
**Projektanta przepływów pracy** to narzędzie pulpitu Windows, która umożliwia projektowanie i tworzenie niestandardowych przepływów pracy kodowania za pomocą **Media Encoder Premium Workflow**.
Korzystając z możliwości narzędzie projektanta przepływu pracy, można projektować i tworzyć złożone przepływy pracy, które będą uruchamiane w **Media Encoder Premium**.  

Przepływy pracy mogą zawierać logiki wyboru klientów i rozgałęzień na podstawie pliku źródła danych wejściowych właściwości. Możesz utworzyć przepływy pracy z możliwym do zastąpienia właściwości i wartości dynamiczne nawet najbardziej złożonych zadań kodowania ułatwia Powtórz i dostosowania dostępnych w chmurze.

Przykład przepływów pracy, które można utworzyć, obejmują:

* Decyzji na podstawie przepływów pracy, które sprawdzić zawartość źródłową do rozpoznawania i kodowanie tylko ścieżki żądaną produktu wyjściowego.  Jest to przydatne, eliminując nieużywanego ścieżek, które może zostać wygenerowany przez przypadkowo upscaling zawartość źródłową.
* Wiele plików wejściowych może służyć do obsługi transkrypcji, nakładki i łączenie razem zawartości. 

To narzędzie może również modyfikować żadnego z naszych [opublikowane przepływów pracy](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Aby uzyskać swoją kopię narzędzia projektanta przepływów pracy, skontaktuj się z pomocą mepd@microsoft.com.

Po utworzeniu pliku przepływu pracy można przekazać jako zasób usługi, a następnie używane do kodowania plików multimedialnych. Aby uzyskać informacje na temat kodowania za pomocą **Media Encoder Premium Workflow** przy użyciu **.NET**, zobacz [Zaawansowane kodowanie za pomocą Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Modyfikowanie istniejących przepływów pracy
Wartość domyślna [opublikowane przepływy pracy](media-services-workflow-designer.md#existing_workflows) można modyfikować przy użyciu narzędzia Projektant. Domyślnie można uzyskać pliki przepływu pracy [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Folder zawiera również opis tych plików.

Następujące filmy wideo pokazują, jak używać projektanta.

### <a name="day-1--getting-started"></a>Dzień 1 — wprowadzenie
1. dnia wideo obejmuje:

* Omówienie projektanta
* Podstawowych przepływów pracy — "Hello World"
* Tworzenie wielu danych wyjściowych plików MP4 do użycia z usługą przesyłania strumieniowego usługi Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2 dni
2 dni wideo obejmuje:

* Różne scenariusze pliku źródłowego — Obsługa audio
* Przepływy pracy za pomocą zaawansowanych logiki
* Etapy wykresu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3 dni
Wideo w dniu 3 obejmuje:

* Wykonywanie skryptów w przepływach pracy/plany
* Ograniczenia z użyciem kodera w bieżącym
* PYTANIA I ODPOWIEDZI

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Jeśli potrzebujesz pomocy technicznej lub pytań dotyczących tworzenia niestandardowych przepływów pracy w narzędziu Projektant przepływu pracy, Wyślij wiadomość e-mail do mepd@microsoft.com.

## <a name="see-also"></a>Zobacz też
[Filmy szkoleniowe projektanta systemu Azure w warstwie Premium Encoder przepływu pracy](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

