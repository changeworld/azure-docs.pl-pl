---
title: O tłumaczenia mowy | Dokumentacja firmy Microsoft
description: Omówienie funkcji mowy tłumaczenia
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 43fcde887c21794989aa43540a214ef34893a630
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349696"
---
# <a name="about-the-speech-translation-api"></a>O tłumaczenia mowy interfejsu API

Microsoft Speech API umożliwia dodanie end-to-end, w czasie rzeczywistym, obsługi wielu języków tłumaczenie mowy do aplikacji, narzędzi i urządzeń. Tego samego interfejsu API może służyć do tłumaczenia zarówno mowy na mowę, jak i mowy na tekst.

Z interfejsem API mowy Translator firmy Microsoft aplikacje klienckie przesyłanie strumieniowe audio mowy z usługą i ponownie odbierać strumień wyników. Wyniki te obejmują rozpoznany języka źródłowego i jego tłumaczenia w języku docelowym. Tymczasowe tłumaczenia mogą być wprowadzone do czasu ukończenia utterance, po tym czasie końcowego tłumaczenie jest dostarczane.

Opcjonalnie syntezatora wersji audio tłumaczenia końcowego można przygotować, włączanie true tłumaczenia mowy do rozpoznawania mowy.

API tłumaczenia mowy używa protokołu Websocket, aby zapewnić kanał pełnego dupleksu komunikacji między klientem a serwerem. Ale nie trzeba uwzględniać Websocket; zestaw SDK mowy obsługuje który dla Ciebie.

Interfejs API tłumaczenia mowy wykorzystuje te same technologie, które zasilania różnych swoich produktów i usług. Ta usługa jest już używana przez tysiące firm na całym świecie, w aplikacji i przepływów pracy.

## <a name="about-the-technology"></a>Technologię

Podstawowy aparat tłumaczenia firmy Microsoft są dwa różne podejścia: statystyczne tłumaczenia maszynowego (SMT) i neuronowej tłumaczenia maszynowego (NMT). Drugie, podejście analizy sztucznego wykorzystanie sieci neuronowe jest bardziej nowoczesnych podejście do tłumaczenia maszynowego. NMT zapewnia lepszą tłumaczeń — nie tylko dokładniejsze, ale również więcej fluent i fizycznych. Klucza Przyczyna ta elastyczność to, że NMT używa pełnego kontekstu zdania tłumaczenie słowa.

Obecnie Microsoft ma migracji do NMT najbardziej popularnych języków wykorzystujących SMT tylko dla rzadziej używanych języków. Wszystkie [języków dostępnych do tłumaczenia mowy mowy](supported-languages.md#speech-translation) są obsługiwane przez NMT. Tłumaczenie mowy na tekst może używać SMT lub NMT, w zależności od pary języka. Jeśli języka docelowego jest obsługiwany przez NMT, pełną tłumaczenia jest zasilane NMT. Jeśli język docelowy nie jest obsługiwany przez NMT, tłumaczenie jest hybrydowego NMT i SMT używany język angielski jako "pivot" między dwóch języków.

Różnice między modelami są wewnętrzne aparat tłumaczenia. Użytkownicy końcowi zauważyć tylko jakości ulepszone tłumaczenia, szczególnie w przypadku chińskim, japońskim i arabski.

> [!NOTE]
> Chcą dowiedzieć się więcej na temat technologii związanej z aparatu tłumaczenia firmy Microsoft? Zobacz [tłumaczenia maszynowego](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
