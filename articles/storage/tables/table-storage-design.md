---
title: Projektowanie skalowalnych i wydajnych tabel w usłudze Azure table storage. | Microsoft Docs
description: Projektowanie skalowalnych i wydajnych tabel w usłudze Azure table storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848286"
---
# <a name="design-scalable-and-performant-tables"></a>Projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Projektowanie skalowalnych i wydajnych tabel należy wziąć pod uwagę czynników, takich jak wydajność, skalowalność i kosztów. Jeśli wcześniej zostały tak zaprojektowane schematów dla relacyjnych baz danych, te zagadnienia są znane, ale podczas, gdy istnieją pewne podobieństwa między modelu magazynu usługi Azure Table i modeli relacyjnych, są również istotne różnice. Różnice te zwykle prowadzić do różnych projektów, które mogą wyglądać counter-intuitive lub jest on nieprawidłowy komuś zapoznać się z relacyjnych baz danych, ale ma sensu w przypadku projektowania dla magazynu klucz wartość NoSQL, takie jak usługa Azure Table. Wiele różnice w zakresie projektowania odzwierciedlają fakt, że usługa Table jest przeznaczona do obsługi chmury aplikacji w skali, które mogą zawierać miliardów jednostek (lub wiersze w terminologii relacyjnej bazy danych) danych lub w przypadku zestawów danych, który musi obsługiwać dużo transakcji woluminy. W związku z tym należy inaczej przemyśleć sposób przechowywania danych i zrozumienie sposobu działania usługi tabel. Dobrze zaprojektowana magazyn danych NoSQL, można włączyć rozwiązania do skalowania jeszcze więcej możliwości i przy niskich kosztach niż w przypadku rozwiązania, który korzysta z relacyjnej bazy danych. Ten przewodnik ułatwi Ci następujące tematy.  

## <a name="about-the-azure-table-service"></a>O usłudze Azure Table
W tej sekcji opisano niektóre z kluczowymi funkcjami usługi Table service, które są szczególnie istotne, aby projektowanie pod kątem wydajności i skalowalności. Jeśli jesteś nowym użytkownikiem usługi Azure Storage i Table service, najpierw zapoznaj się z [wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md) i [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) przed odczytaniem w dalszej części tego artykułu. . Chociaż ten przewodnik koncentruje się na usłudze Table service, zawiera omówienie usług Azure, kolejek i obiektów Blob i jak można ich używać z usługą Table.  

Co to jest usługa Table service? Zgodnie z oczekiwaniami z nazwy, usłudze Table service używa formacie tabelarycznym do przechowywania danych. W terminologii każdy wiersz w tabeli reprezentuje jednostkę, a kolumny przechowywać różne właściwości tej jednostki. Każda jednostka ma pary kluczy do unikatowego identyfikowania i kolumnę sygnatur czasowych, z których korzysta usługa tabeli do śledzenia datę ostatniej aktualizacji jednostki. Sygnatura czasowa jest stosowane automatycznie, a następnie ręcznie nie można zastąpić sygnaturę czasową z dowolną wartość. Usługa Table service używa ostatniej modyfikacji sygnatura czasowa (LMT) do zarządzania optymistycznej współbieżności.  

> [!NOTE]
> Operacje interfejsu API REST usługi Table również zwracać **ETag** wartość, która pochodzi od LMT. Ten dokument używa terminy, element ETag i LMT zamiennie, ponieważ odnoszą się do tych samych danych.  
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
<th>Nazwa działu</th>
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


