---
title: Przetwarzanie danych z usługi Event Hubs Azure przy użyciu usługi Stream Analytics | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak przetwarzać dane z centrum zdarzeń platformy Azure przy użyciu zadania usługi Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991945"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Przetwarzanie danych z centrum zdarzeń przy użyciu usługi Azure Stream Analytics 
Usługa Azure Stream Analytics ułatwia pozyskiwania, przetwarzania i analizowania danych przesyłania strumieniowego z usługi Azure Event Hubs, umożliwiając zaawansowane analizy umożliwiające prowadzenie akcji w czasie rzeczywistym. Ta integracja umożliwia szybkie tworzenie potoku analizy gorącej ścieżki. Za pomocą witryny Azure Portal można wizualizować przychodzące dane i zapisywać kwerendę usługi Stream Analytics. Gdy zapytanie jest gotowe, można przenieść go do produkcji za pomocą zaledwie kilku kliknięć. 

## <a name="key-benefits"></a>Najważniejsze korzyści
Oto najważniejsze zalety usługi Azure Event Hubs i integracji usługi Azure Stream Analytics: 
- **Podgląd danych** — można wyświetlić podgląd danych przychodzących z centrum zdarzeń w witrynie Azure portal.
- **Przetestuj zapytanie** — przygotuj kwerendę transformacji i przetestuj ją bezpośrednio w witrynie Azure portal. Aby zapoznać się ze składnią języka kwerendy, zobacz Dokumentacja [języka zapytań usługi Stream Analytics.](/stream-analytics-query/built-in-functions-azure-stream-analytics)
- **Wdrażanie zapytania w procesach produkcyjnych** — można wdrożyć kwerendę w produkcji, tworząc i uruchamiając zadanie usługi Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Przepływ end-to-end

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
1. Przejdź do **obszaru nazw Centrum zdarzeń,** a następnie przejdź do centrum **zdarzeń,** w którym są dane przychodzące. 
1. Wybierz **pozycję Dane procesu** na stronie Centrum zdarzeń.  

    ![Kafelek danych przetwarzania](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Wybierz **pozycję Eksploruj** na kafelku **Włącz szczegółowe informacje w czasie rzeczywistym z obszaru zdarzeń.** 

    ![Wybierz analizę strumienia](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Zostanie wyświetlona strona kwerendy z wartościami już ustawionymi dla następujących pól:
    1. **Centrum zdarzeń** jako dane wejściowe dla kwerendy.
    1. Przykładowa **kwerenda SQL** z instrukcją SELECT. 
    1. Alias **wyjściowy,** który ma się oddzwonienia do wyników testu kwerendy. 

        ![Edytor zapytań](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Gdy ta funkcja jest używana po raz pierwszy, ta strona prosi o pozwolenie na utworzenie grupy odbiorców i zasad centrum zdarzeń do wyświetlania podglądu danych przychodzących.
1. Wybierz **pozycję Utwórz** w okienku **podglądu danych wejściowych,** jak pokazano na poprzednim obrazie. 
1. Na tej karcie natychmiast zobaczysz migawkę najnowszych danych przychodzących.
    - Typ serializacji w danych jest wykrywany automatycznie (JSON/CSV). Można go ręcznie zmienić również na JSON / CSV / AVRO.
    - Można wyświetlić podgląd danych przychodzących w formacie tabeli lub formacie raw. 
    - Jeśli wyświetlane dane nie są aktualne, wybierz **odśwież,** aby wyświetlić najnowsze zdarzenia. 

        Oto przykład danych w **formacie tabeli**: ![Wyniki w formacie tabeli](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Oto przykład danych w **formacie raw:** 

        ![Wyniki w formacie raw](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Wybierz **opcję Testuj kwerendę,** aby wyświetlić migawkę wyników testów kwerendy na karcie **Wyniki testu.** Możesz również pobrać wyniki.

    ![Wyniki kwerendy testowej](./media/process-data-azure-stream-analytics/test-results.png)
1. Napisz własne zapytanie, aby przekształcić dane. Zobacz [Odwołanie do języka zapytań usługi Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. Po przetestowaniu kwerendy i przeniesieniu jej do produkcji wybierz pozycję **Wdrażanie kwerendy**. Aby wdrożyć kwerendę, utwórz zadanie usługi Azure Stream Analytics, w którym można ustawić dane wyjściowe dla zadania i rozpocząć zadanie. Aby utworzyć zadanie usługi Stream Analytics, określ nazwę zadania i wybierz pozycję **Utwórz**.

      ![Tworzenie zadania usługi Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Zaleca się utworzenie grupy konsumentów i zasad dla każdego nowego zadania usługi Azure Stream Analytics utworzonego na stronie Usługi Event Hubs. Grupy konsumentów zezwalają tylko na pięć równoczesnych czytników, więc zapewnienie dedykowanej grupy odbiorców dla każdego zadania pozwoli uniknąć błędów, które mogą wyniknąć z przekroczenia tego limitu. Dedykowane zasady umożliwia obracanie klucza lub odwoływanie uprawnień bez wpływu na inne zasoby. 
1. Zadanie usługi Stream Analytics jest teraz tworzone, gdy zapytanie jest takie samo, jak testowane, a dane wejściowe są Centrum zdarzeń. 

9.  Aby ukończyć potok, ustaw **dane wyjściowe** kwerendy i wybierz **przycisk Start,** aby uruchomić zadanie.

    > [!NOTE]
    > Przed rozpoczęciem zadania nie zapomnij zastąpić outputalias nazwą danych wyjściowych utworzoną w usłudze Azure Stream Analytics.

      ![Ustawianie danych wyjściowych i uruchamianie zadania](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Znane ograniczenia
Podczas testowania zapytania wyniki testu trwać około 6 sekund, aby załadować. Pracujemy nad poprawą wydajności testów. Jednak po wdrożeniu w produkcji usługa Azure Stream Analytics będzie miała opóźnienie podsekundowe.

## <a name="streaming-units"></a>Jednostki przesyłania strumieniowego
Zadanie usługi Azure Stream Analytics domyślnie ma wartość trzech jednostek przesyłania strumieniowego (SU). Aby dostosować to ustawienie, wybierz opcję **Skaluj** po lewej stronie na stronie **zadania usługi Stream Analytics** w witrynie Azure portal. Aby dowiedzieć się więcej o jednostkach przesyłania strumieniowego, zobacz [Opis i dostosowywanie jednostek przesyłania strumieniowego](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Skalowanie jednostek strumieniowych](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zapytaniach usługi Stream Analytics, zobacz [Język zapytań usługi Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics)
