---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 4f48fc0ad65984df42e2a85853f39e49d7481302
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020440"
---
### <a name="standard-and-neural-voices"></a>Standardowe i neuronowych głosów

Aby ustalić dostępność standardowych i neuronowych głosów według regionu/punktu końcowego, należy użyć tej tabeli:

| Region | Endpoint | Standardowa głosów | Neural Voices |
|--------|----------|-----------------|---------------|
| Australia Wschodnia | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Kanada Środkowa | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Środkowe stany USA | https://centralus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Azja Wschodnia | https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Wschodnie stany USA | https://eastus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Wschodnie stany USA 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Francja Środkowa | https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Indie Środkowe | https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Japonia Wschodnia | https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Korea Środkowa | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Środkowo-północne stany USA | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Europa Północna | https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Środkowo-południowe stany USA | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Azja Południowo-Wschodnia | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Południowe Zjednoczone Królestwo | https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Europa Zachodnia | https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Zachodnie stany USA | https://westus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Nie |
| Zachodnie stany USA 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |

### <a name="custom-voices"></a>Głosów niestandardowych

Jeśli utworzono niestandardową czcionkę głosową, użyj punktu końcowego, który został utworzony. Można również użyć punktów końcowych wymienione poniżej pod warunkiem zastępowania `{deploymentId}` z Identyfikatorem wdrożenia dla modelu głosu.

| Region | Endpoint |
|--------|----------|
| Australia Wschodnia | https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Kanada Środkowa | https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Środkowe stany USA | https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Azja Wschodnia | https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Wschodnie stany USA | https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Wschodnie stany USA 2 | https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Francja Środkowa | https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Indie Środkowe | https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Japonia Wschodnia | https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Korea Środkowa | https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Środkowo-północne stany USA | https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Europa Północna | https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Środkowo-południowe stany USA | https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Azja Południowo-Wschodnia | https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Południowe Zjednoczone Królestwo | https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Europa Zachodnia | https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Zachodnie stany USA | https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Zachodnie stany USA 2 | https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
