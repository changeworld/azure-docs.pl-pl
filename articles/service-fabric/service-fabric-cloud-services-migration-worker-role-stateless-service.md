---
title: Konwertuj Azure Cloud Services, aplikacje do usługi Service Fabric | Dokumentacja firmy Microsoft
description: Ten przewodnik zawiera porównanie usług sieci Web i ról procesów roboczych i chmury usługi Service Fabric usług bezstanowych pomocnych w migracji z usług Cloud Services w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 10fb44b0e76282ad78e7687beaa2e50e819e5cd9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667722"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Przewodnik po konwersji w sieci Web i ról procesów roboczych do usługi bezstanowej usługi Service Fabric
W tym artykule opisano sposób migracji z usług sieci Web i chmury ról procesów roboczych do usługi bezstanowej usługi Service Fabric. Jest to najprostsza ścieżka migracji z usług Cloud Services do usługi Service Fabric dla aplikacji, w których ogólna architektura ma około pozostają takie same.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt usługi w chmurze do projektu aplikacji usługi Service Fabric
 Projekt usługi w chmurze i projektu aplikacji usługi Service Fabric mają podobną strukturę i oba reprezentują jednostki wdrożenia dla aplikacji — oznacza to, że ich zdefiniować pełny pakiet, który jest wdrażany do uruchamiania aplikacji. Projekt usługi w chmurze zawiera co najmniej jednej sieci Web lub ról procesów roboczych. Podobnie projekt aplikacji usługi Service Fabric zawiera co najmniej jednej usługi. 

Różnica polega na tym że projektu usługi w chmurze couples wdrożenie aplikacji z wdrożenia maszyny Wirtualnej i dlatego zawiera ustawienia konfiguracji maszyny Wirtualnej, projekt aplikacji usługi Service Fabric określa tylko aplikacji, która zostanie wdrożona na zestaw istniejące maszyny wirtualne w klastrze usługi Service Fabric. Sam klaster usługi Service Fabric jest wdrożyć tylko raz, za pomocą szablonu usługi Resource Manager lub za pośrednictwem witryny Azure portal i można do niego wdrożyć wiele aplikacji usługi Service Fabric.

![Porównanie projekt usługi Service Fabric i Cloud Services][3]

## <a name="worker-role-to-stateless-service"></a>Rola procesu roboczego do usługi bezstanowej
Koncepcyjnie rolę procesu roboczego reprezentuje obciążenia bezstanowego, co oznacza, każde wystąpienie obciążenie jest identyczny i żądania mogą zostać skierowane do dowolnego wystąpienia w dowolnym momencie. Każde wystąpienie jest nieoczekiwane zapamiętywać poprzednie żądanie. Stan, który przetwarza obciążenie jest zarządzane przez Magazyn stanów zewnętrznych, takich jak Azure Table Storage lub baza danych Documentdb Azure. W usłudze Service Fabric tego rodzaju obciążenia jest reprezentowany przez usługę bezstanową. Najprostszą metodą, jak w przypadku migracji roli proces roboczy w usłudze Service Fabric może odbywać się przez przekonwertowanie kod roli proces roboczy na usługę bezstanową.

![Rola procesu roboczego do usługi bezstanowej][4]

## <a name="web-role-to-stateless-service"></a>Rola sieci Web do usługi bezstanowej
Podobnie jak w roli proces roboczy, roli sieci Web również reprezentuje bezstanowego obciążenia, a więc koncepcyjnie on zbyt mogą zostać zmapowane do usługi bezstanowej usługi Service Fabric. Jednak w przeciwieństwie do ról sieci Web usługi Service Fabric nie obsługuje usług IIS. Aby przeprowadzić migrację sieci web aplikacji z roli sieci Web do usługi bezstanowej wymaga przenoszenia najpierw do struktury sieci web, który może być samodzielnie hostowane i nie zależy od usług IIS lub System.Web, takich jak ASP.NET Core 1.

| **Aplikacja** | **Obsługiwane** | **Ścieżki migracji** |
| --- | --- | --- |
| ASP.NET Web Forms |Nie |Konwertuj na MVC platformy ASP.NET Core 1 |
| ASP.NET MVC |Z migracją |Uaktualnianie do platformy ASP.NET Core 1 MVC |
| Internetowy interfejs API platformy ASP.NET |Z migracją |Użyj własnego serwera lub ASP.NET Core 1 |
| ASP.NET Core 1 |Yes |ND |

## <a name="entry-point-api-and-lifecycle"></a>Interfejs API punktu wejścia i cyklu życia
Punkty wejścia podobne oferty interfejsów API dla usługi roli procesu roboczego i usługi Service Fabric: 

| **Entry Point** | **Rola procesu roboczego** | **Usługa Service Fabric** |
| --- | --- | --- |
| Przetwarzanie |`Run()` |`RunAsync()` |
| Początek maszyny Wirtualnej |`OnStart()` |ND |
| Zatrzymywanie maszyny Wirtualnej |`OnStop()` |ND |
| Otwórz odbiornika do obsługi żądań klientów |ND |<ul><li> `CreateServiceInstanceListener()` Aby uzyskać bezstanowe</li><li>`CreateServiceReplicaListener()` Aby uzyskać stanowe</li></ul> |

### <a name="worker-role"></a>Rola procesu roboczego
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Usługi bezstanowej usługi Service Fabric
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Mają zastąpienie podstawowy "Uruchom", w której chcesz rozpocząć przetwarzanie. Łączenie usługi Service Fabric `Run`, `Start`, i `Stop` do pojedynczego punktu wejścia, `RunAsync`. Usługa ma się zacząć pracę, gdy `RunAsync` rozpoczyna się i ma zostać zatrzymana, gdy praca `RunAsync` zasygnalizowania CancellationToken metody. 

Istnieje kilka podstawowych różnic między cyklu życia i okresem istnienia usługi ról procesów roboczych i usługi Service Fabric:

* **Cykl życia:** Największa różnica jest rola procesu roboczego jest maszyną Wirtualną i dlatego jej cyklu projektowania jest powiązany z maszyną wirtualną, która obejmuje zdarzenia uruchamia i zatrzymuje maszyny Wirtualnej. Usługi Service Fabric zawiera cykl życia, który jest oddzielony od cyklu życia maszyny Wirtualnej, więc nie zawiera zdarzeń hosta maszyny Wirtualnej lub komputerze rozpoczyna się i zatrzymać, ponieważ nie są powiązane.
* **Okres istnienia:** Wystąpienia roli procesu roboczego będą Kosza, jeśli `Run` wyjścia metody. `RunAsync` Metody w usłudze Service Fabric można jednak uruchomić ukończone i wystąpienie usługi będzie nadal działać. 

Usługa Service Fabric udostępnia punkt wejścia komunikacji opcjonalne ustawienia dla usług, które nasłuchują żądań klientów. Punkt wejścia RunAsync i komunikacji są opcjonalne przesłonięć w usługi Service Fabric — usługi mają możliwość nasłuchiwał tylko na żądania klienta lub uruchomić tylko pętli przetwarzania i / lub -, co jest dlaczego metodzie RunAsync jest dozwolone, aby zakończyć pracę bez ponownego uruchamiania wystąpienie usługi, ponieważ może nadal nasłuchuje żądań klienta.

## <a name="application-api-and-environment"></a>Aplikacja interfejsu API i środowiska
Środowisko usługi w chmurze interfejs API zawiera informacje i funkcje dla bieżącego wystąpienia maszyny Wirtualnej, a także informacje o innych wystąpień roli maszyny Wirtualnej. Usługa Service Fabric udostępnia informacje dotyczące jego środowiska uruchomieniowego i pewne informacje o węźle usługi są uruchomione. 

