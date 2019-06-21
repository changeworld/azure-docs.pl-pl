---
title: Konfigurowanie kolejności zasad usługi Azure Stream Analytics zdarzeń
description: W tym artykule opisano sposób wykonywania o konfigurowaniu nawet porządkowanie ustawień w usłudze Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 47a8ee2c03e67d4fd9b34888430ed0cc702205f6
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273176"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurowanie kolejności zasad usługi Azure Stream Analytics zdarzeń

W tym artykule opisano, jak skonfigurować i stosować późnego przybycia i zasad zdarzeń poza kolejnością w usłudze Azure Stream Analytics. Te zasady są stosowane tylko wtedy, gdy używasz [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) klauzuli w zapytaniu.

## <a name="event-time-and-arrival-time"></a>Czas trwania zdarzenia i czas nadejścia

Zadanie usługi Stream Analytics może przetwarzać zdarzenia na podstawie *czas trwania zdarzenia* lub *Godzina nadejścia*. **Czas zdarzenia/aplikacji** jest sygnatura czasowa w ładunku zdarzenia (podczas generowania zdarzenia). **Godzina nadejścia** jest to sygnatura czasowa, gdy otrzymano zdarzenia w źródle danych wejściowych (Event Hubs/IoT Hub/obiekt Blob storage). 

Domyślnie, Stream Analytics może przetwarzać zdarzenia według *Godzina nadejścia*, ale użytkownik może przetwarzać zdarzenia według *czas trwania zdarzenia* przy użyciu [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) klauzuli w zapytaniu. Późnego przybycia i poza kolejnością zasady są stosowane tylko wtedy, gdy przetwarzania zdarzeń przez czas trwania zdarzenia. Podczas konfigurowania tych ustawień, należy wziąć pod uwagę czas oczekiwania i poprawności wymagań dla danego scenariusza. 

## <a name="what-is-late-arrival-policy"></a>Co to jest późnego przybycia zasady?

Czasami zdarzenia późne pojawiają się z różnych powodów. Na przykład zdarzenie przychodzący 40 sekund opóźnienia będzie mieć czas trwania zdarzenia = 00:10:00 i przybycia czas = 00:10:40. Jeśli zasady późnego przybycia jest ustawiona na 15 sekund, dowolne zdarzenie, dostarczane później niż 15 sekund zostanie albo usunięty (nie przetwarzane przez usługę Stream Analytics) lub dostosowanie ich czas trwania zdarzenia. W powyższym przykładzie zdarzenia dostarczeniu 40 sekund opóźnienia (więcej niż zestaw zasad), jego czas zdarzenia zostaną dostosowane do maksymalnej liczby późnego przybycia zasad 00:10:25 (przybycia - późnego przybycia zasad wartość godziny). Domyślne późnego przybycia zasady to 5 sekund.

## <a name="what-is-out-of-order-policy"></a>Co to są zasady poza kolejnością? 

Zdarzenie może pojawić się poza kolejnością także. Po czas zdarzeń jest uwzględniany w oparciu o późnego przybycia zasad, można też automatycznie porzucić lub dostosować zdarzeń, które są poza kolejnością. Jeśli musisz ustawić te zasady do 8 sekund wszelkie zdarzenia, które pojawić się poza kolejnością, ale w obrębie okna 8-sekundowe zostaną ponownie uporządkowane według czasu zdarzeń. Zdarzenia odbierane później zostaną porzucone lub dostosowana do wartości maksymalnej zasady poza kolejnością. Zasada poza kolejnością domyślną jest 0 sekund. 

## <a name="adjust-or-drop-events"></a>Dopasuj lub upuszczania

W przypadku odebrania zdarzeń późno lub poza kolejnością na podstawie zasad, które zostały skonfigurowane, możesz usunąć takie zdarzenia (nie przetwarzane przez usługę Stream Analytics) lub ustawić ich czas trwania zdarzenia dostosowane.

Poinformuj nas zobaczyć przykład te zasady w akcji.
<br> **Późnego przybycia zasady:** 15 sekund
<br> **Poza kolejnością zasady:** 8 sekund

