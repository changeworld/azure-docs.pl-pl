---
title: Obsługa języka — usługa mowy
titleSuffix: Azure Cognitive Services
description: Usługa mowy obsługuje wiele języków w przypadku konwersji mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy. Ten artykuł zawiera kompleksową listę obsługi języków według funkcji usługi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 13d12bfbf64af9e19b66e63c88c9d7e0534571f3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378985"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Obsługa języka i regionu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania zapisu audio + z etykietami ludzkimi lub powiązanego tekstu: zdania. Dostosowanie wymowy jest obecnie dostępne tylko dla `en-US` i `de-DE`. Więcej informacji na temat dostosowywania [znajdziesz tutaj](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Ustawienia regionalne | Język | Obsługiwane | Dostosowania
------|------------|-----------|-------------
`ar-AE` | Arabski (Zjednoczone Emiraty Arabskie) | Yes | Nie
`ar-BH` | Arabski (Bahrajn), nowoczesny Standard | Yes | Model języka
`ar-EG` | Arabski (Egipt) | Yes | Model języka
`ar-KW` | Arabski (Kuwejt) | Yes | Nie
`ar-QA` | Arabski (katar) | Yes | Nie
`ar-SA` | Arabski (Arabia Saudyjska) | Yes | Nie
`ca-ES` | Kataloński | Yes | Model języka
`da-DK` | Duński (Dania) | Yes | Model języka
`de-DE` | Niemiecki (Niemcy) | Yes | Model akustyczny<br>Model języka<br>Wymowa
`en-AU` | Angielski (Australia) | Yes | Model akustyczny<br>Model języka
`en-CA` | Angielski (Kanada) | Yes | Model akustyczny<br>Model języka
`en-GB` | Angielski (Zjednoczone Królestwo) | Yes | Model akustyczny<br>Model języka<br>Wymowa
`en-IN` | English (India) | Yes | Model akustyczny<br>Model języka
`en-NZ` | Angielski (Nowa Zelandia) | Yes | Model akustyczny<br>Model języka
`en-US` | Polski (Polska) | Yes | Model akustyczny<br>Model języka<br>Wymowa
`es-ES` | Hiszpański (Hiszpania) | Yes | Model akustyczny<br>Model języka
`es-MX` | Hiszpański (Meksyk) | Yes | Model akustyczny<br>Model języka
`fi-FI` | Fiński (Finlandia) | Yes | Model języka
`fr-CA` | Francuski (Kanada) | Yes | Model akustyczny<br>Model języka
`fr-FR` | Francuski (Francja) | Yes | Model akustyczny<br>Model języka<br>Wymowa
`gu-IN` | Gudżarati (Indyjski) | Yes | Model języka
`hi-IN` | Hindi (Indie) | Yes | Model akustyczny<br>Model języka
`it-IT` | Włoski (Włochy) | Yes | Model akustyczny<br>Model języka<br>Wymowa
`ja-JP` | Japoński (Japonia) | Yes | Model języka
`ko-KR` | Koreański (Korea) | Yes | Model języka
`mr-IN` | Marathi (Indie) | Yes | Model języka
`nb-NO` | Norweski (Bokmal) (Norwegia) | Yes | Model języka
`nl-NL` | Holenderski (Holandia) | Yes | Model języka
`pl-PL` | Polski (Polska) | Yes | Model języka
`pt-BR` | Portugalski (Brazylia) | Yes | Model akustyczny<br>Model języka<br>Wymowa
`pt-PT` | Portugalski (Portugalia) | Yes | Model języka
`ru-RU` | Rosyjski (Rosja) | Yes | Model akustyczny<br>Model języka
`sv-SE` | Szwedzki (Szwecja) | Yes | Model języka
`ta-IN` | Tamilski (Indie) | Yes | Model języka
`te-IN` | Telugu (Indie) | Yes | Nie
`th-TH` | Tajski (Tajlandia) | Yes | Nie
`tr-TR` | Turecki (Turcja) | Yes | Nie
`zh-CN` | Chiński (mandaryński uproszczony) | Yes | Model akustyczny<br>Model języka
`zh-HK` | Chiński (kantoński, tradycyjny) | Yes | Model języka
`zh-TW` | Chiński (mandaryński tajwańskie) | Yes | Model języka

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zestawy Microsoft Speech SDK i interfejsy API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne.

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe informacje, odwiedź stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa głosu
--------|----------|--------|---------|------------
`de-DE` | Niemiecki (Niemcy) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | {1&gt;Polski (PL)&lt;1} | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, JessaNeural)" | "pl-US-JessaNeural"
`en-US` | {1&gt;Polski (PL)&lt;1} | Mężczyzna | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, GuyNeural)" | "pl-US-GuyNeural"
`it-IT` | Włoski (Włochy) | Kobieta |"Microsoft Server Speech zamiana tekstu na mowę Voice (ElsaNeural)" | "IT-ElsaNeural"
`pt-BR` | Portugalski (Brazylia) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural"
`zh-CN` | Chiński (kontynent) | Kobieta | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa
-------|----------|---------|----------|----------
<sup>1</sup>`ar-EG` | Arabski (Egipt) | Kobieta | "Microsoft Server mowy tekstu na głos mowy (ar np Hoda)" | "AR-EG-Hoda"
`ar-SA` | Arabski (Arabia Saudyjska) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ar-SA Naayf)" | "ar-SA-Naayf"
`bg-BG` | Bułgarski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (bg-BG Ivanowi)" | "BG-BG-Ivan"
`ca-ES` | Kataloński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Kanada ES, HerenaRUS)" | "CA-ES-HerenaRUS"
`cs-CZ` | Czeski | Mężczyzna | "Microsoft Server mowy tekstu na głos mowy (cs-CZ, Jakub)" | "CS-CZ-Jakub"
`da-DK` | Duński | Kobieta | "Microsoft Server mowy Text na głos mowy (da-DK HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Niemiecki (Austria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-AT, Michael)" | "de-AT-Michael"
`de-CH` | Niemiecki (Szwajcaria) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | Niemiecki (Niemcy) | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, Hedda)" | "de-DE-Hedda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | Grecki | Mężczyzna | "Microsoft Server mowy Text na głos mowy (el-GR Stefanos)" | "El-GR-Stefanos"
`en-AU` | Angielski (Australia) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU Catherine)" | "en-AU-Catherine"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-AU HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Angielski (Kanada) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA Anna)" | "en-CA-Linda"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-CA HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | Angielski (Zjednoczone Królestwo) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB Susan, Apollo)" | "pl-GB-Susan-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-GB HazelRUS)" | "pl-GB-HazelRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-GB George, Apollo)" | "pl-GB-George-Apollo"
`en-IE` | Angielski (Irlandia) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IE, Sean)" | "EN-IE-Janusz"
`en-IN` | English (India) | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN, Heera, Apollo)" | "pl-IN-Heera-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en-IN PriyaRUS)" | "pl-IN-PriyaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en-IN, Ravi, Apollo)" | "pl-IN-Ravi-Apollo"
`en-US` | {1&gt;Polski (PL)&lt;1} | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, ZiraRUS)" | "pl-US-ZiraRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, JessaRUS)" | "pl-US-JessaRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, BenjaminRUS)" | "pl-US-BenjaminRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (en US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (en US, Guy24kRUS)" | "pl-US-Guy24kRUS"
`es-ES` | Hiszpański (Hiszpania) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (es-ES, urządzenia Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | Hiszpański (Meksyk) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | Fiński | Kobieta | "Microsoft Server mowy Text na głos mowy (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Francuski (Kanada) |Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA Caroline)" | "fr-CA-Caroline"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CA HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | Francuski (Szwajcaria)| Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francuski (Francja)| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | Hebrajski (Izrael) | Mężczyzna| "Microsoft Server mowy Text na głos mowy (he-IL Asaf)" | "IT-IL-Asaf"
`hi-IN` | Hindi (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (cześć IN, Kalpana)" | "Witaj w Kalpana"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (cześć IN, Hemant)" | "Witaj w Hemant"
`hr-HR` | Chorwacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (hr-HR Matej)" | "HR-HR-Matej"
`hu-HU` | Węgierski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | indonezyjski| Mężczyzna | "Microsoft Server mowy Text na głos mowy (identyfikator ID, Andika)" | "ID-ID-andika"
`it-IT` | Włoski | Mężczyzna | "Microsoft Server mowy Text na głos mowy (it-IT, Cosimo, Apollo)" | "IT-Cosimo-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (it-IT, LuciaRUS)" | "IT-LuciaRUS"
`ja-JP` | Japoński | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Koreański | Kobieta | "Microsoft Server mowy Text na głos mowy (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | malajski | Mężczyzna | "Głosu zamiany tekstu na mowę mowy serwera firmy Microsoft (ms Moje, Rizwan)" | "MS-MY-Rizwan"
`nb-NO` | Norweski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosu (nb — bez HuldaRUS)" | "nb-NO-HuldaRUS"
`nl-NL` | holenderski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | Polski | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Portugalski (Brazylia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Mężczyzna |"Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
`pt-PT` | Portugalski (Portugalia) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
`ro-RO` | rumuński | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ro-RO Andrei)" | "RO-RO-Andrei"
`ru-RU` |Rosyjski| Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (Apollo Pavel, ru-RU)" | "ru-RU-Pavel-Apollo"
| | | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (ru-RU, EkaterinaRUS)" | ru — RU — EkaterinaRUS
`sk-SK` | Słowacki | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sk-SK Filip)" | "sk-SK-Filip"
`sl-SI` | Słoweński | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (sl-SI Lado)" | "SL-SI-Lado"
`sv-SE` | Szwedzki | Kobieta | "Microsoft Server mowy Text na głos mowy (sv-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
`ta-IN` | Tamilski (Indie) | Mężczyzna | "Microsoft Server mowy Text na głos mowy (ta-IN, Valluvar)" | "Ta-IN-Valluvar"
`te-IN` | Telugu (Indie) | Kobieta | "Microsoft Server mowy Text na głos mowy (t IN Chitra)" | "te w Chitra"
`th-TH` | Tajski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosowych (th-TH Pattara)" | "th-TH-Pattara"
`tr-TR` | Turecki (Turcja) | Kobieta | "Microsoft Server mowy zamiany tekstu na mowę głosowych (tr-TR, SedaRUS)" | "TR-TR-SedaRUS"
`vi-VN` | wietnamski | Mężczyzna | "Microsoft Server mowy zamiany tekstu na mowę głosu (vi-VN)" | "VI-VN-an"
`zh-CN` | Chiński (kontynent) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Chiński (SRA Hongkong) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-HK TracyRUS)" | "zh-HK-TracyRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | Chiński (Tajwan) | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Kobieta | "Microsoft Server mowy Text na głos mowy (zh-TW HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Mężczyzna | "Microsoft Server mowy Text na głos mowy (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

**1** *AR-EG obsługuje nowoczesne standardowe arabski (MSA).*

> [!NOTE]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`i `zh-CN`. Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, wybierz pozycję `en-GB`.

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem języka chińskiego w języku angielskim. Wybierz pozycję "dwujęzyczne w języku chińskim English", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich ustawieniach regionalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem `en-US` i `zh-CN`, gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

Interfejs API **tłumaczenia mowy** obsługuje różne języki dla tłumaczenia mowy na mowę i zamiany mowy na tekst. Język źródłowy musi być zawsze z tabeli języka zamiany mowy na tekst. Dostępne języki docelowej zależą od tego, czy element docelowy tłumaczenia jest mowy lub tekstu. Możesz przetłumaczyć przychodzącą mowę na więcej niż [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór języków jest dostępny dla [syntezy mowy](language-support.md#text-languages).

### <a name="text-languages"></a>Tekstu/wszystkie języki

| Język tekstu    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| arabski       | `ar`          |
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
| holenderski      | `nl`          |
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
| indonezyjski      | `id`          |
| Irlandzki      | `ga`          |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Kannada      | `kn`          |
| Suahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malgaski      | `mg`          |
| malajski      | `ms`          |
| Malajalam      | `ml`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski (Brazylia)      | `pt-br`          |
| Portugalski (Portugalia)      | `pt-pt`          |
| Pendżabski      | `pa`          |
| Queretaro Otomi      | `otq`          |
| rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| (Samoa)      | `sm`          |
| Serbski (Cyrylica)      | `sr-Cyrl`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tahitian      | `ty`          |
| Tamilski      | `ta`          |
| Telugu      | `te`          |
| Tajski      | `th`          |
| Pa'anga      | `to`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| urdu      | `ur`          |
| wietnamski      | `vi`          |
| Walijski      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Service](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowęC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
