---
title: Rozpoznawanie mowy, korzystając z interfejsu API REST
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać funkcji rozpoznawania mowy do interfejsu API tłumaczenia tekstu w usłudze mowy w usłudze Cognitive Services.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: 1d8ae196884d800f441943609ac0189260a0ffcf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865740"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Rozpoznawanie mowy, korzystając z interfejsu API REST

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Interfejs API REST może służyć do rozpoznawania krótkich wypowiedzi za pomocą żądania HTTP POST.

Interfejs API REST jest najprostszym sposobem na rozpoznawanie mowy, jeśli nie używasz języka, który jest obsługiwany przez [SDK](speech-sdk.md). Utworzyć żądanie HTTP POST do punktu końcowego usługi i przekazać cały wypowiedź w treści żądania. Otrzymasz odpowiedzi, który został rozpoznany.

> [!NOTE]
> Wypowiedzi są ograniczone do 15 sekund lub mniej, korzystając z interfejsu API REST.
> Zapoznaj się z [zestaw SDK rozpoznawania mowy](how-to-recognize-speech-csharp.md) rozpoznawania wypowiedzi dłuższe.

Aby uzyskać więcej informacji na temat **zamiana mowy na tekst** interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#speech-to-text-api) artykułu. Aby wyświetlić interfejs API w działaniu, Pobierz [przykładów interfejsu API REST](https://github.com/Azure-Samples/SpeechToText-REST) z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [omówienie interfejsu API REST](rest-apis.md).
