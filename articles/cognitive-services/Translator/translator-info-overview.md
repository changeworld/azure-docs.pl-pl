---
title: Omówienie interfejsu API tekstu usługi Microsoft Translator — Azure Cognitive Services | Microsoft Docs
description: Zintegruj interfejs API tekstu usługi Microsoft Translator z aplikacjami, witrynami internetowymi, narzędziami i innymi rozwiązaniami, aby umożliwić tworzenie środowisk użytkownika w wielu językach.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: bfbb316ac41045add7f424b5d478581aa226fc19
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42889326"
---
# <a name="what-is-microsoft-translator"></a>Co to jest Microsoft Translator?

Interfejs API tekstu usługi Microsoft Translator można bez trudu zintegrować z aplikacjami, witrynami internetowymi, narzędziami i innymi rozwiązaniami, aby umożliwić tworzenie środowisk użytkownika w [ponad 60 językach](languages.md). Za jego pomocą można tłumaczyć teksty w różnych językach na dowolnej platformie sprzętowej i w dowolnym systemie operacyjnym.

Interfejs API tekstu usługi Translator firmy Microsoft jest częścią [interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) firmy Microsoft, zbioru algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze, który można łatwo wykorzystać w swoich projektach programistycznych.

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje

Microsoft Translator to oparta na chmurze usługa tłumaczenia maszynowego. Rdzeniem tej usługi są: interfejs API tłumaczenia tekstu w usłudze Translator oraz [interfejs API tłumaczenia mowy w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation), które współpracują z różnymi produktami i usługami firmy Microsoft, a także są używane przez tysiące firm na całym świecie w ich aplikacjach i przepływach pracy, co pozwala ich zawartości na dotarcie do odbiorców na całym świecie.

Tłumaczenie mowy jest również dostępne za pośrednictwem [usługi Cognitive Services Speech w wersji zapoznawczej](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/), która łączy istniejący interfejs API tłumaczenia mowy w usłudze Translator, interfejs API rozpoznawania mowy Bing oraz usługę Custom Speech Service (w wersji zapoznawczej) w ujednoliconą i całkowicie dostosowywalną usługę.  

Dowiedz się więcej o [usłudze Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="language-customization"></a>Dostosowywanie języka

W połączeniu z interfejsem API tłumaczenia tekstu w usłudze Translator można użyć rozszerzenia podstawowej usługi Microsoft Translator, Custom Translator, aby ułatwić dostosowanie systemu tłumaczenia neuronowego i ulepszyć tłumaczenia pod kątem określonej terminologii i stylu.

Korzystając z rozszerzenia Custom Translator, można tworzyć systemy tłumaczenia obsługujące terminologię stosowaną w Twojej firmie lub branży. Dostosowany system tłumaczenia łatwo integruje się z istniejącymi aplikacjami, przepływami pracy i witrynami internetowymi na wielu typach urządzeń za pośrednictwem interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator, używając parametru kategorii. 

Dowiedz się więcej o [dostosowywaniu języków](customization.md)

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator

Neuronowe tłumaczenie maszynowe (NMT) to nowy standard w dziedzinie wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji. Zastępuje on przestarzałą technologię statystycznego tłumaczenia maszynowego (SMT), która osiągnęła pułap swoich możliwości w połowie lat 2010.

Technologia NMT zapewnia lepsze tłumaczenia niż technologia SMT nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku. Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględniała tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyną zauważalną różnicą jest lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski. 

Dowiedz się więcej o [sposobie działania technologii NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [szczegóły dotyczące cen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- [Utwórz konto](translator-text-how-to-signup.md), aby uzyskać klucz dostępu.

- [Szybki Start](quickstarts/csharp.md) to przewodnik po wywołaniach interfejsu API REST napisanych w języku C#. Dowiedz się, jak przetłumaczyć tekst z jednego języka na inny, używając minimalnej ilości kodu.

- [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) zawiera dokumentację techniczną interfejsów API.

## <a name="see-also"></a>Zobacz też

- [Strona dokumentacji usług Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informacje o rozwiązaniach i cenach](https://www.microsoft.com/en-us/translator/default.aspx)
