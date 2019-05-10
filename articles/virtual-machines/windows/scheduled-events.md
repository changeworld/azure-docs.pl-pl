---
title: Zaplanowane zdarzenia dla maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Zaplanowane zdarzenia przy użyciu usługi Azure Metadata dla na maszynach wirtualnych Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: e6a376803d8617e01ee279e40a33f6c1c3b748fd
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508196"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Scheduled Events dla maszyn wirtualnych Windows

Scheduled Events to usługi Azure Metadata Service zapewniającej Twojej czas aplikacji, aby przygotować się do obsługi maszyn wirtualnych. Zawiera informacje o zbliżającej się konserwacji zdarzeń (np. ponownego uruchomienia), aplikacja może przygotować się do nich i ograniczyć przerw w działaniu. Jest ona dostępna dla wszystkich typów maszyn wirtualnych platformy Azure, łącznie z PaaS i IaaS w systemach Windows i Linux. 

Aby uzyskać informacje na temat usługi Scheduled Events w systemie Linux, zobacz [Scheduled Events maszyn wirtualnych systemu Linux](../linux/scheduled-events.md).

> [!Note] 
> Scheduled Events jest ogólnie dostępna we wszystkich regionach platformy Azure. Zobacz [wersji i dostępność regionów dla](#version-and-region-availability) uzyskać najnowsze informacje o wersji.

## <a name="why-scheduled-events"></a>Dlaczego zaplanowane zdarzenia?

Wiele aplikacji mogą korzystać z czasu, aby przygotować się do obsługi maszyn wirtualnych. Czas może służyć do wykonywania określonych zadań aplikacji, które zwiększenia dostępności, niezawodności i użytkowanie w tym: 

- Punkt kontrolny i przywracania
- Opróżnianie połączenia
- Tryb failover repliki podstawowej 
- Usuwanie z puli modułu równoważenia obciążenia
- Rejestrowanie zdarzeń
- Łagodne zamykanie 

Przy użyciu zaplanowanych zdarzeń aplikacji może odnajdywać podczas konserwacji będą występować i wyzwalanie zadań, aby ograniczyć jej wpływ. Włączanie zaplanowanych zdarzeń zapewnia maszynie wirtualnej minimalną ilość czasu przed wykonaniem związanych z konserwacją. Zobacz sekcję planowania zdarzeń poniżej szczegółowe informacje.

Scheduled Events dostępne są zdarzenia w następujących przypadkach użycia:
- [Platforma zainicjowane konserwacji](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (na przykład maszyna wirtualna ponowny rozruch, migracji na żywo lub pamięć zachowywanie aktualizacji hosta)
- Obniżonej wydajności sprzętu
- Użytkownik zainicjował konserwacji (np. ponownego uruchamiania lub ponownie wdraża Maszynę wirtualną)
- [Maszyna wirtualna o niskim priorytecie eksmisji](https://azure.microsoft.com/blog/low-priority-scale-sets) w skali ustawia

## <a name="the-basics"></a>Podstawowe informacje  

Usługi Azure Metadata service udostępnia informacje o uruchamianiu maszyn wirtualnych przy użyciu punktu końcowego REST, dostępna z poziomu maszyny Wirtualnej. Informacje są dostępne za pośrednictwem bez obsługi routingu IP, co nie jest uwidaczniana poza maszyny Wirtualnej.

### <a name="endpoint-discovery"></a>Odnajdywanie punktu końcowego
Dla maszyn wirtualnych z włączoną sieci Wirtualnej, usługa metadanych jest dostępne ze statycznego adresu IP bez obsługi routingu, `169.254.169.254`. Pełny punkt końcowy dla najnowszej wersji Scheduled Events to: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Jeśli maszyna wirtualna nie została utworzona w sieci wirtualnej, a przypadki domyślne usług cloud services i klasycznych maszyn wirtualnych, dodatkowej logiki jest wymagana do odnajdywania adres IP do użycia. Odnoszą się do tego przykładu, aby dowiedzieć się, jak [odnajdywanie punktu końcowego hosta](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Wersja i dostępność regionów
Usługa zdarzeń według harmonogramu jest wersjonowany. Wersje są obowiązkowe, a bieżąca wersja to `2017-11-01`.

| Wersja | Typ zlecenia | Regiony | Informacje o wersji | 
| - | - | - | - |
| 2017-11-01 | Ogólna dostępność | Wszyscy | <li> Dodano obsługę maszyn wirtualnych o niskim priorytecie eksmisji typ zdarzenia "Preempt"<br> | 
| 2017-08-01 | Ogólna dostępność | Wszyscy | <li> Usunięte poprzedzona znakiem podkreślenia z nazwy zasobów dla maszyn wirtualnych IaaS<br><li>Nagłówek metadanych wymaganie wymuszone dla wszystkich żądań | 
| 2017-03-01 | Preview | Wszyscy |<li>Wersja początkowa

> [!NOTE] 
> Poprzednich wersjach zapoznawczych zaplanowanych zdarzeń {najnowsza wersja} są obsługiwane jako parametru api-version. Ten format nie jest już obsługiwane i zostaną wycofane w przyszłości.

### <a name="enabling-and-disabling-scheduled-events"></a>Włączanie i wyłączanie zaplanowane zdarzenia
Scheduled Events jest włączona dla czasu usługi pierwszy wprowadzone dla zdarzeń żądania. Należy oczekiwać odpowiedzi opóźnione w swoje pierwsze wywołanie do dwóch minut. Należy zbadać punktu końcowego okresowo, aby wykrywanie zdarzeń o zbliżającej się konserwacji, a także stan czynności konserwacyjnych, które są wykonywane.

Scheduled Events jest wyłączona dla Twojej usługi, jeśli nie sprawia, że żądanie przez 24 godziny.

### <a name="user-initiated-maintenance"></a>Użytkownik zainicjował konserwacji
Użytkownik zainicjował konserwacji maszyny wirtualnej w witrynie Azure portal, interfejsu API, interfejsu wiersza polecenia lub programu PowerShell powoduje zaplanowane zdarzenie. Można testować logiki przygotowanie konserwacji w aplikacji i umożliwia aplikacji w taki sposób przygotować się do konserwacja inicjowana przez użytkownika.

Ponowne uruchamianie maszyny wirtualnej planuje zdarzenia z typem `Reboot`. Ponowne wdrażanie maszyny wirtualnej planuje zdarzenia z typem `Redeploy`.

## <a name="using-the-api"></a>Korzystanie z interfejsu API

### <a name="headers"></a>Nagłówki
Kiedy wykonujesz zapytanie o Metadata Service, musisz podać nagłówek `Metadata:true` aby upewnić się, żądanie nie zostało przekierowane przypadkowo. `Metadata:true` Nagłówka jest wymagana dla wszystkich żądań zaplanowanych zdarzeń. Niepodanie nagłówka w żądaniu spowoduje nieprawidłowe żądanie odpowiedzi z metadanych usługi.

### <a name="query-for-events"></a>Zapytanie dla zdarzeń
Można wyszukiwać Scheduled Events, po prostu, tworząc następujące wywołanie:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że są obecnie żadne zdarzenia według harmonogramu.
W przypadku których zaplanowanych zdarzeń, odpowiedź zawiera szereg zdarzeń: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation jest element ETag i zapewnia prosty sposób sprawdzić jeśli ładunek zdarzenia uległ zmianie od czasu ostatniego zapytania.

### <a name="event-properties"></a>Właściwości zdarzenia
|Właściwość  |  Opis |
| - | - |
| Identyfikator zdarzenia | Globalnie unikatowy identyfikator dla tego zdarzenia. <br><br> Przykład: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Wpływ, który powoduje, że to zdarzenie. <br><br> Wartości: <br><ul><li> `Freeze`: Maszyna wirtualna jest zaplanowana do wstrzymania przez kilka sekund. Może zostać zawieszone procesora CPU oraz łączności sieciowej, ale nie ma to wpływu na pamięć lub otwarte pliki. <li>`Reboot`: Maszyna wirtualna jest zaplanowana do ponownego uruchomienia (— trwałej pamięci jest utracona). <li>`Redeploy`: Maszyna wirtualna jest zaplanowane na przeniesienie do innego węzła (efemeryczne dyski zostaną utracone). <li>`Preempt`: Trwa usuwanie maszyny wirtualnej o niskim priorytecie (efemeryczne dyski zostaną utracone).|
| ResourceType | Typ zasobu, który ma wpływ na to zdarzenie. <br><br> Wartości: <ul><li>`VirtualMachine`|
| Zasoby| Lista zasobów, które ma wpływ na to zdarzenie. Gwarantuje zawierają maszyn z co najwyżej jeden [domena aktualizacji](manage-availability.md), ale może nie zawierać wszystkich maszyn w UD. <br><br> Przykład: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stan zdarzenia | Stan tego zdarzenia. <br><br> Wartości: <ul><li>`Scheduled`: To zdarzenie jest zaplanowane do uruchomienia po upływie czasu określonego w `NotBefore` właściwości.<li>`Started`: To zdarzenie zostało rozpoczęte.</ul> Nie `Completed` lub podobne stan nigdy nie są dostarczane; zdarzenia nie zostaną zwrócone, po zakończeniu zdarzenia.
| nie wcześniej niż| Czas, po którym to zdarzenie może zostać uruchomiony. <br><br> Przykład: <br><ul><li> MON-19 września 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Planowanie zdarzenia
Każde zdarzenie jest zaplanowane minimalną ilość czasu w przyszłości na podstawie zdarzeń typu. Tym razem znajduje odzwierciedlenie w zdarzeniu `NotBefore` właściwości. 

|EventType  | Minimalna powiadomienia |
| - | - |
| Freeze| 15 minut |
| Uruchom ponownie | 15 minut |
| Ponownie wdróż | 10 minut |
| Wywłaszczenia | 30 sekund |

### <a name="event-scope"></a>Zakres zdarzeń     
Zaplanowane zdarzenia są dostarczane do:
 - Autonomicznych maszyn wirtualnych
 - Wszystkie maszyny wirtualne w usłudze w chmurze      
 - Wszystkie maszyny wirtualne w zestawie dostępności      
 - Wszystkie maszyny wirtualne w grupie umieszczania zestawu skalowania.         

W rezultacie, należy sprawdzić `Resources` pola w zdarzeniu, aby identyfikować, które będzie mieć wpływ na maszyny wirtualne. 

### <a name="starting-an-event"></a>Uruchamianie zdarzenia 

Po przedstawiono nadchodzące zdarzenia i ukończyć logikę dla łagodne zamykanie należy zatwierdzić oczekujące zdarzenia, wprowadzając `POST` wywołań do usługi metadanych z `EventId`. Oznacza to, na platformie Azure skrócić minimalne powiadomień czasu (jeśli jest to możliwe). 

Poniżej znajduje się za pomocą pliku json w oczekiwany `POST` treść żądania. Żądanie może zawierać listę `StartRequests`. Każdy `StartRequest` zawiera `EventId` dla zdarzenia, aby przyspieszyć:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Potwierdzenie zdarzenia umożliwia zdarzeń kontynuować, wszystkie `Resources` w przypadku, nie tylko maszynę wirtualną, która potwierdza zdarzenia. W związku z tym można może wybrać lidera do koordynowania potwierdzenia, które mogą być proste i polega na pierwszej maszynie w `Resources` pola.


## <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell 

Poniższy przykład wykonuje kwerendę usługi metadanych dla zaplanowanych zdarzeń i zatwierdza każdego zdarzenia zaległe.

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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

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

## <a name="next-steps"></a>Kolejne kroki 

- Obejrzyj [zaplanowane zdarzenia pokaz](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) serii Azure Friday. 
- Przejrzyj przykłady kodu Scheduled Events w [repozytorium GitHub Azure wystąpienie metadanych zaplanowane zdarzenia](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Dowiedz się więcej o interfejsami API dostępnymi w [Instance Metadata service](instance-metadata-service.md).
- Dowiedz się więcej o [planowana Konserwacja maszyn wirtualnych Windows na platformie Azure](planned-maintenance.md).
