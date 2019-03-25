---
title: Monitorowanie i diagnostyka usług platformy ASP.NET Core w usłudze Service Fabric na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak skonfigurować monitorowanie i diagnostykę dla aplikacji usługi Azure Service Fabric ASP.NET Core.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/21/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: b5485d1dbde48a9fe52196bbeb449b6e4186a88e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402613"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Samouczek: Monitorowanie i diagnozowanie aplikacji platformy ASP.NET Core w usłudze Service Fabric przy użyciu usługi Application Insights

Niniejszy samouczek jest piątą częścią serii. Przeprowadza on przez proces konfiguracji monitorowania i diagnostyki dla aplikacji platformy ASP.NET Core działającej w klastrze usługi Service Fabric za pomocą usługi Application Insights. Będziemy zbierać dane telemetryczne z aplikacji opracowanej w pierwszej części samouczka: [Tworzenie aplikacji platformy .NET w usłudze Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

W czwartej części serii samouczków zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie usługi Application Insights dla aplikacji
> * Zbieranie danych telemetrycznych odpowiedzi na potrzeby śledzenia komunikacji między usługami opartej na protokole HTTP
> * Używanie funkcji mapy aplikacji w usłudze Application Insights
> * Dodawanie zdarzeń niestandardowych za pomocą interfejsu API usługi Application Insights

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Wdrażanie aplikacji w klastrze zdalnym](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Dodawanie punktu końcowego HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Konfigurowanie monitorowania i diagnostyki dla aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zainstaluj program Visual Studio 2017](https://www.visualstudio.com/), a następnie zainstaluj obciążenia **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
* [Instalowanie zestawu SDK usługi Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie lub terminalu polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Konfigurowanie zasobu usługi Application Insights

Usługa Application Insights pełni funkcję platformy do zarządzania wydajnością aplikacji platformy Azure i zalecanej platformy usługi Service Fabric do monitorowania i diagnostyki aplikacji.

Aby utworzyć zasób usługi Application Insights, przejdź do witryny [Azure Portal](https://portal.azure.com). Kliknij pozycję **Utwórz zasób** w lewym menu nawigacji, aby otworzyć witrynę Azure Marketplace. Kliknij pozycję **Monitorowanie + zarządzanie**, a następnie pozycję **Application Insights**.

![Tworzenie nowego zasobu usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Teraz musisz podać wymagane informacje dotyczące atrybutów zasobu, który ma zostać utworzony. Wprowadź odpowiednią *nazwę*, *grupę zasobów* i *subskrypcję*. Ustaw *lokalizację*, w której ma być wdrażany klaster usługi Service Fabric w przyszłości. W tym samouczku wdrożono aplikację w klastrze lokalnym, więc pole *Lokalizacja* nie jest potrzebne. W polu *Typ aplikacji* pozostaw wartość „Aplikacja internetowa platformy ASP.NET”.

![Atrybuty zasobu usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Po wprowadzeniu wymaganych informacji kliknij pozycję **Utwórz**, aby aprowizować zasób — powinno to potrwać około minuty.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Dodawanie usługi Application Insights do usług aplikacji

Uruchom program Visual Studio 2017 z podwyższonym poziomem uprawnień, kliknij prawym przyciskiem myszy ikonę programu Visual Studio w Start Menu i wybierając pozycję **Uruchom jako administrator**. Kliknij kolejno pozycje **Plik**  >  **Otwórz**  >  **Projekt/rozwiązanie** i przejdź do aplikacji do głosowania (utworzonej w pierwszej części samouczka lub sklonowanej z usługi Git). Otwórz *Voting.sln*. Jeśli zostanie wyświetlony monit, aby przywrócić pakiety NuGet aplikacji, kliknij przycisk **tak**.

Wykonaj następujące kroki, aby skonfigurować usługę Application Insights dla usług VotingWeb i VotingData:

1. Kliknij prawym przyciskiem myszy nazwę usługi, a następnie kliknij przycisk **Dodaj > podłączone usługi > Monitorowanie za pomocą usługi Application Insights**.

    ![Konfigurowanie usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>W zależności od typu projektu po kliknięciu prawym przyciskiem myszy nazwy usługi może być konieczne kliknięcie pozycji Dodaj -> Telemetria usługi Application Insights...

2. Kliknij przycisk **wprowadzenie**.
3. Zaloguj się do konta, którego użyto do konfigurowania subskrypcji platformy Azure i wybierz subskrypcję, w którym został utworzony zasób usługi Application Insights. Znajdź zasób w obszarze *Istniejący zasób usługi Application Insights* na liście rozwijanej „Zasób”. Kliknij pozycję **Zarejestruj**, aby dodać usługę Application Insights do usługi.

    ![Rejestrowanie usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Kliknij przycisk **Zakończ**, gdy okno dialogowe, które się pojawi, ukończy działanie.

> [!NOTE]
> Upewnij się, że wykonano powyższe czynności dla **obu** usług w aplikacji, aby zakończyć konfigurowanie usługi Application Insights dla aplikacji.
> W przypadku obu usług jest używany ten sam zasób usługi Application Insights na potrzeby obsługi żądań przychodzących i wychodzących oraz komunikacji między usługami.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Dodawanie pakietu NuGet Microsoft.ApplicationInsights.ServiceFabric.Native do usług

Usługa Application Insights obejmuje dwa pakiety NuGet usługi Service Fabric, których można używać w zależności od scenariusza. Jeden z nich jest używany z usługami natywnymi usługi Service Fabric, a drugi — z kontenerami i plikami wykonywalnymi gościa. W tym przypadku zostanie użyty pakiet NuGet Microsoft.ApplicationInsights.ServiceFabric.Native, który pozwala rozpoznać udostępniany kontekst usługi. Aby uzyskać więcej informacji na temat zestawu SDK usługi Application Insights i pakietów NuGet usługi Service Fabric, zobacz [Usługa Microsoft Application Insights dla usługi Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md).

Poniżej przedstawiono kroki, aby skonfigurować pakiet NuGet:

1. Kliknij prawym przyciskiem myszy **rozwiązania "Voting"** na samej górze z Eksploratora rozwiązań, a następnie kliknij przycisk **Zarządzaj pakietami NuGet dla rozwiązania...** .
2. Kliknij przycisk **Przeglądaj** w górnym menu nawigacji okna „NuGet — rozwiązanie” i zaznacz pole **Uwzględnij wersję wstępną** obok paska wyszukiwania.
>[!NOTE]
>Przed zainstalowaniem pakietu usługi Application Insights może być konieczne zainstalowanie pakietu Microsoft.ServiceFabric.Diagnostics.Internal w podobny sposób, jeśli nie został on wstępnie zainstalowany

3. Wyszukaj pakiet `Microsoft.ApplicationInsights.ServiceFabric.Native` i kliknij odpowiedni pakiet NuGet.
4. Po prawej stronie, kliknij dwa pola wyboru obok dwóch usług w aplikacji, **VotingWeb** i **VotingData** i kliknij przycisk **zainstalować**.
    ![Pakiet NuGet zestawu SDK usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Kliknij przycisk **OK** na *podgląd zmian* okno dialogowe, które pojawia się i zaakceptuj *akceptacja licencji*. Dodawanie pakietu NuGet do usług zostanie ukończone.
6. Teraz musisz skonfigurować inicjator telemetrii w tych dwóch usługach. Aby to zrobić, otwórz *VotingWeb.cs* i *VotingData.cs*. W przypadku obu plików wykonaj następujące dwie czynności:
    1. Dodaj te dwie *przy użyciu* instrukcji w górnej części każdego  *\<Nazwa_usługi > .cs*, po istniejącej *przy użyciu* instrukcji:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. W obu plikach, w zagnieżdżonej *zwracają* instrukcja *CreateServiceInstanceListeners()* lub *CreateServiceReplicaListeners()* w obszarze  *ConfigureServices* > *usług*, za pomocą innych zadeklarowanymi usługami singleton, należy dodać:
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    Spowoduje to dodanie *kontekstu usługi* do telemetrii, co umożliwi lepsze zrozumienie źródła telemetrii w usłudze Application Insights. Zagnieżdżona instrukcja *return* w pliku *VotingWeb.cs* powinna wyglądać następująco:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Podobnie w pliku *VotingData.cs* powinna wyglądać następująco:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Sprawdź, czy `UseApplicationInsights()` metoda jest wywoływana w obu *VotingWeb.cs* i *VotingData.cs* jak pokazano powyżej.

>[!NOTE]
>Przykładowa aplikacja używa protokołu HTTP dla zapewnienia komunikacji usług. W przypadku tworzenia aplikacji za pomocą wersji 2 zdalnej komunikacji usług musisz również dodać następujące wiersze kodu w tym samym miejscu co wcześniej.

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Teraz możesz wdrożyć aplikację. Kliknij pozycję **Uruchom** u góry (lub naciśnij klawisz **F5**) — program Visual Studio skompiluje aplikację i utworzy jej pakiet, skonfiguruje klaster lokalny i wdroży w nim aplikację.

>[!NOTE]
>Jeśli nie masz aktualnej wersji zainstalowany zestaw .NET Core SDK, może wystąpić błąd kompilacji.

Po zakończeniu wdrażania aplikacji przejdź do lokalizacji [localhost:8080](localhost:8080), w której powinna być wyświetlana jednostronicowa przykładowa aplikacja do głosowania. Zagłosuj na kilka różnych wybranych elementów, aby utworzyć przykładowe dane i dane telemetryczne — w tym przypadku wybrano desery.

![Przykładowe głosy w usłudze AI](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Możesz też *usunąć* niektóre opcje głosowania po dodaniu kilka głosów.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Wyświetlanie danych telemetrycznych i mapy aplikacji w usłudze Application Insights

Przejdź do zasobu usługi Application Insights w witrynie Azure Portal.

Kliknij pozycję **Przegląd**, aby wrócić do strony docelowej zasobu. Następnie kliknij pozycję **Wyszukaj** u góry, aby wyświetlić ślady przychodzące. Ślady pojawią się w usłudze Application Insights po kilku minutach. Jeśli nie będą wyświetlane żadne ślady, poczekaj chwilę i kliknij przycisk **Odśwież** u góry.
![Wyświetlanie śladów usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Przewinięcie w dół w oknie *Wyszukiwanie* spowoduje wyświetlenie wszystkich przychodzących danych telemetrycznych dostarczanych z usługą Application Insights. Dla każdej akcji wykonanej w aplikacji do głosowania powinno istnieć wychodzące żądanie PUT z usługi *VotingWeb* (PUT Votes/Put [nazwa]) i przychodzące żądanie PUT z usługi *VotingData* (PUT VoteData/Put [nazwa]), a po nich para żądań GET umożliwiających odświeżenie wyświetlanych danych. Będzie również istnieć ślad zależności dla protokołu HTTP na hoście lokalnym, ponieważ są to żądania HTTP. Oto przykład dodania jednego głosu:

![Przykładowy ślad żądania usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Możesz kliknąć jeden ze śladów, aby wyświetlić więcej informacji o nim. Są dostępne przydatne informacje dotyczące żądania udostępniane przez usługę Application Insights, w tym *czas odpowiedzi* i *adres URL żądania*. Dodano pakiet NuGet usługi Service Fabric, dlatego uzyskasz również dane dotyczące aplikacji w kontekście klastra usługi Service Fabric w poniższej sekcji *Dane niestandardowe*. Obejmuje to kontekst usługi, aby umożliwić wyświetlenie elementów *PartitionID* i *ReplicaId* źródła żądania oraz lepszej wykrywanie problemów podczas diagnozowania błędów w aplikacji.

![Szczegóły śladu usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Aby przejść do mapy aplikacji przedstawiającej dwie połączone usługi, możesz również kliknąć pozycję *Mapa aplikacji* w lewym menu na stronie Przegląd lub kliknąć ikonę **Mapa aplikacji**.

![Szczegóły śladu usługi AI](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Mapa aplikacji pozwala lepiej zrozumieć topologię aplikacji, szczególnie w przypadku dodawania wielu różnych współdziałających ze sobą usług. Udostępnia ona również podstawowe współczynniki powodzeń żądań i ułatwia diagnozowanie nieudanych żądań, dzięki czemu można lepiej zrozumieć błędy. Aby dowiedzieć się więcej o korzystaniu z mapy aplikacji, zobacz [Mapa aplikacji w usłudze Application Insights](../azure-monitor/app/app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Dodawanie instrumentacji niestandardowej do aplikacji

Usługa Application Insights udostępnia wiele funkcji telemetrycznych, jednak możesz dodać więcej instrumentacji niestandardowych. Może to być instrumentacja dostosowana do określonych wymagań biznesowych lub oferująca lepszą diagnostykę w przypadku problemów w aplikacji. Usługa Application Insights udostępnia interfejs API umożliwiający pozyskiwanie niestandardowych zdarzeń i metryk, o których można przeczytać więcej [tutaj](../azure-monitor/app/api-custom-events-metrics.md).

Dodajmy zdarzenia niestandardowe do pliku *VoteDataController.cs* (w obszarze *VotingData*  >  *Kontrolery*), aby śledzić dodawanie i usuwanie głosów w lokalizacji *votesDictionary*.

1. Dodaj element `using Microsoft.ApplicationInsights;` na końcu pozostałych instrukcji using.
2. Zadeklaruj nowy element *TelemetryClient* na początku klasy po utworzeniu elementu *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. W funkcji *Put()* dodaj zdarzenie potwierdzające dodanie głosu. Dodaj element `telemetry.TrackEvent($"Added a vote for {name}");` po ukończeniu transakcji bezpośrednio przed instrukcją return *OkResult*.
4. W funkcji *Delete()* znajduje się element „if/else” oparty na warunku, zgodnie z którym element *votesDictionary* zawiera głosy dla danej opcji głosowania.
    1. Dodaj zdarzenie potwierdzające usunięcie głosu w instrukcji *if* po elemencie *await tx.CommitAsync()*: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Dodaj zdarzenie, aby wskazać, że usunięcie nie zostało wykonane w instrukcji *else*, przed instrukcją return: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Oto przykładowe funkcje *Put()* i *Delete()* po dodaniu zdarzeń:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Po zakończeniu wprowadzania tych zmian **uruchom** aplikację, aby skompilować i wdrożyć najnowszą wersję. Po zakończeniu wdrażania aplikacji przejdź do lokalizacji [localhost:8080](localhost:8080), a następnie dodaj i usuń niektóre opcje głosowania. Następnie wróć do zasobu usługi Application Insights, aby wyświetlić ślady najnowszego przebiegu (jak wcześniej — ślady mogą zostać wyświetlone w usłudze Application Insights po 1–2 minutach). W przypadku wszystkich dodanych i usuniętych głosów powinny być wyświetlane tekst „Zdarzenie niestandardowe* oraz wszystkie dane telemetryczne odpowiedzi.

![zdarzenia niestandardowe](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie usługi Application Insights dla aplikacji
> * Zbieranie danych telemetrycznych odpowiedzi na potrzeby śledzenia komunikacji między usługami opartej na protokole HTTP
> * Używanie funkcji mapy aplikacji w usłudze Application Insights
> * Dodawanie zdarzeń niestandardowych za pomocą interfejsu API usługi Application Insights

Po zakończeniu konfiguracji monitorowania i diagnostyki dla aplikacji platformy ASP.NET spróbuj wykonać następujące czynności:

* [Dalsze eksplorowanie monitorowania i diagnostyki w usłudze Service Fabric](service-fabric-diagnostics-overview.md)
* [Analiza zdarzeń usługi Service Fabric za pomocą usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* Aby dowiedzieć się więcej na temat usługi Application Insights, zobacz [Dokumentacja usługi Application Insights](https://docs.microsoft.com/azure/application-insights/)
