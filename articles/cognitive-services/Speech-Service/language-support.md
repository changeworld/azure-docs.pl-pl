---
title: Obsługa języków — usługi mowy
titleSuffix: Azure Cognitive Services
description: Usługi mowy platformy Azure obsługuje wiele języków dla konwersji mowy na tekst i zamiany tekstu na mowę, oraz tłumaczenia mowy. Ten artykuł zawiera pełną listę Obsługa języków przez usługę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9b8e12220f220bd8183675d13e25bdcab02707fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65020854"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Obsługa języka i regionu dla usług przetwarzania mowy

Różne języki są obsługiwane przez różne funkcje usług przetwarzania mowy. W poniższej tabeli podsumowano obsługę języka.

## <a name="speech-to-text"></a>Zamiany mowy na tekst

Interfejs API rozpoznawania mowy firmy Microsoft obsługuje następujące języki. Różne poziomy możliwości dostosowania są dostępne dla każdego języka.

  Kod | Język | [Akustyczna](how-to-customize-acoustic-models.md) | [Dostosowania języka](how-to-customize-language-model.md) | [Wymowa dostosowania](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar EG | Arabski (Egipt), standard nowoczesne | Nie | Yes | Nie
 ES urzędu certyfikacji | Kataloński | Nie | Nie | Nie
 Akcelerator deweloperski w wersji DK | Duński (Dania) | Nie | Nie | Nie
 de-DE. | Niemiecki (Niemcy) | Yes | Yes | Nie
 EN-AU | Angielski (Australia) | Nie | Yes | Yes
 EN-CA | Angielski (Kanada) | Nie | Yes | Yes
 en-GB | Angielski (Zjednoczone Królestwo) | Nie | Yes | Yes
 EN-IN | English (India) | Yes | Yes | Yes
 EN NZ | Angielski (Nowa Zelandia) | Nie | Yes | Yes  
 en-US | Angielski (Stany Zjednoczone) | Yes | Yes | Yes
 es-ES | Hiszpański (Hiszpania) | Yes | Yes | Nie
 es-MX | Hiszpański (Meksyk) | Nie | Yes | Nie
 fi-FI | Fiński (Finlandia) | Nie | Nie | Nie
 fr-CA | Francuski (Kanada) | Nie | Yes | Nie
 fr-FR | Francuski (Francja) | Yes | Yes | Nie
 w | Hindi (Indie) | Nie | Yes | Nie
 IT-IT | Włoski (Włochy) | Yes | Yes | Nie
 ja-JP | Japoński (Japonia) | Nie | Yes | Nie
 ko-KR | Koreański (Korea) | Nie | Yes | Nie
 nb-NO | Norweski (Bokmal) (Norwegia) | Nie | Nie | Nie
 NL-NL | Holenderski (Holandia) | Nie | Yes | Nie
 pl-PL | Polski (Polska) | Nie | Nie | Nie
 pt-BR | Portugalski (Brazylia) | Yes | Yes | Nie
 pt-PT | Portugalski (Portugalia) | Nie | Yes | Nie
 ru-RU | Rosyjski (Rosja) | Yes | Yes | Nie
 sv-SE | Szwedzki (Szwecja) | Nie | Nie | Nie
 zh-CN | Chiński (mandaryński uproszczony) | Yes | Yes | Nie
 zh-HK | Chiński (kantoński, tradycyjny) | Nie | Yes | Nie
 zh-TW | Chiński (mandaryński tajwańskie) | Nie | Yes | Nie
 th TH | Tajski (Tajlandia) | Nie | Nie | Nie


## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zamiany tekstu na mowę interfejsu API REST obsługuje tych głosów, z których każdy obsługuje określonego języka i dialektu, identyfikowane za pomocą ustawień regionalnych.

> [!IMPORTANT]
> Ceny różnią się dla standardowych, niestandardowych i neuronowych głosów. Odwiedź stronę [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) strony, aby uzyskać dodatkowe informacje.

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych zamiany tekstu na mowę to nowy rodzaj synteza mowy obsługiwane przez głębokich sieciach neuronowych. Podczas używania neuronowych głosowej, syntezatora mowy jest prawie nie do odróżnienia od ludzi nagrania.

Neuronowych głosów można wprowadzić bardziej naturalne interakcje z czatbotów i asystentów wirtualnego i angażujące konwertować teksty cyfrowych, takich jak książki elektroniczne audiobooks i poprawić funkcjonalność z systemów nawigacji w samochodzie. Przypominającej ludzką prosody naturalnych i wyczyść ustaleniu słów głosy neuronowych znaczące zmęczenie nasłuchiwania podczas interakcji z systemami sztucznej Inteligencji.

Aby uzyskać pełną listę neuronowych głosów i dostępności regionalnej zobacz [regionów](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Mapowanie nazwy wszystkich usług | Głos krótkie nazwy
--------|----------|--------|---------|------------
de-DE. | Niemiecki (Niemcy) | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, GuyNeural)" | "en US-GuyNeural"
en-US | English (US) | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, JessaNeural)" | "en US-JessaNeural"
IT-IT | Włoski (Włochy) | Kobieta |"Microsoft Server mowy Text na głos mowy (it-IT, ElsaNeural)" | "it-IT ElsaNeural"
zh-CN | Chiński (kontynent) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Umożliwia mapowanie nazwy wszystkich usług lub nazwy krótkie głosu w żądaniach synteza mowy.

