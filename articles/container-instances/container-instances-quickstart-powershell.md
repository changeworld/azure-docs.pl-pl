---
title: Szybki start — Tworzenie pierwszego kontenera usługi Azure Container Instances za pomocą programu PowerShell
description: W tym przewodniku Szybki start używasz programu Azure PowerShell do wdrożenia kontenera systemu Windows w usłudze Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075547"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Szybki start: Tworzenie pierwszego kontenera w usłudze Azure Container Instances

Usługa Azure Container Instances ułatwia tworzenie kontenerów Docker na platformie Azure oraz zarządzanie nimi bez konieczności inicjowania obsługi maszyn wirtualnych czy adoptowania usługi wyższego poziomu. Podczas pracy z tym przewodnikiem Szybki start utworzysz kontener systemu Windows na platformie Azure i uwidocznisz go w Internecie przy użyciu w pełni kwalifikowanej nazwy domeny. Ta operacja jest wykonywana za pomocą jednego polecenia. W ciągu zaledwie kilku minut działająca aplikacja będzie widoczna w przeglądarce:

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][qs-powershell-01]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.5 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener można utworzyć, podając nazwę, obraz usługi Docker i grupę zasobów platformy Azure w poleceniu cmdlet [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Opcjonalnie można uwidocznić kontener w Internecie przy użyciu etykiety nazwy DNS.

Wykonaj następujące polecenie w celu uruchomienia kontenera z systemem Nano Server, w ramach którego działają usługi Internet Information Services (IIS). Wartość `-DnsNameLabel` musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienie, dlatego być może trzeba będzie zmodyfikować tę wartość w celu zapewnienia unikatowości.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

W ciągu kilku sekund powinna pojawić się odpowiedź na żądanie. Kontener ma początkowo stan **Tworzenie**, ale powinien zostać uruchomiony w ciągu jednej lub dwóch minut. Stan wdrożenia możesz sprawdzić za pomocą polecenia cmdlet [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

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

Gdy właściwość **ProvisioningState** kontenera będzie miała wartość `Succeeded`, przejdź do niego, podając jego nazwę `Fqdn` w przeglądarce:

![Widziana w przeglądarce usługa IIS wdrożona za pomocą usługi Azure Container Instances][qs-powershell-01]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem usuń go przy użyciu polecenia cmdlet [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start utworzyliśmy wystąpienie kontenera usługi Azure na podstawie obrazu z publicznego rejestru Docker Hub. Jeśli chcesz samodzielnie skompilować obraz kontenera i wdrożyć go w usłudze Azure Container Instances za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
