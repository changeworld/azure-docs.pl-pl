---
title: Szybki start — tworzenie rejestru prywatnego platformy Docker na platformie Azure — PowerShell
description: Szybko naucz się tworzyć prywatny rejestr kontenerów platformy Docker na platformie Azure przy użyciu programu PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299747"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Szybki start: tworzenie prywatnego rejestru kontenerów za pomocą programu Azure PowerShell

Usługa Azure Container Registry to zarządzana, prywatna usługa rejestru kontenerów platformy Docker używana do kompilowania, przechowywania i obsługi obrazów kontenerów Docker. Z tego przewodnika Szybki start dowiesz się, jak utworzyć rejestr kontenerów platformy Azure przy użyciu programu PowerShell. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby sprawdzić zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

Musisz mieć również zainstalowane lokalnie środowisko Docker. Platforma Docker zawiera pakiety dla systemów [macOS][docker-mac], [Windows][docker-windows] i [Linux][docker-linux].

Ze względu na to, że usługa Azure Cloud Shell nie zawiera wszystkich wymaganych składników platformy Docker (demon `dockerd`), nie można używać usługi Cloud Shell na potrzeby tego przewodnika Szybki start.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzureRmAccount][Connect-AzureRmAccount] i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Po zakończeniu uwierzytelniania na platformie Azure utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Grupa zasobów to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tworzenie rejestru kontenerów

Następnie utwórz rejestr kontenerów w nowej grupie zasobów przy użyciu polecenia [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie jest tworzony rejestr o nazwie „myContainerRegistry007”. Zastąp wartość *myContainerRegistry007* w poniższym poleceniu, a następnie uruchom je w celu utworzenia rejestru:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usługi, zobacz [Jednostki SKU rejestru kontenerów][container-registry-skus].

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do swojego rejestru. W scenariuszach ze środowiskiem produkcyjnym do uzyskiwania dostępu do rejestru kontenerów powinna być używana indywidualna tożsamość lub jednostka usługi, ale aby nie wydłużać tego przewodnika Szybki start, włącz administratora w swoim rejestrze za pomocą polecenia [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential]:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Następnie uruchom polecenie [docker login][docker-login], aby się zalogować:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z zasobami utworzonymi w tym przewodniku Szybki start użyj polecenia [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup], aby usunąć grupę zasobów, rejestr kontenerów i przechowywane w nim obrazy kontenerów:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry w programie Azure PowerShell, wypchnięto obraz kontenera oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md