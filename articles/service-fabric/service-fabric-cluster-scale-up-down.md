---
title: Skalowanie klastra usługi Service Fabric wewnątrz lub na zewnątrz | Dokumentacja firmy Microsoft
description: Klaster usługi Service Fabric wewnątrz lub na zewnątrz można skalować w celu dopasowania żądanie przez ustawienie reguł skalowania automatycznego dla każdego zestawu skalowania maszyn typu/wirtualnych węzła. Dodawanie lub usuwanie węzłów w klastrze usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: aljo
ms.openlocfilehash: d820898b1a0cc26d6832be9d302c74306fa4882f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056487"
---
# <a name="read-before-you-scale"></a>Przeczytaj przed przystąpieniem do skalowania
Skalowanie zasobów obliczeniowych do źródła obciążenia robocze aplikacji wymaga planowania zamierzone, prawie zawsze będzie trwać dłużej niż godzinę w środowisku produkcyjnym i wymagają zrozumieć swoje obciążenia i kontekst biznesowy; w rzeczywistości Jeśli to działanie przed nigdy nie zostały wykonane, zaleca się uruchamiania, odczytując i interpretując kolejne [zagadnienia dotyczące planowania pojemności klastra usługi Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-capacity), przed kontynuowaniem pozostałej części tego dokumentu. To zalecenie jest uniknięcie niezamierzone problemy LiveSite i zalecane jest również, że testujesz operacje, które użytkownik chce wykonać w środowisku nieprodukcyjnym. W dowolnym momencie możesz [zgłosić problemy w środowisku produkcyjnym lub poprosić płatnej pomocy technicznej dla platformy Azure](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support#report-production-issues-or-request-paid-support-for-azure). Dla inżynierów przydzielone do wykonywania tych operacji, które posiadają odpowiedniego kontekstu w tym artykule opisano operacje skalowania, ale należy zdecydować i zrozumieć, jakie operacje są odpowiednie dla danego przypadku użycia; jakie zasoby do skali (procesor CPU, Magazyn, pamięć), np. jakie kierunek skalowania (w pionie lub poziomie) i jakie operacje do wykonania (wdrożenie szablonu zasobów, Portal, programu PowerShell/interfejsu wiersza polecenia).

# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skalowanie klastra usługi Service Fabric wewnątrz lub na zewnątrz przy użyciu reguł skalowania automatycznego lub ręcznego
Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze usługi Service Fabric zostanie określony jako oddzielne maszyny wirtualnej zestawu skalowania. Każdy typ węzła może następnie być skalowana w lub się niezależnie od siebie, mają różne zestawy otwartych portów i może mieć różne metryki pojemności. Dowiedz się więcej o go w [elementy NodeType usługi Service Fabric](service-fabric-cluster-nodetypes.md) dokumentu. Ponieważ typy węzłów usługi Service Fabric w klastrze składają się z zestawami skalowania maszyn wirtualnych na zapleczu, należy skonfigurować reguły automatycznego skalowania dla zestawu skalowania maszyn typu/wirtualnej każdego węzła.

> [!NOTE]
> Twoja subskrypcja musi mieć wystarczającej liczby rdzeni, aby dodać nowe maszyny wirtualne, które tworzą ten klaster. Nie ma nie nastąpi sprawdzanie poprawności modelu, dzięki czemu uzyskujesz czas niepowodzenie wdrożenia, jeśli dowolny z limitów przydziału są osiągane. Typ jednego węzła nie można także po prostu przekracza 100 węzłów na zestawu skalowania maszyn wirtualnych. Użytkownik może być konieczne dodanie zestawu skalowania maszyn wirtualnych w celu osiągnięcia skali docelowej i automatyczne skalowanie nie może automagically Dodaj VMSS firmy. Dodawanie firmy VMSS w miejscu do klastra na żywo to trudne zadanie i często skutkuje to użytkownikom inicjowanie obsługi administracyjnej nowych klastrów z typami odpowiedniego węzła, aprowizowane w czasie tworzenia; [Planowanie pojemności klastra](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-capacity) odpowiednio. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Wybierz węzeł typu/wirtualnych zestawu skalowania skalowania maszyn
Obecnie nie jest możliwe do określenia reguł skalowania automatycznego dla zestawów skalowania maszyn wirtualnych przy użyciu portalu, więc Poinformuj nas za pomocą programu Azure PowerShell (1.0 i nowsze) listy typów węzłów, a następnie dodać reguł skalowania automatycznego do nich.

Aby uzyskać listę zestawu skalowania maszyn wirtualnych, które tworzą klaster, uruchom następujące polecenia cmdlet:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ustawianie reguł skalowania automatycznego dla węzła typu/wirtualnych zestawu skalowania maszyn
Jeśli klaster zawiera wiele typów węzłów, a następnie powtórz to dla każdego węzła typy/wirtualnych skalowania maszyn ustawia chcesz skalować (przychodzący lub wychodzący). Przed skonfigurowaniem skalowania automatycznego należy wziąć pod uwagę potrzebną liczbę węzłów. Minimalna liczba węzłów wymagana dla typu węzła podstawowego zależy od wybranego poziomu niezawodności. Przeczytaj więcej na temat [poziomów niezawodności](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skalowanie w dół węzła podstawowego typu aby była ona mniejsza niż minimalna liczba wykonującego niestabilne klastra lub obniżyć jej. Może to spowodować utratę danych dla aplikacji i usług systemowych.
> 
> 

Obecnie funkcja automatycznego skalowania nie jest wymuszany przez obciążeń, które aplikacje mogą raportowania w usłudze Service Fabric. Dlatego w tym momencie, w których Skalowanie automatyczne Ci czysto jest wymuszany przez liczniki wydajności, które są emitowane przez każdą maszynę wirtualną wystąpienia zestawu skalowania.  

Wykonaj te instrukcje [do skonfigurowania automatycznego skalowania dla każdego zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> W dół scenariusza, chyba że typu węzła ma poziom trwałości, Gold i Silver potrzebne do wywoływania [polecenia cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) o nazwie odpowiedni węzeł. Dla trwałości brązowy nie zaleca się skalować w dół więcej niż jeden węzeł w danym momencie.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ręcznie Dodaj maszyny wirtualne z węzła typu/wirtualną zestawu skalowania maszyn
Wykonaj przykładowe/zgodnie z instrukcjami w [galerii szablonów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych w każdym elemencie Nodetype. 

> [!NOTE]
> Dodawanie maszyn wirtualnych trwa pewien czas, dlatego nie powinna mieć dodatki do było natychmiastowe. Dlatego należy zaplanować dodanie pojemności, również w czasie, aby umożliwić ponad 10 minut, zanim pojemności maszyn wirtualnych jest dostępny dla replik / service wystąpienia, aby pobrać umieszczone.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Ręcznie usuń maszyny wirtualne z węzła podstawowego typu/wirtualnych zestawu skalowania maszyn
> [!NOTE]
> Uruchom usługi systemowe service fabric w typie podstawowym węzłem w klastrze. Tak powinien nigdy nie zamknięty lub w dół liczbę wystąpień w tym typy węzłów gwarantuje mniejsza niż warstwa niezawodności. Zapoznaj się [szczegółowe informacje dotyczące warstwy niezawodności tutaj](service-fabric-cluster-capacity.md). 
> 
> 

Należy wykonać następujące kroki w jednym wystąpieniu maszyny Wirtualnej w danym momencie. Dzięki temu usługi systemowe (i usługi stanowe) być zamknięcie wystąpienia maszyny Wirtualnej, które są usuwane i nowej repliki utworzony w innych węzłach.

1. Uruchom [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) z zamiarem "RemoveNode" Wyłącz węzeł zamierzasz usunąć (najwyższy wystąpienia tego typu węzła).
2. Uruchom [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) aby upewnić się, że węzeł rzeczywiście zmienił się na wyłączone. Jeśli nie, zaczekaj, aż węzeł jest wyłączony. Nie można Pospiesz się w tym kroku.
3. Wykonaj przykładowe/zgodnie z instrukcjami w [galerii szablonów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych o jeden w tego typu węzła. Wystąpienie usuwane jest najwyższym wystąpienia maszyny Wirtualnej. 
4. Powtórz kroki od 1 do 3, zgodnie z potrzebami, ale nigdy nie skalowania w zależności od liczby wystąpień w typach węzła podstawowego mniejszą niż gwarantuje warstwy niezawodności. Zapoznaj się [szczegółowe informacje dotyczące warstwy niezawodności tutaj](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Ręcznie usuń maszyny wirtualne z węzła innego niż podstawowy typ/wirtualnych zestawu skalowania maszyn
> [!NOTE]
> Usługa stanowa wymaga określonej liczby węzłów można zawsze do zapewnienia dostępności i zachować stan usługi. W bardzo minimalne należy liczbę węzłów równa docelowej liczbę zestawu replik partycji/usługi. 
> 
> 

Wykonaj następujące kroki w jednym wystąpieniu maszyny Wirtualnej jest konieczne w danym momencie. Dzięki temu usługi systemowe (i usługi stanowe) na łagodne zamykanie na wystąpieniu maszyny Wirtualnej, które są usuwane i nowej repliki utworzony inne miejsce.

1. Uruchom [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) z zamiarem "RemoveNode" Wyłącz węzeł zamierzasz usunąć (najwyższy wystąpienia tego typu węzła).
2. Uruchom [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) aby upewnić się, że węzeł rzeczywiście zmienił się na wyłączone. Jeśli nie, zaczekaj, aż węzeł jest wyłączony. Nie można Pospiesz się w tym kroku.
3. Wykonaj przykładowe/zgodnie z instrukcjami w [galerii szablonów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych o jeden w tego typu węzła. Spowoduje to usunięcie obecnie najwyższy wystąpienia maszyny Wirtualnej. 
4. Powtórz kroki od 1 do 3, zgodnie z potrzebami, ale nigdy nie skalowania w zależności od liczby wystąpień w typach węzła podstawowego mniejszą niż gwarantuje warstwy niezawodności. Zapoznaj się [szczegółowe informacje dotyczące warstwy niezawodności tutaj](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Zachowania, można zaobserwować w narzędziu Service Fabric Explorer
Po przeskalowaniu klastra w górę narzędzia Service Fabric Explorer będzie odzwierciedlać liczbę węzłów (wystąpienia zestawu skalowania maszyn wirtualnych), które są częścią klastra.  Jednak w przypadku skalowania klastra w dół możesz zostanie wyświetlony wystąpienia węzła usuniętych i maszyn wirtualnych, wyświetlana w złej kondycji, chyba że wywołanie [polecenia Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) o nazwie odpowiedni węzeł.   

Oto wyjaśnienia tego zachowania.

Węzły w narzędziu Service Fabric Explorer są odbicia jakie usługi systemowe Service Fabric (FM specjalnie) obsługującemu liczby węzłów w klastrze miały/ma. Skalowanie zestawu w dół skalowania maszyn wirtualnych, maszyna wirtualna została usunięta, ale FM usługi system nadal sądzą, że węzeł (który został zamapowany na maszynę Wirtualną, która została usunięta) przechodzi. Dlatego narzędzia Service Fabric Explorer w dalszym ciągu ten węzeł jest wyświetlany (Jeśli stan kondycji może być błąd lub nieznany).

Aby upewnić się, że węzeł zostanie usunięty po usunięciu maszyny Wirtualnej, masz dwie opcje:

1) Wybierz poziom trwałości Gold i Silver dla typów węzłów w klastrze, co daje integracji infrastruktury. Które następnie automatycznie usunie węzły z naszych stanu usługi (FM) systemu skalowanie w.
Zapoznaj się [szczegółowe informacje dotyczące poziomów trwałości](service-fabric-cluster-capacity.md)

2) Po wystąpieniu maszyny Wirtualnej został przeskalowano w dół, należy wywołać [polecenia cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

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
