---
title: Rozwiązywanie problemów z źle sformułowane zdarzenia wejściowe w Azure Stream Analytics | Dokumentacja firmy Microsoft
description: Jak sprawdzić, które zdarzenie w moich danych wejściowych danych powoduje problem w zadaniu Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766398"
---
# <a name="troubleshoot-for-malformed-input-events"></a>Rozwiązywanie problemów z źle sformułowane zdarzenia wejściowe

Kiedy strumień wejściowy zadania Stream Analytics zawiera źle sformułowane wiadomości, powoduje problemy serializacji. Wadliwe komunikaty obejmują niepoprawne serializacji, takie jak brak nawiasów w obiekcie JSON lub format sygnatury czasowej niepoprawne. Zadanie Stream Analytics odebrania nieprawidłowo sformułowany komunikat porzuca wiadomości i powiadamia użytkownika z ostrzeżeniem. Symbol ostrzeżenie jest wyświetlane na **dane wejściowe** kafelka zadania Stream Analytics:

![Dane wejściowe kafelka](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Przejdź do wprowadzania kafelka i kliknij, aby wyświetlić ostrzeżenia.
2. Dane wejściowe kafelka Wyświetla zestaw ostrzeżenia o szczegółowe informacje o problemie. Poniżej przedstawiono przykładowy komunikat ostrzegawczy, zawiera komunikat ostrzegawczy partycji, przesunięcia i liczby sekwencji przypadku źle sformułowane dane JSON. 

   ![Komunikat ostrzegawczy z przesunięciem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Aby uzyskać dane JSON, który ma niepoprawny format, należy uruchomić poniższy fragment kodu. Ten blok kodu odczytuje partycji identyfikator, przesunięcie i wyświetla dane. Możesz uzyskać pełny przykład z [repozytorium GitHub przykłady](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Po przeczytaniu dane można analizować i Popraw format serializacji.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Kolejne kroki

* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
