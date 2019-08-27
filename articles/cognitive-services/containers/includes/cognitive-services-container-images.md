---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051240"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele stanowią obszerną listę dostępnych obrazów kontenerów oferowanych przez usługę Azure Cognitive Services.

#### <a name="public-container-registry-mcrmicrosoftcom"></a>Public (rejestr kontenerów `mcr.microsoft.com`:)

W Container Registry firmy Microsoft są hostowane wszystkie kontenery ogólnego udostępnienia Cognitive Services.

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>Publiczna wersja zapoznawcza `containerpreview.azurecr.io`(rejestr kontenerów:)

Rejestr w wersji zapoznawczej kontenera zawiera wszystkie kontenery "publiczna wersja zapoznawcza" dla Cognitive Services, które nie były jeszcze ogólnie dostępne. Te kontenery wymagają formalnego żądania dostępu w celu ich użycia.

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Wykrywacz anomalii](../../anomaly-detector/anomaly-detector-container-howto.md) | Narzędzie do wykrywania anomalii | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Rozpoznawanie tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Twarzy](../../face/face-how-to-install-containers.md) | Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Aparat rozpoznawania formularzy](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md) | Zamiany mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md) | Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
