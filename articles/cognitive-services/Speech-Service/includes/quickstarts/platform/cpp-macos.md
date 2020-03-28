---
title: 'Szybki start: konfiguracja platformy SDK C++ (macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do konfigurowania platformy dla języka C++ w systemie macOS za pomocą zestawu SDK usługi mowy.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469469"
---
W tym przewodniku pokazano, jak zainstalować [pakiet SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) dla języka C++ w systemie macOS 10.13 lub nowszym.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Wymagania systemowe

macOS 10.13 i powyżej

## <a name="install-speech-sdk"></a>Instalowanie zestawu Speech SDK

1. Wybierz katalog, do którego pliki zestawów Speech SDK powinny zostać wyodrębnione, i ustaw zmienną środowiskową `SPEECHSDK_ROOT`, aby wskazywała na ten katalog. Ta zmienna ułatwia odwoływanie się do katalogu w przyszłych poleceniach. Jeśli na przykład chcesz używać katalogu `speechsdk` w katalogu macierzystym, użyj polecenia podobnego do poniższego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Utwórz katalog, jeśli jeszcze nie istnieje.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i `.zip` wyodrębnij archiwum zawierające strukturę SDK mowy:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Zweryfikuj zawartość katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Lista katalogów powinna zawierać informacje i pliki licencji innych `MicrosoftCognitiveServicesSpeech.framework` firm, a także katalog.

Teraz możesz przejść do [następnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
