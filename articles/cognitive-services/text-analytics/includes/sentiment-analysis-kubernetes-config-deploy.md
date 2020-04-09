---
title: Analiza tonacji Kubernetes konfiguracji i wdrażania kroków
titleSuffix: Azure Cognitive Services
description: Analiza tonacji Kubernetes konfiguracji i wdrażania kroków
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877847"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Wdrażanie kontenera analiza tonacji w klastrze AKS

1. Otwórz interfejsu wiersza polecenia platformy Azure i zaloguj się na platformie Azure.

    ```azurecli
    az login
    ```

1. Zaloguj się do klastra AKS. Wymień `your-cluster-name` i `your-resource-group` na odpowiednie wartości.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po uruchomieniu tego polecenia zgłasza komunikat podobny do następującego:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Jeśli masz wiele subskrypcji dostępnych na koncie `az aks get-credentials` platformy Azure, a polecenie zwraca się z błędem, częstym problemem jest użycie niewłaściwej subskrypcji. Ustaw kontekst sesji interfejsu wiersza polecenia platformy Azure, aby używać tej samej subskrypcji, z którą utworzono zasoby, i spróbuj ponownie.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otwórz wybrany edytor tekstu. W tym przykładzie użyto programu Visual Studio Code.

    ```console
    code .
    ```

1. W edytorze tekstu utwórz nowy plik o nazwie *sentiment.yaml*i wklej do niego następujący plik YAML. Pamiętaj, aby `billing/value` `apikey/value` zastąpić i z własnymi informacjami.

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
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
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

1. Zapisz plik i zamknij edytor tekstu.
1. Uruchom polecenie Kubernetes `apply` z plikiem *sentiment.yaml* jako jego celem:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Po pomyślnym rozmieszczeniu konfiguracji wdrożenia zostanie wyświetlony komunikat podobny do następującego wyjścia:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Sprawdź, czy zasobnik został wdrożony:

    ```console
    kubectl get pods
    ```

    Dane wyjściowe stanu bieżącego zasobnika:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Sprawdź, czy usługa jest dostępna i uzyskaj adres IP.

    ```console
    kubectl get services
    ```

    Dane wyjściowe dla stanu bieżącego usługi *tonacji* w zasobniku:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
