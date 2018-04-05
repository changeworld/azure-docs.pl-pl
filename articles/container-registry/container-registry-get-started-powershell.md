---
title: Szybki start — tworzenie rejestru prywatnego platformy Docker na platformie Azure przy użyciu programu PowerShell
description: Szybko naucz się tworzyć rejestr prywatny platformy Docker przy użyciu programu PowerShell.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9603ac779c7dbc640a7c24856c32f04edbac849d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Szybki start: tworzenie usługi Azure Container Registry przy użyciu programu PowerShell

Usługa Azure Container Registry to zarządzana usługa rejestru kontenerów platformy Docker używana do przechowywania prywatnych obrazów kontenerów Docker. W tym przewodniku szczegółowo opisano tworzenie wystąpienia usługi Azure Container Registry za pomocą programu PowerShell, wypychanie obrazu kontenera do rejestru i na koniec wdrażanie kontenera z rejestru do usługi Azure Container Instances (ACI).

Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Musisz mieć również zainstalowane lokalnie środowisko Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Login-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Utwórz wystąpienie usługi ACR za pomocą polecenia [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie użyto nazwy *myContainerRegistry007*. Zaktualizuj ją do unikatowej wartości.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Zaloguj się do usługi ACR

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do wystąpienia usługi ACR. Najpierw użyj polecenia [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential), aby uzyskać poświadczenia administratora dla wystąpienia usługi ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Następnie użyj polecenia [docker login][docker-login], aby zalogować się do wystąpienia usługi ACR.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`. Może także zostać wyświetlone ostrzeżenie o zabezpieczeniach zalecające użycie parametru `--password-stdin`. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Zobacz polecenie [docker login][docker-login], aby uzyskać więcej informacji.

## <a name="push-image-to-acr"></a>Wypychanie obrazu do usługi ACR

Aby wypchnąć obraz do usługi Azure Container Registry, najpierw musisz go mieć. W razie potrzeby uruchom następujące polecenie, aby ściągnąć wcześniej utworzony obraz z usługi Docker Hub.

```powershell
docker pull microsoft/aci-helloworld
```

Obraz musi być otagowany nazwą serwera logowania usługi ACR. Użyj polecenia [docker tag][docker-tag], aby to zrobić.

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Na koniec użyj polecenia [docker push][docker-push], aby wypchnąć obraz do usługi ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Wdrażanie obrazu w usłudze ACI
Aby wdrożyć obraz jako wystąpienie kontenera w usłudze Azure Container Instances (ACI), najpierw przekonwertuj poświadczenia rejestru na element PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Aby wdrożyć obraz kontenera z rejestru kontenerów przy użyciu 1 rdzenia procesora CPU i 1 GB pamięci, uruchom następujące polecenie:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Powinna pojawić się wstępna odpowiedź od usługi Azure Resource Manager ze szczegółowymi informacjami o kontenerze. Aby monitorować stan kontenera i sprawdzić, czy jest uruchomiony, powtórz polecenie [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Powinno to zająć mniej niż minutę.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Przykładowe dane wyjściowe: `Succeeded`

## <a name="view-the-application"></a>Wyświetlanie aplikacji
Po pomyślnym wdrożeniu w usłudze ACI uzyskaj publiczny adres IP kontenera za pomocą polecenia [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Przykładowe dane wyjściowe: `"13.72.74.222"`

Aby wyświetlić działającą aplikację, przejdź do publicznego adresu IP w ulubionej przeglądarce. Powinno to wyglądać następująco:

![Aplikacja Hello World w przeglądarce][qs-portal-15]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, usługa Azure Container Registry i wszystkie wystąpienia kontenerów platformy Azure nie będą już potrzebne, możesz je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup].

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzona została usługa Azure Container Registry z interfejsem wiersza polecenia platformy Azure i uruchomione zostało jej wystąpienie w usłudze Azure Container Instances. Przejdź do samouczka usługi Azure Container Instances, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
