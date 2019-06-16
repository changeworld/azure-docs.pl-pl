---
title: Uwierzytelnianie systemu Azure Container Registry za pomocą tożsamości zarządzanych
description: Zapewniają dostęp do obrazów w prywatnego rejestru kontenera za pomocą przypisanego przez użytkownika lub przypisany przez system Azure tożsamość zarządzaną.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: 728a2f8cf61bbe0691350b9de45a5fab6b90cadb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563073"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Skorzystaj z platformy Azure zarządzane tożsamości do uwierzytelniania w usłudze Azure container registry 

Użyj [tożsamości zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) do uwierzytelniania usługi Azure container registry z innego zasobu platformy Azure, bez konieczności Podaj lub zarządzać poświadczenia rejestru. Na przykład skonfigurować przypisanych przez użytkownika lub przypisany systemowo tożsamości zarządzanej maszyny wirtualnej systemu Linux do dostępu do obrazów kontenera z rejestru kontenerów, równie łatwo, jak używać rejestru publicznego.

W tym artykule dowiesz się więcej na temat zarządzanych tożsamości oraz sposób:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanych przez użytkownika lub przypisany systemowo na Maszynie wirtualnej platformy Azure
> * Udzielanie tożsamości dostępu do usługi Azure container registry
> * Umożliwia zarządzaną tożsamością dostępu do rejestru i ściąganie obrazu kontenera 

Tworzenie zasobów platformy Azure, w tym artykule wymaga uruchomienia wiersza polecenia platformy Azure w wersji 2.0.55 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Konfiguracji rejestru kontenera i wypchniesz obraz kontenera do niego, musi również mieć platformę Docker zainstalowaną lokalnie. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Dlaczego warto korzystać z tożsamości zarządzanej?

Tożsamość zarządzaną dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory (Azure AD). Można skonfigurować [niektórych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), tym usługi virtual machines przy użyciu tożsamości zarządzanej. Następnie użyj tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, bez przekazywania poświadczeń w kodzie albo skryptach.

Istnieją dwa typy zarządzanych tożsamości:

* *Tożsamości przypisanych przez użytkownika*, które można przypisać do wielu zasobów i utrzymują się tak długo, jak Twoja chcesz. Tożsamości przypisanych przez użytkownika są obecnie dostępne w wersji zapoznawczej.

* A *tożsamości zarządzanej przez system*, który jest unikatowy dla określonego zasobu, takich jak pojedynczy maszyny wirtualne i obowiązuje przez okres istnienia tego zasobu.

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej oferowanie tożsamości dostępu, który ma inny zasób, podobnie jak wszelkie podmiotu zabezpieczeń. Na przykład przypisać tożsamość zarządzaną rolę za pomocą ściągania, wypychania i ściągania lub inne uprawnienia do prywatnego rejestru na platformie Azure. (Aby uzyskać pełną listę ról rejestru, zobacz [uprawnienia i role usługi Azure Container Registry](container-registry-roles.md).) Możesz udzielić dostępu tożsamości do co najmniej jednego zasobu.

Następnie należy użyć do uwierzytelniania tożsamości [usługa, która obsługuje uwierzytelnianie za pomocą usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), bez żadnych poświadczeń w kodzie. Aby uzyskać dostęp do usługi Azure container registry z maszyny wirtualnej przy użyciu tożsamości, uwierzytelniać się przy użyciu usługi Azure Resource Manager. Wybierz sposób uwierzytelniania przy użyciu tożsamości zarządzanej, w zależności od scenariusza:

