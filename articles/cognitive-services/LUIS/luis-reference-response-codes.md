---
title: Kody odpowiedzi HTTP interfejsu API opis (LUIS) języka - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Zrozumienie, jakie kody odpowiedzi HTTP są zwracane z tworzenia LUIS i interfejsów API punktu końcowego
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347753"
---
# <a name="luis-api-http-response-codes"></a>Kody odpowiedzi HTTP interfejsu API LUIS
[Tworzenia](https://aka.ms/luis-authoring-apis) i [punktu końcowego](https://aka.ms/luis-endpoint-apis) interfejsów API zwraca kody odpowiedzi HTTP. Chociaż wiadomości odpowiedzi zawiera informacje specyficzne dla żądania, kod stanu odpowiedzi HTTP jest ogólne. 

## <a name="common-status-codes"></a>Typowe kody stanu
W poniższej tabeli wymieniono niektóre z najczęściej kody stanu odpowiedzi HTTP dla [tworzenia](https://aka.ms/luis-authoring-apis) i [punktu końcowego](https://aka.ms/luis-endpoint-apis) interfejsów API:

|Kod|Interfejs API|Wyjaśnienie|
|:--|--|--|
|400|Tworzenie punktu końcowego|Parametry żądania są niepoprawne, co oznacza, że wymagane parametry są brakujące, źle sformułowane lub zbyt duży|
|400|Tworzenie punktu końcowego|Treść żądania jest nieprawidłowy, co oznacza, że kod JSON jest Brak, źle sformułowany lub zbyt duży|
|401|Tworzenie|używany klucz subskrypcji punktu końcowego, zamiast tworzenia klucza|
|401|Tworzenie punktu końcowego|nieprawidłowy, źle sformułowane lub pusty klucz|
|401|Tworzenie punktu końcowego| klucz nie pasuje do regionu|
|401|Tworzenie|nie jesteś właścicielem lub współpracownika|
|401|Tworzenie|Nieprawidłowa kolejność wywołań interfejsu API|
|403|Tworzenie punktu końcowego|Całkowity miesięczny limit przydziału klucza przekroczony|
|409|Endpoint|Aplikacja jest nadal ładowany.|
|410|Endpoint|Aplikacja musi być retrained i ponownie opublikować|
|414|Endpoint|Zapytanie przekracza maksymalną dozwoloną długość|
|429|Tworzenie punktu końcowego|Przekroczono limit szybkości (żądania/sekundę)|