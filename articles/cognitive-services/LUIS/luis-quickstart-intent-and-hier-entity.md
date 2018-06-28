---
title: Samouczek tworzenia aplikacji LUIS w celu uzyskania danych lokalizacji — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji i jednostki hierarchicznej do wyodrębnienia danych.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266502"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Samouczek: tworzenie aplikacji używającej jednostki hierarchicznej
W tym samouczku utworzysz aplikację, która pokazuje, jak znaleźć powiązane elementy danych na podstawie kontekstu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek hierarchicznych i wywnioskowanych z kontekstu elementów podrzędnych 
> * Tworzenie nowej aplikacji LUIS dla domeny travel z intencją Bookflight
> * Dodawanie intencji _None_ i dodawanie przykładowych wypowiedzi
> * Dodawanie jednostki hierarchicznej lokalizacji z elementami podrzędnymi miejsca początkowego i docelowego
> * Uczenie i publikowanie aplikacji
> * Wykonywanie względem punktu końcowego zapytania o aplikację w celu sprawdzenia odpowiedzi JSON usługi LUIS, w tym hierarchicznych elementów podrzędnych 

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="purpose-of-the-app-with-this-entity"></a>Przeznaczenie aplikacji z tą jednostką
Ta aplikacja określa, czy użytkownik chce zarezerwować lot. Używa jednostki hierarchicznej do określenia lokalizacji, miasta początkowego i miasta docelowego z tekstu użytkownika. 

Jednostka hierarchiczna jest odpowiednia dla tego typu danych, ponieważ oba elementy danych:

* Są lokalizacjami, zwykle określonymi przy użyciu nazwy miasta lub kodu lotniska.
* Zwykle wokół tych słów występują pewne unikatowe słowa, które pozwalają określić, co jest lokalizacją początkową, a co docelową. Te słowa zwykle obejmują: do, w kierunku, z, odlot.
* Obie lokalizacje często znajdują się w tej samej wypowiedzi. 

Celem jednostki **hierarchicznej** jest znalezienie powiązanych danych w wypowiedzi na podstawie kontekstu. Przeanalizujmy następującą wypowiedź:

