---
title: Zaplanowane zdarzenia dla maszyn wirtualnych systemu Windows na platformie Azure
description: Zaplanowane zdarzenia przy użyciu usługi Azure Metadata dla maszyn wirtualnych systemu Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 2b3aa5d50822863e3aa46fcf9970e0b3e67a6f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944459"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Usługa metadanych platformy Azure: zaplanowane zdarzenia dla maszyn wirtualnych systemu Windows

Zaplanowane zdarzenia to usługa metadanych platformy Azure, która daje czas aplikacji na przygotowanie się do konserwacji maszyny wirtualnej. Zawiera informacje o nadchodzących zdarzeniach konserwacji (np. ponownym uruchomieniu), dzięki czemu aplikacja może się do nich przygotować i ograniczyć zakłócenia. Jest on dostępny dla wszystkich typów maszyn wirtualnych platformy Azure, w tym PaaS i IaaS w systemie Windows i Linux. 

Aby uzyskać informacje o zaplanowanych zdarzeniach w systemie Linux, zobacz [Zaplanowane zdarzenia dla maszyn wirtualnych z systemem Linux](../linux/scheduled-events.md).

> [!Note] 
> Zaplanowane zdarzenia są ogólnie dostępne we wszystkich regionach platformy Azure. Aby uzyskać najnowsze informacje o wersji, zobacz [Dostępność wersji i regionu.](#version-and-region-availability)

## <a name="why-scheduled-events"></a>Dlaczego zaplanowane wydarzenia?

Wiele aplikacji może korzystać z czasu, aby przygotować się do konserwacji maszyny wirtualnej. Czas może służyć do wykonywania zadań specyficznych dla aplikacji, które zwiększają dostępność, niezawodność i łatwość serwisowania, w tym: 

- Punkt kontrolny i przywracanie
- Opróżnianie połączeń
- Podstawowego trybu failover repliki 
- Usuwanie z puli modułów równoważenia obciążenia
- Rejestrowanie zdarzeń
- Wdzięczne zamknięcie 

Za pomocą zaplanowanych zdarzeń aplikacji można odnajdywać, kiedy nastąpi konserwacja i wyzwalać zadania, aby ograniczyć jego wpływ. Włączenie zaplanowanych zdarzeń daje maszynie wirtualnej minimalny czas przed wykonaniem czynności konserwacyjnej. Szczegółowe informacje można znaleźć w sekcji Planowanie zdarzeń poniżej.

Zaplanowane zdarzenia udostępnia zdarzenia w następujących przypadkach użycia:
- [Konserwacja inicjowana przez platformę](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (na przykład ponowne uruchomienie maszyny Wirtualnej, migracja na żywo lub przechowywanie aktualizacji pamięci dla hosta)
- Maszyna wirtualna jest uruchomiona na [zdegradowanym sprzęcie hosta,](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) który wkrótce zakończy się niepowodzeniem
- Konserwacja inicjowana przez użytkownika (np. ponowne uruchomienie lub ponowne wdrożenie maszyny Wirtualnej)
- Eksmisje wystąpienia zestawu instancji [maszyny wirtualnej](spot-vms.md) punktowej i [skali punktowej](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Podstawy  

Usługa metadanych platformy Azure udostępnia informacje o uruchamianiu maszyn wirtualnych przy użyciu punktu końcowego REST dostępne z poziomu maszyny wirtualnej. Informacje są dostępne za pośrednictwem nie rutable IP, tak aby nie jest narażony poza maszyną wirtualną.

### <a name="endpoint-discovery"></a>Odnajdowanie punktu końcowego
W przypadku maszyn wirtualnych z włączoną obsługą metadanych usługa metadanych jest dostępna ze statycznego nie rutowalnej usługi IP. `169.254.169.254` Pełny punkt końcowy dla najnowszej wersji zaplanowanych zdarzeń jest: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Jeśli maszyna wirtualna nie jest tworzona w sieci wirtualnej, domyślne przypadki dla usług w chmurze i klasycznych maszyn wirtualnych, dodatkowa logika jest wymagana do odnajdywanie adresu IP do użycia. Zapoznaj się z tym przykładem, aby dowiedzieć się, jak [odnajdować punkt końcowy hosta](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Dostępność wersji i regionu
Usługa zaplanowanych zdarzeń jest wersjona. Wersje są obowiązkowe, a `2019-01-01`bieżąca wersja jest .

| Wersja | Typ wydania | Regiony | Informacje o wersji | 
| - | - | - | - |
| 2019-01-01 | Ogólna dostępność | Wszystkie | <li> Dodano obsługę zestawów skalowania maszyny wirtualnej EventType "Terminate" |
| 2017-11-01 | Ogólna dostępność | Wszystkie | <li> Dodano obsługę spot vm eksmisji EventType "Preempt"<br> | 
| 2017-08-01 | Ogólna dostępność | Wszystkie | <li> Usunięto poprzedzane podkreślenie z nazw zasobów maszyn wirtualnych IaaS<br><li>Wymagania nagłówka metadanych wymuszane dla wszystkich żądań | 
| 2017-03-01 | Wersja zapoznawcza | Wszystkie |<li>Wersja początkowa |

> [!NOTE] 
> Poprzednie wersje zapoznawcza zaplanowanych zdarzeń obsługiwane {latest} jako wersja interfejsu api. Ten format nie jest już obsługiwany i zostanie przestarzały w przyszłości.

### <a name="enabling-and-disabling-scheduled-events"></a>Enabling and Disabling Scheduled Events (Włączanie i wyłączanie usługi Scheduled Events)
Zaplanowane zdarzenia są włączone dla usługi przy pierwszym zwładniu żądania zdarzeń. Należy się spodziewać opóźnionej odpowiedzi w pierwszym wywołaniu do dwóch minut. Należy okresowo wysyłać zapytania do punktu końcowego, aby wykryć nadchodzące zdarzenia konserwacji, a także stan wykonywanych czynności konserwacyjnych.

Zaplanowane zdarzenia są wyłączone dla usługi, jeśli nie złóż żądania przez 24 godziny.

### <a name="user-initiated-maintenance"></a>Konserwacja inicjowana przez użytkownika
Zainicjowana przez użytkownika konserwacja maszyny wirtualnej za pośrednictwem portalu Azure, interfejsu API, interfejsu wiersza polecenia lub programu PowerShell powoduje zaplanowane zdarzenie. Dzięki temu można przetestować logikę przygotowania konserwacji w aplikacji i umożliwia aplikacji przygotowanie do konserwacji zainicjowanej przez użytkownika.

Ponowne uruchomienie maszyny wirtualnej planuje `Reboot`zdarzenie z typem . Ponowne wdrożenie maszyny wirtualnej planuje zdarzenie z `Redeploy`typem .

## <a name="using-the-api"></a>Korzystanie z interfejsu API

### <a name="headers"></a>Nagłówki
Podczas kwerendy usługi metadanych, należy `Metadata:true` podać nagłówek, aby upewnić się, że żądanie nie zostało przypadkowo przekierowane. Nagłówek `Metadata:true` jest wymagany dla wszystkich żądań zaplanowanych zdarzeń. Nieuwzpochnienie do uwzględnienia nagłówka w żądaniu spowoduje odpowiedź bad request z usługi metadanych.

### <a name="query-for-events"></a>Kwerenda o zdarzenia
Można wyszukiwać zaplanowane zdarzenia po prostu przez następujące wywołanie:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że obecnie nie są zaplanowane żadne zdarzenia.
W przypadku, gdy istnieją zaplanowane zdarzenia, odpowiedź zawiera tablicę zdarzeń: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation jest ETag i zapewnia łatwy sposób, aby sprawdzić, czy ładunek zdarzenia uległ zmianie od ostatniego zapytania.

### <a name="event-properties"></a>Właściwości zdarzenia
|Właściwość  |  Opis |
| - | - |
| Eventid | Globalnie unikatowy identyfikator dla tego zdarzenia. <br><br> Przykład: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ zdarzenia | Wpływ powoduje to zdarzenie. <br><br> Wartości: <br><ul><li> `Freeze`: Maszyna wirtualna jest zaplanowana do wstrzymania na kilka sekund. Łączność procesora i sieci może zostać zawieszona, ale nie ma to wpływu na pamięć lub otwarte pliki. <li>`Reboot`: Maszyna wirtualna jest zaplanowana do ponownego uruchomienia (pamięć nietrwała zostanie utracona). <li>`Redeploy`: Maszyna wirtualna jest zaplanowane do przeniesienia do innego węzła (dyski efemeryczne są tracone). <li>`Preempt`: Maszyna wirtualna punktowa jest usuwana (dyski efemeryczne są tracone). <li> `Terminate`: Maszyna wirtualna jest zaplanowana do usunięcia. |
| ResourceType | Typ zasobu, na który ma wpływ to zdarzenie. <br><br> Wartości: <ul><li>`VirtualMachine`|
| Resources| Lista zasobów, które ma wpływ na to zdarzenie. Gwarantuje to, że zawiera maszyny z co najwyżej jednej [domeny aktualizacji,](manage-availability.md)ale może nie zawierać wszystkich maszyn w UD. <br><br> Przykład: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stan zdarzenia | Stan tego zdarzenia. <br><br> Wartości: <ul><li>`Scheduled`: To zdarzenie ma się rozpocząć po `NotBefore` czasie określonym w właściwości.<li>`Started`: To wydarzenie zostało rozpoczęte.</ul> Nigdy `Completed` nie podano żadnego statusu podobnego; zdarzenie nie zostanie już zwrócone po zakończeniu wydarzenia.
| Nie wcześniej niż| Czas, po którym może się rozpocząć to zdarzenie. <br><br> Przykład: <br><ul><li> pon,19 wrz 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Planowanie zdarzeń
Każde zdarzenie jest zaplanowane przez co najmniej czas w przyszłości na podstawie typu zdarzenia. Ten czas znajduje odzwierciedlenie w `NotBefore` właściwości zdarzenia. 

|Typ zdarzenia  | Minimalne powiadomienie |
| - | - |
| Zamrozić| 15 minut |
| Ponowne uruchamianie | 15 minut |
| Ponowne wdrożenie | 10 minut |
| Wywłaszczyć | 30 sekund |
| Terminate | [Możliwość konfiguracji użytkownika:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)od 5 do 15 minut |

> [!NOTE] 
> W niektórych przypadkach platforma Azure jest w stanie przewidzieć awarię hosta z powodu awarii sprzętu i podejmie próbę złagodzenia zakłóceń w usłudze przez zaplanowanie migracji. Których dotyczy problem maszyny wirtualne otrzymają zaplanowane zdarzenie z `NotBefore` zwykle kilku dni w przyszłości. Rzeczywisty czas różni się w zależności od przewidywanej oceny ryzyka awarii. Platforma Azure próbuje powiadomić z 7-dniowym wyprzedzeniem, jeśli jest to możliwe, ale rzeczywisty czas jest różny i może być mniejszy, jeśli przewidywanie jest, że istnieje duże prawdopodobieństwo, że sprzęt nie działa w najbliższym czasie. Aby zminimalizować ryzyko związane z usługą w przypadku awarii sprzętu przed rozpoczęciem migracji przez system, zaleca się samoreepcie maszyny wirtualnej tak szybko, jak to możliwe.

### <a name="event-scope"></a>Zakres zdarzenia     
Zaplanowane wydarzenia są dostarczane do:
 - Autonomiczne maszyny wirtualne
 - Wszystkie maszyny wirtualne w usłudze w chmurze      
 - Wszystkie maszyny wirtualne w zestawie dostępności      
 - Wszystkie maszyny wirtualne w grupie umieszczania zestawów skalowania.         

W rezultacie należy sprawdzić `Resources` pole w zdarzeniu, aby zidentyfikować, które maszyny wirtualne będą miały wpływ. 

### <a name="starting-an-event"></a>Rozpoczynanie wydarzenia 

Po zapoznaniu się z nadchodzącym wydarzeniem i zakończeniu logiki wdzięku `POST` zamknięcia, można zatwierdzić `EventId`zdarzenie wybitne, nawiązując połączenie z usługą metadanych za pomocą pliku . Oznacza to, że platforma Azure może skrócić minimalny czas powiadamiania (jeśli to możliwe). 

Poniżej przedstawiono json `POST` oczekiwane w treści żądania. Żądanie powinno zawierać listę `StartRequests`. Każdy `StartRequest` zawiera `EventId` dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potwierdzanie zdarzenia umożliwia zdarzenie, aby `Resources` przejść dla wszystkich w przypadku, a nie tylko maszyny wirtualnej, która potwierdza zdarzenie. W związku z tym można wybrać lidera do koordynowania potwierdzenia, które mogą `Resources` być tak proste, jak pierwsza maszyna w terenie.


## <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell 

Poniższy przykład zapytania usługi metadanych dla zaplanowanych zdarzeń i zatwierdza każde zdarzenie zaległe.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Następne kroki 

- Obejrzyj [pokaz zaplanowanych zdarzeń](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) w piątek na platformie Azure. 
- Przejrzyj przykłady kodu zaplanowanych zdarzeń w [repozytorium GitHub zaplanowane zdarzenia wystąpienia platformy Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Dowiedz się więcej o interfejsach API dostępnych w [usłudze metadanych wystąpienia](instance-metadata-service.md).
- Dowiedz się więcej o [planowanej konserwacji maszyn wirtualnych z systemem Windows na platformie Azure](planned-maintenance.md).
