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
ms.openlocfilehash: b23f9532aa1ca6f7bae914ff8cb9d7566a0fec86
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841540"
---
Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów.

Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. Po nadejściu nowszych zdarzeń są one dodawane na końcu sekwencji. Partycję można traktować jako „dziennik zatwierdzania”.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Usługa Event Hubs przechowuje dane przez skonfigurowany czas przechowywania, stosowany do wszystkich partycji w Centrum zdarzeń. Zdarzenia wygasają czasowo — nie można ich jawnie usunąć. Ponieważ partycje są niezależne i zawierają własne sekwencje danych, często rosną z różną szybkością.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 2 do 32. Liczby partycji nie można zmieniać, dlatego ustawiając liczbę partycji, trzeba planować długoterminowo. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Możesz zwiększyć liczbę partycji ponad 32, kontaktując się z zespołem ds. usługi Event Hubs.

Chociaż partycje są identyfikowalne i mogą być wysyłane bezpośrednio do, wysyłając bezpośrednio do partycji nie jest zalecane. Zamiast tego można użyć konstrukcji wyższego poziomu wprowadzonych w [wydawców zdarzeń](../articles/event-hubs/event-hubs-features.md#event-publishers) sekcji. 

Partycje są wypełnione sekwencją danych zdarzenia, które obejmują treść zdarzenia, zdefiniowany przez użytkownika zbiór właściwości oraz metadane, takie jak jego przesunięcie w partycji i jego numer w sekwencji strumienia.

Zalecane jest równoważenie jednostek przepływności 1:1 i partycji w celu osiągnięcia optymalnej skali. Jedna partycja ma gwarantowaną przychodzący i wychodzący z więcej niż jedną jednostkę przepływności. Może być możliwe do uzyskania większej przepływności na partycji, wydajności nie jest gwarantowana. Jest to, dlaczego firma Microsoft zaleca, że liczba partycji w Centrum zdarzeń jest większa lub równa liczbie jednostek przepływności.

Biorąc pod uwagę łącznej przepływności, w którym planujesz wymagające, wiesz, że liczba jednostek przepływności, które są wymagane i minimalna liczba partycji, ale jak wiele partycji, należy mieć? Wybierz liczbę partycji, oparte na równoległością, który chcesz osiągnąć, jak również w zakresie przyszłych przepływności. Nie ma opłat za liczbę partycji, posiadanych w ramach Centrum zdarzeń.

Aby uzyskać więcej informacji na temat partycji i równowagi między dostępnością i niezawodnością, zobacz [Przewodnik dotyczący programowania w usłudze Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) i artykuł [Availability and consistency in Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs).
