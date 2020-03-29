---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcji konfiguracji wykresu steru parasola mowy.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523525"
---
### <a name="speech-umbrella-chart"></a>Mowa (wykres parasolowy)

Wartości na wykresie "parasol" najwyższego poziomu zastępują odpowiednie wartości wykresu podrzędnego. W związku z tym wszystkie wartości dostosowane lokalnie powinny być dodane w tym miejscu.

|Parametr|Opis|Domyślne|
| -- | -- | -- | -- |
| `speechToText.enabled` | Czy usługa **zamiany mowy na tekst** jest włączona. | `true` |
| `speechToText.verification.enabled` | Czy `helm test` funkcja usługi **zamiany mowy na tekst** jest włączona. | `true` |
| `speechToText.verification.image.registry` | Repozytorium obrazów platformy `helm test` docker, które służy do testowania usługi **zamiany mowy na tekst.** Helm tworzy oddzielne zasobników wewnątrz klastra do testowania i pobiera obraz *użycia testu* z tego rejestru. | `docker.io` |
| `speechToText.verification.image.repository` | Repozytorium obrazów platformy `helm test` docker, które służy do testowania usługi **zamiany mowy na tekst.** Czujnik testu helm używa tego repozytorium do ściągnięcia obrazu *użycia testu.* | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Znacznik obrazu docker `helm test` używany do usługi **zamiany mowy na tekst.** Czujnik testowy używa tego tagu do ściągania obrazu *używanego podczas testu.* | `latest` |
| `speechToText.verification.image.pullByHash` | Czy obraz dokowanego *do użycia testowego* jest ściągany przez skrót. Jeśli `true` `speechToText.verification.image.hash` , należy dodać, z prawidłową wartością skrótu obrazu. | `false` |
| `speechToText.verification.image.arguments` | Argumenty używane do wykonywania obrazu dokowane *do użycia testowego.* Czujnik testowy przechodzi te argumenty `helm test`do kontenera podczas uruchamiania . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Czy usługa **zamiany tekstu na mowę** jest włączona. | `true` |
| `textToSpeech.verification.enabled` | Czy `helm test` funkcja usługi **zamiany mowy na tekst** jest włączona. | `true` |
| `textToSpeech.verification.image.registry` | Repozytorium obrazów platformy `helm test` docker, które służy do testowania usługi **zamiany mowy na tekst.** Helm tworzy oddzielne zasobników wewnątrz klastra do testowania i pobiera obraz *użycia testu* z tego rejestru. | `docker.io` |
| `textToSpeech.verification.image.repository` | Repozytorium obrazów platformy `helm test` docker, które służy do testowania usługi **zamiany mowy na tekst.** Czujnik testu helm używa tego repozytorium do ściągnięcia obrazu *użycia testu.* | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Znacznik obrazu docker `helm test` używany do usługi **zamiany mowy na tekst.** Czujnik testowy używa tego tagu do ściągania obrazu *używanego podczas testu.* | `latest` |
| `textToSpeech.verification.image.pullByHash` | Czy obraz dokowanego *do użycia testowego* jest ściągany przez skrót. Jeśli `true` `textToSpeech.verification.image.hash` , należy dodać, z prawidłową wartością skrótu obrazu. | `false` |
| `textToSpeech.verification.image.arguments` | Argumenty do wykonania z obrazem dokowane *do użycia testowego.* Czujnik testu helm przekazuje te argumenty `helm test`do kontenera podczas uruchamiania . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |