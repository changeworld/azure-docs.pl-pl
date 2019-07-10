---
title: Uruchom w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera usług mowy do wystąpienia kontenera platformy Azure i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711517"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Wdrażanie kontenera usługa rozpoznawania mowy w usłudze Azure Container Instances

Informacje o wdrażaniu usług Cognitive Services [usługa rozpoznawania mowy](speech-container-howto.md) kontenera na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobów platformy Azure, usługa rozpoznawania mowy. Następnie omówimy ściąganie obrazów skojarzony kontener. Na koniec zaznacz mamy możliwość wykonywania aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [formularz wniosku kontenery mowy w usłudze Cognitive Services](https://aka.ms/speechcontainerspreview/) Aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
