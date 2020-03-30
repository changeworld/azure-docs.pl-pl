---
title: Wdrażanie wystąpienia kontenera przez akcję GitHub
description: Konfigurowanie akcji Usługi GitHub, która automatyzuje kroki tworzenia, wypychania i wdrażania obrazu kontenera w wystąpieniach kontenera platformy Azure
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258043"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurowanie akcji GitHub w celu utworzenia wystąpienia kontenera

[Akcje GitHub](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) to zestaw funkcji w usłudze GitHub do automatyzacji przepływów pracy tworzenia oprogramowania w tym samym miejscu, w którym kod jest przechowywany i współpracować nad żądaniami ściągnięcia i problemami.

Użyj [akcji Wdrażanie w usłudze Azure Container Instances](https://github.com/azure/aci-deploy) GitHub, aby zautomatyzować wdrażanie kontenera w wystąpieniach kontenera platformy Azure. Akcja umożliwia ustawienie właściwości dla wystąpienia kontenera podobne do tych w [az container create][az-container-create] polecenia.

W tym artykule pokazano, jak skonfigurować przepływ pracy w repozytorium GitHub, który wykonuje następujące akcje:

* Tworzenie obrazu z pliku Dockerfile
* Wypychanie obrazu do rejestru kontenerów platformy Azure
* Wdrażanie obrazu kontenera w wystąpieniu kontenera platformy Azure

W tym artykule przedstawiono dwa sposoby konfigurowania przepływu pracy:

* Skonfiguruj przepływ pracy samodzielnie w repozytorium GitHub przy użyciu akcji Wdrażanie w wystąpieniach kontenerów platformy Azure i innych akcji.  
* Użyj `az container app up` polecenia w [rozszerzeniu Wdrażanie na platformie Azure](https://github.com/Azure/deploy-to-azure-cli-extension) w pliku WIERSZA POLECENIA platformy Azure. To polecenie usprawnia tworzenie kroków przepływu pracy i wdrażania usługi GitHub.

> [!IMPORTANT]
> Akcja GitHub dla wystąpień kontenerów platformy Azure jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto GitHub** — utwórz https://github.com konto, jeśli jeszcze go nie masz.
* **Interfejsu wiersza polecenia platformy Azure** — można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby wykonać kroki interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].
* **Rejestru kontenerów platformy Azure** — jeśli go nie masz, utwórz rejestr kontenerów platformy Azure w warstwie Podstawowa przy użyciu [interfejsu wiersza polecenia platformy Azure,](../container-registry/container-registry-get-started-azure-cli.md) [portalu Azure](../container-registry/container-registry-get-started-portal.md)lub innych metod. Zanotuj grupę zasobów używaną do wdrożenia, która jest używana dla przepływu pracy Usługi GitHub.

## <a name="set-up-repo"></a>Konfigurowanie repozytorium

* Przykłady w tym artykule użyj gitHub, aby rozwidlić następujące repozytorium:https://github.com/Azure-Samples/acr-build-helloworld-node

  To repozytorium zawiera pliki dockerfile i źródłowe do tworzenia obrazu kontenera małej aplikacji sieci web.

  ![Zrzut ekranu przedstawiający przycisk rozwidlenia (wyróżniony) w serwisie GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Upewnij się, że akcje są włączone dla repozytorium. Przejdź do rozwidlonego repozytorium i wybierz pozycję**Akcje** **ustawień** > . W **obszarze Akcje uprawnienia**upewnij się, że dla tego repozytorium jest zaznaczone opcję Włącz akcje lokalne i zewnętrzne dla tego **repozytorium.**

## <a name="configure-github-workflow"></a>Konfigurowanie przepływu pracy usługi GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Tworzenie jednostki usługi dla uwierzytelniania platformy Azure

W przepływie pracy Usługi GitHub należy podać poświadczenia platformy Azure, aby uwierzytelnić się w interfejsu wiersza polecenia platformy Azure. Poniższy przykład tworzy jednostkę usługi z roli współautora o zakresie do grupy zasobów dla rejestru kontenerów.

Najpierw uzyskaj identyfikator zasobu grupy zasobów. Zastąp nazwę grupy [następującym poleceniem az group show:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Użyj [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] aby utworzyć jednostkę usługi:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Dane wyjściowe są podobne do następujących:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Zapisz dane wyjściowe JSON, ponieważ jest on używany w późniejszym kroku. Ponadto należy wziąć `clientId`pod uwagę , które należy zaktualizować jednostki usługi w następnej sekcji.

### <a name="update-service-principal-for-registry-authentication"></a>Aktualizacja jednostki usługi do uwierzytelniania rejestru

Zaktualizuj poświadczenia jednostki usługi platformy Azure, aby zezwolić na uprawnienia wypychania i ściągania w rejestrze kontenerów. Ten krok umożliwia przepływowi pracy usługi GitHub użycie jednostki usługi do [uwierzytelniania za pomocą rejestru kontenerów.](../container-registry/container-registry-auth-service-principal.md) 

Pobierz identyfikator zasobu rejestru kontenerów. Zastąp nazwę rejestru w następującym poleceniu [az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Użyj [az przypisania roli utworzyć][az-role-assignment-create] przypisać AcrPush roli, która daje push i ściągać dostęp do rejestru. Zastąp identyfikator klienta jednostki usługi:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Zapisywanie poświadczeń w repozytorium Usługi GitHub

1. W interfejsie użytkownika gitHub przejdź do rozwidlonego repozytorium i wybierz pozycję **Ustawienia** > **wpisowe.** 

1. Wybierz **pozycję Dodaj nowy klucz tajny,** aby dodać następujące wpisy tajne:

|Wpis tajny  |Wartość  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Całe dane wyjściowe JSON z tworzenia jednostki usługi |
|`REGISTRY_LOGIN_SERVER`   | Nazwa serwera logowania rejestru (wszystkie małe litery). Przykład: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  Dane `clientId` wyjściowe JSON z tworzenia jednostki usługi       |
|`REGISTRY_PASSWORD`     |  Dane `clientSecret` wyjściowe JSON z tworzenia jednostki usługi |
| `RESOURCE_GROUP` | Nazwa grupy zasobów użytej do zakresu jednostki usługi |

### <a name="create-workflow-file"></a>Tworzenie pliku przepływu pracy

1. W interfejsie użytkownika github wybierz pozycję **Akcje** > **Nowy przepływ pracy**.
1. **Wybierz pozycję Skonfiguruj przepływ pracy samodzielnie**.
1. W **polu Edytuj nowy plik**wklej następującą zawartość YAML, aby zastąpić przykładowy kod. Zaakceptuj domyślną nazwę `main.yml`pliku lub podaj wybraną nazwę pliku.
1. Wybierz **przycisk Rozpocznij zatwierdzanie,** opcjonalnie podaj krótkie i rozszerzone opisy zatwierdzenia, a następnie wybierz **pozycję Zatwierdź nowy plik**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Sprawdzanie poprawności przepływu pracy

Po zatwierdzeniu pliku przepływu pracy przepływ pracy jest wyzwalany. Aby przejrzeć postęp przepływu pracy, przejdź do **pozycji Przepływy** > **pracy**akcji . 

![Wyświetlanie postępu przepływu pracy](./media/container-instances-github-action/github-action-progress.png)

Zobacz [Zarządzanie przebiegiem przepływu pracy,](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) aby uzyskać informacje o wyświetlaniu stanu i wyników każdego kroku w przepływie pracy.

Po zakończeniu przepływu pracy, uzyskać informacje o wystąpienie kontenera o nazwie *aci-sampleapp,* uruchamiając [polecenie az container show.][az-container-show] Zastąp nazwę grupy zasobów: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Dane wyjściowe są podobne do następujących:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Po zainicjowaniu obsługi administracyjnej wystąpienia przejdź do nazwy FQDN kontenera w przeglądarce, aby wyświetlić uruchomiającą aplikację sieci web.

![Uruchamianie aplikacji internetowej w przeglądarce](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Użyj rozszerzenia Wdrażanie na platformie Azure

Alternatywnie należy użyć [rozszerzenia Wdrażanie na platformie Azure](https://github.com/Azure/deploy-to-azure-cli-extension) w platformie Azure CLI, aby skonfigurować przepływ pracy. Polecenie `az container app up` w rozszerzeniu pobiera parametry wejściowe od ciebie, aby skonfigurować przepływ pracy do wdrożenia w instancjach kontenera platformy Azure. 

Przepływ pracy utworzony przez platformę Azure CLI jest podobny do przepływu pracy, który można [utworzyć ręcznie za pomocą gitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Dodatkowe warunki wstępne

Oprócz wymagań [wstępnych](#prerequisites) i konfiguracji [repozytorium](#set-up-repo) dla tego scenariusza, należy zainstalować rozszerzenie Wdrażanie na **platformie Azure** dla interfejsu wiersza polecenia platformy Azure.

Uruchom polecenie [dodaj rozszerzenie az,][az-extension-add] aby zainstalować rozszerzenie:

```azurecli
az extension add \
  --name deploy-to-azure
```

Aby uzyskać informacje dotyczące znajdowania, instalowania i zarządzania rozszerzeniami, zobacz [Używanie rozszerzeń z platformą Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Uruchom polecenie `az container app up`

Aby uruchomić polecenie [az container app up,][az-container-app-up] podaj co najmniej:

* Nazwa rejestru kontenerów platformy Azure, na przykład *myregistry*
* Adres URL repozytorium GitHub, na przykład,`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Przykładowe polecenie:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Postęp polecenia

* Po wyświetleniu monitu podaj poświadczenia usługi GitHub lub podaj [token dostępu osobistego Usługi GitHub](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT), który ma *zakresy repozytoriów* i *użytkowników* do uwierzytelniania w rejestrze. Jeśli podasz poświadczenia GitHub, polecenie tworzy PAT dla Ciebie.

* Polecenie tworzy wpisy tajne repozytorium dla przepływu pracy:

  * Poświadczenia jednostki usługi dla interfejsu wiersza polecenia platformy Azure
  * Poświadczenia dostępu do rejestru kontenerów platformy Azure

* Po zatwierdzeniu przez polecenie pliku przepływu pracy do repozytorium przepływ pracy jest wyzwalany. 

Dane wyjściowe są podobne do następujących:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Sprawdzanie poprawności przepływu pracy

Przepływ pracy wdraża wystąpienie kontenera platformy Azure o podstawowej nazwie repozytorium GitHub, w tym przypadku *acr-build-helloworld-node*. W przeglądarce możesz przejść do łącza, aby wyświetlić uruchomiającą aplikację internetową. Jeśli aplikacja nasłuchuje na porcie innym niż 8080, określ to w adresie URL.

Aby wyświetlić stan przepływu pracy i wyniki każdego kroku w interfejsie użytkownika usługi GitHub, zobacz [Zarządzanie przebiegiem przepływu pracy](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zatrzymaj wystąpienie kontenera przy użyciu polecenia [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Aby usunąć grupę zasobów i wszystkie zasoby w niej, uruchom polecenie [delete grupy az:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

Przeglądaj witrynę [Marketplace GitHub,](https://github.com/marketplace?type=actions) aby uzyskać więcej działań w celu zautomatyzowania przepływu pracy programistycznego


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
