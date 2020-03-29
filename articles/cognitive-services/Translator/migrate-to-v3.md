---
title: Migracja do wersji 3 — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera kroki ułatwiające migrację z wersji 2 do wersji 3 interfejsu API tekstowego usługi Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837307"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Migracja interfejsu API tekstu tłumacza od V2 do V3

> [!NOTE]
> Wersja V2 została wycofana 30 kwietnia 2018 roku. Zmigruj aplikacje do wersji 3, aby korzystać z nowych funkcji dostępnych wyłącznie w wersji V3.
> 
> Centrum Microsoft Translator zostanie wycofane 17 maja 2019 r. [Wyświetlanie ważnych informacji o migracji i dat](https://www.microsoft.com/translator/business/hub/).  

Zespół usługi Microsoft Translator wydał wersję 3 (V3) interfejsu API tekstu translatora. Ta wersja zawiera nowe funkcje, przestarzałe metody i nowy format wysyłania i odbierania danych z usługi Microsoft Translator. Ten dokument zawiera informacje dotyczące zmiany aplikacji do korzystania z V3. 

Koniec tego dokumentu zawiera przydatne łącza, aby dowiedzieć się więcej.

## <a name="summary-of-features"></a>Podsumowanie funkcji

* Brak śledzenia — w wersji 3 No-Trace dotyczy wszystkich warstw cenowych w witrynie Azure portal. Ta funkcja oznacza, że żaden tekst przesłany do interfejsu API V3 nie zostanie zapisany przez firmę Microsoft.
* JSON - XML jest zastępowany przez JSON. Wszystkie dane wysyłane do usługi i odbierane z usługi są w formacie JSON.
* Wiele języków docelowych w jednym żądaniu — metoda Translate akceptuje wiele języków "do" do tłumaczenia w jednym żądaniu. Na przykład pojedyncze żądanie może być "od" angielski i "do" niemiecki, hiszpański i japoński, lub dowolna inna grupa języków.
* Słownik dwujęzyczny — do interfejsu API dodano metodę słownika dwujęzycznego. Ta metoda obejmuje "wyszukiwanie" i "przykłady".
* Transliterate — metoda transliteratu została dodana do interfejsu API. Ta metoda spowoduje konwersję słów i zdań w jednym skrypcie (np. arabski) do innego skryptu (np. łaciński).
* Języki — nowa metoda "języków" dostarcza informacji o języku w formacie JSON do użycia z metodami "tłumacz" , "słownik" i "transliteratyczny".
* Nowość w tłumaczeniu — nowe funkcje zostały dodane do metody "translate" do obsługi niektórych funkcji, które były w interfejsie API V2 jako oddzielne metody. Przykładem jest TranslateArray.
* Metoda Speak - Funkcja zamiany tekstu na mowę nie jest już obsługiwana w interfejsie API usługi Microsoft Translator. Funkcja zamiany tekstu na mowę jest dostępna w [usłudze Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Poniższa lista metod V2 i V3 identyfikuje metody V3 i interfejsy API, które zapewnią funkcje, które zostały z v2.

| Metoda interfejsu API w wersji 2   | Zgodność interfejsu API w wersji 3 |
|:----------- |:-------------|
| `Translate`     | [Przetłumacz](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Przetłumacz](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Języki](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Języki](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Usługa mowy firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Usługa mowy firmy Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Wykrywanie](reference/v3-0-detect.md)         |
| `DetectArray`     | [Wykrywanie](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funkcja nie jest już obsługiwana       |
| `AddTranslationArray`    | Funkcja nie jest już obsługiwana          |
| `BreakSentences`      | [Przełamywstwo](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funkcja nie jest już obsługiwana         |
| `GetTranslationsArray`      | Funkcja nie jest już obsługiwana         |

## <a name="move-to-json-format"></a>Przejście do formatu JSON

Microsoft Translator tekstu V2 akceptowane i zwracane dane w formacie XML. W wersji 3 wszystkie dane wysyłane i odbierane za pomocą interfejsu API są w formacie JSON. XML nie będzie już akceptowany ani zwracany w wersji 3.

Ta zmiana będzie miała wpływ na kilka aspektów aplikacji napisanej dla interfejsu API tłumaczenia tekstu V2. Jako przykład: Interfejs API języków zwraca informacje o języku do tłumaczenia tekstu, transliteracji i dwóch metod słownika. Możesz poprosić o wszystkie informacje językowe dla wszystkich metod w jednym wywołaniu lub poprosić o nie indywidualnie.

Metoda języków nie wymaga uwierzytelniania; klikając na poniższy link, możesz zobaczyć wszystkie informacje językowe dla V3 w JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,słownik,transliteracja](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Klucz uwierzytelniania

Klucz uwierzytelniania, którego używasz dla wersji 2, zostanie zaakceptowany dla wersji 3. Nie musisz otrzymywać nowej subskrypcji. Będzie można mieszać v2 i V3 w aplikacjach w okresie migracji przez cały rok, co ułatwia wydawanie nowych wersji podczas migracji z V2-XML do V3-JSON.

## <a name="pricing-model"></a>Model cenowy

Microsoft Translator V3 jest wyceniany w taki sam sposób, w jaki V2 został wyceniony; na znak, w tym spacje. Nowe funkcje w wersji 3 wprowadzać pewne zmiany w tym, jakie znaki są liczone do rozliczeń.

| Metoda V3   | Znaki liczone do rozliczeń |
|:----------- |:-------------|
| `Languages`     | Nie przesłano żadnych znaków, nie liczyno, bez opłat.          |
| `Translate`     | Liczba zależy od liczby znaków przesłanych do tłumaczenia i liczby języków, na które znaki są tłumaczone. 50 nadesłanych znaków i 5 żądanych języków będzie 50x5.           |
| `Transliterate`     | Liczba znaków przesłanych do transliteracji jest zliczona.         |
| `Dictionary lookup & example`     | Liczba znaków przesłanych do wyszukiwania słownika i przykłady są liczone.         |
| `BreakSentence`     | Bez opłat.       |
| `Detect`     | Bez opłat.      |

## <a name="v3-end-points"></a>Punkty końcowe V3

Globalny

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metody tłumaczenia tekstu interfejsu API w wersji V3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatybilność i dostosowywanie

> [!NOTE]
> 
> Centrum Microsoft Translator zostanie wycofane 17 maja 2019 r. [Wyświetlanie ważnych informacji o migracji i dat](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 domyślnie używa neuronowego tłumaczenia maszynowego. W związku z tym nie można go używać z Centrum Microsoft Translator. Translator Hub obsługuje tylko starsze statystyczne tłumaczenie maszynowe. Dostosowanie do tłumaczenia neuronowego jest teraz dostępne przy użyciu translatora niestandardowego. [Dowiedz się więcej o dostosowywaniu tłumaczenia maszyn neuronowych](custom-translator/overview.md)

Tłumaczenie neuronowe za pomocą tekstowego API V3 nie obsługuje stosowania standardowych kategorii (SMT, speech, tech, generalnn).

| |Endpoint|    Zgodność z rodo procesora|  Korzystanie z Centrum translatora| Korzystanie z translatora niestandardowego (wersja zapoznawcza)|
|:-----|:-----|:-----|:-----|:-----|
|Interfejs API tekstu tłumacza w wersji 2| api.microsofttranslator.com|    Nie  |Tak    |Nie|
|Interfejs API tekstu tłumacza w wersji 3| api.cognitive.microsofttranslator.com|  Tak|    Nie| Tak|

**Interfejs API tekstu tłumacza w wersji 3**
* Jest ogólnie dostępna i w pełni obsługiwana.
* Czy RODO jest zgodne jako procesor i spełnia wszystkie wymagania certyfikacji ISO 20001 i 20018, a także wymagania certyfikacji SOC 3. 
* Umożliwia wywołanie systemów tłumaczenia sieci neuronowych dostosowanych za pomocą niestandardowego translatora (wersja zapoznawcza), nowej funkcji dostosowywania translatora NMT. 
* Nie zapewnia dostępu do niestandardowych systemów tłumaczeniowych utworzonych za pomocą Centrum Microsoft Translator.

Używasz wersji 3 interfejsu API tekstu tłumacza, jeśli używasz api.cognitive.microsofttranslator.com punktu końcowego.

**Interfejs API tekstu tłumacza w wersji 2**
* Nie spełnia wszystkich wymagań certyfikacji ISO 20001,20018 i SOC 3. 
* Nie zezwala na wywoływanie systemów tłumaczenia sieci neuronowych dostosowanych za pomocą funkcji dostosowywania translatora.
* Zapewnia dostęp do niestandardowych systemów tłumaczeniowych utworzonych za pomocą Centrum Microsoft Translator.
* Używasz wersji 2 interfejsu API tekstu tłumacza, jeśli używasz api.microsofttranslator.com punktu końcowego.

Żadna wersja interfejsu API translatora nie tworzy rekordu twoich tłumaczeń. Twoje tłumaczenia nigdy nie są nikomu udostępniane. Więcej informacji na stronie internetowej [Translator No-Trace.](https://www.aka.ms/NoTrace)

## <a name="links"></a>Linki

* [Zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informacje prawne platformy Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Warunki korzystania z usług online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyświetl dokumentację wersji 3.0](reference/v3-0-reference.md)
