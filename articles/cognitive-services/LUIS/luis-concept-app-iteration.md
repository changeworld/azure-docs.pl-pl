---
title: Projekt aplikacji iteracyjnej — LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256930"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Cykl tworzenia aplikacji LUIS
Usługa LUIS uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań. 

![Cykl tworzenia](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Budowanie modelu usługi LUIS
Model ma na celu ustalenie użytkownika pyta o (zamiar lub intencje) i które części pytanie podaj szczegóły (jednostki), pomagające ustalić odpowiedź. 

Model musi być specyficzne dla domeny aplikacji, aby określić słów i określającego, czy są odpowiednie, a także typowe word kolejności. 

Model wymaga intencji i _powinien mieć_ jednostki. 

## <a name="add-training-examples"></a>Dodaj przykłady szkolenia
Usługa LUIS musi przykład wypowiedzi w intencji. Przykłady muszą wystarczająco dużo odmianą word wybór i kolejność słów, aby można było określić, który zamiar wypowiedź jest przeznaczona dla. Wypowiedź każdy przykład musi mieć wszystkie wymagane dane oznaczone jako jednostki. 

Poinstruowanie LUIS w celu ignorowania wypowiedzi, które nie mają znaczenia dla domeny Twojej aplikacji, przypisując wypowiedź do **Brak** intencji. Wszelkie słów i fraz, których nie potrzebujesz ściągane poza wypowiedź nie trzeba mieć etykietę. Brak jest etykiety słów i fraz, aby zignorować. 

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji
Po otrzymaniu od 15 do 30 różnych wyrażenia długości w każdym zamiarze, z wymaganymi jednostkami z etykietą, należy przeprowadzić [uczenie](luis-how-to-train.md) i [opublikować](luis-how-to-publish-app.md). Z powiadomienie o pomyślnej publikacji użyj linku, aby pobrać punktów końcowych. Upewnij się, że tworzysz i publikujesz aplikację, tak aby była dostępna w wymaganych [regionach punktów końcowych](luis-reference-regions.md) . 

## <a name="https-prediction-endpoint-testing"></a>Testowanie punktu końcowego przewidywania protokołu HTTPS
Możesz przetestować aplikację LUIS z punktu końcowego przewidywania protokołu HTTPS. Testowanie z punktu końcowego przewidywania umożliwia LUIS wybranie dowolnego wyrażenia długości z niską pewnością do [przeglądu](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Odtwarzanie

Gdy skończysz, z cyklem tworzenia, możesz rozpocząć ponownie. Zacznij od [przejrzenia punktu końcowego przewidywania wyrażenia długości](luis-how-to-review-endpoint-utterances.md) Luis oznaczonego niską pewnością. Sprawdź te wypowiedzi zarówno intencji i jednostki. Po przejrzeniu wypowiedzi, przejrzyj listę powinien być pusty.  

Należy rozważyć [klonowanie](luis-concept-version.md#clone-a-version) bieżącej wersji do nowej wersji, a następnie rozpoczęcie tworzenia zmian w nowej wersji. 

## <a name="batch-testing"></a>Testowanie wsadowe

[Testowanie wsadowe](luis-concept-batch-test.md) jest sposobem na sprawdzenie, ile przykładów wyrażenia długości są oceniane przez Luis. Przykłady powinno być jesteś nowym użytkownikiem usługi LUIS i powinna być poprawnie oznaczone intencji i chcesz, aby usługa LUIS można znaleźć jednostki. Wyniki testu wskazują, jak w tym zestawie wypowiedzi wykona usługi LUIS. 

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [współpracy](luis-concept-keys.md).
