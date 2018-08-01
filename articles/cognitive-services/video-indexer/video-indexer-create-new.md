---
title: Indeksator wideo platformy Azure umożliwia tworzenie informacji szczegółowych wideo z istniejących wideo | Dokumentacja firmy Microsoft
description: W tym temacie dowiesz się, jak tworzyć i publikować oparte na inne pliki wideo informacjom o filmie wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390844"
---
# <a name="create-highlights-from-existing-videos"></a>Utwórz najważniejsze wydarzenia z istniejących wideo

W tym temacie dowiesz się, jak tworzyć i publikować oparte na inne pliki wideo informacjom o filmie wideo.

1. Zaloguj się do Twojej [Video Indexer](https://api-portal.videoindexer.ai/) konta.
2. Znajdź film wideo, z którego chcesz utworzyć informacjom o filmie wideo.
3. Naciśnij klawisz **Odtwórz**.

    Na stronie znajdują się szczegółowe informacje z podsumowaniem wideo. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Naciśnij klawisz **Edytuj** przycisku.

    Ta strona zawiera pełną podział filmu wideo. Podział jest dzielony na bloki. Bloki są tutaj aby ułatwić przechodzą przez dane. Na przykład blok może można podzielić na podstawie prelegentów, zmienić lub istnieje długa wstrzymania. Możesz utworzyć własne listy odtwarzania, która zawiera tylko wiersze, które chcesz. Aby wyświetlić tylko określonych części źródła wideo, można filtrować według tematów/słów kluczowych, tonacji, osoby mówiące. Można jedynie wyświetlać transkrypcji lub optyczne rozpoznawanie znaków w wideo.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Tworzenie listy odtwarzania.

    Aby dodać lub usunąć linie do i z listy odtwarzania, naciśnij klawisz **+** / **-**.

5. Podgląd listy odtwarzania.

    Po zakończeniu tworzenia listy odtwarzania, naciśnij klawisz **Podgląd**.
6. Opublikuj listę odtwarzania.

    Po przejrzeniu listy odtwarzania, możesz opublikować go.

    Po opublikowaniu listy odtwarzania jest ona dodana do listy informacjom o filmie wideo.


## <a name="next-steps"></a>Kolejne kroki 

Po utworzeniu nowej listy odtwarzania, możesz kontynuować przetwarzanie, zgodnie z opisem w jednym z tych tematów: 

- [Przetwarzanie zawartości przy użyciu interfejsu REST API indeksatora wideo](video-indexer-use-apis.md)
- [Osadzanie widżetów visual w aplikacji](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md) 
