---
title: 'Szybki Start: Konfiguracja C++ platformy zestawu Speech SDK (macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby skonfigurować platformę C++ na platformie MACOS z zestawem SDK usługi Speech Service.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 1b01a7df286af65d3363956706ad2703a339a255
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818521"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla programu C++ na macOS 10,13 i nowszych.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Wymagania systemowe

macOS 10,13 i nowsze

## <a name="install-speech-sdk"></a>Instalowanie zestawu Speech SDK

1. Wybierz katalog, do którego pliki zestawów Speech SDK powinny zostać wyodrębnione, i ustaw zmienną środowiskową `SPEECHSDK_ROOT`, aby wskazywała na ten katalog. Ta zmienna ułatwia odwoływanie się do katalogu w przyszłych poleceniach. Jeśli na przykład chcesz używać katalogu `speechsdk` w katalogu macierzystym, użyj polecenia podobnego do poniższego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Utwórz katalog, jeśli jeszcze nie istnieje.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i Wyodrębnij archiwum `.zip` zawierające strukturę zestawu Speech SDK:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Zweryfikuj zawartość katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Lista katalogów powinna zawierać pliki powiadomień i licencji innych firm, a także katalog `MicrosoftCognitiveServicesSpeech.framework`.

Teraz możesz przejść do [kolejnych kroków](#next-steps) poniżej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
