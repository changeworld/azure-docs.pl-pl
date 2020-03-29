---
title: Uruchamianie zadań uruchamiania w usługach w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Zadania uruchamiania pomagają przygotować środowisko usługi w chmurze dla aplikacji. To uczy, jak działają zadania startowe i jak je
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360314"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak skonfigurować i uruchomić zadania uruchamiania usługi w chmurze
Zadania uruchamiania można używać do wykonywania operacji przed rozpoczęciem roli. Operacje, które można wykonać, obejmują instalowanie składnika, rejestrowanie składników COM, ustawianie kluczy rejestru lub uruchamianie długotrwałego procesu.

> [!NOTE]
> Zadania uruchamiania nie mają zastosowania do maszyn wirtualnych, tylko do ról Usługi w chmurze i procesu roboczego.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak działają zadania uruchamiania
Zadania uruchamiania to akcje podejmowane przed rozpoczęciem ról i definiowane w pliku [ServiceDefinition.csdef] przy użyciu elementu [Zadanie] w [elemencie Uruchamiania.] Często zadania uruchamiania są plikami wsadowymi, ale mogą być również aplikacjami konsoli lub plikami wsadowymi uruchamianym skryptami programu PowerShell.

Zmienne środowiskowe przekazują informacje do zadania uruchamiania, a magazyn lokalny może służyć do przekazywania informacji z zadania uruchamiania. Na przykład zmienna środowiskowa może określić ścieżkę do programu, który chcesz zainstalować, a pliki mogą być zapisywane w magazynie lokalnym, które następnie mogą być odczytywane później przez role.

Zadanie uruchamiania może rejestrować informacje i błędy w katalogu określonym przez zmienną środowiskową **TEMP.** Podczas zadania uruchamiania **zmienna środowiskowa TEMP** jest rozpoznawana na *c:\\Temp\\zasobów\\[guid].[ nazwa rolename]\\Katalog RoleTemp* podczas pracy w chmurze.

Zadania uruchamiania mogą być również wykonywane kilka razy między ponownymi uruchomieniami. Na przykład zadanie uruchamiania będzie uruchamiane przy każdym odtworzeniu roli, ale odtwarzanie roli nie zawsze obejmuje ponowne uruchomienie. Zadania startowe powinny być zapisywane w sposób, który pozwala im działać kilka razy bez problemów.

Zadania uruchamiania muszą kończyć się **na poziomie błędu** (lub kod zakończenia) zero, aby proces uruchamiania został ukończony. Jeśli zadanie uruchamiania kończy się na **poziomie błędu**niezerowego, rola nie zostanie uruchomiony.

## <a name="role-startup-order"></a>Kolejność uruchamiania roli
Poniżej wymieniono procedurę uruchamiania roli na platformie Azure:

1. Wystąpienie jest oznaczone jako **Początkowe** i nie odbiera ruchu.
2. Wszystkie zadania uruchamiania są wykonywane zgodnie z **atrybutem taskType.**
   
   * **Proste** zadania są wykonywane synchronicznie, po jednym na raz.
   * Zadania **tła** i **pierwszego planu** są uruchamiane asynchronicznie, równolegle do zadania uruchamiania.  
     
     > [!WARNING]
     > Usługi IIS mogą nie być w pełni skonfigurowane podczas etapu uruchamiania zadania w procesie uruchamiania, więc dane specyficzne dla roli mogą nie być dostępne. Zadania uruchamiania wymagające danych specyficznych dla roli powinny używać programu [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Proces hosta roli jest uruchamiany, a witryna jest tworzona w usługach IIS.
4. Wywoływana jest metoda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart.](/previous-versions/azure/reference/ee772851(v=azure.100))
5. Wystąpienie jest oznaczone jako **Gotowe,** a ruch jest kierowany do wystąpienia.
6. Wywoływana jest metoda [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run.](/previous-versions/azure/reference/ee772746(v=azure.100))

## <a name="example-of-a-startup-task"></a>Przykład zadania uruchamiania
Zadania uruchamiania są definiowane w pliku [ServiceDefinition.csdef] w **elemencie Zadanie.** Atrybut **commandLine** określa nazwę i parametry pliku wsadowego lub polecenia konsoli uruchamiania, atrybut **executionContext** określa poziom uprawnień zadania uruchamiania, a atrybut **TaskType** określa sposób wykonania zadania.

W tym przykładzie zmiennej środowiskowej **MyVersionNumber**, jest tworzony dla zadania uruchamiania i ustawić wartość "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

W poniższym przykładzie plik wsadowy **Startup.cmd** zapisuje wiersz "Bieżąca wersja to 1.0.0.0" do pliku StartupLog.txt w katalogu określonym przez zmienną środowiska TEMP. Linia `EXIT /B 0` zapewnia, że zadanie uruchamiania kończy się **na poziomie błędu** zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> W programie Visual Studio właściwość **Kopiuj do katalogu wyjściowego** dla pliku wsadowego uruchamiania powinna być ustawiona na **Kopiuj zawsze,** aby upewnić się, że plik wsadowy uruchamiania jest poprawnie wdrożony w projekcie na platformie Azure **(pojemnik approot\\** dla ról sieci Web i **approot** dla ról procesu roboczego).
> 
> 

## <a name="description-of-task-attributes"></a>Opis atrybutów elementu Task
Poniżej opisano atrybuty **elementu Zadanie** w pliku [ServiceDefinition.csdef:]

**commandLine** — określa wiersz polecenia dla zadania uruchamiania:

