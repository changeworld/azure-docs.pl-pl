---
title: Uruchamianie kontenera rozpoznawania formularzy w wystąpieniach kontenera platformy Azure
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera rozpoznawania formularzy w wystąpieniu kontenera platformy Azure i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879534"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdrażanie kontenera rozpoznawania formularzy w wystąpieniach kontenera platformy Azure

Dowiedz się, jak wdrożyć kontener [aparatu rozpoznawania formularzy](form-recognizer-container-howto.md) usług Cognitive Services w [wystąpieniach kontenerów](https://docs.microsoft.com/azure/container-instances/)platformy Azure. W tej procedurze pokazano utworzenie zasobu aparatu rozpoznawania formularzy platformy Azure. Następnie omówimy ciągnięcie skojarzonego obrazu kontenera. Na koniec podkreślamy możliwość wykonywania aranżacji dwóch z przeglądarki. Korzystanie z kontenerów można przenieść uwagę deweloperów od zarządzania infrastrukturą zamiast skupiać się na tworzeniu aplikacji.

> [!IMPORTANT]
> Kontenery aparat rozpoznawania formularzy obecnie używają wersji 1.0 interfejsu API aparatu rozpoznawania formularzy. Dostęp do najnowszej wersji interfejsu API można uzyskać za pomocą usługi zarządzanej.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Aby zażądać dostępu do kontenera, należy najpierw wypełnić i przesłać [formularz żądania dostępu kontenerów formularzy usług Cognitive Services,](https://aka.ms/FormRecognizerContainerRequestAccess) aby zażądać dostępu do kontenera. W ten sposób również rejestruje się na Wizji Komputerowej. Nie musisz osobno zapisywać się na formularz żądania wizji komputerowej. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
