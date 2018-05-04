---
title: Modelowanie Wielodostępności w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Poznaj typowe wzorce projektowe dla wielodostępnych aplikacji SaaS przy użyciu usługi Azure Search.
manager: jlembicz
author: ashmaka
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: ashmaka
ms.openlocfilehash: 765f9c4600f762efdd7d57681529751e99c13894
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Projektowanie wzorce dla wielodostępnych aplikacji SaaS i usługi Azure Search
Wielodostępna aplikacji to taki, który zawiera te same usług i funkcji do dowolnej liczby dzierżawców, którzy nie są wyświetlane ani udostępniać dane z innymi dzierżawami. W tym dokumencie omówiono strategii izolacji dzierżawy dla wielodostępnych aplikacji skompilowanej za pomocą usługi Azure Search.

## <a name="azure-search-concepts"></a>Pojęcia wyszukiwanie Azure
Jako rozwiązanie wyszukiwanie jako usługa Azure Search umożliwia deweloperom Dodawanie środowiska wyszukiwania sformatowanego do aplikacji bez dowolnej infrastruktury zarządzania i staje się eksperta w pobierania informacji. Dane są przekazywane do usługi, a następnie przechowywane w chmurze. Za pomocą prostego żądania interfejsu API usługi Azure Search, dane można następnie można modyfikować i przeszukiwane. Omówienie usługi można znaleźć w [w tym artykule](http://aka.ms/whatisazsearch). Przed omówieniem wzorce projektowe, ważne jest zrozumienie niektóre pojęcia dotyczące usługi Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Wyszukiwanie w usługach, indeksów, pól i dokumentów
Podczas korzystania z usługi Azure Search, jeden subskrybuje *usługi wyszukiwania*. Ponieważ dane są przesyłane do usługi Azure Search, jest ona przechowywana w *indeksu* w ramach usługi wyszukiwania. Może to być liczba indeksów w ramach jednej usługi. Aby użyć znane pojęcia związane z baz danych, usługę wyszukiwania można przyrównać do bazy danych podczas indeksy w ramach usługi można przyrównać do tabel w bazie danych.

Każdy indeks w ramach usługi wyszukiwania ma własny schemat, który jest zdefiniowany przez liczbę można dostosowywać *pola*. Dodanie danych do indeksu usługi Azure Search w formularzu Osoba *dokumenty*. Każdy dokument musi zostać przekazana do konkretnego indeksu i musi znajdować się w tym Indeksuj schemat. Podczas wyszukiwania danych przy użyciu usługi Azure Search, zapytania wyszukiwania pełnotekstowego są wystawiony na podstawie określonego indeksu.  Aby porównać te pojęcia do tych bazy danych, pola można przyrównać do kolumn w tabeli i dokumentów można przyrównać do wierszy.

### <a name="scalability"></a>Skalowalność
Wszystkie usługi Azure Search w standardzie [warstwy cenowej](https://azure.microsoft.com/pricing/details/search/) można skalować w dwóch wymiarów: magazynu i dostępności.

* *Partycje* mogą być dodawane do zwiększenia magazynu usługi wyszukiwania.
* *Repliki* mogą być dodawane do usługi w celu zwiększenia przepływności żądań, jaką może obsłużyć usługi wyszukiwania.

Dodawanie i usuwanie partycji i replik o umożliwi pojemność Usługa wyszukiwania w miarę wzrostu ilości danych i ruchu na potrzeby aplikacji. Aby usługi wyszukiwania osiągnąć odczytu [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), wymaga dwóch replik. Aby usługa do osiągnięcia odczytu i zapisu [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), wymaga trzech replik.

### <a name="service-and-index-limits-in-azure-search"></a>Limity usług i indeksu w usłudze Azure Search
Istnieje kilka różnych [warstw cenowych](https://azure.microsoft.com/pricing/details/search/) w usłudze Azure Search, każdy z warstwy ma inną [limity i przydziały](search-limits-quotas-capacity.md). Niektóre z tych limitów są na poziomie usługi, niektóre są na poziomie indeksu, a niektóre są na poziomie partycji.

|  | Podstawowa | Standard1 | Standardowa 2 | Standardowa 3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maksymalna replik dla usługi |3 |12 |12 |12 |12 |
| Maksymalna partycji dla usługi |1 |12 |12 |12 |3 |
| Jednostek wyszukiwania maksymalną (replik * partycje) dla usługi |3 |36 |36 |36 |36 (max partycji 3) |
| Maksymalna dokumentów dla usługi |1 mln |180 milionów |720 milionów |1.4 mld |600 mln |
| Maksymalna przestrzeń magazynowa dla usługi |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maksymalna dokumentów na partycję |1 mln |15 milionów |60 milionów |120 milionów |200 milionów |
| Maksymalna przestrzeń magazynowa dla każdej partycji |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maksymalna indeksów dla usługi |5 |50 |200 |200 |3000 (max 1000 indeksów/partycji) |

#### <a name="s3-high-density"></a>S3 o wysokiej gęstości "
W usłudze Azure Search S3 warstwę cenową istnieje opcja zaprojektowane specjalnie dla scenariuszy wielodostępnym trybu wysokiej gęstości (HD). W wielu przypadkach jest niezbędne do obsługi dużej liczby mniejsze dzierżawców w ramach jednej usługi do osiągnięcia korzyści prostotę i koszt wydajności.

S3 HD zezwala na wiele indeksów małych pakowane w obszarze Zarządzanie usługą wyszukiwania pojedynczego przez handlowymi możliwość skalowania w poziomie indeksów przy użyciu partycje zdolność do obsługi więcej indeksów w jednej usługi.

Konkretnie usługi S3 może mieć od 1 do 200 indeksów, które razem może udostępniać dokumenty do MLD 1.4. HD S3 z drugiej strony umożliwiałyby poszczególnych indeksy tylko przejdź do 1 milionów dokumentów, ale może obsługiwać maksymalnie 1000 indeksów dla partycji (maksymalnie 3000 na usługę) wraz z liczbą całkowitą dokumentu 200 milionów przypadających na partycję (do 600 milionów usługi).

## <a name="considerations-for-multitenant-applications"></a>Zagadnienia dotyczące aplikacji wielodostępnego
Aplikacje wielodostępne musi efektywnie dystrybuuje zasobów między dzierżawcami przy zachowaniu pewnego poziomu prywatności od różnych dzierżawców. Istnieje kilka zagadnień podczas projektowania architektury dla takich aplikacji:

* *Odizolowania dzierżawców:* deweloperzy aplikacji należy podjąć odpowiednie środki w celu zapewnienia, że dzierżawcy nie mają nieautoryzowane lub niechcianego dostępu do danych innych dzierżawców. Poza perspektywy prywatność danych strategii izolacji dzierżawcy wymagają efektywne zarządzanie udostępnionymi zasobami oraz ochrony przed zakłócenia sąsiadów.
* *Koszt zasobów w chmurze:* zgodnie z innej aplikacji, rozwiązania w zakresie oprogramowania muszą pozostać koszt konkurencyjnych jako składnik wielodostępnych aplikacji.
* *Łatwość operacji:* podczas opracowywania architektura wielodostępnej, ważną kwestią jest wpływ na operacje aplikacji i złożoności. Usługa wyszukiwanie Azure ma [SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globalne zużycie:* wielodostępnych aplikacji może być konieczne efektywnie obsługiwać dzierżawcami, które są rozmieszczone na całym świecie.
* *Skalowalność:* deweloperzy aplikacji należy wziąć pod uwagę, jak uzgodnienia między utrzymanie wystarczająco niski poziom złożoności aplikacji i projektowania aplikacji do skalowania liczby dzierżawców i rozmiar danych dzierżawców i Obciążenie pracą.

Usługa Azure Search udostępnia kilka granice, które mogą być używane do izolowania danych i obciążenie dzierżawców.

## <a name="modeling-multitenancy-with-azure-search"></a>Obsługa wielu podmiotów modelowania z usługi Azure Search
W przypadku scenariusza wielodostępnym Deweloper aplikacji wymaga co najmniej jednej usługi wyszukiwania i dzielenia dzierżawcom między usługi i/lub indeksy. Usługa wyszukiwanie Azure ma kilka typowych wzorców podczas modelowania wielodostępnym scenariusza:

1. *Indeks dla poszczególnych dzierżawców:* każdy dzierżawca ma własnych indeksu w usłudze wyszukiwania, która jest współdzielony z innymi dzierżawcami.
2. *Usługi dla poszczególnych dzierżawców:* poszczególne dzierżawy zawierają własne dedykowane usługi Azure Search oferty najwyższy poziom separacji danych i jego obciążenie.
3. *Kombinacja obu:* większych i bardziej aktywny dzierżawcy przypisano dedykowanych usług podczas mniejszych dzierżaw są przypisane poszczególnych indeksy w ramach usług udostępnionych.

## <a name="1-index-per-tenant"></a>1. Indeks dla każdego dzierżawcy
![Wizerunku modelu indeksu na dzierżawy](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

W modelu indeksu na dzierżawy wielu dzierżawców zajmują jednej usługi Azure Search gdzie każdy dzierżawca ma własnych indeksu.

Dzierżawcy osiągnąć izolacji danych, ponieważ wszystkie żądania wyszukiwania, a operacje dokumentu są wydawane na poziomie indeksu w usłudze Azure Search. W warstwie aplikacji ma świadomości potrzeby umożliwiających kierowanie ruchem dzierżawców różnych prawidłowego indeksów podczas również zarządzanie zasobami na poziomie usługi wszystkich dzierżawców.

Atrybut klucza indeksu dla dzierżawy modelu jest możliwość dla deweloperów aplikacji do oversubscribe pojemność usługi wyszukiwania między dzierżawcami aplikacji. Jeśli dzierżawcy nierówny rozkład obciążenia, optymalnego połączenia dzierżawcy może rozłożone na indeksy usługi wyszukiwania w celu uwzględnienia szeregu wysokiej aktywne, obciążający zasoby dzierżawcy jednocześnie służąc długi tail mniej aktywne dzierżawców. Rozwiązanie jest niemożność modelu obsługi sytuacji, w której poszczególne dzierżawy jest jednocześnie wysokiej aktywna.

Wzór indeksu dla dzierżawy stanowi podstawę dla modelu koszt zmiennej, gdzie są kupowane ponoszonych z góry całej usługi Azure Search, a następnie wypełnione z dzierżawcami. Dzięki temu nieużywane miejsce wyznaczonego prób i wolnych kont.

Aplikacje z globalnego wpływ modelu indeksu na dzierżawy nie może być najbardziej efektywny. Jeśli aplikacja dzierżaw są dystrybuowane na całym świecie, oddzielne usługi może być konieczne dla każdego regionu, w którym mogą Zduplikuj kosztów na każdej z nich.

Wyszukiwanie Azure umożliwia skalowanie poszczególnych indeksy i łączna liczba indeksów zwiększa się. Jeśli odpowiednie ceny warstwy wybrano, partycji i replik można dodać do usługi wyszukiwania całego podczas konkretnego indeksu w ramach usługi zbytnio się rozrośnie pod względem ruchu lub magazynu.

Jeśli łączna liczba indeksów zbytnio się rozrośnie dla jednej usługi, innej usługi musi być przygotowana do uwzględnienia nowych dzierżaw. Jeśli indeksów do przeniesienia między usługami wyszukiwania w miarę dodawania nowych usług, danych z indeksu ma ręcznie skopiowane z jednego indeksu wzajemnie zgodnie z usługi Azure Search nie zezwala na indeks do przeniesienia.

## <a name="2-service-per-tenant"></a>2. Usługi dla poszczególnych dzierżawców
![Wizerunku modelu usług dla dzierżawców](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

W architekturze usługi na dzierżawy każdy dzierżawca ma własną usługę wyszukiwania.

W tym modelu aplikacji osiąga maksymalny poziom izolacji dla swoich dzierżaw. Każda usługa ma dedykowany Magazyn oraz przepustowość do obsługi żądania wyszukiwania, a także osobne klucze interfejsu API.

Dla aplikacji, której poszczególne dzierżawy zawierają duże zużycie lub Obciążenie ma mało zmienności dzierżawy dzierżawy modelu usług dla dzierżawy jest rzeczywistą możliwość wyboru zasobów nie są współdzielone przez różnych dzierżaw obciążeń.

Usługa na modelu dzierżawy oferuje również korzyści wynikające z modelu koszt przewidywalne, stały. Do momentu dzierżawcę, aby wypełnić go, jednak koszt na poszczególnych dzierżawy jest wyższy niż model indeksu na dzierżawy nie istnieje żadne początkowych inwestycji w usłudze całego wyszukiwania.

Model usługi na dzierżawy jest wydajne wybór dla aplikacji z globalnego zużycie. Z geograficznie rozproszonych dzierżawcami jest łatwy w celu zapewnienia każdego dzierżawcy usługi odpowiedni region.

Trudności w skalowanie tego wzorca wystąpić, gdy poszczególnych dzierżaw pokonać usługi. Usługa Azure Search nie obsługuje obecnie uaktualniania warstwę cenową usługi wyszukiwania, dlatego wszystkie dane będzie musiał ręcznie skopiowane do nowego dodatku service.

## <a name="3-mixing-both-models"></a>3. Mieszanie obu modeli
Wzorzec innego modelowania wielodostępności jest mieszanie strategii zarówno indeksu na dzierżawy i usług na dzierżawy.

Przez zmieszanie dwóch wzorców, dzierżaw największy aplikacji może być dedykowany usług podczas długich tail mniej aktywne, mniejszym dzierżawców może być indeksy w udostępnionej usłudze. Ten model gwarantuje, że największy dzierżaw spójnie wysoką wydajność usługi Ochrona mniejszych dzierżaw z dowolnym zakłócenia sąsiadów.

Jednak implementacja tej strategii polega prognozowania w przewidywania dzierżawcami, które wymaga dedykowanego usługę indeksu w usług udostępnionych. Złożoność aplikacji zwiększa się konieczność zarządzania oba te modele wielodostępności.

## <a name="achieving-even-finer-granularity"></a>Obsługiwanie jeszcze bardziej szczegółowy
Powyżej wzorce projektowe do modelu wielodostępnym scenariuszy w usłudze Azure Search założono uniform zakresu, w której poszczególne dzierżawy jest całego wystąpienia aplikacji. Jednak aplikacje czasami może obsługiwać wiele zakresów mniejsze.

Jeśli dzierżawa na usługi i indeksu na dzierżawy modele nie są wystarczająco małych zakresów, istnieje możliwość wzór indeksu w celu osiągnięcia jeszcze bardziej precyzyjną stopień szczegółowości.

Ma zachowywać się inaczej dla punktów końcowych klienta inny indeks, można dodać pola do indeksu, który określa wartość dla każdego klienta możliwe. Zawsze klient wywołuje usługi Azure Search do zapytań, lub zmodyfikuj indeks kod z aplikacji klienckiej określa wartość odpowiednią dla tego pola przy użyciu usługi Azure Search [filtru](https://msdn.microsoft.com/library/azure/dn798921.aspx) możliwości podczas przeszukiwania.

Ta metoda służy do osiągnięcia funkcji oddzielnych kont użytkowników, poziomy uprawnień oddzielne i nawet całkowicie różnych aplikacji.

> [!NOTE]
> Przy użyciu podejścia opisanego powyżej skonfigurować jeden indeks do obsługi wielu dzierżawców dotyczy znaczenie wyników wyszukiwania. Znaczenie w wyszukiwaniu są obliczane w zakresie poziomu indeksu, a nie zakresu poziomie dzierżawy, więc dzierżawcy wszystkich danych oceny przydatności podstawowej statystyk, takich jak częstotliwość termin jest włączona.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Usługa Azure Search jest atrakcyjnych wybór dla wielu aplikacji [Dowiedz się więcej o możliwości przeprowadzania usługi](http://aka.ms/whatisazsearch). Podczas obliczania różne wzorce projektowe dla wielodostępnych aplikacji, należy wziąć pod uwagę [różnych warstw cenowych](https://azure.microsoft.com/pricing/details/search/) oraz odpowiednie [usługi limity](search-limits-quotas-capacity.md) najlepiej dostosować usługi Azure Search, aby dopasować obciążeń aplikacji i architektur wszystkich rozmiarów.

Pytania dotyczące usługi Azure Search i scenariusze wielodostępnym może zostać skierowany do azuresearch_contact@microsoft.com.

