---
title: Samouczek dotyczący tworzenia aplikacji LUIS w celu uzyskania tekstu dokładnie pasującego do danych na liście — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji i jednostek listy, aby wyodrębnić dane w tym przewodniku Szybki start.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264831"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Samouczek: tworzenie aplikacji przy użyciu jednostki listy
W tym samouczku utworzysz aplikację, która pokazuje, jak uzyskać dane zgodne ze wstępnie zdefiniowaną listą. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek listy 
> * Tworzenie nowej aplikacji LUIS dla domeny napojów z intencją OrderDrinks
> * Dodawanie intencji _None_ i dodawanie przykładowych wypowiedzi
> * Dodawanie jednostki listy w celu wyodrębnienia elementów napojów z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="purpose-of-the-list-entity"></a>Przeznaczenie jednostki listy
Ta aplikacja pobiera zamówienia na napoje, takie jak `1 coke and 1 milk please`, i zwraca dane, takie jak typ napoju. Jednostka **listy** napojów szuka dokładnych dopasowań tekstu i zwraca te dopasowania. 

Jednostka listy jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli wartości danych są znanym zestawem. Nazwy napojów mogą się różnić w zależności od żargonu i skrótów, jednak nie zmieniają się one zbyt często. 

## <a name="app-intents"></a>Intencje aplikacji
Intencje to kategorie tego, czego chce użytkownik. Ta aplikacja ma dwie intencje: OrderDrink i None. Intencja [None](luis-concept-intent.md#none-intent-is-fallback-for-app) jest zamierzona i wskazuje wszystko spoza aplikacji.  

## <a name="list-entity-is-an-exact-text-match"></a>Jednostka listy jest dokładnym dopasowaniem tekstu
Celem jednostki jest znalezienie fragmentów tekstu w wypowiedzi i przypisanie im kategorii. Jednostka [listy](luis-concept-entity-types.md) umożliwia dokładne dopasowanie słów i fraz.  

W przypadku tej aplikacji napojów aplikacja LUIS wyodrębnia zamówienie na napój w taki sposób, że umożliwia utworzenie i wypełnienie standardowego zamówienia. Usługa LUIS pozwala na to, aby wypowiedzi zawierały wariacje, skróty i żargon. 

Proste przykładowe wypowiedzi użytkowników mogą być następujące:

```
2 glasses of milk
3 bottles of water
2 cokes
```

Wersje wypowiedzi ze skrótami lub żargonem mogą być następujące:

```
5 milk
3 h2o
1 pop
```
 
Jednostka listy dopasowuje ciąg `h2o` do wody, a ciąg `pop` do napoju bezalkoholowego.  

## <a name="what-luis-does"></a>Jak działa usługa LUIS
Gdy intencje i jednostki wypowiedzi zostaną zidentyfikowane, [wyodrębnione](luis-concept-data-extraction.md#list-entity-data) i zwrócone w formacie JSON z [punktu końcowego](https://aka.ms/luis-endpoint-apis), działanie usługi LUIS kończy się. Aplikacja wywołująca lub czatbot pobiera tę odpowiedź JSON i spełnia żądanie — w taki sposób, jaki został zaprojektowany. 

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
1. Zaloguj się w witrynie internetowej usługi [LUIS][LUIS]. Pamiętaj, aby zalogować się w [regionie][LUIS-regions], w którym mają zostać opublikowane punkty końcowe usługi LUIS.

2. W witrynie internetowej usługi [LUIS][LUIS] wybierz pozycję **Create new app** (Utwórz nową aplikację).  

    ![Tworzenie nowej aplikacji](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. W wyskakującym oknie dialogowym wprowadź nazwę `MyDrinklist`. 

    ![Nadanie aplikacji nazwy MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. Po zakończeniu tego procesu aplikacja wyświetli stronę **Intents** (Intencje) z intencją **None**. 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Zrzut ekranu przedstawiający stronę Intents (Intencje)")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Tworzenie nowej intencji

1. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję). 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Zrzut ekranu przedstawiający stronę Intents (Intencje) z wyróżnionym przyciskiem Create new intent (Utwórz nową intencję)")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Wprowadź nazwę nowej intencji: `OrderDrinks`. Ta intencja powinna być wybierana za każdym razem, gdy użytkownik chce zamówić napój.

    Tworząc intencję, tworzysz główną kategorię informacji, którą chcesz identyfikować. Nadanie nazwy kategorii umożliwia każdej innej aplikacji, która używa wyników zapytania usługi LUIS, zastosowanie tej nazwy kategorii w celu znalezienia odpowiedniej odpowiedzi lub podjęcia odpowiedniej akcji. Usługa LUIS nie będzie odpowiadać na te pytania — określi jedynie, jakiego rodzaju informacji dotyczy pytanie w języku naturalnym. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Zrzut ekranu przedstawiający tworzenie nowej intencji OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Dodaj kilka wypowiedzi do intencji `OrderDrinks` — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |Proszę wysłać 2 cole i butelkę wody do mojego pokoju|
    |2 wody perrier z odrobiną limonki|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Zrzut ekranu przedstawiający wprowadzanie wypowiedzi na stronie intencji OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Dodawanie wypowiedzi do intencji None

Aplikacja LUIS obecnie nie zawiera żadnych wypowiedzi dla intencji **None**. Potrzebne są wypowiedzi, na które aplikacja ma nie odpowiadać, dlatego należy dodać wypowiedzi do intencji **None**. Nie zostawiaj jej pustej. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Zrzut ekranu przedstawiający wybieranie linku Intents (Intencje) w lewym panelu")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji:

    | Przykładowe wypowiedzi|
    |--|
    |Anuluj!|
    |Do widzenia|
    |Co się dzieje?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Kiedy dla intencji None jest przewidziana wypowiedź
W aplikacji wywołującej usługę LUIS (takiej jak czatbot), gdy usługa LUIS zwraca intencję **None** dla wypowiedzi, bot może zadać pytanie, czy użytkownik chce zakończyć konwersację. Bot może również podać więcej wskazówek umożliwiających kontynuowanie konwersacji, jeśli użytkownik nie chce jej zakończyć. 

Jednostki działają w intencji **None**. Jeśli najwyżej ocenianą intencją jest intencja **None**, ale wyodrębniona jednostka ma znaczenie dla czatbota, to czatbot może zadać pytanie, które spowoduje ukierunkowanie intencji klienta. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Tworzenie jednostki menu z poziomu strony Intent (Intencja)
Teraz, kiedy te dwie intencje mają wypowiedzi, aplikacja LUIS musi zrozumieć, czym jest napój. Przejdź z powrotem do intencji `OrderDrinks` i oznacz napoje w wypowiedzi, wykonując następujące kroki:

1. Wróć do intencji `OrderDrinks`, wybierając pozycję **Intents** (Intencje) w lewym panelu.

2. Wybierz pozycję `OrderDrinks` z listy intencji.

3. W wypowiedzi `Please send 2 cokes and a bottle of water to my room` zaznacz wyraz `water`. Zostanie wyświetlone menu rozwijane z polem tekstowym w górnej części, umożliwiającym utworzenie nowej jednostki. Wprowadź nazwę jednostki `Drink` w polu tekstowym, a następnie wybierz polecenie **Create new entity** (Utwórz nową jednostkę) w menu rozwijanym. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Zrzut ekranu przedstawiający tworzenie nowej jednostki przez zaznaczenie wyrazu w wypowiedzi")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. W oknie podręcznym wybierz typ jednostki **List** (Lista). Dodaj synonim `h20`. Po wprowadzeniu każdego synonimu naciśnij klawisz Enter. Nie dodawaj wyrazu `perrier` do listy synonimów. Zostanie on dodany w kolejnym kroku jako przykład. Wybierz pozycję **Done** (Gotowe).

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Zrzut ekranu przedstawiający konfigurowanie nowej jednostki")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Teraz, po utworzeniu jednostki, oznacz pozostałe synonimy wyrazu „woda”, zaznaczając je, a następnie wybierając pozycję `Drink` na liście rozwijanej. Przejdź do menu z prawej strony, a następnie wybierz polecenie `Set as synonym` i pozycję `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Zrzut ekranu przedstawiający oznaczanie wypowiedzi za pomocą istniejącej jednostki")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Modyfikowanie jednostki listy na stronie Entity (Jednostka)
Jednostka listy napojów została utworzona, jednak nie zawiera wielu pozycji ani synonimów. Jeśli znasz niektóre terminy, skróty i żargon, szybciej będzie wypełnić listę na stronie **Entity** (Jednostka). 

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Zrzut ekranu przedstawiający wybieranie pozycji Entities (Jednostki) w lewym panelu")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Wybierz pozycję `Drink` na liście jednostek.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Zrzut ekranu przedstawiający wybieranie jednostki Drink na liście jednostek")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. W polu tekstowym wprowadź ciąg `Soda pop`, a następnie naciśnij klawisz Enter. Jest to termin szeroko stosowany w języku angielskim do określania napojów gazowanych. W każdej kulturze istnieje jakaś nazwa lub termin żargonowy określający ten typ napoju.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Zrzut ekranu przedstawiający wprowadzanie nazwy kanonicznej")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. W tym samym wierszu, w którym znajduje się termin `Soda pop`, wprowadź synonimy takie jak: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    Synonimy mogą zawierać frazy, znaki interpunkcyjne, zaimki dzierżawcze i liczbę mnogą. Ponieważ jednostka listy jest dokładnym dopasowaniem tekstu (z wyjątkiem wielkości liter), każdy synonim musi występować we wszystkich odmianach. Listę można poszerzać po poznaniu kolejnych wariacji na podstawie dzienników zapytania lub trafień punktu końcowego. 

    W tym artykule użyto tylko kilku synonimów, aby przykład nie był zbyt długi. Aplikacja LUIS na poziomie produkcyjnym zawierałaby wiele synonimów, które byłyby regularnie przeglądane i dodawane. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Zrzut ekranu przedstawiający dodawanie synonimów")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Uczenie aplikacji](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Uczenie powiodło się](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Zrzut ekranu przedstawiający wybieranie przycisku Publish (Publikuj)")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Zrzut ekranu przedstawiający wybieranie przycisku publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Zrzut ekranu przedstawiający adres URL punktu końcowego na stronie publikowania")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `2 cokes and 3 waters`. Ostatni parametr ciągu zapytania to `q`, czyli **q**uery (zapytanie) wypowiedzi. Ta wypowiedź jest inna, niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `OrderDrinks` z dwoma typami napojów: `cokes` i `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>W którym miejscu jednostki listy jest wykonywane przetwarzanie języka naturalnego? 
Jednostka listy jest dokładnym dopasowaniem tekstu, dlatego nie opiera się na przetwarzaniu języka naturalnego (ani na uczeniu maszynowym). Aplikacja LUIS korzysta z przetwarzania języka naturalnego (lub uczenia maszynowego), aby wybrać właściwą najwyżej ocenianą intencję. Ponadto wypowiedź może zawierać kilka jednostek lub nawet kilka typów jednostek. Każda wypowiedź jest przetwarzana po kątem wszystkich jednostek w aplikacji, w tym jednostek przetwarzania języka naturalnego (lub nauczonych maszynowo), takich jak jednostka **Simple**.

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, zawierająca jedynie dwie intencje i jednostkę listy, zidentyfikowała intencję zapytania w języku naturalnym i zwróciła wyodrębnione dane. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić akcję główną, `OrderDrinks`, i typy napojów, które zostały zamówione z jednostki listy Drink. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje na temat dodawania jednostki wyrażenia regularnego](luis-quickstart-intents-regex-entity.md)

Dodawanie [wstępnie utworzonej jednostki](luis-how-to-add-entities.md#add-prebuilt-entity) **number** (numer) w celu wyodrębnienia numeru. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
