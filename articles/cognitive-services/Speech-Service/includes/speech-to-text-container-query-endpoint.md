---
title: Wysyłanie zapytań do punktu końcowego kontenera zamiany mowy na tekst
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491177"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Zamiana mowy na tekst lub Custom Speech na tekst

Kontener udostępnia interfejsy API punktu końcowego zapytania opartego na protokole WebSocket, do których można uzyskać dostęp za pośrednictwem [zestawu Speech SDK](../index.md). Domyślnie zestaw Speech SDK używa usług online Speech Services. Aby użyć kontenera, należy zmienić metodę inicjacji.

> [!TIP]
> W przypadku korzystania z zestawu Speech SDK z kontenerami nie trzeba podawać klucza subskrypcji zasobów usługi Azure Speech [ani tokenu okaziciela uwierzytelniania](../rest-speech-to-text.md#authentication).

Zapoznaj się z poniższymi przykładami.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tego wywołania przy użyciu [punktu końcowego](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet)kontenera:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

do tego wywołania przy użyciu [punktu końcowego](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)kontenera:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
