---
title: Popraw błędne słowa — LUIS
titleSuffix: Azure Cognitive Services
description: Popraw błędne słowa w wyrażenia długości przez dodanie sprawdzanie pisowni Bing API wersji 7 do zapytań LUIS Endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9c8babac8450bdfd170d3d18b338ba3d64383a67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499013"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Popraw błędne słowa przy użyciu sprawdzanie pisowni Bing

Aplikację LUIS można zintegrować sprawdzanie pisowni Bing z [interfejsem API wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) w celu poprawienia błędnie napisanych wyrazów w wyrażenia długości przed Luis przewidywania wyników i jednostek wypowiedź. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Utwórz pierwszy klucz dla sprawdzanie pisowni Bing wersji 7
[Pierwszy sprawdzanie pisowni Bing klucz wersji 7 interfejsu API](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) jest bezpłatny. 

![Utwórz klucz bezpłatny](./media/luis-tutorial-bing-spellcheck/free-key.png)

< nazwę "Create-Subscription-Key" ></a>
## <a name="create-endpoint-key"></a>Utwórz klucz punktu końcowego
Jeśli Twój bezpłatny klucz wygasł, Utwórz klucz punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wpisz `Bing Spell Check API V7`.

    ![Wyszukaj sprawdzanie pisowni Bing API wersji 7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wybierz usługę. 

5. Panel Informacje pojawia się z prawej strony zawierającej informacje, takie jak Uwaga prawna. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia subskrypcji. 

6. W następnym panelu wprowadź ustawienia usługi. Poczekaj na zakończenie procesu tworzenia usługi.

    ![Wprowadź ustawienia usługi](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wybierz pozycję **wszystkie zasoby** pod tytułem **Ulubione** po lewej stronie.

8. Wybierz nową usługę. Jego typem jest **Cognitive Services** , a lokalizacja jest **globalna**. 

9. W panelu głównym wybierz pozycję **klucze** , aby wyświetlić nowe klucze.

    ![Przechwyć klucze](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Skopiuj pierwszy klucz. Potrzebny jest tylko jeden z tych dwóch kluczy. 

## <a name="using-the-key-in-luis-test-panel"></a>Korzystanie z klucza w panelu testowania LUIS
Aby użyć klucza, istnieją dwa miejsca w LUIS. Pierwszy znajduje się w [panelu test](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klucz nie jest zapisywany w LUIS, ale zamiast tego jest zmienną sesji. Należy ustawić klucz za każdym razem, gdy Panel testowy ma sprawdzanie pisowni Bing zastosować usługę wersji 7 API do wypowiedź. Aby ustawić klucz, zobacz [instrukcje](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) w panelu test.

## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Zapytanie punktu końcowego wymaga klucza przesłanego w parametrach ciągu zapytania dla każdego zapytania, dla którego ma zostać zastosowana korekta pisowni. Może być chatbot, który wywołuje LUIS, lub może bezpośrednio wywołać interfejs API punktu końcowego LUIS. Niezależnie od tego, jak jest wywoływany punkt końcowy, każdy i każde wywołanie musi zawierać informacje wymagane do poprawnego działania poprawek pisowni.

Adres URL punktu końcowego ma kilka wartości, które muszą zostać pomyślnie przesłane. Klucz wersji 7 interfejsu API sprawdzanie pisowni Bing jest innym jednym z nich. Należy ustawić parametr **sprawdzania pisowni** na wartość true, a dla wartości klucza należy ustawić wartość opcji **Bing-test-Check-Subscription-Key** :

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Wyślij błędnie wpisane wypowiedź do LUIS
1. W przeglądarce internetowej Skopiuj poprzedni ciąg i Zastąp `region`, `appId`, `luisKey`i `bingKey` własnymi wartościami. Upewnij się, że używasz regionu punktu końcowego, jeśli jest inny niż [region](luis-reference-regions.md)publikacji.

2. Dodaj błędny wypowiedź, na przykład "jak daleko jest mountainn?". W języku angielskim `mountain`, z jedną `n`, jest poprawną pisownią. 

3. Wybierz klawisz ENTER, aby wysłać zapytanie do LUIS.

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

* Etykieta przykład wyrażenia długości, która ma wszystkie inne pisownie, dzięki czemu LUIS może poznać poprawną pisownię oraz literówki. Ta opcja wymaga więcej nakładów na etykietowanie niż przy użyciu modułu sprawdzania pisowni.
* Utwórz listę fraz ze wszystkimi odmianami wyrazu. W tym rozwiązaniu nie trzeba etykietować odmian wyrazów w przykładzie wyrażenia długości. 

## <a name="publishing-page"></a>Strona publikowania
Na stronie [Publikowanie](luis-how-to-publish-app.md) jest zaznaczone pole wyboru **Włącz sprawdzanie pisowni Bing** . Jest to wygoda do utworzenia klucza i zrozumienia, jak zmienia się adres URL punktu końcowego. Nadal musisz użyć prawidłowych parametrów punktu końcowego, aby poprawić pisownię dla każdego wypowiedźu. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat przykładu wyrażenia długości](luis-how-to-add-example-utterances.md)
