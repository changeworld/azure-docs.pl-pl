---
title: Znajdź LUIS regionu w języku C# w granicach opis języka (LUIS) | Dokumentacja firmy Microsoft
description: Programowo Znajdź publikowania region z kluczem punktu końcowego i aplikacji identyfikator LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: f0df14736e0ed47957999e3aa7c6a22b0b0c0a35
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110415"
---
# <a name="region-can-be-determined-from-api-call"></a>Można określić region z wywołania interfejsu API 
Jeśli masz LUIS identyfikator aplikacji i LUIS identyfikator subskrypcji można znaleźć regionu, którego na potrzeby zapytań punktu końcowego.

> [!NOTE] 
> Kompletne rozwiązanie C# jest dostępne z [ **przykłady LUIS** repozytorium Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

## <a name="luis-endpoint-query-strategy"></a>Strategia zapytania punktu końcowego LUIS
Każde zapytanie punktu końcowego LUIS wymaga:

* Klucz punktu końcowego
* Identyfikator aplikacji
* Region

Jeśli zapytanie punktu końcowego LUIS używa Identyfikatora klucza i aplikacji właściwego punktu końcowego ale nieprawidłowy region, kod odpowiedzi jest 401. Żądania 401 nie są wliczane do limitu przydziału subskrypcji. Włącz to żądanie do strategii w celu sondowania wszystkich regionów, aby znaleźć poprawny region. Poprawny region jest tylko żądania, która zwraca kod stanu 2xx. 

|Kod odpowiedzi|Parametry|
|--|--|
|2xx|klucz właściwego punktu końcowego<br>Popraw identyfikator aplikacji<br>region właściwy host|
|401|klucz właściwego punktu końcowego<br>Popraw identyfikator aplikacji<br>_niepoprawna_ region hosta|

## <a name="c-class-code-to-find-region"></a>Klasa kod C#, aby znaleźć regionu
Aplikacji konsoli przyjmuje LUIS Identyfikatora aplikacji oraz klucz punktu końcowego i zwraca wszystkie regiony skojarzonych z nim. Obecnie jest tworzony klucz punktu końcowego według regionu tak tylko jeden region powinna zostać zwrócona.

Obejmują zależności biblioteki .net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Dodaj ten niestandardowej klasy LUIS wbudowane można znaleźć na region. Zastąp wartości zmiennej `luisAppId` i `luisSubscriptionKey` z własne wartości. Wszystkie regiony, które zwracają 401 będą zapisywane do konsoli debugowania. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Jest to przykład niestandardowej klasy LUIS podczas wywoływania metody Main aplikacji konsoli:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Po uruchomieniu aplikacji konsoli Pokazuje obszaru identyfikatora aplikacji.

![Zrzut ekranu przedstawiający LUIS region aplikacji konsoli](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o LUIS [regionów](luis-reference-regions.md).
