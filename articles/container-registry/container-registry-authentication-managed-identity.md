---
title: Uwierzytelnianie przy użyciu tożsamości zarządzanej
description: Zapewnij dostęp do obrazów w rejestrze kontenerów prywatnych przy użyciu tożsamości zarządzanej platformy Azure przypisanej przez użytkownika lub przypisanej do niego.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456498"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Uwierzytelnianie w rejestrze kontenerów platformy Azure za pomocą tożsamości zarządzanej platformy Azure 

Użyj [tożsamości zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) do uwierzytelniania do rejestru kontenerów platformy Azure z innego zasobu platformy Azure, bez konieczności podawania poświadczeń rejestru lub zarządzania nimi. Na przykład skonfiguruj tożsamość zarządzaną przypisaną przez użytkownika lub przypisaną do systemu na maszynie Wirtualnej systemu Linux, aby uzyskać dostęp do obrazów kontenerów z rejestru kontenerów, tak łatwo, jak w rejestrze publicznym.

W tym artykule dowiesz się więcej na temat tożsamości zarządzanych i jak:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej do systemu na maszynie wirtualnej platformy Azure
> * Udzielanie dostępu do tożsamości do rejestru kontenerów platformy Azure
> * Użyj tożsamości zarządzanej, aby uzyskać dostęp do rejestru i wyciągnąć obraz kontenera 

Aby utworzyć zasoby platformy Azure, w tym artykule należy uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.55 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Aby skonfigurować rejestr kontenerów i wypchnąć do niego obraz kontenera, należy również zainstalować program Docker lokalnie. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Tożsamość zarządzana dla zasobów platformy Azure zapewnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Można skonfigurować [niektóre zasoby platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), w tym maszyny wirtualne, z tożsamością zarządzaną. Następnie użyj tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, bez przekazywania poświadczeń w kodzie lub skryptach.

Tożsamości zarządzane są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i utrzymują się tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie w wersji zapoznawczej.

* *Tożsamość zarządzana przez system*, która jest unikatowa dla określonego zasobu, takiego jak pojedyncza maszyna wirtualna i trwa przez cały okres istnienia tego zasobu.

Po skonfigurowaniu zasobu platformy Azure z tożsamością zarządzaną, nadaj tożsamości żądany dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. Na przykład przypisać tożsamość zarządzaną rolę z ściągnięcia, wypychania i ściągania lub inne uprawnienia do rejestru prywatnego na platformie Azure. (Aby uzyskać pełną listę ról rejestru, zobacz [Role i uprawnienia rejestru kontenerów platformy Azure](container-registry-roles.md)). Można udzielić tożsamości dostęp do jednego lub więcej zasobów.

Następnie użyj tożsamości do uwierzytelniania do dowolnej [usługi, która obsługuje uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), bez żadnych poświadczeń w kodzie. Aby użyć tożsamości, aby uzyskać dostęp do rejestru kontenerów platformy Azure z maszyny wirtualnej, uwierzytelnić się za pomocą usługi Azure Resource Manager. Wybierz sposób uwierzytelniania przy użyciu tożsamości zarządzanej, w zależności od scenariusza:

* [Programowo uzyskiwanie tokenu dostępu usługi Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) przy użyciu wywołań HTTP lub REST

