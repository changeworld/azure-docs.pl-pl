---
title: Zakończenie powiadomienia o wystąpieniach zestawu skalowania maszyn wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się, jak włączyć powiadomienia o zakończeniu dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2019
ms.author: manayar
ms.openlocfilehash: de303032fcbbde30534c802e3d5185aedf05cb98
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076240"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Zakończenie powiadomienia o wystąpieniach zestawu skalowania maszyn wirtualnych platformy Azure (wersja zapoznawcza)
Wystąpienia zestawu skalowania mogą zrezygnować z otrzymywania powiadomień o zakończeniu wystąpienia i ustawiać wstępnie zdefiniowany limit czasu opóźnienia dla operacji przerywania. Powiadomienie o wygaśnięciu jest wysyłane za pomocą usługi Azure Metadata Service — [Scheduled Events](../virtual-machines/windows/scheduled-events.md), która zapewnia powiadomienia i opóźniać wpływ na operacje, takie jak ponowny rozruch i ponowne wdrażanie. Rozwiązanie w wersji zapoznawczej dodaje kolejne zdarzenie — Zakończ — do listy Scheduled Events, a skojarzone opóźnienie zdarzenia zakończenia będzie zależeć od limitu opóźnienia określonego przez użytkowników w konfiguracjach modelu zestawu skalowania.

Po zarejestrowaniu w funkcji wystąpienia zestawu skalowania nie muszą czekać, aż określony limit czasu wygaśnie, zanim wystąpienie zostanie usunięte. Po otrzymaniu powiadomienia o przerwaniu wystąpienie może zostać usunięte w dowolnym momencie przed upływem limitu czasu zakończenia.

> [!IMPORTANT]
> Powiadomienie o przerwaniu dla wystąpień zestawu skalowania jest obecnie dostępne w publicznej wersji zapoznawczej. W przypadku korzystania z funkcji publicznej wersji zapoznawczej opisanej poniżej nie są konieczne żadne procedury zgody.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-terminate-notifications"></a>Włącz powiadomienia o zakończeniu
Istnieje wiele sposobów włączania powiadomień o zakończeniu dla wystąpień zestawu skalowania, jak opisano w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład umożliwia powiadomienie o przerwaniu dla modelu zestawu skalowania.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Powyższy blok Określa opóźnienie limitu czasu wynoszący 5 minut (wskazywane przez *PT5M*) dla każdej operacji przerywania dla wszystkich wystąpień w zestawie skalowania. Pole *notBeforeTimeout* może przyjmować dowolną wartość z przedziału od 5 do 15 minut w formacie ISO 8601. Można zmienić domyślny limit czasu dla operacji przerwania, modyfikując właściwość *notBeforeTimeout* w obszarze *terminateNotificationProfile* opisany powyżej.

Po włączeniu *scheduledEventsProfile* na modelu zestawu skalowania i ustawieniu *notBeforeTimeout*należy zaktualizować poszczególne wystąpienia do [najnowszego modelu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby odzwierciedlić zmiany.

> [!NOTE]
>Powiadomienia o przerwaniu dla wystąpień zestawu skalowania można włączyć tylko przy użyciu interfejsu API w wersji 2019-03-01 lub nowszej.

### <a name="azure-powershell"></a>Azure PowerShell
Podczas tworzenia nowego zestawu skalowania można włączyć powiadomienia o zakończeniu dla zestawu skalowania za pomocą polecenia cmdlet [New-AzVmssVM](/powershell/module/az.compute/new-azvmss) .

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

W powyższym przykładzie tworzony jest nowy zestaw skalowania z włączonymi powiadomieniami o przerwaniu z użyciem domyślnego limitu czasu 5 minut. Podczas tworzenia nowego zestawu skalowania parametr *TerminateScheduledEvents* nie wymaga wartości. Aby zmienić wartość limitu czasu, określ żądany limit czasu za pomocą parametru *TerminateScheduledEventNotBeforeTimeoutInMinutes* .

Użyj polecenia cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmss) , aby włączyć powiadomienia o zakończeniu dla istniejącego zestawu skalowania.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Powyższy przykład włącza powiadomienia o przerwaniu w istniejącym zestawie skalowania i ustawia dla zdarzenia przerwania 15-minutowy limit czasu.

Po włączeniu zaplanowanych zdarzeń w modelu zestawu skalowania i ustawieniu limitu czasu należy zaktualizować poszczególne wystąpienia do [najnowszego modelu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby odzwierciedlić zmiany.

## <a name="get-terminate-notifications"></a>Otrzymuj powiadomienia o zakończeniu

Powiadomienia o zakończeniu są dostarczane za pomocą [Scheduled Events](../virtual-machines/windows/scheduled-events.md), który jest Metadata Service platformy Azure. Usługa Azure Metadata umożliwia ujawnianie informacji na temat uruchamiania Virtual Machines przy użyciu punktu końcowego REST dostępnego z poziomu maszyny wirtualnej. Informacje są dostępne za pośrednictwem adresu IP bez obsługi routingu, aby nie były widoczne poza maszyną wirtualną.

Scheduled Events jest włączone dla zestawu skalowania przy pierwszym żądaniu dla zdarzeń. Możesz oczekiwać opóźnienia odpowiedzi podczas pierwszego wywołania przez maksymalnie dwie minuty. Należy okresowo wysyłać zapytania do punktu końcowego w celu wykrycia przyszłych zdarzeń konserwacji oraz stanu bieżących działań konserwacyjnych.

Scheduled Events jest wyłączone dla zestawu skalowania, jeśli wystąpienia zestawu skalowania nie zgłaszają żądania przez 24 godziny.

### <a name="endpoint-discovery"></a>Odnajdywanie punktów końcowych
W przypadku maszyn wirtualnych z obsługą sieci wirtualnej Metadata Service jest dostępna ze statycznego adresu IP bez obsługi routingu, 169.254.169.254.

Pełny punkt końcowy dla najnowszej wersji Scheduled Events dla tego podglądu to:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="query-response"></a>Odpowiedź na zapytanie
Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że aktualnie nie ma żadnych zaplanowanych zdarzeń.

W przypadku zaplanowanych zdarzeń odpowiedź zawiera tablicę zdarzeń. W przypadku zdarzenia "Przerwij" odpowiedź będzie wyglądać następująco:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation jest elementem ETag i zapewnia łatwy sposób sprawdzenia, czy ładunek zdarzeń został zmieniony od czasu ostatniego zapytania.

Aby uzyskać więcej informacji na temat każdego z powyższych pól, zobacz dokumentację Scheduled Events dla [systemów Windows](../virtual-machines/windows/scheduled-events.md#event-properties) i [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Reagowanie na zdarzenia
Po uzyskaniu nadchodzącego zdarzenia i zakończeniu logiki w celu bezpiecznego zamknięcia możesz zatwierdzić zaległe zdarzenie, wykonując wywołanie do usługi metadanych przy użyciu EventId. Wywołanie POST wskazuje na platformę Azure, że może kontynuować usuwanie maszyny wirtualnej.

Poniżej znajduje się kod JSON oczekiwany w treści żądania POST. Żądanie powinno zawierać listę StartRequests. Każdy StartRequest zawiera EventId dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Upewnij się, że każda maszyna wirtualna w zestawie skalowania zatwierdza tylko EventID odpowiednie dla tej maszyny wirtualnej. Maszyna wirtualna może uzyskać własną nazwę maszyny wirtualnej [za pomocą metadanych wystąpienia](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Ta nazwa przyjmuje postać "{Scale-Set-Name} _ {instance-ID}" i zostanie wyświetlona w sekcji "Resources" odpowiedzi zapytania opisanej powyżej.

Możesz również zapoznać się z przykładowymi skryptami służącymi do wykonywania zapytań i reagowania na zdarzenia przy użyciu [programu PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) i języka [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania
-   Przerywaj powiadomienia tylko w operacjach "Delete" — wszystkie operacje usuwania (skalowanie ręczne lub automatyczne skalowanie zainicjowane) będą generować zdarzenia przerwania, jeśli zestaw skalowania ma włączone *scheduledEventsProfile* . Inne operacje, takie jak ponowne uruchamianie, odtwarzanie obrazu, ponowne wdrażanie i zatrzymywanie/cofanie alokacji nie generują zdarzeń zakończenia. Nie można włączyć powiadomień o przerwaniu dla maszyn wirtualnych o niskim priorytecie.
-   Brak obowiązkowego oczekiwania na przekroczenie limitu czasu — można uruchomić operację przerwania w dowolnym momencie po odebraniu zdarzenia i upływie czasu *NotBefore* zdarzenia.
-   Obowiązkowe usuwanie przy limicie czasu — wersja zapoznawcza nie zapewnia możliwości rozszerzenia wartości limitu czasu po wygenerowaniu zdarzenia. Po upływie limitu czasu zdarzenie oczekujące zakończenie zostanie przetworzone, a maszyna wirtualna zostanie usunięta.
-   Modyfikowalna wartość limitu czasu — można w dowolnym momencie zmodyfikować wartość limitu czasu przed usunięciem wystąpienia, modyfikując właściwość *notBeforeTimeout* w modelu zestawu skalowania i aktualizując wystąpienia maszyn wirtualnych do najnowszego modelu.
-   Zatwierdź wszystkie oczekujące usunięcia — Jeśli istnieje oczekujące usunięcie VM_1, które nie zostało zatwierdzone, i zatwierdzono inne zdarzenie zakończenia na VM_2, VM_2 nie zostanie usunięte, dopóki nie zostanie zatwierdzone zdarzenie zakończenia dla VM_1 lub upłynął limit czasu. Po zatwierdzeniu zdarzenia zakończenia dla elementu VM_1 zostaną usunięte zarówno VM_1, jak i VM_2.
-   Zatwierdź wszystkie jednoczesne operacje usuwania — rozszerzenie powyższego przykładu, jeśli VM_1 i VM_2 mają ten sam czas *NotBefore* , to oba zdarzenia zakończenia muszą być zatwierdzone lub żadna maszyna wirtualna nie zostanie usunięta przed upływem limitu czasu.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nie można włączyć scheduledEventsProfile
Jeśli zostanie wyświetlony błąd "nieprawidłowego żądania" z komunikatem o błędzie z informacją o tym, że nie można odnaleźć elementu członkowskiego "scheduledEventsProfile" w obiekcie typu "VirtualMachineProfile" ", sprawdź wersję interfejsu API używaną dla operacji zestawu skalowania. Dla tej wersji zapoznawczej wymagany jest interfejs API obliczeń w wersji **2019-03-01** lub nowszej.

### <a name="failure-to-get-terminate-events"></a>Nie można pobrać zdarzeń zakończenia
Jeśli nie otrzymujesz żadnych zdarzeń **przerwania** za pomocą Scheduled Events, sprawdź wersję interfejsu API używaną do pobierania zdarzeń. Do zakończenia zdarzeń jest wymagany interfejs API Metadata Service w wersji **2019-01-01** lub nowszej.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Trwa pobieranie zdarzenia przerwania z nieprawidłowym czasem NotBefore  
Po włączeniu *scheduledEventsProfile* na modelu zestawu skalowania i ustawieniu *notBeforeTimeout*należy zaktualizować poszczególne wystąpienia do [najnowszego modelu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby odzwierciedlić zmiany.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
