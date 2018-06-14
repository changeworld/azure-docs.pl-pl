---
title: Uruchom zadania uruchamiania usług w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: Zadania uruchamiania pomóc w przygotowaniu środowiska usługi chmury dla aplikacji. To jest przedstawienie sposób działania uruchamiania zadań i porady były
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 1c1b3aa86dc8211de0c07c9fb68da5685c86f551
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843679"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze
Zadania uruchamiania służy do wykonywania operacji przed rozpoczęciem roli. Operacje, które można wykonać to instalowania składnika, rejestrowanie składników modelu COM, ustawienie kluczy rejestru lub uruchamia proces wymagającą dużo czasu.

> [!NOTE]
> Uruchamianie zadania nie są stosowane do maszyn wirtualnych, tylko do sieci Web usługi chmury i roli proces roboczy.
> 
> 

## <a name="how-startup-tasks-work"></a>Sposób działania uruchamiania zadań
Uruchamianie zadania są działaniach wykonywanych przed rozpocząć role są definiowane w [ServiceDefinition.csdef] pliku przy użyciu [zadań] w elemencie [uruchamiania] element. Często uruchomienia zadania są pliki wsadowe, ale można je również aplikacji konsoli lub pliki wsadowe, które uruchomić skrypty programu PowerShell.

Zmienne środowiskowe przekazywania informacji do uruchomienia zadania, a Magazyn lokalny może służyć do przekazywania informacji poza zadanie uruchamiania. Na przykład zmiennej środowiskowej można określić ścieżkę do programu, który chcesz zainstalować, i do lokalnego magazynu, który można następnie zostać odczytany później przez role można zapisywać pliki.

Zadania uruchamiania można rejestrować informacje i błędy katalogu określonego przez **TEMP** zmiennej środowiskowej. Podczas uruchamiania zadania **TEMP** zmiennej środowiskowej jest rozpoznawana jako *C:\\zasobów\\temp\\[identyfikator guid]. [ rolename]\\RoleTemp* katalogu podczas uruchamiania w chmurze.

Uruchamianie zadania mogą być również wykonywane kilka razy między ponownego uruchomienia. Na przykład zadanie uruchamiania zostanie uruchomione zawsze, gdy rola jest odtwarzana i odtwarza roli nie może zawierać zawsze ponowne uruchomienie komputera. Uruchamianie zadania powinna być zapisana w taki sposób, który pozwala na uruchamianie kilka razy bez problemów.

Zadania uruchamiania musi kończyć się **errorlevel** (lub kod zakończenia) o wartości zero na ukończenie procesu uruchamiania. Jeśli zadanie uruchamiania kończy się na inną niż zero **errorlevel**, rola nie zostanie uruchomiona.

## <a name="role-startup-order"></a>Kolejność uruchamiania roli
Poniższa lista zawiera procedury uruchamiania roli na platformie Azure:

