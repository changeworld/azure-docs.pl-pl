---
title: Znajdź region usługi LUIS przy użyciu środowiska Node.js w granicach Language Understanding (LUIS) | Dokumentacja firmy Microsoft
description: Programowe wyszukiwanie publikowania w regionie za pomocą klucza punktu końcowego i aplikacji identyfikator usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: diberry
ms.openlocfilehash: 66a34b5b611934ca531e7709b13e0c6d1c794206
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222515"
---
# <a name="region-can-be-determined-from-api-call"></a>Można określić region z wywołania interfejsu API 
W przypadku usługi LUIS Identyfikatora aplikacji i identyfikator subskrypcji usługi LUIS można znaleźć który region należy używać dla punktu końcowego zapytań.

> [!NOTE] 
> Kompletne rozwiązanie Node.js jest dostępne z [ **przykłady LUIS** repozytorium Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Strategia kwerendy punktu końcowego usługi LUIS
Każda kwerenda punktu końcowego usługi LUIS wymaga:

* Klucz punktu końcowego
* Identyfikator aplikacji
* Region

Jeśli kwerendy punktu końcowego usługi LUIS korzysta z właściwego punktu końcowego Identyfikatora klucza i aplikacji, ale niewłaściwym regionie, kod odpowiedzi jest 401. Żądania 401 nie jest wliczane do limitu przydziału subskrypcji. Przekształć to żądanie w strategii sondowanie we wszystkich regionach, aby znaleźć poprawny region. Poprawny region jest tylko żądania, które zwraca kod stanu 2xx. 

|Kod odpowiedzi|Parametry|
|--|--|
|2xx|klucz właściwego punktu końcowego<br>Popraw Identyfikatora aplikacji<br>region właściwy host|
|401|klucz właściwego punktu końcowego<br>Popraw Identyfikatora aplikacji<br>_niepoprawne_ regionu hosta|

## <a name="nodejs-code-to-find-region"></a>Kod node.js, aby znaleźć region
Aplikacja konsoli przyjmuje identyfikator aplikacji usługi LUIS i klucza punktu końcowego i zwraca wszystkich regionów skojarzonych z nim. Obecnie klucza punktu końcowego jest tworzony przez region powinien zwrócić więc tylko jeden region.

Zawiera zależności rozwiązania NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Dodaj stałe. Zastąp wartości zmiennej `subscriptionKey` i `appId` własnymi wartościami.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Dodaj `searchRegions` funkcję, aby znaleźć region. We wszystkich regionach niepoprawne Zwróć 401, który jest przechwycony i ignorowane.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Wywołaj `searchRegions` funkcję i zwracają jeden region:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Gdy aplikacja jest uruchomiona, naciśnij w terminalu pokazuje region dla identyfikatora aplikacji.

![Zrzut ekranu przedstawiający aplikację konsolową przedstawiający region usługi LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi LUIS [regionów](luis-reference-regions.md).
