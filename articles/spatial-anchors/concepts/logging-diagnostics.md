---
title: Rejestrowanie i Diagnostyka Azure przestrzenne kotwic | Dokumentacja firmy Microsoft
description: Szczegółowe informacje dotyczące generowania i pobierania, rejestrowania i diagnostyki w kotwic przestrzenne platformy Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: b66dc7d6ec9d11fe645587fe791824009231b7c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964745"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Rejestrowanie i Diagnostyka Azure przestrzenne kotwic

Kotwice przestrzenne platformy Azure zapewnia mechanizm standardowa rejestrowania, który jest przydatne w przypadku tworzenia aplikacji. Tryb rejestrowania diagnostyki kotwic przestrzennych jest przydatne, gdy potrzebujesz dodatkowych informacji do debugowania. Rejestrowanie diagnostyczne są przechowywane obrazy środowiska.

## <a name="standard-logging"></a>Standardowa rejestrowania
Przestrzenne interfejsu API kotwic mogą subskrybować mechanizmie rejestrowania, aby uzyskać przydatne dzienniki do tworzenia aplikacji i debugowania. Rejestrowanie standardowych interfejsów API obrazów środowiska nie należy przechowywać na dysku urządzenia. Zestaw SDK udostępnia te dzienniki jako zdarzenie wywołania zwrotne. To Ty możesz zintegrować te dzienniki na mechanizmie rejestrowania aplikacji.

### <a name="configuration-of-log-messages"></a>Konfiguracja komunikatów dziennika
Istnieją dwa wywołania zwrotne interesujące dla użytkownika. Poniższy przykład przedstawia sposób konfigurowania sesji.

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

Te wywołania zwrotne zdarzenia są dostarczane do przetwarzania dzienników i błędy z sesji:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Określa poziom szczegółowości zdarzeń otrzymać ze środowiska wykonawczego.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Dostępne są zdarzenia dziennika standardowego debugowania.
- [Błąd](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Dostępne są zdarzenia dziennika, które środowisko uruchomieniowe, które uzna za błędy.

## <a name="diagnostics-logging"></a>Rejestrowanie diagnostyczne

Oprócz Tryb standardowy operacji logowania kotwice przestrzenne ma trybu diagnostycznego. Tryb diagnostyki przechwytuje obrazów środowiska i rejestruje je na dysku. Ten tryb umożliwia debugowanie niektóre rodzaje problemów, takich jak awaria przewidywalny zlokalizować elementu zakotwiczenia. Włączanie diagnostyki rejestrowanie wyłącznie do odtworzenia konkretnego problemu. Następnie należy je wyłączyć. Nie włączaj diagnostyki, po uruchomieniu aplikacji normalnie.

Podczas interakcji pomocy technicznej firmy Microsoft przedstawiciela firmy Microsoft może poprosić, jeśli chcesz przesłać pakiet diagnostyki w celu bliższego zbadania problemu. W takim przypadku można zdecydować, Włącz diagnostykę i odtworzyć problem, dzięki czemu możesz przesłać pakietu diagnostycznego. 

Jeśli prześlesz dzienników diagnostycznych do firmy Microsoft bez uprzedniego potwierdzenia z przedstawicielem firmy Microsoft, przesyłania zaczną bez odpowiedzi.

W poniższych sekcjach opisano sposób włączania trybu diagnostycznego, a także jak przesłać dzienników diagnostycznych do firmy Microsoft.

### <a name="enable-diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego

Po włączeniu sesji diagnostyki rejestrowanie wszystkich operacji w sesji mają odpowiednie diagnostyki rejestrowanie w lokalnym systemie plików. Podczas rejestrowania, obrazy środowiska są zapisywane na dysku.

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

Poniższy fragment kodu przedstawia sposób przesyłania pakietu diagnostyczne do firmy Microsoft. Ten pakiet zawierają obrazy środowiska przechwycone przez sesję diagnostyki jest włączona. 

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

### <a name="parts-of-a-diagnostics-bundle"></a>Części pakietu diagnostyki
Pakiet Diagnostyka może zawierać następujące informacje:

- **Ramka kluczowa obrazów**: Obrazy środowiska przechwycone podczas sesji, gdy włączono diagnostyki.
- **Dzienniki**: Rejestruj zdarzenia rejestrowane przez środowisko uruchomieniowe.
- **Metadane sesji**: Metadane, który identyfikuje sesji.
