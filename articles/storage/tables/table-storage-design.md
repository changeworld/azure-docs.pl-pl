---
title: Projektowanie skalowalnych i wydajnych tabel w magazynie tabel platformy Azure. | Microsoft Docs
description: Projektowanie skalowalnych i wydajnych tabel w magazynie tabel platformy Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255147"
---
# <a name="design-scalable-and-performant-tables"></a>Projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę takie czynniki, jak wydajność, skalowalność i koszt. Jeśli wcześniej zaprojektowane schematy dla relacyjnych baz danych, te zagadnienia są znane, ale chociaż istnieją pewne podobieństwa między modelem magazynu usługi Azure Table i modelami relacyjnych, istnieją również istotne różnice. Te różnice zazwyczaj prowadzą do różnych projektów, które mogą wyglądać sprzeczne intuicyjnie lub źle dla kogoś zaznajomionego z relacyjnych baz danych, ale ma sens, jeśli projektujesz dla magazynu klucz/wartość NoSQL, takich jak usługa Azure Table. Wiele różnic projektowych odzwierciedla fakt, że usługa Tabela jest przeznaczona do obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek (lub wierszy w terminologii relacyjnej bazy danych) danych lub dla zestawów danych, które muszą obsługiwać wysokie transakcje Woluminów. W związku z tym należy myśleć inaczej o tym, jak przechowywać dane i zrozumieć, jak działa usługa tabel. Dobrze zaprojektowany magazyn danych NoSQL może umożliwić rozwiązanie skalowanie znacznie dalej i przy niższych kosztach niż rozwiązanie, które używa relacyjnej bazy danych. Ten przewodnik pomaga w tych tematach.  

