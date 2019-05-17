---
title: Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych
description: W tym artykule opisano, jak analizować dzienniki diagnostyki w usłudze Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: e784cfd2956479327cff9c97a09dd0ada6a154c2
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826575"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych

Od czasu do czasu zadania usługi Azure Stream Analytics nieoczekiwanie zatrzymuje przetwarzanie. Jest ważne można było rozwiązać tego rodzaju zdarzenia. Mogą one być spowodowane przez nieoczekiwany wynik zapytania, problemy z łącznością z urządzeniami lub nieoczekiwaną awarię usługi. Dzienniki diagnostyczne w usłudze Stream Analytics może pomóc w zidentyfikowaniu przyczyny problemów, gdy wystąpi i skrócić czas odzyskiwania.

## <a name="log-types"></a>Typy dziennika

Stream Analytics oferuje dwa typy dzienników:

* [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (zawsze włączone), które dają wgląd w operacje wykonywane na zadania.

* [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (z możliwością konfiguracji), zapewniających pełniejszy wgląd do wszystkiego się dzieje z zadaniem. Dzienniki diagnostyczne rozpoczęcia po utworzeniu zadania i zakończenia, gdy zadanie zostanie usunięte. Obejmują one zdarzenia, gdy zadanie zostanie zaktualizowany, i jest uruchomiona.

> [!NOTE]
> Można użyć usług, takich jak usługi Azure Storage, Azure Event Hubs, a usługi Azure Monitor dzienników do analizowania danych niestandardowych. Opłaty są naliczane zależnie od modelu cenowego dla tych usług.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debugowanie przy użyciu działania logowania

Dzienniki aktywności są domyślnie włączone i zapewniają wysokiego poziomu szczegółowych informacji o operacji wykonywanych przez zadania usługi Stream Analytics. Informacje znajdujące się w dziennikach aktywności może pomóc odkryć przyczynę problemów wpływających na zadania. Wykonaj poniższe kroki, aby używać dzienników aktywności w usłudze Stream Analytics:

1. Zaloguj się w witrynie Azure portal i wybierz **dziennika aktywności** w obszarze **Przegląd**.

   ![Dziennik aktywności usługi Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Można wyświetlić listę działań, które zostały wykonane. Każdej operacji, która spowodowała niepowodzenie zadania ma bąbelków czerwony informacje.

3. Kliknij operację, aby wyświetlić jego widok podsumowania. W tym miejscu informacje często jest ograniczona. Aby dowiedzieć się więcej na temat operacji, kliknij przycisk **JSON**.

   ![Stream podsumowaniu operacji dziennika aktywności usługi Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Przewiń w dół do **właściwości** sekcji JSON, która zawiera szczegółowe informacje o błędzie, który spowodował niepowodzenie operacji. W tym przykładzie błąd środowiska uruchomieniowego, przebywając za wartości szerokości geograficznej powiązanej przyczyną niepowodzenia.

   ![Szczegóły błędu w formacie JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Można wykonać akcje naprawcze, oparty na komunikat o błędzie w formacie JSON. W tym przykładzie sprawdza wartość szerokości geograficznej od-90 stopni i 90 stopni, należy dodać do zapytania.

6. Jeśli komunikat o błędzie w dziennikach aktywności nie jest pomocne w określeniu głównych przyczyn, Włączanie dzienników diagnostycznych i przy użyciu dzienników usługi Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Wysyłanie danych diagnostycznych dzienników usługi Azure Monitor

Zdecydowanie zaleca się włączenie dzienników diagnostycznych i wysyła je do dzienników usługi Azure Monitor. Dzienniki diagnostyczne są **poza** domyślnie. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  Zaloguj się do witryny Azure portal i przejdź do zadania usługi Stream Analytics. W obszarze **monitorowanie**, wybierz opcję **dzienniki diagnostyczne**. Następnie wybierz pozycję **Włącz diagnostykę**.

    ![Nawigacja w bloku do dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Tworzenie **nazwa** w **ustawień diagnostycznych** i zaznacz pole wyboru obok pozycji **wysyłanie do usługi Log Analytics**. Następnie Dodaj istniejącą lub Utwórz nową **obszar roboczy usługi Log analytics**. Zaznacz pola wyboru dla **wykonywania** i **tworzenie** w obszarze **dziennika**, i **AllMetrics** w obszarze **METRYKI** . Kliknij pozycję **Zapisz**.

    ![Ustawienia dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Po uruchomieniu zadania usługi Stream Analytics, dzienniki diagnostyczne są kierowane do obszaru roboczego usługi Log Analytics. Przejdź do obszaru roboczego usługi Log Analytics i wybierz pozycję **dzienniki** w obszarze **ogólne** sekcji.

   ![Dzienniki platformy Azure Monitor w sekcji Ogólne](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Możesz [napisać własną kwerendę](../azure-monitor/log-query/get-started-portal.md) można wyszukiwać terminy, identyfikację trendów, analizować wzorce i szczegółowymi informacjami na podstawie danych. Na przykład piszesz zapytania w celu filtrowania tylko dzienniki diagnostyczne, które mają komunikat "zadanie przesyłania strumieniowego nie powiodło się." Dzienniki diagnostyczne z usługi Azure Stream Analytics są przechowywane w **AzureDiagnostics** tabeli.

   ![Diagnostyka zapytań i wyniki](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. W przypadku zapytań, który wyszukuje odpowiednie dzienniki, zapisz go, wybierając **Zapisz** oraz podaj nazwę i kategorię. Następnie można utworzyć alertu, wybierając **Nowa reguła alertu**. Następnie określ warunek alertu. Wybierz **warunek** i wprowadź wartość progową i częstotliwość, w którym jest oceniana to wyszukiwanie dzienników niestandardowych.  

   ![Zapytanie wyszukiwania dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Wybierz grupę akcję i określ szczegóły alertu, takie jak nazwa i opis, aby można było utworzyć regułę alertu. Różne zadania dzienniki diagnostyczne można kierować do tego samego obszaru roboczego usługi Log Analytics. Dzięki temu można skonfigurować alerty, gdy to pomoże we wszystkich zadań.  

## <a name="diagnostics-log-categories"></a>Kategorie dziennika diagnostycznego

Usługa Azure Stream Analytics przechwytuje dwie kategorie dzienników diagnostycznych:

* **Tworzenie**: Przechwytuje zdarzenia dziennika, that are related to zadania tworzenia operacje, takie jak tworzenie zadania, dodając i usuwając dane wejściowe i wyjściowe, dodawanie i aktualizowania zapytania i uruchamiania lub zatrzymywania zadania.

* **Wykonanie**: Przechwytuje zdarzenia, które występują podczas wykonywania zadania.
    * Błędy związane z łącznością
    * Błędy przetwarzania danych, w tym:
        * Zdarzenia, które nie są zgodne z definicji zapytania (typy niezgodne pól i wartości, brakujące pola i tak dalej)
        * Błędy oceny wyrażenia
    * Inne zdarzenia i błędy

## <a name="diagnostics-logs-schema"></a>Schemat dzienniki diagnostyki

Wszystkie dzienniki są przechowywane w formacie JSON. Każdy wpis ma następujące typowe pola ciągu:

Name (Nazwa) | Opis
------- | -------
time | Sygnatura czasowa (w formacie UTC) dziennika.
resourceId | Identyfikator zasobu, że operacja miało miejsce, napisane wielkimi literami. Zawiera identyfikator subskrypcji, grupy zasobów i nazwę zadania. Na przykład   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategoria, zaloguj się albo **wykonywania** lub **tworzenie**.
operationName | Nazwa operacji, który jest zalogowany. Na przykład **wysyłać zdarzenia: Błąd zapisu danych wyjściowych SQL do mysqloutput**.
stan | Stan operacji. Na przykład **niepowodzenie** lub **Powodzenie**.
poziom | Poziom dziennika. Na przykład **błąd**, **ostrzeżenie**, lub **komunikat o charakterze informacyjnym**.
properties | Szczegóły konkretnego wpisu dziennika, zserializowanym w formacie ciągu JSON. Aby uzyskać więcej informacji zobacz następujące sekcje w tym artykule.

### <a name="execution-log-properties-schema"></a>Schemat właściwości dziennika wykonywania

Dzienniki wykonywania ma informacje o zdarzeniach, które wystąpiły podczas wykonywania zadania usługi Stream Analytics. Schemat właściwości różni się w zależności od tego, czy zdarzenie jest błąd danych, czy zdarzenie generyczne.

### <a name="data-errors"></a>Błędy danych

Wszelkie błędy występujące podczas zadania przetwarzania danych jest w tej kategorii dzienników. Te dzienniki w większości przypadków są tworzone podczas odczytu, danych serializacji, operacji i zapisu. Dzienniki te nie zawierają błędy związane z łącznością. Błędy łączności są traktowane jako zdarzenia ogólne.

Name (Nazwa) | Opis
------- | -------
Element źródłowy | Nazwa zadania danych wejściowych lub wyjściowych, w którym wystąpił błąd.
Komunikat | Komunikat skojarzony z powodu błędu.
Typ | Typ błędu. Na przykład **DataConversionError**, **CsvParserError**, lub **ServiceBusPropertyColumnMissingError**.
Dane | Zawiera dane, które są przydatne do dokładnie zlokalizować źródła błędu. Z zastrzeżeniem obcięcie, w zależności od rozmiaru.

W zależności od **operationName** wartości błędów danych mają zgodny z następującym schematem:

* **Serializacja zdarzeń** odbywały się zdarzenia, operacje odczytu. Występują, gdy dane na dane wejściowe nie spełnia schematu zapytania dla jednego z następujących powodów:

   * *Niezgodność typów podczas zdarzenia (de) serializacji*: Określa pole, które powoduje błąd.

   * *Nie można odczytać zdarzenie, nieprawidłowe serializacja*: Wyświetla informacje o lokalizacji danych wejściowych, w którym wystąpił błąd. Zawiera nazwę obiektu blob dla obiektu blob danych wejściowych, przesunięcie i przykładowych danych.

* **Wysyłanie zdarzeń** wystąpić podczas operacji zapisu. Identyfikują one wydarzenia przesyłania strumieniowego, które spowodowały błąd.

### <a name="generic-events"></a>Zdarzenia ogólne

Zdarzenia ogólne obejmuje wszystkie inne elementy.

Name (Nazwa) | Opis
-------- | --------
Błąd | (opcjonalnie) Informacje o błędzie. Zazwyczaj jest to informacje o wyjątku, jeśli jest ona dostępna.
Message| Komunikat dziennika.
Typ | Typ komunikatu. Mapuje do wewnętrznego Kategoryzacja błędów. Na przykład **JobValidationError** lub **BlobOutputAdapterInitializationFailure**.
Identyfikator korelacji | [Identyfikator GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , który jednoznacznie identyfikuje wykonywania zadania. Wszystkie wpisy dziennika wykonywania od momentu zadanie zostało uruchomione, dopóki zadanie zostanie zatrzymane, mające taką samą **identyfikator korelacji** wartość.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
