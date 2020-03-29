---
title: Tagi obrazu kontenera usług Cognitive Services
titleSuffix: Azure Cognitive Services
description: Kompleksowa lista wszystkich tagów obrazów kontenera usługi Cognitive Service.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219446"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Znaczniki obrazów kontenerów usług Azure Cognitive Services

Usługa Azure Cognitive Services oferuje wiele obrazów kontenerów. Rejestry kontenerów i odpowiednie repozytoria różnią się między obrazami kontenerów. Każda nazwa obrazu kontenera oferuje wiele tagów. Znacznik obrazu kontenera jest mechanizmem przechowywania wersji obrazu kontenera. Ten artykuł jest przeznaczony do użycia jako wyczerpujące odniesienie do listy wszystkich obrazów kontenerów usług Cognitive Services i ich dostępnych tagów.

> [!TIP]
> Podczas [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)korzystania z programu należy zwrócić szczególną uwagę na wielkość liter rejestru kontenerów, repozytorium, nazwę obrazu kontenera i odpowiadający im znacznik — tak jak **wielkość liter.**

## <a name="anomaly-detector"></a>Narzędzie do wykrywania anomalii

Obraz [kontenera detektora anomalii][ad-containers] można znaleźć w rejestrze kontenerów. `containerpreview.azurecr.io` Znajduje się w `microsoft` repozytorium i `cognitive-services-anomaly-detector`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Przetwarzanie obrazów

Obraz kontenera [przetwarzania można][cv-containers] znaleźć `containerpreview.azurecr.io` w rejestrze kontenerów. Znajduje się w `microsoft` repozytorium i `cognitive-services-read`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-read`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Rozpoznawanie twarzy

Obraz kontenera [twarzy][fa-containers] można `containerpreview.azurecr.io` znaleźć w rejestrze kontenerów. Znajduje się w `microsoft` repozytorium i `cognitive-services-face`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-face`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Rozpoznawanie formularzy

Obraz kontenera [rozpoznawania formularzy][fr-containers] można `containerpreview.azurecr.io` znaleźć w rejestrze kontenerów. Znajduje się w `microsoft` repozytorium i `cognitive-services-form-recognizer`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

Obraz kontenera [usługi LUIS][lu-containers] `mcr.microsoft.com` można znaleźć w syndykacie rejestru kontenerów. Znajduje się w `azure-cognitive-services` repozytorium i `luis`nosi nazwę . W pełni kwalifikowana nazwa `mcr.microsoft.com/azure-cognitive-services/luis`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Niestandardowa zamiana mowy na tekst

Obraz kontenera [Zamiana mowy na tekst][sp-cstt] `containerpreview.azurecr.io` można znaleźć w rejestrze kontenerów. Znajduje się w `microsoft` repozytorium i `cognitive-services-custom-speech-to-text`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów            | Uwagi |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Niestandardowy tekst na mowę