```JSON
1 ticket from Seattle to Cairo`
```

W wypowiedzi są podane dwie lokalizacje. Jedna to miasto początkowe, Seattle, a drugie to miasto docelowe, Kair. Oba te miasta są ważne przy rezerwowaniu lotu. Chociaż można je znaleźć przy użyciu jednostek prostych, są ze sobą powiązane i często będą znajdowane w tej samej wypowiedzi. W związku z tym warto je zgrupować ze sobą jako elementy podrzędne jednostki hierarchicznej **„Location”**. 

Tak jak jednostki uczone maszynowo, aplikacja potrzebuje przykładowych wypowiedzi z oznaczonymi lokalizacjami początkowymi i docelowymi. To pokazuje usłudze LUIS, gdzie w wypowiedziach znajdują się jednostki, jakie są długie i jakie słowa są wokół nich. 

## <a name="app-intents"></a>Intencje aplikacji
Intencje to kategorie tego, czego chce użytkownik. Ta aplikacja ma dwie intencje: BookFlight i None. Intencja [None](luis-concept-intent.md#none-intent-is-fallback-for-app) jest zamierzona i wskazuje wszystko spoza aplikacji.  

## <a name="hierarchical-entity-is-contextually-learned"></a>Jednostka hierarchiczna jest uczona kontekstowo 
Celem jednostki jest znalezienie fragmentów tekstu w wypowiedzi i przypisanie im kategorii. Jednostka [hierarchiczna](luis-concept-entity-types.md) to jednostka typu „element nadrzędny — element podrzędny” bazująca na kontekście użycia. Osoba może określić miasto początkowe i docelowe w wypowiedzi na podstawie użycia słów `to` i `from`. Oto przykład użycia kontekstowego.  

Dla tej aplikacji Travel usługa LUIS wyodrębnia lokalizację początkową i docelową w taki sposób, aby można było utworzyć i wypełnić standardową rezerwację. Usługa LUIS pozwala na to, aby wypowiedzi zawierały wariacje, skróty i żargon. 

Proste przykładowe wypowiedzi użytkowników mogą być następujące:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

Wersje wypowiedzi ze skrótami lub żargonem mogą być następujące:

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
Jednostka hierarchiczna dopasowuje lokalizację początkową i docelową. Jeśli obecny jest tylko jeden element podrzędny jednostki hierarchicznej (lokalizacja początkowa lub docelowa), też jest wyodrębniany. Nie jest konieczne znalezienie wszystkich elementów podrzędnych, aby wyodrębnić jeden lub część z nich. 

## <a name="what-luis-does"></a>Jak działa usługa LUIS
Gdy intencje i jednostki wypowiedzi zostaną zidentyfikowane, [wyodrębnione](luis-concept-data-extraction.md#list-entity-data) i zwrócone w formacie JSON z [punktu końcowego](https://aka.ms/luis-endpoint-apis), działanie usługi LUIS kończy się. Aplikacja wywołująca lub czatbot pobiera tę odpowiedź JSON i spełnia żądanie — w taki sposób, jaki został zaprojektowany. 

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
1. Zaloguj się w witrynie internetowej usługi [LUIS][LUIS]. Pamiętaj, aby zalogować się w [regionie][LUIS-regions], w którym mają zostać opublikowane punkty końcowe usługi LUIS.

2. W witrynie internetowej usługi [LUIS][LUIS] wybierz pozycję **Create new app** (Utwórz nową aplikację).  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Zrzut ekranu ze stroną App lists (Listy aplikacji)")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. W wyskakującym oknie dialogowym wprowadź nazwę `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "Zrzut ekranu z wyskakującym oknem dialogowym Tworzenie nowej aplikacji")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. Po zakończeniu tego procesu aplikacja wyświetli stronę **Intents** (Intencje) z intencją **None**. 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "Zrzut ekranu z listą Intents (Intencje) i samą intencją None")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Tworzenie nowej intencji

1. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję). 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "Zrzut ekranu z listą Intents (Intencje) i wyróżnionym przyciskiem Create new intent (Utwórz nową intencję)")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Wprowadź nazwę nowej intencji: `BookFlight`. Ta intencja powinna być wybierana za każdym razem, gdy użytkownik chce zarezerwować loty.

    Tworząc intencję, tworzysz główną kategorię informacji, którą chcesz identyfikować. Nadanie nazwy kategorii umożliwia każdej innej aplikacji, która używa wyników zapytania usługi LUIS, zastosowanie tej nazwy kategorii w celu znalezienia odpowiedniej odpowiedzi lub podjęcia odpowiedniej akcji. Usługa LUIS nie będzie odpowiadać na te pytania — określi jedynie, jakiego rodzaju informacji dotyczy pytanie w języku naturalnym. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "Zrzut ekranu z wyskakującym oknem dialogowym Create new intent (Tworzenie nowej intencji)")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Dodaj kilka wypowiedzi do intencji `BookFlight` — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |Zabookuj 2 bilety z Seattle do Kairu na następny poniedziałek|
    |Zarezerwuj bilet do Londynu na jutro|
    |Zamów 4 miejsca z Paryża do Londynu na 1 kwietnia|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "Zrzut ekranu z wprowadzaniem wyrażeń na stronie intencji aplikacji BookFlight")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Dodawanie wypowiedzi do intencji None

Aplikacja LUIS obecnie nie zawiera żadnych wypowiedzi dla intencji **None**. Potrzebne są wypowiedzi, na które aplikacja ma nie odpowiadać, dlatego należy dodać wypowiedzi do intencji **None**. Nie zostawiaj jej pustej. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu. 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "Zrzut ekranu ze stroną intencji aplikacji BookFlight i wyróżnionym przyciskiem Intents (Intencje)")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji:

    | Przykładowe wypowiedzi|
    |--|
    |Anuluj!|
    |Do widzenia|
    |Co się dzieje?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Kiedy dla intencji None jest przewidziana wypowiedź
