---
title: Uruchom Azure Container Instances-analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdróż kontenery analizy tekstu przy użyciu obrazu tonacji Analysis, do wystąpienia kontenera platformy Azure i przetestuj je w przeglądarce internetowej.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552528"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Wdróż kontener analiza tonacji do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Cognitive Services przy użyciu obrazu analiza tonacji do [Container Instances](https://docs.microsoft.com/azure/container-instances/)platformy Azure. Ta procedura exemplifies tworzenie zasobu analiza tekstu, tworzenie skojarzonego obrazu analiza tonacji i możliwość wykonywania tej aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Następne kroki 

* Użyj więcej [kontenerów Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [Analiza tekstu połączonej usługi](../vs-text-connected-service.md)
