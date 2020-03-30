---
title: Tworzenie zaawansowanych przepływów pracy kodowania za pomocą Projektanta przepływów pracy | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć zaawansowane przepływy pracy kodowania za pomocą Projektanta przepływów pracy.
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
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 4dceb558532305c6d2e84563e25ab05508423090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72801941"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Tworzenie zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy  
## <a name="overview"></a>Omówienie
**Projektant przepływu pracy** to narzędzie pulpitu systemu Windows używane do projektowania i tworzenia niestandardowych przepływów pracy do kodowania za pomocą przepływu pracy Media **Encoder Premium .**
Korzystając z mocy narzędzia projektanta przepływu pracy, można projektować i tworzyć złożone przepływy pracy, które będą uruchamiane w **programie Media Encoder Premium**.  

Przepływy pracy mogą obejmować logikę decyzji klienta i rozgałęzienia na podstawie właściwości wejściowego pliku źródłowego. Przepływy pracy można tworzyć z nadrzędnymi właściwościami i wartościami dynamicznymi, aby nawet najbardziej złożone zadania kodowania były łatwe do powtórzenia i dostosowania w chmurze.

Przykładowe przepływy pracy, które można utworzyć, obejmują:

* Przepływy pracy oparte na decyzjach, które sprawdzają zawartość źródłową pod kątem rozdzielczości i kodują tylko żądane ścieżki wyjściowe.  Jest to przydatne przez wyeliminowanie zmarnowanych ścieżek, które zostaną wygenerowane przez skalowanie zawartości źródłowej przypadkowo.
* Wiele plików wejściowych może być używanych do obsługi podpisów, nakładek i łączenia zawartości. 

To narzędzie może być również używane do modyfikowania dowolnego z naszych [opublikowanych przepływów pracy.](media-services-workflow-designer.md#existing_workflows) 

> [!NOTE]
> Aby uzyskać kopię narzędzia Projektant przepływu pracy, skontaktuj się z firmą mepd@microsoft.com.

Po utworzeniu pliku przepływu pracy można go przekazać jako zasób, a następnie użyć go do kodowania plików multimedialnych. Aby uzyskać informacje na temat kodowania za pomocą **przepływu pracy usługi Media Encoder Premium** przy użyciu platformy **.NET,** zobacz [Zaawansowane kodowanie za pomocą przepływu pracy usługi Media Encoder Premium](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Modyfikowanie istniejących przepływów pracy
Domyślne [opublikowane przepływy pracy](media-services-workflow-designer.md#existing_workflows) można modyfikować za pomocą narzędzia projektanta. W [tym](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)miejscu można uzyskać domyślne pliki przepływu pracy . Folder zawiera również opis tych plików.

W poniższych klipach wideo pokazano, jak używać projektanta.

### <a name="day-1--getting-started"></a>Dzień 1 – Pierwsze kroki
Dzień 1 okładki wideo:

* Przegląd projektanta
* Podstawowe przepływy pracy – "Hello World"
* Tworzenie wielu wyjściowych plików MP4 do użytku z przesyłaniem strumieniowym usługi Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dzień 2.
Dzień 2 okładki wideo:

* Różne scenariusze plików źródłowych — obsługa dźwięku
* Przepływy pracy z zaawansowaną logiką
* Etapy wykresu

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dzień 3.
Dzień 3 okładki wideo:

* Skrypty wewnątrz przepływów pracy/schematów
* Ograniczenia dotyczące bieżącego kodera
* Pytania i odpowiedzi

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Następny krok
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Klipy szkoleniowe projektanta przepływów pracy usługi Azure Premium Encoder](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

