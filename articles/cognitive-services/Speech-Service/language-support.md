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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 031367a8a05defad475ae077f9b38b7294837460
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559390"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Obsługa języka i regionu dla usług mowy

Różne języki są obsługiwane dla różnych funkcji usługi Speech Services. W poniższej tabeli podsumowano obsługę języka.

## <a name="speech-to-text"></a>Zamiany mowy na tekst

Zarówno zestaw Microsoft Speech Recognition SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). Różne poziomy możliwości dostosowania są dostępne dla każdego języka.

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

Interfejs API REST zamiany tekstu na mowę obsługuje te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne.

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe [](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) informacje, odwiedź stronę z cennikiem.

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i wirtualnymi asystentami bardziej naturalnymi i atrakcyjnymi, konwertowanie cyfrowych tekstów, takich jak książki elektroniczne, na Audiobooks i ulepszanie systemów nawigacyjnych. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby zapoznać się z pełną listą głosów neuronowych i regionalnej dostępności, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa głosu
--------|----------|--------|---------|------------
de-DE. | Niemiecki (Niemcy) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, GuyNeural)" | "pl-US-GuyNeural"
en-US | English (US) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, JessaNeural)" | "pl-US-JessaNeural"
IT-IT | Włoski (Włochy) | Kobieta |"Microsoft Server Speech zamiana tekstu na mowę Voice (ElsaNeural)" | "IT-ElsaNeural"
zh-CN | Chiński (kontynent) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa głosu
-------|----------|---------|----------|----------
ar EG\* | Arabski (Egipt) | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)" | "AR-EG-Hoda"
ar-SA | Arabski (Arabia Saudyjska) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ar-SA Naayf)" | "ar-SA-Naayf"
bg-BG | Bułgarski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (bg-BG Ivanowi)" | "BG-BG-Ivan"
ES urzędu certyfikacji | Kataloński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Kanada ES, HerenaRUS)" | "CA-ES-HerenaRUS"
cs-CZ | Czeski | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)" | "CS-CZ-Jakub"
Akcelerator deweloperski w wersji DK | Duński | Kobieta | "Microsoft Server mowy Text na głos mowy (da-DK HelleRUS)" | "da-DK-HelleRUS"
de-AT | Niemiecki (Austria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-AT, Michael)" | "de-AT-Michael"
de-CH | Niemiecki (Szwajcaria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-CH, Karsten)" | "de-CH-Karsten"
de-DE. | Niemiecki (Niemcy) | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, Hedda)" | "de-DE-Hedda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
el-GR | Grecki | Mężczyzna | "Microsoft Server mowy Text na głos mowy (el-GR Stefanos)" | "El-GR-Stefanos"
EN-AU | Angielski (Australia) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU Catherine)" | "en-AU-Catherine"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU HayleyRUS)" | "en-AU-HayleyRUS"
EN-CA | Angielski (Kanada) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA Anna)" | "en-CA-Linda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB Susan, Apollo)" | "pl-GB-Susan-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB HazelRUS)" | "pl-GB-HazelRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-GB George, Apollo)" | "pl-GB-George-Apollo"
EN-IE | Angielski (Irlandia) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IE, Sean)" | "EN-IE-Janusz"
EN-IN | English (India) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN, Heera, Apollo)" | "pl-IN-Heera-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN PriyaRUS)" | "pl-IN-PriyaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IN, Ravi, Apollo)" | "pl-IN-Ravi-Apollo"
en-US | English (US) | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, ZiraRUS)" | "pl-US-ZiraRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, JessaRUS)" | "pl-US-JessaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, BenjaminRUS)" | "pl-US-BenjaminRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, Guy24kRUS)" | "pl-US-Guy24kRUS"
es-ES | Hiszpański (Hiszpania) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, urządzenia Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | Hiszpański (Meksyk) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | Fiński | Kobieta | "Microsoft Server mowy Text na głos mowy (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-CA | Francuski (Kanada) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA Caroline)" | "fr-CA-Caroline"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA HarmonieRUS)" | "fr-CA-HarmonieRUS"
FR-CH | Francuski (Szwajcaria)| Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CH, Guillaume)" | "fr-CH-Guillaume"
fr-FR | Francuski (Francja)| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
HE-IL| Hebrajski (Izrael) | Mężczyzna| "Microsoft Server mowy Text na głos mowy (he-IL Asaf)" | "IT-IL-Asaf"
w | Hindi (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | |Kobieta | "Microsoft Server mowy Text na głos mowy (cześć IN, Kalpana)" | "Witaj w Kalpana"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (cześć IN, Hemant)" | "Witaj w Hemant"
hr-HR | Chorwacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (hr-HR Matej)" | "HR-HR-Matej"
hu-HU | Węgierski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
id-ID | Indonezyjski| Mężczyzna | "Microsoft Server mowy Text na głos mowy (identyfikator ID, Andika)" | "ID-ID-andika"
IT-IT | Włoski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (it-IT, Cosimo, Apollo)" | "IT-Cosimo-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (it-IT, LuciaRUS)" | "IT-LuciaRUS"
ja-JP | Japoński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Koreański | Kobieta | "Microsoft Server mowy Text na głos mowy (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
ms-MY | Malajski | Mężczyzna | "Głosu zamiany tekstu na mowę mowy serwera firmy Microsoft (ms Moje, Rizwan)" | "MS-MY-Rizwan"
nb-NO | Norweski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (nb — bez HuldaRUS)" | "nb-NO-HuldaRUS"
NL-NL | Holenderski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Polski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Portugalski (Brazylia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Mężczyzna |"Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | Portugalski (Portugalia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
RO RO | Rumuński | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ro-RO Andrei)" | "RO-RO-Andrei"
ru-RU |Rosyjski| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Apollo Pavel, ru-RU)" | "ru-RU-Pavel-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, EkaterinaRUS)" | ru — RU — EkaterinaRUS
sk-SK | Słowacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sk-SK Filip)" | "sk-SK-Filip"
sl SI | Słoweński | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sl-SI Lado)" | "SL-SI-Lado"
sv-SE | Szwedzki | Kobieta | "Microsoft Server mowy Text na głos mowy (sv-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
Ta w | Tamilski (Indie) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ta-IN, Valluvar)" | "Ta-IN-Valluvar"
Twórz w | Telugu (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (t IN Chitra)" | "te w Chitra"
th TH | Tajlandzki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (th-TH Pattara)" | "th-TH-Pattara"
tr-TR | Turecki | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (tr-TR, SedaRUS)" | "TR-TR-SedaRUS"
vi-VN | Wietnamski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (vi-VN)" | "VI-VN-an"
zh-CN | Chiński (kontynent) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | Chiński (SRA Hongkong) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK TracyRUS)" | "zh-HK-TracyRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | Chiński (Tajwan) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar np obsługuje nowoczesnych standardowa arabski (MSA).*

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla de-DE, en-GB, pl-US, EN-pl, es-MX, fr-FR, it-IT, pt-BR i zh-CN. Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, wybierz pozycję pl-GB.  

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem języka chińskiego w języku angielskim. Wybierz opcję "alfabet Chińska w języku chińskim", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich lokalizacjach lokalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem en-US i zh-CN, gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

**Tłumaczenia mowy** API obsługę innych języków tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst. Język źródłowy musi być zawsze z tabeli języka zamiany mowy na tekst. Dostępne języki docelowej zależą od tego, czy element docelowy tłumaczenia jest mowy lub tekstu. Może być tłumaczenie mowy przychodzących do więcej niż [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór tych języków są dostępne dla [synteza mowy](language-support.md#text-languages).

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


## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
