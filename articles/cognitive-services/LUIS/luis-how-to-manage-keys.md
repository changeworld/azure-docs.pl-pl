---
title: Zarządzanie kluczami do tworzenia i punktu końcowego w usługi LUIS
titleSuffix: Azure Cognitive Services
description: Po utworzeniu klucza punktu końcowego usługi LUIS w witrynie Azure portal, przypisać klawisz do aplikacji usługi LUIS i uzyskać adres URL właściwego punktu końcowego. Użyj tego adresu URL punktu końcowego, aby pobrać LUIS prognozy.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 62081f96e2081833eb705992914899a6764bd792
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033213"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Dodawanie zasobu usługi LUIS platformy Azure do aplikacji

Po utworzeniu zasobu usługi LUIS w witrynie Azure portal, przypisywanie zasobów do aplikacji usługi LUIS i uzyskać adres URL właściwego punktu końcowego. Użyj tego adresu URL punktu końcowego, aby pobrać LUIS prognozy.

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

## <a name="assign-resource"></a>Przydziel zasób

1. Utwórz klucz usługi LUIS w [witryny Azure portal](https://portal.azure.com). Aby uzyskać dalsze instrukcje, zobacz [tworzenia klucza punktu końcowego przy użyciu platformy Azure](luis-how-to-azure-subscription.md).
 
2. Wybierz **Zarządzaj** w prawym górnym menu, a następnie zaznacz **kluczy i punktów końcowych**.

    [ ![Strona klucze i punktów końcowych](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. Aby dodać usługi LUIS, wybrać **przydzielania zasobów +**.

    ![Przydziel zasób do aplikacji](./media/luis-manage-keys/assign-key.png)

4. Wybierz dzierżawę w oknie dialogowym skojarzone z adresem e-mail, którego zalogować się za pomocą witryny sieci Web usługi LUIS.  

5. Wybierz **Nazwa subskrypcji** skojarzone z zasobów platformy Azure, które chcesz dodać.

6. Wybierz **Nazwa zasobu usługi LUIS**. 

7. Wybierz **przypisany zasób**. 

8. Znajdź nowy wiersz w tabeli i skopiuj adres URL punktu końcowego. Jest poprawnie zbudowane się żądanie HTTP GET do punktu końcowego usługi LUIS do przewidywania. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>Cofanie przypisania zasobów
Po odłączeniu klucza punktu końcowego, nie zostanie usunięta z platformy Azure. Jest on tylko została odłączona od usługi LUIS. 

Gdy klucza punktu końcowego nieprzypisane lub nie jest przypisany do aplikacji, żądanie do punktu końcowego adresu URL zwraca błąd: `401 This application cannot be accessed with the current subscription`. 

## <a name="include-all-predicted-intent-scores"></a>Obejmują wszystkie przewidywanych wskaźników intencji
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

## <a name="enable-bing-spell-checker"></a>Włączanie sprawdzania pisowni Bing 
W **ustawienia adresu url punktu końcowego**, **sprawdzania pisowni Bing** przełącznik umożliwia usługi LUIS do poprawianie błędnie napisanych wyrazów przed prognozy. Tworzenie  **[klucz sprawdzania pisowni Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Dodaj **sprawdzania pisowni = true** parametr querystring i **bing pisowni wyboru subscription-key = {YOUR_BING_KEY_HERE}** . Zastąp `{YOUR_BING_KEY_HERE}` kluczem moduł sprawdzania pisowni Bing.

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


## <a name="publishing-regions"></a>Publikowanie regionów

Dowiedz się więcej o publikowaniu [regionów](luis-reference-regions.md) publikowania w tym [Europa](luis-reference-regions.md#publishing-to-europe), i [Australii](luis-reference-regions.md#publishing-to-australia). Regiony publikowania różnią się od tworzenia regionów. Tworzenie aplikacji w regionie tworzenia odpowiadające regionowi publikowania, potrzebne dla punktu końcowego zapytania.

## <a name="next-steps"></a>Kolejne kroki

Użycia tego klucza, aby opublikować aplikację w **Opublikuj aplikację** strony. Aby uzyskać instrukcje dotyczące publikowania, zobacz [Opublikuj aplikację](luis-how-to-publish-app.md).

Zobacz [kluczy w LUIS](luis-concept-keys.md) zrozumienie usługi LUIS do tworzenia i punktu końcowego kluczowych pojęć.