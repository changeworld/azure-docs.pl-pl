---
title: Projekt za pomocą AKS i rejestru kontenera platformy Azure
description: Projekt za pomocą AKS i rejestru kontenera platformy Azure
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5dfecefb6ce1d74e02c64371a864a6d3b07a2e1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Projekt za pomocą usługi Azure Kubernetes (AKS)

Wersja robocza jest narzędziem open source, które ułatwia przechowywania i wdrażania tych kontenerach w klastrze Kubernetes, pozostawiając należy skoncentrować się na cyklu deweloperów — "pętlę wewnętrzny" Programowanie najwięcej wolnego. Projekt działa jako kod jest obecnie opracowywane, ale przed zatwierdzeniem do systemu kontroli wersji. Z wersji próbnej można szybko wdrożyć ponownie aplikację Kubernetes wystąpienia zmian kodu. Aby uzyskać więcej informacji na temat projektu, zobacz [projekt dokumentacji w witrynie Github][draft-documentation].

Szczegóły tego dokumentu z klastrem Kubernetes na AKS przy użyciu wersji roboczej.

## <a name="prerequisites"></a>Wymagania wstępne

W krokach szczegółowo opisanych w tym dokumencie założono, że klaster usługi AKS został utworzony i że zostało nawiązane połączenie kubectl z klastrem. Jeśli potrzebujesz tych elementów, zobacz [szybkiego startu AKS][aks-quickstart].

Należy również prywatnej rejestru Docker w rejestrze kontenera platformy Azure (ACR). Aby uzyskać instrukcje na temat wdrażania wystąpienia ACR, zobacz [Szybki Start Azure kontenera rejestru][acr-quickstart].

Helm również musi być zainstalowany w klastrze AKS. Aby uzyskać więcej informacji na temat instalowania helm, zobacz [Helm użycia z usługi Kubernetes Azure (AKS)][aks-helm].

