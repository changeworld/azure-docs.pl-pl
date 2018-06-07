---
title: Projekt skalowalne i tabele wydajności w magazynie tabel platformy Azure. | Microsoft Docs
description: Projekt skalowalne i tabele wydajności w magazynie tabel platformy Azure.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: ba48283371ac099b162aeb3cbffd6127ccce1676
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660900"
---
# <a name="design-scalable-and-performant-tables"></a>Projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Projektowania skalowalności i wydajności tabel należy wziąć pod uwagę czynników, takich jak wydajności, skalowalności i kosztów. Wcześniej zaprojektowany schematy relacyjnych baz danych, powyższe zagadnienia są znane, ale istnieją podobieństwa relacyjne modele i modelu magazynu tabel Azure usługi, są również istotne różnice. Różnice te zwykle prowadzić do różnych projektów, które może wyglądać counter-intuitive lub jest on nieprawidłowy osobie zapoznać się z relacyjnych baz danych, ale sensu projektowania magazynu klucza i wartości NoSQL, takie jak usługa tabel Azure. Wiele sieci różnice projektu odzwierciedlają fakt, że usługa tabel jest przeznaczony do obsługi aplikacji skali chmury zawierające miliardów jednostek (lub wierszy w terminologii relacyjnej bazy danych) danych lub w przypadku zestawów danych, który musi obsługiwać wysokie transakcji woluminy. W związku z tym należy inaczej traktować jak przechowywać dane i zrozumieć, jak działa usługa tabeli. Magazyn danych NoSQL dobrze zaprojektowanego można włączyć rozwiązania można skalować znacznie bardziej i tańsze niż rozwiązania, które korzysta z relacyjnej bazy danych. Ten przewodnik pomaga w tych tematach.  

## <a name="about-the-azure-table-service"></a>Usługa tabel Azure — informacje
W tej sekcji opisano niektóre najważniejsze funkcje usługi tabel, które są szczególnie istotne projektowanie wydajności i skalowalności. Jeśli jesteś nowym użytkownikiem usługi Azure Storage i usługi tabel, najpierw przeczytać artykuł [wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md) i [Rozpoczynanie pracy z magazynem tabel Azure przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) przed przeczytaniem dalszej części tego artykułu . Chociaż ten przewodnik koncentruje się na usługi tabel, zawiera omówienie usług Azure kolejek i obiektów Blob i jak można ich używać z usługą tabeli.  

Co to jest usługa tabeli? Jak może spodziewać się od nazwy, usługi tabel w formacie tabelarycznym do przechowywania danych. W terminologii każdy wiersz w tabeli reprezentuje jednostkę, a kolumny przechowywać różne właściwości tego obiektu. Każdy obiekt ma pary kluczy do unikatowego identyfikowania i kolumny znaczników czasu tabeli używana przez usługę do śledzenia datę ostatniej aktualizacji jednostki. Sygnatura czasowa jest stosowana automatycznie, a znacznik czasu nie można zastąpić ręcznie dla dowolną wartość. Usługa tabel używa ostatniej modyfikacji sygnatura czasowa (LMT) do zarządzania optymistycznej współbieżności.  

> [!NOTE]
> Operacje interfejsu API REST usługi tabeli zwracają również **ETag** wartość, która dziedziczy LMT. Ten dokument używa postanowienia ETag i LMT zamiennie, ponieważ odnoszą się do tych samych danych.  
> 
> 

W poniższym przykładzie przedstawiono projekt prostą tabelę do przechowywania pracowników i dział jednostek. Wiele przykładowych pokazano w dalszej części tego przewodnika są oparte na ten projekt proste.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Sygnatura czasowa</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Imię</th>
<th>Nazwisko</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>ADAM</td>
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
<th>Imię</th>
<th>Nazwisko</th>
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
<th>Imię</th>
<th>Nazwisko</th>
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


