---
title: Omówienie usługi Azure Event hubs w warstwie dedykowana pojemność | Dokumentacja firmy Microsoft
description: Omówienie programu Microsoft Azure Event hubs w warstwie dedykowana pojemność.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: shvija
ms.openlocfilehash: 1a7a7593e80f08296e3163e528e880f343366b8a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005708"
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie usługi Event Hubs w wersji dedykowanej

*Event hubs w warstwie dedykowana* pojemności oferuje wdrożenia pojedynczej dzierżawy dla klientów o najbardziej wygórowanych wymaganiach. W pełnej skali usługi Azure Event Hubs można ruch przychodzący ponad 2 miliony zdarzeń na sekundę, czyli do 2 GB na sekundę dane telemetryczne z pełni trwałego magazynu i nieprzekraczającymi sekundy opóźnieniami. Umożliwia to również zintegrowane rozwiązania przez przetwarzanie w czasie rzeczywistym i danych wsadowych w tym samym systemie. Za pomocą [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) dostępnych w ramach oferty, dzięki jednemu strumieniowi obsługiwać potoki w czasie rzeczywistym i opartych na partiach może zmniejszyć złożoność rozwiązania.

W poniższej tabeli porównano warstwy dostępna usługa Event Hubs. Oferta Event hubs w warstwie dedykowana jest stałą cenę miesięczną, w porównaniu do użycia ceny dla większości funkcji standardu. Dedykowany warstwa oferuje wszystkie funkcje plan w warstwie standardowa, ale o pojemności skali przedsiębiorstwa dla klientów wymagających obciążeń. 

| Cecha | Standardowa (Standard) | Dedykowany |
| --- |:---:|:---:|:---:|
| Zdarzenia związane z transferem danych przychodzących | Płatność za milion zdarzeń | Dołączono |
| Jednostka przepływności (1 MB/s danych przychodzących, 2 MB/s danych wychodzących) | Płatność za godzinę | Dołączono |
| Rozmiar komunikatu | 256 KB | 1 MB |
| Zasady dotyczące wydawców | Yes | Yes |   
| Grupy odbiorców | 20 | 20 |
| Powtarzanie komunikatu | Yes | Yes |
| Maksymalna liczba jednostek przepływności | 20 (elastyczne do 100)   | 1 jednostka wydajności (CU) ≈ 50 |
| Połączenia obsługiwane przez brokera | 1000 uwzględnione | 100 tys. w cenie |
| Dodatkowe połączenia obsługiwane przez brokera | Yes | Yes |
| Przechowywanie komunikatów | 1 dzień w cenie | Liczba uwzględnianych dni: do 7 |
| Przechwytywanie | Płatność za godzinę | Dołączono |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Zalety Event hubs w warstwie dedykowana pojemność

Korzystając z Event hubs w warstwie dedykowana, dostępne są następujące korzyści:

* Obsługa za pomocą nie szumu z innych dzierżaw pojedynczej dzierżawy.
* Zwiększa rozmiar komunikatu do 1 MB w porównaniu do 256 KB w przypadku warstwy Standard.
* Każdym razem, gdy powtarzalną wydajność.
* Gwarantowana przygotowanie wydajności niezbędnej do własnych potrzeb dużego ruchu.
* Obejmuje [przechwytywania](event-hubs-capture-overview.md) funkcji usługi Event hubs, aby zapewnić integrację z usługą micro batch- i długoterminowego przechowywania.
* Zero konserwacji: usługa zarządza równoważeniem obciążenia, systemu operacyjnego aktualizacje, poprawki zabezpieczeń i partycjonowanie.
* Stałe, co godzinę ceny.
* Komunikat o przechowywania, do 7 dni bez dodatkowych opłat.

Event hubs w warstwie dedykowana usunie także niektóre ograniczenia przepływności standardowego oferty. Jednostki przepływności w warstwie standardowa uprawnia do 1000 zdarzeń na sekundę, oraz 1 MB na sekundę transferu danych przychodzących za jednostek Przepływności, a double ilość danych wychodzących. Oferta dedykowanych skalowania nie ma żadnych ograniczeń na ruch przychodzący i liczby zdarzeń ruchu wychodzącego. Te limity podlegają tylko wydajnością przetwarzania zakupionych event hubs.

Ten zastrzeżony, dedykowanym środowisku zawiera inne funkcje, które są unikatowe dla tej warstwy, takich jak:

* Określa liczbę przestrzeni nazw w klastrze.
* Określa ograniczenia przepływności na każdym z przestrzeni nazw.
* Konfiguruje liczbę usługi event hubs w każdej przestrzeni nazw.
* Określa limit liczby partycji.

Ta usługa jest przeznaczona dla największych użytkowników telemetrii i jest dostępna dla klientów z umową enterprise agreement.

## <a name="how-to-onboard"></a>Jak dołączyć

Pojemności można skalować w górę lub w dół w ciągu miesiąca do swoich potrzeb przez dodanie lub usunięcie jednostki pojemności. Dedykowanego planu jest unikatowy, wówczas będą pojawiać się w praktyce dołączania od zespołu produktu usługi Event Hubs, aby uzyskać elastyczne wdrożenia, który jest odpowiedni dla Ciebie. Aby dołączyć do tej jednostki SKU [skontaktuj się z działem pomocy technicznej dotyczącej rozliczeń](https://ms.portal.azure.com/#create/Microsoft.Support) lub przedstawicielem firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać dodatkowe szczegóły dotyczące Event Hubs dedykowanej pojemności. Możesz także dowiedzieć się więcej o usłudze Event Hubs w warstwach cenowych, odwiedź następujące linki:

- [Usługa Event hubs w warstwie dedykowana — cennik](https://azure.microsoft.com/pricing/details/event-hubs/). Można również skontaktować się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać dodatkowe szczegóły dotyczące Event hubs w warstwie dedykowana pojemność.
- [— Często zadawane pytania dla centrów zdarzeń](event-hubs-faq.md) zawiera informacje o cenach i odpowiedzi na niektóre często zadawane pytania dotyczące usługi Event Hubs. 
