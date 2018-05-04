---
title: Wybierz jednostki SKU lub warstwy cenowej dla usługi Azure Search | Dokumentacja firmy Microsoft
description: 'Usługa wyszukiwanie Azure można udostępnić w tych jednostki SKU: bezpłatne, Basic i Standard, gdzie Standard jest dostępna w różne konfiguracje zasobów i pojemności, poziomów.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 6fe41a8c4d184fef4d1bb0a12fed44a49ef8a6da
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Wybieranie jednostki SKU lub warstwy cenowej usługi Azure Search
W usłudze Azure Search [usługi zostanie zainicjowana](search-create-service-portal.md) w określonych warstwę cenową lub jednostki SKU. Dostępne są następujące opcje **wolne**, **podstawowe**, lub **standardowe**, gdzie **standardowe** są dostępne w wielu konfiguracjach i pojemności.

Celem tego artykułu jest pomocne w wyborze warstwy. Jeśli pojemność warstwy okaże się zbyt niska, należy udostępnić nową usługę w wyższej warstwy, a następnie ponownie załaduj z indeksów. Nie ma żadnych uaktualnienia w miejscu z jednego identyfikatora jednostki Magazynowej tej samej usługi do innego.

> [!NOTE]
> Po wybraniu warstwy i [alokowanie usługi wyszukiwania](search-create-service-portal.md), można zwiększyć repliki, a liczby partycji w ramach usługi. Aby uzyskać instrukcje, zobacz [skalowania zasobu poziomy kwerendy i indeksowania obciążeń](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Jak podejście decyzji warstwy cenowej
W usłudze Azure Search warstwie określa pojemności, nie dostępności funkcji. Ogólnie rzecz biorąc funkcje są dostępne w każdej warstwy, włączając funkcje w wersji zapoznawczej. Jedynym wyjątkiem nie jest obsługiwane dla indeksatorów w S3 HD.

> [!TIP]
> Firma Microsoft zaleca, aby zawsze udostępnieniem **wolne** usługi (po jednym dla każdego subskrypcji bez terminu wygaśnięcia), aby jego łatwo dostępne dla projektów lekki. Użyj **wolne** usługi do testowania i oceny; Utwórz drugi usługą płatną na **podstawowe** lub **standardowe** warstwy do produkcji lub większych obciążeń testu.
>
>

Pojemność i kosztów uruchomiona usługa go ręcznie dostępnych. Informacje przedstawione w tym artykule mogą pomóc zdecydować, który SKU dostarcza odpowiednią równowagę między, ale dla każdego z nich powinna być użyteczna, potrzebujesz co najmniej szacunkowe na następujących:

* Liczba i rozmiar indeksów, które mają być tworzone
* Liczba i rozmiar dokumentów do przekazania
* Niektóre informacje o tym wielkości zapytania pod względem zapytania na drugie (QPS). Aby uzyskać instrukcje, zobacz [wydajności usługi Azure Search i optymalizacji](search-performance-optimization.md).

Liczba i rozmiar są ważne, ponieważ osiągnięto maksymalny limit za pośrednictwem nienaruszalne ograniczenie liczba indeksów dla danej usługi lub zasobów (repliki lub magazynu) używanego przez usługę. Rzeczywiste limitu dla Twojej usługi jest pobiera zależności używany na pierwszym: zasobów lub obiektów.

Szacunkowe w ręcznie poniższe kroki należy uprościć proces:

* **Krok 1** Przejrzyj poniżej, aby dowiedzieć się więcej o dostępnych opcjach opisy jednostki SKU.
* **Krok 2** odpowiedzieć na pytania poniżej na wstępne decyzji.
* **Krok 3** Finalize zdecydować, czy przeglądanie limity stałe w magazynie i cenach.

## <a name="sku-descriptions"></a>Opisy jednostki SKU
Poniższa tabela zawiera opisy każdej warstwy.

| Warstwa | Podstawowe scenariusze |
| --- | --- |
| **W warstwie bezpłatna** |Usługa udostępnionego, bez żadnych opłat, używany do oceny, dochodzenia lub małych obciążeń. Ponieważ są one udostępniane innym użytkownikom, przepływności zapytania i indeksowania w zależności od kogo jeszcze jest za pomocą usługi. Pojemność jest mała (50 MB lub 3 indeksów z się 10 000 dokumentów każdego). |
| **Podstawowa** |Obciążeń produkcyjnych małych na dedykowanym sprzęcie. Wysokiej dostępności. Pojemność zależy od repliki 3 i 1 partycji (2 GB). |
| **S1** |Standardowa 1 obsługuje elastyczne kombinacje partycji (12) i repliki (12), używany w przypadku obciążeń produkcyjnych średnia na dedykowanym sprzęcie. Możesz przydzielić partycji i replik w kombinacji obsługiwane przez maksymalną liczbę jednostek wyszukiwania rozliczeniowy 36. Na tym poziomie partycje są 25 GB. |
| **S2** |Standardowy 2 jest uruchamiane przy użyciu tej samej jednostki 36 wyszukiwania, jak S1, ale większy rozmiar partycji i replik większych obciążeń produkcyjnych. Na tym poziomie partycje są 100 GB. |
| **S3** |Standardowa 3 działa proporcjonalnie większych obciążeń produkcyjnych w systemach wyższej zakończenia w konfiguracjach maksymalnie 12 partycji i replik 12 jednostek wyszukiwania w obszarze 36. Na tym poziomie partycje są 200 GB. |
| **S3 HD** |Standardowa 3 o wysokiej gęstości jest przeznaczona dla dużej liczby mniejsze indeksów. Może mieć maksymalnie 3 partycji, co 200 GB.|

> [!NOTE]
> Maksymalne wartości repliki i partycji są rozliczane się jako jednostek wyszukiwania (36 jednostka maksymalną dla danej usługi), które nakłada skuteczne niższej niż maksymalna oznacza na wartość przedniej. Na przykład, aby użyć maksymalnie 12 replik, może mieć co najwyżej 3 partycji (12 * 3 = 36 jednostek). Podobnie do używania partycji maksymalną zmniejsza replik do 3. Zobacz [skalować poziomy zasobów dla zapytania i obciążeń w usłudze Azure Search indeksowanie](search-capacity-planning.md) wykresu w dozwolonym kombinacji.
>
>

## <a name="review-limits-per-tier"></a>Przejrzyj limity dla warstwy
Poniższy schemat jest podzbiorem limitów z [ograniczenia usługi w usłudze Azure Search](search-limits-quotas-capacity.md). Wyświetla listę czynników, które najprawdopodobniej będą mieć wpływ na decyzję jednostki SKU. Podczas przeglądania poniższe pytania mogą odwoływać się do tego wykresu.

| Zasób | Bezpłatna | Podstawowa | S1 | S2 | S3 | S3 (wysoka gęstość) |
| --- | --- | --- | --- | --- | --- | --- |
| Umowa dotycząca poziomu usług (SLA) |Nie<sup>1</sup> |Yes |Yes |Yes |Yes |Yes |
| Limity indeksu |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Limity dokumentów |10 000 łącznie |1 mln dla usługi |15 milionów dla każdej partycji |60 milionów dla każdej partycji |120 milionów dla każdej partycji |1 mln w indeksie |
| Maksymalna partycji |ND |1 |12 |12 |12 |3<sup>2</sup> |
| Rozmiar partycji |Całkowita liczba 50 MB |2 GB na usługi |25 GB dla jednej partycji |100 GB dla jednej partycji (maksymalnie 1,2 TB na usługę) |200 GB dla jednej partycji (maksymalnie 2.4 TB na usługę) |200 GB (maksymalnie 600 GB dla usługi) |
| Maksymalna repliki |ND |3 |12 |12 |12 |12 |

<sup>1</sup> bezpłatnej warstwy i Podgląd funkcji nie pochodzą z umów dotyczących poziomu usług (SLA). Dla wszystkich warstw rozliczeniowy umowy SLA zaczną obowiązywać podczas obsługi administracyjnej nadmiarowość wystarczające dla Twojej usługi. Co najmniej dwa repliki są wymagane do umowy SLA kwerendy (odczyt). Co najmniej trzech repliki są wymagane dla zapytań i indeksowania SLA (odczytu i zapisu). Liczba partycji nie jest brany pod uwagę umowy dotyczącej poziomu usług. 

<sup>2</sup> S3 i S3 HD obsługiwanych przez infrastrukturę identyczne dużej pojemności, ale każda z nich osiągnie limit maksymalny na różne sposoby. S3 dotyczy mniejszą liczbę bardzo dużych indeksów. W efekcie jej maksymalny limit jest powiązany z zasobów (2.4 TB dla każdej usługi). S3 HD dotyczy duża liczba indeksów bardzo mała. W indeksach 1000 S3 HD osiągnięty limit w formie ograniczenia indeksu. Jeśli klient S3 HD, który wymaga więcej niż 1000 indeksów, skontaktuj się z Microsoft Support informacji na temat kontynuować.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminowanie jednostki magazynowe, które nie spełniają wymagań
Poniższe pytania mogą pomóc w przyjeździe właściwa decyzja jednostki SKU dla obciążenia.

1. Czy masz **Umowa dotycząca poziomu usług (SLA)** wymagania? Można użyć dowolnego rozliczeniowy warstwy (podstawowe w górę), ale konieczne jest skonfigurowanie usługi w celu zapewnienia nadmiarowości. Co najmniej dwa repliki są wymagane do umowy SLA kwerendy (odczyt). Co najmniej trzech repliki są wymagane dla zapytań i indeksowania SLA (odczytu i zapisu). Liczba partycji nie jest brany pod uwagę umowy dotyczącej poziomu usług.
2. **Jak wiele indeksów** czy potrzebne? Jedną z największych zmienne factoring do decyzji jednostka SKU jest liczba indeksów obsługiwanych przez każdy jednostki SKU. Obsługa indeksu jest znacząco różną w dolnej warstwy cenowej. Wymagania dotyczące liczba indeksów można decydujące podstawowy SKU decyzji.
3. **Jak wiele dokumentów** zostaną załadowane do każdego indeksu? Liczby i rozmiaru dokumentów określi rozmiar indeksu. Zakładając, że można oszacować planowanego rozmiar indeksu, można porównać ten numer przed rozmiar partycji na SKU przedłużony liczby partycji wymagana do przechowywania indeks tego rozmiaru.
4. **Co to jest obciążenie zapytaniami oczekiwanego**? Gdy wymagania dotyczące magazynu są zrozumiałe, należy wziąć pod uwagę obciążeń zapytania. S2 i obu SKU S3 oferują niemal równoważne przepływności, ale wymaganiami SLA będzie wykluczyć wszystkie jednostki SKU w wersji zapoznawczej.
5. Planując warstwy S2 lub S3, określić, czy jest wymagany [indeksatory](search-indexer-overview.md). Indeksatory nie są jeszcze dostępne dla warstwy S3 HD. Informacje o innym podejściu jest korzystają z modelu wypychania aktualizacji indeksu, gdzie pisania kodu aplikacji, aby wypychać zestawu danych do indeksu.

Większość klientów może reguły określonej wersji lub na podstawie ich odpowiedzi na te pytania. Jeśli nadal nie masz pewności z wersji produktu, możesz Opublikuj pytania na forach MSDN lub StackOverflow lub się z pomocą techniczną platformy Azure, aby uzyskać dalsze informacje.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Decyzja sprawdzania poprawności: jednostka SKU oferuje wystarczającej ilości miejsca i QPS?
Jako ostatni etap ponownie [cennikiem](https://azure.microsoft.com/pricing/details/search/) i [sekcje-service i na indeks ograniczenia usługi](search-limits-quotas-capacity.md) dokładnie oszacowania przed limity subskrypcji i usług.

Cena lub magazynu wymagania są poza zakresem, warto Refaktoryzuj obciążenia między wieloma usługami mniejsze (na przykład). Na bardziej szczegółowym poziomie można zmodyfikowanie indeksów może być mniejszy lub umożliwia tworzenie kwerend efektywniejsze filtrów.

> [!NOTE]
> Wymagania dotyczące magazynu może być nadmiernie nadmuchany, jeśli dokumenty zawierają nadmiarowe dane. W idealnym przypadku dokumenty zawierają tylko dane z możliwością wyszukiwania lub metadanych. Dane binarne jest niemożliwych i powinny być przechowywane oddzielnie (np. w tabeli lub obiektu blob magazynu Azure) za pomocą pola w indeksie, aby pomieścić URL odwołania do danych zewnętrznych. Maksymalny rozmiar poszczególnych dokumentu jest 16 MB lub mniej Jeśli jesteś zbiorczego przekazywania wielu dokumentów w jedno żądanie. Zobacz [usługi limity w usłudze Azure Search](search-limits-quotas-capacity.md) Aby uzyskać więcej informacji.
>
>

## <a name="next-step"></a>Następny krok
Znając co prawo dopasowanie SKU nadal dalszych kroków:

* [Utwórz usługę wyszukiwania, w portalu](search-create-service-portal.md)
* [Zmienić alokację partycji i replik skalowania usługi](search-capacity-planning.md)
