---
title: Tekst Microsoft Translator interfejsu API — migracja do V3 | Dokumentacja firmy Microsoft
description: Informacje o migracji z wersji 2 do V3 Translator API tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349273"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>Microsoft Translator tekst interfejsu API w wersji 2 do migracji w wersji 3

Zespół Microsoft Translator wydała w wersji 3 (V3) interfejsu API tłumaczenie tekstu. Ta wersja zawiera nowe funkcje, przestarzałe metody i nowy format wysyłania do i odbierania danych z usługi Microsoft translatora. Ten dokument zawiera informacje dotyczące zmieniania aplikacji do użycia w wersji 3. W wersji 2 zostaną wycofane na 30 kwietnia 2018 i 30 kwietnia 2019 przestanie być obsługiwany.

Koniec ten dokument zawiera przydatne łącza można dowiedzieć się więcej.

## <a name="summary-of-features"></a>Podsumowanie funkcji

* Nie śledzenia — w wersji 3 nie-śledzenia ma zastosowanie do wszystkich warstw cenowych w portalu Azure. Oznacza to, że tekst nie przesłane do interfejsu API w wersji 3 zostaną zapisane przez firmę Microsoft.
* JSON — XML zastępuje JSON. Wszystkie dane wysyłane do usługi i odbierane z usługi jest w formacie JSON.
* Wiele języków docelowego pojedyncze żądanie - tłumaczenie — metoda akceptuje wielu "do" języków dla tłumaczenia pojedyncze żądanie. Na przykład pojedynczego żądania można angielski "od" i "do" niemiecki, hiszpański i japoński lub grupę języków.
* Słownik z obsługą dwóch języków — metoda obsługą dwóch języków słownik został dodany do interfejsu API. Ta metoda obejmuje "wyszukiwanie" i "Przykłady".
* Transliterate — metoda transliterate został dodany do interfejsu API. Ta metoda konwertuje słów i zdań w jednym skrypcie (np. Arabski) w innym skrypcie (np. Łaciński).
* Języki — nowej metody "języki" dostarcza informacje o języku w formacie JSON do użycia z programem "tłumaczenie", "Słownik" i "transliterate" metod.
* Jesteś nowym użytkownikiem Przetłumacz — nowe funkcje zostały dodane do metody "tłumaczenie" do obsługi niektórych funkcji, które były w interfejsie API V2 jako osobne metody. Przykładem jest TranslateArray.
* Mowy metoda — tekst na mowę funkcji nie jest już obsługiwana w interfejsie API Translator firmy Microsoft. Tekst na mowę funkcja jest dostępna w usługach Microsoft Azure kognitywnych API mowy usługi Bing.

Poniższa lista metod V2 i V3 zawiera interfejsy API, która udostępnia funkcje dołączonej V2 i V3 metody.

| W wersji 2 Metoda interfejsu API   | W wersji 3 Zgodność z interfejsu API |
|:----------- |:-------------|
| Przetłumacz     | Przetłumacz          |
| TranslateArray      | Przetłumacz          |
| GetLanguageNames      | Języki          |
| GetLanguagesForTranslate     | Języki        |
| GetLanguagesForSpeak      | Mowy kognitywnych usług interfejsu API         |
| Mowy     | Mowy kognitywnych usług interfejsu API          |
| Wykrywanie     | Wykrywanie         |
| DetectArray     | Wykrywanie         |
| AddTranslation     | Interfejs API Centrum Microsoft Translator         |
| AddTranslationArray    | Interfejs API Centrum Microsoft Translator          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Funkcja nie jest już obsługiwana         |
| GetTranslationsArray      | Funkcja nie jest już obsługiwana         |

## <a name="move-to-json-format"></a>Przenieś do formatu JSON

Microsoft Translator tekstu tłumaczenia V2 zaakceptował i zwrócił dane w formacie XML. W wersji 3 wszystkie dane wysyłane i odbierane przy użyciu interfejsu API jest w formacie JSON. XML nie będą akceptowane lub zwracane w wersji 3. 

Ta zmiana będzie miało wpływ na kilka aspektów aplikacji napisanych dla interfejsu API tłumaczenie tekstu V2. Na przykład: języków interfejsu API zwraca informacje o języku tłumaczenie tekstu, transliterację i słownika dwóch metod. Można zażądać wszystkich informacji języka dla wszystkich metod w jednym wywołaniu lub zażądać ich osobno.

Metoda języków nie wymaga uwierzytelniania; Klikając poniższe łącze, można wyświetlić wszystkie informacje języka w wersji 3 w formacie JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, słownik, transliterację](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Klucz uwierzytelniania

Klucz uwierzytelniania używaną dla V2 będą akceptowane do V3. Nie należy uzyskać nową subskrypcję. Będzie można mieszać V2 i V3 w aplikacjach w okresie migracji yearlong, ułatwiając wydania nowej wersji, podczas nadal migracji z pliku XML w wersji 2 do formatu JSON V3.

## <a name="pricing-model"></a>Model cen

Microsoft Translator V3 kosztuje w taki sam sposób, który został cenach V2; na znak łącznie ze spacjami. Nowe funkcje w wersji 3 wprowadzić kilka zmian, w jakie znaki są one uwzględniane dla rozliczeń.

| V3 — metoda   | Znaki, są one uwzględniane dla rozliczeń |
|:----------- |:-------------|
| Języki     | Żadne znaki nie przesłano, brak liczone, bez dodatkowych opłat.          |
| Przetłumacz     | Liczba jest oparta na liczbę znaków są przesyłane do tłumaczenia i jak wiele języków znaki są tłumaczone na. 50 znaków, i 5 języków, żądanie zostanie 50 x 5.           |
| Transliterate     | Liczba znaków do transliterację są zliczane.         |
| Słownik wyszukiwania i przykład     | Liczba znaków do słownika wyszukiwania i przykłady są liczone.         |
| BreakSentence     | Bez dodatkowych opłat.       |
| Wykrywanie     | Bez dodatkowych opłat.      |

## <a name="v3-end-points"></a>W wersji 3 punkty końcowe

Globalny

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Metody tłumaczeń tekstu interfejsu API w wersji 3

[Języki](reference/v3-0-languages.md)

[Przetłumacz](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Wykryj](reference/v3-0-detect.md)

[Słownik/wyszukiwania](reference/v3-0-dictionary-lookup.md)

[Słownik/przykład](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Dostosowywanie

Microsoft Translator V3 domyślnie używa neuronowej tłumaczenia maszynowego. W efekcie nie można używać z Centrum Translator firmy Microsoft, który obsługuje tylko starsze statystyczne tłumaczenia maszynowego. Dostosowywanie neuronowej tłumaczenia jest teraz dostępna, przy użyciu translatora niestandardowe. [Dowiedz się więcej na temat dostosowywania neuronowej tłumaczenia maszynowego](customization.md)

Tłumaczenie neuronowej z tekstem V3 interfejsu API nie obsługuje korzystania standardowe kategorii (smt, mowy, tekst, generalnn).


## <a name="links"></a>Linki

* [Zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informacje prawne platformy Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Warunki dotyczące usług online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyświetl dokumentację w wersji 3.0](reference/v3-0-reference.md)
