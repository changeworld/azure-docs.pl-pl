---
title: Kanały telemetrii w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Jak dostosować kanały telemetryczne w zestawach SDK usługi Azure Application Insights dla platformy .NET i .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275700"
---
# <a name="telemetry-channels-in-application-insights"></a>Kanały telemetrii w usłudze Application Insights

Kanały telemetryczne są integralną częścią zestawów [SDK usługi Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md) Zarządzają buforowania i transmisji danych telemetrycznych do usługi Application Insights. Wersje .NET i .NET Core sdks mają dwa wbudowane `InMemoryChannel` `ServerTelemetryChannel`kanały telemetryczne: i . W tym artykule opisano szczegółowo każdy kanał, w tym sposób dostosowywania zachowania kanału.

## <a name="what-are-telemetry-channels"></a>Co to są kanały telemetryczne?

Kanały telemetryczne są odpowiedzialne za buforowanie elementów telemetrycznych i wysyłanie ich do usługi Application Insights, gdzie są przechowywane do wykonywania zapytań i analizy. Kanał telemetryczny jest dowolną klasą, [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) która implementuje interfejs.

Metoda `Send(ITelemetry item)` kanału telemetrycznego jest wywoływana po wywołaniu wszystkich inicjatorów telemetrii i procesorów telemetrii. Tak więc wszystkie elementy porzucone przez procesor telemetrii nie dotrze do kanału. `Send()`zazwyczaj nie wysyła przedmiotów do zaplecza natychmiast. Zazwyczaj buforuje je w pamięci i wysyła je w partiach, dla wydajnej transmisji.

[Live Metrics Stream](live-stream.md) ma również niestandardowy kanał, który zasila transmisję na żywo danych telemetrycznych. Ten kanał jest niezależny od kanału danych telemetrycznych regularnych i ten dokument nie ma do niego zastosowania.

## <a name="built-in-telemetry-channels"></a>Wbudowane kanały telemetryczne

SDK usługi Application Insights .NET i .NET Core są dostarczane z dwoma wbudowanymi kanałami:

* `InMemoryChannel`: Lekki kanał, który buforuje elementy w pamięci, dopóki nie są wysyłane. Elementy są buforowane w pamięci i opróżniane raz na 30 sekund lub gdy 500 elementów są buforowane. Ten kanał oferuje minimalne gwarancje niezawodności, ponieważ nie ponowi próbę wysłania danych telemetrycznych po awarii. Ten kanał również nie przechowuje elementów na dysku, więc wszelkie niewysłane elementy są tracone trwale po zamknięciu aplikacji (wdzięku lub nie). Ten kanał implementuje `Flush()` metodę, która może służyć do wymuszenia opróżnienia wszelkich elementów telemetrii w pamięci synchronicznie. Ten kanał doskonale nadaje się do zastosowań krótkometrażowych, w których idealnie nadaje się kolor synchroniczne.

    Ten kanał jest częścią większego pakietu Microsoft.ApplicationInsights NuGet i jest domyślnym kanałem używanym przez sdk, gdy nic innego nie jest skonfigurowane.

