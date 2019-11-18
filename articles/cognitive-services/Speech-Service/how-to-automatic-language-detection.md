---
title: Jak używać automatycznego wykrywania języka dla zamiany mowy na tekst
titleSuffix: Azure Cognitive Services
description: Zestaw Speech SDK obsługuje automatyczne wykrywanie języka dla zamiany mowy na tekst. W przypadku korzystania z tej funkcji, podany dźwięk jest porównywany z podaną listą języków i jest określana najprawdopodobniej. Zwracana wartość może następnie zostać użyta do wybrania modelu języka używanego do zamiany mowy na tekst.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: a72f477e64c856c545801533c131c397de627c00
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110181"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatyczne wykrywanie języka dla zamiany mowy na tekst

Funkcja automatycznego wykrywania języka służy do określania najbardziej pasującego dźwięku przekazanego do zestawu Speech SDK w porównaniu z listą podanych języków. Wartość zwracana przez funkcję automatycznego wykrywania języka służy do wybierania modelu języka dla zamiany mowy na tekst, co zapewnia dokładniejsze transkrypcje. Aby zobaczyć, które języki są dostępne, zobacz temat [Obsługa języków](language-support.md).

W tym artykule dowiesz się, jak utworzyć obiekt `SpeechRecognizer` i pobrać wykryty język przy użyciu `AutoDetectSourceLanguageConfig`.

> [!IMPORTANT]
> Ta funkcja jest dostępna tylko dla zestawu Speech SDK dla C++ programu i zestawu Speech SDK dla języka Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatyczne wykrywanie języka za pomocą zestawu Speech SDK

Funkcja automatycznego wykrywania języka ma obecnie limit dwóch języków na wykrycie po stronie usług. To ograniczenie należy wziąć pod uwagę podczas konstruowania obiektu `AudoDetectSourceLanguageConfig`. W poniższych przykładach utworzysz `AutoDetectSourceLanguageConfig`, a następnie użyjesz go do skonstruowania `SpeechRecognizer`.

> [!TIP]
> Możesz również określić model niestandardowy, który ma być używany podczas wykonywania zamiany mowy na tekst. Aby uzyskać więcej informacji, zobacz [Korzystanie z modelu niestandardowego na potrzeby automatycznego wykrywania języka](#use-a-custom-model-for-automatic-language-detection).

Poniższe fragmenty kodu ilustrują sposób korzystania z automatycznego wykrywania języka w aplikacjach:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Używanie modelu niestandardowego do automatycznego wykrywania języka

Oprócz wykrywania języka przy użyciu modeli usługi Speech Service można określić model niestandardowy do rozszerzonego rozpoznawania. Jeśli niestandardowy model nie zostanie podany, usługa użyje domyślnego modelu języka.

Poniższe fragmenty kodu ilustrują sposób określania niestandardowego modelu w wywołaniu usługi mowy. Jeśli wykryty język jest `en-US`, zostanie użyty domyślny model. Jeśli wykryty język jest `fr-FR`, używany jest punkt końcowy dla modelu niestandardowego:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
