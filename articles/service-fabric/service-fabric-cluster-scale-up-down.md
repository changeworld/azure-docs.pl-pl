---
title: Skalowanie klastra usługi Service Fabric wewnątrz lub na zewnątrz | Dokumentacja firmy Microsoft
description: Klaster usługi Service Fabric wewnątrz lub na zewnątrz można skalować w celu dopasowania żądanie przez ustawienie reguł skalowania automatycznego dla zestawu skalowania maszyn typu/wirtualnego każdego węzła. Dodawanie lub usuwanie węzłów w klastrze usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: aljo
ms.openlocfilehash: f201ac1f0ea5a4bc07e8c052e7653194140e8759
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669371"
---
# <a name="scale-a-cluster-in-or-out"></a>Skalowanie klastra w poziomie lub w pionie

> [!WARNING]
> Przeczytaj tę sekcję przed przystąpieniem do skalowania

Skalowanie zasobów obliczeniowych do źródła obciążenia robocze aplikacji wymaga planowania zamierzone, prawie zawsze będzie trwać dłużej niż godzinę w środowisku produkcyjnym i wymagają zrozumieć swoje obciążenia i kontekst biznesowy; w rzeczywistości Jeśli to działanie przed nigdy nie zostały wykonane, zaleca się uruchamiania, odczytując i interpretując kolejne [zagadnienia dotyczące planowania pojemności klastra usługi Service Fabric](service-fabric-cluster-capacity.md), przed kontynuowaniem pozostałej części tego dokumentu. To zalecenie jest uniknięcie niezamierzone problemy LiveSite i zalecane jest również, że testujesz operacje, które użytkownik chce wykonać w środowisku nieprodukcyjnym. W dowolnym momencie możesz [zgłosić problemy w środowisku produkcyjnym lub poprosić płatnej pomocy technicznej dla platformy Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Dla inżynierów przydzielone do wykonywania tych operacji, które posiadają odpowiedniego kontekstu w tym artykule opisano operacje skalowania, ale należy zdecydować i zrozumieć, jakie operacje są odpowiednie dla danego przypadku użycia; jakie zasoby do skali (procesor CPU, Magazyn, pamięć), np. jakie kierunek skalowania (w pionie lub poziomie) i jakie operacje do wykonania (wdrożenie szablonu zasobów, Portal, programu PowerShell/interfejsu wiersza polecenia).

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skalowanie klastra usługi Service Fabric wewnątrz lub na zewnątrz przy użyciu reguł skalowania automatycznego lub ręcznego
Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze usługi Service Fabric zostanie określony jako oddzielne maszyny wirtualnej zestawu skalowania. Każdy typ węzła może następnie być skalowana w lub się niezależnie od siebie, mają różne zestawy otwartych portów i może mieć różne metryki pojemności. Dowiedz się więcej o go w [typy węzłów usługi Service Fabric](service-fabric-cluster-nodetypes.md) dokumentu. Ponieważ typy węzłów usługi Service Fabric w klastrze składają się z zestawami skalowania maszyn wirtualnych na zapleczu, należy skonfigurować reguły automatycznego skalowania dla zestawu skalowania maszyn typu/wirtualnego każdego węzła.

> [!NOTE]
> Twoja subskrypcja musi mieć wystarczającej liczby rdzeni, aby dodać nowe maszyny wirtualne, które tworzą ten klaster. Nie ma nie nastąpi sprawdzanie poprawności modelu, dzięki czemu uzyskujesz czas niepowodzenie wdrożenia, jeśli dowolny z limitów przydziału są osiągane. Typ jednego węzła nie można także po prostu przekracza 100 węzłów na zestawu skalowania maszyn wirtualnych. Użytkownik może być konieczne dodanie zestawu skalowania maszyn wirtualnych w celu osiągnięcia skali docelowej i automatyczne skalowanie nie może automagically Dodaj VMSS firmy. Dodawanie firmy VMSS w miejscu do klastra na żywo to trudne zadanie i często skutkuje to użytkownikom inicjowanie obsługi administracyjnej nowych klastrów z typami odpowiedniego węzła, aprowizowane w czasie tworzenia; [Planowanie pojemności klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) odpowiednio. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Wybierz węzeł typu/wirtualnych zestawu skalowania skalowania maszyn
Obecnie nie jest możliwe do określenia reguł skalowania automatycznego dla zestawów skalowania maszyn wirtualnych przy użyciu portalu, aby utworzyć klaster usługi Service Fabric, dlatego Daj nam listy typów węzłów, a następnie dodać reguł skalowania automatycznego do nich za pomocą programu PowerShell Azure (1.0 i nowsze).

Aby uzyskać listę zestawu skalowania maszyn wirtualnych, które tworzą klaster, uruchom następujące polecenia cmdlet:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Zestaw reguł skalowania automatycznego dla zestawu skalowania maszyny wirtualne/typ węzła
Jeśli klaster zawiera wiele typów węzłów, a następnie powtórz to dla każdej skali maszyny typy/wirtualnego węzła ustawia chcesz skalować (przychodzący lub wychodzący). Przed skonfigurowaniem skalowania automatycznego należy wziąć pod uwagę potrzebną liczbę węzłów. Minimalna liczba węzłów wymagana dla typu węzła podstawowego zależy od wybranego poziomu niezawodności. Przeczytaj więcej na temat [poziomów niezawodności](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skalowanie w dół węzła podstawowego typu aby była ona mniejsza niż minimalna liczba wykonującego niestabilne klastra lub obniżyć jej. Może to spowodować utratę danych dla aplikacji i usług systemowych.
> 
> 

Obecnie funkcja automatycznego skalowania nie jest wymuszany przez obciążeń, które aplikacje mogą raportowania w usłudze Service Fabric. Dlatego w tym momencie, w których Skalowanie automatyczne Ci czysto jest wymuszany przez liczniki wydajności, które są emitowane przez każdą maszynę wirtualną wystąpienia zestawu skalowania.  

Wykonaj te instrukcje [do skonfigurowania automatycznego skalowania dla każdego zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> W dół scenariusza chyba że ma typu węzła [poziom trwałości] [ durability] Gold i Silver potrzebne do wywoływania [polecenia cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) z Nazwa odpowiedniego węzła. Dla trwałości brązowy nie zaleca się skalować w dół więcej niż jeden węzeł w danym momencie.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ręcznie Dodaj maszyny wirtualne do zestawu skalowania maszyny wirtualne/typ węzła

Skalowanie w poziomie polega na dodawaniu wystąpień maszyn wirtualnych do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. 

> [!NOTE]
> Dodawanie maszyn wirtualnych trwa pewien czas, dlatego nie powinna mieć dodatki do było natychmiastowe. Dlatego należy zaplanować dodanie pojemności oraz z wyprzedzeniem, umożliwiające ponad 10 minut, zanim pojemności maszyn wirtualnych jest dostępna dla wystąpień replik/usługi, Pobierz umieszczone.
> 

### <a name="add-vms-using-a-template"></a>Dodawanie maszyn wirtualnych przy użyciu szablonu
Wykonaj przykładowe/zgodnie z instrukcjami w [galerii szablonów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych w każdy typ węzła. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Dodawanie maszyn wirtualnych za pomocą poleceń programu PowerShell lub interfejsu wiersza polecenia
Poniższy kod pobiera nazwę zestawu skalowania i zwiększa jego **pojemność** o 1.

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

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ręcznie usuń maszyny wirtualne z zestawu skalowania maszyny wirtualne/typ węzła
Skalowanie w typ węzła, można usunąć wystąpień maszyn wirtualnych z zestawu skalowania. Jeśli typ węzła jest poziom trwałości brązowy, usługi Service Fabric jest niebranie pod uwagę, co się stało i zgłasza, że węzeł zostanie zgubione. Powoduje to zgłoszenie stanu złej kondycji klastra. Aby uniknąć tego nieprawidłowego stanu, możesz jawnie usunąć węzeł z klastra i usuwać stan węzła.

Uruchom usługi systemowe service fabric w typie podstawowym węzłem w klastrze. Podczas skalowania w dół typu węzła podstawowego nigdy nie skalowania w zależności od liczby wystąpień na wartość mniejszą niż co [warstwy niezawodności](service-fabric-cluster-capacity.md) gwarantuje. 
 
Usługa stanowa wymaga określonej liczby węzłów można zawsze do zapewnienia dostępności i zachować stan usługi. W bardzo minimalne należy liczbę węzłów równa docelowej liczbę zestawu replik partycji/usługi.

### <a name="remove-the-service-fabric-node"></a>Usuwanie węzła usługi Service Fabric

Kroki ręczne usunięcie stanu węzła zastosować tylko do typów węzłów o *brązowa* warstwa trwałości.  Aby uzyskać *Silver* i *Gold* warstwa trwałości te kroki są wykonywane automatycznie przez platformę. Aby uzyskać więcej informacji o trwałości, zobacz [Planowanie pojemności klastra usługi Service Fabric][durability].

Aby zachować równe rozłożenie węzłów klastra w domenach uaktualniania i błędów, a tym samym umożliwić ich równomierne wykorzystywanie, najpierw należy usunąć ostatnio utworzony węzeł. Innymi słowy węzły należy usuwać w kolejności odwrotnej niż były tworzone. Ostatnio utworzony węzeł to ten, który ma największą wartość właściwości `virtual machine scale set InstanceId`. Poniższe przykłady kodu zwracają ostatnio utworzony węzeł.

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

Po usunięciu węzła usługi Service Fabric z klastra skalę zestawu skalowania maszyn wirtualnych można zmniejszyć w poziomie. W poniższym przykładzie pojemność zestawu skalowania została zmniejszona o 1.

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

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Zachowania, można zaobserwować w narzędziu Service Fabric Explorer
Po przeskalowaniu klastra w górę narzędzia Service Fabric Explorer będzie odzwierciedlać liczbę węzłów (wystąpienia zestawu skalowania maszyn wirtualnych), które są częścią klastra.  Jednak w przypadku skalowania klastra w dół możesz zostanie wyświetlony wystąpienia węzła usuniętych i maszyn wirtualnych, wyświetlana w złej kondycji, chyba że wywołanie [polecenia Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) o nazwie odpowiedni węzeł.   

Oto wyjaśnienia tego zachowania.

Węzły w narzędziu Service Fabric Explorer są odbicia jakie usługi systemowe Service Fabric (FM specjalnie) obsługującemu liczby węzłów w klastrze miały/ma. Skalowanie zestawu w dół skalowania maszyn wirtualnych, maszyna wirtualna została usunięta, ale FM usługi system nadal sądzą, że węzeł (który został zamapowany na maszynę Wirtualną, która została usunięta) przechodzi. Dlatego narzędzia Service Fabric Explorer w dalszym ciągu ten węzeł jest wyświetlany (Jeśli stan kondycji może być błąd lub nieznany).

Aby upewnić się, że węzeł zostanie usunięty po usunięciu maszyny Wirtualnej, masz dwie opcje:

1. Wybierz poziom trwałości Gold i Silver dla typów węzłów w klastrze, co daje integracji infrastruktury. Które następnie automatycznie usunie węzły z naszych stanu usługi (FM) systemu skalowanie w.
Zapoznaj się [szczegółowe informacje dotyczące poziomów trwałości](service-fabric-cluster-capacity.md)

2. Po wystąpieniu maszyny Wirtualnej został przeskalowano w dół, należy wywołać [polecenia cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Klastry usługi Service Fabric wymagają określonej liczby węzłów, aby rozpocząć w tym czasie w celu zapewnienia dostępności i zachować stan — określane jako "utrzymania kworum". Tak, jest zazwyczaj niebezpieczne do zamykania wszystkich maszyn w klastrze, o ile nie zostało przeprowadzone [pełną kopię zapasową stanu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące polecenie, aby także wiedzę na temat planowania pojemności klastra, Uaktualnianie klastra i partycjonowanie usług:

* [Planowanie pojemności klastra](service-fabric-cluster-capacity.md)
* [Uaktualnianie klastra](service-fabric-cluster-upgrade.md)
* [Usługi stanowe partycji dla maksymalnej skali](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
