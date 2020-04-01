---
title: Poprawianie błędnie napisanych słów — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Popraw błędnie napisane wyrazy w wypowiedziach, dodając interfejs API sprawdzania pisowni Bing V7 do zapytań punktów końcowych usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a1e43cfc55611c432652055db2ac8411d835608b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396816"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Poprawianie błędnie napisanych wyrazów za pomocą sprawdzania pisowni bing

Można zintegrować aplikację usługi LUIS z [interfejsem API sprawdzania pisowni Bing V7,](https://azure.microsoft.com/services/cognitive-services/spell-check/) aby poprawić błędnie napisane wyrazy w wypowiedziach, zanim usługa LUIS przewiduje wynik i jednostki wypowiedź. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Tworzenie pierwszego klucza sprawdzania pisowni Bing V7

Twój [pierwszy klucz api sprawdzania pisowni Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) jest wolny. 

![Tworzenie klucza wolnego](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Tworzenie klucza punktu końcowego
Jeśli klucz wolny wygasł, utwórz klucz punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wpisz `Bing Spell Check API V7`.

    ![Wyszukaj interfejs API sprawdzania pisowni bing V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wybierz usługę. 

5. Po prawej stronie pojawia się panel informacyjny zawierający informacje, w tym Informacje prawne. Wybierz **pozycję Utwórz,** aby rozpocząć proces tworzenia subskrypcji. 

6. W następnym panelu wprowadź ustawienia usługi. Poczekaj na zakończenie procesu tworzenia usługi.

    ![Wprowadzanie ustawień serwisu](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wybierz **pozycję Wszystkie zasoby** pod tytułem **Ulubione** po lewej stronie nawigacji.

8. Wybierz nową usługę. Jego typem jest **Cognitive Services,** a lokalizacja jest **globalna.** 

9. Na panelu głównym wybierz **klawisze,** aby wyświetlić nowe klawisze.

    ![Klucze chwytania](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Skopiuj pierwszy klucz. Potrzebujesz tylko jednego z dwóch kluczy. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Kwerenda punktu końcowego wymaga klucza przekazanego w parametrach ciągu zapytania dla każdej kwerendy, którą chcesz zastosować korektę pisowni. Może mieć chatbot, który wywołuje usługę LUIS lub można wywołać interfejs API punktu końcowego usługi LUIS bezpośrednio. Niezależnie od tego, jak punkt końcowy jest wywoływany, każde wywołanie musi zawierać wymagane informacje dotyczące poprawek pisowni, aby działały poprawnie.

Adres URL punktu końcowego ma kilka wartości, które muszą być przekazywane poprawnie. Klucz interfejsu API sprawdzania pisowni Bing jest tylko kolejnym z nich. Należy ustawić parametr **sprawdzania pisowni** na true i należy ustawić wartość **bing-spell-check-subscription-key** na wartość klucza:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Wysyłanie błędnie napisanego wypowiedź do usługi LUIS
1. W przeglądarce internetowej skopiuj `region`poprzedni `appId` `luisKey`ciąg `bingKey` i zastąp , , i własnymi wartościami. Upewnij się, że używasz regionu punktu końcowego, jeśli różni się on od [regionu](luis-reference-regions.md)publikowania .

2. Dodaj błędnie napisany wypowiedź, taką jak "Jak daleko jest górale?". W języku `mountain`angielskim, `n`z jednym , jest poprawna pisownia. 

3. Wybierz enter, aby wysłać zapytanie do usługi LUIS.

4. Usługa LUIS odpowiada wynikiem JSON dla `How far is the mountain?`. Jeśli interfejs API sprawdzania pisowni Bing w wersji `query` 7 wykryje błąd pisowni, pole w `alteredQuery` odpowiedzi JSON aplikacji usługi LUIS zawiera oryginalną kwerendę, a pole zawiera poprawioną kwerendę wysłaną do usługi LUIS.

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

Jeśli nie chcesz korzystać z usługi API sprawdzania pisowni Bing w wersji 7, musisz dodać poprawną i niepoprawną pisownię. 

Dwa rozwiązania to:

* Etykiety wypowiedzi przykład, które mają wszystkie różne pisownie, dzięki czemu usługa LUIS może nauczyć się prawidłowej pisowni, jak również literówki. Ta opcja wymaga większego wysiłku etykietowania niż przy użyciu modułu sprawdzania pisowni.
* Utwórz listę fraz ze wszystkimi odmianami wyrazu. Za pomocą tego rozwiązania nie trzeba oznaczać odmiany wyrazu w wypowiedzi przykład. 

## <a name="publishing-page"></a>Strona publikowania
Strona [publikowania](luis-how-to-publish-app.md) ma pole wyboru **Włącz sprawdzanie pisowni Bing.** Jest to wygoda, aby utworzyć klucz i zrozumieć, jak zmienia się adres URL punktu końcowego. Nadal trzeba użyć poprawne parametry punktu końcowego, aby pisownia poprawiona dla każdej wypowiedź. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykładowych wypowiedziach](luis-how-to-add-example-utterances.md)
