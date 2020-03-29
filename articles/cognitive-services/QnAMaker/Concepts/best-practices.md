---
title: Najważniejsze wskazówki - QnA Maker
description: Skorzystaj z tych najlepszych rozwiązań, aby poprawić swoją bazę wiedzy i zapewnić lepsze wyniki użytkownikom końcowym aplikacji / czatu bota.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053132"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Najlepsze praktyki bazy wiedzy QnA Maker

[Cykl życia rozwoju bazy wiedzy](../Concepts/development-lifecycle-knowledge-base.md) prowadzi użytkownika, jak zarządzać bazy wiedzy od początku do końca. Skorzystaj z tych najlepszych rozwiązań, aby ulepszyć swoją bazę wiedzy i zapewnić lepsze wyniki użytkownikom końcowym aplikacji klienckiej lub czatu bota.

## <a name="extraction"></a>Wyodrębnianie

Usługa QnA Maker stale ulepsza algorytmy, które wyodrębniają QnA z zawartości i rozszerza listę obsługiwanych formatów plików i HTML. Postępuj zgodnie ze [wskazówkami dotyczącymi](../Concepts/content-types.md) wyodrębniania danych na podstawie typu dokumentu.

Ogólnie rzecz biorąc, strony często zadawanych pytań powinny być autonomiczne i nie powinny być łączone z innymi informacjami. Instrukcje obsługi produktów powinny mieć jasne nagłówki, a najlepiej stronę indeksu.

### <a name="configuring-multi-turn"></a>Konfigurowanie wielu zakrętów

[Stwórz swoją bazę wiedzy](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) z obsługą ekstrakcji wielokierunkowej. Jeśli baza wiedzy obsługuje hierarchię pytań lub powinna obsługiwać, ta hierarchia może zostać wyodrębniona z dokumentu lub utworzona po wyodrębnianiu dokumentu.

## <a name="creating-good-questions-and-answers"></a>Tworzenie dobrych pytań i odpowiedzi

### <a name="good-questions"></a>Dobre pytania

Najlepsze pytania są proste. Rozważ słowo kluczowe lub frazę dla każdego pytania, a następnie utwórz proste pytanie dla tego słowa lub frazy kluczowej.

Dodaj tyle alternatywnych pytań, ile potrzebujesz, ale zachowaj proste zmiany. Dodanie większej liczby słów lub zwrotów, które nie są częścią głównego celu pytania, nie pomaga QnA Maker znaleźć dopasowanie.


### <a name="add-relevant-alternative-questions"></a>Dodawanie odpowiednich pytań alternatywnych

Użytkownik może wprowadzać pytania za pomocą konwersacyjnego stylu tekstu lub wyszukiwania słów kluczowych, `How do I add a toner cartridge to my printer?` takich jak `toner cartridge`. Baza wiedzy powinna mieć oba style pytań, aby poprawnie zwrócić najlepszą odpowiedź. Jeśli nie masz pewności, jakie słowa kluczowe wprowadza klient, użyj danych usługi Application Insights do analizowania zapytań.

### <a name="good-answers"></a>Dobre odpowiedzi

