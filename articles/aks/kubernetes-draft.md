---
title: Projekt za pomocą usługi AKS i Azure Container Registry
description: Projekt za pomocą usługi AKS i Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: zarhoads
ms.openlocfilehash: 462cfd6ec0a6b25f85dda0245dd4f5feed7cb712
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60465161"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Projekt za pomocą usługi Azure Kubernetes Service (AKS)

Projekt to narzędzie open source, które pomaga pakietów kontenerów i wdrażanie aplikacji w klastrze Kubernetes, pozostawiając użytkownika na skoncentrowanie się na cyklu dev - "wewnętrzną pętlę" najwięcej rozwoju. Działa narzędzie draft, jak kod jest obecnie sporządzana, ale przed zatwierdzeniem do systemu kontroli wersji. Za pomocą narzędzia Draft można szybko wdrożyć ponownie aplikację w usłudze Kubernetes wraz ze zmianami kodu. Aby uzyskać więcej informacji na temat projektu, zobacz [wersję roboczą dokumentacji w serwisie GitHub][draft-documentation].

W tym artykule pokazano, jak używać narzędzia Draft z klastrem Kubernetes w usłudze AKS.

## <a name="prerequisites"></a>Wymagania wstępne

W krokach szczegółowo opisanych w tym artykule przyjęto założenie, został utworzony klaster usługi AKS i ustalonymi `kubectl` połączenia z klastrem. Jeśli potrzebujesz tych elementów, zobacz [szybkiego startu usługi AKS][aks-quickstart].

Należy to prywatny rejestr platformy Docker w usłudze Azure Container Registry (ACR). Aby uzyskać instrukcje dotyczące sposobu tworzenia wystąpienia usługi ACR, zobacz [szybkiego startu usługi Azure Container Registry][acr-quickstart].

Należy także zainstalować narzędzia Helm w klastrze AKS. Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania narzędzia Helm, zobacz [Helm korzystanie z usługi Azure Kubernetes Service (AKS)][aks-helm].

