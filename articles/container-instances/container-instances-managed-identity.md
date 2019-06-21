---
title: Tożsamość zarządzana za pomocą usługi Azure Container Instances
description: Informacje o sposobie korzystania z zarządzanych tożsamości do uwierzytelniania za pomocą innych usług platformy Azure z usługi Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: c14c3aaf2a5d648572fdc251540264e8057a00f9
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144318"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Jak używać zarządzanych tożsamości z usługi Azure Container Instances

Użyj [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) do uruchamiania kodu w usłudze Azure Container Instances, która współdziała z innymi usługami platformy Azure — bez zachowania żadnych wpisów tajnych lub poświadczeń w kodzie. Ta funkcja zapewnia wdrożenia usługi Azure Container Instances za pomocą automatycznie zarządzanych tożsamości w usłudze Azure Active Directory.

W tym artykule dowiesz się więcej na temat zarządzanych tożsamości w usłudze Azure Container Instances oraz:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanych przez użytkownika lub przypisany systemowo w grupie kontenerów
> * Udzielanie tożsamości dostępu do usługi Azure Key Vault
> * Umożliwia dostęp do usługi Key Vault z uruchomionego kontenera tożsamości zarządzanej

Dostosuj przykłady do włączenia i używania tożsamości w usłudze Azure Container Instances, aby uzyskać dostęp do innych usług platformy Azure. Te przykłady są interaktywne. Jednak w praktyce obrazów kontenerów może uruchomić kod, aby uzyskać dostęp do usług platformy Azure.

> [!NOTE]
> Obecnie nie można użyć tożsamość zarządzaną w grupie kontenerów wdrożonych w sieci wirtualnej.

## <a name="why-use-a-managed-identity"></a>Dlaczego warto korzystać z tożsamości zarządzanej?

