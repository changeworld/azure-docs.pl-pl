---
title: Uruchamianie usługi Azure Kubernetes
titleSuffix: Text Analytics - Azure Cognitive Services
description: Wdrażanie kontenerów Analiza tekstu przy użyciu obrazu analizy tonacji, usługi Azure Kubernetes i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 4d5e1da01be531550915a38bed17dd8e57be907a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454951"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Wdrażanie kontenera analizę tonacji do usługi Azure Kubernetes (AKS)

Informacje o wdrażaniu usług Cognitive Services [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) kontenera przy użyciu analizy tonacji obrazu do usługi Azure Kubernetes (AKS). Ta procedura exemplifies tworzenia zasobu analizy tekstu, tworzenie powiązanego obrazu analizę tonacji i możliwość wykonywania tego aranżacji dwa z poziomu przeglądarki. Używanie kontenerów może przechodzić przez deweloperów uwagi od zarządzania infrastrukturą, aby zamiast koncentrowania się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które musi być zainstalowany i uruchamiane lokalnie. Nie należy używać usługi Azure Cloud shell.

* Korzystanie z subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Edytor tekstu, na przykład: [Program Visual Studio Code](https://code.visualstudio.com/download)
* Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Zainstaluj [interfejsu wiersza polecenia Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Zasób platformy Azure z warstwą cenową poprawne. Nie wszystkie warstwy cenowe pracować z tego kontenera:
    * **Analiza tekstu** zasobu o F0 lub standardowej ceny warstwy tylko.
    * **Usługi cognitive Services** warstwa cenowa zasobu z S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Wdrażanie kontenera analizy tekstu do klastra usługi AKS

1. Otwórz interfejs wiersza polecenia platformy Azure i zaloguj się do platformy Azure

    ```azurecli
    az login
    ```

1. Zaloguj się do klastra AKS (Zastąp `your-cluster-name` i `your-resource-group` odpowiednimi wartościami)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po wykonaniu tego polecenia, zgłasza komunikat podobny do następującego:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Jeśli masz wiele subskrypcji, dostępny na koncie platformy Azure i `az aks get-credentials` polecenie zwraca błąd, jest to powszechny problem, że używasz niewłaściwej subskrypcji. Po prostu ustaw kontekst do użycia z tą samą subskrypcją, w której zostały utworzone zasoby przy użyciu sesji wiersza polecenia platformy Azure i spróbuj ponownie.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otwórz Edytor tekstu, oraz (w tym przykładzie użyto __programu Visual Studio Code__):

    ```azurecli
    code .
    ```

1. W edytorze tekstów Utwórz nowy plik o nazwie _sentiment.yaml_ i Wklej poniższego kodu YAML:

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: "https://westus2.api.cognitive.microsoft.com/"
            - name: apikey
              value: "16c12e3419f54ba49a3222177cef781d"
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Zapisz plik i zamknij Edytor tekstu.
1. Wykonanie usługi Kubernetes `apply` polecenia _sentiment.yaml_ jako jego element docelowy:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Polecenie została zastosowana pomyślnie konfiguracji wdrożenia, komunikat podobny do następującego po danych wyjściowych:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Sprawdź, czy wdrożono ZASOBNIK:

    ```console
    kubectl get pods
    ```

    Dane wyjściowe stanu działania ZASOBNIKA:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Sprawdź, czy usługa jest dostępna, a następnie Uzyskaj adres IP:

    ```console
    kubectl get services
    ```

    Będzie to danych wyjściowych stanu działania _tonacji_ w ZASOBNIKA:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Kolejne kroki

* Użycie [kontenerów usługi Cognitive Services](../../cognitive-services-container-support.md)
* Użyj [usługę połączoną z analizy tekstu](../vs-text-connected-service.md)