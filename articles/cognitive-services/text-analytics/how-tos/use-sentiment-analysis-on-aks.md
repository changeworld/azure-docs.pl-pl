---
title: Uruchom usługę Azure Kubernetes Service — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Wdróż kontenery analiza tekstu za pomocą obrazu analizy tonacji w usłudze Azure Kubernetes i przetestuj je w przeglądarce internetowej.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552271"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Wdrażanie kontenera analizy tonacji w usłudze Azure Kubernetes Service

Dowiedz się, jak wdrożyć kontener usługi Azure Cognitive Services [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) za pomocą obrazu analizy tonacji w usłudze Azure Kubernetes Service (AKS). W tej procedurze pokazano, jak utworzyć zasób analiza tekstu, sposób tworzenia skojarzonego obrazu analizy tonacji oraz jak korzystać z tej aranżacji dwóch z przeglądarki. Korzystanie z kontenerów może przesunąć uwagę na zarządzanie infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchomione lokalnie. Nie używaj Azure Cloud Shell. Potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Edytor tekstu, na przykład [Visual Studio Code](https://code.visualstudio.com/download).
* Zainstalowano [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .
* Zainstalowano [interfejs wiersza polecenia Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) .
* Zasób platformy Azure z poprawną warstwą cenową. Nie wszystkie warstwy cenowe pracują z tym kontenerem:
    * Zasób **platformy Azure analiza tekstu** tylko przy użyciu warstw cenowych F0 lub standard.
    * Zasób **Cognitive Services platformy Azure** z warstwą cenową S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Wdrażanie kontenera analiza tekstu w klastrze AKS

1. Otwórz interfejs wiersza polecenia platformy Azure i zaloguj się do platformy Azure.

    ```azurecli
    az login
    ```

1. Zaloguj się do klastra AKS. Zamień `your-cluster-name` i`your-resource-group` na odpowiednie wartości.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po uruchomieniu tego polecenia raport przedstawia komunikat podobny do następującego:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Jeśli masz wiele subskrypcji na Twoim koncie platformy Azure, a `az aks get-credentials` polecenie zwróci błąd, typowy problem polega na tym, że używasz niewłaściwej subskrypcji. Ustaw kontekst sesji interfejsu wiersza polecenia platformy Azure, aby użyć tej samej subskrypcji, w której zostały utworzone zasoby, i spróbuj ponownie.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otwórz Edytor tekstu. Ten przykład używa Visual Studio Code.

    ```azurecli
    code .
    ```

1. W edytorze tekstów Utwórz nowy plik o nazwie _tonacji. YAML_i wklej do niego następujący YAML. Pamiętaj, aby `billing/value` zamienić `apikey/value` i wraz z własnymi informacjami.

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
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
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

1. Zapisz plik i Zamknij Edytor tekstu.
1. Uruchom polecenie Kubernetes `apply` z _tonacji. YAML_ jako obiektem docelowym:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Gdy polecenie pomyślnie zastosuje konfigurację wdrożenia, zostanie wyświetlony komunikat podobny do następującego:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Sprawdź, czy w obszarze został wdrożony:

    ```console
    kubectl get pods
    ```

    Dane wyjściowe dla stanu uruchomienia pod:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Sprawdź, czy usługa jest dostępna i Pobierz adres IP.

    ```console
    kubectl get services
    ```

    Dane wyjściowe dla stanu uruchomienia usługi _tonacji_ w obszarze:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Następne kroki

* Użyj więcej [kontenerów Cognitive Services](../../cognitive-services-container-support.md)
* Korzystanie z [Analiza tekstu połączonej usługi](../vs-text-connected-service.md)