Na koniec należy zainstalować [Docker](https://www.docker.com).

## <a name="install-draft"></a>Zainstaluj wersję roboczą

Projekt interfejsu wiersza polecenia jest klient, który działa w systemie deweloperskim i pozwala na quicky wdrożenie w klastrze Kubernetes kodu.

> [!NOTE]
> Jeśli po zainstalowaniu wersji roboczej poprzedzające wersję 0.12, najpierw należy usunąć projekt z przy użyciu klastra `helm delete --purge draft` , a następnie usuń lokalnej konfiguracji uruchamiając `rm -rf ~/.draft`. Jeśli używany jest system MacOS, uruchom `brew upgrade draft`.

Aby zainstalować projektu interfejsu wiersza polecenia przy użyciu Mac `brew`. Aby uzyskać dodatkowe opcje instalacji Zobacz, [zainstalować wersję roboczą przewodnik][draft-documentation].

```console
brew tap azure/draft
brew install draft
```

Teraz inicjowanie projektu z `draft init` polecenia.

```console
draft init
```

## <a name="configure-draft"></a>Konfigurowanie projektu

Projekt tworzy kontener obrazów na lokalnie, a następnie albo wdraża je z lokalnego rejestru (w przypadku Minikube), lub należy określić rejestru obrazu do użycia. W tym przykładzie użyto rejestru kontenera platformy Azure (ACR), dlatego należy ustanowić relację zaufania między klastrem AKS i rejestr ACR i skonfigurować projekt do dystrybuowania kontenera do ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Tworzenie relacji zaufania między AKS klastra i ACR

Aby ustanowić zaufanie między klastrem AKS i ACR rejestru, należy zmodyfikować główna Azure do usługi Active Directory używany z AKS, dodając do niego roli współautora z zakresem repozytorium ACR. Aby to zrobić, uruchom następujące polecenia, zastępując _&lt;aks zarządcy zasobów nazwy&gt;_ i _&lt;aks klastra name&gt;_ z grupy zasobów i nazwa użytkownika Klaster AKS i _&lt;acr-zarządcy zasobów — nazwa&gt;_ i _&lt;nazwa acr repozytorium&gt;_ z nazwą grupy i repozytorium zasobów rekordu ACR repozytorium, z którym chcesz utworzyć zaufania.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Te kroki i innych mechanizmów uwierzytelniania dostępu ACR do [uwierzytelnianych ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Skonfiguruj projekt wypychania do i wdrażanie z ACR

Teraz, że istnieje relacja zaufania między AKS i ACR, następujące kroki umożliwiają użycie ACR z klastrem AKS.
1. Ustaw konfigurację projektu `registry` wartość, uruchamiając `draft config set registry <registry name>.azurecr.io`, gdzie _&lt;nazwa rejestru&lt;_ to nazwa ACR rejestru.
2. Zaloguj się do rejestru ACR uruchamiając `az acr login -n <registry name>`.

Użytkownik jest obecnie zalogowany lokalnie do ACR i utworzyć relację zaufania z AKS i ACR, dlatego nie hasła lub kluczy tajnych są wymagane do wypychanej na lub ściąganie danych z ACR do AKS. Uwierzytelnianie odbywa się na poziomie usługi Azure Resource Manager, za pomocą usługi Azure Active Directory.

## <a name="run-an-application"></a>Uruchamianie aplikacji

Repozytorium projekt obejmuje kilka przykładowe aplikacje, które mogą służyć do pokaz projektu. Utwórz kopię sklonowanego repozytorium.

```console
git clone https://github.com/Azure/draft
```

Przejdź do katalogu przykłady Java.

```console
cd draft/examples/example-java/
```

Użyj `draft create` polecenie, aby rozpocząć proces. To polecenie tworzy artefaktów, które są używane do uruchamiania aplikacji w klastrze Kubernetes. Elementy te obejmują plik Dockerfile, wykres Helm i `draft.toml` pliku, który jest plikiem konfiguracji projektu.

```console
draft create
```

Dane wyjściowe:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Aby uruchomić aplikację w klastrze Kubernetes, należy użyć `draft up` polecenia. To polecenie tworzy plik Dockerfile, aby utworzyć obraz kontenera, wypychanie obrazu do ACR i instaluje finally wykres Helm, aby uruchomić aplikację w AKS.

Po raz pierwszy jest uruchomiona, wypychanie i ściąganie kontener obrazu może zająć pewien czas; Po warstwy podstawowej są buforowane, czas trwania jest znacznie mniejsza.

```console
draft up
```

Dane wyjściowe:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Testowanie aplikacji

Aby przetestować aplikację, należy użyć `draft connect` polecenia. To polecenie proxy połączenie z pod Kubernetes, dzięki czemu bezpiecznego połączenia lokalnego. Po zakończeniu aplikacji są dostępne na podany adres URL.

W niektórych przypadkach może potrwać kilka minut, aż do pobrania obrazu kontenera i aplikację do uruchomienia. Jeśli wystąpi błąd podczas uzyskiwania dostępu do aplikacji, ponów próbę połączenia.

```console
draft connect
```

Dane wyjściowe:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Teraz przetestować aplikację, przechodząc do http://localhost:46143 (dla poprzedniego przykładu; portów może być inny). Po zakończeniu badania użycia aplikacji `Control+C` zatrzymania połączenia serwera proxy.

> [!NOTE]
> Można również użyć `draft up --auto-connect` polecenia do tworzenia i wdrażania aplikacji i natychmiast nawiązania pierwszego kontenera uruchomionych dokonanie iteracji cyklu szybciej.

## <a name="expose-application"></a>Udostępnianie aplikacji

Podczas testowania aplikacji w Kubernetes, można udostępnić aplikacje w Internecie. Można to zrobić przy użyciu usługi Kubernetes z typem [usługi równoważenia obciążenia] [ kubernetes-service-loadbalancer] lub [kontrolera wejściowych][kubernetes-ingress]. Szczegóły tego dokumentu przy użyciu usługi Kubernetes.


Najpierw projekt pakietu musi zostać zaktualizowany, aby określić, że usługa z typem `LoadBalancer` powinien zostać utworzony. Aby to zrobić, typ usługi, w aktualizacji `values.yaml` pliku.

```console
vi charts/java/values.yaml
```

Zlokalizuj `service.type` właściwości i zaktualizuj wartość z `ClusterIP` do `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Uruchom `draft up` ponowne uruchomienie aplikacji.

```console
draft up
```

Może upłynąć kilka minut, aż usługi powrócić do publicznego adresu IP. Aby monitorować postęp użyj `kubectl get service` z czujki.

```console
kubectl get service -w
```

Początkowo *IP zewnętrznego* dla usługi pojawia się jako `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Po zmianie adresu EXTERNAL-IP z `pending` na `IP address` użyj polecenia `Control+C`, aby zatrzymać proces śledzenia narzędzia kubectl.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Aby wyświetlić aplikację, przejdź do zewnętrznego adresu IP.

```console
curl 52.175.224.118
```

Dane wyjściowe:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterowanie w aplikacji

Projekt został skonfigurowany, a aplikacja jest uruchomiona w Kubernetes, są ustawiane dla kodu iteracji. Kod zawsze chcesz przetestować zaktualizowane, uruchom `draft up` polecenie, aby zaktualizować działającej aplikacji.

Na przykład aktualizacji aplikacji Java hello world.

```console
vi src/main/java/helloworld/Hello.java
```

Aktualizowanie tekstu Hello World.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Uruchom `draft up --auto-connect` polecenie, aby ponownie wdrożyć aplikację wszelki natychmiast pod jest gotowy do odpowiedzi.

```console
draft up --auto-connect
```

Dane wyjściowe

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Na koniec Wyświetl aplikację, aby zobaczyć aktualizacje.

```console
curl 52.175.224.118
```

Dane wyjściowe:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o korzystaniu z wersji próbnej zobacz dokumentację projekt w witrynie GitHub.

> [!div class="nextstepaction"]
> [Dokumentacja projektu][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
