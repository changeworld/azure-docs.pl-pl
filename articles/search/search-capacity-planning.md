---
title: Przydzielić partycje i repliki na potrzeby zapytań i indeksowania — usługa Azure Search
description: Dopasowywanie partycji i replik zasobów komputera w usłudze Azure Search, gdzie każdy zasób jest rozliczana w jednostkach płatnych wyszukiwania.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e2eff6c854dae48961700341a6db19dc7113901c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316118"
---
# <a name="allocate-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Przydziel partycje i repliki dla zapytań i indeksowania obciążeń w usłudze Azure Search
Po zakończeniu [wybierz warstwę cenową](search-sku-tier.md) i [aprowizować usługę wyszukiwania](search-create-service-portal.md), następnym krokiem jest opcjonalnie zwiększyć liczbę repliki lub partycje używane przez usługę. Każda warstwa oferuje stałą liczbę jednostek rozliczeniowych. W tym artykule wyjaśniono, jak można przydzielić tych jednostek, aby osiągnąć optymalną konfigurację, która równoważy wymagań dotyczących wykonywania zapytania, indeksowania i magazynu.

Konfiguracja zasobu jest dostępna podczas konfigurowania usługi po [warstwa podstawowa](https://aka.ms/azuresearchbasic) lub jednego z [warstwy standardowa](search-limits-quotas-capacity.md). W przypadku usług w tych warstwach zostanie kupiona pojemność, z przyrostem równym *wyszukiwanie jednostek* (SUs) gdzie każdej partycji i replik jest liczona jako jeden SU. 

Użycie mniejszej liczby wyników SUs w obniżenie proporcjonalnie. Rozliczenia są włączone dla tak długo, jak skonfigurowano usługę. Możesz tymczasowo nie korzystania z usługi, jedynym sposobem, aby uniknąć rozliczeń jest usunięcie usługi, a następnie ponowne utworzenie go, gdy ich potrzebujesz.

> [!Note]
> Usunięcie usługi spowoduje usunięcie wszystkiego w nim. Nie ma możliwości w ramach usługi Azure Search do wykonywania kopii zapasowych i przywracanie utrwalone dane wyszukiwania. Aby przeprowadzić ponowne wdrożenie istniejącego indeksu na nową usługę, należy uruchomić program używany do tworzenia i załaduj go pierwotnie. 

## <a name="terminology-partitions-and-replicas"></a>Terminologii: partycje i repliki
Partycje i repliki są podstawowe zasoby obsługujące usługi wyszukiwania.

| Zasób | Definicja |
|----------|------------|
|*Partycje* | Udostępnia magazyn indeksów i we/wy operacji odczytu/zapisu (na przykład, gdy przebudowa lub odświeżanie indeks).|
|*Repliki* | Wystąpienia usługi wyszukiwania używane głównie w celu operacje zapytań równoważenia obciążenia. Każdej repliki zawsze hostuje jedną kopię indeksu. Jeśli masz 12 replik, masz 12 kopie każdego indeksu załadowane w usłudze.|

> [!NOTE]
> Nie ma możliwości do bezpośrednio obsługi i zarządzania, których indeksy Uruchom w replice. Po jednej kopii każdego indeksu dla każdej repliki jest częścią architektury usługi.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Jak przydzielić partycje i repliki
W usłudze Azure Search to usługa początkowo jest przydzielany minimalny poziom zasoby składające się z jednej partycji i jednej z replik. W przypadku warstw, które go obsługują zasoby obliczeniowe można dostosować przyrostowe, zwiększając partycji, jeśli potrzebujesz więcej pamięci i operacji We/Wy, lub Dodaj więcej repliki dla większych woluminów zapytania lub lepszą wydajność. Pojedyncza usługa musi mieć wystarczające zasoby do obsługi wszystkich obciążeń (indeksowanie i zapytania). Nie można podzielić obciążenia między wiele usług.

Aby zwiększyć lub zmień przydział replik i partycji, zaleca się przy użyciu witryny Azure portal. Portal wymusza limity dozwolone połączenia, które pozostają poniżej maksymalny limit:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz usługę wyszukiwania.
2. W **ustawienia**, otwórz **skalowania** bloku i użyj suwaków, aby zwiększyć lub zmniejszyć liczbę partycji i replik.

Jeśli potrzebujesz opartych na skryptach lub oparte na kodzie metody inicjowania obsługi administracyjnej, [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/services) stanowi alternatywę dla portalu.

Ogólnie rzecz biorąc Wyszukaj aplikacje muszą większa liczba replik niż partycje, szczególnie w przypadku, gdy operacje usług są obciążona kierunku obciążeń związanych z zapytaniami. W sekcji na [wysokiej dostępności](#HA) wyjaśnia, dlaczego.

> [!NOTE]
> Po aprowizacji usługi nie można uaktualnić do wyższej wersji jednostki SKU. Należy utworzyć usługę wyszukiwania w nowej warstwie i załaduj ponownie indeksów. Zobacz [Tworzenie usługi Azure Search w portalu](search-create-service-portal.md) Aby uzyskać pomoc dotyczącą usługi aprowizacji.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Wysoka dostępność
Ponieważ jest stosunkowo szybkie skalowanie w górę, ogólnie zaleca się uruchomić jedną partycję, oraz jedną lub dwie repliki, a następnie skalowania w górę jako woluminy kwerendy kompilacji. Uruchamianie obciążeń związanych z zapytaniami głównie w replikach. Jeśli potrzebujesz więcej przepływności lub o wysokiej dostępności, prawdopodobnie będziesz potrzebować dodatkowych replik.

Ogólne zalecenia dotyczące wysokiej dostępności są:

* Dwie repliki w celu zapewnienia wysokiej dostępności obciążeń (zapytania) tylko do odczytu
* Co najmniej trzech replik wysokiej dostępności obciążeń odczytu/zapisu (zapytań oraz indeksowania jako pojedyncze dokumenty są dodawane, aktualizowane lub usuwane)

Umowy dotyczące poziomu usług (SLA) dla usługi Azure Search są stosowane w operacjach zapytań i aktualizacji indeksu, które składają się z Dodawanie, aktualizowanie lub usuwanie dokumentów.

Warstwa podstawowa wierzchołki w jednej partycji i trzech replik. Jeśli chcesz, aby mogli natychmiastowe odpowiadanie na wahania zapotrzebowanie na przepustowość indeksowania i zapytania, należy rozważyć użycie jednej z warstwy standardowa.

### <a name="index-availability-during-a-rebuild"></a>Indeks dostępności podczas ponownej kompilacji

Wysoka dostępność dla usługi Azure Search odnoszą się do zapytań i aktualizacji indeksu, które nie obejmują odbudowanie indeksu. Jeśli usuwanie pola, zmiana typu danych lub zmienić nazwę pola, konieczne będzie odbudowanie indeksu. Do odbudowania indeksu, należy usunąć indeks, ponownie utwórz indeks i ponownie Załaduj dane.

> [!NOTE]
> Nowe pola można dodać do indeksu usługi Azure Search, bez odbudowanie indeksu. Wartość nowego pola będą mieć wartości null dla wszystkich dokumentów w indeksie.

Aby zachować dostępność indeksu podczas ponownej kompilacji, musi mieć kopię indeks o innej nazwie w tej samej usługi lub kopię indeks o takiej samej nazwie w innej usłudze, a następnie podaj przekierowania lub logiki trybu failover, w kodzie.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Obecnie nie istnieje wbudowany mechanizm odzyskiwania po awarii. Dodawanie repliki lub partycje będą nieprawidłowe strategii realizacji celów odzyskiwania po awarii. Najbardziej typowym podejściem jest do dodawania nadmiarowości na poziomie usługi, konfigurując drugiej usługi wyszukiwania w innym regionie. Podobnie jak w przypadku dostępności podczas odbudowywanie indeksu, przekierowywanie lub logiki trybu failover muszą pochodzić z kodu.

## <a name="increase-query-performance-with-replicas"></a>Zwiększ wydajność zapytań za pomocą repliki
Opóźnienie kwerendy jest wskazuje, że potrzebne są dodatkowe repliki. Ogólnie rzecz biorąc pierwszy krok w kierunku poprawę wydajności zapytań jest dodanie więcej z tego zasobu. W miarę dodawania repliki dodatkowe kopie indeksu są przełączyć do trybu online do obsługi większych obciążeń związanych z zapytaniami i załadować saldo żądania za pośrednictwem wielu replik.

Firma Microsoft nie może dostarczyć twardych oszacowania zapytań na sekundę (QPS): zapytanie wydajność zależy od złożoności zapytania i konkurencyjnych obciążeń. Mimo że dodawanie replik wyraźnie w wyniku lepszą wydajność, wynik nie jest ściśle liniowa: Dodawanie trzech replik nie gwarantuje Potrójna przepływności.

Aby uzyskać wskazówki dotyczące szacowania liczby zapytań na Sekundę dla obciążeń, zobacz [zagadnienia dotyczące wydajności i optymalizacji usługi Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Zwiększ wydajność indeksowania z partycjami
Aplikacji wyszukiwania, które wymagają niemal odświeżanie danych w czasie rzeczywistym, należy proporcjonalnie więcej partycji niż replik. Dodawanie partycji się operacje odczytu/zapisu między większą liczbą zasobów obliczeniowych. Oferuje on również więcej miejsca na dysku do przechowywania dodatkowych indeksów i dokumentów.

Indeksy większe dłużej do wykonywania zapytań. Jako takie może się okazać, że każdy przyrostowych wzrostu partycje wymaga mniejszy, ale proporcjonalna wzrost replik. Złożoności zapytania i wolumin zapytań będzie wziąć pod uwagę na szybkość wykonywania zapytania włączono.

## <a name="basic-tier-partition-and-replica-combinations"></a>Warstwa podstawowa: Kombinacji partycji i replik
Podstawowa usługa mogą mieć dokładnie jedną partycję i maksymalnie trzech replik dla maksymalnie ograniczyć trzech serwerów SUS. Tylko zmienianych zasób jest replik. Należy co najmniej dwóch replik wysokiej dostępności dla zapytań.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Warstwy standardowa: Kombinacji partycji i replik
W poniższej tabeli przedstawiono SUs wymagane do obsługi kombinacje replik i partycji, z zastrzeżeniem limit 36 SU dla wszystkich warstw standardowa.

|   | **1 partycji** | **2 partycjach** | **3 partycji** | **4 partycje** | **partycje 6** | **12 partycjami** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 repliki** |1 JEDNOSTKA WYSZUKIWANIA |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliki** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliki** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replik** |4 SU |8 SU |12 SU |16 SU |24 SU |ND |
| **5 repliki** |5 SU |10 SU |15 SU |20 SU |30 SU |ND |
| **6 replik** |6 SU |12 SU |18 SU |24 SU |36 SU |ND |
| **12 repliki** |12 SU |24 SU |36 SU |ND |ND |ND |

SUs, ceny i wydajności są szczegółowo opisane w witrynie sieci Web platformy Azure. Aby uzyskać więcej informacji, zobacz [— szczegóły cennika](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Liczba replik i partycji dzieli bez reszty do 12 (w szczególności 1, 2, 3, 4, 6, 12). Jest to spowodowane usługi Azure Search wstępnie dzieli każdy indeks na 12 fragmentów, aby możliwe było ich rozmieszczenie w obszarach równy wszystkich partycji. Na przykład jeśli usługa ma trzy partycje i utworzyć indeks, każda partycja będzie zawierać cztery fragmenty indeksu. W jaki sposób usługi Azure Search fragmentów na indeks jest szczegółowo opisuje implementacja, mogą ulec zmianie w przyszłych wersjach. Mimo że liczba 12 już dziś, nie należy oczekiwać tę liczbę, aby zawsze mieć w przyszłości 12.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Formuła rozliczeń dla replik i partycji zasobów
Obliczanie ilości jednostek przesyłania strumieniowego są używane do konkretnej ich kombinacji jest produktem replik i partycji, lub (R X P = SU). Na przykład trzech replik pomnożona przez trzy partycje jest rozliczana jako dziewięć SUs.

Koszt na SU jest określany przez warstwie wolniejszym tempie rozliczeń na jednostkę Basic niż dla warstwy standardowa. Według stawek dla poszczególnych warstw można znaleźć na [— szczegóły cennika](https://azure.microsoft.com/pricing/details/search/).
