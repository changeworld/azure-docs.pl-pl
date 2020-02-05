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
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1f3d442ea7cf6d95427aaa9c072410119e2735af
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989811"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Szybki Start: jak utworzyć konto i przekazać swoje pierwsze wideo

W tym samouczku wprowadzającym pokazano, jak zalogować się w witrynie internetowej usługi Video Indexer i jak przekazać pierwszy plik wideo.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. Opcja with płatna umożliwia utworzenie konta Video Indexer, które jest [połączone z subskrypcją platformy Azure i kontem Azure Media Services](connect-to-azure.md). Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Tworzenie konta w usłudze Video Indexer

Aby rozpocząć tworzenie rozwiązań za pomocą usługi Video Indexer, przejdź do witryny internetowej usługi [Video Indexer](https://www.videoindexer.com) i utwórz konto.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Przekazywanie pliku wideo za pomocą witryny internetowej usługi Video Indexer

> [!NOTE]
> Nazwa filmu wideo nie może być dłuższa niż 80 znaków.

### <a name="supported-file-formats-for-video-indexer"></a>Obsługiwane formaty plików dla Video Indexer

Listę formatów plików, których można używać z Video Indexer, zawiera artykuł [dane wejściowe dotyczące formatów kontenerów i plików](../latest/media-encoder-standard-formats.md#input-containerfile-formats) .

### <a name="upload-a-video"></a>Przekazywanie wideo

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
2. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    ![Przekazywanie](./media/video-indexer-get-started/video-indexer-upload.png)

    Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    ![Przekazano](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z linkiem do tego pliku wideo i krótki opis tego, co znaleziono w tym nagraniu. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="see-also"></a>Zobacz także

Aby uzyskać więcej informacji [, zobacz Przekazywanie i indeksowanie filmów wideo](upload-index-videos.md) .

Po przekazaniu i poindeksowania wideo możesz zacząć korzystać z witryny sieci Web [Video Indexer](video-indexer-view-edit.md) lub [Video Indexer portalu dla deweloperów](video-indexer-use-apis.md) , aby zobaczyć szczegółowe informacje o filmie wideo. 

[Rozpocznij korzystanie z interfejsów API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Następne kroki

Szczegółowe wprowadzenie można znaleźć w naszym [laboratorium wprowadzającym](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Na końcu warsztatu będziesz mieć dobrą wiedzę o rodzaju informacji, które mogą zostać wyodrębnione z zawartości wideo i audio, ale bardziej przygotowasz się do identyfikowania możliwości związanych z analizą zawartości, wideo o skoku AI na platformie Azure i demonstracją kilka scenariusze dotyczące Video Indexer.

