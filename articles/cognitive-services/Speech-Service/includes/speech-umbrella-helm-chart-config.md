---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły dotyczące opcji konfiguracji grafu Helm.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523525"
---
### <a name="speech-umbrella-chart"></a>Speech (wykres parasol)

Wartości na wykresie "parasol" najwyższego poziomu przesłaniają odpowiednie wartości wykresów podrzędnych. W związku z tym w tym miejscu należy dodać wszystkie lokalne, dostosowane wartości.

|Parametr|Opis|Domyślne|
| -- | -- | -- | -- |
| `speechToText.enabled` | Określa, czy jest włączona usługa **zamiany mowy na tekst** . | `true` |
| `speechToText.verification.enabled` | Czy funkcja `helm test` dla usługi **zamiany mowy na tekst** jest włączona. | `true` |
| `speechToText.verification.image.registry` | Repozytorium obrazów platformy Docker, którego `helm test` używa do testowania usługi **zamiany mowy na tekst** . Helm tworzy oddzielny pod względem klastra w celu *przetestowania i ściąga obraz z* tego rejestru. | `docker.io` |
| `speechToText.verification.image.repository` | Repozytorium obrazów platformy Docker, którego `helm test` używa do testowania usługi **zamiany mowy na tekst** . Test Helm korzysta z tego repozytorium do ściągania obrazu z *użyciem testu* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Tag obrazu platformy Docker używany z `helm test` dla usługi **zamiany mowy na tekst** . Test Helm wykorzystuje ten tag do ściągania obrazu z *użyciem testu* . | `latest` |
| `speechToText.verification.image.pullByHash` | Czy obraz platformy Docker *test-use* jest ściągany przez skrót. W przypadku `true`należy dodać `speechToText.verification.image.hash` z prawidłową wartością skrótu obrazu. | `false` |
| `speechToText.verification.image.arguments` | Argumenty używane do wykonywania *testu — Użyj* obrazu platformy Docker. Helm test pod przekazuje te argumenty do kontenera podczas uruchamiania `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Czy usługa **zamiany tekstu na mowę** jest włączona. | `true` |
| `textToSpeech.verification.enabled` | Czy funkcja `helm test` dla usługi **zamiany mowy na tekst** jest włączona. | `true` |
| `textToSpeech.verification.image.registry` | Repozytorium obrazów platformy Docker, którego `helm test` używa do testowania usługi **zamiany mowy na tekst** . Helm tworzy oddzielny pod względem klastra w celu *przetestowania i ściąga obraz z* tego rejestru. | `docker.io` |
| `textToSpeech.verification.image.repository` | Repozytorium obrazów platformy Docker, którego `helm test` używa do testowania usługi **zamiany mowy na tekst** . Test Helm korzysta z tego repozytorium do ściągania obrazu z *użyciem testu* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Tag obrazu platformy Docker używany z `helm test` dla usługi **zamiany mowy na tekst** . Test Helm wykorzystuje ten tag do ściągania obrazu z *użyciem testu* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Czy obraz platformy Docker *test-use* jest ściągany przez skrót. W przypadku `true`należy dodać `textToSpeech.verification.image.hash` z prawidłową wartością skrótu obrazu. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty, które mają zostać wykonane przy użyciu obrazu platformy Docker *test-use* . Test Helm pod przekazaniem tych argumentów do kontenera podczas uruchamiania `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |