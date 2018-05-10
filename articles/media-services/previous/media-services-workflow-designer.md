---
title: Tworzenie zaawansowanych przepływów pracy kodowania z projektanta przepływów pracy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu tworzenia zaawansowanych przepływów pracy kodowania z projektanta przepływów pracy.
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: d75a3b58934b3da05a15700ecaf82226b549fffa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Tworzenie zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy
## <a name="overview"></a>Przegląd
**Projektanta przepływów pracy** jest narzędziem pulpitu systemu Windows, które jest używane do projektowania i tworzenia niestandardowych przepływów pracy dla z kodowaniem **Media Encoder Premium w przepływie pracy**.
Przy użyciu zasilania narzędzia projektanta workflow, projektowania i tworzenia złożonych przepływów pracy, które będą uruchamiane w **Media Encoder Premium**.  

Przepływy pracy mogą zawierać logiki decyzji klienta i rozgałęzianie oparte na właściwościach pliku źródła danych wejściowych. Możesz utworzyć przepływy pracy z możliwym do zastąpienia właściwości i wartości dynamiczne nawet najbardziej złożoną zadania kodowania ułatwia powtórzeń i dostosowywanie w chmurze.

Przykład przepływy pracy, które możesz utworzyć obejmują:

* Przepływy pracy, które kontrolują zawartość źródłową do rozpoznawania i kodowanie tylko ścieżki żądanego wyniku oparte na decyzji.  Jest to helfpul przez wyeliminowanie nieużywanego ścieżki wygenerowanych przez upscaling inadvertantly zawartości źródłowej.
* Wiele plików wejściowych może służyć do obsługi podpisów, nakładki i łączenie zawartości razem. 

Tego narzędzia można również zmodyfikować naszych [opublikowane przepływy pracy](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Aby uzyskać kopię narzędzia projektanta przepływów pracy, skontaktuj się z mepd@microsoft.com.
> 
> 

Po utworzeniu pliku przepływu pracy mogą być przekazywane jako zasób, a następnie używany do kodowania plików multimedialnych. Aby uzyskać informacje na temat kodowania z **Media Encoder Premium w przepływie pracy** przy użyciu **.NET**, zobacz [zaawansowane kodowania w przepływie pracy Premium kodera multimediów](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Modyfikowanie istniejących przepływów pracy
Wartość domyślna [opublikowane przepływy pracy](media-services-workflow-designer.md#existing_workflows) można modyfikować za pomocą narzędzia projektanta. Domyślnie można pobrać pliki przepływu pracy [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Folder zawiera również opis tych plików.

Następujące filmy wideo pokazują, jak za pomocą projektanta.

### <a name="day-1--getting-started"></a>Dzień 1 — wprowadzenie
Obejmuje 1 dzień wideo:

* Omówienie projektanta
* Podstawowych przepływów pracy — "Hello World"
* Tworzenie wielu wyjściowe pliki MP4 używane przez usługi Azure Media Services przesyłania strumieniowego

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dzień 2
Wideo dzień 2 obejmuje:

* Różne scenariusze źródła plików — Obsługa audio
* Przepływy pracy z logiką zaawansowane
* Etapy wykresu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3 dni
Obejmuje 3 dni wideo:

* Wykonywanie skryptów wewnątrz przepływy pracy/plany
* Ograniczenia z bieżącego kodera
* FUNKCJA PYTANIA I ODPOWIEDZI

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Jeśli muszą obsługiwać lub pytań na temat tworzenia niestandardowych przepływów pracy w narzędziu Projektant przepływu pracy, Wyślij wiadomość e-mail na adres mepd@microsoft.com.

## <a name="see-also"></a>Zobacz też
[Filmy szkoleniowe projektanta przepływu pracy kodera Azure — warstwa Premium](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

