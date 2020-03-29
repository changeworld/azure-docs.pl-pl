---
title: Jak określić język źródłowy mowy do tekstu
titleSuffix: Azure Cognitive Services
description: Pakiet SDK mowy umożliwia określenie języka źródłowego podczas konwertowania mowy na tekst. W tym artykule opisano, jak używać FromConfig i SourceLanguageConfig metody, aby umożliwić usługi mowy znać język źródłowy i zapewnić niestandardowy model docelowy.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235966"
---
# <a name="specify-source-language-for-speech-to-text"></a>Określanie języka źródłowego mowy na tekst

W tym artykule dowiesz się, jak określić język źródłowy dla danych wejściowych audio przekazanych do SDK mowy do rozpoznawania mowy. Ponadto przykładowy kod jest dostarczany, aby określić niestandardowy model mowy dla lepszego rozpoznawania.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Jak określić język źródłowy w języku C #

W tym przykładzie język źródłowy jest jawnie dostarczany jako parametr przy użyciu `SpeechRecognizer` konstrukcji.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

W tym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig`programu . Następnie `sourceLanguageConfig` jest przekazywana jako `SpeechRecognizer` parametr do konstruowania.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

W tym przykładzie język źródłowy i `SourceLanguageConfig`niestandardowy punkt końcowy są dostarczane przy użyciu . Następnie `sourceLanguageConfig` jest przekazywana jako `SpeechRecognizer` parametr do konstruowania.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`i `EndpointId` set metody są przestarzałe z `SpeechConfig` klasy w języku C#. Stosowanie tych metod są odradzane i nie powinny `SpeechRecognizer`być używane podczas konstruowania .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Jak określić język źródłowy w języku C++

W tym przykładzie język źródłowy jest jawnie `FromConfig` dostarczany jako parametr przy użyciu metody.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

W tym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig`programu . Następnie `sourceLanguageConfig` jest przekazywana jako `FromConfig` parametr podczas `recognizer`tworzenia pliku .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

W tym przykładzie język źródłowy i `SourceLanguageConfig`niestandardowy punkt końcowy są dostarczane przy użyciu . Jest `sourceLanguageConfig` przekazywana jako `FromConfig` parametr podczas `recognizer`tworzenia pliku .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`i `SetEndpointId` są przestarzałe `SpeechConfig` metody z klasy w językach C++ i Java. Stosowanie tych metod są odradzane i nie powinny `SpeechRecognizer`być używane podczas konstruowania .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Jak określić język źródłowy w języku Java

W tym przykładzie język źródłowy jest jawnie `SpeechRecognizer`podany podczas tworzenia nowego pliku .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

W tym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig`programu . Następnie `sourceLanguageConfig` jest przekazywana jako parametr podczas `SpeechRecognizer`tworzenia nowego .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

W tym przykładzie język źródłowy i `SourceLanguageConfig`niestandardowy punkt końcowy są dostarczane przy użyciu . Następnie `sourceLanguageConfig` jest przekazywana jako parametr podczas `SpeechRecognizer`tworzenia nowego .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`i `setEndpointId` są przestarzałe `SpeechConfig` metody z klasy w językach C++ i Java. Stosowanie tych metod są odradzane i nie powinny `SpeechRecognizer`być używane podczas konstruowania .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Jak określić język źródłowy w języku Python

W tym przykładzie język źródłowy jest jawnie dostarczany jako parametr przy użyciu `SpeechRecognizer` konstrukcji.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

W tym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig`programu . Następnie `SourceLanguageConfig` jest przekazywana jako `SpeechRecognizer` parametr do konstruowania.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

W tym przykładzie język źródłowy i `SourceLanguageConfig`niestandardowy punkt końcowy są dostarczane przy użyciu . Następnie `SourceLanguageConfig` jest przekazywana jako `SpeechRecognizer` parametr do konstruowania.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`i `endpoint_id` właściwości są przestarzałe z `SpeechConfig` klasy w Pythonie. Korzystanie z tych właściwości są odradzane i nie powinny `SpeechRecognizer`być używane podczas konstruowania .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Jak określić język źródłowy w javascript

Pierwszym krokiem jest `SpeechConfig`utworzenie :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Następnie określ język źródłowy `speechRecognitionLanguage`dźwięku za pomocą:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Jeśli używasz niestandardowego modelu do rozpoznawania, możesz określić punkt końcowy za pomocą: `endpointId`

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Jak określić język źródłowy w języku Objective-C

Pierwszym krokiem jest `speechConfig`utworzenie :

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Następnie określ język źródłowy `speechRecognitionLanguage`dźwięku za pomocą:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Jeśli używasz niestandardowego modelu do rozpoznawania, możesz określić punkt końcowy za pomocą: `endpointId`

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Zobacz też

* Aby uzyskać listę obsługiwanych języków i ustawień regionalnych dla mowy do tekstu, zobacz [Obsługa języka](language-support.md).

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna SDK mowy](speech-sdk.md)
