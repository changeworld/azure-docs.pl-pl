---
title: Obsługiwane języki — interfejs API mowy usługi Translator
titlesuffix: Azure Cognitive Services
description: Wyświetl języki obsługiwane przez interfejs API mowy usługi Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ed8f693e4dc0344a0117ae9d6992b925992ef0c4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446928"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Języki obsługiwane przez interfejs API mowy usługi Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Następujące języki są obsługiwane w przypadku tłumaczenia mowy. Jeśli oba języki są obsługiwane w przypadku tłumaczenia mowy i rozpoznawania mowy do rozpoznawania mowy, lub zamiana mowy na tekst jest dostępny. Jeśli język docelowy nie jest obsługiwane w przypadku tłumaczenia mowy, dostępna jest tylko mowy tłumaczenia tekstów.

| Język mowy    |
|:----------- |
| Arabski (nowoczesnych standardowy)      |
| Portugalski (Brazylia)     |
| Chiński (mandaryński)      |
| Polski      |
| Francuski      |
| Niemiecki      |
| Włoski      |
| Japoński      |
| Rosyjski      |
| Hiszpański      |

Interfejs API mowy usługi Translator obsługuje następujące języki jako język docelowy zamiana mowy na tekst tłumaczenia.

| Język tekstu    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabski       | `ar`          |
| Bengalski      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Kantoński (tradycyjny)      | `yue`          |
| Kataloński      | `ca`          |
| Chiński uproszczony      | `zh-Hans`          |
| Chiński tradycyjny      | `zh-Hant`          |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Holenderski      | `nl`          |
| Polski      | `en`          |
| Estoński      | `et`          |
| Fidżi      | `fj`          |
| Filipino      | `fil`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Haitański      | `ht`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Węgierski      | `hu`          |
|Islandzki|`is`          |
| Indonezyjski      | `id`          |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Suahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malgaski      | `mg`          |
| Malajski      | `ms`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| (Samoa Zachodnie)      | `sm`          |
| Serbski (Cyrylica)      | `sr-Cyrl`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tahitian      | `ty`          |
| Tamilski      | `ta`          |
| Tajlandzki      | `th`          |
| Pa'anga      | `to`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Programowo uzyskać dostęp do listy

Listę obsługiwanych języków, w sposób programowy za pomocą zasobów języków są dostępne. Lista zawiera kod języka, a także nazwę języka w języku angielskim lub innych obsługiwanych języków. Ta lista jest automatycznie aktualizowana przez usługę mowy usługi Translator miarę udostępniania nowych języków.

Zasób języków zwraca listę wszystkich obsługiwanych językach mowy, tekst i zamiany tekstu na mowę. Zasób języków nie wymaga uwierzytelniania.

[Dokumentacja interfejsu API możesz wypróbować metoda języków można znaleźć w](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie internetowej Microsoft Translator

Dla rzut oka na językach usługa Microsoft Translator witryny sieci Web pokazuje wszystkie języki obsługiwane przez tekstu usługi Translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacje specyficzne dla deweloperów, takie jak kodów języków.

[Zobacz listy języków](https://www.microsoft.com/translator/languages.aspx)
