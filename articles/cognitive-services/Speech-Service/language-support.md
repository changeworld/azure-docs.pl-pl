---
title: Obsługa języka — usługa mowy
titleSuffix: Azure Cognitive Services
description: Usługa mowy obsługuje wiele języków w przypadku konwersji mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy. Ten artykuł zawiera kompleksową listę obsługi języków według funkcji usługi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: af8bb24862c05b232b7bb5d831b1eb3b1add3a7f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468810"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Obsługa języka i regionu dla usług mowy

Różne języki są obsługiwane dla różnych funkcji usługi Speech Services. W poniższej tabeli zestawiono obsługę języka.

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania zapisu audio + z etykietami ludzkimi lub powiązanego tekstu: zdania.  Dostosowanie wymowy jest obecnie dostępne tylko dla en-US i de-DE. Więcej informacji na temat dostosowywania [znajdziesz tutaj](how-to-custom-speech.md).

  Ustawienia regionalne | Język | Obsługiwane | Modyfikowalne
 ------|----------|---------------------|---------------------
 AR-EG | Arabski (Egipt), nowoczesny Standard | Tak | Tak
 ar-SA | Arabski (Arabia Saudyjska) | Tak | Tak
 AR — AE | Arabski (Zjednoczone Emiraty Arabskie) | Tak | Tak
 AR – KW | Arabski (Kuwejt) | Tak | Tak
 AR-pytań i odpowiedzi | Arabski (katar) | Tak | Tak
 CA-ES | Kataloński | Tak | Nie
 da-DK | Duński (Dania) | Tak | Nie
 de-DE | Niemiecki (Niemcy) | Tak | Tak
 en-AU | Angielski (Australia) | Tak | Tak
 EN-CA | Angielski (Kanada) | Tak | Tak
 pl GB | Angielski (Zjednoczone Królestwo) | Tak | Tak
 dodatek EN-IN | English (India) | Tak | Tak
 EN-NZ | Angielski (Nowa Zelandia) | Tak | Tak
 pl-US | Angielski (Stany Zjednoczone) | Tak | Tak
 es-ES | Hiszpański (Hiszpania) | Tak | Tak
 es — MX | Hiszpański (Meksyk) | Tak | Tak
 fi-FI | Fiński (Finlandia) | Tak | Nie
 fr — CA | Francuski (Kanada) | Tak | Tak
 fr — FR | Francuski (Francja) | Tak | Tak
 gu | Gudżarati (Indyjski) | Tak | Tak
 Witaj w usłudze | Hindi (Indie) | Tak | Tak
 IT | Włoski (Włochy) | Tak | Tak
 ja-JP | Japoński (Japonia) | Tak | Tak
 Ko — KR | Koreański (Korea) | Tak | Tak
 Mr | Marathi (Indie) | Tak | Tak
 NB — nie | Norweski (bokmål) (Norwegia) | Tak | Nie
 NL-NL | Holenderski (Holandia) | Tak | Tak
 pl-PL | Polski (Polska) | Tak | Nie
 pt-BR | Portugalski (Brazylia) | Tak | Tak
 pt-PT | Portugalski (Portugalia) | Tak | Tak
 ru — RU | Rosyjski (Rosja) | Tak | Tak
 SV — SE | Szwedzki (Szwecja) | Tak | Nie
 Ta — w | Tamilski (Indie) | Tak | Tak
 te — IN | Telugu (Indie) | Tak | Tak
 zh-CN | Chiński (mandarynki, uproszczony) | Tak | Tak
 zh-HK | Chiński (kantoński, tradycyjny) | Tak | Tak
 zh-TW | Chiński (tajwański mandarynki) | Tak | Tak
 th-TH | Tajski (Tajlandia) | Tak | Nie
 TR-TR | Turcja | Tak | Tak |


## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zestaw Microsoft Speech SDK i interfejs API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne.

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe informacje, odwiedź stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby zapoznać się z pełną listą głosów neuronowych i regionalnej dostępności, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa głosu
--------|----------|--------|---------|------------
de-DE | Niemiecki (Niemcy) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
pl-US | English (US) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, GuyNeural)" | "pl-US-GuyNeural"
pl-US | English (US) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, JessaNeural)" | "pl-US-JessaNeural"
IT | Włoski (Włochy) | Kobieta |"Microsoft Server Speech zamiana tekstu na mowę Voice (ElsaNeural)" | "IT-ElsaNeural"
zh-CN | Chiński (kontynent) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa głosu
-------|----------|---------|----------|----------
AR-EG\* | Arabski (Egipt) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (AR-EG, Hoda)" | "AR-EG-Hoda"
ar-SA | Arabski (Arabia Saudyjska) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
BG — BG | Bułgarski | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (BG-BG, Ivan)" | "BG-BG-Ivan"
CA-ES | Kataloński (Hiszpania) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS"
CS — CZ | Czeski | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (CS-CZ, Jakub)" | "CS-CZ-Jakub"
da-DK | Duński | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
de-AT | Niemiecki (Austria) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-AT, Michael)" | "de-AT-Michael"
Usuń CH | Niemiecki (Szwajcaria) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-CH, Karsten)" | "de-CH-Karsten"
de-DE | Niemiecki (Niemcy) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
El-GR | Grecki | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (El-GR, Stefanos)" | "El-GR-Stefanos"
en-AU | Angielski (Australia) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-AU, Catherine)" | "en-AU-Catherine"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
EN-CA | Angielski (Kanada) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-CA, Linda)" | "en-CA-Linda"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
pl GB | English (UK) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-GB, Susan, Apollo)" | "pl-GB-Susan-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-GB, HazelRUS)" | "pl-GB-HazelRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-GB, George, Apollo)" | "pl-GB-George-Apollo"
EN-IE | Angielski (Irlandia) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IE, Janusz)" | "EN-IE-Janusz"
dodatek EN-IN | English (India) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IN, Heera, Apollo)" | "pl-IN-Heera-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IN, PriyaRUS)" | "pl-IN-PriyaRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IN, Ravi, Apollo)" | "pl-IN-Ravi-Apollo"
pl-US | English (US) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, ZiraRUS)" | "pl-US-ZiraRUS"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, JessaRUS)" | "pl-US-JessaRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, BenjaminRUS)" | "pl-US-BenjaminRUS"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, Jessa24kRUS)" | "pl-US-Jessa24kRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, Guy24kRUS)" | "pl-US-Guy24kRUS"
es-ES | Hiszpański (Hiszpania) |Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es — MX | Hiszpański (Meksyk) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Fiński | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr — CA | Francuski (Kanada) |Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
fr-CH | Francuski (Szwajcaria)| Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
fr — FR | Francuski (Francja)| Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
-IL| Hebrajski (Izrael) | Mężczyzna| "Microsoft Server Speech zamiana tekstu na mowę Voice (IT-IL, Asaf)" | "IT-IL-Asaf"
Witaj w usłudze | Hindi (Indie) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (Hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | |Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (Hi-IN, Kalpana)" | "Witaj w Kalpana"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (Hi-IN, Hemant)" | "Witaj w Hemant"
HR-HR | Chorwacki | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (HR-HR, Matej)" | "HR-HR-Matej"
HU — Węgry | Węgierski | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
Identyfikator ID | Indonezyjski| Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (ID-ID, andika)" | "ID-ID-andika"
IT | Włoski | Mężczyzna | "Zamiana tekstu na mowę głosu na mowę serwera firmy Microsoft (Cosimo, Apollo)" | "IT-Cosimo-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (LuciaRUS)" | "IT-LuciaRUS"
ja-JP | Japoński | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
Ko — KR | Koreański | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
MS — MY | Malajski | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (MS-MY, Rizwan)" | "MS-MY-Rizwan"
NB — nie | Norweski | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS"
NL-NL | Holenderski | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (NL-NL, HannaRUS)" | "NL-NL-HannaRUS"
pl-PL | Polski | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Portugalski (Brazylia) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Mężczyzna |"Microsoft Server Speech zamiana tekstu na mowę Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | Portugalski (Portugalia) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
RO-RO | Rumuński | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (RO-RO, Andrei)" | "RO-RO-Andrei"
ru — RU |Rosyjski| Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (ru-RU, EkaterinaRUS)" | ru — RU — EkaterinaRUS
SK-SK | Słowacki | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (SK-SK, Filip)" | "sk-SK-Filip"
SL-SI | Słoweński | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (SL-SI, Lado)" | "SL-SI-Lado"
SV — SE | Szwedzki | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (SV-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
Ta — w | Tamilski (Indie) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (ta-IN, Valluvar)" | "Ta-IN-Valluvar"
te — IN | Telugu (Indie) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (Chitra)" | "te w Chitra"
th-TH | Tajski | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (th-TH, Pattara)" | "th-TH-Pattara"
TR-TR | Turecki | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (TR-TR, SedaRUS)" | "TR-TR-SedaRUS"
VI-VN | Wietnamski | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (VI-VN, an)" | "VI-VN-an"
zh-CN | Chiński (kontynent) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, YaoYao, Apollo)" | "zh-CN-YaoYao-Apollo"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | Chiński (Hongkong SAR) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Chiński (Tajwan) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *AR-EG obsługuje nowoczesne standardowe arabski (MSA).*

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla de-DE, en-GB, pl-US, EN-pl, es-MX, fr-FR, it-IT, pt-BR i zh-CN. Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, wybierz pozycję pl-GB.  

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem języka chińskiego w języku angielskim. Wybierz opcję "alfabet Chińska w języku chińskim", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich lokalizacjach lokalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem en-US i zh-CN, gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

Interfejs API **tłumaczenia mowy** obsługuje różne języki dla tłumaczenia mowy na mowę i zamiany mowy na tekst. Język źródłowy musi być zawsze z tabeli języka zamiany mowy na tekst. Dostępne języki docelowe zależą od tego, czy celem tłumaczenia jest rozpoznawanie mowy czy tekst. Możesz przetłumaczyć przychodzącą mowę na więcej niż [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór tych języków jest dostępny dla [syntezy mowy](language-support.md#text-languages).

### <a name="text-languages"></a>Języki tekstu

| Język tekstu    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabski       | `ar`          |
| Języku      | `bn`          |
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
| Fijian      | `fj`          |
| Filipiński      | `fil`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Haitański      | `ht`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Hmong Kowalski      | `mww`          |
| Węgierski      | `hu`          |
| Indonezyjski      | `id`          |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Madagaskaru      | `mg`          |
| Malajski      | `ms`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Samoan      | `sm`          |
| Serbski (Cyrylica)      | `sr-Cyrl`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tahitian      | `ty`          |
| Tamilski      | `ta`          |
| Tajski      | `th`          |
| Tonga      | `to`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku c #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
