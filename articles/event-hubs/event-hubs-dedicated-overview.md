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
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708012"
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie usługi Event Hubs w wersji dedykowanej

*Zdarzenie Hubs klastry* oferują wdrożenia pojedynczej dzierżawy dla klientów korzystających z najbardziej wymagające potrzeby przesyłania strumieniowego. Ta oferta pojedynczej dzierżawy ma gwarantowanej umowy SLA dostępność przez 99,99% i jest dostępna tylko w naszych dedykowane warstwy cenowej. Klaster usługi Event Hubs można ruch przychodzący miliony zdarzeń na sekundę z gwarantowaną pojemności i nieprzekraczającymi sekundy opóźnieniami. Przestrzenie nazw i centrów zdarzeń utworzonych w dedykowanym klastrze obejmują wszystkie funkcje standardowe oferty i inne, ale bez żadnych ograniczeń transferu danych przychodzących. Obejmuje również popularnej [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) funkcji bez dodatkowych kosztów, co pozwala na automatyczne usługi batch i dziennika strumienie danych do usługi Azure Storage lub Azure Data Lake. 

Dedykowane klastry są aprowizowane i opłata jest naliczana według **jednostek pojemności (CUs)**, wstępnie przydzielić ilości zasobów Procesora i pamięci. Możesz kupić 1, 2, 4, 8, 12, 16 lub 20 jednostek pojemności dla każdego klastra. Ile pozyskiwać i przesyłanie strumieniowe na jednostkę pojemności zależy od wielu czynników, takich jak liczba producentów i konsumentów, ładunek kształtu, ruch wychodzący szybkości (Zobacz więcej szczegółów zawierają wyniki testów porównawczych poniżej). 

## <a name="why-dedicated"></a>Dlaczego w wersji dedykowanej?

Dedykowane usługi Event Hubs zapewnia trzy atrakcyjnych korzyści dla klientów potrzebujących pojemność na poziomie przedsiębiorstwa:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Pojedynczej dzierżawy gwarantuje pojemności w celu zapewnienia lepszej wydajności

Dedykowany klaster gwarantuje pojemności w pełnej skali, a ruch przychodzący do gigabajty danych przesyłanych strumieniowo z pełni trwałe magazynu i nieprzekraczającymi sekundy opóźnieniami, aby uwzględnić dowolny zwiększyć w ruchu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Włączne i wyłączne dostęp do funkcji 
Dedykowana oferta obejmuje funkcje, takie jak przechwytywanie w nie dodatkowych kosztów, a także wyłączny dostęp do przyszłych funkcji, takich jak BYOK. Usługa również zarządza równoważenia obciążenia, systemu operacyjnego aktualizacje, poprawki zabezpieczeń i partycjonowania dla klientów, dzięki czemu możesz poświęcać mniej czasu na konieczność konserwacji infrastruktury i więcej czasu na tworzeniu funkcji, po stronie klienta.  

#### <a name="cost-savings"></a>Oszczędności kosztów
Na woluminy wysokiej transferu danych przychodzących (> 100 jednostek przepływności), koszty klastra liczbą znacznie mniejszą za godzinę od zakupu porównywalne ilość jednostek przepływności w ramach oferty Standard.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs w warstwie standardowa programu vs. Dedykowany

W poniższej tabeli porównano warstwy dostępna usługa Event Hubs. Event hubs w warstwie dedykowana oferta jest rozliczana według stałej stawki naliczanej miesięcznie, w porównaniu do użycia ceny dla większości funkcji standardu. Dedykowany warstwa oferuje wszystkie funkcje plan w warstwie standardowa, ale o pojemności skali przedsiębiorstwa dla klientów wymagających obciążeń. 

