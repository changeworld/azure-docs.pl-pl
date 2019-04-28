---
title: Najlepsze praktyki — QnA Maker
titlesuffix: Azure Cognitive Services
description: Używania tych najlepszych rozwiązań, aby usprawnić bazy wiedzy i zapewniają lepsze wyniki użytkownikom końcowym bota aplikacji/rozmowy.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/28/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 0f94a1fdc01825b5bf78644f84c72e6b031109c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379222"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Najlepsze rozwiązania wiedzy usługi QnA Maker
[Cykl życia projektowania bazy wiedzy knowledge base](../Concepts/development-lifecycle-knowledge-base.md) przeprowadzi Cię o tym, jak zarządzać wiedzy od początku do końca. Używania tych najlepszych rozwiązań, aby usprawnić bazy wiedzy i zapewniają lepsze wyniki użytkownikom końcowym bota aplikacji/rozmowy.

## <a name="extraction"></a>Wyodrębniania
Usługa QnA Maker jest ulepszasz algorytmy, które znacznie wyodrębniania zawartości i rozwijając listę obsługiwanych plików i formatów HTML. Postępuj zgodnie z [wytycznych](../Concepts/data-sources-supported.md) do wyodrębnienia danych na podstawie Twojego typu dokumentu. 

Ogólnie rzecz biorąc często zadawane pytania dotyczące strony powinna być autonomiczne i nie są połączone z innymi informacjami. Podręczniki powinny mieć, usuń zaznaczenie pozycji, a najlepiej strony indeksu. 

## <a name="creating-good-questions-and-answers"></a>Tworzenie dobrej pytań i odpowiedzi

### <a name="good-questions"></a>Dobre pytania

Najlepsze pytania są proste. Należy wziąć pod uwagę słowo kluczowe lub frazy dla każdego zapytania, a następnie utworzyć proste pytanie dla tego słowa kluczowego lub frazy. 

Dodaj dowolną liczbę pytań alternatywne potrzebujesz, ale proste zmiany. Dodanie więcej wyrazy lub frazy, które nie są częścią głównym celem pytanie nie przyczynić się do usługi QnA Maker znaleźć dopasowania. 

### <a name="good-answers"></a>Dobre odpowiedzi

Najlepsze odpowiedzi są odpowiedzi, proste, ale nie zbyt proste, takie jak tak, jak i nie odpowiedzi. Jeśli odpowiedź powinna połączyć inne źródła lub zapewniają bogate środowisko przy użyciu nośnika i łącza, użyj [znakowanie](../how-to/metadata-generateanswer-usage.md) rozróżnienie, jakiego typu odpowiedzi oczekujesz, a następnie Prześlij ten znacznik z zapytaniem, które można pobrać wersji poprawną odpowiedź.