| Nie zdarzenie. | Czas trwania zdarzenia | Godzina nadejścia | System.Timestamp | Wyjaśnienie |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Zdarzenie dostarczone poziom tolerancji późnego i na zewnątrz. Dlatego maksymalna tolerancja spóźnionego przybycia uwzględnieniem pobiera czas trwania zdarzenia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Zdarzenie już korzystać z opóźnieniem, ale w obrębie poziom tolerancji. Czas trwania zdarzenia nie uzyskać ustawiony w taki sposób.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Zdarzenie dotarła na czas. Nie potrzebnych dostosowań.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Zdarzenie dostarczone poza kolejnością, ale w granicach tolerancji 8 sekund. Tak czas trwania zdarzenia nie uzyskać dostosowane. Do celów analizy to zdarzenie jest traktowany jako numer zdarzenia 4 poprzedni.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Zdarzenie dostarczone poza kolejnością i na zewnątrz tolerancja 8 sekund. Tak czas trwania zdarzenia jest dostosowywany do maksymalnej tolerancji poza kolejnością. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Te ustawienia, można opóźnić danymi wyjściowymi Moje zadania? 

Tak. Domyślnie zasady poza kolejnością jest równa zero (00 minut i 00 sekund). Jeśli zmienisz domyślny, pierwsze dane wyjściowe zadania jest opóźnione przez tę wartość (lub nowszego). 

Jeśli jedna z partycji dane wejściowe nie odbiera zdarzenia, należy się spodziewać danych wyjściowych, aby zostać opóźnione przez wartość zasad późnego przybycia. Aby dowiedzieć się, dlaczego, zapoznaj się z sekcją błąd InputPartition poniżej. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Wyświetlane są komunikaty LateInputEvents w mojej dziennika aktywności

Te komunikaty są wyświetlane z informacją, że zdarzenia mają już korzystać z opóźnieniem i są albo porzucone lub dopasowane zgodnie z konfiguracją. Te komunikaty można zignorować, jeśli późnego przybycia zasady zostały skonfigurowane prawidłowo. 

Przykładem tego komunikatu jest: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Czy mogę zobaczyć InputPartitionNotProgressing w mojej dziennika aktywności

Twoje źródło danych wejściowych (Event Hub/IoT Hub) prawdopodobnie jest podzielona na partycje. Usługa Azure Stream Analytics generuje dane wyjściowe dla t1 sygnatury czasu tylko wtedy, gdy wszystkie partycje, które są połączone mają co najmniej czasu t1. Na przykład załóżmy, że zapytanie odczytuje z partycji Centrum zdarzeń, który ma dwie partycje. Jedna z partycji, P1, ma zdarzenia aż do czasu t1. Druga partycja P2, ma zdarzenia aż do czasu t1 + x. Dane wyjściowe są następnie generowane aż do czasu t1. Jednak w przypadku partycji jawne przez klauzulę identyfikatora partycji, postęp partycje niezależnie. 

Połączeniu wiele partycji z tego samego strumienia wejściowego tolerancja spóźnionego przybycia jest maksymalną ilość czasu oczekiwania każdej partycji w nowych danych. Jeśli wystąpi jednej partycji w Centrum zdarzeń, lub jeśli Centrum IoT Hub nie odbiera danych wejściowych, oś czasu dla tej partycji nie postępu aż do osiągnie koniec próg tolerancji przybycia. Spowoduje to opóźnienie danych wyjściowych w późnym progu na uszkodzenia przybyciu. W takich przypadkach może zostać wyświetlony następujący komunikat:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Ten komunikat z informacją, że co najmniej jedną partycję w danych wejściowych jest pusty i zostanie opóźnione, danych wyjściowych przez próg późnego przybycia. Aby tego uniknąć, zalecane jest przeprowadzenie albo:  
1. Upewnij się, że wszystkie partycje usługi Event Hub/IoT Hub odbierania danych. 
2. Użyj partycji przez klauzulę identyfikatora partycji w zapytaniu. 

## <a name="next-steps"></a>Kolejne kroki
* [Zagadnienia dotyczące obsługi czasu](stream-analytics-time-handling.md)
* [Metryk dostępnych w usłudze Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
