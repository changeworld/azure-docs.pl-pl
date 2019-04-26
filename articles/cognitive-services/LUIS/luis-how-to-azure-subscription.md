---
title: Klucze subskrypcji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Jest konieczne tworzenie kluczy subskrypcji do użycia zapytania bezpłatne pierwszego 1000 punkt końcowy. Jeśli zostanie wyświetlony _poza limit przydziału_ błąd w formie HTTP 403 i 429, należy utworzyć klucz i przypisać ją do swojej aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/01/2019
ms.author: diberry
ms.openlocfilehash: 7315c80ad74eae07e41577fb2ac13742002e729e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198656"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Używanie kluczy subskrypcji z aplikacją usługi LUIS

Jest konieczne tworzenie kluczy subskrypcji do użycia zapytania bezpłatne pierwszego 1000 punkt końcowy. Gdy używane są te zapytania punktu końcowego, tworzenie zasobu platformy Azure w [witryny Azure portal](https://portal.azure.com), następnie przypisz tego zasobu z aplikacją usługi LUIS w [portal usługi LUIS](https://www.luis.ai).

Jeśli zostanie wyświetlony _poza limit przydziału_ błąd w formie HTTP 403 i 429, należy utworzyć klucz i przypisać ją do swojej aplikacji. 

Testowanie i prototypu tylko przy użyciu warstwy bezpłatna (F0). W przypadku systemów produkcyjnych, użyj [płatnych](https://aka.ms/luis-price-tier) warstwy. Nie używaj [tworzenia klucza](luis-concept-keys.md#authoring-key) dla punktu końcowego kwerend w środowisku produkcyjnym.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Tworzenie zasobu środowiska uruchomieniowego endpoint prognoz w witrynie Azure portal

Dowiedz się więcej o [tworzenia aplikacji](get-started-portal-build-app.md) Szybki Start.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Przypisz klucz zasobu do aplikacji usługi LUIS w portalu usługi LUIS

Dowiedz się więcej o [wdrożenia](get-started-portal-deploy-app.md) Szybki Start.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Cofanie przypisania zasobów
Po odłączeniu klucza punktu końcowego, nie zostanie usunięta z platformy Azure. Jest on tylko została odłączona od usługi LUIS. 

Gdy klucza punktu końcowego nieprzypisane lub nie jest przypisany do aplikacji, żądanie do punktu końcowego adresu URL zwraca błąd: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Obejmują wszystkie przewidywanych wskaźników intencji
**Uwzględnij wszystkie przewidywane wyniki intencji** pola wyboru pozwala odpowiedzi na zapytanie punkt końcowy do uwzględnienia wyników prognoz dla każdego intencji. 

To ustawienie pozwala używać chatbot lub wywoływania usługi LUIS aplikacji, aby wprowadzić programowe decyzję oparciu o wyniki zwrócone intencji. Ogólnie rzecz biorąc najważniejsze intencji dwa są najbardziej interesujące. W przypadku oceny najważniejszych intencji Twojego chatbot możliwość Zadaj pytanie monitowania, dzięki której ostatecznego wyboru między Brak intencji i oceniania wysokiej intencji None. 

Intencji i ich wyniki są również uwzględnione w dziennikach punktu końcowego. Możesz [wyeksportować](luis-how-to-start-new-app.md#export-app) tych dzienników i przeanalizować wyniki. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Włączanie sprawdzania pisowni Bing 
W **ustawienia adresu url punktu końcowego**, **sprawdzania pisowni Bing** przełącznik umożliwia usługi LUIS do poprawianie błędnie napisanych wyrazów przed prognozy. Tworzenie  **[klucz sprawdzania pisowni Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Dodaj **sprawdzania pisowni = true** parametr querystring i **bing pisowni wyboru subscription-key = {YOUR_BING_KEY_HERE}**. Zastąp `{YOUR_BING_KEY_HERE}` kluczem moduł sprawdzania pisowni Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Publikowanie regionów

Dowiedz się więcej o publikowaniu [regionów](luis-reference-regions.md) publikowania w tym [Europa](luis-reference-regions.md#publishing-to-europe), i [Australii](luis-reference-regions.md#publishing-to-australia). Regiony publikowania różnią się od tworzenia regionów. Tworzenie aplikacji w regionie tworzenia odpowiadające regionowi publikowania, potrzebne dla punktu końcowego zapytania.

## <a name="assign-resource-without-luis-portal"></a>Przydziel zasób bez portal usługi LUIS

Do celów usługi automation, takich jak potok ciągłej integracji/ciągłego wdrażania można automatycznie przypisywać zasobem usługi LUIS z aplikacją usługi LUIS. Aby to zrobić, należy wykonać następujące czynności:

1. Pobierz token do usługi Azure Resource Manager z tego [witryny sieci Web](https://resources.azure.com/api/token?plaintext=true). Ten token wygaśnie więc natychmiast zastosować go. Żądanie zwraca tokenu usługi Azure Resource Manager.

    ![Żądanie tokenu usługi Azure Resource Manager i otrzymywanie tokenu usługi Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Użyj tokenu na potrzeby żądania zasobów usługi LUIS w subskrypcjach, z [uzyskać LUIS azure konta interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), Twoje konto użytkownika ma dostęp do. 

    Ten interfejs API WPIS wymaga następujących ustawień:

    |Nagłówek|Wartość|
    |--|--|
    |`Authorization`|Wartość `Authorization` jest `Bearer {token}`. Należy zauważyć, że wartość tokenu musi być poprzedzona wyraz `Bearer` i spacja.| 
    |`Ocp-Apim-Subscription-Key`|Twoje [tworzenia klucza](luis-how-to-account-settings.md).|

    Ten interfejs API zwraca tablicę obiektów JSON subskrypcji usługi LUIS, m.in. identyfikator subskrypcji, grupy zasobów i nazwę zasobu, zwracane jako nazwy konta. Znajdź jeden element w tablicy, która jest zasobem usługi LUIS do przypisania do aplikacji usługi LUIS. 

1. Przypisz tokenu do zasobu usługi LUIS z [przypisać kont platformy azure do aplikacji usługi LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) interfejsu API. 

    Ten interfejs API WPIS wymaga następujących ustawień:

    |Typ|Ustawienie|Wartość|
    |--|--|--|
    |Nagłówek|`Authorization`|Wartość `Authorization` jest `Bearer {token}`. Należy zauważyć, że wartość tokenu musi być poprzedzona wyraz `Bearer` i spacja.|
    |Nagłówek|`Ocp-Apim-Subscription-Key`|Twoje [tworzenia klucza](luis-how-to-account-settings.md).|
    |Nagłówek|`Content-type`|`application/json`|
    |Ciąg zapytania|`appid`|Identyfikator aplikacji usługi LUIS. 
    |Treść||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e"<br>"ResourceGroup": "Grupa zasobów-2"<br>"Nazwa konta": "Usługa luis-uswest-S0-2"}|

    Po pomyślnym ten interfejs API zwraca 201 - stan utworzone. 

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

1.  W [Azure](https://portal.azure.com), Znajdź swoją subskrypcję usługi LUIS. Wybierz subskrypcję usługi LUIS.
    ![Znajdź swoją subskrypcję usługi LUIS](./media/luis-usage-tiers/find.png)
1.  Wybierz **warstwa cenowa** aby można było wyświetlić dostępnych warstw cenowych. 
    ![Wyświetl warstwy cenowe](./media/luis-usage-tiers/subscription.png)
1.  Wybierz warstwę cenową i wybierz pozycję **wybierz** można zapisać zmiany. 
    ![Zmień warstwę płatności usługi LUIS](./media/luis-usage-tiers/plans.png)
1.  Po zakończeniu zmianą cen okno podręczne sprawdza nowej warstwy cenowej. 
    ![Sprawdź warstwę płatności usługi LUIS](./media/luis-usage-tiers/updated.png)
1. Pamiętaj, aby [przypisania tego klucza punktu końcowego](#assign-endpoint-key) na **Publikuj** strony i używać jej w wszystkie zapytania punktu końcowego. 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>Jak naprawić błędy "limit przydziału", gdy klucz przekracza użycie warstwy cenowej
Każda warstwa umożliwia żądania punktu końcowego do swojego konta usługi LUIS z określoną szybkością. Jeśli liczba żądań jest większy niż dozwolone stopień konta naliczane za minutę lub miesięcznie, żądania otrzymują błąd HTTP "429: Zbyt wiele żądań."

Każda warstwa umożliwia kumulacyjne żądań miesięcznie. Jeśli łączna liczba żądań są wyższe niż dozwolone szybkość, żądania otrzymują błąd HTTP "403: dostęp zabroniony".  

## <a name="viewing-summary-usage"></a>Wyświetlanie podsumowania użycia
Możesz wyświetlić informacje dotyczące użycia usługi LUIS na platformie Azure. **Przegląd** strona zawiera najnowsze informacje podsumowania, w tym połączeń i błędów. Jeśli usługa LUIS żądania punktu końcowego, następnie natychmiast obejrzeć **strony Przegląd**, Zezwalaj na użycie pojawienie się do pięciu minut.

![Wyświetlanie podsumowania użycia](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Dostosowywanie wykresy użycia
Metryki zapewnia bardziej szczegółowy wgląd w dane.

![Domyślne metryki](./media/luis-usage-tiers/metrics-default.png)

Wykresy metryk można skonfigurować w przedziale czasu i typ metryki. 

![Metryki niestandardowe](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Łączna liczba transakcji próg alertu
Jeśli chcesz dowiedzieć się, gdy został osiągnięty limit określonego progu transakcji, na przykład 10 000 transakcji, można utworzyć alertu. 

![Domyślne alerty](./media/luis-usage-tiers/alert-default.png)

Dodaj alert dotyczący metryki dla **łączna liczba wywołań** metryk przez pewien czas. Dodaj adresy e-mail wszystkich osób, które powinien zostać wyświetlony alert. Dodaj elementy webhook dla wszystkich systemów, które powinny otrzymać alert. Można również uruchomić aplikację logiki, gdy zostanie wyzwolony alert. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać [wersji](luis-how-to-manage-versions.md) Zarządzanie zmianami w aplikacją usługi LUIS.
