---
title: Scheduled Events dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Zaplanuj zdarzenia za pomocą usługi Azure Metadata Service dla maszyn wirtualnych z systemem Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 37932a3669dc1ed7f8f3f103db93ee6757a06aad
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390432"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Scheduled Events dla maszyn wirtualnych z systemem Linux

Scheduled Events to Metadata Service platformy Azure, który umożliwia aplikacji przygotowanie czasu do konserwacji maszyn wirtualnych. Zawiera informacje dotyczące nadchodzących zdarzeń konserwacyjnych (na przykład ponownego uruchomienia), aby aplikacja mogła je przygotować i ograniczyć zakłócenia. Jest ona dostępna dla wszystkich typów Virtual Machines platformy Azure, w tym PaaS i IaaS w systemach Windows i Linux. 

Aby uzyskać informacje na temat Scheduled Events w systemie Windows, zobacz [Scheduled Events dla maszyn wirtualnych z systemem Windows](../windows/scheduled-events.md).

> [!Note] 
> Scheduled Events jest ogólnie dostępna we wszystkich regionach świadczenia usługi Azure. Informacje o [dostępności wersji i regionu](#version-and-region-availability) znajdują się w temacie dotyczącym najnowszych informacji o wersji.

## <a name="why-use-scheduled-events"></a>Dlaczego warto używać Scheduled Events?

Wiele aplikacji może korzystać z czasu, aby przygotować się do konserwacji maszyn wirtualnych. Czas może służyć do wykonywania zadań specyficznych dla aplikacji, które zwiększają dostępność, niezawodność i łatwość obsługi, w tym: 

- Punkt kontrolny i przywracanie.
- Opróżnianie połączenia.
- Tryb failover repliki podstawowej.
- Usuwanie z puli modułu równoważenia obciążenia.
- Rejestrowanie zdarzeń.
- Bezpieczne zamykanie.

Dzięki Scheduled Events aplikacja może wykryć, kiedy nastąpi konserwacja, i wyzwala zadania, aby ograniczyć jego wpływ.  

Scheduled Events udostępnia zdarzenia w następujących przypadkach użycia:

- [Konserwacja inicjowana przez platformę](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (na przykład ponowne uruchomienie maszyny wirtualnej, migracja na żywo lub zachowywanie pamięci na potrzeby hosta)
- Sprzęt o obniżonej wydajności
- Konserwacja inicjowana przez użytkownika (na przykład użytkownik uruchamia ponownie lub wdraża maszynę wirtualną)
- Wykluczanie wystąpień [maszyn wirtualnych](spot-vms.md) i [zestawów skalowania](../../virtual-machine-scale-sets/use-spot.md) .

## <a name="the-basics"></a>Podstawowe informacje  

  Metadata Service udostępnia informacje o uruchomionych maszynach wirtualnych za pomocą punktu końcowego REST, który jest dostępny z poziomu maszyny wirtualnej. Informacje są dostępne za pośrednictwem adresu IP nonroutable, dzięki czemu nie jest on ujawniany poza maszyną wirtualną.

### <a name="scope"></a>Zakres
Zaplanowane zdarzenia są dostarczane do:

- Autonomiczna Virtual Machines.
- Wszystkie maszyny wirtualne w usłudze w chmurze.
- Wszystkie maszyny wirtualne w zestawie dostępności.
- Wszystkie maszyny wirtualne w grupie umieszczania zestawu skalowania. 

W związku z tym sprawdź pole `Resources` w zdarzeniu, aby ustalić, które maszyny wirtualne mają na to oddziaływać.

### <a name="endpoint-discovery"></a>Odnajdywanie punktów końcowych
W przypadku maszyn wirtualnych z obsługą sieci wirtualnej Metadata Service jest dostępny z statycznego adresu IP nonroutable, `169.254.169.254`. Pełny punkt końcowy dla najnowszej wersji Scheduled Events to: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Jeśli maszyna wirtualna nie zostanie utworzona w ramach Virtual Network, domyślne przypadki usług Cloud Services i klasycznych maszyn wirtualnych są wymagane do odnajdywania adresu IP do użycia. Aby dowiedzieć się, jak [odnaleźć punkt końcowy hosta](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), zobacz ten przykład.

### <a name="version-and-region-availability"></a>Dostępność wersji i regionu
Usługa Scheduled Events jest w wersji. Wersje są obowiązkowe; Bieżąca wersja jest `2019-01-01`.

| Wersja | Typ zlecenia | Regiony | Informacje o wersji | 
| - | - | - | - | 
| 2019-01-01 | Ogólna dostępność | Wszyscy | <li> Dodano obsługę funkcji EventType dla zestawów skalowania maszyn wirtualnych |
| 2017-11-01 | Ogólna dostępność | Wszyscy | <li> Dodano obsługę elementu EventType punktu wykluczania maszyny wirtualnej<br> | 
| 2017-08-01 | Ogólna dostępność | Wszyscy | <li> Usunięto poprzedzony znak podkreślenia z nazw zasobów dla maszyn wirtualnych IaaS<br><li>Wymagania nagłówka metadanych wymuszone dla wszystkich żądań | 
| 2017-03-01 | Wersja zapoznawcza | Wszyscy | <li>Wersja początkowa |


> [!NOTE] 
> W poprzedniej wersji zapoznawczej Scheduled Events jest obsługiwane {Najnowsza} jako wersja interfejsu API. Ten format nie jest już obsługiwany i będzie przestarzały w przyszłości.

### <a name="enabling-and-disabling-scheduled-events"></a>Włączanie i wyłączanie Scheduled Events
Scheduled Events jest włączona dla Twojej usługi podczas pierwszego żądania zdarzeń. Oczekiwana jest opóźniona odpowiedź w pierwszym wywołaniu przez maksymalnie dwie minuty.

Scheduled Events jest wyłączone dla usługi, jeśli nie zostanie wysłane żądanie przez 24 godziny.

### <a name="user-initiated-maintenance"></a>Konserwacja inicjowana przez użytkownika
Konserwacja maszyn wirtualnych zainicjowana przez użytkownika za pośrednictwem Azure Portal, interfejsu API, wiersza polecenia lub programu PowerShell skutkuje zaplanowanym zdarzeniem. Następnie można testować logikę przygotowania konserwacji w aplikacji, a aplikacja może przygotować się do konserwacji zainicjowanej przez użytkownika.

Po ponownym uruchomieniu maszyny wirtualnej zostanie zaplanowana zdarzenie z typem `Reboot`. Po ponownym wdrożeniu maszyny wirtualnej zostanie zaplanowana zdarzenie z typem `Redeploy`.

## <a name="use-the-api"></a>Używanie interfejsu API

### <a name="headers"></a>Nagłówki
Podczas wykonywania zapytania Metadata Service należy podać nagłówek `Metadata:true`, aby upewnić się, że żądanie nie zostało przypadkowo przekierowane. Nagłówek `Metadata:true` jest wymagany dla wszystkich żądań zaplanowanych zdarzeń. Niepowodzenie dołączenia nagłówka do żądania skutkuje odpowiedzią "złe żądanie" z Metadata Service.

### <a name="query-for-events"></a>Zapytanie o zdarzenia
Możesz wykonywać zapytania o zaplanowane zdarzenia, wykonując następujące wywołanie:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że obecnie nie są zaplanowane żadne zdarzenia.
W przypadku zaplanowanych zdarzeń odpowiedź zawiera tablicę zdarzeń. 
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

### <a name="event-properties"></a>Właściwości zdarzenia
|Właściwość  |  Opis |
| - | - |
| Identyfikator zdarzenia | Unikatowy identyfikator globalny dla tego zdarzenia. <br><br> Przykład: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Klasę | Ma to wpływ na przyczyny tego zdarzenia. <br><br> Wartości: <br><ul><li> `Freeze`: zaplanowano wstrzymanie maszyny wirtualnej przez kilka sekund. Połączenie procesora i sieci może być zawieszone, ale nie ma wpływu na pamięć lub otwieranie plików.<li>`Reboot`: zaplanowano ponowne uruchomienie maszyny wirtualnej (pamięć nietrwała zostanie utracona). <li>`Redeploy`: zaplanowano przeniesienie maszyny wirtualnej do innego węzła (dyski tymczasowe są tracone). <li>`Preempt`: trwa usuwanie miejsca na maszynie wirtualnej (dyski tymczasowe są tracone). <li> `Terminate`: zaplanowano usunięcie maszyny wirtualnej. |
| ResourceType | Typ zasobu, którego dotyczy to zdarzenie. <br><br> Wartości: <ul><li>`VirtualMachine`|
| Zasoby| Lista zasobów, których dotyczy to zdarzenie. Lista powinna zawierać maszyny z co najwyżej jednej [domeny aktualizacji](manage-availability.md), ale może nie zawierać wszystkich maszyn w ud. <br><br> Przykład: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stan tego zdarzenia. <br><br> Wartości: <ul><li>`Scheduled`: to zdarzenie jest zaplanowane do uruchomienia po upływie czasu określonego we właściwości `NotBefore`.<li>`Started`: to zdarzenie zostało uruchomione.</ul> Nie dostarczono żadnego `Completed` ani podobnego stanu. Zdarzenie nie jest już zwracane po zakończeniu zdarzenia.
| NotBefore| Czas, po którym to zdarzenie może zostać uruchomione. <br><br> Przykład: <br><ul><li> PN, 19 wrz 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Planowanie zdarzeń
Każde zdarzenie ma zaplanowaną minimalną ilość czasu w przyszłości w oparciu o typ zdarzenia. Ten czas jest uwzględniany we właściwości `NotBefore` zdarzenia. 

|Klasę  | Minimalny komunikat |
| - | - |
| Funkcja| 15 minut |
| Ponowne uruchamianie | 15 minut |
| Ponowne wdrożenie | 10 minut |
| Stępują | 30 sekund |
| Kończyć | [Użytkownik konfigurowalny](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): od 5 do 15 minut |

### <a name="start-an-event"></a>Rozpocznij zdarzenie 

Po poznaniu nadchodzącego zdarzenia i zakończeniu logiki w celu bezpiecznego zamknięcia możesz zatwierdzić wydarzenie zaległe, wykonując `POST` wywołanie Metadata Service z `EventId`. To wywołanie wskazuje na platformę Azure, że może skrócić minimalny czas powiadomienia (jeśli to możliwe). 

W treści żądania `POST` oczekiwano następującego przykładu JSON. Żądanie powinno zawierać listę `StartRequests`. Każdy `StartRequest` zawiera `EventId` dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Przykład bash
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potwierdzenie zdarzenia pozwala na wykonanie zdarzenia dla wszystkich `Resources` w zdarzeniu, a nie tylko do maszyny wirtualnej, która potwierdzi zdarzenie. W związku z tym można wybrać opcję wyboru lidera, aby koordynować potwierdzenie, które może być proste jako pierwszy komputer w polu `Resources`.

## <a name="python-sample"></a>Przykład języka Python 

Następujące przykładowe zapytania Metadata Service dla zaplanowanych zdarzeń i zatwierdzają każde zaległe zdarzenie:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Następne kroki 
- Obejrzyj [Scheduled Events w piątek](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) z systemem Azure, aby wyświetlić pokaz. 
- Zapoznaj się z przykładami kodu Scheduled Events w [metadanych wystąpienia platformy Azure Scheduled Events repozytorium GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Przeczytaj więcej na temat interfejsów API, które są dostępne w [instance Metadata Service](instance-metadata-service.md).
- Informacje o [planowanej konserwacji maszyn wirtualnych z systemem Linux na platformie Azure](planned-maintenance.md).
