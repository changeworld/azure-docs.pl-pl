---
title: Obsługa języka — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Interfejs API tłumaczenia tekstu w usłudze Translator obsługuje następujące języki dla tłumaczenia tekstu na tekst przy użyciu tłumaczenia maszynowego neuronowych (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 12/02/2019
ms.author: swmachan
ms.openlocfilehash: 2ec8d389c99ad96e59bf49d4345855fa44d6c7aa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902002"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu dla interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator obsługuje tłumaczenie tekstu na tekst przy użyciu następujących języków. Neuronowych Machine Translation (NMT) to nowy standard dla wysokiej jakości tłumaczeń maszyn opartych na formacie AI i jest dostępny jako domyślny przy użyciu wersji 3 interfejs API tłumaczenia tekstu w usłudze Translator, gdy jest dostępny system neuronowych.

[Dowiedz się więcej o tym, jak działa tłumaczenie maszynowe](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tłumaczenie

**Interfejs API usługi Translator w wersji 2**

> [!NOTE]
> Wersja V2 została zaniechana 30 kwietnia 2018. Przeprowadź migrację aplikacji do wersji v3, aby korzystać z nowych funkcji dostępnych wyłącznie w wersji 3.

* Tylko statystyczne: dla tego języka nie jest dostępny system neuronowych.
* Neuronowych dostępne: system neuronowych jest dostępny. Użyj parametru `category=generalnn`, aby uzyskać dostęp do systemu neuronowych.
* Neuronowych domyślne: neuronowych jest domyślnym systemem tłumaczenia. Użyj parametru `category=smt`, aby uzyskać dostęp do systemu statystycznego do użycia z centrum usługi Microsoft Translator.
* Tylko neuronowych: dostępne jest tylko tłumaczenie neuronowych.

**Interfejs API usługi Translator v3** Interfejs API usługi Translator V3 jest neuronowych domyślnie, a systemy statystyczne są dostępne tylko wtedy, gdy nie istnieje system neuronowych.

> [!NOTE]
> Obecnie podzestaw języków neuronowych jest dostępny w usłudze translator niestandardowym i stopniowo dodajemy dodatkowe. [Języki widoku dostępne obecnie w usłudze translator niestandardowym](#customization).

|Język|  Kod języka|  INTERFEJS API V3|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neuronowa|
|Arabski|    `ar`    |   Neuronowa|
|Bengalski|    `bn`    |   Neuronowa|
|Bośniacki (łaciński)|   `bs`    |   Neuronowa|
|Bułgarski| `bg`    |   Neuronowa|
|Kantoński (tradycyjny)|   `yue`|  Statystyczn|
|Kataloński|   `ca`    |   Statystyczn|
|Chiński uproszczony|    `zh-Hans`|Neuronowa|
|Chiński tradycyjny|   `zh-Hant`       |Neuronowa|
|Chorwacki|  `hr`    |Neuronowa|
|Czeski| `cs`    |   Neuronowa|
|Duński|    `da`        |Neuronowa|
|Holenderski| `nl`|   Neuronowa|
|Polski|   `en`    |   Neuronowa|
|Estoński|  `et`    |   Neuronowa|
|Fidżi|    `fj`    |   Statystyczn|
|Filipino|  `fil`   |   Statystyczn|
|Fiński|   `fi`    |   Neuronowa|
|Francuski|    `fr`    |   Neuronowa|
|Niemiecki|    `de`    |   Neuronowa|
|Grecki| `el`    |   Neuronowa|
|Haitański|    `ht`        |Statystyczn|
|Hebrajski |`he`   |Neuronowa
|Hindi| `hi`    |   Neuronowa|
|Hmong Daw| `mww`   |   Statystyczn|
|węgierski| `hu`    |   Neuronowa|
|Islandzki| `is`    |   Neuronowa|
|Indonezyjski|    `id`    |   Statystyczn|
|Irlandzki | `ga`| Neuronowa
|Włoski|   `it`    |   Neuronowa|
|Japoński|  `ja`    |   Neuronowa|
|Kannada|`kn`| Neuronowa
|Suahili| `sw`    |   Statystyczn|
|Klingon|   `tlh`   |   Statystyczn|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statystyczn|
|Koreański |`ko`   |   Neuronowa|
|Łotewski|   `lv`    |   Neuronowa|
|Litewski|    `lt`    |   Neuronowa|
|Malgaski|  `mg`    |   Statystyczn|
|Malajski| `ms`        |Statystyczn|
|Malajalam| `ml` | Neuronowa
|Maltański|   `mt`    |   Statystyczn|
|Maoryjski| `mi`  | Neuronowa|
|Norweski| `nb`    |   Neuronowa|
|Perski|   `fa`    |   Neuronowa|
|Polski|    `pl`    |   Neuronowa|
|Portugalski (Brazylia)|   `pt-br` |   Neuronowa|
|Portugalski (Portugalia)| `pt-pt` | Neuronowa
|Punjabi|`pa`|Neuronowa
|Queretaro Otomi|   `otq`   |   Statystyczn|
|Rumuński|  `ro`    |   Neuronowa|
|Rosyjski|   `ru`    |   Neuronowa|
|(Samoa)|    `sm`    |   Statystyczn|
|Serbski (Cyrylica)|    `sr-Cyrl`|  Statystyczn|
|Serbski (łaciński)|   `sr-Latn`       |Statystyczn|
|Słowacki|    `sk`    |   Neuronowa|
|Słoweński| `sl`    |   Neuronowa|
|Hiszpański|   `es`    |   Neuronowa|
|Szwedzki|   `sv`    |Neuronowa|
|Tahitian|  `ty`    |Statystyczn|
|Tamilski| `ta`    |   Neuronowa|
|Telugu|    `te`    |   Neuronowa|
|Tajlandzki|  `th`    |   Neuronowa|
|Pa'anga|    `to`    |   Statystyczn|
|Turecki|   `tr`        |Neuronowa|
|Ukraiński| `uk`    |   Neuronowa|
|Urdu|  `ur`    |   Statystyczn|
|Wietnamski|    `vi`    |   Neuronowa|
|Walijski| `cy`    |   Neuronowa|
|Yucatec Maya|  `yua`   |   Statystyczn|

> [!NOTE]
> Kod języka `pt` zostanie domyślnie `pt-br`, portugalski (Brazylia).

## <a name="transliteration"></a>Transliteracja

Metoda transliteracji obsługuje następujące języki. W polu "do/z" <--> "wskazuje, że język można zatransliteracjć z lub do jednego z wymienionych skryptów. "-->" Wskazuje, że język może być zatransliteracji tylko z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do/z | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | `ar` | `Arab` arabski | <--> | `Latn` łaciński |
|Bengalski  | `bn` | `Beng` bengalski | <--> | `Latn` łaciński |
| Chiński uproszczony | `zh-Hans` | Chiński uproszczony `Hans`| <--> | `Latn` łaciński |
| Chiński uproszczony | `zh-Hans` | Chiński uproszczony `Hans`| <--> | Chiński tradycyjny `Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | `Latn` łaciński |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Chiński uproszczony `Hans` |
| Gujarati | `gu`  | `Gujr` gudżarati | --> | `Latn` łaciński |
| Hebrajski | `he` | `Hebr` hebrajski | <--> | `Latn` łaciński |
| Hindi | `hi` | Devanagari `Deva` | <--> | `Latn` łaciński |
| Japoński | `ja` | `Jpan` japoński | <--> | `Latn` łaciński |
| Kannada | `kn` | `Knda` kannada | --> | `Latn` łaciński |
| Malajalam | `ml` | `Mlym` malajalam | --> | `Latn` łaciński |
| Marathi | `mr` | Devanagari `Deva` | --> | `Latn` łaciński |
| Orija | `or` | `Orya` orija | <--> | `Latn` łaciński |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | `Latn` łaciński  |
| Serbski (Cyrylica) | `sr-Cyrl` | `Cyrl` cyrylicy  | --> | `Latn` łaciński |
| Serbski (łaciński) | `sr-Latn` | `Latn` łaciński | --> | `Cyrl` cyrylicy|
| Tamilski | `ta` | `Taml` tamilski | --> | `Latn` łaciński |
| Telugu | `te` | `Telu` telugu | --> | `Latn` łaciński |
| Tajlandzki | `th` | `Thai` tajlandzki | <--> | `Latn` łaciński |

## <a name="dictionary"></a>Słownik

Słownik obsługuje następujące języki w języku angielskim lub z niego przy użyciu metod wyszukiwania i przykładów.

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
| węgierski      | `hu`          |
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
| Portugalski (Brazylia)     | `pt-br`          |
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

## <a name="detect"></a>Wykrywanie

Interfejs API tłumaczenia tekstu w usłudze Translator wykrywa wszystkie języki dostępne do tłumaczenia i przekształcenie.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programowe uzyskiwanie dostępu do interfejs API tłumaczenia tekstu w usłudze Translatorej listy języków

Listę obsługiwanych języków dla interfejs API tłumaczenia tekstu w usłudze Translator v 3.0 można pobrać przy użyciu metody Languages. Możesz wyświetlić listę według funkcji, kod języka, a także nazwę języka w języku angielskim lub innym obsługiwanym języku. Ta lista jest automatycznie aktualizowana przez usługę Microsoft Translator w miarę udostępniania nowych języków.

[Dokumentacja dotycząca operacji wyświetlania języków](reference/v3-0-languages.md)

## <a name="customization"></a>Dostosowywanie

Następujące języki są dostępne do dostosowania do języka angielskiego lub z niego w języku angielskim przy użyciu [translatora niestandardowego](https://aka.ms/CustomTranslator).

| Język    | Kod języka |
|:----------- |:-------------:|
| Arabski       | `ar`          |
| Bengalski      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Chiński uproszczony      | `zh-Hans`          |
|Chiński tradycyjny|   `zh-Hant`   |
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
| węgierski      | `hu`          |
| Islandzki | `is` |
| Indonezyjski|   `id`    |
| Irlandzki | `ga`  |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Suahili|    `sw`    |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malgaski| `mg`    |
| Maoryjski| `mi`  |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski (Brazylia) | `pt-br` |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| (Samoa)|   `sm`    |
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

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie sieci Web usługi Microsoft Translator

Aby uzyskać szybki przegląd języków, w witrynie sieci Web usługi Microsoft Translator są wyświetlane wszystkie języki obsługiwane przez tłumaczenie tekstu w usłudze Translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacji specyficznych dla deweloperów, takich jak kody języka.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