1. Wystąpienie jest oznaczony jako **uruchamianie** i nie odbiera ruch.
2. Wszystkie zadania uruchamiania są wykonywane zgodnie z ich **taskType** atrybutu.
   
   * **Proste** zadania są wykonywane synchronicznie, pojedynczo.
   * **Tła** i **pierwszego planu** zadania są uruchamiane asynchronicznie, równoległe do uruchomienia zadania.  
     
     > [!WARNING]
     > IIS może nie być w pełni skonfigurowane na etapie uruchamiania zadań w procesie uruchamiania, dane specyficzne dla roli nie mogą być dostępne. Uruchamianie zadań, które wymagają dane specyficzne dla roli, należy użyć [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. Proces hosta roli została uruchomiona i że utworzono lokację w usługach IIS.
4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metoda jest wywoływana.
5. Wystąpienie jest oznaczony jako **gotowe** , a ruch jest kierowany do wystąpienia.
6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda jest wywoływana.

## <a name="example-of-a-startup-task"></a>Przykład zadania uruchamiania
Uruchamianie zadania są zdefiniowane w [ServiceDefinition.csdef] pliku w **zadań** elementu. **CommandLine** atrybut określa nazwę i parametry uruchamiania konsoli lub pliku polecenia batch, **executionContext** atrybut określa poziom uprawnień zadanie uruchamiania i **taskType** atrybut określa, jak wykonać zadania.

W tym przykładzie wartość zmiennej środowiskowej **MyVersionNumber**, jest tworzony dla uruchamiania zadań i ustawiona na wartość "**1.0.0.0**".

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

W poniższym przykładzie **Startup.cmd** plik wsadowy zapisuje linię "Bieżąca wersja to 1.0.0.0" do pliku StartupLog.txt w katalogu określonym przez zmienną środowiskową TEMP. `EXIT /B 0` Wiersza gwarantuje, że zadanie uruchamiania kończy się **errorlevel** o wartości zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> W programie Visual Studio **Kopiuj do katalogu wyjściowego** powinien mieć ustawioną właściwość uruchamiania pliku wsadowego **zawsze Kopiuj** być się, że plik wsadowy uruchamiania jest prawidłowo wdrożony do projektu na platformie Azure (**approot\\bin** dla ról sieć Web i **approot** dla roli proces roboczy).
> 
> 

## <a name="description-of-task-attributes"></a>Opis zadania atrybutów
Poniżej opisano atrybuty **zadań** element [ServiceDefinition.csdef] pliku:

**commandLine** — Określa wiersz polecenia zadania uruchamiania:

* Polecenie z parametry opcjonalne wiersza polecenia, który zaczyna się zadanie uruchamiania.
* Często jest to nazwa pliku wsadowego cmd i bat.
* Zadanie jest określana względem AppRoot\\folder Bin dla wdrożenia. Zmienne środowiskowe nie są rozwijane w ustalaniu i ścieżka pliku zadania. Jeśli rozszerzania środowiska jest wymagana, można utworzyć skrypt .cmd małych wywołującą zadania uruchamiania.
* Może być aplikacji konsoli lub pliku wsadowego, który rozpoczyna się [skrypt programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** — określa poziom uprawnień do uruchomienia zadania. Poziom uprawnień może być ograniczony lub z podwyższonym poziomem uprawnień:

* **ograniczone**  
  Uruchamia zadanie uruchamiania takie same uprawnienia rolę. Gdy **executionContext** atrybutu dla [środowiska uruchomieniowego] element jest również **ograniczone**, uprawnienia użytkownika będą używane.
* **z podwyższonym poziomem uprawnień**  
  Zadanie uruchamiania jest uruchamiany z uprawnieniami administratora. Dzięki temu bez zwiększania poziom uprawnień roli samego uruchomienia zadania, zainstalować programy, wprowadzić zmiany w konfiguracji usług IIS, wykonaj zmian w rejestrze i innych zadań poziomu administratora.  

> [!NOTE]
> Poziom uprawnień uruchamiania zadania nie musi być taka sama jak samej roli.
> 
> 

**taskType** — Określa, jak jest wykonywane zadanie uruchamiania.

* **proste**  
  Zadania są wykonywane synchronicznie, pojedynczo, w kolejności określonej w [ServiceDefinition.csdef] pliku. Gdy dla jednego **proste** kończy zadanie uruchamiania **errorlevel** zero, następna **proste** jest wykonywane zadanie uruchamiania. Jeśli nie ma już **proste** uruchamiania zadań do wykonania, a następnie samej roli zostanie uruchomiony.   
  
  > [!NOTE]
  > Jeśli **proste** zadań kończy się na inną niż zero **errorlevel**, wystąpienie zostanie zablokowana. Kolejne **proste** uruchamiania zadań i rolę, nie zostaną uruchomione.
  > 
  > 
  
    Aby upewnić się, że plik wsadowy kończy się **errorlevel** zero, wykonaj polecenie `EXIT /B 0` na końcu procesu pliku wsadowego.
* **tła**  
  Zadania są wykonywane asynchronicznie, równolegle z początkową roli.
* **pierwszego planu**  
  Zadania są wykonywane asynchronicznie, równolegle z początkową roli. Najważniejsza różnica między **pierwszego planu** i **tła** zadania oznacza, że **pierwszego planu** zadań uniemożliwia rolę z lub zamykania, dopóki zadanie zostało zakończone. **Tła** zadania nie ma to ograniczenie.

## <a name="environment-variables"></a>Zmienne środowiskowe
Zmienne środowiskowe to sposób przekazywania informacji do uruchomienia zadania. Na przykład możesz umieścić ścieżki do obiektu blob, który zawiera program, aby zainstalować, lub numery portów, które użyje roli użytkownika lub ustawienia w celu sterowania funkcjami uruchomienia zadania.

Istnieją dwa rodzaje zmiennych środowiskowych dla zadania uruchamiania; zmienne środowiskowe statyczne i zmiennych środowiskowych oparte na elementach członkowskich [ RoleEnvironment] klasy. Są w [środowiska] sekcji [ServiceDefinition.csdef] pliku, a oba używają [zmiennej] elementu i **nazwa** atrybut.

Zmienne środowiskowe statycznych używa **wartość** atrybutu [zmiennej] elementu. W powyższym przykładzie tworzy zmienną środowiskową **MyVersionNumber** , który ma wartość statyczny "**1.0.0.0**". Innym przykładem może być, aby utworzyć **StagingOrProduction** zmiennej środowiskowej, którą można ręcznie ustawić wartości "**przemieszczania**"lub"**produkcji**" do wykonania uruchamianie różnych działań na podstawie wartości z **StagingOrProduction** zmiennej środowiskowej.

Nie należy używać zmiennych środowiskowych na podstawie elementów członkowskich klasy RoleEnvironment **wartość** atrybutu [zmiennej] elementu. Zamiast tego [RoleInstanceValue] elementu podrzędnego z odpowiednią **XPath** wartość atrybutu, są używane do tworzenia zmiennej środowiskowej na podstawie określonego składnika z [ RoleEnvironment] klasy. Wartości **XPath** atrybutu, aby uzyskać dostęp różnych [ RoleEnvironment] wartości można znaleźć [tutaj](cloud-services-role-config-xpath.md).

Na przykład, aby utworzyć zmienną środowiskową, który jest "**true**" gdy wystąpienie jest uruchomiony w emulatorze obliczeń i "**false**" podczas uruchamiania w chmurze, należy użyć następującego [zmiennej] i [RoleInstanceValue] elementy:

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
Dowiedz się, jak wykonać niektóre [typowe zadania uruchamiania](cloud-services-startup-tasks-common.md) z usługi w chmurze.

[Pakiet](cloud-services-model-and-package.md) usługi w chmurze.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[zadań]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[uruchamiania]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[środowiska uruchomieniowego]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[środowiska]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[zmiennej]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[ RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
