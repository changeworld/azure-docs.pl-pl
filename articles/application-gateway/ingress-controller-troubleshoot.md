---
title: Rozwiązywanie problemów z kontrolerem transferu danych przychodzących bramy aplikacji
description: Ten artykuł zawiera dokumentację dotyczącą rozwiązywania typowych pytań i/lub problemów z kontrolerem transferu danych przychodzących bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795510"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Rozwiązywanie typowych pytań lub problemów z kontrolerem transferu danych przychodzących

[Usługa Azure Cloud Shell](https://shell.azure.com/) to najwygodniejszy sposób rozwiązywania problemów związanych z instalacją usługi AKS i AGIC. Uruchom powłokę z [shell.azure.com](https://shell.azure.com/) lub klikając link:

[![Uruchom osadź](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testuj za pomocą prostej aplikacji Kubernetes

Poniższe kroki zakładają:
  - Masz klaster AKS z włączoną obsługą sieci zaawansowanej
  - AGIC został zainstalowany w klastrze AKS
  - Brama aplikacji w sieci wirtualnej współużytkowana z klastrem usługi AKS

Aby sprawdzić, czy brama aplikacji + AKS + instalacja AGIC jest poprawnie skonfigurowana, należy wdrożyć najprostszą możliwą aplikację:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Skopiuj i wklej wszystkie wiersze jednocześnie ze skryptu powyżej do [usługi Azure Cloud Shell](https://shell.azure.com/). Upewnij się, że całe polecenie `cat` jest kopiowane `EOF`- począwszy od ostatniego .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Po pomyślnym wdrożeniu aplikacji powyżej klastra AKS będzie miał nowy pod, usługi i transferu przychodzącego.

Pobierz listę strąków z `kubectl get pods -o wide` [Cloud Shell:](https://shell.azure.com/).
Oczekujemy, że pod o nazwie "test-agic-app-pod" zostały utworzone. Będzie miał adres IP. Ten adres musi znajdować się w sieci wirtualnej bramy aplikacji, która jest używana z usługą AKS.

![Strąków](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Pobierz listę usług: `kubectl get services -o wide`. Oczekujemy, że usługa o nazwie "test-agic-app-service".

![Strąków](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Pobierz listę wnikaczach: `kubectl get ingress`. Oczekujemy, że zasób transferu przychodzącego o nazwie "test-agic-app-ingress" został utworzony. Zasób będzie miał nazwę hosta "test.agic.contoso.com".

![Strąków](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Jednym z zasobników będzie AGIC. `kubectl get pods`wyświetli listę zasobników, z których jeden rozpocznie się od "ingress-azure". Pobierz wszystkie dzienniki tego `kubectl logs <name-of-ingress-controller-pod>` zasobnika, aby sprawdzić, czy mieliśmy pomyślne wdrożenie. Pomyślne wdrożenie dodałoby następujące wiersze do dziennika:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternatywnie, z [Cloud Shell](https://shell.azure.com/) możemy pobrać tylko linie wskazujące `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`pomyślną konfigurację bramy aplikacji z , gdzie `<ingress-azure....>` powinna być dokładna nazwa zasobnika AGIC.

Brama aplikacji będzie miała zastosowaną następującą konfigurację:

- Słuchacz: ![słuchacz](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Reguła routingu: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Pula zaplecza:
  - W puli adresów wewnętrznej będzie jeden adres IP i będzie on zgodny z `kubectl get pods -o wide` 
 ![adresem IP zasobnika, który obserwowaliśmy wcześniej z backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Na koniec możemy `cURL` użyć polecenia z poziomu [usługi Cloud Shell,](https://shell.azure.com/) aby ustanowić połączenie HTTP z nowo wdrożoną aplikacją:

1. Użyj, `kubectl get ingress` aby uzyskać publiczny adres IP bramy aplikacji
2. Użycie rozszerzenia `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Strąków](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Wynik `HTTP/1.1 200 OK` wskazuje, że brama aplikacji + AKS + system AGIC działa zgodnie z oczekiwaniami.


## <a name="inspect-kubernetes-installation"></a>Inspekcja instalacji kubernetes

### <a name="pods-services-ingress"></a>Strąki, Usługi, Ruch przychodzący
Kontroler transferu danych przychodzących bramy aplikacji (AGIC) stale monitoruje następujące zasoby kubernetes: [Wdrażanie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) lub [Zasobnik](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Usługa](https://kubernetes.io/docs/concepts/services-networking/service/), [Ruch przychodzący](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Aby AGIC działał zgodnie z oczekiwaniami, muszą być następujące:
  1. AKS musi mieć jeden lub więcej zdrowych **zasobników**.
     Sprawdź to z [Cloud Shell](https://shell.azure.com/) z `kubectl get pods -o wide --show-labels` Jeśli `apsnetapp`masz pod z , dane wyjściowe mogą wyglądać następująco:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Jedna lub więcej **usług**, odwołując się do zasobników powyżej za pomocą pasujących `selector` etykiet.
     Sprawdź to z [Cloud Shell](https://shell.azure.com/) za pomocą`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, z `kubernetes.io/ingress.class: azure/application-gateway`adnotacjami , odwołując się do powyższej usługi Sprawdź to z Usługi Cloud [Shell](https://shell.azure.com/) za pomocą`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Wyświetl adnotacje ruchu przychodzącego `kubectl get ingress aspnetapp -o yaml` powyżej: (zastąp `aspnetapp` nazwą swojego ruchu przychodzącego)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Zasób transferu `kubernetes.io/ingress.class: azure/application-gateway`.ingress musi być oś.
 

### <a name="verify-observed-namespace"></a>Weryfikowanie obserwowanej przestrzeni nazw

* Pobierz istniejące przestrzenie nazw w klastrze Kubernetes. W jakiej przestrzeni nazw działa twoja aplikacja? Czy AGIC oglądania tej przestrzeni nazw? Zapoznaj się z dokumentacją [pomocy technicznej wielu obszarów nazw,](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) dotyczącą prawidłowego konfigurowania obserwowanych obszarów nazw.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Zasobnik AGIC powinien `default` znajdować się w `NAMESPACE`obszarze nazw (patrz kolumna). Zdrowy zasobnik `Running` miałby `STATUS` w kolumnie. Powinna istnieć co najmniej jedna kapsuła AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Jeśli zasobnik AGIC nie`STATUS` jest zdrowy (kolumna z powyższego polecenia nie `Running`jest):
  - pobierz dzienniki, aby zrozumieć, dlaczego:`kubectl logs <pod-name>`
  - dla poprzedniego wystąpienia zasobnika:`kubectl logs <pod-name> --previous`
  - opisać zasobnika, aby uzyskać więcej kontekstu:`kubectl describe pod <pod-name>`


* Czy masz usługi Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) i zasobów [przychodzących?](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Czy Twój [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) jest `kubernetes.io/ingress.class: azure/application-gateway`oznaczony: ? AGIC będzie obserwować tylko zasoby Usługi Przychodzące Kubernetes, które mają tę adnotację.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC emituje zdarzenia Kubernetes dla niektórych błędów krytycznych. Można wyświetlić następujące:
  - w terminalu za pośrednictwem`kubectl get events --sort-by=.metadata.creationTimestamp`
  - w przeglądarce przy użyciu interfejsu [użytkownika sieci Web Kubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Poziomy rejestrowania

AGIC ma 3 poziomy rejestrowania. Poziom 1 jest domyślny i pokazuje minimalną liczbę wierszy dziennika.
Poziom 5, z drugiej strony, będzie wyświetlać wszystkie dzienniki, w tym zdezynfekowanej zawartości config stosowane do ARM.

Społeczność Kubernetes ustanowiła 9 poziomów rejestrowania dla narzędzia [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) W tym repozytorium wykorzystujemy 3 z nich, z podobną semantyką:


| Szczegółowość | Opis |
|-----------|-------------|
|  1        | Domyślny poziom dziennika; pokazuje szczegóły uruchamiania, ostrzeżenia i błędy |
|  3        | Rozszerzone informacje o zdarzeniach i zmianach; listy utworzonych obiektów |
|  5        | Dzienniki obiektów marshal; pokazuje zdezynfekowany config JSON stosowany do ARM |


Poziomy szczegółowości można regulować za `verbosityLevel` pomocą zmiennej w pliku [helm-config.yaml.](#sample-helm-config-file) Zwiększ poziom szczegółowości, `5` aby uzyskać konfigur json wysyłane do [ARM:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - dodać `verbosityLevel: 5` na linii przez siebie w [helm-config.yaml](#sample-helm-config-file) i ponownie zainstalować
  - pobierz dzienniki z`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Przykładowy plik konfiguracyjny helmu
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

