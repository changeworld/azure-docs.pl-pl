---
title: Zainstaluj kontenery mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcje konfiguracji wykresu helm parasola mowy.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711814"
---
### <a name="speech-umbrella-chart"></a>Mowy (wykres ogólny)

Wartości na wykresie najwyższego poziomu "ogólny" Zastąp odpowiednie wartości podrzędnych wykresu. W związku z tym wszystkie wartości w środowisku lokalnym dostosowane powinny zostać dodane w tym miejscu.

|Parametr|Opis|Domyślne|
| -- | -- | -- | -- |
| `speechToText.enabled` | Czy **mowy na tekst** usługa jest włączona. | `true` |
| `speechToText.verification.enabled` | Czy `helm test` możliwość **mowy na tekst** usługa jest włączona. | `true` |
| `speechToText.verification.image.registry` | Repozytorium obrazów platformy docker, `helm test` używa do testowania **mowy na tekst** usługi. Polecenie Helm tworzy osobne zasobnika wewnątrz klastra na potrzeby testowania i ściąga *Użyj testu* obraz na podstawie tego rejestru. | `docker.io` |
| `speechToText.verification.image.repository` | Repozytorium obrazów platformy docker, `helm test` używa do testowania **mowy na tekst** usługi. Zasobnik testu Helm używa tego repozytorium do ściągania *Użyj testu* obrazu. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Tag obrazu platformy docker, które są używane z `helm test` dla **mowy na tekst** usługi. Helm test pod używa ten tag, aby ściągnąć *Użyj testu* obrazu. | `latest` |
| `speechToText.verification.image.pullByHash` | Czy *Użyj testu* obrazu platformy docker zostanie ściągnięty przez wyznaczania wartości skrótu. Jeśli `true`, `speechToText.verification.image.hash` powinny zostać dodane z wartością skrótu prawidłowym obrazem. | `false` |
| `speechToText.verification.image.arguments` | Argumenty używane do wykonywania *Użyj testu* obrazu platformy docker. Zasobnik testu Helm przekazuje te argumenty do kontenera podczas uruchamiania `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Czy **zamiany tekstu na mowę** usługa jest włączona. | `true` |
| `textToSpeech.verification.enabled` | Czy `helm test` możliwość **mowy na tekst** usługa jest włączona. | `true` |
| `textToSpeech.verification.image.registry` | Repozytorium obrazów platformy docker, `helm test` używa do testowania **mowy na tekst** usługi. Polecenie Helm tworzy osobne zasobnika wewnątrz klastra na potrzeby testowania i ściąga *Użyj testu* obraz na podstawie tego rejestru. | `docker.io` |
| `textToSpeech.verification.image.repository` | Repozytorium obrazów platformy docker, `helm test` używa do testowania **mowy na tekst** usługi. Zasobnik testu Helm używa tego repozytorium do ściągania *Użyj testu* obrazu. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Tag obrazu platformy docker, które są używane z `helm test` dla **mowy na tekst** usługi. Helm test pod używa ten tag, aby ściągnąć *Użyj testu* obrazu. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Czy *Użyj testu* obrazu platformy docker zostanie ściągnięty przez wyznaczania wartości skrótu. Jeśli `true`, `textToSpeech.verification.image.hash` powinny zostać dodane z wartością skrótu prawidłowym obrazem. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty, które można wykonać za pomocą *Użyj testu* obrazu platformy docker. Zasobnik testu helm przekazuje te argumenty do kontenera podczas uruchamiania `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |