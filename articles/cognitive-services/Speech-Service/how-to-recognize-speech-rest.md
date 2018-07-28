---
title: Rozpoznawanie mowy, korzystając z interfejsu API REST
description: Dowiedz się, jak używać zamiana mowy na tekst w usłudze rozpoznawania mowy
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331479"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Rozpoznawanie mowy, korzystając z interfejsu API REST

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Interfejs API REST może służyć do rozpoznawania krótkich wypowiedzi za pomocą żądania HTTP POST.

Interfejs API REST jest najprostszym sposobem na rozpoznawanie mowy, jeśli nie używasz języka obsługiwanych przez zestaw SDK.
Wprowadzone żądanie HTTP POST do punktu końcowego usługi przekazywania całego wypowiedź w treści żądania.
Otrzymasz odpowiedź rozpoznany tekst.

> [!NOTE]
> Wypowiedzi są ograniczone do 15 sekund lub mniej, korzystając z interfejsu API REST.
> Zapoznaj się z [zestaw SDK rozpoznawania mowy](how-to-recognize-speech-csharp.md) rozpoznawania wypowiedzi dłuższe.

Aby uzyskać więcej informacji na temat **zamiana mowy na tekst** interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#speech-to-text). Aby zobaczyć go w działaniu, Pobierz [przykładów interfejsu API REST](https://github.com/Azure-Samples/SpeechToText-REST) z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

- [Zobacz Omówienie interfejsu API REST](rest-apis.md)
