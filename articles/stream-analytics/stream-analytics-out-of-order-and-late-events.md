---
title: Obsługa kolejność zdarzeń i opóźnienie w Azure Stream Analytics
description: W tym artykule opisano, jak analiza strumienia obsługuje zdarzenia poza kolejnością lub opóźnień w strumieniach danych.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: e407a95d3ac858ea7180a75f9fbfc399860ad378
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Zagadnienia dotyczące usługi Azure Stream Analytics zdarzeń kolejności

## <a name="arrival-time-and-application-time"></a>Godzina nadejścia i czas aplikacji

W strumieniu danych czasowych zdarzeń każdego zdarzenia jest przypisane sygnatury czasowej. Usługa Azure Stream Analytics przypisuje sygnatury czasowej każdego zdarzenia przy użyciu przyjęcia czasu albo czasu aplikacji. **System.Timestamp** kolumna zawiera sygnaturę czasową przypisany do zdarzenia. 

Godzina nadejścia jest przypisywany w źródła danych wejściowych, gdy zdarzenie osiągnie źródła. Godzina nadejścia mogą korzystać za pomocą **EventEnqueuedTime** właściwości dla wejścia Centrum zdarzeń i przy użyciu [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) właściwości dla obiektu blob danych wejściowych. 

Czas aplikacji jest przypisany, gdy zostanie wygenerowane zdarzenie i jest częścią ładunku. Aby przetwarzać zdarzenia według czasu aplikacji, należy użyć **sygnatury czasowej przez** klauzuli w wykonywania zapytania select. Jeśli **sygnatury czasowej przez** klauzula jest nieobecny, zdarzenia są przetwarzane przez Godzina nadejścia. 

Usługa Azure Stream Analytics generuje dane wyjściowe w kolejności sygnaturę czasową i udostępnia ustawienia radzenia sobie z danych poza kolejnością.


## <a name="handling-of-multiple-streams"></a>Obsługa wielu strumieni

Zadanie usługi analiza strumienia Azure łączy zdarzenia z wielu osi czasu w przypadkach, takich jak następujące:

* Generuje danych wyjściowych z wielu partycji. Zapytania, które nie mają jawnie **partycji według identyfikatora PartitionId** klauzuli połączyć zdarzenia ze wszystkich partycji.
* Złożenie dwóch lub więcej różnych źródeł danych wejściowych.
* Sprzęganie źródeł danych wejściowych.

W scenariuszach, w którym są łączone wiele osi czasu, analiza strumienia Azure generuje dane wyjściowe dla sygnatury czasowej *t1* tylko wtedy, gdy wszystkie źródła, które są łączone są co najmniej w czasie *t1*. Na przykład przypuśćmy, że zapytanie odczytuje z partycji Centrum zdarzeń, które ma dwie partycje. Jedną z partycji, *P1*, ma zdarzeń do czasu *t1*. Partycja innych *P2*, ma zdarzeń do czasu *t1 + x*. Następnie wygenerowania danych wyjściowych do czasu *t1*. Ale w przypadku jawnego **partycji według identyfikatora PartitionId** klauzuli zarówno partycje postępu niezależnie.

Ustawienie późne tolerancji przyjęcia służy do postępowania w przypadku braku danych w niektórych partycji.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Konfigurowanie późne tolerancji przyjęcia i odporność na awarie poza kolejnością.
Strumienie wejściowe, które nie znajdują się w kolejności są:
* Sortowane (i w związku z tym opóźnienie)
* Skorygowane przez system, zgodnie z zasadami określonymi przez użytkownika

Analiza strumienia zaakceptować późne i poza kolejnością zdarzenia, gdy jest przetwarzanie czasu aplikacji. Następujące ustawienia są dostępne w **kolejność zdarzeń** opcji w portalu Azure: 

