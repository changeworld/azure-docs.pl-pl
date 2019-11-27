---
title: Uruchom Azure Container Instances-analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdróż kontenery analizy tekstu w usłudze Azure Container instance i przetestuj je w przeglądarce internetowej.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383526"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Wdróż kontener analiza tekstu do Azure Container Instances

Dowiedz się, jak wdrożyć kontener [analiza tekstu][install-and-run-containers] Cognitive Services na platformie Azure [Container Instances][container-instances]. Ta procedura exemplifies tworzenie zasobu analiza tekstu, tworzenie skojarzonego obrazu analiza tonacji i możliwość wykonywania tej aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[wykrywanie języka](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[analiza tonacji](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Następne kroki 

* Użyj więcej [kontenerów Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [Analiza tekstu połączonej usługi](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances