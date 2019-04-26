---
title: Pojęcia dotyczące usługi Azure Event Grid
description: Opis usługi Azure Event Grid i pojęć z nią związanych. Definiuje kilka najważniejszych składników usługi Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 1c77d0ea9e67c8d69f3f632cace164d8a0c4d921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562359"
---
# <a name="concepts-in-azure-event-grid"></a>Pojęcia dotyczące usługi Azure Event Grid

W tym artykule opisano główne pojęcia w usłudze Azure Event Grid.

## <a name="events"></a>Zdarzenia

Zdarzenie jest najmniejsza ilość informacje opisujące pełni coś, co wydarzyło się w systemie. Każde zdarzenie zawiera wspólne informacje, takie jak: źródło zdarzenia, czas zdarzenia miały miejsce i unikatowy identyfikator. Każde zdarzenie ma również określone informacje, których dotyczy tylko określonych typach zdarzenia. Na przykład zdarzenie o nowy plik tworzonych w usłudze Azure Storage zawiera szczegóły dotyczące pliku, takie jak `lastTimeModified` wartość. Lub zdarzenie usługi Event Hubs ma adres URL pliku przechwytywania. 

Każde wydarzenie jest ograniczone do 64 KB danych.

Dla właściwości, które są wysyłane w zdarzeniu, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="publishers"></a>Wydawcy

Wydawca jest użytkownika lub organizację, która decyduje się na wysyłanie zdarzeń do usługi Event Grid. Firma Microsoft publikuje zdarzenia dla kilka usług platformy Azure. Mogą publikować zdarzenia z własnych aplikacji. Organizacje, które obsługują usługi spoza platformy Azure mogą publikować zdarzenia przy użyciu usługi Event Grid.

## <a name="event-sources"></a>Źródła zdarzeń

Źródłem zdarzenia jest, gdy zdarzenie. Każde źródło zdarzenia jest powiązany z co najmniej jeden typ zdarzenia. Na przykład Azure Storage jest źródła zdarzeń dla zdarzenia utworzony obiekt blob. Usługa IoT Hub jest źródła zdarzeń dla zdarzenia utworzono urządzenie. Aplikacja jest źródła zdarzeń dla zdarzeń niestandardowych, zdefiniowanych przez użytkownika. Źródła zdarzeń jest odpowiedzialny za wysyłanie zdarzeń do usługi Event Grid.

Aby uzyskać informacji dotyczących implementowania dowolnego z obsługiwanych źródeł usługi Event Grid, zobacz [źródła zdarzeń w usłudze Azure Event Grid](event-sources.md).

## <a name="topics"></a>Tematy

Temat usługi event grid udostępnia punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy tematu usługi event grid i decyduje, czy źródło zdarzenia musi temat jeden lub więcej niż jeden temat. Temat jest używany dla kolekcji powiązanych zdarzeń. Aby reagować na wybranych typów zdarzeń, subskrybenci zdecydować, które tematy, aby subskrybować.

System tematy dotyczą wbudowanych udostępnianego przez usługi platformy Azure. Nie widzisz tematów systemu w ramach subskrypcji platformy Azure, ponieważ wydawca jest właścicielem tematy, ale można subskrybować ich. Aby zamówić subskrypcję, możesz podać informacje o zasobie, że chcesz odbierać zdarzenia z. Tak długo, jak długo użytkownik ma dostęp do zasobu, możesz zasubskrybować ze zdarzeniami.

Tematy niestandardowe są tematy innych firm i aplikacji. Podczas tworzenia lub dostępem do tematu niestandardowego, zobaczysz tego niestandardowego tematu, w ramach subskrypcji.

Podczas projektowania aplikacji, należy elastyczność, podejmując decyzję o ile tematy, aby utworzyć. W przypadku dużych rozwiązań tworzenie tematu niestandardowego dla każdej kategorii powiązanych zdarzeń. Na przykład rozważmy aplikację, która wysyła zdarzenia związane z modyfikowanie kont użytkowników i przetwarzanie zamówień. Jest mało prawdopodobne, każdy program obsługi zdarzeń chce, aby obie kategorie zdarzeń. Utwórz dwa tematy niestandardowe i umożliwić subskrybować ten, który je interesujące programy obsługi zdarzeń. W przypadku małych rozwiązań warto wszystkie zdarzenia są wysyłane do jednego tematu. Typy zdarzeń, które zechcą subskrybentów zdarzeń można filtrować.