### <a name="standard-voices"></a>Standardowa głosów

Ponad 75 głosów standardowa są dostępne w ponad 45 języków i ustawień regionalnych, które umożliwiają konwertowanie tekstu syntezatora mowy. Aby uzyskać więcej informacji o dostępności regionalnej, zobacz [regionów](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Mapowanie nazwy wszystkich usług | Głos krótkie nazwy
-------|----------|---------|----------|----------
ar EG\* | Arabski (Egipt) | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)" | "ar np-Hoda"
ar-SA | Arabski (Arabia Saudyjska) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ar-SA Naayf)" | "ar-SA-Naayf"
bg-BG | Bułgarski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (bg-BG Ivanowi)" | "bg-BG-Ivan"
ES urzędu certyfikacji | Kataloński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Kanada ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | Czeski | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)" | "cs-CZ-Jakub"
Akcelerator deweloperski w wersji DK | Duński | Kobieta | "Microsoft Server mowy Text na głos mowy (da-DK HelleRUS)" | "da-DK-HelleRUS"
de-AT | Niemiecki (Austria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-AT, Michael)" | "de-AT-Michael"
de-CH | Niemiecki (Szwajcaria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-CH, Karsten)" | "de-CH Karsten"
de-DE. | Niemiecki (Niemcy) | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, Hedda)" | "de-DE-Hedda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
el-GR | Grecki | Mężczyzna | "Microsoft Server mowy Text na głos mowy (el-GR Stefanos)" | "el-GR-Stefanos"
EN-AU | Angielski (Australia) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU Catherine)" | "en-AU Catherine"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU HayleyRUS)" | "en-AU HayleyRUS"
EN-CA | Angielski (Kanada) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA Anna)" | "en-CA-Anna"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB HazelRUS)" | "en-GB-HazelRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-GB George, Apollo)" | "en-GB-George-Apollo"
EN-IE | Angielski (Irlandia) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IE, Sean)" | "en-IE Stefan"
EN-IN | English (India) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN PriyaRUS)" | "en-IN-PriyaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | English (US) | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, JessaRUS)" | "en-US-JessaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, BenjaminRUS)" | "en US-BenjaminRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, Guy24kRUS)" | "en US-Guy24kRUS"
es-ES | Hiszpański (Hiszpania) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, urządzenia Pablo, Apollo)" | "es-ES-urządzenia Pablo-Apollo"
es-MX | Hiszpański (Meksyk) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Fiński | Kobieta | "Microsoft Server mowy Text na głos mowy (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-CA | Francuski (Kanada) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA Caroline)" | "fr-CA-Caroline"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA HarmonieRUS)" | "fr-CA-HarmonieRUS"
FR-CH | Francuski (Szwajcaria)| Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CH, Guillaume)" | "fr-CH Guillaume"
fr-FR | Francuski (Francja)| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
HE-IL| Hebrajski (Izrael) | Mężczyzna| "Microsoft Server mowy Text na głos mowy (he-IL Asaf)" | "he-IL Asaf"
w | Hindi (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | |Kobieta | "Microsoft Server mowy Text na głos mowy (cześć IN, Kalpana)" | "cześć IN-Kalpana"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (cześć IN, Hemant)" | "cześć IN-Hemant"
hr-HR | Chorwacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (hr-HR Matej)" | "hr-HR-Matej"
hu-HU | Węgierski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
id-ID | Indonezyjski| Mężczyzna | "Microsoft Server mowy Text na głos mowy (identyfikator ID, Andika)" | "identyfikator — identyfikator Andika"
IT-IT | Włoski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (it-IT, Cosimo, Apollo)" | "it-IT — Cosimo-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (it-IT, LuciaRUS)" | "it-IT-LuciaRUS"
ja-JP | Japoński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ayumi, Apollo)" | "ja-JP — Ayumi-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ichiro, Apollo)" | "ja-JP — Ichiro-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Koreański | Kobieta | "Microsoft Server mowy Text na głos mowy (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
ms-MY | Malajski | Mężczyzna | "Głosu zamiany tekstu na mowę mowy serwera firmy Microsoft (ms Moje, Rizwan)" | "ms-MY-Rizwan"
nb-NO | Norweski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (nb — bez HuldaRUS)" | "nb-NO-HuldaRUS"
NL-NL | Holenderski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Polski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Portugalski (Brazylia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, HeloisaRUS)" | "pt-BR HeloisaRUS"
| | | Mężczyzna |"Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, Daniel, Apollo)" | "pt-BR — Daniel-Apollo"
pt-PT | Portugalski (Portugalia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
RO RO | Rumuński | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ro-RO Andrei)" | "ro-RO Andrei"
ru-RU |Rosyjski| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Apollo Pavel, ru-RU)" | "ru-RU-Pavel-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk-SK | Słowacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sk-SK Filip)" | "sk-SK-Filip"
sl SI | Słoweński | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sl-SI Lado)" | "sl-SI Lado"
sv-SE | Szwedzki | Kobieta | "Microsoft Server mowy Text na głos mowy (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
Ta w | Tamilski (Indie) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ta-IN, Valluvar)" | "ta-IN-Valluvar"
Twórz w | Telugu (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (t IN Chitra)" | "test IN-Chitra"
th TH | Tajlandzki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (th-TH Pattara)" | "th-TH Pattara"
tr-TR | Turecki | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Wietnamski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (vi-VN)" | "ruch wiadomość vi VN"
zh-CN | Chiński (kontynent) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | Chiński (SRA Hongkong) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK Tracy, Apollo)" | "zh-HK — Tracy-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK TracyRUS)" | "zh-HK-TracyRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Chiński (Tajwan) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar np obsługuje nowoczesnych standardowa arabski (MSA).*

