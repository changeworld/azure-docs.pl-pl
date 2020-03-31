---
title: Pojęcia — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Pojęcia w siatce zdarzeń na ioT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992550"
---
# <a name="event-grid-concepts"></a>Event Grid — pojęcia

W tym artykule opisano główne pojęcia w usłudze Azure Event Grid.

## <a name="events"></a>Zdarzenia

Zdarzenie jest najmniejszą ilością informacji, które w pełni opisuje coś, co wydarzyło się w systemie. Każde zdarzenie ma wspólne informacje, takie jak: źródło zdarzenia, czas zdarzenia i unikatowy identyfikator. Każde zdarzenie ma również określone informacje, które są istotne tylko dla określonego typu zdarzenia. Obsługa zdarzenia o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej.

Właściwości uwzględnione w zdarzeniu można znaleźć w [schemacie zdarzeń usługi Azure Event Grid](event-schemas.md).

## <a name="publishers"></a>Wydawcy

Wydawcą jest użytkownik lub organizacja, która decyduje się na wysyłanie zdarzeń do usługi Event Grid. Możesz publikować zdarzenia z własnej aplikacji.

## <a name="event-sources"></a>Źródła zdarzeń

Źródłem zdarzenia jest miejsce, w którym zdarzenie się dzieje. Każde źródło zdarzenia jest powiązane z co najmniej jednym typem zdarzenia. Na przykład usługa Azure Storage jest źródłem zdarzeń tworzonych przez obiekty blob. Aplikacja jest źródłem definiowanych przez Ciebie zdarzeń niestandardowych. Źródła zdarzeń służą do wysyłania zdarzeń do usługi Event Grid.

## <a name="topics"></a>Tematy

Temat siatki zdarzeń zawiera punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy temat siatki zdarzeń i decyduje, czy źródło zdarzeń potrzebuje jednego tematu, czy więcej niż jednego tematu. Temat jest używany do kolekcji powiązanych zdarzeń. Aby odpowiedzieć na określone typy zdarzeń, subskrybenci decydują, które tematy mają być subskrybowanych.

Podczas projektowania aplikacji, masz możliwość decydowania o ile tematów do utworzenia. W przypadku dużych rozwiązań utwórz temat niestandardowy dla każdej kategorii powiązanych zdarzeń. Może to na przykład być aplikacja, która wysyła zdarzenia powiązane z modyfikowaniem kont użytkowników i przetwarzaniem zamówień. Istnieje małe prawdopodobieństwo, że procedura obsługi zdarzeń oczekuje obu kategorii zdarzeń. Utwórz dwa tematy niestandardowe, a procedury obsługi zdarzeń subskrybują temat, którymi są zainteresowane. W przypadku małych rozwiązań możesz chcieć wysłać wszystkie zdarzenia do jednego tematu. Subskrybenci zdarzeń mogą filtrować typy zdarzeń, które chcą.

Zobacz [dokumentację interfejsu API REST,](api.md) jak zarządzać tematami w usłudze Event Grid.

## <a name="event-subscriptions"></a>Subskrypcje zdarzeń

Subskrypcja informuje o tym, które zdarzenia na temat cię interesują. Podczas tworzenia subskrypcji, należy podać punkt końcowy do obsługi zdarzenia. Można filtrować zdarzenia, które są wysyłane do punktu końcowego. 

Zobacz [dokumentację interfejsu API REST](api.md) na temat zarządzania subskrypcjami w usłudze Event Grid.

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Z punktu widzenia siatki zdarzeń program obsługi zdarzeń jest miejscem, w którym jest wysyłane zdarzenie. Program obsługi podejmuje dalsze działania w celu przetworzenia zdarzenia. Usługa Event Grid obsługuje kilka typów programów obsługi. Można użyć obsługiwanej usługi platformy Azure lub własnego haka sieci web jako programu obsługi. W zależności od typu programu obsługi usługa Event Grid następuje różne mechanizmy, aby zagwarantować dostarczanie zdarzenia. Jeśli program obsługi zdarzeń docelowych jest hakiem internetowym HTTP, zdarzenie jest `200 – OK`ponawiane, gdy program obsługi zwraca kod stanu . W przypadku centrum brzegowego, jeśli zdarzenie jest dostarczane bez wyjątku, jest uważane za pomyślne.

## <a name="security"></a>Zabezpieczenia

Usługa Event Grid zapewnia bezpieczeństwo subskrybowania tematów i publikowania tematów. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).

## <a name="event-delivery"></a>Dostarczanie zdarzeń

Jeśli usługa Event Grid nie może potwierdzić, że zdarzenie zostało odebrane przez punkt końcowy subskrybenta, ponownie likwia zdarzenie. Aby uzyskać więcej informacji, zobacz [Dostarczanie wiadomości w ucho i ponowie próbę](delivery-retry.md).

## <a name="batching"></a>Tworzenie partii

Podczas korzystania z tematu niestandardowego zdarzenia muszą być zawsze publikowane w tablicy. W przypadku scenariuszy niskiej przepływności tablica będzie miała tylko jedną wartość. W przypadku użycia dużej ilości zaleca się wsadowanie kilku zdarzeń razem na publikowanie, aby osiągnąć wyższą wydajność. Partie mogą mieć maksymalnie 1 MB. Każde zdarzenie nadal nie powinno być większe niż 1 MB (wersja zapoznawcza).