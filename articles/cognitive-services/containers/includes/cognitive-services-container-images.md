---
title: Repozytoria kontenerów i obrazy
services: cognitive-services
author: aahill
manager: nitinme
description: Dwie tabele reprezentujące rejestry kontenerów, repozytoria i nazwy obrazów dla wszystkich ofert usługi Cognitive Service.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876838"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele przedstawiają listę dostępnych obrazów kontenerów oferowanych przez usługi Azure Cognitive Services. Aby uzyskać pełną listę wszystkich dostępnych nazw obrazów kontenerów i ich dostępnych znaczników, zobacz [Znaczniki obrazów kontenerów usług Cognitive Services](../container-image-tags.md). Obecnie nie ma żadnych kontenerów usług Cognitive Services, które są ogólnie dostępne (GA). Na razie, do czasu ogłoszenia kolejnych - kontenery są dostępne jako *Publiczne Ungated* lub *Public Gated Preview*.

 - *Publiczne Ungated*: pojemniki są dostępne publicznie bez mechanizmu gating.
 - *Public Gated Preview*: kontenery są dostępne publicznie, ale najpierw wymagają formalnego żądania dostępu do rejestru kontenerów.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Publiczne "Ungated" (rejestr `mcr.microsoft.com`kontenerów: )

Microsoft Container Registry (MCR) syndykuje wszystkie publicznie dostępne kontenery "ungated" dla usług Cognitive Services. Kontenery są również dostępne bezpośrednio z [centrum platformy Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

| Usługa | Kontener | Rejestr kontenerów / repozytorium / nazwa obrazu |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Publiczny podgląd "Gated" (rejestr kontenerów: `containerpreview.azurecr.io`)

Rejestr podglądu kontenera obsługuje wszystkie publicznie dostępne kontenery "bramnie" dla usług Cognitive Services. Kontenery te wymagają formalnego żądania dostępu do nich za pośrednictwem rejestru kontenerów.

| Usługa | Kontener | Rejestr kontenerów / repozytorium / nazwa obrazu |
|--|--|--|
| [Wykrywacz anomalii](../../anomaly-detector/anomaly-detector-container-howto.md) | Narzędzie do wykrywania anomalii | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Odczyt | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Rozpoznawanie twarzy](../../face/face-how-to-install-containers.md) | Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Aparat rozpoznawania formularzy](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=stt) | Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=cstt) | Niestandardowa zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=tts) | Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ctts) | Niestandardowy tekst na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
