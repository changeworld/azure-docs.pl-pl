---
title: Najlepsze praktyki
titleSuffix: Language Understanding - Azure Cognitive Services
description: Poznaj najlepsze rozwiązania usługi LUIS w celu uzyskania najlepszych wyników z aplikacją usługi LUIS modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: diberry
ms.openlocfilehash: 9a6f9d54c52f36b8f709eacaf25d3fea31dbe516
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812914"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Najlepsze rozwiązania dotyczące tworzenia aplikacji interpretacji języka, dzięki usługom Cognitive Services
Użyj procesu tworzenia aplikacji do tworzenia aplikacji usługi LUIS. 

* Tworzenie modelu językowego
* Dodawanie wypowiedzi przykładu w usłudze kilka szkolenia (10 – 15 na intencje)
* Publikowanie 
* Test z punktu końcowego 
* Dodawanie funkcji

Gdy Twoja aplikacja jest [opublikowane](luis-how-to-publish-app.md), użyj cyklu tworzenia Dodaj funkcje, publikowanie i testowanie z punktu końcowego. Nie zaczynają się na następny cykl i tworzenia pakietów administracyjnych, dodając więcej wypowiedzi przykładu. Nie zezwala, LUIS, Dowiedz się, modelu za pomocą wypowiedzi rzeczywistych użytkowników. 