Na koniec należy zainstalować [Docker](https://www.docker.com).

## <a name="install-draft"></a>Zainstaluj wersję roboczą

Projekt interfejsu wiersza polecenia jest klient, który działa w systemie deweloperskim i pozwala na wdrażanie kodu w klastrze Kubernetes. Aby zainstalować interfejs wiersza polecenia narzędzia Draft na komputerze Mac, należy użyć `brew`. Aby uzyskać dodatkowe opcje instalacji, zobacz [przewodnika instalacji narzędzia Draft][draft-documentation].

> [!NOTE]
> Jeśli zainstalowano wersję roboczą wcześniejszymi niż wersja 0,12, najpierw usuń wersję roboczą z klastra przy użyciu `helm delete --purge draft` , a następnie usuń konfigurację lokalnej, uruchamiając `rm -rf ~/.draft`. Jeśli jesteś w systemie MacOS Uruchom `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Teraz inicjować roboczą z `draft init` polecenia:

```console
draft init
```

## <a name="configure-draft"></a>Konfigurowanie narzędzia Draft

Projekt tworzy obrazy kontenerów lokalnie, a następnie opcję wdraża je z lokalnego rejestru (takie jak za pomocą Minikube), lub korzysta z rejestru obrazów, który określisz. W tym artykule używa usługi Azure Container Registry (ACR), więc należy ustanowić relację zaufania między klastrem AKS i rejestrem ACR, następnie skonfiguruj narzędzie Draft do wypychania obrazów kontenerów do usługi ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Tworzenie relacji zaufania między klastrem AKS i rejestru Azure container Registry

Aby ustanowić zaufanie między klastrem AKS i rejestr ACR, przyznaj uprawnienia dla jednostki usługi Azure Active Directory, które zostały użyte przez klaster AKS dostępu do rejestru usługi ACR. W poniższych poleceniach podać własne `<resourceGroupName>`, Zastąp `<aksName>` o nazwie klastra AKS, i Zastąp `<acrName>` nazwą rejestru ACR:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Aby uzyskać więcej informacji na temat tych kroków, aby dostęp do rejestru Azure container Registry, zobacz [uwierzytelniania przy użyciu usługi ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Konfigurowanie narzędzia Draft i wypychania do wdrażania z rejestru Azure container Registry

Teraz, że ma relację zaufania między AKS i ACR, umożliwiają korzystanie z usługi ACR z klastrem usługi AKS.

1. Ustaw konfigurację projektu *rejestru* wartość. W poniższych poleceniach Zastąp `<acrName>` nazwą rejestru ACR:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Zaloguj się do rejestru usługi ACR za pomocą [az acr login][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Jako relacja zaufania została utworzona między AKS i ACR, nie hasła lub kluczy tajnych są wymagane do wypchnięcia do lub Ściągaj je z rejestru ACR. Uwierzytelnianie odbywa się na poziomie usługi Azure Resource Manager przy użyciu usługi Azure Active Directory.

## <a name="run-an-application"></a>Uruchamianie aplikacji

Aby wyświetlić projekt w działaniu, zajmijmy się wdrożeniem przykładowej aplikacji z [repozytorium narzędzia Draft][draft-repo]. Najpierw sklonuj repozytorium:

```console
git clone https://github.com/Azure/draft
```

Zmień katalog przykładów Java:

```console
cd draft/examples/example-java/
```

Użyj `draft create` polecenie, aby rozpocząć proces. To polecenie tworzy artefaktów, które są używane do uruchamiania aplikacji w klastrze Kubernetes. Elementy te zawierają plik Dockerfile, wykresu Helm i *draft.toml* pliku, który jest plikiem konfiguracji projektu.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Aby uruchomić przykładową aplikację w klastrze AKS, użyj `draft up` polecenia. To polecenie tworzy plik Dockerfile, aby utworzyć obraz kontenera, wypycha obraz do rejestru Azure container Registry i na koniec instaluje wykresu Helm, aby uruchomić aplikację w usłudze AKS.

Wypychanie i ściąganie obrazu kontenera podczas pierwszego uruchomienia tego polecenia, może potrwać pewien czas. Gdy warstwy podstawowa są buforowane, czas potrzebny do wdrożenia aplikacji jest znacznie mniejsza.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Jeśli wystąpią problemy, wypychanie obrazu platformy Docker, upewnij się, że użytkownik pomyślnie zalogował się do rejestru usługi ACR za pomocą [az acr login][az-acr-login], spróbuj `draft up` ponownie polecenie.

## <a name="test-the-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Aby przetestować aplikację, należy użyć `draft connect` polecenia. To polecenie proxy bezpiecznego połączenia do zasobnika Kubernetes. Po zakończeniu aplikacja może zostać oceniony na podany adres URL.

> [!NOTE]
> Może upłynąć kilka minut, zanim obraz kontenera, które mają być pobrane i aplikacji, aby rozpocząć. Jeśli wystąpi błąd podczas uzyskiwania dostępu do aplikacji, próbę połączenia.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Uzyskiwanie dostępu do aplikacji, otwórz przeglądarkę sieci web w taki sposób, aby adres i port określony w `draft connect` dane wyjściowe, takie jak `http://localhost:49804`. 

![Przykładowa aplikacja Java uruchomiona za pomocą narzędzia Draft](media/kubernetes-draft/sample-app.png)

Użyj `Control+C` zatrzymania połączenia serwera proxy.

> [!NOTE]
> Można również użyć `draft up --auto-connect` polecenia do tworzenia i wdrażania aplikacji, a następnie natychmiast nawiązać połączenie z pierwszym uruchomionego kontenera.

## <a name="access-the-application-on-the-internet"></a>Uzyskaj dostęp do aplikacji w Internecie

Poprzedni krok utworzone połączenie z serwerem proxy do zasobnika aplikacji w klastrze AKS. Podczas tworzenia i testowania aplikacji, można udostępnić aplikacje w Internecie. Aby udostępnić aplikacji w Internecie, tworzenia usługi Kubernetes z typem [modułu równoważenia obciążenia][kubernetes-service-loadbalancer], lub Utwórz [kontrolera danych przychodzących] [ kubernetes-ingress]. Utwórzmy *modułu równoważenia obciążenia* usługi.

Najpierw należy zaktualizować *values.yaml* wersję roboczą pakietu, aby określić, że usługa z typem *modułu równoważenia obciążenia* powinny zostać utworzone:

```console
vi charts/java/values.yaml
```

Znajdź *service.type* właściwości i zaktualizuj wartość z *ClusterIP* do *modułu równoważenia obciążenia*, jak pokazano na poniższego, skróconego przykładu:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Zapisz i zamknij plik, a następnie użyj `draft up` do ponownego uruchomienia aplikacji:

```console
draft up
```

Trwa kilka minut, aż usługi zwrócić publiczny adres IP. Aby monitorować postęp, użyj `kubectl get service` polecenia *Obejrzyj* parametru:

```console
kubectl get service --watch
```

Początkowo *EXTERNAL-IP* dla usługi jest wyświetlany jako *oczekujące*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Po zmianie adresu EXTERNAL-IP z *oczekujące* jako adres IP, użyj `Control+C` przestanie `kubectl` Obserwuj proces:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Aby wyświetlić aplikację, przejdź do zewnętrznego adresu IP modułu równoważenia obciążenia za pomocą `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Powtarzanie czynności w aplikacji

Teraz, że projekt został skonfigurowany, a aplikacja jest uruchomiona na platformie Kubernetes, są ustawiane dla iteracji kodu. Każdorazowo, którą chcesz przetestować zaktualizowane kod, uruchom `draft up` polecenie, aby zaktualizować uruchomionej aplikacji.

W tym przykładzie zaktualizuj przykładowej aplikacji Java w celu zmiany tekstu. Otwórz *Hello.java* pliku:

```console
vi src/main/java/helloworld/Hello.java
```

Aktualizowanie tekstu wyjściowego do wyświetlenia, *Witaj, świecie, jestem Java w usłudze AKS!* :

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

Aby wyświetlić zaktualizowaną aplikację, należy ponownie curl adres IP modułu równoważenia obciążenia:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat za pomocą narzędzia Draft dokumentacji projektu w witrynie GitHub.

> [!div class="nextstepaction"]
> [Dokumentacja wersji roboczej][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
