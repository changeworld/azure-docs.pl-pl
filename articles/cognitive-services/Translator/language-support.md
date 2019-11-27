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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 25ab298a8b72b3734e41daf66ef5063b8e43a96e
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286617"
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

|Język|  Kod języka|  INTERFEJS API V2| INTERFEJS API V3|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Tylko statystyczne|  Neuronowa|
|Arabski|    `ar`    |Neuronowych dostępne|  Neuronowa|
|Bengalski|    `bn`    |Neuronowych dostępne|  Neuronowa|
|Bośniacki (łaciński)|   `bs`    |Neuronowych dostępne|  Neuronowa|
|Bułgarski| `bg`    |Neuronowych dostępne|  Neuronowa|
|Kantoński (tradycyjny)|   `yue`   |Tylko statystyczne|  Statystyczn|
|Kataloński|   `ca`    |Tylko statystyczne|  Statystyczn|
|Chiński uproszczony|    `zh-Hans`   |Neuronowych domyślne |Neuronowa|
|Chiński tradycyjny|   `zh-Hant`   |Neuronowych domyślne |Neuronowa|
|Chorwacki|  `hr`    |Neuronowych dostępne|  Neuronowa|
|Czeski| `cs`    |Neuronowych dostępne|  Neuronowa|
|Duński|    `da`    |Neuronowych dostępne   |Neuronowa|
|Holenderski| `nl`    |Neuronowych dostępne|  Neuronowa|
|Polski|   `en`    |Neuronowych dostępne|  Neuronowa|
|Estoński|  `et`    |Neuronowych dostępne|  Neuronowa|
|Fidżi|    `fj`    |Tylko statystyczne|  Statystyczn|
|Filipino|  `fil`   |Tylko statystyczne|  Statystyczn|
|Fiński|   `fi`    |Neuronowych dostępne|  Neuronowa|
|Francuski|    `fr`    |Neuronowych dostępne|  Neuronowa|
|Niemiecki|    `de`    |Neuronowych dostępne|  Neuronowa|
|Grecki| `el`    |Neuronowych dostępne|  Neuronowa|
|Haitański|    `ht`    |Tylko statystyczne   |Statystyczn|
|Hebrajski |`he`   |Neuronowych dostępne   |Neuronowa|
|Hindi| `hi`    |Neuronowych domyślne|    Neuronowa|
|Hmong Daw| `mww`   |Tylko statystyczne|  Statystyczn|
|Węgierski| `hu`    |Neuronowych dostępne|  Neuronowa|
|Islandzki| `is`    |Tylko neuronowych|   Neuronowa|
|Indonezyjski|    `id`    |Tylko statystyczne|  Statystyczn|
|Włoski|   `it`    |Neuronowych dostępne|  Neuronowa|
|Japoński|  `ja`    |Neuronowych dostępne|  Neuronowa|
|Suahili| `sw`    |Tylko statystyczne|  Statystyczn|
|Klingon|   `tlh`   |Tylko statystyczne|  Statystyczn|
|Klingon (plqaD)|   `tlh-Qaak`  |Tylko statystyczne|  Statystyczn|
|Koreański |`ko`   |Neuronowych dostępne|  Neuronowa|
|Łotewski|   `lv`    |Neuronowych dostępne|  Neuronowa|
|Litewski|    `lt`    |Neuronowych dostępne|  Neuronowa|
|Malgaski|  `mg`    |Tylko statystyczne|  Statystyczn|
|Malajski| `ms`    |Tylko statystyczne   |Statystyczn|
|Maltański|   `mt`    |Tylko statystyczne|  Statystyczn|
|Maoryjski| `mi`  |Tylko neuronowych| Neuronowa|
|Norweski| `nb`    |Neuronowych dostępne|  Neuronowa|
|Perski|   `fa`    |Neuronowych dostępne|  Neuronowa|
|Polski|    `pl`    |Neuronowych dostępne|  Neuronowa|
|Portugalski|    `pt`    |Neuronowych dostępne|  Neuronowa|
|Queretaro Otomi|   `otq`   |Tylko statystyczne|  Statystyczn|
|Rumuński|  `ro`    |Neuronowych dostępne|  Neuronowa|
|rosyjski|   `ru`    |Neuronowych dostępne|  Neuronowa|
|(Samoa)|    `sm`    |Tylko statystyczne|  Statystyczn|
|Serbski (Cyrylica)|    `sr-Cyrl`   |Tylko statystyczne|  Statystyczn|
|Serbski (łaciński)|   `sr-Latn`   |Tylko statystyczne   |Statystyczn|
|Słowacki|    `sk`    |Neuronowych dostępne|  Neuronowa|
|Słoweński| `sl`    |Neuronowych dostępne|  Neuronowa|
|Hiszpański|   `es`    |Neuronowych dostępne|  Neuronowa|
|Szwedzki|   `sv`    |Neuronowych dostępne   |Neuronowa|
|Tahitian|  `ty`    |Tylko statystyczne|  Statystyczn|
|Tamilski| `ta`    |Tylko statystyczne|  Statystyczn|
|Telugu|    `te`    |Tylko neuronowych|   Neuronowa|
|Tajlandzki|  `th`    |Neuronowych dostępne|  Neuronowa|
|Pa'anga|    `to`    |Tylko statystyczne|  Statystyczn|
|Turecki|   `tr`    |Neuronowych dostępne   |Neuronowa|
|Ukraiński| `uk`    |Neuronowych dostępne|  Neuronowa|
|Urdu|  `ur`    |Tylko statystyczne|  Statystyczn|
|Wietnamski|    `vi`    |Neuronowych dostępne|  Neuronowa|
|Walijski| `cy`    |Neuronowych dostępne|  Neuronowa|
|Yucatec Maya|  `yua`   |Tylko statystyczne|  Statystyczn|

