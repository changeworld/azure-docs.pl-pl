---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481534"
---
Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów.

Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. Po nadejściu nowszych zdarzeń są one dodawane na końcu sekwencji. Partycję można traktować jako „dziennik zatwierdzania”.

![Usługa Event Hubs](./media/event-hubs-partitions/partition.png)

Usługa Event Hubs przechowuje dane przez skonfigurowany czas przechowywania, który ma zastosowanie do wszystkich partycji w centrum zdarzeń. Zdarzenia wygasają czasowo — nie można ich jawnie usunąć. Ponieważ partycje są niezależne i zawierają własne sekwencje danych, często rosną z różną szybkością.

![Usługa Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 2 do 32. Liczby partycji nie można zmieniać, dlatego ustawiając liczbę partycji, trzeba planować długoterminowo. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Możesz zwiększyć liczbę partycji ponad 32, kontaktując się z zespołem ds. usługi Event Hubs.

Możesz ustawić go jako najwyższą możliwą wartość, która jest 32, w momencie tworzenia. Należy pamiętać, że posiadanie więcej niż jednej partycji spowoduje zdarzenia wysyłane do wielu partycji bez zachowywania kolejności, chyba że skonfigurujesz nadawców tylko wysłać do jednej partycji z 32 pozostawiając pozostałe partycje 31 nadmiarowe. W pierwszym przypadku będziesz musiał przeczytać zdarzenia we wszystkich partycjach 32. W tym ostatnim przypadku nie ma oczywistych dodatkowych kosztów oprócz dodatkowej konfiguracji, którą musisz wykonać na hostie procesora zdarzeń.

Podczas gdy partycje są możliwe do zidentyfikowania i mogą być wysyłane bezpośrednio, wysyłanie bezpośrednio do partycji nie jest zalecane. Zamiast tego można użyć konstrukcji wyższego poziomu wprowadzonych w sekcji [Wydawcy zdarzeń.](../articles/event-hubs/event-hubs-features.md#event-publishers) 

Partycje są wypełnione sekwencją danych zdarzeń, która zawiera treść zdarzenia, bag właściwości zdefiniowane przez użytkownika i metadane, takie jak jego przesunięcie w partycji i jego numer w sekwencji strumienia.

Zaleca się zrównoważenie jednostek przepływności 1:1 i partycji w celu osiągnięcia optymalnej skali. Pojedyncza partycja ma gwarantowany ruch przychodzący i wyjście do jednej jednostki przepływności. Chociaż może być w stanie osiągnąć wyższą przepływność na partycji, wydajność nie jest gwarantowana. Dlatego zdecydowanie zaleca się, aby liczba partycji w centrum zdarzeń była większa lub równa liczbie jednostek przepływności.

Biorąc pod uwagę całkowitą przepływność, którą planujesz na potrzeby, znasz liczbę jednostek przepływności, które są wymagane i minimalną liczbę partycji, ale ile partycji należy mieć? Wybierz liczbę partycji na podstawie równoległości niższego rzędu, który chcesz osiągnąć, a także przyszłych potrzeb przepływności. Nie ma żadnych opłat za liczbę partycji, które masz w Centrum zdarzeń.

Aby uzyskać więcej informacji na temat partycji i równowagi między dostępnością i niezawodnością, zobacz [Przewodnik dotyczący programowania w usłudze Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) i artykuł [Availability and consistency in Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs).
