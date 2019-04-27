---
title: Run Startup Tasks in Azure Cloud Services | Dokumentacja firmy Microsoft
description: Zadania uruchamiania pomocne przy przygotowywaniu środowiska usługi chmury dla aplikacji. To dowiesz się, jak działają zadań uruchamiania i jak za ich
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 59bfa83ab3432adb7a4df5112367f87014a0b292
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405991"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze
Zadania uruchamiania służy do wykonywania operacji przed rozpoczęciem roli. Operacje, które można wykonać obejmują instalowanie składnika, rejestrowanie składników modelu COM, ustawienie kluczy rejestru lub uruchamiania długotrwałych procesu.

> [!NOTE]
> Zadania uruchamiania nie są stosowane do maszyn wirtualnych, tylko do usługi sieci Web i chmury ról procesów roboczych.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak działają zadania uruchamiania
Zadania uruchamiania to akcje, które są wykonywane przed role rozpoczynać się i są definiowane w [ServiceDefinition.csdef] plików przy użyciu [Zadanie podrzędne] elemencie [Uruchamianie] element. Często zadania uruchamiania to pliki wsadowe, ale można je również aplikacje konsoli lub pliki wsadowe, uruchamianych skryptów programu PowerShell.

Zmienne środowiskowe przekazywania informacji do zadania uruchamiania, a Magazyn lokalny może służyć do przekazywania informacji z zadania uruchamiania. Na przykład zmiennej środowiskowej, można określić ścieżkę do programu, który chcesz zainstalować, a następnie można zapisywać pliki w magazynie lokalnym, który może następnie zostać odczytany później przez role.

Zadanie uruchamiania może rejestrować informacje i błędy w katalogu określonym przez **TEMP** zmiennej środowiskowej. Podczas wykonywania zadania uruchamiania **TEMP** zmienna środowiskowa jest rozpoznawana jako *C:\\zasobów\\temp\\[identyfikator guid]. [ rolename]\\RoleTemp* katalogu podczas uruchamiania w chmurze.

Zadania uruchamiania mogą być również wykonywane kilka razy między ponownymi uruchomieniami. Na przykład zadanie uruchamiania będzie uruchamiane przy każdym odtworzeniu roli, ale odtwarzanie roli nie zawsze obejmuje ponowne uruchomienie. Zadania uruchamiania, zapisywane są w sposób, który umożliwia ich wykonywania kilka razy bez problemów.

Zadania uruchamiania muszą kończyć się znakiem **errorlevel** (lub kod zakończenia) równą zero, aby proces uruchamiania zakończyć. Jeśli zadanie uruchamiania kończy różna od zera **errorlevel**, rola nie zostanie uruchomiona.

## <a name="role-startup-order"></a>Kolejność uruchamiania roli
Poniższa lista zawiera procedury uruchamiania roli na platformie Azure:

