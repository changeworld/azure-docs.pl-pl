---
title: Tłumaczenie mowy za pomocą mowy zestawu SDK dla języka C++
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera przykładowy kod do tłumaczenia mowy przy użyciu zestawu SDK rozpoznawania mowy w środowisku języka C++.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: dd73314f5151be848567db3131ef016404f94829
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864319"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-c"></a>Tłumaczenie mowy z zestawem SDK mowy usług Cognitive dla języka C++

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-cpp[Translation using a microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#TranslationWithMicrophone)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj kodu, który jest używany w tym artykule w folderze samples/cpp/windows/console.

## <a name="next-steps"></a>Kolejne kroki

- [Jak rozpoznawać mowę](how-to-recognize-speech-cpp.md)
- [Rozpoznawanie intencji z wypowiedzi](how-to-recognize-intents-from-speech-cpp.md)
