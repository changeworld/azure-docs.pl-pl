---
title: Azure pojęcia siatki zdarzeń
description: Opisuje Azure zdarzeń siatki i jego pojęcia. Definiuje kilka najważniejszych składników zdarzeń siatki.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: fd82d163ba8407a3dfa088cd322f3e236be5d7ea
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="concepts-in-azure-event-grid"></a>Pojęcia dotyczące usługi Azure Event siatki

W tym artykule opisano główne pojęcia w siatce zdarzeń platformy Azure.

## <a name="events"></a>Zdarzenia

Zdarzenie jest najmniejsza ilość danych opisujący pełni coś się stało w systemie. Każde zdarzenie zawiera typowe informacje, takie jak: źródło zdarzenia, czas zdarzenia miały miejsce i unikatowym identyfikatorem. Każde zdarzenie ma również informacje, na które jest tylko istotne dla określonego typu zdarzenia. Na przykład zdarzenia o nowy plik tworzony w magazynie Azure zawiera informacje o pliku, takich jak `lastTimeModified` wartość. Lub zdarzenia usługi Event Hubs ma adres URL pliku przechwytywania. Każde wydarzenie jest ograniczony do 64 KB danych.

## <a name="event-sourcespublishers"></a>Wydawcy źródła zdarzeń

Źródłem zdarzenia jest, gdy zdarzenie. Na przykład usługi Azure Storage jest źródła zdarzeń dla zdarzenia utworzony obiekt blob. Sieci szkieletowej maszyny Wirtualnej Azure jest źródła zdarzeń dla zdarzenia maszyny wirtualnej. Źródła zdarzeń są zobowiązani do publikowania zdarzeń w siatce zdarzeń.

Informacje o implementacji dowolną z obsługiwanych źródeł zdarzeń siatki, zobacz [źródła zdarzeń w siatce zdarzeń Azure](event-sources.md).

## <a name="topics"></a>Tematy

Wydawcy kategoryzowanie zdarzeń do tematów. Temat siatki zdarzenia zawiera punkt końcowy, gdzie wydawcy wysyła zdarzenia. Aby odpowiedzieć wybranych typów zdarzeń, subskrybentów zdecyduj, jakie tematy, aby subskrybować. Tematy zawierają również schematu zdarzeń, aby subskrybenci można dowiedzieć się, jak odpowiednio korzystanie ze zdarzeń.

System tematy dotyczą wbudowanych udostępnionego przez usługi Azure. Tematy niestandardowe są tematy innych firm i aplikacji.

W przypadku projektowania aplikacji, istnieje elastyczność podczas podejmowania decyzji o ile tematy, aby utworzyć. W przypadku dużych rozwiązaniach utworzyć niestandardowego tematu dla każdej kategorii powiązanych zdarzeń. Rozważmy na przykład aplikacja, która wysyła zdarzenia związane z modyfikowanie kont użytkowników i przetwarzania zamówienia. Jest mało prawdopodobne, wszelkie obsługi zdarzeń oczekuje, że oba rodzaje zdarzeń. Utwórz dwa tematy niestandardowych i pozwól subskrybować ten interesującego ich obsługi zdarzeń. Dla małych rozwiązań można wybrać do wysłania wszystkich zdarzeń do jednego tematu. Typy zdarzeń, które mają można filtrować zdarzenia subskrybentów.

## <a name="event-subscriptions"></a>Subskrypcja zdarzeń

Subskrypcję nakazuje siatki zdarzeń na zdarzenia na temat otrzymywać jest subskrybenta. Subskrypcja zawiera również informacji na temat sposobu zdarzenia powinna zostać dostarczona do subskrybenta.

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Z perspektywy siatki zdarzeń program obsługi zdarzeń jest miejscem wysyłania zdarzenia. Program obsługi ma niektórych dalszych kroków w celu przetworzenia zdarzenia. Siatka zdarzeń obsługuje wiele typów subskrybenta. W zależności od typu subskrybenta siatki zdarzeń wykonuje różne mechanizmy gwarantujące dostarczania zdarzenia. Dla programów obsługi zdarzeń elementu webhook HTTP, zdarzenie jest ponawiana dopóki obsługi zwraca kod stanu `200 – OK`. Dla kolejki magazynu Azure zdarzenia są ponawiana dopóki usługa kolejki jest może pomyślnie przetworzyć wypychania wiadomości do kolejki.

Aby dowiedzieć się, jak żadnego z obsługiwanych programów obsługi zdarzeń siatki, zobacz [programów obsługi zdarzeń w siatce zdarzeń Azure](event-handlers.md).

## <a name="filters"></a>Filtry

Podczas subskrybowania temat siatki zdarzenia, można filtrować zdarzenia, które są wysyłane do punktu końcowego. Można filtrować według typu zdarzenia lub wzorca podmiotu. Aby uzyskać więcej informacji, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).

## <a name="security"></a>Bezpieczeństwo

Zdarzenie siatki bezpieczeństwo subskrybowanie tematów i publikowania tematów. W przypadku subskrypcji, musi mieć odpowiednie uprawnienia na temat siatki zasobów lub zdarzeń. Podczas publikowania, musi mieć tokenu sygnatury dostępu Współdzielonego lub uwierzytelniania opartego na kluczu dla tematu. Aby uzyskać więcej informacji, zobacz [siatki zdarzeń zabezpieczeń i uwierzytelniania](security-authentication.md).

## <a name="failed-delivery"></a>Dostarczenie nie powiodło się

Jeśli siatki zdarzenia nie mogą potwierdzić, że zdarzenie zostały odebrane przez punkt końcowy abonenta, redelivers zdarzenia. Aby uzyskać więcej informacji, zobacz [dostarczanie komunikatów zdarzeń siatki i ponów próbę](delivery-and-retry.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).
