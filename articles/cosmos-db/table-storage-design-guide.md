---
title: Projektowanie tabel Azure Cosmos DB na potrzeby skalowania i wydajności
description: 'Przewodnik dotyczący projektowania usługi Azure Table Storage: skalowalne i wydajne tabele w Azure Cosmos DB i Azure Table Storage'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395656"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Przewodnik projektowy tabeli usługi Azure Table Storage: skalowalne i wydajne tabele

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę różne czynniki, w tym koszt. Jeśli posiadasz doświadczenie w projektowaniu schematów na potrzeby relacyjnych baz danych, te zagadnienia będą Ci znane. Mimo że istnieją pewne podobieństwa między usługą Azure Table Storage i modelami relacyjnymi, istnieje również wiele istotnych różnic. Te różnice prowadzą zwykle do różnych projektów, które mogą wyglądać na nieintuicyjne lub błędne dla kogoś, kto ma doświadczenie z relacyjnymi bazami danych, ale mają sens, jeśli projektowanie odbywa się na potrzeby magazynu klucz/wartość bazy danych NoSQL, na przykład usługi Table Storage.

Usługa Table Storage została zaprojektowana do obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek ("wiersze" w terminologii relacyjnej bazy danych) lub zestawy danych, które muszą obsługiwać duże ilości transakcji. W związku z tym należy myśleć o sposobie przechowywania danych i zrozumieć, jak działa magazyn tabel. Dobrze zaprojektowany magazyn danych NoSQL może pozwalać na skalowanie rozwiązań znacznie bardziej wydajnych (i niższych kosztów) niż rozwiązanie korzystające z relacyjnej bazy danych. Ten przewodnik ułatwi Ci następujące tematy.  

