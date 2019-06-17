---
title: Zaplanowane zdarzenia dla maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Planowanie zdarzenia przy użyciu usługi Azure Metadata Service dla maszyn wirtualnych z systemem Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 0831f08eaa3e8e6f6a0d3f68bc50cd927167b7ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507927"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Scheduled Events maszyn wirtualnych systemu Linux

Scheduled Events to usługi Azure Metadata Service zapewniająca czasu aplikacji, aby przygotować się do obsługi maszyny wirtualnej (VM). Zawiera informacje o zbliżającej się konserwacji zdarzeń (na przykład ponownego uruchomienia), aby przygotować się do nich i ograniczyć aplikacji przerw w działaniu. Jest ona dostępna dla wszystkich typów maszyn wirtualnych platformy Azure, łącznie z PaaS i IaaS w systemach Windows i Linux. 

Aby uzyskać informacje na temat usługi Scheduled Events w Windows, zobacz [zaplanowanych zdarzeń dla Windows maszyn wirtualnych](../windows/scheduled-events.md).

> [!Note] 
> Scheduled Events jest ogólnie dostępna we wszystkich regionach platformy Azure. Zobacz [wersji i dostępność regionów dla](#version-and-region-availability) uzyskać najnowsze informacje o wersji.

## <a name="why-use-scheduled-events"></a>Dlaczego warto używać zaplanowanych zdarzeń?

Wiele aplikacji mogą korzystać z czasu, aby przygotować się do obsługi maszyn wirtualnych. Czas może służyć do wykonywania zadań specyficznych dla aplikacji, które poprawić dostępność, niezawodność i użytkowanie, w tym: 

- Punkt kontrolny i przywracania.
- Opróżniania połączenia usługi.
- Tryb failover repliki podstawowej.
- Usuwanie z puli usługi równoważenia obciążenia.
- Rejestrowanie zdarzeń.
- Łagodne zamykanie.

Przy użyciu zaplanowanych zdarzeń aplikacji może odnajdywać podczas konserwacji będą występować i wyzwalanie zadań, aby ograniczyć jej wpływ.  

Scheduled Events dostępne są zdarzenia w następujących przypadkach użycia:

- [Platforma zainicjowane konserwacji](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (na przykład maszyna wirtualna ponowny rozruch, migracji na żywo lub pamięć zachowywanie aktualizacji hosta)
- Obniżonej wydajności sprzętu
- Konserwacji zainicjowanej przez użytkownika (na przykład, ponownego uruchamiania lub ponownie wdraża Maszynę wirtualną)
- [Maszyna wirtualna o niskim priorytecie eksmisji](https://azure.microsoft.com/blog/low-priority-scale-sets) w skali ustawia

## <a name="the-basics"></a>Podstawowe informacje  

  METADATA Service udostępnia informacje o uruchamianiu maszyn wirtualnych przy użyciu punktu końcowego REST, który jest dostępny z poziomu maszyny Wirtualnej. Informacje są dostępne za pośrednictwem nonroutable IP, tak aby nie jest uwidaczniana poza maszyny Wirtualnej.

### <a name="scope"></a>Scope
Zaplanowane zdarzenia są dostarczane do:

- Autonomicznych maszyn wirtualnych.
- Wszystkie maszyny wirtualne w usłudze w chmurze.
- Wszystkie maszyny wirtualne w zestawie dostępności.
- Wszystkie maszyny wirtualne w grupie umieszczania zestawu skalowania. 

Sprawdź, co w efekcie `Resources` pola w zdarzeniu do identyfikowania dotyczy którą maszyny wirtualne.

### <a name="endpoint-discovery"></a>Odnajdywanie punktu końcowego
Dla maszyn wirtualnych z włączoną sieci Wirtualnej, Metadata Service jest dostępna z nonroutable statycznego adresu IP, `169.254.169.254`. Pełny punkt końcowy dla najnowszej wersji Scheduled Events to: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Jeśli maszyna wirtualna nie została utworzona w sieci wirtualnej, a przypadki domyślne usług cloud services i klasycznych maszyn wirtualnych, dodatkowej logiki jest wymagana do odnajdywania adres IP do użycia. Aby dowiedzieć się, jak [odnajdywanie punktu końcowego hosta](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), zobacz temat w tym przykładzie.

### <a name="version-and-region-availability"></a>Wersja i dostępność regionów
Usługa Scheduled Events to numerów wersji. Wersje są obowiązkowe; Bieżąca wersja to `2017-11-01`.

| Version | Typ zlecenia | Regiony | Informacje o wersji | 
| - | - | - | - | 
| 2017-11-01 | Ogólna dostępność | Wszyscy | <li> Dodano obsługę maszyn wirtualnych o niskim priorytecie eksmisji typ zdarzenia "Preempt"<br> | 
| 2017-08-01 | Ogólna dostępność | Wszyscy | <li> Usunięte poprzedzona znakiem podkreślenia z nazwy zasobów dla maszyn wirtualnych IaaS<br><li>Wymaganie nagłówka metadanych wymuszone dla wszystkich żądań | 
| 2017-03-01 | Wersja zapoznawcza | Wszyscy | <li>Wersja początkowa


> [!NOTE] 
> Poprzednich wersjach zapoznawczych zaplanowanych zdarzeń, które są obsługiwane {najnowsza wersja} jako parametru api-version. Ten format nie jest już obsługiwane i zostaną wycofane w przyszłości.

### <a name="enabling-and-disabling-scheduled-events"></a>Włączanie i wyłączanie zaplanowane zdarzenia
Scheduled Events jest włączona dla czasu usługi pierwszy wprowadzone dla zdarzeń żądania. Należy oczekiwać odpowiedzi opóźnione w swoje pierwsze wywołanie do dwóch minut.

Scheduled Events jest wyłączona dla Twojej usługi, jeśli nie sprawia, że żądanie przez 24 godziny.

### <a name="user-initiated-maintenance"></a>Konserwacji zainicjowanej przez użytkownika
Użytkownik zainicjował konserwację maszyny Wirtualnej za pośrednictwem witryny Azure portal, interfejsu API, interfejs wiersza polecenia lub programu PowerShell powoduje zaplanowane zdarzenie. Następnie można przetestować logiki przygotowanie konserwacji w aplikacji, a następnie aplikację można przygotować konserwacji zainicjowanej przez użytkownika.

Jeśli ponowne uruchomienie maszyny Wirtualnej, zdarzenia z typem `Reboot` zostało zaplanowane. Jeśli ponowne wdrożenie maszyny Wirtualnej, zdarzenia z typem `Redeploy` zostało zaplanowane.

## <a name="use-the-api"></a>Używanie interfejsu API

### <a name="headers"></a>Nagłówki
Kiedy wykonujesz zapytanie o Metadata Service, musisz podać nagłówek `Metadata:true` zapewnienie żądania przypadkowo nie został przekierowany. `Metadata:true` Nagłówka jest wymagana dla wszystkich żądań zaplanowanych zdarzeń. Niepodanie nagłówka w żądaniu powoduje "Złe żądanie" odpowiedź z usługi metadanych.

### <a name="query-for-events"></a>Zapytanie dla zdarzeń
Zaplanowane zdarzenia można wyszukać, wprowadzając następujące wywołanie:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że obecnie żadne zdarzenia nie są zaplanowane.
W przypadku których zaplanowanych zdarzeń, odpowiedź zawiera szereg zdarzeń. 
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

### <a name="event-properties"></a>Właściwości zdarzenia
|Właściwość  |  Opis |
| - | - |
| Identyfikator zdarzenia | Globalnie unikatowy identyfikator dla tego zdarzenia. <br><br> Przykład: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ zdarzenia | Wpływ, który powoduje, że to zdarzenie. <br><br> Wartości: <br><ul><li> `Freeze`: Maszyna wirtualna jest zaplanowana do wstrzymania przez kilka sekund. Może zostać zawieszone procesora CPU oraz łączności sieciowej, ale nie ma to wpływu na pamięć lub otwarte pliki.<li>`Reboot`: Maszyna wirtualna jest zaplanowana do ponownego uruchomienia (— trwałej pamięci jest utracona). <li>`Redeploy`: Maszyna wirtualna jest zaplanowane na przeniesienie do innego węzła (efemeryczne dyski zostaną utracone). <li>`Preempt`: Trwa usuwanie maszyny wirtualnej o niskim priorytecie (efemeryczne dyski zostaną utracone).|
| ResourceType | Typ zasobu, który ma wpływ na to zdarzenie. <br><br> Wartości: <ul><li>`VirtualMachine`|
| Zasoby| Lista zasobów, który wpływa na to zdarzenie. Listy może zawierać maszyny z co najwyżej jeden [domena aktualizacji](manage-availability.md), ale nie może nie zawierać wszystkich maszyn w UD. <br><br> Przykład: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Obiektu EventStatus | Stan tego zdarzenia. <br><br> Wartości: <ul><li>`Scheduled`: To zdarzenie jest zaplanowane do uruchomienia po upływie czasu określonego w `NotBefore` właściwości.<li>`Started`: To zdarzenie zostało rozpoczęte.</ul> Nie `Completed` lub podobne stan nigdy nie są dostarczane. Zdarzenie nie jest zwracana, po zakończeniu zdarzenia.
| nie wcześniej niż| Czas, po którym można uruchomić tego zdarzenia. <br><br> Przykład: <br><ul><li> MON-19 września 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Planowanie zdarzenia
Każde zdarzenie jest zaplanowane minimalną ilość czasu w przyszłości na podstawie zdarzeń typu. Tym razem znajduje odzwierciedlenie w zdarzeniu `NotBefore` właściwości. 

|Typ zdarzenia  | Minimalna powiadomienia |
| - | - |
| Freeze| 15 minut |
| Ponowne uruchamianie | 15 minut |
| Ponowne wdrożenie | 10 minut |
| Wywłaszczenia | 30 sekund |

### <a name="start-an-event"></a>Zdarzenie początkowe 

Po dowiedzieć się więcej o nadchodzących zdarzeń i Zakończ logikę do bezpiecznego zamknięcia, należy zatwierdzić oczekujące zdarzenia, wprowadzając `POST` wywołań do usługi metadanych z `EventId`. To wywołanie wskazuje na platformie Azure, skrócić minimalne powiadomień czasu (jeśli jest to możliwe). 

Poniższy przykładowy kod JSON jest oczekiwany w `POST` treść żądania. Żądanie może zawierać listę `StartRequests`. Każdy `StartRequest` zawiera `EventId` dla zdarzenia, aby przyspieszyć:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Potwierdzenie zdarzenia umożliwia zdarzeń kontynuować, wszystkie `Resources` w przypadku, nie tylko maszyn wirtualnych, który potwierdza zdarzenia. W związku z tym, użytkownik może wybrać lidera do koordynowania potwierdzenia, które mogą być proste i polega na pierwszej maszynie w `Resources` pola.

## <a name="python-sample"></a>Przykład środowiska Python 

Poniższy przykład zapytania usługi metadanych dla zaplanowanych zdarzeń i zatwierdza każdego zaległe zdarzenia:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
headers = "{Metadata:true}"
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
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Kolejne kroki 
- Obejrzyj [Scheduled Events serii Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) Aby wyświetlić pokaz. 
- Przejrzyj przykłady kodu Scheduled Events w [Azure wystąpienie metadanych zaplanowane zdarzenia repozytorium usługi GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Przeczytaj więcej na temat interfejsów API, które są dostępne w [Instance Metadata Service](instance-metadata-service.md).
- Dowiedz się więcej o [planowana Konserwacja maszyn wirtualnych systemu Linux na platformie Azure](planned-maintenance.md).
