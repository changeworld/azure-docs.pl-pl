---
title: Wybieranie trybu rozpoznawania mowy Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Jak wybrać najlepszy tryb rozpoznawania w rozpoznawania mowy Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 21615b09a7b9599597706e38b55072cf80f1b69b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345037"
---
# <a name="bing-speech-recognition-modes"></a>Tryby rozpoznawania mowy Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Zamiany mowy na tekst interfejsów API Bing obsługi wielu trybów rozpoznawania mowy. Wybierz tryb, który daje najlepsze rezultaty uznania dla swojej aplikacji.

| Tryb | Opis |
|---|---|
| *Interaktywne* | Rozpoznawanie "Poleceń i kontroli" dla użytkownika interaktywnego zastosowaniach. Użytkownicy Mów krótkich fraz przeznaczony jako polecenia do aplikacji. |
| *Dyktowanie* | Ciągłe rozpoznawania dyktowanie scenariuszach. Użytkownicy Mów dłużej zdań, które są wyświetlane jako tekst. Użytkownicy przyjąć bardziej formalne stylu wypowiedzi. |
| *konwersacji* | Ciągłe doceniać przepisywania konwersacje między ludzi. Użytkownicy przyjmuje mniej formalny stylu wypowiedzi i może przełączać się między dłużej zdania i fraz krótszy.

> [!NOTE]
> Te tryby są stosowane, gdy używasz [interfejsów API REST](../GetStarted/GetStartedREST.md). [Biblioteki klienckie](../GetStarted/GetStartedClientLibraries.md) użyć innych parametrów, aby określić tryb rozpoznawania. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

Aby uzyskać więcej informacji, zobacz [tryby rozpoznawania](../concepts.md#recognition-modes) strony.
