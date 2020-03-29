---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74793448"
---
Aby oszacować dokładniejsze koszty zużycia, należy wziąć pod uwagę możliwą liczbę komunikatów lub zdarzeń, które mogą pojawić się w danym dniu, a nie oprzeć obliczenia tylko na interwał sondowania. Gdy zdarzenie lub wiadomość spełnia kryteria wyzwalacza, wiele wyzwalaczy natychmiast próbuje odczytać wszystkie inne zdarzenia oczekiwania lub wiadomości, które spełniają kryteria. To zachowanie oznacza, że nawet po wybraniu dłuższego interwału sondowania wyzwalacz jest uruchamiany na podstawie liczby zdarzeń oczekiwania lub komunikatów, które kwalifikują się do uruchamiania przepływów pracy. Wyzwalacze, które należy wykonać to zachowanie obejmują usługi Azure Service Bus i Usługi Azure Event Hub.

Tak, na przykład, załóżmy, że skonfigurować wyzwalacz, który sprawdza punkt końcowy codziennie. Gdy wyzwalacz sprawdza punkt końcowy i znajdzie 15 zdarzeń, które spełniają kryteria, wyzwalacz uruchamia i uruchamia odpowiedni przepływ pracy 15 razy. Logic Apps mierzy wszystkie akcje wykonywane przez te 15 przepływów pracy, w tym żądania wyzwalacza. Aby obliczyć potencjalne koszty, wypróbuj [kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).