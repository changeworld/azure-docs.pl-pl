---
title: Omówienie dedykowanych centrów zdarzeń — Usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie dedykowanych centrów zdarzeń platformy Azure, która oferuje wdrożenia centrów zdarzeń z jedną dzierżawą.
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
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516749"
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie dedykowanych centrów zdarzeń

*Klastry centrów zdarzeń* oferują wdrożenia z jedną dzierżawą dla klientów o najbardziej wymagających potrzebach przesyłania strumieniowego. Ta oferta z jedną dzierżawą ma gwarantowaną umowy SLA 99,99% i jest dostępna tylko w naszej dedykowanej warstwie cenowej. Klaster centrum zdarzeń może przychodzących miliony zdarzeń na sekundę z gwarantowaną pojemność i opóźnienie poniżej sekundy. Przestrzenie nazw i centra zdarzeń utworzone w ramach dedykowanego klastra obejmują wszystkie funkcje oferty standardowej i inne, ale bez żadnych limitów transferu danych przychodzących. Zawiera również popularną funkcję [przechwytywania centrów zdarzeń](event-hubs-capture-overview.md) bez dodatkowych kosztów, co pozwala automatycznie wsadować i rejestrować strumienie danych do usługi Azure Storage lub usługi Azure Data Lake. 

Klastry są aprowizowane i rozliczane przez **jednostki pojemności (CU),** wstępnie przydzieloną ilość zasobów procesora CPU i pamięci. Dla każdego klastra można kupić 1, 2, 4, 8, 12, 16 lub 20 punktów zakupu. Ilość pozyskiwania i przesyłania strumieniowego na cu zależy od różnych czynników, takich jak liczba producentów i konsumentów, kształt ładunku, szybkość wyjścia (więcej informacji znajdziesz w poniższych wynikach benchmarku). 

> [!NOTE]
> Wszystkie klastry centrów zdarzeń są domyślnie włączone do platformy Kafka i obsługują punkty końcowe platformy Kafka, które mogą być używane przez istniejące aplikacje oparte na platformie Kafka. Włączenie platformy Kafka w klastrze nie ma wpływu na przypadki użycia nienawiązane z platformą kafka; nie ma opcji lub trzeba wyłączyć kafka w klastrze.

## <a name="why-dedicated"></a>Dlaczego dedykowane?

Dedykowane centra eventów oferują trzy atrakcyjne korzyści dla klientów, którzy potrzebują pojemności na poziomie przedsiębiorstwa:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Pojedynczy najem gwarantuje zdolność do lepszej wydajności

Dedykowany klaster gwarantuje pojemność w pełnej skali i może przenosić się do gigabajtów danych przesyłania strumieniowego z w pełni trwałą pamięcią masową i opóźnieniem poniżej sekundy, aby pomieścić każdą serię w ruchu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Integracyjny i wyłączny dostęp do funkcji 
Oferta dedykowana obejmuje funkcje takie jak Capture bez dodatkowych kosztów, a także wyłączny dostęp do nadchodzących funkcji, takich jak Bring Your Own Key (BYOK). Usługa zarządza również równoważeniem obciążenia, aktualizacjami systemu operacyjnego, poprawkami zabezpieczeń i partycjonowania dla klienta, dzięki czemu można poświęcić mniej czasu na konserwację infrastruktury i więcej czasu na tworzenie funkcji po stronie klienta.  

#### <a name="cost-savings"></a>Oszczędności
Przy dużych wolumenach transferu ruchu przychodzącego (>100 jednostek TU) klaster kosztuje znacznie mniej na godzinę niż zakup porównywalnej ilości jednostek przepływności w ofercie Standardowa.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Przydziały i limity dedykowane centrów zdarzeń

Oferta dedykowana centrum zdarzeń jest rozliczana po stałej cenie miesięcznej, przy minimalnym 4 godzinach użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standardowego, ale z pojemnością i limitami skali przedsiębiorstwa dla klientów z wymagającymi obciążeniami. 

| Funkcja | Standardowa | Dedykowane |
| --- |:---:|:---:|
| Przepustowość | 20 procesorów (do 40 procesorów) | 20 j.p. |
| Namespaces |  1 | 50 na CU |
| Usługa Event Hubs |  10 na obszar nazw | 1000 na obszar nazw |
| Zdarzenia przychodzące | Zapłać za milion zdarzeń | Dołączono |
| Rozmiar wiadomości | 1 milion bajtów | 1 milion bajtów |
| Partycje | 32 na Centrum zdarzeń | 1024 na centrum zdarzeń |
| Grupy odbiorców | 20 na Centrum zdarzeń | Brak limitu na CU, 1000 na koncentrator zdarzeń |
| Połączenia brokerskie | 1000 w zestawie, 5000 maks. | 100 K w zestawie i maks. |
| Przechowywanie wiadomości | 7 dni, 84 GB w zestawie na TU | 90 dni, 10 TB w zestawie na CU |
| Przechwytywanie | Płaca za godzinę | Dołączono |