## <a name="event-subscriptions"></a>Subskrypcje zdarzeń

Subskrypcję usługi Event Grid informuje o zdarzenia na temat interesujących Cię odbieranie. Podczas tworzenia subskrypcji, udostępniasz punkt końcowy dla obsługi zdarzenia. Można filtrować zdarzenia, które są wysyłane do punktu końcowego. Można filtrować według typu zdarzenia lub wzorzec podmiotu. Aby uzyskać więcej informacji, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).

Aby uzyskać przykłady tworzenia subskrypcji zobacz:

* [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Event Grid](cli-samples.md)
* [Przykłady programu Azure PowerShell dla usługi Event Grid](powershell-samples.md)
* [Szablony usługi Azure Resource Manager dla usługi Event Grid](template-samples.md)

Aby dowiedzieć się, jak pobieranie bieżącego zdarzenia subskrypcji siatki, zobacz [subskrypcji usługi Event Grid zapytania](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Wygaśnięcie subskrypcji zdarzeń

[Rozszerzenia usługi Event Grid](/cli/azure/azure-cli-extensions-list) dla wiersza polecenia platformy Azure pozwala na ustawienie wygaśnięcia daty podczas tworzenia subskrypcji zdarzeń. Jeśli korzystasz z interfejsu API REST, należy użyć `api-version=2018-09-15-preview`

Subskrypcja zdarzeń automatycznie wygaśnie po tej dacie. Ustawienia okresu ważności subskrypcji zdarzeń, które są wymagane tylko przez ograniczony czas, a nie chcesz martwić się o czyszczenia tych subskrypcjach. Na przykład podczas tworzenia subskrypcji zdarzeń, aby przetestować scenariusz, możesz chcieć ustawienia okresu ważności. 

Na przykład ustawienia wygaśnięcia, zobacz [subskrypcji z zaawansowanych filtrów](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Z perspektywy usługi Event Grid program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi ma kilka dalszych działań do przetworzenia zdarzenia. Usługa Event Grid obsługuje kilka typów programu obsługi. Obsługiwane usługi platformy Azure lub własnego elementu webhook można użyć jako program obsługi. W zależności od typu obsługi usługi Event Grid następuje różne mechanizmy gwarantuje dostarczenia zdarzeń. Dla programów obsługi zdarzeń elementu webhook protokołu HTTP, zdarzenie jest ponawiana dopóki metoda obsługi zwraca kod stanu `200 – OK`. Dla usługi Azure Storage Queue zdarzenia są ponawiana do wypychania komunikatów w kolejce pomyślnie przetwarzania przez usługę kolejki.

Aby uzyskać informacje o implementowaniu dowolnej obsługiwanej procedury obsługi usługi Event Grid, zobacz [procedury obsługi zdarzeń w usłudze Azure Event Grid](event-handlers.md).

## <a name="security"></a>Bezpieczeństwo

Usługa Event Grid zapewnia zabezpieczenia subskrybowanie tematów i publikowanie tematów. Podczas subskrybowania, musi mieć odpowiednie uprawnienia na temat siatki zasobów lub zdarzenia. Podczas publikowania, musisz mieć uwierzytelniania za pomocą tokenu sygnatury dostępu Współdzielonego lub klucza dla tematu. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](security-authentication.md).

## <a name="event-delivery"></a>Dostarczanie zdarzeń

Jeśli usługa Event Grid można potwierdzić, że zdarzenia zostały odebrane przez punkt końcowy subskrybenta, redelivers zdarzenia. Aby uzyskać więcej informacji, zobacz [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).

## <a name="batching"></a>Tworzenie partii

Korzystając z niestandardowego tematu, zawsze należy opublikować zdarzenia w tablicy. Można w tym zadaniu wsadowym jednego dla scenariuszy niskiej przepustowości, jednak dla dużej liczby przypadków użycia, zalecane jest, batch kilka zdarzeń, które razem na publikowanie osiągnąć wyższą wydajność. Partie można maksymalnie 1 MB. Każde zdarzenie nadal nie powinna być dłuższa niż 64 KB.

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
