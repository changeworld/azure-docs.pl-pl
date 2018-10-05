---
title: Skalowanie klastra usługi Service Fabric na platformie Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak szybko skalować klaster usługi Service Fabric na platformie Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/06/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: da9e1ce17e21f4d87286c0be5d425419f6ed0300
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408514"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Samouczek: skalowanie klastra usługi Service Fabric na platformie Azure

Niniejszy samouczek jest drugą częścią serii. Przedstawiono w nim skalowanie istniejącego klastra w poziomie i w pionie. Ukończenie tego samouczka pozwoli Ci uzyskać wiedzę na temat skalowania klastra i czyszczenia pozostałych zasobów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Odczytywanie liczby węzłów klastra
> * Dodawanie węzłów klastra (skalowanie w poziomie)
> * Usuwanie węzłów klastra (skalowanie w pionie)

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) lub [klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) na platformie Azure przy użyciu szablonu
> * Skalowanie klastra w poziomie lub w pionie
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj [moduł Azure PowerShell w wersji 4.1 lub nowszej](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) albo [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Utwórz bezpieczny [klaster systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) lub [klaster systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) na platformie Azure.
* W przypadku wdrażania klastra systemu Windows skonfiguruj środowisko deweloperskie w systemie Windows. Zainstaluj program [Visual Studio 2017](http://www.visualstudio.com), a następnie zainstaluj obciążenia **Programowanie na platformie Azure**, **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych** oraz **Programowanie dla wielu platform w środowisku .NET Core**.  Następnie skonfiguruj [środowisko deweloperskie platformy .NET](service-fabric-get-started.md).
* W przypadku wdrażania klastra systemu Linux skonfiguruj środowisko projektowe Java w systemie [Linux](service-fabric-get-started-linux.md) lub [MacOS](service-fabric-get-started-mac.md).  Zainstaluj [interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Przed wykonaniem poleceń platformy Azure zaloguj się na konto platformy Azure i wybierz subskrypcję.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Do ukończenia tej części samouczka wymagane jest połączenie zarówno z klastrem usługi Service Fabric, jak i zestawem skalowania maszyn wirtualnych (który hostuje klaster). Zestaw skalowania maszyn wirtualnych jest zasobem, który hostuje usługę Service Fabric na platformie Azure.

Po połączeniu się z klastrem można wykonywać zapytania skierowanie do niego. Komunikując się z klastrem, można dowiedzieć się, jakie węzły są rozpoznawane. W poniższym kodzie, który umożliwia połączenie się z klastrem, użyto certyfikatu utworzonego w pierwszej części tej serii. Pamiętaj, aby zmiennym `$endpoint` i `$thumbprint` nadać odpowiednie wartości.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Po nawiązaniu połączenia możesz pobrać stan poszczególnych węzłów w klastrze za pomocą polecenia. W programie **PowerShell** użyj polecenia `Get-ServiceFabricClusterHealth`, a w programie **sfctl** polecenia `sfctl cluster select`.

## <a name="scale-out"></a>Skalowanie w poziomie

Skalowanie w poziomie polega na dodawaniu wystąpień maszyn wirtualnych do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. Poniższy kod pobiera nazwę zestawu skalowania i zwiększa jego **pojemność** o 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Pojemność jest ustawiana na 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Skalowanie w pionie

Skalowanie w pionie odbywa się tak samo jak skalowanie w poziomie, przy czym używana jest niższa wartość **pojemności**. Skalowanie w pionie polega na usuwaniu wystąpień maszyn wirtualnych z zestawu skalowania. Zazwyczaj usługa Service Fabric traktuje tę zmianę jako zniknięcie węzła. Powoduje to zgłoszenie stanu złej kondycji klastra. Aby temu zapobiec, musisz poinformować usługę Service Fabric o oczekiwanym zniknięciu węzła.

### <a name="remove-the-service-fabric-node"></a>Usuwanie węzła usługi Service Fabric

> [!NOTE]
> Ta część dotyczy tylko warstwy trwałości *Brązowa*. Aby uzyskać więcej informacji o trwałości, zobacz [Planowanie pojemności klastra usługi Service Fabric][durability].

Skalowanie w pionie zestawu skalowania maszyn wirtualnych w większości przypadków powoduje usunięcie ostatnio utworzonego wystąpienia maszyny wirtualnej. A zatem należy znaleźć niedawno utworzony, pasujący węzeł usługi Service Fabric. Można to zrobić, wyszukując największą wartość właściwości `NodeInstanceId` w węzłach usługi Service Fabric. Poniższy przykładowy kod przedstawia sortowanie według wystąpienia węzła i zwraca szczegóły wystąpienia o największej wartości identyfikatora.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Klaster usługi Service Fabric musi „wiedzieć”, że ten węzeł ma zostać usunięty. Musisz wykonać trzy kroki:

1. Wyłącz węzeł, aby zatrzymać replikację danych.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Zatrzymaj węzeł, aby środowisko uruchomieniowe usługi Service Fabric zostało prawidłowo zamknięte, a aplikacja otrzymała żądanie przerwania.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Usuń węzeł z klastra.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Po wykonaniu powyższych kroków węzeł może zostać usunięty z zestawu skalowania. Jeśli korzystasz z innej warstwy trwałości niż [brązowa][durability], kroki te są wykonywane automatycznie w przypadku usunięcia wystąpienia zestawu skalowania.

Poniższy blok kodu obejmuje pobranie ostatnio utworzonego węzła oraz jego wyłączenie, zatrzymanie i usunięcie z klastra.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

W poniższym kodzie przedstawiającym użycie polecenia **sfctl** następujące polecenie służy do pobrania wartości **node-name** ostatnio utworzonego węzła: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Następujące zapytania **sfctl** pozwalają sprawdzić stan każdego kroku
>
> **Sprawdzanie stanu dezaktywacji**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Sprawdzanie stanu zatrzymania**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Skalowanie zestawu w pionie

Po usunięciu węzła usługi Service Fabric z klastra zestaw skalowania maszyn wirtualnych można przeskalować w pionie. W poniższym przykładzie pojemność zestawu skalowania została zmniejszona o 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Pojemność jest ustawiana na 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Odczytywanie liczby węzłów klastra
> * Dodawanie węzłów klastra (skalowanie w poziomie)
> * Usuwanie węzłów klastra (skalowanie w pionie)

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak uaktualnić środowisko uruchomieniowe klastra.
> [!div class="nextstepaction"]
> [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
