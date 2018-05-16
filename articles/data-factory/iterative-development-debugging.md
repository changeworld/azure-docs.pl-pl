---
title: Iteracyjne projektowanie i debugowania w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie i debugowanie potoków fabryki danych wielokrotnie powtarzane w portalu Azure.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: e403afa5c870b2d007a8c5e1d46162cd899ead29
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteracyjne projektowanie i debugowanie z fabryką danych Azure

Fabryka danych Azure umożliwia wielokrotnie powtarzane opracowywania i debugowania potoki fabryki danych.

Wprowadzenie ośmiu minutę i demonstracji tej funkcji Obejrzyj następujące:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Funkcje iteracyjne debugowania
Tworzenie potoków i testowanie działa przy użyciu **debugowania** możliwości na kanwie potoku bez pisania pojedynczy wiersz kodu.

![Debugowanie możliwości na kanwie potoku](media/iterative-development-debugging/iterative-development-image1.png)

Wyświetl wyniki testu, który jest uruchamiany w **dane wyjściowe** okna kanwy potoku.

![Okno dane wyjściowe procesu roboczego](media/iterative-development-debugging/iterative-development-image2.png)

Po uruchomieniu testu zakończy się powodzeniem, Dodaj więcej działań do potoku sieci i Kontynuuj debugowanie w sposób iteracji. Możesz również **anulować** testu, gdy jest w toku.

![Anuluj uruchomienia testu](media/iterative-development-debugging/iterative-development-image3.png)

W przypadku uruchomień testów, nie trzeba Opublikuj zmiany z fabryką danych, przed wybraniem **debugowania**. Jest to przydatne w scenariuszach, w którym ma zostać upewnij się, że zmiany działać zgodnie z oczekiwaniami przed zaktualizowaniem przepływu pracy z fabryki danych.

## <a name="more-info-about-debugging"></a>Więcej informacji o debugowaniu

1. Test jest uruchamiany inicjowane z **debugowania** możliwości nie są dostępne na liście **Monitor** kartę. Można tylko Zobacz uruchamia wyzwalanych z **wyzwalacza teraz**, **harmonogram**, lub **okno wirowania** wyzwala w **Monitor** kartę. Zostanie wyświetlony ostatniego uruchomienia inicjowane z testu **debugowania** możliwości w **dane wyjściowe** okna kanwy potoku.

2. Wybieranie **debugowania** działa potoku. Tak na przykład, jeśli potok zawiera działanie kopiowania, uruchomienie testu kopiuje dane ze źródła do miejsca docelowego. W związku z tym zaleca się używać folderów testu w działaniach kopiowania i innych działań, podczas debugowania. Po potoku udało debugowania, przełącz się do rzeczywistego foldery, w których chcesz użyć podczas wykonywania normalnych operacji.

## <a name="setting-breakpoints-for-debugging"></a>Ustawianie punktów przerwania dla debugowania

Fabryka danych umożliwia także debugowania aż do danego działania na kanwie potoku. Po prostu umieść punkt przerwania działania, do której chcesz przetestować, a następnie wybierz **debugowania**. Fabryka danych zapewnia, że test działa tylko do punktu przerwania działania, w obszarze roboczym potoku. To *debugowania do momentu* funkcja jest przydatna, jeśli nie chcesz przetestować całą potoku, ale tylko podzestaw działania w potoku.

![Punkty przerwania w obszarze roboczym potoku](media/iterative-development-debugging/iterative-development-image4.png)

Aby ustawić punkt przerwania, wybierz element na kanwie potoku. A *debugowania do momentu* opcja jest wyświetlana jako pusta czerwone kółko w prawym górnym rogu elementu.

![Przed skonfigurowaniem punktu przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-image5.png)

Po wybraniu *debugowania do momentu* opcji zmienia się czerwone koło wskazująca punkt przerwania jest włączona.

![Po ustawieniu punkt przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Kolejne kroki
[Ciągłej integracji i wdrażania w fabryce danych Azure](continuous-integration-deployment.md)
