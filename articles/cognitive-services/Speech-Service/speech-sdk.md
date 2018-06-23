---
title: O mowy kognitywnych usługi SDK | Dokumentacja firmy Microsoft
description: Przegląd zestawów SDK dostępne dla usługi mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349716"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>O mowy kognitywnych usługi SDK

Kognitywnych usług mowy Software Development Kit (SDK) umożliwia aplikacji natywnych dostęp do funkcji usługi mowy ułatwiając opracowywania oprogramowania. Obecnie, zestaw SDK udostępnia **mowy na tekst**, **tłumaczenia mowy**, i **rozpoznawania zamiar**.

Tabela zawiera listę aktualnie obsługiwanych języków programowania i systemów operacyjnych.

|Obsługiwany system operacyjny|Język programowania|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Urządzenia|Java\*|

\* *Zestaw SDK Java jest częścią [mowy urządzeń SDK](speech-devices-sdk.md).*

## <a name="get-the-windows-sdk"></a>Zestaw SDK systemu Windows

Wersja Windows SDK mowy zawiera 32-bitowe i 64-bitowych bibliotek klienckich C/C++, a także bibliotek zarządzanych (.NET) do użytku w języku C#. Można zainstalować zestawu SDK programu Visual Studio przy użyciu narzędzia NuGet; po prostu wyszukaj `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Pobierz zestaw SDK systemu Linux

Upewnij się, że masz wymagane kompilator i biblioteki, uruchamiając następujące polecenia powłoki:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> W instrukcjach przyjęto założenie, że używasz Ubuntu 16.04 na komputerze (x86 lub x64). Na innej wersji Ubuntu lub różnych dystrybucji systemu Linux dostosować te kroki dla danego środowiska.

Następnie [Pobierz zestaw SDK](https://aka.ms/csspeech/linuxbinary) i Rozpakuj pliki w wybranym katalogu. W poniższej tabeli zamieszczono struktury folderu zestawu SDK.

|Ścieżka|Opis|
|-|-|
|`license.md`|Licencja|
|`third-party-notices.md`|Uwagi dotyczące innych firm|
|`include`|Pliki nagłówkowe dla C i C++|
|`lib/x64`|X64 natywnej biblioteki do łączenia się z aplikacją|
|`lib/x86`|X86 natywnej biblioteki do łączenia się z aplikacją|

Aby utworzyć aplikację, skopiować lub przenieść wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego i uwzględnić je zgodnie z wymaganiami w procesie kompilacji.

## <a name="get-the-java-sdk"></a>Pobierz Java SDK

Zestaw SDK Java jest częścią [mowy urządzeń SDK](speech-devices-sdk.md).

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
