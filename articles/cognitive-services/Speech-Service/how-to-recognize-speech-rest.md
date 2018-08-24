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
ms.openlocfilehash: 24fa3882a65bf6605444a139ad5d4ee42800a8ef
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42744933"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Rozpoznawanie mowy, korzystając z interfejsu API REST

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Interfejs API REST może służyć do rozpoznawania krótkich wypowiedzi za pomocą żądania HTTP POST.

Interfejs API REST jest najprostszym sposobem na rozpoznawanie mowy, jeśli nie używasz języka obsługiwanych przez [SDK](speech-sdk.md).
Aby włączyć metodę POST protokołu HTTP żądania do punktu końcowego usługi, przekazać cały wypowiedź w treści żądania; i otrzymasz odpowiedź rozpoznany tekst.

> [!NOTE]
> Wypowiedzi są ograniczone do 15 sekund lub mniej, korzystając z interfejsu API REST.
> Zapoznaj się z [zestaw SDK rozpoznawania mowy](how-to-recognize-speech-csharp.md) rozpoznawania wypowiedzi dłuższe.

Aby uzyskać więcej informacji na temat **zamiana mowy na tekst** interfejsu API REST, zobacz [interfejsów API REST](rest-apis.md#speech-to-text). Aby zobaczyć go w działaniu, Pobierz [przykładów interfejsu API REST](https://github.com/Azure-Samples/SpeechToText-REST) z usługi GitHub.

## <a name="next-steps"></a>Kolejne kroki

- [Zobacz Omówienie interfejsu API REST](rest-apis.md)
