---
title: Zrozumienie klucze usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: Użyj klawiszy Language Understanding (LUIS) do tworzenia aplikacji i wysyłania zapytań Twojej endpoing.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: fe6bd0803098854c7ced1a7d816ebbc8cce23b09
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340617"
---
# <a name="keys-in-luis"></a>Klucze usługi LUIS
Usługa LUIS wykorzystuje dwa klucze: [tworzenia](#programmatic-key) i [punktu końcowego](#endpoint-key). Podczas tworzenia konta usługi LUIS tworzenia klucza jest tworzony automatycznie. Gdy wszystko jest gotowe do opublikowania z aplikacją usługi LUIS, musisz [tworzenia klucza punktu końcowego](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [ją przypisać](luis-how-to-manage-keys.md#assign-endpoint-key) z aplikacją usługi LUIS i [pomocą kwerendy punktu końcowego](#use-endpoint-key-in-query). 

|Klucz|Przeznaczenie|
|--|--|
|[Tworzenie klucza](#programmatic-key)|Tworzenie, publikowanie, zarządzanie współpracownikom, przechowywanie wersji|
|[Klucz punktu końcowego](#endpoint-key)| Wykonywanie zapytań|

Ważne jest, aby tworzyć aplikacje usługi LUIS w [regionów](luis-reference-regions.md#publishing-regions) które również chcesz publikować i wykonywać zapytania.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Tworzenie klucza

Tworzenia klucza, znany także jako klucz starter, jest tworzona automatycznie podczas tworzenia konta usługi LUIS i jest ona bezpłatna. Masz jeden klucz tworzenia pakietów administracyjnych dla wszystkich aplikacji usługi LUIS do każdego tworzenia [region](luis-reference-regions.md). Tworzenia klucz jest dostarczany do tworzenia aplikacji usługi LUIS lub do testowania zapytania punktu końcowego. 

Aby znaleźć klucz tworzenia pakietów administracyjnych, zaloguj się do [LUIS] [ LUIS] i kliknij nazwę konta, na pasku nawigacyjnym prawym górnym rogu, aby otworzyć **ustawienia konta**.

![Tworzenie klucza](./media/luis-concept-keys/programatic-key.png)

Jeśli chcesz, aby **kwerendy punktu końcowego produkcji**, tworzenie platformy Azure [subskrypcji usługi LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Dla wygody wiele przykładów użyć tworzenia klucza, ponieważ zapewnia kilka wywołań punktu końcowego w jego [przydziału](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Klucz punktu końcowego
 Gdy będziesz potrzebować **kwerendy punktu końcowego produkcji**, Utwórz [klucz usługi LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) w witrynie Azure portal. Zapamiętaj nazwę używaną do tworzenia klucza, będzie on potrzebny podczas dodawania klucza do aplikacji...

Po zakończeniu procesu subskrypcji usługi LUIS [Dodaj klucz](luis-how-to-manage-keys.md#assign-endpoint-key) z aplikacją na **Publikuj** strony. 

Klucz punktu końcowego umożliwia limit przydziału liczby trafień punktu końcowego zgodnie z planem użycia, które zostały określone podczas tworzenia klucza. Zobacz [ceny usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) Aby uzyskać informacje o cenach.

Dla wszystkich aplikacji usługi LUIS lub dla określonych aplikacji LUIS można klucza punktu końcowego. 

Nie należy używać klucza punktu końcowego na potrzeby tworzenia aplikacji usługi LUIS. 

## <a name="use-endpoint-key-in-query"></a>Użyj klucza punktu końcowego w zapytaniu
Punkt końcowy usługi LUIS akceptuje dwa style zapytania, ale klucza punktu końcowego jednocześnie używać w różnych miejscach:

|zlecenia|Przykład lokalizacji adresu url i klucz|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn światła<br><br>wartość ciągu zapytania `subscription-key`<br><br>Zmień wartość kwerendy punktu końcowego dla `subscription-key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisać klawisz ale nie zmieniaj wartości kwerendy punktu końcowego dla klucza subskrypcji ", nie używasz przydziału klucza punktu końcowego.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> wartość nagłówka `Ocp-Apim-Subscription-Key`<br><br>Zmień wartość kwerendy punktu końcowego dla `Ocp-Apim-Subscription-Key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisz klucza, ale nie zmieniaj wartości kwerendy punktu końcowego dla `Ocp-Apim-Subscription-Key`, nie używasz przydziału klucza punktu końcowego.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Użycie interfejsu API Ocp-Apim-Subscription-Key
Interfejsy API usługi LUIS Użyj wartości header `Ocp-Apim-Subscription-Key`. Nazwa nagłówka nie zmienia się zależnie od którego klucz i zestaw interfejsów API używają. Ustaw nagłówek do tworzenia klucza do tworzenia interfejsów API. Jeśli używasz punktu końcowego Ustaw nagłówek do klucza punktu końcowego. 

Nie można przekazać klucza punktu końcowego do tworzenia interfejsów API. Jeśli to zrobisz, uzyskasz błąd 401 — Odmowa z powodu nieprawidłowy punkt końcowy klucz dostępu. 

## <a name="key-limits"></a>Ograniczenia klucza
Zobacz [klucza limity](luis-boundaries.md#key-limits) i [regionów świadczenia usługi Azure](luis-reference-regions.md). Tworzenia klucza jest wolną i używaną do tworzenia. Klucza punktu końcowego usługi LUIS oferuje bezpłatną warstwę, ale muszą być tworzone przez użytkownika i skojarzone z aplikacją usługi LUIS w **Publikuj** strony. Nie można używać do tworzenia, ale tylko punkt końcowy zapytania.

Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzenie aplikacji w regionie tworzenia odpowiednich do regionu publikowania, który ma.

## <a name="key-limit-errors"></a>Błędy limitu klucza
W przypadku przekroczenia usługi na drugim limit przydziału, wystąpi błąd HTTP 429. W przypadku przekroczenia usługi na miesiąc limit przydziału, komunikat o błędzie HTTP 403. Usuń te błędy, uzyskując LUIS [punktu końcowego](#endpoint-key) klucza, [przypisywanie](luis-how-to-manage-keys.md#assign-endpoint-key) klucza do aplikacji w **publikowania** strony [LUIS] [ LUIS] witryny sieci Web.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [pojęcia](luis-how-to-manage-keys.md#assign-endpoint-key) o kluczach do tworzenia i punktu końcowego.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