Umożliwia uwierzytelnienie przez dowolny tożsamość zarządzaną w kontenerze uruchomiona [usługa, która obsługuje uwierzytelnianie za pomocą usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez konieczności zarządzania poświadczeniami w kodzie kontenera. Dla usług, które nie obsługują uwierzytelnianie w usłudze AD można przechowywać wpisy tajne w usłudze Azure Key Vault i umożliwiają dostęp do usługi Key Vault można pobrać poświadczeń tożsamości zarządzanej. Aby uzyskać więcej informacji o korzystaniu z tożsamości zarządzanej, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna. Obecnie zarządzanych tożsamości są obsługiwane tylko w wystąpieniach kontenera systemu Linux.
>  

### <a name="enable-a-managed-identity"></a>Włączanie tożsamości zarządzanej

 W usłudze Azure Container Instances zarządzanych tożsamości dla zasobów platformy Azure są obsługiwane począwszy od wersji 2018-10-01 interfejsu API REST i odpowiednich zestawów SDK i narzędzi. Podczas tworzenia grupy kontenerów, Włącz co najmniej jeden z zarządzanych tożsamości, ustawiając [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) właściwości. Można również włączyć lub zaktualizować zarządzanych tożsamości, po grupy kontenerów jest uruchomiony; grupy kontenerów o ponowne uruchomienie powoduje, że każda z tych akcji. Aby ustawienie tożsamości bez względu na grupie nowego lub istniejącego kontenera, należy użyć wiersza polecenia platformy Azure, szablon usługi Resource Manager lub pliku YAML. 

Usługa Azure Container Instances obsługuje oba rodzaje zarządzanych tożsamości platformy Azure: użytkownik przypisany i przypisana przez system. W grupie kontenerów aby umożliwić tożsamości przypisanych przez system, co najmniej jeden tożsamości przypisanych przez użytkownika lub oba rodzaje tożsamości. 

* A **użytkownik przypisany** tożsamość zarządzana zostanie utworzona jako autonomiczny zasobów platformy Azure w ramach dzierżawy usługi Azure AD, która jest zaufana dla używanej subskrypcji. Po utworzeniu tożsamości tożsamości można przypisać do co najmniej jednego zasobu platformy Azure (w usłudze Azure Container Instances lub innych usług platformy Azure). Cykl życia tożsamości przypisanych przez użytkownika zarządzanym oddzielnie z cyklem życia grup kontenerów lub innych zasobów usługi, do których jest przypisany. To zachowanie jest szczególnie przydatne w usłudze Azure Container Instances. Ponieważ tożsamość wykracza poza okres istnienia grupy kontenerów, możesz użyć go ponownie oraz inne ustawienia standardowe zapewnienie wysokiej powtarzalne wdrożenia grupy kontenerów.

* A **przypisany systemowo** tożsamości zarządzanej jest włączone bezpośrednio w grupie kontenerów w usłudze Azure Container Instances. Gdy jest włączone, platforma Azure tworzy tożsamość grupy w dzierżawie usługi Azure AD, która jest zaufana subskrypcji wystąpienia. Po utworzeniu tożsamości poświadczenia są aprowizowane w każdym kontenerze w grupie kontenerów. Cykl życia tożsamości przypisanych przez system jest bezpośrednio związany grupy kontenerów, który został włączony na. Usunięcie grupy usługi Azure automatycznie oczyszcza poświadczeń i tożsamości w usłudze Azure AD.

### <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

Aby korzystać z tożsamości zarządzanej, tożsamość musi początkowo można udzielić dostępu do co najmniej jeden zasób usługi platformy Azure (np. aplikacji sieci Web, usługi Key Vault lub konta magazynu) w ramach subskrypcji. Dostęp do zasobów platformy Azure z uruchomionego kontenera, konieczne jest uzyskanie kodu *token dostępu* z punktu końcowego usługi Azure AD. Następnie kod wysyła ten token dostępu w wywołaniu to usługa, która obsługuje uwierzytelnianie za pomocą usługi Azure AD. 

Za pomocą tożsamości zarządzanej w kontenerze uruchomiona zasadniczo jest taka sama, jak przy użyciu tożsamości w Maszynie wirtualnej platformy Azure. Zobacz wskazówki dotyczące maszyny Wirtualnej dla przy użyciu [tokenu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [programu Azure PowerShell lub wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), lub [zestawami SDK Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Przykłady w niniejszym artykule umożliwiają tożsamość zarządzaną w usłudze Azure Container Instances dostęp do klucza tajnego z usługi Azure Key Vault. 

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w *eastus* lokalizacji z następującym [Tworzenie grupy az](/cli/azure/group?view=azure-cli-latest#az-group-create) polecenia:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj [tworzenie az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) polecenie, aby utworzyć usługę Key Vault. Koniecznie Określ unikatową nazwę usługi Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store przykładowy wpis tajny w usłudze Key Vault przy użyciu [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) polecenia:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Wykonaj poniższe przykłady do dostępu do usługi Key Vault, przy użyciu przypisanych przez użytkownika lub przypisany systemowo tożsamość zarządzaną w usłudze Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Przykład 1: Umożliwia dostęp do usługi Azure Key Vault o tożsamości przypisanych przez użytkownika

### <a name="create-an-identity"></a>Utwórz tożsamość

Najpierw utwórz tożsamość w subskrypcji przy użyciu [Utwórz tożsamość az](/cli/azure/identity?view=azure-cli-latest#az-identity-create) polecenia. Możesz użyć tej samej grupie zasobów, które są używane do tworzenia usługi Key Vault lub użyć innego.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Aby używać tożsamości w poniższych krokach, należy użyć [Pokaż tożsamości az](/cli/azure/identity?view=azure-cli-latest#az-identity-show) polecenie, aby przechowywać identyfikator jednostki usługi tożsamości i identyfikator zasobu w zmiennych.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanych przez użytkownika w grupie kontenerów

Uruchom następujące polecenie [utworzyć kontener az](/cli/azure/container?view=azure-cli-latest#az-container-create) polecenie, aby utworzyć wystąpienie kontenera oparte na systemie Ubuntu Server. W poniższym przykładzie przedstawiono grupy jednego kontenera, która służy do interaktywnie korzystać z innymi usługami platformy Azure. `--assign-identity` Parametr przekazuje zarządzanych tożsamości przypisanych przez użytkownika do grupy. Polecenie długotrwałych śledzi działanie kontenera. W tym przykładzie użyto tej samej grupie zasobów, które są użyte do utworzenia magazynu kluczy, ale można określić inne konto.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź stan za pomocą [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) polecenia.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

`identity` Sekcji w danych wyjściowych podobny do poniższego, przedstawiający tożsamość jest ustawiona w grupie kontenerów. `principalID` w obszarze `userAssignedIdentities` jest jednostki usługi tożsamości został utworzony w usłudze Azure Active Directory:

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udzielanie tożsamości przypisanych przez użytkownika dostępu do usługi Key Vault

Uruchom następujące polecenie [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) polecenie, aby ustawić zasady dostępu w usłudze Key Vault. Poniższy przykład umożliwia skonfigurowanie tożsamości przypisanych przez użytkownika do pobrania wpisów tajnych z usługi Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Użyj tożsamości przypisanych przez użytkownika, aby pobrać wpisu tajnego z usługi Key Vault

Teraz można użyć tożsamości zarządzanej dostępu do Key Vault w ramach uruchomionego wystąpienia kontenera. W tym przykładzie najpierw uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu uwierzytelniania w usłudze Key Vault przy użyciu usługi Azure Active Directory, uruchom następujące polecenie:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Dane wyjściowe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Do przechowywania tokenu dostępu w zmiennej używanej w kolejnych poleceniach do uwierzytelniania, uruchom następujące polecenie:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teraz można używać tokenu dostępu do uwierzytelniania w usłudze Key Vault i odczytu wpisu tajnego. Pamiętaj podstawić nazwę magazynu kluczy w adresie URL ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź będzie wyglądać podobnie do poniższego, przedstawiający klucza tajnego. W kodzie czy przeanalizować te dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj klucza tajnego w kolejnych operacji dostępu do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Przykład 2: Użyj tożsamości przypisanych przez system dostępu do usługi Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanych przez system w grupie kontenerów

Uruchom następujące polecenie [utworzyć kontener az](/cli/azure/container?view=azure-cli-latest#az-container-create) polecenie, aby utworzyć wystąpienie kontenera oparte na systemie Ubuntu Server. W poniższym przykładzie przedstawiono grupy jednego kontenera, która służy do interaktywnie korzystać z innymi usługami platformy Azure. `--assign-identity` Parametr bez dodatkowych wartości umożliwia przypisany systemowo tożsamości zarządzanej w grupie. Polecenie długotrwałych śledzi działanie kontenera. W tym przykładzie użyto tej samej grupie zasobów, które są użyte do utworzenia magazynu kluczy, ale można określić inne konto.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź stan za pomocą [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) polecenia.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

`identity` Sekcji w danych wyjściowych będzie wyglądać podobnie do poniższego, pokazujący, że tożsamości przypisanych przez system jest tworzony w usłudze Azure Active Directory:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Ustaw zmienną na wartość `principalId` (identyfikator jednostki usługi) tożsamości, do użycia w kolejnych krokach.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Udzielanie dostępu do kontenera grupy do usługi Key Vault

Uruchom następujące polecenie [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) polecenie, aby ustawić zasady dostępu w usłudze Key Vault. Poniższy przykład umożliwia skonfigurowanie tożsamości zarządzanych przez system do pobrania wpisów tajnych z usługi Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Użyj tożsamości grupy kontenerów można pobrać klucza tajnego z usługi Key Vault

Teraz można użyć tożsamości zarządzanej dostępu do Key Vault w ramach uruchomionego wystąpienia kontenera. W tym przykładzie najpierw uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu uwierzytelniania w usłudze Key Vault przy użyciu usługi Azure Active Directory, uruchom następujące polecenie:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Dane wyjściowe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Do przechowywania tokenu dostępu w zmiennej używanej w kolejnych poleceniach do uwierzytelniania, uruchom następujące polecenie:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teraz można używać tokenu dostępu do uwierzytelniania w usłudze Key Vault i odczytu wpisu tajnego. Pamiętaj podstawić nazwę magazynu kluczy w adresie URL (*https:\//mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź będzie wyglądać podobnie do poniższego, przedstawiający klucza tajnego. W kodzie czy przeanalizować te dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj klucza tajnego w kolejnych operacji dostępu do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Włączanie tożsamości zarządzanej przy użyciu szablonu usługi Resource Manager

Aby włączyć tożsamość zarządzaną w grupie kontenerów przy użyciu [szablonu usługi Resource Manager](container-instances-multi-container-group.md)ustaw `identity` właściwość `Microsoft.ContainerInstance/containerGroups` obiekt z `ContainerGroupIdentity` obiektu. Następujący program fragmenty `identity` właściwości skonfigurowane dla różnych scenariuszy. Zobacz [odwołanie do szablonu usługi Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Określ `apiVersion` z `2018-10-01`.

### <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

Tożsamości przypisanych przez użytkownika jest identyfikator zasobu formularza:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Można włączyć jedną lub więcej tożsamości przypisanych przez użytkownika.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Tożsamości przypisanych przez system

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisanych przez system i użytkownika

W grupie kontenerów aby umożliwić tożsamości przypisanych przez system i jeden lub więcej tożsamości przypisanych przez użytkownika.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Włączanie tożsamości zarządzanej przy użyciu pliku YAML

Aby włączyć tożsamość zarządzaną w grupie kontenerów wdrożonych za pomocą [pliku YAML](container-instances-multi-container-yaml.md), obejmują poniższego kodu YAML.
Określ `apiVersion` z `2018-10-01`.

### <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

Tożsamości przypisanych przez użytkownika jest identyfikator zasobu formularza 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Można włączyć jedną lub więcej tożsamości przypisanych przez użytkownika.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Tożsamości przypisanych przez system

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisanych przez system i użytkownika

W grupie kontenerów aby umożliwić tożsamości przypisanych przez system i jeden lub więcej tożsamości przypisanych przez użytkownika.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono zarządzanych tożsamości w usłudze Azure Container Instances oraz sposób:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanych przez użytkownika lub przypisany systemowo w grupie kontenerów
> * Udzielanie tożsamości dostępu do usługi Azure Key Vault
> * Umożliwia dostęp do usługi Key Vault z uruchomionego kontenera tożsamości zarządzanej

* Dowiedz się więcej o [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).

* Zobacz [przykład Azure zestaw SDK dla języka Go](https://medium.com/@samkreter/c98911206328) dostęp do usługi Key Vault z usługi Azure Container Instances za pomocą tożsamości zarządzanej.
