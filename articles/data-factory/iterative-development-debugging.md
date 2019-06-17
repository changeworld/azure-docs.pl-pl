---
title: Debugowanie w usłudze Azure Data Factory i programowanie iteracyjne | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i debugowania potoków usługi Data Factory iteracyjne w witrynie Azure portal.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: a8028fdde93d06f7b25bf9bd8b4ed5a560a35f83
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686311"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteracyjne projektowanie i debugowania przy użyciu usługi Azure Data Factory

Usługa Azure Data Factory umożliwia iteracyjne tworzenia i debugowania potoków usługi Data Factory.

Wprowadzenie minutę 8 i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iteracyjne funkcji debugowania
Tworzenie potoków i testowanie przebiegów przy użyciu **debugowania** możliwości na kanwie potoku, bez konieczności pisania nawet jednego wiersza kodu.

![Debugowanie funkcji na kanwie potoku](media/iterative-development-debugging/iterative-development-image1.png)

Wyświetl wyniki testu, który jest uruchamiany w **dane wyjściowe** okna na kanwie potoku.

![Okno danych wyjściowych na kanwie potoku](media/iterative-development-debugging/iterative-development-image2.png)

Po pomyślnym przebiegu testowego, Dodaj więcej działań do potoku i Kontynuuj debugowanie w sposób iteracyjny. Możesz również **anulować** testu, gdy jest w toku.

![Anuluj przebieg testu](media/iterative-development-debugging/iterative-development-image3.png)

Podczas przebiegów testów, nie trzeba opublikować zmiany w usłudze data factory, przed wybraniem **debugowania**. Ta funkcja jest przydatne w scenariuszach, w którym chcesz upewnić się, czy zmiany działają zgodnie z oczekiwaniami, przed uaktualnieniem przepływ fabryki danych.

> [!IMPORTANT]
> Wybieranie **debugowania** faktycznie uruchamia potok. Tak na przykład jeśli potok zawiera działanie kopiowania, przebieg testu kopiuje dane ze źródła do miejsca docelowego. Dlatego zaleca się używać foldery testowego w swoje działania kopiowania i innych działań, podczas debugowania. Po już debugowany potoku, przełącz się do rzeczywiste foldery, w których chcesz użyć w normalnych operacji.

## <a name="visualizing-debug-runs"></a>Uruchamia wizualizowania debugowania

Możesz wizualizować wszystkie przebiegi debugowania, które są w toku dla fabryki danych w jednym miejscu. Wybierz **uruchamia widoku debugowania** w prawym górnym rogu strony. Ta funkcja jest przydatne w scenariuszach, gdzie masz wzorca potoki uruchamiania debugowania uruchomień potoków podrzędnych, i chcesz, aby pojedynczy widok, aby wyświetlić uruchomienia aktywnego debugowania.

![Wybierz ikonę przebiegi debugowania aktywnego widoku](media/iterative-development-debugging/view-debug-runs-image1.png)

![Lista przykładów przebiegów aktywnego debugowania](media/iterative-development-debugging/view-debug-runs-image2.png)

## <a name="monitoring-debug-runs"></a>Monitorowanie debugowanie jest uruchamiane

Inicjowane z przebiegów testowych **debugowania** możliwości nie są dostępne na liście **Monitor** kartę. Możesz tylko Zobacz uruchamia wyzwalane za pomocą **Wyzwól teraz**, **harmonogram**, lub **okno wirowania** Wyzwalacze w **Monitor** kartę. Możesz zobaczyć ostatnie testu inicjowane za pomocą **debugowania** możliwości są dostępne w **dane wyjściowe** okna na kanwie potoku.

## <a name="setting-breakpoints-for-debugging"></a>Ustawianie punktów przerwania do debugowania

Fabryka danych umożliwia również debugowanie aż danego działania na kanwę potoku. Po prostu umieść punkt przerwania w działaniu, do której chcesz przetestować, a następnie wybierz pozycję **debugowania**. Data Factory zapewnia uruchamia test tylko do punktu przerwania działania na kanwę potoku. To *debugowania do czasu* funkcja jest przydatna, gdy nie chcesz przetestować cały potok, ale tylko podzbiór działań w potoku.

![Punkty przerwania na kanwie potoku](media/iterative-development-debugging/iterative-development-image4.png)

Aby ustawić punkt przerwania, wybierz element na kanwę potoku. A *debugowania do czasu* opcja jest wyświetlana jako pusta czerwone kółko w prawym górnym rogu elementu.

![Przed rozpoczęciem punkt przerwania na wybrany element](media/iterative-development-debugging/iterative-development-image5.png)

Po wybraniu *debugowania do czasu* opcji zmienia się na wypełnionego okręgu czerwony, aby wskazać punkt przerwania jest włączony.

![Po ustawieniu punktu przerwania dla wybranego elementu](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Kolejne kroki
[Ciągła integracja i wdrażanie w usłudze Azure Data Factory](continuous-integration-deployment.md)
