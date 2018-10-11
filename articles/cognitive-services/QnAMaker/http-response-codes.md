---
title: Kody odpowiedzi HTTP interfejsu API — QnA Maker
titleSuffix: Azure Cognitive Services
description: Zrozumienie, jakie kody odpowiedzi HTTP są zwracane za pomocą interfejsów API usługi QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079683"
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