---
title: Konwertowanie aplikacji usługi w chmurze Azure do mikrousług | Dokumentacja firmy Microsoft
description: Ten przewodnik zawiera porównanie bezstanowych usługi sieci Web usługi w chmurze i roli proces roboczy i sieci szkieletowej usług ułatwia migrację z usług chmury do sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c6bdd6f88c9008a8d9c15d22bdcf263190424649
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Przewodnik po konwersji sieci Web i proces roboczy usług bezstanowych sieci szkieletowej usług
W tym artykule opisano sposób migracji z usług chmury w sieci Web i proces roboczy do usługi sieć szkieletowa usług bezstanowych. Jest to najprostsza ścieżka migracji z usług w chmurze sieci szkieletowej usług dla aplikacji, których ogólna architektura ma około pozostają takie same.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt usługi w chmurze do projektu aplikacji sieci szkieletowej usług
 Projektu usługi w chmurze i projektu aplikacji sieci szkieletowej usług mają podobną strukturę i reprezentuje zarówno jednostki wdrożenia aplikacji — czyli każdy z nich zdefiniuj pełny pakiet, który jest wdrażany do uruchamiania aplikacji. Projekt usługi w chmurze zawiera co najmniej jednej sieci Web lub roli proces roboczy. Podobnie projektu aplikacji sieci szkieletowej usług zawiera co najmniej jedna usługa. 

Różnica polega na tym że projektu usługi w chmurze couples wdrożenie aplikacji z wdrożenia maszyny Wirtualnej, a w związku z tym zawiera ustawienia konfiguracji maszyny Wirtualnej w ramach tego projektu aplikacji sieci szkieletowej usług określa tylko aplikację, która zostanie wdrożona do zestawu istniejące maszyny wirtualne w klastrze usługi sieć szkieletowa usług. Samego klastra usługi sieć szkieletowa jest wdrożona tylko raz, za pomocą szablonu usługi Resource Manager lub za pośrednictwem portalu Azure i można do niego wdrożyć wielu aplikacji sieci szkieletowej usług.

![Porównanie projektu usługi Service Fabric i usług w chmurze][3]

## <a name="worker-role-to-stateless-service"></a>Rola proces roboczy do usługi bezstanowej
Koncepcyjnie rolę procesu roboczego reprezentuje bezstanowego obciążenia, co oznacza każde wystąpienie obciążenie jest identyczne i żądania mogą być kierowane do dowolnego wystąpienia w dowolnym momencie. Każde wystąpienie nie jest oczekiwany do zapamiętania poprzedniego żądania. Stan, który obciążenie działa na jest zarządzana przez Magazyn stanów zewnętrzne, takie jak magazyn tabel Azure lub bazy danych dokumentów platformy Azure. W sieci szkieletowej usług tego rodzaju obciążenia jest reprezentowany przez usługę bezstanową. Najprostsza metoda migrowania roli procesu roboczego platformy Service Fabric może odbywać się konwertując kod roli proces roboczy usług bezstanowych.

![Rola proces roboczy do usługi bezstanowej][4]

## <a name="web-role-to-stateless-service"></a>Rola sieci Web do usługi bezstanowej
Podobnie jak rola proces roboczy, rolę sieci Web również reprezentuje bezstanowego obciążenia, a więc koncepcyjnie on zbyt mogą być mapowane na usługę bezstanową sieci szkieletowej usług. Jednak w przeciwieństwie do ról sieci Web, usługi Service Fabric nie obsługuje usług IIS. Aby przeprowadzić migrację sieci web aplikacji z roli sieci Web do usługi bezstanowej wymaga przenoszenia najpierw do platforma sieci web, która może być hostowana samodzielnie i nie zależą od usług IIS lub System.Web, takich jak ASP.NET Core 1.

| **Aplikacji** | **Obsługiwane** | **Ścieżki migracji** |
| --- | --- | --- |
| Formularze sieci Web ASP.NET |Nie |Konwertuj na MVC ASP.NET Core 1 |
| ASP.NET MVC |Z migracją |Uaktualnienie do platformy ASP.NET Core 1 MVC |
| Składnik Web API platformy ASP.NET |Z migracją |Użyj serwera siebie lub platformy ASP.NET Core 1 |
| ASP.NET Core 1 |Yes |ND |

## <a name="entry-point-api-and-lifecycle"></a>Interfejs API punktu wejścia i cykl życia
Rola proces roboczy i sieci szkieletowej usług usługi interfejsów API oferta podobne punkty wejścia: 

| **Entry Point** | **Rola procesu roboczego** | **Usługa sieci szkieletowej usług** |
| --- | --- | --- |
| Przetwarzanie |`Run()` |`RunAsync()` |
| Początek maszyny Wirtualnej |`OnStart()` |ND |
| Zatrzymanie maszyny Wirtualnej |`OnStop()` |ND |
| Otwórz odbiornika dla żądań klientów |ND |<ul><li> `CreateServiceInstanceListener()` Aby uzyskać bezstanowych</li><li>`CreateServiceReplicaListener()` dla stateful</li></ul> |

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

