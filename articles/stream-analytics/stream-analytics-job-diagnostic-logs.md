---
title: Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych
description: W tym artykule opisano sposób analizowania dzienników diagnostycznych w usłudze Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426132"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych

Czasem może się zdarzyć, że przetwarzanie zadania usługi Azure Stream Analytics zostanie nieoczekiwanie zatrzymane. Ważne jest, aby było możliwe rozwiązanie problemu z tego rodzaju zdarzeniem. Mogą one być spowodowane przez nieoczekiwany wynik zapytania, problemy z łącznością z urządzeniami lub nieoczekiwaną awarię usługi. Dzienniki diagnostyczne w usłudze Stream Analytics mogą pomóc w zidentyfikowaniu przyczyny problemów w przypadku ich wystąpienia i skrócić czas odzyskiwania.

Zdecydowanie zaleca się włączenie dzienników diagnostycznych dla wszystkich zadań, ponieważ znacznie pomoże to w debugowaniu i monitorowaniu.

## <a name="log-types"></a>Typy dzienników

Usługa Stream Analytics oferuje dwa typy dzienników:

* [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (zawsze włączone), które dają wgląd w operacje wykonywane na zadaniach.

* [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurowalne), które zapewniają bogatsze wgląd we wszystko, co dzieje się z zadaniem. Dzienniki diagnostyki rozpoczynają się po utworzeniu zadania i kończą się po usunięciu zadania. Obejmują one zdarzenia, gdy zadanie jest aktualizowane i gdy jest uruchomione.

> [!NOTE]
> Do analizowania danych niezgodnych z zasadami można używać usług, takich jak usługa Azure Storage, usługi Azure Event Hubs i dzienniki usługi Azure Monitor. Opłaty są naliczane na podstawie modelu cenowego dla tych usług.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debugowanie przy użyciu dzienników aktywności

Dzienniki aktywności są domyślnie włączone i zapewniają wgląd wysokiego poziomu w operacje wykonywane przez zadanie usługi Stream Analytics. Informacje zawarte w dziennikach aktywności mogą pomóc w znalezieniu głównej przyczyny problemów wpływających na pracę. Wykonaj następujące czynności, aby używać dzienników aktywności w usłudze Stream Analytics:

1. Zaloguj się do witryny Azure portal i wybierz **dziennik aktywności** w obszarze **Przegląd**.

   ![Dziennik aktywności usługi Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Można wyświetlić listę operacji, które zostały wykonane. Każda operacja, która spowodowała niepowodzenie zadania, ma czerwoną dymkę informacji.

3. Kliknij operację, aby wyświetlić jej widok podsumowania. Informacje tutaj są często ograniczone. Aby dowiedzieć się więcej szczegółów dotyczących operacji, kliknij przycisk **JSON**.

   ![Podsumowanie operacji dziennika aktywności usługi Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Przewiń w dół do **sekcji Właściwości** JSON, która zawiera szczegółowe informacje o błędzie, który spowodował nieudaną operację. W tym przykładzie błąd był spowodowany błędem środowiska uruchomieniowego z wartości szerokości geograficznej powiązanej. Rozbieżność w danych, które są przetwarzane przez zadanie usługi Stream Analytics powoduje błąd danych. Możesz dowiedzieć się więcej o różnych [błędach danych wejściowych i wyjściowych oraz o ich przyczynach.](https://docs.microsoft.com/azure/stream-analytics/data-errors)

   ![Szczegóły błędu JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Można podjąć działania naprawcze na podstawie komunikatu o błędzie w JSON. W tym przykładzie sprawdza, aby upewnić się, że wartość szerokości geograficznej wynosi od -90 stopni do 90 stopni, należy dodać do kwerendy.

6. Jeśli komunikat o błędzie w dziennikach aktywności nie jest pomocne w identyfikowaniu głównej przyczyny, włącz dzienniki diagnostyczne i użyj dzienników usługi Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Wysyłanie diagnostyki do dzienników usługi Azure Monitor

Włączenie dzienników diagnostycznych i wysyłanie ich do dzienników usługi Azure Monitor jest wysoce zalecane. Dzienniki diagnostyczne są domyślnie **wyłączone.** Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1.  Zaloguj się do witryny Azure portal i przejdź do zadania usługi Stream Analytics. W obszarze **Monitorowanie**wybierz **pozycję Dzienniki diagnostyczne**. Następnie wybierz pozycję **Włącz diagnostykę**.

    ![Nawigacja w kasach do dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Utwórz **nazwę** w **ustawieniach diagnostycznych** i zaznacz pole obok **pozycji Wyślij do usługi Log Analytics**. Następnie dodaj istniejący lub utwórz nowy **obszar roboczy analizy dzienników**. Zaznacz pola wyboru **Wykonanie** i **Tworzenie** w obszarze **DZIENNIK**i **Wszystkiemetryki** w obszarze **METRYK**. Kliknij przycisk **Zapisz**. Zaleca się użycie obszaru roboczego usługi Log Analytics w tym samym regionie platformy Azure co zadanie usługi Stream Analytics, aby zapobiec dodatkowym kosztom.

    ![Ustawienia dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Po uruchomieniu zadania usługi Stream Analytics dzienniki diagnostyczne są kierowane do obszaru roboczego usługi Log Analytics. Aby wyświetlić dzienniki diagnostyczne dla zadania, wybierz **dzienniki** w sekcji **Monitorowanie.**

   ![Dzienniki diagnostyczne w obszarze Monitorowanie](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Usługa Stream Analytics udostępnia wstępnie zdefiniowane zapytania, które umożliwiają łatwe wyszukiwanie zainteresowanych dzienników. 3 kategorie to **Ogólne,** **Błędy danych wejściowych** i Błędy danych **wyjściowych.** Na przykład, aby wyświetlić podsumowanie wszystkich błędów zadania w ciągu ostatnich 7 dni, można wybrać **Uruchom** odpowiednią wstępnie zdefiniowaną kwerendę. 

   ![Dzienniki diagnostyczne w obszarze Monitorowanie](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Wyniki dzienników](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Kategorie dzienników diagnostyki

Usługa Azure Stream Analytics przechwytuje dwie kategorie dzienników diagnostycznych:

* **Tworzenie:** Przechwytuje zdarzenia dziennika, które są związane z operacjami tworzenia zadań, takimi jak tworzenie zadań, dodawanie i usuwanie danych wejściowych i wyjściowych, dodawanie i aktualizowanie kwerendy oraz uruchamianie lub zatrzymywanie zadania.

* **Wykonanie:** Przechwytuje zdarzenia, które występują podczas wykonywania zadania.
    * Błędy łączności
    * Błędy przetwarzania danych, w tym:
        * Zdarzenia, które nie są zgodne z definicją kwerendy (niedopasowane typy i wartości pól, brakujące pola itd.)
        * Błędy oceny wyrażenia
    * Inne zdarzenia i błędy

## <a name="diagnostics-logs-schema"></a>Schemat dzienników diagnostycznych

Wszystkie dzienniki są przechowywane w formacie JSON. Każdy wpis ma następujące wspólne pola ciągu:

Nazwa | Opis
------- | -------
time | Sygnatura czasowa dziennika (w czasie UTC).
resourceId | Identyfikator zasobu, na który operacja miała miejsce, w wielkich literach. Zawiera identyfikator subskrypcji, grupę zasobów i nazwę zadania. Na przykład **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategoria dziennika, **wykonanie** lub **tworzenie**.
operationName | Nazwa operacji, która jest rejestrowana. Na przykład **Wyślij zdarzenia: Błąd zapisu danych wyjściowych SQL do mysqloutput**.
status | Stan operacji. Na **przykład** niepowodzenie lub **powodnie**.
poziom | Poziom dziennika. Na przykład **Błąd**, **Ostrzeżenie**lub **Informacje**.
properties | Szczegóły specyficzne dla wpisu dziennika, serializowane jako ciąg JSON. Aby uzyskać więcej informacji, zobacz poniższe sekcje w tym artykule.

### <a name="execution-log-properties-schema"></a>Schemat właściwości dziennika wykonania

Dzienniki wykonywania mają informacje o zdarzeniach, które miały miejsce podczas wykonywania zadania usługi Stream Analytics. Schemat właściwości różni się w zależności od tego, czy zdarzenie jest błędem danych, czy zdarzeniem ogólnym.

### <a name="data-errors"></a>Błędy danych

Każdy błąd, który występuje podczas przetwarzania danych przez zadanie, znajduje się w tej kategorii dzienników. Te dzienniki najczęściej są tworzone podczas odczytu danych, serializacji i operacji zapisu. Te dzienniki nie zawierają błędów łączności. Błędy łączności są traktowane jako zdarzenia ogólne. Możesz dowiedzieć się więcej o przyczynach różnych [błędów danych wejściowych i wyjściowych.](https://docs.microsoft.com/azure/stream-analytics/data-errors)

Nazwa | Opis
------- | -------
Element źródłowy | Nazwa danych wejściowych lub wyjściowych zadania, w których wystąpił błąd.
Komunikat | Komunikat skojarzony z błędem.
Typ | Typ błędu. Na przykład **DataConversionError**, **CsvParserError**lub **ServiceBusPropertyColumnMissingError**.
Dane | Zawiera dane, które są przydatne do dokładnego zlokalizowania źródła błędu. Z zastrzeżeniem obcinania, w zależności od rozmiaru.

W zależności od wartości **operationName** błędy danych mają następujący schemat:

* **Zdarzenia serializacji** występują podczas operacji odczytu zdarzenia. Występują one, gdy dane na wejściu nie spełnia schematu kwerendy z jednego z następujących powodów:

   * *Niezgodność typu podczas zdarzenia (de)serializacji:* Identyfikuje pole, które powoduje błąd.

   * *Nie można odczytać zdarzenia, nieprawidłowa serializacja:* Wyświetla informacje o lokalizacji w danych wejściowych, w których wystąpił błąd. Zawiera nazwę obiektu blob dla danych wejściowych obiektów blob, przesunięcie i próbki danych.

* **Wysyłanie zdarzeń** występuje podczas operacji zapisu. Identyfikują zdarzenie przesyłania strumieniowego, które spowodowało błąd.

### <a name="generic-events"></a>Zdarzenia ogólne

Ogólne zdarzenia obejmują wszystko inne.

Nazwa | Opis
-------- | --------
Błąd | (opcjonalnie) Informacje o błędzie. Zazwyczaj jest to informacja o wyjątku, jeśli jest dostępna.
Komunikat| Komunikat dziennika.
Typ | Typ wiadomości. Mapy do wewnętrznej kategoryzacji błędów. Na przykład **JobValidationError** lub **BlobOutputAdapterInitializationFailure**.
Identyfikator korelacji | [Identyfikator GUID,](https://en.wikipedia.org/wiki/Universally_unique_identifier) który jednoznacznie identyfikuje wykonanie zadania. Wszystkie wpisy dziennika wykonywania od momentu rozpoczęcia zadania do zatrzymania zadania mają taką samą wartość **identyfikatora korelacji.**

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do analizy strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Odwołanie do języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Błędy danych usługi Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