W aplikacji wywołującej usługę LUIS (takiej jak czatbot), gdy usługa LUIS zwraca intencję **None** dla wypowiedzi, bot może zadać pytanie, czy użytkownik chce zakończyć konwersację. Bot może również podać więcej wskazówek umożliwiających kontynuowanie konwersacji, jeśli użytkownik nie chce jej zakończyć. 

Jednostki działają w intencji **None**. Jeśli najwyżej ocenianą intencją jest intencja **None**, ale wyodrębniona jednostka ma znaczenie dla czatbota, to czatbot może zadać pytanie, które spowoduje ukierunkowanie intencji klienta. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Tworzenie jednostki lokalizacji z poziomu strony Intencja
Teraz, kiedy te dwie intencje mają wypowiedzi, usługa LUIS musi zrozumieć, czym jest lokalizacja. Przejdź z powrotem do intencji `BookFlight` i oznacz nazwę miasta w wypowiedzi, wykonując następujące kroki:

1. Wróć do intencji `BookFlight`, wybierając pozycję **Intents** (Intencje) w lewym panelu.

2. Wybierz pozycję `BookFlight` z listy intencji.

3. W wypowiedzi `Book 2 flights from Seattle to Cairo next Monday` zaznacz wyraz `Seattle`. Zostanie wyświetlone menu rozwijane z polem tekstowym w górnej części, umożliwiającym utworzenie nowej jednostki. Wprowadź nazwę jednostki `Location` w polu tekstowym, a następnie wybierz polecenie **Create new entity** (Utwórz nową jednostkę) w menu rozwijanym. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "Zrzut ekranu strony intencji BookFlight podczas tworzenia nowej jednostki na podstawie zaznaczonego tekstu")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. W oknie podręcznym wybierz typ jednostki **Hierarchical** (Hierarchiczna) z elementami `Origin` i `Destination` jako podrzędnymi. Wybierz pozycję **Done** (Gotowe).

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Zrzut ekranu z wyskakującym oknem dialogowym tworzenia jednostki dla nowej jednostki Location (Lokalizacja)")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    Etykieta dla elementu `Seattle` jest oznaczona jako `Location`, ponieważ usługa LUIS nie może ustalić, czy termin jest lokalizacją początkową, docelową czy żadną z nich. Wybierz pozycję `Seattle`, wybierz pozycję Location (Lokalizacja), a następnie przejdź do menu z prawej strony i wybierz pozycję `Origin`.

5. Teraz, gdy jednostka jest utworzona i jedna wypowiedź ma etykiety, oznacz etykietami inne miasta, wybierając nazwę miasta i pozycję Location (Lokalizacja), a następnie z menu po prawej wybierając pozycję `Origin` lub `Destination`.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Zrzut ekranu z jednostką Bookflight i zaznaczonym tekstem wypowiedzi w celu wybrania jednostki")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Uczenie aplikacji](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Uczenie powiodło się](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Zrzut ekranu z intencją BookFlight i wyróżnionym przyciskiem Publish (Opublikuj)")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `1 ticket to Portland on Friday`. Ostatni parametr ciągu zapytania to `q`, czyli **q**uery (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `BookFlight` z wyodrębnioną jednostką hierarchiczną.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, zawierająca jedynie dwie intencje i jednostkę hierarchiczną, zidentyfikowała intencję zapytania w języku naturalnym i zwróciła wyodrębnione dane. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić akcję główną (`BookFlight`) i informacje o lokalizacji znalezione w wypowiedzi. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostkę listy](luis-quickstart-intent-and-list-entity.md) 

Dodawanie [wstępnie utworzonej jednostki](luis-how-to-add-entities.md#add-prebuilt-entity) **number** (numer) w celu wyodrębnienia numeru. 

Dodawanie [wstępnie utworzonej jednostki](luis-how-to-add-entities.md#add-prebuilt-entity) **datetimeV2** (data/godzina w wersji 2) w celu wyodrębnienia informacji o dacie.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
