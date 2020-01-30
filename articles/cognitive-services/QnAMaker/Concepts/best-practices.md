---
title: Najlepsze praktyki — QnA Maker
titleSuffix: Azure Cognitive Services
description: Używania tych najlepszych rozwiązań, aby usprawnić bazy wiedzy i zapewniają lepsze wyniki użytkownikom końcowym bota aplikacji/rozmowy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2fd85e43fb2aa53299b4e37eca5163b7da8fc6ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843807"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Najlepsze rozwiązania wiedzy usługi QnA Maker

[Cykl życia projektowania bazy wiedzy knowledge base](../Concepts/development-lifecycle-knowledge-base.md) przeprowadzi Cię o tym, jak zarządzać wiedzy od początku do końca. Skorzystaj z tych najlepszych rozwiązań, aby ulepszyć bazę wiedzy i zapewnić lepsze wyniki dla aplikacji klienckiej lub użytkowników końcowych programu Chat bot.

## <a name="extraction"></a>Wyodrębniania

Usługa QnA Maker jest ulepszasz algorytmy, które znacznie wyodrębniania zawartości i rozwijając listę obsługiwanych plików i formatów HTML. Postępuj zgodnie z [wytycznych](../Concepts/content-types.md) do wyodrębnienia danych na podstawie Twojego typu dokumentu.

Ogólnie rzecz biorąc często zadawane pytania dotyczące strony powinna być autonomiczne i nie są połączone z innymi informacjami. Podręczniki powinny mieć, usuń zaznaczenie pozycji, a najlepiej strony indeksu.

### <a name="configuring-multi-turn"></a>Konfigurowanie wielostopniowego

[Utwórz bazę wiedzy](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) z włączoną funkcją wyodrębniania wieloskładnikowego. Jeśli baza wiedzy lub powinna obsługiwać hierarchię pytań, ta hierarchia może zostać wyodrębniona z dokumentu lub utworzona po wyodrębnieniu dokumentu.

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Tworzenie dobrych pytań i odpowiedzi

### <a name="good-questions"></a>Dobre pytania

Najlepsze pytania są proste. Rozważ słowo kluczowe lub frazę dla każdego pytania, a następnie utwórz proste pytanie dla tego słowa kluczowego lub frazy.

Dodaj dowolną liczbę pytań alternatywnych, ale zachowuj proste zmiany. Dodanie więcej słów lub frazy, które nie są częścią głównego celu pytania, nie pomaga QnA Maker znaleźć dopasowania.


### <a name="add-relevant-alternative-questions"></a>Dodawanie istotnych pytań alternatywnych

Użytkownik może wprowadzać pytania przy użyciu stylu konwersacji tekstu, `How do I add a toner cartridge to my printer?` lub wyszukiwania słowa kluczowego, takiego jak `toner cartridge`. Baza wiedzy powinna mieć oba style pytań, aby prawidłowo zwrócić najlepszą odpowiedź. Jeśli nie masz pewności, jakie słowa kluczowe są wprowadzane przez klienta, użyj Application Insights danych do analizowania zapytań.

### <a name="good-answers"></a>Dobre odpowiedzi

