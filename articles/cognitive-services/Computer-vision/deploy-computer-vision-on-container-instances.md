---
title: Uruchom w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera wizualizacji komputerowej do wystąpienia kontenera platformy Azure i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711616"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Wdrażanie kontenera przetwarzania obrazów w usłudze Azure Container Instances

Informacje o wdrażaniu usług Cognitive Services [komputerowej](computer-vision-how-to-install-containers.md) kontenera na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobów przetwarzania obrazów. Następnie omówimy ściąganie obrazów skojarzony kontener. Na koniec zaznacz mamy możliwość wykonywania aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]