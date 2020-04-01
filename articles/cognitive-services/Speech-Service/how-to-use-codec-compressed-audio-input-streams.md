---
title: Skompresowany dźwięk kodeka strumieniowego za pomocą gniazda Mowy SDK — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowany dźwięk do usługi Mowy za pomocą SDK mowy. Dostępne dla języka C++, C#i Java dla systemów Linux, Java w systemie Android i Objective-C w systemie iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 6aabd27ae38cac0bb9effad2adcadc4935a28c6e
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409594"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Używanie skompresowanego wejścia audio kodeka z sdk mowy

Interfejs API SDK **Skompresowanego strumienia wejściowego audio** usługi mowy umożliwia strumieniowe przesyłanie skompresowanego dźwięku do usługi Mowy przy użyciu pliku `PullStream` lub `PushStream`.

> [!IMPORTANT]
> Streaming skompresowanego dźwięku wejściowego jest obecnie obsługiwany dla C#, C++, Java w systemie Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Jest również obsługiwany dla języka Java w systemie Android i Objective-C na platformie iOS.
> Wymagany jest numer SDK mowy w wersji 1.7.0 lub nowszej (wersja 1.10.0 lub nowsza dla RHEL 8, CentOS 8).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu skompresowanego wejścia audio kodeka

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak rozpoznać mowę](quickstarts/speech-to-text-from-microphone.md)