Do tej pory tych danych jest podobny do tabeli relacyjnej bazy danych z podstawowych różnic obowiązkowe kolumn i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda właściwości zdefiniowane przez użytkownika, takie jak **imię** lub **wieku** ma typ danych, takie jak liczba całkowita lub ciąg, tylko kolumny w relacyjnej bazie danych. Mimo że w przeciwieństwie do relacyjnej bazy danych bez schematu rodzaj usługi tabel oznacza, że właściwość nie wymagają dane tego samego typu dla każdej jednostki. Aby przechowywać złożone typy danych w jednej właściwości, należy użyć format serializacji JSON i XML. Aby uzyskać więcej informacji dotyczących typów danych usługi, takie jak obsługiwane tabeli, zakresy daty, reguły nazewnictwa i ograniczenia rozmiaru, zobacz [opis modelu danych usługi tabel](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Wybór **PartitionKey** i **RowKey** jest niezbędne, aby projekt dobrej tabeli. Każdy podmiot przechowywane w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, **PartitionKey** i **RowKey** wartości są indeksowane można utworzyć indeksu klastrowanego umożliwiające szybkie wyszukania. Jednak usługa tabel nie tworzy żadnych indeksów pomocniczych tak **PartitionKey** i **RowKey** są tylko właściwości indeksowane. Niektóre wzorce opisanego w [wzorce projektowe tabeli](table-storage-design-patterns.md) zilustrować, jak obejść to ograniczenie oczywista.  

Składa się z co najmniej jednej partycji tabeli i będzie wiele decyzji projektowych wprowadzeniu wokół wybranie odpowiedniej **PartitionKey** i **RowKey** w celu zoptymalizowania rozwiązania. Rozwiązanie może składać się z pojedynczej tabeli, która zawiera wszystkie obiekty podzielony na partycje, ale zazwyczaj rozwiązanie zawiera wiele tabel. Tabele ułatwiają logicznie organizowanie jednostek, ułatwiają zarządzanie dostępem do danych za pomocą listy kontroli dostępu i może usunąć całą tabelę przy użyciu operacji pojedynczy magazyn.  

## <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwę tabeli i **PartitionKey** wspólnie identyfikują partycji w ramach usługi magazynu usługi tabel, w których przechowują się jednostki. A także stanowi część schematu adresowania dla jednostek, partycje definiowania zakresu dla transakcji (zobacz [transakcji grupy jednostek](#entity-group-transactions) poniżej), i stanowią podstawę jak skaluje usługi tabel. Aby uzyskać więcej informacji o partycji, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../../storage/common/storage-scalability-targets.md).  

W usłudze tabel oddzielnego węzła services jedną lub więcej ukończyć partycji i skali usługi przez dynamiczne równoważenie obciążenia partycji w węzłach. Jeśli węzeł ma pod obciążeniem, usługa tabel można *podzielić* zakresu partycji obsługiwanych przez ten węzeł na różnych węzłach; gdy zaspokojenie ruchu, usługa może *scalania* zakresy partycji z węzłów quiet kopii na jednym węźle.  

Aby uzyskać więcej informacji na temat wewnętrznej szczegółów usługi tabel, w szczególności w jaki sposób usługa zarządza partycji, zobacz dokument [Microsoft Azure Storage: A wysoce dostępna usługa magazynu w chmurze with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Grupowanie jednostki transakcji
W usłudze tabel transakcji grupy jednostek (EGTs) są tylko wbudowany mechanizm wykonywanie atomic aktualizacji przez wiele jednostek. EGTs są również czasami określane jako *partii transakcji*. EGTs może działać tylko na jednostek przechowywanych w tej samej partycji (oznacza to, że udział w tym samym kluczem partycji w danej tabeli). Aby w dowolnym momencie atomic transakcyjne zachowanie wymagane przez wiele jednostek, należy się upewnić, że te jednostki są w tej samej partycji. Jest to często przyczynę zachowania wiele typów jednostek w tej samej tabeli (i partycji) i nie używa wielu tabel dla typów jednostek różnych. Pojedynczy EGT może pracować na maksymalnie 100 jednostek.  Jeśli prześlesz wiele równoczesnych EGTs przetwarzania koniecznie upewnij się, że te EGTs nie działają na jednostek, które są wspólne dla EGTs; w przeciwnym razie przetwarzania może być opóźnione.

EGTs również wprowadzać potencjalną zależność umożliwiające ocenę w projekcie. Oznacza to za pomocą więcej partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości Równoważenie obciążenia żądań w węzłach. Jednak przy użyciu więcej partycji może ograniczyć możliwość wykonywania atomic transakcji i obsługa wysoki poziom spójności danych aplikacji. Ponadto istnieją wartości docelowe skalowalności określonych na poziomie partycji, która może ograniczyć przepustowość transakcje, które można spodziewać się do jednego węzła. Aby uzyskać więcej informacji dotyczących skalowalności docelowe konto magazynu Azure i usługi tabel, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności
W poniższej tabeli opisano niektóre z wartości klucza pod uwagę podczas projektowania rozwiązania usługi tabeli:  

| Łączna pojemność konta magazynu platformy Azure | 500 TB |
| --- | --- |
| Liczba tabel na koncie magazynu Azure |Ograniczone tylko przez pojemności konta magazynu |
| Liczba partycji w tabeli |Ograniczone tylko przez pojemności konta magazynu |
| Liczba jednostek w partycji |Ograniczone tylko przez pojemności konta magazynu |
| Rozmiar pojedynczą jednostkę |Maksymalnie 255 właściwości maksymalnie 1 MB (łącznie z **PartitionKey**, **RowKey**, i **sygnatury czasowej**) |
| Rozmiar **PartitionKey** |Ciąg znaków o rozmiarze 1 KB |
| Rozmiar **RowKey** |Ciąg znaków o rozmiarze 1 KB |
| Rozmiar grupy jednostki transakcji |Transakcja może zawierać co najwyżej 100 jednostek i ładunek musi być mniejszy niż 4 MB. EGT można aktualizować tylko jednostki jeden raz. |

Aby uzyskać więcej informacji, zobacz [opis modelu danych usługi tabel](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Zagadnienia dotyczące kosztu
Magazyn tabel jest względnie niedrogie, ale powinna zawierać szacowane koszty dla wykorzystanie pojemności i liczba transakcji w ramach oceny od wszelkich rozwiązań usługi tabeli. Jednak w wielu scenariuszach nieznormalizowany lub zduplikowane dane są przechowywane w celu poprawy wydajności i skalowalności rozwiązania jest to prawidłowy podejście. Aby uzyskać więcej informacji o cenach, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Relacje modelowania](table-storage-design-modeling.md)
- [Projekt do wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych w tabeli](table-storage-design-encrypt-data.md)
- [Projekt do modyfikacji danych](table-storage-design-for-modification.md)