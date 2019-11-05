---
title: 'Szybki Start: pobieranie zamierzeń przy użyciu interfejsów API REST — LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start interfejsu API REST Użyj dostępnej publicznej aplikacji LUIS, aby określić zamiar użytkownika z tekstu konwersacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 3a8badb74bb8919876f3c0670d785f44fbcbb397
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499674"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Szybki Start: pobieranie zamierzeń przy użyciu interfejsów API REST

W tym przewodniku Szybki start użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji. Wyślesz intencję użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji publicznej. W punkcie końcowym usługa LUIS zastosuje model aplikacji publicznej, aby przeanalizować tekst w języku naturalnym pod kątem znaczenia, określając ogólną intencję i wyodrębniając dane dotyczące domeny podmiotu aplikacji. 

W tym przewodniku Szybki start jest używany punkt końcowy interfejsu API REST. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API punktu końcowego](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

W przypadku tego artykułu jest potrzebne bezpłatne konto [usługi LUIS](https://www.luis.ai). 

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"

[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-go"

[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]

::: zone-end
