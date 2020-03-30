---
title: Włączanie wielu obsługa wielu obszarów nazw dla kontrolera transferu danych przychodzących bramy aplikacji
description: Ten artykuł zawiera informacje dotyczące włączania obsługi wielu obszarów nazw w klastrze kubernetes z kontrolerem transferu danych przychodzących bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279925"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Włączanie obsługi wielu obszarów nazw w klastrze AKS z kontrolerem transferu danych przychodzących bramy aplikacji

## <a name="motivation"></a>Motywacja
[Obszary nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) usługi Kubernetes umożliwiają partycjonowanie klastra Kubernetes i przydzielenie ich do podgrup większego zespołu. Te podzespoła mogą następnie wdrażać i zarządzać infrastrukturą za pomocą drobniejszych kontroli zasobów, zabezpieczeń, konfiguracji itp. Kubernetes umożliwia co najmniej jeden zasoby przychodzące do samodzielnego definiowania w każdej przestrzeni nazw.

W wersji 0.7 [Azure Application Gateway Usłudze IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) można pozyskiwania zdarzeń z i obserwować wiele obszarów nazw. Jeśli administrator usługi AKS zdecyduje się użyć [bramy aplikacji](https://azure.microsoft.com/services/application-gateway/) jako transferu danych przychodzących, wszystkie obszary nazw będą używać tego samego wystąpienia bramy aplikacji. Pojedyncza instalacja kontrolera transferu danych przychodzących będzie monitorować dostępne przestrzenie nazw i skonfigurować bramę aplikacji, z nią skojarzoną.

Wersja 0.7 agic będzie nadal wyłącznie `default` obserwować obszar nazw, chyba że jest to jawnie zmienione na jeden lub więcej różnych obszarów nazw w konfiguracji Helm (patrz sekcja poniżej).

## <a name="enable-multiple-namespace-support"></a>Włączanie obsługi wielu przestrzeni nazw
Aby włączyć obsługę wielu obszarów nazw:
1. zmodyfikować plik [helm-config.yaml](#sample-helm-config-file) w jeden z następujących sposobów:
   - usunąć `watchNamespace` klucz całkowicie z [helm-config.yaml](#sample-helm-config-file) - AGIC będzie obserwować wszystkie przestrzenie nazw
   - ustawiona `watchNamespace` na pusty ciąg - AGIC będzie obserwować wszystkie przestrzenie nazw
   - dodać wiele obszarów nazw oddzielonych`watchNamespace: default,secondNamespace`przecinkiem ( ) - AGIC będzie obserwować te przestrzenie nazw wyłącznie
2. zastosuj zmiany szablonu Helm z:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Po wdrożeniu z możliwością obserwowania wielu obszarów nazw, AGIC będzie:
  - lista zasobów przychodzących ze wszystkich dostępnych obszarów nazw
  - filtrowanie do zasobów przychodzących z adnotacjami`kubernetes.io/ingress.class: azure/application-gateway`
  - komponowanie [połączonego configu bramy aplikacji](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - zastosowanie konfiguracji do powiązanej bramy aplikacji za pośrednictwem [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Konfiguracje powodujące konflikt
Wiele zasobów [transferu danych przychodzących w](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) obszarze nazw może poinstruować firmę AGIC o utworzeniu sprzecznych konfiguracji dla jednej bramy aplikacji. (Dwa przychodzące twierdząc, że ta sama domena na przykład.)

W górnej części hierarchii — **odbiorniki** (adres IP, port i host) i **reguły routingu** (odbiornik powiązania, pula wewnętrznej bazy danych i ustawienia HTTP) mogą być tworzone i współużytkowane przez wiele obszarów nazw/transferu danych przychodzących.

Z drugiej strony - ścieżki, pule zaplecza, ustawienia HTTP i certyfikaty TLS mogą być tworzone tylko przez jedną przestrzeń nazw, a duplikaty zostaną usunięte.

Rozważmy na przykład następujące zduplikowane `staging` zasoby `production` `www.contoso.com`ruchu przychodzącego zdefiniowane obszary nazw i:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Pomimo dwóch zasobów przychodzących wymagających ruchu `www.contoso.com` do kierowania do odpowiednich obszarów nazw Kubernetes, tylko jeden zaplecza może obsługiwać ruch. AGIC stworzy konfigurację na zasadzie "kto pierwszy, ten lepszy" dla jednego z zasobów. Jeśli dwa zasoby przychodzące są tworzone w tym samym czasie, jeden wcześniej w alfabecie będzie mieć pierwszeństwo. W powyższym przykładzie będziemy mogli tworzyć `production` tylko ustawienia dla ruchu przychodzącego. Brama aplikacji zostanie skonfigurowana z następującymi zasobami:

  - Odbiornika:`fl-www.contoso.com-80`
  - Reguła routingu:`rr-www.contoso.com-80`
  - Pula zaplecza:`pool-production-contoso-web-service-80-bp-80`
  - Ustawienia HTTP:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda kondycji:`pb-production-contoso-web-service-80-websocket-ingress`

Należy zauważyć, że z wyjątkiem *reguły odbiornika* i *routingu*utworzone`production`zasoby bramy aplikacji zawierają nazwę obszaru nazw ( ), dla którego zostały utworzone.

Jeśli dwa zasoby transferu danych przychodzących są wprowadzane do klastra AKS w różnych momentach w czasie, prawdopodobnie agic skończy się `namespace-B` w `namespace-A`scenariuszu, w którym ponownie konfiguruje bramę aplikacji i ponownie kieruje ruch z do .

Na przykład jeśli `staging` zostanie dodany pierwszy, AGIC skonfiguruje bramę aplikacji do kierowania ruchu do puli wewnętrznej bazy danych przemieszczania. Na późniejszym etapie wprowadzenie `production` transferu danych przychodzących spowoduje, że agic przeprogramować bramę `production` aplikacji, która rozpocznie routing ruchu do puli wewnętrznej bazy danych.

## <a name="restrict-access-to-namespaces"></a>Ograniczanie dostępu do obszarów nazw
Domyślnie AGIC skonfiguruje bramę aplikacji na podstawie transferu przychodzącego z adnotacjami w dowolnym obszarze nazw. Jeśli chcesz ograniczyć to zachowanie, masz następujące opcje:
  - ograniczyć przestrzenie nazw, jawnie definiując przestrzenie nazw `watchNamespace` AGIC należy przestrzegać za pomocą klucza YAML w [helm-config.yaml](#sample-helm-config-file)
  - używanie [funkcji Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) do ograniczania agic do określonych obszarów nazw

## <a name="sample-helm-config-file"></a>Przykładowy plik konfiguracyjny helmu
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