* Polecenie z opcjonalnymi parametrami wiersza polecenia, które rozpoczyna zadanie uruchamiania.
* Często jest to nazwa pliku wsadowego cmd lub .bat.
* Zadanie jest względem folderu\\AppRoot Bin dla wdrożenia. Zmienne środowiskowe nie są rozszerzane przy określaniu ścieżki i pliku zadania. Jeśli wymagane jest rozszerzenie środowiska, można utworzyć mały skrypt cmd, który wywołuje zadanie uruchamiania.
* Może to być aplikacja konsoli lub plik wsadowy uruchamiany skrypt programu [PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** — określa poziom uprawnień dla zadania uruchamiania. Poziom uprawnień może być ograniczony lub podwyższony:

* **Ograniczone**  
  Zadanie uruchamiania jest uruchamiane z tymi samymi uprawnieniami co rola. Gdy **atrybut executionContext** dla elementu [Runtime] jest również **ograniczony,** używane są uprawnienia użytkownika.
* **Podwyższone**  
  Zadanie uruchamiania jest uruchamiane z uprawnieniami administratora. Dzięki temu zadania uruchamiania mogą instalować programy, wprowadzać zmiany konfiguracji usług IIS, wykonywać zmiany w rejestrze i inne zadania na poziomie administratora, bez zwiększania poziomu uprawnień samej roli.  

> [!NOTE]
> Poziom uprawnień zadania uruchamiania nie musi być taki sam jak sama rola.
> 
> 

**taskType** - Określa sposób wykonywania zadania uruchamiania.

* **Prosty**  
  Zadania są wykonywane synchronicznie, po jednym na raz, w kolejności określonej w pliku [ServiceDefinition.csdef.] Gdy jedno **proste** zadanie uruchamiania kończy się **na poziomie błędu** zero, wykonywane jest następne **proste** zadanie uruchamiania. Jeśli nie ma bardziej **prostych** zadań uruchamiania do wykonania, zostanie uruchomiona sama rola.   
  
  > [!NOTE]
  > Jeśli **proste** zadanie kończy się na **poziomie błędu**niezerowego, wystąpienie zostanie zablokowane. Kolejne **proste** zadania uruchamiania i sama rola nie zostaną uruchomiony.
  > 
  > 
  
    Aby upewnić się, że plik wsadowy kończy `EXIT /B 0` się na poziomie **błędu** zero, wykonaj polecenie na końcu procesu pliku wsadowego.
* **Tle**  
  Zadania są wykonywane asynchronicznie, równolegle z uruchomieniem roli.
* **Pierwszym planie**  
  Zadania są wykonywane asynchronicznie, równolegle z uruchomieniem roli. Kluczową różnicą między zadaniem **pierwszego planu** a zadaniem w **tle** jest to, że zadanie pierwszego planu uniemożliwia odtwarzanie lub **zamykanie** roli do czasu zakończenia zadania. Zadania **w tle** nie mają tego ograniczenia.

## <a name="environment-variables"></a>Zmienne środowiskowe
Zmienne środowiskowe są sposobem przekazywania informacji do zadania uruchamiania. Na przykład można umieścić ścieżkę do obiektu blob, który zawiera program do zainstalowania lub numery portów, które będą używane w roli lub ustawienia do kontrolowania funkcji zadania uruchamiania.

Istnieją dwa rodzaje zmiennych środowiskowych dla zadań uruchamiania; statyczne zmienne środowiskowe i zmienne środowiskowe oparte na członkach klasy [RoleEnvironment.] Oba znajdują się w sekcji [Środowisko] pliku [ServiceDefinition.csdef] i używają zarówno [zmiennego] elementu, jak i atrybutu **name.**

Statyczne zmienne środowiskowe używają atrybutu **value** elementu [Variable.] Powyższy przykład tworzy zmienną środowiskową **MyVersionNumber,** która ma wartość statyczną "**1.0.0.0**". Innym przykładem może być utworzenie zmiennej środowiskowej **StagingOrProduction,** którą można ręcznie ustawić na wartości "**przemieszczania**" lub "**produkcji**", aby wykonać różne akcje uruchamiania na podstawie wartości zmiennej środowiskowej **StagingOrProduction.**

Zmienne środowiskowe oparte na elementach członkowskich klasy RoleEnvironment nie używają atrybutu **value** elementu [Variable.] Zamiast tego element podrzędny [RoleInstanceValue,] z odpowiednią wartością atrybutu **XPath,** są używane do tworzenia zmiennej środowiskowej na podstawie określonego elementu członkowskiego klasy [RoleEnvironment.] Wartości atrybutu **XPath,** aby uzyskać dostęp do różnych wartości [roleenvironment] można znaleźć [tutaj](cloud-services-role-config-xpath.md).

Na przykład, aby utworzyć zmienną środowiskową, która jest "**true**", gdy wystąpienie jest uruchomione w emulatorze obliczeniowym i "**false**" podczas uruchamiania w chmurze, należy użyć następujących [elementów Variable] i [RoleInstanceValue:]

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak wykonywać [niektóre typowe zadania uruchamiania](cloud-services-startup-tasks-common.md) za pomocą usługi w chmurze.

[Zapakuj](cloud-services-model-and-package.md) usługę w chmurze.  

[UsługaDefinicja.csdef]: cloud-services-model-and-package.md#csdef
[Zadanie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Uruchamianie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Środowisko uruchomieniowe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Środowisko]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Zmienna]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[Wartość roli]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment (Otoczenie roli)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



