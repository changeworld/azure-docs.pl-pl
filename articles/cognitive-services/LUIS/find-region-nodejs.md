---
title: Znajdź region LUIS za pomocą języka Node.js w granicach opis języka (LUIS) | Dokumentacja firmy Microsoft
description: Programowo Znajdź publikowania region z kluczem subskrypcji i aplikacji identyfikator LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 18ee324c10f074601c0c04573ca1a5266481f21c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2018
ms.locfileid: "35355987"
---
# <a name="region-can-be-determined-from-api-call"></a>Można określić region z wywołania interfejsu API 
Jeśli masz LUIS identyfikator aplikacji i LUIS identyfikator subskrypcji można znaleźć regionu, którego na potrzeby zapytań punktu końcowego.

> [!NOTE] 
> Kompletne rozwiązanie Node.js jest dostępne z [ **przykłady LUIS** repozytorium Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Strategia zapytania punktu końcowego LUIS
Każde zapytanie punktu końcowego LUIS wymaga:

* Klucz subskrypcji
* Identyfikator aplikacji
* Region

Jeśli zapytanie punktu końcowego LUIS używa identyfikator subskrypcji poprawny klucz i aplikacji, ale nieprawidłowy region, kod odpowiedzi jest 401. Żądania 401 nie są wliczane do limitu przydziału subskrypcji. Włącz to żądanie do strategii w celu sondowania wszystkich regionów, aby znaleźć poprawny region. Poprawny region jest tylko żądania, która zwraca kod stanu 2xx. 

|Kod odpowiedzi|Parametry|
|--|--|
|2xx|klucz poprawną subskrypcję<br>Popraw identyfikator aplikacji<br>region właściwy host|
|401|klucz poprawną subskrypcję<br>Popraw identyfikator aplikacji<br>_niepoprawna_ region hosta|

## <a name="nodejs-code-to-find-region"></a>Kod node.js, aby znaleźć regionu
Aplikacji konsoli przyjmuje klucz subskrypcji i identyfikator aplikacji LUIS i zwraca wszystkie regiony skojarzonych z nim. Obecnie jest tworzony klucz subskrypcji według regionu tak tylko jeden region powinna zostać zwrócona.

Obejmują zależności NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Dodaj stałe. Zastąp wartości zmiennej `subscriptionKey` i `appId` z własne wartości.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Dodaj `searchRegions` funkcji można znaleźć na region. Wszystkie regiony niepoprawna zwracać 401, który jest przechwycony i zignorowany.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Wywołanie `searchRegions` funkcji i zwracać jednego regionu:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

Gdy aplikacja jest uruchamiana, terminal zawiera obszar dla aplikacji klucza identyfikator i subskrypcji.

![Zrzut ekranu przedstawiający LUIS region aplikacji konsoli](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o LUIS [regionów](luis-reference-regions.md).