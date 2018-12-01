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
ms.openlocfilehash: 0b1187083c14fc7c536f6a32f3a41957f53f299b
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679719"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu dla interfejsu API tłumaczenia tekstu

Interfejs API tekstu usługi Translator obsługuje następujące języki do tłumaczenia tekstu na tekst. Tłumaczenie neuronowe (NMT) stanowi nowy standard w poszukiwaniu tłumaczeń maszyny bazujących na sztucznej Inteligencji wysokiej jakości i jest dostępny jako domyślne za pomocą interfejsu API tekstu usługi Translator w wersji 3, gdy neuronowych system będzie dostępny. 

[Dowiedz się więcej o tym, jak działa tłumaczenia maszynowego](https://www.microsoft.com/translator/mt.aspx)

**W wersji 2 Translator interfejsu API**

> [!NOTE]
> W wersji 2 została zakończona w dniu 30 kwietnia 2018 i zostanie zakończona w dniu 30 kwietnia 2019 r.

* Tylko statystyczne: nie neuronowych system będzie dostępny dla tego języka.
* Neuronowych dostępne: neuronowych system będzie dostępny. Użyj parametru `category=generalnn` dostęp do tego systemu neuronowych.
* Domyślne neuronowej: neuronowych jest domyślny system tłumaczeń. Użyj parametru `category=smt` dostęp do tego systemu statystycznych do użycia z usługą Microsoft Translator Hub.
* Tylko Neuronowej: tylko tłumaczenie neuronowe jest dostępne.

**Interfejs API usługi Translator w wersji 3** interfejs API usługi Translator w wersji 3 jest neuronowych domyślnie i systemy statystyczne są dostępne tylko, gdy nie istnieje żaden system neuronowych. Niestandardowe w usłudze Translator należy używać tylko z językami neuronowych. 

|Język|  Kod języka|  INTERFEJSY API WERSJI 2| W WERSJI 3 INTERFEJSU API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Tylko statystyczne|  Neuronowych|
|Arabski|    `ar`    |Neuronowych dostępne|  Neuronowych|
|Arabski, Levantine| `apc`   |Neuronowych dostępne|  Neuronowych|
|Bengalski|    `bn`    |Neuronowych dostępne|  Neuronowych|
|Bośniacki (łaciński)|   `bs`    |Tylko statystyczne|  Statystyczne|
|Bułgarski| `bg`    |Neuronowych dostępne|  Neuronowych|
|Kantoński (tradycyjny)|   `yue`   |Tylko statystyczne|  Statystyczne|
|Kataloński|   `ca`    |Tylko statystyczne|  Statystyczne|
|Chiński uproszczony|    `zh-Hans`   |Domyślne neuronowych |Neuronowych|
|Chiński tradycyjny|   `zh-Hant`   |Domyślne neuronowych |Neuronowych|
|Chorwacki|  `hr`    |Neuronowych dostępne|  Neuronowych|
|Czeski| `cs`    |Neuronowych dostępne|  Neuronowych|
|Duński|    `da`    |Neuronowych dostępne   |Neuronowych|
|Holenderski| `nl`    |Neuronowych dostępne|  Neuronowych|
|Polski|   `en`    |Neuronowych dostępne|  Neuronowych|
|Estoński|  `et`    |Neuronowych dostępne|  Neuronowych|
|Fidżi|    `fj`    |Tylko statystyczne|  Statystyczne|
|Filipino|  `fil`   |Tylko statystyczne|  Statystyczne|
|Fiński|   `fi`    |Neuronowych dostępne|  Neuronowych|
|Francuski|    `fr`    |Neuronowych dostępne|  Neuronowych|
|Niemiecki|    `de`    |Neuronowych dostępne|  Neuronowych|
|Grecki| `el`    |Neuronowych dostępne|  Neuronowych|
|Haitański|    `ht`    |Tylko statystyczne   |Statystyczne|
|Hebrajski |`he`   |Neuronowych dostępne   |Neuronowych|
|Hindi| `hi`    |Domyślne neuronowych|    Neuronowych|
|Hmong Daw| `mww`   |Tylko statystyczne|  Statystyczne|
|Węgierski| `hu`    |Neuronowych dostępne|  Neuronowych|
|Islandzki| `is`    |Tylko neuronowych|   Neuronowych|
|Indonezyjski|    `id`    |Tylko statystyczne|  Statystyczne|
|Włoski|   `it`    |Neuronowych dostępne|  Neuronowych|
|Japoński|  `ja`    |Neuronowych dostępne|  Neuronowych|
|Suahili| `sw`    |Tylko statystyczne|  Statystyczne|
|Klingon|   `tlh`   |Tylko statystyczne|  Statystyczne|
|Klingon (plqaD)|   `tlh-Qaak`  |Tylko statystyczne|  Statystyczne|
|Koreański |`ko`   |Neuronowych dostępne|  Neuronowych|
|Łotewski|   `lv`    |Neuronowych dostępne|  Neuronowych|
|Litewski|    `lt`    |Neuronowych dostępne|  Neuronowych|
|Malgaski|  `mg`    |Tylko statystyczne|  Statystyczne|
|Malajski| `ms`    |Tylko statystyczne   |Statystyczne|
|Maltański|   `mt`    |Tylko statystyczne|  Statystyczne|
|Norweski| `nb`    |Neuronowych dostępne|  Neuronowych|
|Perski|   `fa`    |Tylko statystyczne|  Statystyczne|
|Polski|    `pl`    |Neuronowych dostępne|  Neuronowych|
|Portugalski|    `pt`    |Neuronowych dostępne|  Neuronowych|
|Queretaro Otomi|   `otq`   |Tylko statystyczne|  Statystyczne|
|Rumuński|  `ro`    |Neuronowych dostępne|  Neuronowych|
|Rosyjski|   `ru`    |Neuronowych dostępne|  Neuronowych|
|(Samoa Zachodnie)|    `sm`    |Tylko statystyczne|  Statystyczne|
|Serbski (cyrylica)|    `sr-Cyrl`   |Tylko statystyczne|  Statystyczne|
|Serbski (łaciński)|   `sr-Latn`   |Tylko statystyczne   |Statystyczne|
|Słowacki|    `sk`    |Neuronowych dostępne|  Neuronowych|
|Słoweński| `sl`    |Neuronowych dostępne|  Neuronowych|
|Hiszpański|   `es`    |Neuronowych dostępne|  Neuronowych|
|Szwedzki|   `sv`    |Neuronowych dostępne   |Neuronowych|
|Tahitian|  `ty`    |Tylko statystyczne|  Statystyczne|
|Tamilski| `ta`    |Tylko statystyczne|  Statystyczne|
|Telugu|    `te`    |Tylko neuronowych|   Neuronowych|
|Tajlandzki|  `th`    |Neuronowych dostępne|  Neuronowych|
|Pa'anga|    `to`    |Tylko statystyczne|  Statystyczne|
|Turecki|   `tr`    |Neuronowych dostępne   |Neuronowych|
|Ukraiński| `uk`    |Neuronowych dostępne|  Neuronowych|
|Urdu|  `ur`    |Tylko statystyczne|  Statystyczne|
|Wietnamski|    `vi`    |Neuronowych dostępne|  Neuronowych|
|Walijski| `cy`    |Neuronowych dostępne|  Neuronowych|
|Yucatec Maya|  `yua`   |Tylko statystyczne|  Statystyczne|

## <a name="transliteration"></a>Transliteracja

Metoda Transliterate obsługuje następujące języki. W "do/z" "<> -" oznacza, że język może transliteracja, z lub do jednej z skryptach wymienionych. "-->" Wskazuje, że język może tylko można transliteracja z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do i z niej | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | ar | Arabski | <--> | Łaciński |
|Bengalski  | bn | Bengalski | <--> | Łaciński |
| Chiński (uproszczony) | nazwy zh-Hans | Chiński uproszczony | <--> | Łaciński |
| Chiński (uproszczony) | nazwy zh-Hans | Chiński uproszczony | <--> | Chiński tradycyjny |
| Chiński (tradycyjny) | nazwy zh-Hant | Chiński tradycyjny | <--> | Łaciński |
| Chiński (tradycyjny) | nazwy zh-Hant | Chiński tradycyjny | <--> | Chiński uproszczony |
| Gudżarati | gu  | Gudżarati | --> | Łaciński |
| Hebrajski | ADAM | Hebrajski | <--> | Łaciński |
| Hindi | hi | Dewanagari | <--> | Łaciński |
| Japoński | ja | Japoński | <--> | Łaciński |
| Kannada | kn | Kannada | --> | Łaciński |
| Malajalam | ml | Malajalam | --> | Łaciński |
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
| Suahili      | `sw`          |
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
| macedoński |
| Malajski |
| Maltański |
| Norweski |
| Norweski (nynorsk) |
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
| Somali |
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
