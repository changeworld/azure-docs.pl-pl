---
title: Konwertuj aplikacje usługi Azure Cloud Services na sieci szkieletowej usług
description: W tym przewodniku porównano role sieci Web i procesów roboczych usług sieci Web i usług sieci szkieletowej usług sieci szkieletowej w celu ułatwienia migracji z usług w chmurze do sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463344"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Przewodnik po konwertowaniu ról sieci Web i ról procesu roboczego na usługi bezstanowe sieci szkieletowej usług
W tym artykule opisano sposób migracji ról sieci Web i procesów roboczych usług sieci Web usług w chmurze do usług bezstanowych sieci szkieletowej usług. Jest to najprostsza ścieżka migracji z usług w chmurze do sieci szkieletowej usług dla aplikacji, których ogólna architektura pozostanie mniej więcej taka sama.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projekt usługi w chmurze do projektu aplikacji sieci szkieletowej usług
 Projekt usługi w chmurze i projekt aplikacji sieci szkieletowej usług mają podobną strukturę i oba reprezentują jednostkę wdrażania aplikacji — oznacza to, że każdy z nich definiuje kompletny pakiet, który jest wdrażany w celu uruchomienia aplikacji. Projekt usługi w chmurze zawiera jedną lub więcej ról sieci Web lub procesu roboczego. Podobnie projekt aplikacji sieci szkieletowej usług zawiera jedną lub więcej usług. 

Różnica polega na tym, że projekt usługi w chmurze łączy wdrożenie aplikacji z wdrożeniem maszyny Wirtualnej i w związku z tym zawiera w nim ustawienia konfiguracji maszyny Wirtualnej, podczas gdy projekt aplikacji sieci szkieletowej usług definiuje tylko aplikację, która zostanie wdrożona w zestawie istniejących maszyn wirtualnych w klastrze sieci szkieletowej usług. Sam klaster sieci szkieletowej usług jest wdrażany tylko raz za pośrednictwem szablonu Menedżera zasobów lub za pośrednictwem witryny Azure portal, a wiele aplikacji sieci szkieletowej usług można wdrożyć w nim.

![Porównanie projektów sieci szkieletowej usług i usług w chmurze][3]

## <a name="worker-role-to-stateless-service"></a>Rola pracownika do usługi bezstanowej
Pod względem koncepcy roli procesu roboczego reprezentuje obciążenie bezstanowe, co oznacza, że każde wystąpienie obciążenia jest identyczne i żądania mogą być kierowane do dowolnego wystąpienia w dowolnym momencie. Każde wystąpienie nie powinien zapamiętać poprzedniego żądania. Stan, na który działa obciążenie, jest zarządzany przez zewnętrzny magazyn stanu, taki jak Usługa Azure Table Storage lub usługa Azure Cosmos DB. W sieci szkieletowej usług tego typu obciążenia jest reprezentowana przez usługi bezstanowe. Najprostsze podejście do migracji roli procesu roboczego do sieci szkieletowej usług można wykonać konwertując kod roli procesu roboczego do usługi bezstanowej.

![Rola pracownika do usługi bezstanowej][4]

## <a name="web-role-to-stateless-service"></a>Rola sieci Web do usługi bezstanowej
Podobnie jak rola procesu roboczego, rola sieci Web reprezentuje również obciążenie bezstanowe, a więc koncepcyjnie również może być mapowane do usługi bezstanowej sieci szkieletowej usług. Jednak w przeciwieństwie do ról sieci Web sieć szkieletowa usług nie obsługuje usług IIS. Aby przeprowadzić migrację aplikacji sieci web z roli sieci Web do usługi bezstanowej, należy najpierw przejść do struktury sieci web, która może być hostowana samodzielnie i nie zależy od usług IIS lub System.Web, takich jak ASP.NET Core 1.

| **Aplikacja** | **Obsługiwane** | **Ścieżka migracji** |
| --- | --- | --- |
| Formularze sieci Web ASP.NET |Nie |Konwersja do rdzenia ASP.NET 1 MVC |
| ASP.NET MVC |Z migracją |Uaktualnienie do ASP.NET Core 1 MVC |
| Internetowy interfejs API platformy ASP.NET |Z migracją |Korzystanie z serwera hostowanego samodzielnie lub ASP.NET Core 1 |
| ASP.NET rdzeń 1 |Tak |Nie dotyczy |

## <a name="entry-point-api-and-lifecycle"></a>Interfejs API punktu wejścia i cykl życia
Interfejsy API usługi rola i usługa szkieletu procesu roboczego oferują podobne punkty wejścia: 

| **Punkt wejścia** | **Rola pracownika** | **Usługa sieci szkieletowej usług** |
| --- | --- | --- |
| Przetwarzanie |`Run()` |`RunAsync()` |
| Początek maszyny wirtualnej |`OnStart()` |Nie dotyczy |
| Zatrzymanie maszyny Wirtualnej |`OnStop()` |Nie dotyczy |
| Otwórz odbiornik dla żądań klientów |Nie dotyczy |<ul><li> `CreateServiceInstanceListener()`dla bezpaństwowych</li><li>`CreateServiceReplicaListener()`dla stanowych</li></ul> |

