---
title: Używanie tożsamości zarządzanej z Azure Container Instances
description: Dowiedz się, jak za pomocą tożsamości zarządzanej uwierzytelniać się przy użyciu innych Azure Container Instances usług platformy Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 773650e5e5e85d4a5fca0b3755f3730921cc5f2e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325938"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Jak używać tożsamości zarządzanych z Azure Container Instances

Użyj [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) , aby uruchomić kod w Azure Container Instances, który współdziała z innymi usługami platformy Azure — bez utrzymywania żadnych wpisów tajnych lub poświadczeń w kodzie. Ta funkcja udostępnia Azure Container Instances wdrożenie z automatycznie zarządzaną tożsamością w programie Azure Active Directory.

W tym artykule dowiesz się więcej na temat tożsamości zarządzanych w Azure Container Instances i:

> [!div class="checklist"]
> * Włączanie przypisanej do użytkownika lub przypisanej do systemu tożsamości w grupie kontenerów
> * Przyznaj tożsamości dostęp do Azure Key Vault
> * Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do Key Vault z działającego kontenera

Dostosuj przykłady, aby włączyć i użyć tożsamości w Azure Container Instances, aby uzyskać dostęp do innych usług platformy Azure. Te przykłady są interaktywne. Jednak w ramach tej metody obrazy kontenerów będą uruchamiały kod w celu uzyskania dostępu do usług platformy Azure.