Najlepsze odpowiedzi są proste odpowiedzi, ale nie zbyt proste. Nie używaj odpowiedzi, `yes` `no`takich jak i . Jeśli odpowiedź powinna zawierać łącze do innych źródeł lub zapewniać bogate środowisko z multimediami i łączami, użyj [tagowania metadanych,](../how-to/edit-knowledge-base.md#add-metadata) aby odróżnić odpowiedzi, a następnie [prześlij zapytanie](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) z metadanymi tagami we `strictFilters` właściwości, aby uzyskać poprawną wersję odpowiedzi.

|Odpowiedź|Monity follup-up|
|--|--|
|Wyłącz laptop Surface za pomocą przycisku zasilania na klawiaturze.|* Kombinacje klawiszy do uśpienia, wyłączania i ponownego uruchamiania.<br>* Jak uruchomić komputer przenośny Surface<br>* Jak zmienić BIOS dla laptopa Surface<br>* Różnice między uśpienia, zamknij i restart|
|Obsługa klienta jest dostępna przez telefon, Skype i wiadomość tekstową 24 godziny na dobę.|* Informacje kontaktowe dla sprzedaży.<br> * Biura i sklepy lokalizacje i godziny wizyty osobiście.<br> * Akcesoria do laptopa Surface.|

## <a name="chit-chat"></a>Chit-Czat
Dodaj chit-chat do swojego bota, aby twój bot był bardziej konwersacyjny i angażujący, przy niskim wysiłku. Podczas tworzenia bazy wiedzy można łatwo dodawać zestawy danych chit-chat z wstępnie zdefiniowanych osobowości i zmieniać je w dowolnym momencie. Dowiedz się, jak [dodać chit-chat do bazy wiedzy.](../How-To/chit-chat-knowledge-base.md)

Chit-chat jest obsługiwany w [wielu językach.](../how-to/chit-chat-knowledge-base.md#language-support)

### <a name="choosing-a-personality"></a>Wybór osobowości
Chit-chat jest obsługiwany dla kilku wstępnie zdefiniowanych osobowości:

|Osobowości |Plik zestawu danych QnA Maker |
|---------|-----|
|Professional Edition |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Przyjazny |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Dowcipny |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Opiekuńczy |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entuzjastycznie |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Odpowiedzi wahają się od formalnych do nieformalnych i lekceważących. Należy wybrać osobowość, która jest ściśle dopasowana do tonu, który chcesz dla bota. Można wyświetlić [zestawy danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)i wybrać taki, który służy jako podstawa dla bota, a następnie dostosować odpowiedzi.

### <a name="edit-bot-specific-questions"></a>Edytowanie pytań specyficznych dla bota
Istnieje kilka pytań specyficznych dla bota, które są częścią zestawu danych chit-chat i zostały wypełnione ogólnymi odpowiedziami. Zmień te odpowiedzi, aby jak najlepiej odzwierciedlać szczegóły bota.

Zalecamy, aby następujące chit-chat QnAs bardziej szczegółowe:

* Kim jesteś?
* Co możesz zrobić?
* How old are you? (Ile masz lat)?
* Kto cię stworzył?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Dodawanie niestandardowego chit-chatu z tagiem metadanych

Jeśli dodasz własne pary QnA chit-chat, upewnij się, że dodasz metadane, aby te odpowiedzi zostały zwrócone. Para nazwa/wartość metadanych to `editorial:chitchat`.

## <a name="searching-for-answers"></a>Wyszukiwanie odpowiedzi

GenerateAnswer API używa zarówno pytań, jak i odpowiedzi, aby wyszukać najlepsze odpowiedzi na zapytanie użytkownika.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Wyszukiwanie pytań tylko wtedy, gdy odpowiedź nie jest istotna

Użyj, [`RankerType=QuestionOnly`](#choosing-ranker-type) jeśli nie chcesz wyszukiwać odpowiedzi.

Przykładem tego jest, gdy baza wiedzy jest katalog akronimów jako pytania z ich pełną formą jako odpowiedź. Wartość odpowiedzi nie pomoże w poszukiwaniu odpowiedniej odpowiedzi.

## <a name="rankingscoring"></a>Ranking/Punktacja
Upewnij się, że najlepiej wykorzystujesz funkcje rankingowe, które obsługuje QnA Maker. Spowoduje to zwiększenie prawdopodobieństwa, że zapytanie danego użytkownika zostanie odebrane odpowiednią odpowiedzią.

### <a name="choosing-a-threshold"></a>Wybór progu

Domyślny [wynik ufności,](confidence-score.md) który jest używany jako próg wynosi 0, jednak można [zmienić próg](confidence-score.md#set-threshold) bazy wiedzy w zależności od potrzeb. Ponieważ każda kb jest inna, należy przetestować i wybrać próg, który jest najlepiej odpowiedni dla bazy wiedzy.

### <a name="choosing-ranker-type"></a>Wybieranie typu rankera
Domyślnie QnA Maker przeszukuje pytania i odpowiedzi. Jeśli chcesz przeszukiwać tylko pytania, aby wygenerować odpowiedź, użyj `RankerType=QuestionOnly` w treści POST żądania GenerateAnswer.

### <a name="add-alternate-questions"></a>Dodawanie pytań alternatywnych
[Pytania alternatywne](../How-To/edit-knowledge-base.md) zwiększają prawdopodobieństwo dopasowania do kwerendy użytkownika. Alternatywne pytania są przydatne, gdy istnieje wiele sposobów, w którym można zadać to samo pytanie. Może to obejmować zmiany w strukturze zdania i stylu wyrazu.

|Oryginalna kwerenda|Kwerendy alternatywne|Change|
|--|--|--|
|Czy parking jest dostępny?|Czy masz parking?|struktura zdań|
 |Cześć|Yo<br>Hej tam!|styl słowny lub slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrowanie pytań i odpowiedzi za pomocą tagów metadanych

[Metadane](../How-To/edit-knowledge-base.md) dodaje możliwość aplikacji klienckiej wiedzieć, że nie należy przyjmować wszystkie odpowiedzi, ale zamiast zawęzić wyniki kwerendy użytkownika na podstawie tagów metadanych. Odpowiedź bazy wiedzy może się różnić w zależności od tagu metadanych, nawet jeśli kwerenda jest taka sama. Na przykład *"gdzie znajduje się parking"* może mieć inną odpowiedź, jeśli lokalizacja oddziału restauracji jest inna - oznacza to, że metadane to *Lokalizacja: Seattle* kontra *Lokalizacja: Redmond*.

### <a name="use-synonyms"></a>Używanie synonimów
Chociaż istnieje pewne wsparcie dla synonimów w języku angielskim, użyj zmian wyrazów bez uwzględniania wielkości liter za pośrednictwem [interfejsu API zmiany,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) aby dodać synonimy do słów kluczowych, które przyjmują inną formę. Synonimy są dodawane na poziomie usług QnA Maker i współużytkowane przez wszystkie bazy wiedzy w usłudze.

|Oryginalne słowo|Synonimy|
|--|--|
|Kupię|zakup<br>bankowość netto<br>bankowość netto|

### <a name="use-distinct-words-to-differentiate-questions"></a>Używanie różnych wyrazów do rozróżniania pytań
Algorytm rankingowy QnA Maker, który pasuje do zapytania użytkownika z pytaniem w bazie wiedzy, działa najlepiej, jeśli każde pytanie odpowiada na inną potrzebę. Powtarzanie tego samego zestawu wyrazów między pytaniami zmniejsza prawdopodobieństwo wybrania właściwej odpowiedzi dla danego zapytania użytkownika z tymi słowami.

Na przykład możesz mieć dwa oddzielne qna z następującymi pytaniami:

|QnAs (QnA)|
|--|
|gdzie znajduje się *parking*|
|gdzie znajduje się *bankomat*|

Ponieważ te dwa QnA są sformułowane z bardzo podobnych słów, podobieństwo to może spowodować bardzo podobne wyniki dla wielu zapytań użytkowników, które są sformułowane jak *"gdzie jest `<x>` lokalizacja"*. Zamiast tego spróbuj wyraźnie odróżnić się od zapytań, takich jak *"gdzie jest parking"* i *"gdzie jest bankomat",* unikając słów takich jak "lokalizacja", które mogą znajdować się w wielu pytaniach w KB.

## <a name="collaborate"></a>Współpraca
QnA Maker umożliwia użytkownikom [współpracę](../How-to/collaborate-knowledge-base.md) nad bazą wiedzy. Użytkownicy potrzebują dostępu do grupy zasobów programu Azure QnA Maker, aby uzyskać dostęp do baz wiedzy. Niektóre organizacje mogą chcieć zlecić edycję i konserwację bazy wiedzy i nadal mieć możliwość ochrony dostępu do swoich zasobów platformy Azure. Ten model edytora-osoba zatwierdzająca odbywa się przez skonfigurowanie dwóch identycznych [usług QnA Maker](../How-to/set-up-qnamaker-service-azure.md) w różnych subskrypcjach i wybranie jednej dla cyklu testowania edycji. Po zakończeniu testowania zawartość bazy wiedzy są przesyłane za pomocą procesu [importu i eksportu](../Tutorials/migrate-knowledge-base.md) do usługi QnA Maker osoby zatwierdzającej, która ostatecznie opublikuje bazę wiedzy i zaktualizuje punkt końcowy.



## <a name="active-learning"></a>Aktywna nauka

[Aktywne uczenie się](../How-to/use-active-learning.md) najlepiej sugeruje alternatywne pytania, gdy ma szeroki zakres jakości i ilości zapytań opartych na użytkownikach. Ważne jest, aby umożliwić zapytania użytkowników aplikacji klienckich do udziału w aktywnej pętli sprzężenia zwrotnego uczenia się bez cenzury. Po zasugerowaniu pytań w portalu QnA Maker możesz **[filtrować według sugestii,](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** a następnie przeglądać i akceptować lub odrzucać te sugestie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Edytowanie bazy wiedzy](../How-to/edit-knowledge-base.md)
