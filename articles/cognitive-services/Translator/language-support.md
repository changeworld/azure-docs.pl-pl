---
title: Obsługa języków — interfejs API tekstu usługi Translator
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwanych przez interfejs API tekstu usługi Translator.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: 775e098eb2a067e3e0446bccc223c1c54e082347
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435516"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu dla interfejsu API tłumaczenia tekstu

Interfejs API tekstu usługi Translator obsługuje następujące języki do tłumaczenia tekstu na tekst. Tłumaczenie neuronowe (NMT) stanowi nowy standard w poszukiwaniu tłumaczeń maszyny bazujących na sztucznej Inteligencji wysokiej jakości i jest dostępny jako domyślne za pomocą interfejsu API tekstu usługi Translator w wersji 3, gdy neuronowych system będzie dostępny. Neuronowego tłumaczenia maszynowego jest dostępna w wersji 2 za pomocą kategorii "generalnn".

[Dowiedz się więcej o tym, jak działa tłumaczenia maszynowego](https://www.microsoft.com/translator/mt.aspx)

| Język    | Typ tłumaczenia |Kod języka |
|:----------- |:-------:|:-------------:|
| Afrikaans      | Statystyczne |`af`          |
| Arabski      | Neuronowych | `ar`          |
| Arabski, Levantine    | Neuronowych | `apc`
| Bengalski      | Neuronowych |`bn`          |
| Bośniacki (łaciński)      | Statystyczne |`bs`          |
| Bułgarski     |  Neuronowych |`bg`          |
| Kantoński (tradycyjny)      | Statystyczne |`yue`          |
| Kataloński      | Statystyczne |`ca`          |
| Chiński uproszczony        |  Neuronowych |`zh-Hans`          |
| Chiński tradycyjny        |  Neuronowych |`zh-Hant`          |
| Chorwacki      | Neuronowych |`hr`          |
| Czeski        |  Neuronowych |`cs`          |
| Duński        |  Neuronowych |`da`          |
| Holenderski        |  Neuronowych |`nl`          |
| Polski       |  Neuronowych |`en`          |
| Estoński      | Neuronowych |`et`          |
| Fidżi      | Statystyczne |`fj`          |
| Filipino      | Statystyczne |`fil`          |
| Fiński      | Neuronowych |`fi`          |
| Francuski        |  Neuronowych |`fr`          |
| Niemiecki       |  Neuronowych |`de`          |
| Grecki      | Neuronowych |`el`          |
| Haitański      | Statystyczne |`ht`          |
| Hebrajski      | Neuronowych |`he`          |
| Hindi        |  Neuronowych |`hi`          |
| Hmong Daw      | Statystyczne |`mww`          |
| Węgierski      | Neuronowych |`hu`          |
| Islandzki      |  Neuronowych |`is`           |
| Indonezyjski      | Statystyczne |`id`          |
| Włoski        |  Neuronowych |`it`          |
| Japoński        |  Neuronowych |`ja`          |
| Swahili      | Statystyczne |`sw`          |
| Klingon      | Statystyczne |`tlh`          |
| Klingon (plqaD)      | Statystyczne |`tlh-Qaak`          |
| Koreański        |  Neuronowych |`ko`          |
| Łotewski      | Neuronowych |`lv`          |
| Litewski      | Neuronowych |`lt`          |
| Malgaski      | Statystyczne |`mg`          |
| Malajski      | Statystyczne |`ms`          |
| Maltański      | Statystyczne |`mt`          |
| Norweski        |  Neuronowych |`nb`          |
| Perski      | Statystyczne |`fa`          |
| Polski        |  Neuronowych |`pl`          |
| Portugalski        |  Neuronowych |`pt`          |
| Queretaro Otomi      | Statystyczne |`otq`          |
| Rumuński        |  Neuronowych |`ro`          |
| Rosyjski        |  Neuronowych |`ru`          |
| (Samoa Zachodnie)      | Statystyczne |`sm`          |
| Serbski (cyrylica)      | Statystyczne |`sr-Cyrl`          |
| Serbski (łaciński)      | Statystyczne |`sr-Latn`          |
| Słowacki     | Neuronowych |`sk`          |
| Słoweński      | Neuronowych |`sl`          |
| Hiszpański        |  Neuronowych |`es`          |
| Szwedzki        |  Neuronowych |`sv`          |
| Tahitian      | Statystyczne |`ty`          |
| Tamilski      | Statystyczne |`ta`          |
| Telugu   | Neuronowych   | `te` |
| Tajlandzki      | Neuronowych |`th`          |
| Pa'anga      | Statystyczne |`to`          |
| Turecki       |  Neuronowych |`tr`          |
| Ukraiński      | Neuronowych |`uk`          |
| Urdu      | Statystyczne |`ur`          |
| Wietnamski      | Neuronowych |`vi`          |
| Walijski      | Neuronowych |`cy`          |
| Yucatec Maya      | Statystyczne |`yua`          |

## <a name="transliteration"></a>Transliteracja

Metoda Transliterate obsługuje następujące języki. W "do/z" "<> -" oznacza, że język może transliteracja, z lub do jednej z skryptach wymienionych. "-->" Wskazuje, że język może tylko można transliteracja z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do i z niej | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | ar | Arabski | <--> | Łaciński |
|Bengalski  | bn | Bengalski | <--> | Łaciński |
| Chiński (uproszczony) | zh-Hans | Chiński uproszczony | <--> | Łaciński |
| Chiński (uproszczony) | zh-Hans | Chiński uproszczony | <--> | Chiński tradycyjny |
| Chiński (tradycyjny) | zh-Hant | Chiński tradycyjny | <--> | Łaciński |
| Chiński (tradycyjny) | zh-Hant | Chiński tradycyjny | <--> | Chiński uproszczony |
| Gudżarati | gu  | Gudżarati | --> | Łaciński |
| Hebrajski | ADAM | Hebrajski | <--> | Łaciński |
| Hindi | hi | Dewanagari | <--> | Łaciński |
| Japoński | ja | Japoński | <--> | Łaciński |
| Kannada | kn | Kannada | --> | Łaciński |
| Malasian | ml | Malajalam | --> | Łaciński |
| Marathi | mr | Dewanagari | --> | Łaciński |
| Orija | lub | Orija | <--> | Łaciński |
| Pendżabski | pa | Gurmukhi | <--> | Łaciński  |
| Serbski (cyrylica) | Funkcja SR-Cyrl | Cyrylica  | --> | Łaciński |
| Serbski (łaciński) | Funkcja SR-Latn | Łaciński | --> | Cyrylica |
| Tamilski | ta | Tamilski | --> | Łaciński |
| Telugu | Usuń | Telugu | --> | Łaciński |
| Tajlandzki | . | Tajlandzki | <--> | Łaciński |

## <a name="dictionary"></a>Słownik

Słownik obsługuje następujące języki do lub z języka angielskiego, za pomocą metodom Lookup i Examples.

| Język    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabski       | `ar`          |
| Bengalski      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Kataloński      | `ca`          |
| Chiński uproszczony      | `zh-Hans`          |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Holenderski      | `nl`          |
| Estoński      | `et`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Haitański      | `ht`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Węgierski      | `hu`          |
| Islandzki    | `is`  |
| Indonezyjski      | `id`          |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Swahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malajski      | `ms`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski      | `pt`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tamilski      | `ta`          |
| Tajlandzki      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |

## <a name="languages-detected-by-the-detect-method"></a>Języków, wykrytych przez metodę wykrywania

Następujące języki może zostać wykryte przez metodę wykrywania. Wykrywanie maja wykrywanie języków, których nie można przetłumaczyć Microsoft Translator.

| Język    |
|:----------- |
| Afrikaans |
| Albański |
| Arabski |
| Baskijski |
| Białoruski |
| Bułgarski |
| Kataloński |
| Chiński |
| Chiński (uproszczony) |
| Chiński (tradycyjny) |
| Chorwacki |
| Czeski |
| Duński |
| Holenderski |
| Polski |
| Esperanto |
| Estoński |
| Fiński |
| Francuski |
| Galicyjski |
| Niemiecki |
| Grecki |
| Haitański |
| Hebrajski |
| Hindi |
| Węgierski |
| Islandzki |
| Indonezyjski |
| Irlandzki |
| Włoski |
| Japoński |
| Koreański |
| Kurdyjska (arabski) |
| Kurdyjska (łaciński) |
| Łaciński |
| Łotewski |
| Litewski |
| Macedoński |
| Malajski |
| Maltański |
| Norweski |
| Norweski (Nynorsk) |
| Paszto |
| Perski |
| Polski |
| Portugalski |
| Rumuński |
| Rosyjski |
| Serbski (cyrylica) |
| Serbski (łaciński) |
| Słowacki |
| Słoweński |
| Somalijski |
| Hiszpański |
| Suahili |
| Szwedzki |
| Tagalski |
| Telugu |
| Tajlandzki |
| Turecki |
| Ukraiński |
| Urdu |
| Uzbecki (cyrylica) |
| Uzbecki (łaciński) |
| Wietnamski |
| Walijski |
| Jidysz |

## <a name="access-the-list-programmatically"></a>Programowo uzyskać dostęp do listy

Można uzyskać dostęp do listy obsługiwanych języków, w sposób programowy za pomocą języków działania interfejsu API tłumaczenia tekstu w wersji 3.0. Możesz wyświetlić listę funkcji, kod języka, a także nazwę języka, w językach angielskim i innych obsługiwanych języków. Ta lista jest automatycznie aktualizowana przez usługę Microsoft Translator miarę udostępniania nowych języków.

[Przejrzyj dokumentację referencyjną operacji języków](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie internetowej Microsoft Translator

Dla rzut oka na językach usługa Microsoft Translator witryny sieci Web pokazuje wszystkie języki obsługiwane przez tekstu usługi Translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacje specyficzne dla deweloperów, takie jak kodów języków.

[Zobacz listy języków](https://www.microsoft.com/translator/languages.aspx)
