---
title: Wstępnie utworzone modele dla Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS zawiera zestaw wstępnie skompilowanych modeli umożliwiających szybkie dodawanie wspólnych scenariuszy użytkowników.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507782"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Dodaj wstępnie zbudowane modele dla typowych scenariuszy użycia 

LUIS zawiera zestaw prekompilowanych intencji z prekompilowanych domen umożliwiających szybkie dodawanie typowych intencji i wyrażenia długości. Jest to szybki i łatwy sposób dodawania możliwości do konwersacji aplikacji klienckiej bez konieczności zaprojektowania modeli. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

1. Na stronie **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji w sekcji **kompilacja** aplikacji. 

1. Na stronie **intencje** wybierz pozycję **Dodaj wstępnie skompilowane domeny** na dole, po lewej stronie. 

1. Wybierz opcję **Kalendarz** , a następnie wybierz przycisk **Dodaj domenę** .

    ![Dodaj prezbudowaną domenę kalendarza](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Wybierz pozycję **intencje** na lewym pasku nawigacyjnym, aby wyświetlić intencje kalendarza. Każdy cel z tej domeny jest poprzedzony `Calendar.`. Wraz z wyrażenia długości do aplikacji dodawane są dwie jednostki dla tej domeny: `Calendar.Location` i `Calendar.Subject`. 

### <a name="train-and-publish"></a>Uczenie i publikowanie

1. Po dodaniu domeny nauczenie aplikacji przez wybranie pozycji **uczenie** w górnym prawym pasku narzędzi. 

1. Na górnym pasku narzędzi wybierz pozycję **Publikuj**. Publikuj w **środowisku produkcyjnym**. 

1. Po wyświetleniu zielonego powiadomienia o powodzeniu wybierz łącze **odwołaj się do listy punktów końcowych** , aby zobaczyć punkty końcowe.

1. Wybierz punkt końcowy. Zostanie otwarta nowa karta przeglądarki dla tego punktu końcowego. Pozostaw otwartą kartę przeglądarki i przejdź do sekcji **test** .

### <a name="test"></a>Testowanie

Przetestuj nowy cel w punkcie końcowym przez dodanie wartości parametru **q** : `Schedule a meeting with John Smith in Seattle next week`.

LUIS zwraca prawidłowy cel i temat spotkania:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Dodawanie wstępnie skompilowanego zamiaru

1. Na stronie **Moje aplikacje** wybierz aplikację. Spowoduje to otwarcie aplikacji w sekcji **kompilacja** aplikacji. 

1. Na stronie **intencje** wybierz pozycję **Dodaj prebudowane zamierzenie** z paska narzędzi powyżej listy intencje. 

1. Wybierz opcję **Narzędzia. Cancel** w wyskakującym oknie dialogowym. 

    ![Dodaj wstępnie utworzony cel](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Wybierz przycisk **gotowe** .

### <a name="train-and-test"></a>Szkolenie i testowanie

1. Po dodaniu zamiaru nauczenie aplikacji przez wybranie opcji **uczenie** w górnym prawym pasku narzędzi. 

1. Przetestuj nowy cel, wybierając pozycję **Testuj** na pasku narzędzi po prawej stronie. 

1. W polu tekstowym wprowadź wyrażenia długości do anulowania:

    |Test wypowiedź|Współczynnik przewidywania|
    |--|:--|
    |Chcę anulować mój lot.|0,67|
    |Anuluj zakup.|0,52|
    |Anuluj spotkanie.|0,56|

    ![Testowanie prekompilowanego zamiaru](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Dodaj wstępnie utworzoną jednostkę

1. Otwórz aplikację, klikając jej nazwę na stronie **Moje aplikacje** , a następnie klikając pozycję **jednostki** po lewej stronie. 

1. Na stronie **jednostki** kliknij pozycję **Dodaj prekompilowaną jednostkę**.

1. W oknie dialogowym **Dodawanie wstępnie utworzonych jednostek** wybierz wbudowaną jednostkę datetimeV2. 

    ![Okno dialogowe Dodawanie wstępnie skompilowanej jednostki](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Wybierz pozycję **Done** (Gotowe). Po dodaniu jednostki nie ma potrzeby uczenia aplikacji. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikuj w celu wyświetlenia prekompilowanego modelu z poziomu punktu końcowego przewidywania

Najprostszym sposobem wyświetlenia wartości prekompilowanego modelu jest zapytanie z opublikowanego punktu końcowego. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Oznaczanie jednostek zawierających wstępnie utworzony token jednostki
 Jeśli masz tekst, taki jak `HH-1234`, który chcesz oznaczyć jako jednostkę niestandardową _i_ masz [wstępnie skompilowany numer](luis-reference-prebuilt-number.md) dodany do modelu, nie będzie można oznaczyć jednostki niestandardowej w portalu Luis. Można oznaczyć go za pomocą interfejsu API. 

 Aby oznaczyć ten typ tokenu, gdzie jego część jest już oznaczona przy użyciu wstępnie skompilowanej jednostki, Usuń prekompilowaną jednostkę z aplikacji LUIS. Nie musisz uczenia aplikacji. Następnie Oznacz token własnym obiektem niestandardowym. Następnie Dodaj wstępnie utworzoną jednostkę z powrotem do aplikacji LUIS.

 W innym przykładzie Rozważmy wypowiedź jako listę preferencji klasy: `I want first year spanish, second year calculus, and fourth year english lit.`, jeśli aplikacja LUIS ma dodaną liczbę porządkową prekompilacji, `first`, `second`i `fourth` będą już oznaczone jako liczby porządkowe. Jeśli chcesz przechwycić numer porządkowy i klasę, możesz utworzyć jednostkę złożoną i otoczyć ją wokół wstępnie skompilowanej liczby porządkowej i jednostki niestandardowej dla nazwy klasy.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Kompilowanie modelu z pliku CSV przy użyciu interfejsów API REST](./luis-tutorial-node-import-utterances-csv.md)
