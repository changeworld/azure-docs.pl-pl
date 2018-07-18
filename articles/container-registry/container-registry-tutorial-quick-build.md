---
title: Samouczek — kompilowanie obrazów kontenera w chmurze przy użyciu usługi Azure Container Registry Build
description: Z tego samouczka dowiesz się, jak utworzyć obraz kontenera platformy Docker na platformie Azure przy użyciu usługi Azure Container Registry Build (ACR Build), a następnie wdrożyć go w usłudze Azure Container Instances.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d86c47c890fd15515c590e06b395ca82f9747ffe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461473"
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Samouczek: kompilowanie obrazów kontenera w chmurze przy użyciu usługi Azure Container Registry Build

Usługa **ACR Build** to pakiet funkcji usługi Azure Container Registry, który udostępnia udoskonalone i wydajne kompilacje obrazów kontenerów platformy Docker na platformie Azure. Z tego artykułu dowiesz się, jak używać funkcji *szybkiej kompilacji* usługi ACR Build. Szybka kompilacja rozszerza „wewnętrzną pętlę” programowania na chmurę, udostępniając funkcję walidacji powodzenia kompilacji i automatyczne wypychanie pomyślnie skompilowanych obrazów do rejestru kontenerów. Obrazy są wbudowane natywnie w chmurze, blisko rejestru, co umożliwia szybsze wdrażanie.

Całe środowisko pracy z plikami Dockerfile jest przenoszone bezpośrednio do usługi ACR Build. Nie trzeba zmieniać plików Dockerfile w celu przeprowadzania kompilacji w chmurze przy użyciu usługi ACR Build, wystarczy zmienić uruchamiane polecenie.

W tym samouczku, będącym pierwszą częścią serii, zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Pobieranie kodu źródłowego przykładowej aplikacji
> * Kompilowanie obrazu kontenera na platformie Azure
> * Wdrażanie kontenera w usłudze Azure Container Instances

Z kolejnych samouczków dowiesz się, jak używać zadań kompilacji usługi ACR Build na potrzeby automatycznych kompilacji obrazu kontenera podczas zatwierdzania kodu i aktualizacji obrazu podstawowego.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure lokalnie, musisz zainstalować wersję **2.0.32** interfejsu wiersza polecenia platformy Azure lub nowszą. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="github-account"></a>Konto w serwisie GitHub

Utworzenie konta usługi https://github.com, jeśli jeszcze go nie masz. W tym samouczku do zademonstrowania zautomatyzowanych kompilacji obrazu w usłudze ACR Build jest używane repozytorium serwisu GitHub.

### <a name="fork-sample-repository"></a>Tworzenie rozwidlenia przykładowego repozytorium

Następnie należy użyć interfejsu użytkownika serwisu GitHub do utworzenia rozwidlenia przykładowego repozytorium na koncie usługi GitHub. W tym samouczku utworzysz obraz kontenera na podstawie źródła w repozytorium, a w następnym samouczku wypchniesz zatwierdzenie do rozwidlenia repozytorium, aby uruchomić automatyczną kompilację.

Utwórz rozwidlenie tego repozytorium: https://github.com/Azure-Samples/acr-build-helloworld-node

