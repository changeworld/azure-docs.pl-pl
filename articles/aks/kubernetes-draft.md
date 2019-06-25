---
title: Opracowanie dla usługi Azure Kubernetes Service (AKS) za pomocą narzędzia Draft
description: Projekt za pomocą usługi AKS i Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303543"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Szybki start: Opracowanie dla usługi Azure Kubernetes Service (AKS) za pomocą narzędzia Draft

Projekt to narzędzie open source, które pomaga pakiet i uruchamiaj kontenery aplikacji w klastrze Kubernetes. Za pomocą narzędzia Draft można szybko wdrożyć ponownie aplikację w usłudze Kubernetes wraz ze zmianami kodu bez konieczności zatwierdzania zmian do kontroli wersji. Aby uzyskać więcej informacji na temat projektu, zobacz [wersję roboczą dokumentacji w serwisie GitHub][draft-documentation].

W tym artykule przedstawiono sposób użycia pakietu projekt i uruchomić aplikację w usłudze AKS.


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Platformy docker zainstalowany i skonfigurowany. Środowisko docker zawiera pakiety, które konfigurują platformy Docker na [Mac][docker-for-mac], [Windows][docker-for-windows], lub [Linux][platformy docker dla systemu linux] systemu.
* [Zainstalowane narzędzia Helm](https://github.com/helm/helm/blob/master/docs/install.md).
* [Zainstalowana wersja robocza][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Utwórz klaster Azure Kubernetes Service

Tworzenie klastra AKS. Poniższe polecenia Utwórz grupę zasobów o nazwie MyResourceGroup oraz nazywany MyAKS klastra AKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Aby użyć wersji roboczej do uruchamiania aplikacji w klastrze AKS, potrzebne są usługi Azure Container Registry do przechowywania obrazów kontenerów. Poniższym przykładzie używa [tworzenie az acr][az-acr-create] do tworzenia rejestru Azure container Registry o nazwie *MyDraftACR* w *MyResourceGroup* grupę zasobów za pomocą *podstawowe* JEDNOSTKA SKU. Należy podać nazwę rejestru unikatowych. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Dane wyjściowe będą podobne do poniższego przykładu. Zwróć uwagę na *loginServer* wartość rekordu ACR, ponieważ będzie on używany w kolejnym kroku. W poniższym przykładzie *mydraftacr.azurecr.io* jest *loginServer* dla *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


Narzędzia draft można znaleźć do korzystania z wystąpienia usługi ACR należy najpierw zalogować. Użyj [az acr login][az-acr-login] polecenie, aby zalogować się. Poniższym przykładzie zostanie Zaloguj się do rejestru Azure container Registry o nazwie *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Polecenie zwraca komunikat *Logowanie pomyślne* po ukończeniu.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Tworzenie relacji zaufania między klastrem AKS i rejestru Azure container Registry

Klastra usługi AKS wymaga również dostępu do usługi ACR na potrzeby ściągania obrazów kontenera i uruchom je. Możesz zezwolić na dostęp do usługi ACR z usługi AKS, nawiązując z relacją zaufania. Aby ustanowić zaufanie między klastrem AKS i rejestr ACR, przyznaj uprawnienia dla jednostki usługi Azure Active Directory, które zostały użyte przez klaster AKS dostępu do rejestru usługi ACR. Następujące polecenia udzielić uprawnień do jednostki usługi z *MyAKS* klastra w systemie *MyResourceGroup* do *MyDraftACR* rejestru Azure container Registry w  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Połącz z klastrem usługi AKS

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład pobiera poświadczenia klastra AKS, o nazwie *MyAKS* w *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Utwórz konto usługi dla usługi Helm

Przed wdrożeniem narzędzia Helm w klastrze AKS z włączoną funkcją RBAC, potrzebujesz konta usługi i powiązania roli usługi Tiller. Aby uzyskać więcej informacji na temat zabezpieczenia Helm / Tiller w RBAC włączone klastra, zobacz [Tiller, przestrzenie nazw i RBAC][tiller-rbac]. Jeśli klaster AKS nie jest włączone RBAC, Pomiń ten krok.

Utwórz plik o nazwie `helm-rbac.yaml` i skopiuj do poniższego kodu YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Tworzenie konta usługi i powiązanie roli za pomocą `kubectl apply` polecenia:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurowanie narzędzia Helm
Aby wdrożyć podstawowe Tiller w klastrze AKS, wykonaj [polecenia helm init][helm-init] polecenia. Jeśli klaster nie jest włączone RBAC, Usuń `--service-account` argument i wartość.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurowanie narzędzia Draft

Jeśli nie skonfigurowano projektu na komputerze lokalnym, uruchom `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Należy również skonfigurować narzędzie Draft, aby użyć *loginServer* z rekordu ACR. Następujące polecenie używa `draft config set` używać `mydraftacr.azurecr.io` jak rejestr.

```console
draft config set registry mydraftacr.azurecr.io
```

Skonfigurowano projekt, aby użyć usługi ACR, a projekt może wypychanie obrazów kontenera do usługi ACR. Po uruchomieniu aplikacji w klastrze AKS projekt nie hasła lub kluczy tajnych są wymagane do wypchnięcia do lub Ściągaj je z rejestru ACR. Od chwili utworzenia relacji zaufania między klastrem AKS i rekordu ACR, uwierzytelnianie odbywa się na poziomie usługi Azure Resource Manager przy użyciu usługi Azure Active Directory.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Ten przewodnik Szybki Start używa [przykładowej aplikacji java z repozytorium GitHub projektu][example-java]. Klonowanie aplikacji z usługi GitHub i przejdź do `draft/examples/example-java/` katalogu.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Uruchamianie przykładowej aplikacji za pomocą narzędzia Draft

Użyj `draft create` polecenie, aby przygotować aplikację.

```console
draft create
```

To polecenie tworzy artefaktów, które są używane do uruchamiania aplikacji w klastrze Kubernetes. Elementy te zawierają plik Dockerfile, wykresu Helm i *draft.toml* pliku, który jest plikiem konfiguracji projektu.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Aby uruchomić przykładową aplikację w klastrze AKS, użyj `draft up` polecenia.

```console
draft up
```

To polecenie tworzy plik Dockerfile, aby utworzyć obraz kontenera, wypycha obraz do usługi ACR i instaluje wykresu Helm, aby uruchomić aplikację w usłudze AKS. Wypychanie i ściąganie obrazu kontenera podczas pierwszego uruchamiania tego polecenia może potrwać trochę czasu. Gdy warstwy podstawowa są buforowane, czas potrzebny do wdrożenia aplikacji jest znacznie mniejsza.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Łączenie z uruchomioną przykładową aplikację z komputera lokalnego

Aby przetestować aplikację, należy użyć `draft connect` polecenia.

```console
draft connect
```

To polecenie proxy bezpiecznego połączenia do zasobnika Kubernetes. Po zakończeniu aplikacja może zostać oceniony na podany adres URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Przejdź do aplikacji w przeglądarce za pomocą `localhost` adresu url, aby wyświetlić przykładową aplikację. W powyższym przykładzie adres url jest `http://localhost:49804`. Zatrzymaj połączenie za pomocą `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Uzyskaj dostęp do aplikacji w Internecie

Poprzedni krok utworzone połączenie z serwerem proxy do zasobnika aplikacji w klastrze AKS. Podczas tworzenia i testowania aplikacji, można udostępnić aplikacje w Internecie. Aby udostępnić aplikacji w Internecie, można utworzyć usługi Kubernetes za pomocą typu [modułu równoważenia obciążenia][kubernetes-service-loadbalancer].

Aktualizacja `charts/example-java/values.yaml` utworzyć *modułu równoważenia obciążenia* usługi. Zmień wartość właściwości *service.type* z *ClusterIP* do *modułu równoważenia obciążenia*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Zapisz zmiany, zamknij plik i uruchomić `draft up` do ponownego uruchomienia aplikacji.

```console
draft up
```

Trwa kilka minut, aż usługi zwrócić publiczny adres IP. Aby monitorować postęp, użyj `kubectl get service` polecenia *Obejrzyj* parametru:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Przejdź do modułu równoważenia obciążenia aplikacji w przeglądarce za pomocą *EXTERNAL-IP* aby zobaczyć przykładową aplikację. W powyższym przykładzie adres IP jest `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Powtarzanie czynności w aplikacji

Aplikację można wykonać iterację przez wprowadzania zmian lokalnie i ponowne uruchomienie `draft up`.

Aktualizacja wiadomości zwrócona [wiersz 7 src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Uruchom `draft up` polecenia do ponownego wdrożenia aplikacji:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Aby wyświetlić zaktualizowaną aplikację, przejdź do adresu IP modułu równoważenia obciążenia ponownie i sprawdź, czy dokonane zmiany są widoczne.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Gdy klaster nie będzie już potrzebny, należy użyć [usunięcie grupy az][az-group-delete] polecenia, aby usunąć grupę zasobów, klaster AKS, rejestr kontenera, obrazy kontenera tam przechowywane i wszystkie pokrewne zasoby.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby uzyskać instrukcje dotyczące sposobu usuwania nazwy głównej usługi, zobacz [uwagi podmiotu zabezpieczeń i usuwania usługi AKS][sp-delete].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat za pomocą narzędzia Draft dokumentacji projektu w witrynie GitHub.

> [!div class="nextstepaction"]
> [Dokumentacja wersji roboczej][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
