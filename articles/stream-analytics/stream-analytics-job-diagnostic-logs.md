---
title: Rozwiązywanie problemów z usługą Azure Stream Analytics z dzienników diagnostycznych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i analizowanie dzienników diagnostycznych z zadania usługi analiza strumienia w Microsoft Azure.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 164d522d7beaea222dbc408765877fa67a34c203
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych

Czasami zadanie usługi analiza strumienia Azure nieoczekiwanie zatrzymuje przetwarzanie. Jest ważne można było rozwiązać tego rodzaju zdarzenia. Zdarzenie może być spowodowany przez wynik nieoczekiwane zapytanie, łączność urządzeń lub awaria usługi nieoczekiwany. Dzienniki diagnostyczne w Stream Analytics może pomóc w ustaleniu przyczyn problemów podczas wystąpienia i skrócić czas odzyskiwania.

## <a name="log-types"></a>Typy dziennika

Analiza strumienia oferuje dwa typy dzienników: 
* [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (zawsze włączona). Dzienniki aktywności zapewniają wgląd w operacji wykonywanych zadań.
* [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (można konfigurować). Dzienniki diagnostyczne wgląd bardziej rozbudowane w wszystko, co się dzieje z zadaniem. Diagnostyka rejestruje start po utworzeniu zadania i zakończenia, gdy zadanie zostanie usunięta. Obejmują one zdarzeń, gdy zadanie zostanie zaktualizowany i jest uruchomiona.

> [!NOTE]
> Usługi, takie jak magazyn Azure, Azure Event Hubs i analiza dzienników Azure umożliwia analizowanie danych niestandardowych. Naliczane są opłaty oparte na modelu cenowego dla tych usług.
>

## <a name="turn-on-diagnostics-logs"></a>Włączanie dzienników diagnostycznych

Dzienniki diagnostyczne są **poza** domyślnie. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  Zaloguj się do portalu Azure, a następnie przejdź do bloku zadania przesyłania strumieniowego. W obszarze **monitorowanie**, wybierz pozycję **dzienników diagnostycznych**.

    ![Blok nawigacji do dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Wybierz **Włącz diagnostykę**.

    ![Włączanie dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  Na **ustawień diagnostycznych** strony, dla **stan**, wybierz pozycję **na**.

    ![Zmiana stanu dzienników diagnostycznych](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Konfigurowanie archiwizacji docelowego (konto magazynu, Centrum zdarzeń, analizy dzienników), który ma. Wybierz kategorie dzienników, które mają być zbierane (wykonanie, tworzenie). 

5.  Zapisz nową konfigurację diagnostyki.

Konfiguracja diagnostyki trwa około 10 minut zaczęły obowiązywać. Po wykonaniu tej dzienniki start znajdujących się w celu archiwizacji skonfigurowany (można je wyświetlić na **dzienników diagnostycznych** strony):

![Blok nawigacji do dzienników diagnostycznych — archiwizacji elementów docelowych](./media/stream-analytics-job-diagnostic-logs/image4.png)

Aby uzyskać więcej informacji na temat konfigurowania diagnostyki, zobacz [zbierania i wykorzystywania danych diagnostycznych z zasobów platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Kategorie dziennika diagnostycznego

Obecnie możemy przechwytywania dwie kategorie dzienników diagnostycznych:

* **Tworzenie**. Przechwytywanie dziennika zdarzeń, które są związane z zadania tworzenia operacji: zadania tworzenia, dodawania i usuwania danych wejściowych i danych wyjściowych, dodawanie i aktualizowanie kwerendy, uruchamianie i zatrzymywanie zadania.
* **Wykonanie**. Przechwytywania zdarzeń występujących podczas wykonywania zadania:
    * Błędy połączenia
    * Błędy przetwarzania danych, w tym:
        * Zdarzenia, które nie są zgodne z definicji zapytania (typy niezgodne pól i wartości, Brak pól i tak dalej)
        * Wyrażenie oceny błędów
    * Inne zdarzenia i błędy

## <a name="diagnostics-logs-schema"></a>Schemat dzienników diagnostycznych

Wszystkie dzienniki są przechowywane w formacie JSON. Każdy wpis ma następujące typowe pola ciągu:

Name (Nazwa) | Opis
------- | -------
time | Znacznik czasu (w formacie UTC) dziennika.
resourceId | Identyfikator zasobu, że operacja miały miejsce, wielkimi literami. Zawiera identyfikator subskrypcji, grupy zasobów i nazwę zadania. Na przykład   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Dziennika kategorii, albo **wykonywania** lub **tworzenie**.
operationName | Nazwa operacji, które są rejestrowane. Na przykład **wysyłać zdarzenia: błąd zapisu danych wyjściowych SQL do mysqloutput**.
status | Stan operacji. Na przykład **** lub **zakończyło się pomyślnie**.
poziom | Poziom dziennika. Na przykład **błąd**, **ostrzeżenie**, lub **komunikat o charakterze informacyjnym**.
properties | Szczegóły konkretnego wpisu dziennika, zserializowanym w formacie ciągu JSON. Aby uzyskać więcej informacji zobacz następujące sekcje.

### <a name="execution-log-properties-schema"></a>Schemat właściwości dziennika wykonywania

Dzienniki wykonywania ma informacji o zdarzeniach, które wystąpiły podczas wykonywania zadania usługi analiza strumienia. Schemat właściwości różni się zależnie od typu zdarzenia. Mamy obecnie dzienniki wykonywania następujących typów:

### <a name="data-errors"></a>Błędy danych

Wszelkie błędy występujące podczas przetwarzania danych przez zadanie jest w tej kategorii dzienników. Najczęściej te dzienniki są tworzone podczas odczytu, dane serializacji i operacji zapisu. Dzienniki te nie zawierają błędów połączenia. Błędy łączności są traktowane jako zdarzenia ogólne.

Name (Nazwa) | Opis
------- | -------
Element źródłowy | Nazwa zadania wejściowych lub wyjściowych, którym wystąpił błąd.
Komunikat | Komunikat skojarzony z powodu błędu.
Typ | Typ błędu. Na przykład **DataConversionError**, **CsvParserError**, lub **ServiceBusPropertyColumnMissingError**.
Dane | Zawiera dane, które są przydatne do dokładnie zlokalizować źródła błędu. Z zastrzeżeniem obcięcie, w zależności od wielkości.

W zależności od **operationName** wartości błędów danych ma następującego schematu:
* **Serializować zdarzenia**. Serializacji wystąpienia zdarzeń podczas operacji odczytu zdarzenia. Występują, gdy dane w danych wejściowych nie spełnia schematu zapytania dla jednego z następujących powodów:
    * *Niezgodność typów podczas zdarzenia (de) serializować*: Określa pole, które powoduje błąd.
    * *Nie można odczytać zdarzeń, nieprawidłowy serializacji*: Wyświetla informacje o lokalizacji w danych wejściowych, w którym wystąpił błąd. Zawiera nazwę obiektu blob danych wejściowych obiektu blob, przesunięcie oraz przykładowych danych.
* **Wysyłanie zdarzeń**. Wysłanie zdarzenia występują w trakcie operacji zapisu. Identyfikują one zdarzenie przesyłania strumieniowego, który spowodował błąd.

### <a name="generic-events"></a>Zdarzenia ogólne

Zdarzenia ogólne obejmuje wszystkie inne elementy.

Name (Nazwa) | Opis
-------- | --------
Błąd | (opcjonalnie) Informacje o błędzie. Zazwyczaj jest to informacje o wyjątku, jeśli jest dostępna.
Komunikat| Komunikat w dzienniku.
Typ | Typ komunikatu. Mapowany do wewnętrznego kategoryzacji błędów. Na przykład **JobValidationError** lub **BlobOutputAdapterInitializationFailure**.
Identyfikator korelacji | [Identyfikator GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) który unikatowo identyfikuje wykonywania zadania. Wszystkie wpisy dziennika wykonywania od czasu rozpoczęcia zadania aż zadanie zostanie zatrzymane, mieć taki sam **identyfikator korelacji** wartość.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi analiza strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-real-time-fraud-detection.md)
* [Zadania usługi analiza strumienia skali](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytania usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strumienia Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
