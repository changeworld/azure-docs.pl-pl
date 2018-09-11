---
title: Wdrażanie aplikacji usługi Service Fabric siatki z rejestru obraz kontenera prywatnych do usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację z prywatnego obrazu rejestru kontenerów usługi Service Fabric siatki, przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/20/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a2791c86512790f36477e562cb82718174df8dc3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296595"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Wdróż aplikację usługi Service Fabric siatki z prywatnego obrazu rejestru kontenerów

W tym artykule przedstawiono sposób wdrażania aplikacji usługi Azure Service Fabric siatki, która używa prywatnego obrazu rejestru kontenerów.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh

Użyj lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby zakończyć to zadanie. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh, korzystając z tych [instrukcji](service-fabric-mesh-howto-setup-cli.md).

## <a name="install-docker"></a>Instalowanie platformy Docker

#### <a name="windows-10"></a>Windows 10

Pobierz i zainstaluj najnowszą wersję aplikacji [Docker Community Edition for Windows][download-docker] do obsługi konteneryzowanych aplikacji usługi Service Fabric używanych w usłudze Service Fabric Mesh.

Podczas instalacji wybierz opcję **Use Windows containers instead of Linux containers** (Użyj kontenerów systemu Windows zamiast kontenerów systemu Linux), gdy pojawi się odpowiedni monit. 

Jeśli na maszynie nie włączono funkcji Hyper-V, w instalatorze platformy Docker pojawi się sugestia włączenia tej funkcji. Jeśli pojawi się taki monit, kliknij przycisk **OK**, aby to zrobić.

#### <a name="windows-server-2016"></a>Windows Server 2016

