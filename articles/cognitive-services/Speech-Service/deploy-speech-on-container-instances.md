---
title: Uruchom usługę Azure Container Instances-Speech
titleSuffix: Azure Cognitive Services
description: Wdróż kontener usługi mowy w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 41589a12f16f330188e2971e3069eb715d1a7163
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559718"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Wdróż kontener usługi mowy w Azure Container Instances

Dowiedz się, jak wdrożyć kontener [usługi Cognitive Services Speech](speech-container-howto.md) na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu usługi Azure Speech Service. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać [Cognitive Services formularz żądania kontenerów mowy](https://aka.ms/speechcontainerspreview/) , aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
