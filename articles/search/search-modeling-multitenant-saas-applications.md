---
title: Modelowanie wielodostępności na potrzeby izolacji zawartości w jednej usłudze — usługa Azure Search
description: Dowiedz się więcej o często używane wzorce projektowe dla wielodostępnych aplikacji SaaS podczas korzystania z usługi Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 58d7ca65a14f9f774b19796c9beae2a7c84102ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288725"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Wzorce projektowe dla wielodostępnych aplikacji SaaS i usługa Azure Search
Wielodostępnej aplikacji to taki, który zawiera te same usług i funkcji do dowolnej liczby dzierżawców, którzy nie może zobaczyć i udostępnianie danych z innymi dzierżawami. W tym dokumencie omówiono strategii izolacji dzierżawy dla aplikacji wielodostępnych utworzonych za pomocą usługi Azure Search.

## <a name="azure-search-concepts"></a>Pojęcia dotyczące usługi Azure Search
Jako rozwiązanie typu wyszukiwanie jako usługa Azure Search umożliwia deweloperom Dodawanie środowiska sformatowanego wyszukiwania do aplikacji bez konieczności zarządzania dowolną infrastrukturą lub eksperckiej wiedzy o pobierania informacji. Dane są przekazywane do usługi i następnie przechowywane w chmurze. Za pomocą prostych żądań do interfejsu API usługi Azure Search, dane można być zmodyfikowane i przeszukiwane. Omówienie usługi można znaleźć w [w tym artykule](https://aka.ms/whatisazsearch). Przed omówieniem wzorce projektowe, ważne jest zrozumienie niektórych pojęć w usłudze Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Usługi wyszukiwania, indeksów, pola i dokumenty
Korzystając z usługi Azure Search, jeden subskrybuje *usługi wyszukiwania*. Ponieważ dane są przekazywane do usługi Azure Search, jest on przechowywany w *indeksu* w ramach usługi wyszukiwania. Może to być liczba indeksów w jednej usłudze. Aby korzystać z dobrze znanych koncepcji baz danych, usługi wyszukiwania można przyrównać do bazy danych, gdy indeksów w ramach usługi można przyrównać do tabel w bazie danych.

Każdy indeks w usłudze wyszukiwania ma swój własny schemat, który jest definiowany przez szereg dostosowywalnych *pola*. Dane zostaną dodane do indeksu usługi Azure Search w postaci indywidualnego *dokumenty*. Każdy dokument muszą być przesłane do określonego indeksu i należy dopasować schemat tego indeksu. Podczas wyszukiwania danych przy użyciu usługi Azure Search, zapytania wyszukiwania pełnotekstowego są względem określonego indeksu.  Aby porównać te pojęcia do tych bazy danych, pola, które można przyrównać do kolumn w tabeli i dokumenty można przyrównać do wierszy.

### <a name="scalability"></a>Skalowalność
Wszystkie usługi Azure Search w standardzie [warstwy cenowej](https://azure.microsoft.com/pricing/details/search/) można skalować w dwóch wymiarach: magazynu i dostępności.

* *Partycje* mogą być dodawane do zwiększenia magazynu usługi wyszukiwania.
* *Repliki* mogą być dodawane do usługi w celu zwiększenia przepływności żądań, które może obsłużyć usługi wyszukiwania.

Dodawanie i usuwanie partycji i replik w umożliwi pojemność usługi wyszukiwania w miarę wzrostu ilości danych i ruchu wymagań aplikacji. Aby usługa wyszukiwania w celu osiągnięcia odczyt [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), wymaga dwóch replik. W kolejności dla usługi w celu osiągnięcia odczytu i zapisu [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), wymaga trzech replik.

### <a name="service-and-index-limits-in-azure-search"></a>Limity usług i indeksu w usłudze Azure Search
Istnieje kilka różnych [warstw cenowych](https://azure.microsoft.com/pricing/details/search/) w usłudze Azure Search w każdej z warstw są różne w różnych [limity przydziału i ograniczenia](search-limits-quotas-capacity.md). Niektóre z tych limitów są na poziomie usługi, niektóre są na poziomie indeksu, a niektóre są na poziomie partycji.

|  | Podstawowa | Standard1 | Standardowa 2 | Standardowa 3 | Wysoka gęstość standardowa 3 |
| --- | --- | --- | --- | --- | --- |
| Maksymalna replik na usługę |3 |12 |12 |12 |12 |
| Maksymalna partycje na usługę |1 |12 |12 |12 |3 |
| Jednostki wyszukiwania maksymalną (repliki * partycje) na usługę |3 |36 |36 |36 |36 (maks. 3 partycji) |
| Maksymalna pojemność jednej usługi |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maksymalna pojemność jednej partycji |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maksymalna liczba indeksów na usługę |5 |50 |200 |200 |3000 (max 1000 indeksów na partycję) |

#### <a name="s3-high-density"></a>S3 High Density "
W warstwie cenowej S3 usługi Azure Search istnieje opcja trybu wysoka gęstość (HD), przeznaczone specjalnie dla scenariuszy z wieloma dzierżawcami. W wielu przypadkach jest niezbędne do obsługi dużej liczby mniejsze dzierżaw w ramach jednej usługi do osiągnięcia korzyści prostoty i niskiej cenie.

Wysoka gęstość S3 zezwala na wiele małych indeksy będzie bogatymi w obszarze Zarządzanie usługi wyszukiwania pojedynczej przez handlowymi możliwość skalowania w poziomie za pomocą partycji dla możliwości hostowania więcej indeksów w jednej usłudze indeksów.

Konkretnie usługi S3 może mieć od 1 do 200 indeksy, które ze sobą, może udostępniać maksymalnie 1,4 mld dokumentów. Wysoka gęstość S3 z drugiej strony pozwoliłoby pojedynczych indeksów tylko przejść do 1 miliona dokumentów, ale może obsługiwać maksymalnie 1000 indeksów na partycję (na maksymalnie 3000 na usługę) z łączną liczbę dokumentów o 200 mln na partycję (do 600 mln na usługę).

## <a name="considerations-for-multitenant-applications"></a>Zagadnienia dotyczące aplikacji wielodostępnych
Wielodostępne aplikacje muszą efektywnie dystrybuuje zasobów między dzierżawcami przy jednoczesnym zachowaniu pewien poziom prywatności między różnych dzierżawach. Podczas projektowania architektury takiej aplikacji jest kilka istotnych kwestii:

* *Izolacja dzierżawy:* Deweloperzy aplikacji muszą podjąć właściwe środki, aby upewnić się, że nie dzierżaw nieautoryzowany lub niechcianego dostępu do danych z innych dzierżaw. Poza perspektywy prywatność danych strategii izolacji dzierżawcy wymagają efektywne zarządzanie zasobów udostępnionych i ochrony z najbliższego otoczenia generujące dużo alertów.
* *Koszt zasobów chmury:* Podobnie jak w przypadku innych aplikacji, rozwiązania oparte na oprogramowaniu musi pozostać koszt konkurencyjne jako część aplikacji wielodostępnej.
* *Łatwość operacji.* Podczas tworzenia architektury wielodostępnej, ważną kwestią jest negatywny wpływ na operacje i złożoność aplikacji. Usługi Azure Search [poziomem usług 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globalnemu zasięgowi:* Wielodostępne aplikacje może być konieczne skutecznie obsługiwać dzierżaw, które są rozmieszczone na całym świecie.
* *Skalowalność:* Deweloperzy aplikacji należy wziąć pod uwagę, jak uzgodnienia między utrzymywanie wystarczająco niski poziom złożoności aplikacji i projektowania aplikacji do skalowania liczby dzierżawców i rozmiaru danych i obciążenie dzierżawców.

Usługa Azure Search udostępnia kilka granic, które mogą być używane do izolowania danych i obciążenie dzierżawców.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelowanie wielodostępności z usługą Azure Search
W przypadku scenariusza wielodostępne Deweloper aplikacji wykorzystuje co najmniej jednej usługi wyszukiwania i dzielenia dzierżawcom między usługami i/lub indeksy. Usługa Azure Search ma kilka typowych wzorców podczas modelowania wielodostępnego scenariusza:

1. *Indeks każdego dzierżawcy:* Każda dzierżawa ma swój własny indeksu w usłudze wyszukiwania, który jest współużytkowany z innymi dzierżawami.
2. *Usługi za dzierżawy:* Każda dzierżawa ma swój własny dedykowany usługę Azure Search oferuje najwyższy poziom separacji danych i obciążeń.
3. *Mieszany obu tych elementów:* Większych i bardziej aktywne dzierżawy są przypisywane dedykowanych usługi, gdy mniejszych dzierżawców są przypisywane pojedynczych indeksów w ramach usług udostępnionych.

## <a name="1-index-per-tenant"></a>1. Indeks każdego dzierżawcy
![Wizerunku modelu indeksu dla dzierżawcy](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

W modelu indeksu dla dzierżawcy wielu dzierżaw zajmują jednej usługi Azure Search, gdzie każda dzierżawa ma indeks własne.

Dzierżawcy osiągnąć izolacji danych, ponieważ wszystkie wyszukiwania żądań i operacje na dokumentach są wydawane na poziomie indeksu w usłudze Azure Search. W warstwie aplikacji jest rozpoznawanie potrzebę do kierowania ruchem dzierżawców różnych do odpowiednich indeksów zarządzaniu zasobów na poziomie usług dla wszystkich dzierżaw.

Atrybut klucza w modelu indeksu dla dzierżawcy jest możliwość dla deweloperów aplikacji jest nadsubskrybowanie pojemność usługi wyszukiwania między dzierżawami aplikacji. Jeśli dzierżawcy nierówny rozkład obciążenia, optymalną kombinację dzierżaw mogą być rozproszone między indeksy usługę wyszukiwania, aby uwzględnić wiele dzierżaw bardzo aktywne, dużej ilości zasobów podczas jednoczesnego obsługująca długi ciąg mniej aktywne dzierżawy. Jest to kompromis niemożność modelu obsługi sytuacji, gdzie każda dzierżawa jednocześnie jest bardzo aktywne.

Wzór indeksu dla dzierżawcy stanowi podstawę dla modelu kosztów zmiennej, gdzie są kupowane ponoszonych z góry całej usługi Azure Search i następnie wypełnione z dzierżawcami. Pozwala to na nieużywaną pojemność wyznaczonego prób i bezpłatnych kont.

W przypadku aplikacji o zasięgu globalnym modelem indeksu dla dzierżawcy nie może być najbardziej efektywny. Jeśli aplikacja dzierżaw są dystrybuowane na całym świecie, oddzielnej usługi może być konieczne dla każdego regionu, który może być zduplikowana kosztów na każdym z nich.

Usługa Azure Search umożliwia skalowanie zarówno pojedynczych indeksów, jak i łączna liczba indeksów rośnie. Jeśli odpowiednie ceny warstwy jest wybierany partycje i repliki można dodać do usługi wyszukiwania całego po poszczególnych indeksu w usłudze zbytnio się rozrośnie pod względem magazynu lub ruchu.

Jeśli łączna liczba indeksów zbytnio się rozrośnie dla jednej usługi, innej usługi ma być przygotowana do uwzględnienia nowych dzierżaw. Indeksy muszą być przenoszone między usługi wyszukiwania, gdy są dodawane nowe usługi, dane z indeksu będzie musiał ręcznie skopiowane z jednego indeksu do drugiego zgodnie z usługi Azure Search nie zezwala na indeks ma zostać przeniesiona.

## <a name="2-service-per-tenant"></a>2. Usługi w dzierżawie
![Wizerunku modelu usług dla dzierżawcy](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

W architekturze usługi dla dzierżawcy Każda dzierżawa ma własną usługę wyszukiwania.

W tym modelu aplikacja osiąga maksymalny poziom izolacji dla swoich dzierżaw. Każda usługa ma dedykowany magazyn i przepływność do obsługi żądania wyszukiwania, a także oddzielne klucze interfejsu API.

Dla aplikacji, gdzie każda dzierżawa ma duże zużycie lub Obciążenie ma nieco zmienność dzierżawy dzierżawy model usług dla dzierżawcy jest rzeczywistą możliwość wyboru zasobów nie są współdzielone przez różnych dzierżaw obciążeń.

Usługi za modelu dzierżawy oferuje również korzyści wynikające z modelu kosztów przewidywalne, stały. Brak nie początkowych inwestycji w usługi search całego do momentu dzierżawy, aby wypełnić go, jednak koszt na dla dzierżawcy jest wyższa niż model indeksu dla dzierżawcy.

Model usługi dla dzierżawcy jest efektywne wyborem dla aplikacji o zasięgu globalnym. Za pomocą rozproszonej geograficznie dzierżaw jest proste mieć odpowiedni region każdej dzierżawy usługi.

Trudności w ten wzorzec skalowania wystąpić, gdy poszczególne dzierżawy wykraczają poza możliwości usługi. Usługa Azure Search nie obsługuje obecnie uaktualnianie warstwę cenową usługi wyszukiwania, dzięki czemu wszystkie dane będzie trzeba ręcznie skopiowane do nowej usługi.

## <a name="3-mixing-both-models"></a>3. Mieszanie oba modele
Inny wzorzec do modelowania wielodostępność jest mieszanie strategie indeksu dla dzierżawcy i dzierżawy dla usługi.

Przez mieszanie dwa wzorce, dzierżaw największych aplikacji mogą zajmować dedykowanych usług podczas długi ciąg mniej aktywne, mniejsze dzierżawcy mogą zajmować indeksów w udostępnionej usłudze. Ten model zapewnia największą dzierżaw spójnej wysokiej wydajności z usługi podczas ochrona mniejszych dzierżaw z dowolnym hałas sąsiadów.

Jednak ta strategia wdrażania opiera się prognozowania w przewidywaniu dzierżawy, który będzie wymagać dedykowana usługa, a indeks w udostępnionej usłudze. Zwiększa złożoność aplikacji z konieczności zarządzania oba te modele wielodostępności.

## <a name="achieving-even-finer-granularity"></a>Obsługiwanie jeszcze bardziej szczegółowy
Powyższe wzorców projektowych do modelu w scenariuszach wielodostępnych w usłudze Azure Search założono jednolitego zakresu, gdzie każda dzierżawa jest całego wystąpienia aplikacji. Jednak aplikacje czasami może obsługiwać wiele mniejszych zakresach.

Jeśli modeli usługi dla dzierżawcy i indeksu dla dzierżawcy nie są wystarczająco mała zakresów, istnieje możliwość modelu indeks, aby uzyskać jeszcze bardziej precyzyjną stopień szczegółowości.

Aby jeden indeks zachowywać się inaczej dla punktów końcowych z innego klienta, można dodać pola do indeksu, który wyznacza wartość dla każdego klienta, to możliwe. Każdorazowo, klient wywołuje usługi Azure Search zapytania lub zmodyfikować indeksu, kod z aplikacji klienckiej określa wartość odpowiednią dla tego pola przy użyciu usługi Azure Search [filtru](https://msdn.microsoft.com/library/azure/dn798921.aspx) możliwości w czasie wykonywania zapytania.

Ta metoda może służyć do osiągnięcia funkcji oddzielnych kont użytkowników, poziomów uprawnień oddzielne i jeszcze całkowicie niezależne aplikacje.

> [!NOTE]
> Przy użyciu podejścia opisanego powyżej można skonfigurować jeden indeks do obsługi wielu dzierżaw ma wpływ na istotności wyników wyszukiwania. Wyszukiwanie wg istotności wyników są obliczane w zakresie poziomu indeksu, nie zakresu poziomie dzierżawy, dzięki czemu dane wszystkich dzierżaw są włączane do oceny istotności podstawowe statystyki, takie jak częstotliwość termin.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Usługa Azure Search to świetna wybór w przypadku wielu aplikacji [Przeczytaj więcej na temat zaawansowanych funkcji usługi](https://aka.ms/whatisazsearch). Podczas obliczania różnych wzorców projektowych dla aplikacji wielodostępnych, należy wziąć pod uwagę [różnych warstw cenowych](https://azure.microsoft.com/pricing/details/search/) oraz odpowiednie [limitów usług](search-limits-quotas-capacity.md) aby najlepiej dopasować usługi Azure Search, aby dopasować aplikacji obciążenia i architektur różnej wielkości.

Pytania dotyczące usługi Azure Search i scenariuszach wielodostępnych może zostać skierowany do azuresearch_contact@microsoft.com.

