---
title: Dokumentacja tłumaczenia interfejsu API mowy | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Za pomocą API Microsoft Translator mowy tłumaczenia dodać mowy rozpoznawania mowy i głosu tłumaczenie tekstu do aplikacji.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347572"
---
# <a name="microsoft-translator-speech-api"></a>Interfejs API mowy usługi Microsoft Translator
Interfejsu API usługi Microsoft Translator mowy może służyć do dodania tłumaczeń mowy na trasie, w czasie rzeczywistym, aplikacje, narzędzia lub dowolnego rozwiązania wymagające tłumaczenia mowy obsługi wielu języków, niezależnie od docelowego systemu operacyjnego i języków programowania. Interfejs API może służyć do obu mowy mowy i głosu tłumaczenie tekstu.

Interfejs API Microsoft Translator tekst jest usługą platformy Azure, część [kolekcji kognitywnych API usługi Microsoft](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) uczenie maszynowe i algorytmy AI w chmurze, łatwo dostępne w rozwoju projektów.

Z interfejsem API mowy Translator firmy Microsoft aplikacje klienckie przesyłanie strumieniowe audio mowy do usługi i ponownie odbierać strumień tekstowy i audio wyników, które obejmują rozpoznany języka źródłowego i jego tłumaczenia w języku docelowym. Wyniki tekstu są produkowane przez zastosowanie automatycznego rozpoznawania mowy (ASR) obsługiwane przez usługę sieci głębokość neuronowe do przychodzącego strumieniem audio. Nowe techniki o nazwie Tekst_prawdy w celu lepszego odzwierciedlenia zamiarów użytkownika dalsze poprawia nieprzetworzone dane wyjściowe funkcji automatycznego odzyskiwania systemu. Na przykład Tekst_prawdy usuwa disfluencies (hmms i coughs), powtarzające się wyrazy i przywraca odpowiednie znaki interpunkcyjne i wielkość liter. Możliwość zamaskować lub wykluczyć profanities jest również uwzględniony. Aparaty rozpoznawania i translację są uczone specjalnie do obsługi mowy konwersacji. 

Usługi tłumaczenia mowy używa wyciszenia wykrywania w celu określenia koniec utterance. Po przerwie w działaniu głosowych usługa zostanie strumienia końcowego wyniku utterance ukończone. Usługa może także wysłać ponownie częściowe wyniki, które zapewnią uznania pośredniego, tłumaczenia utterance w toku. 

W tłumaczeniu mowy na mowę usługa zapewnia możliwość syntetyzowania mowy (tekst na mowę) z rozmowy tekstu w języku docelowym. Tekst na mowę audio jest tworzony w formacie określonym przez klienta. WAV i MP3 formaty są dostępne.

Interfejs API tłumaczenia mowy używa protokołu WebSocket, aby zapewnić kanał pełnego dupleksu komunikacji między klientem a serwerem. 

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje
Translator firmy Microsoft to usługa oparta na chmurze tłumaczenia maszynowego. Stanowiącej podstawę tej usługi są [Translator tekst API] (https://www.microsoft.com/en-us/translator/translatorapi.aspx) i Translator mowy interfejs API, który zasilania różnych swoich produktów i usług i są używane przez tysiące firm na całym świecie, w aplikacji i przepływy pracy, dzięki czemu jego zawartość do łączność odbiorców na całym świecie.

Dowiedz się więcej o [usługi Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Microsoft Translator Neuronowej tłumaczenia maszynowego (NMT)
Interfejs API Microsoft Translator mowy używa zarówno starszych statystyczne tłumaczenia maszynowego (SMT), jak i nowszej neuronowej tłumaczenia maszynowego (NMT), aby zapewnić tłumaczenia.

Statystyczne tłumaczenia maszynowego osiągnęła płaska pod względem wydajności. Jakość tłumaczenia poprawia się już w znaczący sposób ogólny systemów z SMT. To nowa technologia translacji na podstawie AI jest uzyskanie pęd oparte na sieci Neuronowej (NN).

NMT zapewnia lepszą tłumaczeń nie tylko z jakości raw tłumaczenia, ale także oceniania punktu widzenia, ponieważ dźwiękowej więcej fluent, więcej ludzi, niż SMT z nich. Klucza Przyczyna ta elastyczność to, że NMT używa pełnego kontekstu zdania tłumaczenie słowa. SMT przyjmuje tylko bezpośredniego kontekstu kilka słów przed i po każdym słowie.

Modele NMT są stanowiącej podstawę interfejsu API i nie są widoczne dla użytkowników końcowych. Tylko zauważalne różnice są następujące:
* Jakość ulepszone tłumaczenia, szczególnie w przypadku języków, takich jak chińskim, japońskim i arabski
* Niezgodność istniejące funkcje dostosowywania koncentratora (na potrzeby używania z interfejsu API usługi Microsoft Translator tekst)

Wszystkie języki tłumaczenia mowy obsługiwane są obsługiwane przez NMT. W związku z tym wszystkie tłumaczenia mowy rozpoznawania mowy używa NMT. 

Mowy do tłumaczenie tekstu może używać kombinacji NMT i SMT w zależności od pary języka. Jeśli języka docelowego jest obsługiwany przez NMT, pełną tłumaczenia jest zasilane NMT. Jeśli język docelowy nie jest obsługiwany przez NMT, tłumaczenie jest hybrydowego NMT i SMT używany język angielski jako "pivot" między dwóch języków. 

Widok obsługiwanych języków w [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Dowiedz się więcej o [działania NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarejestruj się](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Rozpoczęcie kodowania](quickstarts/csharp.md)

## <a name="see-also"></a>Zobacz także
- [Kognitywnych stronę dokumentacji usług](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Strona produktu usługi kognitywnych](https://azure.microsoft.com/services/cognitive-services/)
- [Rozwiązania i uzyskać informacje o cenach](https://www.microsoft.com/en-us/translator/home.aspx) 
