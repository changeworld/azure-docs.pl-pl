---
title: Szybki start — tworzenie rejestru prywatnego platformy Docker na platformie Azure — PowerShell
description: Szybko naucz się tworzyć prywatny rejestr kontenerów platformy Docker na platformie Azure przy użyciu programu PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7dcdca594949a3b20000b31db681370ee4f9eac3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255289"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Szybki start: tworzenie prywatnego rejestru kontenerów za pomocą programu Azure PowerShell

Usługa Azure Container Registry to zarządzana, prywatna usługa rejestru kontenerów platformy Docker używana do kompilowania, przechowywania i obsługi obrazów kontenerów Docker. Z tego przewodnika Szybki start dowiesz się, jak utworzyć rejestr kontenerów platformy Azure przy użyciu programu PowerShell. Po utworzeniu rejestru wypchniesz do niego obraz kontenera, a następnie wdrożysz kontener z rejestru do usługi Azure Container Instances (ACI).

## <a name="prerequisites"></a>Wymagania wstępne

Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby sprawdzić zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

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

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do swojego rejestru. Użyj polecenia [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential], aby najpierw uzyskać poświadczenia administratora dla rejestru:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Następnie uruchom polecenie [docker login][docker-login], aby się zalogować:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Pomyślne logowanie zwraca element `Login Succeeded`:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Wypychanie obrazu do rejestru

Teraz — po zalogowaniu do rejestru — możesz wypchnąć do niego obrazy kontenera. Aby uzyskać obraz, który można wypchnąć do rejestru, ściągnij publiczny obraz [aci-helloworld][aci-helloworld-image] z usługi Docker Hub. Obraz [aci-helloworld][aci-helloworld-github] to mała aplikacja środowiska Node.js, która obsługuje statyczną stronę HTML przedstawiającą logo usługi Azure Container Instances.

```powershell
docker pull microsoft/aci-helloworld
```

W przypadku ściągania obrazu dane wyjściowe będą podobne do następujących:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Zanim będzie można wypchnąć obraz do rejestru kontenerów platformy Azure, musisz go otagować w pełni kwalifikowaną nazwą domeny rejestru. Nazwa FQDN rejestrów kontenerów platformy Azure ma format *\<nazwa_rejestru\>.azurecr.io*.

Wypełnij zmienną przy użyciu pełnego tagu obrazu. Dołącz serwer logowania, nazwę repozytorium („aci-helloworld”) i wersję obrazu („v1”):

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

Teraz otaguj obraz przy użyciu polecenia [docker tag][docker-tag]:

```powershell
docker tag microsoft/aci-helloworld $image
```

Na koniec użyj polecenia [docker push][docker-push] w celu wypchnięcia do rejestru:

```powershell
docker push $image
```

W przypadku wypychania obrazu przez klienta platformy Docker dane wyjściowe powinny być podobne do następujących:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Gratulacje! Pierwszy obraz kontenera został właśnie wypchnięty do rejestru.

## <a name="deploy-image-to-aci"></a>Wdrażanie obrazu w usłudze ACI

Obraz znajduje się teraz w rejestrze, więc możesz wdrożyć kontener bezpośrednio w usłudze Azure Container Instances, aby zobaczyć, jak działa na platformie Azure.

Najpierw przekonwertuj poświadczenia rejestru na element *PSCredential*. Polecenie `New-AzureRmContainerGroup`, które jest używane do tworzenia wystąpienia kontenera, wymaga ich w tym formacie.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Ponadto etykieta nazwy DNS kontenera musi być unikatowa w obrębie regionie świadczenia usługi Azure, w którym go tworzysz. Wykonaj następujące polecenie, aby wypełnić zmienną przy użyciu wygenerowanej nazwy:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Na koniec uruchom polecenie [New-AzureRmContainerGroup][New-AzureRmContainerGroup] w celu wdrożenia kontenera z obrazu w rejestrze z 1 rdzeniem procesora CPU i 1 GB pamięci:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Powinna pojawić się wstępna odpowiedź od platformy Azure ze szczegółowymi informacjami o kontenerze. Jego stan będzie na początku ustawiony jako „Oczekujący”:

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Aby monitorować ten stan i określić, kiedy kontener został uruchomiony, kilka razy uruchom polecenie [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Uruchomienie kontenera powinno zająć mniej niż minutę.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Tutaj możesz zobaczyć, że stan ProvisioningState kontenera jest na początku ustawiony na *Tworzenie*, a następnie zmienia się na *Powodzenie* po jego pomyślnym uruchomieniu:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Wyświetlanie działającej aplikacji

Gdy wdrożenie w usłudze ACI zakończyło się pomyślnie i kontener został uruchomiony, przejdź do w pełni kwalifikowanej nazwy domeny (FQDN) w przeglądarce, aby wyświetlić działającą aplikację na platformie Azure.

Aby uzyskać nazwę FQDN swojego kontenera, użyj polecenia [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

Dane wyjściowe polecenia to nazwa FQDN wystąpienia kontenera:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Zanotuj nazwę FQDN i przejdź do niej w przeglądarce:

![Aplikacja Hello World w przeglądarce][qs-psh-01-running-app]

Gratulacje! Masz kontener uruchamiający aplikację na platformie Azure, który został wdrożony bezpośrednio z obrazu kontenera w prywatnym rejestrze kontenerów platformy Azure.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z zasobami utworzonymi w tym przewodniku Szybki Start użyj polecenia [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup], aby usunąć grupę zasobów, rejestr kontenerów i wystąpienie kontenera:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzona została usługa Azure Container Registry z interfejsem wiersza polecenia platformy Azure i uruchomione zostało jej wystąpienie w usłudze Azure Container Instances. Przejdź do samouczka usługi Azure Container Instances, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
