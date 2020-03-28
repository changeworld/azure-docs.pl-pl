---
title: 'Szybki start: konfiguracja platformy SDK C++ (Linux) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do konfigurowania platformy dla języka C++ w systemie Linux za pomocą zestawu SDK usługi mowy.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383950"
---
W tym przewodniku pokazano, jak zainstalować [pakiet SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) dla systemu Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Wymagania systemowe

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Obsługiwane platformy Linux będą wymagały`libssl` zainstalowania niektórych bibliotek `libasound2` (do obsługi warstwy bezpiecznych gniazd i do obsługi dźwięku). Zapoznaj się z poniższą dystrybucją, aby uzyskać polecenia potrzebne do zainstalowania poprawnych wersji tych bibliotek.

   * W systemie Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Na Debianie 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * Na RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> Na RHEL/CentOS 8 postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="install-speech-sdk"></a>Instalowanie zestawu Speech SDK

Zestawu Speech SDK dla systemu Linux można używać do kompilowania aplikacji 64-bitowych i 32-bitowych. Wymagane biblioteki i pliki nagłówkowe można pobrać jako plik tar ze strony https://aka.ms/csspeech/linuxbinary.

Pobierz i zainstaluj zestaw SDK w następujący sposób:

1. Wybierz katalog, do którego pliki zestawów Speech SDK powinny zostać wyodrębnione, i ustaw zmienną środowiskową `SPEECHSDK_ROOT`, aby wskazywała na ten katalog. Ta zmienna ułatwia odwoływanie się do katalogu w przyszłych poleceniach. Jeśli na przykład chcesz używać katalogu `speechsdk` w katalogu macierzystym, użyj polecenia podobnego do poniższego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Utwórz katalog, jeśli jeszcze nie istnieje.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i wyodrębnij archiwum `.tar.gz` zawierające pliki binarne zestawu Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Zweryfikuj zawartość katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Wykaz katalogów powinien zawierać pliki licencji i powiadomienia innych firm, a także katalog `include` zawierający pliki nagłówkowe (`.h`) i katalog `lib` zawierający biblioteki.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Teraz możesz przejść do [następnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
