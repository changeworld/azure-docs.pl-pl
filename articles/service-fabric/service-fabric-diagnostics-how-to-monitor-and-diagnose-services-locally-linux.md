---
title: Debugowanie aplikacji sieci szkieletowej usług Azure w systemie Linux
description: Dowiedz się, jak monitorować i diagnozować usługi sieci szkieletowej usług na lokalnym komputerze deweloperskim systemu Linux.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526298"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitorowanie i diagnozowanie usług w lokalnej konfiguracji tworzenia maszyn systemu Linux


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów umożliwiają usługom kontynuowanie z minimalnymi zakłóceniami w doświadczeniu użytkownika. Monitorowanie i diagnostyka mają kluczowe znaczenie w rzeczywistym wdrożonym środowisku produkcyjnym. Przyjęcie podobnego modelu podczas opracowywania usług zapewnia, że potok diagnostyczny działa po przejściu do środowiska produkcyjnego. Sieć szkieletowa usług ułatwia deweloperom usług implementowanie diagnostyki, która może bezproblemowo pracować zarówno w konfiguracjach lokalnego programowania jednoprzez, jak i w rzeczywistych konfiguracjach klastra produkcyjnego.


## <a name="debugging-service-fabric-java-applications"></a>Aplikacje Java usługi debugowania

W przypadku aplikacji Java dostępnych jest [wiele struktur rejestrowania.](https://en.wikipedia.org/wiki/Java_logging_framework) Ponieważ `java.util.logging` jest to opcja domyślna z jre, jest również używana dla [przykładów kodu w GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). W poniższej dyskusji wyjaśniono, jak skonfigurować platformę. `java.util.logging`

Za pomocą java.util.logging można przekierować dzienniki aplikacji do pamięci, strumieni wyjściowych, plików konsoli lub gniazd. Dla każdej z tych opcji istnieją domyślne programy obsługi już dostarczone w ramach. Można utworzyć `app.properties` plik, aby skonfigurować program obsługi plików dla aplikacji, aby przekierować wszystkie dzienniki do pliku lokalnego.

Poniższy fragment kodu zawiera przykładową konfigurację:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Folder wskazany przez `app.properties` plik musi istnieć. Po `app.properties` utworzeniu pliku należy również zmodyfikować skrypt `entrypoint.sh` punktu `<applicationfolder>/<servicePkg>/Code/` wejścia w folderze, aby ustawić właściwość `java.util.logging.config.file` na `app.properties` plik. Wpis powinien wyglądać następująco:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ta konfiguracja powoduje, że dzienniki są `/tmp/servicefabric/logs/`zbierane w sposób obrotowy w . Plik dziennika w tym przypadku nosi nazwę mysfapp%u.%g.log, gdzie:
* **%u** jest unikatową liczbą do rozwiązywania konfliktów między równoczesnymi procesami Java.
* **%g** jest numerem generacji do rozróżniania obrotowych dzienników.

Domyślnie, jeśli żaden program obsługi nie jest jawnie skonfigurowany, program obsługi konsoli jest zarejestrowany. Można wyświetlić dzienniki w syslog pod /var/log/syslog.

Aby uzyskać więcej informacji, zobacz [przykłady kodu w usłudze GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Aplikacje sieci szkieletowej usługi debugowania W#


Wiele struktur są dostępne do śledzenia aplikacji CoreCLR w systemie Linux. Aby uzyskać więcej informacji, zobacz [GitHub: rejestrowanie](http:/github.com/aspnet/logging).  Ponieważ EventSource jest znany deweloperom języka C#,'w tym artykule używa EventSource do śledzenia w przykładach CoreCLR w systemie Linux.

Pierwszym krokiem jest uwzględnienie System.Diagnostics.Tracing, dzięki czemu można zapisywać dzienniki w pamięci, strumieniach wyjściowych lub plikach konsoli.  W przypadku rejestrowania przy użyciu źródła zdarzeń dodaj do pliku project.json następujący projekt:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Można użyć niestandardowego EventListener nasłuchiwać zdarzenia usługi, a następnie odpowiednio przekierować je do śledzenia plików. Poniższy fragment kodu pokazuje przykładową implementację rejestrowania przy użyciu eventsource i niestandardowego EventListener:


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


Poprzedni fragment kodu wyprowadza dzienniki do pliku w `/tmp/MyServiceLog.txt`pliku . Ta nazwa pliku musi zostać odpowiednio zaktualizowana. Jeśli chcesz przekierować dzienniki do konsoli, użyj następującego fragmentu kodu w dostosowanej klasie EventListener:

```csharp
public static TextWriter Out = Console.Out;
```

Przykłady w [c# przykłady](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) używać EventSource i niestandardowe EventListener do rejestrowania zdarzeń do pliku.



## <a name="next-steps"></a>Następne kroki
Ten sam kod śledzenia dodany do aplikacji działa również z diagnostyką aplikacji w klastrze platformy Azure. Zapoznaj się z tymi artykułami, w których omówiono różne opcje narzędzi i opisano sposób ich konfigurowania.
* [Jak zbierać dzienniki za pomocą usługi Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