Niestandardowy obraz kontenera [zamiany tekstu][sp-ctts] na `containerpreview.azurecr.io` mowę można znaleźć w rejestrze kontenerów. Znajduje się w `microsoft` repozytorium i `cognitive-services-custom-text-to-speech`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów            | Uwagi |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Obraz kontenera [zamiany mowy na][sp-stt] tekst `containerpreview.azurecr.io` można znaleźć w rejestrze kontenerów. Znajduje się w `microsoft` repozytorium i `cognitive-services-speech-to-text`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                  | Uwagi                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ar-ae-preview` | Obraz kontenera `ar-AE` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ar-eg-preview` | Obraz kontenera `ar-EG` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ar-kw-preview` | Obraz kontenera `ar-KW` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ar-qa-preview` | Obraz kontenera `ar-QA` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ar-sa-preview` | Obraz kontenera `ar-SA` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ca-es-preview` | Obraz kontenera `ca-ES` z ustawieniami regionalnymi. |
| `2.1.1-amd64-da-dk-preview` | Obraz kontenera `da-DK` z ustawieniami regionalnymi. |
| `2.1.1-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `2.1.1-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `2.1.1-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `2.1.1-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `2.1.1-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-en-nz-preview` | Obraz kontenera `en-NZ` z ustawieniami regionalnymi. |
| `2.1.1-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `2.1.1-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `2.1.1-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `2.1.1-amd64-fi-fi-preview` | Obraz kontenera `fi-FI` z ustawieniami regionalnymi. |
| `2.1.1-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `2.1.1-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `2.1.1-amd64-gu-in-preview` | Obraz kontenera `gu-IN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-hi-in-preview` | Obraz kontenera `hi-IN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ko-kr-preview` | Obraz kontenera `ko-KR` z ustawieniami regionalnymi. |
| `2.1.1-amd64-mr-in-preview` | Obraz kontenera `mr-IN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-nb-no-preview` | Obraz kontenera `nb-NO` z ustawieniami regionalnymi. |
| `2.1.1-amd64-nl-nl-preview` | Obraz kontenera `nl-NL` z ustawieniami regionalnymi. |
| `2.1.1-amd64-pl-pl-preview` | Obraz kontenera `pl-PL` z ustawieniami regionalnymi. |
| `2.1.1-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `2.1.1-amd64-pt-pt-preview` | Obraz kontenera `pt-PT` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ru-ru-preview` | Obraz kontenera `ru-RU` z ustawieniami regionalnymi. |
| `2.1.1-amd64-sv-se-preview` | Obraz kontenera `sv-SE` z ustawieniami regionalnymi. |
| `2.1.1-amd64-ta-in-preview` | Obraz kontenera `ta-IN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-te-in-preview` | Obraz kontenera `te-IN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-th-th-preview` | Obraz kontenera `th-TH` z ustawieniami regionalnymi. |
| `2.1.1-amd64-tr-tr-preview` | Obraz kontenera `tr-TR` z ustawieniami regionalnymi. |
| `2.1.1-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `2.1.1-amd64-zh-hk-preview` | Obraz kontenera `zh-HK` z ustawieniami regionalnymi. |
| `2.1.1-amd64-zh-tw-preview` | Obraz kontenera `zh-TW` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ar-ae-preview` | Obraz kontenera `ar-AE` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ar-eg-preview` | Obraz kontenera `ar-EG` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ar-kw-preview` | Obraz kontenera `ar-KW` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ar-qa-preview` | Obraz kontenera `ar-QA` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ar-sa-preview` | Obraz kontenera `ar-SA` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ca-es-preview` | Obraz kontenera `ca-ES` z ustawieniami regionalnymi. |
| `2.1.0-amd64-da-dk-preview` | Obraz kontenera `da-DK` z ustawieniami regionalnymi. |
| `2.1.0-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `2.1.0-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `2.1.0-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `2.1.0-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `2.1.0-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-en-nz-preview` | Obraz kontenera `en-NZ` z ustawieniami regionalnymi. |
| `2.1.0-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `2.1.0-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `2.1.0-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `2.1.0-amd64-fi-fi-preview` | Obraz kontenera `fi-FI` z ustawieniami regionalnymi. |
| `2.1.0-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `2.1.0-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `2.1.0-amd64-gu-in-preview` | Obraz kontenera `gu-IN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-hi-in-preview` | Obraz kontenera `hi-IN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ko-kr-preview` | Obraz kontenera `ko-KR` z ustawieniami regionalnymi. |
| `2.1.0-amd64-mr-in-preview` | Obraz kontenera `mr-IN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-nb-no-preview` | Obraz kontenera `nb-NO` z ustawieniami regionalnymi. |
| `2.1.0-amd64-nl-nl-preview` | Obraz kontenera `nl-NL` z ustawieniami regionalnymi. |
| `2.1.0-amd64-pl-pl-preview` | Obraz kontenera `pl-PL` z ustawieniami regionalnymi. |
| `2.1.0-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `2.1.0-amd64-pt-pt-preview` | Obraz kontenera `pt-PT` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ru-ru-preview` | Obraz kontenera `ru-RU` z ustawieniami regionalnymi. |
| `2.1.0-amd64-sv-se-preview` | Obraz kontenera `sv-SE` z ustawieniami regionalnymi. |
| `2.1.0-amd64-ta-in-preview` | Obraz kontenera `ta-IN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-te-in-preview` | Obraz kontenera `te-IN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-th-th-preview` | Obraz kontenera `th-TH` z ustawieniami regionalnymi. |
| `2.1.0-amd64-tr-tr-preview` | Obraz kontenera `tr-TR` z ustawieniami regionalnymi. |
| `2.1.0-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `2.1.0-amd64-zh-hk-preview` | Obraz kontenera `zh-HK` z ustawieniami regionalnymi. |
| `2.1.0-amd64-zh-tw-preview` | Obraz kontenera `zh-TW` z ustawieniami regionalnymi. |
| `2.0.3-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ar-ae-preview` | Obraz kontenera `ar-AE` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ar-eg-preview` | Obraz kontenera `ar-EG` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ar-kw-preview` | Obraz kontenera `ar-KW` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ar-qa-preview` | Obraz kontenera `ar-QA` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ar-sa-preview` | Obraz kontenera `ar-SA` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ca-es-preview` | Obraz kontenera `ca-ES` z ustawieniami regionalnymi. |
| `2.0.2-amd64-da-dk-preview` | Obraz kontenera `da-DK` z ustawieniami regionalnymi. |
| `2.0.2-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `2.0.2-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `2.0.2-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `2.0.2-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `2.0.2-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-en-nz-preview` | Obraz kontenera `en-NZ` z ustawieniami regionalnymi. |
| `2.0.2-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `2.0.2-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `2.0.2-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `2.0.2-amd64-fi-fi-preview` | Obraz kontenera `fi-FI` z ustawieniami regionalnymi. |
| `2.0.2-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `2.0.2-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `2.0.2-amd64-gu-in-preview` | Obraz kontenera `gu-IN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-hi-in-preview` | Obraz kontenera `hi-IN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ko-kr-preview` | Obraz kontenera `ko-KR` z ustawieniami regionalnymi. |
| `2.0.2-amd64-mr-in-preview` | Obraz kontenera `mr-IN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-nb-no-preview` | Obraz kontenera `nb-NO` z ustawieniami regionalnymi. |
| `2.0.2-amd64-nl-nl-preview` | Obraz kontenera `nl-NL` z ustawieniami regionalnymi. |
| `2.0.2-amd64-pl-pl-preview` | Obraz kontenera `pl-PL` z ustawieniami regionalnymi. |
| `2.0.2-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `2.0.2-amd64-pt-pt-preview` | Obraz kontenera `pt-PT` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ru-ru-preview` | Obraz kontenera `ru-RU` z ustawieniami regionalnymi. |
| `2.0.2-amd64-sv-se-preview` | Obraz kontenera `sv-SE` z ustawieniami regionalnymi. |
| `2.0.2-amd64-ta-in-preview` | Obraz kontenera `ta-IN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-te-in-preview` | Obraz kontenera `te-IN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-th-th-preview` | Obraz kontenera `th-TH` z ustawieniami regionalnymi. |
| `2.0.2-amd64-tr-tr-preview` | Obraz kontenera `tr-TR` z ustawieniami regionalnymi. |
| `2.0.2-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `2.0.2-amd64-zh-hk-preview` | Obraz kontenera `zh-HK` z ustawieniami regionalnymi. |
| `2.0.2-amd64-zh-tw-preview` | Obraz kontenera `zh-TW` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ar-ae-preview` | Obraz kontenera `ar-AE` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ar-eg-preview` | Obraz kontenera `ar-EG` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ar-kw-preview` | Obraz kontenera `ar-KW` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ar-qa-preview` | Obraz kontenera `ar-QA` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ar-sa-preview` | Obraz kontenera `ar-SA` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ca-es-preview` | Obraz kontenera `ca-ES` z ustawieniami regionalnymi. |
| `2.0.1-amd64-da-dk-preview` | Obraz kontenera `da-DK` z ustawieniami regionalnymi. |
| `2.0.1-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `2.0.1-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `2.0.1-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `2.0.1-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `2.0.1-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-en-nz-preview` | Obraz kontenera `en-NZ` z ustawieniami regionalnymi. |
| `2.0.1-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `2.0.1-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `2.0.1-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `2.0.1-amd64-fi-fi-preview` | Obraz kontenera `fi-FI` z ustawieniami regionalnymi. |
| `2.0.1-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `2.0.1-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `2.0.1-amd64-gu-in-preview` | Obraz kontenera `gu-IN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-hi-in-preview` | Obraz kontenera `hi-IN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ko-kr-preview` | Obraz kontenera `ko-KR` z ustawieniami regionalnymi. |
| `2.0.1-amd64-mr-in-preview` | Obraz kontenera `mr-IN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-nb-no-preview` | Obraz kontenera `nb-NO` z ustawieniami regionalnymi. |
| `2.0.1-amd64-nl-nl-preview` | Obraz kontenera `nl-NL` z ustawieniami regionalnymi. |
| `2.0.1-amd64-pl-pl-preview` | Obraz kontenera `pl-PL` z ustawieniami regionalnymi. |
| `2.0.1-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `2.0.1-amd64-pt-pt-preview` | Obraz kontenera `pt-PT` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ru-ru-preview` | Obraz kontenera `ru-RU` z ustawieniami regionalnymi. |
| `2.0.1-amd64-sv-se-preview` | Obraz kontenera `sv-SE` z ustawieniami regionalnymi. |
| `2.0.1-amd64-ta-in-preview` | Obraz kontenera `ta-IN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-te-in-preview` | Obraz kontenera `te-IN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-th-th-preview` | Obraz kontenera `th-TH` z ustawieniami regionalnymi. |
| `2.0.1-amd64-tr-tr-preview` | Obraz kontenera `tr-TR` z ustawieniami regionalnymi. |
| `2.0.1-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `2.0.1-amd64-zh-hk-preview` | Obraz kontenera `zh-HK` z ustawieniami regionalnymi. |
| `2.0.1-amd64-zh-tw-preview` | Obraz kontenera `zh-TW` z ustawieniami regionalnymi. |
| `2.0.0-amd64-ar-eg-preview` | Obraz kontenera `ar-EG` z ustawieniami regionalnymi. |
| `2.0.0-amd64-ca-es-preview` | Obraz kontenera `ca-ES` z ustawieniami regionalnymi. |
| `2.0.0-amd64-da-dk-preview` | Obraz kontenera `da-DK` z ustawieniami regionalnymi. |
| `2.0.0-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `2.0.0-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `2.0.0-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `2.0.0-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `2.0.0-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `2.0.0-amd64-en-nz-preview` | Obraz kontenera `en-NZ` z ustawieniami regionalnymi. |
| `2.0.0-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `2.0.0-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `2.0.0-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `2.0.0-amd64-fi-fi-preview` | Obraz kontenera `fi-FI` z ustawieniami regionalnymi. |
| `2.0.0-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `2.0.0-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `2.0.0-amd64-hi-in-preview` | Obraz kontenera `hi-IN` z ustawieniami regionalnymi. |
| `2.0.0-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `2.0.0-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `2.0.0-amd64-ko-kr-preview` | Obraz kontenera `ko-KR` z ustawieniami regionalnymi. |
| `2.0.0-amd64-nb-no-preview` | Obraz kontenera `nb-NO` z ustawieniami regionalnymi. |
| `2.0.0-amd64-nl-nl-preview` | Obraz kontenera `nl-NL` z ustawieniami regionalnymi. |
| `2.0.0-amd64-pl-pl-preview` | Obraz kontenera `pl-PL` z ustawieniami regionalnymi. |
| `2.0.0-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `2.0.0-amd64-pt-pt-preview` | Obraz kontenera `pt-PT` z ustawieniami regionalnymi. |
| `2.0.0-amd64-ru-ru-preview` | Obraz kontenera `ru-RU` z ustawieniami regionalnymi. |
| `2.0.0-amd64-sv-se-preview` | Obraz kontenera `sv-SE` z ustawieniami regionalnymi. |
| `2.0.0-amd64-th-th-preview` | Obraz kontenera `th-TH` z ustawieniami regionalnymi. |
| `2.0.0-amd64-tr-tr-preview` | Obraz kontenera `tr-TR` z ustawieniami regionalnymi. |
| `2.0.0-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `2.0.0-amd64-zh-hk-preview` | Obraz kontenera `zh-HK` z ustawieniami regionalnymi. |
| `2.0.0-amd64-zh-tw-preview` | Obraz kontenera `zh-TW` z ustawieniami regionalnymi. |
| `1.2.0-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `1.2.0-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `1.2.0-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `1.2.0-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `1.2.0-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `1.2.0-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `1.2.0-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `1.2.0-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `1.2.0-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `1.2.0-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `1.2.0-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `1.2.0-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `1.2.0-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `1.2.0-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `1.1.3-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `1.1.3-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `1.1.3-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `1.1.3-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `1.1.3-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `1.1.3-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `1.1.3-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `1.1.3-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `1.1.3-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `1.1.3-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `1.1.3-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `1.1.3-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `1.1.3-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `1.1.3-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `1.1.2-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `1.1.2-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `1.1.2-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `1.1.2-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `1.1.2-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `1.1.2-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `1.1.2-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `1.1.2-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `1.1.2-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `1.1.2-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `1.1.2-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `1.1.2-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `1.1.2-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `1.1.2-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `1.1.1-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `1.1.1-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `1.1.1-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `1.1.1-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `1.1.1-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `1.1.1-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `1.1.1-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `1.1.1-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `1.1.1-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `1.1.1-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `1.1.1-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `1.1.1-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `1.1.1-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `1.1.1-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `1.1.0-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `1.1.0-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `1.1.0-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `1.1.0-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `1.1.0-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `1.1.0-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `1.1.0-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `1.1.0-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `1.1.0-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `1.1.0-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `1.1.0-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `1.1.0-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `1.1.0-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `1.1.0-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |
| `1.0.0-amd64-de-de-preview` | Obraz kontenera `de-DE` z ustawieniami regionalnymi. |
| `1.0.0-amd64-en-au-preview` | Obraz kontenera `en-AU` z ustawieniami regionalnymi. |
| `1.0.0-amd64-en-ca-preview` | Obraz kontenera `en-CA` z ustawieniami regionalnymi. |
| `1.0.0-amd64-en-gb-preview` | Obraz kontenera `en-GB` z ustawieniami regionalnymi. |
| `1.0.0-amd64-en-in-preview` | Obraz kontenera `en-IN` z ustawieniami regionalnymi. |
| `1.0.0-amd64-en-us-preview` | Obraz kontenera `en-US` z ustawieniami regionalnymi. |
| `1.0.0-amd64-es-es-preview` | Obraz kontenera `es-ES` z ustawieniami regionalnymi. |
| `1.0.0-amd64-es-mx-preview` | Obraz kontenera `es-MX` z ustawieniami regionalnymi. |
| `1.0.0-amd64-fr-ca-preview` | Obraz kontenera `fr-CA` z ustawieniami regionalnymi. |
| `1.0.0-amd64-fr-fr-preview` | Obraz kontenera `fr-FR` z ustawieniami regionalnymi. |
| `1.0.0-amd64-it-it-preview` | Obraz kontenera `it-IT` z ustawieniami regionalnymi. |
| `1.0.0-amd64-ja-jp-preview` | Obraz kontenera `ja-JP` z ustawieniami regionalnymi. |
| `1.0.0-amd64-pt-br-preview` | Obraz kontenera `pt-BR` z ustawieniami regionalnymi. |
| `1.0.0-amd64-zh-cn-preview` | Obraz kontenera `zh-CN` z ustawieniami regionalnymi. |

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Obraz kontenera [zamiany tekstu][sp-tts] na mowę można znaleźć w rejestrze kontenerów. `containerpreview.azurecr.io` Znajduje się w `microsoft` repozytorium i `cognitive-services-text-to-speech`nosi nazwę . W pełni kwalifikowana nazwa `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                                  | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obraz kontenera `en-US` z `en-US-JessaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Obraz kontenera `ar-EG` z `ar-EG-Hoda` ustawieniami regionalnymi i głosem.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Obraz kontenera `ar-SA` z `ar-SA-Naayf` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Obraz kontenera `bg-BG` z `bg-BG-Ivan` ustawieniami regionalnymi i głosem.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Obraz kontenera `ca-ES` z `ca-ES-HerenaRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Obraz kontenera `cs-CZ` z `cs-CZ-Jakub` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Obraz kontenera `da-DK` z `da-DK-HelleRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-de-at-michael-preview`         | Obraz kontenera `de-AT` z `de-AT-Michael` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Obraz kontenera `de-CH` z `de-CH-Karsten` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Obraz kontenera `de-DE` z `de-DE-Hedda` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obraz kontenera `de-DE` z `de-DE-Hedda` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obraz kontenera `de-DE` z `de-DE-HeddaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Obraz kontenera `de-DE` z `de-DE-Stefan-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Obraz kontenera `el-GR` z `el-GR-Stefanos` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Obraz kontenera `en-AU` z `en-AU-Catherine` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Obraz kontenera `en-AU` z `en-AU-HayleyRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Obraz kontenera `en-CA` z `en-CA-HeatherRUS` ustawieniami regionalnymi i głosem.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Obraz kontenera `en-CA` z `en-CA-Linda` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Obraz kontenera `en-GB` z `en-GB-George-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Obraz kontenera `en-GB` z `en-GB-HazelRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Obraz kontenera `en-GB` z `en-GB-Susan-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Obraz kontenera `en-IE` z `en-IE-Sean` ustawieniami regionalnymi i głosem.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Obraz kontenera `en-IN` z `en-IN-Heera-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Obraz kontenera `en-IN` z `en-IN-PriyaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Obraz kontenera `en-IN` z `en-IN-Ravi-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Obraz kontenera `en-US` z `en-US-BenjaminRUS` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Obraz kontenera `en-US` z `en-US-Guy24kRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Obraz kontenera `en-US` z `en-US-Jessa24kRUS` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Obraz kontenera `en-US` z `en-US-JessaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Obraz kontenera `en-US` z `en-US-ZiraRUS` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Obraz kontenera `es-ES` z `es-ES-HelenaRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Obraz kontenera `es-ES` z `es-ES-Laura-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Obraz kontenera `es-ES` z `es-ES-Pablo-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Obraz kontenera `es-MX` z `es-MX-HildaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Obraz kontenera `es-MX` z `es-MX-Raul-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Obraz kontenera `fi-FI` z `fi-FI-HeidiRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Obraz kontenera `fr-CA` z `fr-CA-Caroline` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Obraz kontenera `fr-CA` z `fr-CA-HarmonieRUS` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Obraz kontenera `fr-CH` z `fr-CH-Guillaume` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Obraz kontenera `fr-FR` z `fr-FR-HortenseRUS` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Obraz kontenera `fr-FR` z `fr-FR-Julie-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Obraz kontenera `fr-FR` z `fr-FR-Paul-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Obraz kontenera `he-IL` z `he-IL-Asaf` ustawieniami regionalnymi i głosem.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Obraz kontenera `hi-IN` z `hi-IN-Hemant` ustawieniami regionalnymi i głosem.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obraz kontenera `hi-IN` z `hi-IN-Kalpana-Apollo` ustawieniami regionalnymi i głosem.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obraz kontenera `hi-IN` z `hi-IN-Kalpana` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Obraz kontenera `hi-IN` z `hi-IN-Kalpana` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Obraz kontenera `hr-HR` z `hr-HR-Matej` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Obraz kontenera `hu-HU` z `hu-HU-Szabolcs` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-id-id-andika-preview`          | Obraz kontenera `id-ID` z `id-ID-Andika` ustawieniami regionalnymi i głosem.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Obraz kontenera `it-IT` z `it-IT-Cosimo-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Obraz kontenera `it-IT` z `it-IT-LuciaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Obraz kontenera `ja-JP` z `ja-JP-Ayumi-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Obraz kontenera `ja-JP` z `ja-JP-HarukaRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Obraz kontenera `ja-JP` z `ja-JP-Ichiro-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Obraz kontenera `ko-KR` z `ko-KR-HeamiRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Obraz kontenera `ms-MY` z `ms-MY-Rizwan` ustawieniami regionalnymi i głosem.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Obraz kontenera `nb-NO` z `nb-NO-HuldaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Obraz kontenera `nl-NL` z `nl-NL-HannaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Obraz kontenera `pl-PL` z `pl-PL-PaulinaRUS` ustawieniami regionalnymi i głosem.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Obraz kontenera `pt-BR` z `pt-BR-Daniel-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Obraz kontenera `pt-BR` z `pt-BR-HeloisaRUS` ustawieniami regionalnymi i głosem.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Obraz kontenera `pt-PT` z `pt-PT-HeliaRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Obraz kontenera `ro-RO` z `ro-RO-Andrei` ustawieniami regionalnymi i głosem.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Obraz kontenera `ru-RU` z `ru-RU-EkaterinaRUS` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Obraz kontenera `ru-RU` z `ru-RU-Irina-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Obraz kontenera `ru-RU` z `ru-RU-Pavel-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Obraz kontenera `sk-SK` z `sk-SK-Filip` ustawieniami regionalnymi i głosem.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Obraz kontenera `sl-SI` z `sl-SI-Lado` ustawieniami regionalnymi i głosem.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Obraz kontenera `sv-SE` z `sv-SE-HedvigRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Obraz kontenera `ta-IN` z `ta-IN-Valluvar` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Obraz kontenera `te-IN` z `te-IN-Chitra` ustawieniami regionalnymi i głosem.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Obraz kontenera `th-TH` z `th-TH-Pattara` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Obraz kontenera `tr-TR` z `tr-TR-SedaRUS` ustawieniami regionalnymi i głosem.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Obraz kontenera `vi-VN` z `vi-VN-An` ustawieniami regionalnymi i głosem.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Obraz kontenera `zh-CN` z `zh-CN-HuihuiRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Obraz kontenera `zh-CN` z `zh-CN-Kangkang-Apollo` ustawieniami regionalnymi i głosem. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obraz kontenera `zh-CN` z `zh-CN-Yaoyao-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Obraz kontenera `zh-HK` z `zh-HK-Danny-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Obraz kontenera `zh-HK` z `zh-HK-Tracy-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Obraz kontenera `zh-HK` z `zh-HK-TracyRUS` ustawieniami regionalnymi i głosem.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Obraz kontenera `zh-TW` z `zh-TW-HanHanRUS` ustawieniami regionalnymi i głosem.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Obraz kontenera `zh-TW` z `zh-TW-Yating-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Obraz kontenera `zh-TW` z `zh-TW-Zhiwei-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obraz kontenera `de-DE` z `de-DE-Hedda` ustawieniami regionalnymi i głosem.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obraz kontenera `de-DE` z `de-DE-HeddaRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Obraz kontenera `de-DE` z `de-DE-Stefan-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Obraz kontenera `en-AU` z `en-AU-Catherine` ustawieniami regionalnymi i głosem.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Obraz kontenera `en-AU` z `en-AU-HayleyRUS` ustawieniami regionalnymi i głosem.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Obraz kontenera `en-GB` z `en-GB-George-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Obraz kontenera `en-GB` z `en-GB-HazelRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Obraz kontenera `en-GB` z `en-GB-Susan-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Obraz kontenera `en-IN` z `en-IN-Heera-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Obraz kontenera `en-IN` z `en-IN-PriyaRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Obraz kontenera `en-IN` z `en-IN-Ravi-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Obraz kontenera `en-US` z `en-US-BenjaminRUS` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Obraz kontenera `en-US` z `en-US-Guy24kRUS` ustawieniami regionalnymi i głosem.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Obraz kontenera `en-US` z `en-US-Jessa24kRUS` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Obraz kontenera `en-US` z `en-US-JessaRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Obraz kontenera `en-US` z `en-US-ZiraRUS` ustawieniami regionalnymi i głosem.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Obraz kontenera `es-ES` z `es-ES-HelenaRUS` ustawieniami regionalnymi i głosem.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Obraz kontenera `es-ES` z `es-ES-Laura-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Obraz kontenera `es-ES` z `es-ES-Pablo-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Obraz kontenera `es-MX` z `es-MX-HildaRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Obraz kontenera `es-MX` z `es-MX-Raul-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Obraz kontenera `fr-CA` z `fr-CA-Caroline` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Obraz kontenera `fr-CA` z `fr-CA-HarmonieRUS` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Obraz kontenera `fr-FR` z `fr-FR-HortenseRUS` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Obraz kontenera `fr-FR` z `fr-FR-Julie-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Obraz kontenera `fr-FR` z `fr-FR-Paul-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Obraz kontenera `it-IT` z `it-IT-Cosimo-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Obraz kontenera `it-IT` z `it-IT-LuciaRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Obraz kontenera `ja-JP` z `ja-JP-Ayumi-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Obraz kontenera `ja-JP` z `ja-JP-HarukaRUS` ustawieniami regionalnymi i głosem.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Obraz kontenera `ja-JP` z `ja-JP-Ichiro-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Obraz kontenera `ko-KR` z `ko-KR-HeamiRUS` ustawieniami regionalnymi i głosem.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Obraz kontenera `pt-BR` z `pt-BR-Daniel-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Obraz kontenera `pt-BR` z `pt-BR-HeloisaRUS` ustawieniami regionalnymi i głosem.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Obraz kontenera `zh-CN` z `zh-CN-HuihuiRUS` ustawieniami regionalnymi i głosem.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Obraz kontenera `zh-CN` z `zh-CN-Kangkang-Apollo` ustawieniami regionalnymi i głosem. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obraz kontenera `zh-CN` z `zh-CN-Yaoyao-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Obraz kontenera `de-DE` z `de-DE-Hedda` ustawieniami regionalnymi i głosem.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obraz kontenera `de-DE` z `de-DE-Hedda` ustawieniami regionalnymi i głosem.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obraz kontenera `de-DE` z `de-DE-HeddaRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Obraz kontenera `de-DE` z `de-DE-Stefan-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Obraz kontenera `en-AU` z `en-AU-Catherine` ustawieniami regionalnymi i głosem.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Obraz kontenera `en-AU` z `en-AU-HayleyRUS` ustawieniami regionalnymi i głosem.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Obraz kontenera `en-GB` z `en-GB-George-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Obraz kontenera `en-GB` z `en-GB-HazelRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Obraz kontenera `en-GB` z `en-GB-Susan-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Obraz kontenera `en-IN` z `en-IN-Heera-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Obraz kontenera `en-IN` z `en-IN-PriyaRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Obraz kontenera `en-IN` z `en-IN-Ravi-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Obraz kontenera `en-US` z `en-US-BenjaminRUS` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Obraz kontenera `en-US` z `en-US-Guy24kRUS` ustawieniami regionalnymi i głosem.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Obraz kontenera `en-US` z `en-US-Jessa24kRUS` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Obraz kontenera `en-US` z `en-US-JessaRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Obraz kontenera `en-US` z `en-US-ZiraRUS` ustawieniami regionalnymi i głosem.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Obraz kontenera `es-ES` z `es-ES-HelenaRUS` ustawieniami regionalnymi i głosem.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Obraz kontenera `es-ES` z `es-ES-Laura-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Obraz kontenera `es-ES` z `es-ES-Pablo-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Obraz kontenera `es-MX` z `es-MX-HildaRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Obraz kontenera `es-MX` z `es-MX-Raul-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Obraz kontenera `fr-CA` z `fr-CA-Caroline` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Obraz kontenera `fr-CA` z `fr-CA-HarmonieRUS` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Obraz kontenera `fr-FR` z `fr-FR-HortenseRUS` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Obraz kontenera `fr-FR` z `fr-FR-Julie-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Obraz kontenera `fr-FR` z `fr-FR-Paul-Apollo` ustawieniami regionalnymi i głosem.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Obraz kontenera `it-IT` z `it-IT-Cosimo-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Obraz kontenera `it-IT` z `it-IT-LuciaRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Obraz kontenera `ja-JP` z `ja-JP-Ayumi-Apollo` ustawieniami regionalnymi i głosem.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Obraz kontenera `ja-JP` z `ja-JP-HarukaRUS` ustawieniami regionalnymi i głosem.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Obraz kontenera `ja-JP` z `ja-JP-Ichiro-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Obraz kontenera `ko-KR` z `ko-KR-HeamiRUS` ustawieniami regionalnymi i głosem.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Obraz kontenera `pt-BR` z `pt-BR-Daniel-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Obraz kontenera `pt-BR` z `pt-BR-HeloisaRUS` ustawieniami regionalnymi i głosem.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Obraz kontenera `zh-CN` z `zh-CN-HuihuiRUS` ustawieniami regionalnymi i głosem.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Obraz kontenera `zh-CN` z `zh-CN-Kangkang-Apollo` ustawieniami regionalnymi i głosem. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obraz kontenera `zh-CN` z `zh-CN-Yaoyao-Apollo` ustawieniami regionalnymi i głosem.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Obraz kontenera `en-US` z `en-US-BenjaminRUS` ustawieniami regionalnymi i głosem.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Obraz kontenera `en-US` z `en-US-Guy24kRUS` ustawieniami regionalnymi i głosem.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Obraz kontenera `en-US` z `en-US-Jessa24kRUS` ustawieniami regionalnymi i głosem.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Obraz kontenera `en-US` z `en-US-JessaRUS` ustawieniami regionalnymi i głosem.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Obraz kontenera `en-US` z `en-US-ZiraRUS` ustawieniami regionalnymi i głosem.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Obraz kontenera `zh-CN` z `zh-CN-HuihuiRUS` ustawieniami regionalnymi i głosem.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Obraz kontenera `zh-CN` z `zh-CN-Kangkang-Apollo` ustawieniami regionalnymi i głosem. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obraz kontenera `zh-CN` z `zh-CN-Yaoyao-Apollo` ustawieniami regionalnymi i głosem.   |

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Obraz kontenera [wyodrębniania frazy kluczowej][ta-kp] można znaleźć w syndykacie `mcr.microsoft.com` rejestru kontenerów. Znajduje się w `azure-cognitive-services` repozytorium i `keyphrase`nosi nazwę . W pełni kwalifikowana nazwa `mcr.microsoft.com/azure-cognitive-services/keyphrase`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Wykrywanie języka

Obraz [kontenera wykrywania języka][ta-la] `mcr.microsoft.com` można znaleźć w syndykacie rejestru kontenerów. Znajduje się w `azure-cognitive-services` repozytorium i `language`nosi nazwę . W pełni kwalifikowana nazwa `mcr.microsoft.com/azure-cognitive-services/language`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analiza tonacji

Obraz kontenera [analizy tonacji][ta-se] `mcr.microsoft.com` można znaleźć w syndykacie rejestru kontenerów. Znajduje się w `azure-cognitive-services` repozytorium i `sentiment`nosi nazwę . W pełni kwalifikowana nazwa `mcr.microsoft.com/azure-cognitive-services/sentiment`obrazu kontenera to.

Ten obraz kontenera ma dostępne następujące znaczniki:

| Znaczniki obrazów                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
