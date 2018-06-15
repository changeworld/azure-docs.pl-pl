---
title: Samouczek przy użyciu listy frazy zwiększające prognoz LUIS - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dodać listę frazę do aplikacji LUIS i zobacz poprawy wynik.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 4ced7bcec87a9edde2e3ded8c8c61abe96003572
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356084"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Samouczek: Dodaj wyrażenie listy w celu poprawienia prognoz
W tym samouczku, zwiększyć dokładność wyniki konwersji i zidentyfikować jednostek wyrazy, które mają takie samo znaczenie (synonimy), dodając wymienne [funkcja listy frazy](./luis-concept-feature.md).

> [!div class="checklist"]
* Importuj nowej aplikacji  
* Punkt końcowy zapytania ze znanych utterance 
* Punkt końcowy z zapytania _nieznany_ utterance
* Dodawanie listy frazę w celu poprawienia utterance nieznany wynik
* Sprawdź, czy jednostka zostanie znaleziony, używając listy frazy

W tym artykule należy bezpłatny [LUIS] [ LUIS] konta, aby można było tworzyć LUIS aplikacji.

## <a name="import-a-new-app"></a>Importuj nowej aplikacji
1. Pobierz [przykład aplikacji LUIS] [ LuisSampleApp] zaprojektowaną do celów tego samouczka. Zostanie użyty w następnym kroku. 

