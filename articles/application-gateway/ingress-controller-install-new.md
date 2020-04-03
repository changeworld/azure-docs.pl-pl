---
title: Tworzenie kontrolera transferu danych przychodzących z nową bramą aplikacji
description: Ten artykuł zawiera informacje dotyczące sposobu wdrażania kontrolera transferu danych przychodzących bramy aplikacji przy zastosowaniu nowej bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b46c9f8b0cad74f3a4e9be8903270a60993c01f4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585898"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Jak zainstalować kontroler transferu danych (AGIC) przy użyciu nowej bramy aplikacji

Poniższe instrukcje zakładają, że kontroler agic bramy aplikacji zostanie zainstalowany w środowisku bez wcześniej istniejących składników.

## <a name="required-command-line-tools"></a>Wymagane narzędzia wiersza polecenia

Zaleca się użycie [usługi Azure Cloud Shell](https://shell.azure.com/) dla wszystkich operacji wiersza polecenia poniżej. Uruchom powłokę z shell.azure.com lub klikając link:

[![Uruchom osadź](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

Alternatywnie uruchom usługę Cloud Shell z witryny Azure portal przy użyciu następującej ikony:

![Uruchomienie portalu](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Usługa [Azure Cloud Shell](https://shell.azure.com/) ma już wszystkie niezbędne narzędzia. Jeśli zdecydujesz się korzystać z innego środowiska, upewnij się, że są zainstalowane następujące narzędzia wiersza polecenia:

* `az`- Azure CLI: [instrukcje instalacji](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Kubernetes narzędzie wiersza polecenia: [instrukcje instalacji](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Menedżer pakietów Kubernetes: [instrukcje instalacji](https://github.com/helm/helm/releases/latest)
* `jq`- wiersz polecenia JSON procesor: [instrukcja instalacji](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Tworzenie tożsamości

Wykonaj poniższe czynności, aby utworzyć [obiekt jednostkowy usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)Azure Active Directory (AAD). Proszę zapisać `appId` `password`, `objectId` i wartości - będą używane w następujących krokach.

1. Tworzenie jednostki usługi AD ([Dowiedz się więcej o RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Wartości `appId` `password` i wartości z danych wyjściowych JSON będą używane w następujących krokach


1. Użyj `appId` danych wyjściowych poprzedniego polecenia, `objectId` aby uzyskać nową jednostkę usługi:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Dane wyjściowe tego `objectId`polecenia to , które będą używane w szablonie usługi Azure Resource Manager poniżej

1. Utwórz plik parametrów, który będzie używany w usłudze Azure Resource Manager wdrożenia szablonu później.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Aby wdrożyć klaster z włączoną funkcją **RBAC,** ustaw pole na `aksEnabledRBAC``true`

## <a name="deploy-components"></a>Wdrażanie składników
Ten krok spowoduje dodanie następujących składników do subskrypcji:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Brama aplikacji](https://docs.microsoft.com/azure/application-gateway/overview) w wersji 2
- [Sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) z 2 [podsieciami](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Publiczny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Tożsamość zarządzana](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), która będzie używana przez [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Pobierz szablon usługi Azure Resource Manager i zmodyfikuj szablon zgodnie z potrzebami.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Wdrażanie szablonu usługi `az cli`Azure Resource Manager przy użyciu programu . Może to potrwać do 5 minut.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Po zakończeniu wdrożenia pobierz dane wyjściowe `deployment-outputs.json`wdrożenia do pliku o nazwie .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Konfigurowanie kontrolera transferu danych przychodzących bramy

Dzięki instrukcjom zawartym w poprzedniej sekcji utworzyliśmy i skonfigurowaliśmy nowy klaster AKS i bramę aplikacji. Teraz jesteśmy gotowi wdrożyć przykładową aplikację i kontroler transferu danych przychodzących do naszej nowej infrastruktury kubernetes.

### <a name="setup-kubernetes-credentials"></a>Poświadczenia kubernetes instalatora
W poniższych krokach potrzebujemy polecenia konfiguracja [kubectl,](https://kubectl.docs.kubernetes.io/) którego użyjemy do połączenia się z naszym nowym klastrem Kubernetes. [Aplikacja](https://shell.azure.com/) Cloud `kubectl` Shell została już zainstalowana. Użyjemy `az` interfejsu wiersza polecenia do uzyskania poświadczeń dla kubernetes.

Uzyskaj poświadczenia dla nowo wdrożonego AKS[(czytaj więcej):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalowanie tożsamości zasobnika usługi AAD
  Tożsamość zasobnika usługi Azure Active Directory zapewnia dostęp oparty na tokenach do [usługi Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) doda następujące składniki do klastra Kubernetes:
   * Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity`: `AzureAssignedIdentity`,`AzureIdentityBinding`
   * [Składnik kontrolera tożsamości zarządzanej (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [Składnik tożsamości zarządzanej węzła (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Aby zainstalować tożsamość zasobnika usługi AAD w klastrze:

   - *RbAC włączony* Klaster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC wyłączony* Klaster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Zainstaluj helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) jest menedżerem pakietów dla Kubernetes. Wykorzystamy go do `application-gateway-kubernetes-ingress` zainstalowania pakietu:

1. Zainstaluj [helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) i uruchom `application-gateway-kubernetes-ingress` następujące czynności, aby dodać pakiet helm:

    - *RbAC włączony* Klaster AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC wyłączony* Klaster AKS

        ```bash
        helm init
        ```

1. Dodaj repozytorium AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Instalowanie wykresu helm kontrolera transferu danych przychodzących

1. Użyj `deployment-outputs.json` pliku utworzonego powyżej i utwórz następujące zmienne.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Pobierz helm-config.yaml, który skonfiguruje AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Lub skopiuj plik YAML poniżej: 
    
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

1. Edytuj nowo pobrane helm-config.yaml i wypełnij sekcje `appgw` i `armAuth`.
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Wartości:
     - `verbosityLevel`: Ustawia poziom szczegółowości infrastruktury rejestrowania AGIC. Zobacz [Rejestrowanie poziomów](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) dla możliwych wartości.
     - `appgw.subscriptionId`: Identyfikator subskrypcji platformy Azure, w którym znajduje się brama aplikacji. Przykład: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Nazwa grupy zasobów platformy Azure, w której utworzono bramę aplikacji. Przykład: `app-gw-resource-group`
     - `appgw.name`: Nazwa bramy aplikacji. Przykład: `applicationgatewayd0f0`
     - `appgw.shared`: Ta flaga logiczna `false`powinna być domyślnie oznaczona . Ustaw `true` do tej, czy potrzebna jest [brama aplikacji udostępnionej](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Określ przestrzeń nazw, którą AGIC powinien obserwować. Może to być wartość pojedynczego ciągu lub oddzielona przecinkami lista obszarów nazw.
    - `armAuth.type`: może `aadPodIdentity` być lub`servicePrincipal`
    - `armAuth.identityResourceID`: Identyfikator zasobu tożsamości zarządzanej platformy Azure
    - `armAuth.identityClientId`: Identyfikator klienta tożsamości. Zobacz poniżej, aby uzyskać więcej informacji na temat tożsamości
    - `armAuth.secretJSON`: Jest potrzebny tylko wtedy, gdy `armAuth.type` wybrany jest `servicePrincipal`typ klucza tajnego usługi (po ustawieniu na) 


   > [!NOTE]
   > Wartości `identityResourceID` `identityClientID` i są, które zostały utworzone podczas [wdrażania składników](ingress-controller-install-new.md#deploy-components) kroki i można uzyskać ponownie za pomocą następującego polecenia:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`w powyższym poleceniu jest grupa zasobów bramy aplikacji. `<identity-name>`jest nazwą utworzonej tożsamości. Wszystkie tożsamości dla danej subskrypcji mogą być wyświetlane przy użyciu:`az identity list`


1. Zainstaluj pakiet kontrolera transferu danych przychodzących bramy aplikacji:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instalowanie przykładowej aplikacji
Teraz, gdy mamy zainstalowaną bramę aplikacji, AKS i AGIC, możemy zainstalować przykładową aplikację za pośrednictwem [usługi Azure Cloud Shell:](https://shell.azure.com/)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
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
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternatywnie można:

* Pobierz plik YAML powyżej:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Zastosuj plik YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Inne przykłady
Ten [przewodnik zawiera](ingress-controller-expose-service-over-http-https.md) więcej przykładów, jak udostępnić usługę AKS za pośrednictwem protokołu HTTP lub HTTPS, do Internetu z bramą aplikacji.
