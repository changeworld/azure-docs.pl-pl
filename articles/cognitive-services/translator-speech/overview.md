---
title: Co to jest usługa tłumaczenia mowy w usłudze Translator?
titleSuffix: Azure Cognitive Services
description: Interfejs API tłumaczenia mowy w usłudze Translator umożliwia dodawanie do aplikacji tłumaczeń mowy na mowę i mowy na tekst.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 24014bb06a779c214f18f966dfb1d26d61adee8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827526"
---
# <a name="what-is-translator-speech-api"></a>Co to jest interfejs API tłumaczenia mowy w usłudze Translator?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Interfejs API tłumaczenia mowy w usłudze Translator może być używany w celu dodawania kompleksowych tłumaczeń mowy w czasie rzeczywistym do aplikacji, narzędzi i dowolnych rozwiązań wymagających usługi tłumaczenia mowy obsługującej wiele języków niezależnie od docelowego systemu operacyjnego lub języków programowania. Interfejs API może służyć do tłumaczeń polegających na zamianie mowy na mowę i mowy na tekst.

Interfejs API tłumaczenia tekstu w usłudze Translator to usługa platformy Azure stanowiąca część [kolekcji interfejsów API usług Cognitive Services](https://docs.microsoft.com/azure/), zbioru algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze, który można łatwo wykorzystać w swoich projektach programistycznych.

Za pomocą interfejsu API tłumaczenia mowy w usłudze Translator aplikacje klienckie przesyłają strumieniowo dźwięk mowy do usługi i odbierają strumień wyników opartych na tekście i dźwięku, które obejmują rozpoznany tekst w języku źródłowym i jego tłumaczenie w języku docelowym. Wyniki tekstowe są tworzone przez zastosowanie automatycznego rozpoznawania mowy (ASR, Automatic Speech Recognition) obsługiwanego przez głębokie sieci neuronowe do przychodzącego strumienia audio. Nieprzetworzone dane wyjściowe funkcji ASR są ulepszane w jeszcze większym stopniu dzięki użyciu nowej techniki o nazwie TrueText, aby lepiej odzwierciedlać intencję użytkownika. Na przykład technika TrueText usuwa dźwięki zakłócające mowę (chrząknięcia i odkasływanie) i powtarzające się wyrazy oraz przywraca odpowiednie znaki interpunkcyjne oraz wielkość liter. Oferuje ona również możliwość maskowania lub wykluczania przekleństw. Aparaty rozpoznawania i tłumaczenia są specjalnie uczone pod kątem obsługi rozmów.

Usługa tłumaczenia mowy w usłudze Translator używa wykrywania wyciszenia do określenia końca wypowiedzi. Po przerwie w użyciu głosu usługa strumieniowo prześle wynik końcowy dotyczący zakończonej wypowiedzi. Usługa może także wysyłać z powrotem wyniki częściowe, które umożliwiają pośrednie rozpoznawanie i tłumaczenie trwającej wypowiedzi.

W przypadku tłumaczenia mowy na mowę usługa oferuje możliwość syntetyzowania mowy (zamiany tekstu na mowę) z tekstu mówionego w językach docelowych. W przypadku zamiany tekstu na mowę dźwięk jest tworzony w formacie określonym przez klienta. Dostępne formaty to WAV i MP3.

Interfejs API tłumaczenia mowy w usłudze Translator używa protokołu WebSocket w celu zapewnienia kanału komunikacji pełnodupleksowej między klientem a serwerem.

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje
Microsoft Translator to oparta na chmurze usługa tłumaczenia maszynowego. Rdzeniem tej usługi są: [interfejs API tłumaczenia tekstu w usłudze Translator](https://www.microsoft.com/en-us/translator/translatorapi.aspx) oraz interfejs API tłumaczenia mowy w usłudze Translator, które współpracują z różnymi produktami i usługami firmy Microsoft, a także są używane przez tysiące firm na całym świecie w ich aplikacjach i przepływach pracy, co pozwala ich zawartości na dotarcie do odbiorców na całym świecie.

Dowiedz się więcej o [usłudze Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator (NMT)
Interfejs API tłumaczenia mowy w usłudze Translator tworzy tłumaczenia przy użyciu starszego statystycznego tłumaczenia maszynowego (SMT) i nowszego neuronowego tłumaczenia maszynowego (NMT).

Statystyczne tłumaczenie maszynowe osiągnęło pułap swoich możliwości w zakresie poprawy wydajności. Jakość tłumaczeń pochodzących z ogólnych systemów z funkcją SMT nie poprawia się już w znaczący sposób. Rozwój nowej technologii tłumaczenia nabiera tempa dzięki użyciu sieci neuronowych (NN, Neural Networks).

Technologia NMT zapewnia lepsze tłumaczenia nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku niż tłumaczenia technologii SMT.
Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględnia tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyne zauważalne różnice to:
* Lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski
* Niezgodność z istniejącymi funkcjami dostosowywania centrum (do użycia z interfejsem API tłumaczenia tekstu w usłudze Translator firmy Microsoft)

Wszystkie obsługiwane języki tłumaczenia są również obsługiwane przez technologię NMT. W związku z tym wszystkie tłumaczenia mowy na mowę używają technologii NMT.

Tłumaczenie z zamianą mowy na tekst może używać kombinacji technologii NMT i SMT w zależności od pary języków. Jeśli język docelowy jest obsługiwany przez technologię NMT, pełne tłumaczenie jest zarządzane przez technologię NMT. Jeśli język docelowy nie jest obsługiwany przez technologię NMT, tłumaczenie jest hybrydą technologii NMT i SMT używającą języka angielskiego jako „pośrednika” między dwoma językami.

Listę obsługiwanych języków można znaleźć w witrynie [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx).

Dowiedz się więcej o [sposobie działania technologii NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie konta](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Rozpoczynanie kodowania](quickstarts/csharp.md)

## <a name="see-also"></a>Zobacz także
- [Strona dokumentacji usług Cognitive Services](https://docs.microsoft.com/azure/)
- [Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informacje o rozwiązaniach i cenach](https://www.microsoft.com/en-us/translator/home.aspx)
