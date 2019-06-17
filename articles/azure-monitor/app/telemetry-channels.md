---
title: Kanały telemetrii w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak dostosować dane telemetryczne kanały, zestawy SDK usługi Azure Application Insights dla.NET/.NET Core.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255802"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel w usłudze Application Insights

TelemetryChannel jest integralną częścią [zestawów SDK usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Zarządza buforowanie i przesyłanie danych telemetrycznych do usługi Application Insights. Wersje .NET i .NET Core SDK ma dwa wbudowane TelemetryChannels - `InMemoryChannel` i `ServerTelemetryChannel`. W tym artykule opisano każdego kanału szczegółowo, w tym, w jaki sposób dostosować zachowań użytkowników.

## <a name="what-is-a-telemetrychannel"></a>Co to jest TelemetryChannel?

`TelemetryChannel` jest odpowiedzialny za buforowania i wysyłania elementów telemetrii usługi Application Insights, gdzie są przechowywane na wykonywanie zapytań i analizy. Jest dowolnej klasy implementującej interfejs [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

`Send(ITelemetry item)` Po wszystkich zostanie wywołana metoda TelemetryChannel `TelemetryInitializer`s i `TelemetryProcessor`s są wywoływane. Oznacza to, że wszystkie elementy porzuconych przez `TelemetryProcessor` nie dotrzeć do kanału. `Send()` Zazwyczaj wysyłał elementy natychmiast do wewnętrznej bazy danych. Są zazwyczaj buforowany w pamięci i wysłane w partiach, wydajne transmisji.

[LiveMetrics](live-stream.md) ma również niestandardowym kanale, który obsługuje przesyłanie strumieniowe na żywo dane telemetryczne. Ten kanał jest niezależna od kanału regularnych danych telemetrycznych i ten dokument nie ma zastosowania do kanału, używane przez `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>Wbudowane TelemetryChannels

Zestaw SDK usługi Application Insights.NET/.NET Core jest dostarczany z dwóch wbudowanych kanałów:

* **InMemoryChannel** 
 `InMemoryChannel` to kanał lekki, który buforuje elementów w pamięci, zanim zostaną wysłane. Elementy są buforowane w pamięci i liczba opróżnionych co 30 sekund lub zawsze, gdy 500 elementów mają być buforowane. Ten kanał oferuje gwarancje niezawodności minimalne jako nie ponów próbę wykonania wysyłanie danych telemetrycznych po awarii. Ten kanał nie zachowywanie elementów na dysku, tak aby wszystkie niewysłane utracone stałe od zamknięcia aplikacji (bez problemu zmieniała lub nie). Brak `Flush()` metody implementowane przez ten kanał, który może służyć do force opróżnienie wszystkich elementów w pamięci danych telemetrycznych synchronicznie. Jest to odpowiednia dla krótko działających aplikacji, gdzie synchroniczne opróżniania jest idealnym rozwiązaniem.

    Ten kanał jest dostarczany jako część `Microsoft.ApplicationInsights` sam pakiet nuget i jest domyślnym kanale, zestaw SDK używa w przypadku skonfigurowania nic innego.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` jest bardziej zaawansowanych kanał, który ma zasady ponawiania prób i możliwość przechowywania danych na dysku lokalnym. Ten kanał ponawia próbę wysyłanie danych telemetrycznych, jeśli wystąpi błąd przejściowy. Ten kanał używa również magazyn na dysku lokalnym do zachowania elementów na dysku podczas awarii sieci lub woluminy wysokiej telemetrii. Ze względu na te mechanizmy ponawiania prób i magazyn na dysku lokalnym ten kanał jest uważany za bardziej niezawodna i jest zalecana dla wszystkich scenariuszy produkcyjnych. Ten kanał jest ustawieniem domyślnym dla [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) i [platformy ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikacji, które są skonfigurowane zgodnie z połączonych oficjalna dokumentacja. Ten kanał jest zoptymalizowany pod kątem scenariuszy serwerowych długo działających procesów. [ `Flush()` ](#which-channel-should-i-use) Metody implementowane przez ten kanał nie jest synchroniczne.

    Ten kanał jest dostarczany jako pakiet NuGet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`i zostanie przełączony w tryb automatycznie przy użyciu jednej z pakietów NuGet `Microsoft.ApplicationInsights.Web` lub `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Konfigurowanie TelemetryChannel

Kanał danych telemetrycznych można skonfigurować, ustawiając żądany `TelemetryChannel` w aktywnej `TelemetryConfiguration`. Dla aplikacji Asp.Net, konfiguracja obejmuje ustawienia `TelemetryChannel` na `TelemetryConfiguration.Active`, lub modyfikowania `ApplicationInsights.config`. W przypadku aplikacji platformy ASP.NET Core Konfiguracja obejmuje dodawanie żądanego kanału do kontenera iniekcji zależności.

Poniżej przedstawiono przykład, gdzie użytkownik konfiguruje `StorageFolder` dla kanału. `StorageFolder` jest to tylko jeden konfigurowalnych ustawień. Pełną listę ustawień konfiguracji jest opisany [w sekcji Ustawienia](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguracji dla aplikacji platformy ASP.NET za pomocą pliku ApplicationInsights.Config

W temacie następujące [plik ApplicationInsights.config](configuration-with-applicationinsights-config.md) pokazuje ServerTelemetryChannel skonfigurowano `StorageFolder` zestawu do lokalizacji niestandardowej.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguracja w kodzie aplikacji ASP.NET

Poniższy kod ustawia ServerTelemetryChannel z `StorageFolder` zestawu do lokalizacji niestandardowej. Ten kod, powinny zostać dodane na początku aplikacji, zazwyczaj w metodę Application_Start() w `Global.aspx.cs`

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

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguracja w kodzie aplikacji programu ASP.NET Core

Modyfikowanie `ConfigureServices` metody `Startup.cs` klasy, jak pokazano poniżej.

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

> [!NOTE]
> Należy pamiętać, że konfigurowania kanału, za pomocą `TelemetryConfiguration.Active` nie jest zalecane w przypadku aplikacji platformy ASP.NET Core.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Konfigurowanie TelemetryChannel w kodzie dla aplikacji konsoli Core.NET/.NET

W przypadku aplikacji konsoli kod jest taki sam dla platformy .NET i .NET Core i znajdują się poniżej.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Informacje o operacjach programu ServerTelemetryChannel

`ServerTelemetryChannel` Buforuje nadchodzących elementów w buforze w pamięci. Jest serializowana, kompresowane i przechowywane w `Transmission` wystąpienia po co 30 sekund lub gdy 500 elementów są buforowane. Pojedynczy `Transmission` wystąpienie zawiera maksymalnie 500 elementów i reprezentuje partię danych telemetrycznych przesyłanych przez wywołanie jednej https do usługi Application Insights. Domyślnie, może zawierać maksymalnie 10 `Transmission`s są wysyłane równolegle. Jeśli telemetrii jest odebranych stawek szybszy lub sieci/Application Insights wewnętrznej bazy danych jest powolne, następnie `Transmission`s pobrać przechowywane w pamięci. Wydajność domyślna tego buforu transmisji w trybie w pamięci to 5 MB. Po przekracza pojemność w pamięci, `Transmission`s są przechowywane na dysku lokalnym dla maksymalnie 50 MB. `Transmission`s są przechowywane na dysku lokalnym, gdy istnieją również problemy z siecią. Tylko elementy przechowywane na dysku lokalnym przetrwać awarię aplikacji, którego są wysyłane zawsze, gdy aplikacja jest uruchamiana ponownie.

## <a name="configurable-settings-in-channel"></a>Można skonfigurować ustawienia kanału

Pełną listę można skonfigurować ustawienia dla każdego kanału znajdują się tutaj:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Najczęściej używane ustawienia `ServerTelemetryChannel` są wymienione poniżej:

1. `MaxTransmissionBufferCapacity` -Maksymalna ilość pamięci w bajtach, używanego przez kanał do buforu transmisji w trybie w pamięci. Po osiągnięciu tej pojemności, nowe elementy będą przechowywane bezpośrednio na dysku lokalnym. Wartość domyślna to 5 MB. Ustawienie wyższej wartości potencjalnych klientów na mniejsze użycie dysku, ale jest należy pamiętać, że elementy w pamięci zostaną utracone, jeśli wystąpiła awaria aplikacji.

2. `MaxTransmissionSenderCapacity` -Maksymalną ilość `Transmission`s, który zostanie wysłany do usługi Application Insights w tym samym czasie. Wartość domyślna wynosi 10, ale może być skonfigurowana do większej liczby. Jest to zalecane, po wygenerowaniu bardzo dużą ilość danych telemetrycznych, zazwyczaj podczas wykonywania testów obciążenia i/lub podczas pobierania próbek jest wyłączone.

3. `StorageFolder` -Folder używany przez kanał do przechowywania elementów na dysku, zgodnie z potrzebami. W Windows % Temp % lub % LocalAppData % jest używany, jeśli nic nie skonfigurowano jawnie. W środowiskach Windows inne niż użytkownik **musi** skonfigurować prawidłowej lokalizacji, bez których dane telemetryczne nie będą przechowywane na dysku lokalnym.

## <a name="which-channel-should-i-use"></a>Kanał należy używać?

`ServerTelemetryChannel` jest zalecane w przypadku większości scenariuszy produkcyjnych długo działających aplikacji. `Flush()` Implementację metody `ServerTelemetryChannel` nie jest synchroniczne, a `Flush()` nie gwarantuje wysyła wszystkie oczekujące elementy z dysku/pamięci. Jeśli ten kanał jest używany w scenariuszach, w którym aplikacja zostanie zamknięta, a następnie zaleca się wykonać pewne opóźnienie, po wywołaniu `Flush()` w tym kanale. Dokładne zalecenia dotyczące ilości opóźnienia wymaganego nie jest przewidywalne, ponieważ jest ona zależna od takich czynników, takich jak liczbę elementów lub `Transmissions` znajdują się w pamięci, ile jest na dysku, ile są przesyłane do kopię, a Jeśli kanał jest w trakcie scenariuszy wykładniczego wycofywania. Jeśli istnieje potrzeba flush synchroniczne, następnie `InMemoryChannel` jest zalecane.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Kanał dotycząca usługi Application Insights oferuje telemetrii gwarantowane dostarczanie i jakie są scenariusze, w którym mogą zostać utracone dane telemetryczne?*

* Krótka odpowiedź na to, że żadna z wbudowanych kanały oferuje gwarancję typu transakcji, o dostarczaniu telemetrię do zaplecza. Gdy `ServerTelemetryChannel` to zaawansowane, w porównaniu do `InMemoryChannel` telemetrii niezawodne dostarczanie, zapewnia także próba optymalnych wysyłanie danych telemetrycznych i dane telemetryczne nadal mogą zostać utracone w kilku scenariuszach. Typowe scenariusze, w przypadku utraty danych telemetrycznych, należą:

1. Elementów w pamięci zostaną utracone w przypadku, gdy wystąpiła awaria aplikacji.
1. Dane telemetryczne zostaje zapisany na dysku lokalnym podczas awarii sieci lub problemy z zapleczem usługi Application Insights. Jednak elementy starsze niż 24 godziny są odrzucane. Dlatego dane telemetryczne zostaną utracone podczas wydłużonym okresie, problemy z siecią.
1. Domyślne lokalizacje dysku do przechowywania danych telemetrycznych w Windows to % LocalAppData % lub % Temp %. Te lokalizacje są zazwyczaj lokalne maszyny. Jeśli aplikacja jest migrowana fizycznie z jednej lokalizacji do innego, żadnych danych telemetrycznych, przechowywane w tym miejscu zostaną utracone.
1. W usłudze Azure Web Apps (Windows) domyślna lokalizacja dysku jest "D:\local\LocalAppData". Ta lokalizacja nie jest trwały i zostaną wyczyszczone, ponowne uruchomienie aplikacji, Skaluj szczegółowymi informacjami dotyczącymi i tak dalej, co prowadzi do utraty danych telemetrycznych, przechowywane w tych lokalizacjach. Użytkownicy mogą przesłaniać magazynu z utrwalonego lokalizacją, taką jak "D:\home", ale te lokalizacje utrwalonych poniżej są obsługiwane przez Magazyn zdalny i może działać powoli.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel działa w systemach innych niż Windows?*

* Pomimo nazwę pakietu/przestrzeni nazw jest WindowsServer ten kanał jest obsługiwana w systemach innych niż Windows, z następującym wyjątkiem. W innych niż Windows kanał nie tworzy folder lokalny magazyn domyślnie. Użytkownikom należy utworzyć folder lokalny magazyn i skonfigurować kanału z niego korzystać. Po skonfigurowaniu magazynu lokalnego kanał działa tak samo w systemach innych niż Windows i Windows.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*Zestaw SDK tworzy tymczasowy magazyn lokalny? Czy dane są szyfrowane w magazynie?*

* Zestaw SDK są przechowywane dane telemetryczne elementy w magazynie lokalnym podczas problemy z siecią lub ograniczenia przepustowości. Te dane nie są szyfrowane lokalnie.
Dla systemów Windows zestaw SDK automatycznie tworzy tymczasowy folder lokalny w katalogu TEMP lub APPDATA i ogranicza dostęp do administratorów i tylko dla bieżącego użytkownika.
For Non-Windows bez magazynu lokalnego jest tworzony automatycznie przez zestaw SDK i dlatego żadne dane nie są przechowywane lokalnie domyślnie. Użytkownicy mogą również samodzielnie utworzyć katalog magazynu i skonfigurować kanał z niej korzystać. W takim przypadku użytkownik jest odpowiedzialny za zapewnienie, że ten katalog jest zabezpieczony.
Przeczytaj więcej na temat [ochrony danych i prywatności](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
Podobnie jak każdy zestawów SDK Application Insights kanały są również typu open source. Odczyt i przyczynić się do kodu lub zgłosić problemy w czasie [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md)
* [Rozwiązywanie problemów z zestawu SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
