---
title: Obsługiwane języki — interfejs API tłumaczenia mowy w usłudze Translator
titlesuffix: Azure Cognitive Services
description: Języki widoku obsługiwane przez interfejs API tłumaczenia mowy w usłudze Translator.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2f5b48f2dbc2d109c03613676c6a119fd971603b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965440"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Języki obsługiwane przez interfejs API tłumaczenia mowy w usłudze Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Następujące języki są obsługiwane w przypadku tłumaczenia mowy. Jeśli oba języki są obsługiwane w przypadku tłumaczenia mowy, funkcja zamiany mowy na mowę lub zamiany mowy na tekst jest dostępna. Jeśli język docelowy nie jest obsługiwany w przypadku tłumaczenia mowy, dostępne jest tylko tłumaczenie mowy na tekst.

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

Interfejs API tłumaczenia mowy w usłudze Translator obsługuje następujące języki jako język docelowy dla tłumaczenia mowy na tekst.

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

## <a name="access-the-list-programmatically"></a>Programowe uzyskiwanie dostępu do listy

Możesz uzyskać dostęp do listy obsługiwanych języków programowo przy użyciu zasobu Języki. Lista zawiera kod języka, a także nazwę języka w języku angielskim lub inny obsługiwany język. Ta lista jest automatycznie aktualizowana przez usługę tłumaczenie mowy w usłudze Translator, gdy nowe języki staną się dostępne.

Zasób Języki zwraca listę obsługiwanych języków mowy, tekstu i tekstu na mowę. Zasób języków nie wymaga uwierzytelniania.

[Odwiedź odwołanie do interfejsu API, aby wypróbować metodę języka](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie sieci Web usługi Microsoft Translator

Aby uzyskać szybki przegląd języków, w witrynie sieci Web usługi Microsoft Translator są wyświetlane wszystkie języki obsługiwane przez tłumaczenie tekstu w usłudze Translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacji specyficznych dla deweloperów, takich jak kody języka.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
