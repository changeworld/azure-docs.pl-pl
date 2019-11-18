---
title: Jak określić język źródłowy dla zamiany mowy na tekst
titleSuffix: Azure Cognitive Services
description: Zestaw Speech SDK umożliwia określenie języka źródłowego podczas konwertowania mowy na tekst. W tym artykule opisano sposób korzystania z metod FromConfig i SourceLanguageConfig w celu umożliwienia usłudze mowy znajomości języka źródłowego i dostarczania niestandardowego obiektu docelowego modelu.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109953"
---
# <a name="specify-source-language-for-speech-to-text"></a>Określ język źródłowy dla zamiany mowy na tekst

W tym artykule dowiesz się, jak określić język źródłowy wejścia audio przesłanego do zestawu Speech SDK na potrzeby rozpoznawania mowy. Dodatkowo przykładowy kod jest dostarczany, aby określić niestandardowy model mowy na potrzeby ulepszonego rozpoznawania.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Jak określić język źródłowy wC#

Pierwszym krokiem jest utworzenie `SpeechConfig`:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Następnie określ język źródłowy dźwięku za pomocą `SpeechRecognitionLanguage`:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Jeśli używasz modelu niestandardowego do rozpoznawania, możesz określić punkt końcowy z `EndpointId`:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Jak określić język źródłowy wC++

W tym przykładzie język źródłowy jest dostarczany jawnie jako parametr przy użyciu metody `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

W tym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig`. Następnie `sourceLanguageConfig` jest przenoszona jako parametr do `FromConfig` podczas tworzenia `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

W tym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu `SourceLanguageConfig`. `sourceLanguageConfig` jest przenoszona jako parametr do `FromConfig` podczas tworzenia `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` i `SetEndpointId` są przestarzałymi metodami z klasy `SpeechConfig` w C++ języku i Java. Korzystanie z tych metod jest niezalecane i nie powinno być używane podczas konstruowania `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Jak określić język źródłowy w języku Java

W tym przykładzie język źródłowy jest dostarczany jawnie podczas tworzenia nowego `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

W tym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig`. Następnie `sourceLanguageConfig` jest przenoszona jako parametr podczas tworzenia nowego `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

W tym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu `SourceLanguageConfig`. Następnie `sourceLanguageConfig` jest przenoszona jako parametr podczas tworzenia nowego `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` i `setEndpointId` są przestarzałymi metodami z klasy `SpeechConfig` w C++ języku i Java. Korzystanie z tych metod jest niezalecane i nie powinno być używane podczas konstruowania `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Jak określić język źródłowy w języku Python

Pierwszym krokiem jest utworzenie `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Następnie określ język źródłowy dźwięku za pomocą `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Jeśli używasz modelu niestandardowego do rozpoznawania, możesz określić punkt końcowy z `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Jak określić język źródłowy w języku JavaScript

Pierwszym krokiem jest utworzenie `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Następnie określ język źródłowy dźwięku za pomocą `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Jeśli używasz modelu niestandardowego do rozpoznawania, możesz określić punkt końcowy z `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Jak określić język źródłowy w zamierzeniu-C

Pierwszym krokiem jest utworzenie `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Następnie określ język źródłowy dźwięku za pomocą `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Jeśli używasz modelu niestandardowego do rozpoznawania, możesz określić punkt końcowy z `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Zobacz także

* Aby uzyskać listę obsługiwanych języków i ustawień regionalnych dla zamiany mowy na tekst, zobacz temat [Obsługa języków](language-support.md).

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
