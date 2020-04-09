---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcji konfiguracji wykresu steru parasola mowy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874357"
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