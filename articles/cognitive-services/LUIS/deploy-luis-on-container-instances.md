---
title: Uruchom w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera usługi LUIS do wystąpienia kontenera platformy Azure i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711572"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Wdrażanie kontenera Language Understanding (LUIS) w usłudze Azure Container Instances

Informacje o wdrażaniu usług Cognitive Services [LUIS](luis-container-howto.md) kontenera na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu usługi Wykrywanie anomalii. Następnie omówimy ściąganie obrazów skojarzony kontener. Na koniec zaznacz mamy możliwość wykonywania aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
