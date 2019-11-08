---
title: Projektowanie tabel Azure Cosmos DB do obsługi skalowania i wydajności
description: 'Przewodnik dotyczący projektowania tabeli usługi Azure Storage: projektowanie skalowalnych i wydajnych tabel w Azure Cosmos DB i tabeli usługi Azure Storage'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 499ac3a394339ebb07c36abeaaa761de22927941
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827770"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Przewodnik dotyczący projektowania tabeli usługi Azure Storage: projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę różne czynniki, takie jak wydajność, skalowalność i koszt. Jeśli wcześniej zaprojektowano schematy dla relacyjnych baz danych, te zagadnienia będą znane użytkownikowi, ale chociaż istnieją różne podobieństwa między modelem magazynu Table service platformy Azure i modeli relacyjnych, istnieją także liczne istotne różnice. Te różnice zwykle prowadzą do różnych projektów, które mogą wyglądać na intuicyjny lub niewłaściwy dla kogoś znającego relacyjne bazy danych, ale w przypadku projektowania dla magazynu NoSQL klucz/wartość, takiego jak Table service platformy Azure. Wiele z różnic w projekcie odzwierciedla fakt, że Table service jest zaprojektowana do obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek (wiersze w terminologii relacyjnej bazy danych) lub zestawy danych, które muszą obsługiwać wysoką transakcję woluminy: w związku z tym należy myśleć o sposobie przechowywania danych i zrozumieniu sposobu działania Table service. Dobrze zaprojektowany magazyn danych NoSQL umożliwia skalowanie znacznie bardziej wydajnych rozwiązań (i niższych kosztów) niż rozwiązanie korzystające z relacyjnej bazy danych. Ten przewodnik pomoże Ci w zapewnieniu tego tematu.  

