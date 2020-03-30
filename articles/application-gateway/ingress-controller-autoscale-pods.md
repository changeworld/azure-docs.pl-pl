---
title: Automatyczne skalowanie zasobników usługi AKS z metrykami bramy aplikacji platformy Azure
description: Ten artykuł zawiera instrukcje dotyczące skalowania zasobników wewnętrznej bazy danych usługi AKS przy użyciu metryk bramy aplikacji i karty metryk azure kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239449"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Automatyczne skalowanie zasobników AKS przy użyciu metryk bramy aplikacji (beta)

Wraz ze wzrostem ruchu przychodzącego kluczowe znaczenie ma skalowanie aplikacji w zależności od zapotrzebowania.

W poniższym samouczku wyjaśniamy, jak można `AvgRequestCountPerHealthyHost` użyć metryki bramy aplikacji do skalowania aplikacji. `AvgRequestCountPerHealthyHost`mierzy średnie żądania wysyłane do określonej puli wewnętrznej bazy danych i wewnętrznej bazy danych HTTP kombinacji ustawień.

Będziemy używać następujących dwóch elementów:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Użyjemy karty metryki do udostępnienia metryk bramy aplikacji za pośrednictwem serwera metryk. Karta metryka usługi Azure Kubernetes jest projektem open source na platformie Azure, podobnie jak kontroler transferu danych przychodzących bramy aplikacji. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Użyjemy HPA do korzystania z metryk bramy aplikacji i kierowania wdrożenia do skalowania.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Konfigurowanie karty metrycznej usługi Azure Kubernetes

1. Najpierw utworzymy jednostkę usługi Azure AAD i przypiszemy jej `Monitoring Reader` dostęp za pomocą grupy zasobów bramy aplikacji. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Teraz firma We [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) wdroży jednostkę usługi AAD utworzoną powyżej.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Utworzymy `ExternalMetric` zasób `appgw-request-count-metric`o nazwie . Ten zasób poinstruuje `AvgRequestCountPerHealthyHost` kartę `myApplicationGateway` metryki, aby udostępnić metrykę zasobu w `myResourceGroup` grupie zasobów. Za pomocą `filter` tego pola można kierować określone ustawienia HTTP puli wewnętrznej bazy danych i wewnętrznej bazy danych w bramie aplikacji.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Teraz możesz złożyć zapytanie do serwera metryk, aby sprawdzić, czy nasza nowa metryka jest coraz widoczna:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Używanie nowej metryki do skalowania wdrożenia

Gdy jesteśmy w `appgw-request-count-metric` stanie udostępnić za pośrednictwem serwera [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) metryki, jesteśmy gotowi do użycia do skalowania w górę naszego wdrożenia docelowego.

W poniższym przykładzie będziemy `aspnet`kierować przykładowe wdrożenie . Będziemy skalować strąki, gdy `appgw-request-count-metric` > 200 na pod `10` do max strąków.

Zastąp nazwę wdrożenia docelowego i zastosuj następującą konfigurację skalowania automatycznego:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Przetestuj konfigurację za pomocą narzędzia do testowania obciążenia, takiego jak apache bench:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Następne kroki
- [**Rozwiązywanie problemów z kontrolerem transferu danych przychodzących:**](ingress-controller-troubleshoot.md)Rozwiązywanie problemów z kontrolerem transferu danych przychodzących.