---
title: Uruchom w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenera aparatu rozpoznawania formularza do wystąpienia kontenera platformy Azure i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711396"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Wdrażanie kontenera aparatu rozpoznawania formularza do usługi Azure Container Instances

Informacje o wdrażaniu usług Cognitive Services [rozpoznawania formularza](form-recognizer-container-howto.md) kontenera na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu usługi rozpoznawania formularza platformy Azure. Następnie omówimy ściąganie obrazów skojarzony kontener. Na koniec zaznacz mamy możliwość wykonywania aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [Cognitive Services formularza rozpoznawania kontenery dostępu formularz wniosku o](https://aka.ms/FormRecognizerRequestAccess) Aby zażądać dostępu do kontenera. Ten sposób również loguje Cię do przetwarzania obrazów. Nie musisz oddzielnie Zarejestruj się w formularzu żądania przetwarzania obrazów. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
