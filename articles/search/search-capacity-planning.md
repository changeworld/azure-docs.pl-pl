---
title: Dostosowywanie pojemności obciążeń zapytań i indeksów
titleSuffix: Azure Cognitive Search
description: Dostosuj zasoby komputera partycji i replik w usłudze Azure Cognitive Search, gdzie każdy zasób jest wyceniany w jednostkach wyszukiwania podlegających rozliczaniu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546264"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Dostosowywanie pojemności w usłudze Azure Cognitive Search

Przed [inicjowania obsługi administracyjnej usługi wyszukiwania](search-create-service-portal.md) i blokowania w określonej warstwie cenowej, poświęć kilka minut, aby zrozumieć rolę replik i partycji w usłudze i jak można dostosować usługę, aby pomieścić skoki i spadki popytu na zasoby.

Pojemność jest funkcją [warstwy, którą wybierzesz](search-sku-tier.md) (warstwy określają charakterystyki sprzętu) i kombinacji replik i partycji niezbędnych dla przewidywanych obciążeń. W zależności od warstwy i rozmiaru regulacji dodawanie lub zmniejszanie pojemności może potrwać od 15 minut do kilku godzin. 

Podczas modyfikowania alokacji replik i partycji, zaleca się przy użyciu witryny Azure portal. Portal wymusza limity dopuszczalnych kombinacji, które pozostają poniżej maksymalnych limitów warstwy. Jeśli jednak potrzebujesz podejścia do inicjowania obsługi administracyjnej opartej na skryptach lub kodach, [interfejs Azure PowerShell](search-manage-powershell.md) lub [interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/services) są rozwiązaniami alternatywnymi.

## <a name="terminology-replicas-and-partitions"></a>Terminologia: repliki i partycje

|||
|-|-|
|*Partycje* | Zapewnia magazyn indeksu i operacje we/wy dla operacji odczytu/zapisu (na przykład podczas przebudowy lub odświeżania indeksu). Każda partycja ma udział w indeksie sumy. Jeśli przydzielisz trzy partycje, indeks jest podzielony na trzecie. |
|*Repliki* | Wystąpienia usługi wyszukiwania, używane głównie do operacji równoważenia obciążenia kwerendy. Każda replika jest jedną kopią indeksu. Jeśli przydzielisz trzy repliki, będziesz mieć trzy kopie indeksu dostępne do obsługi żądań kwerend.|

## <a name="when-to-add-nodes"></a>Kiedy dodać węzły

Początkowo usługa jest przydzielana minimalny poziom zasobów składający się z jednej partycji i jednej repliki. 

Pojedyncza usługa musi mieć wystarczające zasoby do obsługi wszystkich obciążeń (indeksowanie i kwerendy). Żadne obciążenie nie działa w tle. Można zaplanować indeksowanie dla czasów, gdy żądania zapytań są naturalnie rzadsze, ale usługa w przeciwnym razie nie będzie priorytetyskuj jedno zadanie nad innym. Ponadto pewna ilość nadmiarowości wygładza wydajność kwerendy, gdy usługi lub węzły są aktualizowane wewnętrznie.

