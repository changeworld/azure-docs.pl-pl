---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879030"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Zamiana mowy na tekst lub niestandardowa zamiana mowy na tekst

Kontener udostępnia interfejsy API punktu końcowego programu query oparte na websocket, które są dostępne za pośrednictwem [SDK mowy.](../index.yml) Domyślnie SDK mowy używa usług mowy online. Aby użyć kontenera, należy zmienić metodę inicjowania.

> [!TIP]
> Podczas korzystania z zestawu SDK mowy z kontenerami nie trzeba podawać klucza subskrypcji zasobu usługi Azure Speech [ani tokenu nośnego uwierzytelniania.](../rest-speech-to-text.md#authentication)

Zobacz poniższe przykłady.

# <a name="c"></a>[C#](#tab/csharp)

Zmień z przy użyciu tego wywołania inicjowania chmury platformy Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tego wywołania przy użyciu [hosta kontenera:](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Zmień z przy użyciu tego wywołania inicjowania chmury platformy Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

do tego wywołania przy użyciu [hosta kontenera:](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
