---
title: Niestandardowe liczniki usługa rozpoznawania mowy i przydziały na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o liczniki i przydziały Custom Speech Service na platformie Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: nitinme
ms.openlocfilehash: 3f94ac32198c0d95488f207802ed5693e49b839d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872564"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Niestandardowe liczniki usługa rozpoznawania mowy i przydziały

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Za pomocą opartej na chmurze usłudze Custom Speech Service możesz dostosować modele mowy na tekst zamiany mowy na tekst.

Aby rozpocząć korzystanie z Custom Speech Service, przejdź do [portal usługi Custom Speech Service](https://cris.ai).

Dla bieżącego liczniki, przejdź do cen [cen usług Cognitive Services, usługę Custom Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) strony.

## <a name="tiers-explained"></a>Wyjaśniono warstw
Do testowania i tylko prototypu Sugerujemy korzystać z bezpłatnej warstwy F0. Dla systemów produkcyjnych proponujemy przy użyciu warstwy S2. Przy użyciu warstwy S2 możesz skalować liczbę jednostek skalowania (SUs), które dany scenariusz wymaga wdrożenia.

> [!NOTE]
> Możesz *nie* migracja między warstwami F0 i S2.
>

## <a name="meters-explained"></a>Liczniki wyjaśniono

### <a name="scale-out"></a>Skalowanie w poziomie
Skalowanie w poziomie jest nową funkcję, która udostępniliśmy nowy model cen. Za pomocą skalowanie w poziomie, można kontrolować liczbę żądań, które może przetworzyć modelu.

Współbieżnych żądań można ustawić za pomocą polecenia SU Miara na platformie **Utwórz wdrożenie modelu** widoku. Aby uzyskać więcej informacji, zobacz [utworzyć niestandardowy punkt końcowy rozpoznawania mowy na tekst](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). W zależności od ilości ruchu, który można przewidzieć używania modelu można wybrać odpowiednią liczbę SUs. 

> [!NOTE]
> Każda jednostka skalowania gwarantuje 5 współbieżnych żądań. Możesz kupić SUs co najmniej 1, zgodnie z potrzebami. Ponieważ zwiększa liczbę SUs z przyrostem równym 1, liczbę jednoczesnych żądań gwarantowane zwiększać z przyrostem równym 5.
>

### <a name="log-management"></a>Zarządzanie dziennikami
Możesz zdecydować się na wyłączyć ślady audio w nowo wdrożonym modelu za dodatkową opłatą. Usługa Custom Speech Service nie rejestruje żądań audio i zapisy tego modelu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o sposobie używania Custom Speech Service, przejdź do [portal usługi Custom Speech Service](https://cris.ai).

* [Wprowadzenie](cognitive-services-custom-speech-get-started.md)
* [Często zadawane pytania](cognitive-services-custom-speech-faq.md)
* [Słownik](cognitive-services-custom-speech-glossary.md)
 
