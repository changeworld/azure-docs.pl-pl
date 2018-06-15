---
title: Dodaj przykład zniesławiających w aplikacjach LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak dodać zniesławiających aplikacje opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12cbffdbd9ab299d4405aa50c0e49db9c20ab0fa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349789"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Dodaj zniesławiających przykład i etykiety z jednostki

Zniesławiających przedstawiono pytania użytkownika lub polecenia. Nauczenie opis języka (LUIS), musisz dodać [zniesławiających przykład](luis-concept-utterance.md) do [zamiar](luis-concept-intent.md).

Ogólnie rzecz biorąc najpierw Dodaj utterance, a następnie utwórz jednostki i etykieta zniesławiających na stronie konwersji. Jeśli czy raczej najpierw utworzyć jednostki, zobacz [Dodaj jednostki](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Dodaj utterance
Na stronie konwersji, wprowadź odpowiednie utterance oczekiwać od użytkowników, takich jak `book 2 adult business tickets to Paris tomorrow on Air France` w polu tekstowym pod konwersji nazwy, a następnie naciśnij klawisz Enter. 
 
>[!NOTE]
>LUIS konwertuje wszystkie zniesławiających na małe litery.

![Zrzut ekranu opcji strony szczegółów, z wyróżnioną pozycją utterance](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Zniesławiających zostaną dodane do listy zniesławiających dla bieżącego celem. 

## <a name="add-simple-entity-label"></a>Dodawanie jednostki prostej etykiety
W poniższej procedurze Utwórz i etykiet niestandardowych obiektów w ramach następujących utterance na stronie opcji:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Wybierz "Lotniczego Francja" utterance, aby oznaczyć go jako podmiot prosty.

    > [!NOTE]
    > Podczas wybierania słów, aby oznaczyć je jako jednostek:
    > * Dla pojedynczego wyrazu po prostu zaznacz go. 
    > * Zbiór dwóch lub więcej wyrazów wybierz na początku, a następnie na końcu zestawu.

2. W polu listy rozwijanej jednostki, które pojawia się możesz wybierz istniejącej jednostki lub Dodaj nową jednostkę. Aby dodać nową jednostkę, wpisz jego nazwę w polu tekstowym, a następnie wybierz **utworzyć nową jednostkę**. 
 
    ![Zrzut ekranu opcji strony szczegółów, z jednostką proste etykietowania wyróżnioną opcją](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. W **jakiego typu jednostki chcesz utworzyć?** podręczne okno dialogowe, sprawdź nazwę podmiotu i wybierz typ jednostki proste, a następnie wybierz **gotowe**.

    ![Obraz okna dialogowego potwierdzenia](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#simple-entity-data) Aby dowiedzieć się więcej o wyodrębniania jednostki prostej z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj jednostki prostej [szybkiego startu](luis-quickstart-primary-and-secondary-data.md) Aby dowiedzieć się więcej o sposobie używania prostego jednostki.


## <a name="add-list-entity-and-label"></a>Dodaj jednostki listy i etykiety
Lista podmiotów reprezentują stały, zamknięte zestaw (tekst dokładnie odpowiada) pokrewne słowa w systemie. 

Dla obiektu listy napojów, może mieć dwie wartości znormalizowane: limitu górnego oraz soda pop. Nazwa każdego znormalizowane ma synonimy. Dla limitu górnego synonimy są H20, gaz, płaskim. Soda pop synonimy są owoców, coli, imbir. Nie trzeba znać wszystkie wartości podczas tworzenia jednostki. Możesz dodać więcej po przejrzeniu zniesławiających rzeczywistego użytkownika z synonimy.

|Znormalizowaną nazwę|Synonimy|
|--|--|
|Gospodarka wodna|H20, gazu, płaski|
|Soda pop|Owoców, coli, imbir|

Podczas tworzenia nowego obiektu listy na stronie konwersji, są wykonuje dwie czynności, które mogą nie być oczywista. Po pierwsze tworzysz nową listę przez dodanie pierwszego elementu listy. Po drugie z wyraz lub frazę wybraną z utterance nosi nazwę pierwszego elementu listy. Podczas gdy można zmienić później na stronie jednostki, może być szybciej wybierz utterance zawierający słowo, które mają nazwy elementu listy.

Na przykład, jeśli chcesz utworzyć listę napoju i należy wybrać typ wyraz `h2o` z utterance można utworzyć jednostki, listy miałyby jeden element, którego nazwa została h20. Jeśli potrzebujesz nazwę bardziej ogólnym, należy wybrać utterance o nazwie bardziej ogólnym. 

1. Wybierz utterance, word, który jest pierwszy element na liście, wprowadź nazwę listy w polu tekstowym, a następnie wybierz **utworzyć nową jednostkę**.   

    ![Zrzut ekranu opcji strony szczegółów, z Utwórz nową jednostkę wyróżnione](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. W **jakiego typu jednostki chcesz utworzyć?** okno dialogowe Dodaj synonimy ten element listy. Dla limitu górnego elementu na liście napoju, Dodaj `h20`, `perrier`, i `waters`i wybierz **gotowe**. Zwróć uwagę, że "wody" został dodany, ponieważ synonimy listy są dopasowywane w poziomie tokenu. W angielskiej wersji językowej kultury, że poziom jest na poziomie word więc "wody" może nie można dopasować do "wody" chyba że był on na liście. 

    ![Zrzut ekranu przedstawiający co typu jednostki chcesz utworzyć okno dialogowe](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Ta lista napojów ma napoju tylko jeden typ limitu górnego. Można dodać więcej typów napoju przez inne zniesławiających etykietowania lub edytując jednostka z **jednostek** na lewym pasku nawigacyjnym. [Edytowanie](luis-how-to-add-entities.md#add-list-entities) jednostek udostępnia opcje wprowadzania dodatkowych elementów z odpowiedniego synonimy lub [importowania](luis-how-to-add-entities.md#import-list-entity-values) listy. 

    Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#list-entity-data) Aby dowiedzieć się więcej o wyodrębniania listy jednostek z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj [szybkiego startu](luis-quickstart-intent-and-list-entity.md) Aby dowiedzieć się więcej na temat sposobu korzystania z jednostki listy.

## <a name="add-synonyms-to-the-list-entity"></a>Dodaj do listy jednostki synonimy 
Dodaj synonim do jednostki listy, wybierając wyraz lub frazę w utterance. Jeśli masz napój listy jednostek i chcesz dodać `agua` jako synonimem wody, wykonaj kroki:

W utterance, zaznacz słowo synonimy, takich jak `aqua` wody, następnie wybierz nazwę jednostki listy na liście rozwijanej, takich jak **pić**, a następnie wybierz pozycję **Ustaw jako synonim**, następnie wybierz z listy element jest synonimem, takich jak **wody**.

![Zrzut ekranu opcji strony szczegółów, z Utwórz nowy synonim wyróżnione](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Utwórz nowy element listy jednostki
Utwórz nowy element do istniejącej jednostki listy wybierając wyraz lub frazę w utterance. Jeśli masz napój listy i chcesz dodać `tea` jako nowy element, wykonaj kroki:

W utterance, zaznacz słowo nowego elementu listy, takich jak `tea`, następnie wybierz nazwę jednostki listy na liście rozwijanej, takich jak **pić**, a następnie wybierz pozycję **tworzenie nowych synonim**. 

![Zrzut ekranu przedstawiający dodawanie nowego elementu listy](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Wyraz jest teraz zaznaczone na niebiesko. Jeśli Aktywuj słowo, tag są wyświetlane nazwa elementu listy, takich jak zepołowy.

![Zrzut ekranu przedstawiający nowy znacznik elementów listy](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Zawijanie jednostek w złożonych etykiety
Złożone jednostki są tworzone na podstawie **jednostek**. Nie można utworzyć jednostki złożone ze strony metody konwersji. Po utworzeniu obiektu złożonego może zawijać się jednostek w utterance na stronie konwersji. 

Zakładając, że utterance, `book 2 tickets from Seattle to Cairo`, złożone utterance mogą być zwracane jednostki informacje liczby biletów (2), pochodzenia (Seattle) i miejsce docelowe lokalizacje (Kair) w jednym nadrzędne. 

Postępuj zgodnie z następującymi [kroki](luis-how-to-add-entities.md#add-prebuilt-entity) można dodać **numer** wbudowane jednostki. Po utworzeniu obiektu `2` utterance jest niebieska wskazujący, że jest etykietą jednostki. Wbudowane jednostki są oznaczane etykietami z LUIS. Nie można dodać lub usunąć etykiety wbudowane jednostki z jednym utterance. Można tylko dodawać i usuwać wbudowane etykiet przez dodanie lub usunięcie jednostki wbudowane z aplikacji.

Postępuj zgodnie z następującymi [kroki](#add-hierarchical-entity-and-label) utworzyć **lokalizacji** hierarchiczna jednostki. Etykieta źródło i miejsce docelowe lokalizacje w utterance przykład. 

Przed opakowaniem jednostek w jednostce złożonego, upewnij się, wszystkich obiektów podrzędnych są wyróżnione kolorem niebieskim, co oznacza, że mają zostać oznaczone w utterance.

1. Do zakodowania poszczególnych jednostek w złożonym, wybierz pierwszy jednostki etykietą w utterance dla obiektu złożonego. W utterance przykład `book 2 tickets from Seattle to Cairo`, pierwszy jednostka jest numer 2. Zostanie wyświetlona lista rozwijalna przedstawiający opcje dla tego zaznaczenia.

    ![Zrzut ekranu przedstawiający wybranego kodu i opcji listy rozwijanej wyróżnione](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Wybierz **zawijać złożonego jednostki** z listy rozwijanej. 

    ![Zrzut ekranu przedstawiający opcje listy rozwijanej zawijania jednostki złożone z zawijania w jednostce złożonego wyróżnione](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Wybierz wyraz ostatniej obiektu złożonego. W utterance tego przykładu wybierz "Location::Destination" (reprezentujący Kair). Zielony wiersza jest teraz w obszarze wszystkich wyrazów w tym słowa innego niż jednostka w utterance, które są złożone.

    ![Zrzut ekranu BookFlight zamiar strony, numerem wyróżnione](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Wybierz nazwę jednostki złożone z listy rozwijanej. W tym przykładzie, który jest **TicketOrder**.

    ![Zrzut ekranu przedstawiający zawijania wyrazy złożone jednostki o nazwie podmiotu złożonego wyróżnione na liście rozwijanej](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Poprawnie zawijać się jednostek zielony wiersza podlega całe słowo.

    ![Zrzut ekranu przedstawiający utterance z jednostką złożonego wyróżnione](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#composite-entity-data) Aby dowiedzieć się więcej o wyodrębniania obiekt złożony z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj złożonego jednostki [samouczek](luis-tutorial-composite-entity.md) Aby dowiedzieć się więcej o sposobie używania złożonego jednostki.

## <a name="add-hierarchical-entity-and-label"></a>Dodaj jednostki hierarchicznej i etykiety
Hierarchiczna jednostka jest kategorią kontekstowej zapamiętanych i koncepcyjnie powiązanych jednostek. W poniższym przykładzie jednostki zawiera źródło i miejsce docelowe lokalizacje. 

W utterance `Book 2 tickets from Seattle to Cairo`, Seattle to miejsce pochodzenia i Kair jest lokalizacji docelowej. Każda lokalizacja jest użycie różnych i zapamiętane z kolejność słów i wybór programu word w utterance.

1. Na stronie konwersji utterance, wybierz "Seattle", a następnie wprowadź nazwę jednostki "lokalizacji, a następnie wybierz **utworzyć nową jednostkę**.

    ![Zrzut ekranu z utworzyć hierarchiczne jednostki etykietowania — okno dialogowe](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. W wyświetlonym oknie dialogowym, wybierz hierarchiczna dla **typu jednostki**, następnie dodać `Origin` i `Destination` jako elementy podrzędne, a następnie wybierz **gotowe**.

    ![Zrzut ekranu opcji strony szczegółów, z wyróżnioną pozycją jednostką ToLocation](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Wyraz w utterance został oznaczony hierarchiczna obiektu nadrzędnego. Należy przypisać słowo na jednostce podrzędnej. Wróć do utterance na stronie konwersji. Wybierz word, a następnie z listy rozwijanej wybierz nazwę podmiotu, utworzony, a wykonaj menu z prawej strony, aby wybrać poprawny podrzędne jednostki.

    ![Zrzut ekranu opcji strony szczegółów, z wyróżnioną pozycją jednostką ToLocation](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Nazwy obiektów podrzędnych musi być unikatowa we wszystkich jednostek w jednej aplikacji. Dwoma obiektami hierarchiczna nie może zawierać obiektów podrzędnych o takiej samej nazwie. 

    Zobacz [wyodrębniania danych](luis-concept-data-extraction.md#hierarchical-entity-data) Aby dowiedzieć się więcej o wyodrębniania hierarchiczna jednostki z punktu końcowego JSON odpowiedzi na kwerendę. Spróbuj hierarchiczna jednostki [szybkiego startu](luis-quickstart-intent-and-hier-entity.md) Aby dowiedzieć się więcej o sposobie używania hierarchiczna jednostki.


## <a name="remove-entity-labels-from-utterances"></a>Usuń jednostki etykiety z zniesławiających
Możesz usunąć etykiety jednostki rozpoznane maszyny z utterance na stronie konwersji. Jeśli jednostka nie jest rozpoznawane maszyny, nie można usunąć z utterance. Jeśli musisz usunąć jednostkę rozpoznane maszyny z utterance, należy usunąć jednostkę z całej aplikacji. 

Aby usunąć etykietę jednostki rozpoznane maszyny z utterance, wybierz jednostki w utterance. Następnie wybierz **Usuń etykietę** w wyświetlonym polu listy rozwijanej jednostki.

![Zrzut ekranu opcji strony szczegółów, etykietą Usuń wyróżniony](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Dodaj etykietę wbudowane jednostki
Jeśli dodasz wbudowane jednostek aplikacji LUIS, nie trzeba zniesławiających etykiety z tych obiektów. Aby dowiedzieć się więcej na temat jednostek wbudowane i sposobu dodawania ich, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Dodaj etykietę jednostki wyrażeń regularnych
Jeśli dodasz jednostek wyrażenia regularnego do aplikacji LUIS, nie trzeba zniesławiających etykiety z tych obiektów. Aby dowiedzieć się więcej na temat jednostek wyrażenia regularnego i sposobu dodawania ich, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z utterance
Zobacz [Dodaj wzorca z istniejących utterance na stronie Opcje lub jednostki](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Dodaj etykietę jednostki pattern.any
Jeśli dodasz jednostek pattern.any do aplikacji LUIS nie etykiety zniesławiających z tych obiektów. Są one tylko prawidłowe we wzorcach. Aby dowiedzieć się więcej na temat jednostek pattern.any i sposobu dodawania ich, zobacz [Dodaj jednostki](luis-how-to-add-entities.md#add-patternany-entities).

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
## <a name="train-your-app-after-changing-model-with-utterances"></a>Szkolenie aplikacji po zmianie modelu z zniesławiających
Po Dodawanie, edytowanie lub usuwanie zniesławiających, [uczenia](luis-how-to-train.md) i [publikowania](PublishApp.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki

Po etykietowanie zniesławiających w Twojej lokalizacji docelowych, można teraz utworzyć [złożonego jednostki](luis-how-to-add-entities.md).
