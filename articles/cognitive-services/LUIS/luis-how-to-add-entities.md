---
title: Dodawanie jednostek w aplikacjach usługi LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dodaj jednostki (kluczowe dane w domenie aplikacji) w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: e97f9a5391799849983bd98db5400e0a842627b7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224130"
---
# <a name="manage-entities"></a>Zarządzanie jednostkami
Po zidentyfikowaniu aplikacji [intencji](luis-concept-intent.md), musisz [etykiety wypowiedzi przykład](luis-concept-utterance.md) z [jednostek](luis-concept-entity-types.md). Jednostki są ważnych elementów pytanie lub polecenia i mogą być istotne dla aplikacji klienckiej do wykonywania swoich zadań. 

Dodawanie, edytowanie lub usuwanie jednostek w swojej aplikacji za pośrednictwem **listy jednostek** na **jednostek** strony. Usługa LUIS oferuje dwa główne rodzaje jednostek: [ze wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md)i własne jednostki niestandardowe.

Poniższe sekcje są dostępne tylko w aplikacji usługi LUIS z **kompilacji** sekcji. **Kompilacji** łącze znajduje się w górnym pasku nawigacyjnym. Jeden raz w obrębie **kompilacji** zaznacz **jednostek** menu nawigacji po lewej stronie. Po dodaniu jednostki do aplikacji, jeśli jednostka jest maszyny do opanowania, możesz [etykiety jednostki](luis-how-to-add-example-utterances.md) wewnątrz wypowiedź. Po przeszkolony i opublikowanych aplikacji mogą pojawić się dane jednostki [wyodrębnione](luis-concept-data-extraction.md) z prognozowania. 

