---
title: Quickstart - Create registry - Powershell
description: Quickly learn to create a private Docker registry in Azure Container Registry with PowerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 872b2a29444e5278db34ce44741e2ca90d885702
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456376"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Quickstart: Create a private container registry using Azure PowerShell

Usługa Azure Container Registry to zarządzana, prywatna usługa rejestru kontenerów platformy Docker używana do kompilowania, przechowywania i obsługi obrazów kontenerów Docker. Z tego przewodnika Szybki start dowiesz się, jak utworzyć rejestr kontenerów platformy Azure przy użyciu programu PowerShell. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby sprawdzić zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Musisz mieć również zainstalowane lokalnie środowisko Docker. Docker provides packages for [macOS][docker-mac], [Windows][docker-windows], and [Linux][docker-linux] systems.

Ze względu na to, że usługa Azure Cloud Shell nie zawiera wszystkich wymaganych składników platformy Docker (demon `dockerd`), nie można używać usługi Cloud Shell na potrzeby tego przewodnika Szybki start.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Sign in to your Azure subscription with the [Connect-AzAccount][Connect-AzAccount] command, and follow the on-screen directions.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Once you're authenticated with Azure, create a resource group with [New-AzResourceGroup][New-AzResourceGroup]. Grupa zasobów to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tworzenie rejestru kontenerów

Next, create a container registry in your new resource group with the [New-AzContainerRegistry][New-AzContainerRegistry] command.

Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie jest tworzony rejestr o nazwie „myContainerRegistry007”. Zastąp wartość *myContainerRegistry007* w poniższym poleceniu, a następnie uruchom je w celu utworzenia rejestru:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. For details on available service tiers, see [Container registry SKUs][container-registry-skus].

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do swojego rejestru. In production scenarios you should use an individual identity or service principal for container registry access, but to keep this quickstart brief, enable the admin user on your registry with the [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] command:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Next, run [docker login][docker-login] to log in:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Once you're done working with the resources you created in this quickstart, use the [Remove-AzResourceGroup][Remove-AzResourceGroup] command to remove the resource group, the container registry, and the container images stored there:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry w programie Azure PowerShell, wypchnięto obraz kontenera oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Azure Container Registry tutorials][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