Najlepszym odpowiedziami są proste odpowiedzi, ale nie są one zbyt proste. Nie należy używać odpowiedzi, takich jak `yes` i `no`. Jeśli odpowiedź powinna łączyć się z innymi źródłami lub zapewniać bogate środowisko z nośnikami i łączami, należy użyć [tagowania metadanych](../how-to/edit-knowledge-base.md#add-metadata) do rozróżnienia między odpowiedziami, a następnie [przesłać zapytanie](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) ze znacznikiem metadanych we właściwości `strictFilters`, aby uzyskać poprawną wersję odpowiedzi.

|Odpowiedź|Follup|
|--|--|
|Wyłącz komputer przenośny przy użyciu przycisku energia na klawiaturze.|* Kombinację klawiszy do uśpienia, wyłączenia i ponownego uruchomienia.<br>* Jak wypróbować twardy laptop powierzchniowy<br>* Jak zmienić system BIOS dla komputera przenośnego<br>* Różnice między uśpieniem, wyłączaniem i ponownym uruchomieniem|
|Usługa klienta jest dostępna za pośrednictwem telefonu, Skype'a i wiadomości SMS przez 24 godziny dziennie.|* Informacje kontaktowe dotyczące sprzedaży.<br> * Lokalizacje biur i sklepów oraz godziny dla osób odwiedzających.<br> * Akcesoria dla komputera przenośnego.|

## <a name="chit-chat"></a>Chit rozmowy
Dodaj chit czatu do bota się bota konwersacji i bardziej angażujące, za pomocą mały nakład pracy. Zestawy danych chit-chat można łatwo dodawać ze wstępnie zdefiniowanych osób prywatnych podczas tworzenia bazy wiedzy i zmieniać je w dowolnym momencie. Dowiedz się, jak [Dodaj chit czatu do wiedzy](../How-To/chit-chat-knowledge-base.md).

Chit — rozmowa jest obsługiwana w [wielu językach](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Wybieranie użytkownika
Chit — rozmowa jest obsługiwana w przypadku kilku wstępnie zdefiniowanych funkcji osobistych:

|Osobowości |Plik zestawu danych QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Wyświetlana |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|R |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Zakres odpowiedzi z formalnego nieformalny i zamieszczanie. Należy wybrać osobowości, znajdujący się najbliżej wyrównany tonu, wybranego dla bota. Można wyświetlić [zestawy danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)i wybrać jeden, który służy jako podstawa dla bot, a następnie dostosować odpowiedzi.

### <a name="edit-bot-specific-questions"></a>Edycja pytania dotyczące botów
Istnieją niektóre pytania dotyczące bot, które są częścią zestawu danych chit rozmowy i zostało wypełnione uniwersalne odpowiedzi. Zmień odpowiedzi na te pytania, aby odzwierciedlić najlepiej szczegółów dotyczących bota.

Firma Microsoft zaleca wprowadzania następujących znacznie rozmowy chit bardziej szczegółowe:

* Kim jesteś?
* Co należy zrobić?
* Ile masz lat?
* Który użytkownik utworzył?
* Witamy

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Dodawanie niestandardowego Chit-Chat ze znacznikiem metadanych

Jeśli dodasz własne pary Chit-Chat QnA, pamiętaj o dodaniu metadanych, aby zostały zwrócone te odpowiedzi. Para nazwa/wartość metadanych jest `editorial:chitchat`.

## <a name="searching-for-answers"></a>Wyszukiwanie odpowiedzi

Interfejs API GenerateAnswer używa pytań i odpowiedzi w celu wyszukania najlepszych odpowiedzi na zapytanie użytkownika.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Wyszukiwanie pytań tylko wtedy, gdy odpowiedź nie jest istotna

Użyj [`RankerType=QuestionOnly`](#choosing-ranker-type) , jeśli nie chcesz wyszukiwać odpowiedzi.

Przykładem takiej sytuacji jest to, że baza wiedzy jest wykazem akronimów jako pytań z ich pełną formą jako odpowiedzią. Wartość odpowiedzi nie będzie pomocna w wyszukiwaniu odpowiedniej odpowiedzi.

## <a name="rankingscoring"></a>Klasyfikacja/oceniania
Upewnij się, że wykonujesz optymalnie wykorzystać funkcje klasyfikacji, które obsługuje usługi QnA Maker. To zwiększa prawdopodobieństwo który zapytania danego użytkownika zostanie odebrane z właściwą odpowiedzią.

### <a name="choosing-a-threshold"></a>Wybranie wartości progowej

Domyślnym [wynikiem ufności](confidence-score.md) używanym jako próg jest 50, jednak można [zmienić próg](confidence-score.md#set-threshold) dla bazy wiedzy w zależności od potrzeb. Ponieważ każdy KB jest inny, możesz przetestować i wybierz wartość progową, która najlepiej dopasowane do wiedzy.

### <a name="choosing-ranker-type"></a>Wybieranie typu rangi
Domyślnie QnA Maker przeszukuje pytania i odpowiedzi. Jeśli chcesz przeszukiwać tylko pytania, aby wygenerować odpowiedź, użyj `RankerType=QuestionOnly` w treści wpisu żądania GenerateAnswer.

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania
[Alternatywny pytania](../How-To/edit-knowledge-base.md) zwiększyć prawdopodobieństwo pasują do zapytania użytkownika. Alternatywne pytania są przydatne, gdy istnieje wiele sposobów, w którym może zostać wyświetlony monit tego samego zapytania. Może to obejmować zmiany zdania struktury i style programu word.

|Oryginalne zapytanie|Alternatywnych zapytań|Change|
|--|--|--|
|Jest parkowania dostępne?|Czy masz park samochodu?|Struktura zdania|
 |Witaj|Yo<br>Hej tam!|Styl programu Word lub żargonu|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrowanie pytań i odpowiedzi za pomocą tagów metadanych

[Metadane](../How-To/edit-knowledge-base.md) umożliwiają aplikacji klienckiej, aby wiedzieć, że nie powinna przyjmować wszystkich odpowiedzi, ale zamiast tego zawęża wyniki zapytania użytkownika w oparciu o Tagi metadanych. Odpowiedź bazy wiedzy knowledge base może się różnić w zależności w tagu metadanych nawet, jeśli zapytanie jest taka sama. Na przykład *"gdzie jest umieszczony parkowania"* mogą mieć różne odpowiedzi, jeśli różni się lokalizacja gałęzi, restauracji — metadanych jest *lokalizacji: Seattle* a *lokalizacji: Redmond*.

### <a name="use-synonyms"></a>Używać synonimów
W przypadku niektórych rodzajów synonimów w języku angielskim należy użyć zmian wyrazów bez uwzględniania wielkości liter za pośrednictwem [interfejsu API Alters](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) , aby dodać synonimy do słów kluczowych, które mają inną formę. Synonimy są dodawane na poziomie usługi QnA Maker i udostępniane przez wszystkie bazy wiedzy w usłudze.

|Oryginalny programu word|Synonimy|
|--|--|
|Kup|kup<br>Sieć — bankowość<br>Bankowość netto|

### <a name="use-distinct-words-to-differentiate-questions"></a>Użyj unikatowych słów, aby odróżnić pytania
Algorytm klasyfikacji QnA Maker, który pasuje do zapytania użytkownika z pytaniem w bazie wiedzy, najlepiej sprawdza się, jeśli każde pytanie odniesie się do różnych potrzeb. Powtórzenie tego samego wyrazu ustawiony między pytania zmniejsza prawdopodobieństwo, że wybrano prawidłowej odpowiedzi dla danego użytkownika zapytania za pomocą tych słów.

Na przykład może mieć dwa oddzielne znacznie następujące pytania:

|Znacznie|
|--|
|gdzie jest parkowania *lokalizacji*|
|gdzie jest *Lokalizacja* ATM|

Ponieważ te dwa znacznie są ma inną pisownię słów bardzo podobne, to podobieństwa może spowodować, że bardzo podobne wyniki dla wielu zapytań użytkownika, które są ma inną pisownię, takich jak *"gdzie jest `<x>` lokalizację"* . Zamiast tego spróbuj wyraźnie odróżnić się od zapytań, takich jak *"gdzie jest to parkingi"* i *"gdzie jest ATM"* , unikając słów takich jak "lokalizacja", które mogą mieć wiele pytań w KB.

## <a name="collaborate"></a>Współpraca
Usługa QnA Maker umożliwia użytkownikom [współpracy](../How-to/collaborate-knowledge-base.md) na wiedzy. Użytkownicy muszą mieć dostęp do grupy zasobów platformy Azure usługa QnA Maker w celu uzyskania dostępu do bazy wiedzy. W niektórych organizacjach może być oddelegowanie edytowanie wiedzy i konserwacji i nadal mieć możliwość ochrony dostępu do swoich zasobów platformy Azure. Ten model osoba zatwierdzająca edytora odbywa się przez skonfigurowanie dwóch identycznych [usługi QnA Maker](../How-to/set-up-qnamaker-service-azure.md) w różnych subskrypcjach i wybranie jednego cyklu testowania edycji. Po zakończeniu testowania zawartości bazy wiedzy są przesyłane przy użyciu [import-export](../Tutorials/migrate-knowledge-base.md) procesu usługi QnA Maker osoby zatwierdzającej, która zostanie ostatecznie publikowanie bazy wiedzy knowledge base i aktualizowanie punktu końcowego.



## <a name="active-learning"></a>Aktywne uczenie

[Usługa Active Learning](../How-to/improve-knowledge-base.md) to najlepsze zadanie sugerujące alternatywne pytania, gdy ma szeroki zakres jakości i liczbę zapytań opartych na użytkownikach. Ważne jest, aby zezwolić na uczestnictwo użytkowników aplikacji klienta w aktywnej pętli opinii o uczeniu bez Censorship. Po wybraniu pytań w portalu QnA Maker można **[filtrować według sugestii](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , a następnie przejrzeć i zaakceptować lub odrzucić te sugestie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-to/edit-knowledge-base.md)
