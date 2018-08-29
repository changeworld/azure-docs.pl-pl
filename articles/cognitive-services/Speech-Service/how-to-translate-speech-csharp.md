---
title: Tłumaczenie mowy za pomocą mowy zestawu SDK dla języka C#
titleSuffix: Microsoft Cognitive Services
description: Pokazuje, jak tłumaczenie mowy, używając mowy zestawu SDK dla języka C#.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3ac75c8bab5b4c888ce2cf02e2b270d7eb76a1ef
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144716"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-c"></a>Tłumaczenie mowy z zestawem SDK mowy usług Cognitive dla języka C#

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-csharp[Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-csharp[Translation from file input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj kodu, który jest używany w tym artykule w folderze samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoznawanie mowy](how-to-recognize-speech-csharp.md)
- [Rozpoznawanie intencji z wypowiedzi](how-to-recognize-intents-from-speech-csharp.md)