## <a name="add-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki
Wstępnie utworzone jednostki są zdefiniowane w [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. Typowe ze wstępnie utworzonych jednostek dodanych do aplikacji są *numer* i *datetimeV2*. 

1. W swojej aplikacji z **kompilacji** sekcji, a następnie kliknij przycisk **jednostek** w panelu po lewej stronie.
 
2. Na **jednostek** wybierz opcję **Zarządzanie ze wstępnie utworzonych jednostek**.

    ![Zrzut ekranu przedstawiający dodawanie wstępnie utworzone jednostki, na stronie jednostki](./media/add-entities/manage-prebuilt-entities-button.png)

3. W **Dodawanie lub usuwanie wstępnie utworzonych jednostek** okno dialogowe, wybierz opcję **numer** i **datetimeV2** ze wstępnie utworzonych jednostek. Następnie wybierz pozycję **gotowe**.

    ![Zrzut ekranu Dodaj okno dialogowe wstępnie utworzone jednostki](./media/add-entities/list-of-prebuilt-entities.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#prebuilt-entity-data) dowiedzieć się więcej o wyodrębniania wstępnie utworzone jednostki z punktu końcowego odpowiedź JSON na zapytanie.

## <a name="add-simple-entities"></a>Dodaj prostą jednostki
Proste jednostka jest ogólny jednostki, która opisuje pojęcia pojedynczego. 

1. W swojej aplikacji z **kompilacji** sekcji, a następnie kliknij przycisk **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

    ![Zrzut ekranu jednostki strony Tworzenie nowej jednostki wyróżnionym](./media/add-entities/create-new-entity-button.png)

2. W podręcznym oknie dialogowym, wpisz `Airline` w **nazwa jednostki** wybierz opcję **proste** z **typu jednostki** listy, a następnie wybierz **gotowe**.

    ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie linii lotniczych proste jednostki](./media/add-entities/create-simple-airline-entity.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#simple-entity-data) dowiedzieć się więcej o wyodrębniania proste jednostki z punktu końcowego odpowiedź JSON na zapytanie. Wypróbuj prostą jednostki [Szybki Start](luis-quickstart-primary-and-secondary-data.md) Aby dowiedzieć się więcej o sposobie używania prostego jednostki.

## <a name="add-regular-expression-entities"></a>Dodaj jednostki wyrażeń regularnych
Jednostka wyrażenia regularnego umożliwia wyodrębnienie danych z wypowiedź, w oparciu o podane wyrażenie regularne. 

1. W swojej aplikacji, wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

2. W oknie podręcznym okno dialogowe, typ `AirFrance Flight` w **nazwa jednostki** wybierz opcję **wyrażenia regularnego** z **typu jednostki** listy, wprowadź wyrażenie regularne `AFR[0-9]{3,4}`, a następnie wybierz pozycję **gotowe**. 

    To wyrażenie regularne lotu AirFrance oczekuje, że trzy znaki dosłownie `AFR`, następnie 3 lub 4 cyfr. Liczba cyfr, może być dowolną liczbę z zakresu od 0 do 9. Wyrażenie regularne dopasowuje numery lotów AirFrance: "AFR101", "ARF1302" i "AFR5006". Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md) dowiedzieć się więcej o wyodrębniania jednostki z punktu końcowego odpowiedź JSON na zapytanie.

    ![Obraz okna dialogowego, aby utworzyć jednostkę wyrażeń regularnych](./media/add-entities/regex-entity-create-dialog.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#regular-expression-entity-data) dowiedzieć się więcej o wyodrębnianie jednostek wyrażenia regularnego z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj jednostki wyrażenia regularnego [Szybki Start](luis-quickstart-intents-regex-entity.md) Aby dowiedzieć się więcej o sposobie używania jednostki wyrażenia regularnego.

## <a name="add-hierarchical-entities"></a>Dodaj jednostki hierarchicznych
Hierarchiczna jednostka jest kategoria kontekstowe nauczony i pod względem koncepcyjnym powiązanych jednostek. W poniższym przykładzie jednostki zawiera początkowe i docelowe lokalizacje. 

W polu wypowiedź `Book 2 tickets from Seattle to Cairo`, Seattle, jest to lokalizacja źródła i Kair jest lokalizacja docelowa. Każda lokalizacja jest kontekstowe różnych i nauczony kolejność słów i wybór o słowo w wypowiedź.

Aby dodać jednostki hierarchiczne, wykonaj następujące czynności: 

1. W swojej aplikacji, wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

2. W podręcznym oknie dialogowym, wpisz `Location` w **nazwa jednostki** , a następnie wybierz **hierarchiczne** z **typu jednostki** listy.

    ![Dodawanie jednostki hierarchicznej](./media/add-entities/hier-location-entity-creation.png)

3. Wybierz **Dodaj element podrzędny**, a następnie wpisz "Origin" w **podrzędnych nr 1** pole. 

4. Wybierz **Dodaj element podrzędny**, a następnie wpisz "Miejsce docelowe" w **podrzędnych nr 2** pole. Wybierz pozycję **Done** (Gotowe).
5. 
    >[!NOTE]
    >Aby usunąć element podrzędny, wybierz ikonę Kosza obok niej.

    >[!CAUTION]
    >Nazwy jednostek podrzędny musi być unikatowa we wszystkich jednostek w jednej aplikacji. Dwoma obiektami hierarchiczne nie może zawierać jednostki podrzędne o takiej samej nazwie. 

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#hierarchical-entity-data) dowiedzieć się więcej o wyodrębniania hierarchiczne jednostki z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj hierarchiczne jednostki [Szybki Start](luis-quickstart-intent-and-hier-entity.md) Aby dowiedzieć się więcej o sposobie używania hierarchiczne jednostki.

## <a name="add-composite-entities"></a>Dodaj jednostki złożone
Możesz zdefiniować relacje między kilka istniejących jednostek, tworząc obiekt złożony. W poniższym przykładzie jednostka zawiera liczbę biletów, źródła i lokalizacje docelowe. 

W polu wypowiedź `Book 2 tickets from Seattle to Cairo`, numer 2 jest zgodny ze wstępnie utworzonych jednostek, Seattle, jest to lokalizacja źródła i Kair jest lokalizacja docelowa. Każda jednostka jest częścią większej, jednostka nadrzędna, po utworzeniu obiektu złożonego.

1. W aplikacji, Dodaj wstępnie utworzone jednostki **numer**. Aby uzyskać instrukcje, zobacz [Dodaj ze wstępnie utworzonych jednostek](#add-prebuilt-entity). 

2. Dodaj jednostkę hierarchiczne `Location`, w tym podtypy: `origin`, `destination`. Aby uzyskać więcej instrukcji, zobacz [Dodaj jednostki hierarchiczne](#add-hierarchical-entities). 

3. Wybierz **jednostek** z nawigacji po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

4. W podręcznym oknie dialogowym, wpisz `TicketsOrder` w **nazwa jednostki** , a następnie wybierz **złożonego** z **typu jednostki** listy.

5. Wybierz **Dodaj element podrzędny** można dodać nowego elementu podrzędnego.

6. W **podrzędnych nr 1**, wybierz jednostkę **numer** z listy.

7. W **podrzędnych nr 2**, wybierz jednostkę **Location::Origin** z listy. 

8. W **podrzędnych nr 3**, wybierz jednostkę **Location::Destination** z listy. 

9. Wybierz pozycję **Done** (Gotowe).

    ![Obraz okna dialogowego, aby utworzyć złożone jednostkę](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Aby usunąć element podrzędny, wybierz przycisk Kosza obok niej.

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#composite-entity-data) dowiedzieć się więcej o wyodrębniania złożonego jednostki z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj złożonego jednostki [samouczek](luis-tutorial-composite-entity.md) Aby dowiedzieć się więcej o sposobie używania złożonego jednostki.


## <a name="add-patternany-entities"></a>Dodaj jednostki Pattern.any
[Pattern.any](luis-concept-entity-types.md) jednostek obowiązują tylko w [wzorców](luis-how-to-model-intent-pattern.md). Ta jednostka pomaga LUIS Znajdź koniec jednostki o różnej długości i wybór programu word. Ponieważ tej jednostki są używane we wzorcu, LUIS wie, gdzie jest koniec jednostki w wypowiedź.

Jeśli aplikacja ma `FindBookInfo` intencji, tytuł książki może zakłócać intencji prognozy. W celu wyjaśnienia, które wyrazy są w tytule książki, użyj Pattern.any we wzorcu. Prognozy usługi LUIS rozpoczyna się od wypowiedź. Po pierwsze wypowiedź jest zaznaczone, a takie samo dla jednostki, gdy jednostki zostaną znalezione, a następnie wzorzec jest zaznaczone, a dopasowane. 

W polu wypowiedź `Who wrote the book Ask and when was it published?`, tytuł książki, poproś, jest trudne, ponieważ nie jest kontekstowe oczywiste, gdzie kończy się tytuł i gdzie rozpoczyna się w pozostałej części wypowiedź. Tytułów książek może być w dowolnej kolejności, łącznie z pojedynczego wyrazu wyrazów złożonych wyrażeń przy użyciu znaków interpunkcyjnych i pozbawiona sensu kolejność słów. Wzorzec pozwala na tworzenie jednostki gdzie można wyodrębnić jednostki pełne i dokładne. Po znalezieniu tytułu `FindBookInfo` intencji przewiduje się, ponieważ jest celem dla wzorca.

1. W swojej aplikacji z **kompilacji** sekcji, a następnie kliknij przycisk **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

2. W **Dodaj jednostkę** okno dialogowe, typ `BookTitle` w **nazwa jednostki** pole, a następnie wybierz **Pattern.any** jako **typu jednostki**.
 
    ![Zrzut ekranu przedstawiający tworzenie jednostki pattern.any](./media/add-entities/create-pattern-any-entity.png)

    Korzystają z tej jednostki pattern.any, należy dodać na wzorcu **wzorców** strony (w obszarze **lepsza wydajność aplikacji** sekcji) ze składnią poprawne nawiasów klamrowych, takie jak "dla **{BookTitle}** kto jest autorem? ".

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#patternany-entity-data) dowiedzieć się więcej o wyodrębniania jednostki Pattern.any z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj [wzorzec](luis-tutorial-pattern.md) samouczka, aby dowiedzieć się więcej o sposobie używania jednostki Pattern.any.

Jeśli zauważysz, że Twoje wzorzec, gdy zawiera on Pattern.any, wyodrębnia jednostek, użyj [jawną listę](luis-concept-patterns.md#explicit-lists) Aby rozwiązać ten problem. 

## <a name="add-role-to-pattern-based-entity"></a>Dodaj rolę wzorca na podstawie jednostki
Rola to nazwane podtyp jednostki na podstawie kontekstu. Jest ona porównywalna ze [hierarchiczne](#add-hierarchical-entities) jednostki, ale ról są używane tylko w [wzorców](luis-how-to-model-intent-pattern.md). 

Na przykład bilet płaszczyzny ma *Miasto pochodzenia* i *miasta docelowego*, lecz jedne i drugie są miast. LUIS Określa, że oba są miasta i można ustalić początkowego i docelowego miast na podstawie kontekstu kolejność słów i wybór programu word. 

Składnia dla roli jest **{nazwa jednostki Nazwa: rola}** gdzie nazwa jednostki następuje dwukropek, a następnie nazwę roli. Na przykład "Book biletu pochodzenia {lokalizacji:} do {lokalizacji: Destination}".

1. W swojej aplikacji z **kompilacji** sekcji, a następnie wybierz **jednostek** w panelu po lewej stronie.

2. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę). Wprowadź nazwę `Location`. Wybierz typ **proste** i wybierz **gotowe**

3. Wybierz **jednostek** z panelu po lewej stronie, a następnie wybierz nową jednostkę **lokalizacji** utworzony w kroku 2.

4. W **nazwy roli** polu tekstowym wprowadź nazwę roli `Origin` i wprowadzić. Dodaj drugi nazwę roli programu `Destination`. Na przykład podróż płaszczyzny może mieć pochodzenia i miasta docelowego. Dostępne są dwie role "Origin" i "Miejsce docelowe".

    ![Zrzut ekranu przedstawiający Dodawanie roli źródła do lokalizacji jednostki](./media/add-entities/roles-enter-role-name-text.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md) dowiedzieć się więcej o wyodrębniania ról z punktu końcowego odpowiedź JSON na zapytanie. Wypróbuj samouczek wzorca, aby dowiedzieć się więcej o sposobie używania jednostki Pattern.any.

## <a name="add-list-entities"></a>Dodawanie listy jednostek
Lista jednostek reprezentują stały, zamknięte zbiór powiązanych słów w systemie. 

Dla jednostki listy w przeddzień, może mieć dwie wartości znormalizowane: wody i soda pop. Nazwa każdego znormalizowane ma synonimów. Wody synonimy są H20 i gaz, stałe. W przypadku soda pop synonimy są owoców, receptura, imbir. Nie trzeba znać wszystkie wartości, po utworzeniu jednostki. Możesz dodać więcej po zapoznaniu się z wypowiedzi rzeczywistego użytkownika przy użyciu synonimów.

|Znormalizowaną nazwę|Synonimy|
|--|--|
|Gospodarka wodna|H20, gaz, stosowana jest stała|
|Soda pop|Owoców, receptura, imbir|

1. W swojej aplikacji z **kompilacji** sekcji, a następnie kliknij przycisk **jednostek** w panelu po lewej stronie, a następnie wybierz **Utwórz nową jednostkę**.

2. W **Dodaj jednostkę** okno dialogowe, typ `Drinks` w **nazwa jednostki** pole, a następnie wybierz **listy** jako **typu jednostki**. Wybierz pozycję **Done** (Gotowe).
 
    ![Obraz okna dialogowego Tworzenie jednostki listy w przeddzień](./media/add-entities/menu-list-dialog.png)
  
3.  Strona jednostki listy umożliwia dodawanie nazw znormalizowana. W **wartości** polu tekstowym wprowadź element na liście, taką jak `Water` dla są w przeddzień listy, a następnie naciśnij klawisz Enter na klawiaturze. 

    ![Zrzut ekranu w przeddzień listy jednostek wodą znormalizowane wartości w polu tekstowym](./media/add-entities/entity-list-normalized-name.png)

4. Po prawej stronie wartość znormalizowana **wody**, wprowadź synonimy `h20`, `flat`, i `gas`, naciskając klawisz Enter na klawiaturze, po każdym elemencie.

    ![Zrzut ekranu w przeddzień listy jednostek z elementami synonimów do wody wyróżniony](./media/add-entities/menu-list-synonyms.png)

5. Więcej znormalizowane elementów na liście, zaznacz **zaleca się** Aby wyświetlić opcje z [semantycznego słownika](luis-glossary.md#semantic-dictionary).

    ![Zrzut ekranu pić obiektami listy przy użyciu elementów zalecanych wyróżniony](./media/add-entities/entity-list-recommended-list.png)

6. Wybierz element na liście zalecane, aby dodać je jako znormalizowaną wartość lub wybierz **Dodaj wszystkie** można dodać wszystkich elementów. 

    ![Zrzut ekranu pić listy jednostki z zalecanych elementu piwo i dodać wszystkie wyróżnionym](./media/add-entities/list-entity-add-suggestions.png)

    Zobacz [wyodrębnianie danych](luis-concept-data-extraction.md#list-entity-data) dowiedzieć się więcej o wyodrębniania listy jednostek z punktu końcowego odpowiedź JSON na zapytanie. Spróbuj [Szybki Start](luis-quickstart-intent-and-list-entity.md) Aby dowiedzieć się więcej o sposobie używania obiektami listy.

## <a name="import-list-entity-values"></a>Zaimportować listy wartości jednostki
Wartości można zaimportować do istniejącej jednostki listy.

 1. Na stronie jednostki listy wybierz **Importuj listy**.

 2. W **Importuj nowe wpisy** okno dialogowe, wybierz opcję **wybierz plik** i wybierz plik JSON, który zawiera listę.

    ![Zrzut ekranu importu lista jednostki wartości wyskakujące okno dialogowe](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >Usługa LUIS importuje pliki z rozszerzeniem "JSON" tylko.

 3. Aby dowiedzieć się więcej na temat składni listy obsługiwanych w formacie JSON, wybierz **Dowiedz się więcej na temat składni listy obsługiwanych** Rozwiń okno dialogowe i wyświetlić przykład składni dozwolone. Aby zwinąć okna dialogowego i ukryć składni, wybierz ponownie łącze tytuł.

 4. Wybierz pozycję **Done** (Gotowe).

    Przykład prawidłowy kod json dla **kolory** obiektami listy przedstawiono w poniższym kodzie formacie JSON:

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>Edytuj nazwę jednostki
1. Na **jednostek** strona listy, wybierz obiekt na liście. Ta akcja spowoduje przejście do **jednostki** strony.

2. Na **jednostki** strony edytowania nazwa jednostki, wybierając ikonę edycji obok nazwy jednostki. Typ jednostki nie jest edytowalny. 

## <a name="delete-entity"></a>Usuń jednostkę

Na **jednostki** wybierz opcję **usunąć jednostki** przycisku. Następnie wybierz **Ok** w komunikacie potwierdzenia, aby potwierdzić usunięcie.
 
![Zrzut ekranu z lokalizacji jednostki strony z wyróżnionym przyciskiem Usuń jednostkę](./media/add-entities/entity-delete.png)

>[!NOTE]
>* Usunięcie jednostki hierarchiczne powoduje usunięcie wszystkich jej obiektów podrzędnych.
>* Trwa usuwanie obiektu złożonego usuwa tylko złożone i przerywa złożonych relacji, ale nie powoduje usunięcia jednostki tworzących go.

## <a name="changing-entity-type"></a>Zmiana typu jednostki
Usługa LUIS nie pozwala zmienić typ jednostki, ponieważ nie ma informacji, co należy dodać lub usunąć do skonstruowania tej jednostki. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu o nazwie nieco inne. Po utworzeniu jednostki w każdym wypowiedź usuń starą nazwę jednostki oznaczone i dodać nową nazwę jednostki. Gdy ma zostać relabeled wszystkich wypowiedzi, należy usunąć stare jednostki. 

## <a name="create-a-pattern-from-an-utterance"></a>Tworzenie wzorca z wypowiedź
Zobacz [Dodaj wzorca od istniejących wypowiedź przeznaczenie lub jednostki strony](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Wyszukiwanie wypowiedzi
Możesz wyszukiwać i filtrować wypowiedzi z ikoną lupy na pasku narzędzi. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Uczenie aplikacji po zmianie modelu z jednostkami
Po dodaniu, edytować lub usunąć jednostki, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikacji zmiany wpływają na zapytania punktu końcowego. 

## <a name="next-steps"></a>Kolejne kroki
Teraz, że dodano intencje, wypowiedzi i podmioty, masz podstawową aplikację usługi LUIS. Dowiedz się, jak [szkolenie](luis-how-to-train.md), [test](luis-interactive-test.md), i [publikowania](luis-how-to-publish-app.md) aplikacji.
 
