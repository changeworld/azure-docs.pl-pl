---
title: Najważniejsze wskazówki podczas wybierania Identyfikatora serii czasu w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Opis najlepszych rozwiązań, wybierając Identyfikatora serii czasu w usłudze Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: a7fd1ff4a0fe96724af0c43019afe53666ab81d7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856359"
---
# <a name="best-practices-when-choosing-a-time-series-id"></a>Najważniejsze wskazówki podczas wybierania Identyfikatora serii czasu

W tym dokumencie opisano klucza partycji usługi Azure Time Series Insights (wersja zapoznawcza), **identyfikator serii czasu**i najlepsze rozwiązania, aby wybrać jedno.

## <a name="choose-a-time-series-id"></a>Wybierz identyfikator serii czasu

Wybór **identyfikator serii czasu** przypomina wybór klucza partycji dla bazy danych. Dlatego jest to decyzja ważna, które powinny być podawane w czasie projektowania. Nie można zaktualizować istniejącego środowiska TSI (wersja zapoznawcza), aby użyć innego **identyfikator serii czasu**. Innymi słowy gdy środowisko jest tworzone za pomocą **identyfikator serii czasu**, nie można zmienić zasady, ponieważ jest właściwością niezmienne.

> [!IMPORTANT]
> **Identyfikator serii czasu** jest rozróżniana wielkość liter i niezmienne (nie można zmienić raz ustawionego).

Mając to na uwadze wybranie odpowiedniej **identyfikator serii czasu** ma kluczowe znaczenie. Należy wziąć pod uwagę następujące właściwości podczas wybierania **identyfikator serii czasu**:

> [!div class="checklist"]
> * Wybierz nazwę właściwości, która ma szeroki zakres wartości, a nawet wzorców dostępu. Jest najlepszym rozwiązaniem jest zapewnienie klucza partycji przy użyciu wielu różnych wartości (np. setek lub tysięcy). Dla wielu klientów będzie podobny do **identyfikator urządzenia**, **identyfikator czujnika**itp.  
> * Powinien on być unikatowy na poziomie liścia węzła usługi [modelu szeregów czasowych](./time-series-insights-update-tsm.md).
> * A **identyfikator serii czasu** ciąg znaków nazwy właściwości może być maksymalnie 128 znaków i **identyfikator serii czasu** wartości właściwości mogą być maksymalnie 1024 znaki.
> * Jeśli niektóre unikatowe **identyfikator serii czasu** brakuje wartości właściwości, będą one traktowane jako wartości null, które biorą udział w ograniczenie unikatowości.

Ponadto możesz wybrać maksymalnie **trzy** (3) właściwości klucza jako swojej **identyfikator serii czasu**.

  > [!NOTE]
  > Twoje **trzy** (3) właściwości klucza muszą być ciągami.

W poniższych scenariuszach opisano wybranie więcej niż jedną właściwość klucza jako swojej **identyfikator serii czasu**:  

* Scenariusz 1:

  * Masz flot starszych zasobów, w których każdy może mieć unikatowe klucze. 
  * Na przykład jeden floty jest unikatowo identyfikowana przez właściwość **deviceId** i inny unikatowy właściwości **objectId**.  W obu flot innych floty unikatowy właściwość nie jest obecny. W tym przykładzie należy wybrać dwa klucze, **deviceId**, i **objectId** jako klucze unikatowe. 
  * Możemy zaakceptować wartości null i braku obecność właściwości w ładunku zdarzenia, zostanie rozliczone jako `null` wartość.  Będzie to również odpowiedni sposób obsługi wysyłania danych do dwóch źródeł zdarzeń, gdzie dane w każdym źródła zdarzeń ma inną unikatową **identyfikator serii czasu**.

* Scenariusz 2:

  * Wymagane jest wiele właściwości do wyświetlenia unikatowości w tym samym floty zasoby. Na przykład załóżmy, że są producenta inteligentne tworzenie i wdrażanie czujników w każdym pokoju. W każdym pomieszczeniu, zazwyczaj mają takie same wartości **sensorId**, w tym **sensor1**, **sensor2**, **sensor3**i tak dalej.
  * Ponadto nakładających się produkcją i miejsca w witrynach we właściwości **flrRm** które zawierają wartości, takich jak `1a`, `2b`, `3a`i tak dalej.
  * Na koniec ma właściwości, **lokalizacji** zawierającą wartości, takich jak `Redmond`, `Barcelona`, `Tokyo`i tak dalej. Aby utworzyć unikatowości, czy wyznaczyć wszystkich trzech właściwości jako swojej **identyfikator serii czasu** kluczami — **sensorId**, **flrRm**, i **lokalizacji**.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat [Azure Time Series Insights (wersja zapoznawcza) modeli szeregów czasowych](./time-series-insights-update-tsm.md).

Plan usługi [środowisko usługi Azure Time Series Insights (wersja zapoznawcza)](./time-series-insights-update-plan.md).