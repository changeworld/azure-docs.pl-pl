---
title: Uruchamianie wystąpień kontenera platformy Azure — usługa mowy
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera usługi mowy w wystąpieniu kontenera platformy Azure i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717410"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Wdrażanie kontenera usługi mowy w wystąpieniach kontenera platformy Azure

Dowiedz się, jak wdrożyć kontener [usługi mowy](speech-container-howto.md) usług Cognitive Services w [wystąpieniach kontenerów](https://docs.microsoft.com/azure/container-instances/)platformy Azure. W tej procedurze pokazano utworzenie zasobu usługi Azure Speech. Następnie omówimy ciągnięcie skojarzonego obrazu kontenera. Na koniec podkreślamy możliwość wykonywania aranżacji dwóch z przeglądarki. Korzystanie z kontenerów można przenieść uwagę deweloperów od zarządzania infrastrukturą zamiast skupiać się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić i przesłać [formularz żądania kontenerów mowy usług Cognitive Services,](https://aka.ms/speechcontainerspreview/) aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
