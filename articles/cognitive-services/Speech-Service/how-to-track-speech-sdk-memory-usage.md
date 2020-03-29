---
title: Jak śledzić użycie pamięci SDK mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Zestaw SDK usługi mowy obsługuje wiele języków programowania do konwersji zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany mowy na tekst i zamiany tekstu na mowę, a także do tłumaczenia mowy. W tym artykule omówiono narzędzia do zarządzania pamięcią wbudowane w zestaw SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456433"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Jak śledzić użycie pamięci SDK mowy

Zestaw SDK mowy jest oparty na bazie kodu macierzystego, który jest rzutowany na wiele języków programowania za pośrednictwem serii warstw współdziałania. Każda projekcja specyficzne dla języka ma idiomatycznie poprawne funkcje do zarządzania cyklem życia obiektu. Ponadto zestaw SDK mowy zawiera narzędzia do zarządzania pamięcią do śledzenia użycia zasobów za pomocą rejestrowania obiektów i limitów obiektów. 

## <a name="how-to-read-object-logs"></a>Jak odczytać dzienniki obiektów

Jeśli [rejestrowanie SDK mowy jest włączone,](how-to-use-logging.md)znaczniki śledzenia są emitowane w celu umożliwienia obserwacji obiektów historycznych. Tagi te obejmują: 

* `TrackHandle` lub `StopTracking` 
* Typ obiektu
* Bieżąca liczba obiektów, które są śledzone typ obiektu i bieżąca liczba śledzone.

Oto przykładowy dziennik: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Ustawianie progu ostrzegawczego

Istnieje możliwość utworzenia progu ostrzeżenia, a jeśli próg zostanie przekroczony (przy założeniu, że rejestrowanie jest włączone), rejestrowanie jest rejestrowane. Komunikat ostrzegawczy zawiera zrzut wszystkich istniejących obiektów wraz z ich licznikiem. Te informacje mogą być wykorzystane do lepszego zrozumienia problemów. 

Aby włączyć próg ostrzeżenia, musi być `SpeechConfig` określony na obiekcie. Ten obiekt jest sprawdzany podczas tworzenia nowego aparatu rozpoznawania. W poniższych przykładach załóżmy, że utworzono `SpeechConfig` `config`wystąpienie wywoływane:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Wartość domyślna dla tej właściwości wynosi 10 000.

## <a name="set-an-error-threshold"></a>Ustawianie progu błędu 

Za pomocą zestawu SDK mowy można ustawić maksymalną liczbę obiektów dozwolonych w danym czasie. Jeśli to ustawienie jest włączone, gdy maksymalna liczba zostanie trafiona, próby utworzenia nowych obiektów aparatu rozpoznawania nie powiedzie się. Istniejące obiekty będą nadal działać.

Oto przykładowy błąd:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Aby włączyć próg błędu, musi być `SpeechConfig` określony na obiekcie. Ten obiekt jest sprawdzany podczas tworzenia nowego aparatu rozpoznawania. W poniższych przykładach załóżmy, że utworzono `SpeechConfig` `config`wystąpienie wywoływane:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Wartością domyślną dla tej właściwości jest maksymalna wartość dla danego dla platformy dla typu `size_t` danych. Typowe rozpoznawanie zużywa od 7 do 10 obiektów wewnętrznych.

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję próbną usługi mowy](get-started.md)
* [Dowiedz się, jak rozpoznawać mowę za pomocą mikrofonu](quickstarts/speech-to-text-from-microphone.md)