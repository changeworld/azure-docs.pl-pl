---
title: Włączanie obsługi wielu przestrzeni nazw dla Application Gateway kontrolera transferu danych przychodzących
description: Ten artykuł zawiera informacje dotyczące włączania obsługi wielu przestrzeni nazw w klastrze Kubernetes Application Gateway z kontrolerem danych przychodzących.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279925"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Włączanie obsługi wielu przestrzeni nazw w klastrze AKS przy użyciu kontrolera Application Gateway transferu danych przychodzących

## <a name="motivation"></a>Motywacją
[Przestrzenie nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes umożliwiają partycjonowanie klastra Kubernetes i alokację do podgrup większych zespołów. Te podzespołów mogą następnie wdrożyć infrastrukturę i zarządzać nią przy użyciu dokładniejszych kontroli nad zasobami, zabezpieczeniami, konfiguracją itp. Kubernetes umożliwia zdefiniowanie jednego lub większej liczby zasobów przychodzących niezależnie od poszczególnych przestrzeni nazw.

Począwszy od wersji 0,7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) mogą pozyskiwać zdarzenia z i obserwować wiele przestrzeni nazw. Jeśli administrator AKS zdecyduje się użyć [bramy App Gateway](https://azure.microsoft.com/services/application-gateway/) jako ruchu przychodzącego, wszystkie przestrzenie nazw będą używać tego samego wystąpienia Application Gateway. Pojedyncza instalacja kontrolera usług przychodzących będzie monitorować dostępne przestrzenie nazw i skonfiguruje Application Gateway, z którymi jest skojarzona.

Wersja 0,7 programu AGIC będzie nadal dotyczyć wyłącznie obszaru nazw `default`, chyba że zostanie to jawnie zmienione do co najmniej jednego innego obszaru nazw w konfiguracji Helm (patrz sekcja poniżej).

## <a name="enable-multiple-namespace-support"></a>Włączanie obsługi wielu przestrzeni nazw
Aby włączyć obsługę wielu przestrzeni nazw:
1. Zmodyfikuj plik [Helm-config. YAML](#sample-helm-config-file) w jeden z następujących sposobów:
   - Usuń klucz `watchNamespace` całkowicie z [Helm-config. YAML](#sample-helm-config-file) -AGIC zaobserwuje wszystkie przestrzenie nazw
   - Ustaw `watchNamespace` na pusty ciąg — AGIC będzie obserwować wszystkie przestrzenie nazw
   - Dodaj wiele przestrzeni nazw rozdzielonych przecinkiem (`watchNamespace: default,secondNamespace`) — AGIC będzie obserwować wyłącznie te przestrzenie nazw
2. Zastosuj zmiany szablonu Helm z: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Po wdrożeniu z możliwością obserwowania wielu przestrzeni nazw AGIC będzie:
  - Wyświetlanie listy zasobów przychodzących ze wszystkich dostępnych przestrzeni nazw
  - Przefiltruj do zasobów przychodzących z adnotacją `kubernetes.io/ingress.class: azure/application-gateway`
  - Utwórz scaloną [Application Gateway konfigurację](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - Zastosuj konfigurację do skojarzonej Application Gateway za pomocą [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Konfiguracje powodujące konflikty
Wiele [zasobów](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) związanych z ruchem przychodzącym z przestrzeni nazw może wydać AGIC, aby utworzyć sprzeczne konfiguracje dla jednego Application Gateway. (Na przykład dwie ingresses, które zatwierdzą tę samą domenę).

W górnej części hierarchii- **detektory** (adres IP, port i Host) oraz **reguły routingu** (odbiornik powiązań, Pula zaplecza i ustawienia http) mogą być tworzone i udostępniane przez wiele przestrzeni nazw/ingresses.

W przypadku innych ścieżek ręcznych pule zaplecza, ustawienia protokołu HTTP i certyfikaty TLS mogą być tworzone tylko przez jedną przestrzeń nazw, a duplikaty zostaną usunięte.

Rozważmy na przykład następujące zduplikowane przestrzenie nazw zdefiniowane przez zasoby wejściowe `staging` i `production` dla `www.contoso.com`:
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

Pomimo tego, że oba zasoby związane z ruchem przychodzącym w celu `www.contoso.com` być kierowane do odpowiednich przestrzeni nazw Kubernetes, tylko jeden z zaplecza może obsłużyć ten ruch. AGIC utworzy konfigurację dla jednego z zasobów na podstawie "pierwsze, pierwsze obsłużone". Jeśli dwa zasoby ingresses są tworzone w tym samym czasie, pierwszeństwo ma ten sam w alfabecie. Z powyższego przykładu będzie można tworzyć ustawienia dla `production` ruchu przychodzącego. Application Gateway zostaną skonfigurowane przy użyciu następujących zasobów:

  - Odbiornik: `fl- www.contoso.com-80`
  - Reguła routingu: `rr- www.contoso.com-80`
  - Pula zaplecza: `pool-production-contoso-web-service-80-bp-80`
  - Ustawienia protokołu HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda kondycji: `pb-production-contoso-web-service-80-websocket-ingress`

Należy pamiętać, że oprócz reguły *odbiornika* i *routingu*, utworzone zasoby Application Gateway obejmują nazwę przestrzeni nazw (`production`), dla której zostały utworzone.

Jeśli dwa zasoby związane z ruchem przychodzącym są wprowadzane do klastra AKS w różnych punktach w czasie, najprawdopodobniej AGIC się to w scenariuszu, w którym rekonfiguruje Application Gateway i ponownie kieruje ruch z `namespace-B` do `namespace-A`.

Na przykład jeśli dodano `staging` pierwsze, AGIC skonfiguruje Application Gateway do kierowania ruchu do puli zaplecza wewnętrznego. Na późniejszym etapie wprowadzenie `production` ruchu przychodzącego spowoduje, że program AGIC może ponownie obsłużyć Application Gateway, co spowoduje, że ruch routingu do puli zaplecza `production`.

## <a name="restrict-access-to-namespaces"></a>Ograniczanie dostępu do przestrzeni nazw
Domyślnie AGIC skonfiguruje Application Gateway na podstawie adnotacji przychodzących w dowolnej przestrzeni nazw. Jeśli chcesz ograniczyć to zachowanie, możesz korzystać z następujących opcji:
  - Ogranicz przestrzenie nazw, jawnie definiując przestrzenie nazw AGIC powinny być przestrzegane za pośrednictwem klucza `watchNamespace` YAML w [Helm-config. YAML](#sample-helm-config-file)
  - Użyj [roli/](https://docs.microsoft.com/azure/aks/azure-ad-rbac) rolibinding, aby ograniczyć AGIC do określonych przestrzeni nazw

## <a name="sample-helm-config-file"></a>Przykładowy plik konfiguracji Helm
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

