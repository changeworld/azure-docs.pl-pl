---
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598475"
---
Aby oszacować bardziej dokładnie przewidywać koszty zużycia, należy wziąć pod uwagę liczbę komunikatów lub zdarzeń, które mogą pojawić się w każdym dniu, a nie podstawowy obliczeń z interwałem sondowania. Jeśli zdarzenie lub komunikatu spełnia kryteria wyzwalacza, wiele wyzwalaczy natychmiastową próbę odczytu i wszystkich innych zdarzeń oczekiwania, lub komunikaty, które spełniają kryteria. To zachowanie oznacza, że nawet jeśli wybrano dłuższego interwał sondowania jest on wyzwalany na podstawie liczby zdarzeń oczekiwania lub wiadomości, które kwalifikują się do uruchamiania przepływów pracy. Wyzwalacze, które należy wykonać takie zachowanie obejmują usługi Azure Service Bus i usługi Azure Event Hub.

Tak na przykład, załóżmy, że możesz skonfigurować wyzwalacz, który sprawdza, czy punkt końcowy każdego dnia. Jeśli wyzwalacz sprawdza, czy punkt końcowy i znajdzie 15 zdarzenia, które spełniają kryteria, aktywuje się i uruchamia odpowiednie przepływ pracy 15 razy. Usługa Logic Apps liczniki — wszystkie akcje, które wykonują te 15 przepływy pracy, łącznie z żądania wyzwalacza. Aby obliczyć potencjalne koszty, spróbuj [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).