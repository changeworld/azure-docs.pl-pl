---
title: Zrozumienie klucze usługi LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS używa dwa klucze, tworzenia i punktu końcowego. Podczas tworzenia konta usługi LUIS tworzenia klucza jest tworzony automatycznie. Gdy wszystko jest gotowe do opublikowania z aplikacją usługi LUIS, możesz potrzebne do utworzenia klucza punktu końcowego przypisać go do swojej aplikacji usługi LUIS, a pomocą kwerendy punktu końcowego.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: f7c1753e71025d3ce39b1b6e3fb7362f2df212f5
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637835"
---
# <a name="keys-in-luis"></a>Klucze usługi LUIS
Usługa LUIS wykorzystuje dwa klucze: [tworzenia](#programmatic-key) i [punktu końcowego](#endpoint-key). Podczas tworzenia konta usługi LUIS tworzenia klucza jest tworzony automatycznie. Gdy wszystko jest gotowe do opublikowania z aplikacją usługi LUIS, musisz [tworzenia klucza punktu końcowego](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [ją przypisać](luis-how-to-manage-keys.md#assign-endpoint-key) z aplikacją usługi LUIS i [pomocą kwerendy punktu końcowego](#use-endpoint-key-in-query). 

|Klawisz|Przeznaczenie|
|--|--|
|[Tworzenie klucza](#programmatic-key)|Tworzenie, publikowanie, zarządzanie współpracownikom, przechowywanie wersji|
|[Klucz punktu końcowego](#endpoint-key)| Wykonywanie zapytania|

Ważne jest, aby tworzyć aplikacje usługi LUIS w [regionów](luis-reference-regions.md#publishing-regions) które również chcesz publikować i wykonywać zapytania.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Tworzenie klucza

Tworzenia klucza, znany także jako klucz starter, jest tworzona automatycznie podczas tworzenia konta usługi LUIS i jest ona bezpłatna. Masz jeden klucz tworzenia pakietów administracyjnych dla wszystkich aplikacji usługi LUIS do każdego tworzenia [region](luis-reference-regions.md). Tworzenia klucz jest dostarczany do tworzenia aplikacji usługi LUIS lub do testowania zapytania punktu końcowego. 

Aby znaleźć klucz tworzenia pakietów administracyjnych, zaloguj się do [LUIS](luis-reference-regions.md#luis-website) i kliknij nazwę konta, na pasku nawigacyjnym prawym górnym rogu, aby otworzyć **ustawienia konta**.

![Tworzenie klucza](./media/luis-concept-keys/programatic-key.png)

Jeśli chcesz, aby **kwerendy punktu końcowego produkcji**, tworzenie platformy Azure [subskrypcji usługi LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Dla wygody wiele przykładów użyć tworzenia klucza, ponieważ zapewnia kilka wywołań punktu końcowego w jego [przydziału](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Klucz punktu końcowego
 Gdy będziesz potrzebować **kwerendy punktu końcowego produkcji**, Utwórz [klucz usługi LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) w witrynie Azure portal. Zapamiętaj nazwę używaną do tworzenia klucza, będzie on potrzebny podczas dodawania klucza do aplikacji.

Po zakończeniu procesu subskrypcji usługi LUIS [przypisany klawisz](luis-how-to-manage-keys.md#assign-endpoint-key) do aplikacji. 

Klucz punktu końcowego umożliwia limit przydziału liczby trafień punktu końcowego zgodnie z planem użycia, które zostały określone podczas tworzenia klucza. Zobacz [ceny usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) Aby uzyskać informacje o cenach.

Dla wszystkich aplikacji usługi LUIS lub dla określonych aplikacji LUIS można klucza punktu końcowego. 

Nie należy używać klucza punktu końcowego na potrzeby tworzenia aplikacji usługi LUIS. 

## <a name="use-endpoint-key-in-query"></a>Użyj klucza punktu końcowego w zapytaniu
Punkt końcowy usługi LUIS akceptuje dwa style zapytania, ale klucza punktu końcowego jednocześnie używać w różnych miejscach:

|zlecenia|Przykład lokalizacji adresu url i klucz|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>wartość ciągu zapytania `subscription-key`<br><br>Zmień wartość kwerendy punktu końcowego dla `subscription-key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisać klawisz ale nie zmieniaj wartości kwerendy punktu końcowego dla klucza subskrypcji ", nie używasz przydziału klucza punktu końcowego.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> wartość nagłówka `Ocp-Apim-Subscription-Key`<br><br>Zmień wartość kwerendy punktu końcowego dla `Ocp-Apim-Subscription-Key` z tworzenia klucza (starter), do nowego klucza punktu końcowego, aby można było używać współczynnik przydziału klucza punktu końcowego usługi LUIS. Jeśli klucz zostanie utworzony i przypisz klucza, ale nie zmieniaj wartości kwerendy punktu końcowego dla `Ocp-Apim-Subscription-Key`, nie używasz przydziału klucza punktu końcowego.|

Identyfikator aplikacji używane w poprzednich adresów URL, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, jest publiczny aplikacja IoT, używana do [pokaz interaktywny](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Użycie interfejsu API Ocp-Apim-Subscription-Key
Interfejsy API usługi LUIS Użyj wartości header `Ocp-Apim-Subscription-Key`. Nazwa nagłówka nie zmienia się zależnie od którego klucz i zestaw interfejsów API używają. Ustaw nagłówek do tworzenia klucza do tworzenia interfejsów API. Jeśli używasz punktu końcowego Ustaw nagłówek do klucza punktu końcowego. 

Nie można przekazać klucza punktu końcowego do tworzenia interfejsów API. Jeśli to zrobisz, uzyskasz błąd 401 — Odmowa z powodu nieprawidłowy punkt końcowy klucz dostępu. 

## <a name="key-limits"></a>Ograniczenia klucza
Zobacz [klucza limity](luis-boundaries.md#key-limits) i [regionów świadczenia usługi Azure](luis-reference-regions.md). Tworzenia klucza jest wolną i używaną do tworzenia. Klucza punktu końcowego usługi LUIS oferuje bezpłatną warstwę, ale muszą być tworzone przez użytkownika i skojarzone z aplikacją usługi LUIS w **Publikuj** strony. Nie można używać do tworzenia, ale tylko punkt końcowy zapytania.

Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzenie aplikacji w regionie tworzenia odpowiednich do regionu publikowania, który ma.

## <a name="key-limit-errors"></a>Błędy limitu klucza
W przypadku przekroczenia usługi na drugim limit przydziału, wystąpi błąd HTTP 429. W przypadku przekroczenia usługi na miesiąc limit przydziału, komunikat o błędzie HTTP 403. Usuń te błędy, uzyskując LUIS [punktu końcowego](#endpoint-key) klucza, [przypisywanie](luis-how-to-manage-keys.md#assign-endpoint-key) klucza do aplikacji w **publikowania** strony [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web.

## <a name="automating-assignment-of-the-endpoint-key"></a>Automatyzowanie przypisanie klucza punktu końcowego

Aby można było przypisać klucza punktu końcowego do aplikacji usługi LUIS, należy użyć witryny sieci Web usługi LUIS poprawne tworzenie i publikowanie [regionów](luis-reference-regions.md). Brak **nie** zautomatyzowanej metody to zrobić, niezależnie od tego mechanizmu, takie jak za pomocą usługi Azure resource Menedżera skryptu, wiersza polecenia platformy Azure, SDK programowy, lub za pomocą interfejsów API.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [pojęcia](luis-how-to-manage-keys.md#assign-endpoint-key) o kluczach do tworzenia i punktu końcowego.