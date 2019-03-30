---
title: Debugowanie usługi Azure Service Fabric aplikacje w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować i diagnozować usługi Service Fabric na lokalnym komputerze deweloperskim systemu Linux.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f0b850038a29dd0949def97b359b2b7a5ce920bc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58659750"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorowanie i diagnozowanie usług w lokalnym komputerze deweloperskim


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów z umożliwiają usług kontynuować przy minimalnym zakłóceniu środowisko użytkownika. Monitorowanie i Diagnostyka są szczególnie ważne w środowisku rzeczywistej produkcji wdrożone. Przyjęcie podobne modelu podczas tworzenia usługi gwarantuje, że potok diagnostycznych działa po przejściu do środowiska produkcyjnego. Usługa Service Fabric można łatwo dla deweloperów usługi do zaimplementowania diagnostyki, która bezproblemowo mogą korzystać z różnych ustawień rozwoju lokalnego pojedynczego komputera i konfiguracji klastra produkcyjnego w rzeczywistych warunkach.


## <a name="debugging-service-fabric-java-applications"></a>Debugowanie aplikacji Java usługi Service Fabric

W przypadku aplikacji Java [wiele struktur rejestrowania](https://en.wikipedia.org/wiki/Java_logging_framework) są dostępne. Ponieważ `java.util.logging` to opcja domyślna, za pomocą środowiska JRE, służy również do [przykładów kodu w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). Następujące dyskusji opisano sposób konfigurowania `java.util.logging` framework.

Za pomocą java.util.logging można przekierować Dzienniki aplikacji do pamięci, strumienie wyjściowe, pliki konsoli lub gniazda. Dla każdej z tych opcji Brak obsługi domyślne poprawiał w ramach. Możesz utworzyć `app.properties` plik do skonfigurowania obsługi plików dla aplikacji w taki sposób przekierować wszystkie dzienniki do pliku lokalnego.

Poniższy fragment kodu zawiera przykładową konfigurację:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Folder wskazywany przez `app.properties` plik musi istnieć. Po `app.properties` tworzony jest plik, musisz także zmodyfikować skrypt punktu wejścia `entrypoint.sh` w `<applicationfolder>/<servicePkg>/Code/` folder, aby ustawić właściwości `java.util.logging.config.file` do `app.properties` pliku. Wpis powinien wyglądać w następujący fragment kodu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ta konfiguracja powoduje dzienniki są zbierane w sposób rotacji na `/tmp/servicefabric/logs/`. W takim przypadku plik dziennika ma nazwę mysfapp%u.%g.log gdzie:
* **%u** to unikatowy numer rozwiązywać konflikty między jednoczesnych procesów języka Java.
* **%g** jest liczbą generowania rozróżnienie między rotacji dzienników.

Domyślnie jeśli żadna procedura obsługi nie jest jawnie skonfigurowany, obsługi konsoli jest zarejestrowany. Jeden można przeglądać dzienniki usługi SYSLOG w obszarze /var/log/syslog.

Aby uzyskać więcej informacji, zobacz [przykładów kodu w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Debugowanie aplikacji usługi Service Fabric C#


Wiele struktur są dostępne w celu śledzenia aplikacji CoreCLR w systemie Linux. Aby uzyskać więcej informacji, zobacz [GitHub: rejestrowanie](http:/github.com/aspnet/logging).  Ponieważ źródła zdarzeń jest znane deweloperów języka C# "w tym artykule wykorzystano źródła zdarzeń śledzenia w przykładach CoreCLR w systemie Linux.

Pierwszym krokiem jest zawierać System.Diagnostics.Tracing dzienników można zapisywać do pamięci, strumieni wyjściowych lub pliki konsoli.  Do rejestrowania przy użyciu źródła zdarzeń, należy dodać do Twojego pliku project.json następującego projektu:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Niestandardowe EventListener służy do nasłuchiwania zdarzeń usługi, a następnie odpowiednio przekierowywać je do plików śledzenia. Poniższy fragment kodu przedstawia przykład implementacji rejestrowania przy użyciu źródła zdarzeń i EventListener niestandardowe:


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
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
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


Poprzedni fragment kodu wysyła dzienniki do pliku w `/tmp/MyServiceLog.txt`. Ta nazwa pliku musi być odpowiednio aktualizowane. W przypadku, gdy użytkownik chce przekierowywanie dzienników do konsoli, należy użyć następującego fragmentu kodu w klasie EventListener niestandardowe:

```csharp
public static TextWriter Out = Console.Out;
```

Przykłady w [przykłady w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) używać źródła zdarzeń i niestandardowe EventListener do rejestrowania zdarzeń do pliku.



## <a name="next-steps"></a>Kolejne kroki
Ten sam kod śledzenia dodane do Twojej aplikacji współpracuje również z diagnostyki aplikacji w klastrze platformy Azure. Zapoznaj się z następującymi artykułami, które omówiono różne opcje narzędzi i sposobu ich konfigurowania.
* [Jak gromadzić dzienniki za pomocą diagnostyki Azure](service-fabric-diagnostics-how-to-setup-lad.md)
