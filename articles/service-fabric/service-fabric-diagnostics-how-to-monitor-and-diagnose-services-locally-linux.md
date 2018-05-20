---
title: Debugowanie mikrousług Azure w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie i diagnozowanie usługi napisane przy użyciu usługi sieć szkieletowa usług Microsoft Azure na maszynie lokalnej.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 563f9d73d5a8d56e834c36d03aed75812ec123ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorowanie i diagnozowanie usług w Instalatorze programowanie komputera lokalnego


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów z umożliwiają usług kontynuować przy minimalnym przerw w działaniu środowiska użytkownika. Monitorowania i diagnostyki są szczególnie ważne w środowisku produkcyjnym wdrożony rzeczywisty. Przyjmowanie podobne modelu podczas tworzenia usługi gwarantuje, że diagnostycznych potoku działa w przypadku przenoszenia do środowiska produkcyjnego. Sieć szkieletowa usług ułatwia deweloperom usługa implementuje diagnostyki bezproblemowo działającej na konfiguracje pojedynczego komputera lokalnego rozwoju i w konfiguracji klastra produkcyjnego rzeczywistych.


## <a name="debugging-service-fabric-java-applications"></a>Debugowanie aplikacji Java sieci szkieletowej usług

W przypadku aplikacji Java [wiele platform rejestrowania](http://en.wikipedia.org/wiki/Java_logging_framework) są dostępne. Ponieważ `java.util.logging` jest domyślną opcją ze środowiska JRE, jest również używana dla [przykładów kodu w witrynie github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Następujące dyskusji wyjaśniono sposób konfigurowania `java.util.logging` framework.

Przy użyciu java.util.logging można przekierować Dzienniki aplikacji do pamięci, strumienie wyjściowe, pliki konsoli lub gniazda. Dla każdej z tych opcji są już udostępniane w ramach obsługi domyślne. Można utworzyć `app.properties` pliku, aby skonfigurować obsługę plików dla aplikacji przekierować wszystkie dzienniki do pliku lokalnego.

Poniższy fragment kodu zawiera przykładową konfigurację:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Folder wskazywana przez `app.properties` plik musi istnieć. Po `app.properties` plik jest tworzony, musisz także zmodyfikować skrypt punktu wejścia `entrypoint.sh` w `<applicationfolder>/<servicePkg>/Code/` folderu można ustawić właściwości `java.util.logging.config.file` do `app.propertes` pliku. Wpis powinien wyglądać w następujący fragment kodu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ta konfiguracja powoduje dzienniki są zbierane w sposób obracania w `/tmp/servicefabric/logs/`. W takim przypadku plik dziennika ma nazwę mysfapp%u.%g.log gdzie:
* **%u** to unikatowy numer, aby rozwiązać konflikty między równoczesnych procesów Java.
* **%g** jest to liczba generacji, aby odróżnić obracanie dzienniki.

Domyślnie jeśli jawnie jest skonfigurowany bez obsługi, program obsługi konsoli jest zarejestrowany. Jeden Wyświetl dzienniki SYSLOG w obszarze /var/log/syslog.

Aby uzyskać więcej informacji, zobacz [przykładów kodu w witrynie github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Debugowanie aplikacji usługi sieci szkieletowej C#


Wiele platform są dostępne dla śledzenia środowisko CoreCLR aplikacji w systemie Linux. Aby uzyskać więcej informacji, zobacz [GitHub: rejestrowanie](http:/github.com/aspnet/logging).  Ponieważ EventSource jest znane deweloperom korzystającym z języka C#, "w tym artykule wykorzystano źródła zdarzeń śledzenia w środowisko CoreCLR próbek w systemie Linux.

Pierwszym krokiem jest uwzględnienie System.Diagnostics.Tracing, dzięki czemu może zapisać dzienników pamięci, strumienie wyjściowe lub pliki konsoli.  Przy użyciu źródła zdarzeń logowania, należy dodać następującego projektu do pliku project.json:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Niestandardowe odbiornika danych służy do nasłuchiwania zdarzeń usługi, a następnie odpowiednio przekierować je do plików śledzenia. Poniższy fragment kodu przedstawia przykładowe zastosowanie rejestrowania przy użyciu źródła zdarzeń i niestandardowych odbiornika zdarzeń:


```csharp

 public class ServiceEventSource : EventSource
 {
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
   internal class ServiceEventListener : EventListener
   {

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
            using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))           
        { 
                 // report all event information               
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                if (eventData.Message != null)              
            Out.WriteLine(eventData.Message, eventData.Payload.ToArray());              
            else             
        { 
                    string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                    Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");             
        }
           }
        }
    }
```


Poprzedni fragment danych wyjściowych dzienników do pliku w `/tmp/MyServiceLog.txt`. Ta nazwa pliku musi odpowiednio aktualizacji. W przypadku, gdy chcesz przekierowywać dzienniki, aby konsoli, użyj następującego fragmentu kodu w klasie odbiornika zdarzeń niestandardowych:

```csharp
public static TextWriter Out = Console.Out;
```

Przykłady w [przykłady dotyczące języka C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) Użyj EventSource i niestandardowych odbiornika zdarzeń do dziennika zdarzeń do pliku.



## <a name="next-steps"></a>Kolejne kroki
Ten sam kod śledzenia dodany do aplikacji współdziała również z diagnostyki aplikacji w klastrze platformy Azure. Zapoznaj się z tych artykułach, omówiono w nim różne opcje dla narzędzi, które opisano, jak je skonfigurować.
* [Jak zebrać dzienniki Diagnostyka Azure](service-fabric-diagnostics-how-to-setup-lad.md)
