---
title: Pojęcia, terminologia oraz jednostki związane z usługą Scheduler | Microsoft Docs
description: Pojęcia, terminologia oraz hierarchia jednostek związane z usługą Azure Scheduler, w tym zadania i kolekcje zadań.  Kompleksowy przykład zaplanowanego zadania.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 91302d57c43a6c9d14aeeee95df3d61fa6f73172
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418846"
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Pojęcia i terminologia dotyczące usługi Scheduler oraz hierarchia jednostek
## <a name="scheduler-entity-hierarchy"></a>Hierarchia jednostek w ramach usługi Scheduler
W poniższej tabeli opisano główne zasoby udostępniane lub wykorzystywane przez interfejs API usługi Scheduler:

| Zasób | Opis |
| --- | --- |
| **Kolekcja zadań** |Kolekcja zadań zawiera grupę zadań wraz z ustawieniami, przydziałami i limitami wspólnymi dla zadań należących do kolekcji. Kolekcja zadań jest tworzona przez właściciela subskrypcji i stanowi grupę zadań utworzoną na podstawie granic użycia lub zastosowania. Jest ograniczona do jednego regionu. Umożliwia także wymuszanie przydziałów w celu ograniczenia użycia wszystkich zadań z danej kolekcji. Przykłady przydziałów to MaxJobs (Maksymalna liczba zadań) i MaxRecurrence (Maksymalna liczba cykli). |
| **Zadanie** |Zadanie definiuje jedną powtarzającą się akcję powiązaną z prostymi lub złożonymi strategiami wykonania. Akcje mogą obejmować żądania HTTP, żądania kolejki magazynu i żądania kolejki magistrali usług lub tematu magistrali usług. |
| **Historia zadania** |Historia zadania zawiera szczegółowe informacje dotyczące wykonywania zadania. Zawiera ona informacje na temat pomyślnych wykonań i niepowodzeń, a także wszelkie szczegóły odpowiedzi. niepowodzeń, a także wszelkie szczegóły odpowiedzi. |

## <a name="scheduler-entity-management"></a>Zarządzanie jednostkami usługi Scheduler
Ogólne możliwości interfejsu API usługi Scheduler i zarządzania usługami pozwalają wykonywać następujące operacje na zasobach:

| Możliwości | Opis i adres URI |
| --- | --- |
| **Zarządzanie kolekcją zadań** |Obsługa żądań GET, PUT i DELETE wykorzystywanych do tworzenia i modyfikowania kolekcji zadań oraz składających się na nie zadań. Kolekcja zadań to kontener zadań z mapowaniem przydziałów i wspólnych ustawień. Przykłady przydziałów, które zostały opisane w dalszej części artykułu, dotyczą maksymalnej liczby zadań oraz najmniejszego interwału cyklu. <p>PUT i DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **Zarządzanie zadaniami** |Obsługa żądań GET, PUT, POST, PATCH i DELETE służących do tworzenia i modyfikowania zadań. Wszystkie zadania muszą należeć do kolekcji zadań, która już istnieje, w związku z czym nie mają miejsca żadne operacje niejawnego tworzenia. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **Zarządzanie historią zadania** |Obsługa żądania GET umożliwiającego pobranie historii wykonywania zadania z 60 dni obejmującej m.in. informacje o czasie, który upłynął podczas zadania, oraz o wynikach wykonania zadania. Dodaje obsługę parametru ciągu zapytania służącego do filtrowania na podstawie stanu i statusu. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>Typy zadań
Istnieje wiele typów zadań: zadania HTTP (w tym zadania protokołu HTTPS, które obsługują protokół SSL), zadania kolejki magazynu, zadania kolejki magistrali usług i zadania tematu magistrali usług. Zadania HTTP są odpowiednie, jeśli istnieje punkt końcowy danego obciążenia lub usługi. Zadania kolejki magazynu umożliwiają przesyłanie komunikatów do kolejek magazynu, są więc odpowiednie w przypadku obciążeń, które korzystają z kolejek magazynu. Podobnie zadania magistrali usług są odpowiednie dla obciążeń wykorzystujących kolejki i tematy magistrali usług.

## <a name="the-job-entity-in-detail"></a>Szczegółowy opis jednostki „zadanie”
Ogólna struktura zaplanowanego zadania obejmuje kilka elementów:

* Akcja do wykonania po aktywacji czasomierza zadania  
* (Opcjonalnie) Czas uruchomienia zadania  
* (Opcjonalnie) Termin i częstotliwość powtarzania zadania  
* (Opcjonalnie) Akcja wyzwalana, jeśli akcja podstawowa zakończy się niepowodzeniem  

Wewnętrzna struktura zaplanowanego zadania obejmuje również dane dostarczane przez system, takie jak czas następnego zaplanowanego wykonania.

Poniższy kod stanowi kompleksowy przykład zaplanowanego zadania. Szczegóły można znaleźć w kolejnych sekcjach.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Jak widać na przykładzie powyższego zaplanowanego zadania, definicja zadania obejmuje kilka elementów:

* Czas rozpoczęcia (“startTime”)  
* Akcja („action”), w tym akcja w przypadku błędu („errorAction”)
* Cykl („recurrence”)  
* Stan („state”)  
* Status („status”)  
* Zasady ponawiania („retryPolicy”)  

Przeanalizujmy szczegółowo każdy z nich:

