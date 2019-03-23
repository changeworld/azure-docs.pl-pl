---
title: Przewodnik Szybki Start — wdrażanie kontenera platformy Docker w usłudze Azure Container Instances — PowerShell
description: W tym przewodniku Szybki Start użyjesz programu Azure PowerShell do szybkiego wdrażania aplikacji konteneryzowanych sieci web uruchomioną w wystąpieniu izolowanego kontenera platformy Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8c50a3069ea8b1303e45c571425a6f4c9b4c0d5b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368192"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Szybki start: Wdrożenia wystąpienia kontenera na platformie Azure przy użyciu programu Azure PowerShell

Usługi Azure Container Instances umożliwia uruchamianie bez użycia serwera kontenerów platformy Docker na platformie Azure z prostotą i szybkością. Wdrażanie aplikacji kontenera wystąpienia na żądanie, jeśli nie potrzebujesz pełnej kontenera platformę koordynacji, takich jak usługi Azure Kubernetes Service.

W tym przewodniku Szybki Start użyjesz programu Azure PowerShell do wdrażania izolowanego kontenera Windows i udostępnij swoją aplikację z w pełni kwalifikowaną nazwą domeny (FQDN). Kilka sekund po wykonaniu polecenia pojedyncze wdrożenie, możesz przejść do aplikacji działającej w kontenerze:

![Aplikacja wdrożona w usłudze Azure Container Instances widziana w przeglądarce][qs-powershell-01]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wystąpienia kontenerów platformy Azure, tak jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* za pomocą następującego polecenia [New-AzResourceGroup][New-AzResourceGroup]:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tworzenie kontenera

Teraz, gdy masz już grupę zasobów, możesz uruchomić kontener na platformie Azure. Aby utworzyć wystąpienie kontenera przy użyciu programu Azure PowerShell, podaj nazwę grupy zasobów, nazwę wystąpienia kontenera i obraz kontenera Docker w poleceniu cmdlet [New-AzContainerGroup][New-AzContainerGroup]. W tym przewodniku Szybki Start użyjesz publicznie `mcr.microsoft.com/windows/servercore/iis:nanoserver` obrazu. Ten obraz pakiety Microsoft Internet Information Services (IIS) do uruchamiania w systemie Nano Server.

Możesz uwidocznić swoje kontenery w Internecie, określając co najmniej jeden port do otworzenia, etykietę nazwy DNS lub obie te informacje. W tym przewodniku Szybki start wdrożysz kontener z etykietą nazwy DNS, aby skonfigurować te usługi IIS jako publicznie dostępne.

Wykonaj polecenie podobne do następujących, aby uruchomić wystąpienie kontenera. Ustaw `-DnsNameLabel` wartość, która jest unikatowa w obrębie regionu świadczenia usługi Azure, w której utworzono wystąpienie. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

W ciągu kilku sekund powinna pojawić się odpowiedź z platformy Azure. Stan `ProvisioningState` kontenera początkowo będzie mieć wartość **Tworzenie**, ale w ciągu minuty lub dwóch ta wartość powinna zmienić się na **Powodzenie**. Sprawdź stan wdrożenia za pomocą polecenia cmdlet [Get-AzContainerGroup][Get-AzContainerGroup]:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Stan aprowizowania, w pełni kwalifikowana nazwa domeny (FQDN, fully qualified domain name) oraz adres IP kontenera będą widoczne w danych wyjściowych polecenia cmdlet:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


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

Po zakończeniu pracy z kontenerem usuń go przy użyciu polecenia cmdlet [Remove-AzContainerGroup][Remove-AzContainerGroup]:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Kolejne kroki

Podczas pracy z tym przewodnikiem Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie obrazu z publicznego rejestru Docker Hub. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
