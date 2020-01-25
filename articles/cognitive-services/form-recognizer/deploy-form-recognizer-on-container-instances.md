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
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 5d9ab7d12bd6c5fe59bf521aff2c07446ac2f038
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717306"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdróż kontener aparatu rozpoznawania formularzy do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [aparatu rozpoznawania Cognitive Services formularzy](form-recognizer-container-howto.md) na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu aparatu rozpoznawania formularza platformy Azure. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

> [!IMPORTANT]
> Kontenery aparatu rozpoznawania formularzy używają obecnie wersji 1,0 interfejsu API aparatu rozpoznawania. Możesz uzyskać dostęp do najnowszej wersji interfejsu API za pomocą usługi zarządzanej.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać formularz [żądania dostępu kontenerów rozpoznawania Cognitive Services formularzy](https://aka.ms/FormRecognizerRequestAccess) , aby zażądać dostępu do kontenera. Spowoduje to również zalogowanie się do przetwarzanie obrazów. Nie musisz oddzielnie rejestrować się w celu przetwarzanie obrazów formularza żądania. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
