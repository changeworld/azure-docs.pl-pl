---
title: Omówienie pojemności dedykowanego centra zdarzeń platformy Azure | Dokumentacja firmy Microsoft
description: Przegląd pojemności dedykowanego centra zdarzeń Microsoft Azure.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 7009710328c96660accdcf9c88313ad92d25d41c
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie usługi Event hubs w wersji dedykowanej

*Dedykowane centra zdarzeń* pojemności oferuje wdrożeń pojedynczej dzierżawy dla klientów z najbardziej wygórowanych wymaganiach. W pełnej skali usługi Azure Event Hubs można wejściowych ponad 2 miliony zdarzeń na sekundę, czyli do 2 GB na sekundę telemetrii pełni trwałe opóźnienia magazynu i sekundę podrzędnych. Umożliwia to również zintegrowane rozwiązania przez przetwarzanie w czasie rzeczywistym i partii w tym samym systemie. Z [przechwytywania centra zdarzeń](event-hubs-capture-overview.md) uwzględniona w ofercie, można zmniejszyć złożoność rozwiązania przez jednego strumienia obsługuje potoki w czasie rzeczywistym oraz na podstawie partii.

W poniższej tabeli porównano warstw dostępna usługa Event hubs. Oferta dedykowanego centra zdarzeń jest miesięcznej cenie stałej, w porównaniu do użycia ceny większość funkcji Standard. Dedykowany warstwy są dostępne wszystkie funkcje planie Standard, ale o pojemności skali przedsiębiorstwa, w przypadku klientów z wymagających obciążeń. 

| Cecha | Standardowa (Standard) | Dedykowany |
| --- |:---:|:---:|:---:|
| Zdarzenia związane z transferem danych przychodzących | Należy zwrócić na miliona zdarzeń | Dołączono |
| Jednostki przepustowości (ruch przychodzący 1 MB/s, wyjście 2 MB/s) | Należy zwrócić na godzinę | Dołączono |
| Rozmiar komunikatu | 256 KB | 1 MB |
| Zasady dotyczące wydawców | Yes | Yes |   
| Grupy odbiorców | 20 | 20 |
| Powtarzanie komunikatu | Yes | Yes |
| Maksymalna liczba jednostek przepływności | 20 (elastyczne do 100)   | Jednostka wydajności 1 (CU) ≈ 50 |
| Połączenia obsługiwane przez brokera | 1000 włączone | 100 KB włączone |
| Dodatkowe połączenia obsługiwane przez brokera | Yes | Yes |
| Przechowywanie komunikatów | 1 dzień w cenie | Liczba uwzględnianych dni: do 7 |
| Przechwytywanie | Należy zwrócić na godzinę | Dołączono |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Korzyści wynikające z dedykowanych centra zdarzeń wydajności

Korzystając z dedykowanych centra zdarzeń, dostępne są następujące korzyści:

* Obsługa za pomocą nie szumu od pozostałych dzierżawców pojedynczej dzierżawy.
* Rozmiar komunikatu zwiększa 1 MB w porównaniu do 256 KB dla standardowych.
* Zawsze powtarzalne wydajności.
* Gwarancji wydajności, aby spełnić potrzeby serii.
* Obejmuje [przechwytywania](event-hubs-capture-overview.md) funkcji centra zdarzeń, aby zapewnić integrację z micro partii i długoterminowego przechowywania.
* Zero konserwacji: zarządza usługi równoważenia obciążenia, systemu operacyjnego aktualizacje, poprawki zabezpieczeń i partycjonowania.
* Stałe cennik co godzinę.
* Wiadomości przechowywania w górę do 7 dni z bez dodatkowych opłat.

Dedykowane centra zdarzeń spowoduje również usunięcie niektóre ograniczenia przepustowości Standard oferty. Jednostki przepływności w warstwie standardowa uprawnia do 1000 zdarzeń na sekundę lub 1 MB na sekundę na TU i double ilość ruch wychodzący ruch przychodzący. Liczby zdarzeń dotyczących komunikacji wyjściowej i oferty dedykowanych skalowania nie ma żadnych ograniczeń na transfer danych przychodzących. Te limity reguluje tylko wydajności przetwarzania zakupionych event hubs.

To środowisko zastrzeżonych, dedykowane zapewnia innych funkcji, które są unikatowe dla tej warstwy takich jak:

* Określa liczbę przestrzeni nazw w klastrze.
* Określa ograniczenia przepływności na każdym obszary nazw.
* Konfiguruje liczbę centra zdarzeń w każdej przestrzeni nazw.
* Określa limit liczby partycji.

Ta usługa jest przeznaczona dla użytkowników dane telemetryczne największą i jest dostępny dla klientów z umową enterprise.

## <a name="how-to-onboard"></a>Jak dołączyć

Wydajność można skalować w górę lub w dół w ciągu miesiąca do własnych potrzeb, dodając lub usuwając CUs. Dedykowany plan jest unikatowa, w tym wystąpią bardziej praktyczne dołączania od zespołu produktu usługi Event Hubs można pobrać elastyczne wdrożenia, które jest odpowiednie dla Ciebie. Aby rozpocząć korzystanie z tej wersji [skontaktuj się z obsługą rozliczeń](https://ms.portal.azure.com/#create/Microsoft.Support) lub przedstawicielem firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać więcej informacji na temat pojemności dedykowanego centra zdarzeń. Można też uzyskać więcej o usłudze Event Hubs warstw cenowych odwiedzając następujące linki:

- [Cennik dedykowanego centra zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/). Można również skontaktować się sprzedaży przedstawicielem firmy Microsoft lub Support firmy Microsoft, aby uzyskać więcej informacji na temat pojemności dedykowanego centra zdarzeń.
- [Centra zdarzeń często zadawane pytania dotyczące](event-hubs-faq.md) zawiera informacje o cenach i odpowiedzi na niektóre często zadawane pytania dotyczące usługi Event Hubs. 
