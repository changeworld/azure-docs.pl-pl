---
title: Dodaj API sprawdzania pisowni usługi Bing w wersji 7 do zapytania LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Poprawne pisowni w zniesławiających przez dodanie 7 interfejsu API sprawdzania pisowni usługi Bing LUIS kwerend punktu końcowego.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: v-geberr
ms.openlocfilehash: 96b23146e726b7fee86b7e449c81d7efc0073e8d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127673"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Poprawne pisowni z sprawdzania pisowni usługi Bing

Możesz też zintegrować LUIS aplikacji za pomocą [7 interfejsu API sprawdzania pisowni usługi Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) można poprawić pisowni w zniesławiających przed LUIS prognozuje wynik i jednostek utterance. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Utwórz pierwszy klucz dla 7 sprawdzania pisowni usługi Bing
Twoje [pierwszy klucz w wersji 7 API sprawdzania pisowni usługi Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) jest bezpłatna. 

![Utwórz klucz bezpłatna](./media/luis-tutorial-bing-spellcheck/free-key.png)

< nazwa "Tworzenie subskrypcji klucza" ></a>
## <a name="create-endpoint-key"></a>Utwórz klucz punktu końcowego
Jeśli klucz wolnego ważność, Utwórz klucz punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Wybierz **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wpisz `Bing Spell Check API V7`.

    ![Wyszukaj pisowni usługi Bing Sprawdź interfejsu API w wersji 7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wybierz usługę. 

5. Panelu informacji pojawia się po prawej stronie, zawierającego informacje o tym prawne powiadomienia. Wybierz **Utwórz** do rozpoczęcia procesu tworzenia subskrypcji. 

6. W panelu dalej wprowadź ustawienia usługi. Poczekaj na zakończenie procesu tworzenia usługi.

    ![Wprowadź ustawienia usługi](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wybierz **wszystkie zasoby** w obszarze **ulubione** tytuł w obszarze nawigacji po lewej stronie.

8. Wybierz nową usługę. Jest on typu **kognitywnych usług** znajduje się **globalne**. 

9. Wybierz główny panel **klucze** aby zobaczyć nowe klucze.

    ![Wystarczy pobrać klucze](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Skopiuj klucz pierwszego. Wystarczy tylko jeden z dwóch kluczy. 

## <a name="using-the-key-in-luis-test-panel"></a>W panelu testu LUIS przy użyciu klucza
Istnieją dwa miejsca w LUIS, aby użyć klucza. Pierwsza to w [panelu testu](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klucz nie jest zapisany w LUIS, ale zamiast tego jest zmiennej sesji. Należy określić klucz, za każdym razem, gdy chce panelu testu dotyczyć utterance usługi w wersji 7 API sprawdzania pisowni usługi Bing. Zobacz [instrukcje](interactive-test.md#view-bing-spell-check-corrections-in-test-panel) w panelu testu do ustawiania klucza.

## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Zapytanie punktu końcowego musi mieć klucz przekazano parametrów ciągu zapytania dla każdego zapytania, którego chcesz zastosować poprawkę pisowni. Użytkownik może mieć chatbot, która wywołuje LUIS lub LUIS punkt końcowy interfejsu API może wywołać bezpośrednio. Niezależnie od tego, jak nazywa się punkt końcowy wszystkie wywołania musi zawierać wymagane informacje dotyczące pisowni działała poprawnie.

Punkt końcowy adres URL ma kilka wartości, które muszą zostać przekazane poprawnie. Klucz w wersji 7 API sprawdzania pisowni usługi Bing jest po prostu inną jeden z nich. Należy ustawić **sprawdzanie pisowni** parametr true, należy ustawić wartość **bing pisowni wyboru subskrypcji klucza** wartość klucza:

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appID}?Subscription-Key={luisKey}&spellCheck=**true**& bing pisowni wyboru subskrypcji — klucz =**{bingKey}**& pełne = true & wartości timezoneOffset = 0 & q = {utterance}

## <a name="send-misspelled-utterance-to-luis"></a>Wyślij błędnie utterance do LUIS
1. W przeglądarce sieci web, skopiuj poprzedniego ciąg i Zastąp `region`, `appId`, `luisKey`, i `bingKey` z własne wartości. Upewnij się, że używają punktu końcowego, jeśli różni się od publikowania [region](luis-reference-regions.md).

2. Dodaj błędnie utterance takich jak "jak daleko jest mountainn?". W języku angielskim `mountain`, z jednym `n`, jest prawidłowo. 

3. Wybierz enter, aby wysłać zapytanie do LUIS.

4. LUIS odpowie wyniku JSON `How far is the mountain?`. Jeśli API sprawdzania pisowni usługi Bing w wersji 7 wykryje błąd, `query` oryginalne zapytanie zawiera pola aplikacji LUIS JSON odpowiedzi i `alteredQuery` pole zawiera poprawiony zapytanie wysyłane do LUIS.

```
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

## <a name="ignore-spelling-mistakes"></a>Ignorowanie błędów pisowni
Jeśli nie chcesz korzystać z usługi w wersji 7 API sprawdzania pisowni usługi Bing, można opisać zniesławiających mających błędów pisowni, dzięki czemu LUIS może nauczyć się odpowiednie pisowni, a także literówki. Ta opcja wymaga więcej wysiłku etykietowania niż przy użyciu sprawdzania pisowni.

## <a name="publishing-page"></a>Strona publikowania
[Publikowania](publishapp.md) strona ma **Bing Włącz moduł sprawdzania pisowni** wyboru. Jest to wygody, aby utworzyć klucz i zrozumieć, jak zmiana adresu URL punktu końcowego. Aby przypisać pisowni rozwiązany dla każdego utterance parametry właściwego punktu końcowego nadal mieć. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zniesławiających przykład](luis-how-to-add-example-utterances.md)
