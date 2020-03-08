---
title: Projektowanie skalowalnych i wydajnych tabel w usłudze Azure Table Storage. | Microsoft Docs
description: Projektowanie skalowalnych i wydajnych tabel w usłudze Azure Table Storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 95272956da4567ec21e1c4603b88472e45373a39
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387084"
---
# <a name="design-scalable-and-performant-tables"></a>Projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę czynniki takie jak wydajność, skalowalność i koszt. Jeśli wcześniej zaprojektowano schematy dla relacyjnych baz danych, te zagadnienia są znane, ale chociaż istnieją różne podobieństwa między modelem magazynu Table service platformy Azure i modeli relacyjnych, istnieją także istotne różnice. Te różnice zwykle prowadzą do różnych projektów, które mogą odszukać, że są one intuicyjne lub niewłaściwe dla kogoś, kto zna relacyjne bazy danych, ale warto mieć sens, jeśli projektujesz dla magazynu NoSQL klucz/wartość, takiego jak Table service platformy Azure. Wiele z różnic w projekcie odzwierciedla fakt, że Table service zaprojektowano w celu obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek (lub wierszy w terminologii relacyjnej bazy danych) lub zestawy danych, które muszą obsługiwać wysoką transakcję objętości. W związku z tym należy myśleć o sposobie przechowywania danych i zrozumieć, jak działa Table service. Dobrze zaprojektowany magazyn danych NoSQL może pozwalać na skalowanie rozwiązania znacznie bardziej wydajnych i niższych kosztów niż rozwiązanie korzystające z relacyjnej bazy danych. Ten przewodnik ułatwi Ci następujące tematy.  

