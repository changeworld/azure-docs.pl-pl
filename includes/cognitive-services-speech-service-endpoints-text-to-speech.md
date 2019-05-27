---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145399"
---
### <a name="standard-and-neural-voices"></a>Standardowe i neuronowych głosów

Aby ustalić dostępność standardowych i neuronowych głosów według regionu/punktu końcowego, należy użyć tej tabeli:

| Obszar | Endpoint | Standardowa głosów | Neural Voices |
|--------|----------|-----------------|---------------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Środkowe stany USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Wschodnie stany USA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Południowo-środkowe stany USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Azja Południowo-Wschodnia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Zjednoczone Królestwo (południe) | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Europa Zachodnia | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |

### <a name="custom-voices"></a>Głosów niestandardowych

Jeśli utworzono niestandardową czcionkę głosową, użyj punktu końcowego, który został utworzony. Można również użyć punktów końcowych wymienione poniżej pod warunkiem zastępowania `{deploymentId}` z Identyfikatorem wdrożenia dla modelu głosu.

| Obszar | Endpoint |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada Środkowa | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Środkowe stany USA | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azja Wschodnia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Wschodnie stany USA | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Wschodnie stany USA 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Francja Środkowa | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indie Środkowe | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japonia Wschodnia | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea Środkowa | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Północno-środkowe stany USA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Północna | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Południowo-środkowe stany USA | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azja Południowo-Wschodnia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zjednoczone Królestwo (południe) | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Zachodnia | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
