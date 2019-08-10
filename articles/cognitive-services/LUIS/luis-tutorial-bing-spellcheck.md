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
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: f8f22c70870b149f916eb767c5eb310e063d7413
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945087"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Poprawne błędnie napisanych wyrazów za pomocą sprawdzania pisowni Bing

Można zintegrować z aplikacją usługi LUIS z [Bing pisowni Sprawdź interfejsu API w wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) do poprawianie błędnie napisanych wyrazów w wypowiedzi, zanim usługa LUIS przewidywany wynik i jednostek wypowiedź. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Utwórz pierwszy klucz dla sprawdzanie pisowni Bing w wersji 7
Twoje [pierwszy interfejs API sprawdzania pisowni Bing w wersji 7 klucz](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) jest bezpłatna. 

![Utwórz klucz bezpłatne](./media/luis-tutorial-bing-spellcheck/free-key.png)

< nazwa "Utwórz subscription-key" ></a>
## <a name="create-endpoint-key"></a>Tworzenie klucza punktu końcowego
Jeśli klucz bezpłatne uznawane za wygasłe, należy utworzyć klucza punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Wybierz **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wpisz `Bing Spell Check API V7`.

    ![Wyszukaj pisowni Bing Sprawdź interfejsu API w wersji 7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wybierz usługę. 

5. Panel informacji pojawia się po prawej stronie, zawierający informacje o tym prawne. Wybierz **Utwórz** aby rozpocząć proces tworzenia subskrypcji. 

6. W panelu dalej wprowadź ustawienia usług. Poczekaj, aż do zakończenia procesu tworzenia usługi.

    ![Wprowadź ustawienia usługi](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wybierz **wszystkie zasoby** w obszarze **ulubione** tytuł w okienku nawigacji po lewej stronie.

8. Wybierz nową usługę. Jego typem jest **usług Cognitive Services** znajduje się **globalnego**. 

9. Na panelu głównego wybierz **klucze** aby zobaczyć nowe klucze.

    ![Pobierz klucze](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Skopiuj pierwszy klucz. Wystarczy tylko jeden z dwóch kluczy. 

## <a name="using-the-key-in-luis-test-panel"></a>Za pomocą klucza w panelu test usługi LUIS
Istnieją dwa miejsca w usługi LUIS w celu używania klucza. Trwa pierwsza [panelu testu](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klucz nie są zapisywane do usługi LUIS, ale zamiast tego jest to zmienna sesji. Należy ustawić klucz za każdym razem, gdy chcesz panelu testu dotyczą usługi interfejsu API sprawdzania pisowni Bing w wersji 7 wypowiedź. Zobacz [instrukcje](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) w panelu testu dla klucza.

## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Kwerendy punktu końcowego musi mieć klucz przekazany na parametry ciągu zapytania dla każdego zapytania, że chcesz zastosować korekty pisowni. Możesz mieć chatbot, który wywołuje LUIS lub punkt końcowy interfejsu API usługi LUIS może wywołać bezpośrednio. Niezależnie od tego, jak punkt końcowy jest wywoływana wywołanie każdy musi zawierać wymagane informacje dotyczące pisowni zapewnić prawidłowe działanie.

Punkt końcowy adres URL ma kilka wartości, które muszą zostać prawidłowo przekazane. Klucz interfejsu API sprawdzania pisowni Bing w wersji 7 jest po prostu kolejne jeden z nich. Należy ustawić **sprawdzania pisowni** parametr true, należy ustawić wartość **bing pisowni wyboru subscription-key** wartość klucza:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Wyślij błędnie wypowiedź do usługi LUIS
1. W przeglądarce sieci web, skopiuj poprzedniego ciąg i Zastąp `region`, `appId`, `luisKey`, i `bingKey` własnymi wartościami. Upewnij się, że Użyj regionu punktu końcowego, jeśli jest inny niż publikowania [region](luis-reference-regions.md).

2. Dodaj, błędnie napisane wypowiedź takich jak "jak daleko jest mountainn?". W języku angielskim `mountain`, za pomocą jednego `n`, jest prawidłowo. 

3. Wybierz enter, aby wysłać zapytanie do usługi LUIS.

4. Usługa LUIS odpowiada za pomocą wyniku JSON `How far is the mountain?`. Jeśli interfejs API sprawdzania pisowni Bing w wersji 7 wykryje błąd, `query` pole odpowiedź aplikacji LUIS w formacie JSON zawiera oryginalnego zapytania i `alteredQuery` pole zawiera poprawiony zapytanie wysyłane do usługi LUIS.

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
Jeśli nie chcesz korzystać z interfejsu API sprawdzania pisowni Bing w wersji 7 usługi, możesz oznaczyć wypowiedzi, które mają błędy pisowni, tak aby usługi LUIS można znaleźć prawidłowego pisowni, a także literówki. Ta opcja wymaga wysiłku etykietowania niż używanie sprawdzania pisowni.

## <a name="publishing-page"></a>Strona publikowania
[Publikowania](luis-how-to-publish-app.md) strona ma **sprawdzania pisowni Bing Włącz** pola wyboru. Jest to wygodne do tworzenia klucza i zrozumieć, jak zmienia się adres URL punktu końcowego. Nadal jest konieczne korzystanie z parametrów właściwego punktu końcowego, aby mogła mieć pisownię każdego wypowiedź została poprawiona automatycznie. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
