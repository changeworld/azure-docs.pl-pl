---
title: wyodrębnianie kluczowych fraz Kubernetes konfigurację i wdrażanie kroków
titleSuffix: Azure Cognitive Services
description: wyodrębnianie kluczowych fraz Kubernetes konfigurację i wdrażanie kroków
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: 5ec535fe2ce23a2ead1163e870aae97fd104ef09
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130061"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Wdrażanie kontenera wyodrębnianie kluczowych fraz w klastrze AKS

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

1. W edytorze tekstów Utwórz nowy plik o nazwie *keyphrase. YAML*i wklej do niego następujący YAML. Pamiętaj, aby `billing/value` zamienić `apikey/value` i wraz z własnymi informacjami.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Zapisz plik i Zamknij Edytor tekstu.
1. Uruchom polecenie Kubernetes `apply` z plikiem *keyphrase. YAML* jako obiektem docelowym:

    ```console
    kuberctl apply -f keyphrase.yaml
    ```

    Gdy polecenie pomyślnie zastosuje konfigurację wdrożenia, zostanie wyświetlony komunikat podobny do następującego:

    ```console
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Sprawdź, czy w obszarze został wdrożony:

    ```console
    kubectl get pods
    ```

    Dane wyjściowe dla stanu uruchomienia pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Sprawdź, czy usługa jest dostępna i Pobierz adres IP.

    ```console
    kubectl get services
    ```

    Dane wyjściowe dla stanu uruchomienia usługi *keyphrase* w obszarze:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
