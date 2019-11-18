---
title: Repozytoria kontenerów i obrazy
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Dwie tabele przedstawiające rejestry kontenerów, repozytoria i nazwy obrazów dla wszystkich ofert usług poznawczych.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142211"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele stanowią listę dostępnych obrazów kontenerów oferowanych przez usługę Azure Cognitive Services. Aby uzyskać pełną listę wszystkich dostępnych nazw obrazów kontenerów i ich dostępnych tagów, zobacz [Cognitive Services znacznika obrazu kontenera](../container-image-tags.md).

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "ungated" (rejestr kontenerów: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) łączy wszystkie publicznie dostępne kontenery "niebrama" dla Cognitive Services. Są one również dostępne bezpośrednio w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Public "Gated" (wersja zapoznawcza) (rejestr kontenerów: `containerpreview.azurecr.io`)

Rejestr w wersji zapoznawczej kontenera zawiera wszystkie publicznie dostępne kontenery "Gated" Cognitive Services. Te kontenery wymagają formalnego żądania dostępu w celu ich użycia.

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Narzędzie do wykrywania anomalii](../../anomaly-detector/anomaly-detector-container-howto.md) | Narzędzie do wykrywania anomalii | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Odczyt | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Twarzy](../../face/face-how-to-install-containers.md) | Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Aparat rozpoznawania formularzy](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=stt) | Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech do tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=tts) | Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ctts) | Niestandardowa Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
