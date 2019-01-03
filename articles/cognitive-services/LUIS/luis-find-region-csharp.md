---
title: Region punktu końcowegoC#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Za pomocą C#, Znajdź publikowania w regionie za pomocą klucza punktu końcowego i aplikacji identyfikator usługi LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: f95dec8a539a92a0397421fbde411f646eeca3ca
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720088"
---
# <a name="programmatically-find-endpoint-region-with-c"></a>Programowe Znajdowanie punktu końcowego regionC# 
W przypadku usługi LUIS Identyfikatora aplikacji i identyfikator subskrypcji usługi LUIS można znaleźć który region należy używać dla punktu końcowego zapytań.

> [!NOTE] 
> Pełne C# rozwiązanie jest dostępne z [ **przykładów dla platformy Azure** repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/find-region/csharp/).

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

## <a name="c-class-code-to-find-region"></a>Klasa kod C#, aby znaleźć region
Aplikacja konsoli przyjmuje identyfikator aplikacji usługi LUIS i klucza punktu końcowego i zwraca wszystkich regionów skojarzonych z nim. Obecnie klucza punktu końcowego jest tworzony przez region powinien zwrócić więc tylko jeden region.

Zawiera zależności biblioteki .net:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Dodaj ten niestandardowej klasy usługi LUIS stworzone z myślą o odnaleźć regionu. Zastąp wartości zmiennej `luisAppId` i `luisSubscriptionKey` własnymi wartościami. Wszystkie regiony, które zwracają 401 będą zapisywane do konsoli debugowania. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Oto przykład podczas wywoływania niestandardowej klasy usługi LUIS w metody Main aplikacji konsoli:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

Po uruchomieniu aplikacji w konsoli wyświetlone region dla identyfikatora aplikacji.

![Zrzut ekranu przedstawiający aplikację konsolową przedstawiający region usługi LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi LUIS [regionów](luis-reference-regions.md).
