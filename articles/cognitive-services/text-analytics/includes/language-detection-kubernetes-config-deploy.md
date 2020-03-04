---
title: wykrywanie języka Kubernetes konfigurację i wdrażanie kroków
titleSuffix: Azure Cognitive Services
description: wykrywanie języka Kubernetes konfigurację i wdrażanie kroków
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: addb044d82429a4471e7ecd302351dd7b7eada84
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78261953"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>Wdrażanie kontenera wykrywanie języka w klastrze AKS

1. Otwórz interfejs wiersza polecenia platformy Azure i zaloguj się do platformy Azure.

    ```azurecli
    az login
    ```

1. Zaloguj się do klastra AKS. Zastąp `your-cluster-name` i `your-resource-group` odpowiednimi wartościami.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Po uruchomieniu tego polecenia raport przedstawia komunikat podobny do następującego:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Jeśli masz wiele subskrypcji na Twoim koncie platformy Azure, a polecenie `az aks get-credentials` zwraca z błędem, typowy problem polega na tym, że używasz niewłaściwej subskrypcji. Ustaw kontekst sesji interfejsu wiersza polecenia platformy Azure, aby użyć tej samej subskrypcji, w której zostały utworzone zasoby, i spróbuj ponownie.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Otwórz Edytor tekstu. Ten przykład używa Visual Studio Code.

    ```console
    code .
    ```

1. W edytorze tekstów Utwórz nowy plik o nazwie *Language. YAML*i wklej do niego następujące YAML. Pamiętaj, aby zastąpić `billing/value` i `apikey/value` własnymi informacjami.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
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
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. Zapisz plik i Zamknij Edytor tekstu.
1. Uruchom polecenie Kubernetes `apply` z plikiem *Language. YAML* jako obiektem docelowym:

    ```console
    kubectl apply -f language.yaml
    ```

    Gdy polecenie pomyślnie zastosuje konfigurację wdrożenia, zostanie wyświetlony komunikat podobny do następującego:

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. Sprawdź, czy w obszarze został wdrożony:

    ```console
    kubectl get pods
    ```

    Dane wyjściowe dla stanu uruchomienia pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Sprawdź, czy usługa jest dostępna i Pobierz adres IP.

    ```console
    kubectl get services
    ```

    Dane wyjściowe dla stanu uruchomienia usługi *językowej* w obszarze:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
