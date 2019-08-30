---
title: Obsługa języka — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwanych przez interfejs API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be69f18f30b796e7401ef68aa5088153e07f14f5
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127885"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu dla interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator obsługuje tłumaczenie tekstu na tekst przy użyciu następujących języków. Neuronowych Machine Translation (NMT) to nowy standard dla wysokiej jakości tłumaczeń maszyn opartych na formacie AI i jest dostępny jako domyślny przy użyciu wersji 3 interfejs API tłumaczenia tekstu w usłudze Translator, gdy jest dostępny system neuronowych.

[Dowiedz się więcej o tym, jak działa tłumaczenie maszynowe](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>{1&gt;Translacja&lt;1}

**Interfejs API usługi Translator w wersji 2**

> [!NOTE]
> Wersja V2 została zaniechana 30 kwietnia 2018. Przeprowadź migrację aplikacji do wersji v3, aby korzystać z nowych funkcji dostępnych wyłącznie w wersji 3.

* Tylko statystyczne: Dla tego języka nie jest dostępny żaden system neuronowych.
* Neuronowych dostępne: Dostępny jest system neuronowych. Użyj parametru `category=generalnn` , aby uzyskać dostęp do systemu neuronowych.
* Neuronowych domyślne: Neuronowych to domyślny system tłumaczenia. Użyj parametru `category=smt` , aby uzyskać dostęp do systemu statystycznego do użycia z centrum usługi Microsoft Translator.
* Tylko neuronowych: Dostępne są tylko tłumaczenia neuronowych.

**Interfejs API usługi Translator v3** Interfejs API usługi Translator V3 jest neuronowych domyślnie, a systemy statystyczne są dostępne tylko wtedy, gdy nie istnieje system neuronowych.

> [!NOTE]
> Obecnie podzestaw języków neuronowych jest dostępny w usłudze translator niestandardowym i stopniowo dodajemy dodatkowe. [Języki widoku dostępne obecnie w usłudze translator](#customization)niestandardowym.

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
|Angielski|   `en`    |Neuronowych dostępne|  Neuronowa|
|Estoński|  `et`    |Neuronowych dostępne|  Neuronowa|
|Fidżi|    `fj`    |Tylko statystyczne|  Statystyczn|
|Filipino|  `fil`   |Tylko statystyczne|  Statystyczn|
|Fiński|   `fi`    |Neuronowych dostępne|  Neuronowa|
|Francuski|    `fr`    |Neuronowych dostępne|  Neuronowa|
|niemiecki|    `de`    |Neuronowych dostępne|  Neuronowa|
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
|Norweski| `nb`    |Neuronowych dostępne|  Neuronowa|
|Perski|   `fa`    |Neuronowych dostępne|  Neuronowa|
|Polski|    `pl`    |Neuronowych dostępne|  Neuronowa|
|Portugalski|    `pt`    |Neuronowych dostępne|  Neuronowa|
|Queretaro Otomi|   `otq`   |Tylko statystyczne|  Statystyczn|
|Rumuński|  `ro`    |Neuronowych dostępne|  Neuronowa|
|Rosyjski|   `ru`    |Neuronowych dostępne|  Neuronowa|
|(Samoa Zachodnie)|    `sm`    |Tylko statystyczne|  Statystyczn|
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
| Arabski | `ar` | Arabski`Arab` | <--> | Wielka`Latn` |
|Bengalski  | `bn` | Bengalski`Beng` | <--> | Wielka`Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony`Hans`| <--> | Wielka`Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony`Hans`| <--> | Chiński tradycyjny`Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny`Hant`| <--> | Wielka`Latn` |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny`Hant`| <--> | Chiński uproszczony`Hans` |
| Gudżarati | `gu`  | Gudżarati`Gujr` | --> | Wielka`Latn` |
| Hebrajski | `he` | Hebrajski`Hebr` | <--> | Wielka`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Wielka`Latn` |
| Japoński | `ja` | Japoński`Jpan` | <--> | Wielka`Latn` |
| Kannada | `kn` | Kannada`Knda` | --> | Wielka`Latn` |
| Malajalam | `ml` | Malayalam`Mlym` | --> | Wielka`Latn` |
| Marathi | `mr` | Devanagari`Deva` | --> | Wielka`Latn` |
| Orija | `or` | Orija`Orya` | <--> | Wielka`Latn` |
| Pendżabski | `pa` | Gurmukhi`Guru`  | <--> | Wielka`Latn`  |
| Serbski (Cyrylica) | `sr-Cyrl` | Pisanych`Cyrl`  | --> | Wielka`Latn` |
| Serbski (łaciński) | `sr-Latn` | Wielka`Latn` | --> | Pisanych`Cyrl`|
| Tamilski | `ta` | Tamilski`Taml` | --> | Wielka`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Wielka`Latn` |
| Tajlandzki | `th` | Tajski`Thai` | <--> | Wielka`Latn` |

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
| Włoski      | `it`          |
| Japoński      | `ja`          |
|Suahili| `sw`    |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
|Malgaski|  `mg`    |
| Norweski      | `nb`          |
| Perski      | `fa`          |
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

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie sieci Web usługi Microsoft Translator

Aby uzyskać szybki przegląd języków, w witrynie sieci Web usługi Microsoft Translator są wyświetlane wszystkie języki obsługiwane przez tłumaczenie tekstu w usłudze Translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacji specyficznych dla deweloperów, takich jak kody języka.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