* [Uzyskiwanie tokenu dostępu usługi Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programowo przy użyciu wywołania HTTP lub REST

* Użyj [zestawy SDK platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Zaloguj się do wiersza polecenia platformy Azure lub programu PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) przy użyciu tożsamości. 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Jeśli nie masz jeszcze rejestr Azure container registry, tworzenia rejestru i wypchniesz obraz kontenera próbki do niego. Aby uzyskać instrukcje, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów przy użyciu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

W tym artykule założono, masz `aci-helloworld:v1` obrazu kontenera przechowywany w rejestrze. W przykładach użyto nazwą rejestru *myContainerRegistry*. Zamień na własny rejestru i nazwy obrazów w kolejnych krokach.

## <a name="create-a-docker-enabled-vm"></a>Tworzenie maszyny Wirtualnej z obsługą platformy Docker

Utwórz maszynę wirtualną Ubuntu z obsługą platformy Docker. Należy również zainstalować [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, Pomiń ten krok, aby utworzyć maszynę wirtualną.

Wdróż domyślne maszyny wirtualnej platformy Azure z systemem Ubuntu przy użyciu [tworzenie az vm][az-vm-create]. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myDockerVM* w istniejącej grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu wykonywania polecenia Zwróć uwagę na `publicIpAddress` wyświetlane w interfejsie wiersza polecenia platformy Azure. Nawiązywanie połączeń SSH z maszyną wirtualną, należy użyć tego adresu.

### <a name="install-docker-on-the-vm"></a>Zainstalowanie platformy Docker na maszynie Wirtualnej

Po uruchomieniu maszyny Wirtualnej Utwórz połączenie SSH z maszyną wirtualną. Zastąp *publicznego adresu IP* z publicznym adresem IP swojej maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować platformę Docker na maszynie Wirtualnej:

```bash
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy platformy Docker jest prawidłowo uruchomiona na maszynie Wirtualnej:

```bash
sudo docker run -it hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Postępuj zgodnie z instrukcjami w [zainstalować interfejs wiersza polecenia w usłudze Azure za pomocą narzędzia apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) do zainstalowania interfejsu wiersza polecenia platformy Azure na maszynie wirtualnej systemu Ubuntu. W tym artykule upewnij się, zainstalowanie wersji 2.0.55 lub nowszej.

Zamknij sesję SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Przykład 1: Dostęp przy użyciu tożsamości przypisanych przez użytkownika

### <a name="create-an-identity"></a>Utwórz tożsamość

Utwórz tożsamość w subskrypcji przy użyciu [Utwórz tożsamość az](/cli/azure/identity?view=azure-cli-latest#az-identity-create) polecenia. Można użyć tej samej grupie zasobów, użytego wcześniej do tworzenia rejestru kontenera lub maszyny wirtualnej lub innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Aby skonfigurować tożsamość w poniższych krokach, użyj [Pokaż tożsamości az] [ az-identity-show] polecenia do przechowywania tożsamości zasobu, identyfikator i identyfikator jednostki usługi w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Ponieważ identyfikator tożsamości w późniejszym kroku będzie potrzebny po zalogowaniu się do interfejsu wiersza polecenia z maszyny wirtualnej, Pokaż wartość:

```bash
echo $userID
```

Identyfikator ma postać:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurowanie maszyny Wirtualnej przy użyciu tożsamości

Następujące [przypisać tożsamość maszyny wirtualnej az] [ az-vm-identity-assign] polecenie konfiguruje maszyny Wirtualnej platformy Docker przy użyciu tożsamości przypisanych przez użytkownika:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie tożsamości dostępu do rejestru kontenerów

Teraz można skonfigurować tożsamość do dostępu do rejestru kontenerów. Najpierw użyj [az acr show] [ az-acr-show] polecenie, aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj [utworzenia przypisania roli az] [ az-role-assignment-create] polecenie, aby przypisać rolę AcrPull w rejestrze. Ta rola zapewnia [ściągnięcia uprawnienia](container-registry-roles.md) w rejestrze. Aby przedstawić obie ściągania i wypychania uprawnienia, Przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Użyj tożsamości dostępu do rejestru

Protokół SSH z maszyną wirtualną platformy Docker, który jest skonfigurowany przy użyciu tożsamości. Uruchom następujące polecenia wiersza polecenia platformy Azure przy użyciu wiersza polecenia platformy Azure zainstalowany na maszynie Wirtualnej.

Najpierw uwierzytelniania dla interfejsu wiersza polecenia platformy Azure za pomocą [az login][az-login], przy użyciu tożsamości skonfigurowane na maszynie Wirtualnej. Aby uzyskać `<userID>`, Zastąp identyfikator tożsamości, pobrać w poprzednim kroku. 

```azurecli
az login --identity --username <userID>
```

Następnie, uwierzytelnianie rejestru za pomocą [az acr login][az-acr-login]. Gdy to polecenie interfejsu wiersza polecenia używa tokenu usługi Active Directory, utworzony podczas wykonywania `az login` bezproblemowe uwierzytelnianie sesji za pomocą rejestru kontenerów. (W zależności od konfiguracji maszyny Wirtualnej może być konieczne uruchomienie tego polecenia i poleceń platformy docker z `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Powinien zostać wyświetlony `Login succeeded` wiadomości. Następnie możesz uruchomić `docker` polecenia bez podawania poświadczeń. Na przykład uruchomić [polecenie docker pull] [ docker-pull] do ściągnięcia `aci-helloworld:v1` obrazu, określając nazwę serwera logowania rejestru. Nazwę serwera logowania składa się z nazwy rejestru kontenerów (wszystkie małe litery) następuje `.azurecr.io` — na przykład `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Przykład 2: Dostęp przy użyciu tożsamości przypisanych przez system

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurowanie maszyny Wirtualnej za pomocą tożsamości zarządzanych przez system

Następujące [przypisać tożsamość maszyny wirtualnej az] [ az-vm-identity-assign] polecenie konfiguruje maszyny Wirtualnej platformy Docker przy użyciu tożsamości przypisanych przez system:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Użyj [az vm show] [ az-vm-show] polecenie, aby ustawić zmienną na wartość `principalId` (identyfikator jednostki usługi) tożsamości maszyny Wirtualnej do użycia w kolejnych krokach.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie tożsamości dostępu do rejestru kontenerów

Teraz można skonfigurować tożsamość do dostępu do rejestru kontenerów. Najpierw użyj [az acr show] [ az-acr-show] polecenie, aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj [utworzenia przypisania roli az] [ az-role-assignment-create] polecenie, aby przypisać rolę AcrPull do odpowiedniej tożsamości. Ta rola zapewnia [ściągnięcia uprawnienia](container-registry-roles.md) w rejestrze. Aby przedstawić obie ściągania i wypychania uprawnienia, Przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Użyj tożsamości dostępu do rejestru

Protokół SSH z maszyną wirtualną platformy Docker, który jest skonfigurowany przy użyciu tożsamości. Uruchom następujące polecenia wiersza polecenia platformy Azure przy użyciu wiersza polecenia platformy Azure zainstalowany na maszynie Wirtualnej.

Najpierw uwierzytelnić wiersza polecenia platformy Azure za pomocą [az login][az-login], przy użyciu tożsamości przypisanych przez system na maszynie Wirtualnej.

```azurecli
az login --identity
```

Następnie, uwierzytelnianie rejestru za pomocą [az acr login][az-acr-login]. Gdy to polecenie interfejsu wiersza polecenia używa tokenu usługi Active Directory, utworzony podczas wykonywania `az login` bezproblemowe uwierzytelnianie sesji za pomocą rejestru kontenerów. (W zależności od konfiguracji maszyny Wirtualnej może być konieczne uruchomienie tego polecenia i poleceń platformy docker z `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Powinien zostać wyświetlony `Login succeeded` wiadomości. Następnie możesz uruchomić `docker` polecenia bez podawania poświadczeń. Na przykład uruchomić [polecenie docker pull] [ docker-pull] do ściągnięcia `aci-helloworld:v1` obrazu, określając nazwę serwera logowania rejestru. Nazwę serwera logowania składa się z nazwy rejestru kontenerów (wszystkie małe litery) następuje `.azurecr.io` — na przykład `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono przy użyciu tożsamości zarządzanej przy użyciu usługi Azure Container Registry i jak:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanych przez użytkownika lub przypisany systemowo w Maszynie wirtualnej platformy Azure
> * Udzielanie tożsamości dostępu do usługi Azure container registry
> * Umożliwia zarządzaną tożsamością dostępu do rejestru i ściąganie obrazu kontenera

* Dowiedz się więcej o [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
