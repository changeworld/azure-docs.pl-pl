---
title: Uruchom w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenerów Analiza tekstu przy użyciu obrazu analizy tonacji, wystąpienia kontenera platformy Azure i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9f174d54fcc74eed613eb69412bc0e515f15897b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711726"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Wdrażanie kontenera analiza tonacji w usłudze Azure Container Instances

Informacje o wdrażaniu usług Cognitive Services [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) kontenera przy użyciu obrazu analizę tonacji na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura exemplifies tworzenia zasobu analizy tekstu, tworzenie powiązanego obrazu analizę tonacji i możliwość wykonywania tego aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Korzystanie z subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Kolejne kroki 

* Użycie [kontenerów usługi Cognitive Services](../../cognitive-services-container-support.md)
* Użyj [usługę połączoną z analizy tekstu](../vs-text-connected-service.md)