## <a name="chit-chat"></a>Chit rozmowy
Dodaj chit czatu do bota się bota konwersacji i bardziej angażujące, za pomocą mały nakład pracy. Możesz łatwo dodać zestawy danych chit rozmowy dla 3 osobowości wstępnie zdefiniowanych podczas tworzenia wiedzy i je zmienić w dowolnym momencie. Dowiedz się, jak [Dodaj chit czatu do wiedzy](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Wybieranie użytkownika
Chit Rozmowa jest obsługiwana dla 3 osobowości wstępnie zdefiniowane: 

|Osobowości|
|--|
|Professional|
|Zaprzyjaźnionej|
|Comic|

Zakres odpowiedzi z formalnego nieformalny i zamieszczanie. Należy wybrać osobowości, znajdujący się najbliżej wyrównany tonu, wybranego dla bota. Można wyświetlić zestawy danych i wybierz jedną, która służy jako podstawa bota, a następnie dostosować odpowiedzi. 

### <a name="edit-bot-specific-questions"></a>Edycja pytania dotyczące botów
Istnieją niektóre pytania dotyczące bot, które są częścią zestawu danych chit rozmowy i zostało wypełnione uniwersalne odpowiedzi. Zmień odpowiedzi na te pytania, aby odzwierciedlić najlepiej szczegółów dotyczących bota. 

Firma Microsoft zaleca wprowadzania następujących znacznie rozmowy chit bardziej szczegółowe:

* Kim jesteś?
* Co należy zrobić?
* Ile masz lat?
* Który użytkownik utworzył?
* Witamy
   

## <a name="rankingscoring"></a>Klasyfikacja/oceniania
Upewnij się, że wykonujesz optymalnie wykorzystać funkcje klasyfikacji, które obsługuje usługi QnA Maker. To zwiększa prawdopodobieństwo który zapytania danego użytkownika zostanie odebrane z właściwą odpowiedzią.

### <a name="choosing-a-threshold"></a>Wybranie wartości progowej
Współczynnik ufności domyślny, który jest używany jako próg wynosi 50, jednak można ją zmienić na wiedzy na podstawie własnych potrzeb. Ponieważ każdy KB jest inny, możesz przetestować i wybierz wartość progową, która najlepiej dopasowane do wiedzy. Przeczytaj więcej na temat [współczynnik ufności](../Concepts/confidence-score.md). 

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania
[Alternatywny pytania](../How-To/edit-knowledge-base.md) zwiększyć prawdopodobieństwo pasują do zapytania użytkownika. Alternatywne pytania są przydatne, gdy istnieje wiele sposobów, w którym może zostać wyświetlony monit tego samego zapytania. Może to obejmować zmiany zdania struktury i style programu word.

|Oryginalne zapytanie|Alternatywnych zapytań|Change| 
|--|--|--|
|Jest parkowania dostępne?|Czy masz park samochodu?|Struktura zdania|
 |Witaj|Yo<br>Hej tam!|Styl programu Word lub żargonu|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Za pomocą tagów metadanych do filtru pytań i odpowiedzi

[Metadane](../How-To/edit-knowledge-base.md) dodaje możliwość zawężać wyniki zapytania użytkownika, na podstawie tagów metadanych. Odpowiedź bazy wiedzy knowledge base może się różnić w zależności w tagu metadanych nawet, jeśli zapytanie jest taka sama. Na przykład *"gdzie jest umieszczony parkowania"* mogą mieć różne odpowiedzi, jeśli różni się lokalizacja gałęzi, restauracji — metadanych jest *lokalizacji: Seattle* a *lokalizacji: Redmond*.

### <a name="use-synonyms"></a>Używać synonimów
Choć niektóre Obsługa synonimów w języku angielskim, użyj bez uwzględniania wielkości liter [word zmiany](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) można dodać synonimy do słów kluczowych, które przyjmują różne formularza. Synonimy, należy dodać na poziomie usługi QnA Maker i współużytkowane przez wszystkie bazy wiedzy w usłudze.

|Oryginalny programu word|Synonimy|
|--|--|
|Kup|kup<br>netbanking<br>Bankowość netto|

### <a name="use-distinct-words-to-differentiate-questions"></a>Użyj unikatowych słów, aby odróżnić pytania
Algorytm klasyfikacji usługi QnA Maker zgodną zapytanie użytkownika z zapytania w bazie wiedzy knowledge base działa najlepiej, jeśli każde pytanie dotyczy różne potrzeby. Powtórzenie tego samego wyrazu ustawiony między pytania zmniejsza prawdopodobieństwo, że wybrano prawidłowej odpowiedzi dla danego użytkownika zapytania za pomocą tych słów. 

Na przykład może mieć dwa oddzielne znacznie następujące pytania:

|Znacznie|
|--|
|gdzie jest parkowania *lokalizacji*|
|gdzie jest ATM *lokalizacji*|

Ponieważ te dwa znacznie są ma inną pisownię słów bardzo podobne, to podobieństwa może spowodować, że bardzo podobne wyniki dla wielu zapytań użytkownika, które są ma inną pisownię, takich jak *"gdzie jest `<x>` lokalizację"*. Zamiast tego należy starać się wyraźnie odróżnić za pomocą kwerend, takich jak *"gdzie jest dużo parkowania"* i *"gdzie jest ATM"*, unikając wyrazy, takie jak "Lokalizacja", może być wiele pytań w wiedzy. 

## <a name="collaborate"></a>Współpraca
Usługa QnA Maker umożliwia użytkownikom [współpracy](../How-to/collaborate-knowledge-base.md) na wiedzy. Użytkownicy muszą mieć dostęp do grupy zasobów platformy Azure usługa QnA Maker w celu uzyskania dostępu do bazy wiedzy. W niektórych organizacjach może być oddelegowanie edytowanie wiedzy i konserwacji i nadal mieć możliwość ochrony dostępu do swoich zasobów platformy Azure. Ten model osoba zatwierdzająca edytora odbywa się przez skonfigurowanie dwóch identycznych [usługi QnA Maker](../How-to/set-up-qnamaker-service-azure.md) w różnych subskrypcjach i wybranie jednego cyklu testowania edycji. Po zakończeniu testowania zawartości bazy wiedzy są przesyłane przy użyciu [import-export](../Tutorials/migrate-knowledge-base.md) procesu usługi QnA Maker osoby zatwierdzającej, która zostanie ostatecznie publikowanie bazy wiedzy knowledge base i aktualizowanie punktu końcowego.

## <a name="active-learning"></a>Aktywne uczenie

[Aktywna nauka](../How-to/improve-knowledge-base.md) jest najlepiej sugerowanie alternatywnych pytania, gdy ma ona szeroką gamę jakości i ilości zapytania oparte na użytkownikach. Ważne jest, aby zezwolić na zapytania użytkowników klienta aplikacji do wzięcia udziału w aktywnej nauki sprzężenia zwrotnego bez cenzurą. Po pytania są sugerowane w portalu narzędzia QnA Maker, możesz **[Filtruj według sugestii](../How-To/improve-knowledge-base.md#add-active-learning-suggestion-to-knowledge-base)** , a następnie przejrzeć i zaakceptować lub odrzucić te sugestie. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-to/edit-knowledge-base.md)
