---
title: Omówienie usługi LUIS najlepszych rozwiązań — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, usługa LUIS najlepsze rozwiązania w celu uzyskania najlepszych wyników.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: 81a2523f37cac926d9f498953a831c2d877a7528
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224011"
---
# <a name="best-practices"></a>Najlepsze praktyki
Użyj procesu tworzenia aplikacji do tworzenia aplikacji usługi LUIS. 

* Tworzenie modelu językowego
* Dodawanie wypowiedzi przykładu w usłudze kilka szkolenia (10 – 15 na intencje)
* Publikowanie 
* Test z punktu końcowego 
* Dodawanie funkcji

Gdy Twoja aplikacja jest [opublikowane](luis-how-to-publish-app.md), użyj cyklu tworzenia Dodaj funkcje, publikowanie i testowanie z punktu końcowego. Nie zaczynają się na następny cykl i tworzenia pakietów administracyjnych, dodając więcej wypowiedzi przykładu. Nie zezwala, LUIS, Dowiedz się, modelu za pomocą wypowiedzi rzeczywistych użytkowników. 

Aby usługa LUIS to wydajny w jego zadania uczenia nie rozwijaj wypowiedzi aż bieżący zestaw wypowiedzi punktu końcowego i przykładowe są zwraca wyniki prognozowania pewność, wysoka. Poprawić wyniki za pomocą aktywne uczenie [wzorców](luis-concept-patterns.md), i [frazę list](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Wykonaj, a nie
Poniższa lista zawiera najlepsze rozwiązania dotyczące aplikacji usługi LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Definiowanie różnych intencji](#do-define-distinct-intents) |[Dodawanie wielu wypowiedzi przykład do intencji](#dont-add-many-example-utterances-to-intents) |
|[Odnalezienia punktu sweet między zbyt ogólne i zbyt specyficzny dla każdego intencji](#do-find-sweet-spot-for-intents)|[Użyj usługi LUIS jako platforma szkoleniowa](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne tworzenie aplikacji](#do-build-the-app-iteratively)|[Dodawanie wielu wypowiedzi przykład o tym samym formacie, ignorowanie innych formatów](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodawanie listy frazy i wzorców w późniejszej iteracji](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Mieszanie definicji intencje i podmioty](#dont-mix-the-definition-of-intents-and-entities)|
|[Dodawanie wypowiedzi przykładzie Brak elementu intent](#do-add-example-utterances-to-none-intent)|[Tworzenie list frazy za wszystkie możliwe wartości](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Korzystać z tej funkcji sugerowanej dla aktywne uczenie](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj tak wielu wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji](#do-monitor-the-performance-of-your-app)|[Uczenie i publikowanie za pomocą każdego wypowiedź jednym przykładzie dodano](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiowanie różnych intencji
Upewnij się, że słownika dla każdego celem jest po prostu na tym przeznaczeniem i nie nakładających się przy użyciu innego zamiaru. Na przykład jeśli chcesz mieć aplikację z obsługi uzgodnienia podróży, takie jak linii lotniczych lotów i hoteli, można ich jako osobne intencji lub ten sam cel z jednostkami dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwa między dwiema opcjami jest taka sama, Połącz intencji i korzystanie z jednostek. 

Należy wziąć pod uwagę następujące wypowiedzi przykładu:

```
Book a flight
Book a hotel
```

"Zarezerwuj lot" i "Zarezerwuj hotel" Używanie tego samego słownictwa "book". Pokrywa to dlatego powinna być taka sama intencji z innych wyrazów lotów i hoteli wyodrębnione jednostek. 

## <a name="do-find-sweet-spot-for-intents"></a>Znaleźć miejsce sweet intencji
Użyj danymi przewidywań, Luis, aby określić, jeśli nakładają się na Twoje intencje. Nakładające się intencji confuses usługi LUIS. Wynik jest zbyt Zamknij, aby się u góry oceniania intencji inną intencji. Nakładające się intencji, ponieważ usługa LUIS nie używa dokładnie takiej samej ścieżce danych szkoleniowych każdorazowo, ma prawdopodobieństwo przy pierwszej lub drugiej szkolenia. Chcesz, aby wynik wypowiedź dla każdego zamiar być dalej od siebie, więc nie jest to realizowane. Dobre rozróżnienie na intencje powinno spowodować oczekiwanego intencji najważniejsze każdym razem, gdy. 
 
## <a name="do-build-the-app-iteratively"></a>Iteracyjne tworzenie aplikacji
Zachowaj oddzielnego *ukryta* przetestować zestaw, który nie jest używany jako [wypowiedzi przykład](luis-concept-utterance.md) lub wypowiedzi punktu końcowego. Ulepszaj aplikacji zestawu testowego. Dostosuj ustawienie do odzwierciedlenia wypowiedzi rzeczywistego użytkownika testu. Należy użyć zestawu do oceny każdej iteracji testu ukryta. 

Deweloperzy powinni mieć trzy zestawów danych. Pierwsza to wypowiedzi przykład do tworzenia modelu. Druga jest przeznaczona dla testowania modelu w punkcie końcowym. Trzecia będzie niewidomych test dane używane w [testowania partii](luis-how-to-batch-test.md). Ten ostatni zestaw jest używany w szkolenia aplikacji ani nie wysyłane w punkcie końcowym.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Dodawanie listy frazy i wzorców w późniejszej iteracji
[Frazę list](luis-concept-feature.md) umożliwiają definiowanie słowników wyrazy związane z domeny aplikacji. Inicjatora swojej frazy wystawić w kilku słowach, a następnie korzystać z funkcji sugerowanej, dzięki czemu usługa LUIS obsługującemu więcej słów w słownictwa. Nie dodawaj każdy wyraz słownictwa, ponieważ lista wyrażenie nie jest dokładne dopasowanie. 

Wypowiedzi użytkowników z punktu końcowego, bardzo podobne do siebie nawzajem, może spowodować ujawnienie wzorców wybór programu word i umieszczania. [Wzorzec](luis-concept-patterns.md) funkcja przyjmuje ten wybór programu word i umieszczania wraz z wyrażeń regularnych w celu zwiększenia dokładności prognozy. We wzorcu wyrażenia regularnego umożliwia słów i znaki interpunkcyjne, które zamierzasz Ignoruj podczas nadal pasujących do wzorca. 

Na użytek deseniu opcjonalnych składni znaków interpunkcyjnych, dzięki czemu można zignorować znaki interpunkcyjne.

Praktyki te nie są stosowane, przed aplikacji ma Odebrane żądania punktu końcowego, ponieważ, pochyla zaufania.  

## <a name="do-add-example-utterances-to-none-intent"></a>Dodawanie wypowiedzi przykładzie Brak elementu intent
To jest plan awaryjny celem wskazane wszystko poza aplikację. Dodaj jeden wypowiedź przykład intencji dla każdego wypowiedzi przykład 10 w pozostałej części aplikacją usługi LUIS None.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystać z funkcji sugerowanej aktywne uczenie
Użyj [aktywne uczenie](luis-how-to-review-endoint-utt.md)firmy **Przejrzyj wypowiedzi punktu końcowego** na bieżąco, zamiast opcji dodawania więcej wypowiedzi przykład do intencji. Ponieważ aplikacja otrzymuje stale wypowiedzi punktu końcowego, ta lista jest rosnący i zmianę.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji
Monitoruj dokładności prognozy przy użyciu zestawu testów. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wiele wypowiedzi przykład do intencji
Po opublikowaniu aplikacji tylko dodawanie wypowiedzi z aktywne uczenie w proces iteracyjny. W przypadku zbyt podobne wypowiedzi Dodawanie wzorca. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj usługi LUIS jako platforma szkoleniowa
Usługa LUIS jest specyficzne dla domeny model języka. Nie jest przeznaczona do pracy jako platforma szkolenie ogólne. 

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

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Planowanie aplikacji](luis-how-plan-your-app.md) w aplikacją usługi LUIS.