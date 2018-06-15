---
title: Zrozumienie klucze LUIS - Azure | Dokumentacja firmy Microsoft
description: Użyj klawiszy opis języka (LUIS) do tworzenia aplikacji i endpoing Twojego zapytania.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: bf131877080f46781e74991ef627922384cc4c08
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349780"
---
# <a name="keys-in-luis"></a>Klucze w LUIS
LUIS wykorzystuje dwa klucze: [tworzenia](#programmatic-key) i [punktu końcowego](#endpoint-key). Po utworzeniu konta LUIS tworzenia klucza jest tworzony automatycznie. Gdy wszystko będzie gotowe do publikowania aplikacji LUIS należy [Utwórz klucz punktu końcowego](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [Przypisz do niego](Manage-keys.md#assign-endpoint-key) do aplikacji LUIS, i [korzystania z niego zapytanie punktu końcowego](#use-endpoint-key-in-query). 

|Klucz|Przeznaczenie|
|--|--|
|[Tworzenie klucza](#programmatic-key)|Tworzenie, publikowanie i zarządzanie współpracownicy, przechowywanie wersji|
|[Klucz punktu końcowego](#endpoint-key)| Wykonywanie zapytania|

Ważne jest, aby tworzyć aplikacje LUIS w [regionów](luis-reference-regions.md#publishing-regions) również miejscu do publikowania i zapytania.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Tworzenie klucza

Tworzenia klucza, znanej także jako klucza starter, jest tworzona automatycznie podczas tworzenia konta LUIS i jest bezpłatna. Mieć jeden klucz tworzenia pakietów administracyjnych między swoimi aplikacjami LUIS dla każdego tworzenia [region](luis-reference-regions.md). Tworzenia klucz jest dostarczany do tworzenia aplikacji LUIS lub testowanie zapytań punktu końcowego. 

Aby znaleźć tworzenia klucza, zaloguj się do [LUIS] [ LUIS] i kliknij nazwę konta, na pasku nawigacyjnym prawym górnym, aby otworzyć **ustawienia konta**.

![Tworzenie klucza](./media/luis-concept-keys/programatic-key.png)

Kiedy mają być **zapytań punktu końcowego produkcji**, Utwórz Azure [subskrypcji LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Dla wygody wiele próbek użyć klucza tworzenie, ponieważ zapewnia kilka wywołania punktu końcowego w jego [przydziału](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Klucz punktu końcowego
 Jeśli wymagane jest **zapytań punktu końcowego produkcji**, Utwórz [LUIS klucza](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) w portalu Azure. Zapamiętaj nazwę używaną do tworzenia klucza, należy po dodaniu klucza do aplikacji...

Po zakończeniu procesu subskrypcji LUIS [Dodaj klucz](Manage-keys.md#assign-endpoint-key) z aplikacją na **publikowania** strony. 

Klucz punktu końcowego umożliwia przydziału na podstawie planu użycia określone podczas tworzenia klucza trafień punktu końcowego. Zobacz [kognitywnych cennik usług](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) uzyskać informacje o cenach.

Klucz punktu końcowego może służyć do pracy z aplikacjami LUIS lub dla określonych aplikacji LUIS. 

Nie należy używać do tworzenia aplikacji LUIS klucz punktu końcowego. 

## <a name="use-endpoint-key-in-query"></a>Klucz punktu końcowego używany w zapytaniu
Punkt końcowy LUIS akceptuje dwa style zapytania, oba rozwiązania używają punktu końcowego o klucza, ale w różnych miejscach:

|Zlecenie|Przykładowy adres url i klucza lokalizacji|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn na kontrolki<br><br>wartość ciągu kwerendy `subscription-key`<br><br>Zmień wartość zapytania punktu końcowego dla `subscription-key` z tworzenia klucza (początkową), aby nowy klucz punktu końcowego, aby można było używać częstotliwość klucza przydziału LUIS punktu końcowego. Jeśli tworzenie klucza i przypisz klucz ale nie należy zmieniać wartości zapytania punktu końcowego dla klucza subskrypcji ", nie używasz limitu przydziału klucza punktu końcowego.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> wartość nagłówka `Ocp-Apim-Subscription-Key`<br><br>Zmień wartość zapytania punktu końcowego dla `Ocp-Apim-Subscription-Key` z tworzenia klucza (początkową), aby nowy klucz punktu końcowego, aby można było używać częstotliwość klucza przydziału LUIS punktu końcowego. Jeśli tworzenia klucza i przypisz klucza, ale nie należy zmieniać wartości zapytania punktu końcowego dla `Ocp-Apim-Subscription-Key`, limitu przydziału klucza punktu końcowego nie jest używany.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Użycie Ocp-Apim-subskrypcji-klucza interfejsu API
Interfejsy API LUIS Użyj wartości header `Ocp-Apim-Subscription-Key`. Nazwa nagłówka nie zmienia oparte na które klucza i zestaw interfejsów API używasz. Ustaw nagłówek do tworzenia klucza do tworzenia interfejsów API. Jeśli używasz punktu końcowego, ustawić nagłówek klucz punktu końcowego. 

Nie można przekazać klucz punktu końcowego do tworzenia interfejsów API. Jeśli to zrobisz, otrzymasz błąd 401 — Odmowa dostępu z powodu subskrypcji nieprawidłowy klucz. 

## <a name="key-limits"></a>Ograniczenia klucza
Zobacz [klucza limity](luis-boundaries.md#key-limits) i [regiony platformy Azure](luis-reference-regions.md). Tworzenia klucza jest wolną i używaną do tworzenia. Klucz LUIS subskrypcja ma warstwę bezpłatna, ale musi być utworzone przez użytkownika i skojarzone z aplikacją LUIS na **publikowania** strony. Nie można używać do tworzenia, ale tylko kwerendy punktu końcowego.

Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzenie aplikacji w polu tworzenia region do publikowania żądany region.

## <a name="key-limit-errors"></a>Błędy limit kluczy
Po przekroczeniu użytkownika na drugim przydziału komunikat o błędzie HTTP 429. Po przekroczeniu użytkownika na przydziału miesięcznego komunikat o błędzie HTTP 403. Usuń te błędy, pobierając LUIS [punktu końcowego](#endpoint-key) klucza, [przypisywanie](Manage-keys.md#assign-endpoint-key) klucz aplikacji na **publikowania** strony [LUIS] [ LUIS] witryny sieci Web.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się [pojęcia](Manage-Keys.md#assign-endpoint-key) o kluczach do tworzenia i punktu końcowego.

[LUIS]:luis-reference-regions.md#luis-website
