---
title: Kody odpowiedzi HTTP interfejsu API — QnA Maker
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jakie kody odpowiedzi HTTP są zwracane za pomocą interfejsów API usługi QnA Maker. Ułatwi to usuń wszelkie błędy.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9eb5f1aefc8a36727e837af79f553108f624fc70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207976"
---
# <a name="qna-maker-api-http-response-codes"></a>Kody odpowiedzi usługi QnA Maker API HTTP
[Zarządzania](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) i prognozowania interfejsy API zwracają kody odpowiedzi HTTP. Chociaż komunikatach odpowiedzi zawiera informacje specyficzne dla żądania, kod stanu odpowiedzi HTTP jest ogólny. 

### <a name="management"></a>Zarządzanie

|Kod|Wyjaśnienie|
|:--|--|
|2xx|Powodzenie|
|400|Parametry żądania są nieprawidłowe, co oznacza, że wymagane parametry są brakujące, źle sformułowane lub zbyt duży|
|400|Treść żądania jest nieprawidłowa, co oznacza, że za pomocą pliku JSON jest Brak, źle sformułowane lub zbyt duży|
|401|Nieprawidłowy klucz|
|403|Dostęp zabroniony - nie masz odpowiednich uprawnień|
|404|KB nie istnieje.|