Do tej pory tych danych zostanie wyświetlony podobny do tabeli w relacyjnej bazie danych podstawowych różnic obowiązkowe kolumn i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda właściwości zdefiniowane przez użytkownika, takie jak **FirstName** lub **wiek** ma typ danych, takie jak liczba całkowita lub ciąg, po prostu, takich jak kolumny w relacyjnej bazie danych. Mimo że w przeciwieństwie do relacyjnej bazy danych bez schematu charakter usługi Table service oznacza właściwości nie wymagają tych samych danych, wpisz dla każdej jednostki. Aby złożone typy danych są przechowywane w pojedynczej właściwości, należy użyć serializacji formatu JSON lub XML. Aby uzyskać więcej informacji na temat usługi, takie jak obsługiwane typy danych w tabelach, zakresów dat obsługiwanych, zasady nazewnictwa i ograniczeń związanych z rozmiarem, zobacz [opis modelu danych usługi Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Wybór **PartitionKey** i **RowKey** ma podstawowe znaczenie dla projektu dobre tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, **PartitionKey** i **RowKey** wartości są indeksowane, aby utworzyć indeks klastrowany, aby umożliwić szybkie wyszukania. Jednak usługa Table service nie tworzy żadnych indeksów pomocniczych, więc **PartitionKey** i **RowKey** są tylko właściwości indeksowanych. Niektóre wzorce opisane w [wzorce projektowe oparte na tabeli](table-storage-design-patterns.md) pokazują, jak można obejść to ograniczenie jawnego.  

Tabela składa się z co najmniej jedną partycję i będzie wiele decyzji projektowych wprowadzeniu wokół wybierając odpowiedni **PartitionKey** i **RowKey** zoptymalizować swoje rozwiązanie. To rozwiązanie może składać się z pojedynczej tabeli, która zawiera wszystkie jednostki podzielony na partycje, ale zazwyczaj to rozwiązanie ma wiele tabel. Tabele ułatwiają logicznie organizowanie jednostek, ułatwiają zarządzanie dostępem do danych za pomocą listy kontroli dostępu i może usunąć całą tabelę przy użyciu operacji pojedynczy magazyn.  

## <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwę tabeli i **PartitionKey** razem identyfikują partycji w ramach usługi magazynu, w której przechowuje jednostki w usłudze table service. A także bycie częścią schematu adresowania dla jednostek, partycje zdefiniować zakres transakcji (zobacz [transakcji grup jednostek](#entity-group-transactions) poniżej), i stanowią podstawę jak jest skalowana w usłudze table service. Aby uzyskać więcej informacji o partycji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../../storage/common/storage-scalability-targets.md).  

W usłudze Table service oddzielnego węzła usługi jeden lub więcej zakończą się partycji i skali usługi przez dynamiczne równoważenie obciążenia partycji na węzłach. Jeśli węzeł ma pod obciążeniem, usłudze table service można *podziału* zakresu partycji obsługiwanych przez ten węzeł na różnych węzłach; gdy zmniejszenia ruchu, usługa może *scalania* zakresu partycji z węzłów cichy na jednym węźle.  

Aby uzyskać więcej informacji na temat szczegółami wewnętrznymi usługi Table service, a w szczególności w jaki sposób usługa zarządza partycjami, zobacz dokument [usługi Microsoft Azure Storage: Usługi magazynu w chmurze o wysokiej dostępności przy użyciu silnej spójności](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W usłudze Table service transakcji grup jednostek (EGTs) są tylko wbudowanego mechanizmu do wykonywania niepodzielnych aktualizacje w wielu jednostkach. EGTs są również czasami nazywane *partii transakcji*. EGTs może działać tylko na jednostek przechowywanych w tej samej partycji (czyli udział w tym samym kluczem partycji w danej tabeli). Aby w dowolnym momencie atomic transakcyjnych zachowanie jest wymagana w wielu jednostkach, upewnij się, że te jednostki są w tej samej partycji. Jest to często Przyczyna zachowaniem typów jednostek w tej samej tabeli (i partycji), a nie przy użyciu wielu tabel dla jednostek różnych typów. Pojedynczy EGT może operować na co najwyżej 100 jednostek.  Jeśli prześlesz wielu jednoczesnych EGTs do przetwarzania, jest ważne, aby upewnić się, że te EGTs nie działają na jednostkach, które są wspólne dla EGTs; w przeciwnym razie przetwarzania może być opóźnione.

EGTs również wprowadzać potencjalnych kompromis umożliwiające ocenę w projekcie. Oznacza to za pomocą więcej partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure oferuje więcej możliwości Równoważenie obciążenia żądań w węzłach. Jednak przy użyciu więcej partycji może ograniczyć możliwości aplikacji, aby wykonywać transakcje niepodzielne i obsługa silnej spójności danych. Ponadto są cele skalowalności określonego na poziomie partycji, które mogą ograniczać przepustowość transakcji, których można oczekiwać, że dla pojedynczego węzła. Aby uzyskać więcej informacji na temat cele skalowalności dla konta usługi Azure storage i table service, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności
W poniższej tabeli opisano niektóre z kluczowych wartości pod uwagę podczas projektowania rozwiązania usługi tabeli:  

| Całkowita pojemność konta usługi Azure storage | 500 TB |
| --- | --- |
| Liczba tabel na koncie usługi Azure storage |Ograniczone tylko przez pojemność konta magazynu |
| Liczba partycji w tabeli |Ograniczone tylko przez pojemność konta magazynu |
| Liczba jednostek w partycji |Ograniczone tylko przez pojemność konta magazynu |
| Rozmiar pojedynczą jednostkę |Do 1 MB, maksymalnie 255 właściwości (w tym **PartitionKey**, **RowKey**, i **sygnatura czasowa**) |
| Rozmiar **PartitionKey** |Ciąg do 1 KB rozmiaru |
| Rozmiar **RowKey** |Ciąg do 1 KB rozmiaru |
| Rozmiar transakcji grup jednostek |Transakcja może zawierać co najwyżej 100 jednostek i ładunek musi być mniejszy niż 4 MB. EGT można aktualizować tylko jednostki jeden raz. |

Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest względnie niedrogie ulepszenie, ale powinien zawierać szacowane koszty użycia pojemności i liczba transakcji w ramach oceny od wszelkich rozwiązań do tabeli usługi. Jednak w wielu scenariuszach nieznormalizowany lub zduplikowane dane są przechowywane w celu poprawy wydajności i skalowalności rozwiązania jest prawidłowy podejście. Aby uzyskać więcej informacji o cenach, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe oparte na tabelę](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych w tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
