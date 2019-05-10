---
title: Omówienie dedykowanych event hubs — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie dedykowanej usługi Azure Event Hubs, oferująca wdrożenia pojedynczej dzierżawy usługi event hubs.
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
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4f721dc4fda5bef002c794d79dfd2f054f9eaf38
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511177"
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie usługi Event Hubs w wersji dedykowanej

*Zdarzenie Hubs klastry* oferują wdrożenia pojedynczej dzierżawy dla klientów korzystających z najbardziej wymagające potrzeby przesyłania strumieniowego. Ta oferta pojedynczej dzierżawy ma gwarantowanej umowy SLA dostępność przez 99,99% i jest dostępna tylko w naszych dedykowane warstwy cenowej. Klaster usługi Event Hubs można ruch przychodzący miliony zdarzeń na sekundę z gwarantowaną pojemności i nieprzekraczającymi sekundy opóźnieniami. Przestrzenie nazw i centrów zdarzeń utworzonych w dedykowanym klastrze obejmują wszystkie funkcje standardowe oferty i inne, ale bez żadnych ograniczeń transferu danych przychodzących. Obejmuje również popularnej [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) funkcji bez dodatkowych kosztów, co pozwala na automatyczne usługi batch i dziennika strumienie danych do usługi Azure Storage lub Azure Data Lake. 

Klastry są aprowizowane i opłata jest naliczana według **jednostek pojemności (CUs)**, wstępnie przydzielić ilości zasobów Procesora i pamięci. Możesz kupić 1, 2, 4, 8, 12, 16 lub 20 jednostek pojemności dla każdego klastra. Ile pozyskiwać i przesyłanie strumieniowe na jednostkę pojemności zależy od wielu czynników, takich jak liczba producentów i konsumentów, ładunek kształtu, ruch wychodzący szybkości (Zobacz więcej szczegółów zawierają wyniki testów porównawczych poniżej). 

> [!NOTE]
> Wszystkie klastry usługi Event Hubs są platformy Kafka — domyślnie włączone i obsługują punkty końcowe platformy Kafka, które mogą być używane przez istniejące platformy Kafka na podstawie aplikacji. Posiadanie Kafka włączone na klastrze nie ma wpływu na swoje przypadki użycia innego niż Kafka; nie ma opcji lub trzeba wyłączyć platformy Kafka w klastrze.

## <a name="why-dedicated"></a>Dlaczego w wersji dedykowanej?

Dedykowane usługi Event Hubs zapewnia trzy atrakcyjnych korzyści dla klientów potrzebujących pojemność na poziomie przedsiębiorstwa:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Pojedynczej dzierżawy gwarantuje pojemności w celu zapewnienia lepszej wydajności

Dedykowany klaster gwarantuje pojemności w pełnej skali, a ruch przychodzący do gigabajty danych przesyłanych strumieniowo z pełni trwałe magazynu i nieprzekraczającymi sekundy opóźnieniami, aby uwzględnić dowolny zwiększyć w ruchu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Włączne i wyłączne dostęp do funkcji 
Dedykowana oferta obejmuje funkcje, takie jak przechwytywanie w nie dodatkowych kosztów, a także wyłączny dostęp do przyszłych funkcji, takich jak BYOK. Usługa również zarządza równoważenia obciążenia, systemu operacyjnego aktualizacje, poprawki zabezpieczeń i partycjonowania dla klientów, dzięki czemu możesz poświęcać mniej czasu na konieczność konserwacji infrastruktury i więcej czasu na tworzeniu funkcji, po stronie klienta.  

#### <a name="cost-savings"></a>Oszczędności kosztów
Na woluminy wysokiej transferu danych przychodzących (> 100 jednostek przepływności), koszty klastra liczbą znacznie mniejszą za godzinę od zakupu porównywalne ilość jednostek przepływności w ramach oferty Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Usługa Event Hubs w wersji dedykowanej limity przydziału i ograniczenia

Event hubs w warstwie dedykowana oferta jest rozliczana w stałej miesięcznej cenie, co najmniej 4 godziny użycia. Dedykowane warstwy są dostępne wszystkie funkcje plan w warstwie standardowa, ale o pojemności Skala przedsiębiorstwa i limity dla klientów wymagających obciążeń. 

| Cecha | Standardowa (Standard) | Dedykowane |
| --- |:---:|:---:|
| Przepustowość | 20 jednostek przepływności (maksymalnie 40 jednostek przepływności) | 20 jednostek pojemności |
| Przestrzenie nazw |  1 | 50 na jednostkę pojemności |
| Event Hubs |  10 | Brak limitu tematy centrów zdarzeń |
| Zdarzenia związane z transferem danych przychodzących | Płatność za milion zdarzeń | Dołączone |
| Rozmiar komunikatu | 1 milion bajtów | 1 milion bajtów |
| Partycje | 40 przestrzeni nazw | 2000 na jednostkę pojemności |
| Grupy konsumentów | 20 za Centrum zdarzeń | Dopuszczalne na jednostkę pojemności, 1000 na Centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 1000 uwzględnione | 100 tys. w cenie |
| Okres przechowywania wiadomości | 7 dni, 84 GB na jednostek Przepływności | 90 dni, 10 TB uwzględnione na jednostkę pojemności |
| Przechwyć | Płatność za godzinę | Dołączone |

