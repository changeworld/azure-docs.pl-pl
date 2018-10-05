---
title: 'Samouczek: tworzenie konta w usłudze Video Indexer i przekazywanie pierwszego pliku wideo'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć konto i przekazać pierwszy plik wideo za pomocą portalu usługi Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 89d15e83954f529705b3ac3c6f12e2a16a17479d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220024"
---
# <a name="tutorial-how-to-sign-up-and-upload-your-first-video"></a>Samouczek: jak utworzyć konto i przekazać pierwszy plik wideo

W tym samouczku wprowadzającym pokazano, jak zalogować się w witrynie internetowej usługi Video Indexer i jak przekazać pierwszy plik wideo.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku opcji płatnej utworzone zostaje konto usługi Video Indexer [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services](connect-to-azure.md). Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Tworzenie konta w usłudze Video Indexer

Aby rozpocząć tworzenie rozwiązań za pomocą usługi Video Indexer, przejdź do witryny internetowej usługi [Video Indexer](https://www.videoindexer.com) i utwórz konto.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Przekazywanie pliku wideo za pomocą witryny internetowej usługi Video Indexer

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
2. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    ![Upload](./media/video-indexer-get-started/video-indexer-upload.png)

    Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    ![Przekazano](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z linkiem do tego pliku wideo i krótki opis tego, co znaleziono w tym nagraniu. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="next-steps"></a>Następne kroki

Teraz możesz zapoznać się ze szczegółowymi informacjami dotyczącymi tego pliku wideo za pomocą witryny internetowej usługi [Video Indexer](video-indexer-view-edit.md) lub [portalu dla deweloperów usługi Video Indexer](video-indexer-use-apis.md). 

## <a name="see-also"></a>Zobacz też

[Omówienie usługi Video Indexer](video-indexer-overview.md)

[Rozpoczynanie korzystania z interfejsów API](video-indexer-use-apis.md).

