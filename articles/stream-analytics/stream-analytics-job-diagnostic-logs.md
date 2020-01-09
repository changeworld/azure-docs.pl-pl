---
title: Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych
description: W tym artykule opisano, jak analizować dzienniki diagnostyki w usłudze Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426132"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych

Czasem może się zdarzyć, że przetwarzanie zadania usługi Azure Stream Analytics zostanie nieoczekiwanie zatrzymane. Ważne jest, aby było możliwe rozwiązanie problemu z tego rodzaju zdarzeniem. Mogą one być spowodowane przez nieoczekiwany wynik zapytania, problemy z łącznością z urządzeniami lub nieoczekiwaną awarię usługi. Dzienniki diagnostyczne w Stream Analytics mogą pomóc w zidentyfikowaniu przyczyny problemów występujących i skróceniu czasu odzyskiwania.

Zdecydowanie zaleca się włączenie dzienników diagnostycznych dla wszystkich zadań, ponieważ ułatwi to debugowanie i monitorowanie.

## <a name="log-types"></a>Typy dziennika

Stream Analytics oferuje dwa typy dzienników:

* [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (zawsze włączone), które dają wgląd w operacje wykonywane na zadaniach.

* [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurowalne), które zapewniają bogatszy wgląd w wszystko, co się dzieje z zadaniami. Dzienniki diagnostyczne są uruchamiane, gdy zadanie zostanie utworzone i zakończy się, gdy zadanie zostanie usunięte. Obejmują one zdarzenia, gdy zadanie zostanie zaktualizowany, i jest uruchomiona.

> [!NOTE]
> Aby analizować dane niezgodne, można użyć usług takich jak Azure Storage, Azure Event Hubs i Azure Monitor logs. Opłaty są naliczane zależnie od modelu cenowego dla tych usług.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debugowanie przy użyciu dzienników aktywności

Dzienniki aktywności są domyślnie włączone i zapewniają szczegółowe informacje o operacjach wykonywanych przez zadanie Stream Analytics. Informacje znajdujące się w dziennikach aktywności mogą pomóc w znalezieniu głównej przyczyny problemów wpływających na zadanie. Wykonaj następujące kroki, aby użyć dzienników aktywności w Stream Analytics:

1. Zaloguj się do Azure Portal i wybierz pozycję **Dziennik aktywności** w obszarze **Przegląd**.

   ![Stream Analytics dziennik aktywności](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Można wyświetlić listę operacji, które zostały wykonane. Każda operacja, która spowodowała niepowodzenie zadania, zawiera czerwony dymek informacyjny.

3. Kliknij operację, aby wyświetlić jej widok podsumowania. Informacje są często ograniczone. Aby dowiedzieć się więcej na temat operacji, kliknij pozycję **JSON**.

   ![Podsumowanie operacji dziennika aktywności Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Przewiń w dół do sekcji **Właściwości** w formacie JSON, która zawiera szczegółowe informacje o błędzie, który spowodował niepowodzenie operacji. W tym przykładzie błąd był spowodowany błędem środowiska uruchomieniowego z nieprawidłowych wartości szerokości geograficznej. Niezgodność danych przetwarzanych przez zadanie Stream Analytics powoduje błąd danych. Można dowiedzieć się więcej o różnych [błędach danych wejściowych i wyjściowych oraz o tym, dlaczego występują](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Szczegóły błędu JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Możesz podejmować działania naprawcze na podstawie komunikatu o błędzie w formacie JSON. W tym przykładzie sprawdza, czy wartość szerokości geograficznej jest między-90 stopni i 90 stopni należy dodać do zapytania.

6. Jeśli komunikat o błędzie w dziennikach aktywności nie jest pomocny w przypadku identyfikowania głównej przyczyny, Włącz dzienniki diagnostyczne i użyj dzienników Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Wyślij diagnostykę do dzienników Azure Monitor

Zdecydowanie zaleca się włączenie dzienników diagnostycznych i wysłanie ich do dzienników Azure Monitor. Dzienniki diagnostyczne są **poza** domyślnie. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  Zaloguj się do Azure Portal i przejdź do zadania Stream Analytics. W obszarze **monitorowanie**, wybierz opcję **dzienniki diagnostyczne**. Następnie wybierz pozycję **Włącz diagnostykę**.

    ![Nawigacja w bloku do dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Utwórz **nazwę** w **ustawieniach diagnostycznych** i zaznacz pole wyboru obok pozycji **Wyślij do log Analytics**. Następnie Dodaj istniejący lub Utwórz nowy **obszar roboczy usługi log Analytics**. Zaznacz pola wyboru do **wykonania** i **tworzenia** w obszarze **Dziennik**, a następnie pozycję **AllMetrics** w obszarze **Metryka**. Kliknij pozycję **Zapisz**. Zalecane jest używanie obszaru roboczego Log Analytics w tym samym regionie świadczenia usługi Azure co zadanie Stream Analytics, aby zapobiec dodatkowym kosztom.

    ![Ustawienia dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Po rozpoczęciu zadania Stream Analytics dzienniki diagnostyczne są kierowane do obszaru roboczego Log Analytics. Aby wyświetlić dzienniki diagnostyczne dla danego zadania, wybierz pozycję **dzienniki** w sekcji **monitorowanie** .

   ![Dzienniki diagnostyczne objęte monitorowaniem](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics udostępnia wstępnie zdefiniowane zapytania, które umożliwiają łatwe wyszukiwanie dzienników, które Cię interesują. 3 kategorie są **Ogólne**, **błędy danych wejściowych** i **błędy danych wyjściowych**. Na przykład, aby zobaczyć podsumowanie wszystkich błędów zadania w ciągu ostatnich 7 dni, można wybrać opcję **uruchomienia** odpowiedniego wstępnie zdefiniowanego zapytania. 

   ![Dzienniki diagnostyczne objęte monitorowaniem](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Wyniki dzienników](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Kategorie dziennika diagnostycznego

Azure Stream Analytics przechwytuje dwie kategorie dzienników diagnostycznych:

* **Tworzenie**: przechwytuje zdarzenia dziennika, które są związane z operacjami tworzenia zadań, takimi jak tworzenie zadań, Dodawanie i usuwanie danych wejściowych i wyjściowych, Dodawanie i aktualizowanie zapytania oraz uruchamianie lub zatrzymywanie zadania.

* **Wykonywanie**: przechwytuje zdarzenia, które wystąpiły podczas wykonywania zadania.
    * Błędy łączności
    * Błędy przetwarzania danych, w tym:
        * Zdarzenia, które nie są zgodne z definicji zapytania (typy niezgodne pól i wartości, brakujące pola i tak dalej)
        * Błędy oceny wyrażenia
    * Inne zdarzenia i błędy

## <a name="diagnostics-logs-schema"></a>Schemat dzienniki diagnostyki

Wszystkie dzienniki są przechowywane w formacie JSON. Każdy wpis ma następujące typowe pola ciągu:

Nazwa | Opis
------- | -------
time | Sygnatura czasowa (w formacie UTC) dziennika.
resourceId | Identyfikator zasobu, że operacja miało miejsce, napisane wielkimi literami. Zawiera identyfikator subskrypcji, grupy zasobów i nazwę zadania. Na przykład   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategoria, zaloguj się albo **wykonywania** lub **tworzenie**.
operationName | Nazwa operacji, który jest zalogowany. Na przykład **wysyłać zdarzenia: błąd zapisu danych wyjściowych SQL do mysqloutput**.
status | Stan operacji. Na przykład **niepowodzenie** lub **Powodzenie**.
level | Poziom dziennika. Na przykład **błąd**, **ostrzeżenie**, lub **komunikat o charakterze informacyjnym**.
properties | Szczegóły konkretnego wpisu dziennika, zserializowanym w formacie ciągu JSON. Aby uzyskać więcej informacji, zobacz następujące sekcje w tym artykule.

### <a name="execution-log-properties-schema"></a>Schemat właściwości dziennika wykonywania

Dzienniki wykonywania ma informacje o zdarzeniach, które wystąpiły podczas wykonywania zadania usługi Stream Analytics. Schemat właściwości różni się w zależności od tego, czy zdarzenie jest błędem danych czy zdarzeniem ogólnym.

### <a name="data-errors"></a>Błędy danych

Wszelkie błędy występujące podczas zadania przetwarzania danych jest w tej kategorii dzienników. Te dzienniki w większości przypadków są tworzone podczas odczytu, danych serializacji, operacji i zapisu. Dzienniki te nie zawierają błędy związane z łącznością. Błędy łączności są traktowane jako zdarzenia ogólne. Możesz dowiedzieć się więcej na temat przyczyny różnych [błędów danych wejściowych i wyjściowych](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Nazwa | Opis
------- | -------
Źródło | Nazwa zadania danych wejściowych lub wyjściowych, w którym wystąpił błąd.
Wiadomość | Komunikat skojarzony z powodu błędu.
Typ | Typ błędu. Na przykład **DataConversionError**, **CsvParserError**, lub **ServiceBusPropertyColumnMissingError**.
Dane | Zawiera dane, które są przydatne do dokładnie zlokalizować źródła błędu. Z zastrzeżeniem obcięcie, w zależności od rozmiaru.

W zależności od **operationName** wartości błędów danych mają zgodny z następującym schematem:

* Podczas operacji odczytu zdarzeń wystąpią **serializowane zdarzenia** . Występują, gdy dane na dane wejściowe nie spełnia schematu zapytania dla jednego z następujących powodów:

   * *Niezgodność typów podczas zdarzenia (de) serializacji*: Określa pole, które powoduje błąd.

   * *Nie można odczytać zdarzenie, nieprawidłowe serializacja*: Wyświetla informacje o lokalizacji danych wejściowych, w którym wystąpił błąd. Zawiera nazwę obiektu blob dla obiektu blob danych wejściowych, przesunięcie i przykładowych danych.

* **Zdarzenia wysyłania** występują podczas operacji zapisu. Identyfikują one wydarzenia przesyłania strumieniowego, które spowodowały błąd.

### <a name="generic-events"></a>Zdarzenia ogólne

Zdarzenia ogólne obejmuje wszystkie inne elementy.

Nazwa | Opis
-------- | --------
Błąd | (opcjonalnie) Informacje o błędzie. Zazwyczaj są to informacje o wyjątkach, jeśli są dostępne.
Wiadomość| Komunikat dziennika.
Typ | Typ komunikatu. Mapuje do wewnętrznego Kategoryzacja błędów. Na przykład **JobValidationError** lub **BlobOutputAdapterInitializationFailure**.
Identyfikator korelacji | [Identyfikator GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , który jednoznacznie identyfikuje wykonywania zadania. Wszystkie wpisy dziennika wykonywania od momentu zadanie zostało uruchomione, dopóki zadanie zostanie zatrzymane, mające taką samą **identyfikator korelacji** wartość.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Błędy danych Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
