---
title: Konfigurowanie zasad zamawiania zdarzeń dla usługi Azure Stream Analytics
description: W tym artykule opisano, jak skonfigurować nawet ustawienia zamawiania w usłudze Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461193"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurowanie zasad zamawiania zdarzeń dla usługi Azure Stream Analytics

W tym artykule opisano sposób konfigurowania i używania zasad późnego przybycia i zdarzeń poza kolejnością w usłudze Azure Stream Analytics. Te zasady są stosowane tylko wtedy, gdy używasz [klauzuli TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) w zapytaniu.

## <a name="event-time-and-arrival-time"></a>Czas wydarzenia i godzina przylotu

Zadanie usługi Stream Analytics może przetwarzać zdarzenia na podstawie *czasu zdarzenia* lub *godziny przybycia.* **Czas zdarzenia/aplikacji** jest sygnaturą czasą obecną w ładunku zdarzeń (gdy zdarzenie zostało wygenerowane). **Czas przyjścia** jest sygnaturą czasową, gdy zdarzenie zostało odebrane w źródle wejściowym (Event Hubs/IoT Hub/Blob storage). 

Domyślnie usługa Stream Analytics przetwarza zdarzenia według *czasu przybycia,* ale można wybrać przetwarzanie zdarzeń według *czasu zdarzenia* przy użyciu klauzuli [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) w zapytaniu. Zasady późnego przyjazdu i poza kolejnością mają zastosowanie tylko wtedy, gdy przetwarzasz zdarzenia według czasu zdarzenia. Podczas konfigurowania tych ustawień należy wziąć pod uwagę wymagania dotyczące opóźnienia i poprawności dla danego scenariusza. 

## <a name="what-is-late-arrival-policy"></a>Co to są zasady spóźnionego przybycia?

Czasami wydarzenia przychodzą późno z różnych powodów. Na przykład zdarzenie, które przychodzi 40 sekund późno będzie miał czas zdarzenia = 00:10:00 i czas przybycia = 00:10:40. Jeśli ustawisz zasady późnego przybycia na 15 sekund, każde zdarzenie, które pojawi się później niż 15 sekund, zostanie usunięte (nie przetworzone przez analizę stream analytics) lub zostanie skorygowane o czas zdarzenia. W powyższym przykładzie, gdy zdarzenie dotarło z 40 sekundem (więcej niż zestaw zasad), jego czas zdarzenia zostanie dostosowany do maksymalnej zasady późnego przyjazdu 00:10:25 (czas przybycia - wartość zasad późnego przyjazdu). Domyślna zasada późnego przyjazdu to 5 sekund.

## <a name="what-is-out-of-order-policy"></a>Co to jest polityka poza kolejnością? 

Zdarzenie może również nastąpić w porządku. Po dostosowaniu czasu zdarzenia na podstawie zasad późnego przyjazdu można również wybrać opcję automatycznego upuszczania lub dostosowywania zdarzeń, które są poza kolejnością. Jeśli ta zasada zostanie ustawiona na 8 sekund, wszystkie zdarzenia, które przychodzą poza kolejnością, ale w 8-sekundowym oknie, zostaną uporządkowane według czasu zdarzenia. Zdarzenia, które pojawią się później, zostaną usunięte lub dostosowane do maksymalnej wartości zasad poza kolejnością. Domyślna zasada poza kolejnością wynosi 0 sekund. 

## <a name="adjust-or-drop-events"></a>Dopasowywanie zdarzeń lub upuszczanie

Jeśli zdarzenia przychodzą późno lub poza kolejnością na podstawie skonfigurowanych zasad, możesz usunąć takie zdarzenia (nie przetworzone przez analizę stream analytics) lub dostosować czas ich zdarzenia.

Zobaczmy przykład tych polityk w działaniu.
<br> **Zasady późnego przyjazdu:** 15 sekund
<br> **Zasady poza kolejnością:** 8 sekund

