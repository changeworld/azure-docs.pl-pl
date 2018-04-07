---
title: Azure Stream Analytics — informacje o wersji
description: W tym artykule opisano Historia wersji usługi Azure Stream Analytics i odpowiednie narzędzia programu Visual Studio.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/03/2017
ms.openlocfilehash: b5f6f4f42929127521320e56bcc9b36c324cde89
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="stream-analytics-release-notes"></a>Informacje o wersji usługi analiza strumienia

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Informacje o aktualizacji 06/14/2017 Stream Analytics narzędzi dla programu Visual Studio
Ta aktualizacja dotyczy programu Visual Studio Tools. Ta wersja zawiera następujące nowe funkcje:

| Stanowisko | Opis |
| --- | --- |
| Obsługa Edytor skryptów Java |Pozwala korzystać środowiska java natywnego skryptu edytora po utworzeniu skryptu funkcje programu java.|
| Błąd czasu wykonywania zadania, widok | Jeśli występują błędy podczas wykonywania podczas wykonywania zadania, można wyświetlić na karcie błędy, dopasowując okna czasu. Domyślnie zawiera komunikaty o błędach w ciągu ostatnich 30 minut. |
| CSV i Avro obsługuje reakcję testowania lokalnego | Oprócz JSON teraz służy format pliku CSV i Avro reakcję testowania lokalnego.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Informacje o aktualizacji 05/03/2017 analiza strumienia
Ta aktualizacja jest w wersji dokumentacji rozwiązywania problemów.

[Przewodnik rozwiązywania problemów](stream-analytics-troubleshooting-guide.md) i inne dokumenty zostały wydane. Przejrzyj tego przewodnika, a Twoja opinia jest powitalnej.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Informacje o aktualizacji 2017-04/24 Stream Analytics narzędzi dla programu Visual Studio
Ta aktualizacja dotyczy programu Visual Studio Tools. Ta wersja zawiera następujące nowe funkcje:

| Stanowisko | Opis |
| --- | --- |
| Wynik testu lokalnego widoku w programie Visual Studio | Aby wyświetlić dane wyjściowe wynik testu lokalnego, po prostu naciśnij klawisz ENTER w oknie dane wyjściowe konsoli lub go zamknąć. Wynik jest wyświetlany w oknie programu Visual Studio w formacie tabeli. |
| Wynik lokalnych danych wyjściowych w formacie JSON | Podczas uruchamiania lokalnego testu wynik dane wyjściowe są generowane, zgodnie z formatów plików JSON i CSV. |
| Wyświetl podgląd danych wejść/wyjść magazynu obiektów Blob/tabeli | Dwukrotne kliknięcie w magazynie obiektów blob lub tabeli wejścia/wyjścia w widoku zadania, można łatwo podglądu danych w programie Visual Studio. |
| Wyświetl komunikat o błędzie dla wejścia/wyjścia | W razie błędu czasu wykonywania dotyczące wejściowych i wyjściowych zadania, jest wyświetlany na diagramie zadania, których możesz najechać Aby wyświetlić szczegółowy komunikat o błędzie.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Informacje o wersji 2017-02/01 usługi analiza strumienia
Ta wersja zawiera następującą aktualizację:

| Stanowisko | Opis |
| --- | --- |
| Wprowadzenie do funkcji JavaScript zdefiniowane przez użytkownika (UDF) |[Funkcje zdefiniowane przez użytkownika Java](stream-analytics-javascript-user-defined-functions.md) są teraz dostępne dla dodatkowa elastyczność tworzenia zapytań. |
| Wprowadzenie do narzędzi Visual Studio i analiza strumienia |[Narzędzia dla programu Visual Studio](stream-analytics-tools-for-visual-studio.md) są teraz dostępne dla narzędzia debugowania i większa. |
| Wprowadzenie do rejestrowania diagnostycznego |[Rejestrowanie diagnostyczne](stream-analytics-job-diagnostic-logs.md) jest teraz dostępna w dodatkowych opcji rozwiązywania problemów. |
| Wprowadzenie do funkcji lokalizacji geograficznych |[Funkcje dane geograficzne](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) są teraz ogólnie dostępna. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Informacje o wersji usługi Stream Analytics 2016-04-15
Ta wersja zawiera następującą aktualizację:

