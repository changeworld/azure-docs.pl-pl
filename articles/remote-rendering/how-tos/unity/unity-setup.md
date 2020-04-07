---
title: Konfigurowanie renderowania zdalnego dla jedności
description: Jak zainicjować zdalne renderowanie platformy Azure w projekcie Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681144"
---
# <a name="set-up-remote-rendering-for-unity"></a>Konfigurowanie renderowania zdalnego dla jedności

Aby włączyć renderowania zdalnego platformy Azure (ARR) w unity, udostępniamy dedykowane metody, które zajmują się niektórych aspektów specyficznych dla unity.

## <a name="startup-and-shutdown"></a>Uruchamianie i zamykanie

Aby zainicjować renderowanie `RemoteManagerUnity`zdalne, użyj programu . Ta klasa wywołuje `RemoteManager` ogólne, ale już implementuje szczegóły specyficzne dla unity dla Ciebie. Na przykład Unity używa określonego układu współrzędnych. Podczas `RemoteManagerUnity.Initialize`wywoływania zostanie skonfigurowana właściwa konwencja. Wywołanie wymaga również podania kamery Unity, która powinna być używana do wyświetlania zdalnie renderowanych treści.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Aby zamknąć zdalne renderowanie, zadzwoń `RemoteManagerStatic.ShutdownRemoteRendering()`.

Po `AzureSession` utworzeniu i wybraniu jako podstawowej sesji renderowania `RemoteManagerUnity`musi ona być zarejestrowana w :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Pełny przykładowy kod

Poniższy kod przedstawia wszystkie kroki potrzebne do zainicjowania zdalnego renderowania platformy Azure w unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funkcje wygodne

### <a name="session-state-events"></a>Zdarzenia stanu sesji

`RemoteManagerUnity.OnSessionUpdate`emituje zdarzenia, gdy jego stan sesji zmienia się, zobacz dokumentację kodu, aby uzyskać szczegółowe informacje.

### <a name="arrserviceunity"></a>ARRServiceUnity (ARRServiceUnity)

`ARRServiceUnity`jest opcjonalnym komponentem usprawnianym konfiguracją i zarządzaniem sesjami. Zawiera opcje, aby automatycznie zatrzymać jego sesji, gdy aplikacja jest wyjście lub tryb odtwarzania jest zamykany w edytorze, a także automatycznie odnowić dzierżawę sesji w razie potrzeby. Buforuje dane, takie jak właściwości sesji `LastProperties` (zobacz jego zmienną) i udostępnia zdarzenia dla zmian stanu sesji i błędów sesji.

Nie może być więcej niż `ARRServiceUnity` jedno wystąpienie naraz. Jest przeznaczony do szybszego rozpoczęcia pracy przez wdrożenie niektórych typowych funkcji. Dla większej aplikacji może być lepiej zrobić te rzeczy samodzielnie, choć.

Na przykład jak skonfigurować `ARRServiceUnity` i używać zobacz [Samouczek: Konfigurowanie projektu Unity od podstaw](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Następne kroki

* [Instalowanie pakietu renderowania zdalnego dla unity](install-remote-rendering-unity-package.md)
* [Samouczek: Konfigurowanie projektu Unity od podstaw](../../tutorials/unity/project-setup.md)
