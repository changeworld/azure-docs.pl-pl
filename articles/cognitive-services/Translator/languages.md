---
title: Obsługiwane języki w interfejsie API Translator Microsoft | Dokumentacja firmy Microsoft
description: Wyświetl języki obsługiwane przez interfejs API Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 10/30/2017
ms.author: v-jansko
ms.openlocfilehash: f388e9e39809774f307c0d1752e29f34b041ed13
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356403"
---
# <a name="supported-languages-in-the-microsoft-translator-text-api"></a>Obsługiwane języki w interfejsie API Microsoft Translator tekstu 
Interfejs API Microsoft Translator tekstu obsługuje następujące języki do tłumaczenia tekst — tekst. Tłumaczenie neuronowej (NMT) nowy standard tłumaczeń maszyny zasilane AI wysokiej jakości i jest dostępny jako domyślny za pomocą V3 Translator API tekstu, gdy neuronowej systemu jest dostępne. Neuronowej tłumaczenia maszynowego jest dostępna w wersji 2 za pomocą kategorii "generalnn". 

[Dowiedz się więcej na temat działania tłumaczenia maszynowego](https://www.microsoft.com/translator/mt.aspx)

| Język    | Typ tłumaczenia |Kod języka |
|:----------- |:-------:|:-------------:|
| Afrikaans      | Statystyczne |`af`          |
| Arabski      | Neuronowej | `ar`          |
| Bengalski      | Neuronowej |`bn`          |
| Bośniacki (łaciński)      | Statystyczne |`bs`          |
| Bułgarski     |  Neuronowej |`bg`          |
| Kantoński (tradycyjny)      | Statystyczne |`yue`          |
| Kataloński      | Statystyczne |`ca`          |
| Chiński uproszczony        |  Neuronowej |`zh-Hans`          | 
| Chiński tradycyjny        |  Neuronowej |`zh-Hant`          |
| Chorwacki      | Neuronowej |`hr`          |
| Czeski        |  Neuronowej |`cs`          |
| Duński        |  Neuronowej |`da`          |
| Holenderski        |  Neuronowej |`nl`          |
| Polski       |  Neuronowej |`en`          |
| Estoński      | Neuronowej |`et`          |
| Fidżi      | Statystyczne |`fj`          |
| Filipino      | Statystyczne |`fil`          |
| Fiński      | Neuronowej |`fi`          |
| Francuski        |  Neuronowej |`fr`          |
| Niemiecki       |  Neuronowej |`de`          |
| Grecki      | Neuronowej |`el`          |
| Haitański      | Statystyczne |`ht`          |
| Hebrajski      | Neuronowej |`he`          |
| Hindi        |  Neuronowej |`hi`          |
| Hmong Daw      | Statystyczne |`mww`          |
| Węgierski      | Neuronowej |`hu`          |
| Islandzki      |  Neuronowej |`is`           |
| Indonezyjski      | Statystyczne |`id`          |
| Włoski        |  Neuronowej |`it`          |
| Japoński        |  Neuronowej |`ja`          |
| Swahili      | Statystyczne |`sw`          |
| Klingon      | Statystyczne |`tlh`          |
| Klingon (plqaD)      | Statystyczne |`tlh-Qaak`          |
| Koreański        |  Neuronowej |`ko`          |
| Łotewski      | Neuronowej |`lv`          |
| Litewski      | Neuronowej |`lt`          |
| Malgaskie      | Statystyczne |`mg`          |
| Malajski      | Statystyczne |`ms`          |
| Maltański      | Statystyczne |`mt`          |
| Norweski        |  Neuronowej |`nb`          |
| Perski      | Statystyczne |`fa`          |
| Polski        |  Neuronowej |`pl`          |
| Portugalski        |  Neuronowej |`pt`          |
| Queretaro Otomi      | Statystyczne |`otq`          |
| Rumuński        |  Neuronowej |`ro`          |
| Rosyjski        |  Neuronowej |`ru`          |
| (Samoa Zachodnie)      | Statystyczne |`sm`          |
| Serbski (cyrylica)      | Statystyczne |`sr-Cyrl`          |
| Serbski (łaciński)      | Statystyczne |`sr-Latn`          |
| Słowacki     | Neuronowej |`sk`          |
| Słoweński      | Neuronowej |`sl`          |
| Hiszpański        |  Neuronowej |`es`          |
| Szwedzki        |  Neuronowej |`sv`          |
| Tahitian      | Statystyczne |`ty`          |
| Tamilski      | Statystyczne |`ta`          |
| Tajlandzki      | Neuronowej |`th`          |
| Tongan      | Statystyczne |`to`          |
| Turecki       |  Neuronowej |`tr`          |
| Ukraiński      | Neuronowej |`uk`          |
| Urdu      | Statystyczne |`ur`          |
| Wietnamski      | Neuronowej |`vi`          |
| Walijski      | Neuronowej |`cy`          |
| Yucatec Maya      | Statystyczne |`yua`          |

## <a name="transliteration"></a>Transliteracja

Metoda Transliterate obsługuje następujące języki. W "do/z" "<>--" oznacza, że język może transliteracja z lub do albo Lista skryptów. "-->" Wskazuje, że język można tylko można transliteracja ze skryptu jednego do drugiego.

| Język    | Kod języka | Skrypt | Z | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | ar | Arabski | <--> | Łacińska |
|Bengalski  | bn | Bengalski | <--> | Łacińska |
| Chiński (uproszczony) | zh-Hans | Chiński uproszczony | <--> | Łacińska |
| Chiński (uproszczony) | zh-Hans | Chiński uproszczony | <--> | Chiński tradycyjny |
| Chiński (tradycyjny) | zh-Hant | Chiński tradycyjny | <--> | Łacińska |
| Chiński (tradycyjny) | zh-Hant | Chiński tradycyjny | <--> | Chiński uproszczony |
| Gudżarati | gu  | Gudżarati | --> | Łacińska |
| Hebrajski | ADAM | Hebrajski | <--> | Łacińska |
| Hindi | hi | Dewanagari | <--> | Łacińska |
| Japoński | ja | Japoński | <--> | Łacińska |
| Kannada | kn | Kannada | --> | Łacińska |
| Malasian | ml | Malajalam | --> | Łacińska |
| Marathi | mr | Dewanagari | --> | Łacińska |
| Orija | lub | Orija | <--> | Łacińska |
| Pendżabski | pa | Gurmukhi | <--> | Łacińska  |
| Serbski (cyrylica) | SR-Cyrl | Cyrylica  | --> | Łacińska |
| Serbski (łaciński) | SR-Latn | Łacińska | --> | Cyrylica |
| Tamilski | ta | Tamilski | --> | Łacińska |
| Telugu | te | Telugu | --> | Łacińska |
| Tajlandzki | . | Tajlandzki | <--> | Łacińska |

## <a name="dictionary"></a>Słownik

Słownik obsługuje następujące języki do lub z metodami wyszukiwania i przykłady w języku angielskim. 

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

## <a name="languages-detected-by-the-detect-method"></a>Języki wykrywane przez metodę wykrywania

Następujące języki mogą być wykrywane przez metodę wykrywania. Wykryj może wykryć języki, które nie może dokonywać translacji Translator firmy Microsoft. 

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
| Chinese_Simplified |
| Chinese_Traditional |
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
| Haitian_Creole |
| Hebrajski |
| Hindi |
| Węgierski |
| Islandzki |
| Indonezyjski |
| Irlandzki |
| Włoski |
| Japoński |
| Koreański |
| Kurdish_Arabic |
| Kurdish_Latin |
| Łacińska |
| Łotewski |
| Litewski |
| macedoński |
| Malajski |
| Maltański |
| Norweski |
| Norwegian_Nynorsk |
| Paszto |
| Perski |
| Polski |
| Portugalski |
| Rumuński |
| Rosyjski |
| Serbian_Cyrillic |
| Serbian_Latin |
| Słowacki |
| Słoweński |
| Somali |
| Hiszpański |
| Suahili |
| Szwedzki |
| Tagalski |
| Tajlandzki |
| Turecki |
| Ukraiński |
| Urdu |
| Uzbek_Cyrillic |
| Uzbek_Latin |
| Wietnamski |
| Walijski |
| Jidysz |

## <a name="access-the-list-programmatically"></a>Programowy dostęp do listy

Listę obsługiwanych języków, programowo przy użyciu operacji języków tekstu interfejsu API w wersji 3.0 są dostępne. Można wyświetlić listę funkcji, kod języka, a także nazwę języka w języku angielskim lub dowolnego obsługiwanego języka. Ta lista jest automatycznie aktualizowany przez usługę Microsoft Translator jako nowe języki staną się dostępne.

[Przejrzyj dokumentację referencyjną operacji języków](/reference/languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie sieci Web Microsoft Translator

Witryny sieci Web Microsoft Translator zawiera dla krótki przegląd języki, wszystkie języki obsługiwane przez tłumaczenie tekstu i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacje specyficzne dla deweloperów, takie jak kodów języków.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
