---
title: Azure pojęcia siatki zdarzeń
description: Opis usługi Azure Event Grid i pojęć z nią związanych. Definiuje kilka najważniejszych składników zdarzeń siatki.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: tomfitz
ms.openlocfilehash: abc1302f0317c8d5ecdc7ddaf8ca6d3a9e82b582
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626039"
---
# <a name="concepts-in-azure-event-grid"></a>Pojęcia dotyczące usługi Azure Event siatki

W tym artykule opisano główne pojęcia w siatce zdarzeń platformy Azure.

## <a name="events"></a>Zdarzenia

Zdarzenie jest najmniejsza ilość danych opisujący pełni coś się stało w systemie. Każde zdarzenie zawiera typowe informacje, takie jak: źródło zdarzenia, czas zdarzenia miały miejsce i unikatowym identyfikatorem. Każde zdarzenie ma również informacje, na które jest tylko istotne dla określonego typu zdarzenia. Na przykład zdarzenia o nowy plik tworzony w magazynie Azure zawiera informacje o pliku, takich jak `lastTimeModified` wartość. Lub zdarzenia usługi Event Hubs ma adres URL pliku przechwytywania. 

Każde wydarzenie jest ograniczony do 64 KB danych.

Dla właściwości, które są wysyłane w zdarzeniu, zobacz [schematu zdarzeń siatki zdarzeń Azure](event-schema.md).

## <a name="publishers"></a>Wydawcy

Wydawca jest użytkownika lub organizację, która zadecyduje do wysyłania zdarzeń do siatki zdarzeń. Firma Microsoft publikuje zdarzeń związanych z kilku usług Azure. Zdarzenia można publikować z własnej aplikacji. Organizacje, które udostępniają usługi poza platformą Azure mogą publikować zdarzenia przy użyciu siatki zdarzeń.

## <a name="event-sources"></a>Źródła zdarzeń

Źródłem zdarzenia jest, gdy zdarzenie. Każdy źródło zdarzenia jest powiązany z co najmniej jeden typ zdarzenia. Na przykład usługi Azure Storage jest źródła zdarzeń dla zdarzenia utworzony obiekt blob. Centrum IoT to źródła zdarzeń dla zdarzenia utworzonego urządzenia. Aplikacja jest źródła zdarzeń dla niestandardowych zdarzeń zdefiniowanych przez użytkownika. Źródła zdarzeń jest odpowiedzialny za wysyłanie zdarzeń do zdarzenia siatki.

Informacje o implementacji dowolną z obsługiwanych źródeł zdarzeń siatki, zobacz [źródła zdarzeń w siatce zdarzeń Azure](event-sources.md).

## <a name="topics"></a>Tematy

Temat siatki zdarzenia zawiera punkt końcowy, gdy źródło wysyła zdarzenia. Wydawcy tworzy temat siatki zdarzenia i decyduje o tym, czy źródło zdarzenia musi temat jeden lub więcej niż jeden temat. Temat jest używany dla kolekcji powiązanych zdarzeń. Aby odpowiedzieć wybranych typów zdarzeń, subskrybentów zdecyduj, jakie tematy, aby subskrybować.

System tematy dotyczą wbudowanych udostępnionego przez usługi Azure. Nie zobacz tematy systemu w ramach subskrypcji platformy Azure, ponieważ wydawcy jest właścicielem tematy, ale będzie możliwe subskrybowanie je. Do subskrypcji, musisz podać informacje o zasobie, że chcesz otrzymywać zdarzeń z. Tak długo, jak długo mają dostęp do zasobu, należy subskrybować jego zdarzenia.

Tematy niestandardowe są tematy innych firm i aplikacji. Podczas tworzenia lub dostępem do niestandardowego tematu widzisz niestandardowych tematu w ramach subskrypcji.

W przypadku projektowania aplikacji, istnieje elastyczność podczas podejmowania decyzji o ile tematy, aby utworzyć. W przypadku dużych rozwiązaniach utworzyć niestandardowego tematu dla każdej kategorii powiązanych zdarzeń. Rozważmy na przykład aplikacja, która wysyła zdarzenia związane z modyfikowanie kont użytkowników i przetwarzania zamówienia. Jest mało prawdopodobne, wszelkie obsługi zdarzeń oczekuje, że oba rodzaje zdarzeń. Utwórz dwa tematy niestandardowych i pozwól subskrybować ten interesującego ich obsługi zdarzeń. Dla małych rozwiązań można wybrać do wysłania wszystkich zdarzeń do jednego tematu. Typy zdarzeń, które mają można filtrować zdarzenia subskrybentów.

## <a name="event-subscriptions"></a>Subskrypcja zdarzeń

Subskrypcja informuje siatki zdarzeń zdarzenia na temat myślisz odbieranie. Podczas tworzenia subskrypcji, musisz podać punkt końcowy do obsługi zdarzenia. Można filtrować zdarzenia, które są wysyłane do punktu końcowego. Można filtrować według typu zdarzenia lub wzorca podmiotu. Aby uzyskać więcej informacji, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).

Aby uzyskać przykłady tworzenia subskrypcji zobacz:

* [Przykładów dla platformy Azure CLI siatki zdarzeń](cli-samples.md)
* [Przykładów dla platformy Azure PowerShell siatki zdarzeń](powershell-samples.md)
* [Szablony usługi Azure Resource Manager dla zdarzeń siatki](template-samples.md)

Aby informacji na temat pobierania zdarzenie bieżącej subskrypcji siatki, zobacz [subskrypcji zdarzeń siatki](query-event-subscriptions.md).

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Z perspektywy siatki zdarzeń program obsługi zdarzeń jest miejscem wysyłania zdarzenia. Program obsługi ma niektórych dalszych kroków w celu przetworzenia zdarzenia. Siatka zdarzeń obsługuje wiele typów programu obsługi. Obsługiwane usługi Azure lub własnego elementu webhook służy jako program obsługi. Zależnie od typu programu obsługi zdarzeń siatki następuje różne mechanizmy gwarantujące dostarczania zdarzenia. Dla programów obsługi zdarzeń elementu webhook HTTP, zdarzenie jest ponawiana dopóki obsługi zwraca kod stanu `200 – OK`. Dla kolejki magazynu Azure zdarzenia są ponawiana dopóki usługa kolejki jest może pomyślnie przetworzyć wypychania wiadomości do kolejki.

Aby dowiedzieć się, jak żadnego z obsługiwanych programów obsługi zdarzeń siatki, zobacz [programów obsługi zdarzeń w siatce zdarzeń Azure](event-handlers.md).

## <a name="security"></a>Bezpieczeństwo

Zdarzenie siatki bezpieczeństwo subskrybowanie tematów i publikowania tematów. W przypadku subskrypcji, musi mieć odpowiednie uprawnienia na temat siatki zasobów lub zdarzeń. Podczas publikowania, musi mieć tokenu sygnatury dostępu Współdzielonego lub uwierzytelniania opartego na kluczu dla tematu. Aby uzyskać więcej informacji, zobacz [siatki zdarzeń zabezpieczeń i uwierzytelniania](security-authentication.md).

## <a name="event-delivery"></a>Dostarczania zdarzeń

Jeśli siatki zdarzenia nie mogą potwierdzić, że zdarzenie zostały odebrane przez punkt końcowy abonenta, redelivers zdarzenia. Aby uzyskać więcej informacji, zobacz [dostarczanie komunikatów zdarzeń siatki i ponów próbę](delivery-and-retry.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).
