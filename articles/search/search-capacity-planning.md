---
title: Skalowanie partycji i replik na potrzeby zapytań i indeksowania — Azure Search
description: Dostosuj zasoby partycji i repliki komputera w Azure Search, w przypadku których poszczególne zasoby są wyceniane w jednostkach wyszukiwania do rozliczenia.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c048dcf31d8f434f742d2da9351ef9b46f0a71d4
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "69650062"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Skalowanie partycji i replik dla obciążeń zapytań i indeksowania w Azure Search
Po [wybraniu warstwy cenowej](search-sku-tier.md) i [zainicjowaniu obsługi administracyjnej usługi wyszukiwania](search-create-service-portal.md)następnym krokiem jest opcjonalne zwiększenie liczby replik lub partycji używanych przez usługę. Każda warstwa oferuje stałą liczbę jednostek rozliczeniowych. W tym artykule wyjaśniono, jak przydzielić te jednostki w celu zapewnienia optymalnej konfiguracji, która równoważy wymagania dotyczące wykonywania zapytań, indeksowania i magazynu.

Konfiguracja zasobów jest dostępna podczas konfigurowania usługi w [warstwie Podstawowa](https://aka.ms/azuresearchbasic) lub jednej z [warstw zoptymalizowanych pod kątem magazynu](search-limits-quotas-capacity.md). W przypadku usług w tych warstwach pojemność jest kupowana w przyrostach *jednostek wyszukiwania* (SUs), w których każda partycja i replika są liczone jako jeden su. 

Użycie mniejszego poziomu usługi SUs spowoduje zmniejszenie proporcjonalnie rachunku. Opłaty są naliczane tak długo, jak usługa jest skonfigurowana. Jeśli nie korzystasz tymczasowo z usługi, jedynym sposobem na uniknięcie rozliczeń jest usunięcie usługi, a następnie jej ponowne utworzenie w razie potrzeby.

> [!Note]
> Usunięcie usługi spowoduje usunięcie jej wszystkich. Nie ma możliwości w Azure Search tworzenia kopii zapasowych i przywracania utrwalonych danych wyszukiwania. Aby ponownie wdrożyć istniejący indeks nowej usługi, należy uruchomić program użyty do jego pierwotnego utworzenia i załadowania. 

## <a name="terminology-replicas-and-partitions"></a>Terminologia: repliki i partycje
Repliki i partycje są zasobami podstawowymi, które wykonują usługi wyszukiwania.

| Zasób | Definicja |
|----------|------------|
|*Partycje* | Zapewnia magazyn indeksu oraz operacje we/wy dla operacji odczytu i zapisu (na przykład podczas odbudowywania lub odświeżania indeksu).|
|*Replik* | Wystąpienia usługi wyszukiwania, używane głównie do równoważenia obciążenia operacji zapytań. Każda replika zawsze obsługuje jedną kopię indeksu. Jeśli masz 12 replik, będziesz mieć 12 kopii każdego indeksu załadowanego do usługi.|

> [!NOTE]
> Nie ma możliwości bezpośredniego manipulowania indeksami i zarządzania nimi w replice. Jedna kopia każdego indeksu na każdej replice jest częścią architektury usługi.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Jak przydzielić repliki i partycje
W Azure Search usługa jest początkowo przydzielono minimalnym poziomem zasobów obejmujących jedną partycję i jedną replikę. W przypadku warstw, które go obsługują, można stopniowo dostosowywać zasoby obliczeniowe, zwiększając partycje, jeśli potrzebujesz większej ilości miejsca w magazynie i operacji we/wy, lub Dodaj więcej replik dla większych woluminów zapytań lub lepszej wydajności. Pojedyncza usługa musi mieć wystarczającą ilość zasobów do obsługi wszystkich obciążeń (indeksowanie i zapytania). Nie można podzielić obciążeń między wiele usług.

Aby zwiększyć lub zmienić alokację replik i partycji, zalecamy użycie Azure Portal. Portal wymusza limity dla dozwolonych kombinacji, które pozostają poniżej maksymalnych limitów. Jeśli wymagasz metody inicjowania obsługi opartej na skrypcie lub kodzie, [Azure PowerShell](search-manage-powershell.md) lub [interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/services) są rozwiązaniami alternatywnymi.

Ogólnie rzecz biorąc, aplikacje wyszukiwania wymagają większej liczby replik niż partycje, szczególnie w przypadku, gdy operacje usługi są wliczane do obciążeń zapytań. W sekcji o [wysokiej dostępności](#HA) wyjaśniono, dlaczego.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i wybierz usługę wyszukiwania.

2. W obszarze **Ustawienia**Otwórz stronę **skalowanie** , aby zmodyfikować repliki i partycje. 

   Poniższy zrzut ekranu przedstawia usługę standardową, która została zainicjowana z jedną repliką i partycją. Formuła u dołu wskazuje liczbę używanych jednostek wyszukiwania (1). Jeśli cena jednostkowa była $100 (nie cena rzeczywista), miesięczny koszt uruchamiania tej usługi będzie wynosić $100.

   ![Strona skalowania przedstawiająca bieżące wartości](media/search-capacity-planning/1-initial-values.png "Strona skalowania przedstawiająca bieżące wartości")

3. Użyj suwaka, aby zwiększyć lub zmniejszyć liczbę partycji. Formuła u dołu wskazuje liczbę używanych jednostek wyszukiwania.

   Ten przykład podwaja pojemność, z dwiema replikami i partycjami każdy. Zwróć uwagę na liczbę jednostek wyszukiwania; jest teraz cztery, ponieważ formuła rozliczania jest repliką pomnożoną przez partycje (2 x 2). Podwojenie pojemności więcej niż podwaja koszt działania usługi. Jeśli koszt jednostki wyszukiwania to $100, nowe miesięczne rozliczenie będzie teraz $400.

   W przypadku bieżących kosztów na jednostkę dla każdej warstwy odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/search/).

   ![Dodawanie replik i partycji](media/search-capacity-planning/2-add-2-each.png "Dodawanie replik i partycji")

3. Kliknij przycisk **Zapisz** , aby potwierdzić zmiany.

   ![Potwierdź zmiany w zakresie skalowania i rozliczeń](media/search-capacity-planning/3-save-confirm.png "Potwierdź zmiany w zakresie skalowania i rozliczeń")

   Zmiany pojemności potrwają kilka godzin. Nie można anulować po rozpoczęciu procesu i nie ma monitorowania w czasie rzeczywistym dla zmian replik i partycji. Jednak następujący komunikat pozostanie widoczny w czasie trwania zmian.

   ![Komunikat o stanie w portalu](media/search-capacity-planning/4-updating.png "Komunikat o stanie w portalu")


> [!NOTE]
> Po aprowizacji usługi nie można jej uaktualnić do wyższej jednostki SKU. Należy utworzyć usługę wyszukiwania w nowej warstwie i ponownie załadować indeksy. Zobacz [Tworzenie usługi Azure Search w portalu,](search-create-service-portal.md) Aby uzyskać pomoc dotyczącą aprowizacji usług.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinacje partycji i repliki

Usługa podstawowa może mieć dokładnie jedną partycję i maksymalnie trzy repliki, co pozwala na maksymalne ograniczenie trzech usług SUs. Jedyny przystawka zasobów to repliki. Aby zapewnić wysoką dostępność zapytań, potrzebne są co najmniej dwie repliki.

Wszystkie usługi wyszukiwania zoptymalizowane pod kątem standardowej i magazynu mogą przyjąć następujące kombinacje replik i partycji, z zastrzeżeniem limitu 36-SU. 

|   | **1 partycja** | **dwie partycje** | **3 partycje** | **4 partycje** | **6 partycji** | **12 partycji** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliki** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliki** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliki** |4 SU |8 SU |12 SU |16 SU |24 SU |ND |
| **5 replik** |5 SU |10 SU |15 SU |20 SU |30 SU |ND |
| **6 replik** |6 SU |12 SU |18 SU |24 SU |36 SU |ND |
| **12 replik** |12 SU |24 SU |36 SU |ND |ND |ND |

W witrynie sieci Web systemu Azure szczegółowo objaśniono usługi SUs, cennik i pojemność. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Liczba replik i partycji jest dzielona równomiernie na 12 (w odnoszącym się do 1, 2, 3, 4, 6, 12). Jest to spowodowane tym, że Azure Search wstępnie dzieli każdy indeks na 12 fragmentów, tak aby można go było rozłożyć w równe fragmenty we wszystkich partycjach. Na przykład jeśli usługa ma trzy partycje i tworzysz indeks, każda partycja będzie zawierać cztery fragmentów indeksu. Jak Azure Search fragmentów indeksu to szczegóły implementacji, które mogą ulec zmianie w przyszłych wydaniach. Mimo że liczba to 12 dzisiaj, nie należy oczekiwać, że ta liczba będzie zawsze 12 w przyszłości.
>


<a id="HA"></a>

## <a name="high-availability"></a>Wysoka dostępność
Ponieważ jest to łatwe i stosunkowo szybkie skalowanie, zazwyczaj zalecamy rozpoczęcie od jednej partycji i jednej lub dwóch replik, a następnie skalowanie w górę jako kompilacja woluminów zapytań. Obciążenia zapytań są uruchamiane głównie w replikach. Jeśli potrzebujesz większej przepływności lub wysokiej dostępności, prawdopodobnie będziesz potrzebować dodatkowych replik.

Ogólne zalecenia dotyczące wysokiej dostępności są następujące:

* Dwie repliki w celu zapewnienia wysokiej dostępności obciążeń tylko do odczytu (zapytania)

* Trzy lub więcej replik w celu zapewnienia wysokiej dostępności obciążeń odczytu/zapisu (zapytania i indeksowania jako pojedyncze dokumenty są dodawane, aktualizowane lub usuwane)

Umowy dotyczące poziomu usług (SLA) dla Azure Search są przeznaczone dla operacji zapytań i aktualizacji indeksu, które obejmują dodawanie, aktualizowanie lub usuwanie dokumentów.

Warstwa Podstawowa jest przeznaczona dla jednej partycji i trzech replik. Jeśli chcesz, aby elastyczność natychmiast reagować na wahania popytu dla indeksowania i przepływności zapytań, weź pod uwagę jedną z warstw standardowych.  Jeśli okaże się, że wymagania dotyczące magazynu rośnie znacznie szybciej niż przepływność zapytań, należy rozważyć jedną z warstw zoptymalizowanych pod kątem magazynu.

### <a name="index-availability-during-a-rebuild"></a>Dostępność indeksu podczas odbudowywania

Wysoka dostępność dla Azure Search dotyczy zapytań i aktualizacji indeksów, które nie obejmują ponownego kompilowania indeksu. Jeśli usuniesz pole, zmienisz typ danych lub zmienisz nazwę pola, należy ponownie skompilować indeks. Aby ponownie skompilować indeks, należy usunąć indeks, ponownie utworzyć indeks i ponownie załadować dane.

> [!NOTE]
> Nowe pola można dodać do indeksu Azure Search bez ponownego kompilowania indeksu. Wartość nowego pola będzie równa null dla wszystkich dokumentów znajdujących się już w indeksie.

Aby zachować dostępność indeksu podczas odbudowywania, musisz mieć kopię indeksu z inną nazwą w tej samej usłudze lub kopię indeksu o tej samej nazwie w innej usłudze, a następnie dostarczyć przekierowania lub logiki trybu failover w kodzie.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Obecnie nie ma wbudowanego mechanizmu odzyskiwania po awarii. Dodawanie partycji lub replik będzie niewłaściwym strategią dla celów odzyskiwania po awarii. Najbardziej typowym podejściem jest dodanie nadmiarowości na poziomie usługi przez skonfigurowanie drugiej usługi wyszukiwania w innym regionie. Podobnie jak w przypadku dostępności podczas odbudowywania indeksu, przekierowania lub logiki trybu failover muszą pochodzić z kodu.

## <a name="increase-query-performance-with-replicas"></a>Zwiększanie wydajności zapytań za pomocą replik
Opóźnienie zapytania jest wskaźnikiem, że dodatkowe repliki są zbędne. Ogólnie rzecz biorąc, pierwszy krok w kierunku poprawy wydajności zapytania polega na dodaniu większej ilości zasobów. Podczas dodawania replik dodatkowe kopie indeksu są przenoszone do trybu online w celu obsługi większych obciążeń zapytań i równoważenia obciążenia żądań przez wiele replik.

Nie możemy zapewnić mocnych szacunków dla zapytań na sekundę (zapytań): wydajność zapytań zależy od złożoności zapytania i konkurencyjnych obciążeń. Mimo że dodanie replik jasno skutkuje lepszą wydajnością, wynik nie jest ściśle liniowy: dodanie trzech replik nie gwarantuje trzykrotnej przepływności.

Aby uzyskać wskazówki dotyczące szacowania zapytań dla obciążeń, zobacz [Azure Search zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Zwiększanie wydajności indeksowania przy użyciu partycji
Wyszukiwanie aplikacji, które wymagają odświeżenia danych w czasie rzeczywistym, będzie wymagało jeszcze większej liczby partycji niż repliki. Dodawanie partycji powoduje rozłożenie operacji odczytu/zapisu na większą liczbę zasobów obliczeniowych. Zapewnia również więcej miejsca na dysku do przechowywania dodatkowych indeksów i dokumentów.

Więcej indeksów trwa dłużej. W związku z tym może się okazać, że każdy wzrost przyrostowy partycji wymaga mniejszego, ale proporcjonalnego wzrostu w replikach. Złożoność zapytań i woluminu zapytania polega na tym, jak szybko wykonywanie zapytań jest wyłączone.


## <a name="next-steps"></a>Następne kroki

[Wybierz warstwę cenową dla Azure Search](search-sku-tier.md)
