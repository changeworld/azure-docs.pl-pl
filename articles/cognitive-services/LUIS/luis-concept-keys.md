---
title: Klucze subskrypcji — LUIS
titleSuffix: Azure Cognitive Services
description: LUIS używa dwa klucze, bezpłatny klucz tworzenia pakietów administracyjnych, aby utworzyć model i klucza punktu końcowego mierzonych podczas wykonywania zapytań do endpoint prognoz z wypowiedzi użytkowników.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: diberry
ms.openlocfilehash: 0d52445d82965973c2d1e0f4fca2ef463312ad5f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560733"
---
# <a name="authoring-and-query-prediction-endpoint-keys-in-luis"></a>Tworzenie i prognozowanie kluczy końca punktu końcowego w LUIS
Usługa LUIS wykorzystuje dwa klucze: [tworzenia](#programmatic-key) i [punktu końcowego](#endpoint-key). Podczas tworzenia konta usługi LUIS tworzenia klucza jest tworzony automatycznie. Gdy wszystko jest gotowe do opublikowania z aplikacją usługi LUIS, musisz [tworzenia klucza punktu końcowego](luis-how-to-azure-subscription.md), [ją przypisać](luis-how-to-azure-subscription.md) z aplikacją usługi LUIS i [pomocą kwerendy punktu końcowego](#use-endpoint-key-in-query). 

|Klucz|Przeznaczenie|
|--|--|
|[Tworzenie klucza](#programmatic-key)|Tworzenie, publikowanie, zarządzanie współpracownikom, przechowywanie wersji|
|[Klucz punktu końcowego](#endpoint-key)| Wykonywanie zapytania|

Ważne jest, aby tworzyć aplikacje usługi LUIS w [regionów](luis-reference-regions.md#publishing-regions) które również chcesz publikować i wykonywać zapytania.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Tworzenie klucza

Tworzenia klucza, znany także jako klucz starter, jest tworzona automatycznie podczas tworzenia konta usługi LUIS i jest ona bezpłatna. Masz jeden klucz tworzenia pakietów administracyjnych dla wszystkich aplikacji usługi LUIS do każdego tworzenia [region](luis-reference-regions.md). Tworzenia klucz jest dostarczany do tworzenia aplikacji usługi LUIS lub do testowania zapytania punktu końcowego. 

Aby znaleźć klucz tworzenia, zaloguj się do [Luis](luis-reference-regions.md#luis-website) i kliknij nazwę konta w prawym górnym pasku nawigacyjnym, aby otworzyć **Ustawienia konta**.

![Tworzenie klucza](./media/luis-concept-keys/programatic-key.png)

Jeśli chcesz, aby **kwerendy punktu końcowego produkcji**, tworzenie platformy Azure [subskrypcji usługi LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Dla wygody wiele przykładów użyć tworzenia klucza, ponieważ zapewnia kilka wywołań punktu końcowego w jego [przydziału](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Klucz punktu końcowego
Jeśli potrzebujesz **zapytań dotyczących produkcyjnych punktów końcowych**, utwórz zasób platformy Azure, a następnie przypisz go do aplikacji Luis. 

[!INCLUDE [Azure resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Po zakończeniu procesu tworzenia zasobu platformy Azure [Przypisz klucz](luis-how-to-azure-subscription.md) do aplikacji. 

* Klucz punktu końcowego umożliwia limit przydziału liczby trafień punktu końcowego zgodnie z planem użycia, które zostały określone podczas tworzenia klucza. Zobacz [ceny usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) Aby uzyskać informacje o cenach.

* Dla wszystkich aplikacji usługi LUIS lub dla określonych aplikacji LUIS można klucza punktu końcowego. 

* Nie należy używać klucza punktu końcowego na potrzeby tworzenia aplikacji usługi LUIS. 

## <a name="use-endpoint-key-in-query"></a>Użyj klucza punktu końcowego w zapytaniu
Punkt końcowy usługi LUIS akceptuje dwa style zapytania, ale klucza punktu końcowego jednocześnie używać w różnych miejscach:

|zlecenia|Przykład lokalizacji adresu url i klucz|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>wartość ciągu zapytania `subscription-key`<br><br>Zmień wartość kwerendy punktu końcowego dla `subscription-key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisz klucza, ale nie zmieniaj wartości kwerendy punktu końcowego dla `subscription-key`, nie używasz przydziału klucza punktu końcowego.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> wartość nagłówka `Ocp-Apim-Subscription-Key`<br><br>Zmień wartość kwerendy punktu końcowego dla `Ocp-Apim-Subscription-Key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisz klucza, ale nie zmieniaj wartości kwerendy punktu końcowego dla `Ocp-Apim-Subscription-Key`, nie używasz przydziału klucza punktu końcowego.|

Identyfikator aplikacji używane w poprzednich adresów URL, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, jest publiczny aplikacja IoT, używana do [pokaz interaktywny](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Użycie interfejsu API Ocp-Apim-Subscription-Key
Interfejsy API usługi LUIS Użyj wartości header `Ocp-Apim-Subscription-Key`. Nazwa nagłówka nie zmienia się zależnie od którego klucz i zestaw interfejsów API używają. Ustaw nagłówek do tworzenia klucza do tworzenia interfejsów API. Jeśli używasz punktu końcowego Ustaw nagłówek do klucza punktu końcowego. 

Nie można przekazać klucza punktu końcowego do tworzenia interfejsów API. Jeśli to zrobisz, uzyskasz błąd 401 — Odmowa z powodu nieprawidłowy punkt końcowy klucz dostępu. 

## <a name="key-limits"></a>Ograniczenia klucza
Zobacz [klucza limity](luis-boundaries.md#key-limits) i [regionów świadczenia usługi Azure](luis-reference-regions.md). Tworzenia klucza jest wolną i używaną do tworzenia. Klucza punktu końcowego usługi LUIS oferuje bezpłatną warstwę, ale muszą być tworzone przez użytkownika i skojarzone z aplikacją usługi LUIS w **Publikuj** strony. Nie można używać do tworzenia, ale tylko punkt końcowy zapytania.

Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzenie aplikacji w regionie tworzenia odpowiednich do regionu publikowania, który ma.

## <a name="key-limit-errors"></a>Błędy limitu klucza
W przypadku przekroczenia usługi na drugim limit przydziału, wystąpi błąd HTTP 429. W przypadku przekroczenia usługi na miesiąc limit przydziału, komunikat o błędzie HTTP 403. Usuń te błędy, uzyskując LUIS [punktu końcowego](#endpoint-key) klucza, [przypisywanie](luis-how-to-azure-subscription.md) klucza do aplikacji w **publikowania** strony [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web.

## <a name="assignment-of-the-endpoint-key"></a>Przypisanie klucza punktu końcowego

Klucz punktu końcowego można [przypisać](luis-how-to-azure-subscription.md) w [portalu Luis](https://www.luis.ai) lub za pośrednictwem odpowiednich interfejsów API. 


## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [pojęcia](luis-how-to-azure-subscription.md) o kluczach do tworzenia i punktu końcowego.
