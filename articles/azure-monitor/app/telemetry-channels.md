---
title: Kanały telemetrii w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak dostosować dane telemetryczne kanały, zestawy SDK usługi Azure Application Insights dla platformy .NET i .NET Core.
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
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561347"
---
# <a name="telemetry-channels-in-application-insights"></a>Kanały telemetrii w usłudze Application Insights

Kanały dane telemetryczne są integralną częścią [zestawów SDK usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Zarządzają, buforowanie i przesyłanie danych telemetrycznych do usługi Application Insights. Wersje .NET i .NET Core SDK ma dwa kanały wbudowanych telemetrii: `InMemoryChannel` i `ServerTelemetryChannel`. W tym artykule opisano każdego kanału szczegółowo, w tym sposobu dostosowywania zachowania kanału.

## <a name="what-are-telemetry-channels"></a>Co to są kanały telemetrię?

Kanały dane telemetryczne są odpowiedzialne za buforowania elementów telemetrii i wysyła je do usługi Application Insights, w którym są one przechowywane na wykonywanie zapytań i analizy. Kanał danych telemetrycznych jest każda klasa implementująca [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) interfejsu.

`Send(ITelemetry item)` Metoda kanału danych telemetrycznych jest wywoływana po wszystkich inicjatorów telemetrii i procesorów dane telemetryczne są wywoływane. Tak wszystkie elementy porzuconych przez podmiot przetwarzający dane telemetryczne nie będą dotrzeć do kanału. `Send()` Zazwyczaj nie natychmiast wysyła elementy do zaplecza. Zazwyczaj buforuje je w pamięci i wysyła je w partiach, wydajne transmisji.

[Live Stream metryki](live-stream.md) ma również niestandardowym kanale, który obsługuje przesyłanie strumieniowe na żywo dane telemetryczne. Ten kanał jest niezależna od kanału regularnych danych telemetrycznych, a w tym dokumencie nie ma zastosowania do niego.

## <a name="built-in-telemetry-channels"></a>Kanały wbudowanych telemetrii

Application Insights .NET oraz zestawów .NET Core SDK są dostarczane z dwóch wbudowanych kanałów:

* `InMemoryChannel`: Uproszczone kanału, który buforuje elementów w pamięci, dopóki nie są one wysyłane. Elementy są buforowane w pamięci i liczba opróżnionych co 30 sekund, lub gdy 500 elementów są buforowane. Ten kanał oferuje gwarancje niezawodności minimalny, ponieważ on nie ponów próbę wykonania wysyłanie danych telemetrycznych po awarii. Ten kanał również nie Zachowaj elementy na dysku, dzięki czemu wszystkie niewysłane zostaną utracone stałe od zamknięcia aplikacji (bezpieczne lub nie). Implementuje ten kanał `Flush()` metodę, która może służyć do force opróżnienie wszystkich elementów w pamięci danych telemetrycznych synchronicznie. Ten kanał jest odpowiednia dla aplikacji krótko działających gdzie synchroniczne opróżniania jest idealnym rozwiązaniem.

    Ten kanał jest częścią pakietu Microsoft.ApplicationInsights NuGet większych i domyślnym kanale, który zestaw SDK używa w przypadku skonfigurowania nic innego.

* `ServerTelemetryChannel`: Więcej informacji o zaawansowanych kanału, który ma zasady ponawiania prób i możliwość przechowywania danych na dysku lokalnym. Ten kanał ponawia próbę wysyłanie danych telemetrycznych, jeśli wystąpi błąd przejściowy. Ten kanał używa również magazyn na dysku lokalnym do zachowania elementów na dysku podczas awarii sieci lub woluminy wysokiej telemetrii. Ze względu na te mechanizmy ponawiania prób i magazyn na dysku lokalnym ten kanał jest uważany za bardziej niezawodna i jest zalecana dla wszystkich scenariuszy produkcyjnych. Ten kanał jest ustawieniem domyślnym dla [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) i [platformy ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikacji, które są skonfigurowane zgodnie z oficjalnej dokumentacji. Ten kanał jest zoptymalizowany pod kątem serwera scenariusze obejmujące procesy długotrwałe. [ `Flush()` ](#which-channel-should-i-use) Metodę, która jest implementowany przez ten kanał nie jest synchroniczne.

    Ten kanał jest dostarczany jako pakiet Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet i są uzyskiwane automatycznie podczas używania Microsoft.ApplicationInsights.Web lub Microsoft.ApplicationInsights.AspNetCore NuGet pakiet.

## <a name="configure-a-telemetry-channel"></a>Konfigurowanie kanału danych telemetrycznych

Skonfiguruj kanał danych telemetrycznych, ustawiając dla niej w konfiguracji aktywne dane telemetryczne. Dla aplikacji ASP.NET, konfiguracja obejmuje ustawienie wystąpienia kanału danych telemetrycznych `TelemetryConfiguration.Active`, lub modyfikując `ApplicationInsights.config`. W przypadku aplikacji platformy ASP.NET Core Konfiguracja obejmuje dodawanie kanał do kontenera iniekcji zależności.

W poniższych sekcjach przedstawiono przykłady konfiguracji `StorageFolder` ustawienie dla kanału w różne typy aplikacji. `StorageFolder` jest to tylko jeden konfigurowalnych ustawień. Aby uzyskać pełną listę ustawień konfiguracji, zobacz [w sekcji Ustawienia](telemetry-channels.md#configurable-settings-in-channels) w dalszej części tego artykułu.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurację za pomocą pliku ApplicationInsights.config dla aplikacji ASP.NET

W temacie następujące [plik ApplicationInsights.config](configuration-with-applicationinsights-config.md) pokazuje `ServerTelemetryChannel` skonfigurowano kanał `StorageFolder` zestawu do lokalizacji niestandardowej:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguracja w kodzie aplikacji ASP.NET

Poniższy kod ustawia z wystąpienia "ServerTelemetryChannel" `StorageFolder` zestawu do lokalizacji niestandardowej. Dodaj ten kod na początku aplikacji, zazwyczaj w `Application_Start()` metody w pliku Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguracja w kodzie aplikacji platformy ASP.NET Core

Modyfikowanie `ConfigureServices` metody `Startup.cs` klasy, jak pokazano poniżej:

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
> Konfigurowanie kanału za pomocą `TelemetryConfiguration.Active` nie jest zalecane w przypadku aplikacji platformy ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguracja w kodzie aplikacji konsoli.NET/.NET Core

W przypadku aplikacji konsoli kod jest taki sam, zarówno dla platformy .NET i .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Informacje o operacjach programu ServerTelemetryChannel

`ServerTelemetryChannel` Sklepy odebranych elementów w buforze w pamięci. Elementy są serializowane, skompresowania i zapisania w `Transmission` wystąpienia co 30 sekund, lub gdy audycja 500 elementów. Pojedynczy `Transmission` wystąpienie zawiera maksymalnie 500 elementów i reprezentuje partię telemetrii, że zostało wysłane za pośrednictwem jednego wywołania protokołu HTTPS do usługi Application Insights.

Domyślnie, maksymalnie 10 `Transmission` wystąpienia mogą być wysyłane równolegle. Czy telemetrii jest odebranych stawek szybciej, jeśli sieci lub usługi Application Insights z powrotem end jest powolne, `Transmission` wystąpienia są przechowywane w pamięci. Wydajność domyślna to w pamięci `Transmission` buforu to 5 MB. Po przekroczeniu pojemności w pamięci `Transmission` wystąpienia są przechowywane na dysku lokalnym do limitu 50 MB. `Transmission` wystąpienia są przechowywane na dysku lokalnym również w przypadku problemów z siecią. Tylko te elementy, które są przechowywane na dysku lokalnym przetrwać awarię aplikacji. Są one wysyłane zawsze, gdy aplikacja jest uruchamiana ponownie.

## <a name="configurable-settings-in-channels"></a>Można skonfigurować ustawienia w kanałach

Aby uzyskać pełną listę ustawień można skonfigurować dla poszczególnych kanałów zobacz:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

W tym miejscu są najczęściej używane ustawienia dla `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: Maksymalna ilość pamięci w bajtach, używanego przez kanał do buforu transmisji w trybie w pamięci. Po osiągnięciu tej pojemności nowe elementy są przechowywane bezpośrednio na dysku lokalnym. Wartość domyślna to 5 MB. Ustawienie wyższej wartości skutkuje to mniejszą użycie dysku, ale należy pamiętać, że elementy w pamięci zostaną utracone, jeśli wystąpiła awaria aplikacji.

1. `MaxTransmissionSenderCapacity`: Maksymalna liczba `Transmission` wystąpień, które będą wysyłane do usługi Application Insights w tym samym czasie. Wartość domyślna to 10. To ustawienie można skonfigurować do większej liczby, co jest zalecane, po wygenerowaniu bardzo dużą ilość danych telemetrycznych. Duża zazwyczaj występuje podczas testowania obciążenia, lub podczas pobierania próbek jest wyłączone.

1. `StorageFolder`: Folder, który jest używany przez kanał do przechowywania elementów na dysku, zgodnie z potrzebami. W Windows % TEMP % lub % LOCALAPPDATA % jest używany jeśli ścieżka nie jest jawnie określona. W środowiskach innych niż Windows należy określić prawidłową lokalizację lub dane telemetryczne nie zostanie on zapisany na dysku lokalnym.

## <a name="which-channel-should-i-use"></a>Kanał należy używać?

`ServerTelemetryChannel` jest zalecane w przypadku większości scenariuszy produkcyjnych, obejmujące długo działających aplikacji. `Flush()` Metody implementowane przez `ServerTelemetryChannel` nie jest synchroniczne, a także nie gwarantuje wysyła wszystkie oczekujące elementy z pamięci lub na dysku. Jeśli używasz tego kanału w scenariuszach gdy aplikacja zostanie zamknięta, zaleca się wprowadzenie pewne opóźnienie po wywołaniu `Flush()`. Dokładne zalecenia dotyczące ilości opóźnienie, które mogą wymagać nie jest przewidywalne. To zależy od takich czynników, takich jak liczbę elementów lub `Transmission` wystąpienia znajdują się w pamięci, ile znajdują się na dysku, ile są przesyłane do zaplecza i tego, czy kanał jest w trakcie scenariuszy wykładniczego wycofywania.

Jeśli konieczne opróżniania synchroniczne, firma Microsoft zaleca używanie `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Kanał usługi Application Insights, gwarantuje dostarczanie danych telemetrycznych? Jeśli nie, jakie są scenariusze, w których mogą zostać utracone dane telemetryczne?

Najkrócej mówiąc, że żadna z wbudowanych kanały gwarancją, że typ transakcji dostawy telemetrię do zaplecza. `ServerTelemetryChannel` to zaawansowane, w porównaniu z `InMemoryChannel` dla niezawodne dostarczanie, ale sprawia, że jedynie optymalnych próba wysyłanie danych telemetrycznych. Dane telemetryczne nadal mogą zostać utracone w wielu sytuacjach, w tym następujących typowych scenariuszach:

1. Elementy w pamięci zostaną utracone w przypadku, gdy wystąpiła awaria aplikacji.

1. Dane telemetryczne zostaną utracone podczas dłuższy problemy z siecią. Dane telemetryczne są przechowywane na dysku lokalnym podczas awarii sieci lub gdy wystąpić problemy z zapleczem usługi Application Insights. Jednak elementy starsze niż 24 godziny są odrzucane.

1. Domyślne lokalizacje dysku do przechowywania danych telemetrycznych w Windows to % LOCALAPPDATA % lub % TEMP %. Te lokalizacje są zazwyczaj lokalne maszyny. Jeśli aplikacja jest migrowana fizycznie z jednej lokalizacji do innego, żadnych danych telemetrycznych, przechowywane w oryginalnej lokalizacji zostaną utracone.

1. W aplikacji sieci Web na Windows domyślną lokalizację dysku przechowywania jest D:\local\LocalAppData. Ta lokalizacja nie jest trwały. Wyczyszczone ponownych uruchomień aplikacji, szczegółowymi skalowania i inne takie operacje, co prowadzi do utraty żadnych danych telemetrycznych tam przechowywane. Można zastąpić to ustawienie domyślne i określić magazyn do utrwalonego lokalizacji, takiej jak D:\home. Jednak takie utrwalonych lokalizacje są obsługiwane przez Magazyn zdalny i dlatego może działać powoli.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel działa w systemach innych niż Windows?

Mimo że nazwę pakietu i przestrzeń nazw obejmuje "WindowsServer", ten kanał jest obsługiwana w systemach innych niż Windows, z następującym wyjątkiem. W systemach innych niż Windows kanał nie tworzy folder lokalny magazyn domyślnie. Należy utworzyć folder lokalny magazyn i skonfigurować kanału z niego korzystać. Po skonfigurowaniu magazynu lokalnego kanał działa tak samo we wszystkich systemach.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Zestaw SDK tworzy tymczasowy magazyn lokalny? Czy dane są szyfrowane w magazynie?

Zestaw SDK są przechowywane dane telemetryczne elementy w magazynie lokalnym podczas problemy z siecią lub ograniczenia przepustowości. Te dane nie są szyfrowane lokalnie.

Dla systemów Windows zestaw SDK automatycznie tworzy tymczasowy folder lokalny w % TEMP % lub % LOCALAPPDATA % katalog i ogranicza dostęp do administratorów i tylko dla bieżącego użytkownika.

Dla systemów innych niż Windows bez magazynu lokalnego jest tworzony automatycznie przez zestaw SDK, a więc żadne dane nie są przechowywane lokalnie domyślnie. Można samodzielnie utworzyć katalog magazynu i skonfigurować kanał z niego korzystać. W tym przypadku jest odpowiedzialny za zapewnienie, że katalog jest zabezpieczony.
Przeczytaj więcej na temat [ochrony danych i prywatności](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
Podobnie jak każdy zestaw SDK usługi Application Insights kanały są typu open source. Odczytywanie i Współtworzenie kodu lub zgłaszanie problemów w [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Kolejne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md)
* [Rozwiązywanie problemów z zestawu SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