* Korzystanie z zestawów [SDK platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Zaloguj się do interfejsu wiersza polecenia platformy Azure lub programu PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) przy obliczu tożsamości. 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Jeśli nie masz jeszcze rejestru kontenerów platformy Azure, utwórz rejestr i wypchnij do niego przykładowy obraz kontenera. Aby uzyskać instrukcje, zobacz [Szybki start: Tworzenie rejestru kontenerów prywatnych przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

W tym artykule `aci-helloworld:v1` przyjęto założenie, że obraz kontenera jest przechowywany w rejestrze. W przykładach użyto nazwy rejestru *myContainerRegistry*. Zamień na własne nazwy rejestru i obrazów w późniejszych krokach.

## <a name="create-a-docker-enabled-vm"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

Tworzenie maszyny wirtualnej Ubuntu z obsługą platformy Docker. Należy również zainstalować interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, pomiń ten krok, aby utworzyć maszynę wirtualną.

Wdrażanie domyślnej maszyny wirtualnej platformy Azure Ubuntu za pomocą [programu az vm create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM* w istniejącej grupie zasobów o nazwie *myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu polecenia należy wziąć `publicIpAddress` pod uwagę wyświetlane przez interfejsu wiersza polecenia platformy Azure. Ten adres służy do nawiązywać połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Instalowanie platformy Docker na maszynie Wirtualnej

Po uruchomieniu maszyny Wirtualnej nawiązać połączenie SSH z maszyną wirtualną. Zamień *publicIpAddress* na publiczny adres IP maszyny Wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować program Docker na maszynie Wirtualnej:

```bash
sudo apt install docker.io -y
```

Po instalacji uruchom następujące polecenie, aby sprawdzić, czy docker działa poprawnie na maszynie Wirtualnej:

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

Wykonaj kroki opisane w [install Azure CLI z apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. W tym artykule upewnij się, że instalujesz wersję 2.0.55 lub nowszą.

Zamknij sesję SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Przykład 1: Dostęp z tożsamością przypisaną przez użytkownika

### <a name="create-an-identity"></a>Tworzenie tożsamości

Utwórz tożsamość w ramach subskrypcji przy użyciu polecenia [tworzenia tożsamości az.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Można użyć tej samej grupy zasobów, która była wcześniej używana do utworzenia rejestru kontenera lub maszyny wirtualnej, lub innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Aby skonfigurować tożsamość w poniższych krokach, użyj polecenia [az identity show][az-identity-show] do przechowywania identyfikatora zasobu tożsamości i identyfikatora jednostki usługi w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Ponieważ identyfikator tożsamości jest potrzebny w późniejszym kroku po zalogowaniu się do interfejsu wiersza polecenia z maszyny wirtualnej, pokaż wartość:

```bash
echo $userID
```

Identyfikator jest w formularzu:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurowanie maszyny Wirtualnej przy obliczu tożsamości

Następujące polecenie [przypisywania tożsamości az vm][az-vm-identity-assign] konfiguruje maszynę wirtualną platformy Docker z tożsamością przypisaną przez użytkownika:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie dostępu do tożsamości do rejestru kontenerów

Teraz skonfiguruj tożsamość, aby uzyskać dostęp do rejestru kontenerów. Najpierw użyj polecenia [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Polecenie [tworzenia przypisania roli az][az-role-assignment-create] umożliwia przypisanie roli AcrPull do rejestru. Ta rola zapewnia [uprawnienia ściągania](container-registry-roles.md) do rejestru. Aby zapewnić uprawnienia ściągania i wypychania, przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Uzyskiwanie dostępu do rejestru za pomocą tożsamości

SSH do maszyny wirtualnej platformy Docker, która jest skonfigurowana z tożsamością. Uruchom następujące polecenia interfejsu wiersza polecenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure zainstalowanego na maszynie Wirtualnej.

Najpierw uwierzytelnij się w interfejsie wiersza polecenia platformy Azure za pomocą [logowania az][az-login], przy użyciu tożsamości skonfigurowane na maszynie Wirtualnej. Dla `<userID>`, zastąpić identyfikator tożsamości pobranej w poprzednim kroku. 

```azurecli
az login --identity --username <userID>
```

Następnie uwierzytelnić się w rejestrze z [az acr logowania][az-acr-login]. W przypadku korzystania z tego polecenia interfejsu wiersza polecenia `az login` używa tokenu usługi Active Directory utworzonego podczas pracy w celu bezproblemowego uwierzytelnienia sesji w rejestrze kontenerów. (W zależności od konfiguracji maszyny Wirtualnej może być konieczne uruchomienie tego `sudo`polecenia i poleceń docker za pomocą .)

```azurecli
az acr login --name myContainerRegistry
```

Powinien zostać `Login succeeded` wyświetlony komunikat. Następnie można `docker` uruchamiać polecenia bez podawania poświadczeń. Na przykład uruchom [docker pull,][docker-pull] aby wyciągnąć `aci-helloworld:v1` obraz, określając nazwę serwera logowania rejestru. Nazwa serwera logowania składa się z nazwy rejestru kontenera `.azurecr.io` (wszystkie `mycontainerregistry.azurecr.io`małe litery), po której następuje - na przykład.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Przykład 2: Dostęp z tożsamością przypisaną do systemu

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurowanie maszyny wirtualnej przy obliczu tożsamości zarządzanej przez system

Następujące polecenie [przypisywania tożsamości az vm][az-vm-identity-assign] konfiguruje maszynę wirtualną platformy Docker z tożsamością przypisaną do systemu:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Użyj polecenia [az vm show,][az-vm-show] aby ustawić `principalId` zmienną na wartość (identyfikator jednostki usługi) tożsamości maszyny Wirtualnej, aby użyć w późniejszych krokach.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie dostępu do tożsamości do rejestru kontenerów

Teraz skonfiguruj tożsamość, aby uzyskać dostęp do rejestru kontenerów. Najpierw użyj polecenia [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj polecenia [tworzenia przypisania roli az,][az-role-assignment-create] aby przypisać rolę AcrPull do tożsamości. Ta rola zapewnia [uprawnienia ściągania](container-registry-roles.md) do rejestru. Aby zapewnić uprawnienia ściągania i wypychania, przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Uzyskiwanie dostępu do rejestru za pomocą tożsamości

SSH do maszyny wirtualnej platformy Docker, która jest skonfigurowana z tożsamością. Uruchom następujące polecenia interfejsu wiersza polecenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure zainstalowanego na maszynie Wirtualnej.

Najpierw uwierzytelnij interfejs wiersza polecenia platformy Azure za pomocą [logowania az][az-login], przy użyciu tożsamości przypisanej do systemu na maszynie Wirtualnej.

```azurecli
az login --identity
```

Następnie uwierzytelnić się w rejestrze z [az acr logowania][az-acr-login]. W przypadku korzystania z tego polecenia interfejsu wiersza polecenia `az login` używa tokenu usługi Active Directory utworzonego podczas pracy w celu bezproblemowego uwierzytelnienia sesji w rejestrze kontenerów. (W zależności od konfiguracji maszyny Wirtualnej może być konieczne uruchomienie tego `sudo`polecenia i poleceń docker za pomocą .)

```azurecli
az acr login --name myContainerRegistry
```

Powinien zostać `Login succeeded` wyświetlony komunikat. Następnie można `docker` uruchamiać polecenia bez podawania poświadczeń. Na przykład uruchom [docker pull,][docker-pull] aby wyciągnąć `aci-helloworld:v1` obraz, określając nazwę serwera logowania rejestru. Nazwa serwera logowania składa się z nazwy rejestru kontenera `.azurecr.io` (wszystkie `mycontainerregistry.azurecr.io`małe litery), po której następuje - na przykład.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o używaniu tożsamości zarządzanych za pomocą rejestru kontenerów platformy Azure i jak:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej do systemu na maszynie Wirtualnej platformy Azure
> * Udzielanie dostępu do tożsamości do rejestru kontenerów platformy Azure
> * Użyj tożsamości zarządzanej, aby uzyskać dostęp do rejestru i wyciągnąć obraz kontenera

* Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).


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