## <a name="transliteration"></a>Transliteracja

Metoda transliteracji obsługuje następujące języki. W polu "do/z" <--> "wskazuje, że język można zatransliteracjć z lub do jednego z wymienionych skryptów. "-->" Wskazuje, że język może być zatransliteracji tylko z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do/z | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | `ar` | `Arab` arabski | <--> | `Latn` łaciński |
|Bengalski  | `bn` | `Beng` bengalski | <--> | `Latn` łaciński |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony `Hans`| <--> | `Latn` łaciński |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony `Hans`| <--> | Chiński tradycyjny `Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | `Latn` łaciński |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Chiński uproszczony `Hans` |
| Gudżarati | `gu`  | `Gujr` gudżarati | --> | `Latn` łaciński |
| Hebrajski | `he` | `Hebr` hebrajski | <--> | `Latn` łaciński |
| Hindi | `hi` | Devanagari `Deva` | <--> | `Latn` łaciński |
| Japoński | `ja` | `Jpan` japoński | <--> | `Latn` łaciński |
| Kannada | `kn` | `Knda` kannada | --> | `Latn` łaciński |
| Malajalam | `ml` | `Mlym` malajalam | --> | `Latn` łaciński |
| Marathi | `mr` | Devanagari `Deva` | --> | `Latn` łaciński |
| Orija | `or` | `Orya` orija | <--> | `Latn` łaciński |
| Pendżabski | `pa` | Gurmukhi `Guru`  | <--> | `Latn` łaciński  |
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
| rosyjski      | `ru`          |
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
| Węgierski      | `hu`          |
| Islandzki | `is` |
| Indonezyjski|   `id`    |
| Irlandzki | `ga`  |
| Włoski      | `it`          |
| Japoński      | `ja`          |
|Suahili| `sw`    |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
|Malgaski|  `mg`    |
|Maoryjski| `mi`  |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski      | `pt`          |
| Rumuński      | `ro`          |
| rosyjski      | `ru`          |
|(Samoa)|    `sm`    |
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
