---
title: Skalowanie klastra sieci szkieletowej usług do lub z zewnątrz
description: Skalowanie klastra sieci szkieletowej usług w lub na zewnątrz, aby dopasować popyt, ustawiając reguły automatycznej skali dla każdego typu węzła/zestawu skalowania maszyny wirtualnej. Dodawanie lub usuwanie węzłów do klastra sieci szkieletowej usług
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258735"
---
# <a name="scale-a-cluster-in-or-out"></a>Skalowanie klastra w poziomie lub w pionie

> [!WARNING]
> Przeczytaj tę sekcję przed skalowaniem

Skalowanie zasobów obliczeniowych w celu pozyskania obciążenia roboczego aplikacji wymaga zamierzonego planowania, prawie zawsze trwa dłużej niż godzinę dla środowiska produkcyjnego i wymaga zrozumienia obciążenia i kontekstu biznesowego; w rzeczywistości, jeśli nigdy wcześniej nie wykonałeś tego działania, zaleca się rozpoczęcie od przeczytania i zrozumienia [zagadnień planowania pojemności klastra sieci szkieletowej usług,](service-fabric-cluster-capacity.md)przed kontynuowaniem pozostałej części tego dokumentu. To zalecenie ma na celu uniknięcie niezamierzonych problemów livesite i zaleca się również pomyślnie przetestować operacje, które zdecydujesz się wykonać w środowisku nieprodukcyjnym. W dowolnym momencie można [zgłaszać problemy z produkcją lub żądać płatnej pomocy technicznej dla platformy Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Dla inżynierów przydzielonych do wykonywania tych operacji, które posiadają odpowiedni kontekst, w tym artykule opisano operacje skalowania, ale należy zdecydować i zrozumieć, które operacje są odpowiednie dla danego przypadku użycia; takie jak jakie zasoby do skalowania (CPU, Storage, Memory), jaki kierunek skalowania (pionowo lub poziomo) i jakie operacje wykonać (wdrożenie szablonu zasobów, portal, program PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skalowanie klastra sieci szkieletowej usług w lub poza niego przy użyciu reguł automatycznego skalowania lub ręcznie
Zestawy skalowania maszyny wirtualnej to zasób obliczeniowy platformy Azure, którego można używać do wdrażania kolekcji maszyn wirtualnych i zarządzania nią jako zestaw. Każdy typ węzła zdefiniowany w klastrze sieci szkieletowej usług jest skonfigurowany jako oddzielny zestaw skalowania maszyny wirtualnej. Każdy typ węzła można następnie skalować w lub na zewnątrz niezależnie, mają różne zestawy portów otwarte i może mieć różne metryki pojemności. Przeczytaj więcej na ten temat w dokumencie [typów węzłów sieci szkieletowej usług.](service-fabric-cluster-nodetypes.md) Ponieważ typy węzłów sieci szkieletowej usług w klastrze są wykonane z zestawów skalowania maszyny wirtualnej w wewnętrznej bazy danych, należy skonfigurować reguły skalowania automatycznego dla każdego typu węzła/zestawu skalowania maszyny wirtualnej.

> [!NOTE]
> Subskrypcja musi mieć wystarczającą liczbę rdzeni, aby dodać nowe maszyny wirtualne, które tworzą ten klaster. Obecnie nie ma sprawdzania poprawności modelu, więc wystąpi błąd czasu wdrożenia, jeśli którykolwiek z limitów przydziału zostanie trafiony. Również typ pojedynczego węzła nie może po prostu przekroczyć 100 węzłów na VMSS. Może być konieczne dodanie VMSS w celu osiągnięcia skali docelowej i automatyczne skalowanie nie można automatycznie dodać VMSS.You may need to add VMSS's to achieve the targeted scale, and auto-scaling can not automagically add VMSS's. Dodawanie VMSS w miejscu do klastra na żywo jest trudnym zadaniem i często powoduje to użytkowników inicjowania obsługi administracyjnej nowych klastrów z odpowiednich typów węzłów aprowizowanych w czasie tworzenia; [odpowiednio zaplanować pojemność klastra.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Wybierz typ węzła/skalę maszyny wirtualnej ustawioną na skalowanie
Obecnie nie można określić reguł skalowania automatycznego dla zestawów skalowania maszyny wirtualnej przy użyciu portalu do tworzenia klastra sieci szkieletowej usług, więc użyjmy usługi Azure PowerShell (1.0+), aby wyświetlić listę typów węzłów, a następnie dodać do nich reguły automatycznej skali.

Aby uzyskać listę zestaw skalowania maszyny wirtualnej, które tworzą klaster, uruchom następujące polecenia cmdlet:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ustawianie reguł automatycznej skali dla zestawu skalowania typu węzła/maszyny wirtualnej
Jeśli klaster ma wiele typów węzłów, powtórz to dla każdego typu węzła/zestawów skalowania maszyny wirtualnej, które chcesz skalować (w lub na zewnątrz). Przed skonfigurowaniem skalowania automatycznego należy wziąć pod uwagę potrzebną liczbę węzłów. Minimalna liczba węzłów wymagana dla typu węzła podstawowego zależy od wybranego poziomu niezawodności. Dowiedz się więcej o [poziomach niezawodności](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skalowanie w dół typu węzła podstawowego do mniej niż minimalna liczba sprawiają, że klaster niestabilny lub obniżyć go. Może to spowodować utratę danych dla aplikacji i usług systemowych.
> 
> 

Obecnie funkcja automatycznego skalowania nie jest napędzana przez obciążenia, które aplikacje mogą raportować do sieci szkieletowej usług. Tak więc w tej chwili automatyczna skala, którą otrzymujesz, jest wyłącznie napędzana przez liczniki wydajności emitowane przez każde wystąpienie zestawu skalowania maszyny wirtualnej.  

Postępuj zgodnie z tymi [instrukcjami, aby skonfigurować automatyczne skalowanie dla każdego zestawu skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> W scenariuszu skalowania w dół, chyba że typ węzła ma [poziom trwałości][durability] Gold lub Silver należy wywołać [polecenie cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) z odpowiednią nazwą węzła. W przypadku trwałości brązu nie zaleca się skalowania w dół więcej niż jednego węzła naraz.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ręczne dodawanie maszyn wirtualnych do zestawu skalowania typu węzła/maszyny wirtualnej

Skalowanie w poziomie polega na dodawaniu wystąpień maszyn wirtualnych do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. 

> [!NOTE]
> Dodawanie maszyn wirtualnych wymaga czasu, więc nie oczekuj, że dodatki będą natychmiastowe. Dlatego należy zaplanować dodanie pojemności z dużym wyprzedzeniem, aby umożliwić ponad 10 minut, zanim pojemność maszyny Wirtualnej jest dostępna dla wystąpień replik/usługi, aby uzyskać umieszczone.
> 

### <a name="add-vms-using-a-template"></a>Dodawanie maszyn wirtualnych przy użyciu szablonu
Postępuj zgodnie z przykładowymi/instrukcjami w [galerii szablonów przewodnika Szybki start,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) aby zmienić liczbę maszyn wirtualnych w każdym typie węzła. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Dodawanie maszyn wirtualnych przy użyciu poleceń programu PowerShell lub CLI
Poniższy kod pobiera nazwę zestawu skalowania i zwiększa jego **pojemność** o 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Pojemność jest ustawiana na 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ręczne usuwanie maszyn wirtualnych z zestawu skalowania typu węzła/maszyny wirtualnej
Podczas skalowania w typie węzła, należy usunąć wystąpienia maszyn wirtualnych z zestawu skalowania. Jeśli typem węzła jest poziom trwałości z brązu, sieci szkieletowej usług nie jest świadomy, co się stało i zgłasza, że węzeł zaginął. Powoduje to zgłoszenie stanu złej kondycji klastra. Aby zapobiec temu złemu stanowi, należy jawnie usunąć węzeł z klastra i usunąć stan węzła.

Usługi systemu sieci szkieletowej usług są uruchamiane w typie węzła podstawowego w klastrze. Podczas skalowania w dół typu węzła podstawowego, nigdy nie skaluj w dół liczby wystąpień do mniej niż gwarantuje [warstwa niezawodności.](service-fabric-cluster-capacity.md) 
 
Dla usługi stanowej, trzeba pewną liczbę węzłów, aby zawsze do utrzymania dostępności i zachowania stanu usługi. Co najmniej potrzebna jest liczba węzłów równa docelowej liczbie zestawów replik partycji/usługi.

### <a name="remove-the-service-fabric-node"></a>Usuwanie węzła usługi Service Fabric

Kroki ręcznego usuwania stanu węzła mają zastosowanie tylko do typów węzłów z warstwą Trwałość *z brązu.*  W przypadku warstwy trwałości *Silver* i *Gold* te kroki są wykonywane automatycznie przez platformę. Aby uzyskać więcej informacji o trwałości, zobacz [Planowanie pojemności klastra usługi Service Fabric][durability].

Aby zachować równe rozłożenie węzłów klastra w domenach uaktualniania i błędów, a tym samym umożliwić ich równomierne wykorzystywanie, najpierw należy usunąć ostatnio utworzony węzeł. Innymi słowy węzły należy usuwać w kolejności odwrotnej niż były tworzone. Ostatnio utworzony węzeł to ten, który ma największą wartość właściwości `virtual machine scale set InstanceId`. Poniższe przykłady kodu zwracają ostatnio utworzony węzeł.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
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

```shell
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
> **Sprawdź stan dezaktywacji**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Sprawdź stan zatrzymania**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Skalowanie zestawu w pionie

Po usunięciu węzła usługi Service Fabric z klastra skalę zestawu skalowania maszyn wirtualnych można zmniejszyć w poziomie. W poniższym przykładzie pojemność zestawu skalowania została zmniejszona o 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Pojemność jest ustawiana na 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Zachowania, które można zaobserwować w Eksploratorze sieci szkieletowej usług
Podczas skalowania w górę klastra Eksplorator sieci szkieletowej usług będzie odzwierciedlał liczbę węzłów (wystąpień zestawu skalowania maszyny wirtualnej), które są częścią klastra.  Jednak podczas skalowania klastra w dół zostanie wyświetlone usunięte wystąpienie węzła/maszyny Wirtualnej wyświetlane w stanie złej kondycji, chyba że wywołasz [polecenie cmd Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) o odpowiedniej nazwie węzła.   

Oto wyjaśnienie tego zachowania.

Węzły wymienione w Eksploratorze sieci szkieletowej usług są odzwierciedleniem tego, co usługi systemu sieci szkieletowej usług (FM w szczególności) wiedzą o liczbie węzłów, które miał/ma klaster. Po skalowaniu skalę maszyny wirtualnej ustawionej w dół maszyna wirtualna została usunięta, ale usługa systemu FM nadal myśli, że węzeł (który został zamapowany na maszynę wirtualną, która została usunięta) powróci. Więc Eksplorator sieci szkieletowej usług nadal wyświetla ten węzeł (chociaż stan kondycji może być błąd lub nieznany).

Aby upewnić się, że węzeł jest usuwany po usunięciu maszyny Wirtualnej, dostępne są dwie opcje:

1. Wybierz poziom trwałości Gold lub Silver dla typów węzłów w klastrze, co zapewnia integrację infrastruktury. Który następnie automatycznie usunąć węzły z naszych usług systemowych (FM) stan podczas skalowania w dół.
Zapoznaj się [ze szczegółami dotyczącymi poziomów trwałości tutaj](service-fabric-cluster-capacity.md)

2. Po skalowaniu wystąpienia maszyny Wirtualnej w dół należy wywołać [polecenie cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Klastry sieci szkieletowej usług wymagają pewnej liczby węzłów, które mają być dostępne przez cały czas, aby zachować dostępność i zachować stan — określany jako "utrzymywanie kworum". Tak, jest to zazwyczaj niebezpieczne, aby zamknąć wszystkie maszyny w klastrze, chyba że po raz pierwszy wykonał [pełną kopię zapasową stanu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższymi tematami, aby dowiedzieć się więcej o planowaniu pojemności klastra, uaktualnianiu klastra i usługach partycjonowania:

* [Planowanie pojemności klastra](service-fabric-cluster-capacity.md)
* [Uaktualnienia klastra](service-fabric-cluster-upgrade.md)
* [Usługi stanowe partycji dla maksymalnej skali](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