## <a name="about-the-azure-table-service"></a>Informacje o Table service platformy Azure
W tej sekcji omówiono niektóre kluczowe funkcje Table service, które są szczególnie przydatne podczas projektowania pod kątem wydajności i skalowalności. Jeśli dopiero zaczynasz korzystać z usługi Azure Storage i Table service, najpierw Przeczytaj [wprowadzenie do Microsoft Azure Storage](../storage/common/storage-introduction.md) i [Zacznij korzystać z platformy Azure Table Storage przy użyciu platformy .NET](table-storage-how-to-use-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Chociaż ten przewodnik znajduje się na Table service, będzie on zawierał kilka dyskusji na temat usługi Azure Queue i BLOB Services oraz jak można ich używać wraz z Table service w rozwiązaniu.  

Co to jest Table service? Zgodnie z oczekiwaniami, Table service używa formatu tabelarycznego do przechowywania danych. W standardowej terminologii każdy wiersz tabeli reprezentuje jednostkę, a kolumny przechowują różne właściwości tej jednostki. Każda jednostka ma parę kluczy do unikatowego identyfikowania, a kolumna sygnatury czasowej, której używa Table service do śledzenia czasu ostatniej aktualizacji jednostki (pole sygnatury czasowej jest dodawane automatycznie i nie można ręcznie nadpisać sygnatury czasowej z dowolną wartością). Table service używa tego ostatniej modyfikacji sygnatury czasowej (LMT) w celu zarządzania optymistyczną współbieżność.  

> [!NOTE]
> Operacje interfejsu API REST Table service zwracają również wartość **ETag** , która pochodzi od ostatniej modyfikacji sygnatury czasowej (LMT). W tym dokumencie zobaczysz elementy ETag i LMT zamiennie, ponieważ odwołują się do tych samych danych bazowych.  
> 
> 

W poniższym przykładzie przedstawiono prosty projekt tabeli do przechowywania jednostek pracowników i działów. Wiele przykładów przedstawionych w dalszej części tego przewodnika jest opartych na tym prostym projekcie.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Znacznik czasu</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Nie</td>
<td>Parter</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Lip</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Dział</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Krzysztof</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Do tej pory ten projekt wygląda podobnie do tabeli w relacyjnej bazie danych, a kluczowe różnice są obowiązkowymi kolumnami i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda Właściwość zdefiniowana przez użytkownika, taka jak **FirstName** lub **Age** ma typ danych, na przykład liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. Chociaż w przeciwieństwie do relacyjnej bazy danych, charakter bez schematu Table service oznacza, że właściwość nie musi mieć tego samego typu danych dla każdej jednostki. Aby przechowywać złożone typy danych w pojedynczej właściwości, należy użyć serializowanego formatu, takiego jak JSON lub XML. Aby uzyskać więcej informacji na temat usługi Table Service, takiej jak obsługiwane typy danych, obsługiwane zakresy dat, reguły nazewnictwa i ograniczenia rozmiaru, zobacz [Omówienie modelu danych usługi Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Jak zobaczysz, wybór **PartitionKey** i **RowKey** jest podstawą dla dobrego projektu tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, wartości **PartitionKey** i **RowKey** są indeksowane, aby utworzyć klastrowany indeks, który umożliwia szybkie wyszukiwanie; jednak Table service nie tworzy żadnych indeksów pomocniczych, więc są to jedyne właściwości indeksowane (niektóre z opisanych poniżej wzorców pokazują, jak można obejść to oczywiste ograniczenie).  

Tabela składa się z co najmniej jednej partycji, w miarę jak zobaczysz, że wiele podjętych decyzji projektowych będzie wokół wyboru odpowiednich **PartitionKey** i **RowKey** w celu zoptymalizowania rozwiązania. Rozwiązanie może zawierać tylko jedną tabelę zawierającą wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie będzie zawierać wiele tabel. Tabele ułatwiają logiczne organizowanie jednostek, ułatwiają zarządzanie dostępem do danych przy użyciu list kontroli dostępu, a także usuwanie całej tabeli przy użyciu jednej operacji magazynu.  

### <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwa tabeli i **PartitionKey** wspólnie identyfikują partycję w ramach usługi magazynu, w której usługa Table przechowuje jednostkę. Jak również część schematu adresowania dla jednostek, partycje definiują zakres dla transakcji (zobacz [transakcje grupy jednostek](#entity-group-transactions) poniżej) i stanowią podstawę skalowania usługi Table Service. Aby uzyskać więcej informacji o partycjach, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](../storage/common/storage-scalability-targets.md).  

W Table service poszczególne usługi węzłów są co najmniej jedną kompletną partycją i usługą skalowania przez dynamiczne partycje równoważenia obciążenia między węzłami. Jeśli węzeł jest w obciążeniu, usługa Table Service może *podzielić* zakres partycji objętych przez ten węzeł na różne węzły; Po nawrocie ruchu usługa może *scalić* zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji o wewnętrznych szczegółach Table service, a w szczególności o tym, jak usługa zarządza partycjami, zobacz dokument [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W Table service, transakcje grupy jednostek (EGTs) są jedynym wbudowanym mechanizmem do wykonywania niepodzielnych aktualizacji w wielu jednostkach. EGTs są również nazywane *transakcjami wsadowymi* w niektórych dokumentach. EGTs może działać tylko na jednostkach przechowywanych w tej samej partycji (należy udostępnić ten sam klucz partycji w danej tabeli), więc w dowolnym momencie potrzebujesz niepodzielnych zachowań transakcyjnych dla wielu jednostek, aby zapewnić, że te jednostki znajdują się w tej samej partycji. Jest to często powód, aby zachować wiele typów jednostek w tej samej tabeli (i partycji), a nie używać wielu tabel dla różnych typów jednostek. Pojedynczy EGT może działać na maksymalnie 100 jednostkach.  Jeśli przesyłasz wiele współbieżnych EGTs do przetwarzania, należy upewnić się, że te EGTs nie działają na jednostkach, które są wspólne dla EGTs, ponieważ przetwarzanie w przeciwnym razie może być opóźnione.

EGTs również wprowadza potencjalne rozwiązanie do szacowania w projekcie: użycie większej liczby partycji spowoduje zwiększenie skalowalności aplikacji, ponieważ platforma Azure ma więcej możliwości w przypadku żądań równoważenia obciążenia w różnych węzłach, ale może to ograniczyć możliwości Twoja aplikacja do wykonywania transakcji niepodzielnych i zachowania silnej spójności danych. Ponadto istnieją konkretne cele skalowalności na poziomie partycji, która może ograniczyć przepływność transakcji, które można oczekiwać dla jednego węzła: Aby uzyskać więcej informacji o skalowalności dla kont usługi Azure Storage i usłudze Table Service, zobacz [Cele dotyczące skalowalności i wydajności usługi Azure Storage](../storage/common/storage-scalability-targets.md). W kolejnych sekcjach tego przewodnika omówiono różne strategie projektowania, które ułatwiają zarządzanie niektórymi potrzebami, takimi jak ten, i omawiamy najlepszy wybór klucza partycji na podstawie określonych wymagań aplikacji klienckiej.  

### <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności
Poniższa tabela zawiera kilka najważniejszych wartości, które należy znać podczas projektowania rozwiązania Table service:  

| Łączna pojemność konta usługi Azure Storage | 500 TB |
| --- | --- |
| Liczba tabel na koncie usługi Azure Storage |Ograniczone tylko pojemności konta magazynu |
| Liczba partycji w tabeli |Ograniczone tylko pojemności konta magazynu |
| Liczba jednostek w partycji |Ograniczone tylko pojemności konta magazynu |
| Rozmiar pojedynczej jednostki |Do 1 MB i maksymalnie 255 właściwości (łącznie z **PartitionKey**, **RowKey**i **sygnaturą czasową**) |
| Rozmiar **PartitionKey** |Ciąg o rozmiarze do 1 KB |
| Rozmiar **RowKey** |Ciąg o rozmiarze do 1 KB |
| Rozmiar transakcji grupy jednostek |Transakcja może obejmować maksymalnie 100 jednostek, a ładunek musi mieć rozmiar mniejszy niż 4 MB. Element EGT może aktualizować tylko raz jednostkę. |

Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogie, ale należy uwzględnić oszacowania kosztów zarówno w przypadku użycia pojemności, jak i liczby transakcji w ramach oceny dowolnego rozwiązania, które używa Table service. Jednak w wielu scenariuszach przechowywanie nieznormalizowanych lub zduplikowanych danych w celu poprawienia wydajności lub skalowalności rozwiązania jest prawidłowym podejściem do wykonania. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel
Te listy zawierają podsumowanie najważniejszych wytycznych, które należy wziąć pod uwagę podczas projektowania tabel. w tym przewodniku opisano je wszystkie w dalszej części artykułu. Wytyczne te różnią się od wytycznych, które zwykle są stosowane w przypadku projektowania relacyjnej bazy danych.  

Projektowanie rozwiązania Table service w celu uzyskania efektywnego *odczytu* :

* ***Projekt do wykonywania zapytań w aplikacjach z dużym obciążeniem.*** Podczas projektowania tabel należy zastanowić się nad zapytaniami (zwłaszcza z uwzględnieniem opóźnień), które zostaną wykonane przed zawieszeniem się, jak należy zaktualizować jednostki. Zwykle jest to wydajne i wydajne rozwiązanie.  
* ***W zapytaniach Określ zarówno PartitionKey, jak i RowKey.*** *Zapytania punktowe* , takie jak te, to najbardziej wydajne zapytania dotyczące usługi Table Service.  
* ***Rozważ przechowywanie zduplikowanych kopii jednostek.*** Magazyn tabel jest tani, dlatego należy rozważyć przechowywanie tej samej jednostki wielokrotnie (z różnymi kluczami) w celu umożliwienia wydajniejszych zapytań.  
* ***Rozważ wyprowadzenie denormalizacji danych.*** Magazyn tabel jest tani, dlatego należy rozważyć denormalizację danych. Na przykład, przechowuj jednostki podsumowujące, tak aby zapytania dotyczące danych agregowanych musieli uzyskać dostęp do pojedynczej jednostki.  
* ***Użyj wartości klucza złożonego.*** Jedyne klucze są **PartitionKey** i **RowKey**. Na przykład użyj wartości klucza złożonego, aby włączyć alternatywne ścieżki dostępu do jednostek.  
* ***Użyj projekcji zapytania.*** Możesz zmniejszyć ilość danych przesyłanych przez sieć za pomocą zapytań, które wybierają tylko potrzebne pola.  

Projektowanie rozwiązania Table service do wydajnego *zapisu* :  

* ***Nie twórz partycji aktywnych.*** Wybierz klucze, które umożliwiają rozproszenie żądań w wielu partycjach w dowolnym momencie.  
* ***Unikaj wzrostu ruchu.*** Wygładź ruch w rozsądnym czasie i unikaj wzrostu ruchu.
* ***Niekoniecznie utwórz oddzielną tabelę dla każdego typu jednostki.*** Gdy wymagane są transakcje niepodzielne między typami jednostek, można przechowywać te wiele typów jednostek w tej samej partycji w tej samej tabeli.
* ***Należy wziąć pod uwagę maksymalną przepływność, która ma zostać osiągnięta.*** Należy pamiętać o obiektach docelowych skalowalności Table service i upewnić się, że projekt nie spowoduje przekroczenia ich.  

Po przeczytaniu tego przewodnika zobaczysz przykłady, które stosują wszystkie te zasady do ćwiczeń.  

## <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Rozwiązania Table service mogą być bardzo czasochłonne, duże ilościowe lub mieszane. Ta sekcja koncentruje się na zagadnieniach, które należy wziąć pod uwagę podczas projektowania Table service w celu wydajnej obsługi operacji odczytu. Zwykle projekt obsługujący operacje odczytu wydajnie jest również wydajny w przypadku operacji zapisu. Istnieją jednak dodatkowe zagadnienia, które należy wziąć pod uwagę podczas projektowania, aby obsługiwały operacje zapisu, omówione w następnej sekcji [projekt do modyfikacji danych](#design-for-data-modification).

Dobrym punktem początkowym do projektowania rozwiązania Table service, aby umożliwić odczytywanie danych w sposób efektywny, jest zaproszenie "jakie zapytania należy wykonać moja aplikacja, aby pobrać potrzebne dane z Table service?".  

> [!NOTE]
> Mając Table service, ważne jest, aby projekt został skorygowany z góry, ponieważ jest trudny i kosztowny do jego późniejszej zmiany. Na przykład w relacyjnej bazie danych często możliwe jest rozwiązanie problemów z wydajnością po prostu przez dodanie indeksów do istniejącej bazy danych: nie jest to opcja z Table service.  
> 
> 

W tej sekcji omówiono kluczowe problemy, które należy rozwiązać podczas projektowania tabel do wykonywania zapytań. Tematy omówione w tej sekcji obejmują:

* [Jak wybór PartitionKey i RowKey wpływa na wydajność zapytań](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Wybieranie odpowiedniego PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optymalizowanie zapytań dla Table service](#optimizing-queries-for-the-table-service)
* [Sortowanie danych w Table service](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór PartitionKey i RowKey wpływa na wydajność zapytań
W poniższych przykładach przyjęto założenie, że usługa Table Service zapisuje jednostki pracowników przy użyciu następującej struktury (większość przykładów pomija Właściwość **timestamp** dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (Nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **Imię** |Ciąg |
| **Nazwisko** |Ciąg |
| **Ważności** |Liczba całkowita |
| **EmailAddress** |Ciąg |

W poprzedniej sekcji usługi Azure Table service Overview opisano niektóre kluczowe funkcje Table service platformy Azure, które mają bezpośredni wpływ na projektowanie zapytań. Poniżej przedstawiono ogólne wytyczne dotyczące projektowania zapytań Table service. Składnia filtru użyta w poniższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz temat [jednostki zapytań](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* ***Zapytanie punktowe*** jest najbardziej wydajnym wyszukiwaniem do użycia i jest zalecane do użycia w odnośnikach lub wyszukiwaniach o wysokim poziomie, które wymagają najmniejszego opóźnienia. Takie zapytanie może służyć do wydajnego lokalizowania pojedynczej jednostki przez określenie wartości **PartitionKey** i **RowKey** . Na przykład: $filter = (PartitionKey EQ "Sales") i (RowKey EQ "2")  
* Druga Najlepsza to ***zapytanie zakresowe*** używające **PartitionKey** i filtrów dla zakresu wartości **RowKey** , aby zwrócić więcej niż jedną jednostkę. Wartość **PartitionKey** identyfikuje konkretną partycję, a wartości **RowKey** identyfikują podzestaw jednostek w tej partycji. Na przykład: $filter = PartitionKey EQ "Sales" i RowKey GE "i RowKey lt"  
* Trzecia Najlepsza to ***skanowanie partycji*** korzystające z **PartitionKey** i filtrów dla innej właściwości niebędącej kluczem, które może zwrócić więcej niż jedną jednostkę. Wartość **PartitionKey** identyfikuje konkretną partycję, a wartości właściwości wybierają podzbiór jednostek w tej partycji. Na przykład: $filter = PartitionKey EQ "Sales" i LastName EQ "Kowalski"  
* ***Skanowanie tabeli*** nie zawiera **PartitionKey** i jest niewydajne, ponieważ przeszukuje wszystkie partycje, które składają się na tabelę, z uwzględnieniem dowolnych pasujących jednostek. Przeprowadzi skanowanie tabeli niezależnie od tego, czy filtr używa **RowKey**. Na przykład: $filter = LastName EQ "Nowak"  
* Zapytania Table Storage platformy Azure zwracające wiele jednostek zwracają je posortowane w kolejności **PartitionKey** i **RowKey** . Aby uniknąć tworzenia jednostek w kliencie, wybierz **RowKey** , który definiuje najbardziej typowy porządek sortowania. Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Użycie "**lub**" do określenia filtru opartego na wartościach **RowKey** powoduje skanowanie partycji i nie jest traktowane jako zapytanie o zakres. W związku z tym należy unikać zapytań, które używają filtrów takich jak: $filter = PartitionKey EQ "Sales" i (RowKey EQ "121" lub RowKey EQ "322")  

Aby zapoznać się z przykładami kodu po stronie klienta, które używają biblioteki klienta usługi Storage do wykonywania wydajnych zapytań, zobacz:  

* [Wykonywanie zapytania punktu przy użyciu biblioteki klienta usługi Storage](#executing-a-point-query-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu LINQ](#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, które mogą obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli, zobacz:  

* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien zrównoważyć potrzebę umożliwienia używania EGTs (w celu zapewnienia spójności) przed wymaganiem dystrybucji jednostek na wielu partycjach (aby zapewnić skalowalne rozwiązanie).  

Z jednej najwyższej klasy można przechowywać wszystkie jednostki w jednej partycji, ale może to ograniczyć skalowalność rozwiązania i uniemożliwić usłudze tabel możliwość równoważenia obciążenia żądań. Z drugiej najwyższej wartości można przechowywać jedną jednostkę na partycję, która byłaby wysoce skalowalna, co umożliwia usłudze tabel Równoważenie obciążenia żądań, ale uniemożliwi korzystanie z transakcji grupy jednostek.  

Idealny **PartitionKey** to taki, który umożliwia korzystanie z wydajnych zapytań i zawiera wystarczające partycje, aby zapewnić skalowalność rozwiązania. Zazwyczaj można stwierdzić, że jednostki będą mieć odpowiednią właściwość, która dystrybuuje jednostki w wystarczającej partycji.

> [!NOTE]
> Na przykład w systemie, w którym są przechowywane informacje o użytkownikach lub pracownikach, UserID może być dobry PartitionKey. Może istnieć kilka jednostek, które używają danego identyfikatora użytkownika jako klucza partycji. Każda jednostka, która przechowuje dane dotyczące użytkownika, jest zgrupowana w jedną partycję, więc te jednostki są dostępne za pośrednictwem transakcji grupy jednostek, przy czym nadal są wysoce skalowalne.
> 
> 

Dostępne są dodatkowe zagadnienia dotyczące wyboru **PartitionKey** , które odnoszą się do sposobu wstawiania, aktualizowania i usuwania jednostek: Zobacz sekcję [dotyczącą modyfikacji danych](#design-for-data-modification) poniżej.  

### <a name="optimizing-queries-for-the-table-service"></a>Optymalizowanie zapytań dla Table service
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** w pojedynczym indeksie klastrowanym, w związku z czym przyczyny tego zapytania są najbardziej wydajne. Jednak żadne indeksy nie są inne niż w indeksie klastrowanym w **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby umożliwić wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład lokalizowanie jednostek pracowników na podstawie poczty e-mail, identyfikatora pracownika lub nazwiska. [Wzorce projektowe tabeli](#table-design-patterns) sekcji, które dotyczą tych typów wymagań i opisują sposoby obejścia tego faktu, że Table Service nie udostępniają indeksów pomocniczych:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości **RowKey** w oddzielnych partycjach lub w oddzielnych tabelach w celu szybkiego i efektywnego wyszukiwania oraz alternatywnych zamówień sortowania przy użyciu różnych  **RowKey** wartości.  
* [Indeks jednostek indeksu](#index-entities-pattern) — utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

### <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w Table service

Wyniki zapytania zwrócone przez Table service są sortowane w kolejności rosnącej na podstawie **PartitionKey** , a następnie według **RowKey**.

> [!NOTE]
> Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Klucze w tabeli usługi Azure Storage to wartości ciągów i aby zapewnić prawidłowe sortowanie wartości liczbowych, należy przekonwertować je na stałą długość i uzupełnić je zerami. Na przykład jeśli wartość identyfikatora pracownika, która jest używana jako **RowKey** , jest wartością całkowitą, należy przekonwertować pracownika o identyfikatorze **123** do **00000123**. 

Wiele aplikacji ma wymagania dotyczące korzystania z danych posortowanych w różnych zamówieniach: na przykład sortowanie pracowników według nazwy lub dołączanie daty. Następujące wzorce w [wzorach projektu tabeli](#table-design-patterns) sekcji przedstawiają sposób alternatywnych zamówień sortowania dla jednostek:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości RowKey (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości RowKey.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości RowKey w oddzielnych partycjach w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości RowKey.
* [Wzorzec końca dziennika](#log-tail-pattern) — pobranie *n* jednostek, które zostały ostatnio dodane do partycji przy użyciu wartości **RowKey** , która jest sortowana w odwrotnej kolejności daty i godziny.  

## <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ta sekcja koncentruje się na zagadnieniach projektowych dotyczących optymalizacji operacji wstawiania, aktualizacji i usuwania. W niektórych przypadkach należy oszacować różnice między projektami, które optymalizują się w celu wykonywania zapytań dotyczących projektów, które optymalizują się pod kątem modyfikacji danych, tak samo jak w przypadku projektów relacyjnych baz danych (chociaż techniki zarządzania zasobami projektu są inne w relacyjnej bazie danych). W sekcjach [wzorów projektowania tabeli](#table-design-patterns) opisano niektóre szczegółowe wzorce projektowe dla Table Service i wyróżniono niektóre z nich. W ramach tego problemu okaże się, że wiele projektów zoptymalizowanych pod kątem wykonywania zapytań dotyczących jednostek również dobrze sprawdza się w przypadku modyfikowania jednostek.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optymalizowanie wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć jednostkę, musi być możliwe jej zidentyfikowanie przy użyciu wartości **PartitionKey** i **RowKey** . W związku z tym wybór opcji **PartitionKey** i **RowKey** w celu zmodyfikowania jednostek powinien spełniać podobne kryteria, aby obsługiwały zapytania dotyczące punktów, ponieważ chcemy identyfikować jednostki tak efektywnie, jak to możliwe. Nie chcesz korzystać z niewydajnej partycji lub skanowania tabeli, aby zlokalizować jednostkę w celu odnalezienia wartości **PartitionKey** i **RowKey** , które należy zaktualizować lub usunąć.  

Następujące wzorce w [wzorach projektu tabeli](#table-design-patterns) sekcji dotyczą optymalizacji wydajności lub operacji wstawiania, aktualizowania i usuwania:  

* [Duże Usuwanie woluminu wzorzec](#high-volume-delete-pattern) — umożliwia usunięcie dużej liczby jednostek przez zapisanie wszystkich jednostek do jednoczesnego usunięcia w oddzielnej tabeli. obiekty można usunąć, usuwając tabelę.  
* [Wzorzec serii danych](#data-series-pattern) — Przechowaj kompletne serie danych w pojedynczej jednostce, aby zminimalizować liczbę wysyłanych żądań.  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern) — używanie wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  
* [Wzorzec dużych jednostek](#large-entities-pattern) — Użyj magazynu obiektów BLOB do przechowywania dużych wartości właściwości.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Zapewnianie spójności przechowywanych jednostek
Drugim czynnikiem, który ma wpływ na wybór kluczy do optymalizacji modyfikacji danych, jest sposób zapewnienia spójności przy użyciu transakcji niepodzielnych. Elementu EGT można używać tylko do obsługi jednostek przechowywanych w tej samej partycji.  

W poniższych wzorcach [wzorców projektu tabeli](#table-design-patterns) sekcji adres zarządzania spójnością:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości RowKey w oddzielnych partycjach lub w oddzielnych tabelach w celu szybkiego i efektywnego wyszukiwania oraz alternatywnych zamówień sortowania przy użyciu różnych **RowKey** wartością.  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern) — Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.
* [Indeks jednostek indeksu](#index-entities-pattern) — utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  
* [Wzorzec denormalizacji](#denormalization-pattern) — połącz powiązane dane ze sobą w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu pojedynczego punktu zapytania.  
* [Wzorzec serii danych](#data-series-pattern) — Przechowaj kompletne serie danych w pojedynczej jednostce, aby zminimalizować liczbę wysyłanych żądań.  

Informacje o transakcjach grupy jednostek można znaleźć w sekcji dotyczącej [transakcji grupy jednostek](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zagwarantowanie, że projekt do wydajnej modyfikacji ułatwia wykonywanie wydajnych zapytań
W wielu przypadkach projekt do wydajnego wykonywania zapytań umożliwia wydajne modyfikacje, ale zawsze należy sprawdzić, czy jest to przypadek dla konkretnego scenariusza. Niektóre wzorce w [wzorach projektu tabeli](#table-design-patterns) w sekcji jawnie szacują kompromisy między wykonywaniem zapytań i modyfikowaniem jednostek, a zawsze należy wziąć pod uwagę liczbę poszczególnych typów operacji.  

Poniższe wzorce w sekcjach [projektowanie projektowe](#table-design-patterns) , które stanowią rozróżnienia między projektowaniem pod kątem wydajnych zapytań i projektowaniem do wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](#compound-key-pattern) — Użyj złożonych wartości **RowKey** , aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  
* [Wzorzec końca dziennika](#log-tail-pattern) — pobranie *n* jednostek, które zostały ostatnio dodane do partycji przy użyciu wartości **RowKey** , która jest sortowana w odwrotnej kolejności daty i godziny.  

## <a name="encrypting-table-data"></a>Szyfrowanie danych tabeli
Biblioteka klienta usługi Azure Storage dla platformy .NET obsługuje szyfrowanie właściwości jednostki ciągów dla operacji wstawiania i zamieniania. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem do ciągów po odszyfrowaniu.    

W przypadku tabel oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić przez określenie atrybutu [EncryptProperty] (dla jednostek POCO, które pochodzą z klasy tableentity) lub mechanizmu rozwiązywania konfliktów w opcjach żądania. Program rozpoznawania szyfrowania jest delegatem, który pobiera klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania Biblioteka kliencka będzie używać tych informacji do decydowania, czy właściwość powinna być szyfrowana podczas zapisywania w sieci. Delegat udostępnia również możliwość logiki dotyczącej sposobu szyfrowania właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Nie trzeba podawać tych informacji podczas odczytywania lub wykonywania zapytań dotyczących jednostek.

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości mógł zostać zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonania dodatkowych wywołań usługi do odczytu istniejącej jednostki z usługi lub użycia nowego klucza dla każdej właściwości, co nie jest odpowiednie ze względu na wydajność.     

Aby uzyskać informacje na temat szyfrowania danych tabeli, zobacz [szyfrowanie po stronie klienta i Azure Key Vault Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modelowanie relacji
Tworzenie modeli domen to kluczowy etap projektowania złożonych systemów. Zazwyczaj proces modelowania jest używany do identyfikowania jednostek i relacji między nimi w celu zrozumienia domeny biznesowej i poinformowania o projekcie systemu. Ta sekcja koncentruje się na sposobach przekształcania niektórych typowych typów relacji znalezionych w modelach domen na projekty dla Table service. Proces mapowania z logicznego modelu danych na fizyczny model danych oparty na NoSQL różni się od tego, który jest używany podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zazwyczaj przyjmuje proces normalizacji danych zoptymalizowany pod kątem minimalizowania nadmiarowości — oraz możliwość wykonywania zapytań deklaratywnych, która stanowi streszczenie sposobu działania bazy danych.  

### <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacja jeden do wielu między obiektami domeny biznesowej występuje często: na przykład jeden dział ma wielu pracowników. Istnieje kilka sposobów implementacji relacji jeden-do-wielu w Table service każdy ze specjalistami i wadami, które mogą mieć zastosowanie w konkretnym scenariuszu.  

Rozważmy przykład dużej firmy wielonarodowej z dziesiątki tysięcy działów i jednostek pracowników, gdzie każdy dział ma wielu pracowników i każdego pracownika jako skojarzony z jednym określonym działem. Jednym z metod jest przechowywanie oddzielnych jednostek działu i pracowników, takich jak następujące:  

![Jednostka działu i pracownika][1]

Ten przykład pokazuje niejawną relację "jeden do wielu" między typami opartymi na wartości **PartitionKey** . Każdy dział może mieć wielu pracowników.  

Ten przykład pokazuje również jednostkę działu i powiązane jednostki pracowników w tej samej partycji. Można użyć różnych partycji, tabel, a nawet kont magazynu dla różnych typów jednostek.  

Alternatywnym podejściem jest denormalizowanie danych i przechowywanie tylko jednostek pracowników z nieznormalizowanymi danymi działu, jak pokazano w poniższym przykładzie. W tym konkretnym scenariuszu takie nieznormalizowane podejście może nie być najlepszym rozwiązaniem, jeśli istnieje wymóg, aby można było zmienić szczegóły menedżera działu, ponieważ w tym celu należy zaktualizować każdego pracownika w dziale.  

![Jednostka Employee][2]

Aby uzyskać więcej informacji, zobacz [wzorzec denormalizacji](#denormalization-pattern) w dalszej części tego przewodnika.  

Poniższa tabela zawiera podsumowanie specjalistów i wad każdego z podejść przedstawionych powyżej do przechowywania jednostek pracowników i działów, które mają relację jeden do wielu. Należy również wziąć pod uwagę, jak często mają być wykonywane różne operacje: może być możliwe posiadanie projektu, który obejmuje kosztowną operację, jeśli ta operacja jest wykonywana tylko rzadko.  

<table>
<tr>
<th>Wynosi</th>
<th>Formaty</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, ta sama partycja, ta sama tabela</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu przy użyciu jednej operacji.</li>
<li>Można użyć EGT, aby zachować spójność, jeśli istnieje potrzeba modyfikacji jednostki działu przy każdej aktualizacji/wstawieniu/usunięciu jednostki pracownika. Na przykład w przypadku utrzymania liczby pracowników działu dla poszczególnych działów.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Operacje magazynu są wykonywane w tej samej partycji. W przypadku dużych ilości transakcji może to spowodować powstanie punktu aktywnego.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Oddzielne typy jednostek, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu lub jednostkę pracownika przy użyciu jednej operacji.</li>
<li>W przypadku dużych ilości transakcji może to ułatwić rozproszenie obciążenia na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Nie można użyć EGTs, aby zachować spójność podczas aktualizowania/wstawiania/usuwania pracownika i aktualizowania działu. Na przykład w jednostce działu można aktualizować liczbę pracowników.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizowanie do typu pojedynczej jednostki</td>
<td>
<ul>
<li>Wszystkie potrzebne informacje można pobrać przy użyciu pojedynczego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Zachowanie spójności może być kosztowne, jeśli trzeba zaktualizować informacje działu (wymaga to zaktualizowania wszystkich pracowników w dziale).</li>
</ul>
</td>
</tr>
</table>

Wybór między tymi opcjami oraz to, które z zalet i wad są najbardziej znaczące, zależy od konkretnych scenariuszy aplikacji. Na przykład jak często modyfikujesz jednostki działu; wszystkie zapytania pracowników potrzebują dodatkowych informacji działu; jak blisko ograniczeń skalowalności w partycjach lub na koncie magazynu?  

### <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modele domeny mogą zawierać relacje jeden do jednego między jednostkami. Jeśli zachodzi potrzeba zaimplementowania relacji jeden-do-jednego w Table service, należy również wybrać, jak połączyć dwie powiązane jednostki, gdy trzeba je pobrać. Ten link może być niejawny, na podstawie Konwencji w wartościach klucza lub jawnie przez przechowywanie linku w postaci wartości **PartitionKey** i **RowKey** w każdej jednostce względem powiązanej jednostki. Aby zapoznać się z omówieniem, czy należy przechowywać powiązane jednostki w tej samej partycji, zobacz [relacje jeden do wielu](#one-to-many-relationships).  

Istnieją także uwagi dotyczące implementacji, które mogą prowadzić do wdrożenia relacji jeden-do-jednego w Table service:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [wzorzec dużych jednostek](#large-entities-pattern)).  
* Implementowanie kontroli dostępu (Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu za pomocą sygnatur dostępu współdzielonego](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Dołącz do klienta
Chociaż istnieją metody do modelowania relacji w Table service, nie należy zapominać, że dwa podstawowe przyczyny używania Table service są skalowalne i wydajnościowe. Jeśli okaże się, że modelowanie ma wiele relacji, które naruszają wydajność i skalowalność rozwiązania, należy poproszony o to, jeśli konieczne jest skompilowanie wszystkich relacji danych w projekcie tabeli. Może być możliwe uproszczenie projektowania i zwiększenie skalowalności i wydajności rozwiązania, Jeśli zezwolisz aplikacji klienckiej na wykonywanie dowolnych koniecznych sprzężeń.  

Na przykład jeśli masz małe tabele zawierające dane, które nie zmieniają się często, możesz pobrać te dane raz i umieścić je w pamięci podręcznej na kliencie. Może to uniknąć powtarzanych roundtrip, aby pobrać te same dane. W przykładach, które zostały opisane w tym przewodniku, zestaw działów w małej organizacji może być niewielki i często zmieniany, dzięki czemu jest to dobry kandydat dla danych, które aplikacja kliencka może pobrać raz i buforować jako dane wyszukiwania.  

### <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka korzysta z zestawu klas, które stanowią część relacji dziedziczenia reprezentującej jednostki biznesowe, można łatwo utrzymać te jednostki w Table service. Na przykład w aplikacji klienckiej można określić następujący zestaw klas, gdzie **osoba** jest klasą abstrakcyjną.

![Diagram ER relacji dziedziczenia][3]

Można utrzymywać wystąpienia dwóch konkretnych klas w Table service przy użyciu pojedynczej tabeli osób, która będzie wyglądać następująco:  

![Diagram jednostki klienta i jednostki pracownika][4]

Więcej informacji o pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta znajduje się w sekcji [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types) w dalszej części tego przewodnika. Zawiera przykłady sposobu rozpoznawania typu jednostki w kodzie klienta.  

## <a name="table-design-patterns"></a>Wzorce projektowe tabeli
W poprzednich sekcjach pojawiły się pewne szczegółowe dyskusje na temat optymalizowania projektu tabeli na potrzeby pobierania danych jednostki przy użyciu zapytań oraz wstawiania, aktualizowania i usuwania danych jednostki. W tej sekcji opisano niektóre wzorce, które są odpowiednie do użycia z rozwiązaniami Table service. Ponadto zobaczysz, jak można praktycznie rozwiązać niektóre problemy i wady, które zostały wcześniej zgłoszone w tym przewodniku. Poniższy diagram podsumowuje relacje między różnymi wzorcami:  

![Obraz wzorców projektu tabeli][5]

Powyższa Mapa wzorca podświetla niektóre relacje między wzorcami (Blue) i antywzorców (pomarańczowy), które są opisane w tym przewodniku. Istnieją oczywiście wiele innych wzorców, które warto wziąć pod uwagę. Na przykład jeden z najważniejszych scenariuszy usługi Table Service to użycie [wzorca widoku materiałowego](https://msdn.microsoft.com/library/azure/dn589782.aspx) z wzorca [podziału odpowiedzialności (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego wewnątrz partycji
Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** . Aktualizacje między kopiami można utrzymywać spójnie przy użyciu EGTs.  

#### <a name="context-and-problem"></a>Kontekst i problem
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** . Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład przy użyciu struktury tabeli pokazanej poniżej, aplikacja kliencka może użyć zapytania punkt do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości **PartitionKey** i **RowKey** ). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.

![Jednostka Employee][6]

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że usługa Table Service nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż kolejność **RowKey** .  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii poszczególnych jednostek z każdą kopią przy użyciu innej wartości **RowKey** . Jeśli przechowujesz jednostkę z poniższymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów **RowKey**"empid_" i "email_" umożliwiają wykonywanie zapytań dotyczących jednego pracownika lub zakresu pracowników przy użyciu zakresu adresów e-mail lub identyfikatorów pracowników.  

![Jednostka pracownika z różnymi wartościami RowKey][7]

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika i jeden z nich w adresie e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey EQ "Sales") i (RowKey EQ "empid_000223")  
* $filter = (PartitionKey EQ "Sales") i (RowKey EQ "email_jonesj@contoso.com")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail przez wyszukanie jednostek z odpowiednim prefiksem w **RowKey**.  

* Aby znaleźć wszystkich pracowników działu sprzedaży z identyfikatorem pracownika z zakresu od 000100 do 000199, użyj: $filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000100") i (RowKey Le "empid_000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail rozpoczynającego się od litery "a", użyj: $filter = (PartitionKey EQ "Sales") i (RowKey GE "email_a") i (RowKey lt "email_b")  
  
  Składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz temat [jednostki zapytań](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Użycie magazynu tabel jest stosunkowo niedrogie, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Należy jednak zawsze oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, które będą wykonywane w aplikacji klienckiej.  
* Ponieważ pomocnicze jednostki indeksu są przechowywane w tej samej partycji co oryginalne jednostki, należy upewnić się, że elementy docelowe skalowalności dla pojedynczej partycji nie zostaną przekroczone.  
* Zduplikowane jednostki można zachować spójnie ze sobą za pomocą EGTs, aby zaktualizować dwie kopie jednostki jako niepodzielne. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz sekcję [Korzystanie z transakcji grupy jednostek](#entity-group-transactions).  
* Wartość używana dla **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w **RowKey** (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie na podstawie górnych i dolnych granic.  
* Nie trzeba duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w **RowKey** nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:

![Jednostka Employee][8]

* Zazwyczaj lepszym rozwiązaniem jest przechowywanie zduplikowanych danych i upewnienie się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania, niż w celu znalezienia jednostki i drugiej, aby wyszukać wymagane dane.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy, gdy klient musi pobrać jednostki w różnych zamówieniach sortowania i gdzie można zidentyfikować poszczególne jednostki przy użyciu różnych unikatowych wartości. Należy jednak pamiętać, że nie przekracza limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych wartości **RowKey** .  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern)
* [Wzorzec klucza złożonego](#compound-key-pattern)
* [Transakcje grupy jednostek](#entity-group-transactions)
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego między partycjami
Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **RowKey** w oddzielnych partycjach lub w oddzielnych tabelach, aby włączyć szybkie i wydajne wyszukiwania oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  

#### <a name="context-and-problem"></a>Kontekst i problem
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** . Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład przy użyciu struktury tabeli pokazanej poniżej, aplikacja kliencka może użyć zapytania punkt do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości **PartitionKey** i **RowKey** ). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.  

![Jednostka Employee][9]

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że usługa Table Service nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż kolejność **RowKey** .  

Przewidujesz dużą liczbę transakcji w odniesieniu do tych jednostek i chcesz zminimalizować ryzyko związane z Table service szybkością ograniczającą klienta.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki przy użyciu różnych wartości **PartitionKey** i **RowKey** . Jeśli przechowujesz jednostkę z poniższymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów **PartitionKey**"empid_" i "email_" umożliwiają zidentyfikowanie indeksu, który ma być używany w zapytaniu.  

![Jednostka pracownika z indeksem podstawowym i jednostką pracownika z indeksem pomocniczym][10]

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika i jeden z nich w adresie e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey EQ "empid_Sales") i (RowKey EQ "000223")
* $filter = (PartitionKey EQ "email_Sales") i (RowKey EQ "jonesj@contoso.com")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail przez wyszukanie jednostek z odpowiednim prefiksem w **RowKey**.  

* Aby znaleźć wszystkich pracowników działu sprzedaży z identyfikatorem pracownika z zakresu od **000100** do **000199** posortowanego w kolejności identyfikatorów pracowników: $Filter = (PartitionKey EQ "empid_Sales") i (RowKey GE "000100") i (RowKey Le "000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail rozpoczynającego się od "a" posortowanego w kolejności adresów e-mail Użyj: $filter = (PartitionKey EQ "email_Sales") i (RowKey GE "a") i (RowKey lt "b")  

Należy zauważyć, że składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz temat [jednostki zapytań](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Zduplikowane jednostki można zachować ze sobą spójnie, korzystając ze [wzorca ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern) , aby zachować jednostki indeksu podstawowego i pomocniczego.  
* Użycie magazynu tabel jest stosunkowo niedrogie, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Należy jednak zawsze oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, które będą wykonywane w aplikacji klienckiej.  
* Wartość używana dla **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w **RowKey** (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie na podstawie górnych i dolnych granic.  
* Nie trzeba duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania, które przeszukują jednostki przy użyciu adresu e-mail w **RowKey** , nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:
  
  ![Jednostka pracownika z dodatkowym indeksem][11]
* Zazwyczaj lepszym rozwiązaniem jest przechowywanie zduplikowanych danych i upewnienie się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania niż w celu wyszukania jednostki przy użyciu pomocniczego indeksu i innego do przeszukiwania wymaganych danych w indeksie podstawowym.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy, gdy klient musi pobrać jednostki w różnych zamówieniach sortowania i gdzie można zidentyfikować poszczególne jednostki przy użyciu różnych unikatowych wartości. Użyj tego wzorca, jeśli chcesz uniknąć przekroczenia limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych wartości **RowKey** .  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Wzorzec ostatecznie spójnych transakcji
Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.  

#### <a name="context-and-problem"></a>Kontekst i problem
EGTs Włącz transakcję niepodzielną w wielu jednostkach, które współużytkują ten sam klucz partycji. Ze względu na wydajność i skalowalność można zdecydować się na przechowywanie jednostek, które mają wymagania spójności w oddzielnych partycjach lub w osobnym systemie magazynu: w takiej sytuacji nie można użyć EGTs do zachowania spójności. Na przykład może istnieć wymóg zachowania spójności ostatecznej między:  

* Jednostki przechowywane w dwóch różnych partycjach w tej samej tabeli, w różnych tabelach lub na różnych kontach magazynu.  
* Jednostka przechowywana w Table service i obiekt BLOB przechowywany w Blob service.  
* Jednostka przechowywana w Table service i pliku w systemie plików.  
* Magazyn jednostek w Table service nie został jeszcze zindeksowany przy użyciu usługi Azure Wyszukiwanie poznawcze.  

#### <a name="solution"></a>Rozwiązanie
Korzystając z kolejek platformy Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną na co najmniej dwóch partycjach lub systemach magazynowania.
Aby zilustrować to podejście, Załóżmy, że wymagane jest umożliwienie archiwizowania starych jednostek pracowników. Stare jednostki pracowników są rzadko badane i powinny być wykluczone z wszelkich działań, które zajmują się bieżącymi pracownikami. Aby zaimplementować to wymaganie, należy przechowywać aktywnych pracowników w **bieżącej** tabeli i starych pracowników w tabeli **archiwum** . Archiwizowanie pracownika wymaga usunięcia jednostki z **bieżącej** tabeli i dodania jednostki do tabeli **archiwum** , ale nie można użyć EGT do wykonania tych dwóch operacji. Aby uniknąć ryzyka, że błąd powoduje, że jednostka pojawia się w obu tabelach lub żadnej z nich, operacja archiwizowania musi być ostatecznie spójna. Poniższy diagram sekwencji przedstawia kroki tej operacji. Więcej szczegółów na temat ścieżek wyjątków znajduje się w tekście poniżej.  

![Diagram rozwiązania na potrzeby spójności ostatecznej][12]

Klient inicjuje operację archiwizowania, umieszczając komunikat w kolejce platformy Azure, w tym przykładzie, aby zarchiwizować #456 pracownika. Rola procesu roboczego sonduje kolejkę pod kątem nowych komunikatów; gdy go znajdzie, odczytuje komunikat i pozostawia ukrytą kopię w kolejce. Następnie rola proces roboczy pobiera kopię jednostki z **bieżącej** tabeli, wstawia kopię w tabeli **archiwum** , a następnie usuwa oryginalną z **bieżącej** tabeli. Na koniec w przypadku braku błędów z poprzednich kroków rola proces roboczy usuwa ukryty komunikat z kolejki.  

W tym przykładzie krok 4 wstawia pracownika do tabeli **archiwum** . Może dodać pracownika do obiektu BLOB w Blob service lub pliku w systemie plików.  

#### <a name="recovering-from-failures"></a>Odzyskiwanie po awarii
Należy pamiętać, że operacje w krokach **4** i **5** muszą być *idempotentne* w przypadku, gdy rola proces roboczy wymaga ponownego uruchomienia operacji archiwizowania. Jeśli używasz Table service, w kroku **4** należy użyć operacji INSERT lub Replace. w kroku **5** należy użyć operacji "Usuń Jeśli istnieje" w używanej bibliotece klienckiej. W przypadku korzystania z innego systemu magazynu należy użyć odpowiedniej operacji idempotentne.  

Jeśli rola proces roboczy nigdy nie kończy krok **6**, po upływie limitu czasu komunikat pojawi się ponownie w kolejce gotowej do przetworzenia przez rolę procesu roboczego. Rola proces roboczy może sprawdzić, ile razy wiadomość w kolejce została odczytana i w razie potrzeby flagą jest komunikat "Trująca" w celu zbadania przez wysłanie go do oddzielnej kolejki. Aby uzyskać więcej informacji o odczytywaniu komunikatów w kolejce i sprawdzaniu liczby odgałęzień, zobacz [pobieranie komunikatów](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Niektóre błędy usług tabel i kolejek są błędami przejściowymi, a aplikacja kliencka powinna zawierać odpowiednią logikę ponawiania, aby je obsłużyć.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie zapewnia izolacji transakcji. Na przykład klient może odczytać **bieżące** i **archiwalne** tabele, gdy rola proces roboczy przeprowadzono między krokami **4** i **5**, i wyświetlić niespójny widok danych. Dane będą ostatecznie spójne.  
* Musisz się upewnić, że kroki 4 i 5 są idempotentne w celu zapewnienia spójności ostatecznej.  
* Możesz skalować rozwiązanie przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz zagwarantowania ostatecznej spójności między jednostkami, które istnieją w różnych partycjach lub tabelach. Ten wzorzec można rozwinąć, aby zapewnić spójność ostateczną operacji dla Table service i Blob service i innych źródeł danych niezwiązanych z usługą Azure Storage, takich jak baza danych lub system plików.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Scal lub Zamień](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacja transakcji jest ważna dla Twojego rozwiązania, należy rozważyć przeprojektowanie tabel, aby umożliwić korzystanie z EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Zachowaj jednostki indeksu, aby włączyć wydajne wyszukiwania zwracające listy jednostek.  

#### <a name="context-and-problem"></a>Kontekst i problem
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** . Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu zapytania punktowego. Na przykład przy użyciu struktury tabeli pokazanej poniżej, aplikacja kliencka może efektywnie pobrać konkretną jednostkę pracownika przy użyciu nazwy działu i identyfikatora pracownika ( **PartitionKey** i **RowKey**).  

![Jednostka Employee][13]

Jeśli chcesz również uzyskać listę jednostek pracowników na podstawie wartości innej nieunikatowej właściwości, takiej jak jej nazwisko, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowania zamiast używać indeksu do bezpośredniego wyszukiwania. Wynika to z faktu, że usługa Table Service nie udostępnia indeksów pomocniczych.  

#### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska przy użyciu struktury jednostki pokazanej powyżej, należy zachować listy identyfikatorów pracowników. Jeśli chcesz pobrać jednostki pracownika z określoną nazwiskiem, np. Nowak, należy najpierw znaleźć listę identyfikatorów pracowników dla pracowników, którzy mają nazwisko Kowalski, a następnie pobrać te jednostki pracowników. Istnieją trzy główne opcje przechowywania list identyfikatorów pracowników:  

* Użyj magazynu obiektów BLOB.  
* Utwórz jednostki indeksu w tej samej partycji co jednostki pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

<u>Opcja #1: Użyj magazynu obiektów BLOB</u>  

Dla pierwszej opcji utworzysz obiekt BLOB dla każdego unikatowej nazwy, a w każdym z nich znajduje się lista wartości **PartitionKey** (dział) i **RowKey** (identyfikator pracownika) dla pracowników, którzy mają tę ostatnią nazwę. Po dodaniu lub usunięciu pracownika należy upewnić się, że zawartość odpowiedniego obiektu BLOB jest ostatecznie spójna z jednostkami pracowników.  

<u>#2 opcji:</u> Utwórz jednostki indeksu w tej samej partycji  

Dla drugiej opcji Użyj obiektów index, które przechowują następujące dane:  

![Jednostka pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tej samej nazwie][14]

Właściwość **idpracownikas** zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w **RowKey**.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy dodajesz nowego pracownika, jeśli używasz drugiej opcji. W tym przykładzie dodajemy pracownika o identyfikatorze 000152 i nazwisko Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu z wartością **PartitionKey** "Sales" i wartością **RowKey** "Nowak". Zapisz element ETag tej jednostki do użycia w kroku 2.  
2. Utwórz transakcję grupy jednostek (czyli operację wsadową), która wstawia nową jednostkę pracownika (**PartitionKey** wartość "Sales" i **RowKey** Value "000152"), a następnie aktualizuje jednostkę indeksu (wartość**PartitionKey** "Sales" i **RowKey** wartość "Nowak") przez dodanie nowego identyfikatora pracownika do listy w polu idpracownikas. Aby uzyskać więcej informacji o transakcjach grupy jednostek, zobacz [transakcje grupy jednostek](#entity-group-transactions).  
3. Jeśli transakcja grupy jednostek kończy się niepowodzeniem z powodu błędu współbieżności optymistycznej (ktoś inny zmodyfikował jednostkę indeksu), należy ponownie zacząć od początku krok 1.  

Jeśli używasz drugiej opcji, możesz użyć podobnej metody do usuwania pracownika. Zmiana nazwiska pracownika jest nieco bardziej złożona, ponieważ należy wykonać transakcję grupy jednostek, która aktualizuje trzy jednostki: jednostki pracownika, jednostki indeksu dla starej nazwy i jednostki indeksu dla nowej nazwy. Każdą jednostkę należy pobrać przed wprowadzeniem jakichkolwiek zmian w celu pobrania wartości ETag, których można użyć do przeprowadzenia aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy konieczne jest wyszukanie wszystkich pracowników z daną ostatnią nazwą w dziale, jeśli używasz drugiej opcji. W tym przykładzie szukamy wszystkich pracowników o nazwisku Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu z wartością **PartitionKey** "Sales" i wartością **RowKey** "Nowak".  
2. Przeanalizuj listę identyfikatorów pracowników w polu Idpracownikas.  
3. Jeśli potrzebujesz dodatkowych informacji na temat każdego z tych pracowników (takich jak adresy e-mail), Pobierz każdą jednostkę pracownika przy użyciu wartości **PartitionKey** "Sales" i wartości **RowKey** z listy pracowników uzyskanych w kroku 2.  

<u>#3 opcji:</u> Tworzenie jednostek indeksu w oddzielnej partycji lub tabeli  

Dla trzeciej opcji Użyj obiektów index, które przechowują następujące dane:  

![Jednostka pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tej samej nazwie][15]

Właściwość **idpracownikas** zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w **RowKey**.  

Z trzecią opcją nie można używać EGTs, aby zachować spójność, ponieważ jednostki indeksu znajdują się w oddzielnej partycji od jednostek pracowników. Należy upewnić się, że jednostki indeksu są ostatecznie spójne z jednostkami pracowników.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań, aby pobrać pasujące jednostki: jeden do zapytania o jednostki indeksu w celu uzyskania listy wartości **RowKey** , a następnie wysyła zapytanie w celu pobrania każdej jednostki na liście.  
* Mając na względzie, że indywidualna jednostka ma maksymalny rozmiar 1 MB, #2 opcji i #3 opcji w rozwiązaniu założono, że lista identyfikatorów pracowników dla danego nazwiska nie jest nigdy większa niż 1 MB. Jeśli lista identyfikatorów pracowników może mieć rozmiar większy niż 1 MB, użyj opcji #1 i Zapisz dane indeksu w usłudze BLOB Storage.  
* Jeśli używasz opcji #2 (za pomocą EGTs, aby obsługiwać Dodawanie i usuwanie pracowników oraz zmieniać nazwisko pracownika), należy sprawdzić, czy liczba transakcji zbliża się do ograniczeń skalowalności w danej partycji. W takim przypadku należy wziąć pod uwagę ostatecznie spójne rozwiązanie (opcja #1 lub #3 opcji), które korzysta z kolejek do obsługi żądań aktualizacji i umożliwia przechowywanie jednostek indeksu w oddzielnej partycji od jednostek pracowników.  
* Opcja #2 w tym rozwiązaniu założono, że chcesz wyszukać według nazwiska w ramach działu: na przykład chcesz pobrać listę pracowników o nazwisku Kowalski w dziale sprzedaży. Jeśli chcesz mieć możliwość wyszukania wszystkich pracowników o nazwisku Nowak w całej organizacji, użyj opcji #1 lub #3 opcji.
* Możesz zaimplementować rozwiązanie oparte na kolejce, które zapewnia spójność ostateczną (Aby uzyskać więcej informacji, zobacz [wzorzec spójnych transakcji](#eventually-consistent-transactions-pattern) ).  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz wyszukać zestaw jednostek, które współużytkują wspólną wartość właściwości, takich jak wszyscy pracownicy o nazwisku Nowak.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Wzorzec denormalizacji
Połącz powiązane dane razem w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania pojedynczego punktu.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizuje dane w celu usunięcia duplikatów, co powoduje, że zapytania pobierające dane z wielu tabel. Jeśli normalizuje dane w tabelach platformy Azure, musisz wykonać wiele rund z klienta do serwera, aby pobrać powiązane dane. Na przykład w przypadku struktury tabeli pokazanej poniżej potrzebne są dwie rundy, aby pobrać szczegóły działu: jeden do pobrania jednostki działu, która zawiera identyfikator Menedżera, a następnie inne żądanie pobrania szczegółów kierownika w jednostce pracownika.  

![Jednostka działu i jednostka Employee][16]

#### <a name="solution"></a>Rozwiązanie
Zamiast przechowywania danych w dwóch osobnych jednostkach, należy deznormalizować dane i zachować kopię szczegółów kierownika w jednostce działu. Na przykład:  

![Nieznormalizowana i połączona jednostka działu][17]

Za pomocą jednostek działu przechowywanych z tymi właściwościami można teraz pobrać wszystkie szczegóły potrzebne do działu przy użyciu zapytania punkt.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje kilka kosztów związanych z magazynowaniem danych. Korzyść wydajności (wynikająca z mniejszej liczby żądań do usługi magazynu) zazwyczaj zmniejsza krańcowy wzrost kosztów magazynowania (a koszt jest częściowo przesunięty o zmniejszenie liczby transakcji wymaganych do pobrania szczegółów działu) ).  
* Należy zachować spójność dwóch jednostek, które przechowują informacje o menedżerach. Problem ze spójnością można obsłużyć, korzystając z EGTs do aktualizowania wielu jednostek w jednej transakcji niepodzielnej: w tym przypadku jednostka działu i Jednostka pracownika dla menedżera działu są przechowywane w tej samej partycji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy często potrzebujesz wyszukać powiązane informacje. Ten wzorzec zmniejsza liczbę zapytań, które klient musi wykonać, aby pobrać wymagane dane.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Wzorzec klucza złożonego
Użyj złożonych wartości **RowKey** , aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą zapytania z pojedynczym punktem.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych można używać sprzężeń w zapytaniach, aby zwrócić powiązane fragmenty danych do klienta w jednym zapytaniu. Na przykład możesz użyć identyfikatora pracownika, aby wyszukać listę powiązanych jednostek, które zawierają dane dotyczące wydajności i przeglądu dla tego pracownika.  

Załóżmy, że w Table service są przechowywane jednostki pracowników, korzystając z następującej struktury:  

![Jednostka Employee][18]

Musisz również przechowywać dane historyczne dotyczące przeglądów i wydajności dla każdego roku, dla których pracownik pracował w organizacji, i musisz mieć dostęp do tych informacji według roku. Jedną z opcji jest utworzenie innej tabeli, która przechowuje jednostki o następującej strukturze:  

![Jednostka przeglądu pracownika][19]

Należy zauważyć, że w tym podejściu można zdecydować się na zduplikowanie niektórych informacji (takich jak imię i nazwisko) w nowej jednostce, aby umożliwić pobieranie danych za pomocą pojedynczego żądania. Nie można jednak zachować silnej spójności, ponieważ nie można użyć EGT do aktualizowania dwóch jednostek.  

#### <a name="solution"></a>Rozwiązanie
Zapisz nowy typ jednostki w oryginalnej tabeli przy użyciu jednostek o następującej strukturze:  

![Jednostka pracownika z kluczem złożonym][20]

Zwróć uwagę na to, jak **RowKey** jest teraz kluczem złożonym składającym się z identyfikatora pracownika i roku danych przeglądu, który umożliwia pobieranie wydajności pracownika i przeglądanie danych za pomocą pojedynczego żądania dla jednej jednostki.  

Poniższy przykład przedstawia, w jaki sposób można pobrać wszystkie dane dotyczące przeglądu określonego pracownika (na przykład Employee 000123 w dziale sprzedaży):  

$filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000123") i (RowKey lt "empid_000124") & $select = RowKey, klasyfikacja Menedżera, klasyfikacja elementu równorzędnego, Komentarze  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego znaku separatora, który ułatwia przeanalizowanie wartości **RowKey** : na przykład **000123_2012**.  
* Ta jednostka jest również przechowywana w tej samej partycji co inne jednostki, które zawierają powiązane dane dla tego samego pracownika, co oznacza, że można użyć EGTs do utrzymania silnej spójności.
* Należy zastanowić się, jak często będą wykonywane zapytania o dane, aby określić, czy ten wzorzec jest odpowiedni.  Na przykład, jeśli będziesz uzyskiwać dostęp do danych przeglądu rzadko, a główne dane pracowników często powinny być przechowywane jako osobne jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Tego wzorca należy używać, gdy konieczne jest przechowywanie co najmniej jednej powiązanej jednostki.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Wzorzec końca dziennika
Pobierz *n* obiektów ostatnio dodanych do partycji przy użyciu wartości **RowKey** , która jest sortowana w odwrotnej kolejności daty i godziny.  

> [!NOTE]
> Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. W ten sposób ten wzorzec jest odpowiedni dla Table Storage platformy Azure, a nie Azure Cosmos DB. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Table Storage platformy Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymaganiem jest możliwość pobrania ostatnio utworzonych jednostek, na przykład dziesięciu ostatnich oświadczeń wydatków przesłanych przez pracownika. Zapytania tabeli obsługują **$Top** operacji zapytania, aby zwrócić pierwsze *n* jednostek z zestawu: nie istnieje równoważna operacja zapytania, która nie zwraca ostatnich n jednostek w zestawie.  

#### <a name="solution"></a>Rozwiązanie
Przechowuj jednostki przy użyciu **RowKey** , który w sposób naturalny sortuje w odwrotnej kolejności daty/godziny przy użyciu, tak aby najnowszy wpis był zawsze pierwszym z nich w tabeli.  

Na przykład, aby można było pobrać dziesięć najnowszych oświadczeń wydatków przedstawionych przez pracownika, można użyć wartości odwróconej osi pochodnej od bieżącej daty/godziny. Poniższy C# przykład kodu przedstawia jeden ze sposobów tworzenia odpowiednich "odwróconych" znaczników dla **RowKey** , które są sortowane od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Można wrócić do wartości daty i godziny przy użyciu następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zapewnić, że wartość ciągu będzie sortowana zgodnie z oczekiwaniami, należy uzupełnić wartość odwrotnej osi.  
* Należy pamiętać o obiektach docelowych skalowalności na poziomie partycji. Należy zachować ostrożność, aby nie tworzyć partycji punktu aktywnego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz uzyskać dostęp do jednostek w odwrotnej kolejności daty/godziny lub gdy potrzebujesz dostępu do ostatnio dodanych jednostek.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Poprzedź/Dołącz Antywzorzec](#prepend-append-anti-pattern)  
* [Pobieranie jednostek](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Wzorzec usuwania dużych ilości
Włącz usuwanie dużej liczby jednostek przez przechowywanie wszystkich jednostek do jednoczesnego usuwania w ich własnej osobnej tabeli; obiekty można usunąć, usuwając tabelę.  

#### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji usuwa stare dane, które nie muszą już być dostępne dla aplikacji klienckiej, lub że aplikacja została zarchiwizowana na innym nośniku magazynowania. Dane te są zwykle identyfikowane przez datę: na przykład, istnieje wymóg usuwania rekordów wszystkich żądań logowania, które są starsze niż 60 dni.  

Jednym z możliwych wzorów jest użycie daty i godziny żądania logowania w **RowKey**:  

![Jednostka próby zalogowania][21]

To podejście pozwala uniknąć hotspotów partycji, ponieważ aplikacja może wstawiać i usuwać jednostki logowania dla każdego użytkownika w oddzielnej partycji. Jednak takie podejście może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek, ponieważ najpierw należy przeprowadzić skanowanie tabeli w celu zidentyfikowania wszystkich jednostek do usunięcia, a następnie usunąć każdą starą jednostkę. Można zmniejszyć liczbę operacji okrężnych do serwera wymaganego do usunięcia starych jednostek przez przetwarzanie wsadowe wielu żądań Delete do EGTs.  

#### <a name="solution"></a>Rozwiązanie
Użyj oddzielnej tabeli dla każdego dnia prób logowania. Możesz użyć powyższego projektu jednostki, aby uniknąć hotspotów podczas wstawiania jednostek, a usunięcie starych jednostek jest teraz już pytaniem o usunięcie jednej tabeli codziennie (pojedynczej operacji magazynu) zamiast znajdowania i usuwania setek i tysięcy pojedynczych Każdy dzień w jednostkach.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy projekt obsługuje inne sposoby używania danych przez aplikację, takich jak wyszukiwanie konkretnych jednostek, łączenie z innymi danymi lub generowanie zagregowanych informacji?  
* Czy projekt nie pozwala na aktywne plamy podczas wstawiania nowych jednostek?  
* Należy spodziewać się opóźnienia, jeśli chcesz użyć tej samej nazwy tabeli po jej usunięciu. Lepiej jest zawsze używać unikatowych nazw tabel.  
* Należy spodziewać się pewnej szybkości ograniczania przy pierwszym użyciu nowej tabeli, podczas gdy Table service nauczy wzorców dostępu i dystrybuuje partycje między węzłami. Należy zastanowić się, jak często należy utworzyć nowe tabele.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Tego wzorca należy używać w przypadku dużej ilości jednostek, które należy usunąć w tym samym czasie.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Modyfikowanie jednostek](#modifying-entities)  

### <a name="data-series-pattern"></a>Wzorzec serii danych
Przechowuj kompletne serie danych w jednej jednostce, aby zminimalizować liczbę żądań, które należy wykonać.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz polega na tym, że aplikacja przechowuje serię danych, które zazwyczaj muszą zostać pobrane jednocześnie. Na przykład aplikacja może rejestrować liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika co godzinę, a następnie użyć tych informacji do wykreślania liczby komunikatów wysyłanych przez każdego użytkownika w ciągu ostatnich 24 godzin. Jednym z projektów może być przechowywanie 24 jednostek dla każdego pracownika:  

![Jednostka statystyk komunikatów][22]

Dzięki temu projektowi można łatwo zlokalizować i zaktualizować jednostkę do aktualizacji dla każdego pracownika, gdy aplikacja musi zaktualizować wartość liczby komunikatów. Aby jednak pobrać informacje w celu wykreślenia wykresu aktywności przez poprzednie 24 godziny, należy pobrać 24 jednostki.  

#### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z osobną właściwością do przechowywania liczby komunikatów dla każdej godziny:  

![Jednostka statystyk komunikatów z oddzielonymi właściwościami][23]

W tym projekcie możesz użyć operacji scalania, aby zaktualizować liczbę komunikatów dla pracownika przez określoną godzinę. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli kompletna seria danych nie mieści się w pojedynczej jednostce (jednostka może mieć do 252 właściwości), użyj alternatywnego magazynu danych, takiego jak obiekt BLOB.  
* Jeśli wielu klientów jednocześnie aktualizuje jednostkę, należy użyć elementu **ETag** do zaimplementowania optymistycznej współbieżności. Jeśli masz wielu klientów, może wystąpić wysoka rywalizacja.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz zaktualizować i pobrać serie danych skojarzone z konkretną jednostką.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scal lub Zamień](#merge-or-replace)  
* [Wzorzec z ostatecznie spójnymi transakcjami](#eventually-consistent-transactions-pattern) (Jeśli przechowujesz serie danych w obiekcie BLOB)  

### <a name="wide-entities-pattern"></a>Wzorzec szerokiej jednostki
Użyj wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka może mieć nie więcej niż 252 właściwości (z wyłączeniem obowiązkowych właściwości systemu) i nie może zawierać więcej niż 1 MB danych. W relacyjnej bazie danych zwykle uzyskuje się zaokrąglanie wszelkich limitów rozmiaru wiersza przez dodanie nowej tabeli i wymuszenie relacji 1-do-jednego między nimi.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą Table service można przechowywać wiele jednostek do reprezentowania pojedynczego dużego obiektu biznesowego, który ma więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika przez ostatnie 365 dni, możesz użyć następującego projektu, który używa dwóch jednostek z różnymi schematami:  

![Jednostka statystyki komunikatu z Rowkey 01 i jednostką stanu komunikatu z Rowkey 02][24]

Jeśli musisz wprowadzić zmianę, która wymaga aktualizacji obu jednostek, aby zachować ich synchronizację ze sobą, możesz użyć EGT. W przeciwnym razie można użyć pojedynczej operacji scalania, aby zaktualizować liczbę komunikatów w określonym dniu. Aby pobrać wszystkie dane dla danego pracownika, należy pobrać obie jednostki, które można wykonać przy użyciu dwóch wydajnych żądań, które używają zarówno **PartitionKey** , jak i wartości **RowKey** .  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Pobieranie kompletnej jednostki logicznej obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania każdej jednostki fizycznej.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz przechowywać jednostki, których rozmiar lub liczba właściwości przekraczają limity dla poszczególnych jednostek w Table service.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Scal lub Zamień](#merge-or-replace)

### <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Przechowywanie dużych wartości właściwości przy użyciu magazynu obiektów BLOB.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka nie może przechowywać więcej niż 1 MB danych. Jeśli jedna lub kilka właściwości magazynu wartości, które powodują, że całkowity rozmiar jednostki przekracza tę wartość, nie można zapisać całej jednostki w Table service.  

#### <a name="solution"></a>Rozwiązanie
Jeśli rozmiar jednostki przekracza 1 MB, ponieważ co najmniej jedna z właściwości zawiera dużą ilość danych, można przechowywać dane w Blob service a następnie przechowywać adres obiektu BLOB we właściwości w jednostce. Na przykład można przechowywać zdjęcie pracownika w usłudze BLOB Storage i przechowywać link do zdjęcia we właściwości **Fotografia** jednostki pracownika:  

![Jednostka pracownika z ciągiem dla fotografii wskazujących na magazyn obiektów BLOB][25]

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczną między jednostką w Table service i danymi w Blob service, użyj [wzorca stałego spójnych transakcji](#eventually-consistent-transactions-pattern) do obsługi jednostek.
* Pobieranie kompletnej jednostki obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania jednostki i jeden w celu pobrania danych obiektów BLOB.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz przechowywać jednostki, których rozmiar przekracza limity dla pojedynczej jednostki w Table service.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Poprzedź/Dołącz Antywzorzec
Zwiększenie skalowalności w przypadku dużej ilości operacji wstawiania poprzez rozłożenie operacji wstawiania na wiele partycji.  

#### <a name="context-and-problem"></a>Kontekst i problem
Oczekiwanie na lub dołączenie jednostek do przechowywanych jednostek zwykle powoduje, że aplikacja dodaje nowe jednostki do pierwszej lub ostatniej partycji sekwencji partycji. W takim przypadku wszystkie operacje wstawiania w dowolnym momencie odbywają się w tej samej partycji, co powoduje utworzenie hotspotu, który uniemożliwia użycie usługi tabeli w ramach równoważenia obciążenia w wielu węzłach, i może spowodować, że aplikacja osiągnie elementy docelowe skalowalności dla podzielić. Na przykład jeśli masz aplikację, która rejestruje dostęp do sieci i zasobów przez pracowników, wówczas struktura jednostki, jak pokazano poniżej, może spowodować, że partycja bieżącej godziny staje się punktem aktywny, jeśli ilość transakcji osiągnie miejsce docelowe skalowalności dla pojedyncza partycja:  

![Jednostka Employee][26]

#### <a name="solution"></a>Rozwiązanie
Następująca alternatywna Struktura jednostek pozwala uniknąć hotspotu na określonej partycji, gdy aplikacja rejestruje zdarzenia:  

![Jednostka pracownika z RowKeyą, która składa się z roku, miesiąca, dnia, godziny i identyfikatora zdarzenia][27]

Zwróć uwagę, że w tym przykładzie zarówno **PartitionKey** , jak i **RowKey** są kluczami złożonymi. **PartitionKey** używa zarówno identyfikatora działu, jak i pracownika do dystrybuowania rejestrowania między wieloma partycjami.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy struktura klucza alternatywnego, która pozwala uniknąć tworzenia aktywnych partycji w przypadku wstawiania efektywnie obsługuje zapytania, które udostępnia aplikacja kliencka?  
* Czy Przewidywana ilość transakcji oznacza, że najkorzystniej osiągniesz cele skalowalności dla danej partycji i zostanie ona ograniczona przez usługę magazynu?  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać dodawania lub dołączania antywzorców, gdy ilość transakcji prawdopodobnie spowoduje ograniczenie szybkości przez usługę magazynu podczas uzyskiwania dostępu do partycji aktywnej.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec końca dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Antywzorzec danych dziennika
Zazwyczaj należy używać Blob service zamiast Table service do przechowywania danych dziennika.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym przypadkiem użycia danych dziennika jest pobranie wybranych wpisów dziennika dla określonego zakresu dat/godzin: na przykład, chcesz znaleźć wszystkie komunikaty o błędach i krytyczne, które zostały zarejestrowane przez aplikację między 15:04 i 15:06 w określonym dniu. Nie chcesz używać daty i godziny komunikatu dziennika w celu określenia partycji, do której zapisywane są jednostki dziennika: spowoduje to, że w danym momencie wszystkie jednostki dziennika będą współużytkować tę samą wartość **PartitionKey** (patrz poprzednia sekcja [/ Dołącz ciąg antywzorców](#prepend-append-anti-pattern)). Na przykład poniższy schemat jednostki dla komunikatu dziennika tworzy partycję gorącą, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika na partycji dla bieżącej daty i godziny:  

![Jednostka komunikatu dziennika][28]

W tym przykładzie **RowKey** obejmuje datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są przechowywane w kolejności daty/godziny i zawiera identyfikator komunikatu w przypadku, gdy wiele komunikatów dziennika ma taką samą datę i godzinę.  

Innym podejściem jest użycie **PartitionKey** , która zapewnia, że aplikacja zapisuje komunikaty w różnych partycjach. Na przykład, jeśli źródło komunikatu dziennika zapewnia sposób dystrybucji komunikatów w wielu partycjach, można użyć następującego schematu jednostki:  

![Jednostka komunikatu dziennika][29]

Jednak problem z tym schematem polega na tym, że pobranie wszystkich komunikatów dziennika dla określonego przedziału czasu wymaga przeszukania każdej partycji w tabeli.

#### <a name="solution"></a>Rozwiązanie
W poprzedniej sekcji został wyróżniony problem polegający na tym, że próba użycia Table service do przechowywania wpisów dziennika i sugerowanych dwóch, niezadowalających projektów. Jedno rozwiązanie prowadzi do gorącej partycji z ryzykiem, w którym są zapisywane komunikaty dziennika zapisu wydajności. inne rozwiązanie spowodowało niską wydajność zapytań, ponieważ wymaga skanowania każdej partycji w tabeli w celu pobrania komunikatów dziennika dla określonego przedziału czasu. Magazyn obiektów BLOB oferuje lepsze rozwiązanie tego typu, dlatego w analityka magazynu platformy Azure są przechowywane zbierane dane dziennika.  

W tej sekcji opisano sposób, w jaki analityka magazynu przechowuje dane dziennika w usłudze BLOB Storage jako ilustrację tego podejścia do przechowywania danych, które są zwykle wykonywane przez wiele zapytań.  

Analityka magazynu przechowuje komunikaty dzienników w formacie rozdzielanym w wielu obiektach Blob. Format rozdzielany ułatwia aplikacji klienckiej analizowanie danych w komunikacie dziennika.  

Analityka magazynu używa konwencji nazewnictwa obiektów blob, które umożliwiają znalezienie obiektów BLOB (lub obiektów BLOB) zawierających komunikaty dziennika, dla których odbywa się wyszukiwanie. Na przykład obiekt BLOB o nazwie "queue/2014/07/31/1800/000001. log" zawiera komunikaty dziennika, które odnoszą się do usługi kolejki przez godzinę, rozpoczynając od 18:00 31 lipca 2014. "000001" wskazuje, że jest to pierwszy plik dziennika dla tego okresu. Analityka magazynu rejestruje także sygnatury czasowe pierwszych i ostatnich komunikatów dziennika przechowywanych w pliku w ramach metadanych obiektu BLOB. Interfejs API usługi BLOB Storage umożliwia lokalizowanie obiektów BLOB w kontenerze na podstawie prefiksu nazwy: aby zlokalizować wszystkie obiekty blob zawierające dane dziennika kolejki dla godziny rozpoczynającej się o 18:00, można użyć prefiksu "queue/2014/07/31/1800".  

Analityka magazynu buforuje komunikaty dzienników wewnętrznie, a następnie okresowo aktualizuje odpowiedni obiekt BLOB lub tworzy nowy z najnowszymi partiami wpisów dziennika. Pozwala to zmniejszyć liczbę zapisów, które muszą zostać wykonane w usłudze BLOB Service.  

Jeśli wdrażasz podobne rozwiązanie w swojej aplikacji, musisz wziąć pod uwagę sposób zarządzania wymianą zapasową między niezawodnością (zapisanie każdego wpisu dziennika do magazynu obiektów BLOB) i obniżeniem kosztów i skalowalności (buforowanie aktualizacji w aplikacji i zapisywanie je do magazynu obiektów BLOB w partiach).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas decydowania o sposobie przechowywania danych dzienników należy wziąć pod uwagę następujące kwestie:  

* W przypadku utworzenia projektu tabeli, który pozwala uniknąć potencjalnych partycji, może się okazać, że nie można wydajnie uzyskać dostępu do danych dziennika.  
* Aby przetwarzać dane dziennika, klient często musi załadować wiele rekordów.  
* Mimo że dane dziennika są często strukturalne, usługa BLOB Storage może być lepszym rozwiązaniem.  

### <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, które należy wziąć pod uwagę podczas implementowania wzorców opisanych w poprzednich sekcjach. W większości tej sekcji są używane przykłady napisane C# przy użyciu biblioteki klienta magazynu (wersja 4.3.0 w momencie pisania).  

### <a name="retrieving-entities"></a>Pobieranie jednostek
Jak opisano w temacie [projekt sekcji dotyczącej wykonywania zapytań](#design-for-querying), najbardziej wydajnym zapytaniem jest zapytanie punktowe. Jednak w niektórych scenariuszach może być konieczne pobranie wielu jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek przy użyciu biblioteki klienta usługi Storage.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Wykonywanie zapytania punktu przy użyciu biblioteki klienta usługi Storage
Najprostszym sposobem wykonania zapytania punktu jest użycie operacji **Pobierz** tabelę, jak pokazano w poniższym C# fragmencie kodu, który pobiera jednostkę z **PartitionKey** wartości "Sales" i **RowKey** wartość "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekiwana jest jednostka pobierana z typu **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Pobieranie wielu jednostek przy użyciu LINQ
Można pobrać wiele jednostek przy użyciu LINQ z biblioteką klienta usługi Storage i określić zapytanie z klauzulą **WHERE** . Aby uniknąć skanowania tabeli, należy zawsze uwzględnić wartość **PartitionKey** w klauzuli WHERE, a jeśli to możliwe, wartość **RowKey** w celu uniknięcia skanowania tabeli i partycji. Usługa Table Service obsługuje ograniczony zbiór operatorów porównania (większe niż, większe niż lub równe, mniejsze niż, mniejsze niż lub równe, równe i nierówne) do użycia w klauzuli WHERE. Poniższy C# fragment kodu znajduje wszystkich pracowników, których nazwisko zaczyna się od "B" (przy założeniu, że **RowKey** przechowuje nazwisko) w dziale sprzedaży (przy założeniu, że **PartitionKey** przechowuje nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Zwróć uwagę, jak zapytanie określa zarówno **RowKey** , jak i **PartitionKey** , aby zapewnić lepszą wydajność.  

Poniższy przykładowy kod przedstawia równoważne funkcje przy użyciu interfejsu API Fluent (Aby uzyskać więcej informacji na temat interfejsów API Fluent ogólnie, zobacz [najlepsze rozwiązania dotyczące projektowania interfejsu API Fluent](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Przykład zagnieżdża wiele metod **CombineFilters** , aby uwzględnić trzy warunki filtru.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Pobieranie dużej liczby jednostek z zapytania
Optymalne zapytanie zwraca indywidualną jednostkę na podstawie wartości **PartitionKey** i wartości **RowKey** . Jednak w niektórych scenariuszach może istnieć wymóg zwrócenia wielu jednostek z tej samej partycji lub nawet z wielu partycji.  

Należy zawsze w pełni przetestować wydajność aplikacji w takich scenariuszach.  

Zapytanie dotyczące usługi Table Service może zwrócić maksymalnie 1 000 jednostek jednocześnie i może być wykonywane przez maksymalnie pięć sekund. Jeśli zestaw wyników zawiera więcej niż 1 000 jednostek, jeśli zapytanie nie zostało ukończone w ciągu pięciu sekund lub jeśli zapytanie przecina granicę partycji, Table service zwraca token kontynuacji, aby umożliwić aplikacji klienckiej żądanie następnego zestawu jednostek. Aby uzyskać więcej informacji na temat działania tokenów kontynuacji, zobacz [limit czasu zapytania i podział na strony](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Jeśli używasz biblioteki klienta usługi Storage, może ona automatycznie obsługiwać tokeny kontynuacji, ponieważ zwraca jednostki z Table service. Następujący C# przykładowy kod korzystający z biblioteki klienta usługi Storage automatycznie obsługuje tokeny kontynuacji, jeśli usługa tabeli zwróci je w odpowiedzi:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Poniższy C# kod obsługuje jawnie tokeny kontynuacji:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Używając jawnie tokenów kontynuacji, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład, jeśli aplikacja kliencka umożliwia użytkownikom przechodzenie przez jednostki przechowywane w tabeli, użytkownik może zrezygnować ze strony przez wszystkie jednostki pobrane przez zapytanie, tak aby aplikacja korzystała z tokenu kontynuacji do pobierania następnego segmentu, gdy użytkownik zakończył stronicowanie przez wszystkie jednostki w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Umożliwia ograniczenie ilości danych do pobrania z Table service i przechodzenie przez sieć.  
* Umożliwia wykonywanie asynchronicznych operacji we/wy na platformie .NET.  
* Umożliwia ona Serializowanie tokenu kontynuacji do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zwykle zwraca segment zawierający 1 000 jednostek, chociaż może być mniej. Ma to również wpływ na liczbę wpisów zwracanych przez zapytanie przy użyciu funkcji **Take** , aby zwrócić pierwsze n jednostek, które pasują do kryteriów wyszukiwania: usługa Table może zwrócić segment zawierający mniej niż n jednostek wraz z tokenem kontynuacji, aby umożliwia pobranie pozostałych jednostek.  
> 
> 

Poniższy C# kod pokazuje, jak zmodyfikować liczbę jednostek zwróconych wewnątrz segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekcja po stronie serwera
Jedna jednostka może mieć do 255 właściwości i mieć rozmiar maksymalnie 1 MB. Podczas wykonywania zapytania dotyczącego tabeli i pobierania jednostek mogą nie być potrzebne wszystkie właściwości i mogą uniknąć niepotrzebnych transferów danych (aby pomóc w zmniejszeniu opóźnień i kosztów). Możesz użyć projekcji po stronie serwera, aby przetransferować tylko potrzebne właściwości. Poniższy przykład pobiera tylko właściwość **poczty e-mail** (wraz z **PartitionKey**, **RowKey**, **timestamp**i **ETag**) z jednostek wybranych przez zapytanie.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Zauważ, że wartość **RowKey** jest dostępna, nawet jeśli nie została uwzględniona na liście właściwości do pobrania.  

### <a name="modifying-entities"></a>Modyfikowanie jednostek
Biblioteka klienta usługi Storage umożliwia modyfikowanie jednostek przechowywanych w usłudze Table Service przez wstawianie, usuwanie i aktualizowanie jednostek. Za pomocą EGTs można wsadowo wiele operacji wstawiania, aktualizacji i usuwania, aby zmniejszyć liczbę wymaganych podróży i zwiększyć wydajność rozwiązania.  

Wyjątki zgłaszane, gdy biblioteka klienta magazynu wykonuje EGT zazwyczaj zawiera indeks jednostki, która spowodowała niepowodzenie wykonywania partii. Jest to przydatne w przypadku debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projekt ma wpływ na sposób, w jaki Twoja aplikacja kliencka obsługuje operacje współbieżności i aktualizacji.  

#### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa Table Service implementuje optymistyczne kontrole współbieżności na poziomie poszczególnych jednostek dla operacji **wstawiania**, **scalania**i **usuwania** , chociaż jest możliwe, aby klient wymusił ominięcie tych elementów przez usługę Table Service szuka. Aby uzyskać więcej informacji o tym, jak usługa Table zarządza współbieżnością, zobacz [Zarządzanie współbieżnością w Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Scal lub Zamień
Metoda **replace** klasy **TableOperation** zawsze zastępuje kompletną jednostkę w Table Service. Jeśli nie dołączysz właściwości w żądaniu, gdy ta właściwość istnieje w przechowywanej jednostce, żądanie usunie tę właściwość z przechowywanej jednostki. Jeśli nie chcesz usunąć właściwości jawnie z przechowywanej jednostki, musisz dołączyć każdą właściwość w żądaniu.  

Można użyć metody **merge** klasy **TableOperation** , aby zmniejszyć ilość danych wysyłanych do Table Service, gdy chcesz zaktualizować jednostkę. Metoda **merge** zastępuje wszystkie właściwości w przechowywanej jednostce wartościami właściwości z jednostki zawartej w żądaniu, ale pozostawia nienaruszone właściwości w jednostce przechowywanej, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz duże jednostki i potrzebujesz jedynie zaktualizować niewielką liczbę właściwości w żądaniu.  

> [!NOTE]
> Metody **replace** i **merge** kończą się niepowodzeniem, jeśli jednostka nie istnieje. Alternatywnie można użyć metod **InsertOrReplace** i **InsertOrMerge** , które tworzą nową jednostkę, jeśli nie istnieje.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Praca z heterogenicznymi typami jednostek
Table service to magazyn tabel bez *schematu* , który oznacza, że pojedyncza tabela może przechowywać jednostki wielu typów zapewniające dużą elastyczność w projekcie. Poniższy przykład ilustruje tabelę przechowującą jednostki pracownika i działu:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Znacznik czasu</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Każda jednostka musi nadal mieć wartości **PartitionKey**, **RowKey**i **timestamp** , ale może mieć dowolny zestaw właściwości. Ponadto nie ma nic do wskazania typu jednostki, chyba że użytkownik zdecyduje się przechowywać te informacje w innym miejscu. Dostępne są dwie opcje identyfikacji typu jednostki:  

* Dołącz typ jednostki do **RowKey** (lub prawdopodobnie **PartitionKey**). Na przykład **EMPLOYEE_000123** lub **DEPARTMENT_SALES** jako wartości **RowKey** .  
* Użyj oddzielnej właściwości, aby zarejestrować typ jednostki, jak pokazano w poniższej tabeli.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Znacznik czasu</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Elementy</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Zatrudnion</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Elementy</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Zatrudnion</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Elementy</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Dział</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Elementy</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Zatrudnion</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Pierwsza opcja, w zależności od typu obiektu do **RowKey**, jest przydatna, jeśli istnieje możliwość, że dwie jednostki różnych typów mogą mieć taką samą wartość klucza. Grupuje również jednostki tego samego typu razem w partycji.  

Techniki omówione w tej sekcji są szczególnie istotne dla [relacji dziedziczenia](#inheritance-relationships) dyskusji wcześniej w tym przewodniku w sekcji relacje modelowania.  

> [!NOTE]
> Należy rozważyć uwzględnienie numeru wersji w wartości typ jednostki, aby umożliwić aplikacjom klienckim rozwijanie obiektów POCO i współpracują z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre funkcje w bibliotece klienta magazynu, które ułatwiają pracę z wieloma typami jednostek w tej samej tabeli.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Pobieranie typów jednostek heterogenicznych
W przypadku korzystania z biblioteki klienta usługi Storage dostępne są trzy opcje pracy z wieloma typami jednostek.  

Jeśli znasz typ jednostki przechowywanej przy użyciu określonych wartości **RowKey** i **PartitionKey** , możesz określić typ jednostki podczas pobierania jednostki, jak pokazano w poprzednich dwóch przykładach, które pobierają jednostki typu **EmployeeEntity** : [Wykonywanie zapytania punktowego przy użyciu biblioteki klienta magazynu](#executing-a-point-query-using-the-storage-client-library) i [pobieranie wielu jednostek przy użyciu LINQ](#retrieving-multiple-entities-using-linq).  

Druga opcja to użycie typu **dynamictableentity lub** (zbiór właściwości) zamiast konkretnego typu jednostki poco (Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby serializacji i deserializacji jednostki do typów .NET). Poniższy C# kod potencjalnie pobiera wiele jednostek różnych typów z tabeli, ale zwraca wszystkie jednostki jako wystąpienia **dynamictableentity lub** . Następnie używa właściwości **EntityType** do określenia typu każdej jednostki:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Aby pobrać inne właściwości, należy użyć metody **TryGetValue** we właściwości **Właściwości** klasy **dynamictableentity lub** .  

Trzecia opcja polega na połączeniu przy użyciu typu **dynamictableentity lub** i wystąpienia **EntityResolver** . Pozwala to na rozpoznanie wielu typów POCO w tym samym zapytaniu. W tym przykładzie delegat **EntityResolver** używa właściwości **EntityType** do rozróżnienia między dwoma typami jednostek, które zwraca zapytanie. Metoda **Rozwiązuj** używa delegata programu **rozpoznawania nazw** do rozwiązywania wystąpień **dynamictableentity lub** z wystąpieniami **klasy tableentity** .  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Modyfikowanie typów jednostek heterogenicznych
Nie musisz znać typu jednostki, aby ją usunąć, i zawsze znasz typ jednostki podczas jej wstawiania. Można jednak użyć typu **dynamictableentity lub** do zaktualizowania jednostki bez znajomości jej typu i bez użycia klasy jednostki poco. Poniższy przykład kodu pobiera pojedynczą jednostkę i sprawdza, czy właściwość **EmployeeCount** istnieje przed jej aktualizacją.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Kontrolowanie dostępu za pomocą sygnatur dostępu współdzielonego
Można użyć tokenów sygnatury dostępu współdzielonego (SAS), aby umożliwić aplikacjom klienckim modyfikowanie (i zapytanie) jednostek tabeli bezpośrednio bez konieczności uwierzytelniania bezpośrednio w usłudze Table Service. Zazwyczaj istnieją trzy główne korzyści związane z używaniem sygnatury dostępu współdzielonego w aplikacji:  

* Nie jest konieczne dystrybuowanie klucza konta magazynu do niezabezpieczonej platformy (na przykład urządzenia przenośnego) w celu umożliwienia temu urządzeniu dostępu do jednostek w Table service i modyfikowania ich.  
* Można odciążyć część pracy wykonywanej przez role sieci Web i proces roboczy w celu zarządzania jednostkami na urządzeniach klienckich, takich jak komputery użytkowników końcowych i urządzenia przenośne.  
* Do klienta można przypisać ograniczony i bezterminowy zestaw uprawnień (na przykład Zezwalanie na dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów SAS z Table service, zobacz [using Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Należy jednak nadal generować tokeny sygnatury dostępu współdzielonego, które przydzielą aplikację kliencką jednostkom w usłudze Table Service: Zrób to w środowisku, które ma bezpieczny dostęp do kluczy konta magazynu. Zwykle w celu wygenerowania tokenów SAS i dostarczenia ich do aplikacji klienckich, które wymagają dostępu do jednostek, należy użyć roli sieci Web lub procesu roboczego. Ze względu na to, że nadal występują obciążenia związane z generowaniem i dostarczaniem tokenów SAS klientom, należy wziąć pod uwagę, jak najlepiej zredukować ten koszt, szczególnie w scenariuszach o dużej pojemności.  

Istnieje możliwość wygenerowania tokenu SAS, który udziela dostępu do podzbioru jednostek w tabeli. Domyślnie można utworzyć token sygnatury dostępu współdzielonego dla całej tabeli, ale można również określić, że token SAS udziela dostępu do zakresu wartości **PartitionKey** lub zakresu wartości **PartitionKey** i **RowKey** . Można generować tokeny sygnatury dostępu współdzielonego dla poszczególnych użytkowników systemu w taki sposób, aby token SAS każdego użytkownika zezwalał na dostęp do ich własnych jednostek w usłudze Table Service.  

### <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Pod warunkiem, że są rozłożone żądania na wielu partycjach, można zwiększyć przepływność i czas odpowiedzi klienta przy użyciu zapytań asynchronicznych lub równoległych.
Na przykład może istnieć co najmniej dwa wystąpienia roli procesu roboczego, które uzyskują dostęp do tabel równolegle. Mogą istnieć poszczególne role procesów roboczych odpowiedzialne za określone zestawy partycji lub po prostu ma wiele wystąpień roli procesu roboczego, z których każdy może uzyskać dostęp do wszystkich partycji w tabeli.  

W ramach wystąpienia klienta można zwiększyć przepływność przez wykonywanie operacji magazynu asynchronicznie. Biblioteka klienta magazynu ułatwia pisanie asynchronicznych zapytań i modyfikacji. Na przykład możesz zacząć od metody synchronicznej, która pobiera wszystkie jednostki w partycji, jak pokazano w poniższym C# kodzie:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Możesz łatwo zmodyfikować ten kod, aby zapytanie zostało uruchomione asynchronicznie w następujący sposób:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

W tym przykładzie asynchronicznym można zobaczyć następujące zmiany w wersji synchronicznej:  

* Sygnatura metody obejmuje teraz modyfikator **Async** i zwraca wystąpienie **zadania** .  
* Zamiast wywoływania metody **ExecuteSegmented** w celu pobierania wyników metoda wywołuje teraz metodę **ExecuteSegmentedAsync** i używa modyfikatora **await** , aby pobrać wyniki asynchronicznie.  

Aplikacja kliencka może wywołać tę metodę wiele razy (z różnymi wartościami dla parametru **działu** ), a każde zapytanie zostanie uruchomione w osobnym wątku.  

Brak asynchronicznej wersji metody **Execute** w klasie **TableQuery** , ponieważ interfejs **IEnumerable** nie obsługuje asynchronicznego wyliczania.  

Można także wstawiać, aktualizować i usuwać jednostki asynchronicznie. Poniższy C# przykład pokazuje prostą, synchroniczną metodę wstawiania lub zastępowania jednostki pracownika:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Możesz łatwo zmodyfikować ten kod, aby aktualizacja działała asynchronicznie w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchronicznym można zobaczyć następujące zmiany w wersji synchronicznej:  

* Sygnatura metody obejmuje teraz modyfikator **Async** i zwraca wystąpienie **zadania** .  
* Zamiast wywoływania metody **Execute** w celu zaktualizowania jednostki, metoda wywołuje teraz metodę **wywoływanie ExecuteAsync** i używa modyfikatora **await** do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywoływać wiele metod asynchronicznych, takich jak ta, i każde wywołanie metody zostanie uruchomione w osobnym wątku.  

### <a name="credits"></a>Środki
Chcemy podziękowanie następującym członkom zespołu platformy Azure w celu ich udostępnienia: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jan Irwin, Vamshidhar Kommineni, Vinay Shah i Serdar Ozler, jak i Tomasz Hollander from Microsoft DX. 

Będziemy również podziękowanie następujące informacje na temat programu Microsoft MVP, aby uzyskać cenną opinię w ramach cyklu przeglądu: Igora Papirov i Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

