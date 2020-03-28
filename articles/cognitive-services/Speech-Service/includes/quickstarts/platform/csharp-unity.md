---
title: 'Szybki start: SDK mowy dla konfiguracji platformy Unity języka C# — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do konfigurowania platformy dla języka C# Unity z zestawu SDK usługi mowy.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469420"
---
W tym przewodniku pokazano, jak zainstalować [pakiet SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) dla [unity](https://unity3d.com/).

> [!NOTE]
> Zestaw SDK mowy dla unity obsługuje komputery Windows Desktop (x86 i x64) lub uniwersalną platformę windows (x86, x64, ARM/ARM64), android (x86, ARM32/64) i iOS (symulator x64, ARM32 i ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

- [Unity 2018.3 lub nowsze](https://store.unity.com/) z [Unity 2019.1 dodając obsługę PLATFORMY UNIWERSALNEJ SYSTEMU UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Wersja 15.9 lub nowsza programu Visual Studio 2017 jest również dopuszczalna.
- W przypadku obsługi systemu Windows ARM64 zainstaluj [opcjonalne narzędzia kompilacji dla arm64 i zestaw Windows 10 SDK dla ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Aby zainstalować pakiet SDK mowy dla unity, wykonaj następujące kroki:

1. Pobierz i otwórz [zestawu SDK mowy dla unity](https://aka.ms/csspeech/unitypackage), który jest pakowany jako pakiet zasobów Unity (.unitypackage) i powinien być już skojarzony z Unity. Po otwarciu pakietu zasobów zostanie wyświetlone okno dialogowe **Importowanie pakietu unity.** Może być konieczne utworzenie i otwarcie pustego projektu dla tego kroku do pracy.

   [![Okno dialogowe Importowanie pakietu Unity w Edytorze Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Upewnij się, że wszystkie pliki są zaznaczone, a następnie wybierz pozycję **Importuj**. Po kilku chwilach pakiet zasobów Unity jest importowany do projektu.

Aby uzyskać więcej informacji na temat importowania pakietów zasobów do unity, zobacz [dokumentację Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Teraz możesz przejść do [następnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