| **Zadania środowiska** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Ustawienia konfiguracji i powiadomienia o zmianie |`RoleEnvironment` |`CodePackageActivationContext` |
| Magazyn lokalny |`RoleEnvironment` |`CodePackageActivationContext` |
| Informacje o punkcie końcowym |`RoleInstance` <ul><li>Bieżące wystąpienie: `RoleEnvironment.CurrentRoleInstance`</li><li>Inne role i wystąpienia: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` dla bieżącego adresu węzła</li><li>`FabricClient` i `ServicePartitionResolver` do odnajdywania punktu końcowego usługi</li> |
| Emulacja środowiska |`RoleEnvironment.IsEmulated` |ND |
| Zdarzenia zmiany jednoczesnych |`RoleEnvironment` |ND |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Ustawienia konfiguracji w usługach Cloud Services są ustawiane dla roli maszyny Wirtualnej i dotyczą wszystkich wystąpień tej roli maszyny Wirtualnej. Te ustawienia są pary klucz wartość ustawiona w plikach ServiceConfiguration.*.cscfg i dostępne bezpośrednio przez RoleEnvironment. W usłudze Service Fabric ustawienia stosowane osobno do poszczególnych usług i do każdej aplikacji, a nie do maszyny Wirtualnej, ponieważ maszyna wirtualna może obsługiwać wiele usług i aplikacji. Usługa składa się z trzech pakietów:

* **Kod:** zawiera pliki wykonywalne usługi, pliki binarne, biblioteki dll i innych plików, usługa musi zostać uruchomiony.
* **Config:** wszystkie pliki konfiguracji i ustawień usługi.
* **Dane:** pliki danych statycznych, związane z usługą.

Każda z tych pakietów może być niezależnie wersjonowana i uaktualniana. Podobnie jak usługi w chmurze, pakiet konfiguracji można uzyskać programistycznie przy użyciu interfejsu API i zdarzenia są dostępne do powiadamiania usługi zmianie pakietu konfiguracji. Plik Settings.xml może służyć do konfiguracji klucz wartość i dostęp programowy, podobna do sekcji Ustawienia aplikacji w pliku App.config. Jednak w przeciwieństwie do usług w chmurze, pakiet konfiguracji usługi Service Fabric może zawierać wszystkie pliki konfiguracji, w dowolnym formacie XML, JSON, YAML lub niestandardowy format binarny. 

### <a name="accessing-configuration"></a>Uzyskiwanie dostępu do konfiguracji
#### <a name="cloud-services"></a>Cloud Services
Ustawienia konfiguracji z ServiceConfiguration.*.cscfg jest możliwy za pośrednictwem `RoleEnvironment`. Te ustawienia są ogólnie dostępna dla wszystkich wystąpień roli w tym samym wdrożeniu usługi w chmurze.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Każda usługa ma swój własny pakiet poszczególnych konfiguracji. Nie istnieje wbudowany mechanizm dla globalne ustawienia konfiguracji dostępne przez wszystkie aplikacje w klastrze. Podczas korzystania z usługi Service Fabric specjalne Settings.xml konfiguracji pliku w ramach pakietu konfiguracyjnego wartości Settings.xml można zastąpić na poziomie aplikacji, umożliwiając ustawień konfiguracji na poziomie aplikacji.

Ustawienia konfiguracji są dostępu w ramach każde wystąpienie usługi za pośrednictwem usługi `CodePackageActivationContext`.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Zdarzeń aktualizacji konfiguracji
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed` Zdarzeń służy do Powiadom wszystkich wystąpień roli, gdy nastąpi zmiana w środowisku, takie jak zmiana konfiguracji. Umożliwia używanie aktualizacji konfiguracji bez odtwarzania wystąpień roli lub ponowne uruchomienie procesu roboczego.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Każdy z typów trzech pakietów w usłudze — kodu, konfiguracji i danych — ma zdarzenia, które powiadamiają o wystąpienie usługi, gdy pakiet zostanie zaktualizowany, dodany lub usunięty. Usługa może zawierać wiele pakietów każdego typu. Na przykład usługa może być wiele pakietów konfiguracji każdego indywidualnie numerów wersji oraz możliwość uaktualnienia. 

Te zdarzenia są dostępne z zmiany w pakietach usług bez konieczności ponownego uruchamiania wystąpienie usługi.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Zadania uruchamiania
Zadania uruchamiania to akcje, które są wykonywane przed uruchomieniem aplikacji. Zadanie uruchamiania jest zazwyczaj używany do uruchamiania skryptów instalacji przy użyciu podniesionych przywilejów. Cloud Services i Service Fabric obsługuje zadań uruchamiania. Główną różnicą jest to, czy w usługach w chmurze, zadanie uruchamiania jest powiązany z maszyny Wirtualnej, ponieważ jest on częścią wystąpienia roli, natomiast w usłudze Service Fabric zadanie uruchamiania jest powiązany z usługą, która nie jest związany z dowolnej maszyny Wirtualnej z określonego.

| Service Fabric | Cloud Services |
| --- | --- |
| Lokalizacja konfiguracji |ServiceDefinition.csdef |
| Przywileje |"ograniczony" lub "z podwyższonym poziomem uprawnień" |
| Sekwencyjne |"prosty", "tła", "narzędzia" |

### <a name="cloud-services"></a>Cloud Services
W usługach w chmurze punktu wejścia uruchamiania jest konfigurowana w ramach roli w ServiceDefinition.csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
W usłudze Service Fabric punktu wejścia uruchamiania jest konfigurowana w ramach usługi w ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Uwaga dotycząca środowiska deweloperskiego
Usługi Service Fabric i Cloud Services są zintegrowane z programem Visual Studio przy użyciu szablonów projektu i obsługę debugowania, konfigurowania i wdrażania zarówno lokalnie, jak i na platformie Azure. Zarówno usług w chmurze, jak i usługi Service Fabric udostępniają również lokalne środowisko uruchomieniowe Środowisko deweloperskie. Różnica polega na podczas środowiska uruchomieniowego rozwoju usługi w chmurze emuluje środowiska platformy Azure, na którym jest uruchomione, usługi Service Fabric nie korzysta z emulatora — używa ona pełne środowisko uruchomieniowe usługi Service Fabric. Środowisko usługi Service Fabric, których uruchamianie na lokalnej maszynie do programowania jest środowisko, w którym jest uruchamiany w środowisku produkcyjnym.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj więcej na temat usług Reliable Services usługi Service Fabric i podstawowe różnice między usługami w chmurze i Architektura aplikacji usługi Service Fabric, aby zrozumieć, jak korzystać z pełnego zestawu funkcji usługi Service Fabric.

* [Wprowadzenie do usług Reliable Services usługi Service Fabric](service-fabric-reliable-services-quick-start.md)
* [Przewodnik koncepcyjny różnice między usługami w chmurze i usługi Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