![Obsługa zdarzeń usługi analiza strumienia](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Późne tolerancji przyjęcia
Późne tolerancji przyjęcia ma zastosowanie tylko wtedy, gdy jest przetwarzanie czasu aplikacji. W przeciwnym razie ustawienie jest ignorowane.

Późne tolerancja przyjęcia to maksymalna różnica między Godzina nadejścia i czas aplikacji. Jeśli zdarzenie dociera później niż późne tolerancja przybyciu (na przykład czas aplikacji *app_t* < Godzina nadejścia *arr_t* -późne tolerancji zasad przyjęcia *late_t*), zdarzenia jest dostosowana do maksymalnie późne tolerancja przybyciu (*arr_t* - *late_t*). Późne okno odbioru jest maksymalne opóźnienie od wygenerowania zdarzenia do przyjęcia zdarzenia w źródła danych wejściowych. 

Gdy są łączone wiele partycji z tego samego strumienia wejściowego lub wiele strumieni wejściowych późne tolerancji przyjęcia jest maksymalną ilość czasu oczekiwania każdej partycji dla nowych danych. 

Dostosowanie oparte na opóźnione tolerancji przyjęcia odbywa się najpierw. Następną korekty oparte na uszkodzenia poza kolejnością. **System.Timestamp** kolumna zawiera sygnaturę czasową końcowego przypisany do zdarzenia.

### <a name="out-of-order-tolerance"></a>Tolerancja poza kolejnością.
Zdarzenia, które pojawiają się poza kolejnością, ale w ramach zestawu poza kolejnością tolerancji zostaną ponownie uporządkowane według sygnatury czasowej. Zdarzenia przychodzące później niż tolerancji są:
* **Dostosowana**: dostosowana do wydają się przybyły najnowszych akceptowalnego czasu. 
* **Porzucone**: odrzucone.

Gdy analiza strumienia zmienia kolejność zdarzeń, które są odbierane poza kolejnością tolerancji, wyniki kwerendy jest opóźniony o tolerancji poza kolejnością.

### <a name="early-events"></a>Wczesne zdarzenia
Podczas przetwarzania aplikacji czasu, zdarzenia, których czas aplikacji ma więcej niż 5 minut przed ich Godzina nadejścia są usuwane lub dostosowywane opcji konfiguracji.

### <a name="example"></a>Przykład

* Późne tolerancji przyjęcia = 10 minut<br/>
* Tolerancja poza kolejnością = 3 minuty<br/>
* Przetwarzanie przez czas aplikacji<br/>
* Wydarzenia:
   1. **Czas aplikacji** = 00:00:00, **Godzina nadejścia** = 00:10:01 **System.Timestamp** = 00:00:01, dostosowane, ponieważ (**Godzina nadejścia — czas aplikacji**) jest więcej niż późne tolerancji przyjęcia.
   2. **Czas aplikacji** = 00:00:01 **Godzina nadejścia** = 00:10:01 **System.Timestamp** = 00:00:01, nie dostosowana, ponieważ czas aplikacji jest opóźnione okna odbioru.
   3. **Czas aplikacji** = 00:10:00, **Godzina nadejścia** = 00:10:02 **System.Timestamp** = 00:10:00, nie dostosowana, ponieważ czas aplikacji jest opóźnione okna odbioru.
   4. **Czas aplikacji** = 00:09:00, **Godzina nadejścia** = 00:10:03 **System.Timestamp** = 00:09:00, zaakceptowane z oryginalnego sygnaturę czasową, ponieważ czas aplikacji w ramach poza kolejnością Tolerancja.
   5. **Czas aplikacji** = 00:06:00, **Godzina nadejścia** = 00:10:04 **System.Timestamp** = 00:07:00, dostosowane, ponieważ czas aplikacji jest starsza niż tolerancja poza kolejnością.

### <a name="practical-considerations"></a>Kwestie praktyczne
Jak wspomniano wcześniej, późne tolerancji przyjęcia jest maksymalną różnicę czasu aplikacji i Godzina nadejścia. Po jest przetwarzanie przez czas aplikacji zdarzenia, które są nowsze niż z późnym tolerancją przyjęcia zostaną skorygowane przed zastosowaniem tolerancja poza kolejnością. Tak minimalnym późne tolerancji przyjęcia i odporność poza kolejnością jest obowiązującej poza kolejnością.

Zdarzenia poza kolejnością w strumieniu przyczyny:
* Zegara między nadawcy.
* Zmienne opóźnienie między nadawcą i źródło zdarzenia wejściowe.

Późne przyjęcia przyczyny:
* Nadawców przetwarzania wsadowego i wysyłania zdarzeń dla interwału później, po upływie interwału.
* Opóźnienie między wysyłanie zdarzeń przez nadawcę i odbieranie zdarzeń na źródła danych wejściowych.

Podczas konfiguracji późne uszkodzenia przyjęcia i tolerancję poza kolejnością dla danego zadania, należy wziąć pod uwagę poprawności, opóźnienia i poprzedniego czynników.

Poniżej przedstawiono kilka przykładów.

#### <a name="example-1"></a>Przykład 1 
Kwerenda ma **partycji według identyfikatora PartitionId** klauzuli. W ramach jednej partycji zdarzenia są wysyłane za pomocą metod synchronicznych wysyłania. Synchroniczne wysłać bloku metody do momentu zdarzenia są wysyłane.

W takim przypadku poza kolejnością wynosi zero, ponieważ zdarzenia są wysyłane w kolejności z jawnym potwierdzenie przed wysłaniem następne zdarzenie. Późne przyjęcia jest maksymalne opóźnienie między generowania zdarzenia i wysyła zdarzenia, a także maksymalnego czasu oczekiwania między nadawcą i źródła danych wejściowych.

#### <a name="example-2"></a>Przykład 2
Kwerenda ma **partycji według identyfikatora PartitionId** klauzuli. W ramach jednej partycji zdarzenia są wysyłane za pośrednictwem metody asynchronicznego wysyłania. Metody asynchronicznego wysyłania można inicjować wielu wysyła w tym samym czasie, co może powodować zdarzenia poza kolejnością.

W takim przypadku przyjęcia zarówno poza kolejnością i późne są co najmniej maksymalne opóźnienie między generowania zdarzenia i wysyła zdarzenia, a także maksymalnego czasu oczekiwania między nadawcą i źródła danych wejściowych.

#### <a name="example-3"></a>Przykład 3
Zapytanie nie ma **partycji według identyfikatora PartitionId** klauzuli, i czy istnieją co najmniej dwie partycje.

Konfiguracja jest taka sama jak przykład 2. Jednak brak danych w jednej partycji można opóźnić danych wyjściowych przez dodatkowe późne przyjęcia tolerancji.

## <a name="handling-event-producers-with-differing-timelines"></a>Obsługa producentów zdarzeń z różnych osi czasu
Strumień pojedyncze zdarzenie wejściowe często zawiera zdarzenia, które pochodzą od wielu producentów zdarzeń, takich jak poszczególnych urządzeń. Zdarzenia te mogą dotrzeć poza kolejnością z powodu omówionych wcześniej przyczyn. W tych scenariuszach chociaż pogorszenie przez producentów zdarzeń może być duży, pogorszenie w zdarzeniach z pojedynczego producenta jest mały (lub nawet nieistniejącego).

Usługa Azure Stream Analytics zawiera ogólne mechanizmy zajmujących się zdarzenia poza kolejnością. Tych mechanizmów doprowadzi do przetwarzania opóźnień (podczas oczekiwania na straggling zdarzenia do osiągnięcia system), porzucić lub dostosowywana zdarzenia lub oba.

Jeszcze w wielu scenariuszach żądanego zapytania przetwarzania zdarzeń z różnych zdarzeń producentów niezależnie. Na przykład go może być agregowanie zdarzeń na okna na urządzenie. W takich przypadkach jest niepotrzebna opóźnienia umożliwiająca producent jedno zdarzenie podczas oczekiwania na innych producentów zdarzeń uwzględnić dane wyjściowe. Innymi słowy jest niepotrzebna radzenia sobie z pochylenia między producentami czasu. Można je zignorować.

Oczywiście oznacza to, że zdarzeniach wyjściowych, same są poza kolejnością względem ich sygnatury czasowe. Podrzędne konsument musi mieć możliwość postępowania w przypadku takiego zachowania. Ale każde zdarzenie w danych wyjściowych jest poprawna.

Usługa Azure Stream Analytics implementuje tę funkcję za pomocą [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) klauzuli.

## <a name="summary"></a>Podsumowanie
* Skonfiguruj późne tolerancji przyjęcia i okno poza kolejnością na podstawie prawidłowości i opóźnień. Należy również rozważyć, jak zdarzenia są wysyłane.
* Zaleca się, że poza kolejnością na uszkodzenia jest mniejszy niż późne tolerancji przyjęcia.
* Podczas łączenia wielu osi czasu, Brak danych w jednym źródeł lub partycji może opóźnić danych wyjściowych przez dodatkowe późne przyjęcia tolerancji.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-real-time-fraud-detection.md)
* [Zadania usługi analiza strumienia skali](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytania usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strumienia Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
