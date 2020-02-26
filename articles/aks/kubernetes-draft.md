---
title: Opracowywanie w usłudze Azure Kubernetes Service (AKS) przy użyciu wersji roboczej
description: Użyj wersji roboczej z AKS i Azure Container Registry
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: b03256ee65a3c40d8a64d70b877c49e44e68f822
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595225"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Szybki Start: Programowanie w usłudze Azure Kubernetes Service (AKS) przy użyciu wersji roboczej

Wersja robocza to narzędzie typu "open source", które ułatwia pakowanie i uruchamianie kontenerów aplikacji w klastrze Kubernetes. Za pomocą wersji roboczej można szybko ponownie wdrożyć aplikację w celu Kubernetes, gdy nastąpi zmiana kodu, bez konieczności zatwierdzania zmian w systemie kontroli wersji. Aby uzyskać więcej informacji na temat wersji roboczej, zobacz [dokumentację roboczą w witrynie GitHub][draft-documentation].

W tym artykule pokazano, jak używać wersji roboczej do tworzenia pakietów i uruchamiania aplikacji w systemie AKS.


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Zainstalowano i skonfigurowano platformę Docker. Platforma Docker zawiera pakiety, które konfigurują platformę Docker w systemie [Mac][docker-for-mac], [Windows][docker-for-windows]lub [Linux][docker-for-linux] .
* [Helm v2][helm-install].
* [Zainstalowano wersję roboczą][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Utwórz klaster AKS. Poniższe polecenia tworzą grupę zasobów o nazwie Moja zasobów i klaster AKS o nazwie MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Aby można było uruchomić aplikację w klastrze AKS przy użyciu wersji roboczej, musisz mieć Azure Container Registry do przechowywania obrazów kontenerów. Poniższy przykład używa [AZ ACR Create][az-acr-create] , aby utworzyć ACR o nazwie *MyDraftACR* w grupie *zasobów zasobu* z *podstawową* jednostką SKU. Należy podać własną unikatową nazwę rejestru. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. *Podstawowa* jednostka SKU to zoptymalizowany pod kątem kosztów punkt wejścia do celów programistycznych zapewniający równowagę między przestrzenią dyskową i przepływnością.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj wartość *loginServer* dla ACR, ponieważ zostanie ona użyta w późniejszym kroku. W poniższym przykładzie *mydraftacr.azurecr.IO* jest *loginServer* dla *mydraftacr*.

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


Aby wersja robocza mogła używać wystąpienia ACR, musisz najpierw się zalogować. Użyj polecenia [AZ ACR login][az-acr-login] , aby się zalogować. Poniższy przykład zaloguje się do ACR o nazwie *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Polecenie zwraca komunikat *Logowanie pomyślne* po ukończeniu.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Tworzenie zaufania między klastrem AKS a ACR

Klaster AKS wymaga również dostępu do ACR w celu ściągania obrazów kontenera i uruchamiania ich. Możesz zezwolić na dostęp do ACR z AKS przez ustanowienie zaufania. Aby ustanowić relację zaufania między klastrem AKS i rejestrem ACR, Udziel uprawnień dla jednostki usługi Azure Active Directory używanej przez klaster AKS w celu uzyskania dostępu do rejestru ACR. Następujące polecenia przyznają uprawnienia do nazwy głównej usługi klastra *MyAKS* w grupie *zasobów* do *MyDraftACR* ACR w liście *zasobów*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Nawiązywanie połączenia z klastrem AKS

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć [polecenia kubectl][kubectl], klienta wiersza polecenia Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład pobiera poświadczenia dla klastra AKS o nazwie *MyAKS* w liście *zasobów*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Utwórz konto usługi dla Helm

Przed wdrożeniem Helm w klastrze AKS z włączoną funkcją RBAC należy mieć konto usługi i powiązanie roli dla usługi. Aby uzyskać więcej informacji na temat zabezpieczania Helm/do usługi w klastrze z obsługą RBAC, zobacz odniesień [, przestrzenie nazw i RBAC][tiller-rbac]. Jeśli w klastrze AKS nie włączono kontroli RBAC, Pomiń ten krok.

Utwórz plik o nazwie `helm-rbac.yaml` i skopiuj go do następującej YAML:

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

Utwórz konto usługi i powiązanie roli za pomocą polecenia `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Konfigurowanie Helm
Aby wdrożyć podstawową usługę do klastra AKS, użyj polecenia [init Helm][helm-init] . Jeśli w klastrze nie włączono kontroli RBAC, Usuń `--service-account` argument i wartość.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Konfigurowanie wersji roboczej

Jeśli nie skonfigurowano wersji roboczej na komputerze lokalnym, uruchom `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Należy również skonfigurować wersję roboczą, aby korzystała z *LOGINSERVER* ACR. Następujące polecenie używa `draft config set`, aby użyć `mydraftacr.azurecr.io` jako rejestru.

```console
draft config set registry mydraftacr.azurecr.io
```

Skonfigurowano wersję roboczą do korzystania z ACR, a wersja robocza może wypchnąć obrazy kontenerów do ACR. Gdy wersja robocza uruchamia aplikację w klastrze AKS, nie są wymagane żadne hasła ani wpisy tajne do wypychania lub ściągania z rejestru ACR. Ponieważ relacja zaufania została utworzona między klastrem AKS a ACR, uwierzytelnianie odbywa się na poziomie Azure Resource Manager przy użyciu Azure Active Directory.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Ten przewodnik Szybki Start używa [przykładowej aplikacji Java z repozytorium GitHub][example-java]. Sklonuj aplikację z witryny GitHub i przejdź do katalogu `draft/examples/example-java/`.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Uruchamianie przykładowej aplikacji z wersją roboczą

Użyj `draft create` polecenie, aby przygotować aplikację.

```console
draft create
```

To polecenie tworzy artefakty, które są używane do uruchamiania aplikacji w klastrze Kubernetes. Te elementy obejmują pliku dockerfile, wykres Helm i plik *Draft. toml* , który jest plikiem konfiguracji wersji roboczej.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Aby uruchomić przykładową aplikację w klastrze AKS, użyj polecenia `draft up`.

```console
draft up
```

To polecenie kompiluje pliku dockerfile, aby utworzyć obraz kontenera, wypchnięcie obrazu do ACR i zainstalowanie wykresu Helm w celu uruchomienia aplikacji w AKS. Przy pierwszym uruchomieniu tego polecenia, wypychanie i ściąganie obrazu kontenera może zająć trochę czasu. Po zbuforowaniu warstw podstawowych czas wdrażania aplikacji jest znacznie zmniejszany.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Nawiązywanie połączenia z uruchomioną przykładową aplikacją z komputera lokalnego

Aby przetestować aplikację, użyj polecenia `draft connect`.

```console
draft connect
```

To polecenie proxy jest bezpiecznym połączeniem z Kubernetes pod. Po zakończeniu będzie można uzyskać dostęp do aplikacji przy użyciu podanego adresu URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Przejdź do aplikacji w przeglądarce przy użyciu adresu URL `localhost`, aby wyświetlić przykładową aplikację. W powyższym przykładzie adres URL jest `http://localhost:49804`. Zatrzymaj połączenie przy użyciu `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Dostęp do aplikacji w Internecie

W poprzednim kroku utworzono połączenie serwera proxy z aplikacją znajdującą się w klastrze AKS. Podczas tworzenia i testowania aplikacji możesz chcieć udostępnić ją w Internecie. Aby udostępnić aplikację w Internecie, można utworzyć usługę Kubernetes z typem [modułu równoważenia obciążenia][kubernetes-service-loadbalancer].

Aktualizowanie `charts/example-java/values.yaml` w celu utworzenia usługi *modułu równoważenia obciążenia* . Zmień wartość parametru *Service. Type* z *ClusterIP* na moduł *równoważenia obciążenia*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Zapisz zmiany, zamknij plik i uruchom `draft up`, aby ponownie uruchomić aplikację.

```console
draft up
```

Zwrócenie publicznego adresu IP przez usługę może potrwać kilka minut. Aby monitorować postęp, użyj `kubectl get service` polecenia z parametrem *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Przejdź do modułu równoważenia obciążenia aplikacji w przeglądarce przy użyciu *zewnętrznego adresu IP* , aby wyświetlić przykładową aplikację. W powyższym przykładzie adres IP jest `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Wykonaj iterację aplikacji

Możesz wykonać iterację aplikacji, wprowadzając zmiany lokalnie i ponownie uruchamiając `draft up`.

Aktualizowanie wiadomości zwróconej w [wierszu 7 src/Main/Java/HelloWorld/Hello. Java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Uruchom polecenie `draft up`, aby ponownie wdrożyć aplikację:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Aby wyświetlić zaktualizowaną aplikację, przejdź ponownie do adresu IP modułu równoważenia obciążenia i sprawdź, czy zmiany są widoczne.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Gdy klaster nie jest już wymagany, użyj polecenia [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów, klaster AKS, rejestr kontenerów, zapisane obrazy kontenerów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby zapoznać się z instrukcjami dotyczącymi usuwania jednostki usługi, zobacz temat [zagadnienia i usuwanie głównej usługi AKS][sp-delete].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o korzystaniu z wersji roboczej, zobacz dokumentację roboczą w witrynie GitHub.

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
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
