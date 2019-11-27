---
title: Najlepsze rozwiązania dotyczące tworzenia aplikacji LUIS
titleSuffix: Azure Cognitive Services
description: Poznaj najlepsze rozwiązania, aby uzyskać najlepsze wyniki z modelu aplikacji LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280925"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Najlepsze rozwiązania dotyczące tworzenia aplikacji do interpretacji języka (LUIS)
Tworzenie aplikacji LUIS za pomocą procesu tworzenia aplikacji: 

* Tworzenie modeli języka (intencje i jednostki)
* Dodaj kilka przykładów szkoleniowych wyrażenia długości (15-30 na intencję)
* Publikowanie w punkcie końcowym
* Test z punktu końcowego 

Po [opublikowaniu](luis-how-to-publish-app.md)aplikacji użyj cyklu życia programowania, aby dodać funkcje, publikować i testować z punktu końcowego. Nie rozpoczynaj następnego cyklu tworzenia przez dodanie więcej przykładowych wyrażenia długości, ponieważ to nie pozwala LUIS na naukę modelu z rzeczywistymi wyrażenia długościami użytkowników. 

Nie rozszerzaj wyrażenia długości do momentu, aż bieżący zestaw obu przykładów i punkt końcowy wyrażenia długości nie zwracają wątpliwości, wysokie wyniki przewidywania. Popraw wyniki przy użyciu [aktywnej nauki](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Wykonaj, a nie
Poniższa lista zawiera najlepsze rozwiązania dotyczące aplikacji usługi LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Definiowanie unikatowych intencji](#do-define-distinct-intents)<br>[Dodaj deskryptory do intencji](#do-add-descriptors-to-intents) |[Dodaj wiele przykładowych wyrażenia długości do intencji](#dont-add-many-example-utterances-to-intents)<br>[Użyj kilku lub prostych jednostek](#dont-use-few-or-simple-entities) |
|[Znajdź słodkie miejsce między ogólnym i zbyt specyficzne dla każdego zamiaru](#do-find-sweet-spot-for-intents)|[Korzystanie z LUIS jako platformy szkoleniowej](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne Kompilowanie aplikacji przy użyciu wersji](#do-build-your-app-iteratively-with-versions)<br>[Kompiluj jednostki na potrzeby dekompozycji modelu](#do-build-for-model-decomposition)|[Dodaj wiele przykładowych wyrażenia długości tego samego formatu, ignorując inne formaty](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodaj wzorce w późniejszych iteracjach](#do-add-patterns-in-later-iterations)|[Mieszanie definicji intencji i jednostek](#dont-mix-the-definition-of-intents-and-entities)|
|[Zrównoważ swój wyrażenia długości we wszystkich intencjach](#balance-your-utterances-across-all-intents) , z wyjątkiem intencji none.<br>[Dodaj przykład wyrażenia długości do opcji none](#do-add-example-utterances-to-none-intent)|[Utwórz deskryptory ze wszystkimi możliwymi wartościami](#dont-create-descriptors-with-all-the-possible-values)|
|[Korzystanie z funkcji Sugeruj w przypadku aktywnego uczenia](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj zbyt wiele wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji za pomocą testów wsadowych](#do-monitor-the-performance-of-your-app)|[Uczenie i publikowanie za pomocą każdego pojedynczego przykładu wypowiedź dodanego](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiowanie różnych intencji
Upewnij się, że słownika dla każdego celem jest po prostu na tym przeznaczeniem i nie nakładających się przy użyciu innego zamiaru. Na przykład jeśli chcesz mieć aplikację, która obsługuje układy podróży, takie jak loty lotnicze i Hotele, możesz wybierać te obszary tematyczne jako osobne intencje lub te same cele z jednostkami dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwa między dwiema opcjami jest taka sama, Połącz intencji i korzystanie z jednostek. 

Należy wziąć pod uwagę następujące wypowiedzi przykładu:

|Przykładowe wypowiedzi|
|--|
|Zarezerwuj lot|
|Zarezerwuj hotelu|

`Book a flight` i `Book a hotel` używają tego samego słownictwa `book a `. Ten format jest taki sam, więc powinien być taki sam, jak w przypadku różnych wyrazów `flight` i `hotel` jako wyodrębnionych jednostek. 

## <a name="do-add-descriptors-to-intents"></a>Dodaj deskryptory do intencji

Deskryptory ułatwiają opisywanie funkcji dla zamiaru. Deskryptor może być listą fraz wyrazów, które są istotne dla tego zamiaru lub jednostki, która jest istotna dla tego celu. 

## <a name="do-find-sweet-spot-for-intents"></a>Znaleźć miejsce sweet intencji
Użyj danymi przewidywań, Luis, aby określić, jeśli nakładają się na Twoje intencje. Nakładające się intencje odmylić LUIS. Wynik jest zbyt Zamknij, aby się u góry oceniania intencji inną intencji. Nakładające się intencji, ponieważ usługa LUIS nie używa dokładnie takiej samej ścieżce danych szkoleniowych każdorazowo, ma prawdopodobieństwo przy pierwszej lub drugiej szkolenia. Chcesz, aby wyniki wypowiedź dla każdego zamiaru były od siebie oddzielone, aby nie nastąpiło Przerzucanie/flop. Dobre rozróżnienie na intencje powinno spowodować oczekiwanego intencji najważniejsze każdym razem, gdy. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Wykonaj iteracyjne Kompilowanie aplikacji przy użyciu wersji

Każdy cykl tworzenia powinien znajdować się w nowej [wersji](luis-concept-version.md), sklonowany z istniejącej wersji. 

## <a name="do-build-for-model-decomposition"></a>Wykonaj kompilację dla dekompozycji modelu

Dekompozycja modelu ma typowy proces:

* Tworzenie **zamiaru** na podstawie założeń użytkownika aplikacji klienta
* Dodaj 15-30 przykład wyrażenia długości na podstawie rzeczywistych danych wejściowych użytkownika
* Oznacz koncepcję danych najwyższego poziomu na przykład wypowiedź
* Podziel koncepcję danych na podskładniki
* Dodawanie deskryptorów (funkcji) do podskładników
* Dodaj deskryptory (funkcje) do celu 

Po utworzeniu zamiaru i dodaniu przykładu wyrażenia długości, w poniższym przykładzie opisano dekompozycję jednostki. 

Zacznij od zidentyfikowania kompletnych koncepcji dotyczących danych, które mają zostać wyodrębnione w wypowiedź. To jest jednostka, którą uczysz. Następnie rozłożyć frazę na części. Obejmuje to zidentyfikowanie podskładników (jako jednostek) wraz z deskryptorami i ograniczeniami. 

Na przykład jeśli chcesz wyodrębnić adres, najpopularniejsza jednostka poznania maszynowego może być wywoływana `Address`. Podczas tworzenia adresu Zidentyfikuj niektóre jego składniki, takie jak adres ulicy, miasto, Województwo i kod pocztowy. 

Kontynuuj składanie tych elementów, **ograniczając** kod pocztowy do wyrażenia regularnego. Rozdziel adres ulicy na części numeru ulicy (przy użyciu wstępnie skompilowanego numeru), nazwy ulicy i typu ulicy. Typ ulicy można opisać przy użyciu listy **deskryptorów** , takiej jak promień, okrąg, droga i tor.

Interfejs API tworzenia w wersji 3 pozwala na dekompozycję modelu. 

## <a name="do-add-patterns-in-later-iterations"></a>Dodaj wzorce w późniejszych iteracjach

Należy zrozumieć, jak działa aplikacja przed dodaniem [wzorców](luis-concept-patterns.md) , ponieważ wzorce są ważone bardziej silnie niż przykład wyrażenia długości i spowodują pochylenie. 

Po zrozumieniu sposobu działania aplikacji Dodaj wzorce, które są stosowane do aplikacji. Nie trzeba dodawać ich razem z każdą [iteracją](luis-concept-app-iteration.md). 

Nie trzeba dodawać ich na początku projektu modelu, ale łatwiej jest zobaczyć, jak każdy wzorzec zmienia model po przetestowaniu modelu z wyrażenia długości. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Aby zrównoważyć wyrażenia długości na wszystkie intencje

Aby przewidywania LUIS były dokładne, liczba przykładowych wyrażenia długości w każdym zamiarie (z wyjątkiem intencji None) musi być stosunkowo równa. 

Jeśli masz zamiar z 100 przykładem wyrażenia długości i zamiarem z 20 przykład wyrażenia długości, zamiaru 100-wypowiedź będzie miała wyższą stawkę prognozowania.  

## <a name="do-add-example-utterances-to-none-intent"></a>Dodawanie wypowiedzi przykładzie Brak elementu intent

Ta metoda jest zamiarem rezerwowym, co oznacza, że wszystko poza aplikacją. Dodaj jeden wypowiedź przykład intencji dla każdego wypowiedzi przykład 10 w pozostałej części aplikacją usługi LUIS None.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystać z funkcji sugerowanej aktywne uczenie

Regularnie korzystaj z **wyrażenia długości punktu końcowego** usługi [Active Learning](luis-how-to-review-endpoint-utterances.md), zamiast dodawać więcej przykładowych wyrażenia długości do zamiar. Ponieważ aplikacja otrzymuje stale wypowiedzi punktu końcowego, ta lista jest rosnący i zmianę.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji

Monitoruj dokładność przewidywania przy użyciu zestawu [testów wsadowych](luis-concept-batch-test.md) . 

Przechowuj oddzielny zestaw wyrażenia długości, które nie są używane jako [przykład wyrażenia długości](luis-concept-utterance.md) lub Endpoint wyrażenia długości. Ulepszaj aplikacji zestawu testowego. Dostosuj ustawienie do odzwierciedlenia wypowiedzi rzeczywistego użytkownika testu. Ten zestaw testów służy do obliczania każdej iteracji lub wersji aplikacji. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wiele wypowiedzi przykład do intencji

Po opublikowaniu aplikacji należy dodać tylko wyrażenia długości z aktywnego uczenia w procesie cyklu życia projektu. W przypadku zbyt podobne wypowiedzi Dodawanie wzorca. 

## <a name="dont-use-few-or-simple-entities"></a>Nie używaj kilku lub prostych jednostek

Jednostki są kompilowane do wyodrębniania i przewidywania danych. Należy pamiętać, że każdy z zamiarów ma jednostki, które opisują dane w zamiarach. Pozwala to LUIS na przewidywalność celu, nawet jeśli aplikacja kliencka nie musi używać wyodrębnionej jednostki. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj usługi LUIS jako platforma szkoleniowa

Usługa LUIS jest specyficzne dla domeny model języka. Nie jest ona przeznaczona do pracy jako ogólna platforma szkoleń w języku naturalnym. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nie dodawaj wiele wypowiedzi przykład o tym samym formacie, ignorowanie innych formatów

Usługa LUIS oczekuje, że różnice między wypowiedzi intencji. Wypowiedzi mogą się różnić, a jednocześnie ma takie samo znaczenie ogólnej. Zmiany mogą obejmować wypowiedź długości, wybór programu word i umieszczania programu word. 

|Nie używaj tego samego formatu|Użyj formatu zmiennych|
|--|--|
|Kup biletu do Seattle<br>Kup biletu do Paryża<br>Kup biletu do Orlando|Kup 1 biletu do Seattle<br>Zarezerwuje dwóch na czerwony oka do Paryża poniedziałku<br>Chcę zarezerwować 3 bilety do Orlando przerwą spring|

Drugiej kolumny używa różne czasowniki (zakup, rezerwy, książki), różnych ilości (1, 3 dwa), a różne rozmieszczenia wyrazów, ale wszystkie mają ten sam zamiar zakupów biletów linii lotniczych dla podróży. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nie można mieszać definicji intencje i podmioty

Utwórz opcję dla każdej akcji, że zajmie bota. Jako parametry, które umożliwiają tę akcję, należy użyć jednostki. 

W przypadku botów, które będą księgować loty lotnicze, należy utworzyć intencję **BookFlight** . Nie należy tworzyć intencji dla każdej linii lotniczych lub każdego miejsca docelowego. Użyj tych elementów danych jako [jednostek](luis-concept-entity-types.md) i oznacz je w przykładowym wyrażenia długości. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Nie twórz deskryptorów ze wszystkimi możliwymi wartościami

Podaj kilka przykładów w [frazach](luis-concept-feature.md) deskryptorów, ale nie każdy wyraz. Usługa LUIS stanowi uogólnienie i uwzględnia kontekstu. 

## <a name="dont-add-many-patterns"></a>Nie należy dodawać wielu wzorców

Nie dodawaj zbyt wielu [wzorców](luis-concept-patterns.md). Usługa LUIS jest przeznaczona do Dowiedz się szybko, wraz z przykładami mniejszej liczby. Nie doprowadzić do przeciążenia systemu niepotrzebnie.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie uczenie i publikowanie przy użyciu każdego pojedynczego przykład wypowiedź

Dodawanie wypowiedzi 10 lub 15 przed szkolenia i publikowania. Pozwala wyświetlić wpływ na dokładność prognozowania. Dodawanie pojedynczego wypowiedź nie może mieć widoczne wpływ na wynik. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zaplanować aplikację](luis-how-plan-your-app.md) w aplikacji Luis.
