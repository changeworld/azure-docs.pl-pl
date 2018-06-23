---
title: Obsługiwane języki w interfejsie API mowy Translator Microsoft | Dokumentacja firmy Microsoft
description: Wyświetl języki obsługiwane przez interfejs API Microsoft Translator mowy.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 0d33033442a012290baa78d80f1b8bde0499b3f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349724"
---
# <a name="languages-supported-by-the-microsoft-translator-speech-api"></a>Języki obsługiwane przez interfejs API mowy Translator firmy Microsoft
Następujące języki są obsługiwane w celu przetłumaczenia mowy. Jeśli oba języki są obsługiwane w tłumaczeniu mowy, mowy rozpoznawania mowy lub mowy na tekst jest dostępny. Jeśli język docelowy nie jest obsługiwana dla mowy tłumaczenia, tylko rozpoznawania mowy tłumaczenie tekstu jest dostępna. 

| Język mowy    |
|:----------- |
| Arabski (Modern standardowy)      |
| Chiński (mandaryński)      |
| Polski      |
| Francuski      |
| Niemiecki      |
| Włoski      |
| Japoński      |
| Portugalski (Brazylia)     |
| Rosyjski      |
| Hiszpański      | 

Interfejs API Microsoft Translator mowy obsługuje następujące języki jako języka docelowego mowy do tłumaczenie tekstu. 

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
| Swahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malgaskie      | `mg`          |
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
| Serbski (cyrylica)      | `sr-Cyrl`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tahitian      | `ty`          |
| Tamilski      | `ta`          |
| Tajlandzki      | `th`          |
| Tongan      | `to`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Programowy dostęp do listy

Listę obsługiwanych języków, programowo przy użyciu zasobów języki są dostępne. Lista zawiera kod języka, a także nazwę języka w języku angielskim lub dowolnego obsługiwanego języka. Ta lista jest automatycznie aktualizowany przez usługę Microsoft Translator jako nowe języki staną się dostępne.

Zasób języków zwraca listę języków obsługiwanych mowy, tekst i tekst na mowę. Zasób języków nie wymaga uwierzytelniania.

[Odwiedź stronę dokumentacji interfejsu API wypróbowanie metody języków](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie sieci Web Microsoft Translator

Witryny sieci Web Microsoft Translator zawiera dla krótki przegląd języki, wszystkie języki obsługiwane przez tłumaczenie tekstu i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacje specyficzne dla deweloperów, takie jak kodów języków.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx) 