2. Zgodnie z opisem w [Utwórz aplikację](Create-new-app.md#import-new-app), zaimportuj plik do [LUIS] [ LUIS] witryny sieci Web jako nową aplikację. Nazwa aplikacji jest "Mój frazy samouczek." Składa się z lokalizacji docelowych, jednostki i zniesławiających. 

3. [Train](luis-how-to-train.md) aplikacji. Dopiero po jego przygotowaniu nie [interaktywnie test](Train-Test.md#interactive-testing) w [LUIS] [ LUIS] witryny sieci Web. 

4. Na [publikowania](PublishApp.md) wybierz pozycję **uwzględnianie wszystkich przewidzieć wyniki konwersji** pole wyboru. Po zaznaczeniu pola wyboru, zwracane są wszystkie opcje. Gdy pole wyboru jest wyczyszczone, jest zwracany top celem. 

5. [Publikowanie](PublishApp.md) aplikacji. Publikowanie aplikacji umożliwia przetestować go przy użyciu punktu końcowego protokołu HTTPS. 

## <a name="test-a-trained-utterance"></a>Testowanie przeszkolone utterance
Umożliwia zapytania utterance, która aplikacja już zna opublikowanych punktu końcowego. Ponieważ LUIS zna już utterance, wynik jest wysoka, a jednostka została wykryta.

1. Na [opis języka (LUIS)] [ LUIS] witryny sieci Web na **publikowania** strony dla nowej aplikacji, wybierz adres URL punktu końcowego w **zasobów i klucze**sekcji. 

    ![Publikuj adres URL punktu końcowego](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. W przeglądarce na końcu adresu URL, Dodaj następujące zapytanie po `q=`.

    `I want a computer replacement`

    Punkt końcowy odpowiada JSON następujące:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    Wynik konwersji 0.973 i wynik wykrywania jednostki 0.846 są wysoka, ponieważ aplikacja została uczone z tym utterance. Utterance znajduje się w aplikacji LUIS na stronie konwersji **GetHardware**. Tekst utterance `computer`, jest oznaczone jako **sprzętu** jednostki. 
    
    |Stan|Word| Wynik konwersji | Wynik jednostki |
    |--|--|--|--|
    |Uczony| chcesz | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testowanie nieprzeszkolonych utterance
W przeglądarce za pomocą tego samego punktu końcowego opublikowanych w zapytaniu utterance, która aplikacja już nie może ustalić:

`I require a computer replacement`

Ta utterance używa synonim poprzedniej utterance:

| Przeszkolone programu word | Synonim nieprzeszkolonych |
|--|--|
| chcesz | Wymagaj |

Odpowiedź punktu końcowego jest:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Stan | Word | Wynik konwersji | Wynik jednostki |
|--|--|--|--|
| Uczony| chcesz | 0.973 | 0.846 |
| Nieprzeszkolonych| Wymagaj | 0.840 | - |

Wynik konwersji nieprzeszkolonych utterance jest mniejszy niż utterance etykietą, ponieważ LUIS wie, że zdanie gramatycznie jest taka sama. Ale LUIS nie wie, że zniesławiających mają takie samo znaczenie. Ponadto bez listy frazy **sprzętu** nie znaleziono jednostki.

Muszą uczyć LUIS *mają* i *wymagają* oznaczają to samo w tej domenie aplikacji, ponieważ wyraz może mieć więcej niż jeden znaczenie. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Poprawa wynik nieprzeszkolonych utterance z listą frazy 
1. Dodaj [listy frazy](luis-how-to-add-features.md) funkcji o nazwie **mają** z wartością `want`, a następnie wybierz **Enter**.

    > [!TIP]
    > Po każdym wyraz lub frazę, wybierz **Enter** klucza. Wyraz lub frazę zostanie dodany do **frazę wartości listy** polu, gdy wskaźnik pozostaje w **wartość** pole. Można wprowadzić wiele wartości szybko przy użyciu tej funkcji.

2. Zaznacz, aby wyświetlić słowa, które zaleca LUIS **zaleca**. 

    ![Zaleca się wartości](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Dodaj wszystkie wyrazy. Jeśli `require` jest spoza listy zalecane, dodaj go jako wymaganej wartości. 

4. Ponieważ te wyrazy są synonimy, Zachowaj *wymienne* ustawienia, a następnie wybierz **zapisać**.

    ![Wyrażenie wartości listy](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. W górnym pasku nawigacyjnym, wybierz **uczenia** do uczenia aplikacji, ale nie publikuj go. Masz teraz dwa modele. Możesz porównać wartości w obu modeli.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Porównywanie modelu listy frazę do opublikowanego modelu
W tej aplikacji opublikowanych modelu nie jest uczenia z synonimy. Aktualnie edytowany model zawiera listę frazy synonimów. Aby porównać modele, użyj [interakcyjne testowania](Train-Test.md#interactive-testing). 

1. Otwórz **testu** okienku, a następnie wprowadź następujące utterance:

    `I require a computer replacement`

2. Aby otworzyć panel inspekcji, wybierz **inspekcję**. 

    ![Wybierz inspekcji](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Aby porównać opublikowanego modelu do nowego modelu listy frazy, wybierz **opublikowane Porównaj z elementem**.

    ![Sprawdź opublikowane w porównaniu z bieżącego](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Po dodaniu listy frazy większą dokładność utterance i **sprzętu** odnaleźć jednostki. 

|Stan | Lista fraz| Wynik konwersji | Wynik jednostki |
|--|--|--|--|
| Opublikowano | - | 0,84 | - |
| Obecnie edycji |✔| 0.92 | Podmiot sprzętu |

> [!TIP]
> * Za pomocą [interakcyjne testowania](Train-Test.md#interactive-testing), możesz porównać opublikowanego modelu do wszelkich przeszkolone zmian wprowadzonych po opublikowaniu. 
> * Za pomocą [testowania punktu końcowego](PublishApp.md#test-your-published-endpoint-in-a-browser), można wyświetlić dokładna reakcja LUIS JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Pobierz wynik jednostki z testem punktu końcowego
Aby wyświetlić wynik jednostki [opublikować model](PublishApp.md) i zapytania punktu końcowego. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

**Sprzętu** jednostki przedstawia wynik 0.595 z listą frazę. Przed wprowadzeniem listy frazy, jednostka nie została wykryta. 

|Stan | Lista fraz| Wynik konwersji | Wynik jednostki |
|--|--|--|--|
| Opublikowano | - | 0,84 | - |
| Obecnie edycji |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie są już potrzebne, usunięcie aplikacji LUIS. Aby to zrobić, należy wybrać menu trzy kropki (...), z prawej strony nazwy aplikacji na liście aplikacji zaznacz **usunąć**. W wyskakującym oknie dialogowym **aplikacji Usuń?**, wybierz pozycję **Ok**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pobierz utterance prognozowania z zapytaniem punktu końcowego](luis-get-started-cs-get-intent.md)

[LUIS]: luis-reference-regions.md

  [LUIS]:luis-reference-regions.md
  [LuisFeatures]: luis-concept-feature.md
  [LuisSampleApp]:https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
