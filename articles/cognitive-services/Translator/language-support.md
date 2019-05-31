---
title: Obsługa języków — interfejs API tekstu usługi Translator
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwanych przez interfejs API tekstu usługi Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 153fd5a8202ca0d919a2254449d846f8798bd10d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382493"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu dla interfejsu API tłumaczenia tekstu

Interfejs API tekstu usługi Translator obsługuje następujące języki do tłumaczenia tekstu na tekst. Tłumaczenie neuronowe (NMT) stanowi nowy standard w poszukiwaniu tłumaczeń maszyny bazujących na sztucznej Inteligencji wysokiej jakości i jest dostępny jako domyślne za pomocą interfejsu API tekstu usługi Translator w wersji 3, gdy neuronowych system będzie dostępny.

[Dowiedz się więcej o tym, jak działa tłumaczenia maszynowego](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>{1&gt;Translacja&lt;1}

**W wersji 2 Translator interfejsu API**

> [!NOTE]
> W wersji 2 została zakończona na 30 kwietnia 2018 r. Przeprowadź migrację aplikacji do wersji 3, aby można było korzystać z zalet nowych funkcji dostępnych wyłącznie w wersji 3.

* Statystyczne tylko: System neuronowej, nie jest dostępna dla tego języka.
* Neuronowych dostępne: Neuronowych system jest niedostępny. Użyj parametru `category=generalnn` dostęp do tego systemu neuronowych.
* Domyślnie neuronowej: Neuronowych jest domyślny system tłumaczeń. Użyj parametru `category=smt` dostęp do tego systemu statystycznych do użycia z usługą Microsoft Translator Hub.
* Neuronowych tylko: Tylko tłumaczenie neuronowe jest dostępna.

**Interfejs API usługi Translator w wersji 3** interfejs API usługi Translator w wersji 3 jest neuronowych domyślnie i systemy statystyczne są dostępne tylko, gdy nie istnieje żaden system neuronowych. 

> [!NOTE]
> Obecnie podzbioru języków neuronowych są dostępne w niestandardowych w usłudze Translator i stopniowo dodajemy także dodatkowe. [Wyświetl języków jest obecnie dostępna w niestandardowych w usłudze Translator](#customization).

|Język|  Kod języka|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Tylko statystyczne|  Neuronowa|
|Arabski|    `ar`    |Neuronowych dostępne|  Neuronowa|
|Bengalski|    `bn`    |Neuronowych dostępne|  Neuronowa|
|Bośniacki (łaciński)|   `bs`    |Neuronowych dostępne|  Neuronowa|
|Bułgarski| `bg`    |Neuronowych dostępne|  Neuronowa|
|Kantoński (tradycyjny)|   `yue`   |Tylko statystyczne|  Statystyczne|
|Kataloński|   `ca`    |Tylko statystyczne|  Statystyczne|
|Chiński uproszczony|    `zh-Hans`   |Domyślne neuronowych |Neuronowa|
|Chiński tradycyjny|   `zh-Hant`   |Domyślne neuronowych |Neuronowa|
|Chorwacki|  `hr`    |Neuronowych dostępne|  Neuronowa|
|Czeski| `cs`    |Neuronowych dostępne|  Neuronowa|
|Duński|    `da`    |Neuronowych dostępne   |Neuronowa|
|Holenderski| `nl`    |Neuronowych dostępne|  Neuronowa|
|Polski|   `en`    |Neuronowych dostępne|  Neuronowa|
|Estoński|  `et`    |Neuronowych dostępne|  Neuronowa|
|Fidżi|    `fj`    |Tylko statystyczne|  Statystyczne|
|Filipino|  `fil`   |Tylko statystyczne|  Statystyczne|
|Fiński|   `fi`    |Neuronowych dostępne|  Neuronowa|
|Francuski|    `fr`    |Neuronowych dostępne|  Neuronowa|
|Niemiecki|    `de`    |Neuronowych dostępne|  Neuronowa|
|Grecki| `el`    |Neuronowych dostępne|  Neuronowa|
|Haitański|    `ht`    |Tylko statystyczne   |Statystyczne|
|Hebrajski |`he`   |Neuronowych dostępne   |Neuronowa|
|Hindi| `hi`    |Domyślne neuronowych|    Neuronowa|
|Hmong Daw| `mww`   |Tylko statystyczne|  Statystyczne|
|Węgierski| `hu`    |Neuronowych dostępne|  Neuronowa|
|Islandzki| `is`    |Tylko neuronowych|   Neuronowa|
|Indonezyjski|    `id`    |Tylko statystyczne|  Statystyczne|
|Włoski|   `it`    |Neuronowych dostępne|  Neuronowa|
|Japoński|  `ja`    |Neuronowych dostępne|  Neuronowa|
|Suahili| `sw`    |Tylko statystyczne|  Statystyczne|
|Klingon|   `tlh`   |Tylko statystyczne|  Statystyczne|
|Klingon (plqaD)|   `tlh-Qaak`  |Tylko statystyczne|  Statystyczne|
|Koreański |`ko`   |Neuronowych dostępne|  Neuronowa|
|Łotewski|   `lv`    |Neuronowych dostępne|  Neuronowa|
|Litewski|    `lt`    |Neuronowych dostępne|  Neuronowa|
|Malgaski|  `mg`    |Tylko statystyczne|  Statystyczne|
|Malajski| `ms`    |Tylko statystyczne   |Statystyczne|
|Maltański|   `mt`    |Tylko statystyczne|  Statystyczne|
|Norweski| `nb`    |Neuronowych dostępne|  Neuronowa|
|Perski|   `fa`    |Tylko statystyczne|  Statystyczne|
|Polski|    `pl`    |Neuronowych dostępne|  Neuronowa|
|Portugalski|    `pt`    |Neuronowych dostępne|  Neuronowa|
|Queretaro Otomi|   `otq`   |Tylko statystyczne|  Statystyczne|
|Rumuński|  `ro`    |Neuronowych dostępne|  Neuronowa|
|Rosyjski|   `ru`    |Neuronowych dostępne|  Neuronowa|
|(Samoa Zachodnie)|    `sm`    |Tylko statystyczne|  Statystyczne|
|Serbski (Cyrylica)|    `sr-Cyrl`   |Tylko statystyczne|  Statystyczne|
|Serbski (łaciński)|   `sr-Latn`   |Tylko statystyczne   |Statystyczne|
|Słowacki|    `sk`    |Neuronowych dostępne|  Neuronowa|
|Słoweński| `sl`    |Neuronowych dostępne|  Neuronowa|
|Hiszpański|   `es`    |Neuronowych dostępne|  Neuronowa|
|Szwedzki|   `sv`    |Neuronowych dostępne   |Neuronowa|
|Tahitian|  `ty`    |Tylko statystyczne|  Statystyczne|
|Tamilski| `ta`    |Tylko statystyczne|  Statystyczne|
|Telugu|    `te`    |Tylko neuronowych|   Neuronowa|
|Tajlandzki|  `th`    |Neuronowych dostępne|  Neuronowa|
|Pa'anga|    `to`    |Tylko statystyczne|  Statystyczne|
|Turecki|   `tr`    |Neuronowych dostępne   |Neuronowa|
|Ukraiński| `uk`    |Neuronowych dostępne|  Neuronowa|
|Urdu|  `ur`    |Tylko statystyczne|  Statystyczne|
|Wietnamski|    `vi`    |Neuronowych dostępne|  Neuronowa|
|Walijski| `cy`    |Neuronowych dostępne|  Neuronowa|
|Yucatec Maya|  `yua`   |Tylko statystyczne|  Statystyczne|

## <a name="transliteration"></a>Transliteracja

Metoda Transliterate obsługuje następujące języki. W "do/z" "<> -" oznacza, że język może transliteracja, z lub do jednej z skryptach wymienionych. "-->" Wskazuje, że język może tylko można transliteracja z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do i z niej | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | `ar` | Arabski `Arab` | <--> | Łaciński `Latn` |
|Bengalski  | `bn` | Bengalski `Beng` | <--> | Łaciński `Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony `Hans`| <--> | Łaciński `Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony `Hans`| <--> | Chiński tradycyjny `Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Łaciński `Latn` |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Chiński uproszczony `Hans` |
| Gudżarati | `gu`  | Gudżarati `Gujr` | --> | Łaciński `Latn` |
| Hebrajski | `he` | Hebrajski `Hebr` | <--> | Łaciński `Latn` |
| Hindi | `hi` | Dewanagari `Deva` | <--> | Łaciński `Latn` |
| Japoński | `ja` | Japoński `Jpan` | <--> | Łaciński `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Łaciński `Latn` |
| Malajalam | `ml` | Malayalam `Mlym` | --> | Łaciński `Latn` |
| Marathi | `mr` | Dewanagari `Deva` | --> | Łaciński `Latn` |
| Orija | `or` | Oriya `Orya` | <--> | Łaciński `Latn` |
| Pendżabski | `pa` | Gurmukhi `Guru`  | <--> | Łaciński `Latn`  |
| Serbski (Cyrylica) | `sr-Cyrl` | Cyrylica `Cyrl`  | --> | Łaciński `Latn` |
| Serbski (łaciński) | `sr-Latn` | Łaciński `Latn` | --> | Cyrylica `Cyrl`|
| Tamilski | `ta` | Tamilski `Taml` | --> | Łaciński `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Łaciński `Latn` |
| Tajlandzki | `th` | Tajski `Thai` | <--> | Łaciński `Latn` |

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

## <a name="detect"></a>Detect

Interfejs API tekstu usługi Translator wykrywa wszystkie języki dostępne dla tłumaczenia i transliterację.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programowy dostęp Lista języków interfejsu API tłumaczenia tekstu

Możesz pobrać listę obsługiwanych języków dla interfejsu API tłumaczenia tekstu w wersji 3.0, przy użyciu metody języków. Możesz wyświetlić listę funkcji, kod języka, a także nazwę języka, w językach angielskim i innych obsługiwanych języków. Ta lista jest automatycznie aktualizowana przez usługę Microsoft Translator jako nowe języki są udostępniane.

[Przejrzyj dokumentację referencyjną operacji języków](reference/v3-0-languages.md)

## <a name="customization"></a>Dostosowywanie

Następujące języki są możliwe do dostosowania do lub z angielskiego przy użyciu [niestandardowe w usłudze Translator](https://aka.ms/CustomTranslator).

| Język    | Kod języka |
|:----------- |:-------------:|
| Arabski       | `ar`          |
| Bengalski      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Chiński uproszczony      | `zh-Hans`          |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Holenderski      | `nl`          |
| Polski    | `en`     |
| Estoński      | `et`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Węgierski      | `hu`          |
| Islandzki | `is` |
| Indonezyjski|   `id`    |
| Włoski      | `it`          |
| Japoński      | `ja`          |
|Suahili| `sw`    |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
|Malgaski|  `mg`    |
| Norweski      | `nb`          |
| Polski      | `pl`          |
| Portugalski      | `pt`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
|(Samoa Zachodnie)|    `sm`    |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tajlandzki      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Wietnamski      | `vi`          |
| Walijski | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie internetowej Microsoft Translator

Dla rzut oka na językach usługa Microsoft Translator witryny sieci Web pokazuje wszystkie języki obsługiwane przez tekstu usługi Translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacje specyficzne dla deweloperów, takie jak kodów języków.

[Zobacz listy języków](https://www.microsoft.com/translator/languages.aspx)