Jeśli nie masz włączonej roli funkcji Hyper-V, otwórz program PowerShell jako administrator i uruchom następujące polecenie, aby włączyć funkcję Hyper-V, a następnie uruchom ponownie komputer. Aby uzyskać więcej informacji, zobacz [Docker Enterprise Edition for Windows Server (Oprogramowanie Docker Enterprise Edition for Windows Server)][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Uruchom ponownie komputer.

Otwórz program PowerShell jako administrator i uruchom następujące polecenie, aby zainstalować aplikację Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Logowanie do platformy Azure i ustawić aktywną subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Tworzenie rejestru kontenerów i wypchnąć obraz do niego

Wykonaj następujące kroki, aby utworzyć rejestr Azure Container Registry.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Użyj następującego polecenia, aby utworzyć grupę zasobów o nazwie *myResourceGroup* w *eastus* lokalizacji.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Tworzenie usługi Azure container registry (ACR) wystąpienia za pomocą `az acr create` polecenia. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i zawiera 5 do 50 znaków alfanumerycznych. W poniższym przykładzie nazwa *myContainerRegistry007* jest używany. Jeśli wystąpi błąd, który nazwa rejestru jest używana, wybierz inną nazwę. Użyj nazwy wszędzie `<acrName>` pojawia się w tych instrukcjach.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po utworzeniu rejestru zostanie wyświetlone dane wyjściowe podobne do następujących:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="push-the-image-to-azure-container-registry"></a>Wypchnij obraz do usługi Azure Container Registry

Aby wypchnąć obraz do usługi Azure container registry (ACR), najpierw musisz mieć obraz kontenera. Jeśli nie masz jeszcze żadnych lokalnych obrazów kontenerów, uruchom następujące polecenie, aby ściągnąć obraz z usługi Docker Hub (może być konieczne przełączyć platformę Docker, aby pracować z obrazów systemu Linux, klikając prawym przyciskiem myszy ikonę platformy docker i wybierając **przełączyć się do kontenerów systemu Linux**).

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Zanim będzie można wypchnąć obraz do rejestru, musisz go otagować w pełni kwalifikowaną nazwą serwera logowania usługi ACR.

Uruchom następujące polecenie, aby uzyskać nazwę serwera logowania pełnego wystąpienia usługi ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Pełną nazwę serwera logowania zwracana będzie nazywany `<acrLoginServer>` w dalszej części tego artykułu.

Teraz otaguj, używając obrazu platformy docker [docker tag] [ docker-tag] polecenia. W poniższym poleceniu zastąp `<acrLoginServer>` nazwą serwera logowania zgłoszone za pomocą polecenia powyżej. Poniższy przykład tagów seabreeze/azure siatki-helloworld:1.1-alpine obrazu. Jeśli używasz innego obrazu, zastąp nazwę obrazu, w poniższym poleceniu.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Na przykład: `docker tag seabreeze/azure-mesh-helloworld:1.1-alpine myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

Zaloguj się do usługi Azure Container Registry.

```bash
az acr login -n <acrName>
```

Na przykład: `az acr login -n myContainerRegistry007`

Wypchnij obraz do rejestru kontenerów platformy azure za pomocą następującego polecenia:

```bash
docker push <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Na przykład: `docker push myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

### <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Poniższy przykład wyświetla listę repozytoriów w rejestrze. Przykładach założono, w przypadku korzystania z platformy azure — siatki-helloworld:1.1-alpine obrazu. Jeśli używasz innego obrazu zamiast jego nazwę, na którym jest używany obraz azure siatki helloworld.

```azurecli
az acr repository list --name <acrName> --output table
```
Na przykład: `az acr repository list --name myContainerRegistry007 --output table`

Dane wyjściowe:

```bash
Result
-------------------------------
seabreeze/azure-mesh-helloworld
```

Poniższy przykład wyświetla listę tagów w **siatki azure-helloworld** repozytorium.

```azurecli
az acr repository show-tags --name <acrName> --repository seabreeze/azure-mesh-helloworld --output table
```

Na przykład: `az acr repository show-tags --name myContainerRegistry007 --repository seabreeze/azure-mesh-helloworld --output table`

Dane wyjściowe:

```bash
Result
--------
1.1-alpine
```

Dane wyjściowe poprzedniego potwierdza obecność `azure-mesh-helloworld:1.1-alpine` w prywatnym rejestrze kontenerów.

## <a name="retrieve-credentials-for-the-registry"></a>Pobieranie poświadczeń rejestru

> [!IMPORTANT]
> Włączanie użytkownika administratora w usłudze Azure container registry nie jest zalecane na potrzeby scenariuszy produkcyjnych. Jest wykonywane tutaj aby zachować krótki opis tej prezentacji. W przypadku scenariuszy produkcyjnych, użyj [nazwy głównej usługi](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal) uwierzytelniania zarówno użytkowników, jak i systemu scenariuszy produkcyjnych.

Aby wdrożyć wystąpienie kontenera z rejestru, który został utworzony, musisz podać poświadczenia podczas wdrażania. Włącz administratora w swoim rejestrze za pomocą następującego polecenia:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Na przykład: `az acr update --name myContainerRegistry007 --admin-enabled true`

Pobierz rejestru nazwę serwera, nazwę użytkownika i hasło, za pomocą następujących poleceń:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Wartości podanych w tym celu przed polecenia są określone jako `<acrLoginServer>`, `<acrUserName>`, i `<acrPassword>` poniżej.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Tworzenie aplikacji i powiązanych zasobów przy użyciu następującego polecenia i podaj poświadczenia z poprzedniego kroku.

`registry-password` Parametr szablonu jest ciągiem bezpiecznym. Go nie będą wyświetlane w stan wdrożenia i `az mesh service show` poleceń.

Jeśli używasz konsoli programu Bash, uruchom następujące polecenie:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

Jeśli używasz konsoli programu PowerShell, uruchom następujące polecenie:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{'location': {'value': 'eastus'}, 'registry-server': {'value': '<acrLoginServer>'}, 'registry-username': {'value': '<acrUserName>'}, 'registry-password': {'value': '<acrPassword>'}}"
```

W ciągu kilku minut powinien zostać wyświetlony:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Otwieranie aplikacji

Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Wyświetla stronę sieci web za pomocą usługi Service Fabric siatki logo.

Polecenie wdrożenia zwraca publiczny adres IP punktu końcowego usługi. Opcjonalnie można także badać zasobu sieciowego, aby znaleźć publiczny adres IP punktu końcowego usługi. 
 
Nazwa zasobu sieci dla tej aplikacji jest `helloWorldPrivateRegistryNetwork`, pobierania informacji o nim przy użyciu następującego polecenia. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Usuń zasoby

Często usunąć zasoby, których już używasz na platformie Azure. Aby usunąć zasoby związane z tym przykładzie, należy usunąć grupę zasobów, w którym zostały wdrożone (co spowoduje usunięcie wszystkich skojarzonych z tą grupą zasobów) za pomocą następującego polecenia:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

Jeśli przełączenie Docker, aby pracować z obrazów systemu Linux na potrzeby tego ćwiczenia i musisz przejść do pracy z Windows obrazy, kliknij prawym przyciskiem myszy ikonę platformy docker, a następnie wybierz pozycję **przełączyć się do kontenerów Windows**

## <a name="next-steps"></a>Kolejne kroki

- Wyświetl przykładowej aplikacji Hello World na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Aby dowiedzieć się więcej o modelu zasobów sieci szkieletowej usług, zobacz [siatki modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/