Aby usługa LUIS to wydajny w jego zadania uczenia nie rozwijaj wypowiedzi aż bieżący zestaw wypowiedzi punktu końcowego i przykładowe są zwraca wyniki prognozowania pewność, wysoka. Poprawić wyniki za pomocą [aktywne uczenie](luis-concept-review-endpoint-utterances.md), [wzorców](luis-concept-patterns.md), i [frazę list](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Wykonaj, a nie
Poniższa lista zawiera najlepsze rozwiązania dotyczące aplikacji usługi LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Definiowanie różnych intencji](#do-define-distinct-intents) |[Dodawanie wielu wypowiedzi przykład do intencji](#dont-add-many-example-utterances-to-intents) |
|[Odnalezienia punktu sweet między zbyt ogólne i zbyt specyficzny dla każdego intencji](#do-find-sweet-spot-for-intents)|[Użyj usługi LUIS jako platforma szkoleniowa](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne tworzenie aplikacji](#do-build-the-app-iteratively)|[Dodawanie wielu wypowiedzi przykład o tym samym formacie, ignorowanie innych formatów](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodawanie listy frazy i wzorców w późniejszej iteracji](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Mieszanie definicji intencje i podmioty](#dont-mix-the-definition-of-intents-and-entities)|
|[Saldo swoje wypowiedzi we wszystkich intencji](#balance-your-utterances-across-all-intents) , z wyjątkiem intencji None.<br>[Dodawanie wypowiedzi przykładzie Brak elementu intent](#do-add-example-utterances-to-none-intent)|[Tworzenie list frazy za wszystkie możliwe wartości](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Korzystać z tej funkcji sugerowanej dla aktywne uczenie](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj zbyt wielu wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji](#do-monitor-the-performance-of-your-app)|[Uczenie i publikowanie za pomocą każdego wypowiedź jednym przykładzie dodano](#dont-train-and-publish-with-every-single-example-utterance)|
|[Użyj wersji dla każdej iteracji aplikacji](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definiowanie różnych intencji
Upewnij się, że słownika dla każdego celem jest po prostu na tym przeznaczeniem i nie nakładających się przy użyciu innego zamiaru. Na przykład jeśli chcesz mieć aplikację, że uchwyty podróży uzgodnień, takich jak linii lotniczych lotów i hoteli, można mieć tych dziedzin jako osobne intencji lub ten sam cel z jednostkami dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwa między dwiema opcjami jest taka sama, Połącz intencji i korzystanie z jednostek. 

Należy wziąć pod uwagę następujące wypowiedzi przykładu:

|Przykładowe wypowiedzi|
|--|
|Zarezerwuj lot|
|Zarezerwuj hotelu|

"Zarezerwuj lot" i "Zarezerwuj hotel" Używanie tego samego słownictwa "book". Ten format jest taka sama, więc powinno być tego samego intencji z innych wyrazów lotów i hoteli jako wyodrębnione jednostki. 

Informacje dodatkowe:
* Pojęcia: [Pojęcia dotyczące intencje w aplikacją usługi LUIS](luis-concept-intent.md)
* Samouczek: [Tworzenie aplikacji usługi LUIS Określanie zamiarach użytkownika](luis-quickstart-intents-only.md)
* Instrukcje: [Dodawanie intencji, aby określić użytkownika zamiar wypowiedzi](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Znaleźć miejsce sweet intencji
Użyj danymi przewidywań, Luis, aby określić, jeśli nakładają się na Twoje intencje. Nakładające się intencji mylić usługi LUIS. Wynik jest zbyt Zamknij, aby się u góry oceniania intencji inną intencji. Nakładające się intencji, ponieważ usługa LUIS nie używa dokładnie takiej samej ścieżce danych szkoleniowych każdorazowo, ma prawdopodobieństwo przy pierwszej lub drugiej szkolenia. Chcesz, aby wynik wypowiedź dla każdego zamiar być dalej od siebie, więc nie jest realizowane tej drugiej/Odwróć w pionie. Dobre rozróżnienie na intencje powinno spowodować oczekiwanego intencji najważniejsze każdym razem, gdy. 
 
## <a name="do-build-the-app-iteratively"></a>Iteracyjne tworzenie aplikacji
Zachowaj osobny zestaw nie jest używana jako wypowiedzi [wypowiedzi przykład](luis-concept-utterance.md) lub wypowiedzi punktu końcowego. Ulepszaj aplikacji zestawu testowego. Dostosuj ustawienie do odzwierciedlenia wypowiedzi rzeczywistego użytkownika testu. Użyj tego testu, zestawu do oceny każdej iteracji lub wersji aplikacji. 

Deweloperzy powinni mieć trzy zestawów danych. Pierwsza to wypowiedzi przykład do tworzenia modelu. Druga jest przeznaczona dla testowania modelu w punkcie końcowym. Trzecia będzie niewidomych test dane używane w [testowania partii](luis-how-to-batch-test.md). Ten ostatni zestaw nie jest używany w szkolenia aplikacji ani wysyłane w punkcie końcowym.  

Informacje dodatkowe:
* Pojęcia: [Cykl tworzenia aplikacji usługi LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Dodawanie listy frazy i wzorców w późniejszej iteracji

Najlepszym rozwiązaniem jest nie chcesz stosować te rozwiązania przed aplikacja została przetestowana. Należy zrozumieć sposób działania aplikacji przed dodaniem list frazy i wzorce, ponieważ te funkcje mają większą wagę niż przykład wypowiedzi i będzie pochylanie zaufania. 

Po zapoznaniu się, jak aplikacja zachowuje się bez nich, należy dodać każdą z tych funkcji odnoszących się do aplikacji. Nie trzeba dodawać te funkcje z każdym [iteracji](luis-concept-app-iteration.md) lub zmieniać funkcje z każdą wersją. 

Nie powoduje żadnych problemów, dodając je na początku projektu modelu, ale znacznie łatwiej zobaczyć każdej funkcji zmiany wyników po wypowiedzi została przetestowana dla modelu. 

Najlepszym rozwiązaniem jest przetestowanie za pośrednictwem [punktu końcowego](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) tak, aby uzyskać jednocześnie ma dodatkową zaletę [aktywne uczenie](luis-concept-review-endpoint-utterances.md). [Interaktywne okienko testowania](luis-interactive-test.md) jest również metodologii prawidłowej testów. 
 

### <a name="phrase-lists"></a>Listy fraz

[Frazę list](luis-concept-feature.md) umożliwiają definiowanie słowników wyrazy związane z domeny aplikacji. Inicjator frazy Twojej listy za pomocą kilku słowach, a następnie korzystać z funkcji sugerowanej, więc LUIS obsługującemu więcej słów w określonych słownictwa aplikacji. Lista fraz zwiększa intencji wykrywania i klasyfikacji jednostki, ulepszanie sygnał skojarzony z słów i fraz, które są istotne dla twojej aplikacji. 

Nie dodawaj każdy wyraz słownictwa, ponieważ lista wyrażenie nie jest dokładne dopasowanie. 

Informacje dodatkowe:
* Pojęcia: [Wyrażenie funkcji listy aplikacją usługi LUIS](luis-concept-feature.md)
* Porady: [Użyj frazy Wyświetla sygnału boost listy programu word](luis-how-to-add-features.md)

### <a name="patterns"></a>Wzorce

Wypowiedzi użytkowników z punktu końcowego, bardzo podobne do siebie nawzajem, może spowodować ujawnienie wzorców wybór programu word i umieszczania. [Wzorzec](luis-concept-patterns.md) funkcja przyjmuje ten wybór programu word i umieszczania wraz z wyrażeń regularnych w celu zwiększenia dokładności prognozy. We wzorcu wyrażenia regularnego umożliwia słów i znaki interpunkcyjne, które zamierzasz Ignoruj podczas nadal pasujących do wzorca. 

Użyj wzorca [opcjonalnych składni](luis-concept-patterns.md) znaki interpunkcyjne, dzięki czemu można zignorować znaki interpunkcyjne. Użyj [jawną listę](luis-concept-patterns.md#explicit-lists) celu kompensacji pattern.any składni problemów. 

Informacje dodatkowe:
* Pojęcia: [Wzorce zwiększenia dokładności prognozy](luis-concept-patterns.md)
* Porady: [Jak dodać wzorców w celu zwiększenia dokładności prognozy](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Saldo swoje wypowiedzi we wszystkich intencji

Aby prognoz usługi LUIS muszą być dokładne ilość przykład wypowiedzi w każdym intencji (z wyjątkiem Brak intencji), musi być stosunkowo równy. 

Jeśli masz intencji ze 100 przykład wypowiedzi i przeznaczenie z 20 wypowiedzi przykład intencji wypowiedź 100 będzie mieć wyższy stopień prognozowania.  

## <a name="do-add-example-utterances-to-none-intent"></a>Dodawanie wypowiedzi przykładzie Brak elementu intent

Celem tego jest plan awaryjny celem wskazane wszystko poza aplikację. Dodaj jeden wypowiedź przykład intencji dla każdego wypowiedzi przykład 10 w pozostałej części aplikacją usługi LUIS None.

Informacje dodatkowe:
* Pojęcia: [Zrozumienie, co dobre wypowiedzi związanych z aplikacją usługi LUIS](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystać z funkcji sugerowanej aktywne uczenie

Użyj [aktywne uczenie](luis-how-to-review-endpoint-utterances.md)firmy **Przejrzyj wypowiedzi punktu końcowego** na bieżąco, zamiast opcji dodawania więcej wypowiedzi przykład do intencji. Ponieważ aplikacja otrzymuje stale wypowiedzi punktu końcowego, ta lista jest rosnący i zmianę.

Informacje dodatkowe:
* Pojęcia: [Pojęcia dotyczące włączania aktywne uczenie, przeglądając wypowiedzi punktu końcowego](luis-concept-review-endpoint-utterances.md)
* Samouczek: [Samouczek: Napraw prognozy nie wiesz, przeglądając wypowiedzi punktu końcowego](luis-tutorial-review-endpoint-utterances.md)
* Porady: [Jak przeglądać wypowiedzi punktu końcowego w portalu usługi LUIS](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji

Monitorowanie przy użyciu dokładność prognozowania [partii testów](luis-concept-batch-test.md) zestawu. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wiele wypowiedzi przykład do intencji

Po opublikowaniu aplikacji tylko dodawanie wypowiedzi z aktywne uczenie w proces iteracyjny. W przypadku zbyt podobne wypowiedzi Dodawanie wzorca. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj usługi LUIS jako platforma szkoleniowa

Usługa LUIS jest specyficzne dla domeny model języka. Nie jest on przeznaczony do pracy jako platforma szkoleniowa ogólne języka naturalnego. 

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

## <a name="do-use-versions-for-each-app-iteration"></a>Należy używać wersji dla każdej iteracji aplikacji

Każdy cykl tworzenia powinna mieścić się w nowej [wersji](luis-concept-version.md), sklonowany z istniejącą wersję. Usługa LUIS nie ma limitu dla wersji. Nazwa wersji jest używany jako część trasy interfejsu API, tak ważne jest, aby wybrać znaków dozwoloną w adresie URL, a także przechowywanie w ramach liczby 10 znaków dla wersji. Opracowanie strategii nazwy wersji o zachowaniu wersji zorganizowane. 

Informacje dodatkowe:
* Pojęcia: [Zrozumienie, jak i kiedy używać wersji usługi LUIS](luis-concept-version.md)
* Porady: [Użyj wersji do edycji i przetestować bez wywierania wpływu na aplikacje przejściowych lub produkcyjnych](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Planowanie aplikacji](luis-how-plan-your-app.md) w aplikacją usługi LUIS.
