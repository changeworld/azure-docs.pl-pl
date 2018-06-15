---
title: Użyj indeksatora wideo Azure do tworzenia analiz wideo z istniejących klipy wideo | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób tworzenia i publikowania wideo insights oparte na inne pliki wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 1834fa9f1dd7db618850ab897a85a1bcfdaa89ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349872"
---
# <a name="create-video-insights-from-existing-videos"></a>Tworzenia analiz wideo z istniejących klipy wideo

W tym temacie przedstawiono sposób tworzenia i publikowania wideo insights oparte na inne pliki wideo.

1. Zaloguj się do Twojego [indeksatora wideo](https://api-portal.videoindexer.ai/) konta.
2. Znalezienie pliku wideo, z którego chcesz utworzyć wideo szczegółowe dane.
3. Naciśnij klawisz **odtwarzanie**.

    Na stronie znajdują się szczegółowe informacje podsumowujące wideo. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Naciśnij klawisz **Edytuj** przycisku.

    Ta strona przedstawia podział pełne wideo. Podział jest dzielony na bloki. Bloki są tutaj aby ułatwić przejść przez dane. Na przykład bloku może można podzielić na podstawie podczas zmiany głośniki lub istnieje długa wstrzymania. Możesz utworzyć własne listy odtwarzania, która zawiera tylko wiersze, które mają. Aby wyświetlić tylko określone fragmenty źródła wideo, można filtrować według tematów/słowa kluczowe, opinie, osoby, głośniki. Można tylko wyświetlić wykaz wideo lub Rozpoznawania.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Tworzenie listy odtwarzania.

    Aby dodać lub usunąć wiersze do/z listy odtwarzania, naciśnij klawisz **+** / **-**.

5. Wyświetl podgląd listy odtwarzania.

    Po zakończeniu tworzenia listy odtwarzania, naciśnij klawisz **Podgląd**.
6. Publikowanie listy odtwarzania.

    Po przejrzeniu listy odtwarzania można go opublikować.

    Po opublikowaniu listy odtwarzania, jest ona dodana do listy wideo szczegółowe dane.


## <a name="next-steps"></a>Kolejne kroki 

Po utworzeniu nowej listy odtwarzania, można kontynuować przetwarzania, zgodnie z opisem w jednym z poniższych tematów: 

- [Przetwarzanie zawartości za pomocą interfejsu API REST indeksatora wideo](video-indexer-use-apis.md)
- [Osadź visual elementy widget w aplikacji](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Zobacz także

[Film poglądowy dotyczący indeksatora](video-indexer-overview.md) 