### <a name="service-fabric-stateless-service"></a>Usługa sieci szkieletowej usług bezstanowych
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

Mają zastąpienie podstawowego "Uruchom", w którym na przetworzenie. Łączenie usługi sieć szkieletowa usług `Run`, `Start`, i `Stop` na jeden punkt wejścia, `RunAsync`. Podczas pracy z usługą powinny one zacząć `RunAsync` uruchamia i ma zostać zatrzymana, gdy praca `RunAsync` zostanie zasygnalizowane metody CancellationToken. 

Istnieje kilka podstawowych różnic między cykl życia i okresem istnienia usług roli proces roboczy i sieci szkieletowej usług:

* **Cykl życia:** największych różnica polega na roli proces roboczy jest maszyny Wirtualnej, a więc cykl życia jest związany z maszyny Wirtualnej, która obejmuje zdarzenia podczas uruchamiania i zatrzymywania w Maszynie wirtualnej. Usługa Service Fabric ma cykl życia, która jest oddzielona od maszyny Wirtualnej cykl życia, więc nie zawiera zdarzenia po hosta maszyny Wirtualnej lub maszyny uruchamia i zatrzymać, ponieważ nie są powiązane.
* **Okres istnienia:** odtwarzana wystąpienia roli proces roboczy, jeśli `Run` wyjścia metody. `RunAsync` Metody usługi sieci szkieletowej usług jednak można uruchomić w celu ukończenia i wystąpienie usługi będą przechowywane w górę. 

Sieć szkieletowa usług udostępnia punkt wejścia instalacji komunikacji opcjonalne dla usług, które nasłuchuje żądań klienta. Punkt wejścia zarówno RunAsync, jak i komunikacji są opcjonalne zastąpień w - usługi wybrać nasłuchiwał tylko na żądania klientów, lub uruchomić tylko w pętli przetwarzania i/lub - usługi sieć szkieletowa usług, które dlatego metodzie RunAsync będzie mógł zamknąć bez ponownego uruchamiania Usługa wystąpienia, ponieważ mogą nadal nasłuchuje żądań klienta.

## <a name="application-api-and-environment"></a>Interfejs API aplikacji i środowiska
Interfejs API środowiska usługi w chmurze zawiera informacje oraz funkcje dla bieżącego wystąpienia maszyny Wirtualnej, a także informacje o innych wystąpień roli maszyny Wirtualnej. Usługi Service Fabric zawiera informacje dotyczące jego środowiska uruchomieniowego i niektóre informacje o węźle usługi jest obecnie uruchomiony w. 

| **Zadania środowiska** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Ustawienia konfiguracji i powiadomienia o zmianie |`RoleEnvironment` |`CodePackageActivationContext` |
| Magazyn lokalny |`RoleEnvironment` |`CodePackageActivationContext` |
| Informacje o punkcie końcowym |`RoleInstance` <ul><li>Bieżące wystąpienie: `RoleEnvironment.CurrentRoleInstance`</li><li>Inne role i wystąpienie: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` dla bieżącego adresu węzła</li><li>`FabricClient` i `ServicePartitionResolver` do odnajdywania punktu końcowego usługi</li> |
| Emulacja środowiska |`RoleEnvironment.IsEmulated` |ND |
| Zdarzenie zmiany jednoczesnych |`RoleEnvironment` |ND |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Ustawienia konfiguracji usług w chmurze są ustawiane dla roli maszyny Wirtualnej i dotyczą wszystkich wystąpień tej roli maszyny Wirtualnej. Te ustawienia są pary klucz wartość ustawiona w plikach ServiceConfiguration.*.cscfg i jest dostępny bezpośrednio za pomocą RoleEnvironment. W sieci szkieletowej usług ustawienia stosowane osobno do każdej usługi i dla każdej aplikacji, a nie do maszyny Wirtualnej, ponieważ maszyna wirtualna może obsługiwać wiele usług i aplikacji. Usługa składa się z trzech pakietów:

* **Kod:** zawiera pliki wykonywalne usługi, plików binarnych biblioteki dll i inne pliki wymagane do uruchomienia usługę.
* **Config:** wszystkich plików konfiguracji i ustawień usługi.
* **Dane:** plików statycznych danych skojarzony z usługą.

Każdy z tych pakietów można niezależnie określonej wersji, jak i uaktualnionych. Podobnie jak usługi w chmurze, pakietu konfiguracji można uzyskać programistycznie przy użyciu interfejsu API i zdarzenia są dostępne do powiadamiania usługi o zmianie konfiguracji pakietu. Pliku Settings.xml może służyć do kluczy i wartości konfiguracji i dostęp programistyczny podobne w sekcji Ustawienia aplikacji w pliku App.config. Jednak w przeciwieństwie do usługi w chmurze, pakiet konfiguracji sieci szkieletowej usług może zawierać pliki konfiguracji w dowolnym formacie XML, JSON, yaml programu lub niestandardowy format binarny. 

### <a name="accessing-configuration"></a>Uzyskiwanie dostępu do konfiguracji
#### <a name="cloud-services"></a>Cloud Services
Ustawienia konfiguracji z ServiceConfiguration.*.cscfg jest możliwy za pośrednictwem `RoleEnvironment`. Te ustawienia są ogólnie dostępna dla wszystkich wystąpień roli w ramach tego samego wdrożenia usługi w chmurze.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Każda usługa ma własny pakiet konfiguracji poszczególnych. Nie istnieje wbudowany mechanizm dla globalne ustawienia konfiguracji dostępne przez wszystkie aplikacje w klastrze. Podczas korzystania z usługi Service Fabric specjalne Settings.xml konfiguracji pliku w ramach pakietu konfiguracji wartości w pliku Settings.xml można zastąpić na poziomie aplikacji, umożliwiając ustawień konfiguracji na poziomie aplikacji.

Ustawienia konfiguracji są dostępu w ramach każdego wystąpienia usługi za pośrednictwem usługi `CodePackageActivationContext`.

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

### <a name="configuration-update-events"></a>Zdarzenia aktualizacji konfiguracji
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed` Zdarzeń jest używana do powiadamiania wszystkich wystąpień roli w przypadku zmiany w środowisku, np. o zmianie konfiguracji. Służy to użycie konfiguracji aktualizacji bez odtwarzania wystąpień roli lub ponowne uruchomienie procesu roboczego.

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
Poszczególnych typów pakietu trzy usługi kodu, konfiguracji i danych - ma zdarzeń, które powiadamiają o wystąpieniu usługi, gdy pakiet zostanie zaktualizowany, dodany lub usunięty. Usługa może zawierać wielu pakietów każdego typu. Na przykład usługa może mieć wielu pakietów konfiguracji każdego indywidualnie numerów wersji i możliwej. 

