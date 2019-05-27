---
title: Projektowanie tabel usługi Azure Cosmos DB do obsługi skalowania i wydajności
description: 'Przewodnik po projektowaniu tabel usługi Azure Storage: Projektowanie skalowalnych i wydajnych tabel w usłudze Azure Cosmos DB i Azure Storage Table'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: af155b5adb2e4b45412a8b84818852ed1b1c5e72
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966097"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Przewodnik po projektowaniu tabel usługi Azure Storage: Projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Projektowanie skalowalnych i wydajnych tabel, które należy wziąć pod uwagę wiele czynników, takich jak wydajność, skalowalność i kosztów. Jeśli wcześniej zostały tak zaprojektowane schematów dla relacyjnych baz danych, tych zagadnień będzie znane, ale istnieją pewne podobieństwa między modelu magazynu usługi Azure Table i modeli relacyjnych, są również istotne różnice w wielu. Różnice te zwykle prowadzić do różnych projektów, który może wyglądać counter-intuitive lub jest on nieprawidłowy komuś zapoznać się z relacyjnych baz danych, ale Jeśli projektujesz do magazynu klucz wartość NoSQL, takie jak usługa Azure Table ułatwiających wiedział. Wiele różnice w zakresie projektowania odzwierciedlają fakt, że usługa Table jest przeznaczona do obsługi aplikacji w skali chmury, zawierające miliardów jednostek ze (wiersze w terminologii relacyjnej bazy danych) danych lub w przypadku zestawów danych, który musi obsługiwać dużo transakcji woluminy: w związku z tym, musisz inaczej przemyśleć sposób przechowywania danych i zrozumienie sposobu działania usługi tabel. Dobrze zaprojektowana magazyn danych NoSQL, można włączyć rozwiązania do skalowania znacznie bardziej (i przy niskich kosztach) niż w przypadku rozwiązania, który korzysta z relacyjnej bazy danych. Ten przewodnik ułatwi Ci następujące tematy.  

## <a name="about-the-azure-table-service"></a>O usłudze Azure Table
W tej sekcji opisano niektóre z kluczowymi funkcjami usługi Table service, które są szczególnie istotne, aby projektowanie pod kątem wydajności i skalowalności. Jeśli jesteś nowym użytkownikiem usługi Azure Storage i Table service, najpierw zapoznaj się z [wprowadzenie do usługi Microsoft Azure Storage](../storage/common/storage-introduction.md) i [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](table-storage-how-to-use-dotnet.md) przed odczytaniem w dalszej części tego artykułu. . Chociaż ten przewodnik koncentruje się na usłudze Table service, będzie ona zawierała pewne dyskusję na temat usługi Azure, kolejek i obiektów Blob i jak można wykorzystać je wraz z usługi tabel w rozwiązaniu.  

Co to jest usługa Table service? Zgodnie z oczekiwaniami z nazwy, usłudze Table service używa formacie tabelarycznym do przechowywania danych. W terminologii każdy wiersz w tabeli reprezentuje jednostkę, a kolumny przechowywać różne właściwości tej jednostki. Każda jednostka ma parę kluczy, umożliwiającą jej jednoznaczną identyfikację i kolumnę sygnatur czasowych, używanymi przez usługę tabeli do śledzenia w jednostki został ostatnio zaktualizowany (jest automatycznie dodawane pole znacznika czasu i nie można ręcznie zastąpić sygnaturę czasową z dowolną wartością). Usługa Table service używa ostatniej modyfikacji sygnatura czasowa (LMT) do zarządzania optymistycznej współbieżności.  

> [!NOTE]
> Operacje interfejsu API REST usługi Table również zwracać **ETag** wartość, która pochodzi od ostatniej modyfikacji sygnatura czasowa (LMT). W tym dokumencie można zauważyć terminy, element ETag i LMT zamiennie, ponieważ odnoszą się do tych samych danych.  
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