> [!NOTE]
> Umożliwia mapowanie nazwy wszystkich usług lub nazwy krótkie głosu w żądaniach synteza mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głos jest dostępna dla de-DE, en-GB, en-IN, en US, es-MX, fr-FR, it-IT, pt-BR i zh-CN. Wybierz odpowiednie ustawień regionalnych, które dopasowuje dane szkoleniowe, masz do uczenia modelu niestandardowego głosu. Na przykład jeśli rejestrowanie danych, do których masz jest używany w języku angielskim z motywem British, wybierz en-GB.  

> [!NOTE]
> Nie obsługujemy szkoleń modelowych bi języków w niestandardowego połączenia głosowe, oprócz języka angielskiego chiński bi języków. Wybierz opcję "Angielski chiński dwujęzyczny", to w opracowywaniu chińskich głosowych, które potrafią mówić także język angielski. Szkolenie głosu we wszystkich ustawieniach regionalnych rozpoczyna się od zestawu danych z ponad 2000 wypowiedzi, z wyjątkiem en US i zh-CN, w którym można uruchomić z dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

**Tłumaczenia mowy** API obsługę innych języków tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst. Język źródłowy zawsze musi być z tabeli języka mowy na tekst. Dostępne języki docelowej zależą od tego, czy element docelowy tłumaczenia jest mowy lub tekstu. Może być tłumaczenie mowy przychodzących do więcej niż [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór tych języków są dostępne dla [synteza mowy](language-support.md#text-languages).

### <a name="text-languages"></a>Tekstu/wszystkie języki

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


## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