* `ServerTelemetryChannel`: Bardziej zaawansowany kanał, który ma zasady ponawiania prób i możliwość przechowywania danych na dysku lokalnym. Ten kanał ponawia ponowną próbę wysłania danych telemetrycznych, jeśli wystąpią błędy przejściowe. Ten kanał używa również magazynu na dysku lokalnym do przechowywania elementów na dysku podczas awarii sieci lub dużych woluminów telemetrycznych. Z powodu tych mechanizmów ponawiania prób i magazynu dysków lokalnych ten kanał jest uważany za bardziej niezawodny i jest zalecany dla wszystkich scenariuszy produkcyjnych. Ten kanał jest domyślnym dla [aplikacji ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) i [ASP.NET Core,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) które są skonfigurowane zgodnie z oficjalną dokumentacją. Ten kanał jest zoptymalizowany pod kątem scenariuszy serwera z długotrwałymi procesami. Metoda [`Flush()`](#which-channel-should-i-use) zaimplementowana przez ten kanał nie jest synchroniczne.

    Ten kanał jest dostarczany jako pakiet Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet i jest uzyskiwane automatycznie podczas korzystania z pakietu Microsoft.ApplicationInsights.Web lub Microsoft.ApplicationInsights.AspNetCore NuGet Pakiet.

## <a name="configure-a-telemetry-channel"></a>Konfigurowanie kanału telemetryczne

Kanał telemetrii można skonfigurować, ustawiając go na aktywną konfigurację telemetrii. W przypadku ASP.NET aplikacji konfiguracja polega na ustawieniu `TelemetryConfiguration.Active`wystąpienia kanału `ApplicationInsights.config`telemetryki na lub przez modyfikację . W przypadku aplikacji ASP.NET Core konfiguracja obejmuje dodanie kanału do kontenera iniekcji zależności.

W poniższych sekcjach przedstawiono `StorageFolder` przykłady konfigurowania ustawienia kanału w różnych typach aplikacji. `StorageFolder`to tylko jedno z konfigurowalnych ustawień. Aby uzyskać pełną listę ustawień konfiguracji, zobacz [sekcję ustawień](telemetry-channels.md#configurable-settings-in-channels) w dalszej części tego artykułu.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguracja przy użyciu applicationinsights.config dla aplikacji ASP.NET

W poniższej sekcji z [applicationinsights.config](configuration-with-applicationinsights-config.md) `ServerTelemetryChannel` `StorageFolder` przedstawiono kanał skonfigurowany z ustawioną lokalizacją niestandardową:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguracja w kodzie dla aplikacji ASP.NET

Poniższy kod konfiguruje wystąpienie "ServerTelemetryChannel" z `StorageFolder` ustawioną lokalizacją niestandardową. Dodaj ten kod na początku aplikacji, `Application_Start()` zazwyczaj w metodzie w Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguracja w kodzie dla aplikacji ASP.NET Core

Zmodyfikuj `ConfigureServices` metodę klasy, `Startup.cs` jak pokazano poniżej:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Konfigurowanie kanału przy `TelemetryConfiguration.Active` użyciu nie jest zalecane dla aplikacji ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguracja w kodzie aplikacji konsoli .NET/.NET Core

W przypadku aplikacji konsoli kod jest taki sam zarówno dla platformy .NET, jak i .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Szczegóły operacyjne ServerTelemetryChannel

`ServerTelemetryChannel`przechowuje przychodzące elementy w buforze w pamięci. Elementy są serializowane, kompresowane i `Transmission` przechowywane w wystąpieniu raz na 30 sekund lub gdy 500 elementów zostało buforowanych. Pojedyncze `Transmission` wystąpienie zawiera maksymalnie 500 elementów i reprezentuje partię danych telemetrycznych, która jest wysyłana za pośrednictwem jednego wywołania HTTPS do usługi Application Insights.

Domyślnie można wysyłać maksymalnie `Transmission` 10 wystąpień równolegle. Jeśli dane telemetryczne są nachybijające z większą szybkością `Transmission` lub jeśli sieć lub zaplecze usługi Application Insights jest powolny, wystąpienia są przechowywane w pamięci. Domyślna pojemność tego `Transmission` buforu w pamięci wynosi 5 MB. Po przekroczeniu pojemności w `Transmission` pamięci wystąpienia są przechowywane na dysku lokalnym do limitu 50 MB. `Transmission`wystąpienia są przechowywane na dysku lokalnym również wtedy, gdy występują problemy z siecią. Tylko te elementy, które są przechowywane na dysku lokalnym przetrwać awarii aplikacji. Są one wysyłane przy każdym ponownym uruchomieniu aplikacji.

## <a name="configurable-settings-in-channels"></a>Konfigurowalne ustawienia w kanałach

Aby uzyskać pełną listę konfigurowalnych ustawień dla każdego kanału, zobacz:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Oto najczęściej używane ustawienia dla: `ServerTelemetryChannel`

1. `MaxTransmissionBufferCapacity`: Maksymalna ilość pamięci w bajtach używana przez kanał do buforowania transmisji w pamięci. Po osiągnięciu tej pojemności nowe elementy są przechowywane bezpośrednio na dysku lokalnym. Wartość domyślna to 5 MB. Ustawienie wyższej wartości prowadzi do mniejszego użycia dysku, ale należy pamiętać, że elementy w pamięci zostaną utracone, jeśli aplikacja ulegnie awarii.

1. `MaxTransmissionSenderCapacity`: Maksymalna `Transmission` liczba wystąpień, które będą wysyłane do usługi Application Insights w tym samym czasie. Wartość domyślna to 10. To ustawienie można skonfigurować na większą liczbę, co jest zalecane podczas generowania ogromnej ilości danych telemetrycznych. Duża objętość zazwyczaj występuje podczas testowania obciążenia lub gdy próbkowanie jest wyłączone.

1. `StorageFolder`: Folder używany przez kanał do przechowywania elementów na dysku zgodnie z potrzebami. W systemie Windows %LOCALAPPDATA% lub %TEMP% jest używane, jeśli żadna inna ścieżka nie jest wyraźnie określona. W środowiskach innych niż Windows należy określić prawidłową lokalizację lub dane telemetryczne nie będą przechowywane na dysku lokalnym.

## <a name="which-channel-should-i-use"></a>Którego kanału należy użyć?

`ServerTelemetryChannel`jest zalecane dla większości scenariuszy produkcyjnych obejmujących długotrwałe aplikacje. Metoda `Flush()` zaimplementowana przez `ServerTelemetryChannel` nie jest synchroniczne, a także nie gwarantuje wysyłania wszystkich oczekujących elementów z pamięci lub dysku. Jeśli używasz tego kanału w scenariuszach, w których aplikacja ma zostać zamknięta, zalecamy wprowadzenie pewnego opóźnienia po wywołaniu `Flush()`. Dokładna ilość opóźnienia, które może wymagać nie jest przewidywalna. Zależy to od czynników, takich `Transmission` jak ile elementów lub wystąpień znajduje się w pamięci, ile znajduje się na dysku, ile jest przesyłanych do zaplecza i czy kanał znajduje się w środku wykładniczych scenariuszy wycofywania.

Jeśli chcesz wykonać kolor synchroniowy, zalecamy `InMemoryChannel`użycie programu .

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Czy kanał usługi Application Insights gwarantuje dostarczanie danych telemetrycznych? Jeśli nie, jakie są scenariusze, w których dane telemetryczne mogą zostać utracone?

Krótka odpowiedź jest to, że żaden z wbudowanych kanałów oferują gwarancję typu transakcji dostarczania telemetrii do zaplecza. `ServerTelemetryChannel`jest bardziej zaawansowany `InMemoryChannel` w porównaniu z niezawodnym dostarczaniem, ale również sprawia, że tylko próba najlepszego wysiłku wysyłania danych telemetrycznych. Telemetria nadal może zostać utracona w kilku sytuacjach, w tym w następujących typowych scenariuszach:

1. Elementy w pamięci zostaną utracone po awarii aplikacji.

1. Telemetria jest tracona podczas dłuższych okresów problemów z siecią. Dane telemetryczne są przechowywane na dysku lokalnym podczas awarii sieci lub gdy wystąpią problemy z zaplecza usługi Application Insights. Jednak przedmioty starsze niż 48 godzin są odrzucane.

1. Domyślne lokalizacje dysków do przechowywania danych telemetrycznych w systemie Windows to %LOCALAPPDATA% lub %TEMP%. Te lokalizacje są zazwyczaj lokalne dla komputera. Jeśli aplikacja migruje fizycznie z jednej lokalizacji do innej, wszelkie dane telemetryczne przechowywane w oryginalnej lokalizacji zostaną utracone.

1. W aplikacjach sieci Web w systemie Windows domyślną lokalizacją magazynu dysku jest D:\local\LocalAppData. Ta lokalizacja nie jest utrwalona. Jest wymazany w aplikacji ponownie uruchamia, skalowano w poziomie i innych takich operacji, co prowadzi do utraty wszelkich danych telemetrycznych przechowywanych tam. Można zastąpić domyślne i określić magazyn do utrwalonych lokalizacji, takich jak D:\home. Jednak takie utrwalone lokalizacje są obsługiwane przez magazyn zdalny i dlatego mogą być powolne.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Czy ServerTelemetryChannel działa na systemach innych niż Windows?

Mimo że nazwa jego pakietu i obszaru nazw zawiera "WindowsServer", ten kanał jest obsługiwany w systemach innych niż Windows, z następującym wyjątkiem. W systemach innych niż Windows kanał domyślnie nie tworzy folderu magazynu lokalnego. Należy utworzyć folder magazynu lokalnego i skonfigurować kanał, aby go używał. Po skonfigurowaniu magazynu lokalnego kanał działa w ten sam sposób we wszystkich systemach.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Czy SDK tworzy tymczasową pamięć lokalną? Czy dane są szyfrowane w magazynie?

SDK przechowuje elementy telemetrii w magazynie lokalnym podczas problemów z siecią lub podczas ograniczania przepustowości. Te dane nie są szyfrowane lokalnie.

W przypadku systemów Windows zestaw SDK automatycznie tworzy tymczasowy folder lokalny w katalogu %TEMP% lub %LOCALAPPDATA% i ogranicza dostęp tylko do administratorów i bieżącego użytkownika.

W przypadku systemów innych niż Windows żaden magazyn lokalny nie jest tworzony automatycznie przez zestaw SDK, więc żadne dane nie są domyślnie przechowywane lokalnie. Katalog magazynu można utworzyć samodzielnie i skonfigurować kanał do jego używania. W takim przypadku użytkownik jest odpowiedzialny za zapewnienie, że katalog jest zabezpieczony.
Dowiedz się więcej o [ochronie danych i prywatności](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK typu open source
Jak każdy SDK dla usługi Application Insights, kanały są open source. Przeczytaj i wnieś udział w kodzie lub zgłoś problemy w [oficjalnym repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md)
* [Rozwiązywanie problemów z systemem SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
