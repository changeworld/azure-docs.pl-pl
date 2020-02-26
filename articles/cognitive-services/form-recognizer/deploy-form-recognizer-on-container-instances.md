---
title: Uruchom kontener aparatu rozpoznawania formularzy w Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener aparatu rozpoznawania formularzy w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 9a3456eb4d30aa8d163488f558b571dd97c73bf4
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605123"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdróż kontener aparatu rozpoznawania formularzy do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [aparatu rozpoznawania Cognitive Services formularzy](form-recognizer-container-howto.md) na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu aparatu rozpoznawania formularza platformy Azure. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

> [!IMPORTANT]
> Kontenery aparatu rozpoznawania formularzy używają obecnie wersji 1,0 interfejsu API aparatu rozpoznawania. Możesz uzyskać dostęp do najnowszej wersji interfejsu API za pomocą usługi zarządzanej.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać formularz [żądania dostępu kontenerów rozpoznawania Cognitive Services formularzy](https://aka.ms/FormRecognizerContainerRequestAccess) , aby zażądać dostępu do kontenera. Spowoduje to również zalogowanie się do przetwarzanie obrazów. Nie musisz oddzielnie rejestrować się w celu przetwarzanie obrazów formularza żądania. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