## <a name="starttime"></a>startTime
Parametr „startTime” oznacza czas rozpoczęcia i umożliwia obiektowi wywołującemu określenie przesunięcia strefy czasowej w sieci w [formacie ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action i errorAction
Wartość parametru „action” odnosi się do akcji wywoływanej przy każdym wystąpieniu i opisuje typ wywołania usługi. Wskazana akcja będzie wykonywana zgodnie z harmonogramem. Usługa Scheduler obsługuje akcje HTTP, akcje kolejki magazynu oraz akcje tematu i kolejki magistrali usług.

Akcja w powyższym przykładzie to akcja HTTP. Poniżej znajduje się przykład akcji kolejki magazynu:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Poniżej znajduje się przykład akcji tematu magistrali usług:

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Możliwe opcje to netMessaging i AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Wybrany komunikat", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Poniżej znajduje się przykład akcji kolejki magistrali usług:

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Możliwe opcje to netMessaging i AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Wybrany komunikat",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

Obiekt „errorAction” oznacza procedurę obsługi błędów, czyli akcję wywoływaną, gdy akcja podstawowa zakończy się niepowodzeniem. Ta zmienna umożliwia wywołanie punktu końcowego obsługi błędów lub wysłanie powiadomienia użytkownikowi. Można jej użyć w celu połączenia się z dodatkowym punktem końcowym, jeśli podstawowy punkt końcowy nie jest dostępny (np. w przypadku awarii w lokalizacji punktu końcowego) lub w celu powiadamiania punktu końcowego obsługi błędu. Podobnie jak akcja podstawowa, akcja w przypadku błędu także może charakteryzować się logiką prostą lub złożoną w oparciu o inne akcje. Aby dowiedzieć się, jak utworzyć token SAS, zapoznaj się z artykułem [Create and Use a Shared Access Signature](https://msdn.microsoft.com/library/azure/jj721951.aspx) (Tworzenie i używanie sygnatury dostępu współdzielonego).

## <a name="recurrence"></a>recurrence
Na cykl składa się kilka elementów:

* Częstotliwość (frequency): minuta, godzina, dzień, tydzień, miesiąc lub rok  
* Interwał (interval): odstęp czasu przy podanej częstotliwości dla cyklu  
* Określony harmonogram (schedule): określ minuty, godziny, dni tygodnia, miesiące oraz dni miesiąca cyklu  
* Liczba (count): liczba wystąpień  
* Czas zakończenia (endTime): po jego upływie zadanie nie będzie już wykonywane  

Zadanie określa się jako cykliczne, jeśli jego definicja JSON zawiera obiekt cykliczny. Jeśli określono zarówno zmienną count, jak i endTime, uznawana jest ta reguła ukończenia, która zostanie zrealizowana jako pierwsza.

## <a name="state"></a>state
Stan zadania stanowi jedną z czterech wartości: „enabled” (włączone), „disabled” (wyłączone), „completed” (ukończone) lub „faulted” (wystąpił błąd). Zadania z żądaniami PUT i PATCH można ustawić tak, aby były one aktualizowane do stanu włączonego lub wyłączonego. Jeśli zadanie zostało wykonane (stan „completed”) lub jeśli wystąpił błąd (stan „faulted”), to stan zadania ma charakter ostateczny i nie można go zaktualizować (choć nadal można użyć żądania DELETE w celu usunięcia). Przykład właściwości stanu:

        "state": "disabled", // enabled, disabled, completed, or faulted
Zadania ukończone oraz zadania z błędami są usuwane po upływie 60 dni.

## <a name="status"></a>status
Po rozpoczęciu zadania usługi Scheduler zostaną zwrócone informacje o bieżącym statusie zadania. Nie jest możliwe ustawienie tego obiektu przez użytkownika — jest on ustawiony przez system. Jednak wchodzi on w skład obiektu zadania (a nie oddzielnego powiązanego zasobu), dzięki czemu można łatwo sprawdzić status zadania.

Status zadania obejmuje dane na temat czasu poprzedniego wykonania (jeśli miało miejsce), czasu następnego zaplanowanego wykonania (dotyczy zadań w toku) oraz liczby wykonań zadania.

## <a name="retrypolicy"></a>retryPolicy
Na wypadek niepowodzenia zadania usługi Scheduler można zdefiniować zasady ponawiania, aby określić, czy i w jaki sposób akcja zostanie ponowiona. Do tego celu służy obiekt **retryType** — w przypadku braku zasad ponawiania wartość tego obiektu to **none**, jak pokazano powyżej. Jeśli istnieją zasady ponawiania, należy ustawić wartość **fixed**.

Aby ustawić zasady ponawiania, można określić dwa dodatkowe ustawienia: interwał ponawiania (**retryInterval**) i liczbę ponowień (**retryCount**).

Interwał ponawiania określony z użyciem obiektu **retryInterval** to odstęp czasu dzielący kolejne próby. Jego wartość domyślna to 30 sekund. Minimalna wartość, jaką można skonfigurować, wynosi 15 sekund, a wartość maksymalna — 18 miesięcy. Ta wartość jest definiowana w formacie ISO 8601. Wartość liczby ponowień określa się z użyciem obiektu **retryCount**, którego wartość określa liczbę ponownych prób wykonania zadania. Jego wartość domyślna to 4, a wartość maksymalna to 20. Zarówno obiekt **retryInterval**, jak i **retryCount** są opcjonalne. Jeśli dla obiektu **retryType** wybrana zostanie opcja **fixed** i żadne wartości nie zostaną wyraźnie wskazane, obu obiektom zostaje przypisana wartość domyślna.

## <a name="see-also"></a>Zobacz też
 [Co to jest usługa Scheduler?](scheduler-intro.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Tworzenie złożonych harmonogramów i zaawansowanych cykli z użyciem usługi Azure Scheduler](scheduler-advanced-complexity.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

