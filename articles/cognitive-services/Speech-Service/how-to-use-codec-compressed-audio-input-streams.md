---
title: Koder-dekoder audio skompresowany dźwięk przy użyciu zestawu mowy SDK-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi mowy przy użyciu zestawu Speech SDK. Dostępne dla C++systemów C#, i Java dla systemu Linux, Java w systemach Android i w środowisku C w systemie iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943775"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Korzystanie z kodera-dekoder skompresowanego audio przy użyciu zestawu Speech SDK

Interfejs API **strumienia danych wejściowych audio** usługi Speech Service SDK umożliwia przesyłanie strumieniowe skompresowanego dźwięku do usługi mowy przy użyciu `PullStream` lub `PushStream`.

> [!IMPORTANT]
> Przesyłane strumieniowo skompresowane audio wejściowe jest obecnie C#obsługiwane C++przez program,, Java w systemie Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Jest ona również obsługiwana w przypadku języka Java w systemach Android i C na platformie iOS.
> Wymagany jest program Speech SDK w wersji 1.7.0 lub nowszej (w wersji 1.10.0 lub nowszej dla RHEL 8, CentOS 8).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak rozpoznać mowę](quickstarts/speech-to-text-from-microphone.md)
