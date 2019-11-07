---
title: Repozytoria kontenerów i obrazy
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Dwie tabele przedstawiające rejestry kontenerów, repozytoria i nazwy obrazów dla wszystkich ofert usług poznawczych.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: c2e1707c6d757b2bf568f413ce2ab9f7a28ab6e4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604438"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele stanowią obszerną listę dostępnych obrazów kontenerów oferowanych przez usługę Azure Cognitive Services.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "ungated" (rejestr kontenerów: `mcr.microsoft.com`)

Firma Microsoft Container Registry obsługuje wszystkie publicznie dostępne kontenery "niebrama" dla Cognitive Services.

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
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Odczytywanie | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Stoi](../../face/face-how-to-install-containers.md) | Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Aparat rozpoznawania formularzy](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=stt) | Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech do tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=tts) | Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ctts) | Niestandardowa Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Tłumaczenie tekstu w usłudze Translator](../../translator/how-to-install-containers.md) | Tekst usługi Translator | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |

