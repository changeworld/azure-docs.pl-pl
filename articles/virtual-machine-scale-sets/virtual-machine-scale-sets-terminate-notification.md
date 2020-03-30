---
title: Zakończenie powiadamiania o wystąpieniach zestawu skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak włączyć powiadomienia o zakończeniu dla wystąpień zestawu skalowania maszyny wirtualnej platformy Azure
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250753"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Zakończenie powiadamiania o wystąpieniach zestawu skalowania maszyny wirtualnej platformy Azure
Wystąpienia zestawu skalowania mogą wyrazić zgodę na otrzymywanie powiadomień o zakończeniu wystąpienia i ustawić wstępnie zdefiniowany limit czasu opóźnienia na operację zakończenia. Powiadomienie o zakończeniu jest wysyłane za pośrednictwem usługi Azure Metadata Service — [zaplanowane zdarzenia](../virtual-machines/windows/scheduled-events.md), która zapewnia powiadomienia i opóźnianie operacji o skutkach, takich jak ponowne uruchomienie i ponowne wdrożenie. Rozwiązanie dodaje inne zdarzenie — Zakończ — do listy zaplanowanych zdarzeń, a skojarzone opóźnienie zdarzenia zakończenia będzie zależeć od limitu opóźnienia określonego przez użytkowników w ich konfiguracjach modelu zestawu skalowania.

Po zarejestrowaniu się w funkcji wystąpienia zestawu skalowania nie muszą czekać na wygaśnięcie określonego limitu czasu przed usunięciem wystąpienia. Po otrzymaniu powiadomienia o zakończeniu wystąpienia można wybrać do usunięcia w dowolnym momencie przed wygaśnięciem limitu czasu zakończenia.

## <a name="enable-terminate-notifications"></a>Włączanie powiadomień o zakończeniu
Istnieje wiele sposobów włączania powiadomień o zakończeniu w wystąpieniach zestawu skalowania, jak opisano w poniższych przykładach.

### <a name="azure-portal"></a>Portal Azure

Poniższe kroki umożliwiają zakończenie powiadamiania podczas tworzenia nowego zestawu skalowania. 

1. Przejdź do **zestawów skalowania maszyny wirtualnej**.
1. Wybierz **+ Dodaj,** aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **Zarządzanie.** 
1. Znajdź sekcję **Zakończenia wystąpienia.**
1. Dla **powiadomienia o rozwiązaniu wystąpienia**wybierz włącz . **On**
1. W polu **Opóźnienie zakończenia (minuty)** ustaw żądany domyślny limit czasu.
1. Po zakończeniu tworzenia nowego zestawu skalowania wybierz przycisk **Przejrzyj + utwórz.** 

> [!NOTE]
> Nie można ustawić powiadomień o zakończeniu istniejących zestawów skalowania w witrynie Azure Portal

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład umożliwia zakończenie powiadamiania w modelu zestawu skalowania.

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

Powyższy blok określa opóźnienie limitu czasu 5 minut (jak wskazuje *PT5M)* dla każdej operacji zakończenia dla wszystkich wystąpień w zestawie skalowania. Pole *notBeforeTimeout* może trwać dowolną wartość od 5 do 15 minut w formacie ISO 8601. Domyślny limit czasu dla operacji zakończenia można zmienić, modyfikując właściwość *notBeforeTimeout* w obszarze *terminateNotificationProfile opisanego* powyżej.

Po włączeniu *scheduledEventsProfile* w modelu zestawu skalowania i ustawieniu *nieBeforeTimeout,* zaktualizuj poszczególne wystąpienia do [najnowszego modelu,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) aby odzwierciedlić zmiany.

> [!NOTE]
>Powiadomienia o zakończeniu w wystąpieniach zestawu skalowania można włączyć tylko w wersji interfejsu API 2019-03-01 i wyższej

### <a name="azure-powershell"></a>Azure PowerShell
Podczas tworzenia nowego zestawu skalowania można włączyć powiadomienia o zakończeniu na zestawie skalowania przy użyciu polecenia cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Ten przykładowy skrypt przechodzi przez tworzenie zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Utwórz kompletny zestaw skalowania maszyny wirtualnej](./scripts/powershell-sample-create-complete-scale-set.md). Można podać skonfigurować zakończyć powiadomienie przez dodanie parametrów *TerminateScheduledEvents* i *TerminateScheduledEventNotBeforeTimeoutInMinutes* do obiektu konfiguracji do tworzenia zestawu skalowania. Poniższy przykład włącza funkcję z limitem czasu opóźnienia 10 minut.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Użyj polecenia cmdlet [Update-AzVmss,](/powershell/module/az.compute/update-azvmss) aby włączyć powiadomienia o zakończeniu w istniejącym zestawie skalowania.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Powyższy przykład umożliwia zakończenie powiadomień na istniejącym zestawie skalowania i ustawia 15-minutowy limit czasu dla zdarzenia zakończenia.

Po włączeniu zaplanowanych zdarzeń w modelu zestawu skalowania i ustawieniu limitu czasu zaktualizuj poszczególne wystąpienia do [najnowszego modelu,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) aby odzwierciedlić zmiany.

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniższy przykład służy do włączania powiadomienia o zakończeniu podczas tworzenia nowego zestawu skalowania.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

Powyższy przykład najpierw tworzy grupę zasobów, a następnie tworzy nowy zestaw skalowania z powiadomieniami zakończenia włączone dla 10-minutowego domyślnego limitu czasu.

Poniższy przykład służy do włączania powiadomienia o zakończeniu w istniejącym zestawie skalowania.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Otrzymywanie powiadomień o zakończeniu

Powiadomienia o zakończeniu są dostarczane za pośrednictwem [zaplanowanych zdarzeń,](../virtual-machines/windows/scheduled-events.md)która jest usługą Azure Metadata Service. Usługa metadanych platformy Azure udostępnia informacje o uruchamianiu maszyn wirtualnych przy użyciu punktu końcowego REST dostępne z poziomu maszyny wirtualnej. Informacje są dostępne za pośrednictwem nie rutable IP, dzięki czemu nie jest narażony poza maszyną wirtualną.

Zaplanowane zdarzenia są włączone dla zestawu skalowania przy pierwszym żądaniu zdarzeń. Możesz spodziewać się opóźnionej odpowiedzi w pierwszym wywołaniu do dwóch minut. Okresowo wysyłaj zapytania do punktu końcowego, aby wykryć nadchodzące zdarzenia konserwacji i stan bieżących działań konserwacyjnych.

Zaplanowane zdarzenia są wyłączone dla zestawu skalowania, jeśli wystąpienia zestawu skalowania nie składają żądania przez 24 godziny.

### <a name="endpoint-discovery"></a>Odnajdowanie punktu końcowego
W przypadku maszyn wirtualnych usługa metadanych jest dostępna ze statycznego nierutowalnej usługi IP 169.254.169.254.

Pełny punkt końcowy dla najnowszej wersji zaplanowanych zdarzeń jest:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Odpowiedź na zapytanie
Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że obecnie nie są zaplanowane żadne zdarzenia.

W przypadku, gdy istnieją zaplanowane zdarzenia, odpowiedź zawiera tablicę zdarzeń. W przypadku zdarzenia "Zakończ" odpowiedź będzie wyglądać następująco:
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
*DocumentIncarnation* jest ETag i zapewnia łatwy sposób, aby sprawdzić, czy ładunek zdarzenia uległ zmianie od ostatniego zapytania.

Aby uzyskać więcej informacji na temat każdego z powyższych pól, zobacz dokumentację zaplanowane zdarzenia dla [systemów Windows](../virtual-machines/windows/scheduled-events.md#event-properties) i [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Reagowanie na zdarzenia
Po zapoznaniu się z nadchodzącym wydarzeniem i zakończeniu logiki dla wdzięku zamknięcia, można zatwierdzić zdarzenie zaległe, wykonując wywołanie POST do usługi metadanych z EventId. Wywołanie POST wskazuje platformie Azure, że może kontynuować usuwanie maszyny Wirtualnej.

Poniżej znajduje się json oczekiwany w treści żądania POST. Żądanie powinno zawierać listę żądań StartRequests. Każdy StartRequest zawiera EventId dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Upewnij się, że każda maszyna wirtualna w zestawie skalowania zatwierdza tylko identyfikator zdarzenia odpowiedni tylko dla tej maszyny Wirtualnej. Maszyna wirtualna może uzyskać własną nazwę maszyny Wirtualnej [za pośrednictwem metadanych wystąpienia](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Ta nazwa ma postać "{scale-set-name}_{instance-id}" i będzie wyświetlana w sekcji "Zasoby" odpowiedzi na zapytanie opisanej powyżej.

Można również odwołać się do przykładowych skryptów do wykonywania zapytań i odpowiadania na zdarzenia przy użyciu programów [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) i [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania
-   Zakończ powiadomienia tylko na "delete" operacji — wszystkie operacje usuwania (ręczne usuwanie lub skalowanie automatyczne in) wygeneruje Zakończ zdarzenia, jeśli zestaw skalowania *zaplanowałEwydaniaProfile* włączone. Inne operacje, takie jak ponowne uruchamianie, ponowne zakładanie, ponowne wdrożenie i zatrzymanie/przydzielenia nie generują zdarzeń Zakończenia. Nie można włączyć powiadomień o zakończeniu dla maszyn wirtualnych o niskim priorytecie.
-   Nie obowiązkowe oczekiwanie na limit czasu — można rozpocząć operację zakończenia w dowolnym momencie po odebraniu zdarzenia i przed wygaśnięciem czasu *nieprzeznaczenia* zdarzenia.
-   Obowiązkowe usuwanie w przesuwu — nie ma możliwości przedłużenia wartości limitu czasu po wygenerowaniu zdarzenia. Po upływie limitu czasu zdarzenie oczekujące zakończenie zostanie przetworzone, a maszyna wirtualna zostanie usunięta.
-   Wartość limitu czasu modyfikowalne — można zmodyfikować wartość limitu czasu w dowolnym momencie przed usunięciem wystąpienia, modyfikując *właściwość notBeforeTimeout* w modelu zestawu skalowania i aktualizując wystąpienia maszyny Wirtualnej do najnowszego modelu.
-   Zatwierdź wszystkie oczekujące usunięcia — jeśli na VM_1 nie zostanie zatwierdzony, a w VM_2 zatwierdzono inne zdarzenie zakończenia, VM_2 nie zostanie usunięte, dopóki zdarzenie zakończenia dla VM_1 nie zostanie zatwierdzone lub upłynie limit czasu. Po zatwierdzeniu zdarzenia zakończenia dla VM_1, usuwane są zarówno VM_1, jak i VM_2.
-   Zatwierdź wszystkie jednoczesne usunięcia — rozszerzenie powyższego przykładu, jeśli VM_1 i VM_2 mają ten sam *NotBefore* czas, a następnie oba zakończenia zdarzenia muszą zostać zatwierdzone lub żadna maszyna wirtualna nie jest usuwana przed upływem limitu czasu.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="failure-to-enable-scheduledeventsprofile"></a>Niepowodzenie włączeniu zaplanowanego profilu Wwentylcje
Jeśli pojawi się błąd "BadRequest" z komunikatem o błędzie z napisem "Nie można znaleźć elementu członkowskiego 'scheduledEventsProfile' na obiekcie typu "VirtualMachineProfile", sprawdź wersję interfejsu API używaną dla operacji zestawu skalowania. Wymagany jest interfejs API obliczeń w wersji **2019-03-01** lub nowszej. 

### <a name="failure-to-get-terminate-events"></a>Nieustanne uzyskanie zdarzeń Zakończenia
Jeśli nie otrzymujesz żadnych zdarzeń **Zakończenia** za pośrednictwem zaplanowanych zdarzeń, sprawdź wersję interfejsu API używaną do uzyskiwania zdarzeń. Interfejs API usługi metadanych w wersji **2019-01-01** lub nowszej jest wymagany dla zdarzeń Zakończenia.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Pierwsze zdarzenie Zakończ z niepoprawnym czasem NotBefore  
Po włączeniu *scheduledEventsProfile* w modelu zestawu skalowania i ustawieniu *nieBeforeTimeout,* zaktualizuj poszczególne wystąpienia do [najnowszego modelu,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) aby odzwierciedlić zmiany.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyny wirtualnej.
