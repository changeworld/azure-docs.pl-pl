---
title: Dodawanie wypowiedzi przykładu w aplikacjach usługi LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodawanie wypowiedzi w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 80ccedc7299836cae689edd8a277a7c6d23befa7
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345060"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Dodawanie wypowiedzi przykład i etykiety z jednostkami

Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Do nauki Language Understanding (LUIS), musisz dodać [wypowiedzi przykład](luis-concept-utterance.md) do [intencji](luis-concept-intent.md).

Ogólnie rzecz biorąc najpierw Dodaj wypowiedź przykład intencji, a następnie utworzyć etykietę wypowiedzi i jednostek na stronie intencji. Jeśli czy raczej najpierw utworzyć jednostki, zobacz [Dodaj jednostki](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Dodaj wypowiedź
Na stronie intencji wprowadź wypowiedź odpowiedni przykład, można oczekiwać od użytkowników, takie jak `book 2 adult business tickets to Paris tomorrow on Air France` w polu tekstowym pod nazwę metody konwersji, a następnie naciśnij klawisz Enter. 
 
>[!NOTE]
>Usługa LUIS konwertuje wszystkie wypowiedzi na małe litery.

![Strona Szczegóły zrzut ekranu z opcjami, z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Wypowiedzi są dodawane do listy wypowiedzi dla bieżącego intencji. 

## <a name="ignoring-words-and-punctuation"></a>Ignorowanie słów i znaki interpunkcyjne
Ignorowanie konkretnych słów lub znaki interpunkcyjne w wypowiedź przykładu, należy użyć [wzorzec](luis-concept-patterns.md#pattern-syntax) z _Ignoruj_ składni. 

## <a name="add-simple-entity-label"></a>Dodaj etykietę proste jednostki
Poniższa procedura służy do tworzenia i etykiety niestandardowe jednostki w ramach następujących wypowiedź na stronie opcji:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Wybierz "Air France" wypowiedź, aby oznaczyć go jako podmiot prosty.

    > [!NOTE]
    > Podczas wybierania wyrazy, aby oznaczyć je jako jednostki:
    > * Dla pojedynczego słowa po prostu wybierz ją. 
    > * Aby uzyskać zestaw dwóch lub więcej wyrazów wybierz na początku, a następnie na końcu zestawu.

2. W polu listy rozwijanej jednostki, które pojawia się możesz wybrać istniejącej jednostki lub Dodaj nową jednostkę. Aby dodać nową jednostkę, wpisz jego nazwę w polu tekstowym, a następnie wybierz **Utwórz nową jednostkę**. 
 
    ![Zrzut ekranu z opcjami strony szczegółów, od prostych jednostki etykietowania wyróżnioną opcją](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. W **jakiego typu jednostki, czy chcesz utworzyć?** wyskakujące okno dialogowe, sprawdź nazwę podmiotu i wybierz typ jednostki prostej, a następnie wybierz **gotowe**.

    ![Obraz przedstawiający okno dialogowe potwierdzenia](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#simple-entity-data) dowiedzieć się więcej o wyodrębniania proste jednostki z punktu końcowego odpowiedź JSON na zapytanie. Wypróbuj prostą jednostki [Szybki Start](luis-quickstart-primary-and-secondary-data.md) Aby dowiedzieć się więcej o sposobie używania prostego jednostki.


## <a name="add-list-entity-and-label"></a>Dodawanie jednostki listy i etykiety
Lista jednostek reprezentują stały, zamknięte zestaw (pasuje do tekstu do dokładnego dopasowania) powiązanych słów w Twoim systemie. 

Dla jednostki listy w przeddzień, może mieć dwie wartości znormalizowane: wody i soda pop. Nazwa każdego znormalizowane ma synonimów. Wody synonimy są H20 i gaz, stałe. W przypadku soda pop synonimy są owoców, receptura, imbir. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

|Znormalizowaną nazwę|Synonimy|
|--|--|
|Gospodarka wodna|H20, gaz, stosowana jest stała|
|Soda pop|Owoców, receptura, imbir|

Podczas tworzenia nowej jednostki listy ze strony metody konwersji, którą wykonujesz dwie rzeczy, które mogą nie być oczywista. Najpierw utworzysz nową listę, dodając pierwszego elementu listy. Po drugie za pomocą słowo lub frazę, która została wybrana w zaufanym wypowiedź nosi nazwę pierwszego elementu listy. Gdy ustawienia te można zmienić później na stronie jednostki, może być szybsze do wybrania wypowiedź zawierającym wyraz przewidzianą dla nazwy elementu listy.

Na przykład, jeśli chcesz utworzyć listę napoju i wybrane typy wyraz `h2o` z wypowiedź, aby utworzyć jednostkę, listy miałby jeden element, którego nazwa została h20. Jeśli chce się nazwę bardziej ogólnym, należy wybrać wypowiedź, który używa nazwy bardziej ogólnymi. 

1. W polu wypowiedź, wybierz wyrazu, który jest pierwszym elementem na liście, wprowadź nazwę listy w polu tekstowym, a następnie wybierz **Utwórz nową jednostkę**.   

    ![Zrzut ekranu z opcjami szczegółów strony Utwórz nową jednostkę wyróżniony](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. W **jakiego typu jednostki, czy chcesz utworzyć?** okna dialogowego Dodaj synonimy ten element listy. Dla elementu wody na liście napoju, Dodaj `h20`, `perrier`, i `waters`i wybierz **gotowe**. Należy zauważyć, że "wody" został dodany, ponieważ synonimy listy są dopasowywane na poziomie tokenu. W języku angielskim kulturze, że poziom jest na poziomie word więc "wody" nie zostaną dopasowane do "water", chyba że został on na liście. 

    ![Zrzut ekranu przedstawiający typ jednostki, czy użytkownik chce utworzyć okno dialogowe](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Tej listy w przeddzień ma napoju tylko jeden typ, wody. Można dodać większą liczbę typów napoju etykietowania innych wypowiedzi lub edytowania jednostek z **jednostek** w nawigacji po lewej stronie. [Edytowanie](luis-how-to-add-entities.md#add-list-entities) jednostek oferuje opcje wprowadzania dodatkowych elementów za pomocą odpowiedniego synonimy lub [importowania](luis-how-to-add-entities.md#import-list-entity-values) listy. 

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#list-entity-data) dowiedzieć się więcej o wyodrębniania listy jednostek z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj [Szybki Start](luis-quickstart-intent-and-list-entity.md) Aby dowiedzieć się więcej o sposobie używania obiektami listy.

## <a name="add-synonyms-to-the-list-entity"></a>Dodawanie synonimów do jednostki listy 
Dodawanie synonimów do jednostki listy, wybierając wyraz lub frazę w polu wypowiedź. Jeśli masz napój listę jednostek i chcesz dodać `agua` jako synonim dla limitu górnego, postępuj zgodnie z instrukcjami:

W wypowiedź, wybierz równoznaczny słowa, takie jak `aqua` do wody, następnie wybierz listę jednostki nazwę na liście rozwijanej, takich jak **pić**, a następnie wybierz **ustawiony jako synonim**, następnie wybierz listę element jest synonimem, takie jak **wody**.

![Zrzut ekranu z opcjami szczegółów strony Tworzenie nowych synonim wyróżniony](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Utwórz nowy element listy jednostki
Utwórz nowy element do istniejącej jednostki listę, wybierając wyraz lub frazę w polu wypowiedź. Jeśli masz napój listy i chcesz dodać `tea` jako nowy element, postępuj zgodnie z instrukcjami:

W wypowiedź, zaznacz wyraz dla nowego elementu listy, takich jak `tea`, następnie wybierz nazwę jednostki listy na liście rozwijanej, takich jak **pić**, a następnie wybierz **tworzenie nowych synonim**. 

![Zrzut ekranu przedstawiający dodawanie nowego elementu listy](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Słowa zostanie wyróżniony kolorem niebieskim. Po umieszczeniu wskaźnika myszy nad wyraz tag wyświetlane nazwa elementu listy, takich jak herbaty.

![Zrzut ekranu przedstawiający nowy tag elementu listy](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>OPAKOWYWANIE jednostek w etykiecie złożone
Złożone jednostki są tworzone na podstawie **jednostek**. Nie można utworzyć jednostki złożonego ze strony metody konwersji. Po utworzeniu obiektu złożonego w wypowiedź na stronie opcji może zawijać się jednostki. 

Zakładając, że wypowiedź `book 2 tickets from Seattle to Cairo`, złożonego wypowiedź może zwrócić informacje jednostki liczby biletów (2), punkt początkowy (Seattle) i miejsce docelowe lokalizacje (Kair) w jednostce pojedynczym nadrzędnym. 

Postępuj zgodnie z tymi [kroki](luis-how-to-add-entities.md#add-prebuilt-entity) dodać **numer** wstępnie utworzone jednostki. Po utworzeniu jednostki `2` wypowiedź jest niebieska, co oznacza etykietami jednostki. Wstępnie utworzone jednostki są oznaczane etykietami z usługi LUIS. Nie można dodać lub usunąć etykietę wstępnie utworzone jednostki z jednym wypowiedź. Można jedynie dodać lub usunąć wbudowanych etykiet przez dodanie lub usunięcie wstępnie utworzone jednostki z aplikacji.

Postępuj zgodnie z tymi [kroki](#add-hierarchical-entity-and-label) utworzyć **lokalizacji** hierarchiczne jednostki. Etykiety początkowe i docelowe lokalizacje w wypowiedź przykładu. 

Przed opakowaniem jednostek w jednostce złożone, upewnij się, wszystkich obiektów podrzędnych, zostały wyróżnione na niebiesko, co oznacza, że mają zostać oznaczone w wypowiedź.

1. Aby zawijać poszczególne jednostki do złożonego, wybierz pierwszy obiekt oznaczone w wypowiedź dla złożonego obiektu. W polu wypowiedź przykład `book 2 tickets from Seattle to Cairo`, pierwszy obiekt jest numer 2. Zostanie wyświetlona lista listy rozwijanej wyświetlane opcje do wyboru.

    ![Zrzut ekranu przedstawiający wybrany numer i lista rozwijana opcji wyróżnionych](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Wybierz **opakować złożonego jednostki** z listy rozwijanej. 

    ![Zrzut ekranu przedstawiający listę rozwijaną opcje zawijania złożonego jednostki ignorowane w jednostce złożone wyróżniony](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Wybierz ostatni wyraz złożony jednostki. Wypowiedź w tym przykładzie wybierz "Location::Destination" (reprezentującej Kair). Zieloną linią jest teraz w ramach wszystkich słów w tym słowa innego niż jednostka w wypowiedź, które są złożonego.

    ![Zrzut ekranu BookFlight intencji stronie numerem wyróżniony](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Wybierz nazwę jednostki złożonej z listy rozwijanej. W tym przykładzie jest **TicketOrder**.

    ![Zrzut ekranu przedstawiający zawijanie wyrazów złożonych jednostki o nazwie złożonego jednostki z wyróżnioną pozycją na liście rozwijanej](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Jednostki są poprawnie zawijać, zieloną linię podlega cała fraza.

    ![Zrzut ekranu przedstawiający wypowiedź z jednostką złożonego wyróżniony](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#composite-entity-data) dowiedzieć się więcej o wyodrębniania złożonego jednostki z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj złożonego jednostki [samouczek](luis-tutorial-composite-entity.md) Aby dowiedzieć się więcej o sposobie używania złożonego jednostki.

## <a name="add-hierarchical-entity-and-label"></a>Dodawanie jednostki hierarchiczne i etykiety
Hierarchiczna jednostka jest kategoria kontekstowe nauczony i pod względem koncepcyjnym powiązanych jednostek. W poniższym przykładzie jednostki zawiera początkowe i docelowe lokalizacje. 

W polu wypowiedź `Book 2 tickets from Seattle to Cairo`, Seattle, jest to lokalizacja źródła i Kair jest lokalizacja docelowa. Każda lokalizacja jest kontekstowe różnych i nauczony kolejność słów i wybór o słowo w wypowiedź.

1. Na stronie intencji wypowiedź, wybierz "Seattle", a następnie wprowadź nazwę jednostki "lokalizacji, a następnie wybierz **Utwórz nową jednostkę**.

    ![Zrzut ekranu z utworzyć hierarchiczne jednostki etykietowania okno dialogowe](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. W podręcznym oknie dialogowym, wybierz hierarchiczne dla **typu jednostki**, następnie dodać `Origin` i `Destination` jako elementy podrzędne, a następnie wybierz **gotowe**.

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką ToLocation wyróżniony](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Wyraz wypowiedź został oznaczony za pomocą hierarchicznych obiektu nadrzędnego. Musisz przypisać słowo na jednostce podrzędnej. Wróć do wypowiedź na stronie opcji. Zaznacz wyraz, a następnie z listy rozwijanej wybierz nazwę jednostki, utworzonej i postępuj zgodnie z menu po prawej stronie wybranie jednostki podrzędne poprawne.

    ![Strona Szczegóły zrzut ekranu z opcjami, z jednostką ToLocation wyróżniony](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Nazwy jednostek podrzędny musi być unikatowa we wszystkich jednostek w jednej aplikacji. Dwoma obiektami hierarchiczne nie może zawierać jednostki podrzędne o takiej samej nazwie. 

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#hierarchical-entity-data) dowiedzieć się więcej o wyodrębniania hierarchiczne jednostki z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj hierarchiczne jednostki [Szybki Start](luis-quickstart-intent-and-hier-entity.md) Aby dowiedzieć się więcej o sposobie używania hierarchiczne jednostki.


## <a name="remove-entity-labels-from-utterances"></a>Usuń jednostki etykiety wypowiedzi
Usuń z etykiety maszyny do opanowania jednostki, z wypowiedź na stronie opcji. Jeśli jednostka nie jest maszyny do opanowania, nie można usunąć z wypowiedź. Jeśli musisz usunąć jednostka przedstawiono maszyny z wypowiedź musisz usunąć jednostkę z całej aplikacji. 

Aby usunąć etykietę maszyny do opanowania jednostki wypowiedź, wybrać jednostkę wypowiedź. Następnie wybierz pozycję **Usuń etykietę** w wyświetlonym polu listy rozwijanej jednostki.

![Zrzut ekranu z opcjami strony szczegółów, usunąć etykietę, wyróżniony](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Dodaj etykietę wstępnie utworzone jednostki
Jeśli dodasz ze wstępnie utworzonych jednostek z aplikacją usługi LUIS, nie trzeba wypowiedzi etykiety z tymi jednostkami. Aby dowiedzieć się więcej na temat wstępnie utworzonych jednostek i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Dodaj etykietę jednostki wyrażeń regularnych
Jeśli dodasz jednostek wyrażenia regularnego z aplikacją usługi LUIS, nie trzeba wypowiedzi etykiety z tymi jednostkami. Aby dowiedzieć się więcej na temat jednostek wyrażenia regularnego i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z wypowiedź
Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Dodaj etykietę jednostki pattern.any
Jeśli dodasz jednostek pattern.any z aplikacją usługi LUIS nie etykiety wypowiedzi z tymi jednostkami. Tylko są prawidłowe we wzorcach. Aby dowiedzieć się więcej na temat jednostek pattern.any i jak je dodać, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Uczenie aplikacji po zmianie modelu z wypowiedzi
Po Dodawanie, edytowanie lub usuwanie wypowiedzi, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Po etykietowanie wypowiedzi w Twoje intencje, możesz teraz utworzyć [złożonego jednostki](luis-how-to-add-entities.md).