## <a name="how-to-onboard"></a>Jak dołączyć

Aby dołączyć do Event hubs w warstwie dedykowana, skontaktuj się z [zespołu usługi Event Hubs](mailto:askeventhubs@microsoft.com). Dedykowanego planu jest unikatowy, wówczas będą pojawiać się w praktyce dołączania od zespołu produktu usługi Event Hubs, aby uzyskać elastyczne wdrożenia, który jest odpowiedni dla Ciebie. 

## <a name="faqs"></a>Często zadawane pytania

#### <a name="what-can-i-achieve-with-a-cluster"></a>Co można osiągnąć za pomocą klastra?

Dla klastra usługi Event Hubs ile pozyskiwania i przesyłanie strumieniowe zależy od różnych czynników, takich jak usługi producentów konsumentów, szybkość, z jaką są wprowadzane i przetwarzania i wiele więcej. 

Poniższej tabeli przedstawiono wyniki testów porównawczych, że możemy osiągnąć podczas testowania naszych:

| Kształt ładunku | Odbiorniki | Przepustowość ruchu przychodzącego| Komunikaty przychodzące | Przepustowość danych wychodzących | Komunikaty wychodzące | Łączna liczba jednostek przepływności | Jednostek przepływności na jednostkę pojemności |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | 400 k komunikatów na sekundę | 800 MB/s | 800 KB komunikatów na sekundę | 400 jednostek przepływności | 100 jednostek przepływności | 
| Partie 10x10KB | 2 | 666 MB/s | 66.6 k komunikatów na sekundę | 1.33 GB/s | 133 k komunikatów na sekundę | 666 jednostek przepływności | 166 jednostek przepływności |
| Partie 6x32KB | 1 | 1,05 GB/s | 34 k komunikaty / sekundę | 1,05 GB/s | 34 k komunikatów na sekundę | 1000 jednostek przepływności | 250 jednostek przepływności |

Podczas testowania, użyto następujących kryteriów:

- Klaster warstwy dedykowana dla usługi Event Hubs przy użyciu czterech jednostek pojemności (CUs) został użyty. 
- Centrum zdarzeń, umożliwiający wprowadzanie ma partycje 200. 
- Dane, które zostało pozyskane została odebrana przez dwie aplikacje odbiorcy odbieranie od wszystkich partycji.

#### <a name="can-i-scale-down-my-cluster"></a>Czy mogę skalować w dół do mojego klastra?

Po utworzeniu klastry są naliczane za okres co najmniej 4 godziny użycia. W wersji zapoznawczej środowiska samoobsługowego, możesz przesłać [żądania pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) do zespołu usługi Event Hubs, w obszarze *techniczne > przydziału > żądanie skalowania w górę lub Skaluj w dół dedykowany klaster*. Może potrwać maksymalnie 7 dni, aby ukończyć żądanie, aby skalować w dół klastra. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Jak Geo-DR będą współpracować z mojego klastra?

Możesz to zrobić pary geograficznie przestrzeni nazw w ramach klastra dedykowane warstwy z innej przestrzeni nazw w ramach klastra dedykowane warstwy. Firma Microsoft nie zachęca parowanie warstwy dedykowana przestrzeń nazw z przestrzeni nazw w naszych standardowych oferty, ponieważ limit przepływności będzie niezgodna będą powodować błędy. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Czy mogę zmigrować Moje standardowych przestrzeni nazw, należy do klastra warstwy dedykowana
Firma Microsoft nie obsługujemy proces automatycznej migracji dotyczące migracji danych centra zdarzeń ze standardową przestrzeń nazw dedykowana, jeden. Aby przeprowadzić migrację do klastra z warstwy dedykowana, firma Microsoft zaleca opróżniania po lewej stronie wszelkie komunikaty usługi event hubs warstwy standardowa i zastępując punkty końcowe połączenia, dedykowanej przestrzeni nazw.

## <a name="next-steps"></a>Kolejne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać dodatkowe szczegóły dotyczące Event Hubs dedykowanej pojemności. Możesz także dowiedzieć się więcej o usłudze Event Hubs w warstwach cenowych, odwiedź następujące linki:

- [Usługa Event hubs w warstwie dedykowana — cennik](https://azure.microsoft.com/pricing/details/event-hubs/). Można również skontaktować się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać dodatkowe szczegóły dotyczące Event hubs w warstwie dedykowana pojemność.
- [— Często zadawane pytania dla centrów zdarzeń](event-hubs-faq.md) zawiera informacje o cenach i odpowiedzi na niektóre często zadawane pytania dotyczące usługi Event Hubs.