1. Wystąpienie jest oznaczony jako **od** i nie otrzymuje ruchu.
2. Wszystkie zadania uruchamiania są wykonywane zgodnie z opisem w ich **taskType** atrybutu.
   
   * **Proste** zadania są wykonywane synchronicznie, pojedynczo.
   * **Tła** i **pierwszego planu** zadania są uruchamiane asynchronicznie, równoległe zadania uruchamiania.  
     
     > [!WARNING]
     > Usługi IIS może nie być w pełni skonfigurowany na etapie uruchamiania zadań w procesie uruchamiania, dzięki czemu dane specyficzne dla roli mogą nie być dostępne. Należy użyć zadań uruchamiania, które wymagają dane specyficzne dla roli [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Proces hosta roli jest uruchomiona, a witryna jest tworzona w usługach IIS.
4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metoda jest wywoływana.
5. Wystąpienie jest oznaczony jako **gotowe** i ruch jest kierowany do wystąpienia.
6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metoda jest wywoływana.

## <a name="example-of-a-startup-task"></a>Przykład zadania uruchamiania
Zadania uruchamiania są zdefiniowane w [ServiceDefinition.csdef] pliku w **zadań** elementu. **CommandLine** atrybut określa nazwę i parametry uruchamiania wsadowego pliku lub konsoli polecenie **kontekście wykonywania** atrybut określa poziom uprawnień zadania uruchamiania i **taskType** atrybut określa, jak zadania zostaną wykonane.

W tym przykładzie zmienna środowiskowa **MyVersionNumber**, jest tworzone dla zadań uruchamiania i ustawiona na wartość "**1.0.0.0**".

**Plik ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

W poniższym przykładzie **Startup.cmd** zapisu pliku wsadowego wiersza "Bieżąca wersja to 1.0.0.0" do pliku StartupLog.txt w katalogu określonym przez zmienną środowiskową TEMP. `EXIT /B 0` Wiersza gwarantuje, że zadanie uruchamiania zakończy się za pomocą **errorlevel** o wartości zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> W programie Visual Studio **Kopiuj do katalogu wyjściowego** powinna być równa właściwości do uruchamiania pliku wsadowego **zawsze Kopiuj** jako się, że uruchamianie pliku wsadowego jest prawidłowo wdrożony do projektu na platformie Azure (**approot\\bin** dla ról sieć Web i **approot** dla ról procesów roboczych).
> 
> 

## <a name="description-of-task-attributes"></a>Opis atrybutów elementu task
Poniżej opisano atrybuty **zadań** element [ServiceDefinition.csdef] pliku:

**commandLine** — Określa wiersz polecenia zadania podrzędnego uruchamiania:

* Polecenie z parametrów opcjonalnych wiersza polecenia, który rozpoczyna się zadanie uruchamiania.
* Często jest to nazwa pliku, cmd lub bat pliku wsadowego.
* Zadanie jest określana względem AppRoot\\folder Bin dla wdrożenia. Zmienne środowiskowe nie zostaną rozwinięte w określeniu, ścieżkę i zadania. Rozszerzanie środowiska jest wymagany, po utworzeniu skryptu .cmd małych, który wywołuje zadanie uruchamiania.
* Może być aplikacji konsoli lub plik wsadowy, który rozpoczyna się [skrypt programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**kontekście wykonywania** -określa poziom uprawnień dla zadania uruchamiania. Poziom uprawnień mogą być ograniczone lub z podwyższonym poziomem uprawnień:

* **Ograniczone**  
  Zadanie uruchamiania jest uruchamiane z takie same uprawnienia jako rolę. Gdy **kontekście wykonywania** atrybutu dla [Środowisko uruchomieniowe] element jest również **ograniczone**, używane są uprawnienia użytkownika.
* **z podwyższonym poziomem uprawnień**  
  Zadanie uruchamiania jest uruchamiane z uprawnieniami administratora. Dzięki temu bez zwiększania poziomu uprawnień roli samego zadania uruchamiania, zainstalować programy, wprowadzić zmiany w konfiguracji usług IIS, wykonaj zmiany w rejestrze i innych zadań poziomu administratora.  

> [!NOTE]
> Poziom uprawnień zadanie uruchamiania nie musi być taka sama jak rola sam.
> 
> 

**taskType** — Określa, jak jest wykonywane zadanie uruchamiania.

* **Proste**  
  Zadania są wykonywane synchronicznie, pojedynczo, w kolejności określonej w [ServiceDefinition.csdef] pliku. Gdy dla jednego **proste** zadanie uruchamiania zakończy się za pomocą **errorlevel** o wartości zero, następne **proste** jest wykonywane zadanie uruchamiania. Jeśli nie będą wyświetlane nie **proste** uruchamiania zadań do wykonania, a następnie sam roli zostanie uruchomiona.   
  
  > [!NOTE]
  > Jeśli **proste** zadań kończy się różna od zera **errorlevel**, wystąpienie zostanie zablokowane. Kolejne **proste** zadaniach uruchamiania i rolę, nie zostanie uruchomiona.
  > 
  > 
  
    Aby upewnić się, że plik wsadowy kończy się **errorlevel** o wartości zero, wykonaj polecenie `EXIT /B 0` na końcu procesu pliku wsadowego.
* **Tło**  
  Zadania są wykonywane asynchronicznie, równolegle z uruchamiania roli.
* **Pierwszy plan**  
  Zadania są wykonywane asynchronicznie, równolegle z uruchamiania roli. Główną różnicą między **pierwszego planu** i **tła** zadanie jest to, że **pierwszego planu** zadań zapobiega rolę z lub zamykania, dopóki zadanie zostało zakończone. **Tła** zadania bez tego ograniczenia.

## <a name="environment-variables"></a>Zmienne środowiskowe
Zmienne środowiskowe służą do przekazywania informacji do zadania uruchamiania. Na przykład możesz umieścić ścieżki do obiektu blob, który zawiera program, aby zainstalować, numery portów, które będą używać Twojej roli lub ustawienia w celu sterowania funkcjami zadania uruchamiania.

Istnieją dwa rodzaje zmiennych środowiskowych dla zadania uruchamiania; zmienne środowiskowe statycznych i zmiennych środowiskowych oparte na elementach członkowskich [RoleEnvironment] klasy. Znajdują się zarówno w [Środowisko] części [ServiceDefinition.csdef] pliku, a oba użyj [Zmienna] elementu i **nazwa** atrybut.

Używa zmiennych środowiskowych statyczne **wartość** atrybutu [Zmienna] elementu. W powyższym przykładzie tworzy zmienną środowiskową **MyVersionNumber** , który ma wartość statyczne "**1.0.0.0**". Innym przykładem może być, aby utworzyć **StagingOrProduction** zmiennej środowiskowej, który można ręcznie ustawić wartości "**przemieszczania**"or"**produkcji**" do wykonania Akcje uruchamiania różnych oparte na wartości **StagingOrProduction** zmiennej środowiskowej.

Nie należy używać zmiennych środowiskowych, oparte na elementach członkowskich klasy RoleEnvironment **wartość** atrybutu [Zmienna] elementu. Zamiast tego [RoleInstanceValue] elementu podrzędnego, z odpowiednią **XPath** wartość atrybutu, są używane do tworzenia zmienną środowiskową, w oparciu o określonym członkiem [RoleEnvironment] klasy. Wartości **XPath** atrybutu, aby uzyskać dostęp różnych [RoleEnvironment] wartości można znaleźć [tutaj](cloud-services-role-config-xpath.md).

Na przykład, aby utworzyć zmienną środowiskową, która jest "**true**" gdy wystąpienie jest uruchomiona w emulatorze obliczeń i "**false**" podczas uruchamiania w chmurze, należy użyć następującego [Zmienna] i [RoleInstanceValue] elementy:

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

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak wykonywać niektóre [popularne zadania uruchamiania](cloud-services-startup-tasks-common.md) z usługą w chmurze.

[Pakiet](cloud-services-model-and-package.md) usługi w chmurze.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Zadanie podrzędne]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Uruchamianie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Środowisko uruchomieniowe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Środowisko]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Zmienna]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