### <a name="worker-role"></a>Rola pracownika
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

### <a name="service-fabric-stateless-service"></a>Usługa bezstanowa sieci szkieletowej usług
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

Oba mają podstawowe zastąpienie "Uruchom", w którym należy rozpocząć przetwarzanie. Usługi sieci `Run`szkieletowej usług łączą się `Start`w `Stop` jeden punkt `RunAsync`wejścia. Usługa powinna rozpocząć `RunAsync` pracę po uruchomieniu i powinna `RunAsync` przestać działać, gdy metoda CancellationToken jest sygnalizowana. 

Istnieje kilka kluczowych różnic między cyklem życia i okresem istnienia ról procesu roboczego i usług sieci szkieletowej usług:

* **Cykl życia:** Największą różnicą jest to, że rola procesu roboczego jest maszyną wirtualną, a więc jej cykl życia jest powiązany z maszyną wirtualną, która zawiera zdarzenia podczas uruchamiania i zatrzymywania maszyny Wirtualnej. Usługa sieci szkieletowej usług ma cykl życia, który jest oddzielony od cyklu życia maszyny Wirtualnej, więc nie obejmuje zdarzeń, gdy maszyna wirtualna hosta lub maszyna uruchamia się i zatrzymuje, ponieważ nie są one powiązane.
* **Okres istnienia:** Wystąpienie roli procesu roboczego `Run` zostanie odtwożeć, jeśli metoda zostanie zakończę działanie. Metoda `RunAsync` w usłudze sieci szkieletowej usług można jednak uruchomić do zakończenia i wystąpienie usługi pozostanie w górę. 

Sieć szkieletowa usług udostępnia opcjonalny punkt wejścia konfiguracji komunikacji dla usług, które nasłuchuje żądań klientów. Zarówno RunAsync i punkt wejścia komunikacji są opcjonalne zastąpienia w usługach sieci szkieletowej usług — usługa może wybrać tylko słuchać żądań klientów lub tylko uruchomić pętlę przetwarzania lub oba — dlatego RunAsync metoda może zakończyć się bez ponownego uruchamiania wystąpienia usługi, ponieważ może nadal nasłuchiwać żądań klientów.

## <a name="application-api-and-environment"></a>Interfejs API i środowisko aplikacji
Interfejs API środowiska usług w chmurze zawiera informacje i funkcje dla bieżącego wystąpienia maszyny Wirtualnej, a także informacje o innych wystąpieniach roli maszyny Wirtualnej. Sieci szkieletowej usług zawiera informacje związane z jego środowiska wykonawczego i niektóre informacje o węźle usługa jest obecnie uruchomiony na. 

| **Zadanie środowiska** | **Usługi w chmurze** | **Service Fabric** |
| --- | --- | --- |
| Ustawienia konfiguracji i powiadomienie o zmianie |`RoleEnvironment` |`CodePackageActivationContext` |
| Magazyn lokalny |`RoleEnvironment` |`CodePackageActivationContext` |
| Informacje o punkcie końcowym |`RoleInstance` <ul><li>Bieżące wystąpienie:`RoleEnvironment.CurrentRoleInstance`</li><li>Inne role i wystąpienie:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`dla bieżącego adresu węzła</li><li>`FabricClient`oraz `ServicePartitionResolver` do odnajdowania punktu końcowego usługi</li> |
| Emulacja środowiska |`RoleEnvironment.IsEmulated` |Nie dotyczy |
| Jednoczesne zdarzenie zmiany |`RoleEnvironment` |Nie dotyczy |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Ustawienia konfiguracji w usługach w chmurze są ustawiane dla roli maszyny Wirtualnej i dotyczą wszystkich wystąpień tej roli maszyny Wirtualnej. Te ustawienia są parami klucz-wartość ustawionymi w plikach ServiceConfiguration.*.cscfg i są dostępne bezpośrednio za pośrednictwem funkcji RoleEnvironment. W sieci szkieletowej usług ustawienia mają zastosowanie indywidualnie do każdej usługi i do każdej aplikacji, a nie do maszyny Wirtualnej, ponieważ maszyna wirtualna może obsługiwać wiele usług i aplikacji. Usługa składa się z trzech pakietów:

* **Kod:** zawiera pliki wykonywalne usługi, pliki binarne, biblioteki DLL i inne pliki, które usługa musi uruchomić.
* **Config:** wszystkie pliki konfiguracyjne i ustawienia usługi.
* **Dane:** statyczne pliki danych skojarzone z usługą.

Każdy z tych pakietów może być niezależnie wersjonowane i uaktualniane. Podobnie jak w chmurze, pakiet konfiguracyjny jest dostępny programowo za pośrednictwem interfejsu API, a zdarzenia są dostępne w celu powiadamiania usługi o zmianie pakietu konfiguracyjnego. Plik Settings.xml może być używany do konfiguracji klucza i dostępu programowego podobnego do sekcji ustawień aplikacji pliku App.config. Jednak w przeciwieństwie do usług w chmurze pakiet konfiguracyjny sieci szkieletowej usług może zawierać dowolne pliki konfiguracyjne w dowolnym formacie, niezależnie od tego, czy jest to format XML, JSON, YAML, czy niestandardowy format binarny. 

### <a name="accessing-configuration"></a>Uzyskiwanie dostępu do konfiguracji
#### <a name="cloud-services"></a>Cloud Services
Ustawienia konfiguracji z ServiceConfiguration.*.cscfg można `RoleEnvironment`uzyskać za pośrednictwem programu . Te ustawienia są globalnie dostępne dla wszystkich wystąpień roli w tym samym wdrożeniu usługi w chmurze.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Każda usługa ma swój własny pakiet konfiguracji. Nie ma wbudowanego mechanizmu dla globalnych ustawień konfiguracji dostępnych dla wszystkich aplikacji w klastrze. Podczas korzystania ze specjalnego pliku konfiguracyjnego Settings.xml sieci szkieletowej w pakiecie konfiguracyjnym wartości w pliku Settings.xml można je napełnić na poziomie aplikacji, dzięki czemu możliwe są ustawienia konfiguracji na poziomie aplikacji.

Ustawienia konfiguracji są dostępami w każdym wystąpieniu usługi za pośrednictwem usługi `CodePackageActivationContext`.

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
Zdarzenie `RoleEnvironment.Changed` jest używane do powiadamiania wszystkich wystąpień roli, gdy nastąpi zmiana w środowisku, takich jak zmiana konfiguracji. Służy do korzystania z aktualizacji konfiguracji bez przetwarzania wystąpień roli lub ponownego uruchamiania procesu roboczego.

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
Każdy z trzech typów pakietów w usłudze — Kod, Config i Dane — ma zdarzenia, które powiadamiają wystąpienie usługi, gdy pakiet jest aktualizowany, dodawany lub usuwany. Usługa może zawierać wiele pakietów każdego typu. Na przykład usługa może mieć wiele pakietów konfiguracyjnych, z których każdy jest wersjonowany indywidualnie i można go uaktualnić. 

Te zdarzenia są dostępne do korzystania ze zmian w pakietach usług bez ponownego uruchamiania wystąpienia usługi.

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
Zadania uruchamiania to akcje, które są wykonywane przed uruchomieniem aplikacji. Zadanie uruchamiania jest zwykle używane do uruchamiania skryptów konfiguracji przy użyciu podwyższonych uprawnień. Usługi w chmurze i sieci szkieletowej usług obsługują zadania uruchamiania. Główną różnicą jest to, że w usługach w chmurze zadanie uruchamiania jest powiązane z maszyną wirtualną, ponieważ jest częścią wystąpienia roli, podczas gdy w sieci szkieletowej usług zadanie uruchamiania jest powiązane z usługą, która nie jest powiązana z żadną konkretną maszyną wirtualną.

| Service Fabric | Cloud Services |
| --- | --- |
| Lokalizacja konfiguracji |UsługaDefinicja.csdef |
| Uprawnienia |"ograniczone" lub "podwyższone" |
| Sekwencjonowanie |"proste", "tło", "pierwszy plan" |

### <a name="cloud-services"></a>Cloud Services
W usługach w chmurze punkt wejścia uruchamiania jest skonfigurowany dla roli w servicedefinition.csdef. 

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
W sieci szkieletowej usług punkt wejścia uruchamiania jest skonfigurowany dla usługi w pliku ServiceManifest.xml:

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

## <a name="a-note-about-development-environment"></a>Uwaga dotycząca środowiska programistycznego
Usługi w chmurze i sieć szkieletowa usług są zintegrowane z programem Visual Studio z szablonami projektów i obsługą debugowania, konfigurowania i wdrażania zarówno lokalnie, jak i na platformie Azure. Usługi w chmurze i sieci szkieletowej usług zapewniają również środowisko wykonawcze rozwoju lokalnego. Różnica polega na tym, że podczas gdy środowisko wykonawcze rozwoju usługi w chmurze emuluje środowisko platformy Azure, w którym działa, sieci szkieletowej usług nie używa emulatora — używa pełnego środowiska uruchomieniowego sieci szkieletowej usługi. Środowisko sieci szkieletowej usług uruchamiane na komputerze deweloperskim lokalnym jest tym samym środowiskiem, które działa w środowisku produkcyjnym.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o niezawodnych usługach sieci szkieletowej usług i podstawowych różnicach między architekturą aplikacji w chmurze i sieci szkieletowej usług, aby dowiedzieć się, jak korzystać z pełnego zestawu funkcji sieci szkieletowej usług.

* [Pierwsze kroki z usługami niezawodne sieci szkieletowej usług](service-fabric-reliable-services-quick-start.md)
* [Przewodnik koncepcyjny dotyczący różnic między usługami w chmurze a siecią szkieletową usług](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
