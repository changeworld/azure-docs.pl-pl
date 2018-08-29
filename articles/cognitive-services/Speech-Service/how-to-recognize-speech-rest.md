---
title: Rozpoznawanie mowy, korzystając z interfejsu API REST
description: Dowiedz się, jak używać funkcji rozpoznawania mowy do interfejsu API tłumaczenia tekstu w usłudze mowy w usłudze Cognitive Services.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: eafec2dd262098bc4b7e485293818b79debe3d27
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126850"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Rozpoznawanie mowy, korzystając z interfejsu API REST

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Interfejs API REST może służyć do rozpoznawania krótkich wypowiedzi za pomocą żądania HTTP POST.

Interfejs API REST jest najprostszym sposobem na rozpoznawanie mowy, jeśli nie używasz języka, który jest obsługiwany przez [SDK](speech-sdk.md). Utworzyć żądanie HTTP POST do punktu końcowego usługi i przekazać cały wypowiedź w treści żądania. Otrzymasz odpowiedzi, który został rozpoznany.

> [!NOTE]
> Wypowiedzi są ograniczone do 15 sekund lub mniej, korzystając z interfejsu API REST.
> Zapoznaj się z [zestaw SDK rozpoznawania mowy](how-to-recognize-speech-csharp.md) rozpoznawania wypowiedzi dłuższe.

Aby uzyskać więcej informacji na temat **zamiana mowy na tekst** interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#speech-to-text) artykułu. Aby wyświetlić interfejs API w działaniu, Pobierz [przykładów interfejsu API REST](https://github.com/Azure-Samples/SpeechToText-REST) z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [omówienie interfejsu API REST](rest-apis.md).
