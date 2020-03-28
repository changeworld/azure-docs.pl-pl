---
title: 'Szybki start: tworzenie niestandardowego asystenta głosowego — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241143"
---
W tym przewodniku Szybki start użyjesz [SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) do utworzenia niestandardowej aplikacji asystenta głosowego, która łączy się z botem, który został już utworzony i skonfigurowany. Jeśli chcesz utworzyć bota, zobacz [powiązany samouczek,](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) aby uzyskać bardziej kompleksowy przewodnik.

Po spełnieniu kilku wymagań wstępnych podłączenie niestandardowego asystenta głosowego wymaga tylko kilku kroków:
> [!div class="checklist"]
> * Utwórz `BotFrameworkConfig` obiekt z klucza subskrypcji i regionu.
> * Utwórz `DialogServiceConnector` obiekt `BotFrameworkConfig` przy użyciu obiektu z góry.
> * Za `DialogServiceConnector` pomocą obiektu, rozpocząć proces nasłuchiwania dla pojedynczej wypowiedzi.
> * Sprawdź `ActivityReceivedEventArgs` zwrócony.
