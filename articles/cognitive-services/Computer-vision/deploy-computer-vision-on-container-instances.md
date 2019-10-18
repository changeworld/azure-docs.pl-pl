---
title: Uruchom kontener przetwarzanie obrazów w Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener przetwarzanie obrazów w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 2de879d271ab001e62725424ea72e0604176efb4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516864"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Wdróż kontener przetwarzanie obrazów do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [przetwarzanie obrazów](computer-vision-how-to-install-containers.md) Cognitive Services na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje, jak utworzyć zasób przetwarzanie obrazów. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Zażądaj dostępu do rejestru kontenera prywatnego

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]