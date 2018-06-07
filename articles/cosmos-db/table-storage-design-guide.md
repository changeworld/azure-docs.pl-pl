---
title: Podręcznik projektowania tabeli magazynu systemu Azure | Dokumentacja firmy Microsoft
description: Projektowania skalowalności i wydajności tabel Azure Table Storage
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: sngun
ms.openlocfilehash: 4f3cafd80c713697a8b8fdde56c021be1c5319fb
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824591"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Przewodnik projektowania tabeli magazynu systemu Azure: Projektowanie skalowalności i wydajności tabele
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Projekt skalowalne i tabele wydajności, które należy wziąć pod uwagę wiele czynników, takich jak wydajności, skalowalności i kosztów. Wcześniej zaprojektowany schematy relacyjnych baz danych, tych zagadnień będzie znane, ale istnieją podobieństwa relacyjne modele i modelu magazynu tabel Azure usługi, są również wiele istotnych różnic. Te różnice są zwykle prowadzić do bardzo różnych projektów, który może wyglądać counter-intuitive lub jest on nieprawidłowy osobie zapoznać się z relacyjnych baz danych, ale które wprowadzaj wiedział, w przypadku projektowania magazynu klucza i wartości NoSQL, takie jak usługa tabel Azure. Wiele sieci różnice projekt będzie odzwierciedlać fakt, że usługa tabel jest przeznaczony do obsługi aplikacji w skali chmury, zawierające miliardów jednostek (wiersze w terminologii relacyjnej bazy danych), danych lub w przypadku zestawów danych, który musi obsługiwać transakcji bardzo duże woluminy: w związku z tym należy inaczej traktować jak przechowywać dane i zrozumieć, jak działa usługa tabeli. Magazyn danych NoSQL przemyślany można włączyć rozwiązania można skalować znacznie więcej (i koszt będzie niższy koszt) niż rozwiązania, które korzysta z relacyjnej bazy danych. Ten przewodnik pomaga w tych tematach.  

## <a name="about-the-azure-table-service"></a>Usługa tabel Azure — informacje
W tej sekcji opisano niektóre najważniejsze funkcje usługi tabel, które są szczególnie istotne projektowanie wydajności i skalowalności. Jeśli jesteś nowym użytkownikiem usługi Azure Storage i usługi tabel, najpierw przeczytać artykuł [wprowadzenie do usługi Microsoft Azure Storage](../storage/common/storage-introduction.md) i [Rozpoczynanie pracy z magazynem tabel Azure przy użyciu platformy .NET](table-storage-how-to-use-dotnet.md) przed przeczytaniem dalszej części tego artykułu. Chociaż ten przewodnik koncentruje się na usługi tabel, obejmuje niektóre Omówienie usługi Azure kolejek i obiektów Blob i sposobu ich wykorzystania wraz z usługi tabel w rozwiązaniu.  

Co to jest usługa tabeli? Jak może spodziewać się od nazwy, usługi tabel w formacie tabelarycznym do przechowywania danych. W terminologii każdy wiersz w tabeli reprezentuje jednostkę, a kolumny przechowywać różne właściwości tego obiektu. Każdy obiekt ma parę kluczy w celu jego jednoznacznej identyfikacji i kolumny znaczników czasu tabeli używana przez usługę do śledzenia jednostki został ostatnio zaktualizowany (dzieje się to automatycznie i nie można ręcznie zastąpić sygnatura czasowa z dowolną wartością). Usługa tabel używa ostatniej modyfikacji sygnatura czasowa (LMT) do zarządzania optymistycznej współbieżności.  

> [!NOTE]
> Operacje interfejsu API REST usługi tabeli zwracają również **ETag** wartość, która dziedziczy po ostatniej modyfikacji znacznik czasu (LMT). W tym dokumencie użyjemy postanowienia ETag i LMT zamiennie ponieważ odnoszą się do tych samych danych.  
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


