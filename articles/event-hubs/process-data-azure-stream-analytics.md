---
title: Przetwarzanie danych z platformy Azure z centrów zdarzeń, przy użyciu usługi Stream Analytics | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak przetwarzać dane z Centrum zdarzeń platformy Azure przy użyciu zadania usługi Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723417"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Przetwarzanie danych z Centrum zdarzeń za pomocą usługi Azure Stream Analytics
Usługa Azure Stream Analytics ułatwia pozyskiwania procesu i analizować dane przesyłane strumieniowo z usługi Azure Event Hubs, uzyskiwanie szczegółowych zaawansowane, aby napędzać akcje w czasie rzeczywistym. Ta integracja umożliwia szybkie tworzenie potoku analizy na gorąco ścieżki. Witryna Azure portal umożliwia wizualizowanie danych przychodzących i zapisywanie zapytań usługi Stream Analytics. Gdy zapytanie jest gotowy, można przenieść go w środowisku produkcyjnym za pomocą tylko kilku kliknięć. 

## <a name="key-benefits"></a>Najważniejsze korzyści
Poniżej przedstawiono główne zalety integracji usługi Azure Event Hubs i Azure Stream Analytics: 
- **Podgląd danych** — możesz wyświetlić podgląd danych przychodzących z Centrum zdarzeń w witrynie Azure portal.
- **Testowanie zapytania** — przygotowanie zapytanie przekształcenia i przetestować go bezpośrednio w witrynie Azure portal. Dla składni języka zapytań, zobacz [języka zapytań usługi Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) dokumentacji.
- **Wdrażanie zapytania do środowiska produkcyjnego** — możesz wdrożyć zapytania do środowiska produkcyjnego, tworzenie i uruchamianie zadania usługi Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Przepływ end-to-end

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do usługi **przestrzeni nazw usługi Event Hubs** , a następnie przejdź do **Centrum zdarzeń**, który ma danych przychodzących. 
1. Wybierz **przetwarzania danych** na stronie Centrum zdarzeń.  

    ![Proces danych kafelków](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Wybierz **Eksploruj** na **Włącz analiz w czasie rzeczywistym na podstawie zdarzeń** kafelka. 

    ![Wybierz usługi Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Zostanie wyświetlona strona zapytania przy użyciu wartości skonfigurowane dla następujących pól:
    1. Twoje **Centrum zdarzeń** jako dane wejściowe dla zapytania.
    1. Przykładowe **zapytania SQL** przy użyciu instrukcji SELECT. 
    1. **Dane wyjściowe** alias do odwoływania się do wyników testu. 

        ![Edytor zapytań](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Korzystając z tej funkcji po raz pierwszy, ta strona poprosi o podanie Twojej zgody na utworzenie grupy odbiorców i zasad Centrum zdarzeń, aby wyświetlić podgląd danych przychodzących.
1. Wybierz **Utwórz** w **dane wejściowe w wersji zapoznawczej** okienko, jak pokazano na poprzedniej ilustracji. 
1. Natychmiast zauważysz migawkę najnowsze dane przychodzące na tej karcie.
    - Typ serializacji w danych jest automatycznie wykryte (w formacie JSON na CSV). Możesz można ręcznie zmienić również na JSON/CSV/AVRO.
    - Możesz wyświetlić podgląd danych przychodzących w formacie tabeli lub w formacie nieprzetworzonym. 
    - Jeśli Twoje dane pokazywane nie jest aktualny, wybierz opcję **Odśwież** aby zobaczyć najnowsze zdarzenia. 

        Oto przykład danych w **format tabeli**:   ![Wyniki w formacie tabeli](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Oto przykład danych w **raw format**: 

        ![Wyniki w formacie nieprzetworzonym](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Wybierz **Testovat dotaz** Aby wyświetlić migawkę testu wyniki zapytania w **wyniki testu** kartę. Można również pobrać wyniki.

    ![Wyniki zapytania](./media/process-data-azure-stream-analytics/test-results.png)
1. Napisz zapytanie do przekształcania danych. Zobacz [dokumentacja języka zapytań usługi Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).
1. Po przetestowaniu zapytania i chcesz przenieść go w środowisku produkcyjnym, wybierz opcję **zapytania Wdróż**. Aby wdrożyć zapytania, należy utworzyć zadanie usługi Azure Stream Analytics, w którym można ustawić dane wyjściowe dla zadania i uruchomić zadanie. Aby utworzyć zadanie usługi Stream Analytics, podaj nazwę dla zadania, a następnie wybierz pozycję **Utwórz**.

      ![Tworzenie zadania usługi Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Zaleca się utworzenie grupy odbiorców i zasad dla każdego nowego zadania usługi Azure Stream Analytics, utworzony ze strony usługi Event Hubs. Grupy konsumentów dopuszczają tylko 5 współbieżnych czytników, zapewniając dedykowanej grupy klientów dla każdego zadania pozwoli uniknąć błędów, które mogą wystąpić przekroczenie tego limitu. Zasady dedykowanej umożliwia Obróć klucz lub odwołać uprawnienia bez wywierania wpływu na inne zasoby. 
1. Zadanie usługi Stream Analytics jest gotowy, gdy zapytanie jest taki sam, który został przetestowany i dane wejściowe są Centrum zdarzeń. 

9.  Aby ukończyć potok, należy ustawić **dane wyjściowe** zapytania, a następnie zaznacz **Start** Aby uruchomić zadanie.

    > [!NOTE]
    > Przed wykonaniem zadania, nie zapomnij zastąpić outputalias według nazwy wyjściowego, utworzonej w usłudze Azure Stream Analytics.

      ![Ustaw dane wyjściowe i uruchamianie zadania](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Znane ograniczenia
Podczas testowania zapytanie, wyniki testów zająć około 6 sekund do załadowania. Pracujemy nad poprawę wydajności testowania. Podczas wdrażania w środowisku produkcyjnym, Azure Stream Analytics będzie jednak subsecond opóźnienia.

## <a name="streaming-units"></a>Jednostki przesyłania strumieniowego
Usługi Azure Stream Analytics zadań, wartość domyślna to trzy jednostek przesyłania strumieniowego (SUs). Aby zmienić to ustawienie, wybierz pozycję **skalowania** w menu po lewej stronie w **zadania usługi Stream Analytics** strony w witrynie Azure portal. Aby dowiedzieć się więcej na temat jednostek przesyłania strumieniowego, zobacz [opis i dostosowywanie jednostek przesyłania strumieniowego](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Możliwość skalowania jednostek przesyłania strumieniowego](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat zapytań usługi Stream Analytics, zobacz [języka zapytań usługi Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics)