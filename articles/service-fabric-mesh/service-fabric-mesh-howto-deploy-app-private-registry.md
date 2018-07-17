---
title: Wdrażanie aplikacji z rejestru prywatnego do usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację, która używa prywatnego rejestru kontenerów do usługi Service Fabric siatki, przy użyciu wiersza polecenia platformy Azure.
services: service-fabric
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
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 0a70cd1bd8cd7df099250ca59b3f00b1cab29e5c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076301"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Wdróż aplikację usługi Service Fabric siatki z prywatnego obrazu rejestru kontenerów

W tym artykule przedstawiono sposób wdrażania aplikacji usługi Azure Service Fabric siatki, która używa prywatnego obrazu rejestru kontenerów.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie usługi Service Fabric siatki interfejsu wiersza polecenia 
Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Instalowanie modułu rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>Instalowanie platformy Docker

Instalowanie platformy Docker w celu obsługi konteneryzowanych aplikacji usługi Service Fabric używana przez usługę Service Fabric siatki.

### <a name="windows-10"></a>Windows 10

Pobierz i zainstaluj najnowszą wersję [Docker Community Edition for Windows][download-docker]. 

Podczas instalacji należy wybrać **kontenery Windows użyj zamiast kontenerów systemu Linux** po wyświetleniu monitu. Konieczne będzie wówczas Wyloguj się i zaloguj się ponownie. Po zalogowaniu się ponownie, jeśli wcześniej nie włączać funkcji Hyper-V, monit może zostać umożliwia funkcji Hyper-V. Należy włączyć funkcji Hyper-V i ponownie uruchom komputer.

Po ponownym uruchomieniu komputera, Docker zostanie wyświetlony monit, aby włączyć **kontenery** funkcji, włącz go i ponownie uruchomić komputer.

### <a name="windows-server-2016"></a>Windows Server 2016

Użyj następujących poleceń programu PowerShell, aby zainstalować platformę Docker. Aby uzyskać więcej informacji, zobacz [Docker Enterprise Edition w systemie Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Uruchom ponownie komputer.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Logowanie do platformy Azure i ustawić aktywną subskrypcję:

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Tworzenie rejestru kontenerów i wypchnąć obraz do niego

Tworzenie usługi Azure Container Registry, postępując zgodnie z instrukcjami wyświetlanymi w [Tworzenie prywatnego rejestru platformy Docker na platformie Azure przy użyciu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md). Wykonaj kroki do [Zaloguj się do usługi ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) kroku. 

### <a name="push-image-to-acr"></a>Wypychanie obrazu do usługi ACR

Aby wypchnąć obraz do usługi Azure Container Registry, najpierw musisz go mieć. Jeśli nie masz jeszcze żadnych lokalnych obrazów kontenerów, uruchom następujące polecenie, aby ściągnąć istniejący obraz z usługi Docker Hub.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Zanim będzie można wypchnąć obraz do rejestru, musisz go otagować w pełni kwalifikowaną nazwą serwera logowania usługi ACR. Uruchom następujące polecenie, aby uzyskać pełną nazwę serwera logowania wystąpienia usługi ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Aby dodać tag do obrazu, użyj polecenia [docker tag][docker-tag]. Zastąp element `<acrLoginServer>` nazwą serwera logowania wystąpienia usługi ACR.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Poniższy przykład wyświetla listę repozytoriów w rejestrze:

```azurecli
az acr repository list --name <acrName> --output table
```

Dane wyjściowe:

```bash
Result
----------------
azure-mesh-helloworld
```

Poniższy przykład wyświetla listę tagów w **siatki azure-helloworld** repozytorium.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Dane wyjściowe:

```bash
Result
--------
1.1-alpine
```

Pokazuje to, że `azure-mesh-helloworld:1.1-alpine` obraz znajduje się w prywatnym rejestrze kontenerów.

## <a name="retrieve-credentials-for-the-registry"></a>Pobieranie poświadczeń rejestru

Aby wdrożyć wystąpienie kontenera z rejestru, który został utworzony, należy wprowadzić poświadczenia podczas wdrażania. Włącz administratora w swoim rejestrze za pomocą następującego polecenia:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Pobierz rejestru nazwę serwera, nazwę użytkownika i hasło, za pomocą następujących poleceń:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Wartości podanych w tym celu przed poleceń jest określany jako `<acrLoginServer>`, `<acrUserName>`, i `<acrPassword>` w następującym poleceniu.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Tworzenie aplikacji i powiązanych zasobów przy użyciu następującego polecenia i podaj poświadczenia z poprzedniego kroku.

`registry-password` Parametr szablonu jest `securestring`. Go nie będą wyświetlane w stan wdrożenia i `az mesh service show` poleceń. Upewnij się, że jest poprawnie określona w następującym poleceniu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

W ciągu kilku minut polecenia powinny zostać zwrócone przy użyciu:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otwórz aplikację
Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Powinna zostać wyświetlona strony sieci web za pomocą usługi Service Fabric siatki logo.

Polecenie wdrożenia zwraca publiczny adres IP punktu końcowego usługi. Można także badać zasobu sieciowego, aby znaleźć publiczny adres IP punktu końcowego usługi.
 
Nazwa zasobu sieci dla tej aplikacji jest `helloWorldPrivateRegistryNetwork`, pobierania informacji o nim przy użyciu następującego polecenia. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Usuń zasoby

Aby zaoszczędzić ograniczonych zasobów przydzielonych do programu wersji zapoznawczej, Usuń zasoby często. Aby usunąć zasoby związane z tym przykładzie, Usuń grupę zasobów, w którym zostały wdrożone.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Kolejne kroki
- Wyświetl przykładowej aplikacji Hello World na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Aby dowiedzieć się więcej o modelu zasobów sieci szkieletowej usług, zobacz [siatki modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/