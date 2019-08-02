---
title: Najlepsze praktyki — LUIS
titleSuffix: Azure Cognitive Services
description: Poznaj najlepsze rozwiązania usługi LUIS w celu uzyskania najlepszych wyników z aplikacją usługi LUIS modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7a2802bd4daa1a009c610688120c9a56583b054f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639278"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Najlepsze rozwiązania dotyczące tworzenia aplikacji interpretacji języka, dzięki usługom Cognitive Services
Tworzenie aplikacji LUIS za pomocą procesu tworzenia aplikacji: 

* Tworzenie modelu językowego
* Dodawanie wypowiedzi przykładu w usłudze kilka szkolenia (10 – 15 na intencje)
* Publikowanie 
* Test z punktu końcowego 
* Dodawanie funkcji

Po opublikowaniu aplikacji [](luis-how-to-publish-app.md)należy użyć cyklu tworzenia do dodawania funkcji, publikowania i testowania z punktu końcowego. Nie zaczynają się na następny cykl i tworzenia pakietów administracyjnych, dodając więcej wypowiedzi przykładu. Nie zezwala, LUIS, Dowiedz się, modelu za pomocą wypowiedzi rzeczywistych użytkowników. 

Aby usługa LUIS to wydajny w jego zadania uczenia nie rozwijaj wypowiedzi aż bieżący zestaw wypowiedzi punktu końcowego i przykładowe są zwraca wyniki prognozowania pewność, wysoka. Popraw wyniki przy użyciu [aktywnego uczenia](luis-concept-review-endpoint-utterances.md), [wzorców](luis-concept-patterns.md)i [list fraz](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Wykonaj, a nie
Poniższa lista zawiera najlepsze rozwiązania dotyczące aplikacji usługi LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Definiowanie różnych intencji](#do-define-distinct-intents) |[Dodawanie wielu wypowiedzi przykład do intencji](#dont-add-many-example-utterances-to-intents) |
|[Odnalezienia punktu sweet między zbyt ogólne i zbyt specyficzny dla każdego intencji](#do-find-sweet-spot-for-intents)|[Użyj usługi LUIS jako platforma szkoleniowa](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne tworzenie aplikacji](#do-build-the-app-iteratively)|[Dodawanie wielu wypowiedzi przykład o tym samym formacie, ignorowanie innych formatów](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodawanie listy frazy i wzorców w późniejszej iteracji](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Mieszanie definicji intencje i podmioty](#dont-mix-the-definition-of-intents-and-entities)|
|[Zrównoważ swój wyrażenia długości we wszystkich intencjach](#balance-your-utterances-across-all-intents) , z wyjątkiem intencji none.<br>[Dodawanie wypowiedzi przykładzie Brak elementu intent](#do-add-example-utterances-to-none-intent)|[Tworzenie list frazy za wszystkie możliwe wartości](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Korzystać z tej funkcji sugerowanej dla aktywne uczenie](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj zbyt wiele wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji](#do-monitor-the-performance-of-your-app)|[Uczenie i publikowanie za pomocą każdego wypowiedź jednym przykładzie dodano](#dont-train-and-publish-with-every-single-example-utterance)|
|[Użyj wersji dla każdej iteracji aplikacji](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definiowanie różnych intencji
Upewnij się, że słownika dla każdego celem jest po prostu na tym przeznaczeniem i nie nakładających się przy użyciu innego zamiaru. Na przykład jeśli chcesz mieć aplikację, która obsługuje układy podróży, takie jak loty lotnicze i Hotele, możesz wybierać te obszary tematyczne jako osobne intencje lub te same cele z jednostkami dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwa między dwiema opcjami jest taka sama, Połącz intencji i korzystanie z jednostek. 

Należy wziąć pod uwagę następujące wypowiedzi przykładu:

|Przykładowe wypowiedzi|
|--|
|Zarezerwuj lot|
|Zarezerwuj hotelu|

"Zarezerwuj lot" i "Zarezerwuj hotel" Używanie tego samego słownictwa "book". Ten format jest taki sam, więc powinien być taki sam, jak w przypadku różnych wyrazów lotów i hotelu jako wyodrębnionych jednostek. 

Informacje dodatkowe:
* Związane [Pojęcia dotyczące intencji w aplikacji LUIS](luis-concept-intent.md)
* Samouczek: [Kompiluj aplikację LUIS, aby określić intencje użytkownika](luis-quickstart-intents-only.md)
* Instrukcje: [Dodawanie intencji w celu określenia zamiaru użytkownika wyrażenia długości](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Znaleźć miejsce sweet intencji
Użyj danymi przewidywań, Luis, aby określić, jeśli nakładają się na Twoje intencje. Nakładające się intencje odmylić LUIS. Wynik jest zbyt Zamknij, aby się u góry oceniania intencji inną intencji. Nakładające się intencji, ponieważ usługa LUIS nie używa dokładnie takiej samej ścieżce danych szkoleniowych każdorazowo, ma prawdopodobieństwo przy pierwszej lub drugiej szkolenia. Chcesz, aby wyniki wypowiedź dla każdego zamiaru były od siebie oddzielone, aby nie nastąpiło Przerzucanie/flop. Dobre rozróżnienie na intencje powinno spowodować oczekiwanego intencji najważniejsze każdym razem, gdy. 
 
## <a name="do-build-the-app-iteratively"></a>Iteracyjne tworzenie aplikacji
Przechowuj oddzielny zestaw wyrażenia długości, który nie jest używany jako [przykład wyrażenia długości](luis-concept-utterance.md) lub Endpoint wyrażenia długości. Ulepszaj aplikacji zestawu testowego. Dostosuj ustawienie do odzwierciedlenia wypowiedzi rzeczywistego użytkownika testu. Ten zestaw testów służy do obliczania każdej iteracji lub wersji aplikacji. 

Deweloperzy powinni mieć trzy zestawów danych. Pierwsza to wypowiedzi przykład do tworzenia modelu. Druga jest przeznaczona dla testowania modelu w punkcie końcowym. Trzecia będzie niewidomych test dane używane w [testowania partii](luis-how-to-batch-test.md). Ten ostatni zestaw nie jest używany w szkoleniu aplikacji ani wysyłania jej w punkcie końcowym.  

Informacje dodatkowe:
* Związane [Cykl tworzenia aplikacji LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Dodawanie listy frazy i wzorców w późniejszej iteracji

Najlepszym rozwiązaniem jest niestosowanie tych zasad przed przetestowaniem aplikacji. Należy zrozumieć, jak działa aplikacja przed dodaniem [list](luis-concept-feature.md) i [wzorców](luis-concept-patterns.md) frazy, ponieważ te funkcje są ważone bardziej silnie niż przykład wyrażenia długości i pochylenie pozostanie niebezpieczne. 

Po zrozumieniu, jak działa aplikacja, Dodaj każdą z tych funkcji, które mają zastosowanie do aplikacji. Nie trzeba dodawać tych funkcji razem z każdą iteracją [](luis-concept-app-iteration.md) ani zmieniać funkcji w każdej wersji. 

Nie trzeba dodawać ich na początku projektu modelu, ale łatwiej jest zobaczyć, jak każda z nich zmienia wyniki po przetestowaniu modelu z wyrażenia długości. 

Najlepszym rozwiązaniem jest przetestowanie za pośrednictwem [punktu końcowego](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) , aby uzyskać dodatkową korzyść z [aktywnego uczenia](luis-concept-review-endpoint-utterances.md). [Okienko testowanie interaktywne](luis-interactive-test.md) jest również prawidłową metodologią testu. 
 

### <a name="phrase-lists"></a>Listy fraz

[Frazę list](luis-concept-feature.md) umożliwiają definiowanie słowników wyrazy związane z domeny aplikacji. Wypisz swoją listę wyrazów za pomocą kilku słów, a następnie użyj funkcji Sugeruj, aby LUIS wie o większej liczbie wyrazów specyficznych dla aplikacji. Lista fraz usprawnia wykrywanie zamierzeń i klasyfikację jednostek przez zwiększenie sygnału związanego z wyrazami lub frazami, które są istotne dla Twojej aplikacji. 

Nie dodawaj każdy wyraz słownictwa, ponieważ lista wyrażenie nie jest dokładne dopasowanie. 

Informacje dodatkowe:
* Związane [Funkcje list fraz w aplikacji LUIS](luis-concept-feature.md)
* Porady: [Użyj list fraz, aby zwiększyć liczbę sygnałów listy słów](luis-how-to-add-features.md)

### <a name="patterns"></a>Wzorce

Wypowiedzi użytkowników z punktu końcowego, bardzo podobne do siebie nawzajem, może spowodować ujawnienie wzorców wybór programu word i umieszczania. [Wzorzec](luis-concept-patterns.md) funkcja przyjmuje ten wybór programu word i umieszczania wraz z wyrażeń regularnych w celu zwiększenia dokładności prognozy. We wzorcu wyrażenia regularnego umożliwia słów i znaki interpunkcyjne, które zamierzasz Ignoruj podczas nadal pasujących do wzorca. 

Użyj opcjonalnej [składni](luis-concept-patterns.md) wzorca dla interpunkcji, aby można było zignorować interpunkcję. Użyj [jawnej listy](luis-concept-patterns.md#explicit-lists) , aby skompensować wzorzec. wszelkie problemy z składnią. 

Informacje dodatkowe:
* Związane [Wzorce poprawiają dokładność przewidywania](luis-concept-patterns.md)
* Porady: [Jak dodać wzorce, aby poprawić dokładność przewidywania](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Zrównoważ swój wyrażenia długości na wszystkie intencje

Aby przewidywania LUIS były dokładne, liczba przykładowych wyrażenia długości w każdym zamiarie (z wyjątkiem intencji None) musi być stosunkowo równa. 

Jeśli masz zamiar z 100 przykładem wyrażenia długości i zamiarem z 20 przykład wyrażenia długości, zamiaru 100-wypowiedź będzie miała wyższą stawkę prognozowania.  

## <a name="do-add-example-utterances-to-none-intent"></a>Dodawanie wypowiedzi przykładzie Brak elementu intent

To zamierzenie jest zamiarem rezerwowym, które wykazało wszystko poza aplikacją. Dodaj jeden wypowiedź przykład intencji dla każdego wypowiedzi przykład 10 w pozostałej części aplikacją usługi LUIS None.

Informacje dodatkowe:
* Związane [Dowiedz się, co to jest dobry wyrażenia długości dla aplikacji LUIS](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystać z funkcji sugerowanej aktywne uczenie

Użyj [aktywne uczenie](luis-how-to-review-endpoint-utterances.md)firmy **Przejrzyj wypowiedzi punktu końcowego** na bieżąco, zamiast opcji dodawania więcej wypowiedzi przykład do intencji. Ponieważ aplikacja otrzymuje stale wypowiedzi punktu końcowego, ta lista jest rosnący i zmianę.

Informacje dodatkowe:
* Związane [Pojęcia dotyczące włączania aktywnego uczenia przez przeglądanie punktu końcowego wyrażenia długości](luis-concept-review-endpoint-utterances.md)
* Samouczek: [Samouczek: Usuń niegwarantowane przewidywania, przeglądając punkt końcowy wyrażenia długości](luis-tutorial-review-endpoint-utterances.md)
* Porady: [Jak przejrzeć program Endpoint wyrażenia długości w portalu LUIS](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji

Monitoruj dokładność przewidywania przy użyciu zestawu [testów wsadowych](luis-concept-batch-test.md) . 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wiele wypowiedzi przykład do intencji

Po opublikowaniu aplikacji tylko dodawanie wypowiedzi z aktywne uczenie w proces iteracyjny. W przypadku zbyt podobne wypowiedzi Dodawanie wzorca. 

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

Chatbot, który będzie Zarezerwuj loty, można utworzyć **BookFlight** intencji. Nie należy tworzyć intencji dla każdej linii lotniczych lub każdego miejsca docelowego. Użyj tych elementów danych jako [jednostek](luis-concept-entity-types.md) i oznacz je w wypowiedzi przykładu. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nie należy tworzyć przy użyciu wszystkich możliwych wartości listy fraz

Zapewnia kilka przykładów w [frazę list](luis-concept-feature.md) , ale nie każdy wyraz. Usługa LUIS stanowi uogólnienie i uwzględnia kontekstu. 

## <a name="dont-add-many-patterns"></a>Nie należy dodawać wielu wzorców

Nie dodawaj zbyt wiele [wzorców](luis-concept-patterns.md). Usługa LUIS jest przeznaczona do Dowiedz się szybko, wraz z przykładami mniejszej liczby. Nie doprowadzić do przeciążenia systemu niepotrzebnie.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie uczenie i publikowanie przy użyciu każdego pojedynczego przykład wypowiedź

Dodawanie wypowiedzi 10 lub 15 przed szkolenia i publikowania. Pozwala wyświetlić wpływ na dokładność prognozowania. Dodawanie pojedynczego wypowiedź nie może mieć widoczne wpływ na wynik. 

## <a name="do-use-versions-for-each-app-iteration"></a>Użyj wersji dla każdej iteracji aplikacji

Każdy cykl tworzenia powinien znajdować się w nowej [wersji](luis-concept-version.md), sklonowany z istniejącej wersji. LUIS nie ma limitu dla wersji. Nazwa wersji jest używana jako część trasy interfejsu API, co jest ważne, aby można było wybrać znaki dozwolone w adresie URL, a także zachować w ciągu 10 znaków dla wersji. Utwórz strategię nazw wersji, aby zachować swoją organizację. 

Informacje dodatkowe:
* Związane [Informacje o tym, jak i kiedy używać wersji LUIS](luis-concept-version.md)
* Porady: [Używanie wersji do edytowania i testowania bez wpływu na aplikacje tymczasowe i produkcyjne](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [Planowanie aplikacji](luis-how-plan-your-app.md) w aplikacją usługi LUIS.
