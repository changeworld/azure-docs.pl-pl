---
title: Wielodostępność i izolacja treści
titleSuffix: Azure Cognitive Search
description: Dowiedz się więcej o typowych wzorcach projektowych dla wielodostępnych aplikacji SaaS podczas korzystania z usługi Azure Cognitive Search.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d8e453336005f3389f67e9571fac438bfc340c1b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549015"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Wzorce projektowe dla wielodostępnych aplikacji SaaS i usługi Azure Cognitive Search
Aplikacja wielodostępna to taka, która zapewnia te same usługi i możliwości dowolnej liczbie dzierżawców, którzy nie mogą wyświetlać ani udostępniać danych innej dzierżawy. W tym dokumencie omówiono strategie izolacji dzierżawy dla aplikacji wielodostępnych utworzonych za pomocą usługi Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Pojęcia usługi Azure Cognitive Search
Jako rozwiązanie do wyszukiwania jako usługi usługa Azure Cognitive Search umożliwia deweloperom dodawanie zaawansowanych środowisk wyszukiwania do aplikacji bez zarządzania infrastrukturą lub stawania się ekspertem w zakresie pobierania informacji. Dane są przekazywane do usługi, a następnie przechowywane w chmurze. Za pomocą prostych żądań do interfejsu API usługi Azure Cognitive Search, dane mogą być następnie modyfikowane i przeszukiwane. Omówienie usługi można znaleźć w [tym artykule](https://aka.ms/whatisazsearch). Przed omówieniem wzorców projektowych, ważne jest, aby zrozumieć niektóre pojęcia w usłudze Azure Cognitive Search.

### <a name="search-services-indexes-fields-and-documents"></a>Usługi wyszukiwania, indeksy, pola i dokumenty
Podczas korzystania z usługi Azure Cognitive Search subskrybuje *usługę wyszukiwania.* Ponieważ dane są przekazywane do usługi Azure Cognitive Search, są przechowywane w *indeksie* w usłudze wyszukiwania. Może istnieć wiele indeksów w ramach jednej usługi. Aby użyć znanych pojęć baz danych, usługa wyszukiwania można porównać do bazy danych, podczas gdy indeksy w usłudze można porównać do tabel w bazie danych.

Każdy indeks w usłudze wyszukiwania ma swój własny schemat, który jest definiowany przez liczbę konfigurowalnych *pól.* Dane są dodawane do indeksu usługi Azure Cognitive Search w postaci poszczególnych *dokumentów.* Każdy dokument musi zostać przekazany do określonego indeksu i musi pasować do schematu tego indeksu. Podczas wyszukiwania danych przy użyciu usługi Azure Cognitive Search kwerendy wyszukiwania pełnotekstowego są wydawane względem określonego indeksu.  Aby porównać te pojęcia z pojęciami bazy danych, pola można porównać do kolumn w tabeli, a dokumenty można porównać do wierszy.

### <a name="scalability"></a>Skalowalność
Każda usługa Azure Cognitive Search w [standardowej warstwie cenowej](https://azure.microsoft.com/pricing/details/search/) może być skalowana w dwóch wymiarach: magazynie i dostępności.

* *Partycje* można dodać, aby zwiększyć magazyn usługi wyszukiwania.
* *Repliki* można dodać do usługi, aby zwiększyć przepływność żądań, które usługa wyszukiwania może obsłużyć.

Dodawanie i usuwanie partycji i replik w pozwoli pojemność usługi wyszukiwania do wzrostu z ilością danych i ruchu wymaga aplikacji. Aby usługa wyszukiwania osiągnęła odczyt [umowy SLA,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)wymaga dwóch replik. Aby usługa osiągnęła [umowy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)odczytu i zapisu, wymaga trzech replik.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Limity usług i indeksów w usłudze Azure Cognitive Search
Istnieje kilka różnych [warstw cenowych](https://azure.microsoft.com/pricing/details/search/) w usłudze Azure Cognitive Search, każda z warstw ma różne [limity i przydziały.](search-limits-quotas-capacity.md) Niektóre z tych limitów są na poziomie usługi, niektóre są na poziomie indeksu, a niektóre są na poziomie partycji.

|  | Podstawowy | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maksymalna liczba replik na usługę |3 |12 |12 |12 |12 |
| Maksymalna liczba partycji na usługę |1 |12 |12 |12 |3 |
| Maksymalna liczba jednostek wyszukiwania (repliki*partycje) na usługę |3 |36 |36 |36 |36 (maksymalnie 3 partycje) |
| Maksymalna ilość miejsca na usługę |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maksymalny magazyn na partycję |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maksymalna liczba indeksów na usługę |5 |50 |200 |200 |3000 (maksymalnie 1000 indeksów/partycja) |

#### <a name="s3-high-density"></a>S3 Wysoka gęstość"
W warstwie cenowej S3 usługi Azure Cognitive Search istnieje opcja dla trybu wysokiej gęstości (HD) zaprojektowanego specjalnie dla scenariuszy wielodostępnych. W wielu przypadkach konieczne jest wsparcie dużej liczby mniejszych najemców w ramach jednej usługi, aby osiągnąć korzyści z prostoty i efektywności kosztowej.

S3 HD pozwala na wiele małych indeksów, które mają być pakowane pod zarządzanie jednej usługi wyszukiwania, handlu możliwość skalowania indeksów w poziomie przy użyciu partycji dla możliwości hostowania więcej indeksów w jednej usłudze.

Usługa S3 jest przeznaczona do obsługi stałej liczby indeksów (maksymalnie 200) i zezwalania na skalowanie każdego indeksu w poziomie w miarę dodawania nowych partycji do usługi. Dodanie partycji do usług S3 HD zwiększa maksymalną liczbę indeksów, które usługa może obsługiwać. Idealny maksymalny rozmiar dla pojedynczego indeksu S3HD wynosi około 50 - 80 GB, chociaż nie ma twardego limitu rozmiaru dla każdego indeksu nałożonego przez system.

## <a name="considerations-for-multitenant-applications"></a>Zagadnienia dotyczące aplikacji wielodostępnych
Aplikacje wielodostępne muszą skutecznie dystrybuować zasoby między dzierżawcami, zachowując pewien poziom prywatności między różnymi dzierżawcami. Istnieje kilka zagadnień podczas projektowania architektury dla takiej aplikacji:

* *Izolacja dzierżawy:* Deweloperzy aplikacji należy podjąć odpowiednie środki, aby upewnić się, że żaden dzierżawca nieautoryzowany lub niepożądany dostęp do danych innych dzierżawców. Poza perspektywą prywatności danych strategie izolacji dzierżawy wymagają skutecznego zarządzania zasobami współdzielonymi i ochrony przed hałaśliwymi sąsiadami.
* *Koszt zasobów w chmurze:* Podobnie jak w przypadku innych aplikacji, rozwiązania programowe muszą pozostać konkurencyjne pod względem kosztów jako składnik aplikacji wielodostępnej.
* *Łatwość obsługi:* Podczas tworzenia architektury wielodostępnej, wpływ na działania aplikacji i złożoność jest ważnym czynnikiem. Usługa Azure Cognitive Search ma [99,9% umowy SLA.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
* *Globalny zasięg:* Aplikacje wielodostępne mogą być konieczne do skutecznego obsługi dzierżaw, które są dystrybuowane na całym świecie.
* *Skalowalność:* Deweloperzy aplikacji należy wziąć pod uwagę, jak one pogodzić między utrzymaniem wystarczająco niski poziom złożoności aplikacji i projektowania aplikacji do skalowania z liczbą dzierżawców i wielkości danych dzierżawców i obciążenia.

Usługa Azure Cognitive Search oferuje kilka granic, które mogą służyć do izolowania danych i obciążenia dzierżawców.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modelowanie wielodostępności za pomocą usługi Azure Cognitive Search
W przypadku scenariusza wielodostępnego deweloper aplikacji zużywa jedną lub więcej usług wyszukiwania i dzieli ich dzierżawy między usługi, indeksy lub oba te usługi. Usługa Azure Cognitive Search ma kilka typowych wzorców podczas modelowania scenariusza wielodostępnego:

1. *Indeks na dzierżawcę:* Każdy dzierżawca ma swój własny indeks w ramach usługi wyszukiwania, który jest współużytkowane z innymi dzierżawami.
2. *Usługa dla dzierżawcy:* Każdy dzierżawca ma własną dedykowaną usługę Azure Cognitive Search, oferującą najwyższy poziom danych i separacji obciążeń.
3. *Mieszanka obu:* Większe, bardziej aktywne dzierżawy są przypisywane dedykowane usługi, podczas gdy mniejsze dzierżawy są przypisane poszczególnych indeksów w ramach usług udostępnionych.

## <a name="1-index-per-tenant"></a>1. Indeks na najemcę
![Przedstawianie modelu indeksu na dzierżawcę](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

W modelu indeksu na dzierżawę wielu dzierżaw zajmują jedną usługę Azure Cognitive Search, w której każda dzierżawa ma własny indeks.

Dzierżawcy osiągnąć izolację danych, ponieważ wszystkie żądania wyszukiwania i operacje dokumentu są wydawane na poziomie indeksu w usłudze Azure Cognitive Search. W warstwie aplikacji istnieje świadomość potrzeby kierowania ruchu różnych dzierżaw do odpowiednich indeksów, a także zarządzania zasobami na poziomie usług we wszystkich dzierżawców.

Kluczowym atrybutem modelu indeksu na dzierżawcę jest możliwość dla dewelopera aplikacji do oversubscribe pojemności usługi wyszukiwania wśród dzierżaw aplikacji. Jeśli dzierżawcy mają nierównomierny rozkład obciążenia, optymalna kombinacja dzierżaw mogą być dystrybuowane między indeksami usługi wyszukiwania, aby pomieścić szereg bardzo aktywnych, zasobochłonnych dzierżaw jednocześnie obsługujących długi ogon mniej aktywnych dzierżaw. Kompromis jest niezdolność modelu do obsługi sytuacji, w których każdy dzierżawy jest jednocześnie bardzo aktywny.

Model indeksu na dzierżawę stanowi podstawę dla modelu kosztu zmiennego, w którym cała usługa Azure Cognitive Search jest kupowana z góry, a następnie wypełniona dzierżawcami. Pozwala to na nieużywane zdolności produkcyjne przeznaczone dla wersji próbnych i bezpłatnych kont.

W przypadku aplikacji o globalnym śladzie model indeksu na dzierżawcę może nie być najbardziej wydajny. Jeśli dzierżawy aplikacji są dystrybuowane na całym świecie, dla każdego regionu może być konieczna oddzielna usługa, która może powielać koszty w każdym z nich.

Usługa Azure Cognitive Search umożliwia skalowanie zarówno poszczególnych indeksów, jak i całkowitej liczby indeksów do zwiększenia. Jeśli zostanie wybrana odpowiednia warstwa cenowa, partycje i repliki można dodać do całej usługi wyszukiwania, gdy pojedynczy indeks w usłudze rośnie zbyt duży pod względem magazynu lub ruchu.

Jeśli całkowita liczba indeksów rośnie zbyt duża dla jednej usługi, inna usługa musi być aprowizowana, aby pomieścić nowych dzierżawców. Jeśli indeksy muszą być przenoszone między usługami wyszukiwania w miarę dodawania nowych usług, dane z indeksu muszą być ręcznie kopiowane z jednego indeksu do drugiego, ponieważ usługa Azure Cognitive Search nie zezwala na przenoszenie indeksu.

## <a name="2-service-per-tenant"></a>2. Usługa na najemcę
![Obraz modelu usługi na dzierżawcę](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

W architekturze usługi na dzierżawę każdy dzierżawca ma własną usługę wyszukiwania.

W tym modelu aplikacja osiąga maksymalny poziom izolacji dla swoich dzierżawców. Każda usługa ma dedykowaną pamięć masową i przepływność do obsługi żądania wyszukiwania, a także oddzielne klucze interfejsu API.

W przypadku aplikacji, w których każdy dzierżawca ma duży ślad lub obciążenie ma niewielką zmienność od dzierżawy do dzierżawy, model usługi na dzierżawę jest skutecznym wyborem, ponieważ zasoby nie są współużytkowane przez różne obciążenia dzierżawy.

Usługa na model dzierżawy oferuje również korzyści z przewidywalnego modelu kosztów stałych. Nie ma inwestycji z góry w całej usługi wyszukiwania, dopóki nie ma dzierżawy, aby ją wypełnić, jednak koszt dzierżawcy jest wyższy niż indeks na model dzierżawy.

Model usługi na dzierżawcę jest wydajnym wyborem dla aplikacji o globalnym zasięgu. Z geograficznie rozproszonych dzierżaw, jest łatwo mieć usługi każdego dzierżawcy w odpowiednim regionie.

Wyzwania związane ze skalowaniem tego wzorca pojawiają się, gdy poszczególni dzierżawcy przerastają swoją usługę. Usługa Azure Cognitive Search nie obsługuje obecnie uaktualniania warstwy cenowej usługi wyszukiwania, więc wszystkie dane musiałyby zostać ręcznie skopiowane do nowej usługi.

## <a name="3-mixing-both-models"></a>3. Mieszanie obu modeli
Innym wzorcem modelowania wielodostępności jest mieszanie strategii indeksu na dzierżawę i usługi na dzierżawę.

Mieszając dwa wzorce, największych dzierżaw aplikacji może zajmować dedykowane usługi, podczas gdy długi ogon mniej aktywnych, mniejszych dzierżawców może zajmować indeksy w usłudze udostępnionej. Ten model zapewnia, że najwięksi dzierżawcy mają stale wysoką wydajność z usługi, pomagając jednocześnie chronić mniejszych dzierżawców przed hałaśliwymi sąsiadami.

Jednak implementowanie tej strategii opiera się prognozowania w przewidywaniu, które dzierżawy będą wymagać dedykowanej usługi w porównaniu do indeksu w usłudze udostępnionej. Złożoność aplikacji zwiększa się wraz z koniecznością zarządzania obu tych modeli wielodostępności.

## <a name="achieving-even-finer-granularity"></a>Osiągnięcie jeszcze większej szczegółowości
Powyższe wzorce projektowe do modelowania scenariuszy wielodostępnych w usłudze Azure Cognitive Search zakładają jednolity zakres, w którym każda dzierżawa jest całym wystąpieniem aplikacji. Jednak aplikacje mogą czasami obsługiwać wiele mniejszych zakresów.

Jeśli modele usługi na dzierżawę i indeks na dzierżawę nie są wystarczająco małe zakresy, jest możliwe do modelowania indeksu, aby osiągnąć jeszcze bardziej szczegółowy stopień szczegółowości.

Aby pojedynczy indeks zachowywał się inaczej dla różnych punktów końcowych klienta, pole można dodać do indeksu, który wyznacza pewną wartość dla każdego możliwego klienta. Za każdym razem, gdy klient wywołuje usługę Azure Cognitive Search w celu kwerendy lub zmodyfikowania indeksu, kod z aplikacji klienckiej określa odpowiednią wartość dla tego pola przy użyciu funkcji [filtrowania](https://msdn.microsoft.com/library/azure/dn798921.aspx) usługi Azure Cognitive Search w czasie kwerendy.

Ta metoda może służyć do osiągnięcia funkcji oddzielnych kont użytkowników, oddzielnych poziomów uprawnień, a nawet całkowicie oddzielnych aplikacji.

> [!NOTE]
> Za pomocą podejścia opisanego powyżej, aby skonfigurować pojedynczy indeks do obsługi wielu dzierżawy wpływa na trafność wyników wyszukiwania. Wyniki trafności wyszukiwania są obliczane w zakresie na poziomie indeksu, a nie w zakresie na poziomie dzierżawy, więc wszystkie dane dzierżawców są uwzględniane w podstawowych statystykach wyników trafności, takich jak częstotliwość terminów.
> 
> 

## <a name="next-steps"></a>Następne kroki
Usługa Azure Cognitive Search to atrakcyjny wybór dla wielu aplikacji. Oceniając różne wzorce projektowe dla aplikacji wielodostępnych, należy wziąć pod uwagę [różne warstwy cenowe](https://azure.microsoft.com/pricing/details/search/) i odpowiednie [limity usług,](search-limits-quotas-capacity.md) aby najlepiej dostosować usługę Azure Cognitive Search do obciążeń aplikacji i architektur o różnych rozmiarach.

Wszelkie pytania dotyczące usługi Azure Cognitive Search i azuresearch_contact@microsoft.comscenariuszy wielodostępnych można kierować do .

