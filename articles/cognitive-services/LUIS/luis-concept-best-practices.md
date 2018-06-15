---
title: Zrozumienie LUIS najlepsze rozwiązania w zakresie - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się LUIS najlepsze rozwiązania w celu uzyskania najlepszych wyników.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 729f510de59fe27761389fb1f6edb4025021565a
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356395"
---
# <a name="best-practices"></a>Najlepsze praktyki
Umożliwia tworzenie aplikacji LUIS procesu tworzenia aplikacji. 

* Tworzenie modelu języka
* Dodaj kilka szkolenia zniesławiających przykład (10 – 15 na zamiar)
* Publikowanie 
* Test z punktu końcowego 
* Dodawanie funkcji

Gdy aplikacja jest [opublikowane](publishapp.md), użyj cyklu tworzenia dodać funkcje, publikowanie i test z punktu końcowego. Nie rozpoczęcia następnego cyklu tworzenia pakietów administracyjnych, dodając więcej zniesławiających przykład. LUIS modelu za pomocą zniesławiających użytkownika rzeczywistych Dowiedz się, który uniemożliwia. 

Aby LUIS być wydajny w jego zadania uczenia zwiększa zniesławiających aż do bieżącego zestawu zniesławiających zarówno przykład, jak i punktu końcowego zwróconego wyniki prognozowania pewność, wysoka. Poprawy wyników przy użyciu uczenia active [wzorce](luis-concept-patterns.md), i [frazę list](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Wykonaj, a nie
Poniższa lista zawiera najlepsze rozwiązania dotyczące LUIS aplikacji:

|Zalecenia|Zakazy|
|--|--|
|[Definiowanie różnych opcji](#do-define-distinct-intents) |[Dodawanie wielu zniesławiających przykład do lokalizacji docelowych](#dont-add-many-example-utterances-to-intents) |
|[Odnalezienia punktu słodka między zbyt ogólne i zbyt specyficznych dla każdego zamiar](#do-find-sweet-spot-for-intents)|[Użyj LUIS jako platforma szkolenia](#dont-use-luis-as-a-training-platform)|
|[Tworzenie aplikacji wielokrotnie powtarzane](#do-build-the-app-iteratively)|[Dodawanie wielu zniesławiających przykład o tym samym formacie, ignorowanie innych formatach](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodawanie listy frazy i wzorce w późniejszym iteracji](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Mieszane definicji intencje i jednostek](#dont-mix-the-definition-of-intents-and-entities)|
|[Dodaj zniesławiających przykład brak konwersji](#do-add-example-utterances-to-none-intent)|[Tworzenie list frazę z wszystkie możliwe wartości](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Korzystaj z funkcji sugerowanej active szkoleniowe](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj tyle wzorce](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji.](#do-monitor-the-performance-of-your-app)|[Szkolenie i publikowanie za pomocą każdej utterance pojedynczym przykład dodane](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiowanie różnych opcji
Upewnij się, że słownictwa dla każdego celem jest tylko do tego celem i nie nakładające się z innego zamiaru. Na przykład jeśli chcesz mieć aplikację, która obsługi zorganizowanie takich jak lotach linii lotniczych i hotele, można tych jako osobne intencje lub tego samego zamiar z jednostkami dla określonych danych wewnątrz utterance.

Jeśli słownictwa między dwa profile jest taki sam, zamiar łączyć, a następnie użyj jednostek. 

Należy wziąć pod uwagę następujące zniesławiających przykładzie:

```
Book a flight
Book a hotel
```

"Book lot" i "Book hoteli" używać tego samego słownictwa z "książki". To jest nakładających się więc powinna być taka sama zamiar z innych wyrazów transmitowane i hoteli wyodrębnione jednostek. 

## <a name="do-find-sweet-spot-for-intents"></a>Znajdź miejscu słodka dla lokalizacji docelowych
Dane prognozowania z LUIS umożliwia określenie, czy nakładają się na Twojej lokalizacji docelowych. Nakładające się intencje confuses LUIS. Wynik jest za Zamknij, aby jest górnej oceniania zamiar innym celem. Celem nakładające się, ponieważ LUIS nie używa dokładnie tej samej ścieżce za pośrednictwem danych szkolenia każdorazowo, ma stosowany jest pierwszym lub drugim w szkolenia. Ma wynik utterance dla każdego zamiar będzie dalej od siebie, więc nie jest to realizowane. Dobrym rozróżnienie dla lokalizacji docelowych powinno spowodować oczekiwanego zamiar top zawsze. 
 
## <a name="do-build-the-app-iteratively"></a>Tworzenie aplikacji wielokrotnie powtarzane
Zachowaj oddzielnej *ukryta* zestawu, który nie jest używany jako test [zniesławiających przykład](luis-concept-utterance.md) lub zniesławiających punktu końcowego. Zachowaj poprawy aplikacji zestawu testowego. Dostosować ustawienie w celu odzwierciedlenia zniesławiających rzeczywistego użytkownika testu. Za pomocą tego testu ukryta ustawioną oceny każdej iteracji. 

Deweloperzy powinien mieć trzy zestawy danych. Pierwsza to zniesławiających przykład konstruowania modelu. Drugim jest do testowania modelu w punkcie końcowym. Trzeci jest blind test danych używanych w [testowania partii](luis-how-to-batch-test.md). Ten ostatni zestaw nie jest używany w szkolenia aplikacji ani wysyłane w punkcie końcowym.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Dodawanie listy frazy i wzorce w późniejszym iteracji
[Frazę list](luis-concept-feature.md) umożliwiają definiowanie słowniki słowa związane z domeny aplikacji. Inicjatora Twoje hasło listy kilka słów, a następnie funkcja sugerowanej tak LUIS obsługującemu wyrazów więcej słownictwa. Nie dodawaj każdego wyrazu słownictwa, ponieważ lista wyrażenie nie jest dokładnego dopasowania. 

Zniesławiających rzeczywistego użytkownika z punktu końcowego, bardzo podobne do siebie, może ujawnić wzorców wybór programu word i umieszczania. [Wzorzec](luis-concept-patterns.md) funkcja przyjmuje tego wybór programu word i umieszczania wraz z wyrażeń regularnych w celu zwiększenia dokładności prognozy. Wyrażenie regularne we wzorcu umożliwia słów i znaków interpunkcyjnych, które mają zostać Ignoruj podczas nadal pasujących do wzorca. 

Ze składnią deseniu opcjonalne dla znaki interpunkcyjne, znaki interpunkcyjne można zignorować.

Nie należy stosować te rozwiązania przed aplikacji otrzymał żądania punktu końcowego, ponieważ który pochyla zaufania.  

## <a name="do-add-example-utterances-to-none-intent"></a>Dodaj zniesławiających przykład brak konwersji
Jest to powrotu przeznaczeniu wskazanych wszystko poza aplikacji. Dodaj jeden przykład utterance konwersji dla każdego zniesławiających przykład 10 w pozostałej części aplikacji LUIS None.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystać z tej funkcji sugerowanej active szkoleniowe
Użyj [active learning](label-suggested-utterances.md)w **Przejrzyj zniesławiających punktu końcowego** regularnie, zamiast opcji dodawania więcej zniesławiających przykład do lokalizacji docelowych. Ponieważ aplikacja otrzymuje stale zniesławiających punktu końcowego, ta lista jest powiększania i zmiana.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji.
Monitorowanie dokładności prognozy przy użyciu zestawu testów. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie należy dodawać wielu zniesławiających przykład do lokalizacji docelowych
Po opublikowaniu aplikacji dodawać tylko zniesławiających z learning aktywne w procesie iteracji. W przypadku zbyt podobne zniesławiających Dodaj wzorca. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj LUIS jako platforma szkolenia
LUIS jest specyficzne dla domeny model języka. Nie jest przeznaczona do pracy jako platforma szkolenie ogólne. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nie należy dodawać wielu zniesławiających przykład o tym samym formacie, ignorowanie innych formatach
LUIS oczekuje zmian w zniesławiających celem. Zniesławiających może się różnić, a jednocześnie ma takie samo znaczenie dla ogólnej. Zmiany mogą obejmować utterance długości, wybór programu word i rozmieszczenia programu word. 

|Nie używaj tego samego formatu|Użyj formatu różnych|
|--|--|
|Kup biletu do Seattle<br>Kup bilet, aby Paryża<br>Kup bilet, aby Orlando|Kup 1 bilet Seattle<br>Zarezerwuj dwóch miejsc w czerwonym oczu Paryża następny poniedziałek<br>Chcę Rezerwacja 3 biletów do Orlando dla spring podziału|

Drugi kolumny używa innego zlecenia (dostawcy, rezerwy, książki), różnych ilości (1, dwóch, 3), i inne ustalenia wyrazy, ale wszystkie mają takie same zakupu linii lotniczych biletów dla podróży. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nie można mieszać definicji intencje i jednostek
Utwórz celem jakichkolwiek działań, że Twoje bot spowoduje przejście. Użyj jednostek jako parametry, które umożliwiają tej akcji. 

Chatbot, który będzie zarezerwować lotach linii lotniczych, można utworzyć **BookFlight** celem. Nie należy tworzyć celem dla każdej linii lotniczych lub każdego miejsca docelowego. Użyj tych fragmentów danych jako [jednostek](luis-concept-entity-types.md) i oznacz je w zniesławiających przykład. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nie należy tworzyć listy frazę z wszystkie możliwe wartości
Podaj kilka przykładów w [frazę list](luis-concept-feature.md) , ale nie każdego wyrazu. LUIS stanowi uogólnienie i uwzględnia kontekstu. 

## <a name="dont-add-many-patterns"></a>Nie należy dodawać wielu wzorców
Nie dodawaj zbyt wiele [wzorców](luis-concept-patterns.md). Oznacza, że LUIS Dowiedz się szybko wraz z przykładami mniej. System nie być niepotrzebnie przeciążenia.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie nauczenia i publikowanie za pomocą każdej utterance pojedynczym przykład
Dodaj 10 lub 15 zniesławiających przed szkolenia i publikowania. Który pozwala zobaczyć wpływ na dokładność prognozowania. Dodanie jednego utterance nie może mieć wpływ widoczne na wynik. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Planowanie aplikacji](plan-your-app.md) w aplikacji LUIS.
[LUIS]: luis — odwołanie regions.md #luis-witryny sieci Web
