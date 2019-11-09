---
title: Tworzenie konta w usłudze Video Indexer i przekazywanie pierwszego pliku wideo — Azure
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto i przekazać pierwszy plik wideo za pomocą portalu usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: juliako
ms.openlocfilehash: 957acc25c3218069a20e90fe83e00e441b6303d6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839557"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Szybki Start: jak utworzyć konto i przekazać swoje pierwsze wideo

W tym samouczku wprowadzającym pokazano, jak zalogować się w witrynie internetowej usługi Video Indexer i jak przekazać pierwszy plik wideo.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. Opcja with płatna umożliwia utworzenie konta Video Indexer, które jest [połączone z subskrypcją platformy Azure i kontem Azure Media Services](connect-to-azure.md). Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Tworzenie konta w usłudze Video Indexer

Aby rozpocząć tworzenie rozwiązań za pomocą usługi Video Indexer, przejdź do witryny internetowej usługi [Video Indexer](https://www.videoindexer.com) i utwórz konto.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Przekazywanie pliku wideo za pomocą witryny internetowej usługi Video Indexer

> [!NOTE]
> Nazwa filmu wideo nie może być dłuższa niż 80 znaków.

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
2. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    ![Przekazywanie](./media/video-indexer-get-started/video-indexer-upload.png)

    Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    ![Przekazano](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z linkiem do tego pliku wideo i krótki opis tego, co znaleziono w tym nagraniu. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="next-steps"></a>Następne kroki

Teraz możesz zapoznać się ze szczegółowymi informacjami dotyczącymi tego pliku wideo za pomocą witryny internetowej usługi [Video Indexer](video-indexer-view-edit.md) lub [portalu dla deweloperów usługi Video Indexer](video-indexer-use-apis.md). 

## <a name="see-also"></a>Zobacz także

[Omówienie usługi Video Indexer](video-indexer-overview.md)

[Rozpoczynanie korzystania z interfejsów API](video-indexer-use-apis.md).