Do tej pory ten projekt jest podobny do tabeli w relacyjnej bazie danych przy użyciu podstawowych różnic obowiązkowe kolumn i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto poszczególne właściwości zdefiniowanych przez użytkownika, takie jak **FirstName** lub **wiek** ma typ danych, takie jak liczba całkowita lub ciąg, po prostu, takich jak kolumny w relacyjnej bazie danych. Mimo że w przeciwieństwie do relacyjnej bazy danych bez schematu charakter usługi Table service oznacza właściwości nie wymagają tych samych danych, wpisz dla każdej jednostki. Aby złożone typy danych są przechowywane w pojedynczej właściwości, należy użyć serializacji formatu JSON lub XML. Aby uzyskać więcej informacji na temat usługi, takie jak obsługiwane typy danych w tabelach, zakresów dat obsługiwanych, zasady nazewnictwa i ograniczeń związanych z rozmiarem, zobacz [opis modelu danych usługi Table Service](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Jak będzie widać, wybór **PartitionKey** i **RowKey** ma podstawowe znaczenie dla projektu dobre tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, **PartitionKey** i **RowKey** wartości są indeksowane, aby utworzyć indeks klastrowany, która umożliwia szybkie wyszukania; w usłudze Table service tworzy jednak żadnego indeksy pomocnicze, dzięki czemu są one tylko dwie właściwości indeksowane (wzorców opisanym w dalszej części widoczna jest jak obejść to ograniczenie widoczna).  

Tabeli składa się z co najmniej jedną partycję, bo pozwoli zauważyć wiele decyzji projektowych, wprowadzone. zostanie ona wokół wybierając odpowiedni **PartitionKey** i **RowKey** zoptymalizować swoje rozwiązanie. To rozwiązanie może zawierać tylko pojedynczą tabelę, która zawiera wszystkie jednostki podzielony na partycje, ale zazwyczaj rozwiązanie będzie zawierać wiele tabel. Tabele ułatwiają logicznie organizowanie jednostek, ułatwiają zarządzanie dostępem do danych za pomocą listy kontroli dostępu i może usunąć całą tabelę przy użyciu operacji pojedynczy magazyn.  

### <a name="table-partitions"></a>Partycje tabel
Nazwa konta, nazwę tabeli i **PartitionKey** razem identyfikują partycji w ramach usługi magazynu, w której przechowuje jednostki w usłudze table service. A także bycie częścią schematu adresowania dla jednostek, partycje zdefiniować zakres transakcji (zobacz [transakcji grup jednostek](#entity-group-transactions) poniżej), i stanowią podstawę jak jest skalowana w usłudze table service. Aby uzyskać więcej informacji o partycji, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](../storage/common/storage-scalability-targets.md).  

W usłudze Table service oddzielnego węzła usługi jeden lub więcej zakończą się partycji i skali usługi przez dynamiczne równoważenie obciążenia partycji na węzłach. Jeśli węzeł ma pod obciążeniem, usłudze table service można *podziału* zakresu partycji obsługiwanych przez ten węzeł na różnych węzłach; gdy zmniejszenia ruchu, usługa może *scalania* zakresu partycji z węzłów cichy na jednym węźle.  

Aby uzyskać więcej informacji na temat szczegółami wewnętrznymi usługi Table service, a w szczególności w jaki sposób usługa zarządza partycjami, zobacz dokument [usługi Microsoft Azure Storage: Usługi magazynu w chmurze o wysokiej dostępności przy użyciu silnej spójności](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W usłudze Table service transakcji grup jednostek (EGTs) są tylko wbudowanego mechanizmu do wykonywania niepodzielnych aktualizacje w wielu jednostkach. EGTs są również nazywane *partii transakcji* w dokumentacji. EGTs może działać tylko na jednostek przechowywanych w tej samej partycji (udział ten sam klucz partycji w danej tabeli), więc potrzebujesz atomic transakcyjnych zachowanie w wielu jednostkach, należy się upewnić, że te jednostki są w tej samej partycji. Jest to często Przyczyna zachowaniem typów jednostek w tej samej tabeli (i partycji), a nie przy użyciu wielu tabel dla jednostek różnych typów. Pojedynczy EGT może operować na co najwyżej 100 jednostek.  Jeśli prześlesz wielu jednoczesnych EGTs do przetwarzania, jest ważne, aby upewnić się, że te EGTs nie działają na jednostek, które są wspólne w EGTs, ponieważ w przeciwnym razie przetwarzania może być opóźniony w.

EGTs również wprowadzać potencjalnych kompromis umożliwiające ocenę w projekcie: przy użyciu więcej partycji spowoduje zwiększenie skalowalności aplikacji, ponieważ platforma Azure oferuje dodatkowe możliwości dla równoważenia obciążenia żądań między węzłami, ale może ograniczać możliwości aplikację można wykonywać transakcje niepodzielne i obsługa silnej spójności danych. Ponadto, są cele skalowalności określonego na poziomie partycję, która może ograniczać przepływności można oczekiwać, że dla pojedynczego węzła transakcji: Aby uzyskać więcej informacji na temat cele skalowalności dla konta usługi Azure storage i table service, zobacz [Cele wydajności i skalowalności usługi azure Storage](../storage/common/storage-scalability-targets.md). Kolejnych sekcjach tego przewodnika omówiono różne projektowania strategii, które ułatwiają zarządzanie wad i zalet, taką jak ta i omówić najlepszy sposób, aby wybrać klucz partycji, na podstawie określonych wymagań aplikacji klienckiej.  

### <a name="capacity-considerations"></a>Zagadnienia dotyczące wydajności
Poniższa tabela zawiera niektóre z kluczowych wartości pod uwagę podczas projektowania rozwiązania usługi tabeli:  

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

### <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest względnie niedrogie ulepszenie, ale powinien zawierać szacowane koszty użycia pojemności i liczba transakcji jako część oceny dowolnego rozwiązania, które korzysta z usługi tabel. Jednak w wielu scenariuszach nieznormalizowany lub zduplikowane dane są przechowywane w celu poprawy wydajności i skalowalności rozwiązania jest prawidłowy podejście do wykonania. Aby uzyskać więcej informacji o cenach, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel
Te listy podsumowanie niektórych kluczowych wytyczne, które należy mieć na uwadze podczas projektowania tabel, a ten przewodnik pozwala sprostać je wszystkie w bardziej szczegółowo w dalszej części w. Te wytyczne są różne od wytyczne, które należy zwykle wykonać dla relacyjnych baz danych.  

Projektowania rozwiązania usługi tabeli jako *odczytu* efektywne:

* ***Projektowanie pod kątem wykonywania zapytania w odczycie aplikacji.*** Podczas projektowania tabel wziąć pod uwagę zapytań (szczególnie opóźnienia poufnych z nich), które będą wykonywane przed zaplanowaniem jak spowoduje zaktualizowanie jednostki. Skutkuje to zazwyczaj wydajne i wydajne rozwiązanie.  
* ***Należy określić zarówno właściwości PartitionKey i RowKey w zapytaniach.*** *Polecenie zapytania* np. są to najbardziej wydajne zapytania usługi tabeli.  
* ***Rozważ przechowywanie duplikatów kopii jednostki.*** Magazyn tabel jest magazynem tania dlatego należy rozważyć przechowywanie tego samego obiektu wiele razy (przy użyciu różnych kluczy) umożliwiające wydajniejszych zapytań.  
* ***Należy wziąć pod uwagę denormalizing danych.*** Magazyn tabel jest magazynem tania zatem należy wziąć pod uwagę denormalizing danych. Na przykład przechowywać podsumowania jednostki tak, aby zapytania dla danych zagregowanych wystarczy dostęp do pojedynczej jednostki.  
* ***Użyj wartości klucza złożonego.*** To tylko klucze masz **PartitionKey** i **RowKey**. Aby włączyć alternatywnego kluczem dostępu do ścieżki do jednostek, na przykład użyć wartości klucza złożonego.  
* ***Użyj projekcji zapytań.*** Można zmniejszyć ilość danych, który transferu za pośrednictwem sieci przy użyciu kwerend wybierających tylko pola, które są potrzebne.  

Projektowania rozwiązania usługi tabeli jako *zapisu* efektywne:  

* ***Nie należy tworzyć partycje gorąca.*** Wybierz klucze, które umożliwiają żądań rozkłada się na wielu partycjach w dowolnym momencie.  
* ***Należy unikać skoków ruchu.*** Smooth ruch za pośrednictwem uzasadnionym czasie i uniknąć skoków ruchu.
* ***Nie musi utworzyć oddzielną tabelę dla każdego typu jednostki.*** Transakcje niepodzielne jest stosowanie różnych typów jednostek, tych typów jednostek można przechowywać w jednej partycji w tej samej tabeli.
* ***Należy wziąć pod uwagę maksymalną przepływność, którą należy osiągnąć.*** Należy wiedzieć o wartości docelowe skalowalności usługi tabel i upewnij się, że projekt nie spowoduje przekroczenie je.  

Ponieważ w tym przewodniku zobaczysz przykładów, które praktycznego wdrożenia wszystkich tych zasad.  

## <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Rozwiązania usługi tabeli mogą być odczytywane znaczne wykorzystanie mocy, intensywnie zapisu lub zmieszanie dwóch. Ta sekcja koncentruje się na warto pamiętać podczas projektowania usługi Table service, aby efektywnie obsługiwać operacje odczytu. Zazwyczaj projekt, że obsługuje operacje odczytu efektywnie również jest wydajne dla operacji zapisu. Jednak istnieją pewne dodatkowe zagadnienia dotyczące pamiętać podczas projektowania do obsługi operacji zapisu, omówiona w następnej sekcji [projektowanie pod kątem modyfikacji danych](#design-for-data-modification).

Dobry punkt wyjścia dla projektowania rozwiązania usługi tabeli umożliwienie wydajnego odczytywania danych jest "jakie zapytania mojej aplikacji należy wykonać, aby pobrać dane, których potrzebuje z usługi tabel?"  

> [!NOTE]
> Z usługą Table jest ważne, aby pobrać projekt poprawne na początku ponieważ jest trudne i kosztowna później zmienić. Na przykład w relacyjnej bazie danych jest często można rozwiązać problemy z wydajnością — wystarczy dodawania indeksów do istniejącej bazy danych: nie jest opcją w usłudze tabel.  
> 
> 

Ta sekcja koncentruje się na kluczowych zagadnień, które należy rozwiązać podczas projektowania tabel dla zapytań. Tematy omówione w tej sekcji obejmują:

* [Jak wybór właściwości PartitionKey i RowKey ma wpływ na wydajność zapytań](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Wybieranie odpowiedniego PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optymalizowanie zapytań dotyczących Table service](#optimizing-queries-for-the-table-service)
* [Sortowanie danych w usłudze Table service](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór właściwości PartitionKey i RowKey ma wpływ na wydajność zapytań
W poniższych przykładach założono, usłudze table service zapisuje jednostek pracownika o następującej strukturze (Pomiń Większość przykładów **sygnatura czasowa** właściwości dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **Imię** |Ciąg |
| **Nazwisko** |Ciąg |
| **Wiek** |Liczba całkowita |
| **EmailAddress** |String |

Wcześniej Omówienie usługi Azure Table sekcji opisano niektóre z kluczowymi funkcjami usługi Azure Table service, które mają bezpośredni wpływ na projektowanie pod kątem zapytań. Wynikiem tych ogólne wytyczne dotyczące projektowania zapytań usługi tabeli. Składnia filtru użyte w poniższych przykładach jest z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [wykonywanie zapytań dotyczących jednostek](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* A ***kwerendy punktu*** jest najbardziej efektywny sposób wyszukiwania, aby użyć i jest zalecana służący do wyszukiwania mocno obciążające lub wyszukiwania, wymagających najmniejszego opóźnienia. W przypadku takiego zapytania umożliwia wydajne Znajdź pojedynczą jednostkę, określając zarówno indeksy **PartitionKey** i **RowKey** wartości. Na przykład: $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq '2')  
* Po drugie, najlepiej jest ***kwerendy zakresu*** , który używa **PartitionKey** i filtry na szeroką gamę **RowKey** wartości do zwrócenia więcej niż jedną jednostkę. **PartitionKey** wartość określa określonej partycji i **RowKey** wartości identyfikują podzestawu jednostek w partycji. Na przykład: $filter = PartitionKey eq "Sprzedaż i RowKey ge" i RowKey lt t "  
* Trzeci najlepiej ***skanowania partycji*** , który używa **PartitionKey** i filtry na inną właściwość klucza i który może zwrócić więcej niż jedną jednostkę. **PartitionKey** wartość określa określonej partycji, a właściwość wartości wybranych dla podzestawu jednostek w partycji. Na przykład: $filter = PartitionKey eq "Sprzedaż" i LastName eq 'Smith'  
* A ***skanowanie tabeli*** nie obejmuje **PartitionKey** jest nieefektywne, ponieważ wszystkie partycje, które tworzą tabelę z kolei do żadnych zgodnych jednostek wyszukiwania. Zostanie przeprowadzone skanowanie tabeli, niezależnie od tego, czy filtr używa **RowKey**. Na przykład: $filter = LastName eq "Kowalski"  
* Azure zapytań magazynu tabel, które zwracają wiele jednostek zwraca je posortowane w **PartitionKey** i **RowKey** zamówienia. Aby uniknąć konieczności uciekania się jednostki w obiekcie klienta, wybierz opcję **RowKey** definiujący najbardziej typowe kolejność sortowania. Wyniki zapytania zwracana przez interfejs API tabeli platformy Azure w usłudze Azure Cosmos DB nie są sortowane według klucza partycji i klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejsu Table API usługi Azure Cosmos DB i Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Za pomocą "**lub**" Aby określić filtr oparty na **RowKey** wartości wyniki podczas skanowania partycji i nie jest traktowana jako zapytanie zakresu. W związku z tym, należy unikać zapytań, które używają filtrów, takich jak: $filter = PartitionKey eq "Sales" oraz (RowKey eq "121" lub RowKey eq "322")  

Przykłady kodu po stronie klienta, które można wykonać wydajne zapytania za pomocą biblioteki klienta magazynu zobacz:  

* [Wykonywanie kwerendy punktu, przy użyciu biblioteki klienta magazynu](#executing-a-point-query-using-the-storage-client-library)
* [Trwa pobieranie wielu jednostek za pomocą LINQ](#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli zobacz:  

* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien równoważyć potrzeby włączyć EGTs (w celu zapewnienia spójności) wymaganiem, aby rozprowadzić jednostek na wiele partycji (w celu zapewnienia skalowalne rozwiązanie).  

Jeden extreme wszystkich jednostek można przechowywać w jednej partycji, ale to może ograniczać skalowalność rozwiązania i uniemożliwi usłudze table service mogli wysyłać żądania równoważenia obciążenia. Y. można przechowywać jednej jednostki w jednej partycji, byłoby wysoce skalowalna i umożliwiająca usłudze table service, aby wysyłać żądania równoważenia obciążenia, ale która może uniemożliwić przy użyciu transakcji grup jednostek.  

Idealna **PartitionKey** jeden, który umożliwia wydajne zapytania i ma wystarczającą partycji, aby upewnić się, Twoje rozwiązanie jest skalowalna. Zazwyczaj znajdziesz, że obiekty odpowiednie właściwości, która dystrybuuje jednostek na partycji wystarczającą.

> [!NOTE]
> Na przykład w systemie, która przechowuje informacje dotyczące użytkowników lub pracowników, nazwa użytkownika może być dobrym PartitionKey. Może mieć kilka jednostek, korzystających z danego identyfikatora użytkownika jako klucza partycji. Każda jednostka, która przechowuje dane o użytkowniku są pogrupowane w jednej partycji, a więc te jednostki są dostępne za pośrednictwem transakcji grup jednostek, przy zachowaniu o wysokim stopniu skalowalności.
> 
> 

Istnieją pewne dodatkowe zagadnienia dotyczące w wybranym **PartitionKey** wydania jak będzie Wstawianie, aktualizowanie i usuwanie jednostek: zobacz sekcję [projektowanie pod kątem modyfikacji danych](#design-for-data-modification) poniżej.  

### <a name="optimizing-queries-for-the-table-service"></a>Optymalizowanie zapytań dotyczących Table service
Usługa Table service automatycznie indeksuje jednostek za pomocą **PartitionKey** i **RowKey** wartości jeden indeks klastrowany, dlatego Przyczyna, wskazujące zapytania są najbardziej wydajne do użycia. Istnieją jednak żadne indeksy innych niż na indeks klastrowany w **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby włączyć wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład Lokalizowanie jednostek pracowników opartych na adres e-mail, identyfikator pracownika lub nazwisko. Następujących wzorów w określonej sekcji [wzorce projektowe oparte na tabeli](#table-design-patterns) rozwiązać tego rodzaju wymagań oraz opisano sposoby obejścia faktów, usłudze Table service nie zapewnia indeksów pomocniczych:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](#intra-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec pomocniczy indeks partycji między](#inter-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości w oddzielnych partycji lub w oddzielnych tabelach umożliwiające szybkie i wydajne wyszukiwania i sortowania zamówień za pomocą różnych **RowKey** wartości.  
* [Wzorzec jednostek indeksu](#index-entities-pattern) — Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  

### <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w usłudze Table service

Wyniki zapytania, zwracane przez usługę tabeli są sortowane w kolejności rosnącej na podstawie **PartitionKey** a następnie według **RowKey**.

> [!NOTE]
> Wyniki zapytania zwracana przez interfejs API tabeli platformy Azure w usłudze Azure DB nie są sortowane według klucza partycji i klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejsu Table API usługi Azure Cosmos DB i Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Kluczy w tabeli usługi Azure Storage są wartości typu ciąg i aby zapewnić poprawne sortowanie wartości liczbowych, należy przekonwertować je na stałej długości i uzupełniania zer. Na przykład, jeśli wartość identyfikatora pracowników jest używany jako **RowKey** jest liczbą całkowitą, należy przekonwertować identyfikator pracownika **123** do **00000123**. 

Wiele aplikacji ma wymagania dotyczące korzystania z danych w różnych zleceniach sortowane: na przykład sortowanie pracowników według nazwy lub dołączenie do daty. Następujących wzorów w określonej sekcji [wzorce projektowe oparte na tabeli](#table-design-patterns) rozwiązać jak alternatywny porządek sortowania dla jednostki:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](#intra-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek przy użyciu różnych wartości RowKey (w tej samej partycji), aby umożliwić szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych wartości RowKey.  
* [Wzorzec pomocniczy indeks partycji między](#inter-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek przy użyciu różnych wartości RowKey w oddzielnych partycjach w oddzielnych tabelach, aby umożliwić szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych wartości RowKey .
* [Wzorzec ogona dziennika](#log-tail-pattern) — pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  

## <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ta sekcja koncentruje się na informacjach dotyczących projektowania optymalizacji operacji wstawiania, aktualizacji i usuwa. W niektórych przypadkach konieczne będzie oceniać kompromisu między projekty, które optymalizują do wykonywania zapytań względem projektów, które Optymalizuj pod kątem modyfikacji danych, podobnie jak w projektach dla relacyjnych baz danych (mimo że techniki projektowania wad i zalet zarządzania są inne w przypadku relacyjnej bazy danych). Sekcja [wzorce projektowe oparte na tabeli](#table-design-patterns) opisuje niektóre wzorce szczegółowego projektowania usługi Table i opisano niektóre z tych wad. W praktyce znajdziesz, że wiele projektów, zoptymalizowane pod kątem zapytań jednostki również działać dobrze w przypadku modyfikowania jednostek.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optymalizacja wydajności INSERT, aktualizowania i usuwania działań
Aby zaktualizować lub usunąć jednostkę, musi mieć możliwość identyfikację za pomocą **PartitionKey** i **RowKey** wartości. W związku z tym wybór **PartitionKey** i **RowKey** modyfikowanie jednostek powinno postępuj zgodnie z podobne kryteria z wybranymi do obsługi zapytań o punkt, ponieważ chcesz zidentyfikować jednostki jako najbardziej wydajny sposób. Czy chcesz użyć nieefektywne skanowanie za pomocą partycji lub tabeli, aby zlokalizować jednostki w celu odnajdywania **PartitionKey** i **RowKey** wartości, musisz zaktualizować lub usunąć go.  

Następujących wzorów w określonej sekcji [wzorce projektowe oparte na tabeli](#table-design-patterns) adresów, optymalizacji wydajności lub usługi insert, update, a operacje usuwania:  

* [Duża Usuń wzorzec](#high-volume-delete-pattern) -Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki do usunięcia jednoczesnych w ich własnych osobnej tabeli; jednostki możesz usunąć przez usunięcie tabeli.  
* [Wzorzec serii danych](#data-series-pattern) -Store serii kompletne dane w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzeniu.  
* [Wzorzec szerokiego jednostek](#wide-entities-pattern) — korzystanie z wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwości.  
* [Wzorzec dużych jednostek](#large-entities-pattern) — usługa blob storage do przechowywania wartości właściwości dużych.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Zapewnienie spójności w jednostkach składowanych
Kluczowym czynnikiem, który ma wpływ na wybór klucze na potrzeby optymalizacji modyfikacji danych jest sposób zapewnienia spójności za pomocą transakcji niepodzielnej. EGT można używać tylko do wykonywania operacji jednostek przechowywanych w tej samej partycji.  

Następujących wzorów w określonej sekcji [wzorce projektowe oparte na tabeli](#table-design-patterns) adresów, zarządzanie spójności:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](#intra-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec pomocniczy indeks partycji między](#inter-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek za pomocą różnych wartości RowKey w oddzielnych partycjach lub w oddzielnych tabelach umożliwiają szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych **RowKey** wartości.  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) -Włącz ostatecznie spójne zachowanie całej granice partycji lub granice system magazynu za pomocą kolejek systemu Azure.
* [Wzorzec jednostek indeksu](#index-entities-pattern) — Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  
* [Wzorzec denormalizacja](#denormalization-pattern) — łączenie pokrewnych danych ze sobą w pojedynczej jednostki, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania pojedynczy punkt.  
* [Wzorzec serii danych](#data-series-pattern) -Store serii kompletne dane w pojedynczej jednostki, aby zminimalizować liczbę żądań wprowadzeniu.  

Aby uzyskać informacje o transakcji grup jednostek, zobacz sekcję [transakcji grup jednostek](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zapewnienie projekt pod kątem efektywnego modyfikacje ułatwia wydajne zapytania
Projektowanie pod kątem wydajne tworzenie zapytań powoduje efektywne modyfikacje, ale w wielu przypadkach zawsze powinni sprawdzić, czy to w przypadku określonego scenariusza. Niektóre wzorce w sekcji [wzorce projektowe oparte na tabeli](#table-design-patterns) jawnie oceny kompromis między wykonywania zapytań i modyfikowania obiektów, a użytkownik powinien zawsze wziąć pod uwagę liczbę każdego typu operacji.  

Następujących wzorów w określonej sekcji [wzorce projektowe oparte na tabeli](#table-design-patterns) kompromis między projektowanie pod kątem wydajne zapytania i projektowanie pod kątem modyfikacji danych wydajne rozwiązania:  

* [Wzorca klucza złożonego](#compound-key-pattern) — Użyj złożone **RowKey** wartości, aby włączyć klienta w celu wyszukania powiązanych danych, za pomocą zapytania pojedynczy punkt.  
* [Wzorzec ogona dziennika](#log-tail-pattern) — pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  

## <a name="encrypting-table-data"></a>Szyfrowanie danych w tabeli
Biblioteka klienta .NET Azure Storage obsługuje szyfrowanie właściwości jednostki ciągu wstawiania i zamienianie operacji. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarnych i są konwertowane do ciągów po odszyfrowywania.    

W przypadku tabel, oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, albo określając atrybutu [EncryptProperty] (dla jednostki POCO, które wynikają z TableEntity) lub szyfrowania mechanizmu rozwiązywania konfliktów w opcji żądania. Mechanizm rozpoznawania szyfrowania jest delegat, który przyjmuje klucza partycji, klucz wiersza i nazwę właściwości i zwraca wartość Boolean wskazującą, czy mają być szyfrowane tej właściwości. Podczas szyfrowania biblioteki klienckiej użyje tych informacji do określania, czy właściwości mają być szyfrowane podczas zapisu w sieci. Delegat udostępnia także możliwości logiki wokół jak zaszyfrowane właściwości. (Na przykład, jeśli X, następnie szyfrować właściwość A; w przeciwnym razie szyfrowanie właściwości, A i B.) Nie jest konieczne może przekazać tę informację podczas odczytywania lub podczas badania jednostki.

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie albo wymaga wywołań usługi dodatkowe odczytać wstępnie istniejącej jednostki usługi lub przy użyciu nowego klucza dla właściwości, które nie są odpowiednie dla ze względu na wydajność.     

Aby uzyskać informacje o szyfrowaniu danych w tabeli, zobacz [szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modelowanie relacji
Tworzenie modeli domeny jest krokiem w projekcie złożonych systemów. Zazwyczaj używasz procesie modelowania do identyfikowania jednostek i relacji między nimi jako sposób na zrozumienie domeny biznesowej i natychmiastowego informowania projektu systemu. Ta sekcja koncentruje się na sposób niektóre popularne typy relacji w modeli domeny do projektów usługi tabeli translacji. Proces mapowanie logiczny model danych do fizycznego opartych na NoSQL — model danych różni się od używanej podczas projektowania relacyjnej bazy danych. Projektowania relacyjnych baz danych zwykle zakłada proces normalizacji danych zoptymalizowana pod kątem minimalizując nadmiarowość — i deklaratywne możliwości zapytań, zapewniający abstrakcję jak implementacja jak baza danych działa.  

### <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacje jeden do wielu między obiektami domeny biznesowej często występują: na przykład jednego działu ma wielu pracowników. Istnieje kilka sposobów, aby zaimplementować relacji jeden do wielu w usłudze Table service każdego z zalet i wad, które mogą być istotne dla konkretnego scenariusza.  

Rozważmy przykład dużych przedsiębiorstw wielonarodowym dziesiątki tysięcy działów i jednostki pracowników, gdzie każdy dział ma wielu pracowników i każdego pracownika, jako skojarzone z określonym dziale. Jednym z podejść jest do przechowywania działu oddzielne i pracowników jednostki, takie jak te:  

![Dział i pracownik jednostki][1]

Ten przykład przedstawia niejawnych relacji jeden do wielu między typami, na podstawie **PartitionKey** wartości. Każdy dział może mieć wielu pracowników.  

Ten przykład pokazuje także jednostki działu i jego jednostek powiązanych pracowników w tej samej partycji. Można korzystać z różnych partycji, tabel lub kont magazynu nawet dla typów innej jednostki.  

Alternatywnym podejściem jest zdenormalizowanie danych i przechowywać tylko jednostki pracowników z działu nieznormalizowany danych, jak pokazano w poniższym przykładzie. W tym scenariuszu to nieznormalizowanym podejściu nie może być najlepiej, jeśli masz wymaganie, aby można było zmienić szczegóły kierownik działu, ponieważ w tym celu należy zaktualizować każdy pracownik działu.  

![Jednostka pracownika][2]

Aby uzyskać więcej informacji, zobacz [wzorzec Denormalizacja](#denormalization-pattern) później w tym przewodniku.  

Poniższa tabela zawiera podsumowanie zalet i wad każdej z metod opisanych powyżej do przechowywania pracowników i obiektów działu, które mają relacje jeden do wielu. Należy również rozważyć, jak często chcą wykonywać różne operacje: może być akceptowalne, aby projekt, który zawiera kosztowna operacja, jeśli ta operacja odbywa się tylko rzadko.  

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
<li>Może być konieczne pobrać jednostki działu, w przypadku niektórych działań klienta i pracownika.</li>
<li>Magazyn — liczba operacji pojawiają się w tej samej partycji. Na woluminach dużo transakcji może to spowodować punkt największej aktywności.</li>
<li>Nie można przenieść pracownika nowy dział przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy osobne jednostki, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Należy zaktualizować jednostki działu lub pracowników przy użyciu jednej operacji.</li>
<li>Na woluminach dużo transakcji może to pomóc rozłożenie obciążenia na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobrać jednostki działu, w przypadku niektórych działań klienta i pracownika.</li>
<li>Nie można użyć EGTs, aby zachować spójność po użytkownik aktualizacji/insert/delete pracownika i zaktualizuj dział. Na przykład aktualizowanie liczba pracowników w jednostce działu.</li>
<li>Nie można przenieść pracownika nowy dział przy użyciu EGT.</li>
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
<li>Może to być drogie w utrzymaniu spójności, jeśli konieczne zaktualizowanie informacji działu (wymagałoby to zaktualizowania wszystkich pracowników działu).</li>
</ul>
</td>
</tr>
</table>

Jak wybrać między te opcje, a które z zalet i wad są najbardziej istotne, zależy od Twoich scenariuszy określonej aplikacji. Na przykład jak często należy możesz modyfikować jednostek działu; czy wszystkie zapytania pracowników muszą dodatkowe informacje dla działu; jak blisko czy limitów skalowalności na partycji lub konta magazynu?  

### <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modeli domeny może zawierać jeden do jednego relacje między jednostkami. Musisz zaimplementować relacja jeden do jednego w usłudze Table service, należy także wybrać jak połączyć dwie jednostki powiązane, gdy trzeba pobrać ich obu. Ten link może być niejawne, zależnie od Konwencji w wartościach klucza lub jawne, przechowując link w formie **PartitionKey** i **RowKey** wartości w każdej jednostki do jego powiązanej jednostki. Aby uzyskać omówienie tego, czy należy przechowywać powiązanych jednostek w tej samej partycji, zobacz sekcję [jeden do wielu relacji](#one-to-many-relationships).  

Dostępne są również istotne informacje dotyczące implementacji, które mogą prowadzić do zaimplementowania relacji jeden do jednego w usłudze Table service:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [dużych jednostek wzorzec](#large-entities-pattern)).  
* Implementowanie kontroli dostępu (Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu przy użyciu sygnatury dostępu współdzielonego](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Dołącz do klienta
Mimo że istnieją sposoby relacje modelu w usłudze Table service, należy nie zapominać, że dwie główne przyczyny za pomocą usługi Table jest skalowalność i wydajność. Jeśli okaże się, że są modelowania wiele relacji, które negatywnie wpłynąć na wydajność i skalowalność rozwiązania, należy poprosić samodzielnie Jeśli jest niezbędne do utworzenia relacji danych w projekcie tabeli. Można Uproszczenie projektowania i poprawy skalowalności i wydajności rozwiązania, jeśli zezwolisz aplikacji klienckiej wykonać wszelkie niezbędne sprzężenia.  

Na przykład jeśli masz małe tabele, które zawierają dane, która nie zmienia się często, następnie można pobrać te dane jeden raz i Buforuj go na komputerze klienckim. To uniknąć wielokrotnego natężenie ruchu do pobrania tych samych danych. W przykładach, które mają przyjrzeliśmy się w tym przewodniku zbiór działów w organizacji małych prawdopodobnie jest mały i zmienić rzadko co dobrym kandydatem do danych, które aplikacja kliencka może pobrać raz i pamięci podręcznej jako dane wyszukiwania.  

### <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli Twoja aplikacja kliencka korzysta z zestawu klas, które stanowią część relacji dziedziczenia do reprezentowania jednostek biznesowych, można łatwo utrwalić tych jednostek w usłudze Table service. Na przykład może być przedstawiony poniżej zestaw klas zdefiniowanych w aplikacji klienckiej gdzie **osoby** jest klasą abstrakcyjną.

![Diagram ER relacji dziedziczenia][3]

Jednak można utrwalić wystąpieniami dwóch klas konkretnych w usłudze Table service, za pomocą pojedynczej tabeli osoby za pomocą jednostek w tym wyglądał następująco:  

![Diagram jednostki Klient i jednostki pracownika][4]

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostki w tej samej tabeli, w kodzie klienta, zobacz sekcję [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types) później w tym przewodniku. Zapewnia to przykłady sposobu rozpoznaje typ jednostki w kodzie klienta.  

## <a name="table-design-patterns"></a>Wzorce projektowe oparte na tabelę
W poprzednich sekcjach wiesz, że niektóre szczegółowe dyskusje na temat sposobu optymalizacji układ tabeli dla obu podczas pobierania danych jednostki przy użyciu zapytań i wstawiania, aktualizowania i usuwania danych dotyczących jednostki. W tej sekcji opisano niektóre wzorce, które są przeznaczone do użycia z rozwiązaniami usługi tabeli. Ponadto zobaczysz, jak praktycznie rozwiązać niektóre problemy i charakterystyczne kompromisowe zgłoszone wcześniej w tym przewodniku. Poniższy diagram przedstawia relacje między różnych wzorców:  

![Obraz przedstawiający wzorce projektowe oparte na tabelę][5]

Mapa wzorzec powyżej wyróżnia niektórych relacji między (niebieski) wzorce i niezalecane wzorce (kolor pomarańczowy), które są opisane w tym przewodniku. Oczywiście istnieje wiele wzorców, które są warte biorąc pod uwagę. Na przykład jednego z kluczowych scenariuszy dla usługi Table Service jest użycie [użycia wzorca zmaterializowanego widoku](https://msdn.microsoft.com/library/azure/dn589782.aspx) z [podział odpowiedzialności polecenia w zapytania (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) wzorca.  

### <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeks pomocniczy wewnątrz partycji
Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji) Włącz szybkie i efektywne wyszukiwań naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości. Aktualizacje między kopie mogą być przechowywane na spójne przy użyciu EGTs.  

#### <a name="context-and-problem"></a>Kontekst i problem
Usługa Table service automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja kliencka można pobrać jednostki wydajnie za pomocą tych wartości. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka można użyć kwerendy punktu można pobrać jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator ( **PartitionKey** i **RowKey**  wartości). Klienta można także pobrać jednostki posortowanych według identyfikatorów pracowników w ramach każdego działu.

![Jednostka pracownika][6]

Jeśli chcesz także być w stanie znaleźć jednostki pracowników na podstawie wartości innej właściwości, takie jak adres e-mail musi być znaleźć dopasowania mniej wydajne rozwiązanie skanowania partycji. Jest to spowodowane Usługa tabeli nie zapewnia indeksów pomocniczych. Ponadto, nie ma opcji żądania listy pracowników posortowane w kolejności innej niż **RowKey** zamówienia.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść braku indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdą kopią przy użyciu innego **RowKey** wartości. Jeśli przechowujesz jednostki ze strukturami, pokazano poniżej, pozwala na efektywne pobieranie jednostek pracowników na podstawie identyfikatora wiadomości e-mail adres lub pracowników. Prefiks wartości **RowKey**, "empid_" i "email_" pozwalają na wykonywanie zapytań dla jednego pracownika lub szeroką gamę pracowników przy użyciu zakresu adresów e-mail lub identyfikatory pracownika.  

![Pracownik jednostki ze zróżnicowanymi wartościami RowKey][7]

Następujące kryteria filtrowania dwóch (jeden wyszukiwanie według identyfikatora pracowników i jeden wyszukiwanie według adresu e-mail), zarówno Określ zapytań o punkt:  

* $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq 'email_jonesj@contoso.com")  

Po wykonaniu zapytania dotyczącego zakresu jednostek pracowników, można określić zakres posortowane w kolejności identyfikator pracownika lub posortowane w kolejności adres poczty e-mail przy użyciu zapytań dotyczących jednostek, używając odpowiedniego prefiksu w zakresie **RowKey**.  

* Aby znaleźć wszyscy pracownicy działu sprzedaży, identyfikator pracownika używany zakres 000100 do 000199: $filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "empid_000100") i (RowKey le "empid_000199")  
* Aby znaleźć wszyscy pracownicy działu sprzedaży przy użyciu adresu e-mail, rozpoczynając od litery "" Użyj: $filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "email_a") i (RowKey lt "email_b")  
  
  Składnia filtru używanych w powyższych przykładach jest z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [wykonywanie zapytań dotyczących jednostek](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tania do użycia, więc obciążenie koszt przechowywania zduplikowanych danych, nie powinien być głównym problemem. Jednak należy zawsze należy przeprowadzić ocenę koszt projektu, w zależności od wymagań przewidywanych i dodawać tylko zduplikowane jednostki do obsługi zapytań, których będą wykonywane przez aplikację kliencką.  
* Ponieważ jednostki indeks pomocniczy są przechowywane w tej samej partycji co oryginalne jednostek, należy upewnić się, przekracza cele skalowalności dla poszczególnych partycji.  
* Możesz zachować swoje zduplikowanych wpisów zgodne ze sobą przy użyciu EGTs niepodzielne zaktualizować dwie kopie jednostki. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz sekcję [przy użyciu transakcji grup jednostek](#entity-group-transactions).  
* Wartość **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Wypełnienie wartości liczbowe w **RowKey** (na przykład identyfikator pracownika 000223), pozwala rozwiązać, sortowanie i filtrowanie na podstawie górną i dolną.  
* Nie zawsze należy zduplikowane właściwości jednostki. Na przykład, jeśli adresem zapytania wyszukujące jednostek opartego na poczcie e-mail **RowKey** nigdy nie potrzebować wiek pracownika, te jednostki może mieć następującą strukturę:

![Jednostka pracownika][8]

* Zazwyczaj lepiej do przechowywania zduplikowanych danych i upewnij się, pobrać wszystkie dane potrzebne za pomocą jednego zapytania, niż Aby użyć jednego zapytania, aby zlokalizować jednostki, a drugi do wyszukiwania danych wymagane jest.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy Twoja aplikacja kliencka musi pobrać jednostki przy użyciu szeregu różnych kluczy, gdy Twój klient musi pobrać jednostki w poszczególnych kolejności sortowania, a w przypadku, gdy zidentyfikuje każdej jednostki przy użyciu różnych unikatowe wartości. Jednak należy się upewnić, że nie przekraczają limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostki przy użyciu różnych **RowKey** wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Komunikacja między partycji wzorzec indeks pomocniczy](#inter-partition-secondary-index-pattern)
* [Wzorzec klucza złożona (c)](#compound-key-pattern)
* [Transakcje grupy jednostek](#entity-group-transactions)
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Komunikacja między partycji wzorzec indeks pomocniczy
Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości w oddzielnych partycji lub w oddzielnych tabele, aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey**wartości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Usługa Table service automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja kliencka można pobrać jednostki wydajnie za pomocą tych wartości. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka można użyć kwerendy punktu można pobrać jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator ( **PartitionKey** i **RowKey**  wartości). Klienta można także pobrać jednostki posortowanych według identyfikatorów pracowników w ramach każdego działu.  

![Jednostka pracownika][9]

Jeśli chcesz także być w stanie znaleźć jednostki pracowników na podstawie wartości innej właściwości, takie jak adres e-mail musi być znaleźć dopasowania mniej wydajne rozwiązanie skanowania partycji. Jest to spowodowane Usługa tabeli nie zapewnia indeksów pomocniczych. Ponadto, nie ma opcji żądania listy pracowników posortowane w kolejności innej niż **RowKey** zamówienia.  

Są przewidywania dużej liczby transakcji względem tych jednostek i chcesz zminimalizować ryzyko ograniczania klienta szybkości usługi tabeli.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść braku indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdego kopiowania za pomocą różnych **PartitionKey** i **RowKey** wartości. Jeśli przechowujesz jednostki ze strukturami, pokazano poniżej, pozwala na efektywne pobieranie jednostek pracowników na podstawie identyfikatora wiadomości e-mail adres lub pracowników. Prefiks wartości **PartitionKey**, "empid_" i "email_" pozwala na zidentyfikowanie indeksu, której chcesz użyć dla zapytania.  

![Jednostka pracownika o podstawowy indeks i jednostki pracownika o indeks pomocniczy][10]

Następujące kryteria filtrowania dwóch (jeden wyszukiwanie według identyfikatora pracowników i jeden wyszukiwanie według adresu e-mail), zarówno Określ zapytań o punkt:  

* $filter = (PartitionKey eq ' empid_Sales") i (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") i (RowKey eq 'jonesj@contoso.com")  

Po wykonaniu zapytania dotyczącego zakresu jednostek pracowników, można określić zakres posortowane w kolejności identyfikator pracownika lub posortowane w kolejności adres poczty e-mail przy użyciu zapytań dotyczących jednostek, używając odpowiedniego prefiksu w zakresie **RowKey**.  

* Aby znaleźć wszyscy pracownicy działu sprzedaży o identyfikatorze pracowników w zakresie **000100** do **000199** sortowane mogli oni skorzystać identyfikator pracownika: $filter = (PartitionKey eq ' empid_Sales") i (RowKey ge"000100") i (RowKey le "000199")  
* Aby znaleźć wszyscy pracownicy działu sprzedaży za pomocą adresu e-mail, który rozpoczyna się od "" posortowanych w wiadomości e-mail adres mogli oni skorzystać: $filter = (PartitionKey eq ' email_Sales") i (RowKey ge"") i (RowKey lt"b")  

Należy zauważyć, że składnia filtru używanych w powyższych przykładach jest z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [wykonywanie zapytań dotyczących jednostek](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Możesz zachować swoje zduplikowanych wpisów ostatecznie spójną ze sobą przy użyciu [wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) do zachowania jednostek podstawowych i pomocniczych indeksu.  
* Magazyn tabel jest stosunkowo tania do użycia, więc obciążenie koszt przechowywania zduplikowanych danych, nie powinien być głównym problemem. Jednak należy zawsze należy przeprowadzić ocenę koszt projektu, w zależności od wymagań przewidywanych i dodawać tylko zduplikowane jednostki do obsługi zapytań, których będą wykonywane przez aplikację kliencką.  
* Wartość **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Wypełnienie wartości liczbowe w **RowKey** (na przykład identyfikator pracownika 000223), pozwala rozwiązać, sortowanie i filtrowanie na podstawie górną i dolną.  
* Nie zawsze należy zduplikowane właściwości jednostki. Na przykład jeśli zapytania wyszukiwania tej jednostki przy użyciu wiadomości e-mail adresów w **RowKey** nigdy nie potrzebować wiek pracownika, te jednostki może mieć następującą strukturę:
  
  ![Jednostka pracownika o indeks pomocniczy][11]
* Zazwyczaj lepiej do przechowywania zduplikowanych danych i upewnij się, że można pobrać wszystkie dane potrzebne przy użyciu pojedynczego zapytania niż Aby użyć jednego zapytania, aby zlokalizować jednostki za pomocą pomocniczy indeks i umożliwia wyszukiwanie wymagane dane w podstawowy indeks jest.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy Twoja aplikacja kliencka musi pobrać jednostki przy użyciu szeregu różnych kluczy, gdy Twój klient musi pobrać jednostki w poszczególnych kolejności sortowania, a w przypadku, gdy zidentyfikuje każdej jednostki przy użyciu różnych unikatowe wartości. Użyj tego wzorca, gdy użytkownik chce uniknąć przekroczenia limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostki przy użyciu różnych **RowKey** wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeks pomocniczy wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Wzorzec transakcji ostatecznie spójną
Włącz ostatecznie spójne zachowanie całej granice partycji lub granice system magazynu za pomocą kolejek systemu Azure.  

#### <a name="context-and-problem"></a>Kontekst i problem
EGTs Włącz transakcje niepodzielne w wielu jednostkach, które współużytkują ten sam klucz partycji. Względu na wydajność i skalowalność, możesz zdecydować o do przechowywania obiektów, które mają wymagań spójności w oddzielnych partycjach lub w systemie magazynu oddzielne: w takiej sytuacji EGTs nie można użyć, aby zachować spójność. Na przykład może być wymagane, aby zachować spójność między:  

* Jednostki przechowywane w dwóch różnych partycji w tej samej tabeli, w różnych tabelach lub w różnych kontach magazynu.  
* Jednostki przechowywane w usłudze Table service i obiektów blob przechowywanych w usłudze obiektów Blob.  
* Jednostki przechowywane w usłudze Table service i pliku w systemie plików.  
* Magazyn jednostki w usłudze Table service jeszcze indeksowane, za pomocą usługi Azure Search.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą kolejek systemu Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną dwa lub więcej partycji lub systemów magazynowania.
Aby zilustrować to podejście, zakłada się, że masz wymaganie, aby można było archiwum starych jednostek pracownika. Starych jednostek pracowników badane są tabele rzadko i powinny być wykluczone z żadnych działań, które zajmują się obecnie pracowników. Aby zaimplementować ten wymóg przechowywania zatrudnionych pracowników w **bieżącego** tabeli i starego pracowników w **archiwum** tabeli. Archiwizowanie pracownika, musisz usunąć jednostkę z **bieżącego** tabeli, a następnie Dodaj jednostkę którą chcesz **archiwum** tabeli, ale nie można użyć EGT wykonywać te dwie operacje. Aby uniknąć ryzyka, że błąd spowoduje, że jednostki się pojawiać w obu lub ani tabel, operacja archiwizacji musi być ostatecznie spójny. Poniższy diagram sekwencji przedstawia kroki opisane w tej operacji. Więcej szczegółów znajduje się dla ścieżki wyjątku poniżej tekst.  

![Diagram rozwiązania w celu zachowania spójności ostatecznej][12]

Klient inicjuje operację archiwum, umieszczając komunikat w kolejce platformy Azure, w tym przykładzie do archiwizacji pracowników #456. Rola procesu roboczego sonduje kolejkę dla nowych komunikatów; gdy zostanie znaleziony, odczytuje komunikat i pozostawia kopię ukryte w kolejce. Rola procesu roboczego obok pobiera kopię obiektu z **bieżącego** tabeli, wstawia kopię w **archiwum** tabeli, a następnie usuwa oryginalny z **bieżącego** tabeli. Na koniec Jeśli nie wystąpiły błędy z poprzednich kroków, rola procesu roboczego usuwa ukryty komunikat z kolejki.  

W tym przykładzie kroku 4 wstawia pracowników do **archiwum** tabeli. Można go dodać pracownika do obiektu blob w usłudze obiektów Blob lub pliku w systemie plików.  

#### <a name="recovering-from-failures"></a>Odzyskiwanie po awarii
Ważne jest, że operacje w krokach **4** i **5** musi być *idempotentne* w przypadku, gdy rola procesu roboczego musi ponownie uruchomić operację archiwum. Jeśli używasz usługi Table w kroku **4** należy użyć operacji "wstawić lub Zastąp"; kroku **5** należy używać "usunąć, jeśli istnieje" operacji w bibliotece klienta, którego używasz. Jeśli używasz innego systemu magazynowania, należy użyć odpowiedniej idempotentna operacja.  

Jeśli rola procesu roboczego nigdy nie ukończy kroku **6**, a następnie po upływie limitu czasu pojawi się komunikat w kolejce, gotowe do roli procesu roboczego spróbować ponownie przetworzyć go. Rola procesu roboczego można sprawdzić, ile razy komunikat w kolejce został czytać i, jeśli to konieczne, Flaga jest komunikat "skażone" dla badania, wysyłając je do oddzielnej kolejki. Aby uzyskać więcej informacji na temat odczytywania komunikatów z kolejki i sprawdzanie liczby usuwania z kolejki, zobacz [pobierania komunikatów](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Błędy w usługach tabela i kolejka są błędami przejściowymi, a Twoja aplikacja kliencka powinna zawierać logikę ponawiania odpowiednie do ich obsługi.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie wymaga izolacji transakcji. Na przykład, klient może odczytać **bieżącego** i **archiwum** tabel, gdy rola procesu roboczego zostało między krokami **4** i **5**i zobacz niespójne widok danych. Dane będą zgodne po pewnym czasie.  
* Należy się upewnić, że kroki 4 i 5 są idempotentne, w celu zapewnienia spójności ostatecznej.  
* Możesz skalować rozwiązanie przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy użytkownik chce zagwarantować spójność między jednostkami, które istnieją w różnych partycjach lub tabel. Możesz rozszerzyć tego wzorca, aby zapewnić spójność ostateczną dla operacji w usłudze Table service i usługi obiektów Blob i innych niż - usługi Azure Storage źródeł danych, takich jak bazy danych lub systemu plików.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Scalanie lub zastępowanie](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacji transakcji ważne jest, aby rozwiązanie, należy rozważyć przeprojektowanie tabel w celu umożliwienia używania EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  

#### <a name="context-and-problem"></a>Kontekst i problem
Usługa Table service automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja kliencka w celu pobrania jednostki wydajnie za pomocą kwerendy punktu. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka pozwala na efektywne pobieranie jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator ( **PartitionKey** i **RowKey**).  

![Jednostka pracownika][13]

Jeśli chcesz również będzie można go pobrać listę jednostek pracowników na podstawie wartości innej właściwości nie jest unikatowa, takie jak nazwisko, musi być znaleźć dopasowań, a nie przy użyciu indeksu, aby wyszukać bezpośrednio mniej wydajne rozwiązanie skanowania partycji. Jest to spowodowane Usługa tabeli nie zapewnia indeksów pomocniczych.  

#### <a name="solution"></a>Rozwiązanie
Umożliwia wyszukiwanie według nazwiska ze strukturą entity powyżej, musisz utrzymywać listę identyfikatorów pracownika. Jeśli chcesz pobrać jednostki pracownika o określonej nazwie ostatniego, takich jak Jones, należy w pierwszej kolejności zlokalizować listę identyfikatorów pracowników dla pracowników z Jones jako swoje nazwisko, a następnie pobrać tych jednostek pracownika. Istnieją trzy główne opcje przechowywania listy identyfikatorów pracowników:  

* Użyj usługi blob storage.  
* Utwórz jednostki indeksu w jednej partycji jako jednostki pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

<u>Opcja #1: Usługa blob storage</u>  

Pierwszej opcji Tworzenie obiektu blob dla każdego unikatowy nazwisko i w każdym magazynie obiektów blob listę **PartitionKey** (dział) i **RowKey** (identyfikator pracownika) wartości dla pracowników, które mają ten nazwisko. Podczas dodawania lub usuwania pracownika, należy się upewnić, że zawartości odpowiedniego obiektu blob jest ostatecznie spójny z jednostkami pracownika.  

<u>Opcja #2.</u> Tworzenie jednostek indeksu w jednej partycji  

Druga opcja można użyć jednostki indeksu, zawierające następujące dane:  

![Jednostka pracownika o ciąg zawierający listę identyfikatorów pracowników o takiej samej nazwie ostatniego][14]

**EmployeeIDs** właściwość zawiera listę identyfikatorów pracowników dla pracowników z nazwisko przechowywane w **RowKey**.  

Następujące kroki przedstawiają procesu, które należy wykonać podczas korzystania z drugą opcję dodawania nowego pracownika. W tym przykładzie dodamy pracownika z identyfikatorem 000152 i nazwisko Jones działu sprzedaży:  

1. Pobrać jednostki indeksu z **PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Kowalski". Zapisz element ETag to jednostki do użycia w kroku 2.  
2. Tworzenie jednostki transakcji grupy (czyli operacji zbiorczej), która wstawia nową jednostkę pracowników (**PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "000152") i aktualizuje jednostki indeksu (**PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Kowalski") przez dodanie nowego identyfikatora pracowników do listy w polu EmployeeIDs. Aby uzyskać więcej informacji na temat transakcji grup jednostek, zobacz [transakcji grup jednostek](#entity-group-transactions).  
3. Jeśli transakcji grup jednostek zakończy się niepowodzeniem z powodu błędu optymistycznej współbieżności (ktoś inny zmodyfikował jednostki index), należy zacząć od początku w kroku 1 ponownie.  

Można użyć podejście podobne do usuwania pracownika, korzystając z pierwszej opcji. Zmiana nazwisko pracownika jest nieco bardziej skomplikowane, ponieważ będą potrzebne do wykonania transakcji grupy jednostki, która aktualizuje trzech jednostek: jednostki pracowników, jednostki indeksu dla starego nazwisko i jednostki indeksu dla nowe nazwisko. Każda jednostka musi pobrać przed wprowadzeniem jakichkolwiek zmian w celu pobrania wartości element ETag, które następnie służy do przeprowadzania aktualizacji za pomocą optymistycznej współbieżności.  

Poniższe kroki opisują proces, który należy wykonać w przypadku konieczności wyszukania wszystkich pracowników z danym nazwisko w dziale, korzystając z drugą opcją. W tym przykładzie firma Microsoft jest wyszukanie wszystkich pracowników z nazwisko Jones działu sprzedaży:  

1. Pobrać jednostki indeksu z **PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Kowalski".  
2. Przeanalizować listy identyfikatorów w polu EmployeeIDs pracowników.  
3. Aby uzyskać dodatkowe informacje na temat każdego z tych pracowników (na przykład ich adresów e-mail), pobrać każdej jednostki pracowników przy użyciu **PartitionKey** wartość "Sprzedaż" i **RowKey** wartości z kolekcji Lista pracowników, który został uzyskany w kroku 2.  

<u>Opcja #3:</u> Tworzenie indeksu jednostek w oddzielnej partycji lub tabeli  

Trzecia opcja można użyć jednostki indeksu, zawierające następujące dane:  

![Jednostka pracownika o ciąg zawierający listę identyfikatorów pracowników o takiej samej nazwie ostatniego][15]

**EmployeeIDs** właściwość zawiera listę identyfikatorów pracowników dla pracowników z nazwisko przechowywane w **RowKey**.  

Trzecia opcja EGTs nie można użyć do zapewniania spójności, ponieważ jednostki indeksu znajdują się w oddzielnej partycji z jednostek pracownika. Należy upewnić się, że jednostki indeksu są ostatecznie spójny z jednostkami pracownika.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań do pobierania zgodnych jednostek: jeden do wykonywania zapytań jednostki indeks, aby uzyskać listę **RowKey** wartości i zapytania, aby pobrać każdej jednostki na liście.  
* Biorąc pod uwagę, że poszczególne jednostki ma maksymalnym rozmiarze 1 MB, opcja #2 i opcję #3 w rozwiązaniu założono, czy lista identyfikatorów pracowników dla dowolnej podanej nazwy ostatniego nigdy nie jest większa niż 1 MB. W przypadku może być większa niż 1 MB — na listę identyfikatorów pracowników, użyj opcji #1 i przechowywać dane indeksu w usłudze blob storage.  
* W przypadku użycia opcji #2 (przy użyciu EGTs obsługiwać Dodawanie i usuwanie pracowników i zmiana jego nazwisko) należy ocenić, jeśli wielkość transakcji będzie podejście limity skalowalności w danej partycji. Jeśli jest to możliwe, należy rozważyć ostatecznie spójną rozwiązanie (opcja #1 lub #3) używa kolejek do obsługi żądań aktualizacji, która umożliwia przechowywanie jednostek indeksu w oddzielnej partycji z jednostek pracownika.  
* Opcja #2, w tym rozwiązaniu założono, że można wyszukiwać według nazwiska w dziale: na przykład chcesz pobrać listę pracownikom nazwisko Jones w dziale sprzedaży. Jeśli chcesz można było wyszukać wszystkich pracowników z nazwisko Jones w całej organizacji, użyj opcji #1 lub opcja #3.
* Możesz zaimplementować rozwiązanie oparte na kolejce zapewnia spójność ostateczną (zobacz [wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) Aby uzyskać więcej informacji).  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy chcesz wyszukać zestaw jednostek, mających wspólne wartość właściwości, na przykład wszystkich pracowników z nazwisko Jones.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Wzorzec denormalizacja
Ze sobą łączyć powiązanych danych w jednej jednostce umożliwiają można pobrać wszystkie dane potrzebne przy użyciu zapytania pojedynczy punkt.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizacji danych, aby usunąć zduplikowane wynikiem zapytań, które pobierają dane z wielu tabel. Jeśli użytkownik normalizować dane w tabelach platformy Azure, upewnij się wiele rund od klienta do serwera w celu pobrania danych powiązanych. Na przykład struktura tabeli poniżej możesz wymaga dwóch wystąpień komunikacji dwustronnej można pobrać szczegółów dla działu: jeden do pobierania jednostki działu, która zawiera identyfikator menedżera, a następnie kolejnego żądania można pobrać szczegółów Menedżera w jednostce pracownika.  

![Dział a pracownika][16]

#### <a name="solution"></a>Rozwiązanie
Zamiast przechowywać dane w dwóch oddzielnych jednostkach, zdenormalizowanie danych i przechowywać kopię szczegółach menedżera w jednostce działu. Na przykład:  

![Jednostki działu nieznormalizowany i połączone][17]

Z jednostkami działu przechowywane z tymi właściwościami można teraz pobierać wszystkie szczegóły potrzebne o dział przy użyciu kwerendy punktu.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje pewien koszt obciążenie związanych z przechowywaniem niektóre dane w dwa razy. Korzyści w zakresie wydajności (wynikające z mniejszą liczbą żądań do usługi storage) zwykle przewyższa brzegowych wzrost kosztów magazynowania (i ten koszt jest częściowo przesunięcie poprzez zmniejszenie liczby transakcji wymagane w celu pobrania szczegółów dotyczących dział ).  
* Należy zachować spójność dwie jednostki, które przechowują informacje o menedżerów. Problem spójności może obsługiwać za pomocą EGTs do zaktualizowania wielu jednostek w ramach jednej transakcji niepodzielnej: w tym przypadku jednostki działu i jednostki pracowników działu Menedżera są przechowywane w tej samej partycji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz często do wyszukania powiązanych informacji. Ten wzorzec pozwala zmniejszyć liczbę zapytań, które należy wprowadzić klienta, aby pobierać dane, które wymaga.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Wzorzec klucza złożona (c)
Użyj złożone **RowKey** wartości, aby włączyć klienta do wyszukiwania danych powiązane z zapytaniem pojedynczy punkt.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych jest naturalnym zwrócić powiązane elementy danych do klienta w ramach pojedynczego zapytania za pomocą sprzężeń w zapytaniach. Identyfikator pracownika może na przykład użyć, aby wyszukać listę powiązanych jednostek, które zawierają wydajności i przeglądać dane dla tego pracownika.  

Przyjęto założenie, że pracownik jednostki będą przechowywane w usłudze tabeli przy użyciu następującej struktury:  

![Jednostka pracownika][18]

Należy również do przechowywania danych historycznych dotyczących przeglądów i wydajności dla każdego roku, w których pracownik pracował dla Twojej organizacji i muszą mieć możliwość dostępu do tych informacji przez rok. Jedną z opcji jest, aby utworzyć inną tabelę, która przechowuje jednostki o następującej strukturze:  

![Jednostka Recenzja pracownika][19]

Zwróć uwagę, w tym podejściu użytkownik może zdecydować o zduplikowane niektórych informacji (takich jak imię i nazwisko) w nowej jednostce umożliwiają pobranie danych za pomocą pojedynczego żądania. Nie można jednak obsługa silnej spójności, ponieważ EGT nie można użyć do zaktualizowania niepodzielne dwie jednostki.  

#### <a name="solution"></a>Rozwiązanie
Store nowy typ jednostki w oryginalnej tabeli za pomocą jednostek o następującej strukturze:  

![Jednostki pracowników przy użyciu klucza złożonego][20]

Zwróć uwagę sposób, w jaki **RowKey** jest teraz klucza złożonego składa się z identyfikator pracownika i roku przeglądanie danych, która umożliwia pobieranie wydajność pracowników i przeglądanie danych z pojedynczego żądania dla pojedynczej jednostki.  

Poniższy przykład przedstawia, jak pobrać przeglądanie danych dla danego pracownika (na przykład 000123 pracownik działu sprzedaży):  

$filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "empid_000123") i (RowKey lt "empid_000124") & $select = RowKey, ocena menedżera, ocena elementów równorzędnych, komentarze  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego separatora, który ułatwia analizowanie **RowKey** wartość: na przykład **000123_2012**.  
* Tę jednostkę będą również przechowywane w tej samej partycji, co inne jednostki, które zawierają dane dotyczące tych samych pracownika, co oznacza, że używasz EGTs utrzymanie wysokiego poziomu spójności.
* Należy rozważyć, jak często będą zapytań danych, aby ustalić, czy ten wzorzec jest odpowiedni.  Na przykład jeśli będzie dostęp rzadko przeglądanie danych i danych o pracownikach głównego często warto przechowywać je jako osobne jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy chcesz przechować jeden lub więcej powiązanych jednostek to zapytanie można często.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Wzorzec ogona dziennika
Pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  

> [!NOTE]
> Wyniki zapytania, zwracana przez interfejs API tabeli platformy Azure w usłudze Azure DB nie są sortowane według klucza partycji i klucza wiersza. W związku z tym ten wzorzec jest odpowiednia dla usługi Azure Table Storage, a nie usługi Azure Cosmos DB. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice interfejsu API tabel w usłudze Azure Cosmos DB i Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymogiem jest będzie można go pobrać ostatnio utworzone jednostki, na przykład dziesięć ostatnich wydatków oświadczenia złożone przez pracownika. Tabela zapytania pomocy technicznej **$top** operację, aby powrócić do pierwszego zapytania *n* jednostki z zestawu: istnieje żadna operacja równoważne zapytania do zwrócenia ostatnie n jednostki w zestawie.  

#### <a name="solution"></a>Rozwiązanie
Store jednostki przy użyciu **RowKey** że naturalnie sortuje w kolejności odwrotnej daty/godziny przy użyciu więc najnowszy wpis jest zawsze pierwszej tabeli.  

Na przykład aby można było pobrać dziesięć ostatnich oświadczenia wydatków przesłane przez pracownika, służy wartość odwrotnej osi pochodzi od bieżącej daty/godziny. Poniższy przykładowy kod języka C# zawiera jeden ze sposobów tworzenia odpowiednią wartość "odwrócony znaczników" dla **RowKey** która sortuje od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić do wartość Data / czas, używając następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie dotyczące tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy konsoli wartość odwrotnej osi z wiodącymi zerami w celu zapewnienia, że wartość ciągu Sortuje zgodnie z oczekiwaniami.  
* Należy pamiętać o cele skalowalności na poziomie partycji. Należy zachować ostrożność nie należy tworzyć partycje aktywny.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz dostępu do jednostek w kolejności odwrotnej daty/godziny lub konieczność dostęp do ostatnio dodanych jednostek.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Dołączana / dołączanie wzorzec niezalecane](#prepend-append-anti-pattern)  
* [Pobieranie jednostki](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Wzorzec usuwania duża
Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki do usunięcia jednoczesnych w ich własnych osobnej tabeli; jednostki można usunąć przez usunięcie tabeli.  

#### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji, Usuń stare dane, nie musi już być dostępne dla aplikacji klienckich, lub aplikacja ma zarchiwizować na innym urządzeniu pamięci masowej. Zazwyczaj zidentyfikować te dane według daty: na przykład masz wymagania do usuwania rekordów z wszystkich żądań logowania, które są starsze niż 60 dni.  

Jeden projekt możliwe jest użycie datę i godzinę w żądaniu logowania **RowKey**:  

![Jednostki próba logowania][21]

W tym podejściu unika obszarów nadmiernej aktywności partycji, ponieważ aplikacja może wstawiania i usuwania logowania jednostki dla każdego użytkownika w oddzielnej partycji. Jednak takie podejście może być kosztowna i czasochłonna Jeśli masz dużą liczbę jednostek, ponieważ najpierw skanowanie tabeli, aby zidentyfikować wszystkie jednostki do usunięcia, a następnie należy usunąć stare jednostki. Można zmniejszyć liczby rund do serwera, wymagane, aby usunąć stare jednostek, przetwarzanie wsadowe wielu żądań delete służących do EGTs.  

#### <a name="solution"></a>Rozwiązanie
Dla każdego dnia prób logowania, należy użyć osobnej tabeli. Powyżej projektu jednostki można użyć, aby uniknąć obszarów aktywności, gdy wstawiasz jednostek i usuwania starych jednostek jest teraz po prostu pytanie usunięcia każdego dnia w jednej tabeli (operacją pojedynczy magazyn) zamiast Znajdowanie i usuwanie setki i tysiące osoby znaki w jednostkach każdego dnia.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Projekt obsługuje inne sposoby, aplikacja będzie używać danych, takie jak wyszukiwanie określonych jednostek, łączenie z innymi danych lub generowania agregują informacje?  
* Projekt uniknąć punktów aktywnych, wstawiając nowych jednostek?  
* Oczekiwane opóźnienie, jeśli chcesz ponownie użyć tej samej nazwy tabeli po jego usunięciu. Zaleca się zawsze używać nazw unikatowej tabeli.  
* Oczekiwać, że niektóre szybkości przy pierwszym użyciu nową tabelę Table service uczy się wzorców dostępu i dystrybuuje partycji na węzłach. Należy rozważyć, jak często należy utworzyć nowe tabele.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli masz dużą liczbę jednostek, które należy usunąć w tym samym czasie.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Modyfikowanie jednostek](#modifying-entities)  

### <a name="data-series-pattern"></a>Wzorzec serii danych
Seria kompletne dane Store w pojedynczą jednostkę, aby zminimalizować liczbę żądań, które wprowadzasz.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz polega na dla aplikacji, aby przechowywać serię danych, które zazwyczaj musi pobrać wszystkie na raz. Na przykład aplikacja może rejestrować liczbę komunikatów wiadomości Błyskawicznych każdemu pracownikowi wysyła co godzinę, a następnie te informacje służą do wykreślenia liczbę komunikatów każdego użytkownika wysłanych w ciągu poprzednich 24 godzin. Jeden projekt może być przechowywanie podmioty 24 dla każdego pracownika:  

![Jednostki statystyki komunikatów][22]

W tym projekcie można łatwo zlokalizować i aktualizować jednostki do zaktualizowania dla każdego pracownika, zawsze wtedy, gdy aplikacja musi zaktualizować wartość licznika komunikatów. Jednak aby pobrać informacje do wykreślenia wykresu aktywności w poprzednich 24 godzin, możesz pobrać jednostki 24.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą następujących projektowych osobne właściwości do przechowywania liczba komunikatów dla każdej godziny:  

![Jednostki statystyki komunikatów z właściwościami rozdzielonych][23]

W tym projekcie można użyć operacji scalania, można zaktualizować liczba komunikatów dla pracownika określonej godziny. Teraz możesz pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli serii kompletne dane nie mieści się w pojedynczej jednostki (jednostka może mieć maksymalnie 252 właściwości), należy korzystać z magazynu danych alternatywne, np. obiekt blob.  
* Jeśli masz wielu klientów jednocześnie aktualizowania jednostki, musisz użyć **ETag** na potrzeby optymistycznej współbieżności. Jeśli masz wielu klientów, może wystąpić wysokiej rywalizacji o zasoby.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba zaktualizować i pobrania serii danych skojarzonych z pojedynczą jednostkę.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scalanie lub zastępowanie](#merge-or-replace)  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) (Jeśli przechowujesz serii danych w obiekcie blob)  

### <a name="wide-entities-pattern"></a>Wzorzec szerokiego jednostek
Wiele jednostek fizycznych umożliwiają przechowywanie jednostek logicznych z więcej niż 252 właściwości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Poszczególne jednostki może mieć maksymalnie 252 właściwości (z wyjątkiem właściwości systemowe obowiązkowy) i nie można zapisać więcej niż 1 MB danych w sumie. W relacyjnej bazie danych zazwyczaj otrzymamy round jakiekolwiek ograniczenia dotyczące rozmiaru wiersz, dodając nową tabelę i wymuszanie 1-do-1 relację między nimi.  

#### <a name="solution"></a>Rozwiązanie
Usługa Table service można przechowywać wiele jednostek do reprezentowania obiekt pojedynczego dużych firm z ponad 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości Błyskawicznych komunikatów wysyłanych przez każdego pracownika w ciągu ostatnich 365 dni, można użyć następujących projekt, który wykorzystuje dwie jednostki z różnymi schematami:  

![Jednostka statystyki wiadomości o Rowkey 01 i komunikat jednostki stanu z Rowkey 02][24]

Jeśli musisz wprowadzić zmianę, która wymaga zaktualizowania obu jednostek, aby były ze sobą zsynchronizowane, można użyć EGT. W przeciwnym razie można użyć operacji scalania pojedynczego można zaktualizować liczba komunikatów dla określonego dnia. Aby pobrać wszystkie dane dla poszczególnych pracowników musi pobrać obie jednostki, można to zrobić za pomocą dwóch żądania wydajne, które używają zarówno **PartitionKey** i **RowKey** wartości.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Trwa pobieranie całą jednostkę logiczną obejmuje co najmniej dwie transakcje magazynu: jedną do pobrania każda jednostka fizyczna.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy konieczne przechowywanie podmioty, których rozmiar lub liczbę właściwości przekracza limit pojedynczą jednostkę w usłudze Table service.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Scalanie lub zastępowanie](#merge-or-replace)

### <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Użyj usługi blob storage do przechowywania wartości właściwości dużych.  

#### <a name="context-and-problem"></a>Kontekst i problem
Poszczególne jednostki nie można przechowywać więcej niż 1 MB danych, w sumie. Jeśli jeden lub kilka właściwości przechowywać wartości, które powodują całkowity rozmiar jednostki przekracza tę wartość, nie można zapisać całej jednostki w usłudze Table service.  

#### <a name="solution"></a>Rozwiązanie
Jeśli jednostka przekracza 1 MB rozmiar, ponieważ co najmniej jedna właściwość zawiera dużą ilość danych, możesz przechowywać dane w usłudze obiektów Blob i następnie zapisać adres obiektu blob w właściwości w obiekcie. Na przykład można przechowywać zdjęcia pracownika w usłudze blob storage i przechowywać łącze do zdjęć w **zdjęcie** właściwości jednostki pracowników:  

![Jednostki pracowników przy użyciu parametrów, zdjęcia, wskazując do magazynu obiektów blob][25]

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność między jednostki w usłudze Table service i danych w usłudze obiektów Blob, należy użyć [wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) do zachowania jednostek.
* Pobieranie całą jednostkę obejmuje co najmniej dwie transakcje magazynowe: jeden do pobierania jednostki, a drugi do pobierania danych obiektów blob.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba przechowywanie podmioty, których rozmiar przekracza limit pojedynczą jednostkę w usłudze Table service.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  
* [Wzorzec szerokiego jednostek](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Dołączana/dołączanie zapobieganie wzorzec
Zwiększa skalowalność, jeśli masz dużą liczbę operacji wstawienia przez rozłożenie operacje wstawiania na wielu partycjach.  

#### <a name="context-and-problem"></a>Kontekst i problem
Dołączenie lub dołączanie jednostek do jednostki przechowywanej zwykle powoduje w aplikacji, dodając nowe jednostki do partycji pierwszej lub ostatniej sekwencji partycji. W takim przypadku wszystkie operacje wstawiania w danym momencie pojawiają się w tej samej partycji, tworzenie informacji o hotspotach, który uniemożliwia równoważenia obciążenia w usłudze table service wstawia w wielu węzłach i prawdopodobnie jest przyczyną trafień cele skalowalności dla aplikacji partycja. Na przykład, jeśli masz aplikację, dzienniki sieci i zasobów dostęp przez pracowników, następnie struktury jednostki, jak pokazano poniżej może spowodować partycji bieżącej godziny staje się aktywny, jeśli wielkość transakcji osiągnie tę docelową skalowalność dla poszczególnych partycji:  

![Jednostka pracownika][26]

#### <a name="solution"></a>Rozwiązanie
Z następującej struktury jednostki alternatywnych pozwala uniknąć punkt aktywny na żadnej określonej partycji jako zdarzenia z dzienników aplikacji:  

![Jednostka pracownika o RowKey łączenia rok, miesiąc, dzień, godzinę i Identyfikatora zdarzenia][27]

Powiadomienie z tym przykładem sposobu zarówno **PartitionKey** i **RowKey** są klucze złożone. **PartitionKey** używa identyfikatora dziale i pracowników do dystrybucji rejestrowanie na wielu partycjach.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Alternatywne struktury klucza, który pozwala uniknąć tworzenia partycji w warstwie gorąca na operacje wstawiania efektywnie obsługuje zapytania, który sprawia, że Twoja aplikacja kliencka?  
* Przewidywane ilości transakcji oznacza że prawdopodobnie osiągnąć cele skalowalności dla poszczególnych partycji i ograniczona przez usługę storage?  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać prepend/dołączanie wzorzec zapobieganie po skutkować ograniczania za usługi storage, gdy uzyskujesz dostęp do gorącą partycją szybkości ilości transakcji.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* [Wzorzec ogona dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Wzorzec ochrony danych dziennika
Zazwyczaj należy użyć usługi obiektów Blob zamiast usługi tabel do przechowywania danych dziennika.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy przypadek użycia, danych dziennika jest pobrać wybór przyrostu wpisów dziennika dla zakresu określonej daty/godziny: na przykład chcesz odnaleźć wszystkich błędów i krytycznych wiadomości o zarejestrowane przez aplikację między 15:04 i 15:06 w określonym dniu. Czy chcesz umożliwia określenia partycji, Zapisz dziennik jednostek do daty i godziny komunikatu dziennika: wyniki w warstwie gorąca partycji, ponieważ w dowolnym momencie wszystkie jednostki dziennika będą współużytkować ten sam **PartitionKey** wartości (zobacz sekcja [Prepend/dołączanie zapobieganie wzorzec](#prepend-append-anti-pattern)). Na przykład poniższy schemat jednostki dla komunikatu dziennika powoduje gorącą partycją ponieważ aplikacja zapisuje wszystkie komunikaty dziennika do partycji dla bieżącej daty i godziny:  

![Jednostka wiadomości dziennika][28]

W tym przykładzie **RowKey** zawiera datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są przechowywane, sortowane w kolejności daty/godziny oraz identyfikator komunikatu w przypadku, gdy wiele komunikatów dziennika udostępnianie tej samej daty i godziny.  

Innym rozwiązaniem jest użycie **PartitionKey** temu, że aplikacja zapisuje komunikaty w wielu partycjach. Na przykład źródło komunikatu dziennika umożliwia rozłożenie wiadomości na wielu partycjach, można użyć następującego schematu jednostki:  

![Jednostka wiadomości dziennika][29]

Problem z tym schemacie jest jednak, że można pobrać wszystkie komunikaty dziennika dla zakresu określonej godzinie wyszukiwanie wszystkich partycji w tabeli.

#### <a name="solution"></a>Rozwiązanie
Wcześniejsze wyróżnione problem próby Table service służy do przechowywania wpisów dziennika i sugerowane dwóm niezadowalające, projekty. Jedno rozwiązanie, które doprowadziły do gorącą partycją o ryzyku niską wydajnością, zapisywanie komunikatów dziennika; inne rozwiązanie w wyniku zapytania niskiej wydajności ze względu na konieczność skanowania wszystkich partycji w tabeli, aby pobrać komunikaty dziennika dla zakresu określonej godzinie. Usługa blob storage oferuje lepsze rozwiązania dla tego typu scenariusza i jest to, jak usługa Azure Storage Analytics magazyny danych dzienników są zbierane.  

W tej sekcji opisano, jak usługa Storage Analytics przechowuje dane dzienników w magazynie obiektów blob jako ilustrację tego podejścia do przechowywania danych, które zazwyczaj zapytania według zakresu.  

Usługa Storage Analytics przechowuje komunikaty w dzienniku w formacie rozdzielanym w wielu obiektów blob. Rozdzielany format ułatwia aplikacji klienckiej do analizowania danych w komunikacie dziennika.  

Usługa Storage Analytics używa konwencji nazewnictwa dla obiektów blob, które umożliwia lokalizowanie obiektu blob (lub obiektów blob), zawierające komunikaty dziennika, dla których potrzebujesz. Na przykład obiekt blob o nazwie "queue/2014/07/31/1800/000001.log" zawiera komunikaty dziennika, które odnoszą się do usługi kolejki, godzinę, począwszy od 18:00 dnia 31 lipca 2014. "000001" oznacza, że jest pierwszy plik dziennika dla tego okresu. Usługa Storage Analytics rejestruje także sygnatury czasowe komunikatów dziennika imię i nazwisko, przechowywane w pliku metadanych obiektu blob w ramach. Interfejs API umożliwia magazynu obiektów blob, zlokalizuj obiektów blob w kontenerze na podstawie prefiksu nazwy: Aby znaleźć wszystkie obiekty BLOB, które zawierają dane dziennika w kolejce na godzinę, począwszy od 18:00, możesz użyć prefiksu "kolejki/2014/07/31/1800."  

Storage Analytics buforów rejestrowania komunikatów wewnętrznie i następnie okresowo zaktualizować odpowiedniego obiektu blob lub tworzy nową grupę za pomocą najnowszego wsadu wpisy dziennika. Zmniejsza to liczbę operacji zapisu, które należy wykonać na usługę blob Service.  

Przed zaimplementowaniem podobnego rozwiązania w swojej aplikacji, należy wziąć pod uwagę sposób zarządzania kompromis między niezawodności (zapisywania każdy wpis dziennika do magazynu obiektów blob, jak to się dzieje) oraz kosztu i skalowalności (buforowanie aktualizacje w aplikacji i zapisywanie ich do usługi blob storage w partiach).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie przechowywania danych dziennika, należy rozważyć następujące kwestie:  

* Jeśli tworzysz projektowaniu tabel, która pozwala uniknąć potencjalnych partycje gorąca, może się okazać, że nie masz dostępu do danych dziennika wydajnie.  
* Aby przetwarzać dane dziennika, klient często musi załadować wiele rekordów.  
* Mimo, że dane dziennika często mają strukturę, magazynu obiektów blob może być lepszym rozwiązaniem.  

### <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, mieć na uwadze podczas implementowania wzorców opisanych w poprzednich sekcjach. Większość w tej sekcji używa przykłady napisane w języku C#, korzystających z biblioteki klienta usługi Storage (wersja 4.3.0 w czasie pisania).  

### <a name="retrieving-entities"></a>Pobieranie jednostki
Zgodnie z opisem w sekcji [projektowanie pod kątem zapytań](#design-for-querying), najbardziej wydajny zapytanie jest zapytaniem punktu. Jednak w niektórych scenariuszach może być konieczne pobieranie wielu jednostek. W tej sekcji opisano niektóre typowe sposoby pobierania jednostki przy użyciu biblioteki klienta usługi Storage.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Wykonywanie kwerendy punktu, przy użyciu biblioteki klienta magazynu
Najprostszym sposobem wykonania kwerendy punktu jest użycie **pobrać** tabelę operacji, jak pokazano w poniższym C# fragmentu kodu, które pobiera jednostki z **PartitionKey** wartości "Sprzedaż" i  **RowKey** wartości "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekuje, że jednostka pobiera typu **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Trwa pobieranie wielu jednostek za pomocą LINQ
Możesz pobrać wiele jednostek przy użyciu LINQ za pomocą biblioteki klienta usługi Storage i określając zapytanie o **gdzie** klauzuli. Aby uniknąć skanowanie tabeli, zawsze powinna zawierać **PartitionKey** wartości w klauzuli where klauzuli i jeśli to możliwe **RowKey** wartość, aby uniknąć skanowanie tabeli i partycji. Usługa table service obsługuje ograniczony zestaw operatory porównania (większa, większa niż lub równe mniej niż, mniejsze lub równe, równe i nie równa się), aby użyć w klauzuli where — klauzula. Poniższy fragment kodu języka C# znajduje wszystkich pracowników, którego ostatnie nazwa rozpoczyna się od ciągu "B" (przy założeniu, że **RowKey** przechowuje nazwisko) używanych w dziale sprzedaży (zakładając, że **PartitionKey** przechowuje Nazwa działu):  

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

Poniższy przykład kodu pokazuje równoważne funkcje przy użyciu interfejsu API fluent (Aby uzyskać więcej informacji na temat płynnych interfejsów API ogólnie rzecz biorąc, zobacz [najlepsze rozwiązania dotyczące projektowania interfejs Fluent API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Przykład zagnieżdżony wielu **CombineFilters** metody służące do dołączania trzech warunków filtrowania.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Trwa pobieranie dużej liczby obiektów z kwerendy
Optymalne zapytanie zwraca pojedynczą jednostkę na podstawie **PartitionKey** wartość i **RowKey** wartości. Jednak w niektórych scenariuszach może być wymagane do zwrócenia wielu jednostek z jednej partycji lub nawet od liczby partycji.  

W takich scenariuszach, należy zawsze w pełni przetestować wydajność aplikacji.  

Zapytanie względem usługi tabeli może zwrócić więcej niż 1000 jednostek w tym samym czasie i może być wykonywane przez maksymalnie pięć sekund. Jeśli zestaw wyników zawiera więcej niż 1000 jednostek, jeśli zapytanie nie została ukończona w ciągu pięciu sekund lub zapytanie przecina granicę partycji, usłudze Table service zwraca token kontynuacji, aby umożliwić aplikacji klienckiej zażądać następnego zestawu jednostek. Aby uzyskać więcej informacji na temat sposobu kontynuacji tokenów pracy, zobacz [limit czasu zapytania i dzielenia na strony](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Jeśli używasz biblioteki klienta usługi Storage go automatycznie obsługiwać tokenów kontynuacji dla Ciebie jako zwraca jednostki z usługi tabel. Poniższy przykład kodu C# automatycznie przy użyciu biblioteki klienta usługi Storage obsługuje tokeny kontynuacji, jeśli usługa table service zwraca je w odpowiedzi:  

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

Korzystając z kontynuacji tokenów jawnie, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład, jeśli Twoja aplikacja kliencka pozwala użytkownikom na stronie za pośrednictwem jednostki przechowywane w tabeli, użytkownik może zadecydować o stronie za pośrednictwem wszystkich jednostek, które są pobierane przez zapytanie, dzięki czemu aplikacja używa się tylko token kontynuacji do pobrania następnej segmentu, gdy użytkownik miał zakończył stronicować wszystkich jednostek w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Umożliwia ograniczenie ilości danych, które można pobrać z usługi tabel i Przenieś za pośrednictwem sieci.  
* Umożliwia wykonywanie asynchronicznych operacji We/Wy na platformie .NET.  
* Pozwala ona do serializacji token kontynuacji w magazynie trwałym, aby można było kontynuować na wypadek awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zwykle zwraca segment zawierający 1000 jednostek, chociaż może być mniej. Dotyczy to także ograniczyć liczbę wpisów zapytanie zwraca przy użyciu **zająć** do zwrócenia pierwsze n jednostek spełniających podane kryteria wyszukiwania: table service może zwrócić segment zawierający mniej niż n jednostki wraz z programem token kontynuacji, aby umożliwić pobieranie pozostałych jednostek.  
> 
> 

Poniższy kod C# pokazuje sposób modyfikowania liczby jednostek zwróconych w segmencie:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekcja po stronie serwera
Pojedynczy element może mieć maksymalnie 255 właściwości i mieć rozmiar maksymalnie 1 MB. Gdy odpytać tabelę i pobieranie jednostek, mogą nie być potrzebne wszystkie właściwości i uniknąć przesyłania danych niepotrzebnie (w celu zmniejszenia opóźnień i kosztów). Projekcja po stronie serwera umożliwia transfer tylko właściwości, których potrzebujesz. Poniższy przykład pobiera tylko **E-mail** właściwości (wraz z **PartitionKey**, **RowKey**, **sygnatura czasowa**i **ETag**) z jednostek wybranych przez zapytanie.  

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

Zwróć uwagę sposób, w jaki **RowKey** wartość jest dostępna, nawet jeśli nie zostało uwzględnione w listy właściwości do pobrania.  

### <a name="modifying-entities"></a>Modyfikowanie jednostek
Biblioteki klienta usługi Storage umożliwia modyfikowanie jednostek przechowywanych w usłudze tabeli, wstawianie, usuwanie i aktualizowanie jednostek. EGTs Umożliwia wsadowe wielu operacji wstawiania, aktualizacji i operacje usuwania razem w celu zmniejszenia liczby rund wymagane i zwiększa wydajność rozwiązania.  

Wyjątki zgłaszane, gdy biblioteka klienta magazynu EGT zwykle obejmują indeks jednostki, który spowodował partii nie powiedzie się. Jest to przydatne podczas debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projekt wpływa na sposób Twoja aplikacja kliencka obsługi operacji współbieżności i aktualizacji.  

#### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa table service implementuje optymistycznej współbieżności testy na poziomie poszczególnych jednostek dla **Wstaw**, **scalania**, i **Usuń** operacji, mimo że go jest możliwe, aby klient wymusić uruchomienia usługi tabeli, aby pominąć te testy. Aby uzyskać więcej informacji na temat sposobu zarządzania współbieżności w usłudze table service, zobacz [Zarządzanie współbieżnością w Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Scalanie lub zastępowanie
**Zastąp** metody **TableOperation** klasy zawsze zastępuje całą jednostkę w usłudze Table service. Jeśli nie ma właściwości w żądaniu w jednostki przechowywanej istnieje tę właściwość, żądanie usuwa tę właściwość z jednostki przechowywanej. Jeśli nie chcesz jawnie usunąć właściwość z jednostki przechowywanej musi zawierać dla każdej właściwości w żądaniu.  

Możesz użyć **scalania** metody **TableOperation** klasy, aby zmniejszyć ilość danych przesyłanych do usługi Table zaktualizować jednostkę. **Scalania** metoda zastępuje wszystkie właściwości w jednostki przechowywanej wartości właściwości z jednostki zawarty w żądaniu, i pozostawienie bez zmian żadnych właściwości przechowywanych jednostki, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz dużych jednostek i wymaga tylko zaktualizowania niewielkiej liczby właściwości w żądaniu.  

> [!NOTE]
> **Zastąp** i **scalania** metody się nie powieść, jeśli jednostka nie istnieje. Alternatywnie, można użyć **InsertOrReplace** i **InsertOrMerge** metody, które Utwórz nową jednostkę, jeśli nie istnieje.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Praca z typami encji heterogenicznych
Table service to *bez schematu* magazynu tabel, oznacza to, że pojedynczej tabeli można przechowywać jednostki wiele typów, zapewniając elastyczność w projekcie. W poniższym przykładzie pokazano tabelę przechowywania pracownika i działu jednostki:  

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

Każda jednostka może nadal mieć **PartitionKey**, **RowKey**, i **sygnatura czasowa** wartości, ale mogą mieć dowolny zbiór właściwości. Ponadto nie ma nic do wskazać typ jednostki, jeśli nie chcesz przechowywać tych informacji w jakimś miejscu. Dostępne są dwie opcje do identyfikowania typu jednostki:  

* Dodaj typ jednostki do **RowKey** (albo ewentualnego **PartitionKey**). Na przykład **EMPLOYEE_000123** lub **DEPARTMENT_SALES** jako **RowKey** wartości.  
* Aby zarejestrować typ jednostki, jak pokazano w poniższej tabeli, należy użyć oddzielnych właściwości.  

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

Pierwsza opcja AS jednostki wpisz, aby **RowKey**, jest przydatne, jeśli istnieje możliwość, że dwie jednostki o różnych typach może mieć taką samą wartość klucza. Grup jednostek z tego samego typu, ze sobą w partycji.  

Techniki opisane w tej sekcji są szczególnie istotne w dyskusji [relacje dziedziczenia](#inheritance-relationships) we wcześniejszej części tego przewodnika, w sekcji modelowanie relacji.  

> [!NOTE]
> Należy rozważyć, w tym numer wersji w wartości typu jednostki, aby umożliwić aplikacjom klienckim ewoluować obiektów POCO i pracy z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre z funkcji w bibliotece klienta usługi Storage, które ułatwiają pracę z wieloma typami jednostki w tej samej tabeli.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Trwa pobieranie typów jednostek heterogenicznych
Jeśli używasz biblioteki klienta usługi Storage są trzy opcje do pracy z wieloma typami encji.  

Jeśli jest znany typ jednostki przechowywane z określonym **RowKey** i **PartitionKey** wartości, a następnie po pobraniu jednostki, jak pokazano w poprzednich dwóch przykładach, można określić typu jednostki, Pobieranie jednostki typu **EmployeeEntity**: [Wykonywanie kwerendy punktu, przy użyciu biblioteki klienta magazynu](#executing-a-point-query-using-the-storage-client-library) i [Pobieranie wielu jednostek za pomocą LINQ](#retrieving-multiple-entities-using-linq).  

Drugą opcją jest użycie **DynamicTableEntity** typu (zbiór właściwości) zamiast konkretny typ jednostki POCO (Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby do serializacji i deserializacji jednostki do typów .NET). Poniższy kod C# potencjalnie pobiera wielu jednostek o różnych typach z tabeli, ale zwraca wszystkie jednostki jako **DynamicTableEntity** wystąpień. Następnie używa **EntityType** właściwości, aby określić typ każdego obiektu:  

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

Aby uzyskać inne właściwości, należy użyć **TryGetValue** metody **właściwości** właściwość **DynamicTableEntity** klasy.  

Trzecia opcja to się połączyć przy użyciu **DynamicTableEntity** typu i **EntityResolver** wystąpienia. Dzięki temu można prowadzić do wielu typów POCO tego samego zapytania. W tym przykładzie **EntityResolver** używa delegata **EntityType** właściwości do rozróżniania między dwoma rodzajami jednostki, która zwraca kwerendy. **Rozwiązać** metoda używa **rozpoznawania** delegata, aby rozwiązać **DynamicTableEntity** wystąpień do **TableEntity** wystąpień.  

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
Nie trzeba znać typ jednostki, aby go usunąć, i zawsze znasz typ jednostki po wstawieniu go. Można jednak użyć **DynamicTableEntity** typu, aby zaktualizować jednostkę, nie wiedząc o tym jej typ i bez użycia klasą jednostki POCO. Poniższy przykładowy kod umożliwia pobranie pojedynczej jednostki i sprawdza **EmployeeCount** istnieje właściwość przed aktualizacją.  

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
Tokeny sygnatury dostępu współdzielonego (SAS) można użyć, aby umożliwić aplikacjom klienckim zmodyfikować (i wykonywanie zapytań) jednostek tablic bezpośrednio, bez konieczności uwierzytelniania bezpośrednio z usługą table. Zwykle istnieją trzy główne zalety za pomocą sygnatury dostępu Współdzielonego w Twojej aplikacji:  

* Nie trzeba rozpowszechniać klucz konta magazynu, platformę niezabezpieczone (na przykład urządzenie przenośne) w celu umożliwienia tego urządzenia do dostępu i modyfikowania obiektów w usłudze Table service.  
* Można odciążyć niektóre czynności, które role sieć web i proces roboczy wykonywania związanych z zarządzaniem jednostek na urządzeniach klienckich, takich jak na komputerach użytkowników końcowych i urządzeniach przenośnych.  
* Możesz przypisać ograniczone i czas ograniczony zestaw uprawnień do klienta (na przykład zezwalając na dostęp tylko do odczytu, do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów sygnatur dostępu Współdzielonego z usługą tabeli, zobacz [przy użyciu dostępu współdzielonego Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Jednakże nadal należy wygenerować tokeny sygnatur dostępu Współdzielonego, określającymi udzielenie aplikacji klienckiej jednostek w usłudze table service: to zrobić w środowisku zawierającym bezpiecznego dostępu do kluczy konta magazynu. Zazwyczaj używasz rola sieć web lub procesu roboczego do generowania tokenów sygnatur dostępu Współdzielonego i dostarczaj je na potrzeby aplikacji klienckich, które muszą mieć dostęp do jednostek. Ponieważ jest nadal obciążenie związane z generowania i dostarczania tokeny sygnatur dostępu Współdzielonego do klientów, należy rozważyć, jak najlepiej Aby zmniejszyć to obciążenie, szczególnie w przypadku dużej liczby scenariuszy.  

Istnieje możliwość wygenerowania tokenu sygnatury dostępu Współdzielonego, która udziela dostępu do podzestawu jednostek w tabeli. Domyślnie, możesz utworzyć token sygnatury dostępu Współdzielonego dla całej tabeli, jednak istnieje również możliwość określenia, czy token sygnatury dostępu Współdzielonego udzielić dostępu do jednej szeroką gamę **PartitionKey** wartości lub zakres **PartitionKey** i **RowKey** wartości. Możesz wybrać do generowania tokenów sygnatur dostępu Współdzielonego dla poszczególnych użytkowników systemu w taki sposób, że każdy użytkownik tokenu sygnatury dostępu Współdzielonego tylko umożliwia im dostęp do ich własnych jednostek w usłudze table service.  

### <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Podana rozpraszania żądań na wielu partycjach możesz zwiększyć szybkość reakcji przepływność i klienta, za pomocą asynchronicznego lub równoległego zapytań.
Na przykład Niewykluczone, że wystąpienia roli co najmniej dwóch procesu roboczego uzyskiwania dostępu do tabel równolegle. Możesz używać ról procesów roboczych indywidualnych, które odpowiada za określonych zestawów partycji lub po prostu ma wiele wystąpienia roli procesu roboczego, każdy mogli korzystać z wszystkich partycji w tabeli.  

W ramach wystąpienie klienta może zwiększyć przepływność, wykonując operacje magazynu asynchronicznie. Biblioteki klienta usługi Storage ułatwia pisanie zapytania asynchroniczne i modyfikacji. Na przykład możesz zacząć od metody synchronicznej, który pobiera wszystkie jednostki w partycji, jak pokazano w poniższym kodzie języka C#:  

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

Można łatwo modyfikować ten kod, tak aby zapytanie jest uruchamiane asynchronicznie, w następujący sposób:  

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

* Podpis metody zawiera teraz **async** modyfikator i zwraca **zadań** wystąpienia.  
* Zamiast wywoływać metodę **ExecuteSegmented** wywołuje metodę, aby pobrać wyniki, metoda teraz **ExecuteSegmentedAsync** metody i używa **await** modyfikatora Pobierz wyniki asynchronicznie.  

Aplikacja kliencka tę metodę można wywołać wiele razy (z różnymi wartościami dla **działu** parametru), a każda kwerenda zostanie uruchomiony w oddzielnym wątku.  

Nie ma wersji asynchronicznej z **Execute** method in Class metoda **TableQuery** klasy, ponieważ **IEnumerable** interfejs nie obsługuje asynchroniczne wyliczenia.  

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

Można łatwo modyfikować ten kod, tak aby aktualizacja jest uruchamiane asynchronicznie, w następujący sposób:  

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

* Podpis metody zawiera teraz **async** modyfikator i zwraca **zadań** wystąpienia.  
* Zamiast wywoływać metodę **Execute** wywołuje metodę, aby zaktualizować jednostki, metoda teraz **ExecuteAsync** metody i używa **await** modyfikator do pobierania wyników asynchronicznie.  

Aplikacja kliencka można wywołać wiele metod asynchronicznych, takich jak ta, a następnie uruchomi każdego wywołania metody w oddzielnym wątku.  

### <a name="credits"></a>Napisy końcowe
Chcielibyśmy podziękować następujących członków zespołu platformy Azure dla swojego wkładu: Dominic Betts, firma Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, i vamshidhar Kommineni, Vinay Shah i Serdar Ozler także Tom Hollander z DX firmy Microsoft. 

Chcielibyśmy także podziękować następujące MVPs firmy Microsoft dla swoich opinii podczas cykli przeglądu: Papirov Igora i Edward Bakker.

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