## <a name="about-azure-table-storage"></a>Informacje o usłudze Azure Table Storage
Ta sekcja zawiera najważniejsze funkcje usługi Table Storage, które są szczególnie przydatne do projektowania pod kątem wydajności i skalowalności. Jeśli dopiero zaczynasz korzystać z usługi Azure Storage i magazynu tabel, zobacz [wprowadzenie do Microsoft Azure Storage](../storage/common/storage-introduction.md) i [Zacznij korzystać z usługi Azure Table Storage przy użyciu platformy .NET](table-storage-how-to-use-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Mimo że ten przewodnik dotyczy magazynu tabel, zawiera on kilka dyskusji na temat usługi Azure queue storage i usługi Azure Blob Storage oraz jak można ich używać razem z magazynem tabel w rozwiązaniu.  

Magazyn tabel używa formatu tabelarycznego do przechowywania danych. W terminologii każdy wiersz w tabeli reprezentuje jednostkę, a kolumny przechowywać różne właściwości tej jednostki. Każda jednostka ma parę kluczy do unikatowego identyfikowania, a kolumna sygnatur czasowych używanej przez magazyn tabel do śledzenia czasu ostatniej aktualizacji jednostki. Pole sygnatury czasowej jest dodawane automatycznie i nie można ręcznie nadpisać sygnatury czasowej z dowolną wartością. W usłudze Table Storage jest używany ostatnio modyfikowany znacznik czasu (LMT) w celu zarządzania optymistyczną współbieżnością.  

> [!NOTE]
> Operacje interfejsu API REST usługi Table Storage zwracają również wartość `ETag`, która pochodzi od LMT. W tym dokumencie terminy ETag i LMT są używane zamiennie, ponieważ odnoszą się do tych samych danych źródłowych.  
> 
> 

Poniższy przykład pokazuje wzór prostej tabeli do przechowywania podmiotów pracowników i działu. Wiele z przykładów pokazanych w dalszej części tego przewodnika są oparte na tego prostego projektu.  

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
<td>Don</td>
<td>Hall</td>
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
<td>Cze</td>
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
<td>Sprzedaż</td>
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


Do tej pory ten projekt wygląda podobnie do tabeli w relacyjnej bazie danych. Kluczowe różnice to obowiązkowe kolumny i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda Właściwość zdefiniowana przez użytkownika, taka jak **FirstName** lub **Age**, ma typ danych, na przykład liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. W przeciwieństwie do relacyjnej bazy danych, jednak bez schematu natura magazynu tabel oznacza, że właściwość nie musi mieć tego samego typu danych dla każdej jednostki. Aby złożone typy danych są przechowywane w pojedynczej właściwości, należy użyć serializacji formatu JSON lub XML. Aby uzyskać więcej informacji, zobacz [Omówienie modelu danych usługi Table Storage](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Wybór `PartitionKey` i `RowKey` ma podstawowe znaczenie dla dobrego projektu tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację `PartitionKey` i `RowKey`. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, `PartitionKey` i `RowKey` wartości są indeksowane, aby utworzyć klastrowany indeks, który umożliwia szybkie wyszukiwanie. W usłudze Table Storage nie są jednak tworzone żadne indeksy pomocnicze, więc są to jedyne dwie właściwości indeksowane (Niektóre wzorce opisane w dalszej części pokazują, jak można obejść to oczywiste ograniczenie).  

Tabela składa się z co najmniej jednej partycji, a wiele podjętych decyzji projektowych będzie wokół wyboru odpowiednich `PartitionKey` i `RowKey` w celu zoptymalizowania rozwiązania. Rozwiązanie może składać się tylko z pojedynczej tabeli, która zawiera wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie ma wiele tabel. Tabele pomagają logicznie organizować jednostki i ułatwiają zarządzanie dostępem do danych przy użyciu list kontroli dostępu. Całą tabelę można usunąć przy użyciu jednej operacji magazynu.  

### <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwa tabeli i `PartitionKey` wspólnie identyfikują partycję w ramach usługi magazynu, w której magazyn tabel przechowuje jednostkę. Jak również część schematu adresowania dla jednostek, partycje definiują zakres dla transakcji (zobacz sekcję w dalszej części tego artykułu, [transakcje grupy jednostek](#entity-group-transactions)) i stanowią podstawę skalowania magazynu tabel. Aby uzyskać więcej informacji na temat partycji tabeli, zobacz [Lista kontrolna wydajności i skalowalności dla usługi Table Storage](../storage/tables/storage-performance-checklist.md).  

W usłudze Table Storage pojedyncze usługi węzłów mają jedną lub większą liczbę kompletnych partycji, a usługa jest skalowana przez dynamiczne Równoważenie obciążenia partycji między węzłami. Jeśli węzeł jest w obciążeniu, usługa Table Storage może podzielić zakres partycji objętych przez ten węzeł na różne węzły. Po nawrocie ruchu magazyn tabel może scalać zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji na temat wewnętrznych szczegółów magazynu tabel, a zwłaszcza sposobu zarządzania partycjami, zobacz [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W usłudze Table Storage transakcje grupy jednostek (EGTs) są jedynym wbudowanym mechanizmem do wykonywania niepodzielnych aktualizacji w wielu jednostkach. EGTs są również nazywane *transakcjami wsadowymi*. EGTs może działać tylko na jednostkach przechowywanych w tej samej partycji (udostępniając ten sam klucz partycji w określonej tabeli), dlatego w dowolnym momencie potrzebna jest niepodzielna zachowań transakcyjnych w wielu jednostkach, upewnij się, że te jednostki znajdują się w tej samej partycji. Jest to często powód, aby zachować wiele typów jednostek w tej samej tabeli (i partycji), a nie używać wielu tabel dla różnych typów jednostek. Pojedynczy EGT może operować na co najwyżej 100 jednostek.  Jeśli przesyłasz wiele współbieżnych EGTs do przetwarzania, należy upewnić się, że te EGTs nie działają na jednostkach, które są wspólne dla EGTs. W przeciwnym razie ryzyko opóźnienia przetwarzania.

EGTs również wprowadza potencjalne rozwiązanie do szacowania w projekcie. Użycie większej liczby partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości w przypadku żądań równoważenia obciążenia między węzłami. Jednak może to ograniczyć możliwość wykonywania przez aplikacje niepodzielnych transakcji i zapewnienia silnej spójności danych. Ponadto istnieją konkretne cele skalowalności na poziomie partycji, która może ograniczyć przepływność transakcji, które można oczekiwać dla jednego węzła.

Aby uzyskać więcej informacji na temat celów skalowalności dla kont usługi Azure Storage, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../storage/common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji dotyczących skalowalności dla magazynu tabel, zobacz [cele skalowalności i wydajności dla usługi Table Storage](../storage/tables/scalability-targets.md). Kolejnych sekcjach tego przewodnika omówiono różne projektowania strategii, które ułatwiają zarządzanie wad i zalet, taką jak ta i omówić najlepszy sposób, aby wybrać klucz partycji, na podstawie określonych wymagań aplikacji klienckiej.  

### <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności
Poniższa tabela zawiera kilka najważniejszych wartości, które należy znać podczas projektowania rozwiązania magazynu tabel:  

| Całkowita pojemność konta usługi Azure storage | 500 TB |
| --- | --- |
| Liczba tabel na koncie usługi Azure storage |Ograniczone tylko pojemności konta magazynu. |
| Liczba partycji w tabeli |Ograniczone tylko pojemności konta magazynu. |
| Liczba jednostek w partycji |Ograniczone tylko pojemności konta magazynu. |
| Rozmiar pojedynczą jednostkę |Do 1 MB i maksymalnie 255 właściwości (w tym `PartitionKey`, `RowKey`i `Timestamp`). |
| Rozmiar `PartitionKey` |Ciąg o rozmiarze do 1 KB. |
| Rozmiar `RowKey` |Ciąg o rozmiarze do 1 KB. |
| Rozmiar transakcji grupy jednostek |Transakcja może obejmować maksymalnie 100 jednostek, a ładunek musi mieć rozmiar mniejszy niż 4 MB. EGT można aktualizować tylko jednostki jeden raz. |

Aby uzyskać więcej informacji, zobacz [Omówienie modelu danych Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogi, ale należy uwzględnić oszacowania kosztów zarówno w przypadku użycia pojemności, jak i liczby transakcji w ramach oceny dowolnego rozwiązania korzystającego z usługi Table Storage. Jednak w wielu scenariuszach przechowywanie nieznormalizowanych lub zduplikowanych danych w celu poprawy wydajności lub skalowalności rozwiązania jest prawidłowym podejściem. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel
Te listy zawierają podsumowanie najważniejszych wytycznych, które należy wziąć pod uwagę podczas projektowania tabel. Ten przewodnik zawiera więcej szczegółów w dalszej części artykułu. Wytyczne te różnią się od wytycznych, które zwykle są stosowane w przypadku projektowania relacyjnej bazy danych.  

Projektowanie magazynu tabel do wydajnego *odczytu* :

* **Projekt do wykonywania zapytań w aplikacjach z dużym obciążeniem.** Podczas projektowania tabel należy zastanowić się nad zapytaniami (szczególnie w odróżnieniu od opóźnienia), które zostaną uruchomione przed zawieszeniem, jak należy zaktualizować jednostki. Skutkuje to zazwyczaj wydajne i wydajne rozwiązanie.  
* **W zapytaniach Określ zarówno `PartitionKey`, jak i `RowKey`.** *Zapytania punktowe* , takie jak te, to najbardziej wydajne zapytania dotyczące magazynu tabel.  
* **Rozważ przechowywanie zduplikowanych kopii jednostek.** Magazyn tabel jest tani, więc Rozważ przechowywanie tej samej jednostki wielokrotnie (z różnymi kluczami), aby umożliwić wydajniejsze zapytania.  
* **Rozważ wyprowadzenie denormalizacji danych.** Magazyn tabel jest tani, dlatego należy rozważyć denormalizację danych. Na przykład przechowywać podsumowania jednostki tak, aby zapytania dla danych zagregowanych wystarczy dostęp do pojedynczej jednostki.  
* **Użyj wartości klucza złożonego.** Jedyne klucze są `PartitionKey` i `RowKey`. Aby włączyć alternatywnego kluczem dostępu do ścieżki do jednostek, na przykład użyć wartości klucza złożonego.  
* **Użyj projekcji zapytania.** Można zmniejszyć ilość danych, który transferu za pośrednictwem sieci przy użyciu kwerend wybierających tylko pola, które są potrzebne.  

Projektowanie magazynu tabel do wydajnego *zapisu* :  

* **Nie twórz partycji aktywnych.** Wybierz klucze, które umożliwiają żądań rozkłada się na wielu partycjach w dowolnym momencie.  
* **Unikaj wzrostu ruchu.** Dystrybuuj ruch w rozsądnym czasie i unikaj wzrostu ruchu.
* **Niekoniecznie utwórz oddzielną tabelę dla każdego typu jednostki.** Transakcje niepodzielne jest stosowanie różnych typów jednostek, tych typów jednostek można przechowywać w jednej partycji w tej samej tabeli.
* **Należy wziąć pod uwagę maksymalną przepływność, która ma zostać osiągnięta.** Należy pamiętać o możliwościach skalowalności w usłudze Table Storage i upewnić się, że projekt nie spowoduje przekroczenia tych elementów.  

W dalszej części tego przewodnika zobaczysz przykłady, które przenoszą wszystkie te zasady do ćwiczeń.  

## <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Magazyn tabel może być odczytywany w dużym stopniu, intensywnie napisanym zapisem lub kombinacją dwóch. Ta sekcja uwzględnia projektowanie w celu wydajnej obsługi operacji odczytu. Zazwyczaj projekt, że obsługuje operacje odczytu efektywnie również jest wydajne dla operacji zapisu. Podczas projektowania programu w celu obsługi operacji zapisu należy jednak uwzględnić dodatkowe zagadnienia. Są one omówione w następnej sekcji [projekt do modyfikacji danych](#design-for-data-modification).

Dobrym punktem początkowym umożliwiającym odczytywanie danych w sposób efektywny jest zaproszenie "jakie zapytania będą wymagane do uruchomienia aplikacji w celu pobrania potrzebnych danych?".  

> [!NOTE]
> W przypadku używania magazynu tabel ważne jest, aby projekt mógł zostać skorygowany z góry, ponieważ jest trudny i kosztowny do jego późniejszej zmiany. Na przykład w relacyjnej bazie danych często istnieje możliwość rozwiązywania problemów z wydajnością po prostu przez dodanie indeksów do istniejącej bazy danych. Nie jest to opcja z magazynem tabel.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Jak wybór `PartitionKey` i `RowKey` wpływa na wydajność zapytań
W poniższych przykładach założono, że magazyn tabel przechowuje jednostki pracowników z następującą strukturą (większość przykładów pomija Właściwość `Timestamp` do przejrzystości):  

| Nazwa kolumny | Typ danych |
| --- | --- |
| `PartitionKey` (Nazwa działu) |Ciąg |
| `RowKey` (identyfikator pracownika) |Ciąg |
| `FirstName` |Ciąg |
| `LastName` |Ciąg |
| `Age` |Liczba całkowita |
| `EmailAddress` |Ciąg |

Poniżej przedstawiono niektóre ogólne wytyczne dotyczące projektowania zapytań usługi Table Storage. Składnia filtru użyta w poniższych przykładach pochodzi z interfejsu API REST usługi Table Storage. Aby uzyskać więcej informacji, zobacz [jednostki zapytań](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* *Zapytanie punktowe* jest najbardziej wydajnym wyszukiwaniem do użycia i jest zalecane w przypadku wyszukiwania lub wyszukiwania wysokiego poziomu, które wymaga najmniejszego opóźnienia. Takie zapytanie może służyć do wydajnego lokalizowania pojedynczej jednostki przez określenie wartości `PartitionKey` i `RowKey`. Na przykład: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Druga Najlepsza to *zapytanie zakresowe*. Używa `PartitionKey`i filtrów dla zakresu wartości `RowKey`, aby zwrócić więcej niż jedną jednostkę. Wartość `PartitionKey` identyfikuje konkretną partycję, a wartości `RowKey` identyfikują podzestaw jednostek w tej partycji. Na przykład: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Trzecia Najlepsza to *skanowanie partycji*. Używa `PartitionKey`i filtrów dla innej właściwości niebędącej kluczem i może zwrócić więcej niż jedną jednostkę. Wartość `PartitionKey` identyfikuje konkretną partycję, a wartości właściwości wybierają podzbiór jednostek w tej partycji. Na przykład: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Skanowanie tabeli* nie zawiera `PartitionKey`i jest niewydajne, ponieważ przeszukuje wszystkie partycje wchodzące w skład tabeli pod kątem pasujących jednostek. Wykonuje skanowanie tabeli niezależnie od tego, czy filtr używa `RowKey`. Na przykład: `$filter=LastName eq 'Jones'`.  
* Zapytania usługi Azure Table Storage zwracające wiele jednostek sortują je w `PartitionKey` i `RowKey` kolejności. Aby uniknąć tworzenia jednostek w kliencie, wybierz `RowKey`, który definiuje najbardziej typowy porządek sortowania. Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Użycie "**or**" do określenia filtru opartego na wartościach `RowKey` powoduje skanowanie partycji i nie jest traktowane jako zapytanie o zakres. W związku z tym Unikaj zapytań używających filtrów takich jak: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Aby zapoznać się z przykładami kodu po stronie klienta, które używają biblioteki klienta usługi Storage do uruchamiania wydajnych zapytań, zobacz:  

* [Uruchamianie zapytania punktu przy użyciu biblioteki klienta usługi Storage](#run-a-point-query-by-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projekcja po stronie serwera](#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli zobacz:  

* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Wybierz odpowiednią `PartitionKey`
Wybór `PartitionKey` powinien zrównoważyć potrzebę umożliwienia używania EGTs (w celu zapewnienia spójności) przed wymaganiem dystrybucji jednostek na wielu partycjach (aby zapewnić skalowalne rozwiązanie).  

Z jednej najwyższej wartości można przechowywać wszystkie jednostki w jednej partycji. Może to jednak ograniczyć skalowalność rozwiązania i uniemożliwić usłudze Table Storage możliwość równoważenia obciążenia żądań. Z drugiej najwyższej wartości można przechowywać jedną jednostkę na partycję. Jest to wysoce skalowalne i umożliwia usłudze Table Storage Równoważenie obciążenia żądań, ale uniemożliwia korzystanie z transakcji grupy jednostek.  

Idealny `PartitionKey` umożliwia korzystanie z wydajnych zapytań i zawiera wystarczające partycje, aby zapewnić skalowalność rozwiązania. Zazwyczaj można sprawdzić, czy jednostki będą mieć odpowiednią właściwość, która dystrybuuje jednostki w wystarczającej partycji.

> [!NOTE]
> Na przykład w systemie, w którym są przechowywane informacje o użytkownikach lub pracownikach, `UserID` mogą być dobrym `PartitionKey`. Może istnieć kilka jednostek, które używają określonego `UserID` jako klucza partycji. Każda jednostka, która przechowuje dane dotyczące użytkownika, jest zgrupowana w jedną partycję. Te jednostki są dostępne za pośrednictwem EGTs, podczas gdy nadal są wysoce skalowalne.
> 
> 

Istnieją dodatkowe zagadnienia dotyczące wyboru `PartitionKey`, które odnoszą się do sposobu wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz artykuł [projektowanie pod kątem modyfikacji danych](#design-for-data-modification) w dalszej części tego artykułu.  

### <a name="optimize-queries-for-table-storage"></a>Optymalizowanie zapytań dotyczących usługi Table Storage
Magazyn tabel automatycznie indeksuje jednostki przy użyciu wartości `PartitionKey` i `RowKey` w pojedynczym indeksie klastrowanym. Wynika to z tego, że zapytania punktowe są najbardziej wydajne do użycia. Nie ma jednak żadnych indeksów, które są inne niż w indeksie klastrowanym `PartitionKey` i `RowKey`.

Wiele projektów musi spełniać wymagania, aby włączyć wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład lokalizowanie jednostek pracowników na podstawie poczty e-mail, identyfikatora pracownika lub nazwiska. Poniższe wzorce przedstawione w sekcji [wzorce projektowe tabeli](#table-design-patterns) dotyczą tych typów wymagań. Wzorce opisują także sposoby obejścia faktu, że magazyn tabel nie udostępnia indeksów pomocniczych.  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern): Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości `RowKey` (w tej samej partycji). Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern): przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości `RowKey` w oddzielnych partycjach lub w oddzielnych tabelach. Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.  
* [Wzorzec jednostek indeksu](#index-entities-pattern): utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

### <a name="sort-data-in-table-storage"></a>Sortowanie danych w usłudze Table Storage

Usługa Table Storage zwraca wyniki zapytania posortowane w kolejności rosnącej na podstawie `PartitionKey`, a następnie przez `RowKey`.

> [!NOTE]
> Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Klucze w magazynie tabel są wartościami ciągu. Aby upewnić się, że wartości liczbowe są sortowane prawidłowo, należy przekonwertować je na stałą długość i uzupełnić je zerami. Na przykład jeśli wartość identyfikatora pracownika, która jest używana jako `RowKey`, jest wartością całkowitą, należy przekonwertować pracownika o IDENTYFIKATORze **123** do **00000123**. 

Wiele aplikacji ma wymagania dotyczące korzystania z danych w różnych zleceniach sortowane: na przykład sortowanie pracowników według nazwy lub dołączenie do daty. Następujące wzorce w [wzorach projektu tabeli](#table-design-patterns) sekcji przedstawiają sposób alternatywnych zamówień sortowania dla jednostek:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern): Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości `RowKey` (w tej samej partycji). Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern): przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości `RowKey` w oddzielnych partycjach w oddzielnych tabelach. Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.
* [Wzorzec końca dziennika](#log-tail-pattern): pobranie *n* jednostek, które zostały ostatnio dodane do partycji, przy użyciu wartości `RowKey`, która sortuje w odwrotnej kolejności dat i godzin.  

## <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ta sekcja koncentruje się na informacjach dotyczących projektowania optymalizacji operacji wstawiania, aktualizacji i usuwa. W niektórych przypadkach należy oszacować handel między projektami, które optymalizują się w celu wykonywania zapytań dotyczących projektów, które optymalizują się pod kątem modyfikacji danych. Ta ocena jest podobna do działania w projektach relacyjnych baz danych (chociaż techniki zarządzania projektami są inne w relacyjnej bazie danych). W sekcjach [wzorów projektowania tabeli](#table-design-patterns) opisano niektóre szczegółowe wzorce projektowe usługi Table Storage, a niektóre z nich różnią się. W ćwiczeniu można sprawdzić, czy wiele projektów zoptymalizowanych pod kątem wykonywania zapytań dotyczących jednostek również działa prawidłowo w przypadku modyfikowania jednostek.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizowanie wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć jednostkę, musisz mieć możliwość identyfikowania jej przy użyciu wartości `PartitionKey` i `RowKey`. W związku z tym wybór `PartitionKey` i `RowKey` do modyfikacji jednostek powinien spełniać podobne kryteria, aby obsługiwać zapytania dotyczące punktów. Chcesz identyfikować jednostki tak efektywnie, jak to możliwe. Nie chcesz korzystać z niewydajnej partycji lub skanowania tabeli, aby zlokalizować jednostkę w celu odnalezienia `PartitionKey` i `RowKey` wartości, które należy zaktualizować lub usunąć.  

Następujące wzorce w [wzorach projektu tabeli](#table-design-patterns) sekcji dotyczą optymalizacji wydajności operacji INSERT, Update i DELETE:  

* [Wzorzec usuwania dużych woluminów](#high-volume-delete-pattern): Włącz usuwanie dużej liczby jednostek przez zapisanie wszystkich jednostek do jednoczesnego usunięcia w osobnej tabeli. Obiekty można usunąć, usuwając tabelę.  
* [Wzorzec serii danych](#data-series-pattern): Przechowuj kompletne serie danych w pojedynczej jednostce, aby zminimalizować liczbę żądań, które należy wykonać.  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern): Użyj wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  
* [Wzorzec dużych jednostek](#large-entities-pattern): Użyj magazynu obiektów BLOB do przechowywania dużych wartości właściwości.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnianie spójności przechowywanych jednostek
Kluczowym czynnikiem, który ma wpływ na wybór klucze na potrzeby optymalizacji modyfikacji danych jest sposób zapewnienia spójności za pomocą transakcji niepodzielnej. EGT można używać tylko do wykonywania operacji jednostek przechowywanych w tej samej partycji.  

W poniższych wzorcach [wzorców projektu tabeli](#table-design-patterns) sekcji adres zarządzania spójnością:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern): Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości `RowKey` (w tej samej partycji). Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern): przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości `RowKey` w oddzielnych partycjach lub w oddzielnych tabelach. Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern): Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.
* [Wzorzec jednostek indeksu](#index-entities-pattern): utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  
* [Wzorzec denormalizacji](#denormalization-pattern): połącz powiązane dane ze sobą w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania z pojedynczym punktem.  
* [Wzorzec serii danych](#data-series-pattern): Przechowuj kompletne serie danych w jednej jednostce, aby zminimalizować liczbę podejmowanych żądań.  

Aby uzyskać więcej informacji, zobacz [transakcje grupy jednostek](#entity-group-transactions) w dalszej części tego artykułu.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Upewnij się, że projekt do wydajnej modyfikacji ułatwia wykonywanie wydajnych zapytań
Projektowanie pod kątem wydajne tworzenie zapytań powoduje efektywne modyfikacje, ale w wielu przypadkach zawsze powinni sprawdzić, czy to w przypadku określonego scenariusza. Niektóre wzorce w [wzorach projektu tabeli](#table-design-patterns) w sekcji jawnie szacują kompromisy między wykonywaniem zapytań i modyfikowaniem jednostek, a zawsze należy wziąć pod uwagę liczbę poszczególnych typów operacji.  

Poniższe wzorce w sekcjach [projektowanie projektowe](#table-design-patterns) , które stanowią rozróżnienia między projektowaniem pod kątem wydajnych zapytań i projektowaniem do wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](#compound-key-pattern): Użyj złożonych wartości `RowKey`, aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  
* [Wzorzec końca dziennika](#log-tail-pattern): pobranie *n* jednostek, które zostały ostatnio dodane do partycji, przy użyciu wartości `RowKey`, która sortuje w odwrotnej kolejności dat i godzin.  

## <a name="encrypt-table-data"></a>Szyfruj dane tabeli
Biblioteka klienta usługi Azure Storage dla platformy .NET obsługuje szyfrowanie właściwości jednostki ciągów dla operacji wstawiania i zamieniania. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem do ciągów po odszyfrowaniu.    

W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Określ atrybut `EncryptProperty` (dla jednostek POCO, które pochodzą z `TableEntity`), lub określ mechanizm rozpoznawania szyfrowania w opcjach żądania. Program rozpoznawania szyfrowania jest delegatem, który pobiera klucz partycji, klucz wiersza i nazwę właściwości, i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania, Biblioteka klienta używa tych informacji do określenia, czy właściwość powinna być szyfrowana podczas zapisywania w sieci. Delegat udostępnia także możliwości logiki wokół jak zaszyfrowane właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Nie trzeba podawać tych informacji podczas odczytywania lub wysyłania zapytań do jednostek.

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości mógł zostać wcześniej zaszyfrowany przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonania dodatkowych wywołań usługi do odczytania istniejącej jednostki z usługi lub użycia nowego klucza dla każdej właściwości. Żadna z tych elementów nie jest odpowiednia ze względu na wydajność.     

Aby uzyskać informacje na temat szyfrowania danych tabeli, zobacz [szyfrowanie po stronie klienta i Azure Key Vault Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relacje modelu
Tworzenie modeli domeny jest krokiem w projekcie złożonych systemów. Zazwyczaj proces modelowania jest używany do identyfikowania jednostek i relacji między nimi w celu zrozumienia domeny biznesowej i poinformowania o projekcie systemu. Ta sekcja koncentruje się na sposobie tłumaczenia niektórych wspólnych typów relacji znalezionych w modelach domen na projekty dla magazynu tabel. Proces mapowania z logicznego modelu danych na fizyczny model danych oparty na NoSQL różni się od tego, który jest używany podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zazwyczaj zakłada proces normalizacji danych zoptymalizowany pod kątem minimalizowania nadmiarowości. Taki projekt zakłada również możliwość wykonywania zapytań deklaratywnych, która stanowi streszczenie implementacji działania bazy danych.  

### <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacje jeden do wielu między obiektami domeny biznesowej często występują: na przykład jednego działu ma wielu pracowników. Istnieje kilka sposobów implementacji relacji jeden-do-wielu w magazynie tabel, z których każdy ma zalety i wady, które mogą dotyczyć konkretnego scenariusza.  

Rozważmy przykład dużej firmy wielonarodowej z dziesiątki tysięcy działów i jednostek pracowników. Każdy dział ma wielu pracowników, a każdy pracownik jest skojarzony z jednym określonym działem. Jednym z rozwiązań jest przechowywanie oddzielnych jednostek działu i pracowników, takich jak następujące:  

![Ilustracja przedstawiająca jednostkę działu i jednostkę pracownika][1]

Ten przykład pokazuje niejawną relację "jeden do wielu" między typami, na podstawie wartości `PartitionKey`. Każdy dział może mieć wielu pracowników.  

Ten przykład pokazuje także jednostki działu i jego jednostek powiązanych pracowników w tej samej partycji. Można wybrać użycie różnych partycji, tabel, a nawet kont magazynu dla różnych typów jednostek.  

Alternatywnym podejściem jest denormalizowanie danych i przechowywanie tylko jednostek pracowników z nieznormalizowanymi danymi działu, jak pokazano w poniższym przykładzie. W tym konkretnym scenariuszu podejście nieznormalizowane może nie być najlepszym rozwiązaniem, jeśli istnieje wymóg, aby można było zmienić szczegóły menedżera działu. W tym celu należy zaktualizować każdego pracownika w dziale.  

![Ilustracja jednostki Employee][2]

Aby uzyskać więcej informacji, zobacz [wzorzec denormalizacji](#denormalization-pattern) w dalszej części tego przewodnika.  

Poniższa tabela zawiera podsumowanie specjalistów i wad poszczególnych metod przechowywania jednostek pracowników i działów, które mają relację jeden-do-wielu. Należy również wziąć pod uwagę częstotliwość wykonywania różnych operacji. Może być możliwe zaakceptowanie projektu, który obejmuje kosztowną operację, jeśli ta operacja jest wykonywana tylko rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Specjaliści</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddziel typów jednostek, tę samą partycję, w tej samej tabeli</td>
<td>
<ul>
<li>Należy zaktualizować jednostki działu, przy użyciu jednej operacji.</li>
<li>EGT służy do zapewniania spójności, jeśli masz wymaganie, aby zmodyfikować jednostki działu zawsze wtedy, gdy użytkownik aktualizacji/insert/usuwania jednostki pracowników. Na przykład, jeśli to Ty masz liczba pracowników działów dla każdego działu.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Magazyn — liczba operacji pojawiają się w tej samej partycji. W przypadku dużych ilości transakcji może to spowodować powstanie hotspotu.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy osobne jednostki, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Należy zaktualizować jednostki działu lub pracowników przy użyciu jednej operacji.</li>
<li>W przypadku dużych ilości transakcji może to ułatwić rozproszenie obciążenia na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Nie można użyć EGTs, aby zachować spójność podczas aktualizowania/wstawiania/usuwania pracownika i aktualizowania działu. Na przykład aktualizowanie liczba pracowników w jednostce działu.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizowanie w pojedynczej jednostki typu</td>
<td>
<ul>
<li>Możesz pobrać wszystkie informacje potrzebne przy użyciu pojedynczego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Zachowanie spójności może być kosztowne, jeśli trzeba zaktualizować informacje działu (wymaga to zaktualizowania wszystkich pracowników w dziale).</li>
</ul>
</td>
</tr>
</table>

Jak wybierać spośród tych opcji i które z nich są najbardziej znaczące, zależy to od konkretnych scenariuszy aplikacji. Na przykład jak często modyfikujesz jednostki działu? Czy wszystkie zapytania pracowników potrzebują dodatkowych informacji działu? Jak blisko ograniczeń skalowalności w partycjach lub na koncie magazynu?  

### <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modele domeny mogą zawierać relacje jeden do jednego między jednostkami. Jeśli zachodzi potrzeba zaimplementowania relacji jeden-do-jednego w magazynie tabel, należy również wybrać opcję łączenia dwóch powiązanych jednostek, gdy trzeba je pobrać. Ten link może być niejawny, na podstawie Konwencji w wartościach klucza lub jawne, przez zapisanie linku w postaci `PartitionKey` i `RowKey` wartości w każdej jednostce względem powiązanej jednostki. Aby zapoznać się z omówieniem, czy należy przechowywać powiązane jednostki w tej samej partycji, zobacz [relacje jeden do wielu](#one-to-many-relationships).  

Istnieją także uwagi dotyczące implementacji, które mogą prowadzić do wdrożenia relacji jeden-do-jednego w magazynie tabel:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [wzorzec dużych jednostek](#large-entities-pattern)).  
* Implementowanie kontroli dostępu (Aby uzyskać więcej informacji, zobacz [Kontrola dostępu za pomocą sygnatur dostępu współdzielonego](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Dołącz do klienta
Chociaż istnieją sposoby tworzenia relacji między modelami w usłudze Table Storage, nie zapomnij, że dwa podstawowe przyczyny korzystania z usługi Table Storage to skalowalność i wydajność. Jeśli okaże się, że modelowanie ma wiele relacji, które naruszają wydajność i skalowalność rozwiązania, należy poproszony o to, jeśli konieczne jest skompilowanie wszystkich relacji danych w projekcie tabeli. Może być możliwe uproszczenie projektowania i zwiększenie skalowalności i wydajności rozwiązania, Jeśli zezwolisz aplikacji klienckiej na wykonywanie dowolnych wymaganych sprzężeń.  

Na przykład jeśli masz małe tabele zawierające dane, które nie zmieniają się często, możesz pobrać te dane raz i umieścić je w pamięci podręcznej na kliencie. To uniknąć wielokrotnego natężenie ruchu do pobrania tych samych danych. W przykładach, które zostały przedstawione w tym przewodniku, zestaw działów w małej organizacji może być niewielki i nierzadko zmieniany. Sprawia to, że jest to dobry kandydat dla danych, które aplikacja kliencka może pobrać raz i buforować jako dane wyszukiwania.  

### <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka korzysta z zestawu klas, które stanowią część relacji dziedziczenia reprezentującej jednostki biznesowe, można łatwo utrzymać te jednostki w magazynie tabel. Na przykład w aplikacji klienckiej można określić następujący zestaw klas, gdzie `Person` jest klasą abstrakcyjną.

![Diagram relacji dziedziczenia][3]

Można utrzymywać wystąpienia dwóch konkretnych klas w usłudze Table Storage przy użyciu jednej tabeli `Person`. Użyj jednostek, które wyglądają następująco:  

![Ilustracja przedstawiająca jednostkę klienta i jednostkę pracownika][4]

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta, zobacz [Praca z typami jednostek heterogenicznych](#work-with-heterogeneous-entity-types) w dalszej części tego przewodnika. Zapewnia to przykłady sposobu rozpoznaje typ jednostki w kodzie klienta.  

## <a name="table-design-patterns"></a>Wzorce projektowe tabel
W poprzednich sekcjach zawarto informacje na temat optymalizowania projektu tabeli na potrzeby pobierania danych jednostki przy użyciu zapytań oraz wstawiania, aktualizowania i usuwania danych jednostki. W tej sekcji opisano niektóre wzorce, które są odpowiednie do użytku z usługą Table Storage. Ponadto zobaczysz, jak można praktycznie rozwiązać niektóre problemy i wady, które zostały wcześniej zgłoszone w tym przewodniku. Poniższy diagram podsumowuje relacje między różnymi wzorcami:  

![Diagram wzorców projektu tabeli][5]

Mapa wzorca wyróżnia pewne relacje między wzorcami (Blue) i antywzorców (pomarańczowy), które są opisane w tym przewodniku. Oczywiście istnieje wiele wzorców, które są warte biorąc pod uwagę. Na przykład jeden z kluczowych scenariuszy dotyczących usługi Table Storage polega na użyciu [wzorca widoku materiałowego](https://msdn.microsoft.com/library/azure/dn589782.aspx) ze wzorca [segregowania odpowiedzialności z poleceniami](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeks pomocniczy wewnątrz partycji
Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości `RowKey` (w tej samej partycji). Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`. Aktualizacje między kopiami można zachować spójność za pomocą EGTs.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki przy użyciu wartości `PartitionKey` i `RowKey`. Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład korzystając z poniższej struktury tabeli, aplikacja kliencka może użyć zapytania Point do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości `PartitionKey` i `RowKey`). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.

![Ilustracja jednostki Employee][6]

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że magazyn tabel nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż kolejność `RowKey`.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii poszczególnych jednostek, z których każda ma inną wartość `RowKey`. Jeśli przechowujesz jednostkę z następującymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów `RowKey`, `empid_`i `email_` umożliwiają wykonywanie zapytań dotyczących jednego pracownika lub zakresu pracowników przy użyciu zakresu adresów e-mail lub identyfikatorów pracowników.  

![Ilustracja przedstawiająca jednostkę pracownika z różnymi wartościami RowKey][7]

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika) i jeden przeszukiwany przez adres e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq "empid_000223")  
* $filter = (PartitionKey EQ "Sales") i (RowKey EQ "email_jonesj@contoso.com")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail. Zapytanie dotyczące jednostek z odpowiednim prefiksem w `RowKey`.  

* Aby znaleźć wszystkich pracowników działu sprzedaży z IDENTYFIKATORem pracownika z zakresu od 000100 do 000199, użyj: $filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000100") i (RowKey Le "empid_000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail rozpoczynającego się od litery "a", należy użyć: $filter = (PartitionKey EQ "Sales") i (RowKey GE "email_a") i (RowKey lt "email_b")  
  
Składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST usługi Table Storage. Aby uzyskać więcej informacji, zobacz [jednostki zapytań](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tani, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Należy jednak zawsze oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, które będą uruchamiane przez aplikację kliencką.  
* Ponieważ pomocnicze jednostki indeksu są przechowywane w tej samej partycji co oryginalne jednostki, należy się upewnić, że elementy docelowe skalowalności dla pojedynczej partycji nie zostaną przekroczone.  
* Możesz zachować swoje zduplikowanych wpisów zgodne ze sobą przy użyciu EGTs niepodzielne zaktualizować dwie kopie jednostki. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz [Korzystanie z transakcji grupy jednostek](#entity-group-transactions).  
* Wartość używana dla `RowKey` musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w `RowKey` (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie w oparciu o górną i dolną granicę.  
* Nie musisz koniecznie duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w `RowKey` nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:

  ![Ilustracja jednostki Employee][8]

* Zazwyczaj lepiej jest przechowywać duplikaty danych i upewnić się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania, niż używać jednego zapytania do lokalizowania jednostki i drugiej, aby wyszukać wymagane dane.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy:

- Aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy.
- Klient musi pobrać jednostki w różnych zamówieniach sortowania.
- Każdą jednostkę można zidentyfikować przy użyciu różnych unikatowych wartości.

Należy jednak pamiętać, że nie przekracza limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych wartości `RowKey`.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern)
* [Wzorzec klucza złożonego](#compound-key-pattern)
* [Transakcje grupy jednostek](#entity-group-transactions)
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Komunikacja między partycji wzorzec indeks pomocniczy
Przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości `RowKey` w oddzielnych partycjach lub w oddzielnych tabelach. Dzięki temu można szybko i wydajnie odszukać oraz alternatywnej kolejności sortowania przy użyciu różnych wartości `RowKey`.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki przy użyciu wartości `PartitionKey` i `RowKey`. Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład korzystając z poniższej struktury tabeli, aplikacja kliencka może użyć zapytania Point do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości `PartitionKey` i `RowKey`). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.  

![Ilustracja jednostki Employee][9]

Jeśli chcesz także być w stanie znaleźć jednostki pracowników na podstawie wartości innej właściwości, takie jak adres e-mail musi być znaleźć dopasowania mniej wydajne rozwiązanie skanowania partycji. Wynika to z faktu, że magazyn tabel nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż kolejność `RowKey`.  

Przewidujesz dużą liczbę transakcji w odniesieniu do tych jednostek i chcesz zminimalizować ryzyko związane z szybkością magazynowania tabeli ograniczającej klienta.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki, przy czym każda kopia używa różnych `PartitionKey` i `RowKey` wartości. Jeśli przechowujesz jednostkę z następującymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów dla `PartitionKey`, `empid_`i `email_` umożliwiają zidentyfikowanie indeksu, który ma być używany w zapytaniu.  

![Ilustracja przedstawiająca jednostkę pracownika z indeksem podstawowym i jednostką pracownika z indeksem pomocniczym][10]

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika) i jeden przeszukiwany przez adres e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey eq ' empid_Sales") i (RowKey eq"000223")
* $filter = (PartitionKey EQ "email_Sales") i (RowKey EQ "jonesj@contoso.com")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail. Zapytanie dotyczące jednostek z odpowiednim prefiksem w `RowKey`.  

* Aby znaleźć wszystkich pracowników działu sprzedaży z IDENTYFIKATORem pracownika z zakresu od **000100** do **000199**, posortowanych w kolejności identyfikatorów pracowników, użyj: $Filter = (PartitionKey EQ "empid_Sales") i (RowKey GE "000100") i (RowKey Le "000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail, który rozpoczyna się od "a", posortowanych w kolejności adresów e-mail, użyj: $filter = (PartitionKey EQ "email_Sales") i (RowKey GE "a") i (RowKey lt "b")  

Należy zauważyć, że składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST usługi Table Storage. Aby uzyskać więcej informacji, zobacz [jednostki zapytań](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Zduplikowane jednostki można zachować ze sobą spójnie, korzystając ze [wzorca ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern) , aby zachować jednostki indeksu podstawowego i pomocniczego.  
* Magazyn tabel jest stosunkowo tani, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Jednak zawsze należy oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodać tylko zduplikowane jednostki do obsługi zapytań, które będą uruchamiane przez aplikację kliencką.  
* Wartość używana dla `RowKey` musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w `RowKey` (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie w oparciu o górną i dolną granicę.  
* Nie musisz koniecznie duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w `RowKey` nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:
  
  ![Ilustracja przedstawiająca jednostkę pracownika z dodatkowym indeksem][11]
* Zazwyczaj lepiej jest przechowywać zduplikowane dane i upewnić się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania, niż używać jednego zapytania do lokalizowania jednostki przy użyciu pomocniczego indeksu i innego do wyszukiwania wymaganych danych w indeksie podstawowym.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy:

- Aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy.
- Klient musi pobrać jednostki w różnych zamówieniach sortowania.
- Każdą jednostkę można zidentyfikować przy użyciu różnych unikatowych wartości.

Użyj tego wzorca, jeśli chcesz uniknąć przekroczenia limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych wartości `RowKey`.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Wzorzec transakcji ostatecznie spójną
Włącz ostatecznie spójne zachowanie całej granice partycji lub granice system magazynu za pomocą kolejek systemu Azure.  

#### <a name="context-and-problem"></a>Kontekst i problem
EGTs Włącz transakcje niepodzielne w wielu jednostkach, które współużytkują ten sam klucz partycji. Ze względu na wydajność i skalowalność można zdecydować się na przechowywanie jednostek, które mają wymagania spójności w oddzielnych partycjach lub w osobnym systemie magazynu. W takim scenariuszu nie można używać EGTs, aby zachować spójność. Na przykład może być wymagane, aby zachować spójność między:  

* Jednostki przechowywane w dwóch różnych partycji w tej samej tabeli, w różnych tabelach lub w różnych kontach magazynu.  
* Jednostka przechowywana w magazynie tabel i obiekt BLOB przechowywany w usłudze BLOB Storage.  
* Jednostka przechowywana w magazynie tabel i pliku w systemie plików.  
* Jednostka przechowywana w magazynie tabel, jeszcze indeksowana przy użyciu usługi Azure Wyszukiwanie poznawcze.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą kolejek systemu Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną dwa lub więcej partycji lub systemów magazynowania.

Aby zilustrować to podejście, Załóżmy, że istnieje wymóg, aby móc archiwizować dawne jednostki pracowników. Wcześniejsze jednostki pracowników są rzadko badane i powinny być wykluczone z wszelkich działań, które zajmują się bieżącymi pracownikami. Aby zaimplementować to wymaganie, należy przechowywać aktywnych pracowników w **bieżącej** tabeli i byłych pracowników w tabeli **archiwum** . Archiwizowanie pracownika wymaga usunięcia jednostki z **bieżącej** tabeli i dodania jednostki do tabeli **archiwum** .

Nie można jednak wykonać tych dwóch operacji za pomocą EGT. Aby uniknąć ryzyka, że błąd spowoduje, że jednostki się pojawiać w obu lub ani tabel, operacja archiwizacji musi być ostatecznie spójny. Poniższy diagram sekwencji przedstawia kroki opisane w tej operacji.  

![Diagram rozwiązania w celu zachowania spójności ostatecznej][12]

Klient inicjuje operację archiwizowania, umieszczając komunikat w kolejce platformy Azure (w tym przykładzie, aby zarchiwizować pracownika #456). Rola procesu roboczego sonduje kolejkę dla nowych komunikatów; gdy zostanie znaleziony, odczytuje komunikat i pozostawia kopię ukryte w kolejce. Następnie rola proces roboczy pobiera kopię jednostki z **bieżącej** tabeli, wstawia kopię w tabeli **archiwum** , a następnie usuwa oryginalną z **bieżącej** tabeli. Na koniec Jeśli nie wystąpiły błędy z poprzednich kroków, rola procesu roboczego usuwa ukryty komunikat z kolejki.  

W tym przykładzie krok 4 na diagramie wstawia pracownika do tabeli **archiwum** . Może dodać pracownika do obiektu BLOB w magazynie obiektów blob lub w pliku w systemie plików.  

#### <a name="recover-from-failures"></a>Odzyskiwanie po awarii
Należy pamiętać, że operacje wykonywane w ramach kroków 4-5 na diagramie *idempotentne* się na wypadek, gdyby rola procesu roboczego mogła ponownie uruchomić operację archiwizowania. W przypadku korzystania z usługi Table Storage w kroku 4 należy użyć operacji INSERT lub Replace. w kroku 5 należy użyć operacji "Usuń Jeśli istnieje" w używanej bibliotece klienta. Jeśli używasz innego systemu magazynu, musisz użyć odpowiedniej operacji idempotentne.  

Jeśli rola proces roboczy nigdy nie ukończy kroku 6 na diagramie, wówczas po upływie limitu czasu komunikat zostanie wyświetlony ponownie w kolejce gotowej do przetworzenia przez rolę procesu roboczego. Rola proces roboczy może sprawdzić, ile razy wiadomość w kolejce została odczytana i, w razie potrzeby, oflagować ją jako komunikat "Trująca" w celu zbadania przez wysłanie go do oddzielnej kolejki. Aby uzyskać więcej informacji o odczytywaniu komunikatów w kolejce i sprawdzaniu liczby odgałęzień, zobacz [pobieranie komunikatów](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Niektóre błędy z magazynu tabel i magazynu kolejek są błędami przejściowymi, a aplikacja kliencka powinna zawierać odpowiednią logikę ponawiania, aby je obsłużyć.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie zapewnia izolacji transakcji. Na przykład klient może odczytać **bieżące** i **archiwalne** tabele, gdy rola proces roboczy przeprowadzono między krokami 4-5 na diagramie i widzi niespójny widok danych. Dane będą zgodne po pewnym czasie.  
* Musisz się upewnić, że kroki 4-5 są idempotentne w celu zapewnienia spójności ostatecznej.  
* Możesz skalować rozwiązanie przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy użytkownik chce zagwarantować spójność między jednostkami, które istnieją w różnych partycjach lub tabel. Ten wzorzec można rozwinąć, aby zapewnić spójność dla operacji w ramach magazynu tabel i magazynu obiektów blob oraz innych źródeł danych niezwiązanych z usługą Azure Storage, takich jak baza danych lub system plików.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Scal lub Zamień](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacja transakcji jest ważna dla Twojego rozwiązania, rozważ przeprojektowanie tabel, aby umożliwić korzystanie z EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki przy użyciu wartości `PartitionKey` i `RowKey`. Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu zapytania punktowego. Na przykład przy użyciu poniższej struktury tabeli aplikacja kliencka może efektywnie pobrać poszczególne jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (`PartitionKey` i `RowKey`).  

![Ilustracja jednostki Employee][13]

Jeśli chcesz również mieć możliwość pobrania listy jednostek pracowników na podstawie wartości innej nieunikatowej właściwości, takiej jak nazwisko, należy użyć mniej wydajnego skanowania partycji. To skanowanie wyszukuje dopasowania, zamiast używać indeksu, aby wyszukiwać je bezpośrednio. Wynika to z faktu, że magazyn tabel nie udostępnia indeksów pomocniczych.  

#### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska z poprzednią strukturą jednostki, należy zachować listy identyfikatorów pracowników. Jeśli chcesz pobrać jednostki pracownika z określoną nazwiskiem, np. Nowak, należy najpierw znaleźć listę identyfikatorów pracowników dla pracowników, którzy mają nazwisko Kowalski, a następnie pobrać te jednostki pracowników. Istnieją trzy główne opcje przechowywania list identyfikatorów pracowników:  

* Użyj magazynu obiektów BLOB.  
* Utwórz jednostki indeksu w jednej partycji jako jednostki pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

Opcja 1. Korzystanie z magazynu obiektów BLOB  

Utwórz obiekt BLOB dla każdej unikatowej nazwy i w każdym z nich przechowuj listę wartości `PartitionKey` (dział) i `RowKey` (identyfikator pracownika) dla pracowników, którzy mają tę ostatnią nazwę. Po dodaniu lub usunięciu pracownika upewnij się, że zawartość odpowiedniego obiektu BLOB jest ostatecznie spójna z jednostkami pracownika.  

Opcja 2. Tworzenie jednostek indeksu w tej samej partycji  

Użyj obiektów index, które przechowują następujące dane:  

![Ilustracja przedstawiająca jednostkę pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tej samej nazwie][14]

Właściwość `EmployeeIDs` zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w `RowKey`.  

Poniższe kroki przedstawiają proces, który należy wykonać podczas dodawania nowego pracownika. W tym przykładzie dodajemy pracownika o IDENTYFIKATORze 000152 i nazwisko Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu o wartości `PartitionKey` "Sales" i wartości `RowKey` "Nowak". Zapisz element ETag to jednostki do użycia w kroku 2.  
2. Utwórz transakcję grupy jednostek (czyli operację wsadową), która wstawia nową jednostkę pracownika (`PartitionKey` wartość "Sales" i `RowKey` wartość "000152") i aktualizuje jednostkę indeksu (`PartitionKey` wartość "Sales" i `RowKey` wartość "Nowak"). EGT to przez dodanie nowego identyfikatora pracownika do listy w polu Idpracownikas. Aby uzyskać więcej informacji na temat EGTs, zobacz [transakcje grupy jednostek](#entity-group-transactions).  
3. Jeśli EGT nie powiedzie się z powodu optymistycznego błędu współbieżności (oznacza to, że ktoś inny zmodyfikował jednostkę indeksu), należy zacząć od nowa w kroku 1.  

Jeśli używasz drugiej opcji, możesz użyć podobnej metody do usuwania pracownika. Zmiana nazwiska pracownika jest nieco bardziej złożona, ponieważ należy uruchomić EGT, który aktualizuje trzy jednostki: jednostki pracownika, jednostki indeksu dla starej nazwy i jednostki indeksu dla nowej nazwy. Przed wprowadzeniem jakichkolwiek zmian należy pobrać każdą jednostkę, aby pobrać wartości ETag, których można użyć do przeprowadzenia aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy konieczne jest wyszukanie wszystkich pracowników z określoną nazwiskiem w dziale. W tym przykładzie szukamy wszystkich pracowników o nazwisku Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu o wartości `PartitionKey` "Sales" i wartości `RowKey` "Nowak".  
2. Przeanalizuj listę identyfikatorów pracowników w polu `EmployeeIDs`.  
3. Jeśli potrzebujesz dodatkowych informacji na temat każdego z tych pracowników (takich jak adresy e-mail), Pobierz każdą z jednostek pracowników przy użyciu wartości `PartitionKey` "Sales" i wartości `RowKey` z listy pracowników uzyskanych w kroku 2.  

Opcja 3: tworzenie jednostek indeksu w oddzielnej partycji lub tabeli  

W przypadku tej opcji Użyj jednostek indeksu, które przechowują następujące dane:  

![Ilustracja przedstawiająca jednostkę pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tej samej nazwie][15]

Właściwość `EmployeeIDs` zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w `RowKey`.  

Nie można użyć EGTs do zachowania spójności, ponieważ jednostki indeksu znajdują się w oddzielnej partycji od jednostek pracowników. Upewnij się, że jednostki indeksu są ostatecznie spójne z jednostkami pracowników.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań, aby pobrać pasujące jednostki: jeden do zapytania o jednostki indeksu w celu uzyskania listy wartości `RowKey`, a następnie wysyła zapytanie w celu pobrania każdej jednostki na liście.  
* Ponieważ każda jednostka ma maksymalny rozmiar 1 MB, opcja 2 i opcja 3 w rozwiązaniu założono, że lista identyfikatorów pracowników dla każdej z określonych nazwisk nie ma więcej niż 1 MB. Jeśli lista identyfikatorów pracowników może mieć rozmiar większy niż 1 MB, użyj opcji 1 i Zapisz dane indeksu w usłudze BLOB Storage.  
* Jeśli używasz opcji 2 (przy użyciu EGTs do obsługi dodawania i usuwania pracowników i zmieniania nazwiska pracownika), należy oszacować, czy liczba transakcji będzie zbliżać się do ograniczeń skalowalności określonej partycji. W takim przypadku należy wziąć pod uwagę ostatecznie spójne rozwiązanie (opcja 1 lub 3). Te kolejki służą do obsługi żądań aktualizacji i umożliwiają przechowywanie jednostek indeksu w oddzielnej partycji od jednostek pracowników.  
* Opcja 2 w tym rozwiązaniu zakłada, że chcesz wyszukać według nazwiska w ramach działu. Na przykład chcesz pobrać listę pracowników o nazwisku Nowak w dziale sprzedaży. Jeśli chcesz mieć możliwość wyszukania wszystkich pracowników o nazwisku Nowak w całej organizacji, użyj opcji 1 lub Option 3.
* Możesz zaimplementować rozwiązanie oparte na kolejce, które zapewnia spójność ostateczną. Aby uzyskać więcej informacji, zobacz [wzorce ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz wyszukać zestaw jednostek, które współużytkują wspólną wartość właściwości, na przykład wszystkich pracowników o nazwisku Nowak.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Wzorzec denormalizacja
Ze sobą łączyć powiązanych danych w jednej jednostce umożliwiają można pobrać wszystkie dane potrzebne przy użyciu zapytania pojedynczy punkt.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizuje dane w celu usunięcia duplikatów, które występuje, gdy zapytania pobierają dane z wielu tabel. Jeśli użytkownik normalizować dane w tabelach platformy Azure, upewnij się wiele rund od klienta do serwera w celu pobrania danych powiązanych. Na przykład w przypadku poniższej struktury tabeli potrzebne są dwie rundy, aby pobrać szczegóły dotyczące działu. Jedna podróż pobiera jednostkę działu, która zawiera identyfikator Menedżera, a druga — w przypadku pobierania szczegółów kierownika w jednostce pracownika.  

![Ilustracja przedstawiająca jednostkę działu i jednostkę pracownika][16]

#### <a name="solution"></a>Rozwiązanie
Zamiast przechowywać dane w dwóch oddzielnych jednostkach, zdenormalizowanie danych i przechowywać kopię szczegółach menedżera w jednostce działu. Na przykład:  

![Ilustracja nieznormalizowanej i połączonej jednostki działu][17]

W przypadku jednostek działu przechowywanych z tymi właściwościami można teraz pobrać wszystkie wymagane szczegóły dotyczące działu przy użyciu zapytania punktowego.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje pewien koszt obciążenie związanych z przechowywaniem niektóre dane w dwa razy. Korzyść wydajności wynikająca z mniejszej liczby żądań do usługi Table Storage zazwyczaj zmniejsza krańcowy wzrost kosztów magazynowania. Dodatkowo ten koszt jest częściowo przesunięty o obniżkę liczby transakcji wymaganych do pobrania szczegółów działu.  
* Należy zachować spójność dwie jednostki, które przechowują informacje o menedżerów. Problem ze spójnością można obsłużyć, korzystając z EGTs do aktualizowania wielu jednostek w pojedynczej niepodzielnej transakcji. W takim przypadku jednostka działu i jednostka Employee dla menedżera działu są przechowywane w tej samej partycji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz często do wyszukania powiązanych informacji. Ten wzorzec pozwala zmniejszyć liczbę zapytań, które należy wprowadzić klienta, aby pobierać dane, które wymaga.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Wzorzec klucza złożona (c)
Użyj wartości złożonych `RowKey`, aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych można używać sprzężeń w zapytaniach, aby zwrócić powiązane fragmenty danych do klienta w jednym zapytaniu. Na przykład możesz użyć identyfikatora pracownika, aby wyszukać listę powiązanych jednostek, które zawierają dane dotyczące wydajności i przeglądu dla tego pracownika.  

Załóżmy, że przechowujesz jednostki pracowników w usłudze Table Storage, korzystając z następującej struktury:  

![Ilustracja jednostki Employee][18]

Należy również przechowywać dane historyczne dotyczące przeglądów i wydajności dla każdego roku, dla których pracownik pracował w organizacji, i musi mieć dostęp do tych informacji przez rok. Jedną z opcji jest, aby utworzyć inną tabelę, która przechowuje jednostki o następującej strukturze:  

![Ilustracja przedstawiająca jednostkę przeglądu pracownika][19]

Korzystając z tego podejścia, możesz zdecydować się na zduplikowanie niektórych informacji (takich jak imię i nazwisko) w nowej jednostce, aby umożliwić pobieranie danych za pomocą pojedynczego żądania. Nie można jednak zachować silnej spójności, ponieważ nie można użyć EGT do aktualizowania dwóch jednostek.  

#### <a name="solution"></a>Rozwiązanie
Zapisz nowy typ jednostki w oryginalnej tabeli przy użyciu jednostek o następującej strukturze:  

![Ilustracja przedstawiająca jednostkę pracownika z kluczem złożonym][20]

Zwróć uwagę, jak `RowKey` jest teraz kluczem złożonym, składającym się z identyfikatora pracownika i roku na dane przeglądu. Pozwala to na pobieranie wydajności pracownika i przeglądanie danych za pomocą pojedynczego żądania dla jednej jednostki.  

Poniższy przykład przedstawia, jak pobrać przeglądanie danych dla danego pracownika (na przykład 000123 pracownik działu sprzedaży):  

$filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "empid_000123") i (RowKey lt "empid_000124") & $select = RowKey, ocena menedżera, ocena elementów równorzędnych, komentarze  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego znaku separatora, który ułatwia analizowanie wartości `RowKey`: na przykład **000123_2012**.  
* Ta jednostka jest również przechowywana w tej samej partycji co inne jednostki, które zawierają powiązane dane dla tego samego pracownika. Oznacza to, że można użyć EGTs, aby zachować silną spójność.
* Należy zastanowić się, jak często będą wykonywane zapytania o dane, aby określić, czy ten wzorzec jest odpowiedni. Na przykład, Jeśli uzyskujesz dostęp do danych przeglądu rzadko, a główne dane pracowników często, powinny być przechowywane jako osobne jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy chcesz przechować jeden lub więcej powiązanych jednostek to zapytanie można często.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Wzorzec ogona dziennika
Pobierz *n* obiektów ostatnio dodanych do partycji przy użyciu wartości `RowKey`, która sortuje się w odwrotnej kolejności daty i godziny.  

> [!NOTE]
> Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. W ten sposób, chociaż ten wzorzec jest odpowiedni dla magazynu tabel, nie jest odpowiedni dla Azure Cosmos DB. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Table Storage platformy Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymogiem jest będzie można go pobrać ostatnio utworzone jednostki, na przykład dziesięć ostatnich wydatków oświadczenia złożone przez pracownika. Zapytania tabeli obsługują `$top` operacji zapytania, aby zwrócić pierwsze *n* jednostek z zestawu. Brak równoważnej operacji zapytania do zwrócenia ostatnich *n* jednostek w zestawie.  

#### <a name="solution"></a>Rozwiązanie
Przechowuj jednostki przy użyciu `RowKey`, które w sposób naturalny sortuje w odwrotnej kolejności daty/godziny, więc ostatni wpis jest zawsze pierwszym z nich w tabeli.  

Na przykład aby można było pobrać dziesięć ostatnich oświadczenia wydatków przesłane przez pracownika, służy wartość odwrotnej osi pochodzi od bieżącej daty/godziny. Poniższy C# przykład kodu przedstawia jeden ze sposobów tworzenia odpowiednich "odwróconych" znaczników dla `RowKey`, które są sortowane od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić do wartości daty/godziny przy użyciu następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie dotyczące tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zapewnić, że wartość ciągu będzie sortowana zgodnie z oczekiwaniami, należy uzupełnić wartość odwrotnej osi.  
* Należy pamiętać o cele skalowalności na poziomie partycji. Należy zachować ostrożność, aby nie tworzyć partycji punktu aktywnego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz uzyskać dostęp do jednostek w odwrotnej kolejności daty/godziny lub gdy potrzebujesz dostępu do ostatnio dodanych jednostek.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Poprzedź/Dołącz Antywzorzec](#prepend-append-anti-pattern)  
* [Pobierz jednostki](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Wzorzec usuwania duża
Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki w celu jednoczesnego usunięcia w oddzielnych tabelach. Obiekty można usunąć, usuwając tabelę.  

#### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji, Usuń stare dane, nie musi już być dostępne dla aplikacji klienckich, lub aplikacja ma zarchiwizować na innym urządzeniu pamięci masowej. Dane te są zwykle identyfikowane według daty. Na przykład istnieje wymóg usuwania rekordów wszystkich żądań logowania, które są starsze niż 60 dni.  

Jednym z możliwych wzorów jest użycie daty i godziny żądania logowania w `RowKey`:  

![Ilustracja przedstawiająca jednostkę próby zalogowania][21]

To podejście pozwala uniknąć hotspotów partycji, ponieważ aplikacja może wstawiać i usuwać jednostki logowania dla każdego użytkownika w oddzielnej partycji. Jednak takie podejście może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek. Najpierw należy przeprowadzić skanowanie tabeli, aby zidentyfikować wszystkie jednostki do usunięcia, a następnie usunąć każdą starą jednostkę. Można zmniejszyć liczby rund do serwera, wymagane, aby usunąć stare jednostek, przetwarzanie wsadowe wielu żądań delete służących do EGTs.  

#### <a name="solution"></a>Rozwiązanie
Dla każdego dnia prób logowania, należy użyć osobnej tabeli. Możesz użyć powyższego projektu jednostki, aby uniknąć hotspotów podczas wstawiania jednostek. Usunięcie starych jednostek jest teraz jednym z pytań dotyczących usuwania jednej tabeli codziennie (pojedynczej operacji magazynu), zamiast znajdowania i usuwania setek i tysięcy poszczególnych jednostek logowania każdego dnia.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy projekt obsługuje inne sposoby używania danych przez aplikację, takie jak wyszukiwanie konkretnych jednostek, łączenie z innymi danymi lub generowanie zagregowanych informacji?  
* Projekt uniknąć punktów aktywnych, wstawiając nowych jednostek?  
* Oczekiwane opóźnienie, jeśli chcesz ponownie użyć tej samej nazwy tabeli po jego usunięciu. Zaleca się zawsze używać nazw unikatowej tabeli.  
* Należy oczekiwać pewnej szybkości ograniczania przy pierwszym użyciu nowej tabeli, podczas gdy usługa Table Storage uzyskuje wzorce dostępu i dystrybuuje partycje między węzłami. Należy rozważyć, jak często należy utworzyć nowe tabele.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli masz dużą liczbę jednostek, które należy usunąć w tym samym czasie.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Modyfikuj jednostki](#modify-entities)  

### <a name="data-series-pattern"></a>Wzorzec serii danych
Seria kompletne dane Store w pojedynczą jednostkę, aby zminimalizować liczbę żądań, które wprowadzasz.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz polega na dla aplikacji, aby przechowywać serię danych, które zazwyczaj musi pobrać wszystkie na raz. Na przykład aplikacja może rejestrować liczbę komunikatów wiadomości Błyskawicznych każdemu pracownikowi wysyła co godzinę, a następnie te informacje służą do wykreślenia liczbę komunikatów każdego użytkownika wysłanych w ciągu poprzednich 24 godzin. Jeden projekt może być przechowywanie podmioty 24 dla każdego pracownika:  

![Ilustracja przedstawiająca jednostkę statystyk komunikatów][22]

W tym projekcie można łatwo zlokalizować i aktualizować jednostki do zaktualizowania dla każdego pracownika, zawsze wtedy, gdy aplikacja musi zaktualizować wartość licznika komunikatów. Jednak aby pobrać informacje do wykreślenia wykresu aktywności w poprzednich 24 godzin, możesz pobrać jednostki 24.  

#### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z osobną właściwością do przechowywania liczby komunikatów dla każdej godziny:  

![Ilustracja przedstawiająca jednostkę statystyki komunikatu z właściwościami rozdzielonymi][23]

W tym projekcie można użyć operacji scalania, można zaktualizować liczba komunikatów dla pracownika określonej godziny. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli kompletna seria danych nie mieści się w pojedynczej jednostce (jednostka może mieć do 252 właściwości), użyj alternatywnego magazynu danych, takiego jak obiekt BLOB.  
* Jeśli wielu klientów jednocześnie aktualizuje jednostkę, użyj elementu **ETag** , aby zaimplementować optymistyczną współbieżność. Jeśli masz wielu klientów, może wystąpić wysoka rywalizacja.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba zaktualizować i pobrania serii danych skojarzonych z pojedynczą jednostkę.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scal lub Zamień](#merge-or-replace)  
* [Wzorzec z ostatecznie spójnymi transakcjami](#eventually-consistent-transactions-pattern) (Jeśli przechowujesz serie danych w obiekcie BLOB)  

### <a name="wide-entities-pattern"></a>Wzorzec szerokiego jednostek
Wiele jednostek fizycznych umożliwiają przechowywanie jednostek logicznych z więcej niż 252 właściwości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka może mieć nie więcej niż 252 właściwości (z wyłączeniem obowiązkowych właściwości systemu) i nie może zawierać więcej niż 1 MB danych. W relacyjnej bazie danych, zazwyczaj można obejść limity rozmiaru wiersza przez dodanie nowej tabeli i wymuszenie relacji 1-do-1 między nimi.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą usługi Table Storage można przechowywać wiele jednostek do reprezentowania pojedynczego dużego obiektu biznesowego, który ma więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika przez ostatnie 365 dni, możesz użyć następującego projektu, który używa dwóch jednostek z różnymi schematami:  

![Ilustracja przedstawiająca jednostkę statystyki komunikatu z Rowkey 01 i jednostką statystyki komunikatu z Rowkey 02][24]

Jeśli musisz wprowadzić zmianę, która wymaga zaktualizowania obu jednostek, aby były ze sobą zsynchronizowane, można użyć EGT. W przeciwnym razie można użyć operacji scalania pojedynczego można zaktualizować liczba komunikatów dla określonego dnia. Aby pobrać wszystkie dane dla danego pracownika, należy pobrać obie jednostki. Można to zrobić z dwoma wydajnymi żądaniami, które używają zarówno wartości `PartitionKey`, jak i `RowKey`.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Przed podjęciem decyzji o sposobie wdrożenia tego wzorca należy wziąć pod uwagę następujący punkt:  

* Trwa pobieranie całą jednostkę logiczną obejmuje co najmniej dwie transakcje magazynu: jedną do pobrania każda jednostka fizyczna.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz przechowywać jednostki, których rozmiar lub liczba właściwości przekraczają limity dla poszczególnych jednostek w magazynie tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Scal lub Zamień](#merge-or-replace)

### <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Przechowywanie dużych wartości właściwości przy użyciu magazynu obiektów BLOB.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka nie może przechowywać więcej niż 1 MB danych. Jeśli jedna lub kilka właściwości magazynu wartości, które powodują, że całkowity rozmiar jednostki przekracza tę wartość, nie można przechowywać całej jednostki w magazynie tabel.  

#### <a name="solution"></a>Rozwiązanie
Jeśli rozmiar jednostki przekracza 1 MB, ponieważ co najmniej jedna z właściwości zawiera dużą ilość danych, można przechowywać dane w magazynie obiektów blob, a następnie przechowywać adres obiektu BLOB we właściwości w jednostce. Na przykład można przechowywać zdjęcie pracownika w usłudze BLOB Storage i przechowywać link do zdjęcia we właściwości `Photo` jednostki pracownika:  

![Ilustracja przedstawiająca jednostkę pracownika z ciągiem dla zdjęć wskazujących na obiekt BLOB Storage][25]

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczną między jednostką w magazynie tabel i danymi w usłudze BLOB Storage, należy użyć [wzorca spójnych transakcji](#eventually-consistent-transactions-pattern) do obsługi jednostek.
* Pobieranie całą jednostkę obejmuje co najmniej dwie transakcje magazynowe: jeden do pobierania jednostki, a drugi do pobierania danych obiektów blob.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz przechowywać jednostki, których rozmiar przekracza limity dla poszczególnych jednostek w magazynie tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Dołączana/dołączanie zapobieganie wzorzec
W przypadku dużej ilości operacji wstawiania zwiększaj skalowalność poprzez rozłożenie operacji wstawiania na wiele partycji.  

#### <a name="context-and-problem"></a>Kontekst i problem
Dołączenie lub dołączanie jednostek do jednostki przechowywanej zwykle powoduje w aplikacji, dodając nowe jednostki do partycji pierwszej lub ostatniej sekwencji partycji. W takim przypadku wszystkie operacje wstawiania w dowolnym konkretnym czasie są realizowane w tej samej partycji, tworząc punkt aktywny. Zapobiega to korzystaniu z funkcji równoważenia obciążenia w usłudze Table Storage w wielu węzłach, co może spowodować, że aplikacja osiągnie elementy docelowe skalowalności dla partycji. Rozważmy na przykład przypadek aplikacji, która rejestruje dostęp do sieci i zasobów przez pracowników. Struktura jednostki, taka jak następujące, może spowodować, że partycja bieżąca godziny staje się hotspotem, jeśli ilość transakcji osiągnie miejsce docelowe skalowalności dla pojedynczej partycji:  

![Ilustracja jednostki Employee][26]

#### <a name="solution"></a>Rozwiązanie
Następująca alternatywna Struktura jednostek pozwala uniknąć hotspotu na określonej partycji, ponieważ aplikacja rejestruje zdarzenia:  

![Ilustracja przedstawiająca jednostkę pracownika z RowKey złożonym rok, miesiąc, dzień, godzinę i identyfikator zdarzenia][27]

Należy zauważyć, że w tym przykładzie zarówno `PartitionKey`, jak i `RowKey` są kluczami złożonymi. `PartitionKey` używa zarówno identyfikatora działu, jak i pracownika do dystrybuowania rejestrowania między wieloma partycjami.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Alternatywne struktury klucza, który pozwala uniknąć tworzenia partycji w warstwie gorąca na operacje wstawiania efektywnie obsługuje zapytania, który sprawia, że Twoja aplikacja kliencka?  
* Czy Przewidywana ilość transakcji oznacza, że najkorzystniej osiągniesz elementy docelowe skalowalności dla danej partycji i że zostanie ograniczona przez magazyn tabel?  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać dodawania lub dołączania antywzorców, gdy ilość transakcji prawdopodobnie spowoduje ograniczenie szybkości przez magazyn tabel podczas uzyskiwania dostępu do partycji gorąca.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec końca dziennika](#log-tail-pattern)  
* [Modyfikuj jednostki](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Wzorzec ochrony danych dziennika
Zazwyczaj należy używać magazynu obiektów BLOB zamiast magazynu tabel do przechowywania danych dziennika.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy przypadek użycia dla danych dziennika polega na pobraniu wybranych wpisów dziennika dla określonego zakresu dat/godzin. Na przykład, chcesz znaleźć wszystkie komunikaty o błędach i krytyczne, które są rejestrowane przez aplikację między 15:04 a 15:06 w określonym dniu. Nie chcesz używać daty i godziny komunikatu dziennika, aby określić partycję, do której zapisywane są jednostki dziennika. Powoduje to, że w danym momencie wszystkie jednostki dziennika będą współużytkować tę samą `PartitionKey` wartość (patrz prefiks [/dołączenie antywzorców](#prepend-append-anti-pattern)). Na przykład poniższy schemat jednostki dla komunikatu dziennika prowadzi do aktywnej partycji, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika do partycji dla bieżącej daty i godziny:  

![Ilustracja przedstawiająca obiekt komunikatu dziennika][28]

W tym przykładzie `RowKey` obejmuje datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są sortowane w kolejności daty/godziny. `RowKey` również zawiera identyfikator komunikatu, w przypadku gdy wiele komunikatów dziennika ma taką samą datę i godzinę.  

Innym podejściem jest użycie `PartitionKey`, która zapewnia, że aplikacja zapisuje komunikaty w różnych partycjach. Na przykład, jeśli źródło komunikatu dziennika zapewnia sposób dystrybucji komunikatów w wielu partycjach, można użyć następującego schematu jednostki:  

![Ilustracja przedstawiająca obiekt komunikatu dziennika][29]

Jednak problem z tym schematem polega na tym, że pobranie wszystkich komunikatów dziennika dla określonego przedziału czasu wymaga przeszukania każdej partycji w tabeli.

#### <a name="solution"></a>Rozwiązanie
W poprzedniej sekcji wyróżniono problem polegający na tym, że próbujesz użyć magazynu tabel do przechowywania wpisów dziennika i sugerowanych dwóch niezadowalających projektów. Jedno rozwiązanie prowadzi do gorącej partycji z ryzykiem, w którym są zapisywane komunikaty dziennika zapisu wydajności. Inne rozwiązanie spowodowało niską wydajność zapytań, ponieważ wymaga skanowania każdej partycji w tabeli w celu pobrania komunikatów dziennika dla określonego przedziału czasu. Usługa BLOB Storage oferuje lepsze rozwiązanie dla tego typu scenariusza i jest to sposób, w jaki Analiza usługi Azure Storage przechowuje zbierane dane dziennika.  

W tej sekcji przedstawiono sposób przechowywania danych dziennika w usłudze BLOB Storage w usłudze Storage Analytics, ponieważ ilustracja tego podejścia do przechowywania danych, które są zwykle wykonywane zapytania według zakresu.  

W usłudze Storage Analytics są przechowywane komunikaty dziennika w formacie rozdzielanym w wielu obiektach Blob. Rozdzielany format ułatwia aplikacji klienckiej do analizowania danych w komunikacie dziennika.  

Analiza magazynu używa konwencji nazewnictwa obiektów blob, które umożliwiają znalezienie obiektu BLOB (lub obiektów BLOB), które zawierają komunikaty dziennika, dla których odbywa się wyszukiwanie. Na przykład obiekt BLOB o nazwie "queue/2014/07/31/1800/000001. log" zawiera komunikaty dziennika, które są powiązane z usługą kolejki przez godzinę, począwszy od dnia 18:00 lipca 2014. "000001" oznacza, że jest pierwszy plik dziennika dla tego okresu. Analiza magazynu rejestruje także sygnatury czasowe pierwszych i ostatnich komunikatów dziennika przechowywanych w pliku w ramach metadanych obiektu BLOB. Interfejs API usługi BLOB Storage umożliwia lokalizowanie obiektów BLOB w kontenerze na podstawie prefiksu nazwy. Aby zlokalizować wszystkie obiekty blob zawierające dane dziennika kolejki dla godziny rozpoczynającej się o 18:00, można użyć prefiksu "queue/2014/07/31/1800".  

Usługa Storage Analytics buforuje komunikaty dziennika wewnętrznie, a następnie okresowo aktualizuje odpowiedni obiekt BLOB lub tworzy nowy z najnowszymi partiami wpisów dziennika. Zmniejsza to liczbę zapisów, które muszą zostać wykonane w usłudze BLOB Storage.  

Jeśli wdrażasz podobne rozwiązanie w swojej aplikacji, weź pod uwagę sposób zarządzania handlem między niezawodnością a kosztami i skalowalnością. Innymi słowy, należy oszacować efekt zapisu każdego wpisu dziennika do magazynu obiektów blob, w porównaniu do buforowania aktualizacji w aplikacji i zapisywania ich w magazynie obiektów BLOB w partiach.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie przechowywania danych dziennika, należy rozważyć następujące kwestie:  

* W przypadku utworzenia projektu tabeli, który pozwala uniknąć potencjalnych partycji, może się okazać, że nie można wydajnie uzyskać dostępu do danych dziennika.  
* Aby przetwarzać dane dziennika, klient często musi załadować wiele rekordów.  
* Mimo że dane dziennika są często strukturalne, usługa BLOB Storage może być lepszym rozwiązaniem.  

### <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, mieć na uwadze podczas implementowania wzorców opisanych w poprzednich sekcjach. Większość w tej sekcji używa przykłady napisane w języku C#, korzystających z biblioteki klienta usługi Storage (wersja 4.3.0 w czasie pisania).  

### <a name="retrieve-entities"></a>Pobierz jednostki
Jak opisano w temacie [projekt sekcji dotyczącej wykonywania zapytań](#design-for-querying), najbardziej wydajnym zapytaniem jest zapytanie punktowe. Jednak w niektórych scenariuszach może być konieczne pobranie wielu jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek przy użyciu biblioteki klienta usługi Storage.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Uruchamianie zapytania punktu przy użyciu biblioteki klienta usługi Storage
Najprostszym sposobem uruchomienia zapytania punktu jest użycie operacji **Pobierz** tabelę. Jak pokazano w poniższym C# fragmencie kodu, ta operacja pobiera jednostkę o `PartitionKey` wartości "Sales" i `RowKey` wartości "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekiwana jest jednostka pobierana z typu `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Pobieranie wielu jednostek przy użyciu LINQ
Można pobrać wiele jednostek przy użyciu LINQ z biblioteką klienta usługi Storage i określić zapytanie z klauzulą **WHERE** . Aby uniknąć skanowania tabeli, należy zawsze uwzględnić wartość `PartitionKey` w klauzuli WHERE, a jeśli to możliwe, `RowKey` wartość, aby uniknąć skanowania tabeli i partycji. Magazyn tabel obsługuje ograniczony zbiór operatorów porównania (większe niż, większe niż lub równe, mniejsze niż, mniejsze niż lub równe, równe i nierówne) do użycia w klauzuli WHERE. Poniższy C# fragment kodu znajduje wszystkich pracowników, których nazwisko zaczyna się od "B" (przy założeniu, że `RowKey` przechowuje nazwisko) w dziale sprzedaży (przy założeniu, że `PartitionKey` przechowuje nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Zwróć uwagę, jak zapytanie określa zarówno `RowKey`, jak i `PartitionKey`, aby zapewnić lepszą wydajność.  

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
> Przykład zagnieżdża wiele metod `CombineFilters`, aby uwzględnić trzy warunki filtru.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Pobieranie dużej liczby jednostek z zapytania
Optymalne zapytanie zwraca indywidualną jednostkę na podstawie wartości `PartitionKey` i `RowKey` wartość. Jednak w niektórych scenariuszach może być wymagane zwrócenie wielu jednostek z jednej partycji lub nawet z wielu partycji. W takich scenariuszach, należy zawsze w pełni przetestować wydajność aplikacji.  

Zapytanie dotyczące magazynu tabel może zwrócić maksymalnie 1 000 jednostek jednocześnie i działać przez maksymalnie pięć sekund. Magazyn tabel zwraca token kontynuacji, aby umożliwić aplikacji klienckiej żądanie następnego zestawu jednostek, jeśli którykolwiek z następujących warunków jest spełniony:

- Zestaw wyników zawiera więcej niż 1 000 jednostek.
- Zapytanie nie zostało ukończone w ciągu pięciu sekund.
- Zapytanie przecina granicę partycji. 

Aby uzyskać więcej informacji na temat działania tokenów kontynuacji, zobacz [limit czasu zapytania i podział na strony](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Jeśli używasz biblioteki klienta usługi Storage, może ona automatycznie obsługiwać tokeny kontynuacji, ponieważ zwraca jednostki z magazynu tabel. Na przykład poniższy C# przykładowy kod automatycznie obsługuje tokeny kontynuacji, jeśli magazyn tabeli zwróci je w odpowiedzi:  

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

Poniższy kod C# obsługuje tokeny kontynuacji jawnie:  

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

Korzystając z kontynuacji tokenów jawnie, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład, jeśli aplikacja kliencka umożliwia użytkownikom przeglądanie jednostek przechowywanych w tabeli, użytkownik może zrezygnować ze strony przez wszystkie jednostki pobrane przez zapytanie. Aplikacja będzie używać tokenu kontynuacji do pobierania następnego segmentu, gdy użytkownik zakończył stronicowanie przez wszystkie jednostki w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Można ograniczyć ilość danych pobieranych z magazynu tabel i przenoszonych przez sieć.  
* Można wykonywać asynchroniczne operacje we/wy na platformie .NET.  
* Token kontynuacji można serializować do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zwykle zwraca segment zawierający 1 000 jednostek, chociaż może zawierać mniej. Ma to również zastosowanie w przypadku ograniczenia liczby wpisów zwracanych przez **zapytanie w celu** zwrócenia pierwszych n jednostek, które pasują do kryteriów wyszukiwania. Magazyn tabel może zwracać segment zawierający mniej niż n jednostek wraz z tokenem kontynuacji, aby umożliwić pobieranie pozostałych jednostek.  
> 
> 

Poniższy kod C# pokazuje sposób modyfikowania liczby jednostek zwróconych w segmencie:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekcja po stronie serwera
Pojedynczy element może mieć maksymalnie 255 właściwości i mieć rozmiar maksymalnie 1 MB. Podczas wykonywania zapytania dotyczącego tabeli i pobierania jednostek mogą nie być potrzebne wszystkie właściwości i mogą uniknąć niepotrzebnych transferów danych (w celu zmniejszenia opóźnień i kosztów). Projekcja po stronie serwera umożliwia transfer tylko właściwości, których potrzebujesz. Poniższy przykład pobiera tylko Właściwość `Email` (wraz z `PartitionKey`, `RowKey`, `Timestamp`i `ETag`) z jednostek wybranych przez zapytanie.  

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

Zauważ, że wartość `RowKey` jest dostępna, nawet jeśli nie jest uwzględniona na liście właściwości do pobrania.  

### <a name="modify-entities"></a>Modyfikowanie jednostek
Biblioteka klienta magazynu umożliwia modyfikowanie jednostek przechowywanych w usłudze Table Storage przez wstawianie, usuwanie i aktualizowanie jednostek. Za pomocą EGTs można wsadowo wiele operacji wstawiania, aktualizowania i usuwania, aby zmniejszyć liczbę wymaganych podróży i zwiększyć wydajność rozwiązania.  

Wyjątki zgłaszane, gdy biblioteka klienta magazynu uruchamia EGT zazwyczaj zawiera indeks jednostki, która spowodowała niepowodzenie wykonywania partii. Jest to przydatne podczas debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projekt wpływa na sposób Twoja aplikacja kliencka obsługi operacji współbieżności i aktualizacji.  

#### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa Table Storage implementuje optymistyczne kontrole współbieżności na poziomie poszczególnych jednostek dla operacji wstawiania, scalania i usuwania, chociaż istnieje możliwość, aby klient wymusił ominięcie tych sprawdzeń przez klienta. Aby uzyskać więcej informacji, zobacz [Zarządzanie współbieżnością w Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Scalanie lub zastępowanie
Metoda `Replace` klasy `TableOperation` zawsze zastępuje kompletną jednostkę w magazynie tabel. Jeśli nie dołączysz właściwości w żądaniu, gdy ta właściwość istnieje w przechowywanej jednostce, żądanie usunie tę właściwość z przechowywanej jednostki. Jeśli nie chcesz jawnie usunąć właściwość z jednostki przechowywanej musi zawierać dla każdej właściwości w żądaniu.  

Za pomocą metody `Merge` klasy `TableOperation` można zmniejszyć ilość danych wysyłanych do magazynu tabel, gdy chcesz zaktualizować jednostkę. Metoda `Merge` zastępuje wszystkie właściwości w przechowywanej jednostce wartościami właściwości z jednostki zawartej w żądaniu. Ta metoda pozostawia nienaruszone wszystkie właściwości w przechowywanej jednostce, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz duże jednostki i potrzebujesz jedynie zaktualizować niewielką liczbę właściwości w żądaniu.  

> [!NOTE]
> Metody `*Replace` i `Merge` kończą się niepowodzeniem, jeśli jednostka nie istnieje. Alternatywnie można użyć metod `InsertOrReplace` i `InsertOrMerge`, które tworzą nową jednostkę, jeśli nie istnieje.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Współpraca z niejednorodnymi typami jednostek
Magazyn tabel jest magazynem tabel bez *schematu* . Oznacza to, że pojedyncza tabela może przechowywać jednostki wielu typów, zapewniając doskonałą elastyczność w projekcie. W poniższym przykładzie pokazano tabelę przechowywania pracownika i działu jednostki:  

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

Każda jednostka musi nadal mieć wartości `PartitionKey`, `RowKey`i `Timestamp`, ale może mieć dowolny zestaw właściwości. Ponadto nie ma nic, aby wskazać typ jednostki, chyba że zdecydujesz się przechowywać te informacje w innym miejscu. Dostępne są dwie opcje do identyfikowania typu jednostki:  

* Dołącz typ jednostki do `RowKey` (lub prawdopodobnie `PartitionKey`). Na przykład `EMPLOYEE_000123` lub `DEPARTMENT_SALES` jako wartości `RowKey`.  
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
<th>Typ entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Pracownik</td>
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
<th>Typ entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Pracownik</td>
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
<th>Typ entityType</th>
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
<th>Typ entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Pierwsza opcja, w zależności od typu jednostki do `RowKey`, jest przydatna, jeśli istnieje możliwość, że dwie jednostki różnych typów mogą mieć taką samą wartość klucza. Grup jednostek z tego samego typu, ze sobą w partycji.  

Techniki omówione w tej sekcji są szczególnie istotne dla dyskusji na temat[relacji dziedziczenia](#inheritance-relationships).  

> [!NOTE]
> Rozważ uwzględnienie numeru wersji w wartości typ jednostki, aby umożliwić aplikacjom klienckim rozwijanie obiektów POCO i współpracują z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre z funkcji w bibliotece klienta usługi Storage, które ułatwiają pracę z wieloma typami jednostki w tej samej tabeli.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Pobieranie typów jednostek heterogenicznych
W przypadku korzystania z biblioteki klienta usługi Storage dostępne są trzy opcje pracy z wieloma typami jednostek.  

Jeśli znasz typ jednostki przechowywanej przy użyciu określonych wartości `RowKey` i `PartitionKey`, możesz określić typ jednostki podczas pobierania jednostki. Pokazano to w poprzednich dwóch przykładach, które pobierają jednostki typu `EmployeeEntity`: [Uruchom zapytanie punktu przy użyciu biblioteki klienta magazynu](#run-a-point-query-by-using-the-storage-client-library) i [Pobierz wiele jednostek przy użyciu LINQ](#retrieve-multiple-entities-by-using-linq).  

Druga opcja to użycie typu `DynamicTableEntity` (zbiór właściwości), a nie konkretnego typu jednostki POCO. Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby serializacji i deserializacji jednostki do typów .NET. Poniższy C# kod potencjalnie pobiera wiele jednostek różnych typów z tabeli, ale zwraca wszystkie jednostki jako wystąpienia `DynamicTableEntity`. Następnie używa właściwości `EntityType` do określenia typu każdej jednostki:  

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

Aby pobrać inne właściwości, należy użyć metody `TryGetValue` na właściwości `Properties` klasy `DynamicTableEntity`.  

Trzecią opcją jest łączenie przy użyciu typu `DynamicTableEntity` i wystąpienia `EntityResolver`. Dzięki temu można prowadzić do wielu typów POCO tego samego zapytania. W tym przykładzie delegat `EntityResolver` używa właściwości `EntityType` do rozróżnienia między dwoma typami jednostek zwracanymi przez zapytanie. Metoda `Resolve` używa delegata `resolver` do rozwiązywania wystąpień `DynamicTableEntity` z wystąpieniami `TableEntity`.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Modyfikowanie typów jednostek heterogenicznych
Nie musisz znać typu jednostki, aby ją usunąć, i zawsze znasz typ jednostki podczas jej wstawiania. Można jednak użyć typu `DynamicTableEntity` do zaktualizowania jednostki bez znajomości jej typu i bez użycia klasy jednostki POCO. Poniższy przykład kodu pobiera pojedynczą jednostkę i sprawdza, czy właściwość `EmployeeCount` istnieje przed jej aktualizacją.  

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

### <a name="control-access-with-shared-access-signatures"></a>Kontrola dostępu za pomocą sygnatur dostępu współdzielonego
Tokeny sygnatury dostępu współdzielonego (SAS) mogą być używane do włączania aplikacji klienckich do bezpośredniej modyfikacji (i zapytania) jednostek tabeli bez konieczności uwierzytelniania bezpośrednio w usłudze Table Storage. Zwykle istnieją trzy główne zalety za pomocą sygnatury dostępu Współdzielonego w Twojej aplikacji:  

* Nie jest konieczne dystrybuowanie klucza konta magazynu do niezabezpieczonej platformy (na przykład urządzenia przenośnego) w celu umożliwienia temu urządzeniu dostępu do jednostek w usłudze Table Storage i modyfikowania ich.  
* Można odciążyć część pracy, którą wykonują role sieci Web i procesu roboczego w zarządzaniu jednostkami. Można odciążyć urządzenia klienckie, takie jak komputery użytkowników końcowych i urządzenia przenośne.  
* Do klienta można przypisywać ograniczone i czasochłonne zbiory uprawnień (np. Zezwalanie na dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów SAS z magazynem tabel, zobacz [using Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Należy jednak nadal generować tokeny sygnatury dostępu współdzielonego, które przyznają aplikacji klienckiej jednostki w usłudze Table Storage. Zrób to w środowisku, które ma bezpieczny dostęp do kluczy konta magazynu. Zazwyczaj używasz rola sieć web lub procesu roboczego do generowania tokenów sygnatur dostępu Współdzielonego i dostarczaj je na potrzeby aplikacji klienckich, które muszą mieć dostęp do jednostek. Ponieważ jest nadal obciążenie związane z generowania i dostarczania tokeny sygnatur dostępu Współdzielonego do klientów, należy rozważyć, jak najlepiej Aby zmniejszyć to obciążenie, szczególnie w przypadku dużej liczby scenariuszy.  

Istnieje możliwość wygenerowania tokenu SAS, który udziela dostępu do podzbioru jednostek w tabeli. Domyślnie można utworzyć token sygnatury dostępu współdzielonego dla całej tabeli. Można jednak określić, że token SAS udziela dostępu do zakresu wartości `PartitionKey` lub zakresu wartości `PartitionKey` i `RowKey`. Można generować tokeny sygnatury dostępu współdzielonego dla poszczególnych użytkowników systemu, tak aby token SAS każdego użytkownika umożliwiał tylko dostęp do ich własnych jednostek w magazynie tabel.  

### <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Podana rozpraszania żądań na wielu partycjach możesz zwiększyć szybkość reakcji przepływność i klienta, za pomocą asynchronicznego lub równoległego zapytań.
Na przykład Niewykluczone, że wystąpienia roli co najmniej dwóch procesu roboczego uzyskiwania dostępu do tabel równolegle. Poszczególne role procesów roboczych mogą być odpowiedzialne za określone zestawy partycji lub po prostu mają wiele wystąpień roli procesu roboczego, z których każdy może uzyskać dostęp do wszystkich partycji w tabeli.  

W ramach wystąpienia klienta można zwiększyć przepływność przez wykonywanie operacji magazynu asynchronicznie. Biblioteki klienta usługi Storage ułatwia pisanie zapytania asynchroniczne i modyfikacji. Na przykład możesz zacząć od metody synchronicznej, która pobiera wszystkie jednostki w partycji, jak pokazano w poniższym C# kodzie:  

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

W tym przykładzie asynchronicznych można wyświetlić następujące zmiany z wersji synchronicznego:  

* Podpis metody zawiera teraz modyfikator `async` i zwraca wystąpienie `Task`.  
* Zamiast wywołania metody `ExecuteSegmented`, aby pobrać wyniki, metoda wywołuje teraz metodę `ExecuteSegmentedAsync`. Metoda używa modyfikatora `await` do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywoływać tę metodę wiele razy, z różnymi wartościami parametru `department`. Każde zapytanie jest uruchamiane w osobnym wątku.  

Brak asynchronicznej wersji metody `Execute` w klasie `TableQuery`, ponieważ interfejs `IEnumerable` nie obsługuje asynchronicznego wyliczania.  

Można także wstawianie, aktualizowanie i usuwanie jednostek asynchronicznie. Poniższy przykład C# pokazuje prosty, synchronicznej metody Wstawianie lub zastępowanie jednostki pracowników:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Możesz łatwo zmodyfikować ten kod, aby aktualizacja działała asynchronicznie, w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchronicznych można wyświetlić następujące zmiany z wersji synchronicznego:  

* Podpis metody zawiera teraz modyfikator `async` i zwraca wystąpienie `Task`.  
* Zamiast wywoływania metody `Execute` w celu zaktualizowania jednostki metoda wywołuje teraz metodę `ExecuteAsync`. Metoda używa modyfikatora `await` do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywoływać wiele metod asynchronicznych, takich jak ta, i każde wywołanie metody jest uruchamiane w osobnym wątku.  


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

