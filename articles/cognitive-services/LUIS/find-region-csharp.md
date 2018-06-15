---
title: Znajdź LUIS regionu w języku C# w granicach opis języka (LUIS) | Dokumentacja firmy Microsoft
description: Programowo Znajdź publikowania region z kluczem subskrypcji i aplikacji identyfikator LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2018
ms.locfileid: "35355980"
---
# <a name="region-can-be-determined-from-api-call"></a>Można określić region z wywołania interfejsu API 
Jeśli masz LUIS identyfikator aplikacji i LUIS identyfikator subskrypcji można znaleźć regionu, którego na potrzeby zapytań punktu końcowego.

> [!NOTE] 
> Kompletne rozwiązanie C# jest dostępne z [ **przykłady LUIS** repozytorium Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>Klasa kod C#, aby znaleźć regionu
Aplikacji konsoli przyjmuje klucz subskrypcji i identyfikator aplikacji LUIS i zwraca wszystkie regiony skojarzonych z nim. Obecnie jest tworzony klucz subskrypcji według regionu tak tylko jeden region powinna zostać zwrócona.

Obejmują zależności biblioteki .net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Dodaj ten niestandardowej klasy LUIS wbudowane można znaleźć na region. Zastąp wartości zmiennej `luisAppId` i `luisSubscriptionKey` z własne wartości. Wszystkie regiony, które zwracają 401 będą zapisywane do konsoli debugowania. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Jest to przykład niestandardowej klasy LUIS podczas wywoływania metody Main aplikacji konsoli:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Po uruchomieniu aplikacji konsoli Pokazuje obszar dla aplikacji klucza identyfikator i subskrypcji.

![Zrzut ekranu przedstawiający LUIS region aplikacji konsoli](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o LUIS [regionów](luis-reference-regions.md).
