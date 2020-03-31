---
title: Tworzenie konta w usłudze Video Indexer i przekazywanie pierwszego pliku wideo — Azure
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto i przekazać pierwszy plik wideo za pomocą portalu usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: 22b06ce99333750d48d1cb65d9f60779572723b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499630"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Szybki start: jak zarejestrować się i przesłać pierwszy film

Ten szybki start z wprowadzeniem pokazuje, jak zalogować się w witrynie indeksatora wideo i jak przesłać pierwszy film.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. Za pomocą opcji płatnej można utworzyć konto indeksatora wideo, które jest [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services.](connect-to-azure.md) Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Tworzenie konta w usłudze Video Indexer

Aby rozpocząć tworzenie rozwiązań za pomocą usługi Video Indexer, przejdź do witryny internetowej usługi [Video Indexer](https://www.videoindexer.com) i utwórz konto.

> [!NOTE]
> Po rozpoczęciu korzystania z video indexer, wszystkie przechowywane dane i przekazanej zawartości są szyfrowane w spoczynku za pomocą klucza zarządzanego przez firmę Microsoft.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Przekazywanie pliku wideo za pomocą witryny internetowej usługi Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Obsługiwane formaty plików dla indeksatora wideo

Zobacz artykuł [o formatach kontenera/pliku wejściowego,](../latest/media-encoder-standard-formats.md#input-containerfile-formats) aby uzyskać listę formatów plików, których można używać za pomocą indeksatora wideo.

### <a name="upload-a-video"></a>Przekazywanie wideo

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
2. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    > [!NOTE]
    > Nazwa filmu nie może być większa niż 80 znaków.

    ![Upload](./media/video-indexer-get-started/video-indexer-upload.png)

    Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    ![Przekazano](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z linkiem do tego pliku wideo i krótki opis tego, co znaleziono w tym nagraniu. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="see-also"></a>Zobacz też

Zobacz [Przesyłanie i indeks wideo,](upload-index-videos.md) aby uzyskać więcej informacji.

Po przesłaniu i zindeksji wideo możesz zacząć korzystać z [witryny video Indexer](video-indexer-view-edit.md) lub [portalu dewelopera indeksatora](video-indexer-use-apis.md) wideo, aby zobaczyć szczegółowe informacje o filmie. 

[Rozpoczynanie korzystania z interfejsów API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Następne kroki

Szczegółowe informacje na temat wstępu można znaleźć w naszym [laboratorium wprowadzającym.](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) 

Na koniec warsztatów będziesz miał dobre zrozumienie rodzaju informacji, które mogą być wyodrębnione z treści wideo i audio, będziesz bardziej przygotowany do identyfikacji możliwości związanych z inteligencją treści, pitch video AI na platformie Azure i demo kilka scenariuszy na indeksatorze wideo.

