---
title: Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych
description: W tym artykule opisano, jak analizować dzienniki diagnostyki w usłudze Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: db3c9874676e3240f6896c1e1ff8f873360c20d5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090826"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych

Od czasu do czasu zadania usługi Azure Stream Analytics nieoczekiwanie zatrzymuje przetwarzanie. Jest ważne można było rozwiązać tego rodzaju zdarzenia. Zdarzenie może być spowodowany przez wynik nieoczekiwane zapytanie, łączność urządzeń lub awaria usługi nieoczekiwany. Dzienniki diagnostyczne w usłudze Stream Analytics może pomóc w zidentyfikowaniu przyczyny problemów, gdy wystąpi i skrócić czas odzyskiwania.

## <a name="log-types"></a>Typy dziennika

Stream Analytics oferuje dwa typy dzienników: 
* [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (zawsze włączona). Dzienniki aktywności zapewniają wgląd w operacje wykonywane na zadania.
* [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (z możliwością konfiguracji). Dzienniki diagnostyczne udostępniają więcej szczegółowych informacji o wszystko, co się dzieje z zadaniem. Dzienniki diagnostyczne rozpoczęcia, po utworzeniu zadania i zakończenia, gdy zadanie zostanie usunięte. Obejmują one zdarzenia, gdy zadanie zostanie zaktualizowany, i jest uruchomiona.

> [!NOTE]
> Za pomocą usług, takich jak Azure Storage, Azure Event Hubs i Azure Log Analytics do analizowania danych niestandardowych. Opłaty są naliczane zależnie od modelu cenowego dla tych usług.
>

## <a name="turn-on-diagnostics-logs"></a>Włącz dzienniki diagnostyki

Dzienniki diagnostyczne są **poza** domyślnie. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  Zaloguj się do witryny Azure portal i przejdź do bloku zadanie przesyłania strumieniowego. W obszarze **monitorowanie**, wybierz opcję **dzienniki diagnostyczne**.

    ![Nawigacja w bloku do dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Wybierz **Włącz diagnostykę**.

    ![Włącz dzienniki diagnostyczne usługi Stream Analytics](./media/stream-analytics-job-diagnostic-logs/turn-on-diagnostic-logs.png)

3.  Na **ustawień diagnostycznych** strony dla **stan**, wybierz opcję **na**.

    ![Zmień stan dzienniki diagnostyczne](./media/stream-analytics-job-diagnostic-logs/save-diagnostic-log-settings.png)

4.  Ustawienia archiwizacji docelowego (konto magazynu, Centrum zdarzeń, usługi Log Analytics), który chcesz. Wybierz kategorie dzienników, które mają być zbierane (wykonanie, tworzenie). 

5.  Zapisz nową konfigurację diagnostyki.

Konfiguracja diagnostyki trwa około 10 minut, aby zastosować zmiany. Po tym dzienniki start znajdujących się w celu archiwizacji skonfigurowany (możesz to zobaczyć na **dzienniki diagnostyczne** strony):

![Nawigacja w bloku do dzienników diagnostycznych - celów archiwizacji](./media/stream-analytics-job-diagnostic-logs/view-diagnostic-logs-page.png)

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [zbieranie i używanie danych diagnostycznych z Twoich zasobów platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Kategorie dziennika diagnostycznego

Obecnie przechwytujemy dwie kategorie dzienników diagnostycznych:

* **Tworzenie**. Przechwytywanie rejestrowania zdarzeń, that are related to operacje tworzenia zadania: zadania tworzenia, dodawania i usuwania danych wejściowych i danych wyjściowych, dodawania i aktualizowania zapytania, uruchamianie i zatrzymywanie zadania.
* **Wykonanie**. Przechwytuje zdarzenia, które występują podczas wykonywania zadania:
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
operationName | Nazwa operacji, który jest zalogowany. Na przykład **wysyłać zdarzenia: błąd zapisu danych wyjściowych SQL do mysqloutput**.
status | Stan operacji. Na przykład **niepowodzenie** lub **Powodzenie**.
poziom | Poziom dziennika. Na przykład **błąd**, **ostrzeżenie**, lub **komunikat o charakterze informacyjnym**.
properties | Szczegóły konkretnego wpisu dziennika, zserializowanym w formacie ciągu JSON. Aby uzyskać więcej informacji zobacz następujące sekcje.

### <a name="execution-log-properties-schema"></a>Schemat właściwości dziennika wykonywania

Dzienniki wykonywania ma informacje o zdarzeniach, które wystąpiły podczas wykonywania zadania usługi Stream Analytics. Schemat właściwości różni się w zależności od typu zdarzenia. Obecnie firma Microsoft ma następujące dzienniki wykonywania:

### <a name="data-errors"></a>Błędy danych

Wszelkie błędy występujące podczas zadania przetwarzania danych jest w tej kategorii dzienników. Te dzienniki w większości przypadków są tworzone podczas odczytu, danych serializacji, operacji i zapisu. Dzienniki te nie zawierają błędy związane z łącznością. Błędy łączności są traktowane jako zdarzenia ogólne.

Name (Nazwa) | Opis
------- | -------
Element źródłowy | Nazwa zadania danych wejściowych lub wyjściowych, w którym wystąpił błąd.
Komunikat | Komunikat skojarzony z powodu błędu.
Typ | Typ błędu. Na przykład **DataConversionError**, **CsvParserError**, lub **ServiceBusPropertyColumnMissingError**.
Dane | Zawiera dane, które są przydatne do dokładnie zlokalizować źródła błędu. Z zastrzeżeniem obcięcie, w zależności od rozmiaru.

W zależności od **operationName** wartości błędów danych mają zgodny z następującym schematem:
* **Serializacja zdarzeń**. Serializowanie zdarzenia występują w trakcie zdarzenia, operacje odczytu. Występują, gdy dane na dane wejściowe nie spełnia schematu zapytania dla jednego z następujących powodów:
    * *Niezgodność typów podczas zdarzenia (de) serializacji*: Określa pole, które powoduje błąd.
    * *Nie można odczytać zdarzenie, nieprawidłowe serializacja*: Wyświetla informacje o lokalizacji danych wejściowych, w którym wystąpił błąd. Zawiera nazwę obiektu blob dla obiektu blob danych wejściowych, przesunięcie i przykładowych danych.
* **Wysyłanie zdarzeń**. Wysyłanie zdarzenia występują podczas operacji zapisu. Identyfikują one wydarzenia przesyłania strumieniowego, które spowodowały błąd.

### <a name="generic-events"></a>Zdarzenia ogólne

Zdarzenia ogólne obejmuje wszystkie inne elementy.

Name (Nazwa) | Opis
-------- | --------
Błąd | (opcjonalnie) Informacje o błędzie. Zazwyczaj jest to informacje o wyjątku, jeśli jest ona dostępna.
Komunikat| Komunikat dziennika.
Typ | Typ komunikatu. Mapuje do wewnętrznego Kategoryzacja błędów. Na przykład **JobValidationError** lub **BlobOutputAdapterInitializationFailure**.
Identyfikator korelacji | [Identyfikator GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , który jednoznacznie identyfikuje wykonywania zadania. Wszystkie wpisy dziennika wykonywania od momentu zadanie zostało uruchomione, dopóki zadanie zostanie zatrzymane, mające taką samą **identyfikator korelacji** wartość.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
