---
title: Kody odpowiedzi HTTP interfejsu API — QnA Maker
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jakie kody odpowiedzi HTTP są zwracane za pomocą interfejsów API usługi QnA Maker. Ułatwi to usuń wszelkie błędy.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: cdfa5212f321cc6ec976ea9df301243acc54a23f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794860"
---
# <a name="qna-maker-api-http-response-codes"></a>Kody odpowiedzi usługi QnA Maker API HTTP
[Zarządzania](https://go.microsoft.com/fwlink/?linkid=2092179) i prognozowania interfejsy API zwracają kody odpowiedzi HTTP. Chociaż komunikatach odpowiedzi zawiera informacje specyficzne dla żądania, kod stanu odpowiedzi HTTP jest ogólny. 

### <a name="management"></a>Zarządzanie

|Kod|Wyjaśnienie|
|:--|--|
|2xx|Powodzenie|
|400|Parametry żądania są nieprawidłowe, co oznacza, że wymagane parametry są brakujące, źle sformułowane lub zbyt duży|
|400|Treść żądania jest nieprawidłowa, co oznacza, że za pomocą pliku JSON jest Brak, źle sformułowane lub zbyt duży|
|401|Nieprawidłowy klucz|
|403|Dostęp zabroniony - nie masz odpowiednich uprawnień|
|404|KB nie istnieje.|
|410|Ten interfejs API jest przestarzały i nie jest już dostępny|
