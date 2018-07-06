---
title: Samouczek przy użyciu listy frazę w celu przewidywania usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dodać listy fraz z aplikacją usługi LUIS i zobacz ulepszanie wynik.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868977"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Samouczek: Dodawanie listy frazę w celu poprawienia prognozy
W tym samouczku, zwiększyć dokładność wyników intencji i zidentyfikować jednostki dla słowa, które mają takie samo znaczenie (synonimy), dodając wymienne [funkcji listy fraz](./luis-concept-feature.md).

> [!div class="checklist"]
* Zaimportuj nową aplikację  
* Punkt końcowy zapytanie, za pomocą znanych wypowiedź 
* Punkt końcowy z zapytania _nieznany_ wypowiedź
* Dodawanie listy frazę w celu poprawienia wypowiedź nieznany wynik
* Upewnij się, że jednostka zostanie znaleziony, używając listy fraz

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="import-a-new-app"></a>Zaimportuj nową aplikację
1. Pobierz [przykład aplikacją usługi LUIS] [ LuisSampleApp] zaprojektowaną na potrzeby tego samouczka. Użyjesz go w następnym kroku. 

2. Zgodnie z opisem w [tworzenie aplikacji](Create-new-app.md#import-new-app), zaimportuj plik który został pobrany do [LUIS] [ LUIS] witryny sieci Web jako nową aplikację. Nazwa aplikacji jest "Mój samouczek listy fraz." Posiada intencji, jednostek i wypowiedzi. 

3. [Szkolenie](luis-how-to-train.md) aplikacji. Do momentu jego przygotowaniu nie [interaktywnie test](interactive-test.md#interactive-testing) go w [LUIS] [ LUIS] witryny sieci Web. 

4. Na [Publikuj](luis-how-to-publish-app.md) wybierz opcję **Uwzględnij wszystkie przewidywane wyniki intencji** pole wyboru. Gdy pole wyboru jest zaznaczone, zwracane są wszystkie opcje. Gdy pole wyboru jest wyczyszczone, zwracany jest najważniejsze intencji. 

5. [Publikowanie](luis-how-to-publish-app.md) aplikacji. Publikowanie aplikacji, można ją przetestować przy użyciu punktu końcowego HTTPS. 

## <a name="test-a-trained-utterance"></a>Testowanie uczonego wypowiedź
Użycie opublikowanej punktu końcowego do wykonywania zapytań wypowiedź, że aplikacja jest już zna. Ponieważ usługa LUIS już zna wypowiedź, wynik jest wysoki i wykryciu jednostki.

1. Na [Language Understanding (LUIS)] [ LUIS] witryny sieci Web na **Publikuj** strony dla nowej aplikacji, wybierz adres URL punktu końcowego w **zasobów i klucze**sekcji. 

    ![Opublikuj adres URL punktu końcowego](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. W przeglądarce, na końcu adresu URL, Dodaj następujące zapytanie po `q=`.

    `I want a computer replacement`

    Punkt końcowy odpowiada następującym kodem JSON:
    
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

    Wynik konwersji 0.973 i wynik wykrywania jednostki 0.846 są wysokie, ponieważ aplikacja została wyszkolonego przy tym wypowiedź. Wypowiedź znajduje się w aplikacji usługi LUIS na stronie intencji **GetHardware**. Tekstowe wypowiedź `computer`, jest oznaczona jako **sprzętu** jednostki. 
    
    |Stan|Word| Wynik konwersji | Wynik jednostki |
    |--|--|--|--|
    |Przeszkoleni| chcesz | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testowanie nieprzeszkolonych wypowiedź
W przeglądarce za pomocą tego samego opublikowanych punktu końcowego do wykonywania zapytań wypowiedź, aplikacja już nie wiesz, że:

`I require a computer replacement`

Ta wypowiedź używa synonim poprzedniego wypowiedź:

| Uczony programu word | Synonim nieprzeszkolonych |
|--|--|
| chcesz | Wymagaj |

Odpowiedź na punkt końcowy jest:

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
| Przeszkoleni| chcesz | 0.973 | 0.846 |
| Nieprzeszkolonych| Wymagaj | 0.840 | - |

Wynik konwersji nieprzeszkolonych wypowiedź jest mniejszy niż z etykietami wypowiedź, ponieważ usługa LUIS wie, że zdanie gramatycznie jest taka sama. Jednak usługa LUIS nie wie, że wypowiedzi mają takie samo znaczenie. Ponadto bez listy fraz **sprzętu** nie znaleziono jednostki.

Musi uczyć LUIS *ma* i *wymagają* oznaczają to samo w tej domenie aplikacji, ponieważ słowo może mieć więcej niż jeden znaczenie. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Poprawa wynik nieprzeszkolonych wypowiedź przy użyciu listy fraz 
1. Dodaj [listy fraz](luis-how-to-add-features.md) funkcji o nazwie **ma** z wartością `want`, a następnie wybierz pozycję **Enter**.

    > [!TIP]
    > Po każdy wyraz lub frazę, wybierz **Enter** klucza. Wyraz lub frazę, zostanie dodany do **frazę wartości listy** pola, gdy kursor pozostaje w **wartość** pole. Można wprowadzić wiele wartości, szybko przy użyciu tej funkcji.

2. Zaznacz, aby wyświetlić wyrazy, które usługa LUIS zaleca **zaleca się**. 

    ![Zaleca się wartości](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Dodaj wszystkie wyrazy. Jeśli `require` jest nie w liście zalecanych Dodaj ją jako wymaganą wartość. 

4. Ponieważ te wyrazy są synonimy, Zachowaj *wymienne* ustawienia, a następnie wybierz **Zapisz**.

    ![Wyrażenie wartości listy](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. Na górnym pasku nawigacyjnym, wybierz **szkolenie** to w opracowywaniu aplikacji, ale nie opublikujesz zmian. Masz teraz dwa modele. Można porównać wartości w dwóch modeli.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Porównaj modelu listy frazę do opublikowanego modelu
W tej aplikacji opublikowany model nie jest uczony przy użyciu synonimów. Tylko aktualnie edytowany model zawiera listę frazy synonimów. Aby porównać te modele, należy użyć [interaktywne testowania](interactive-test.md#interactive-testing). 

1. Otwórz **testu** okienka, a następnie wprowadź następujące wypowiedź:

    `I require a computer replacement`

2. Aby otworzyć panel inspekcji, zaznacz **Sprawdź**. 

    ![Wybierz opcję Sprawdź](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Aby porównać opublikowany model do nowego modelu listy fraz, wybierz **Porównaj z opublikowanych**.

    ![Zbadaj opublikowane w stosunku do bieżącego](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Po dodaniu listy fraz większą dokładność wypowiedź i **sprzętu** odnaleźć jednostki. 

|Stan | Lista fraz| Wynik konwersji | Wynik jednostki |
|--|--|--|--|
| Opublikowano | - | 0,84 | - |
| Trwa edytowanie |✔| 0.92 | Podmiot sprzętu |

> [!TIP]
> * Za pomocą [interaktywne testowania](interactive-test.md#interactive-testing), można porównać opublikowany model w celu zmiany przeszkolonych, które zostały wprowadzone po opublikowaniu. 
> * Za pomocą [punkt końcowy testowania](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser), można wyświetlić dokładną odpowiedzi usługi LUIS JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Uzyskaj ocenę jednostki z testem punktu końcowego
Aby wyświetlić wynik jednostki [Opublikuj model](luis-how-to-publish-app.md) i wykonywania zapytań względem punktu końcowego. 

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

**Sprzętu** jednostki pokazuje wynik 0.595 przy użyciu listy fraz. Przed wprowadzeniem listy fraz, jednostki nie została wykryta. 

|Stan | Lista fraz| Wynik konwersji | Wynik jednostki |
|--|--|--|--|
| Opublikowano | - | 0,84 | - |
| Trwa edytowanie |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz przycisk wielokropka (***...*** ) po prawej stronie nazwy aplikacji, na liście aplikacji wybierz **Usuń**. W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pobierz prognozowania wypowiedź za pomocą kwerendy punktu końcowego](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
