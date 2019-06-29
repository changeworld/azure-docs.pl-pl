---
title: Uruchom w usłudze Azure Container Instances
titleSuffix: Text Analytics - Azure Cognitive Services
description: Wdrażanie kontenerów Analiza tekstu przy użyciu obrazu analizy tonacji, wystąpienia kontenera platformy Azure i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454984"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>Wdrażanie kontenera analizę tonacji do usługi Azure Container Instances (ACI)

Informacje o wdrażaniu usług Cognitive Services [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) kontenera przy użyciu obrazu analizę tonacji na platformie Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Ta procedura exemplifies tworzenia zasobu analizy tekstu, tworzenie powiązanego obrazu analizę tonacji i możliwość wykonywania tego aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Korzystanie z subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Kolejne kroki 

* Użycie [kontenerów usługi Cognitive Services](../../cognitive-services-container-support.md)
* Użyj [usługę połączoną z analizy tekstu](../vs-text-connected-service.md)
