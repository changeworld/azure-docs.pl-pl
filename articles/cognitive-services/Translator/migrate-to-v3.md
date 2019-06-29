---
title: Migrowanie do V3 — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację z V2 do V3 interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 8a2530a4eb4365a022ab11279b344a5d2852430b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448295"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>W usłudze Translator tekstu interfejsu API w wersji 2 do migracji V3

> [!NOTE]
> W wersji 2 została zakończona na 30 kwietnia 2018 r. Przeprowadź migrację aplikacji do wersji 3, aby można było korzystać z zalet nowych funkcji dostępnych wyłącznie w wersji 3.
> 
> W Centrum w usłudze Translator firmy Microsoft zostaną wycofane z dniem 17 maja 2019 r. [Wyświetl informacje na temat migracji ważne i dat](https://www.microsoft.com/translator/business/hub/).  

Zespół Microsoft Translator została wydana w wersji 3 (V3) z interfejsu API tłumaczenia tekstu. Ta wersja zawiera nowe funkcje, metody przestarzałe i nowego formatu do wysyłania do i odbierania danych z usługi Microsoft Translator. Ten dokument zawiera informacje dotyczące zmieniania aplikacji do korzystania z wersji 3. 

Koniec ten dokument zawiera przydatne linki dla Ciebie dowiedzieć się więcej.

## <a name="summary-of-features"></a>Podsumowanie funkcji

* Brak śladu — w bez śledzenia V3 ma zastosowanie do wszystkich warstw cenowych w witrynie Azure portal. Tej funkcji oznacza, że tekst nie jest przesyłany do usługi w wersji 3 interfejsu API, zostaną zapisane przez firmę Microsoft.
* JSON - XML jest zastępowany przez JSON. Wszystkie dane wysyłane do usługi i odbierane z usługi jest w formacie JSON.
* Wielu języków docelowych pojedyncze żądanie — tłumaczenie metoda akceptuje "do" języki do tłumaczenia z jednego żądania. Na przykład pojedyncze żądanie może być angielski "from" i "do" niemiecki, hiszpański i japoński lub jakakolwiek inna grupa języków.
* Słownik dwujęzyczny — metoda słownik dwujęzyczny została dodana do interfejsu API. Ta metoda obejmuje "lookup" i "Przykładowe".
* Transliteracja — metoda transliterate została dodana do interfejsu API. Ta metoda zostanie przekonwertowana słowa i zdania w jednym skrypcie (np. Arabski) do innego skryptu (np. Łaciński).
* Języki — nowej metody "języki" dostarcza informacje o języku, w formacie JSON, do użytku z programem "translacji", "Słownik" i "transliteracja" metod.
* Jesteś nowym użytkownikiem Translate — nowe funkcje zostały dodane do metody "translacji" w celu obsługi niektórych funkcji, które były w interfejsie API w wersji 2 jako odrębne metody. Przykładem jest TranslateArray.
* Czytaj metody — funkcji zamiany tekstu na mowę nie jest już obsługiwana w interfejsie API w usłudze Translator firmy Microsoft. Dostępne są funkcje zamiany tekstu na mowę [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Poniższa lista metod V2 i V3 zawiera metody V3 i interfejsów API, które zapewnia funkcje, dołączonej do wersji 2.

| W wersji 2 Metoda interfejsu API   | W wersji 3 Zgodnością z interfejsem API |
|:----------- |:-------------|
| `Translate`     | [Tłumaczenie](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Tłumaczenie](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Języki](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Języki](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Wykrywanie](reference/v3-0-detect.md)         |
| `DetectArray`     | [Wykrywanie](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funkcja nie jest już obsługiwana       |
| `AddTranslationArray`    | Funkcja nie jest już obsługiwana          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funkcja nie jest już obsługiwana         |
| `GetTranslationsArray`      | Funkcja nie jest już obsługiwana         |

## <a name="move-to-json-format"></a>Przenieś do formatu JSON

V2 tłumaczenia tekstu w usłudze Translator firmy Microsoft zaakceptowane i zwrócił dane w formacie XML. W wersji 3 wszystkich danych wysłanych i odebranych przy użyciu interfejsu API jest w formacie JSON. XML, nie zostanie zaakceptowany lub zwracany w wersji 3.

Ta zmiana wpłynie na różne aspekty aplikacji napisanych dla interfejsu API tłumaczenia tekstu w wersji 2. Na przykład: Interfejs API języków zwraca informacje języka tłumaczenie tekstu, transliterację i metod dwóch słownika. Można zażądać wszystkie informacje o języku dla wszystkich metod w jednym wywołaniu lub zażądać ich osobno.

Metoda języków nie wymaga uwierzytelniania Klikając poniższe łącze, można wyświetlić wszystkich informacji o języku V3 w formacie JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, słownik, transliterację](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Klucz uwierzytelniania

Klucz uwierzytelniania, którego używasz w wersji 2 będą akceptowane dla V3. Nie należy uzyskać nową subskrypcję. Będzie można mieszać V2 i V3 w aplikacjach w okresie migracji yearlong, ułatwiając wydania nowe wersje podczas nadal migracji z pliku XML V2 do V3 JSON.

## <a name="pricing-model"></a>Model cen

Microsoft Translator w wersji 3 jest rozliczana w taki sam sposób, który został cena w wersji 2; na znak łącznie ze spacjami. Nowe funkcje w wersji 3 wprowadzić pewne zmiany, w jakie znaki są uwzględniany w rozliczeniach.

| Metoda v3   | Znaki uwzględniany w rozliczeniach |
|:----------- |:-------------|
| `Languages`     | Nie przesłano żadnych znaków, brak zliczane, bez opłat.          |
| `Translate`     | Liczba zależy od liczby znaków są przesyłane do tłumaczenia i jak wiele języków znaki są tłumaczone na. przesłany 50 znaków i 5 języków, wymagane będzie 50 x 5.           |
| `Transliterate`     | Liczba znaków, które przesłano transliterację są uwzględniane.         |
| `Dictionary lookup & example`     | Liczba znaków przesłane do słownika lookup i examples są uwzględniane.         |
| `BreakSentence`     | Dostępny bezpłatnie.       |
| `Detect`     | Dostępny bezpłatnie.      |

## <a name="v3-end-points"></a>V3 punkty końcowe

Globalny

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metody tłumaczenia tekstu w wersji 3 interfejsu API

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Zgodność i dostosowywania

> [!NOTE]
> 
> W Centrum w usłudze Translator firmy Microsoft zostaną wycofane z dniem 17 maja 2019 r. [Wyświetl informacje na temat migracji ważne i dat](https://www.microsoft.com/translator/business/hub/).   

Domyślnie V3 w usłudze Translator firmy Microsoft używa neuronowego tłumaczenia maszynowego. W efekcie nie można używać z Centrum w usłudze Translator firmy Microsoft. Centrum usługi Translator obsługuje tylko starszego statystycznego tłumaczenia maszynowego. Dostosowywanie na potrzeby tłumaczenie neuronowe jest teraz dostępna, przy użyciu niestandardowych w usłudze Translator. [Dowiedz się więcej o dostosowywaniu neuronowego tłumaczenia maszynowego](custom-translator/overview.md)

Tłumaczenie neuronowe z tekstem w wersji 3 interfejsu API nie obsługuje korzystanie z kategorii standard (SMT, mowy, technologii, generalnn).

| |Endpoint|    Zgodność z RODO procesora|  Za pomocą Centrum w usłudze Translator| Użyj niestandardowego w usłudze Translator (wersja zapoznawcza)|
|:-----|:-----|:-----|:-----|:-----|
|Interfejs API tekstu usługi Translator w wersji 2| api.microsofttranslator.com|    Nie  |Yes    |Nie|
|Wersja interfejsu API tekstu usługi Translator 3| api.cognitive.microsofttranslator.com|  Yes|    Nie| Yes|

**Wersja interfejsu API tekstu usługi Translator 3**
* Jest ogólnie dostępna i w pełni obsługiwane.
* Jest zgodny jako podmiot przetwarzający z RODO i spełniający wszystkie wymagania ISO 20001 i 20018 także SOC 3 certyfikacji. 
* Umożliwia wykonywanie systemów translation sieci neuronowych, które zostały dostosowane przy użyciu niestandardowych w usłudze Translator (wersja zapoznawcza), nowa funkcja dostosowywania Translator NMT. 
* Nie zapewnia dostępu do systemów tłumaczenia niestandardowych utworzone za pomocą Centrum w usłudze Translator firmy Microsoft.

Używasz wersji 3 interfejsu API Rozpoznawania tekstu usługi Translator, korzystając z punktu końcowego api.cognitive.microsofttranslator.com.

**Interfejs API tekstu usługi Translator w wersji 2**
* Nie spełnia wszystkich ISO 20001,20018 i wymagania dotyczące certyfikacji SOC 3. 
* Umożliwia wywoływanie systemów translation sieci neuronowych, które zostały dostosowane przy użyciu funkcji dostosowywania w usłudze Translator.
* Zapewnia dostęp do systemów tłumaczenia niestandardowych utworzone za pomocą Centrum w usłudze Translator firmy Microsoft.
* Używasz wersji 2 API tekstu usługi Translator, korzystając z punktu końcowego api.microsofttranslator.com.

Brak wersji interfejsu API usługi Translator tworzy rekord tłumaczenia. Tłumaczenia nigdy nie są udostępniane nikomu. Więcej informacji na temat [bez śledzenia w usłudze Translator](https://www.aka.ms/NoTrace) strony sieci Web.

## <a name="links"></a>Linki

* [Zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Warunki dotyczące usług online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyświetl dokumentację w wersji 3.0](reference/v3-0-reference.md)