![Zrzut ekranu przedstawiający przycisk rozwidlenia (wyróżniony) w serwisie GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Klonowanie rozwidlenia

Po utworzeniu rozwidlenia repozytorium sklonuj rozwidlenie, a następnie wprowadź katalog zawierający klon lokalny.

Sklonuj repozytorium przy użyciu polecenia `git` i zastąp wartość **\<your-github-username\>** swoją nazwą użytkownika serwisu GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Wprowadź katalog zawierający kod źródłowy:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Powłoka programu Bash

Polecenia w tym samouczku zostały sformatowane pod kątem powłoki programu Bash. Jeśli wolisz używać programu PowerShell, wiersza polecenia lub innej powłoki, konieczne może być odpowiednie dostosowanie kontynuacji wiersza i formatu zmiennych środowiskowych.

## <a name="build-in-azure-with-acr-build"></a>Kompilowanie na platformie Azure przy użyciu usługi ACR Build

Teraz, gdy kod źródłowy został pobrany do komputera, wykonaj następujące kroki, aby utworzyć rejestr kontenerów i skompilować obraz kontenera przy użyciu usługi ACR Build.

Aby wykonywanie przykładowych poleceń było łatwiejsze, w tej serii samouczków używaj zmiennych środowiskowych powłoki. Uruchom następujące polecenie, aby ustawić zmienną `ACR_NAME`. Zastąp element **\<registry-name\>** unikatową nazwą nowego rejestru kontenerów. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Inne zasoby tworzone w tym samouczku opierają się na tej nazwie, dlatego konieczne powinno być zmodyfikowanie tylko tej pierwszej zmiennej.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Po wypełnieniu zmiennej środowiskowej rejestru kontenerów teraz powinno być możliwe skopiowanie i wklejenie pozostałej części poleceń w samouczku bez edytowania wartości. Wykonaj następujące polecenia, aby utworzyć grupę zasobów i rejestr kontenerów:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Teraz, gdy masz rejestr, użyj usługi ACR Build do skompilowania obrazu kontenera na podstawie kodu przykładowego. Wykonaj polecenie [az acr build][az-acr-build] w celu przeprowadzenia *szybkiej kompilacji*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

Dane wyjściowe polecenia [az acr build][az-acr-build] są podobne do poniższych. Możesz wyświetlić przekazany kod źródłowy („kontekst”) na platformie Azure i szczegóły operacji `docker build`, którą usługa ACR Build uruchamia w chmurze. Ponieważ usługa ACR Build używa polecenia `docker build` do kompilowania obrazów, do natychmiastowego rozpoczęcia pracy z usługą ACR Build nie są wymagane żadne zmiany plików Dockerfile.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (4.909 KiB) to ACR.
Queued a build with build ID: aa1
Waiting for a build agent...
Sending build context to Docker daemon  22.53kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 0c4814714938
Step 3/5 : RUN cd /src && npm install
 ---> Running in 4f77ce7b330d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.096s
Removing intermediate container 4f77ce7b330d
 ---> e0eb24339e07
Step 4/5 : EXPOSE 80
 ---> Running in 872bd29edbc7
Removing intermediate container 872bd29edbc7
 ---> 22ba8d8ffb4e
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1a40c05c4122
Removing intermediate container 1a40c05c4122
 ---> 0a9a4b74fb53
Successfully built 0a9a4b74fb53
Successfully tagged mycontainerregistry.azurecr.io/helloacrbuild:v1
time="2018-05-10T19:10:20Z" level=info msg="Running command docker push mycontainerregistry.azurecr.io/helloacrbuild:v1"
The push refers to repository [mycontainerregistry.azurecr.io/helloacrbuild]
d2b301f7ef94: Preparing
d0e0f2bb8747: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
26b0c207c4a9: Pushed
d2b301f7ef94: Pushed
d0e0f2bb8747: Pushed
9dfa40a0da3b: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd size: 1366
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd"]"
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: aa1 was successful after 52.522222729s
```

Pod koniec danych wyjściowych usługa ACR Build wyświetla odnalezione zależności obrazu. Umożliwia to usłudze ACR Build automatyzowanie kompilacji obrazu podczas aktualizacji obrazu podstawowego, na przykład gdy obraz podstawowy jest aktualizowany przy użyciu poprawek systemu operacyjnego lub platformy. Obsługa aktualizacji obrazu podstawowego w usłudze ACR Build została omówiona w dalszej części tej serii samouczków.

## <a name="deploy-to-azure-container-instances"></a>Wdrażanie w usłudze Azure Container Instances

Usługa ACR Build domyślnie automatycznie wypycha pomyślnie skompilowane obrazy do rejestru, dzięki czemu można natychmiast wdrożyć je z rejestru.

W tej sekcji utworzysz magazyn Azure Key Vault i jednostkę usługi, a następnie wdrożysz kontener w usłudze Azure Container Instances (ACI) przy użyciu poświadczeń jednostki usługi.

### <a name="configure-registry-authentication"></a>Konfigurowanie uwierzytelniania rejestru

Wszystkie scenariusze produkcyjne powinny używać [jednostek usługi][service-principal-auth] do uzyskiwania dostępu do rejestru kontenerów platformy Azure. Jednostki usługi umożliwiają sprawowanie kontroli dostępu opartej na rolach nad obrazami kontenera. Na przykład można skonfigurować jednostkę usługi z dostępem tylko do ściągania do rejestru.

#### <a name="create-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu w usłudze [Azure Key Vault](/azure/key-vault/), utwórz go przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu poniższych poleceń.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Tworzenie jednostki usługi i poświadczenia magazynu

Musisz teraz utworzyć jednostkę usługi i przechowywać jej poświadczenia w magazynie kluczy.

Użyj polecenia [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] do utworzenia jednostki usługi i polecenia [az keyvault secret set][az-keyvault-secret-set] do przechowywania **hasła** jednostki usługi w magazynie:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Argument `--role` w poprzednim poleceniu konfiguruje jednostkę usługi z rolą *reader* (czytelnik), co spowoduje przyznanie dostępu tylko do ściągania do rejestru. Aby przyznać prawa dostępu do wypychania i ściągania, należy zmienić argument `--role` na wartość *contributor* (współautor).

Następnie przechowuj jednostkę usługi *appId* w magazynie. Jest to **nazwa użytkownika** przekazywana do usługi Azure Container Registry na potrzeby uwierzytelniania:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Utworzono magazyn Azure Key Vault, w którym są przechowywane dwa wpisy tajne:

* `$ACR_NAME-pull-usr`: identyfikator jednostki usługi do użycia jako **nazwa użytkownika** rejestru kontenerów.
* `$ACR_NAME-pull-pwd`: hasło jednostki usługi do użycia jako **hasło** rejestru kontenerów.

Teraz możesz odwoływać się do tych wpisów tajnych według nazwy, gdy Ty lub Twoje aplikacje i usługi ściągają obrazy z rejestru.

### <a name="deploy-container-with-azure-cli"></a>Wdrażanie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

Teraz, gdy poświadczenia główne są przechowywane jako wpisy tajne usługi Azure Key Vault, aplikacje i usługi mogą używać ich do uzyskiwania dostępu do rejestru prywatnego.

Wykonaj polecenie [az container create][az-container-create] w celu wdrożenia wystąpienia kontenera. Polecenie używa poświadczeń jednostki usługi przechowywanych w usłudze Azure Key Vault do uwierzytelniania rejestru kontenerów.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Wartość `--dns-name-label` musi być unikatowa na platformie Azure, dlatego poprzednie polecenie dołącza nazwę rejestru kontenerów do etykiety nazwy DNS kontenera. Dane wyjściowe polecenia wyświetlają w pełni kwalifikowaną nazwę domeny (FQDN) kontenera, na przykład:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Zanotuj nazwę FQDN kontenera. Będzie ona używana w następnej sekcji.

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Aby obejrzeć proces uruchamiania kontenera, użyj polecenia [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

Dane wyjściowe `az container attach` najpierw wyświetlają stan kontenera podczas ściągania obrazu i uruchamiania, a następnie wiążą elementy STDOUT i STDERR konsoli lokalnej z tym kontenerem.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Po wyświetleniu elementu `Server running at http://localhost:80` przejdź do nazwy FQDN kontenera w przeglądarce, aby zobaczyć działającą aplikację:

![Zrzut ekranu przedstawiający przykładową aplikację renderowaną w przeglądarce][quick-build-02-browser]

Aby odłączyć konsolę od kontenera, wybierz pozycję `Control+C`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zatrzymaj wystąpienie kontenera przy użyciu polecenia [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Aby usunąć *wszystkie* zasoby, które zostały utworzone w tej serii samouczków, w tym rejestr kontenerów, wystąpienie kontenera, magazyn kluczy i jednostkę usługi, należy zastosować poniższe polecenia. Te zasoby są jednak używane w [następnym samouczku](container-registry-tutorial-build-task.md) w serii, więc warto je zachować w przypadku bezpośredniego przechodzenia do następnego samouczka.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Następne kroki

Teraz, po przetestowaniu pętli wewnętrznej przy użyciu szybkiej kompilacji, skonfiguruj **zadanie kompilacji** w celu wyzwolenia kompilacji obrazów kontenera podczas zatwierdzania kodu źródłowego w repozytorium usługi Git:

> [!div class="nextstepaction"]
> [Wyzwalanie kompilacji automatycznych przy użyciu zadań kompilacji](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
