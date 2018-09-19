---
title: Migrowanie do V3 — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację z V2 do V3 interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: eaf65bef28110d73378c213ae4781a409b86e1bd
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128183"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>W usłudze Translator tekstu interfejsu API w wersji 2 do migracji V3

Zespół Microsoft Translator została wydana w wersji 3 (V3) z interfejsu API tłumaczenia tekstu. Ta wersja zawiera nowe funkcje, metody przestarzałe i nowego formatu do wysyłania do i odbierania danych z usługi Microsoft Translator. Ten dokument zawiera informacje dotyczące zmieniania aplikacji do korzystania z wersji 3. W wersji 2 zostaną wycofane w dniu 30 kwietnia 2018 i zostanie zakończona w dniu 30 kwietnia 2019 r.

Koniec ten dokument zawiera przydatne linki dla Ciebie dowiedzieć się więcej.

## <a name="summary-of-features"></a>Podsumowanie funkcji

* Brak śladu — w bez śledzenia V3 ma zastosowanie do wszystkich warstw cenowych w witrynie Azure portal. Tej funkcji oznacza, że tekst nie jest przesyłany do usługi w wersji 3 interfejsu API, zostaną zapisane przez firmę Microsoft.
* JSON - XML jest zastępowany przez JSON. Wszystkie dane wysyłane do usługi i odbierane z usługi jest w formacie JSON.
* Wielu języków docelowych pojedyncze żądanie — tłumaczenie metoda akceptuje "do" języki do tłumaczenia z jednego żądania. Na przykład pojedyncze żądanie może być angielski "from" i "do" niemiecki, hiszpański i japoński lub jakakolwiek inna grupa języków.
* Słownik dwujęzyczny — metoda słownik dwujęzyczny została dodana do interfejsu API. Ta metoda obejmuje "lookup" i "Przykładowe".
* Transliteracja — metoda transliterate została dodana do interfejsu API. Ta metoda zostanie przekonwertowana słowa i zdania w jednym skrypcie (np. Arabski) do innego skryptu (np. Łaciński).
* Języki — nowej metody "języki" dostarcza informacje o języku, w formacie JSON, do użytku z programem "translacji", "Słownik" i "transliteracja" metod.
* Jesteś nowym użytkownikiem Translate — nowe funkcje zostały dodane do metody "translacji" w celu obsługi niektórych funkcji, które były w interfejsie API w wersji 2 jako odrębne metody. Przykładem jest TranslateArray.
* Czytaj metody — funkcji zamiany tekstu na mowę nie jest już obsługiwana w interfejsie API w usłudze Translator firmy Microsoft. Funkcja zamiany tekstu na mowę jest dostępna w usługach Azure Cognitive modułu Speech API Bing.

Poniższa lista metod V2 i V3 zawiera metody V3 i interfejsów API, które zapewnia funkcje, dołączonej do wersji 2.

| W wersji 2 Metoda interfejsu API   | W wersji 3 Zgodnością z interfejsem API |
|:----------- |:-------------|
| Tłumaczenie     | Tłumaczenie          |
| TranslateArray      | Tłumaczenie          |
| GetLanguageNames      | Języki          |
| GetLanguagesForTranslate     | Języki        |
| GetLanguagesForSpeak      | Interfejs API rozpoznawania mowy usług cognitive Services         |
| Czytaj     | Interfejs API rozpoznawania mowy usług cognitive Services          |
| Wykrywanie     | Wykrywanie         |
| DetectArray     | Wykrywanie         |
| AddTranslation     | Interfejs API Centrum usługi Microsoft Translator         |
| AddTranslationArray    | Interfejs API Centrum usługi Microsoft Translator          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Funkcja nie jest już obsługiwana         |
| GetTranslationsArray      | Funkcja nie jest już obsługiwana         |

## <a name="move-to-json-format"></a>Przenieś do formatu JSON

V2 tłumaczenia tekstu w usłudze Translator firmy Microsoft zaakceptowane i zwrócił dane w formacie XML. W wersji 3 wszystkich danych wysłanych i odebranych przy użyciu interfejsu API jest w formacie JSON. XML, nie zostanie zaakceptowany lub zwracany w wersji 3. 

Ta zmiana wpłynie na różne aspekty aplikacji napisanych dla interfejsu API tłumaczenia tekstu w wersji 2. Na przykład: interfejs API języków zwraca informacje języka na tłumaczenie tekstu, transliterację i metod dwóch słownika. Można zażądać wszystkie informacje o języku dla wszystkich metod w jednym wywołaniu lub zażądać ich osobno.

Metoda języków nie wymaga uwierzytelniania Klikając poniższe łącze, można wyświetlić wszystkich informacji o języku V3 w formacie JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, słownik, transliterację](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Klucz uwierzytelniania

Klucz uwierzytelniania, którego używasz w wersji 2 będą akceptowane dla V3. Nie należy uzyskać nową subskrypcję. Będzie można mieszać V2 i V3 w aplikacjach w okresie migracji yearlong, ułatwiając wydania nowe wersje podczas nadal migracji z pliku XML V2 do V3 JSON.

## <a name="pricing-model"></a>Model cen

Microsoft Translator w wersji 3 jest rozliczana w taki sam sposób, który został cena w wersji 2; na znak łącznie ze spacjami. Nowe funkcje w wersji 3 wprowadzić pewne zmiany, w jakie znaki są uwzględniany w rozliczeniach.

| Metoda v3   | Znaki uwzględniany w rozliczeniach |
|:----------- |:-------------|
| Języki     | Nie przesłano żadnych znaków, brak zliczane, bez opłat.          |
| Tłumaczenie     | Liczba zależy od liczby znaków są przesyłane do tłumaczenia i jak wiele języków znaki są tłumaczone na. przesłany 50 znaków i 5 języków, wymagane będzie 50 x 5.           |
| Transliteracja     | Liczba znaków, które przesłano transliterację są uwzględniane.         |
| Słownik odnośników i przykład     | Liczba znaków przesłane do słownika lookup i examples są uwzględniane.         |
| BreakSentence     | Dostępny bezpłatnie.       |
| Wykrywanie     | Dostępny bezpłatnie.      |

## <a name="v3-end-points"></a>V3 punkty końcowe

Global

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Metody tłumaczenia tekstu w wersji 3 interfejsu API

[Języki](reference/v3-0-languages.md)

[Przetłumacz](reference/v3-0-translate.md)

[Transliteracja](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Wykrywanie](reference/v3-0-detect.md)

[Słownik/wyszukiwania](reference/v3-0-dictionary-lookup.md)

[Słownik/przykład](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Dostosowywanie

Domyślnie V3 w usłudze Translator firmy Microsoft używa neuronowego tłumaczenia maszynowego. W efekcie nie można używać z Centrum w usłudze Translator firmy Microsoft. Centrum usługi Translator obsługuje tylko starszego statystycznego tłumaczenia maszynowego. Dostosowywanie na potrzeby tłumaczenie neuronowe jest teraz dostępna, przy użyciu niestandardowych w usłudze Translator. [Dowiedz się więcej o dostosowywaniu neuronowego tłumaczenia maszynowego](customization.md)

Tłumaczenie neuronowe z tekstem w wersji 3 interfejsu API nie obsługuje korzystanie z kategorii standard (SMT, mowy, tekst, generalnn).


## <a name="links"></a>Linki

* [Zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Warunki dotyczące usług online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyświetl dokumentację w wersji 3.0](reference/v3-0-reference.md)
