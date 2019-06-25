---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183521"
---
### <a name="standard-and-neural-voices"></a>Standardowe i neuronowych głosów

Aby ustalić dostępność standardowych i neuronowych głosów według regionu/punktu końcowego, należy użyć tej tabeli:

| Region | Endpoint | Standardowa głosów | Neural Voices |
|--------|----------|-----------------|---------------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Yes |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Środkowe stany USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Nie |
| Wschodnie stany USA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Nie |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Środkowo-północne stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Środkowo-południowe stany USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Yes |
| Azja Południowo-Wschodnia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Yes |
| Europa Zachodnia | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Tak |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Nie |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Tak | Tak |

### <a name="custom-voices"></a>Głosów niestandardowych

Jeśli utworzono niestandardową czcionkę głosową, użyj punktu końcowego, który został utworzony. Można również użyć punktów końcowych wymienione poniżej pod warunkiem zastępowania `{deploymentId}` z Identyfikatorem wdrożenia dla modelu głosu.

| Region | Endpoint |
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
| Środkowo-północne stany USA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Północna | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Środkowo-południowe stany USA | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azja Południowo-Wschodnia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Południowe Zjednoczone Królestwo | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Zachodnia | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Zachodnie stany USA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