## <a name="about-the-azure-table-service"></a>O usłudze Azure Table
W tej sekcji opisano niektóre z kluczowymi funkcjami usługi Table service, które są szczególnie istotne, aby projektowanie pod kątem wydajności i skalowalności. Jeśli dopiero zaczynasz korzystać z usługi Azure Storage i Table service, najpierw Przeczytaj [wprowadzenie do Microsoft Azure Storage](../../storage/common/storage-introduction.md) i [Rozpocznij pracę z platformą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Chociaż ten przewodnik znajduje się na Table service, zawiera omówienie usługi Azure Queue i BLOB Services oraz sposób ich używania z Table service.  

Co to jest usługa Table service? Zgodnie z oczekiwaniami z nazwy, usłudze Table service używa formacie tabelarycznym do przechowywania danych. W terminologii każdy wiersz w tabeli reprezentuje jednostkę, a kolumny przechowywać różne właściwości tej jednostki. Każda jednostka ma parę kluczy do unikatowego identyfikowania oraz kolumnę sygnatury czasowej używaną przez Table service do śledzenia czasu ostatniej aktualizacji jednostki. Sygnatura czasowa jest stosowana automatycznie i nie można ręcznie nadpisać sygnatury czasowej z dowolną wartością. Usługa Table service używa ostatniej modyfikacji sygnatura czasowa (LMT) do zarządzania optymistycznej współbieżności.  

> [!NOTE]
> Operacje interfejsu API REST Table service zwracają również wartość **ETag** , która pochodzi od LMT. Ten dokument używa elementów ETag i LMT zamiennie, ponieważ odwołują się do tych samych danych źródłowych.  
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


Do tej pory te dane wyglądają podobnie do tabeli w relacyjnej bazie danych, a kluczowe różnice są obowiązkowymi kolumnami i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda Właściwość zdefiniowana przez użytkownika, taka jak **FirstName** lub **Age** ma typ danych, na przykład liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. Mimo że w przeciwieństwie do relacyjnej bazy danych bez schematu charakter usługi Table service oznacza właściwości nie wymagają tych samych danych, wpisz dla każdej jednostki. Aby złożone typy danych są przechowywane w pojedynczej właściwości, należy użyć serializacji formatu JSON lub XML. Aby uzyskać więcej informacji na temat usługi Table Service, takiej jak obsługiwane typy danych, obsługiwane zakresy dat, reguły nazewnictwa i ograniczenia rozmiaru, zobacz [Omówienie modelu danych usługi Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Wybór **PartitionKey** i **RowKey** ma podstawowe znaczenie dla dobrego projektu tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, wartości **PartitionKey** i **RowKey** są indeksowane w celu utworzenia indeksu klastrowanego umożliwiającego szybkie wyszukiwanie. Jednak Table service nie tworzy żadnych indeksów pomocniczych, więc **PartitionKey** i **RowKey** są jedynymi właściwościami indeksowanymi. Niektóre wzorce opisane w [wzorcach projektowych tabeli](table-storage-design-patterns.md) ilustrują, jak można obejść to oczywiste ograniczenie.  

Tabela składa się z jednej lub kilku partycji, a wiele z podjętych decyzji projektowych będzie wokół wyboru odpowiednich **PartitionKey** i **RowKey** w celu zoptymalizowania rozwiązania. Rozwiązanie może składać się z pojedynczej tabeli, która zawiera wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie ma wiele tabel. Tabele ułatwiają logicznie organizowanie jednostek, ułatwiają zarządzanie dostępem do danych za pomocą listy kontroli dostępu i może usunąć całą tabelę przy użyciu operacji pojedynczy magazyn.  

## <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwa tabeli i **PartitionKey** wspólnie identyfikują partycję w ramach usługi magazynu, w której usługa Table przechowuje jednostkę. Jak również część schematu adresowania dla jednostek, partycje definiują zakres dla transakcji (zobacz [transakcje grupy jednostek](#entity-group-transactions) poniżej) i stanowią podstawę skalowania usługi Table Service. Aby uzyskać więcej informacji o partycjach, zobacz [Lista kontrolna wydajności i skalowalności dla usługi Table Storage](storage-performance-checklist.md).  

W Table service poszczególne usługi węzłów mają co najmniej jedną kompletną partycję, a usługa jest skalowana przez dynamiczne Równoważenie obciążenia partycji między węzłami. Jeśli węzeł jest w obciążeniu, usługa Table Service może *podzielić* zakres partycji objętych przez ten węzeł na różne węzły; Po nawrocie ruchu usługa może *scalić* zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji o wewnętrznych szczegółach Table service, a w szczególności o tym, jak usługa zarządza partycjami, zobacz dokument [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W usłudze Table service transakcji grup jednostek (EGTs) są tylko wbudowanego mechanizmu do wykonywania niepodzielnych aktualizacje w wielu jednostkach. EGTs są czasami nazywane również *transakcjami wsadowymi*. EGTs może działać tylko na jednostkach przechowywanych w tej samej partycji (czyli udostępnić ten sam klucz partycji w danej tabeli). Aby w dowolnym momencie wymagać niepodzielnych zachowań transakcyjnych w wielu jednostkach, należy się upewnić, że te jednostki znajdują się w tej samej partycji. Jest to często Przyczyna zachowaniem typów jednostek w tej samej tabeli (i partycji), a nie przy użyciu wielu tabel dla jednostek różnych typów. Pojedynczy EGT może operować na co najwyżej 100 jednostek.  Jeśli przesyłasz wiele współbieżnych EGTs do przetwarzania, należy upewnić się, że te EGTs nie działają na jednostkach wspólnych dla EGTs; w przeciwnym razie przetwarzanie może być opóźnione.

EGTs również wprowadza potencjalne rozwiązanie do szacowania w projekcie. Oznacza to, że użycie większej liczby partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości w przypadku żądań równoważenia obciążenia między węzłami. Jednak użycie większej liczby partycji może ograniczyć możliwość wykonywania przez aplikacje niepodzielnych transakcji i zapewnienia silnej spójności danych. Ponadto istnieją konkretne cele skalowalności na poziomie partycji, która może ograniczyć przepływność transakcji, które można oczekiwać dla jednego węzła. Aby uzyskać więcej informacji na temat celów skalowalności dla kont magazynu w warstwie Standardowa, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji dotyczących skalowalności Table service, zobacz [cele skalowalności i wydajności dla usługi Table Storage](scalability-targets.md).

## <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności
W poniższej tabeli opisano niektóre kluczowe wartości, które należy wziąć pod uwagę podczas projektowania rozwiązania Table service:  

| Całkowita pojemność konta usługi Azure storage | 500 TB |
| --- | --- |
| Liczba tabel na koncie usługi Azure storage |Ograniczone tylko przez pojemność konta magazynu |
| Liczba partycji w tabeli |Ograniczone tylko przez pojemność konta magazynu |
| Liczba jednostek w partycji |Ograniczone tylko przez pojemność konta magazynu |
| Rozmiar pojedynczą jednostkę |Do 1 MB i maksymalnie 255 właściwości (łącznie z **PartitionKey**, **RowKey**i **sygnaturą czasową**) |
| Rozmiar **PartitionKey** |Ciąg do 1 KB rozmiaru |
| Rozmiar **RowKey** |Ciąg do 1 KB rozmiaru |
| Rozmiar transakcji grup jednostek |Transakcja może zawierać co najwyżej 100 jednostek i ładunek musi być mniejszy niż 4 MB. EGT można aktualizować tylko jednostki jeden raz. |

Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogi, ale należy uwzględnić oszacowania kosztów zarówno w przypadku użycia pojemności, jak i liczby transakcji w ramach oceny dowolnego Table service rozwiązania. Jednak w wielu scenariuszach przechowywanie nieznormalizowanych lub zduplikowanych danych w celu poprawy wydajności lub skalowalności rozwiązania jest prawidłowym podejściem. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Relacje modelowania](table-storage-design-modeling.md)
- [Projektowanie na potrzeby wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie na potrzeby modyfikacji danych](table-storage-design-for-modification.md)