| Nr zdarzenia | Czas zdarzenia | Godzina przyjazdu | System.Timestamp | Wyjaśnienie |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Zdarzenie dotarło późno i poza poziomem tolerancji. Tak więc czas zdarzenia dostosowuje się do maksymalnej tolerancji późnego przybycia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Zdarzenie dotarło późno, ale w granicach poziomu tolerancji. Więc czas zdarzenia nie jest dostosowany.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Wydarzenie dotarło na czas. Nie jest wymagana żadna regulacja.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Zdarzenie dotarło poza kolejnością, ale w granicach 8 sekund. Tak więc czas zdarzenia nie jest dostosowywany. Do celów analitycznych to zdarzenie będzie traktowane jako poprzedzające zdarzenie o numerze 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Zdarzenie dotarło poza kolejnością i zewnętrzną tolerancją 8 sekund. Tak więc czas zdarzenia jest dostosowany do maksymalnej tolerancji poza kolejnością. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Czy te ustawienia mogą opóźnić wyjście mojej pracy? 

Tak. Domyślnie zasady poza kolejnością jest ustawiona na zero (00 minut i 00 sekund). Jeśli zmienisz wartość domyślną, pierwsze dane wyjściowe zadania są opóźnione o tę wartość (lub większą). 

Jeśli jedna z partycji danych wejściowych nie odbiera zdarzeń, należy oczekiwać, że dane wyjściowe mają być opóźnione przez wartość zasad późnego przybycia. Aby dowiedzieć się, dlaczego, przeczytaj inputpartition sekcji błędu poniżej. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>W moim dzienniku aktywności widzę komunikaty LateInputEvents

Te komunikaty są wyświetlane, aby poinformować, że zdarzenia dotarły późno i są usuwane lub dostosowywane zgodnie z konfiguracją. Można zignorować te komunikaty, jeśli zostały skonfigurowane zasady późnego przybycia odpowiednio. 

Przykładem tego komunikatu jest: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>W dzienniku aktywności widzę informacje InputPartitionNotProgressing

Źródło danych wejściowych (Centrum zdarzeń/IoT Hub) prawdopodobnie ma wiele partycji. Usługa Azure Stream Analytics generuje dane wyjściowe dla sygnatury czasowej t1 tylko po wszystkich partycji, które są połączone są co najmniej w czasie t1. Załóżmy na przykład, że kwerenda odczytuje z partycji centrum zdarzeń, która ma dwie partycje. Jedna z partycji, P1, ma zdarzenia do czasu t1. Druga partycja, P2, ma zdarzenia do czasu t1 + x. Wyjście jest następnie produkowane do czasu t1. Ale jeśli istnieje jawne partition przez PartitionId klauzuli, zarówno partycje postępu niezależnie. 

Gdy wiele partycji z tego samego strumienia wejściowego są połączone, tolerancji późnego przybycia jest maksymalna ilość czasu, że każda partycja czeka na nowe dane. Jeśli istnieje jedna partycja w Centrum zdarzeń lub jeśli Usługa IoT Hub nie odbiera danych wejściowych, oś czasu dla tej partycji nie postępuje, dopóki nie osiągnie progu tolerancji późnego przybycia. Opóźnia to wyjście o próg tolerancji późnego przybycia. W takich przypadkach może zostać wyświetlony następujący komunikat:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ten komunikat informuje, że co najmniej jedna partycja w danych wejściowych jest pusta i opóźni dane wyjściowe o próg późnego przybycia. Aby temu zażepnąć, zaleca się:  
1. Upewnij się, że wszystkie partycje usługi Event Hub/IoT Hub odbierać dane wejściowe. 
2. Użyj partition by PartitionID klauzuli w kwerendzie. 

## <a name="next-steps"></a>Następne kroki
* [Zagadnienia dotyczące obsługi czasu](stream-analytics-time-handling.md)
* [Dane dostępne w usłudze Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
