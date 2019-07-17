---
title: Uruchom Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener aparatu rozpoznawania formularzy w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1fc516d309c2a51230f53e814a2dabdb774db9c2
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297710"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdróż kontener aparatu rozpoznawania formularzy do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [aparatu rozpoznawania Cognitive Services formularzy](form-recognizer-container-howto.md) na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu aparatu rozpoznawania formularza platformy Azure. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać formularz [żądania dostępu kontenerów rozpoznawania Cognitive Services formularzy](https://aka.ms/FormRecognizerRequestAccess) , aby zażądać dostępu do kontenera. Spowoduje to również zalogowanie się do przetwarzanie obrazów. Nie musisz oddzielnie rejestrować się w celu przetwarzanie obrazów formularza żądania. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
