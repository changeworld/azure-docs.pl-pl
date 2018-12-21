---
title: 'Szybki start: uruchamianie aplikacji w usłudze Azure Container Instances — PowerShell'
description: W tym przewodniku Szybki start wdrożysz aplikację kontenera Docker w usłudze Azure Container Instances przy użyciu programu Azure PowerShell
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b17cca7f0c00aba260b97b29345ff33156a50138
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183951"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-azure-powershell"></a>Szybki start: uruchamianie aplikacji kontenera w usłudze Azure Container Instances przy użyciu programu PowerShell

Używając usługi Azure Container Instances, możesz łatwo i szybko uruchamiać kontenery Docker na platformie Azure. Nie musisz wdrażać maszyn wirtualnych ani korzystać z pełnej platformy orkiestracji kontenerów, takiej jak Kubernetes. Podczas pracy z tym przewodnikiem Szybki start utworzysz kontener systemu Windows na platformie Azure przy użyciu witryny Azure Portal i udostępnisz jego aplikację za pomocą w pełni kwalifikowanej nazwy domeny (FQDN). Kilka sekund po wykonaniu pojedynczego polecenia wdrożenia możesz przejść do uruchomionej aplikacji:

![Aplikacja wdrożona w usłudze Azure Container Instances widziana w przeglądarce][qs-powershell-01]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.5 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wystąpienia kontenerów platformy Azure, tak jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* za pomocą następującego polecenia [New-AzureRmResourceGroup][New-AzureRmResourceGroup]:

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tworzenie kontenera

Teraz, gdy masz już grupę zasobów, możesz uruchomić kontener na platformie Azure. Aby utworzyć wystąpienie kontenera przy użyciu programu Azure PowerShell, podaj nazwę grupy zasobów, nazwę wystąpienia kontenera i obraz kontenera Docker w poleceniu cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Możesz uwidocznić swoje kontenery w Internecie, określając co najmniej jeden port do otworzenia, etykietę nazwy DNS lub obie te informacje. W tym przewodniku Szybki start wdrożysz kontener z etykietą nazwy DNS, hostujący usługi Internet Information Services (IIS) działające w systemie Nano Server.

Wykonaj poniższe polecenie, aby uruchomić wystąpienie kontenera. Wartość `-DnsNameLabel` musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienie. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

W ciągu kilku sekund powinna pojawić się odpowiedź z platformy Azure. Stan `ProvisioningState` kontenera początkowo będzie mieć wartość **Tworzenie**, ale w ciągu minuty lub dwóch ta wartość powinna zmienić się na **Powodzenie**. Sprawdź stan wdrożenia za pomocą polecenia cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Stan aprowizowania, w pełni kwalifikowana nazwa domeny (FQDN, fully qualified domain name) oraz adres IP kontenera będą widoczne w danych wyjściowych polecenia cmdlet:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Kiedy stan `ProvisioningState` kontenera będzie mieć wartość **Powodzenie**, przejdź do jego nazwy `Fqdn` w przeglądarce. Jeśli zostanie wyświetlona strona internetowa podobna do poniższej — gratulacje! Aplikacja działającą w kontenerze Docker została pomyślne wdrożona na platformie Azure.

![Widziana w przeglądarce usługa IIS wdrożona za pomocą usługi Azure Container Instances][qs-powershell-01]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem usuń go przy użyciu polecenia cmdlet [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie obrazu z publicznego rejestru Docker Hub. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
