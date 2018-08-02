---
title: Użyj indeksatora wideo platformy Azure, aby znaleźć dokładnie momentów w pliku wideo | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak znaleźć dokładnie momentów w pliku wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 1cffa067d8028adab4dbcc82c529f77d980ce6be
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397547"
---
# <a name="find-exact-moments-within-videos"></a>Znajdowanie konkretnych momentów w wideo

Ten temat przedstawia opcje wyszukiwania, które umożliwiają znajdowanie konkretnych momentów w wideo pliku.

1. Zaloguj się do Twojej [Video Indexer](https://api-portal.videoindexer.ai/) konta.
2. Przeszukiwanie wszystkich filmów wideo w ramach Twojego konta.

    W poniższym przykładzie przeszukaliśmy dla wszystkich filmów wideo utworzone przez Channel9 ze Scottem Hanselmanem.

    ![Wyszukiwanie](./media/video-indexer-search/video-indexer-search01.png)
    
3. Wyszukaj podsumowane szczegółowe informacje dotyczące filmu wideo.

    Możesz wyszukiwać w filmie wideo, klikając **Odtwórz** film wideo. Następnie możesz przeszukiwać wideo, wybierając **wyszukiwania** kartę. Na przykład przeszukaliśmy dla wszystkich miejsc, w których jest używany tekst "identity protection". 

    ![Wyszukiwanie](./media/video-indexer-search/video-indexer-search02.png)

    Po kliknięciu jednego z wyników, gracz oferuje do tej pory w trakcie filmu wideo. W aplikacji, można osiągnąć player/szczegółowe dane i synchronizacja. Aby uzyskać więcej informacji, zobacz [osadzić Video Indexer widżetów w aplikacji](video-indexer-embed-widgets.md). 

    
4. Wyszukaj szczegółowy podział filmu wideo.

    Jeśli chcesz tworzyć własne podział oparty na film wideo, który można znaleźć, naciśnij **Edytuj** przycisku. Ta strona zawiera pełną podział filmu wideo. Możesz przeszukiwać podział do pokazania tylko wiersze, które Cię interesuje. Aby uzyskać więcej informacji, zobacz [wyświetlanie i edytowanie informacji szczegółowych indeksatora wideo](video-indexer-view-edit.md).

    W tym przykładzie przeszukaliśmy tekst "identity protection". Możemy również stosowane dodatkowe filtry, jak pokazano na poniższym ekranie.

    ![Wyszukiwanie](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Kolejne kroki 

Po znalezieniu wideo, który chcesz pracować, możesz kontynuować przetwarzanie filmu wideo, zgodnie z opisem w jednym z tych tematów: 

- [Tworzenie nowych informacji szczegółowych wideo, oparty na istniejących wideo](video-indexer-create-new.md)
- [Przetwarzanie zawartości przy użyciu interfejsu REST API indeksatora wideo](video-indexer-use-apis.md)
- [Osadzanie widżetów visual w aplikacji](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md)