> [!NOTE]
> Obecnie nie można używać tożsamości zarządzanej w grupie kontenerów wdrożonej w sieci wirtualnej.

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Użyj zarządzanej tożsamości w działającym kontenerze do uwierzytelniania w dowolnej [usłudze obsługującej uwierzytelnianie w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez konieczności zarządzania poświadczeniami w kodzie kontenera. W przypadku usług, które nie obsługują uwierzytelniania usługi AD, można przechowywać wpisy tajne w Azure Key Vault i używać tożsamości zarządzanej do uzyskiwania dostępu Key Vault do pobierania poświadczeń. Aby uzyskać więcej informacji o korzystaniu z tożsamości zarządzanej, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna. Obecnie zarządzane tożsamości są obsługiwane tylko w wystąpieniach kontenera systemu Linux.
>  

### <a name="enable-a-managed-identity"></a>Włącz zarządzaną tożsamość

 W Azure Container Instances zarządzane tożsamości dla zasobów platformy Azure są obsługiwane w przypadku interfejsu API REST w wersji 2018-10-01 i odpowiednich zestawów SDK i narzędzi. Podczas tworzenia grupy kontenerów należy włączyć co najmniej jedną zarządzaną tożsamość przez ustawienie właściwości [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) . Możesz również włączyć lub zaktualizować zarządzane tożsamości po uruchomieniu grupy kontenerów; Każda akcja powoduje ponowne uruchomienie grupy kontenerów. Aby ustawić tożsamości w nowej lub istniejącej grupie kontenerów, użyj interfejsu wiersza polecenia platformy Azure, szablonu Menedżer zasobów lub pliku YAML. 

Azure Container Instances obsługuje oba typy zarządzanych tożsamości platformy Azure: przypisane przez użytkownika i przypisane do systemu. W grupie kontenerów można włączyć tożsamość przypisaną do systemu, jedną lub więcej tożsamości przypisanych do użytkownika lub oba typy tożsamości. 

* Tożsamość zarządzana przypisana przez **użytkownika** jest tworzona jako autonomiczny zasób platformy Azure w dzierżawie usługi Azure AD, która jest zaufana przez używaną subskrypcję. Po utworzeniu tożsamości można przypisać ją do jednego lub większej liczby zasobów platformy Azure (w Azure Container Instances lub innych usługach platformy Azure). Cykl życia tożsamości przypisanej przez użytkownika jest zarządzany osobno od cyklu życia grup kontenerów lub innych zasobów usług, do których jest przypisany. To zachowanie jest szczególnie przydatne w Azure Container Instances. Ze względu na to, że tożsamość wykracza poza okres istnienia grupy kontenerów, można użyć jej ponownie wraz z innymi ustawieniami standardowymi, aby zapewnić wysoką powtarzalność wdrożeń grup kontenerów.

* Tożsamość zarządzana przypisana przez **system** jest włączona bezpośrednio w grupie kontenerów w Azure Container Instances. Gdy ta funkcja jest włączona, platforma Azure tworzy tożsamość grupy w dzierżawie usługi Azure AD, która jest zaufana przez subskrypcję wystąpienia. Po utworzeniu tożsamości poświadczenia są inicjowane dla każdego kontenera w grupie kontenerów. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z grupą kontenerów, w której jest włączona. Po usunięciu grupy platforma Azure automatycznie czyści poświadczenia i tożsamość w usłudze Azure AD.

### <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

Aby można było korzystać z tożsamości zarządzanej, należy początkowo udzielić dostępu do jednego lub większej liczby zasobów usługi platformy Azure (takich jak aplikacja internetowa, Key Vault lub konto magazynu) w ramach subskrypcji. Aby uzyskać dostęp do zasobów platformy Azure z działającego kontenera, kod musi uzyskać *token dostępu* z punktu końcowego usługi Azure AD. Następnie kod wysyła token dostępu w wywołaniu usługi obsługującej uwierzytelnianie w usłudze Azure AD. 

Używanie tożsamości zarządzanej w działającym kontenerze zasadniczo jest takie samo jak w przypadku używania tożsamości na maszynie wirtualnej platformy Azure. Zobacz wskazówki dotyczące maszyn wirtualnych, aby korzystać z [tokenu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell lub interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)lub [zestawów SDK platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

W przykładach w tym artykule użyto tożsamości zarządzanej w Azure Container Instances, aby uzyskać dostęp do Azure Key Vault klucza tajnego. 

Najpierw utwórz grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* przy użyciu następującego polecenia [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Aby utworzyć Key Vault, użyj polecenia [AZ Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) webmagazynion. Pamiętaj, aby określić unikatową nazwę Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Zapisz przykładowy klucz tajny w Key Vault przy użyciu polecenia AZ webstore [Secret Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Aby uzyskać dostęp do Key Vault przy użyciu tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu w programie Azure Container Instances, wykonaj następujące przykłady.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Przykład 1: Dostęp do Azure Key Vault przy użyciu tożsamości przypisanej do użytkownika

### <a name="create-an-identity"></a>Tworzenie tożsamości

Najpierw utwórz tożsamość w subskrypcji za pomocą polecenia [AZ Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) . Możesz użyć tej samej grupy zasobów używanej do tworzenia Key Vault lub użyć innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Aby użyć tożsamości w poniższych krokach, użyj polecenia [AZ Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) do przechowywania identyfikatora jednostki usługi tożsamości i identyfikatora zasobu w zmiennych.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej przez użytkownika w grupie kontenerów

Uruchom następujące polecenie [AZ Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) , aby utworzyć wystąpienie kontenera na podstawie serwera Ubuntu. Ten przykład zawiera grupę z pojedynczym kontenerem, za pomocą której można interaktywnie uzyskiwać dostęp do innych usług platformy Azure. `--assign-identity` Parametr przekazuje do grupy zarządzaną tożsamość przypisaną przez użytkownika. Długotrwałe polecenie utrzymuje uruchomiony kontener. W tym przykładzie użyto tej samej grupy zasobów, która została użyta do utworzenia Key Vault, ale można określić inną.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź swój stan za pomocą polecenia [AZ Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

`identity` Sekcja w danych wyjściowych wygląda podobnie do poniższego, pokazując tożsamość jest ustawiona w grupie kontenerów. `principalID` Poniżej`userAssignedIdentities` znajduje się nazwa główna usługi tożsamości utworzonej w Azure Active Directory:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Przyznaj użytkownikowi dostęp do tożsamości przypisanej do Key Vault

Uruchom następujące polecenie [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) , aby ustawić zasady dostępu dla Key Vault. Poniższy przykład umożliwia tożsamości przypisanej przez użytkownika uzyskanie wpisów tajnych z Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Użyj tożsamości przypisanej do użytkownika, aby uzyskać klucz tajny z Key Vault

Teraz można użyć tożsamości zarządzanej w celu uzyskania dostępu do Key Vault w ramach uruchomionego wystąpienia kontenera. W tym przykładzie najpierw Uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu służący do uwierzytelniania Azure Active Directory do Key Vault, uruchom następujące polecenie:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Dane wyjściowe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Aby zapisać token dostępu w zmiennej, która ma być używana w kolejnych poleceniach do uwierzytelniania, uruchom następujące polecenie:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teraz Użyj tokenu dostępu do uwierzytelniania, aby Key Vault i odczytać wpis tajny. Pamiętaj, aby zastąpić nazwę magazynu kluczy w adresie URL ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź wygląda podobnie do poniższego, pokazując wpis tajny. W kodzie można analizować te dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj wpisu tajnego w kolejnej operacji, aby uzyskać dostęp do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Przykład 2: Dostęp do Azure Key Vault za pomocą tożsamości przypisanej do systemu

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej do systemu w grupie kontenerów

Uruchom następujące polecenie [AZ Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) , aby utworzyć wystąpienie kontenera na podstawie serwera Ubuntu. Ten przykład zawiera grupę z pojedynczym kontenerem, za pomocą której można interaktywnie uzyskiwać dostęp do innych usług platformy Azure. `--assign-identity` Parametr bez dodatkowych wartości włącza do grupy tożsamość zarządzaną przez system. Długotrwałe polecenie utrzymuje uruchomiony kontener. W tym przykładzie użyto tej samej grupy zasobów, która została użyta do utworzenia Key Vault, ale można określić inną.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź swój stan za pomocą polecenia [AZ Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

`identity` Sekcja w danych wyjściowych wygląda podobnie do poniższego, pokazując, że tożsamość przypisana przez system jest tworzona w Azure Active Directory:

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

Ustaw zmienną na wartość `principalId` (identyfikator jednostki usługi) tożsamości, która będzie używana w dalszych krokach.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Przyznaj grupie kontenerów dostęp do Key Vault

Uruchom następujące polecenie [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) , aby ustawić zasady dostępu dla Key Vault. Poniższy przykład umożliwia tożsamości zarządzanej przez system uzyskanie wpisów tajnych z Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Użyj tożsamości grupy kontenerów, aby uzyskać klucz tajny z Key Vault

Teraz można użyć tożsamości zarządzanej w celu uzyskania dostępu do Key Vault w ramach uruchomionego wystąpienia kontenera. W tym przykładzie najpierw Uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu służący do uwierzytelniania Azure Active Directory do Key Vault, uruchom następujące polecenie:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Dane wyjściowe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Aby zapisać token dostępu w zmiennej, która ma być używana w kolejnych poleceniach do uwierzytelniania, uruchom następujące polecenie:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teraz Użyj tokenu dostępu do uwierzytelniania, aby Key Vault i odczytać wpis tajny. Pamiętaj, aby zastąpić nazwę magazynu kluczy adresem URL (*https:\//mykeyvault.Vault.Azure.NET/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź wygląda podobnie do poniższego, pokazując wpis tajny. W kodzie można analizować te dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj wpisu tajnego w kolejnej operacji, aby uzyskać dostęp do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Włączanie tożsamości zarządzanej przy użyciu szablonu Menedżer zasobów

Aby włączyć zarządzaną tożsamość w grupie kontenerów przy użyciu [szablonu Menedżer zasobów](container-instances-multi-container-group.md), ustaw `identity` Właściwość `Microsoft.ContainerInstance/containerGroups` obiektu `ContainerGroupIdentity` na obiekt. Poniższe fragmenty kodu pokazują `identity` Właściwość skonfigurowaną dla różnych scenariuszy. Zobacz [odwołanie do szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups). `apiVersion` Określ .`2018-10-01`

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika jest IDENTYFIKATORem zasobu w postaci:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Można włączyć co najmniej jedną tożsamość przypisaną do użytkownika.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Tożsamość przypisana przez system

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane do systemu i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisana do systemu, jak i jedną lub więcej tożsamości przypisanych do użytkownika.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Włącz zarządzaną tożsamość przy użyciu pliku YAML

Aby włączyć zarządzaną tożsamość w grupie kontenerów wdrożonej przy użyciu [pliku YAML](container-instances-multi-container-yaml.md), należy uwzględnić następujące YAML.
`apiVersion` Określ .`2018-10-01`

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika to identyfikator zasobu formularza 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Można włączyć co najmniej jedną tożsamość przypisaną do użytkownika.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Tożsamość przypisana przez system

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane do systemu i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisana do systemu, jak i jedną lub więcej tożsamości przypisanych do użytkownika.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o zarządzanych tożsamościach w Azure Container Instances i instrukcje:

> [!div class="checklist"]
> * Włączanie przypisanej do użytkownika lub przypisanej do systemu tożsamości w grupie kontenerów
> * Przyznaj tożsamości dostęp do Azure Key Vault
> * Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do Key Vault z działającego kontenera

* Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).

* Zobacz [przykład zestawu SDK platformy Azure](https://medium.com/@samkreter/c98911206328) , aby uzyskać dostęp do Key Vault z Azure Container Instances przy użyciu tożsamości zarządzanej.