| Cecha | Standardowa (Standard) | Dedykowany |
| --- |:---:|:---:|
| Zdarzenia związane z transferem danych przychodzących | Płatność za milion zdarzeń | Dołączono |
| Jednostka przepływności (1 MB/s danych przychodzących, 2 MB/s danych wychodzących) | Płatność za godzinę | Dołączono |
| Rozmiar komunikatu | 1 MB | 1 MB |
| Partycje | 40 przestrzeni nazw | 2000 na jednostkę pojemności |
| Grupy odbiorców | 20 za Centrum zdarzeń | 1000 na Centrum zdarzeń |
| Maksymalnie z Przepustowość | 20 jednostek przepływności (maksymalnie 40 jednostek przepływności)    | 20 jednostek pojemności |
| Połączenia obsługiwane przez brokera | 1000 uwzględnione | 100 tys. w cenie |
| Przechowywanie komunikatów | 1 dzień w cenie | Liczba uwzględnianych dni: do 7 |
| Przechwytywanie | Płatność za godzinę | Dołączono |

## <a name="what-can-i-achieve-with-a-cluster"></a>Co można osiągnąć za pomocą klastra?

Dla klastra usługi Event Hubs ile pozyskiwania i przesyłanie strumieniowe zależy od różnych czynników, takich jak usługi producentów konsumentów, szybkość, z jaką są wprowadzane i przetwarzania i wiele więcej. 

Poniższej tabeli przedstawiono wyniki testów porównawczych, że możemy osiągnąć podczas testowania naszych:

| Kształt ładunku | Odbiorniki | Przepustowość ruchu przychodzącego| Komunikaty przychodzące | Przepustowość danych wychodzących | Komunikaty wychodzące | Łączna liczba jednostek przepływności | Jednostek przepływności na jednostkę pojemności |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | 400 wiad KB/s | 800 MB/s | 800 KB wiad. / s | 400 jednostek przepływności | 100 jednostek przepływności | 
| Partie 10x10KB | 2 | 666 MB/s | 66.6 wiad KB/s | 1.33 GB/s | 133 wiad KB/s | 666 jednostek przepływności | 166 jednostek przepływności |
| Partie 6x32KB | 1 | 1,05 GB/s | 34 wiad KB / s | 1,05 GB/s | 34 wiad KB/s | 1000 jednostek przepływności | 250 jednostek przepływności |

Podczas testowania, użyto następujących kryteriów:

- Klaster warstwy dedykowana dla usługi Event Hubs przy użyciu czterech jednostek pojemności (CUs) został użyty. 
- Centrum zdarzeń, umożliwiający wprowadzanie ma partycje 200. 
- Dane, które zostało pozyskane została odebrana przez dwie aplikacje odbiorcy odbieranie od wszystkich partycji.

## <a name="how-to-onboard"></a>Jak dołączyć

Aby dołączyć do tej jednostki SKU [skontaktuj się z działem pomocy technicznej dotyczącej rozliczeń](https://ms.portal.azure.com/#create/Microsoft.Support) lub przedstawicielem firmy Microsoft. Pojemności można skalować w górę lub w dół w ciągu miesiąca do swoich potrzeb przez dodanie lub usunięcie jednostki pojemności. Dedykowanego planu jest unikatowy, wówczas będą pojawiać się w praktyce dołączania od zespołu produktu usługi Event Hubs, aby uzyskać elastyczne wdrożenia, który jest odpowiedni dla Ciebie. 

## <a name="next-steps"></a>Kolejne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać dodatkowe szczegóły dotyczące Event Hubs dedykowanej pojemności. Możesz także dowiedzieć się więcej o usłudze Event Hubs w warstwach cenowych, odwiedź następujące linki:

- [Usługa Event hubs w warstwie dedykowana — cennik](https://azure.microsoft.com/pricing/details/event-hubs/). Można również skontaktować się z przedstawicielem handlowym firmy Microsoft lub Microsoft Support, aby uzyskać dodatkowe szczegóły dotyczące Event hubs w warstwie dedykowana pojemność.
- [— Często zadawane pytania dla centrów zdarzeń](event-hubs-faq.md) zawiera informacje o cenach i odpowiedzi na niektóre często zadawane pytania dotyczące usługi Event Hubs.
