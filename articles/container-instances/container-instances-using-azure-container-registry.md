---
title: Wdrażanie usługi Azure Container instances z usługi Azure Container Registry
description: Dowiedz się, jak wdrożyć kontenery w usłudze Azure Container Instances za pomocą obrazów kontenerów w usłudze Azure container registry.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b596af8ae9fbbaee6964622df44d316a11582cb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686159"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Wdrażanie usługi Azure Container instances z usługi Azure Container Registry

[Usługa Azure Container Registry](../container-registry/container-registry-intro.md) jest oparta na platformie Azure, zarządzana Usługa rejestru kontenerów używany do przechowywania prywatnych obrazów kontenerów platformy Docker. W tym artykule opisano, jak wdrożyć obrazy kontenera przechowywany w usłudze Azure container registry do usługi Azure Container Instances.

## <a name="prerequisites"></a>Wymagania wstępne

**Usługa Azure container registry**: Potrzebna usługa Azure container registry — i co najmniej jeden kontener obrazów w rejestrze — wykonanie czynności opisanych w tym artykule. Jeśli konieczne jest zarejestrowanie, zobacz [Tworzenie rejestru kontenerów przy użyciu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Interfejs wiersza polecenia platformy Azure**: Przykłady wiersza polecenia w tym artykule korzystają [wiersza polecenia platformy Azure](/cli/azure/) i formatowania do powłoki Bash. Możesz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lokalnie, lub użyj [usługi Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurowanie uwierzytelniania rejestru

W żadnym scenariuszu produkcyjnym, należy podać dostępu do usługi Azure container registry przy użyciu [jednostki usług](../container-registry/container-registry-auth-service-principal.md). Nazwy główne usług umożliwiają dostarczenie [kontroli dostępu opartej na rolach](../container-registry/container-registry-roles.md) obrazów kontenera. Na przykład można skonfigurować jednostkę usługi z dostępem tylko do ściągania do rejestru.

W poniższej sekcji Tworzenie usługi Azure key vault i nazwy głównej usługi i przechowywać poświadczenia nazwy głównej usługi w magazynie. 

### <a name="create-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu w usłudze [Azure Key Vault](../key-vault/key-vault-overview.md), utwórz go przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu poniższych poleceń.

Aktualizacja `RES_GROUP` zmiennej z nazwą istniejącej grupy zasobów, w której chcesz utworzyć magazyn kluczy i `ACR_NAME` nazwą rejestru kontenerów. Określ nazwę dla nowego magazynu kluczy w `AKV_NAME`. Nazwa magazynu musi być unikatowa w obrębie platformy Azure i musi być 3 do 24 znaków alfanumerycznych oraz zaczynać się literą, kończyć literą lub cyfrą i nie może zawierać występujących po sobie łączników.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Tworzenie jednostki usługi i poświadczenia magazynu

Musisz teraz utworzyć jednostkę usługi i przechowywać jej poświadczenia w magazynie kluczy.

Następujące polecenie używa [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] Aby utworzyć jednostkę usługi i [az keyvault secret set] [ az-keyvault-secret-set] do przechowywania jednostki usługi **hasło** w magazynie.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Argument `--role` w poprzednim poleceniu konfiguruje jednostkę usługi z rolą *acrpull*, co spowoduje przyznanie dostępu tylko do ściągania do rejestru. Aby przyznać prawa dostępu do wypychania i ściągania, należy zmienić argument `--role` na wartość *acrpush*.

Następnie przechowywać nazwy głównej usługi *appId* w magazynie, który jest **username** są przekazywane do usługi Azure Container Registry do uwierzytelniania.

```azurecli
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

## <a name="deploy-container-with-azure-cli"></a>Wdrażanie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

Teraz, poświadczenia nazwy głównej usługi są przechowywane w usłudze Azure Key Vault, klucze tajne, aplikacji i usług umożliwia im dostęp do prywatnego rejestru.

Najpierw uzyskać nazwę serwera logowania rejestru za pomocą [az acr show] [ az-acr-show] polecenia. Nazwa serwera logowania to wszystkie małe litery i podobne do `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Wykonaj polecenie [az container create][az-container-create] w celu wdrożenia wystąpienia kontenera. Polecenie używa poświadczenia nazwy głównej usługi, przechowywane w usłudze Azure Key Vault do uwierzytelniania rejestru kontenerów i zakłada się, został wcześniej wypchnięty [aci-helloworld](container-instances-quickstart.md) obrazu do rejestru. Aktualizacja `--image` wartość, jeśli chcesz użyć innego obrazu z rejestru.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` Wartość musi być unikatowa na platformie Azure, więc poprzednie polecenie dołącza losową liczbę do etykiety nazwy DNS dla kontenera. Dane wyjściowe polecenia wyświetlają w pełni kwalifikowaną nazwę domeny (FQDN) kontenera, na przykład:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Gdy kontener został uruchomiony pomyślnie, można przejść do jego nazwy FQDN w przeglądarce, aby sprawdzić, czy aplikacja została pomyślnie uruchomiona.

## <a name="deploy-with-azure-resource-manager-template"></a>Wdrażanie przy użyciu szablonu usługi Azure Resource Manager

Można określić właściwości usługi Azure Container Registry w szablonie usługi Azure Resource Manager, umieszczając `imageRegistryCredentials` właściwość definicja grupy kontenerów:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Aby uzyskać szczegółowe informacje dotyczące odwoływania się do hasła usługi Azure Key Vault w szablonie usługi Resource Manager, zobacz [użycia usługi Azure Key Vault do przekazywania wartości parametru secure podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Wdrażanie przy użyciu witryny Azure portal

Jeśli zachowasz obrazów kontenerów w usłudze Azure container registry, można łatwo utworzyć kontener w usłudze Azure Container Instances za pomocą witryny Azure portal. Wdróż wystąpienie kontenera z rejestru kontenerów za pomocą portalu, należy włączyć w rejestrze [konta administratora](../container-registry/container-registry-authentication.md#admin-account). Konto administratora jest przeznaczony dla jednego użytkownika, dostęp do rejestru, głównie do celów testowych. 

1. W witrynie Azure portal przejdź do rejestru kontenerów.

1. Aby upewnić się, że konto administratora jest włączone, wybierz pozycję **klucze dostępu**, a następnie w obszarze **administrator** wybierz **Włącz**.

1. Wybierz **repozytoriów**, a następnie wybierz repozytorium, które chcesz wdrożyć, kliknij prawym przyciskiem myszy tag obrazu kontenera, które chcesz wdrożyć, a następnie wybierz pozycję **uruchomienia wystąpienia**.

    !["Uruchom wystąpienie" w usłudze Azure Container Registry w witrynie Azure portal][acr-runinstance-contextmenu]

1. Wprowadź nazwę kontenera i nazwy grupy zasobów. Można również zmienić wartości domyślne, jeśli chcesz.

    ![Tworzenie menu dla usługi Azure Container Instances][acr-create-deeplink]

1. Po zakończeniu wdrażania możesz przejść do grupy kontenerów, za pomocą okienko powiadomień Aby znaleźć adres IP i inne właściwości.

    ![Widok szczegółów dla grupy kontenerów w usłudze Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania usługi Azure Container Registry, zobacz [Uwierzytelnij za pomocą usługi Azure container registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