Do tej pory to wygląda bardzo podobnie do tabeli w bazie danych relacyjnych z podstawowych różnic obowiązkowe kolumn i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każdej z właściwości zdefiniowane przez użytkownika, takie jak **imię** lub **wieku** ma typ danych, takie jak liczba całkowita lub ciąg, tylko kolumny w relacyjnej bazie danych. Mimo że w przeciwieństwie do relacyjnej bazy danych bez schematu rodzaj usługi tabel oznacza, że właściwość nie wymagają dane tego samego typu dla każdej jednostki. Aby przechowywać złożone typy danych w jednej właściwości, należy użyć format serializacji JSON i XML. Aby uzyskać więcej informacji dotyczących typów danych usługi, takie jak obsługiwane tabeli, zakresy daty, reguły nazewnictwa i ograniczenia rozmiaru, zobacz [opis modelu danych usługi tabel](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Jak pokażemy, wybór **PartitionKey** i **RowKey** jest niezbędne, aby projekt dobrej tabeli. Każdy podmiot przechowywane w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, **PartitionKey** i **RowKey** wartości są indeksowane, aby utworzyć indeks klastrowany, który umożliwia szybkie wyszukania; jednak usługa tabel nie utworzyć żadnych indeksów pomocniczych, dzięki czemu są one tylko dwie właściwości indeksowane (niektóre wzorce w dalszej części Pokaż jak obejść to ograniczenie widoczna).  

Tabeli składa się z co najmniej jedną partycję, i jak pokażemy, będzie wiele decyzji projektowych wprowadzeniu wokół wybranie odpowiedniej **PartitionKey** i **RowKey** w celu zoptymalizowania rozwiązania. Rozwiązanie może składać się z tylko pojedynczej tabeli, która zawiera wszystkie obiekty podzielony na partycje, ale zazwyczaj rozwiązanie będzie mieć wielu tabel. Tabele ułatwiają logicznie organizowanie jednostek, ułatwiają zarządzanie dostępem do danych za pomocą listy kontroli dostępu i może usunąć całą tabelę przy użyciu operacji pojedynczy magazyn.  

### <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwę tabeli i **PartitionKey** wspólnie identyfikują partycji w ramach usługi magazynu usługi tabel, w których przechowują się jednostki. A także stanowi część schematu adresowania dla jednostek, partycje definiowania zakresu dla transakcji (zobacz [transakcji grupy jednostek](#entity-group-transactions) poniżej), i stanowią podstawę jak skaluje usługi tabel. Aby uzyskać więcej informacji na partycje, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../storage/common/storage-scalability-targets.md).  

W usłudze tabel oddzielnego węzła services jedną lub więcej zakończenia partycji i skali usługi przez dynamiczne równoważenie obciążenia partycji w węzłach. Jeśli węzeł ma pod obciążeniem, usługa tabel można *podzielić* zakresu partycji obsługiwanych przez ten węzeł na różnych węzłach; gdy zaspokojenie ruchu, usługa może *scalania* zakresy partycji z węzłów quiet kopii na jednym węźle.  

Aby uzyskać więcej informacji na temat wewnętrznej szczegółów usługi tabel, w szczególności w jaki sposób usługa zarządza partycji, zobacz dokument [Microsoft Azure Storage: A wysoce dostępna usługa magazynu w chmurze with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Grupowanie jednostki transakcji
W usłudze tabel transakcji grupy jednostek (EGTs) są tylko wbudowany mechanizm wykonywanie atomic aktualizacji przez wiele jednostek. EGTs są również nazywane *partii transakcji* w części dokumentacji. EGTs może działać tylko na jednostek przechowywanych w tej samej partycji (udział ten sam klucz partycji w danej tabeli), dlatego w dowolnym momencie należy atomic zachowanie transakcyjnego przez wiele jednostek, należy się upewnić, że te jednostki znajdują się w tej samej partycji. Jest to często przyczynę zachowania wiele typów jednostek w tej samej tabeli (i partycji) i nie używa wielu tabel dla typów jednostek różnych. Pojedynczy EGT może pracować na maksymalnie 100 jednostek.  Jeśli prześlesz wiele równoczesnych EGTs przetwarzania koniecznie upewnij się, że te EGTs nie działają na jednostek, które są wspólne w EGTs, ponieważ w przeciwnym razie przetwarzania może być opóźnione.

EGTs również wprowadzać potencjalną zależność umożliwiające ocenę w projekcie: przy użyciu więcej partycji spowoduje zwiększenie skalowalności aplikacji, ponieważ platforma Azure ma więcej możliwości Równoważenie obciążenia żądań w węzłach, ale może to ograniczyć możliwość wykonywania atomic transakcji i obsługa wysoki poziom spójności danych aplikacji. Ponadto istnieją wartości docelowe skalowalności określonych na poziomie partycji, która może ograniczyć przepustowość transakcji można oczekiwać, że dla jednego węzła: Aby uzyskać więcej informacji dotyczących skalowalności docelowe konto magazynu Azure i usługi tabel, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](../storage/common/storage-scalability-targets.md). Kolejnych sekcjach niniejszego przewodnika omówiono w nim różne projektowania strategii, które ułatwiają zarządzanie kompromis, taką jak i omówienia najlepszy sposób, aby wybrać klucz partycji na podstawie określonych wymagań aplikacji klienta.  

### <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności
W poniższej tabeli przedstawiono niektóre z wartości klucza pod uwagę podczas projektowania rozwiązania usługi tabeli:  

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

### <a name="cost-considerations"></a>Zagadnienia dotyczące kosztu
Magazyn tabel jest względnie niedrogie, ale powinna zawierać szacowane koszty dla wykorzystanie pojemności i liczba transakcji w ramach próbnego dowolnego rozwiązania, które korzysta z usługi tabeli. Jednak w wielu scenariuszach nieznormalizowany lub zduplikowane dane są przechowywane w celu poprawy wydajności i skalowalności rozwiązania jest prawidłowy podejście do wykonania. Aby uzyskać więcej informacji o cenach, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Wskazówki dotyczące projektowania tabeli
Te listy Podsumuj część klucza wskazówki, które należy mieć na uwadze podczas projektowania tabel, a w tym przewodniku zajmie się nimi wszystkie szczegółowo w dalszej części. Niniejsze wytyczne bardzo różnią się od wskazówki, które zwykle należy wykonać dla projektu relacyjnej bazy danych.  

Projektowania rozwiązania usługi tabeli jako *odczytu* wydajne:

* ***Projekt do wykonywania zapytań w aplikacjach ciężki odczytu.*** Podczas projektowania tabel pomyśleć o zapytań (szczególnie opóźnienia poufnych z nich), które będą wykonywane przed zaplanowaniem będzie aktualizowania jednostki. Powoduje to zwykle rozwiązania wydajne i wydajności.  
* ***Określ zarówno PartitionKey i RowKey zapytania.*** *Polecenie zapytania* takich jak te są najbardziej efektywny zapytania usługi tabeli.  
* ***Warto przechowywać kopie jednostek.*** Magazyn tabel jest tanie dlatego należy rozważyć przechowywania tej samej jednostki zbyt wiele razy (z różnymi kluczami) umożliwiające efektywniejsze kwerendy.  
* ***Należy rozważyć denormalizing danych.*** Magazyn tabel jest tanie dlatego należy rozważyć denormalizing danych. Na przykład przechowywanie podsumowania jednostek, tak, aby zapytania dotyczące agregowanie danych wymagane jest tylko dostęp do pojedynczej jednostki.  
* ***Użyj wartości klucza złożonego.*** Są tylko klucze masz **PartitionKey** i **RowKey**. Na przykład użyć wartości klucza złożonego włączyć ścieżki alternatywnej kluczem dostępu do jednostek.  
* ***Użyj projekcji zapytań.*** Można zmniejszyć ilość danych za pośrednictwem sieci przy użyciu kwerend wybierających tylko pola, które są potrzebne.  

Projektowania rozwiązania usługi tabeli jako *zapisu* wydajne:  

* ***Nie należy tworzyć gorących partycji.*** Wybierz klucze, które umożliwiają rozłożyć żądań na wiele partycji w dowolnym czasie.  
* ***Unikaj największego ruchu.*** Wygładzanie ruch w odpowiednim przedziale czasu i uniknąć największego ruchu.
* ***Nie należy tworzyć niekoniecznie osobnej tabeli dla każdego typu jednostki.*** Jeśli wymagane jest atomic transakcji między typami jednostek, można przechowywać te wiele typów jednostek w tej samej partycji w tej samej tabeli.
* ***Należy wziąć pod uwagę maksymalną przepływność, którą należy osiągnąć.*** Należy znać docelowe skalowalności usługi tabel i upewnij się, że projektu nie spowoduje przekroczenie je.  

Omówione w tym przewodniku, zobaczysz przykłady, których praktycznego wdrożenia wszystkich tych zasad.  

## <a name="design-for-querying"></a>Projekt do wykonywania zapytań
Tabela rozwiązań usług mogą być odczytywane CPU, intensywnie zapisu lub zmieszanie dwóch. Ta sekcja dotyczy przede wszystkim warto pamiętać podczas projektowania usługi tabeli, aby efektywnie obsługiwać operacje odczytu. Projekt, że obsługuje operacje odczytu wydajnie również jest zazwyczaj wydajne dla operacji zapisu. Istnieją dodatkowe zagadnienia dotyczące przy tym pamiętać podczas projektowania w celu obsługi operacji zapisu, omówiona w następnej sekcji [projektu do modyfikacji danych](#design-for-data-modification).

Dobry punkt wyjścia do projektowania rozwiązania usługi tabeli umożliwiają wydajne odczytać danych jest "jakie zapytania mojej aplikacji należy wykonać można pobrać z usługi tabel danych, których potrzebuje?"  

> [!NOTE]
> Z usługi tabel należy pobrać projekt poprawne góry ponieważ jest trudne i drogie w później zmienić. Na przykład relacyjnej bazy danych jest często możliwe problemy z wydajnością adres po prostu dodając indeksów do istniejącej bazy danych: nie jest opcją w usłudze tabeli.  
> 
> 

W tej sekcji koncentruje się na najważniejszych problemów, które muszą spełnić przy projektowaniu tabel do wykonywania zapytań. Tematy przedstawione w tej sekcji zawierają:

* [Jak wybór PartitionKey i RowKey ma wpływ na wydajność zapytań](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Wybieranie odpowiedniego PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optymalizacja zapytania dotyczące usługi tabel](#optimizing-queries-for-the-table-service)
* [Sortowanie danych w usłudze tabel](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór PartitionKey i RowKey ma wpływ na wydajność zapytań
W następujących przykładach przyjmuje usługi tabel jest przechowywanie pracownika jednostek o następującej strukturze (Pomiń Większość przykładów **sygnatury czasowej** właściwości dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **Imię** |Ciąg |
| **Nazwisko** |Ciąg |
| **okres ważności** |Liczba całkowita |
| **EmailAddress** |Ciąg |

We wcześniejszej sekcji [Omówienie usługi Azure tabeli](#overview) opisano niektóre najważniejsze funkcje usługi tabel Azure, które mają bezpośredni wpływ na projektowanie dla zapytania. Wynikiem tych następujące ogólne wskazówki dotyczące projektowania zapytań usługi tabeli. Należy zauważyć, że składnia filtru używane w poniższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [jednostek zapytania](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

* A ***zapytania punktu*** jest najbardziej wydajnym odnośnika do użycia i jest zalecany do zastosowania w przypadku dużych wyszukiwań lub wyszukiwań wymagające najniższym opóźnieniu. Takie zapytanie może być bardzo wydajnie Znajdź pojedynczą jednostkę, określając zarówno indeksy **PartitionKey** i **RowKey** wartości. Na przykład: $filter = (PartitionKey eq 'Sales') i (RowKey eq "2")  
* Drugi najlepiej jest ***kwerendy zakresu*** używającą **PartitionKey** i filtry w zakresie **RowKey** wartości do zwrócenia więcej niż jednej jednostki. **PartitionKey** wartość identyfikuje określonej partycji i **RowKey** identyfikuje podzbiór jednostki w tej partycji. Na przykład: $filter = PartitionKey eq "Sprzedaży i RowKey ge" i RowKey lt 'T "  
* Trzeci najlepiej ***skanowania partycji*** używającą **PartitionKey** i filtry w innej właściwości niekluczowe i mogą zwracać więcej niż jednej jednostki. **PartitionKey** wartość identyfikuje określonej partycji i wybierz dla podzbioru jednostek w partycji tej wartości właściwości. Na przykład: $filter = PartitionKey eq "Sprzedaży" i nazwisko eq 'Smith'  
* A ***skanowania tabeli*** nie obejmuje **PartitionKey** i jest bardzo mało wydajne, ponieważ wszystkie partycje, które składają się na tabelę z kolei do żadnych zgodnych jednostek wyszukiwania. Zostanie przeprowadzone skanowanie tabeli niezależnie od tego, czy korzysta z filtrem **RowKey**. Na przykład: $filter = nazwisko eq 'Nowak'  
* Zapytań zwracających wiele jednostek zwrócić posortowane w **PartitionKey** i **RowKey** kolejności. Aby uniknąć ponowne sortowanie jednostek na komputerze klienckim, wybierz opcję **RowKey** definiuje najczęściej kolejności sortowania.  

Należy pamiętać, że za pomocą "**lub**" do określenia filtru na podstawie **RowKey** wartości powoduje skanowania partycji i nie jest traktowana jako kwerendy zakresu. W związku z tym należy unikać zapytań, które używać filtrów, takich jak: $filter = PartitionKey eq 'Sales' i (RowKey eq "121" lub RowKey eq "322")  

Przykłady kodu po stronie klienta, korzystające z biblioteki klienta magazynu na wykonanie kwerend wydajne zobacz:  

* [Wykonywanie zapytania punktu za pomocą biblioteki klienta usługi Storage](#executing-a-point-query-using-the-storage-client-library)
* [Trwa pobieranie wiele jednostek za pomocą LINQ](#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](#server-side-projection)  

Przykłady kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli zobacz:  

* [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien saldo trzeba włączyć EGTs (w celu zapewnienia spójności) z wymaganiem, aby rozpowszechniają jednostek wiele partycji (w celu zapewnienia skalowalna).  

W jednym extreme można przechowywać wszystkie obiekty w jednej partycji, ale to może ograniczać skalowalność rozwiązania i uniemożliwi usłudze tabeli mogli wysyłać żądania równoważenia obciążenia. Y. można przechowywać jednej jednostki dla każdej partycji, będzie wysoce skalowalna i które umożliwia usłudze tabeli wysyłać żądania równoważenia obciążenia, ale co może uniemożliwić używanie jednostek grupy transakcji.  

Idealnym **PartitionKey** jest jedną, która pozwala na użycie wydajność zapytań, na którym jest wystarczające partycji w celu zapewnienia jest skalowalne rozwiązanie. Zazwyczaj można zauważyć, że obiekty odpowiednie właściwości, która dystrybuuje jednostek na partycji wystarczającą.

> [!NOTE]
> Na przykład w systemie, która przechowuje informacje dotyczące użytkowników lub pracowników, nazwa użytkownika może być dobrym PartitionKey. Może mieć kilka jednostki, które korzystają z danym UserID jako klucza partycji. Każda jednostka, która przechowuje dane o użytkowniku są pogrupowane w jednej partycji, a więc te jednostki są dostępne przy użyciu transakcji grupy jednostek, przy zachowaniu wysokiej skalowalności.
> 
> 

Istnieją dodatkowe zagadnienia w wybranym **PartitionKey** dotyczących sposobu będzie wstawiania, aktualizowania i usuwania jednostek: zobacz sekcję [projektu do modyfikacji danych](#design-for-data-modification) poniżej.  

### <a name="optimizing-queries-for-the-table-service"></a>Optymalizacja zapytania dotyczące usługi tabel
Usługa tabel automatycznie indeksuje jednostek przy użyciu **PartitionKey** i **RowKey** wartości w jeden indeks klastrowany, dlatego powód punktu zapytania jest najbardziej wydajnym do użycia. Istnieją jednak nie indeksów inną niż indeksu klastrowanego na **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby włączyć wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład Lokalizowanie jednostek pracownika opartych na wiadomości e-mail, identyfikator lub nazwisko. Następujące wzorce w sekcji [wzorce projektowe tabeli](#table-design-patterns) adresów tego rodzaju wymaganie oraz opisano sposoby obchodzić fakt, że usługa tabel nie udostępnia indeksów pomocniczych:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](#intra-partition-secondary-index-pattern) -przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), Włącz szybkie i wydajne wyszukiwań i alternatywne sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec między partycji pomocniczy indeks](#inter-partition-secondary-index-pattern) -przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości w osobnych partycji lub w różnych tabele, aby włączyć szybkie i wydajne wyszukiwanie i sortowanie alternatywne zamówienia przy użyciu różnych **RowKey** wartości.  
* [Wzorzec jednostek indeksu](#index-entities-pattern) — Obsługa indeksu jednostek umożliwiające wydajne wyszukiwanie, które zwracają list jednostek.  

### <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w usłudze tabel
Usługa tabel zwraca jednostki sortowane w kolejności rosnącej według **PartitionKey** , a następnie według **RowKey**. Klucze te są ciągami i w celu zapewnienia poprawnie sortowanie wartości liczbowe, należy przekonwertować je na o stałej długości i uzupełniania zer. Na przykład, jeśli wartość identyfikatora pracownika jest używany jako **RowKey** jest wartość całkowita należy przekonwertować identyfikator **123** do **00000123**.  

Wiele aplikacji ma wymagania dotyczące korzystania z danych posortowane w różnej kolejności: na przykład sortowanie pracowników według nazwy lub przez przyłączenie daty. Następujące wzorce w sekcji [wzorce projektowe tabeli](#table-design-patterns) omówiony sposób alternatywny sortowania dla jednostki:  

* [Wzorzec pomocniczy indeks wewnątrz partycji](#intra-partition-secondary-index-pattern) — przechowywać kopie każdego obiektu przy użyciu różnych wartości RowKey (w tej samej partycji), aby włączyć funkcję szybkiego i wydajne wyszukiwanie i sortowanie alternatywne porządkuje przy użyciu innej wartości RowKey.  
* [Wzorzec między partycji pomocniczy indeks](#inter-partition-secondary-index-pattern) — przechowywać wiele kopii każdej jednostki przy użyciu innej wartości RowKey w osobnych partycji w oddzielnych tabelach umożliwia szybkie i wydajne wyszukiwanie i sortowanie alternatywne porządkuje przy użyciu innej wartości RowKey .
* [Wzorzec końcowego fragmentu dziennika](#log-tail-pattern) -pobrać *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość sortujące odwrotnej daty i czasu kolejności.  

## <a name="design-for-data-modification"></a>Projekt do modyfikacji danych
W tej sekcji koncentruje się na informacjach dotyczących projektowania optymalizacji operacji wstawienia, aktualizacje i usuwa. W niektórych przypadkach należy ocenić równowagę między projekty, które Optymalizuj dla zapytaniach dotyczących projektów, które Optymalizuj dla modyfikacji danych tak samo, jak w projektach relacyjnych baz danych (chociaż techniki zarządzania kompromisy projektu są różne relacyjnej bazy danych). Sekcja [wzorce projektowe tabeli](#table-design-patterns) opisano niektóre wzorce projektowe szczegółowe dla usługi tabel i opisano niektóre te kompromisy. W praktyce znajdziesz, że wiele projektów, zoptymalizowana pod kątem zapytań jednostek również działać efektywne w przypadku modyfikowania jednostek.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optymalizacja wydajności wstawiania, aktualizowania i usuwania operacji
Aby zaktualizować lub usunąć jednostki, musi być mógł zidentyfikować, używając **PartitionKey** i **RowKey** wartości. W tym zakresie wybór **PartitionKey** i **RowKey** do modyfikowania obiektów należy stosować podobnych kryteriów wybrany do obsługi punktu zapytania, ponieważ chcesz zidentyfikować jednostek jak najbardziej wydajny. Czy chcesz użyć nieefektywne partycji lub tabeli skanowanie można zlokalizować jednostki, aby odnaleźć **PartitionKey** i **RowKey** wartości, należy zaktualizować lub usunąć.  

Następujące wzorce w sekcji [wzorce projektowe tabeli](#table-design-patterns) adresów optymalizacji wydajności lub Twoje insert, update i operacji usunięcia:  

* [Wzorzec usunąć dużą](#high-volume-delete-pattern) -Włącz usuwanie dużej liczby jednostek przez zapisanie wszystkich jednostek do usunięcia jednoczesnych w ich własnych osobnej tabeli; usuwanie jednostek przez usunięcie tabeli.  
* [Wzorzec serii danych](#data-series-pattern) -magazynu pełnych danych serii w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzania.  
* [Wzorzec szeroki jednostek](#wide-entities-pattern) — umożliwia przechowywanie jednostek logicznych z więcej niż 252 właściwości wiele jednostek fizycznych.  
* [Wzorzec dużych jednostek](#large-entities-pattern) — magazyn obiektów blob służy do przechowywania dużej wartości.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Zapewnienie spójności w jednostki przechowywanej
Kluczowym czynnikiem, który ma wpływ na wybór klucze optymalizacji modyfikacji danych jest sposób zapewnienia spójności za pomocą atomic transakcji. EGT można używać tylko do działania na jednostek przechowywanych w tej samej partycji.  

Następujące wzorce w sekcji [wzorce projektowe tabeli](#table-design-patterns) adresu zarządzania spójności:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](#intra-partition-secondary-index-pattern) -przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), Włącz szybkie i wydajne wyszukiwań i alternatywne sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec między partycji pomocniczy indeks](#inter-partition-secondary-index-pattern) — przechowywać wiele kopii każdej jednostki przy użyciu różnych wartości RowKey w osobnych partycji lub w oddzielnych tabelach umożliwia szybkie i wydajne wyszukiwanie i sortowanie alternatywne porządkuje przy użyciu różnych **RowKey** wartości.  
* [Wzorzec transakcji po pewnym czasie spójne](#eventually-consistent-transactions-pattern) -włączyć spójności po pewnym czasie działania przez granice partycji lub granice systemu magazynu za pomocą kolejek platformy Azure.
* [Wzorzec jednostek indeksu](#index-entities-pattern) — Obsługa indeksu jednostek umożliwiające wydajne wyszukiwanie, które zwracają list jednostek.  
* [Wzorzec denormalization](#denormalization-pattern) — łączenie dane dotyczące razem w pojedynczej jednostki umożliwiają można pobrać wszystkich danych, należy za pomocą kwerendy pojedynczy punkt.  
* [Wzorzec serii danych](#data-series-pattern) -magazynu pełnych danych serii w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzania.  

Informacje o transakcjach grupy jednostek, w sekcji [transakcji grupy jednostek](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zapewnienie projektu wydajne modyfikacji ułatwia efektywne zapytań
W wielu przypadkach zaprojektować wydajne podczas badania powoduje efektywne modyfikacje, ale zawsze należy ocenić, czy jest to dla konkretnego scenariusza. Niektóre wzorców w sekcji [wzorce projektowe tabeli](#table-design-patterns) jawnie ocenić kompromis między sprawdzanie i modyfikowanie jednostek, a użytkownik powinien zawsze wziąć pod uwagę liczbę każdego typu operacji.  

Następujące wzorce w sekcji [wzorce projektowe tabeli](#table-design-patterns) kompromis między projektowania zapytań wydajne i projektowania modyfikacji danych wydajne rozwiązania:  

* [Wzorca klucza złożonego](#compound-key-pattern) -Użyj złożone **RowKey** wartości, aby włączyć klienta do wyszukiwania powiązanych danych z zapytaniem pojedynczy punkt.  
* [Wzorzec końcowego fragmentu dziennika](#log-tail-pattern) -pobrać *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość sortujące odwrotnej daty i czasu kolejności.  

## <a name="encrypting-table-data"></a>Szyfrowanie danych w tabeli
Biblioteka klienta magazynu Azure .NET obsługuje szyfrowanie właściwości jednostki parametrów w operacjach wstawiania i zamienianie operacji. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są one konwertowana z powrotem do ciągów po odszyfrowywania.    

W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, określając atrybutu [EncryptProperty] (dla jednostki POCO, które pochodzą z TableEntity) lub szyfrowania, program rozpoznawania nazw w opcjach żądania. Rozwiązywanie problemów z szyfrowania jest delegata, który ma klucz partycji, klucz wiersza i nazwy właściwości i zwraca wartość Boolean wskazującą, czy ma być szyfrowana tej właściwości. Podczas szyfrowania biblioteki klienckiej użyje tych informacji do określania, czy właściwości powinny być szyfrowane podczas zapisywania można przewodowo. Delegat także możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, następnie szyfrować właściwości A; w przeciwnym razie szyfrowania właściwości A i B.) Należy pamiętać, że nie jest niezbędne do zapewnienia tych informacji podczas odczytywania lub zapytanie jednostki.

Należy pamiętać, że scalania nie jest obecnie obsługiwany. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej za pomocą innego klucza, po prostu scalanie nowych właściwości i aktualizowania metadanych spowoduje utratę danych. Scalanie albo wymaga wywołań usługi dodatkowe odczytać istniejące jednostki z usługi lub przy użyciu nowego klucza dla właściwości, które nie są odpowiednie ze względu na wydajność.     

Informacje o szyfrowaniu danych z tabeli, zobacz [szyfrowania po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="modelling-relationships"></a>Relacje modelowania
Tworzenie modeli domeny jest klucza krok w projektowaniu złożonych systemów. Zwykle Użyj procesu modelowania do identyfikowania jednostki i relacje między nimi sposób zrozumieć domeny biznesowych i poinformować o podczas projektowania systemu. Tej części przedstawiono sposób niektórych typowych relacji w modelach domen do projektów dla usługi tabel translacji. Proces mapowania z logiczną modelu danych do fizycznego danych NoSQL oparta-modelu bardzo różni się od używanego podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zwykle zakłada proces normalizacji danych zoptymalizowane pod kątem minimalizując nadmiarowość — i deklaratywne możliwość podczas badania, która abstracts jak implementacja jak bazy danych działa.  

### <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacje jeden do wielu obiektów domeny biznesowych występować bardzo często: na przykład w jednym dziale ma wielu pracowników. Istnieje kilka sposobów do zaimplementowania relacje jeden do wielu tabela usługi każdego z zalet i wad, które mogą być istotne dla konkretnego scenariusza.  

Rozważmy przykład dużych korporacji międzynarodowej z dziesiątkami tysięcy działów i podmiotów pracownika, gdzie każdy dział ma wiele pracowników i pracowników, poszczególnych, jak skojarzony z jednym określonym dziale. Jednym z podejść jest do przechowywania działu oddzielne i jednostek pracownika, takich jak te:  

![][1]

W tym przykładzie pokazano niejawnych relacji jeden do wielu między typami na podstawie **PartitionKey** wartości. Każdy dział może mieć wielu pracowników.  

Ten przykład przedstawia również jednostki działu i jego jednostek pokrewnych pracowników w tej samej partycji. Użytkownik może używać różnych partycji, tabel lub kont magazynu nawet dla typów inną jednostkę.  

Informacje o innym podejściu jest denormalize danych i przechowywanie tylko jednostki pracowników z działu nieznormalizowany danych, jak pokazano w poniższym przykładzie. W tym scenariuszu określonego takie podejście nieznormalizowany nie może być najlepiej, jeśli masz wymaganie, aby można było zmienić szczegóły manager działu, ponieważ w tym celu należy zaktualizować każdy pracownik działu.  

![][2]

Aby uzyskać więcej informacji, zobacz [wzorzec Denormalization](#denormalization-pattern) dalszej części tego przewodnika.  

Poniższa tabela zawiera podsumowanie zalet i wad każdej z metod opisanych powyżej do przechowywania pracowników i działu obiektów, które mają relacji jeden do wielu. Należy również rozważyć, jak często mają wykonywać różne operacje: może być akceptowane ma projekt, który zawiera kosztowna operacja, jeśli ta operacja odbywa się tylko rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Specjaliści</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, tej samej partycji, tej samej tabeli</td>
<td>
<ul>
<li>Można aktualizować jednostek działu z jednej operacji.</li>
<li>EGT służy do zapewniania spójności, jeśli nie można zmodyfikować jednostki działu zawsze, gdy użytkownik aktualizacji/insert/usuwania jednostki pracownika. Na przykład, jeśli liczba pracowników działów, dla każdego działu.</li>
</ul>
</td>
<td>
<ul>
<li>Konieczne może być pobieranie zarówno pracownika, jak i do działu jednostki w przypadku niektórych działań klienta.</li>
<li>Operacje magazynu pojawiają się w tej samej partycji. W transakcji wysokiej woluminów może to spowodować punktu aktywnego.</li>
<li>Nie można przenieść pracownika działu nowe przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy oddzielnych jednostek, różnych partycji lub tabel lub kont magazynu</td>
<td>
<ul>
<li>Należy zaktualizować działu jednostki lub jednostek pracowników z jednej operacji.</li>
<li>W transakcji wysokiej woluminów to może pomóc rozłożyć obciążenie na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Konieczne może być pobieranie zarówno pracownika, jak i do działu jednostki w przypadku niektórych działań klienta.</li>
<li>Nie można użyć EGTs, aby zachować spójność podczas możesz aktualizacji/insert/usuwania pracownik i aktualizacji działu. Na przykład aktualizowania liczba pracowników w jednostce działu.</li>
<li>Nie można przenieść pracownika działu nowe przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize do pojedynczej jednostki typu</td>
<td>
<ul>
<li>Można pobrać wszystkich informacji potrzebnych z pojedynczym żądaniem.</li>
</ul>
</td>
<td>
<ul>
<li>Może być kosztowne do zapewniania spójności, aby zaktualizować informacje o działu (to wymaga aktualizacji wszystkich pracowników działu).</li>
</ul>
</td>
</tr>
</table>

Jak można wybrać te opcje, a które z zalet i wad są najważniejsze, zależy od scenariuszy określonej aplikacji. Na przykład jak często należy można modyfikować jednostek działu; czy wszystkie zapytania pracowników muszą dodatkowe informacje dla działu; jak blisko czy do limity skalowalności partycji lub Twoje konto magazynu?  

### <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modele domeny może obejmować jeden do jednego relacje między obiektami. Jeśli musisz wdrożyć relacją w usłudze tabel, musisz wybrać jak połączyć dwóch powiązanych jednostek, gdy trzeba je pobrać. To łącze może być niejawna, oparte na Konwencji w wartości klucza lub jawne, przechowując łącze w formie **PartitionKey** i **RowKey** wartości w każdej jednostki do jego obiektu pokrewnego. Aby uzyskać informacje dotyczące tego, czy należy przechowywać powiązanych jednostek w tej samej partycji, zobacz sekcję [jeden do wielu relacji](#one-to-many-relationships).  

Pamiętaj, że jest także uwagi dotyczące implementacji, które mogą prowadzić do zaimplementowania relacje jeden do jednego w usłudze tabel:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [dużych jednostek wzorzec](#large-entities-pattern)).  
* Wdrożenie kontroli dostępu (Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu przy użyciu sygnatury dostępu współdzielonego](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Dołącz do klienta
Mimo że istnieją sposoby do modelowania relacji w usłudze tabel, należy nie zapomnij, czy dwa główne powody przy użyciu usługi tabel skalowalność i wydajność. Jeśli okaże się, że są modelowania wiele relacji, które negatywnie wpłynąć na wydajność i skalowalność rozwiązania, należy poprosić samodzielnie Jeśli jest niezbędne do utworzenia relacji danych w projekcie tabeli. Można uprościć projektu i zwiększyć skalowalność i wydajność rozwiązania, jeśli umożliwisz aplikacja kliencka wykonać wszelkie niezbędne sprzężenia.  

Na przykład jeśli małych tabel, które zawierają dane, które nie zmienia się bardzo często, następnie można pobrać dane raz i pamięci podręcznej go na komputerze klienckim. Można to uniknąć wielokrotnego dwukierunkowe przesyłanie danych do pobrania tych samych danych. W przykładach, które firma Microsoft sprawdzono, w tym przewodniku prawdopodobnie zbiór działów w organizacji małych małe i zmienić rzadko co odpowiednimi kandydatami dla danych, który aplikacja kliencka można pobrać raz i pamięci podręcznej jako wyszukiwania danych.  

### <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka używa zestawu klas, które stanowią część relacji dziedziczenia do reprezentowania jednostki biznesowe, można łatwo utrwalić tych jednostek w usłudze tabel. Przykładowo może istnieć następujący zestaw klas zdefiniowanych w aplikacji klienta gdzie **osoby** jest klasą abstrakcyjną.

![][3]

Można ją utrwalić wystąpienia dwóch klas konkretnych w usłudze tabel za pomocą pojedynczej tabeli osoby za pomocą jednostek tego wyglądają następująco:  

![][4]

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta, zobacz sekcję [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types) dalszej części tego przewodnika. Zapewnia to przykłady rozpoznaje typ jednostki w kodu klienta.  

## <a name="table-design-patterns"></a>Wzorce projektowe tabeli
W poprzednich sekcjach jak już wspomniano niektóre szczegółowe dyskusjach na temat sposobu optymalizacji projektu tabeli dla obu podczas pobierania danych jednostki, za pomocą zapytań i wstawiania, aktualizowania i usuwania danych jednostki. W tej sekcji opisano niektóre wzorców, które są odpowiednie do użytku z rozwiązań usług tabeli. Ponadto zobaczysz, jak praktycznie rozwiązać niektóre problemy i kompromisy wywoływane wcześniej w tym przewodniku. Poniższy diagram przedstawia relacje między różnych wzorców:  

![][5]

Mapy wzorzec powyżej omówiono niektóre relacje między wzorce (niebieski) i wzorce przed (kolor pomarańczowy), które są opisane w tym przewodniku. Oczywiście istnieje wielu wzorców, które warto uwzględnieniu. Na przykład jedną z kluczowych scenariuszy dla usługi tabel jest użycie [Zmaterializowany wzorzec widoku](https://msdn.microsoft.com/library/azure/dn589782.aspx) z [polecenia zapytania odpowiedzialność podział (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) wzorca.  

### <a name="intra-partition-secondary-index-pattern"></a>Wzorzec pomocniczy indeks wewnątrz partycji
Przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji) Włącz szybkie i wydajne wyszukiwań i alternatywne sortowania przy użyciu różnych **RowKey** wartości. Aktualizacje między kopie były spójne przy użyciu EGT firmy.  

#### <a name="context-and-problem"></a>Kontekst i problem
Usługa tabel automatycznie indeksuje jednostek przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja klienta do pobierania jednostki efektywne wykorzystanie tych wartości. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja klienta można użyć zapytania punktu do pobierania jednostki pracownika przy użyciu nazwy działów i identyfikator ( **PartitionKey** i **RowKey** wartości). Klient może również pobierać jednostki sortowane według identyfikatora pracownika każdego działu.

![][6]

Jeśli chcesz także można znaleźć jednostki pracowników na podstawie wartości z innej właściwości, takie jak adres e-mail, należy użyć mniej wydajne skanowania partycji można odnaleźć dopasowania. Jest to spowodowane usługi tabel nie zapewnia indeksów pomocniczych. Ponadto nie jest dostępna opcja można zażądać listy pracowników posortowanych w kolejności innej niż **RowKey** kolejności.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść braku indeksów pomocniczych, można przechowywać kopie każdego obiektu z każdej kopii przy użyciu innego **RowKey** wartości. Jeśli przechowujesz jednostki o strukturze pokazano poniżej, pozwala na efektywne pobieranie jednostek pracowników na podstawie identyfikatora wiadomości e-mail adres lub pracowników. Prefiks wartości **RowKey**, "empid_" i "email_" umożliwiają kwerendy dla pojedynczego pracownika lub zakres pracowników przy użyciu zakresu adresów e-mail ani identyfikatorów pracownika.  

![][7]

Następujące kryteria filtrowania dwóch (jeden wyszukiwanie według identyfikatora pracowników i jeden wyszukiwanie według adresu e-mail) zarówno Określ punkt zapytania:  

* $filter = (PartitionKey eq 'Sales') i (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq 'Sales') i (RowKey eq 'email_jonesj@contoso.com")  

Po wykonaniu zapytania dla zakresu jednostek pracownika, można określić zakres sortowane w kolejności identyfikator pracownika, lub zakresem sortowane w kolejności adresu e-mail przez wysyłanie zapytań dla jednostek o prefiksu w **RowKey**.  

* Aby znaleźć wszyscy pracownicy działu sprzedaży z identyfikatorem pracownika w przypadku użycia zakresu 000100 do 000199: $filter = (PartitionKey eq 'Sales') i (RowKey ge "empid_000100") i (RowKey le "empid_000199")  
* Aby znaleźć wszyscy pracownicy działu sprzedaży przy użyciu adresu e-mail, począwszy od litery "" Użyj: $filter = (PartitionKey eq 'Sales') i (RowKey ge "email_a") i (lt RowKey "email_b")  
  
  Należy zauważyć, że składnia filtru użyte w powyższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [jednostek zapytania](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tanie do użycia, więc obciążenie koszt przechowywania danych zduplikowane nie powinno być głównym problemem. Jednak należy zawsze ocenić koszt projektu, w zależności od wymagań przewidywanego magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, który aplikacja kliencka będzie wykonywać.  
* Pomocniczy indeks jednostki są przechowywane w tej samej partycji w oryginalnej jednostki, dlatego należy upewnić się, nie przekraczają wartości docelowe skalowalności dla poszczególnych partycji.  
* Można zachować zduplikowane obiekty zgodne ze sobą przy użyciu EGTs można automatycznie zaktualizować dwie kopie jednostki. Oznacza to, że wszystkie kopie jednostki należy przechowywać w tej samej partycji. Aby uzyskać więcej informacji, zobacz sekcję [przy użyciu transakcji grupy jednostek](#entity-group-transactions).  
* Wartość używaną do **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Dopełnienie wartości liczbowe w **RowKey** (na przykład identyfikator 000223), umożliwia Popraw sortowania i filtrowania na podstawie górną i dolną granicę.  
* Koniecznie zbędna, nie mają zostać zduplikowane właściwości jednostki. Na przykład jeśli zapytania tego odnośnika jednostki za pomocą wiadomości e-mail adres w **RowKey** nigdy nie trzeba wiek pracownika, te jednostki można mieć następującą strukturę:

![][8]

* Zwykle lepiej do przechowywania danych duplikatu i upewnij się, że można pobrać wszystkie potrzebne dane z pojedynczego zapytania, niż Aby użyć jednego zapytania, aby zlokalizować jednostki i drugiego wyszukać wymaganych danych.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostek przy użyciu różnych różne klucze, gdy klient musi pobrać jednostki w różnych sortowania, a można zidentyfikować każdej jednostki przy użyciu różnych unikatowe wartości. Jednak należy się upewnić, że nie przekraczają limity skalowalności partycji podczas przeprowadzania wyszukiwania jednostki przy użyciu różnych **RowKey** wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec pomocniczy indeks między partycji](#inter-partition-secondary-index-pattern)
* [Wzorca złożonego klucza](#compound-key-pattern)
* [Grupowanie jednostki transakcji](#entity-group-transactions)
* [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Wzorzec pomocniczy indeks między partycji
Przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości w osobnych partycji lub w oddzielnych tabelach Włącz szybkie i wydajne wyszukiwań i alternatywne sortowania przy użyciu różnych **RowKey** wartości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Usługa tabel automatycznie indeksuje jednostek przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja klienta do pobierania jednostki efektywne wykorzystanie tych wartości. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja klienta można użyć zapytania punktu do pobierania jednostki pracownika przy użyciu nazwy działów i identyfikator ( **PartitionKey** i **RowKey** wartości). Klient może również pobierać jednostki sortowane według identyfikatora pracownika każdego działu.  

![][9]

Jeśli chcesz także można znaleźć jednostki pracowników na podstawie wartości z innej właściwości, takie jak adres e-mail, należy użyć mniej wydajne skanowania partycji można odnaleźć dopasowania. Jest to spowodowane usługi tabel nie zapewnia indeksów pomocniczych. Ponadto nie jest dostępna opcja można zażądać listy pracowników posortowanych w kolejności innej niż **RowKey** kolejności.  

Są przewidywanie bardzo dużą liczbę transakcji względem tych jednostek i chcesz ograniczyć ryzyko ograniczanie klienta usługi tabeli.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść braku indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdym Kopiuj, używając innego **PartitionKey** i **RowKey** wartości. Jeśli przechowujesz jednostki o strukturze pokazano poniżej, pozwala na efektywne pobieranie jednostek pracowników na podstawie identyfikatora wiadomości e-mail adres lub pracowników. Prefiks wartości **PartitionKey**, "empid_" i "email_" umożliwiają identyfikowanie indeksu, który ma być używany dla zapytania.  

![][10]

Następujące kryteria filtrowania dwóch (jeden wyszukiwanie według identyfikatora pracowników i jeden wyszukiwanie według adresu e-mail) zarówno Określ punkt zapytania:  

* $filter = (PartitionKey eq ' empid_Sales") i (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") i (RowKey eq 'jonesj@contoso.com")  

Po wykonaniu zapytania dla zakresu jednostek pracownika, można określić zakres sortowane w kolejności identyfikator pracownika, lub zakresem sortowane w kolejności adresu e-mail przez wysyłanie zapytań dla jednostek o prefiksu w **RowKey**.  

* Można znaleźć wszyscy pracownicy działu sprzedaży z identyfikatorem pracowników w zakresie **000100** do **000199** posortowane w użyciu kolejności identyfikator pracownika: $filter = (PartitionKey eq ' empid_Sales") i (RowKey ge"000100") i (RowKey le"000199")  
* Aby znaleźć wszyscy pracownicy działu sprzedaży, adres e-mail, który rozpoczyna się od "" posortowane w Użyj kolejności adres e-mail: $filter = (PartitionKey eq ' email_Sales") i (RowKey ge"") i (RowKey lt 'b')  

Należy zauważyć, że składnia filtru użyte w powyższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [jednostek zapytania](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Możesz zachować zduplikowane obiekty ostatecznie zgodne ze sobą przy użyciu [wzorzec transakcji po pewnym czasie spójne](#eventually-consistent-transactions-pattern) do zachowania jednostek indeksu podstawowego i pomocniczego.  
* Magazyn tabel jest stosunkowo tanie do użycia, więc obciążenie koszt przechowywania danych zduplikowane nie powinno być głównym problemem. Jednak należy zawsze ocenić koszt projektu, w zależności od wymagań przewidywanego magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, który aplikacja kliencka będzie wykonywać.  
* Wartość używaną do **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Dopełnienie wartości liczbowe w **RowKey** (na przykład identyfikator 000223), umożliwia Popraw sortowania i filtrowania na podstawie górną i dolną granicę.  
* Koniecznie zbędna, nie mają zostać zduplikowane właściwości jednostki. Na przykład jeśli zapytania tego odnośnika jednostki za pomocą wiadomości e-mail adres w **RowKey** nigdy nie trzeba wiek pracownika, te jednostki można mieć następującą strukturę:
  
  ![][11]
* Zwykle lepiej do przechowywania danych duplikatu i upewnij się, że można pobrać wszystkie dane, które należy z pojedynczego zapytania niż Aby użyć jednego zapytania, aby zlokalizować jednostki przy użyciu pomocniczy indeks i drugiego do wyszukiwania danych wymaganych w głównej indeksu jest.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostek przy użyciu różnych różne klucze, gdy klient musi pobrać jednostki w różnych sortowania, a można zidentyfikować każdej jednostki przy użyciu różnych unikatowe wartości. Użyj tego wzorca, gdy chce się uniknąć przekracza limity skalowalności partycji podczas przeprowadzania wyszukiwania jednostki przy użyciu różnych **RowKey** wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójne transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec pomocniczy indeks wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorca złożonego klucza](#compound-key-pattern)  
* [Grupowanie jednostki transakcji](#entity-group-transactions)  
* [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Wzorzec ostatecznie spójne transakcji
Włączyć spójności po pewnym czasie działania przez granice partycji lub granice systemu magazynu za pomocą kolejek platformy Azure.  

#### <a name="context-and-problem"></a>Kontekst i problem
EGTs włączać atomic transakcji między wiele jednostek, które mają ten sam klucz partycji. Względu na wydajność i skalowalność, można zdecydować o przechowywania obiektów, które mają wymagań spójności w osobnych partycji lub w systemie magazynu oddzielne: w takiej sytuacji nie można użyć EGTs, aby zachować spójność. Na przykład może być wymagane, aby zachować spójność ostateczna:  

* Jednostek przechowywanych w dwóch różnych partycji w tej samej tabeli, różnych tabel lub różnych kont magazynu.  
* Jednostki przechowywana w usłudze tabel i obiektu blob przechowywane w usłudze obiektów Blob.  
* Jednostka przechowywane w usłudze tabel i plik w systemie plików.  
* Jednostki magazynu w usłudze tabel jeszcze indeksowany przy użyciu usługi Azure Search.  

#### <a name="solution"></a>Rozwiązanie
Korzystanie z kolejek platformy Azure, można zaimplementować rozwiązania, które zapewnia spójność ostateczna przez dwa lub więcej partycji ani systemów pamięci masowej.
Aby zilustrować takie podejście, założono, że wymaganie możliwość archiwizacji starego jednostek pracownika. Stary jednostki pracownika rzadko będą pytani i powinny być wykluczone z żadnych działań, które zajmują się bieżący pracowników. Aby zaimplementować ten wymóg przechowywania aktywnych pracowników w **bieżącego** tabeli i starego pracowników w **archiwum** tabeli. Archiwizowanie pracownika, musisz usunąć jednostkę z **bieżącego** tabeli i Dodaj jednostki do **archiwum** tabeli, ale nie można użyć EGT do wykonania tych dwóch operacji. Aby uniknąć zagrożenia, powodujący jednostki pojawią się w awarii w obu lub ani tabel, operacja archiwizacji musi być ostatecznie zgodne. Na poniższym diagramie sekwencji opisano kroki w tej operacji. Więcej szczegółów podano ścieżek wyjątków w poniższy tekst.  

![][12]

Klient inicjuje operację archiwum umieszczając wiadomości w kolejce platformy Azure, w tym przykładzie archiwizacji pracownika #456. Rola proces roboczy sonduje kolejkę nowe wiadomości; Po znalezieniu jednego odczytuje komunikat i pozostawia kopię ukryte w kolejce. Roli procesu roboczego obok pobiera kopię jednostka z **bieżącego** tabeli, wstawia kopię w **archiwum** tabeli, a następnie usuwa oryginalną z **bieżącego** tabeli. Jeśli nie wystąpiły błędy z poprzednich kroków, roli procesu roboczego usuwa ukryte wiadomości z kolejki.  

W tym przykładzie krok 4 wstawia pracownika w **archiwum** tabeli. Można go dodać pracownika do obiektu blob w usłudze obiektów Blob lub pliku w systemie plików.  

#### <a name="recovering-from-failures"></a>Odzyskiwanie z błędami
Ważne jest, że czynności w krokach **4** i **5** musi być *idempotentności* w przypadku roli procesu roboczego, należy ponownie uruchomić operację archiwum. Jeśli używasz usługi tabel, dla kroku **4** należy użyć operacji "Wstawianie lub zastępowanie"; kroku **5** należy używać "usunąć, jeśli istnieje" operacji w bibliotece klienta używasz. Jeśli korzystasz z innego systemu magazynu, należy użyć operacji odpowiednie idempotentności.  

Jeśli roli proces roboczy nigdy nie wykonuje kroku **6**, a następnie po upływie limitu czasu pojawi się wiadomość w kolejce roli proces roboczy spróbować ponownie przetworzyć go teraz. Roli procesu roboczego można sprawdzić, ile razy komunikat w kolejce został odczytu i, jeśli to konieczne, Flaga jest komunikat "skażone" do badania przez wysłanie ich do oddzielnych kolejki. Aby uzyskać więcej informacji na temat odczytywania wiadomości w kolejce i sprawdzanie liczby kolejki, zobacz [pobrać wiadomości](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Błędy z tabeli i kolejki usług są błędów przejściowych i aplikacja kliencka powinna zawierać logikę ponawiania odpowiednie do ich obsługi.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie wymaga izolacji transakcji. Na przykład klient może odczytać **bieżącego** i **archiwum** tabel, gdy rola proces roboczy został między krokami **4** i **5**i zobaczyć niespójne widoku danych. Należy pamiętać, że dane będą zgodne po pewnym czasie.  
* Należy upewnić się, że kroki 4 i 5 są idempotentności, aby zapewnić spójność ostateczna.  
* Można też skalować rozwiązania przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy użyć zagwarantować spójność ostateczna między jednostkami, które istnieją w różnych partycji lub tabele. Można rozszerzyć tego wzorca zapewniające spójność ostateczna dla operacji usługi tabel i usługa Blob i innych niż Azure magazynowania źródeł danych, takich jak bazy danych lub systemu plików.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Grupowanie jednostki transakcji](#entity-group-transactions)  
* [Scalanie lub Zastąp](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacji transakcji ważne jest, aby rozwiązania, należy rozważyć zmodyfikowanie tabel do umożliwienia używania EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Obsługa indeksu jednostek umożliwiające wydajne wyszukiwanie, które zwracają list jednostek.  

#### <a name="context-and-problem"></a>Kontekst i problem
Usługa tabel automatycznie indeksuje jednostek przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja klienta do pobierania jednostki efektywne wykorzystanie zapytania punktu. Na przykład za pomocą struktury tabeli pokazano poniżej, aplikacja kliencka pozwala na efektywne pobieranie jednostki pracownika przy użyciu nazwy działów i identyfikator ( **PartitionKey** i **RowKey**).  

![][13]

Jeśli chcesz mieć możliwość pobrania listy jednostek pracowników na podstawie wartości inna właściwość nie jest unikatowa, takie jak nazwisko, również należy użyć mniej wydajne skanowania partycji można znaleźć dopasowania, a nie do odszukania bezpośrednio przy użyciu indeksu. Jest to spowodowane usługi tabel nie zapewnia indeksów pomocniczych.  

#### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska ze strukturą entity przedstawionych powyżej, możesz zachować listę identyfikatorów pracownika. Chcesz pobrać jednostek pracowników o określonej nazwie ostatniego, takich jak Nowak, musi w pierwszej kolejności zlokalizować listę identyfikatorów pracowników dla pracowników z Nowak jako nazwisk, a następnie pobrać tych jednostek pracownika. Istnieją trzy główne opcje przechowywania listy identyfikatorów pracowników:  

* Użyj magazynu obiektów blob.  
* Utwórz indeks jednostki w tej samej partycji co jednostek pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

<u>Opcja #1: Magazyn obiektów blob służy</u>  

Pierwszej opcji utworzenie obiektu blob dla każdego unikatowy nazwisko i w każdym magazynu obiektów blob listę **PartitionKey** (dział) i **RowKey** (identyfikator pracownika) wartości dla pracowników, którzy ostatniego nazwy. Podczas dodawania lub usuwania pracownika należy się upewnić, że zawartość odpowiednich obiektów blob jest ostatecznie zgodne z jednostkami pracownika.  

<u>Opcja #2:</u> utworzenie indeksu jednostek w tej samej partycji  

Druga opcja można użyć indeksu jednostek, które przechowuje następujące dane:  

![][14]

**EmployeeIDs** właściwość zawiera listę identyfikatorów pracowników dla pracowników z nazwisko przechowywane w **RowKey**.  

Poniższe kroki wchodzą w skład procesu, które należy wykonać podczas dodawania nowych pracowników, jeśli używasz drugiej opcji. W tym przykładzie dodajemy pracownika z identyfikatorem 000152 i nazwisko Nowak działu sprzedaży:  

1. Pobrać jednostki indeksu o **PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Nowak". Zapisz element ETag tej jednostki do użycia w kroku 2.  
2. Utwórz jednostki transakcji grupy (to znaczy operacji zbiorczej), która wstawia nową jednostkę pracowników (**PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "000152") i aktualizuje jednostki indeksu (**PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Nowak") przez dodanie nowego identyfikatora pracowników do listy w polu EmployeeIDs. Aby uzyskać więcej informacji o transakcji grupy jednostek, zobacz [transakcji grupy jednostek](#entity-group-transactions).  
3. Jeśli jednostka transakcji grupy zakończy się niepowodzeniem ze względu na błąd optymistycznej współbieżności (ktoś właśnie zmodyfikował jednostki indeksu), następnie należy zacząć od początku w kroku 1 ponownie.  

Można użyć podejście podobne do usuwania pracownika, jeśli używasz drugiej opcji. Zmiana jego nazwisko jest nieco bardziej skomplikowane, ponieważ będą potrzebne do wykonania transakcji grupy jednostki, która aktualizuje trzy jednostki: Jednostka pracownika, jednostki indeksu dla starego nazwisko i jednostki indeksu dla nowej nazwy ostatniego. Każda jednostka musi pobrać przed wprowadzeniem jakichkolwiek zmian w celu pobrania wartości ETag, które następnie można przeprowadzić aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki wchodzą w skład procesu, które należy wykonać w przypadku konieczności wyszukania wszystkich pracowników z danym nazwisko w dziale, korzystając z drugą opcją. W tym przykładzie czekamy wszystkich pracowników z nazwisko Nowak działu sprzedaży:  

1. Pobrać jednostki indeksu o **PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Nowak".  
2. Przeanalizować listy identyfikatorów w polu EmployeeIDs pracowników.  
3. Aby uzyskać dodatkowe informacje na temat każdego z tych pracowników (na przykład ich adresów e-mail), należy pobrać poszczególnych jednostek pracowników przy użyciu **PartitionKey** wartość "Sprzedaż" i **RowKey** wartości z listy pracowników uzyskanym w kroku 2.  

<u>Opcja #3:</u> Utwórz indeks jednostki w oddzielnej partycji lub tabeli  

Trzecia opcja można użyć indeksu jednostek, które przechowuje następujące dane:  

![][15]

**EmployeeIDs** właściwość zawiera listę identyfikatorów pracowników dla pracowników z nazwisko przechowywane w **RowKey**.  

Trzecia opcja EGTs nie można użyć do zapewniania spójności jednostek indeksu znajdują się w oddzielnej partycji z jednostek pracownika. Należy się upewnić, że jednostek indeksu są ostatecznie zgodne z jednostkami pracownika.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwa zapytania można pobrać zgodne jednostki: jeden do badania jednostek indeksu, aby uzyskać listę **RowKey** wartości, a następnie zapytania, aby pobrać każdego obiektu z listy.  
* Biorąc pod uwagę, że poszczególne jednostki ma maksymalnie rozmiar równy 1 MB, opcję #2 i #3 w rozwiązaniu założono lista pracowników identyfikatorów dowolnym danym nazwisko nigdy nie jest większa niż 1 MB. Jeśli lista identyfikatorów pracowników jest może być większa niż 1 MB, rozmiar, użyj opcji #1 i przechowywać dane indeksu w magazynie obiektów blob.  
* Jeśli używasz opcji #2 (przy użyciu EGTs obsługiwać Dodawanie i usuwanie pracowników i zmienianie jego nazwisko) należy ocenić Jeśli wielkość transakcji będzie podejścia limity skalowalności w danej partycji. Jeśli jest to możliwe, należy rozważyć rozwiązanie po pewnym czasie spójne (#1 lub opcję #3), które używa kolejek do obsługi żądań aktualizacji i umożliwia przechowywanie obiekty indeksu w oddzielnej partycji z jednostek pracownika.  
* Opcja #2 w tym rozwiązaniu założono, że do wyszukiwania według nazwisk działu: na przykład chcesz pobrać listę pracowników z nazwisko Nowak działu sprzedaży. Jeśli chcesz można było odszukać wszystkich pracowników z nazwisko Kowalski w całej organizacji, należy użyć opcji #1 lub opcja #3.
* Można implementować rozwiązania na podstawie kolejki, które zapewnia spójność ostateczna (zobacz [wzorzec transakcji po pewnym czasie spójne](#eventually-consistent-transactions-pattern) więcej szczegółów).  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy użyć wyszukać zestawu jednostek, że wszystkie mają wspólne wartość właściwości, na przykład wszystkich pracowników z nazwisko Nowak.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorca złożonego klucza](#compound-key-pattern)  
* [Wzorzec ostatecznie spójne transakcji](#eventually-consistent-transactions-pattern)  
* [Grupowanie jednostki transakcji](#entity-group-transactions)  
* [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Wzorzec denormalization
Łączenie danych powiązanych ze sobą w pojedynczej jednostki pozwalające pobrać wszystkie dane, które należy za pomocą kwerendy pojedynczy punkt.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zwykle normalizacji danych, aby usunąć zduplikowane co w zapytaniach, które pobierają dane z wielu tabel. Jeśli normalizacji danych w tabelach platformy Azure, musisz wprowadzić wiele rund od klienta do serwera powiązanych danych. Na przykład struktury tabeli pokazano poniżej możesz wymaga dwóch rund można pobrać szczegółów dla działu: jeden do pobierania jednostki działu, która zawiera identyfikator menedżera, a następnie inne żądania w celu pobrania szczegółów Menedżera w jednostce pracownika.  

![][16]

#### <a name="solution"></a>Rozwiązanie
Zamiast przechowywanie danych w dwa osobne jednostki, denormalize danych i przechowywać kopię szczegóły menedżera w jednostce działu. Na przykład:  

![][17]

Z jednostkami działu przechowywane z tymi właściwościami można teraz pobrać wszystkie szczegóły, które należy o dział przy użyciu zapytania punktu.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Brak niektórych koszt nakładów pracy związanych z przechowywaniem niektóre dane dwa razy. Wydajność korzyści (wynikające z mniejszą liczbę żądań do usługi magazynu) zwykle przeważa nad brzegowego wzrost kosztów magazynowania (i ten koszt częściowo jest przesuwane przez redukcję liczby transakcji wymagają można pobrać szczegółów dział).  
* Należy zachować spójność dwie jednostki, w których są przechowywane informacje o menedżerach. Problem spójności można obsługiwać przy użyciu EGTs zaktualizować wiele jednostek w ramach jednej transakcji atomic: w tym przypadku jednostki działu, a jednostka pracowników działu Menedżera są przechowywane w tej samej partycji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy używać wtedy, gdy często konieczne do odszukania powiązane informacje. Ten wzorzec zmniejsza liczbę klienta należy wprowadzić, aby pobrać dane, które wymaga zapytań.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorca złożonego klucza](#compound-key-pattern)  
* [Grupowanie jednostki transakcji](#entity-group-transactions)  
* [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Wzorca złożonego klucza
Użyj złożone **RowKey** wartości, aby włączyć klienta do wyszukiwania powiązanych danych z zapytaniem pojedynczy punkt.  

#### <a name="context-and-problem"></a>Kontekst i problem
Relacyjnej bazy danych jest dość fizycznych użyć sprzężenia w zapytaniach w celu zwracania powiązane elementy danych do klienta w jednym zapytaniu. Na przykład można użyć do wyszukiwania listę powiązanych jednostek zawierających wydajności i przeglądanie danych dla określonego pracownika identyfikator pracownika.  

Przyjęto założenie, że pracownik jednostki są przechowywane w usłudze tabel przy użyciu następującej struktury:  

![][18]

Należy również do przechowywania danych historycznych dotyczących przeglądy i wydajności dla każdego roku, w których pracownik pracował dla Twojej organizacji i muszą być w stanie uzyskać dostępu do tych informacji przez rok. Jedną z opcji jest można utworzyć innej tabeli, która przechowuje jednostek o następującej strukturze:  

![][19]

Zwróć uwagę z tej metody użytkownik może zdecydować o zduplikowane niektóre informacje (takie jak imię i nazwisko) w nowej jednostce umożliwiają pobieranie danych z pojedynczym żądaniem. Nie można jednak obsługa wysoki poziom spójności, ponieważ nie można użyć EGT można automatycznie zaktualizować dwie jednostki.  

#### <a name="solution"></a>Rozwiązanie
Przechowywanie nowy typ jednostek w oryginalnej tabeli przy użyciu jednostek o następującej strukturze:  

![][20]

Powiadomienie jak **RowKey** jest teraz klucza złożonego identyfikator pracownika oraz roku danych przeglądu, który pozwala na pobieranie pracownika wydajności i przejrzyj dane z pojedynczego żądania dla pojedynczej jednostki.  

Poniższy przykład przedstawia, jak można pobrać wszystkich danych przeglądu danego pracownika (na przykład 000123 pracownik działu sprzedaży):  

$filter = (PartitionKey eq 'Sales') i (RowKey ge "empid_000123") i (lt RowKey "empid_000124") & $select = RowKey, Menedżer klasyfikacji, Klasyfikacja równorzędnej, komentarze  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego separatora, który można łatwo analizować **RowKey** wartość: na przykład **000123_2012**.  
* Tej jednostki są także przechowywane w tej samej partycji co inne jednostki, które zawierają dane dotyczące dla tego samego pracownika, co oznacza, że EGTs służy do zapewniania spójności silne.
* Należy rozważyć, jak często będą zapytania danych, aby ustalić, czy ten wzorzec jest odpowiedni.  Na przykład jeśli będą uzyskiwać dostęp do rzadko przeglądanie danych i często dane główne pracownika należy go przechowywać jako osobne jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca należy przechowywać jedną lub więcej jednostek powiązanych z tej kwerendy można często.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Grupowanie jednostki transakcji](#entity-group-transactions)  
* [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types)  
* [Wzorzec ostatecznie spójne transakcji](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Wzorzec końcowego fragmentu dziennika
Pobrać *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość sortujące odwrotnej daty i czasu kolejności.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymaganiem jest aby można było pobrać ostatnio utworzonych jednostki, na przykład 10 najnowszych wydatków oświadczenia złożone przez pracownika. Obsługa wysyła zapytanie do tabeli **$top** operacji do zwrócenia pierwszego zapytania *n* jednostek z zestawu: Brak operacji równoważne zapytania do zwrócenia n ostatnich jednostki w zestawie.  

#### <a name="solution"></a>Rozwiązanie
Przechowywanie jednostek przy użyciu **RowKey** czy naturalnie sortowania w kolejności odwrotnej daty/godziny przy użyciu tak ostatni wpis jest zawsze pierwsza z nich w tabeli.  

Na przykład aby można było pobrać 10 najnowszych oświadczenia wydatków przesłane przez pracownika, używając wartości osi w odwrotnej pochodzi od bieżącej daty/godziny. Poniższy przykład kodu C# przedstawia sposób utworzyć odpowiednie wartości "odwrócony Takty" **RowKey** który sortuje od ostatniego najstarszym:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić na wartość daty i godziny przy użyciu następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy konsoli wartości osi w odwrotnej z zer, aby upewnić się, że wartość ciągu Sortuje zgodnie z oczekiwaniami.  
* Należy pamiętać o wartości docelowe skalowalności na poziomie partycji. Uważaj, aby nie tworzyć partycji punktu aktywnego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli musisz uzyskiwać dostęp do jednostki w kolejności odwrotnej daty/godziny lub kiedy trzeba uzyskać dostępu do jednostek ostatnio dodane.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Dołączenie wartości / append przed wzorca](#prepend-append-anti-pattern)  
* [Pobieranie jednostki](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Wzorzec delete dużych obciążeń
Włącz usuwanie dużej liczby jednostek przez zapisanie wszystkich jednostek do usunięcia jednoczesnych w ich własnych osobnej tabeli; Możesz usunąć jednostek przez usunięcie tabeli.  

#### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji Usuń stare dane, nie będzie już potrzebował mają być dostępne dla aplikacji klienckich, lub aplikacji ma zarchiwizowane na innym nośniku. Zwykle określenie tych danych w dniu: na przykład nie można usunąć rekordy wszystkich żądań logowania, które są starsze niż 60 dni.  

Jeden projekt możliwe jest użycie datę i godzinę żądania logowania **RowKey**:  

![][21]

Takie podejście pozwala uniknąć hotspotami partycji, ponieważ aplikacja może Wstawianie i usuwanie jednostek logowania dla każdego użytkownika w oddzielnej partycji. Jednak ta metoda może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek, ponieważ najpierw należy przeprowadzić skanowanie tabeli, aby zidentyfikować wszystkie jednostki do usunięcia, a następnie musi Usuń każdej jednostki stary. Należy pamiętać, że można zmniejszyć liczby rund do serwera wymagany w celu usunięcia starego jednostek przez przetwarzanie wsadowe wiele żądań delete służących do EGTs.  

#### <a name="solution"></a>Rozwiązanie
Użyj osobnej tabeli dla każdego dnia prób logowania. Powyżej projektu jednostki można użyć w celu uniknięcia punkty aktywne podczas wstawiania jednostek i usuwania starych jednostek jest teraz po prostu kwestia usunięcie jednej tabeli codziennie (operację jednego magazynu) zamiast znajdowania i usuwania setki i tysiącami jednostek logowania poszczególnych każdego dnia.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Projekt obsługuje inne sposoby aplikacja będzie korzystać z danych, takich jak wyszukiwania konkretnych obiektów połączeń przy użyciu innych danych lub generowania agregują informacje?  
* Projektu uniknąć punkty aktywne wstawiając nowych jednostek  
* Oczekiwane opóźnienia, jeśli chcesz ponownie użyć tej samej nazwy tabeli po jego usunięciu. Warto zawsze używać unikatowej tabeli nazw.  
* Oczekiwać, że niektóre ograniczenia przepustowości przy pierwszym użyciu nową tabelę podczas usługi tabel uzyskuje informacje o wzorce dostępu i rozpowszechnia partycji w węzłach. Należy rozważyć, jak często należy utworzyć nowe tabele.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli masz dużą liczbę jednostek, które należy usunąć w tym samym czasie.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Grupowanie jednostki transakcji](#entity-group-transactions)
* [Modyfikowanie jednostek](#modifying-entities)  

### <a name="data-series-pattern"></a>Wzorzec serii danych
Seria pełnych danych magazynu w pojedynczej jednostki, aby zminimalizować liczbę żądań, które należy wykonać.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz obejmuje dla aplikacji, aby przechowywać serię danych, które zwykle należy pobrać jednocześnie. Na przykład aplikacja może rejestrować liczbę wiadomości Błyskawiczne każdy pracownik wysyła co godzinę, a następnie te informacje służą do wykreślenia ile komunikatów każdego użytkownika wysłanych w ciągu poprzednich 24 godzin. Jeden projekt może się do przechowywania 24 jednostek dla każdego pracownika:  

![][22]

Ten projekt można łatwo zlokalizować i aktualizować jednostki do zaktualizowania dla każdego pracownika, gdy aplikacja musi zaktualizować wartości liczby komunikatów. Jednak aby pobrać informacje do wykreślenia wykresu działania w ciągu poprzednich 24 godzin, należy pobrać 24 jednostek.  

#### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z właściwością oddzielne do przechowywania liczba komunikatów dla każdej godziny:  

![][23]

Ten projekt umożliwia operacji scalania zaktualizować liczba komunikatów dla pracowników na określone godziny. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli pełnych danych serii nie pasuje do pojedynczej jednostki (jednostka może mieć maksymalnie 252 właściwości), należy użyć magazynu danych alternatywnych, takich jak obiektu blob.  
* Jeśli wielu klientów jednocześnie aktualizowania jednostki, będą musieli używać **ETag** do zaimplementowania optymistycznej współbieżności. Jeśli wielu klientów, może wystąpić wysokiej rywalizacji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Używać tego wzorca należy zaktualizować i pobrać skojarzony z jednostką poszczególnych serii danych.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scalanie lub Zastąp](#merge-or-replace)  
* [Wzorzec transakcji po pewnym czasie spójne](#eventually-consistent-transactions-pattern) (jeśli są przechowywane serii danych w obiekcie blob)  

### <a name="wide-entities-pattern"></a>Wzorzec szeroki jednostek
Użycie wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedynczą jednostkę może mieć maksymalnie 252 właściwości (z wyjątkiem właściwości systemu obowiązkowe) i nie można przechowywać więcej niż 1 MB danych, w sumie. W relacyjnej bazie danych zwykle jak round limit rozmiaru wiersza przez dodanie nowej tabeli i wymuszania między nimi relacji 1-do-1.  

#### <a name="solution"></a>Rozwiązanie
Korzystanie z usługi tabeli, można przechowywać wiele jednostek do reprezentowania obiekt pojedynczego dużych firm z więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać licznik wiadomości Błyskawicznych wysyłane przez każdego pracownika w ciągu ostatnich 365 dni, można użyć następujących projekt, który używa dwie jednostki z różnych schematów:  

![][24]

Jeśli trzeba wprowadzić zmianę, która wymaga aktualizowanie zarówno jednostek, aby były synchronizowane ze sobą służy EGT. W przeciwnym razie umożliwia operacji scalania pojedynczego zaktualizować liczba komunikatów dla określonego dnia. Można pobrać wszystkich danych dla poszczególnych pracowników musi pobrać obie te jednostki, co można zrobić za pomocą dwóch wydajne żądania, które używają obu **PartitionKey** i **RowKey** wartości.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Pobieranie całą jednostkę logiczną obejmuje co najmniej dwóch transakcji magazynowych: jedna, aby pobrać każdej fizycznej jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy konieczne do przechowywania obiektów, których rozmiar lub liczbę właściwości przekracza limity dla poszczególnych jednostek w usłudze tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Grupowanie jednostki transakcji](#entity-group-transactions)
* [Scalanie lub Zastąp](#merge-or-replace)

### <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Użyj magazynu obiektów blob do przechowywania dużej wartości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Poszczególne jednostki nie można przechowywać więcej niż 1 MB danych w całości. Jeśli jeden lub kilka właściwości przechowywane wartości powodujące całkowity rozmiar jednostki przekracza tę wartość, nie można zapisać całą jednostkę w usłudze tabel.  

#### <a name="solution"></a>Rozwiązanie
Jeśli jednostki przekracza 1 MB rozmiar, ponieważ co najmniej jednej właściwości zawierają dużą ilość danych, można przechowywać dane w usłudze obiektów Blob i następnie przechowywać we właściwości w obiekcie adres obiektu blob. Na przykład można przechowywać zdjęcie pracownika w magazynie obiektów blob i przechowywać łącze do zdjęć w **fotografii** właściwości jednostki pracowników:  

![][25]

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczna między jednostki w usłudze tabel i danych w usłudze obiektów Blob, użyj [wzorzec transakcji po pewnym czasie spójne](#eventually-consistent-transactions-pattern) do zachowania jednostek.
* Pobieranie całą jednostkę obejmuje co najmniej dwóch transakcji magazynowych: jeden do pobierania jednostki i jeden do pobierania danych obiektów blob.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec używać do przechowywania obiektów, których rozmiar przekracza limity dla poszczególnych jednostek w usłudze tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójne transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec szeroki jednostek](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Dołączenie wartości Dołącz przed wzorca
Zwiększenie skalowalności, jeśli masz dużą liczbę operacji wstawienia przez rozłożenie wstawianych przez wiele partycji.  

#### <a name="context-and-problem"></a>Kontekst i problem
Dołączanie lub dołączanie jednostek przechowywanych jednostek zwykle powoduje dodanie nowych jednostek do pierwszego lub ostatniego partycji sekwencji partycji aplikacji. W takim przypadku wszystkie wstawia w danym momencie odbywają się w tej samej partycji, tworzenie punktu aktywnego, który uniemożliwia usługi tabel z obciążenia równoważenia operacji wstawienia na wielu węzłach i może doprowadzić do aplikacji trafienie wartości docelowe skalowalności dla partycji. Na przykład jeśli masz aplikację, która dzienniki sieci i dostęp do zasobów przez pracowników, następnie jednostki struktury w sposób przedstawiony poniżej może skutkować partycji bieżącej godziny staje się aktywny, jeśli wielkość transakcji osiągnie docelową skalowalność dla poszczególnych partycji:  

![][26]

#### <a name="solution"></a>Rozwiązanie
Z następującej struktury jednostek alternatywnych pozwala uniknąć punkt aktywny na określonej partycji jako dzienniki zdarzeń aplikacji:  

![][27]

Powiadomienie z tym przykładem sposobu zarówno **PartitionKey** i **RowKey** są klucze złożone. **PartitionKey** używa identyfikatora dziale i pracowników, aby rozpowszechniają rejestrowanie wielu partycji.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Alternatywne klucza strukturę, która pozwala uniknąć tworzenia gorących partycji w operacji wstawienia wydajnie obsługuje zapytania, który sprawia, że aplikacja kliencka?  
* Przewidywany woluminu transakcji oznacza że użytkownik prawdopodobnie do osiągnięcia wartości docelowe skalowalności dla poszczególnych partycji i jest ograniczany przez usługę magazynu?  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Unikaj prepend/dołączanie wzorzec przed po może spowodować ograniczanie przez usługę magazynu, gdy uzyskujesz dostęp do partycji gorących woluminu transakcji.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorca złożonego klucza](#compound-key-pattern)  
* [Wzorzec końcowego fragmentu dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Wzorzec przed dane dziennika
Zwykle należy używać usługi Blob zamiast usługi tabel do przechowywania danych.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy przypadek użycia dla danych dziennika jest pobrać wybrane wpisy dziennika dla zakresu określonej daty/godziny: na przykład chcesz znaleźć wszystkie i krytyczne komunikaty o błędach zarejestrowane przez aplikację między 15:04 i 15:06 w określonym dniu. Czy chcesz użyć datę i godzinę komunikatu dziennika ustalenie partycji, Zapisz dziennik jednostki do: wyniki w partycji gorących ponieważ w dowolnym momencie wszystkie jednostki dziennika udostępni takie same **PartitionKey** wartość (zobacz sekcję [Prepend/dołączanie wzorzec przed](#prepend-append-anti-pattern)). Na przykład następujące schematu encji komunikatu dziennika wyniki gorących partycji, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika do partycji dla bieżącej daty i godziny:  

![][28]

W tym przykładzie **RowKey** daty i godziny komunikatu dziennika, aby upewnić się, że komunikaty dziennika są przechowywane sortowane w kolejności daty/godziny i zawierają identyfikator komunikatu w przypadku, gdy wiele komunikatów dziennika udostępnianie tego samego daty i godziny.  

Innym rozwiązaniem jest użycie **PartitionKey** która sprawia, że aplikacja zapisuje komunikaty w zakresie partycji. Na przykład jeśli źródło komunikatu dziennika umożliwia rozłożenie wiadomości między wiele partycji, można użyć następującego schematu jednostki:  

![][29]

Problem z tym schemacie jest jednak, że można pobrać wszystkie komunikaty dziennika dla określonych okres wyszukiwanie każdej partycji w tabeli.

#### <a name="solution"></a>Rozwiązanie
Poprzedniej sekcji wyróżnione problem próbuje użyć usługi tabel do przechowywania wpisy dziennika i sugerowane dwa, niezadowalające, projektów. Jedno rozwiązanie, które doprowadziły do dynamicznej partycji z ryzykiem niską wydajnością, zapisywanie wiadomości dziennika. inne rozwiązanie w wyniku zapytania niską wydajność ze względu na wymaganie skanowanie każdej partycji w tabeli, aby pobrać komunikaty dziennika dla zakresu określonego czasu. Magazyn obiektów blob oferuje lepszym rozwiązaniem dla tego typu scenariuszy i są to zbiera jak Azure analityka magazynu magazynów danych dziennika.  

W tej sekcji opisano, jak analityka magazynu przechowuje dane dziennika w magazynie obiektów blob ilustracją tego podejścia do przechowywania danych, które zwykle zapytania według zakresu.  

Analityka magazynu przechowuje komunikaty dziennika w formacie rozdzielanym w wielu obiektów blob. Format rozdzielanej ułatwia aplikacji klienckiej, można przeanalizować danych w komunikacie dziennika.  

Analityka magazynu używa konwencji nazewnictwa dla obiektów blob, które umożliwia lokalizowanie obiekt blob (lub obiektów blob) zawierające komunikaty dziennika, dla których w przypadku wyszukiwania. Na przykład obiektu blob o nazwie "queue/2014/07/31/1800/000001.log" zawiera komunikatów dziennika, które odnoszą się do usługi kolejki na godzinę, począwszy od 18:00 w 31 lipca 2014 r. "000001" wskazuje, że to jest pierwszy plik dziennika dla tego okresu. Analityka magazynu rejestruje także sygnatury komunikatów dziennika imię i nazwisko, przechowywane w pliku jako część metadane obiektu blob. Interfejs API dla Znajdź obiekty BLOB w kontenerze na podstawie prefiksu nazwy umożliwia magazynu obiektów blob: Aby znaleźć wszystkie obiekty BLOB, które zawierają dane dziennika kolejki na godzinę, począwszy od 18:00, można używać prefiksu "kolejki/2014/07/31/1800."  

Bufory analityka magazynu komunikaty dziennika wewnętrznie a następnie okresowo aktualizuje odpowiednich obiektów blob lub tworzy nowy adres w usłudze partia najnowsze wpisy dziennika. Zmniejsza liczbę operacji zapisu, które należy wykonać, aby usługa blob.  

W przypadku wdrażania rozwiązania podobne w swojej aplikacji, należy rozważyć sposób zarządzania równowagę między niezawodności (zapisywania każdy wpis dziennika do magazynu obiektów blob, ponieważ występuje) i kosztów i skalowalność (buforowanie aktualizacji aplikacji i zapisaniu ich do magazynu obiektów blob w partiach).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie przechowywania danych, należy wziąć pod uwagę następujące kwestie:  

* Jeśli tworzysz projekt tabeli, pozwalający na uniknięcie potencjalnych gorących partycji, może się okazać, że nie masz dostępu do danych dziennika wydajnie.  
* Do przetwarzania danych dziennika, gdy klient często musi załadować wiele rekordów.  
* Mimo że często mają strukturę danych dziennika, magazynu obiektów blob może okazać się lepszym rozwiązaniem.  

### <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia dotyczące przy tym pamiętać podczas implementowania wzorce opisanych w poprzednich sekcjach. Większość ta sekcja używa przykłady napisane w języku C#, korzystających z biblioteki klienta usługi Storage (wersja 4.3.0 w czasie zapisywania).  

### <a name="retrieving-entities"></a>Pobieranie jednostki
Zgodnie z opisem w sekcji [projektowania zapytań](#design-for-querying), najbardziej efektywne zapytanie jest zapytaniem punktu. Jednak w niektórych scenariuszach może być konieczne pobrać wiele jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek za pomocą biblioteki klienta usługi Storage.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Wykonywanie zapytania punktu za pomocą biblioteki klienta usługi Storage
Najprostszym sposobem wykonania kwerendy punkt jest użycie **pobrać** tabeli operacji, jak pokazano w poniższych C# fragmentu kodu, które pobiera jednostki z **PartitionKey** wartości "Sprzedaż" i **RowKey** wartości "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekuje jednostki pobierania typu **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Trwa pobieranie wiele jednostek za pomocą LINQ
Możesz pobrać wiele jednostek za pomocą LINQ z biblioteki klienta usługi Storage i określenie zapytania z **gdzie** klauzuli. Aby uniknąć skanowania tabeli, należy zawsze pamiętać **PartitionKey** wartości w klauzuli where klauzuli oraz w miarę możliwości **RowKey** wartość, aby uniknąć skanowania tabeli i partycji. Usługa tabel obsługuje ograniczony zestaw operatory porównania (większe, większa niż lub równy mniej niż mniej niż lub równy takie same i nie równa) do użycia w przypadku, gdy klauzula. Poniższy fragment kodu C# znajduje wszystkich pracowników, których ostatnia nazwa rozpoczyna się od znaku "B" (przy założeniu, że **RowKey** przechowuje nazwisko) z działu sprzedaży (przy założeniu **PartitionKey** przechowuje nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Zwróć uwagę, jak zapytanie określa zarówno **RowKey** i **PartitionKey** zapewniające lepszą wydajność.  

Poniższy przykład kodu pokazuje równoważne funkcje przy użyciu interfejsu API fluent (Aby uzyskać więcej informacji o interfejsach API fluent ogólnie rzecz biorąc, zobacz [najlepsze rozwiązania dotyczące projektowania Fluent API](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Przykład zagnieżdżony wielu **CombineFilters** metody służące do dołączania trzy warunki filtru.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Pobieranie dużą liczbą jednostek w wyniku zapytania
Optymalne zapytanie zwraca pojedynczą jednostkę na podstawie **PartitionKey** wartość i **RowKey** wartości. Jednak w niektórych scenariuszach może być wymagane zwraca wiele jednostek w tej samej partycji lub nawet z wielu partycji.  

W takich scenariuszach należy zawsze pełni przetestować wydajność aplikacji.  

Zapytanie usługi tabel może zwrócić maksymalnie 1000 jednostek w tym samym czasie i mogą realizować maksymalnie pięć sekund. Jeśli zestaw wyników zawiera więcej niż 1000 jednostek, jeśli kwerenda nie została ukończona w ciągu pięciu sekund lub jeśli zapytanie przecina granicę partycji, usługa tabel zwraca token kontynuacji, aby umożliwić aplikacji klienckiej zażądać następnego zestawu jednostek. Aby uzyskać więcej informacji dotyczących sposobu kontynuacji tokeny pracy, zobacz [limit czasu zapytania i podział na strony](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

Jeśli korzystasz z biblioteki klienta usługi Storage, go automatycznie obsługiwać tokenów kontynuacji dla Ciebie zgodnie z usługi tabel zwraca jednostek. Poniższy C# przykładowy kod automatycznie za pomocą biblioteki klienta usługi Storage obsługuje kontynuacji tokenów, jeśli usługa tabel zwraca je w odpowiedzi:  

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

Poniższy kod C# obsługuje tokenów kontynuacji jawnie:  

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

Za pomocą tokenów kontynuacji jawnie, można kontrolować, gdy aplikacja pobiera następnego segmentu danych. Na przykład jeśli aplikacja kliencka umożliwia użytkownikom strony za pomocą jednostek przechowywane w tabeli, użytkownik może zrezygnować z przeglądania wszystkich jednostek, które są pobierane przez zapytanie, więc aplikacja użyje tylko token kontynuacji, aby pobrać następny segment, gdy użytkownik miał zakończył stronicowania za pośrednictwem wszystkich jednostek w bieżącym segmencie. Takie podejście ma wiele zalet:  

* Umożliwia ograniczenie ilości danych, które można pobrać z usługi tabel i Przenieś za pośrednictwem sieci.  
* Umożliwia ona wykonywanie asynchroniczne We/Wy w .NET.  
* Umożliwia ona serializować token kontynuacji do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zwykle zwraca segment zawierający 1000 jednostek, chociaż może być mniej. Dotyczy to również ograniczyć liczbę wpisów zapytanie zwraca przy użyciu **zająć** do zwrócenia pierwsze n jednostek spełniających podane kryteria wyszukiwania: Usługa tabel może zwrócić segment zawiera mniej niż n jednostki oraz token kontynuacji, aby umożliwić pobieranie pozostałych jednostek.  
> 
> 

Poniższy kod C# przedstawia sposób modyfikowania liczba zwróconych wewnątrz segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekcja po stronie serwera
Pojedynczy element można mieć maksymalnie 255 właściwości i mieć rozmiar maksymalnie 1 MB. Gdy zapytanie tabeli i pobrać jednostek, mogą nie być potrzebne wszystkie właściwości i można uniknąć transferu danych niepotrzebnie (w celu zmniejszenia opóźnienia i kosztów). Projekcja po stronie serwera umożliwia transfer tylko właściwości, które należy. Poniższy przykład pobiera tylko **E-mail** właściwości (wraz z **PartitionKey**, **RowKey**, **sygnatury czasowej**i **ETag**) z jednostek wybrane przez zapytanie.  

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

Powiadomienie jak **RowKey** wartość jest dostępna, nawet jeśli nie została uwzględniona na liście właściwości do pobrania.  

### <a name="modifying-entities"></a>Modyfikowanie jednostek
Biblioteka klienta magazynu umożliwia modyfikowanie obiekty przechowywane w usłudze tabeli, wstawianie, usuwanie i aktualizowanie jednostek. EGTs Umożliwia wsadowe wielu insert, update i operacji usuwania ze sobą w celu zmniejszenia liczby rund wymagane i zwiększyć wydajność rozwiązania.  

Zwróć uwagę, że wyjątki zgłaszane, gdy biblioteka klienta magazynu wykonuje EGT zwykle obejmuje indeks jednostki, który spowodował niepowodzenie zadania wsadowego. Jest to przydatne podczas debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projektu wpływa na sposób obsługi przez aplikację kliencką operacje współbieżności i aktualizacji.  

#### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa tabel implementuje optymistycznej współbieżności testy na poziomie poszczególnych jednostek dla **Wstaw**, **scalania**, i **usunąć** operacje, chociaż można wymusić uruchomienia usługi tabeli, aby pominąć te sprawdzenia klienta. Aby uzyskać więcej informacji o zarządzaniu współbieżności usługi tabel, zobacz [Zarządzanie współbieżność w magazynie platformy Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Scalanie lub Zastąp
**Zastąp** metody **TableOperation** klasy zastąpi całą jednostkę w usłudze tabel. Jeśli nie zostanie uwzględniony właściwości w żądaniu w jednostki przechowywanej istnieje tej właściwości, żądanie spowoduje usunięcie tej właściwości z przechowywanych jednostki. Chyba że chcesz usunąć właściwość jawnie z przechowywanych jednostki, należy uwzględnić dla każdej właściwości w żądaniu.  

Można użyć **scalania** metody **TableOperation** klasę, aby zmniejszyć ilość danych, który możesz wysłać do usługi tabel, jeśli chcesz zaktualizować jednostki. **Scalania** metody zastępuje wszystkie właściwości w obiekcie przechowywane wartości właściwości od podmiotu w żądaniu uwzględniono, ale pozostawia niezmienione żadnych właściwości przechowywanych jednostki, które nie znajdują się w żądaniu. Jest to przydatne, jeśli masz dużych jednostek i wymaga tylko zaktualizowania niewielkiej liczby właściwości w żądaniu.  

> [!NOTE]
> **Zastąp** i **scalania** metody zawiodą, jeśli jednostka nie istnieje. Alternatywnie, można użyć **InsertOrReplace** i **InsertOrMerge** metod, które Utwórz nową jednostkę, jeśli nie istnieje.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Praca z typami jednostek heterogenicznych
Usługa tabel jest *bez schematu* sklepu tabeli, która oznacza, że pojedynczej tabeli można przechowywać jednostek wiele typów, zapewniając elastyczność w projekcie. Poniższy przykład przedstawia przechowywania zarówno pracowników i działu jednostek tabeli:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Sygnatura czasowa</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Imię</th>
<th>Nazwisko</th>
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
<th>Imię</th>
<th>Nazwisko</th>
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
<th>Nazwa działu</th>
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
<th>Imię</th>
<th>Nazwisko</th>
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

Należy pamiętać, że każdy obiekt muszą mieć **PartitionKey**, **RowKey**, i **sygnatury czasowej** wartości, którzy mogą mieć dowolny zbiór właściwości. Ponadto nie ma nic do wskazują na typ jednostki, chyba że zostanie wybrana do przechowywania tych informacji innym. Dostępne są dwie opcje w celu zidentyfikowania typu jednostki:  

* Dołączenie wartości typu jednostki **RowKey** (lub **PartitionKey**). Na przykład **EMPLOYEE_000123** lub **DEPARTMENT_SALES** jako **RowKey** wartości.  
* Aby zarejestrować typ jednostki, jak pokazano w poniższej tabeli, należy użyć oddzielnych właściwości.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Sygnatura czasowa</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Dla obiektu</th>
<th>Imię</th>
<th>Nazwisko</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Pracownika</td>
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
<th>Dla obiektu</th>
<th>Imię</th>
<th>Nazwisko</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Pracownika</td>
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
<th>Dla obiektu</th>
<th>Nazwa działu</th>
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
<th>Dla obiektu</th>
<th>Imię</th>
<th>Nazwisko</th>
<th>Wiek</th>
<th>Email</th>
</tr>
<tr>
<td>Pracownika</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Pierwsza opcja dołączanie jednostki typu do **RowKey**, jest przydatne, jeśli istnieje możliwość, że dwie jednostki z różnymi typami może mieć taką samą wartość klucza. Grup jednostek z tego samego typu w partycji.  

Techniki opisane w tej sekcji są szczególnie istotne w dyskusji [relacji dziedziczenia](#inheritance-relationships) wcześniej w tym przewodniku w sekcji [modelowania relacji](#modelling-relationships).  

> [!NOTE]
> Należy rozważyć tym numeru wersji w wartości typu jednostki, aby umożliwić aplikacjom klienckim ewoluują obiektów POCO i pracy z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre z funkcji w bibliotece klienta usługi Storage, które ułatwiają pracę z wieloma typami jednostek w tej samej tabeli.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Trwa pobieranie typów jednostek heterogenicznych
Jeśli korzystasz z biblioteki klienta usługi Storage, masz trzy opcje do pracy z wielu typów jednostek.  

Jeśli znasz typ jednostki przechowywane z określonym **RowKey** i **PartitionKey** wartości, a następnie można określić typu obiektu podczas pobierania jednostki, jak pokazano w poprzednich przykładach dwa, które służą do pobierania jednostki typu **EmployeeEntity**: [wykonywania zapytania punktu, przy użyciu biblioteki klienta usługi Storage](#executing-a-point-query-using-the-storage-client-library) i [pobierania wiele jednostek za pomocą LINQ](#retrieving-multiple-entities-using-linq).  

Drugą opcją jest użycie **DynamicTableEntity** typu (zbiór właściwości), zamiast do konkretnego typu jednostki POCO (Ta opcja może także zwiększenia wydajności, ponieważ nie istnieje potrzeba do serializacji i deserializacji jednostki do typów .NET). Poniższy kod C# potencjalnie pobiera wielu jednostek o różnych typach z tabeli, ale zwraca wszystkie jednostki jako **DynamicTableEntity** wystąpień. Następnie używa **EntityType** właściwości można określić typu każdej jednostki:  

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

Należy pamiętać, że można pobrać inne właściwości, należy użyć **TryGetValue** metoda **właściwości** właściwość **DynamicTableEntity** klasy.  

Trzecia opcja jest łączenie za pomocą **DynamicTableEntity** typu i **EntityResolver** wystąpienia. Dzięki temu można rozwiązać do różnych POCO w jednym zapytaniu. W tym przykładzie **EntityResolver** używa delegata **EntityType** właściwości do rozróżniania między dwoma typami jednostek, że kwerenda zwraca. **Rozwiązać** używa metody **rozpoznawania** pełnomocnika, aby rozwiązać **DynamicTableEntity** wystąpień do **TableEntity** wystąpień.  

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

#### <a name="modifying-heterogeneous-entity-types"></a>Modyfikowanie jednostek heterogenicznych typów
Nie trzeba znany typ jednostki go usunąć i zawsze będzie wiadomo, typ jednostki po wstawieniu go. Można jednak użyć **DynamicTableEntity** typu, aby zaktualizować jednostkę, bez uprzedniego uzyskania informacji o jego typ, a także bez korzystania z klasą jednostki POCO. Poniższy przykładowy kod pobiera pojedynczej jednostki i sprawdza **EmployeeCount** istnieje właściwość przed zaktualizowaniem go.  

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

### <a name="controlling-access-with-shared-access-signatures"></a>Kontrolowanie dostępu przy użyciu sygnatury dostępu współdzielonego
Aby umożliwić aplikacjom klienckim zmodyfikować (i zapytania) jednostek tabeli bezpośrednio, bez konieczności uwierzytelnienia bezpośrednio z usługi tabel, można użyć tokenów dostępu sygnatury dostępu Współdzielonego. Zazwyczaj są trzy podstawowe zalety przy użyciu sygnatury dostępu Współdzielonego w aplikacji:  

* Nie trzeba rozpowszechniać klucz konta magazynu dla niezabezpieczonych platformy (np. urządzenie przenośne), aby zezwolić na tym urządzeniu dostępu i modyfikowania obiektów w usłudze tabel.  
* Można odciążyć niektóre pracę, aby wykonać role sieci web i proces roboczy w zarządzaniu jednostek na urządzeniach klienckich, takich jak komputery użytkowników końcowych i urządzeń przenośnych.  
* Można przypisać ograniczone, a czas ograniczony zestaw uprawnień do klienta (na przykład umożliwiając dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji o korzystaniu z usługi tabel tokeny sygnatury dostępu Współdzielonego, zobacz [przy użyciu dostępu sygnatur dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Jednak nadal należy wygenerować tokeny sygnatury dostępu Współdzielonego, które udzielić aplikacji klienckich, do obiektów w usłudze tabel: należy to zrobić w środowisku zawierającym bezpieczny dostęp do kluczy konta magazynu. Zwykle Użyj roli sieci web lub procesu roboczego do generowania tokenów SAS oraz dostarczania ich do aplikacji klienckich, które wymagają dostępu do jednostek. Ponieważ jest nadal koszty związane z generowania i dostarczania tokeny sygnatury dostępu Współdzielonego do klientów, należy rozważyć najlepszy sposób, aby zmniejszyć to obciążenie, szczególnie w sytuacjach dużego.  

Istnieje możliwość wygenerowania tokenu sygnatury dostępu Współdzielonego, która udziela dostępu do podzbioru jednostek w tabeli. Domyślnie utworzyć token sygnatury dostępu Współdzielonego dla całej tabeli, ale jest również możliwe określenie, czy token sygnatury dostępu Współdzielonego udzielić dostępu do jednego zakresu **PartitionKey** wartości lub zakres **PartitionKey** i **RowKey** wartości. Można wygenerować tokeny sygnatury dostępu Współdzielonego dla poszczególnych użytkowników systemu w taki sposób, że każdy użytkownik tokenu sygnatury dostępu Współdzielonego tylko umożliwia im dostęp do ich własnych jednostek w usłudze tabel.  

### <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległych
Pod warunkiem rozpraszania swoje żądania między wieloma partycjami można zwiększyć elastyczność przepływności i klienta przy użyciu asynchronicznej lub równoległego zapytań.
Na przykład może być wystąpień roli dwóch lub więcej procesów roboczych podczas uzyskiwania dostępu do tabel równolegle. Można mieć role poszczególnych pracowników odpowiedzialnych za poszczególne zbiory partycji lub po prostu ma wiele procesu roboczego wystąpienia roli, każdy mógł uzyskać dostępu do wszystkich partycji w tabeli.  

W wystąpieniu klienta może zwiększyć przepływność, wykonując operacje magazynu asynchronicznie. Biblioteki klienta usługi Storage ułatwia pisanie zapytań asynchroniczne i zmiany. Na przykład rozpoczynać metoda synchroniczna, która pobiera wszystkich jednostek w partycji, jak pokazano w poniższym kodzie C#:  

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

Można łatwo zmodyfikować ten kod, dzięki czemu zapytanie uruchamia asynchronicznie w następujący sposób:  

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

W tym przykładzie asynchroniczne można wyświetlić następujące zmiany z synchronicznego wersji:  

* Podpis metody zawiera teraz **async** modyfikator i zwraca **zadań** wystąpienia.  
* Zamiast wywoływać metodę **ExecuteSegmented** wywołuje metodę, aby pobrać wyniki, metoda teraz **ExecuteSegmentedAsync** — metoda i używa **await** modyfikator, aby asynchronicznie pobrać wyniki.  

Aplikacja kliencka tę metodę mogą wywoływać wielokrotnie (o różnych wartościach dla **działu** parametru), i każda kwerenda zostanie uruchomiona w oddzielnym wątku.  

Należy pamiętać, że nie ma asynchronicznych wersji **Execute** metody w **TableQuery** klasy, ponieważ **IEnumerable** interfejs nie obsługuje asynchroniczne wyliczenia.  

Można również wstawiania, aktualizacji i usuwania jednostek asynchronicznie. W poniższym przykładzie C# przedstawiono prosty, synchronicznej metodę Wstawianie lub zastępowanie jednostki pracowników:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Można łatwo zmodyfikować ten kod, dzięki czemu aktualizację uruchamia asynchronicznie w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchroniczne można wyświetlić następujące zmiany z synchronicznego wersji:  

* Podpis metody zawiera teraz **async** modyfikator i zwraca **zadań** wystąpienia.  
* Zamiast wywoływać metodę **Execute** wywołuje metodę, aby zaktualizować jednostki, metoda teraz **ExecuteAsync** — metoda i używa **await** modyfikator, aby asynchronicznie pobrać wyniki.  

Aplikacja kliencka można wywołać wiele metod asynchronicznych podobny do tego, a każde wywołanie metody zostanie uruchomiony w oddzielnym wątku.  

### <a name="credits"></a>Środki na korzystanie z
Chcielibyśmy Dziękujemy następujących członków zespołu Azure ich wkład: Dominic Betts, Jason Hogg, Jean Ghanem, Haridas WSiSW, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah i Serdar Ozler jak również Hollander Tomasz z DX firmy Microsoft. 

Chcemy też Dziękujemy następujące Microsoft MVP w swoich opinii przydatna podczas cykli przeglądu: Igor Papirov i Edward Bakker.

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

