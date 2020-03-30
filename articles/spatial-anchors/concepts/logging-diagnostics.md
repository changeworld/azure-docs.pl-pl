---
title: Rejestrowanie i diagnostyka
description: Szczegółowe wyjaśnienie sposobu generowania i pobierania rejestrowania i diagnostyki w usłudze Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270125"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Rejestrowanie i diagnostyka w zakotwiczeniach przestrzennych platformy Azure

Zakotwiczenia przestrzenne platformy Azure zapewnia standardowy mechanizm rejestrowania, który jest przydatny do tworzenia aplikacji. Tryb rejestrowania zakotwiczenia przestrzennego jest przydatny, gdy potrzebujesz więcej informacji do debugowania. Rejestrowanie diagnostyki przechowuje obrazy środowiska.

## <a name="standard-logging"></a>Standardowe rejestrowanie
W interfejsie API zakotwiczenia przestrzennego można subskrybować mechanizm rejestrowania, aby uzyskać przydatne dzienniki do tworzenia aplikacji i debugowania. Standardowe interfejsy API rejestrowania nie przechowują obrazów środowiska na dysku urządzenia. SDK udostępnia te dzienniki jako wywołania zwrotne zdarzeń. To do Ciebie, aby zintegrować te dzienniki do mechanizmu rejestrowania aplikacji.

### <a name="configuration-of-log-messages"></a>Konfiguracja komunikatów dziennika
Istnieją dwa wywołania zwrotne interesujące dla użytkownika. W poniższym przykładzie pokazano, jak skonfigurować sesję.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Zdarzenia i właściwości

Te wywołania zwrotne zdarzeń są dostarczane do przetwarzania dzienników i błędów z sesji:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Określa poziom szczegółowości zdarzeń do odebrania ze środowiska wykonawczego.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Udostępnia standardowe zdarzenia dziennika debugowania.
- [Błąd:](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)Udostępnia zdarzenia dziennika, które środowisko wykonawcze uważa za błędy.

## <a name="diagnostics-logging"></a>Rejestrowanie diagnostyczne

Oprócz standardowego trybu pracy do rejestrowania, Przestrzenne kotwice ma również tryb diagnostyczny. Tryb diagnostyki przechwytuje obrazy środowiska i rejestruje je na dysku. Ten tryb służy do debugowania niektórych rodzajów problemów, takich jak niepowodzenie przewidywalnie zlokalizować kotwicę. Włącz rejestrowanie diagnostyki tylko odtworzyć określony problem. Następnie wyłącz go. Nie włączaj diagnostyki podczas normalnego uruchamiania aplikacji.

Podczas interakcji z pomocą techniczną z firmą Microsoft przedstawiciel firmy Microsoft może zapytać, czy chcesz przesłać pakiet diagnostyczny do dalszego badania. W takim przypadku można zdecydować się włączyć diagnostykę i odtworzyć problem, dzięki czemu można przesłać pakiet diagnostyczny.

Jeśli użytkownik prześle dziennik diagnostyki do firmy Microsoft bez uprzedniego potwierdzenia ze strony przedstawiciela firmy Microsoft, przesłanie pozostanie bez odpowiedzi.

W poniższych sekcjach pokazano, jak włączyć tryb diagnostyki, a także jak przesyłać dzienniki diagnostyczne do firmy Microsoft.

### <a name="enable-diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego

Po włączeniu sesji do rejestrowania diagnostyki, wszystkie operacje w sesji mają odpowiednie rejestrowanie diagnostyki w lokalnym systemie plików. Podczas rejestrowania obrazy środowiska są zapisywane na dysku.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Prześlij pakiet diagnostyki

Poniższy fragment kodu pokazuje, jak przesłać pakiet diagnostyki do firmy Microsoft. Ten pakiet będzie zawierać obrazy środowiska przechwycone przez sesję po włączeniu diagnostyki.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Części pakietu diagnostycznego
Pakiet diagnostyki może zawierać następujące informacje:

- **Obrazy klatek kluczowych:** obrazy środowiska przechwycone podczas sesji podczas diagnostyki zostały włączone.
- **Dzienniki**: Rejestrowanie zdarzeń zarejestrowanych przez środowisko wykonawcze.
- **Metadane sesji:** Metadane identyfikujące sesję.
