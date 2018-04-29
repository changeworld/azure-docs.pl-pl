---
title: Wdrażanie do wystąpień kontenera platformy Azure z rejestru kontenera platformy Azure
description: Dowiedz się, jak wdrażanie kontenerów w wystąpień kontenera platformy Azure przy użyciu obrazów kontenera w rejestrze kontenera platformy Azure.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7a7d2aa61f25bc4782c6a1a6744e329935477f8c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Wdrażanie do wystąpień kontenera platformy Azure z rejestru kontenera platformy Azure

Rejestr kontenera Azure jest Azure, prywatnego rejestru dla obrazów kontenera Docker. W tym artykule opisano sposób wdrażania obrazów kontenera przechowywane w rejestrze kontenera platformy Azure do wystąpień kontenera platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

**Azure rejestru kontenera**: wystarczy rejestru kontenera platformy Azure — i co najmniej jeden kontener obrazu w rejestrze —, aby wykonać kroki opisane w tym artykule. Jeśli konieczne jest zarejestrowanie, zobacz [utworzyć kontener rejestru przy użyciu interfejsu wiersza polecenia Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: przykłady wiersza polecenia w tym artykule użyj [interfejsu wiersza polecenia Azure](/cli/azure/) i sformatowane dla powłoki Bash. Możesz [instalowanie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli) lokalnie, lub użyj [powłoki chmury Azure][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Skonfiguruj uwierzytelnianie rejestru

W żadnym scenariuszu produkcyjnym należy przedstawić dostępu do rejestru kontenera platformy Azure przy użyciu [usługi podmiotów](../container-registry/container-registry-auth-service-principal.md). Nazwy główne usług umożliwiają zapewnienia kontroli dostępu opartej na rolach obrazów kontenera. Na przykład nazwy głównej usługi można skonfigurować tylko ściąganych dostępu do rejestru.

W tej sekcji Tworzenie usługi Azure key vault i nazwy głównej usługi i przechowywania w magazynie poświadczeń nazwy głównej usługi.

### <a name="create-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu [usługi Azure Key Vault](/azure/key-vault/), utworzyć je przy użyciu wiersza polecenia platformy Azure przy użyciu następujących poleceń.

Aktualizacja `RES_GROUP` zmiennej o nazwie grupy zasobów, w którym ma zostać utworzony magazyn kluczy i `ACR_NAME` o nazwie rejestru kontenera. Określ nazwę dla nowego magazynu kluczy w `AKV_NAME`. Nazwa magazynu muszą być unikatowe w obrębie platformy Azure i musi być 3 do 24 znaków alfanumerycznych długości, zaczynać się od litery i kończyć literą lub cyfrą i nie może zawierać następujących po sobie łączników.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Tworzenie nazwy głównej usługi i przechowywanie poświadczeń

Musisz teraz utworzyć nazwy głównej usługi i przechowywać swoje poświadczenia w magazynie kluczy.

Następujące polecenie używa [az ad sp utworzyć do rbac] [ az-ad-sp-create-for-rbac] można utworzyć nazwy głównej usługi, i [az keyvault zbioru] [ az-keyvault-secret-set] do przechowywania jednostki usługi **hasło** w magazynie.

```azurecli
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

`--role` Argument w poprzednim poleceniu konfiguruje nazwę główną usługi z *czytnika* roli, która spowoduje przyznanie tylko ściąganych dostępu do rejestru. Aby przyznać zarówno wypychania i ściągania dostępu, należy zmienić `--role` argument *współautora*.

Następnie należy zapisać nazwy głównej usługi *appId* w magazynie, który jest **username** przekazać do rejestru kontenera Azure do uwierzytelniania.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Utworzeniu magazynu kluczy Azure i w niej przechowywane dwa klucze tajne:

* `$ACR_NAME-pull-usr`Identyfikator podmiotu zabezpieczeń usługi, do użycia jako rejestru kontenera **username**.
* `$ACR_NAME-pull-pwd`: Główny hasło usługi, do użycia jako rejestru kontenera **hasło**.

Teraz możesz odwoływać się tych kluczy tajnych według nazwy, gdy użytkownik lub aplikacje i usługi pobierania obrazów z rejestru.

## <a name="deploy-container-with-azure-cli"></a>Wdrażanie kontenera z wiersza polecenia platformy Azure

Teraz, poświadczenia główne są przechowywane w kluczy tajnych usługi Azure Key Vault, aplikacje i usługi można używać ich uzyskać dostępu do sieci prywatnej rejestru.

Wykonaj następujące czynności, [utworzyć kontener az] [ az-container-create] polecenie, aby wdrożyć wystąpień kontenera. Polecenie używa nazwy głównej usługi poświadczeń przechowywanych w magazynie kluczy Azure do uwierzytelniania rejestru kontenera i zakłada zostały wcześniej wypychana [aci helloworld](container-instances-quickstart.md) obrazu do rejestru. Aktualizacja `--image` wartość, jeśli chcesz użyć innego obrazu z rejestru.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` Wartość musi być unikatowa na platformie Azure, więc poprzednie polecenie dołącza liczbę losową etykieta nazwy DNS kontenera. Dane wyjściowe polecenia wyświetla kontenera w pełni kwalifikowaną nazwę domeny (FQDN), na przykład:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Po pomyślnie uruchomił kontenera, można przejść do nazwy FQDN w przeglądarce, aby sprawdzić, czy aplikacja działa prawidłowo.

## <a name="deploy-with-azure-resource-manager-template"></a>Rozmieszczanie za pomocą szablonu usługi Azure Resource Manager

Można określić właściwości rejestru kontenera platformy Azure w szablonie usługi Azure Resource Manager przez dołączenie `imageRegistryCredentials` właściwości w definicji kontenera grupy:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Aby uzyskać szczegółowe informacje dotyczące odwoływania się do kluczy tajnych usługi Azure Key Vault w szablonie usługi Resource Manager, zobacz [użycia usługi Azure Key Vault w celu Przekaż wartość parametru bezpieczne podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Rozmieszczanie za pomocą portalu Azure

Jeśli obsługa obrazów kontenera w rejestrze kontenera platformy Azure, można łatwo utworzyć kontener w wystąpień kontenera Azure za pomocą portalu Azure.

1. W portalu Azure przejdź do kontenera rejestru.

1. Wybierz **repozytoria**, a następnie wybierz pozycję repozytorium, który chcesz wdrożyć, kliknij prawym przyciskiem myszy tag obrazu kontenera chcesz wdrożyć, a następnie wybierz **uruchomione wystąpienie**.

    !["Uruchomione wystąpienie" w rejestrze kontenera platformy Azure w portalu Azure][acr-runinstance-contextmenu]

1. Wprowadź nazwę kontenera i nazwy grupy zasobów. Jeśli chcesz, możesz również zmienić wartości domyślne.

    ![Tworzenie menu dla wystąpień kontenera platformy Azure][acr-create-deeplink]

1. Po zakończeniu wdrożenia, można przejść do kontenera grupy z okienka powiadomienia, aby znaleźć adres IP i inne właściwości.

    ![Widok szczegółów grupy kontener wystąpień kontenera platformy Azure][aci-detailsview]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o uwierzytelnianiu rejestru kontenera platformy Azure, zobacz [Uwierzytelnij z rejestru kontenera platformy Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set