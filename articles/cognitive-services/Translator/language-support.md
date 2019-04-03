---
title: Obsługa języków — interfejs API tekstu usługi Translator
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwanych przez interfejs API tekstu usługi Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: d98922937781fd169d34881fa67a6b5746d06df7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882468"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu dla interfejsu API tłumaczenia tekstu

Interfejs API tekstu usługi Translator obsługuje następujące języki do tłumaczenia tekstu na tekst. Tłumaczenie neuronowe (NMT) stanowi nowy standard w poszukiwaniu tłumaczeń maszyny bazujących na sztucznej Inteligencji wysokiej jakości i jest dostępny jako domyślne za pomocą interfejsu API tekstu usługi Translator w wersji 3, gdy neuronowych system będzie dostępny.

[Dowiedz się więcej o tym, jak działa tłumaczenia maszynowego](https://www.microsoft.com/translator/mt.aspx)

**W wersji 2 Translator interfejsu API**

> [!NOTE]
> W wersji 2 została zakończona w dniu 30 kwietnia 2018 i zostanie zakończona w dniu 30 kwietnia 2019 r.

* Statystyczne tylko: System neuronowej, nie jest dostępna dla tego języka.
* Neuronowych dostępne: Neuronowych system jest niedostępny. Użyj parametru `category=generalnn` dostęp do tego systemu neuronowych.
* Domyślnie neuronowej: Neuronowych jest domyślny system tłumaczeń. Użyj parametru `category=smt` dostęp do tego systemu statystycznych do użycia z usługą Microsoft Translator Hub.
* Neuronowych tylko: Tylko tłumaczenie neuronowe jest dostępna.

**Interfejs API usługi Translator w wersji 3** interfejs API usługi Translator w wersji 3 jest neuronowych domyślnie i systemy statystyczne są dostępne tylko, gdy nie istnieje żaden system neuronowych. Niestandardowe w usłudze Translator należy używać tylko z językami neuronowych. [Wyświetl języków jest obecnie dostępna w niestandardowych w usłudze Translator](#customization).

|Język|  Kod języka|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Tylko statystyczne|  Neuronowych|
|Arabski|    `ar`    |Neuronowych dostępne|  Neuronowych|
|Bengalski|    `bn`    |Neuronowych dostępne|  Neuronowych|
|Bośniacki (łaciński)|   `bs`    |Neuronowych dostępne|  Neuronowych|
|Bułgarski| `bg`    |Neuronowych dostępne|  Neuronowych|
|Kantoński (tradycyjny)|   `yue`   |Tylko statystyczne|  Statystyczne|
|Kataloński|   `ca`    |Tylko statystyczne|  Statystyczne|
|Chiński (uproszczony)|    `zh-Hans`   |Domyślne neuronowych |Neuronowych|
|Chiński tradycyjny|   `zh-Hant`   |Domyślne neuronowych |Neuronowych|
|Chorwacki|  `hr`    |Neuronowych dostępne|  Neuronowych|
|Czeski| `cs`    |Neuronowych dostępne|  Neuronowych|
|Duński|    `da`    |Neuronowych dostępne   |Neuronowych|
|Holenderski| `nl`    |Neuronowych dostępne|  Neuronowych|
|Polski|   `en`    |Neuronowych dostępne|  Neuronowych|
|Estoński|  `et`    |Neuronowych dostępne|  Neuronowych|
|Fidżyjski|    `fj`    |Tylko statystyczne|  Statystyczne|
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
|Samoański|    `sm`    |Tylko statystyczne|  Statystyczne|
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
|Tonga|    `to`    |Tylko statystyczne|  Statystyczne|
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
| Arabski | `ar` | Arabski `Arab` | <--> | Łaciński `Latn` |
|Bengalski  | `bn` | Bengalski `Beng` | <--> | Łaciński `Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński (uproszczony) `Hans`| <--> | Łaciński `Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński (uproszczony) `Hans`| <--> | Chiński tradycyjny `Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Łaciński `Latn` |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Chiński (uproszczony) `Hans` |
| Gudżarati | `gu`  | Gudżarati `Gujr` | --> | Łaciński `Latn` |
| Hebrajski | `he` | Hebrajski `Hebr` | <--> | Łaciński `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Łaciński `Latn` |
| Japoński | `ja` | Japoński `Jpan` | <--> | Łaciński `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Łaciński `Latn` |
| Malajalam | `ml` | Malajalam `Mlym` | --> | Łaciński `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Łaciński `Latn` |
| Orija | `or` | Orija `Orya` | <--> | Łaciński `Latn` |
| Pendżabski | `pa` | Gurmukhi `Guru`  | <--> | Łaciński `Latn`  |
| Serbski (cyrylica) | `sr-Cyrl` | Cyrylica `Cyrl`  | --> | Łaciński `Latn` |
| Serbski (łaciński) | `sr-Latn` | Łaciński `Latn` | --> | Cyrylica `Cyrl`|
| Tamilski | `ta` | Tamilski `Taml` | --> | Łaciński `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Łaciński `Latn` |
| Tajlandzki | `th` | Tajlandzki `Thai` | <--> | Łaciński `Latn` |

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
| Chiński (uproszczony)      | `zh-Hans`          |
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

Za pomocą metody wykrywania są obsługiwane następujące języki. Wykrywanie może rozpoznać języków, które nie może tłumaczyć Microsoft Translator.

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
| Chiński (uproszczony)      | `zh-Hans`          |
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
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Norweski      | `nb`          |
| Polski      | `pl`          |
| Portugalski      | `pt`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
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
