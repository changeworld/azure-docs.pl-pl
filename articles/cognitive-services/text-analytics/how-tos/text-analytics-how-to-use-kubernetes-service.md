---
title: Uruchamianie usługi Azure Kubernetes — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdrażanie obrazu kontenera analizy tekstu w usłudze Azure Kubernetes i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877848"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Wdrażanie kontenera analizy tekstu w usłudze Azure Kubernetes

Dowiedz się, jak wdrożyć obraz kontenera usługi Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) w usłudze Azure Kubernetes Service (AKS). W tej procedurze pokazano, jak utworzyć zasób analizy tekstu, jak utworzyć skojarzony obraz analizy tonacji i jak wykonywać tę aranżację tych dwóch z przeglądarki. Korzystanie z kontenerów można przenieść uwagę od zarządzania infrastrukturą zamiast koncentrując się na rozwoju aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchamiane lokalnie. Nie używaj usługi Azure Cloud Shell. Potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Edytor tekstu, na przykład [Visual Studio Code](https://code.visualstudio.com/download).
* Zainstalowano [narzędzie interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Zainstalowano [plik wiersza polecenia Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* Zasób platformy Azure z poprawną warstwą cenową. Nie wszystkie warstwy cenowe działają z tym kontenerem:
    * **Zasób usługi Azure Text Analytics** tylko w warstwach F0 lub standardowych warstwach cenowych.
    * **Zasobów usługi Azure Cognitive Services** z warstwą cenową S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Następne kroki

* Użyj większej liczby [kontenerów usług Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [usługi połączonej analizy tekstu](../vs-text-connected-service.md)
