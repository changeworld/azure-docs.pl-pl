---
title: Rejestrowanie i Diagnostyka Azure przestrzenne kotwic | Dokumentacja firmy Microsoft
description: Szczegółowe informacje dotyczące generowania i pobierania, rejestrowania i diagnostyki w kotwic przestrzenne platformy Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: d9377e2b5b66a7d426373a8a85e4880dafeaeee6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753171"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Rejestrowanie i Diagnostyka Azure przestrzenne kotwic

Kotwice przestrzenne platformy Azure udostępnia mechanizm standardowa rejestrowania przydatne do tworzenia aplikacji. Ponadto jest tryb rejestrowania diagnostyki przydatny podczas nawet więcej informacji jest wymagane do debugowania. Rejestrowanie diagnostyczne obejmuje przechowywanie obrazów środowiska.

## <a name="standard-logging-in-azure-spatial-anchors"></a>Standardowa zalogowaniu kotwic przestrzenne platformy Azure
Przestrzenne kotwic interfejsu API usługi Azure zapewnia mechanizm rejestrowania, które aplikacje mogą subskrybować otrzymywanie przydatne dzienniki do opracowywania aplikacji i debugowanie. Rejestrowanie standardowych interfejsów API nie są zachowywane obrazów środowiska na dysku urządzenia. Zestaw SDK udostępnia te dzienniki jako zdarzenie wywołania zwrotne. To Ty możesz zintegrować te dzienniki na mechanizmie rejestrowania aplikacji.

### <a name="how-to-configure-the-log-messages"></a>Jak skonfigurować komunikaty dziennika
Istnieją dwa wywołania zwrotne interesujące dla użytkownika. W poniższym przykładzie widać sposób konfigurowania sesji.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events--properties"></a>Właściwości & zdarzenia

Wywołania zwrotne zdarzenia dostarczone do przetwarzania dzienników i błędy z sesji.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Określa poziom szczegółowości zdarzeń otrzymać ze środowiska wykonawczego.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): To wywołanie zwrotne zdarzenia dostępne są zdarzenia dziennika standardowego debugowania.
- [Błąd](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): To wywołanie zwrotne zdarzenia dostępne są zdarzenia dziennika traktowane jako błędy w czasie wykonywania.

## <a name="diagnostics-logging-in-azure-spatial-anchors"></a>Diagnostyka logowania kotwic przestrzenne platformy Azure

Oprócz standardowych trybu operacji dla rejestrowania omówione powyżej kotwice przestrzenne Azure ma trybu diagnostycznego, które deweloperzy mogą wyrazić zgodę. Diagnostyka przechwytuje obrazów środowiska i rejestruje je na dysku. Ten tryb jest przydatne w przypadku debugowanie niektórych rodzajów problemy, takie jak w przypadku, gdy nie są w stanie zlokalizować przewidywalny elementu zakotwiczenia. Tylko włączyć diagnostyki rejestrowanie do odtworzenia konkretnego problemu, a następnie ją wyłącz. Nie uruchamiaj swoje aplikacje zwykle z włączoną diagnostyką.

Podczas interakcji pomocy technicznej firmy Microsoft przedstawiciela firmy Microsoft może wystąpić, jeśli chcesz przesłać diagnostyki pakietu do firmy Microsoft w celu bliższego zbadania problemu. W takim przypadku użytkownik może zdecydować o Włącz diagnostykę, odtworzenia problemu i przesłać pakiet diagnostyczne do firmy Microsoft w celu bliższego zbadania problemu. Dzienniki diagnostyczne przesłanych do firmy Microsoft bez uprzedniego potwierdzenie przez przedstawiciela firmy Microsoft zaczną bez odpowiedzi.

Poniższe fragmenty kodu pokazano, jak włączyć trybu diagnostycznego i jak można przesyłać dzienniki diagnostyczne do firmy Microsoft.

### <a name="enabling-diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego

Podczas sesji jest włączony dla rejestrowania diagnostycznego, wszystkie operacje w sesji mają odpowiednie diagnostyki logowanie się w lokalnym systemie plików. Rejestrowanie powoduje umieszczenie zapisywanie obrazów środowiska na dysku.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // Opt-in to diagnostics logging of environment images.
    // If this is enabled, the diagnostics bundle will include images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submitting-the-diagnostic-bundle"></a>Przesyłanie pakietu diagnostycznego

Poniższy fragment kodu przedstawia sposób przesyłania pakietu diagnostyczne do firmy Microsoft. Uwaga: ten zawiera obrazy środowiska przechwycone przez sesję po włączeniu diagnostyki. Ponadto pakiety diagnostyki przesłanych do firmy Microsoft bez uprzedniego potwierdzenie przez przedstawiciela firmy Microsoft zaczną bez odpowiedzi.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest  to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="anatomy-of-the-diagnostics-bundle"></a>Anatomia pakietu diagnostyki
Poniższe informacje mogą być obecne w pakiecie diagnostyki:

- Ramka kluczowa obrazy - środowiska przechwycone podczas sesji, gdy włączono diagnostyki.
- Dzienniki — zdarzenia dziennika rejestrowane w czasie wykonywania.
- Metadane sesji — metadanych, który identyfikuje sesji.
