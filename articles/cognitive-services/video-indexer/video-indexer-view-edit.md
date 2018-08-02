---
title: Wyświetlanie i edytowanie informacji szczegółowych indeksatora wideo platformy Azure | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób wyświetlania i edytowania informacji szczegółowych indeksatora wideo.
services: cognitive services
documentationcenter: ''
author: juliako
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 797c09d72402cfc1ee2524e7792cc1310a53fb1e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399481"
---
# <a name="view-and-edit-video-indexer-insights"></a>Wyświetlanie i edytowanie informacji szczegółowych w usłudze Video Indexer

W tym temacie przedstawiono sposób wyświetlania i edytowania informacji szczegółowych indeksatora wideo, filmu wideo.

1. Zaloguj się do Twojej [Video Indexer](https://api-portal.videoindexer.ai/) konta.
2. Znajdź film wideo, z którego chcesz utworzyć szczegółowe dane usługa Video Indexer. Aby uzyskać więcej informacji, zobacz [znajdowanie konkretnych momentów w wideo pliku](video-indexer-search.md).
3. Naciśnij klawisz **Odtwórz**.

    Na stronie znajdują się szczegółowe informacje z podsumowaniem wideo. 

    ![Insights](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. Wyświetlanie podsumowania szczegółowe informacje dotyczące filmu wideo. 

    Informacje podsumowania Pokaż zagregowany widok danych: twarzy, słowa kluczowe, tonacji. Na przykład widoczne twarze osób i zakresy czasu, który każdej twarzy pojawia się w i procent czasu, w których jest on wyświetlany.

    Odtwarzacz i szczegółowe informacje są synchronizowane. Na przykład jeśli klikniesz słowo kluczowe lub wiersza transkrypcji, gracz oferuje do tej pory w trakcie filmu wideo. W aplikacji, można osiągnąć player/szczegółowe dane i synchronizacja. Aby uzyskać więcej informacji, zobacz [widżetów indeksatora osadzić platformy Azure w swojej aplikacji](video-indexer-embed-widgets.md). 

3. Edytowanie informacji szczegółowych indeksatora wideo.

    Naciśnij klawisze edycji pod filmem. Zostanie wyświetlona strona, pokazujący pełny podział filmu wideo. Podział jest dzielony na bloki. Bloki są tutaj aby ułatwić przechodzą przez dane. Na przykład blok może można podzielić na podstawie prelegentów, zmienić lub istnieje długa wstrzymania. Możesz utworzyć własne listy odtwarzania, która zawiera tylko wiersze, które chcesz. Aby wyświetlić tylko określonych części źródła wideo, można filtrować według tematów/słów kluczowych, tonacji, osoby mówiące. Można jedynie wyświetlać transkrypcji lub optyczne rozpoznawanie znaków w wideo.  

    ![Insights](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak utworzyć własne informacji szczegółowych indeksatora wideo, oparte na inne pliki wideo](video-indexer-create-new.md).

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md)

