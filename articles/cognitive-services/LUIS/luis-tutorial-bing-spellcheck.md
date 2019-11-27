---
title: Popraw błędne słowa — LUIS
titleSuffix: Azure Cognitive Services
description: Poprawne błędnie napisanych wyrazów w wypowiedzi przez dodanie 7 interfejsu API sprawdzanie pisowni Bing do kwerendy punktu końcowego usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225463"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Poprawne błędnie napisanych wyrazów za pomocą sprawdzania pisowni Bing

Aplikację LUIS można zintegrować sprawdzanie pisowni Bing z [interfejsem API wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) w celu poprawienia błędnie napisanych wyrazów w wyrażenia długości przed Luis przewidywania wyników i jednostek wypowiedź. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Utwórz pierwszy klucz dla sprawdzanie pisowni Bing w wersji 7

[Pierwszy sprawdzanie pisowni Bing klucz wersji 7 interfejsu API](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) jest bezpłatny. 

![Utwórz klucz bezpłatne](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Tworzenie klucza punktu końcowego
Jeśli klucz bezpłatne uznawane za wygasłe, należy utworzyć klucza punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wpisz `Bing Spell Check API V7`.

    ![Wyszukaj pisowni Bing Sprawdź interfejsu API w wersji 7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wybierz usługę. 

5. Panel informacji pojawia się po prawej stronie, zawierający informacje o tym prawne. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia subskrypcji. 

6. W panelu dalej wprowadź ustawienia usług. Poczekaj, aż do zakończenia procesu tworzenia usługi.

    ![Wprowadź ustawienia usługi](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wybierz pozycję **wszystkie zasoby** pod tytułem **Ulubione** po lewej stronie.

8. Wybierz nową usługę. Jego typem jest **Cognitive Services** , a lokalizacja jest **globalna**. 

9. W panelu głównym wybierz pozycję **klucze** , aby wyświetlić nowe klucze.

    ![Pobierz klucze](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Skopiuj pierwszy klucz. Wystarczy tylko jeden z dwóch kluczy. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Kwerendy punktu końcowego musi mieć klucz przekazany na parametry ciągu zapytania dla każdego zapytania, że chcesz zastosować korekty pisowni. Możesz mieć chatbot, który wywołuje LUIS lub punkt końcowy interfejsu API usługi LUIS może wywołać bezpośrednio. Niezależnie od tego, jak punkt końcowy jest wywoływana wywołanie każdy musi zawierać wymagane informacje dotyczące pisowni zapewnić prawidłowe działanie.

Punkt końcowy adres URL ma kilka wartości, które muszą zostać prawidłowo przekazane. Klucz interfejsu API sprawdzania pisowni Bing w wersji 7 jest po prostu kolejne jeden z nich. Należy ustawić parametr **sprawdzania pisowni** na wartość true, a dla wartości klucza należy ustawić wartość opcji **Bing-test-Check-Subscription-Key** :

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Wyślij błędnie wypowiedź do usługi LUIS
1. W przeglądarce internetowej Skopiuj poprzedni ciąg i Zastąp `region`, `appId`, `luisKey`i `bingKey` własnymi wartościami. Upewnij się, że używasz regionu punktu końcowego, jeśli jest inny niż [region](luis-reference-regions.md)publikacji.

2. Dodaj, błędnie napisane wypowiedź takich jak "jak daleko jest mountainn?". W języku angielskim `mountain`, z jedną `n`, jest poprawną pisownią. 

3. Wybierz enter, aby wysłać zapytanie do usługi LUIS.

4. LUIS reaguje na wynik JSON dla `How far is the mountain?`. Jeśli sprawdzanie pisowni Bing API wersji 7 wykrywa błąd pisowni, pole `query` w odpowiedzi JSON aplikacji LUIS zawiera oryginalne zapytanie, a pole `alteredQuery` zawiera skorygowane zapytanie wysyłane do LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignoruj błędy pisowni

Jeśli nie chcesz używać sprawdzanie pisowni Bing API wersji 7, musisz dodać poprawną i niepoprawną pisownię. 

Dwa rozwiązania:

* Etykieta przykład wyrażenia długości, która ma wszystkie inne pisownie, dzięki czemu LUIS może poznać poprawną pisownię oraz literówki. Ta opcja wymaga wysiłku etykietowania niż używanie sprawdzania pisowni.
* Utwórz listę fraz ze wszystkimi odmianami wyrazu. W tym rozwiązaniu nie trzeba etykietować odmian wyrazów w przykładzie wyrażenia długości. 

## <a name="publishing-page"></a>Strona publikowania
Na stronie [Publikowanie](luis-how-to-publish-app.md) jest zaznaczone pole wyboru **Włącz sprawdzanie pisowni Bing** . Jest to wygodne do tworzenia klucza i zrozumieć, jak zmienia się adres URL punktu końcowego. Nadal jest konieczne korzystanie z parametrów właściwego punktu końcowego, aby mogła mieć pisownię każdego wypowiedź została poprawiona automatycznie. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat przykładu wyrażenia długości](luis-how-to-add-example-utterances.md)