| Stanowisko | Opis |
| --- | --- |
| Ogólnie dla wyjść usługi Power BI |[Power BI wyjść](stream-analytics-power-bi-dashboard.md) są teraz ogólnie dostępna. Po upływie 90-dniowy autoryzacji dla usługi Power BI została usunięta. Aby uzyskać więcej informacji na scenariuszach, w którym wymaga autoryzacji odnowienia, zobacz [odnowić autoryzacji](stream-analytics-power-bi-dashboard.md#renew-authorization) sekcji Tworzenie pulpitu nawigacyjnego usługi Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Informacje o wersji 2016-03-03 usługi analiza strumienia
Ta wersja zawiera następującą aktualizację:

| Stanowisko | Opis |
| --- | --- |
| Nowe elementy język zapytań usługi analiza strumienia |Ma teraz SAQL [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "strony MSDN GetType"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "strony MSDN TRY_CAST") i [funkcji REGEXMATCH] (https://msdn.microsoft.com/library/azure/mt643891.aspx "Strony MSDN funkcji REGEXMATCH"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Informacje o wersji 12/10/2015 usługi analiza strumienia
Ta wersja zawiera następującą aktualizację:

| Stanowisko | Opis |
| --- | --- |
| Aktualizacja wersji interfejsu API REST |Wersja interfejsu API REST został zaktualizowany do 2015-10-01. Szczegółowe informacje można znaleźć w witrynie MSDN w [dokumentacja interfejsu API REST zarządzania usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn835031.aspx) i [uczenia maszynowego integracji usługi Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Azure Machine Learning integracji |W tej wersji zawiera obsługę Azure Machine Learning funkcje zdefiniowane przez użytkownika. Aby uzyskać więcej informacji, zobacz [samouczek](stream-analytics-machine-learning-integration-tutorial.md) i [anonsu ogólne blogu](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Informacje o wersji 2015-11-12 usługi analiza strumienia
Ta wersja zawiera następującą aktualizację:

| Stanowisko | Opis |
| --- | --- |
| Nowe zachowanie wybierz |Wybierz pozycję analiza strumienia został rozszerzony umożliwia * jako metody dostępu właściwości zagnieżdżonych rekordu. Aby uzyskać więcej informacji, zobacz [ http://msdn.microsoft.com/library/mt622759.aspx ] (http://msdn.microsoft.com/library/mt622759.aspx "złożone typy danych"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Informacje o wersji 2015-10-22 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Dodatkowe funkcje językowe |Analiza strumienia ma rozwinięty język zapytań poprzez włączenie następujących funkcji: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [limitu](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [ POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [znak](https://msdn.microsoft.com/library/azure/mt605290.aspx), [kwadratowe](https://msdn.microsoft.com/library/azure/mt605288.aspx), i [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Usunięto ograniczenia agregacji |Ta wersja usuwa ograniczenie 15 agregacji w zapytaniu. Obecnie nie ma żadnego limitu liczby agregacji dla kwerendy. |
| Dodano funkcję grupy przez System.Timestamp |[GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) funkcja umożliwia teraz albo window_type lub [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Dodano przesunięcie wirowania i skaczące systemu windows |Domyślnie [wirowania](https://msdn.microsoft.com/library/azure/dn835055.aspx) i [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) systemu windows są wyrównane względem czas wynosi zero (1/1/0001 UTC 00:00:00: 00). Nowy parametr (opcjonalny) `offsetsize` umożliwia określanie niestandardowych przesunięcie (lub wyrównanie). |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Informacje o wersji 2015-09-29 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Azure IoT Suite Public Preview |Analiza strumienia znajduje się w publicznej wersji zapoznawczej pakietu IoT Azure. |
| Integracja portalu Azure |Oprócz dalszą obecność w portalu Azure Stream Analytics jest teraz zintegrowany w [portalu Azure](https://azure.microsoft.com/overview/preview-portal/). Należy pamiętać, że funkcje usługi Stream Analytics w portalu w wersji zapoznawczej obecnie podzbiór funkcji oferowane w portalu Azure, bez obsługi testowanie zapytań w przeglądarce, usługa Power BI output konfiguracji i przeglądania lub Utwórz nowe dane wejściowe i wyjściowe Subskrypcje, do których masz dostęp do zasobów. |
| Obsługa rozwiązania Cosmos bazy danych wyjściowych |Teraz można danych wyjściowych do zadania usługi analiza strumienia [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/documentdb/). |
| Obsługa danych wejściowych Centrum IoT |Zadania usługi analiza strumienia może teraz obsługiwać danych z centra IoT. |
| TIMESTAMP BY heterogenicznych zdarzeń |Jeśli jeden strumień zawiera wiele typów zdarzeń muszą być polami sygnatury czasowe, można teraz używać [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) z wyrażenia, aby określić inną sygnaturą czasową pól w każdym przypadku. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Informacje o wersji 2015-09-10 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Obsługa grup usługi Power BI |Aby włączyć udostępniania danych innym użytkownikom usługi Power BI, zadania usługi analiza strumienia może teraz zapisać [grup usługi Power BI](stream-analytics-define-outputs.md#power-bi) wewnątrz koncie usługi Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Informacje o wersji 2015-08-20 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Dodaje OSTATNIEJ funkcji |[Ostatniego](http://msdn.microsoft.com/library/mt421186.aspx) funkcja jest teraz dostępna w zadania usługi analiza strumienia, dzięki któremu można pobrać najnowszych zdarzeń w strumieniu zdarzeń w danym przedziale czasu. |
| Nowe funkcje tablicy |Tablica funkcji [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) i [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) są teraz dostępne. |
| Nowe funkcje rekordów |Funkcje rejestrowania [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) i [funkcji GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) są teraz dostępne. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Informacje o wersji 2015-07-30 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Power BI pomocą identyfikatora organizacji całkowicie niezależna od identyfikator platformy Azure |Ta funkcja umożliwia [wyjście usługi Power BI](stream-analytics-power-bi-dashboard.md) ASA zadania w obszarze dowolnego typu konta platformy Azure (identyfikator organizacji lub Live Id). Ponadto możesz mieć jeden identyfikator organizacji dla konta platformy Azure i użyć różnych autoryzacji wyjście usługi Power BI. |
| Obsługa kolejek usługi Service Bus w danych wyjściowych |[Kolejek usługi Service Bus](stream-analytics-define-outputs.md#service-bus-queues) dane wyjściowe są teraz dostępne w zadania usługi analiza strumienia. |
| Obsługa danych wyjściowych tematów usługi Service Bus |[Tematów usługi Service Bus](stream-analytics-define-outputs.md#service-bus-topics) dane wyjściowe są teraz dostępne w zadania usługi analiza strumienia. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Informacje o wersji 2015-07-09 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Niestandardowe obiektu Blob partycjonowania danych wyjściowych |Dane wyjściowe z magazynu obiektów blob zezwala teraz opcję, aby określić częstotliwość te dane wyjściowe, które obiekty BLOB są zapisywane i struktury i format struktury folderów ścieżka danych wyjściowych. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Informacje o wersji 2015-05-03 usługi analiza strumienia
Ta wersja zawiera następujące aktualizacje:

| Stanowisko | Opis |
| --- | --- |
| Zwiększona wartość maksymalną dla porządku poza tolerancji |Maksymalny rozmiar tolerancji Out of kolejności jest teraz 59:59 (mm: ss) |
| Format danych wyjściowych JSON: Rozdzielone lub tablicy |Obecnie ma opcji dla magazynu obiektów Blob lub Centrum zdarzeń, aby dane wyjściowe jako albo tablica obiektów JSON lub rozdzielając obiektów JSON znakiem nowego wiersza. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Informacje o wersji 2015-04-16 usługi analiza strumienia
| Stanowisko | Opis |
| --- | --- |
| Opóźnienie w konfiguracji konta usługi Azure Storage |Podczas tworzenia zadania usługi analiza strumienia w regionie po raz pierwszy, wyświetlany jest monit o utworzenie nowego konta magazynu lub określenie istniejącego konta do monitorowania zadania usługi analiza strumienia, w tym regionie. Z powodu opóźnienia w konfigurowaniu monitorowania, tworzenie inne zadanie usługi analiza strumienia w tym samym regionie, w ciągu 30 minut wiersza do określenia drugiego konta magazynu, zamiast przedstawiający ostatnio skonfigurowany jeden w listy rozwijanej konto magazynu monitorowania. Aby uniknąć tworzenia niepotrzebnych konta magazynu, Odczekaj 30 minut po utworzeniu zadania w obszarze po raz pierwszy przed zainicjowaniem obsługi administracyjnej dodatkowych zadań, w tym regionie. |
| Zadanie uaktualnienia |W tej chwili Stream Analytics nie obsługuje edycji na żywo do definicji lub konfiguracji uruchomionego zadania. Aby zmienić dane wejściowe, dane wyjściowe, zapytania, skali lub konfiguracji uruchomionego zadania, należy zatrzymać zadanie. |
| Typy danych wywnioskować ze źródła danych wejściowych |Jeśli w instrukcji CREATE TABLE nie jest używany, typ danych wejściowych jest pochodną format wejściowy, na przykład wszystkie pola z pliku CSV są ciągu. Jawnie przekonwertować pola odpowiedniego typu przy użyciu funkcji CAST w celu uniknięcia błędy niezgodności typów. |
| Brakujące pola są wyjściowych jako wartości null |Odwołanie do pola, które nie jest obecny w źródła danych wejściowych powoduje wartości null w przypadku danych wyjściowych. |
| W instrukcjach musi poprzedzać instrukcji "SELECT" |W zapytaniu podzapytania zdefiniowany za pomocą instrukcji musi następować po instrukcji "SELECT". |
| Problem braku pamięci |Ponowne uruchomienie zadań przesyłania strumieniowego Analytics z tolerancją duża dla zdarzenia poza kolejnością i/lub złożonych zapytań obsługi dużej liczby stanu może spowodować uruchomienie za mało pamięci, co w przypadku zadania. Uruchamianie i zatrzymywanie operacji są widoczne w dzienniki operacji zadania. Aby uniknąć tego zachowania, skalowanie w poziomie zapytania między wieloma partycjami. W przyszłym wydaniu to ograniczenie dotyczy zmniejszenie wydajności na wpływ na zadania zamiast ponownego uruchamiania ich. |
| Dane wejściowe dużych obiektów blob bez sygnatury czasowej ładunek może spowodować problem braku pamięci |Zużywa duże pliki z magazynu obiektów Blob może spowodować zadania usługi analiza strumienia do awarii, jeśli nie określono pola sygnatury czasowej za pośrednictwem TIMESTAMP BY. Aby uniknąć tego problemu, należy każdy obiekt blob poniżej 10 MB rozmiar. |
| Ograniczenie woluminu zdarzeń bazy danych SQL |Podczas korzystania z bazy danych SQL jako miejsce docelowe danych wyjściowych, dużych ilości danych wyjściowych może spowodować zadanie usługi Stream Analytics limitu czasu. Aby rozwiązać ten problem, zmniejszyć ilość danych wyjściowych za pomocą wartości zagregowanych lub operatory filtru lub zamiast tego wybrać magazynu obiektów Blob platformy Azure lub usługi Event Hubs jako miejsce docelowe danych wyjściowych. |
| Zestawy danych usługi Power BI może zawierać tylko jedną tabelę |Usługa Power BI nie obsługuje więcej niż jednej tabeli w danym zestawie danych. |

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, wypróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