## <a name="about-the-azure-table-service"></a>Informacje o usłudze tabel platformy Azure
W tej sekcji przedstawiono niektóre z kluczowych funkcji usługi Tabela, które są szczególnie istotne dla projektowania dla wydajności i skalowalności. Jeśli jesteś nowy w usłudze Azure Storage i usługi tabel, najpierw przeczytaj [wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md) i wprowadzenie do usługi Azure Table Storage przy użyciu platformy [.NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Chociaż ten przewodnik koncentruje się na table service, zawiera omówienie usługi Kolejki platformy Azure i obiektów Blob i jak można ich używać z usługą tabel.  

Co to jest usługa Tabela? Jak można oczekiwać od nazwy, usługa tabela używa formatu tabelaryczne do przechowywania danych. W terminologii standardowej każdy wiersz tabeli reprezentuje jednostkę, a kolumny przechowują różne właściwości tej jednostki. Każda jednostka ma parę kluczy, aby jednoznacznie ją zidentyfikować, oraz kolumnę sygnatury czasowej używaną przez usługę tabel do śledzenia, kiedy jednostka została ostatnio zaktualizowana. Sygnatura czasowa jest stosowana automatycznie i nie można ręcznie zastąpić sygnatury czasowej dowolną wartością. Usługa tabela używa tej ostatnio zmodyfikowanej sygnatury czasowej (LMT) do zarządzania optymistyczną współbieżnością.  

> [!NOTE]
> Operacje interfejsu API rest usługi tabel zwraca również wartość **ETag,** która pochodzi z LMT. W tym dokumencie użyto terminów ETag i LMT zamiennie, ponieważ odnoszą się one do tych samych danych źródłowych.  
> 
> 

W poniższym przykładzie przedstawiono prosty projekt tabeli do przechowywania jednostek pracowników i działów. Wiele przykładów pokazanych w dalszej części tego przewodnika są oparte na tej prostej konstrukcji.  

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
<th>Adres e-mail</th>
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
<th>Adres e-mail</th>
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
<th>Liczba pracowników</th>
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
<th>Adres e-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Do tej pory te dane są podobne do tabeli w relacyjnej bazie danych z kluczowymi różnicami są kolumny obowiązkowe i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda z właściwości zdefiniowanych przez użytkownika, takich jak **FirstName** lub **Age** ma typ danych, takich jak liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. Chociaż w przeciwieństwie do relacyjnej bazy danych, bez schematu charakter Usługi Tabel oznacza, że właściwość nie musi mieć tego samego typu danych dla każdej jednostki. Aby przechowywać złożone typy danych w jednej właściwości, należy użyć formatu szeregowego, takiego jak JSON lub XML. Aby uzyskać więcej informacji na temat usługi tabel, takich jak obsługiwane typy danych, obsługiwane zakresy dat, reguły nazewnictwa i ograniczenia rozmiaru, zobacz [Opis modelu danych usługi tabel](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Twój wybór **PartitionKey** i **RowKey** ma fundamentalne znaczenie dla dobrego projektu stołu. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, **partitionkey** i **RowKey** wartości są indeksowane w celu utworzenia indeksu klastrowanego, aby umożliwić szybkie wyszukiwania. Jednak usługa tabel nie tworzy żadnych indeksów pomocniczych, więc **PartitionKey** i **RowKey** są tylko właściwości indeksowane. Niektóre wzorce opisane w [tabeli wzorów projektowania](table-storage-design-patterns.md) ilustrują, jak można obejść to ograniczenie pozorne.  

Tabela składa się z jednej lub więcej partycji, a wiele z podejmowanych decyzji projektowych będzie wokół wyboru odpowiedniego **PartitionKey** i **RowKey** w celu optymalizacji rozwiązania. Rozwiązanie może składać się z pojedynczej tabeli, która zawiera wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie ma wiele tabel. Tabele ułatwiają logiczne organizowanie encji, ułatwianie zarządzania dostępem do danych przy użyciu list kontroli dostępu i można upuścić całą tabelę przy użyciu jednej operacji magazynowania.  

## <a name="table-partitions"></a>Partycje tabeli
Nazwa konta, nazwa tabeli i **PartitionKey** razem zidentyfikować partycję w ramach usługi magazynu, gdzie usługa tabel przechowuje jednostki. Partycje nie tylko są częścią schematu adresowania dla jednostek, ale także definiują zakres transakcji (zobacz [transakcje grupy jednostek](#entity-group-transactions) poniżej) i stanowią podstawę skalowania usługi tabeli. Aby uzyskać więcej informacji na temat partycji, zobacz [Lista kontrolna wydajności i skalowalności magazynu tabel](storage-performance-checklist.md).  

W usłudze tabeli poszczególne usługi węzła co najmniej jedną pełną partycję, a usługa skaluje się dynamicznie równoważąc partycje między węzłami. Jeśli węzeł jest pod obciążeniem, usługa tabel może *podzielić* zakres partycji obsługiwanych przez ten węzeł na różne węzły; gdy ruch ustępuje, usługa może *scalić* zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji na temat wewnętrznych szczegółów usługi Tabela, a w szczególności sposobu zarządzania partycjami przez usługę, zobacz papierowy microsoft azure storage: usługa magazynu w [chmurze o wysokiej dostępności z silną spójnością.](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

## <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W usłudze tabel transakcje grupy jednostek (EGTs) są jedynym wbudowanym mechanizmem do wykonywania aktualizacji niepodzielnych w wielu jednostkach. EGT są czasami określane również jako *transakcje wsadowe*. EGTs mogą działać tylko na jednostkach przechowywanych w tej samej partycji (oznacza to, że współużytkuje ten sam klucz partycji w danej tabeli). Tak więc w każdej chwili wymagają niepodzielne zachowanie transakcyjne w wielu jednostkach, należy upewnić się, że te jednostki są w tej samej partycji. Jest to często powodem utrzymania wielu typów jednostek w tej samej tabeli (i partycji) i nie przy użyciu wielu tabel dla różnych typów jednostek. Pojedynczy EUWT może działać na co najwyżej 100 podmiotach.  W przypadku przedłożenia wielu równoczesnych EGT do przetwarzania, ważne jest, aby upewnić się, że te EGT nie działają na podmiotach, które są wspólne dla EGT; w przeciwnym razie przetwarzanie może być opóźnione.

EGTs również wprowadzić potencjalny kompromis do oceny w projekcie. Oznacza to, że przy użyciu większej liczby partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości równoważenia obciążenia żądań między węzłami. Jednak przy użyciu większej liczby partycji może ograniczyć zdolność aplikacji do wykonywania transakcji niepodzielnych i utrzymania silnej spójności dla danych. Ponadto istnieją określone cele skalowalności na poziomie partycji, które mogą ograniczać przepływność transakcji, których można oczekiwać dla pojedynczego węzła. Aby uzyskać więcej informacji na temat celów skalowalności dla standardowych kont magazynu platformy Azure, zobacz [Cele skalowalności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji na temat celów skalowalności usługi Tabel, zobacz [Cele skalowalności i wydajności magazynu tabel](scalability-targets.md).

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogi, ale należy uwzględnić szacunki kosztów zarówno użycia pojemności, jak i ilości transakcji w ramach oceny dowolnego rozwiązania usługi tabel. Jednak w wielu scenariuszach przechowywanie nieprawidłowych lub zduplikowanych danych w celu zwiększenia wydajności lub skalowalności rozwiązania jest prawidłowym podejściem. Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowania tabel](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
