---
title: Lokalizacja na platformie Azure przy użyciu aplikacji LUIS obsługuje | Dokumentacja firmy Microsoft
description: Więcej informacji o językach obsługiwanych przez usługę LUIS.
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2017
ms.author: cahann
ms.openlocfilehash: 1eabc01ee07f8791680738a156471e3efe2c44ff
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356115"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>Opis specyficzne dla kultury w aplikacjach LUIS

Aplikacja LUIS jest specyficzne dla kultury i nie można zmienić po ustawieniu. 

## <a name="multi-language-luis-apps"></a>Aplikacje LUIS obsługi wielu języków.
Jeśli potrzebujesz aplikacji klienckiej LUIS obsługi wielu języków, takich jak chatbot, masz kilka opcji. Jeśli LUIS obsługuje wszystkie języki, jest opracowanie aplikacji LUIS, dla każdego języka. Każda aplikacja LUIS ma identyfikator unikatowy aplikacji i punkt końcowy dziennika. Jeśli musisz podać opis języka LUIS nie obsługuje, można użyć języka [interfejsu API usługi Microsoft Translator](../Translator/translator-info-overview.md) tłumaczenie utterance w obsługiwanym języku, przesyłanie utterance do punktu końcowego LUIS i odbierania Wynikowa wyniki.

## <a name="languages-supported"></a>Obsługiwane języki
LUIS rozumie zniesławiających w następujących językach:


| Język |Ustawienia regionalne  |  Wbudowane domeny | Wbudowane jednostki | Sugestie frazy | **[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | 
|--|--|:--:|:--:|:--:|:--:|
| Angielski |`en-US` | ✔ | ✔  |✔|✔|
| Francuski kanadyjskich |`fr-CA` |-|   -   |-|✔|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holenderski |`nl-NL` |-|  -   |-|✔|
| Francuski (Francja) |`fr-FR` |-| ✔ |✔ |✔|
| Niemiecki |`de-DE` |-| ✔ |✔ |✔|
| Włoski |`it-IT` |-| ✔ |✔|✔|
| *[Japoński](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Tylko frazy klucza|
| Koreański |`ko-KR` |-|   -   |-|Tylko frazy klucza|
| Portugalski (Brazylia) |`pt-BR` |-| ✔ |✔ |nie wszystkie podrzędne kultur|
| Hiszpański (Hiszpania) |`es-ES` |-| ✔ |✔|✔|
| Hiszpański (Meksyk)|`es-MX` |-|  -   |✔|✔|


Obsługa języków pobieranych plików zależy od [wbudowane jednostek](luis-reference-prebuilt-entities.md) i [wbudowane domen](luis-reference-prebuilt-domains.md). 

### <a name="chinese-support-notes"></a>* Uwagi dotyczące chińskiej wersji językowej pomocy technicznej

 - W `zh-cn` kultury, LUIS oczekuje uproszczony zestaw zamiast tradycyjnych zestawu znaków chińskiej wersji językowej.
 - Nazwy lokalizacji docelowych, jednostek, funkcje i wyrażenia regularne mogą być znakami języka chińskiego lub łaciński.
 - Zobacz [odwołania wbudowane domen ](luis-reference-prebuilt-domains.md) informacji obsługiwanych domen wbudowane w `zh-cn` kultury.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Japoński obsługuje uwagi

 - Ponieważ LUIS nie zapewnia analizy składni i nie będzie zrozumieć różnicę między Keigo i japoński nieformalne, należy zastosować różne poziomy formalności jako przykłady szkoleń dla aplikacji. 
     - でございます nie jest taka sama jak です. 
     - です nie jest taka sama jak だ. 

### <a name="text-analytics-support-notes"></a>** Analiza tekstu obsługuje uwagi
Tylko portugalski jest obsługiwana w przypadku podhodowli: `pt-PT` i `pt-BR`. Wszystkie inne kultur są obsługiwane na poziomie podstawowym kultury. Dowiedz się więcej o Analiza tekstu [obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). 

### <a name="speech-api-supported-languages"></a>Mowy interfejsu API, obsługiwane języki
Zobacz mowy [obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) dla języków tryb dyktowania mowy.

### <a name="bing-spell-check-supported-languages"></a>Języki obsługiwane sprawdzania pisowni usługi Bing
Zobacz sprawdzania pisowni usługi Bing [obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) listę obsługiwanych języków i stanu.

## <a name="rare-or-foreign-words-in-an-application"></a>Rzadkie lub obcego wyrazów w aplikacji
W `en-us` kultury, uzyskuje informacje o rozróżnienia wyrazów najbardziej w języku angielskim, łącznie z żargon LUIS. W `zh-cn` kultury, uzyskuje informacje o rozróżnienia znaki chińskie LUIS. Jeśli używasz rzadkich programu word w `en-us` lub znak w `zh-cn`, zobaczyć LUIS wydaje się odróżnienie tego wyrazu lub znaków, Dodaj ten wyraz lub znak do [Lista fraz funkcji](luis-how-to-add-features.md). Na przykład słowa poza kultura aplikacji--słowa obce — powinien można dodać do funkcji Lista fraz. Ta lista frazy powinna być oznaczona jako nie wymienne, aby wskazać, że zestaw rzadkich wyrazów formularzy klasę, która LUIS powinien Dowiedz się, jak rozpoznać, ale nie są one synonimy lub wymienne ze sobą.

### <a name="hybrid-languages"></a>Języki hybrydowego
Języki hybrydowego łączyć słowa z dwóch kultury, takich jak angielski i chiński. Te języki nie są obsługiwane w LUIS, ponieważ aplikacja jest oparta na jednym kultury.

## <a name="tokenization"></a>Tokenizacji
Aby wykonać uczenia maszynowego, LUIS dzieli utterance na [tokenów](luis-glossary.md#token) oparte na kultury. 

|Język|  Każdy spacji lub znaku specjalnego | poziom znaków|wyrazy złożone|[Jednostka tokenami zwracana](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chiński||✔||✔|
|Holenderski|||✔|✔|
|Angielski (en-us)|✔ ||||
|Francuski (fr-FR)|✔||||
|Francuski (fr Kanada)|✔||||
|Niemiecki|||✔|✔|
|Włoski|✔||||
|Japoński||||✔|
|Koreański||✔||✔|
|Portugalski (Brazylia)|✔||||
|Hiszpański (es-ES)|✔||||
|Hiszpański (es-MX)|✔||||

 