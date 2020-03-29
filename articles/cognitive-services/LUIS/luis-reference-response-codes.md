---
title: Kody odpowiedzi HTTP interfejsu API — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jakie kody odpowiedzi HTTP są zwracane z interfejsów API tworzenia usługi LUIS i punktu końcowego
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 405381a2d80ee6469b2b90bcb25923004fe55882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270248"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Wspólne kody odpowiedzi API i ich znaczenie

Interfejsy API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) i [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) zwracają kody odpowiedzi HTTP. Podczas gdy komunikaty odpowiedzi zawierają informacje specyficzne dla żądania, kod stanu odpowiedzi HTTP jest ogólny.

## <a name="common-status-codes"></a>Wspólne kody stanu
W poniższej tabeli wymieniono niektóre z najczęstszych kodów stanu odpowiedzi HTTP dla [interfejsów](https://go.microsoft.com/fwlink/?linkid=2092087) API tworzenia i [punktu końcowego:](https://go.microsoft.com/fwlink/?linkid=2092356)

|Code|interfejs API|Wyjaśnienie|
|:--|--|--|
|400|Tworzenie, punkt końcowy|parametry żądania są niepoprawne, co oznacza, że brakuje wymaganych parametrów, zniekształconych lub zbyt dużych|
|400|Tworzenie, punkt końcowy|treść żądania jest nieprawidłowa, co oznacza, że json jest brak, zniekształcony lub zbyt duży|
|401|Tworzenie|używany klucz subskrypcji punktu końcowego, zamiast klucza tworzenia|
|401|Tworzenie, punkt końcowy|nieprawidłowy, nieprawidłowo sformułowany lub pusty klucz|
|401|Tworzenie, punkt końcowy| klucz nie jest zgodny z regionem|
|401|Tworzenie|nie jesteś właścicielem ani współpracownikiem|
|401|Tworzenie|nieprawidłowa kolejność wywołań interfejsu API|
|403|Tworzenie, punkt końcowy|całkowity miesięczny limit przydziału klucza przekroczony|
|409|Endpoint|aplikacja nadal się ładuje|
|410|Endpoint|wniosek musi zostać przeszkolony i ponownie opublikowany|
|414|Endpoint|kwerenda przekracza maksymalny limit znaków|
|429|Tworzenie, punkt końcowy|Limit szybkości jest przekroczony (żądania/sekundę)|

## <a name="next-steps"></a>Następne kroki

* Tworzenie [interfejsu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) API REST i dokumentacja [punktu końcowego](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