Te zdarzenia są dostępne zużyje zmiany pakietów usług bez konieczności ponownego uruchamiania wystąpienie usługi.

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
Uruchamianie zadania są działaniach wykonywanych przed uruchomieniem aplikacji. Zadanie uruchamiania jest zwykle używany do uruchamiania skryptów instalacji, korzystając z podwyższonym poziomem uprawnień. Usługa Service Fabric i usług w chmurze obsługuje zadania uruchamiania. Główna różnica polega na tym, że usług w chmurze, zadanie uruchamiania jest związany z maszyny Wirtualnej, ponieważ jest ona częścią wystąpienia roli, natomiast w sieci szkieletowej usług zadanie uruchamiania jest związany z usługą, które nie są powiązane do żadnej określonej maszyny Wirtualnej.

| Service Fabric | Cloud Services |
| --- | --- | --- |
| Lokalizacja konfiguracji |ServiceDefinition.csdef |
| Przywileje |"ograniczony" lub "z podwyższonym poziomem uprawnień" |
| Sekwencjonowanie |"prosty", "tła", "narzędzia" |

### <a name="cloud-services"></a>Cloud Services
Usług w chmurze punktu wejścia uruchamiania jest skonfigurowany dla każdej roli w ServiceDefinition.csdef. 

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
W sieci szkieletowej usług punktu wejścia uruchamiania jest skonfigurowany dla usługi w pliku ServiceManifest.xml:

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

## <a name="a-note-about-development-environment"></a>Uwagi dotyczące środowiska deweloperskiego
Usługi Service Fabric i usług w chmurze są zintegrowane z programem Visual Studio z szablonów projektu i obsługę debugowania, konfigurowanie i wdrażanie, zarówno lokalnie, jak i na platformie Azure. Usługa Service Fabric i usług w chmurze udostępniają rozwoju lokalnego środowiska uruchomieniowego. Różnica polega na podczas rozwoju środowiska uruchomieniowego usługi w chmurze emuluje środowiska platformy Azure, na którym jest uruchomiony, Service Fabric nie korzysta z emulatora — używa pełne środowisko uruchomieniowe usługi sieć szkieletowa usług. Środowisko sieci szkieletowej usług uruchomionych na komputerze deweloperskim lokalnego jest tym samym środowisku, który jest uruchamiany w środowisku produkcyjnym.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj więcej na temat usługi sieci szkieletowej usług niezawodnych i podstawowe różnice między usługi w chmurze i architektury aplikacji usługi Service Fabric, zrozumienie, jak korzystać z pełnego zestawu funkcji usługi sieć szkieletowa usług.

* [Wprowadzenie do korzystania z usługi sieć szkieletowa niezawodne usługi](service-fabric-reliable-services-quick-start.md)
* [Przewodnik koncepcyjny różnice między usługami w chmurze i sieci szkieletowej usług](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