## <a name="how-to-onboard"></a>Jak na pokładzie

Samoobsługowe środowisko [tworzenia klastra centrów zdarzeń](event-hubs-dedicated-cluster-create-portal.md) za pośrednictwem [witryny Azure Portal](https://aka.ms/eventhubsclusterquickstart) jest teraz w wersji zapoznawczej. Jeśli masz jakieś pytania lub potrzebujesz pomocy w dołączaniu do usługi Event Hubs Dedicated, skontaktuj się z [zespołem Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Często zadawane pytania

#### <a name="what-can-i-achieve-with-a-cluster"></a>Co mogę osiągnąć za pomocą klastra?

W przypadku klastra usługi Event Hubs, ile można pozyskiwania i przesyłania strumieniowego zależy od różnych czynników, takich jak producenci, konsumenci, szybkość, z jaką są pozyskiwania i przetwarzania i wiele więcej. 

Poniższa tabela przedstawia wyniki testów porównawczych, które osiągnęliśmy podczas naszych testów:

| Kształt ładowności | Odbiorniki | Przepustowość transferu ruchu przychodzącego| Komunikaty przychodzące | Przepustowość ruchu wychodzącego | Wiadomości wychodzące | Całkowita liczba TU | TUs na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | 400k wiadomości/s | 800 MB/s | 800k wiadomości/s | 400 tus | 100 tus | 
| Partie 10x10KB | 2 | 666 MB/s | 66,6 tys. | 1,33 GB/s | 133k wiadomości/s | 666 tus | 166 tus |
| Partie 6x32KB | 1 | 1,05 GB/s | Komunikaty 34k /s | 1,05 GB/s | Komunikaty 34k/s | 1000 tus | 250 tUs |

W badaniu zastosowano następujące kryteria:

- Użyto klastra centrum zdarzeń warstwy dedykowanej z czterema jednostkami pojemności (CU). 
- Centrum zdarzeń używane do pozyskiwania miał 200 partycji. 
- Dane, które zostały pozyskane zostały odebrane przez dwie aplikacje odbiornika odbierające ze wszystkich partycji.

#### <a name="can-i-scale-updown-my-cluster"></a>Czy mogę skalować w górę/w dół mojego klastra?

Po utworzeniu klastry są naliczane za co najmniej 4 godziny użycia. W wersji zapoznawczej środowiska samoobsługowego można przesłać [żądanie pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) do zespołu centrów zdarzeń w obszarze > *przydziału technicznego > żądanie skalowania w górę lub skalowanie w dół dedykowanego klastra* w celu skalowania klastra w górę lub w dół. Może upłynąć do 7 dni, aby zakończyć żądanie skalowania w dół klastra. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Jak geo-DR będzie działać z moim klastrem?

Obszar nazw w klastrze warstwy dedykowanej można połączyć geograficznie z innym obszarem nazw w klastrze warstwy dedykowanej. Nie zachęcamy do parowania obszaru nazw warstwy dedykowanej z obszarem nazw w naszej ofercie standardowej, ponieważ limit przepływności będzie niezgodny, co spowoduje błędy. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Czy mogę migrować moje standardowe przestrzenie nazw, aby należeć do klastra warstwy dedykowanej?
Obecnie nie obsługujemy zautomatyzowanego procesu migracji do migracji danych centrów zdarzeń ze standardowego obszaru nazw do dedykowanego. 

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub pomocą techniczną firmy Microsoft, aby uzyskać dodatkowe informacje na temat dedykowanych centrów zdarzeń. Możesz również utworzyć klaster lub dowiedzieć się więcej o warstwach cenowych usługi Event Hubs, odwiedzając następujące łącza:

- [Tworzenie klastra centrów zdarzeń za pośrednictwem witryny Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Ceny dedykowane dla centrów zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/). Możesz również skontaktować się z przedstawicielem handlowym firmy Microsoft lub pomocą techniczną firmy Microsoft, aby uzyskać dodatkowe informacje na temat dedykowanej pojemności centrum zdarzeń.
- [Często zadawane pytania dotyczące centrów zdarzeń](event-hubs-faq.md) zawierają informacje o cenach i odpowiedzi na niektóre często zadawane pytania dotyczące centrów zdarzeń.