Zgodnie z ogólną zasadą aplikacje wyszukiwania zwykle potrzebują więcej replik niż partycje, szczególnie gdy operacje usługi są stronnicze w kierunku obciążeń zapytań. Sekcja dotycząca [wysokiej dostępności](#HA) wyjaśnia, dlaczego.

> [!NOTE]
> Dodanie większej liczby replik lub partycji zwiększa koszt uruchomienia usługi i może wprowadzić niewielkie różnice w sposobie zamawiania wyników. Pamiętaj, aby sprawdzić [kalkulator cen,](https://azure.microsoft.com/pricing/calculator/) aby zrozumieć implikacje rozliczeniowe związane z dodawaniem większej liczby węzłów. Poniższy [wykres](#chart) może pomóc w dowiązaniu do liczby jednostek wyszukiwania wymaganych dla określonej konfiguracji. Aby uzyskać więcej informacji na temat wpływu dodatkowych replik na przetwarzanie kwerendy, zobacz [Kolejność wyników](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Jak przydzielić repliki i partycje

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz usługę wyszukiwania.

1. W **obszarze Ustawienia**otwórz stronę **Skalowanie,** aby zmodyfikować repliki i partycje. 

   Poniższy zrzut ekranu przedstawia standardową usługę aprowizowana za pomocą jednej repliki i partycji. Formuła na dole wskazuje, ile jednostek wyszukiwania jest używanych (1). Jeśli cena jednostkowa wynosiła $100 (nie rzeczywista cena), miesięczny koszt uruchomienia tej usługi wyniósłby średnio $100.

   ![Skaluj stronę z bieżącymi wartościami](media/search-capacity-planning/1-initial-values.png "Skaluj stronę z bieżącymi wartościami")

1. Użyj suwaka, aby zwiększyć lub zmniejszyć liczbę partycji. Formuła u dołu wskazuje, ile jednostek wyszukiwania jest używanych.

   W tym przykładzie podwaja pojemność, z dwóch replik i partycji każdego. Zwróć uwagę na liczbę jednostek wyszukiwania; jest teraz cztery, ponieważ formuła rozliczeń jest replikami pomnożonymi przez partycje (2 x 2). Podwojenie pojemności ponad dwukrotnie podwaja koszt uruchomienia usługi. Jeśli koszt jednostki wyszukiwania wynosił 100 USD, nowy miesięczny rachunek wyniesie teraz 400 USD.

   W przypadku bieżących kosztów jednostkowych każdej warstwy odwiedź [stronę Cennik](https://azure.microsoft.com/pricing/details/search/).

   ![Dodawanie replik i partycji](media/search-capacity-planning/2-add-2-each.png "Dodawanie replik i partycji")

1. Kliknij **przycisk Zapisz,** aby potwierdzić zmiany.

   ![Potwierdzanie zmian w skalowaniu i rozliczaniu](media/search-capacity-planning/3-save-confirm.png "Potwierdzanie zmian w skalowaniu i rozliczaniu")

   Zmiany w pojemności trwać kilka godzin. Nie można anulować po rozpoczęciu procesu i nie ma monitorowania w czasie rzeczywistym dla repliki i partycji korekty. Jednak poniższy komunikat pozostaje widoczny podczas zmian.

   ![Komunikat o stanie w portalu](media/search-capacity-planning/4-updating.png "Komunikat o stanie w portalu")

> [!NOTE]
> Po aprowizacji usługi nie można uaktualnić do wyższej warstwy. Należy utworzyć usługę wyszukiwania w nowej warstwie i ponownie załadować indeksy. Zobacz [Tworzenie usługi Azure Cognitive Search w portalu, aby](search-create-service-portal.md) uzyskać pomoc dotyczącą inicjowania obsługi administracyjnej usługi.
>
> Ponadto partycje i repliki są zarządzane wyłącznie i wewnętrznie przez usługę. Nie istnieje pojęcie koligacji procesora lub przypisywanie obciążenia do określonego węzła.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinacje partycji i replik

Usługa Podstawowa może mieć dokładnie jedną partycję i maksymalnie trzy repliki, dla maksymalnego limitu trzech SU. Jedynym regulowanym zasobem są repliki. Potrzebujesz co najmniej dwóch replik dla wysokiej dostępności w kwerendach.

Wszystkie usługi wyszukiwania zoptymalizowane pod kątem standardowego i magazynu mogą zakładać następujące kombinacje replik i partycji, z zastrzeżeniem limitu 36 SU. 

|   | **1 partycja** | **2 partycje** | **3 partycje** | **4 partycje** | **6 partycji** | **12 partycji** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 su |2 su |3 su |4 su |6 su |12 su |
| **2 repliki** |2 su |4 su |6 su |8 su |12 su |24 su |
| **3 repliki** |3 su |6 su |9 su |12 su |18 su |36 su |
| **4 repliki** |4 su |8 su |12 su |16 su |24 su |Nie dotyczy |
| **5 replik** |5 su |10 su |15 su |20 su |30 su |Nie dotyczy |
| **6 replik** |6 su |12 su |18 su |24 su |36 su |Nie dotyczy |
| **12 replik** |12 su |24 su |36 su |Nie dotyczy |Nie dotyczy |Nie dotyczy |

SUs, cen i pojemności są szczegółowo wyjaśnione w witrynie sieci Web platformy Azure. Aby uzyskać więcej informacji, zobacz [Szczegóły cen](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Liczba replik i partycji dzieli się równomiernie na 12 (w szczególności 1, 2, 3, 4, 6, 12). Dzieje się tak, ponieważ usługa Azure Cognitive Search wstępnie dzieli każdy indeks na 12 fragmentów, dzięki czemu można go rozłożyć w równych częściach na wszystkie partycje. Na przykład jeśli usługa ma trzy partycje i utworzyć indeks, każda partycja będzie zawierać cztery fragmenty indeksu. Jak Azure Cognitive Search fragmenty indeksu jest szczegóły implementacji, mogą ulec zmianie w przyszłych wersjach. Chociaż liczba ta wynosi dziś 12, nie należy oczekiwać, że liczba ta będzie zawsze 12 w przyszłości.
>

<a id="HA"></a>

## <a name="high-availability"></a>Wysoka dostępność

Ponieważ skalowanie w górę jest łatwe i stosunkowo szybkie, zazwyczaj zaleca się rozpoczęcie pracy z jedną partycją i jedną lub dwiema replikami, a następnie skalowanie w górę jako tworzenie woluminów zapytań. Obciążenia kwerendy są uruchamiane głównie w replikach. Jeśli potrzebujesz więcej przepływności lub wysokiej dostępności, prawdopodobnie będzie wymagać dodatkowych replik.

Ogólne zalecenia dotyczące wysokiej dostępności to:

* Dwie repliki o wysokiej dostępności obciążeń tylko do odczytu (kwerendy)

* Trzy lub więcej replik o wysokiej dostępności obciążeń odczytu/zapisu (zapytania plus indeksowanie w miarę dodawania, aktualizowanie lub usuwanie pojedynczych dokumentów)

Umowy dotyczące poziomu usług (SLA) dla usługi Azure Cognitive Search są przeznaczone dla operacji zapytań i aktualizacji indeksu, które polegają na dodawaniu, aktualizowaniu lub usuwaniu dokumentów.

Warstwa podstawowa jest na szczycie jednej partycji i trzech replik. Jeśli chcesz, aby elastyczność natychmiast reagować na wahania popytu zarówno indeksowania i przepływności zapytań, należy wziąć pod uwagę jedną z warstw standardu.  Jeśli okaże się, że wymagania dotyczące magazynu rosną znacznie szybciej niż przepływność zapytania, należy wziąć pod uwagę jedną z warstw zoptymalizowanych pod kątem magazynu.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Obecnie nie ma wbudowanego mechanizmu odzyskiwania po awarii. Dodawanie partycji lub replik byłoby złą strategią dla realizacji celów odzyskiwania po awarii. Najczęstszym podejściem jest dodanie nadmiarowości na poziomie usługi przez skonfigurowanie drugiej usługi wyszukiwania w innym regionie. Podobnie jak w odniesieniu do dostępności podczas przebudowy indeksu, logika przekierowania lub pracy awaryjnej musi pochodzić z kodu.

## <a name="estimate-replicas"></a>Szacowanie replik

W usłudze produkcyjnej należy przydzielić trzy repliki do celów umowy SLA. Jeśli wystąpi niska wydajność kwerendy, można dodać repliki, dzięki czemu dodatkowe kopie indeksu są przenoszone do trybu online do obsługi większych obciążeń zapytań i równoważenia obciążenia żądań za pośrednictwem wielu replik.

Nie udostępniamy wskazówek dotyczących liczby replik potrzebnych do uwzględnienia obciążeń zapytań. Wydajność kwerendy zależy od złożoności zapytania i konkurencyjnych obciążeń. Chociaż dodanie replik wyraźnie powoduje lepszą wydajność, wynik nie jest ściśle liniowy: dodanie trzech replik nie gwarantuje potrójnej przepływności.

Aby uzyskać wskazówki dotyczące szacowania QPS dla rozwiązania, zobacz [Skalowanie zapytań dotyczących wydajności](search-performance-optimization.md)i [monitorowanie](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Szacowanie partycji

[Wybrana warstwa](search-sku-tier.md) określa rozmiar i szybkość partycji, a każda warstwa jest zoptymalizowana wokół zestawu cech, które pasują do różnych scenariuszy. Jeśli wybierzesz warstwę wyższej klasy, może być konieczne mniej partycji niż w przypadku korzystania z S1. Jednym z pytań, na które musisz odpowiedzieć za pomocą testów kierowanych przez siebie, jest to, czy większa i droższa partycja zapewnia lepszą wydajność niż dwie tańsze partycje w usłudze aprowizowana w niższej warstwie.

Aplikacje wyszukiwania, które wymagają odświeżania danych w czasie zbliżonym do rzeczywistego, będą potrzebować proporcjonalnie więcej partycji niż repliki. Dodawanie partycji rozkładów operacji odczytu/zapisu w większej liczbie zasobów obliczeniowych. Zapewnia również więcej miejsca na dysku do przechowywania dodatkowych indeksów i dokumentów.

Większe indeksy trwać dłużej do kwerendy. W związku z tym może się okazać, że każdy przyrostowy wzrost partycji wymaga mniejszego, ale proporcjonalnego zwiększenia liczby replik. Złożoność zapytań i woluminu zapytania będzie czynnikiem, jak szybko wykonanie kwerendy jest odwrócony.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie warstwy cenowej dla usługi Azure Cognitive Search](search-sku-tier.md)