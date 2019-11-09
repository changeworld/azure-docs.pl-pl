---
title: Znajdowanie dokładnego momentu w filmach wideo — Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak znaleźć dokładną chwilę w filmach wideo przy użyciu Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833792"
---
# <a name="find-exact-moments-within-videos"></a>Znajdowanie konkretnych momentów w wideo

W tym temacie przedstawiono opcje wyszukiwania umożliwiające znalezienie dokładnego momentu w filmach wideo.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Przeszukaj wszystkie filmy wideo na Twoim koncie.

    W poniższym przykładzie wyszukamy wszystkie filmy wideo, które mówią o zabezpieczeniach i w których Satya pojawia się.

    ![Wyszukiwanie](./media/video-indexer-search/video-indexer-search01.png)
3. Przeszukaj podsumowanie informacji o filmie wideo.

    Następnie możesz przeszukać w filmie wideo, klikając pozycję **Odtwórz** na filmie wideo. Następnie możesz wyszukać w filmie wideo, wybierając kartę **Wyszukiwanie** . 

    W poniższym przykładzie wyszukamy "Secure" w wybranym filmie wideo.

    ![Wyszukiwanie](./media/video-indexer-search/video-indexer-search02.png)

    Po kliknięciu jednego z wyników gracz zostanie pożądany do tego momentu w filmie wideo. Możesz uzyskać informacje o widoku odtwarzacza/analizy i synchronizacji w aplikacji. Aby uzyskać więcej informacji, zobacz [osadzanie Video Indexer elementów widget w aplikacji](video-indexer-embed-widgets.md). 
4. Przeszukaj szczegółowy podział wideo.
    
    Jeśli chcesz utworzyć własny klip na podstawie znalezionego filmu wideo, naciśnij przycisk **Edytuj** . Na tej stronie znajduje się film wideo wraz ze szczegółowymi informacjami na temat filtrów. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i edytowanie Video Indexer Insights](video-indexer-view-edit.md). 

    Możesz wyszukać w filmie wideo, aby wyświetlić tylko te, które Cię interesują, i użyć szczegółowych informacji do filtrowania części, które mają być wyświetlane. Po zakończeniu możesz wyświetlić podgląd klipu i nacisnąć przycisk **Publikuj** , aby utworzyć nowy klip, który pojawia się w galerii.
    
    W poniższym przykładzie przeszukano tekst "rzeczywistość mieszana". Zastosowano również dodatkowe filtry, jak pokazano na poniższym ekranie.
    
    ![Wyszukiwanie](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Następne kroki 

Po znalezieniu wideo, z którym chcesz współpracować, możesz kontynuować przetwarzanie wideo, zgodnie z opisem w jednym z następujących tematów: 

- [Korzystaj z szczegółowego wglądu w dane wideo](use-editor-create-project.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API usługi Video Indexer](video-indexer-use-apis.md)
- [Osadzanie widgetów wizualnych w aplikacji](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Zobacz też

[Omówienie usługi Video Indexer](video-indexer-overview.md)
