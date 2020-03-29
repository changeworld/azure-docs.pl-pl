---
title: Uruchamianie wystąpień kontenerów platformy Azure — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdrażanie kontenerów analizy tekstu w wystąpieniu kontenera platformy Azure i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383526"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Wdrażanie kontenera analizy tekstu w wystąpieniach kontenerów platformy Azure

Dowiedz się, jak wdrożyć kontener [analizy tekstu][install-and-run-containers] usług Cognitive Services w [wystąpieniach kontenerów][container-instances]platformy Azure. Ta procedura jest przykładem tworzenia zasobu analizy tekstu, tworzenia skojarzonego obrazu analizy tonacji i możliwości wykonywania tej aranżacji dwóch z przeglądarki. Korzystanie z kontenerów można przenieść uwagę deweloperów od zarządzania infrastrukturą zamiast skupiać się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Następne kroki 

* Użyj większej liczby [kontenerów usług Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [usługi połączonej analizy tekstu](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances