---
title: Skalowanie klastra usługi sieć szkieletowa przychodzący lub wychodzący | Dokumentacja firmy Microsoft
description: Skalowanie klastra usługi sieć szkieletowa przychodzący lub wychodzący odpowiadające żądanie przez ustawienie zasady automatycznego skalowania dla zestawu skalowania maszyn typu/wirtualnej każdego węzła. Dodaj lub usuń węzły do klastra sieci szkieletowej usług
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
ms.openlocfilehash: c2479dad013bfcb738e61e67cc8cf9584b4d11cc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204818"
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skalowanie klastra usługi sieć szkieletowa przychodzący lub wychodzący przy użyciu reguł automatycznego skalowania lub ręcznie
Zestawy skalowania maszyny wirtualnej są zasobu obliczeń platformy Azure, który służy do wdrażania i zarządzania nimi jako zestaw kolekcji maszyn wirtualnych. Każdy typ węzła który jest zdefiniowany w klastrze usługi sieć szkieletowa jest skonfigurowany jako zestaw skali oddzielnej maszynie wirtualnej. Każdy typ węzła można skalować w lub wychodzących niezależnie, mają różne zestawy otwartych portów i może mieć inną pojemność metryki. Dowiedz się więcej o w [elementów sieci szkieletowej usług NodeType](service-fabric-cluster-nodetypes.md) dokumentu. Ponieważ sieci szkieletowej usług typy węzłów w klastrze składają się z zestawy skalowania maszyny wirtualnej w wewnętrznej bazie danych, należy skonfigurować reguły automatycznego skalowania dla zestawu skali maszyny każdego węzła typu/wirtualnej.

> [!NOTE]
> Subskrypcja musi mieć za mało rdzeni, aby dodać nowe maszyny wirtualne wchodzące w skład tego klastra. Nie ma Weryfikacja modelu nie, aby uzyskać awaria czas wdrażania, jeśli dowolne limity przydziału są osiągane.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Wybierz węzeł typu/wirtualnej zestaw skalowania skalowania maszyny
Obecnie nie jest możliwe do określenia reguł automatycznego skalowania zestawy skalowania maszyny wirtualnej za pomocą portalu, więc Daj nam listy typów węzłów, a następnie dodaj zasady automatycznego skalowania do nich za pomocą programu Azure PowerShell (1.0 +).

Aby uzyskać listę zestaw skali maszyny wirtualnej, która składa się z klastrem, uruchom następujące polecenia cmdlet:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ustawianie reguł automatycznego skalowania dla węzła typu/wirtualnej zestawu skali maszyny
Jeśli klaster ma wiele typów węzła, następnie powtarzać dla każdego węzła typy/wirtualnej skalowania maszyny ustawia chcesz skalować (przychodzący lub wychodzący). Przed skonfigurowaniem skalowania automatycznego należy wziąć pod uwagę potrzebną liczbę węzłów. Minimalna liczba węzłów wymagana dla typu węzła podstawowego zależy od wybranego poziomu niezawodności. Przeczytaj więcej na temat [poziomów niezawodności](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skalowania węzła podstawowego wpisz, aby była mniejsza niż minimalna liczba upewnij, niestabilny klastra lub przełączyć go w dół. Może to spowodować utratę danych dla aplikacji i usług systemowych.
> 
> 

Obecnie funkcja automatycznego skalowania nie jest wymuszany przez obciążeń, które aplikacje mogą raportowania sieci szkieletowej usług. Dlatego w tym czasie skalowania automatycznego uzyskasz czysto jest wymuszany przez liczniki wydajności, które są emitowane przez każdy z maszyny wirtualnej zestawu skalowania wystąpień.  

Wykonaj te instrukcje [ustanowienie automatycznego skalowania dla każdego zestawu skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> W skali w dół scenariusza, chyba że danego typu węzła ma poziom trwałości Gold lub Silver należy wywołać [polecenia cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) o nazwie odpowiedni węzeł.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ręcznie Dodaj maszyny wirtualne z węzła typu/wirtualną zestaw skali maszyny
Wykonaj przykładowe/instrukcje [galerię szablonów szybki start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych w każdym Nodetype. 

> [!NOTE]
> Dodawanie maszyn wirtualnych jest czasochłonne, więc nie będzie dodatków być natychmiastowe. Dlatego należy zaplanować zwiększyć wydajność również w czasie, aby umożliwić ponad 10 minut, zanim pojemności maszyny Wirtualnej jest dostępna dla replik / service instancje, aby pobrać umieszczone.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Ręcznie usuń maszyny wirtualne z węzła podstawowego typu/wirtualnej zestawu skali maszyny
> [!NOTE]
> Usługa sieci szkieletowej systemu usługi są uruchomione w typie podstawowym węzła w klastrze. Nigdy nie należy zamknąć lub skalować liczbę wystąpień w tym typy węzłów gwarantuje mniejsza niż warstwa niezawodności. Zapoznaj się [szczegółowe informacje w tym miejscu warstwach niezawodności](service-fabric-cluster-capacity.md). 
> 
> 

Trzeba wykonać następujące kroki jednego wystąpienia maszyny Wirtualnej w czasie. Dzięki temu usługi systemowe (i usługi stanowej) można zamknąć bezpiecznie na wystąpienie maszyny Wirtualnej, które są usuwane i nowej repliki utworzony w innych węzłach.

1. Uruchom [ServiceFabricNode Wyłącz](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) z zamiarem "RemoveNode", aby wyłączyć węzła zamierzasz usunąć (najwyższy wystąpienia tego typu węzła).
2. Uruchom [Get ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) aby upewnić się, że węzeł w rzeczywistości przeszła na wyłączone. Jeśli nie, poczekaj, aż węzła jest wyłączone. Nie można Pospiesz się w tym kroku.
3. Wykonaj przykładowe/instrukcje [galerię szablonów szybki start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych o jeden w ramach tego typu węzła. Wystąpienie usunięte jest najwyższym wystąpienia maszyny Wirtualnej. 
4. Powtórz kroki od 1 do 3 zgodnie z potrzebami, ale nigdy nie skalować liczbę wystąpień w typach węzła podstawowego mniej niż gwarantuje warstwa niezawodności. Zapoznaj się [szczegółowe informacje w tym miejscu warstwach niezawodności](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Ręcznie usuń maszyny wirtualne z węzła innego niż podstawowy typ/wirtualnej zestawu skali maszyny
> [!NOTE]
> Dla usługi stanowej trzeba niektórych liczbę węzłów do maksymalnie zawsze można zachować dostępność i zachować stanu usługi. W bardzo minimalna należy liczby węzłów równa docelowa liczba zestawu replik partycji/usługi. 
> 
> 

Wykonaj następujące kroki jednego wystąpienia maszyny Wirtualnej należy naraz. Dzięki temu usługi systemowe (i usługi stanowej) można zamknąć bezpiecznie w wystąpieniu maszyny Wirtualnej, które są usuwane i nowej repliki utworzony else where.

1. Uruchom [ServiceFabricNode Wyłącz](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) z zamiarem "RemoveNode", aby wyłączyć węzła zamierzasz usunąć (najwyższy wystąpienia tego typu węzła).
2. Uruchom [Get ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) aby upewnić się, że węzeł w rzeczywistości przeszła na wyłączone. Jeśli nie, poczekaj, aż węzła jest wyłączone. Nie można Pospiesz się w tym kroku.
3. Wykonaj przykładowe/instrukcje [galerię szablonów szybki start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Aby zmienić liczbę maszyn wirtualnych o jeden w ramach tego typu węzła. Spowoduje to usunięcie teraz najwyższy wystąpienia maszyny Wirtualnej. 
4. Powtórz kroki od 1 do 3 zgodnie z potrzebami, ale nigdy nie skalować liczbę wystąpień w typach węzła podstawowego mniej niż gwarantuje warstwa niezawodności. Zapoznaj się [szczegółowe informacje w tym miejscu warstwach niezawodności](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Zachowania można zaobserwować w narzędziu Service Fabric Explorer
Skalowanie w górę klastra Eksploratora usługi sieć szkieletowa będzie odzwierciedlać liczba węzłów (wystąpienia zestawu skalowania maszyn wirtualnych), które są częścią klastra.  Jednak podczas skalowania klastra w dół możesz zostanie wyświetlone wystąpienie usunięty węzeł/wirtualna wyświetlane w złej kondycji, chyba że wywołujesz [cmd ServiceFabricNodeState Usuń](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) o nazwie odpowiedni węzeł.   

Oto wyjaśnienia tego zachowania.

Węzły wymienionych w narzędziu Service Fabric Explorer są odbicie jakie usługi systemowe platformy Service Fabric (FM specjalnie) zna liczby węzłów klastra ma/ma. Skalowanie w ustalonych skali maszyny wirtualnej, maszyna wirtualna została usunięta, ale usługa systemowa FM nadal sądzi węzeł (który został zamapowany na maszynie Wirtualnej, która została usunięta) zostanie następnie powrót. Dlatego Eksploratora usługi sieć szkieletowa jest nadal wyświetlana tego węzła (chociaż kondycja może być błąd lub nieznany).

Aby upewnić się, że węzeł zostanie usunięta po usunięciu maszyny Wirtualnej, dostępne są dwie opcje:

1) Wybierz poziom trwałości Gold lub Silver dla typów węzłów w klastrze, co umożliwia integrację infrastruktury. Które następnie automatycznie usunie węzły z naszych stanu usługi (FM) systemu skalowanie w.
Zapoznaj się [szczegółów dotyczących poziomów trwałości tutaj](service-fabric-cluster-capacity.md)

2) Po wystąpieniu maszyny Wirtualnej zostały skalowany w dół, należy wywołać [polecenia cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Klastry usługi sieć szkieletowa usług wymagają określonej liczby węzłów wynosi cały czas w celu zapewnienia dostępności i zachować stan — nazywany "utrzymywania kworum". Tak, nie jest zwykle bezpieczne zamknie wszystkie komputery w klastrze, chyba że najpierw wykonali [pełnej kopii zapasowej według stanu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące również informacje na temat planowania pojemności klastra, Uaktualnianie klastra i partycjonowanie usług:

* [Planowanie pojemności sieci klastra](service-fabric-cluster-capacity.md)
* [Uaktualnienia klastra](service-fabric-cluster-upgrade.md)
* [Usługi stanowej partycji dla maksymalną skalę](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
