---
title: Projektowanie tabel usługi Azure Cosmos DB w celu skalowania i wydajności
description: 'Przewodnik po projektowaniu usługi Azure Table Storage: Skalowalne i wydajne tabele w usłudze Azure Cosmos DB i magazynie tabel platformy Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246476"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Przewodnik po projektowaniu tabel magazynu tabel platformy Azure: tabele skalowalne i wydajne

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę różne czynniki, w tym koszt. Jeśli posiadasz doświadczenie w projektowaniu schematów na potrzeby relacyjnych baz danych, te zagadnienia będą Ci znane. Mimo że istnieją pewne podobieństwa między usługą Azure Table Storage i modelami relacyjnymi, istnieje również wiele istotnych różnic. Te różnice prowadzą zwykle do różnych projektów, które mogą wyglądać na nieintuicyjne lub błędne dla kogoś, kto ma doświadczenie z relacyjnymi bazami danych, ale mają sens, jeśli projektowanie odbywa się na potrzeby magazynu klucz/wartość bazy danych NoSQL, na przykład usługi Table Storage.

Magazyn tabel jest przeznaczony do obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek ("wiersze" w terminologii relacyjnej bazy danych) danych lub dla zestawów danych, które muszą obsługiwać duże wolumeny transakcji. W związku z tym należy myśleć inaczej o tym, jak przechowywać dane i zrozumieć, jak działa magazyn tabel. Dobrze zaprojektowany magazyn danych NoSQL może umożliwić rozwiązanie skalowanie znacznie dalej (i po niższych kosztach) niż rozwiązanie, które używa relacyjnej bazy danych. Ten przewodnik pomaga w tych tematach.  

## <a name="about-azure-table-storage"></a>Informacje o magazynie tabel platformy Azure
W tej sekcji przedstawiono niektóre z kluczowych funkcji magazynu tabel, które są szczególnie istotne dla projektowania pod kątem wydajności i skalowalności. Jeśli jesteś nowym użytkownikiem usługi Azure Storage i magazynu tabel, zobacz [Wprowadzenie do usługi Microsoft Azure Storage](../storage/common/storage-introduction.md) i Wprowadzenie do magazynu tabel platformy Azure przy użyciu platformy [.NET](table-storage-how-to-use-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Chociaż w tym przewodniku znajduje się magazyn tabel, zawiera on niektóre dyskusje na temat usługi Azure Queue storage i magazynu obiektów Blob platformy Azure oraz sposobu ich używania wraz z magazynem tabel w rozwiązaniu.  

Magazyn tabel używa formatu tabelaryczne do przechowywania danych. W terminologii standardowej każdy wiersz tabeli reprezentuje jednostkę, a kolumny przechowują różne właściwości tej jednostki. Każda encja ma parę kluczy, aby jednoznacznie ją zidentyfikować, oraz kolumnę sygnatury czasowej używaną do śledzenia podczas ostatniej aktualizacji jednostki. Pole sygnatury czasowej jest dodawane automatycznie i nie można ręcznie zastąpić sygnatury czasowej dowolną wartością. Magazyn tabeli używa tej ostatnio zmodyfikowanej sygnatury czasowej (LMT) do zarządzania optymistyczną współbieżnością.  

> [!NOTE]
> Operacje interfejsu API REST `ETag` magazynu tabel zwraca również wartość, która pochodzi z LMT. W tym dokumencie terminy ETag i LMT są używane zamiennie, ponieważ odnoszą się do tych samych danych źródłowych.  
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


Do tej pory ten projekt wygląda podobnie do tabeli w relacyjnej bazie danych. Kluczowe różnice to kolumny obowiązkowe i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda z właściwości zdefiniowanych przez użytkownika, takich jak **FirstName** lub **Age**, ma typ danych, taki jak liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. W przeciwieństwie do relacyjnej bazy danych, jednak bez schematu charakter magazynu tabel oznacza, że właściwość nie musi mieć tego samego typu danych dla każdej jednostki. Aby przechowywać złożone typy danych w jednej właściwości, należy użyć formatu szeregowego, takiego jak JSON lub XML. Aby uzyskać więcej informacji, zobacz [Opis modelu danych magazynu tabel](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Twój wybór `PartitionKey` `RowKey` i ma fundamentalne znaczenie dla dobrego projektu stołu. Każda jednostka przechowywana w tabeli `PartitionKey` musi `RowKey`mieć unikatową kombinację i . Podobnie jak w przypadku kluczy `PartitionKey` w `RowKey` tabeli relacyjnej bazy danych, wartości i wartości są indeksowane w celu utworzenia indeksu klastrowanego, który umożliwia szybkie wyszukiwania. Magazyn tabel nie tworzy jednak żadnych indeksów pomocniczych, więc są to tylko dwie właściwości indeksowane (niektóre wzorce opisane później pokazują, jak można obejść to pozorne ograniczenie).  

Tabela składa się z jednej lub więcej partycji, a wiele z podejmowanych `PartitionKey` `RowKey` decyzji projektowych będzie wokół wyboru odpowiedniego i zoptymalizować rozwiązanie. Rozwiązanie może składać się tylko z jednej tabeli, która zawiera wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie ma wiele tabel. Tabele ułatwiają logiczne organizowanie encji i ułatwiają zarządzanie dostępem do danych przy użyciu list kontroli dostępu. Można upuścić całą tabelę przy użyciu jednej operacji magazynu.  

### <a name="table-partitions"></a>Partycje tabeli
Nazwa konta, nazwa tabeli i `PartitionKey` razem identyfikują partycję w usłudze magazynowania, w której magazyn tabel przechowuje jednostkę. Partycje nie tylko są częścią schematu adresowania dla jednostek, ale także definiują zakres transakcji (zobacz sekcję w dalszej części tego artykułu Transakcje [grupy jednostek)](#entity-group-transactions)i stanowią podstawę skalowania magazynu tabel. Aby uzyskać więcej informacji na temat partycji tabel, zobacz [Lista kontrolna wydajności i skalowalności magazynu tabel](../storage/tables/storage-performance-checklist.md).  

W magazynie tabel poszczególnych węzłów usługi co najmniej jedną pełną partycję, a usługa skaluje się dynamicznie równoważąc partycje między węzłami. Jeśli węzeł jest pod obciążeniem, magazyn tabel może podzielić zakres partycji obsługiwanych przez ten węzeł na różne węzły. Gdy ruch ustępuje, magazyn tabel może scalić zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji na temat wewnętrznych szczegółów magazynu tabel, a w szczególności sposobu zarządzania partycjami, zobacz [Microsoft Azure Storage: Usługa magazynu w chmurze o wysokiej dostępności z dużą spójnością.](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

### <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W magazynie tabel transakcje grupy jednostek (EGTs) są jedynym wbudowanym mechanizmem wykonywania aktualizacji niepodzielnych w wielu jednostkach. EGT są również określane jako *transakcje wsadowe*. EGTs mogą działać tylko na jednostkach przechowywanych w tej samej partycji (udostępnianie tego samego klucza partycji w określonej tabeli), więc w każdej chwili potrzebujesz niepodzielnego zachowania transakcyjnego w wielu jednostkach, upewnij się, że te jednostki znajdują się w tej samej partycji. Jest to często powodem przechowywania wielu typów jednostek w tej samej tabeli (i partycji) i nie przy użyciu wielu tabel dla różnych typów jednostek. Pojedynczy EUWT może działać na co najwyżej 100 podmiotach.  Jeśli prześlesz wiele równoczesnych EGT do przetwarzania, ważne jest, aby upewnić się, że te EGTs nie działają na podmioty, które są wspólne dla EGTs. W przeciwnym razie istnieje ryzyko opóźnienia przetwarzania.

EGTs również wprowadzić potencjalny kompromis do oceny w projekcie. Użycie większej liczby partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości równoważenia obciążenia żądań między węzłami. Może to jednak ograniczyć możliwość wykonywania transakcji niepodzielnych przez aplikację i utrzymywania silnej spójności danych. Ponadto istnieją określone cele skalowalności na poziomie partycji, które mogą ograniczać przepływność transakcji, których można oczekiwać dla pojedynczego węzła.

Aby uzyskać więcej informacji na temat celów skalowalności dla kont magazynu platformy Azure, zobacz [Cele skalowalności dla standardowych kont magazynu](../storage/common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji na temat celów skalowalności magazynu tabel, zobacz [Cele skalowalności i wydajności magazynu tabel](../storage/tables/scalability-targets.md). W późniejszych sekcjach tego przewodnika omówiono różne strategie projektowania, które ułatwiają zarządzanie kompromisami, takimi jak ten, i omówić sposób najlepszego wyboru klucza partycji na podstawie określonych wymagań aplikacji klienckiej.  

### <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności
Poniższa tabela zawiera niektóre z kluczowych wartości, o których należy pamiętać podczas projektowania rozwiązania magazynu tabel:  

| Całkowita pojemność konta magazynu platformy Azure | 500 TB |
| --- | --- |
| Liczba tabel na koncie magazynu platformy Azure |Ograniczone tylko pojemnością konta magazynu. |
| Liczba partycji w tabeli |Ograniczone tylko pojemnością konta magazynu. |
| Liczba jednostek w partycji |Ograniczone tylko pojemnością konta magazynu. |
| Wielkość pojedynczego podmiotu |Do 1 MB, z maksymalnie 255 właściwościami `PartitionKey` `RowKey`(w `Timestamp`tym , , i ). |
| Wielkość`PartitionKey` |Ciąg o rozmiarze do 1 KB. |
| Wielkość`RowKey` |Ciąg o rozmiarze do 1 KB. |
| Rozmiar transakcji grupy jednostek |Transakcja może zawierać co najwyżej 100 jednostek, a ładunek musi mieć rozmiar mniejszy niż 4 MB. EUWT może zaktualizować jednostkę tylko raz. |

Aby uzyskać więcej informacji, zobacz [Opis modelu danych usługi tabeli](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogi, ale należy uwzględnić szacunki kosztów zarówno użycia pojemności, jak i ilości transakcji w ramach oceny dowolnego rozwiązania, które używa magazynu tabel. W wielu scenariuszach jednak przechowywanie zdenormalizowane lub zduplikowane dane w celu zwiększenia wydajności lub skalowalności rozwiązania jest prawidłowym podejściem do podjęcia. Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel
Te listy zawierają podsumowanie niektórych kluczowych wskazówek, o których należy pamiętać podczas projektowania tabel. Ten przewodnik dotyczy ich wszystkich bardziej szczegółowo później. Te wskazówki różnią się od wytycznych, które zazwyczaj należy wykonać dla relacyjnego projektowania bazy danych.  

Projektowanie magazynu tabel, aby można *było odczytać* wydajne:

* **Projektowanie do wykonywania zapytań w aplikacjach intensywnie odczytu.** Podczas projektowania tabel, pomyśl o zapytaniach (zwłaszcza te wrażliwe na opóźnienia) będziesz działać, zanim zastanowisz się, jak będziesz aktualizować swoje jednostki. Zazwyczaj skutkuje to wydajnym i wydajnym rozwiązaniem.  
* **Określ `PartitionKey` `RowKey` zarówno w kwerendach.** *Zapytania punktowe,* takie jak te, są najbardziej wydajnymi zapytaniami magazynu tabel.  
* **Należy rozważyć przechowywanie zduplikowanych kopii jednostek.** Magazyn tabel jest tani, więc należy rozważyć przechowywanie tej samej jednostki wiele razy (z różnymi kluczami), aby włączyć bardziej wydajne zapytania.  
* **Należy wziąć pod uwagę denormalizing danych.** Przechowywanie tabel jest tanie, więc należy rozważyć denormalizing danych. Na przykład należy przechowywać jednostki podsumowania, tak aby kwerendy dotyczące danych agregujących muszą mieć dostęp tylko do jednej jednostki.  
* **Użyj złożonych wartości klucza.** Jedyne klucze, `PartitionKey` które `RowKey`masz, to i . Na przykład użyj złożonych wartości klucza, aby włączyć alternatywne ścieżki dostępu z kluczem do jednostek.  
* **Użyj projekcji kwerendy.** Można zmniejszyć ilość danych przesyłanych przez sieć za pomocą kwerend, które wybierają tylko pola, których potrzebujesz.  

Projektowanie magazynu tabel, aby był wydajny *do zapisu:*  

* **Nie należy tworzyć gorących partycji.** Wybierz klawisze, które umożliwiają rozłożenie żądań na wiele partycji w dowolnym momencie.  
* **Unikaj skoków w ruchu.** Rozpowszechniaj ruch w rozsądnym czasie i unikaj skoków w ruchu.
* **Nie koniecznie należy utworzyć oddzielną tabelę dla każdego typu encji.** Jeśli potrzebujesz transakcji niepodzielnych między typami jednostek, można przechowywać te typy wielu jednostek w tej samej partycji w tej samej tabeli.
* **Należy wziąć pod uwagę maksymalną przepływność, którą należy osiągnąć.** Musisz znać cele skalowalności dla magazynu tabel i upewnij się, że projekt nie spowoduje ich przekroczenia.  

W dalszej części tego przewodnika zobaczysz przykłady, które wcieli wszystkie te zasady w życie.  

## <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Przechowywanie tabel może być odczytywane intensywnie, intensywnie pisać lub mieszać te dwa. W tej sekcji rozważa projektowania do obsługi operacji odczytu skutecznie. Zazwyczaj projekt, który obsługuje operacje odczytu wydajnie jest również wydajne dla operacji zapisu. Istnieją jednak dodatkowe zagadnienia podczas projektowania do obsługi operacji zapisu. Zostały one omówione w następnej sekcji [Projektowanie do modyfikacji danych](#design-for-data-modification).

Dobrym punktem wyjścia umożliwiającym efektywne odczytywanie danych jest pytanie "Jakie zapytania będzie musiała uruchomić moja aplikacja, aby pobrać potrzebne dane?"  

> [!NOTE]
> W magazynie tabel ważne jest, aby projekt był poprawny z góry, ponieważ późniejsza jego zmiana jest trudna i kosztowna. Na przykład w relacyjnej bazie danych często można rozwiązać problemy z wydajnością, po prostu dodając indeksy do istniejącej bazy danych. Nie jest to opcja z magazynem tabel.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Jak wybór `PartitionKey` i `RowKey` wpływa na wydajność zapytań
Poniższe przykłady zakładają, że magazyn tabel przechowuje jednostki pracowników o `Timestamp` następującej strukturze (większość przykładów pomija właściwość dla jasności):  

| Nazwa kolumny | Typ danych |
| --- | --- |
| `PartitionKey`(Nazwa działu) |Ciąg |
| `RowKey`(Identyfikator pracownika) |Ciąg |
| `FirstName` |Ciąg |
| `LastName` |Ciąg |
| `Age` |Liczba całkowita |
| `EmailAddress` |Ciąg |

Oto kilka ogólnych wskazówek dotyczących projektowania zapytań dotyczących magazynu tabel. Składnia filtru używana w poniższych przykładach pochodzi z interfejsu API REST magazynu tabel. Aby uzyskać więcej informacji, zobacz [Encje kwerendy](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* *Kwerenda punktowa* jest najbardziej efektywnym wyszukiwaniem w użyciu i jest zalecana w przypadku wyszukiwań o dużej objętości lub wyszukiwań wymagających najniższego opóźnienia. Takie zapytanie można użyć indeksów, aby skutecznie zlokalizować `PartitionKey` poszczególne `RowKey` jednostki, określając zarówno wartości, jak i wartości. Na przykład: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Drugim najlepszym jest *zapytanie zakresowe*. Używa `PartitionKey`, i filtruje na zakres `RowKey` wartości, aby zwrócić więcej niż jedną jednostkę. Wartość `PartitionKey` identyfikuje określoną partycję, `RowKey` a wartości identyfikują podzbiór jednostek w tej partycji. Na przykład: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Trzeci najlepszy jest *skanowanie partycji*. Używa `PartitionKey`, i filtry na innej właściwości nie klucz i może zwrócić więcej niż jedną jednostkę. Wartość `PartitionKey` identyfikuje określoną partycję, a wartości właściwości wybrać dla podzbioru jednostek w tej partycji. Na przykład: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Skanowanie tabeli* nie zawiera `PartitionKey`programu , i jest nieefektywne, ponieważ przeszukuje wszystkie partycje, które tworzą tabelę dla wszystkich pasujących jednostek. Wykonuje skanowanie tabeli niezależnie od tego, czy filtr `RowKey`używa pliku . Na przykład: `$filter=LastName eq 'Jones'`.  
* Kwerendy magazynu tabel platformy Azure, które `PartitionKey` `RowKey` zwracają wiele jednostek sortować je w i porządku. Aby uniknąć uciekania jednostek w kliencie, `RowKey` wybierz, który definiuje najbardziej typowe kolejność sortowania. Wyniki kwerendy zwracane przez interfejs API tabeli platformy Azure w usłudze Azure Cosmos DB nie są sortowane według klucza partycji ani klucza wiersza. Aby uzyskać szczegółową listę różnic funkcji, zobacz [różnice między interfejsem API tabel w usłudze Azure Cosmos DB i magazynie tabel platformy Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Za pomocą "**lub**" do `RowKey` określenia filtru na podstawie wartości powoduje skanowanie partycji i nie jest traktowany jako kwerenda zakres. W związku z tym należy unikać zapytań, które używają filtrów, takich jak: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Przykłady kodu po stronie klienta, które używają biblioteki klienta magazynu do uruchamiania wydajnych zapytań, zobacz:  

* [Uruchamianie kwerendy punktowej przy użyciu biblioteki klienta magazynu](#run-a-point-query-by-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu funkcji LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projekcja po stronie serwera](#server-side-projection)  

Przykłady kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli, zobacz:  

* [Praca z heterogenizami typów jednostek](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Wybierz odpowiedni`PartitionKey`
Wybór `PartitionKey` należy zrównoważyć potrzebę umożliwienia użycia EGTs (w celu zapewnienia spójności) z wymogu dystrybucji jednostek na wielu partycjach (w celu zapewnienia skalowalnego rozwiązania).  

W jednej skrajności można przechowywać wszystkie jednostki w jednej partycji. Może to jednak ograniczyć skalowalność rozwiązania i uniemożliwić magazyn tabel jest w stanie równoważyć żądania. Z drugiej strony można przechowywać jedną jednostkę na partycję. Jest to wysoce skalowalne i umożliwia magazyn tabel do żądania równoważenia obciążenia, ale uniemożliwia korzystanie z transakcji grupy jednostek.  

Ideał `PartitionKey` umożliwia korzystanie z wydajnych zapytań i ma wystarczające partycje, aby upewnić się, że rozwiązanie jest skalowalne. Zazwyczaj okaże się, że jednostki będą miały odpowiednią właściwość, która dystrybuuje jednostki na wystarczające partycje.

> [!NOTE]
> Na przykład w systemie, który przechowuje informacje `UserID` o użytkownikach lub pracownikach, może być dobrym `PartitionKey`. Może być kilka jednostek, `UserID` które używają określonego jako klucz partycji. Każda jednostka przechowująca dane o użytkowniku jest pogrupowana w jedną partycję. Podmioty te są dostępne za pośrednictwem EGT, a jednocześnie są wysoce skalowalne.
> 
> 

Istnieją dodatkowe zagadnienia w `PartitionKey` wyborze, które odnoszą się do sposobu wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz [Projektowanie modyfikacji danych](#design-for-data-modification) w dalszej części tego artykułu.  

### <a name="optimize-queries-for-table-storage"></a>Optymalizuj zapytania pod kątem przechowywania tabel
Magazyn tabel automatycznie indeksuje encje `PartitionKey` przy `RowKey` użyciu i wartości w jednym indeksie klastrowanym. Jest to powód, że zapytania punktowe są najbardziej wydajne w użyciu. Jednak nie ma żadnych indeksów innych niż w `PartitionKey` indeksie klastrowanym na i `RowKey`.

Wiele projektów musi spełniać wymagania, aby umożliwić wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład lokalizowanie jednostek pracowników na podstawie wiadomości e-mail, identyfikatora pracownika lub nazwiska. Następujące wzorce w sekcji [Wzorce projektowania tabeli](#table-design-patterns) odnoszą się do tych typów wymagań. Wzorce opisują również sposoby pracy wokół faktu, że magazyn tabel nie zapewnia indeksów pomocniczych.  

* [Wewnątrzpoziomowy wzorzec indeksu pomocniczego:](#intra-partition-secondary-index-pattern)Przechowuj `RowKey` wiele kopii każdej jednostki przy użyciu różnych wartości (w tej samej partycji). Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec indeksu pomocniczego między partycjami:](#inter-partition-secondary-index-pattern)Przechowuj `RowKey` wiele kopii każdej jednostki przy użyciu różnych wartości w oddzielnych partycjach lub w oddzielnych tabelach. Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec jednostek indeksu](#index-entities-pattern): Obsługa jednostek indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

### <a name="sort-data-in-table-storage"></a>Sortowanie danych w magazynie tabel

Magazyn tabel zwraca wyniki kwerendy posortowane w kolejności rosnącej, na `PartitionKey` podstawie programu `RowKey`.

> [!NOTE]
> Wyniki kwerendy zwracane przez interfejs API tabeli platformy Azure w usłudze Azure Cosmos DB nie są sortowane według klucza partycji ani klucza wiersza. Aby uzyskać szczegółową listę różnic funkcji, zobacz [różnice między interfejsem API tabel w usłudze Azure Cosmos DB i magazynie tabel platformy Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Klucze w magazynie tabel są wartościami ciągu. Aby upewnić się, że wartości liczbowe są sortowane poprawnie, należy przekonwertować je na stałą długość i zasypać zerami. Jeśli na przykład wartość identyfikatora pracownika `RowKey` używana jako wartość całkowita jest wartością całkowitą, należy przekonwertować identyfikator pracownika **123** na **00000123**. 

Wiele aplikacji ma wymagania dotyczące używania danych posortowanych w różnych zamówieniach: na przykład sortowania pracowników według nazwy lub przez datę przystąpienia. Następujące wzorce w sekcji [Wzorce projektowania tabeli](#table-design-patterns) dotyczą sposobu alternatywnego sortowania zamówień dla encji:  

* [Wewnątrzpoziomowy wzorzec indeksu pomocniczego:](#intra-partition-secondary-index-pattern)Przechowuj `RowKey` wiele kopii każdej jednostki przy użyciu różnych wartości (w tej samej partycji). Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec indeksu pomocniczego między partycjami:](#inter-partition-secondary-index-pattern)Przechowuj `RowKey` wiele kopii każdej jednostki przy użyciu różnych wartości w oddzielnych partycjach w oddzielnych tabelach. Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.
* [Wzorzec ogona dziennika:](#log-tail-pattern)Pobierz *n* jednostek ostatnio dodane `RowKey` do partycji, przy użyciu wartości, która sortuje w odwrotnej kolejności daty i godziny.  

## <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
W tej sekcji koncentruje się na zagadnienia projektowe do optymalizacji wstawia, aktualizacje i usuwa. W niektórych przypadkach należy ocenić kompromis między projektami, które optymalizują kwerendy względem projektów, które optymalizują pod kątem modyfikacji danych. Ta ocena jest podobna do tego, co robisz w projektach dla relacyjnych baz danych (chociaż techniki zarządzania kompromisami projektowymi różnią się w relacyjnej bazie danych). W sekcji [Wzorce projektowania tabeli](#table-design-patterns) opisano niektóre szczegółowe wzorce projektowe dla magazynu tabel i wyróżniają niektóre z tych kompromisów. W praktyce okaże się, że wiele projektów zoptymalizowanych pod kątem wykonywania zapytań jednostek również działa dobrze do modyfikowania jednostek.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizacja wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć encję, musisz mieć możliwość `PartitionKey` `RowKey` jej zidentyfikowania przy użyciu wartości i wartości. W związku z tym `PartitionKey` `RowKey` wybór i do modyfikowania jednostek powinny być zgodne z kryteriami podobne do wyboru do obsługi zapytań punktowych. Chcesz zidentyfikować jednostki tak wydajnie, jak to możliwe. Nie chcesz używać nieefektywnej partycji lub skanowania tabeli, aby zlokalizować `PartitionKey` `RowKey` jednostkę w celu odnajdowania wartości i wartości, które należy zaktualizować lub usunąć.  

Następujące wzorce w sekcji [Wzorce projektowania tabeli](#table-design-patterns) są adres optymalizacji wydajności operacji wstawiania, aktualizowania i usuwania:  

* [Wzorzec usuwania dużej ilości:](#high-volume-delete-pattern)Włącz usunięcie dużej liczby jednostek, przechowując wszystkie jednostki do jednoczesnego usunięcia w ich osobnej tabeli. Jednostki można usunąć, usuwając tabelę.  
* [Wzorzec serii danych:](#data-series-pattern)Przechowuj pełną serię danych w jednej jednostce, aby zminimalizować liczbę żądań.  
* [Szeroki wzorzec elementów:](#wide-entities-pattern)Użyj wielu jednostek fizycznych do przechowywania elementów logicznych z więcej niż 252 właściwości.  
* [Wzorzec dużych elementów:](#large-entities-pattern)Użyj magazynu obiektów blob do przechowywania dużych wartości właściwości.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnij spójność przechowywanych jednostek
Innym kluczowym czynnikiem, który wpływa na wybór kluczy do optymalizacji modyfikacji danych jest zapewnienie spójności przy użyciu transakcji niepodzielnych. EGT można używać tylko do działania na jednostkach przechowywanych w tej samej partycji.  

Następujące wzorce w sekcji [Wzorce projektowania tabeli](#table-design-patterns) dotyczą zarządzania spójnością:  

* [Wewnątrzpoziomowy wzorzec indeksu pomocniczego:](#intra-partition-secondary-index-pattern)Przechowuj `RowKey` wiele kopii każdej jednostki przy użyciu różnych wartości (w tej samej partycji). Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec indeksu pomocniczego między partycjami:](#inter-partition-secondary-index-pattern)Przechowuj `RowKey` wiele kopii każdej jednostki przy użyciu różnych wartości w oddzielnych partycjach lub w oddzielnych tabelach. Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Ostatecznie spójny wzorzec transakcji:](#eventually-consistent-transactions-pattern)Włącz ostatecznie spójne zachowanie w granicach partycji lub granice systemu magazynu przy użyciu kolejek platformy Azure.
* [Wzorzec jednostek indeksu](#index-entities-pattern): Obsługa jednostek indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  
* [Wzorzec denormalizacji](#denormalization-pattern): Połącz powiązane dane w jednej jednostce, aby umożliwić pobranie wszystkich potrzebnych danych za pomocą kwerendy punktowej.  
* [Wzorzec serii danych:](#data-series-pattern)Przechowuj kompletne serie danych w jednej jednostce, aby zminimalizować liczbę żądań.  

Aby uzyskać więcej informacji, zobacz [transakcje grupy jednostek](#entity-group-transactions) w dalszej części tego artykułu.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zadbaj o to, by twój projekt zapewniał wydajne modyfikacje, ułatwiające efektywne zapytania
W wielu przypadkach projekt efektywnego wykonywania zapytań powoduje wydajne modyfikacje, ale zawsze należy ocenić, czy jest to przypadek dla określonego scenariusza. Niektóre wzorce w sekcji [Wzorce projektowania tabel jawnie](#table-design-patterns) oceniają kompromisy między jednostkami kwerendy i modyfikujące i zawsze należy wziąć pod uwagę liczbę każdego typu operacji.  

Następujące wzorce w sekcji [Wzorce projektowania tabeli](#table-design-patterns) dotyczą kompromisów między projektowaniem w celu efektywnego poszukiwania i projektowaniem w celu wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](#compound-key-pattern): Użyj wartości złożonych, `RowKey` aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą kwerendy z pojedynczym punktem.  
* [Wzorzec ogona dziennika:](#log-tail-pattern)Pobierz *n* jednostek ostatnio dodane `RowKey` do partycji, przy użyciu wartości, która sortuje w odwrotnej kolejności daty i godziny.  

## <a name="encrypt-table-data"></a>Szyfruj dane tabeli
Biblioteka klienta usługi .NET Azure Storage obsługuje szyfrowanie właściwości jednostki ciągu dla operacji wstawiania i zamieniania. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem na ciągi po odszyfrowaniu.    

W przypadku tabel, oprócz zasad szyfrowania, użytkownicy muszą określić właściwości, które mają być szyfrowane. Określ `EncryptProperty` atrybut (dla jednostek POCO, które `TableEntity`pochodzą od ), lub określ program rozpoznawania szyfrowania w opcjach żądań. Program rozpoznawania nazw szyfrowania jest pełnomocnikiem, który przyjmuje klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną, która wskazuje, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania biblioteka klienta używa tych informacji, aby zdecydować, czy właściwość powinna być szyfrowana podczas zapisywania do sieci. Delegat zapewnia również możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, a następnie zaszyfrować właściwość A; w przeciwnym razie szyfrować właściwości A i B.) Nie jest konieczne podanie tych informacji podczas odczytywania lub wykonywania zapytań o jednostki.

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości mógł być wcześniej zaszyfrowany przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonywania dodatkowych wywołań usługi, aby odczytać istniejącą jednostkę z usługi lub przy użyciu nowego klucza na właściwość. Żadna z nich nie jest odpowiednia ze względu na wydajność.     

Aby uzyskać informacje dotyczące szyfrowania danych tabeli, zobacz [Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relacje modelu
Tworzenie modeli domen jest kluczowym krokiem w projektowaniu złożonych systemów. Zazwyczaj proces modelowania służy do identyfikowania jednostek i relacji między nimi, jako sposób na zrozumienie domeny biznesowej i poinformowanie projektu systemu. W tej sekcji skupiono się na tym, jak można przetłumaczyć niektóre typowe typy relacji znalezione w modelach domen na projekty magazynu tabel. Proces mapowania z modelu danych logicznych do fizycznego modelu danych opartego na nosql różni się od procesu używanego podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zazwyczaj zakłada proces normalizacji danych zoptymalizowany pod kątem minimalizacji nadmiarowości. Taki projekt zakłada również deklaratywne możliwości wykonywania zapytań, który abstrakcji implementacji jak działa bazy danych.  

### <a name="one-to-many-relationships"></a>Relacje jeden-do-wielu
Relacje jeden-do-wielu między obiektami domeny biznesowej występują często: na przykład jeden dział ma wielu pracowników. Istnieje kilka sposobów implementacji relacji jeden do wielu w magazynie tabel, każdy z zaletami i wadami, które mogą być istotne dla określonego scenariusza.  

Weźmy przykład dużej międzynarodowej korporacji z dziesiątkami tysięcy działów i podmiotów pracowniczych. Każdy dział ma wielu pracowników, a każdy pracownik jest skojarzony z jednym konkretnym działem. Jednym z podejść jest przechowywanie oddzielnych jednostek działu i pracowników, takich jak:  

![Grafika przedstawiająca jednostkę działu i jednostkę pracownika][1]

W tym przykładzie pokazano niejawną relację jeden `PartitionKey` do wielu między typami, na podstawie wartości. Każdy dział może mieć wielu pracowników.  

W tym przykładzie pokazano również jednostki działu i jego powiązanych jednostek pracowników w tej samej partycji. Można użyć różnych partycji, tabel, a nawet kont magazynu dla różnych typów jednostek.  

Alternatywnym podejściem jest denormalize danych i przechowywać tylko jednostki pracowników z zdezormalizowane dane działu, jak pokazano w poniższym przykładzie. W tym konkretnym scenariuszu to zdenormalizowane podejście może nie być najlepsze, jeśli masz wymóg, aby móc zmienić szczegóły menedżera działu. Aby to zrobić, należy zaktualizować każdego pracownika w dziale.  

![Grafika jednostki pracownika][2]

Aby uzyskać więcej informacji, zobacz [wzorzec denormalizacji](#denormalization-pattern) w dalszej części tego przewodnika.  

W poniższej tabeli podsumowano zalety i minusy każdego z podejść do przechowywania jednostek pracowników i działów, które mają relację jeden do wielu. Należy również wziąć pod uwagę, jak często należy oczekiwać, aby wykonać różne operacje. Może być dopuszczalne, aby mieć projekt, który zawiera kosztowną operację, jeśli ta operacja występuje rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Zalety</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, ta sama partycja, ta sama tabela</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu za pomocą jednej operacji.</li>
<li>Za pomocą EUWT można zachować spójność, jeśli masz obowiązek modyfikowania jednostki działu przy każdej aktualizacji/wstawiania/usuwania jednostki pracownika. Na przykład, jeśli zachowasz liczbę pracowników działów dla każdego działu.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie zarówno pracownika, jak i jednostki działu dla niektórych działań klienta.</li>
<li>Operacje magazynowania odbywa się w tej samej partycji. Przy dużych wolumenach transakcji może to spowodować hotspot.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EUWT.</li>
</ul>
</td>
</tr>
<tr>
<td>Oddzielne typy encji, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu lub jednostkę pracownika za pomocą jednej operacji.</li>
<li>Przy dużych wolumenach transakcji może to pomóc rozłożyć obciążenie na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie zarówno pracownika, jak i jednostki działu dla niektórych działań klienta.</li>
<li>Egt nie można używać EGT do zachowania spójności podczas aktualizowania/wstawiania/usuwania pracownika i aktualizowania działu. Na przykład aktualizowanie liczby pracowników w encji działu.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EUWT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize do pojedynczego typu jednostki</td>
<td>
<ul>
<li>Wszystkie potrzebne informacje można pobrać za pomocą jednego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Utrzymanie spójności może być kosztowne, jeśli konieczne jest zaktualizowanie informacji o dziale (wymagałoby to aktualizacji wszystkich pracowników w dziale).</li>
</ul>
</td>
</tr>
</table>

Jak wybrać jedną z tych opcji i które z zalet i wad są najbardziej znaczące, zależy od konkretnych scenariuszy aplikacji. Na przykład, jak często modyfikujesz jednostki działu? Czy wszystkie zapytania pracowników wymagają dodatkowych informacji o działach? Jak blisko jesteś do limitów skalowalności na partycjach lub koncie magazynu?  

### <a name="one-to-one-relationships"></a>Relacje jeden-do-jednego
Modele domeny mogą zawierać relacje jeden-do-jednego między jednostkami. Jeśli trzeba zaimplementować relację jeden do jednego w magazynie tabel, należy również wybrać sposób łączenia dwóch powiązanych jednostek, gdy trzeba pobrać je zarówno. To łącze może być niejawne, oparte na konwencji w wartościach klucza `PartitionKey` lub `RowKey` jawne, przechowując łącze w postaci i wartości w każdej jednostce do jej jednostki pokrewnej. Aby zapoznać się z kwestią, czy encje pokrewne należy [przechowywać](#one-to-many-relationships)w tej samej partycji, zobacz sekcję Relacje jeden do wielu .  

Istnieją również zagadnienia dotyczące implementacji, które mogą prowadzić do zaimplementowania relacji jeden-do-jednego w magazynie tabel:  

* Obsługa dużych elementów (aby uzyskać więcej informacji, zobacz [Wzorzec dużych elementów).](#large-entities-pattern)  
* Implementowanie kontroli dostępu (aby uzyskać więcej informacji, zobacz [Kontrolowanie dostępu za pomocą sygnatur dostępu współdzielonego).](#control-access-with-shared-access-signatures)  

### <a name="join-in-the-client"></a>Dołącz do klienta
Chociaż istnieją sposoby modelowania relacji w magazynie tabel, nie należy zapominać, że dwa główne powody korzystania z magazynu tabel to skalowalność i wydajność. Jeśli okaże się, że modelujesz wiele relacji, które zagrażają wydajności i skalowalności rozwiązania, należy zadać sobie pytanie, czy jest to konieczne do utworzenia wszystkich relacji danych w projekcie tabeli. Można uprościć projekt i poprawić skalowalność i wydajność rozwiązania, jeśli pozwolisz aplikacji klienckiej wykonać wszelkie niezbędne sprzężenia.  

Na przykład jeśli masz małe tabele, które zawierają dane, które nie zmieniają się często, można pobrać te dane raz i buforować je na kliencie. Może to uniknąć powtarzających się objazdów, aby pobrać te same dane. W przykładach, które przyjrzeliśmy się w tym przewodniku, zestaw działów w małej organizacji może być mały i zmieniać rzadko. To sprawia, że dobrym kandydatem dla danych, które aplikacja kliencka można pobrać raz i pamięci podręcznej jako dane wyszukiwania.  

### <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka używa zestawu klas, które stanowią część relacji dziedziczenia do reprezentowania jednostek biznesowych, można łatwo utrwalić te jednostki w magazynie tabel. Na przykład może mieć następujący zestaw klas zdefiniowanych w `Person` aplikacji klienckiej, gdzie jest klasą abstrakcyjną.

![Diagram relacji dziedziczenia][3]

Można utrwalić wystąpienia dwóch klas betonu `Person` w magazynie tabel przy użyciu jednej tabeli. Użyj encji, które wyglądają następująco:  

![Grafika przedstawiająca jednostkę odbiorcy i jednostkę pracownika][4]

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta, zobacz [Praca z heterogenicznych typów jednostek](#work-with-heterogeneous-entity-types) w dalszej części tego przewodnika. Zawiera przykłady rozpoznawania typu jednostki w kodzie klienta.  

## <a name="table-design-patterns"></a>Wzorce projektowe tabel
W poprzednich sekcjach dowiesz się, jak zoptymalizować projekt tabeli zarówno pod kątem pobierania danych jednostki przy użyciu kwerend, jak i wstawiania, aktualizowania i usuwania danych encji. W tej sekcji opisano niektóre wzorce odpowiednie do użycia z magazynem tabel. Ponadto zobaczysz, jak praktycznie rozwiązać niektóre problemy i kompromisy podniesione wcześniej w tym przewodniku. Na poniższym diagramie podsumowano relacje między różnymi wzorami:  

![Diagram wzorów projektowania tabeli][5]

Mapa wzorca wyróżnia niektóre relacje między wzorami (niebieskimi) i anty-wzorkami (pomarańczowymi), które są udokumentowane w tym przewodniku. Istnieje oczywiście wiele innych wzorców, które są warte rozważenia. Na przykład jednym z kluczowych scenariuszy dla magazynu tabel jest użycie [zmaterializowanego wzorca widoku](https://msdn.microsoft.com/library/azure/dn589782.aspx) z wzorca [segregacji odpowiedzialność kwerendy polecenia.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego wewnątrz partycji
Przechowuj wiele kopii każdej jednostki `RowKey` przy użyciu różnych wartości (w tej samej partycji). Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości. Aktualizacje między kopiami mogą być spójne przy użyciu EGTs.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki `PartitionKey` `RowKey` przy użyciu i wartości. Dzięki temu aplikacja kliencka do pobierania jednostki wydajnie przy użyciu tych wartości. Na przykład za pomocą następującej struktury tabeli aplikacja kliencka może użyć kwerendy punktowej do pobierania `PartitionKey` jednostki indywidualnego pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości i `RowKey` wartości). Klient może również pobierać jednostki posortowane według identyfikatora pracownika w każdym dziale.

![Grafika jednostki pracownika][6]

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, należy użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Jest to spowodowane magazyn tabel nie zapewnia indeksów pomocniczych. Ponadto nie ma opcji żądania listy pracowników posortowanych w innej `RowKey` kolejności niż kolejność.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki, przy czym każda kopia przy użyciu innej `RowKey` wartości. Jeśli przechowujesz jednostkę z następującymi strukturami, można skutecznie pobierać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksu dla `RowKey`programu , `empid_`i `email_` umożliwiają wykonywanie zapytań o jednego pracownika lub szereg pracowników przy użyciu zakresu adresów e-mail lub identyfikatorów pracowników.  

![Grafika przedstawiająca encję pracownika z różnymi wartościami Klawisza Wiersza][7]

Następujące dwa kryteria filtrowania (jedno wyszukuje identyfikator pracownika, a drugie wyszukuje według adresu e-mail) określa zapytania punktowe:  

* $filter=(PartitionKey eq 'Sales') i (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') i (RowKey eq 'email_jonesj@contoso.com')  

W przypadku kwerendy dla zakresu encji pracowników można określić zakres posortowany w kolejności identyfikatora pracownika lub zakres posortowany w kolejności adresów e-mail. Zapytanie dla jednostek z odpowiednim `RowKey`prefiksem w pliku .  

* Aby znaleźć wszystkich pracowników w dziale sprzedaży z identyfikatorem pracownika w zakresie od 000100 do 000199, należy użyć: $filter=(PartitionKey eq 'Sales') i (RowKey ge 'empid_000100') i (RowKey le 'empid_000199')  
* Aby znaleźć wszystkich pracowników działu sprzedaży z adresem e-mail zaczyna ł się od litery "a", należy użyć: $filter=(PartitionKey eq 'Sales') i (RowKey ge 'email_a') i (RowKey lt 'email_b')  
  
Składnia filtru używana w poprzednich przykładach pochodzi z interfejsu API REST magazynu tabel. Aby uzyskać więcej informacji, zobacz [Encje kwerendy](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tani w użyciu, więc koszty związane z przechowywaniem zduplikowanych danych nie powinny być głównym problemem. Jednak zawsze należy ocenić koszt projektu na podstawie wymagań przewidywanego magazynu i tylko dodać zduplikowane jednostki do obsługi zapytań, które aplikacja kliencka będzie uruchamiana.  
* Ponieważ jednostki indeksu pomocniczego są przechowywane w tej samej partycji co oryginalne jednostki, upewnij się, że nie przekraczasz docelowych skalowalności dla pojedynczej partycji.  
* Zduplikowane jednostki można zachować spójne ze sobą przy użyciu EGT do aktualizacji dwóch kopii jednostki niepodzielnie. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz [Używanie transakcji grupy encji](#entity-group-transactions).  
* Wartość używana dla `RowKey` musi być unikatowa dla każdej jednostki. Należy rozważyć użycie złożonych wartości klucza.  
* Dopełnienie wartości liczbowych w `RowKey` (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie na podstawie górnej i dolnej granicy.  
* Nie musisz koniecznie duplikować wszystkich właściwości jednostki. Na przykład jeśli kwerendy, które wyszukują jednostki `RowKey` przy użyciu adresu e-mail w nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:

  ![Grafika jednostki pracownika][8]

* Zazwyczaj lepiej jest przechowywać zduplikowane dane i upewnić się, że można pobrać wszystkie dane potrzebne za pomocą jednej kwerendy, niż użyć jednego zapytania, aby zlokalizować jednostkę, a drugą do wyszukynia wymaganych danych.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy:

- Aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy.
- Klient musi pobrać jednostki w różnych zamówieniach sortowania.
- Każdą jednostkę można zidentyfikować przy użyciu różnych unikatowych wartości.

Należy jednak upewnić się, że nie przekraczasz limitów skalowalności partycji podczas `RowKey` wykonywania odnośności encji przy użyciu różnych wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern)
* [Wzór klucza złożonego](#compound-key-pattern)
* [Transakcje grupy jednostek](#entity-group-transactions)
* [Praca z heterogenizami typów jednostek](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego między partycjami
Przechowuj wiele kopii każdej jednostki `RowKey` przy użyciu różnych wartości w oddzielnych partycjach lub w oddzielnych tabelach. Umożliwia to szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki `PartitionKey` `RowKey` przy użyciu i wartości. Dzięki temu aplikacja kliencka do pobierania jednostki wydajnie przy użyciu tych wartości. Na przykład za pomocą następującej struktury tabeli aplikacja kliencka może użyć kwerendy punktowej do pobierania `PartitionKey` jednostki indywidualnego pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości i `RowKey` wartości). Klient może również pobierać jednostki posortowane według identyfikatora pracownika w każdym dziale.  

![Grafika jednostki pracownika][9]

Jeśli chcesz również mieć możliwość znalezienia jednostki pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, należy użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Jest to spowodowane magazyn tabel nie zapewnia indeksów pomocniczych. Ponadto nie ma opcji żądania listy pracowników posortowanych w innej `RowKey` kolejności niż kolejność.  

Przewidujesz dużą liczbę transakcji względem tych jednostek i chcesz zminimalizować ryzyko ograniczenia szybkości magazynowania tabeli, ograniczając klienta.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej `PartitionKey` jednostki, z każdej kopii przy użyciu różnych i `RowKey` wartości. Jeśli przechowujesz jednostkę z następującymi strukturami, można skutecznie pobierać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksu dla `PartitionKey`programu , `empid_`i `email_` umożliwiają określenie, który indeks ma być używany dla kwerendy.  

![Grafika przedstawiająca jednostkę pracownika z indeksem podstawowym i jednostką pracownika z indeksem pomocniczym][10]

Następujące dwa kryteria filtrowania (jedno wyszukuje identyfikator pracownika, a drugie wyszukuje według adresu e-mail) określa zapytania punktowe:  

* $filter=(PartitionKey eq 'empid_Sales') i (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') i (RowKey eqjonesj@contoso.com' )  

W przypadku kwerendy dla zakresu encji pracowników można określić zakres posortowany w kolejności identyfikatora pracownika lub zakres posortowany w kolejności adresów e-mail. Zapytanie dla jednostek z odpowiednim `RowKey`prefiksem w pliku .  

* Aby znaleźć wszystkich pracowników w dziale sprzedaży z identyfikatorem pracownika w zakresie **od 000100** do **000199**, posortowanym w kolejności identyfikatora pracownika, należy użyć: $filter=(PartitionKey eq 'empid_Sales') i (RowKey ge '000100') i (RowKey le '000199')  
* Aby znaleźć wszystkich pracowników działu sprzedaży z adresem e-mail zaczyna nym od "a", posortowanym w kolejności adresu e-mail, należy użyć: $filter=(PartitionKey eq 'email_Sales') oraz (RowKey ge 'a') i (RowKey lt 'b')  

Należy zauważyć, że składnia filtru używana w poprzednich przykładach pochodzi z interfejsu API REST magazynu tabel. Aby uzyskać więcej informacji, zobacz [Encje kwerendy](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Można zachować zduplikowane jednostki ostatecznie zgodne ze sobą przy użyciu [ostatecznie spójne wzorca transakcji](#eventually-consistent-transactions-pattern) do obsługi jednostek indeksu podstawowego i pomocniczego.  
* Magazyn tabel jest stosunkowo tani w użyciu, więc koszty związane z przechowywaniem zduplikowanych danych nie powinny być głównym problemem. Jednak zawsze oceniaj koszt projektu na podstawie oczekiwanych wymagań dotyczących magazynu i dodaj tylko zduplikowane jednostki do obsługi zapytań, które będzie uruchamiana aplikacja kliencka.  
* Wartość używana dla `RowKey` musi być unikatowa dla każdej jednostki. Należy rozważyć użycie złożonych wartości klucza.  
* Dopełnienie wartości liczbowych w `RowKey` (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie na podstawie górnej i dolnej granicy.  
* Nie musisz koniecznie duplikować wszystkich właściwości jednostki. Na przykład jeśli kwerendy, które wyszukują jednostki `RowKey` przy użyciu adresu e-mail w nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:
  
  ![Grafika przedstawiająca jednostkę pracownika z indeksem pomocniczym][11]
* Zazwyczaj lepiej jest przechowywać zduplikowane dane i upewnić się, że można pobrać wszystkie dane potrzebne za pomocą jednej kwerendy, niż użyć jednego zapytania, aby zlokalizować jednostkę przy użyciu indeksu pomocniczego, a drugi do wyszukiwania wymaganych danych w indeksie podstawowym.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy:

- Aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy.
- Klient musi pobrać jednostki w różnych zamówieniach sortowania.
- Każdą jednostkę można zidentyfikować przy użyciu różnych unikatowych wartości.

Ten wzorzec służy, aby uniknąć przekroczenia limitów skalowalności partycji `RowKey` podczas wykonywania odnośności jednostki przy użyciu różnych wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzór klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenizami typów jednostek](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Ostatecznie spójny wzorzec transakcji
Włącz ostatecznie spójne zachowanie w granicach partycji lub granice systemu magazynu przy użyciu kolejek platformy Azure.  

#### <a name="context-and-problem"></a>Kontekst i problem
EGT włączyć transakcje niepodzielne w wielu jednostkach, które współużytkuje ten sam klucz partycji. Ze względu na wydajność i skalowalność można zdecydować się na przechowywanie jednostek, które mają wymagania spójności w oddzielnych partycjach lub w oddzielnym systemie magazynu. W takim scenariuszu nie można użyć EGTs do zachowania spójności. Na przykład może być wymagane, aby zachować spójność ostateczną między:  

* Jednostki przechowywane w dwóch różnych partycjach w tej samej tabeli, w różnych tabelach lub na różnych kontach magazynu.  
* Jednostka przechowywana w magazynie tabel i obiekt blob przechowywany w magazynie obiektów Blob.  
* Jednostka przechowywana w magazynie tabel i plik w systemie plików.  
* Jednostka przechowywana w magazynie tabel, ale indeksowana przy użyciu usługi Azure Cognitive Search.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą kolejek platformy Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną między co najmniej dwie partycje lub systemy magazynu.

Aby zilustrować to podejście, załóżmy, że masz wymóg, aby móc archiwizować byłych jednostek pracowników. Byłye jednostki pracownicze rzadko są poszukiwane i powinny być wyłączone z wszelkich działań, które dotyczą obecnych pracowników. Aby zaimplementować to wymaganie, należy przechowywać aktywnych pracowników w tabeli **Bieżąca** i byłych pracowników w tabeli **Archiwum.** Archiwizacja pracownika wymaga usunięcia encji z **bieżącej** tabeli i dodania encji do tabeli **Archiwum.**

Ale nie można użyć EGT do wykonania tych dwóch operacji. Aby uniknąć ryzyka, że błąd powoduje, że jednostka ma pojawić się w obu lub żadnej z tabel, operacja archiwum musi być ostatecznie spójne. Na poniższym diagramie sekwencji przedstawiono kroki opisane w tej operacji.  

![Diagram rozwiązania dla spójności ostatecznej][12]

Klient inicjuje operację archiwum, umieszczając komunikat w kolejce platformy Azure (w tym przykładzie do archiwizacji #456 pracownika). Rola procesu roboczego sonduje kolejkę dla nowych wiadomości; gdy go znajdzie, odczytuje wiadomość i pozostawia ukrytą kopię w kolejce. Rola procesu roboczego następnie pobiera kopię encji z **bieżącej** tabeli, wstawia kopię do tabeli **Archiwum,** a następnie usuwa oryginał z **bieżącej** tabeli. Na koniec, jeśli nie było żadnych błędów z poprzednich kroków, rola procesu roboczego usuwa ukrytą wiadomość z kolejki.  

W tym przykładzie krok 4 na diagramie wstawia pracownika do tabeli **Archiwum.** Można dodać pracownika do obiektu blob w magazynie obiektów Blob lub pliku w systemie plików.  

#### <a name="recover-from-failures"></a>Odzyskiwanie po awarii
Ważne jest, aby operacje w krokach 4-5 na diagramie były *idempotentne* w przypadku, gdy rola procesu roboczego musi ponownie uruchomić operację archiwum. Jeśli używasz magazynu tabel, w kroku 4 należy użyć operacji "wstaw lub wymień"; w kroku 5 należy użyć operacji "usuń, jeśli istnieje" w bibliotece klienta, której używasz. Jeśli używasz innego systemu magazynu, należy użyć odpowiedniej operacji idempotent.  

Jeśli rola procesu roboczego nigdy nie kończy kroku 6 na diagramie, a następnie, po upływie limitu czasu, komunikat pojawia się ponownie w kolejce gotowy do roli procesu roboczego, aby spróbować ponownie przetworzyć go. Rola procesu roboczego może sprawdzić, ile razy wiadomość w kolejce została odczytana i, jeśli to konieczne, oznaczyć ją jako "truną" wiadomość do zbadania, wysyłając ją do osobnej kolejki. Aby uzyskać więcej informacji na temat odczytywania wiadomości kolejki i sprawdzania liczby dequeue, zobacz [Usuwanie wiadomości](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Niektóre błędy z magazynu tabel i magazynu kolejki są błędy przejściowe, a aplikacja kliencka powinna zawierać odpowiednie logiki ponawiania ich obsługi.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie zapewnia izolacji transakcji. Na przykład klient może odczytać **bieżące** i **archiwum** tabel, gdy rola procesu roboczego był między krokami 4-5 na diagramie i zobacz niespójny widok danych. Dane będą spójne po pewnym czasie.  
* Należy upewnić się, że kroki 4-5 są idempotentne w celu zapewnienia spójności ostatecznej.  
* Rozwiązanie można skalować przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, aby zagwarantować spójność ostateczną między jednostkami, które istnieją w różnych partycjach lub tabelach. Można rozszerzyć ten wzorzec, aby zapewnić spójność ostateczną dla operacji w magazynie tabel i magazynu obiektów Blob i innych źródeł danych usługi Azure Storage, takich jak baza danych lub system plików.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Scalanie lub zastępowanie](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacja transakcji jest ważna dla rozwiązania, należy rozważyć przeprojektowanie tabel, aby umożliwić korzystanie z EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Obsługa jednostek indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki `PartitionKey` `RowKey` przy użyciu i wartości. Dzięki temu aplikacja kliencka skutecznie pobiera jednostkę przy użyciu kwerendy punktowej. Na przykład przy użyciu poniższej struktury tabeli, aplikacja kliencka może skutecznie pobrać jednostkę indywidualnego pracownika przy użyciu nazwy działu i identyfikator pracownika `PartitionKey` (i `RowKey`).  

![Grafika jednostki pracownika][13]

Jeśli chcesz również mieć możliwość pobierania listy jednostek pracowników na podstawie wartości innej właściwości nieunikatowych, takich jak nazwisko, należy użyć mniej wydajnego skanowania partycji. To skanowanie wyszukuje dopasowania, zamiast używać indeksu do bezpośredniego wyszukiwania. Jest to spowodowane magazyn tabel nie zapewnia indeksów pomocniczych.  

#### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska z poprzednią strukturą encji, należy obsługiwać listy identyfikatorów pracowników. Jeśli chcesz pobrać jednostki pracownika o określonym nazwisku, takim jak Jones, należy najpierw zlokalizować listę identyfikatorów pracowników dla pracowników z Jones jako ich nazwisko, a następnie pobrać te jednostki pracownika. Istnieją trzy główne opcje przechowywania list identyfikatorów pracowników:  

* Użyj magazynu obiektów blob.  
* Tworzenie jednostek indeksu w tej samej partycji co jednostki pracownika.  
* Tworzenie jednostek indeksu w osobnej partycji lub tabeli.  

Opcja 1: Użyj magazynu obiektów blob  

Utwórz obiekt blob dla każdego unikatowego nazwiska, a `PartitionKey` w każdym `RowKey` magazynie obiektów blob listę wartości (dział) i (identyfikator pracownika) dla pracowników, którzy mają to nazwisko. Po dodaniu lub usunięciu pracownika upewnij się, że zawartość odpowiedniego obiektu blob jest ostatecznie zgodna z encjami pracownika.  

Opcja 2: Tworzenie jednostek indeksu w tej samej partycji  

Użyj jednostek indeksu, które przechowują następujące dane:  

![Grafika przedstawiająca encję pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tym samym nazwisku][14]

Właściwość `EmployeeIDs` zawiera listę identyfikatorów pracowników dla pracowników z nazwiskiem `RowKey`przechowywanym w pliku .  

Poniższe kroki przedstawiają proces, który należy wykonać podczas dodawania nowego pracownika. W tym przykładzie dodajemy pracownika o identyfikatorze 000152 i nazwisku Jones w dziale sprzedaży:  

1. Pobierz encję indeksu `PartitionKey` o wartości "Sprzedaż" i `RowKey` wartość "Jones". Zapisz eTag tej encji do użycia w kroku 2.  
2. Utwórz transakcję grupy jednostek (czyli operację wsadową),`PartitionKey` która wstawia `RowKey` nową encję pracownika (wartość "Sprzedaż" i`PartitionKey` wartość "000152") i aktualizuje encję indeksu ( wartość "Sprzedaż" i `RowKey` wartość "Jones"). EUWT robi to, dodając nowy identyfikator pracownika do listy w polu Identyfikatory pracowników. Aby uzyskać więcej informacji na temat EGT, zobacz [Transakcje grupy jednostek](#entity-group-transactions).  
3. Jeśli EUWT nie powiedzie się z powodu błędu współbieżności optymistyczne (oznacza to, że ktoś inny zmodyfikował jednostkę indeksu), a następnie należy rozpocząć od nowa w kroku 1.  

Podobne podejście można użyć do usunięcia pracownika, jeśli używasz drugiej opcji. Zmiana nazwiska pracownika jest nieco bardziej złożona, ponieważ należy uruchomić EGT, który aktualizuje trzy jednostki: jednostkę pracownika, jednostkę indeksu dla starego nazwiska i jednostkę indeksu dla nowego nazwiska. Należy pobrać każdej jednostki przed wprowadzeniem jakichkolwiek zmian, w celu pobrania wartości ETag, które następnie można użyć do wykonywania aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy trzeba wyszukać wszystkich pracowników z określonym nazwiskiem w dziale. W tym przykładzie szukamy wszystkich pracowników z nazwiskiem Jones w dziale sprzedaży:  

1. Pobierz encję indeksu `PartitionKey` o wartości "Sprzedaż" i `RowKey` wartość "Jones".  
2. Przejaszeń listę identyfikatorów pracowników `EmployeeIDs` w tym polu.  
3. Jeśli potrzebujesz dodatkowych informacji o każdym z tych pracowników (takich jak ich adresy `PartitionKey` e-mail), `RowKey` pobierz każdą z jednostek pracownika przy użyciu wartości "Sprzedaż" i wartości z listy pracowników uzyskanych w kroku 2.  

Opcja 3: Tworzenie jednostek indeksu w osobnej partycji lub tabeli  

W przypadku tej opcji należy użyć jednostek indeksu, które przechowują następujące dane:  

![Grafika przedstawiająca encję pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tym samym nazwisku][15]

Właściwość `EmployeeIDs` zawiera listę identyfikatorów pracowników dla pracowników z nazwiskiem `RowKey`przechowywanym w pliku .  

Egt nie można używać EGT do zachowania spójności, ponieważ jednostki indeksu znajdują się w oddzielnej partycji od jednostek pracownika. Upewnij się, że jednostki indeksu są po pewnym czasie zgodne z encjami pracownika.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań do pobierania pasujących jednostek: jeden `RowKey` do kwerendy jednostek indeksu, aby uzyskać listę wartości, a następnie kwerendy do pobrania każdej jednostki na liście.  
* Ponieważ pojedyncza jednostka ma maksymalny rozmiar 1 MB, opcja 2 i opcja 3 w rozwiązaniu zakłada, że lista identyfikatorów pracowników dla określonego nazwiska nigdy nie jest większa niż 1 MB. Jeśli lista identyfikatorów pracowników może mieć rozmiar większy niż 1 MB, użyj opcji 1 i przechowuj dane indeksu w magazynie obiektów Blob.  
* Jeśli używasz opcji 2 (za pomocą EGTs do obsługi dodawania i usuwania pracowników i zmiany nazwiska pracownika), należy ocenić, czy liczba transakcji zbliży się do limitów skalowalności w określonej partycji. W takim przypadku należy rozważyć ostatecznie spójne rozwiązanie (opcja 1 lub opcja 3). Używają kolejek do obsługi żądań aktualizacji i umożliwiają przechowywanie jednostek indeksu w osobnej partycji z encjami pracownika.  
* Opcja 2 w tym rozwiązaniu zakłada, że chcesz wyszukać nazwisko w dziale. Na przykład chcesz pobrać listę pracowników o nazwisku Jones w dziale sprzedaży. Jeśli chcesz mieć możliwość wyszukiwania wszystkich pracowników z nazwiskiem Jones w całej organizacji, użyj opcji 1 lub 3.
* Można zaimplementować rozwiązanie oparte na kolejce, które zapewnia spójność ostateczną. Aby uzyskać więcej informacji, zobacz [wzorzec ostatecznie spójne transakcje](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec służy do wyszukiwania zestawu jednostek, które mają wspólną wartość właściwości, na przykład wszystkich pracowników o nazwisku Jones.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzór klucza złożonego](#compound-key-pattern)  
* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenizami typów jednostek](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Wzór denormalizacji
Połącz powiązane dane w jednej jednostce, aby umożliwić pobranie wszystkich potrzebnych danych za pomocą zapytania pojedynczego punktu.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizuje się dane, aby usunąć powielanie, które występuje, gdy kwerendy pobierają dane z wielu tabel. Jeśli normalizujesz dane w tabelach platformy Azure, należy wykonać wiele rund podróży z klienta do serwera, aby pobrać powiązane dane. Na przykład w poniższej strukturze tabeli potrzebne są dwie rundy, aby pobrać szczegóły dla działu. Jedna podróż pobiera jednostkę działu, która zawiera identyfikator menedżera, a druga podróż pobiera dane menedżera w jednostce pracownika.  

![Grafika jednostki działu i jednostki pracownika][16]

#### <a name="solution"></a>Rozwiązanie
Zamiast przechowywać dane w dwóch oddzielnych jednostkach, zdenormalizuj dane i przechowuj kopię szczegółów menedżera w jednostce działu. Przykład:  

![Grafika jednostki departamentu zdenormalizowanego i połączonego][17]

Z jednostek działów przechowywanych z tych właściwości, można teraz pobrać wszystkie szczegóły potrzebne o dziale przy użyciu kwerendy punktowej.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje pewne obciążenie kosztowe związane z przechowywaniem niektórych danych dwa razy. Korzyści z wydajności wynikające z mniejszej liczby żądań do magazynu tabel zazwyczaj przewyższają marginalny wzrost kosztów magazynowania. Ponadto ten koszt jest częściowo kompensowany przez zmniejszenie liczby transakcji wymaganych do pobrania szczegółów działu.  
* Należy zachować spójność dwóch jednostek, które przechowują informacje o menedżerach. Problem spójności można rozwiązać za pomocą EGTs do aktualizowania wielu jednostek w jednej transakcji niepodzielny. W takim przypadku jednostka działu i jednostka pracownika dla menedżera działu są przechowywane w tej samej partycji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy często trzeba wyszukać powiązane informacje. Ten wzorzec zmniejsza liczbę zapytań, które klient musi wykonać, aby pobrać dane, których wymaga.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzór klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenizami typów jednostek](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Wzór klucza złożonego
Użyj `RowKey` wartości złożonych, aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą kwerendy z jednym punktem.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych jest naturalne, aby użyć sprzężeń w kwerendach do zwrócenia powiązanych fragmentów danych do klienta w jednej kwerendzie. Na przykład można użyć identyfikatora pracownika, aby wyszukać listę powiązanych jednostek, które zawierają dane wydajności i przeglądu dla tego pracownika.  

Załóżmy, że są przechowywane jednostki pracowników w magazynie tabel przy użyciu następującej struktury:  

![Grafika jednostki pracownika][18]

Należy również przechowywać dane historyczne dotyczące opinii i wydajności dla każdego roku, w który pracownik pracował dla Twojej organizacji, i musisz mieć dostęp do tych informacji według roku. Jedną z opcji jest utworzenie innej tabeli, która przechowuje jednostki o następującej strukturze:  

![Grafika jednostki przeglądu pracowników][19]

Dzięki takiemu podejściu można zduplikować niektóre informacje (takie jak imię i nazwisko) w nowej jednostce, aby umożliwić pobieranie danych za pomocą jednego żądania. Jednak nie można zachować silną spójność, ponieważ nie można użyć EGT do aktualizacji dwóch jednostek niepodzielnie.  

#### <a name="solution"></a>Rozwiązanie
Przechowuj nowy typ encji w oryginalnej tabeli przy użyciu encji o następującej strukturze:  

![Grafika jednostki pracownika z kluczem złożonym][20]

Zwróć uwagę, `RowKey` jak jest teraz klucz złożony, składający się z identyfikatora pracownika i roku danych przeglądu. Dzięki temu można pobrać wydajność pracownika i przeglądać dane za pomocą jednego żądania dla jednej jednostki.  

W poniższym przykładzie opisano, jak można pobrać wszystkie dane przeglądu dla określonego pracownika (na przykład pracownika 000123 w dziale sprzedaży):  

$filter=(PartitionKey eq 'Sales') i (RowKey ge 'empid_000123') oraz (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego znaku separatora, który ułatwia analizę `RowKey` wartości: na przykład **000123_2012**.  
* Przechowujesz również tę jednostkę w tej samej partycji, co inne jednostki, które zawierają powiązane dane dla tego samego pracownika. Oznacza to, że można użyć EGTs zachować silną spójność.
* Należy wziąć pod uwagę, jak często będziesz wysyłać zapytania do danych, aby ustalić, czy ten wzorzec jest odpowiedni. Na przykład jeśli dostęp do danych przeglądu rzadko i główne dane pracowników często, należy zachować je jako oddzielne jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec służy do przechowywania jednej lub więcej powiązanych jednostek, które często kwerendy.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Praca z heterogenizami typów jednostek](#work-with-heterogeneous-entity-types)  
* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Wzór ogona dziennika
Pobieranie *n* jednostek ostatnio dodane do partycji `RowKey` przy użyciu wartości, która sortuje w odwrotnej kolejności daty i godziny.  

> [!NOTE]
> Wyniki kwerendy zwracane przez interfejs API tabeli platformy Azure w usłudze Azure Cosmos DB nie są sortowane według klucza partycji ani klucza wiersza. W związku z tym, podczas gdy ten wzorzec jest odpowiedni dla magazynu tabel, nie jest odpowiedni dla usługi Azure Cosmos DB. Aby uzyskać szczegółową listę różnic funkcji, zobacz [różnice między interfejsem API tabel w usłudze Azure Cosmos DB i usłudze Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymaganiem jest możliwość pobierania ostatnio utworzonych encji, na przykład dziesięciu najnowszych oświadczeń o wydatkach złożonych przez pracownika. Kwerendy tabeli `$top` obsługują operację kwerendy, aby zwrócić pierwsze *n* jednostki z zestawu. Nie ma równoważnej operacji kwerendy, aby zwrócić ostatnie *n* jednostek w zestawie.  

#### <a name="solution"></a>Rozwiązanie
Przechowuj jednostki przy `RowKey` użyciu, który naturalnie sortuje w odwrotnej kolejności daty/godziny, więc najnowszy wpis jest zawsze pierwszym w tabeli.  

Na przykład, aby móc pobrać dziesięć najnowszych oświadczeń o wydatkach przesłanych przez pracownika, można użyć odwrotnej wartości znacznika uzyskanej od bieżącej daty/godziny. Poniższy przykład kodu C# pokazuje jeden sposób, aby utworzyć odpowiednią `RowKey` wartość "odwróconych znaczników" dla sortowania od najnowszego do najstarszego:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić do wartości daty/godziny, używając następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Kwerenda tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy unieść wartość znacznika wstecznego z zerami wiodącymi, aby upewnić się, że wartość ciągu jest sortowane zgodnie z oczekiwaniami.  
* Należy pamiętać o cele skalowalności na poziomie partycji. Należy uważać, aby nie tworzyć partycji hot spot.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec służy, gdy trzeba uzyskać dostęp do jednostek w odwrotnej kolejności daty/godziny lub gdy trzeba uzyskać dostęp do ostatnio dodanych jednostek.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Poprzedzaj / dołączanie anty-wzór](#prepend-append-anti-pattern)  
* [Pobieranie encji](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Wzorzec usuwania dużej objętości
Włącz usunięcie dużej liczby jednostek, przechowując wszystkie jednostki do jednoczesnego usunięcia w ich osobnej tabeli. Jednostki można usunąć, usuwając tabelę.  

#### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji usuwa stare dane, które nie muszą być już dostępne dla aplikacji klienckiej lub że aplikacja została zarchiwizowana na innym nośniku magazynu. Zazwyczaj można zidentyfikować takie dane według daty. Na przykład masz wymóg, aby usunąć rekordy wszystkich żądań logowania, które są więcej niż 60 dni.  

Jednym z możliwych projektów jest użycie daty i godziny `RowKey`żądania logowania w:  

![Grafika jednostki próby logowania][21]

Takie podejście pozwala uniknąć hotspotów partycji, ponieważ aplikacja może wstawiać i usuwać jednostki logowania dla każdego użytkownika w osobnej partycji. Jednak takie podejście może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek. Najpierw należy wykonać skanowanie tabeli w celu zidentyfikowania wszystkich jednostek do usunięcia, a następnie należy usunąć każdą starą jednostkę. Można zmniejszyć liczbę rund do serwera wymagane do usunięcia starych jednostek przez przetwarzanie wielu żądań usuwania do EGTs.  

#### <a name="solution"></a>Rozwiązanie
Użyj osobnej tabeli dla każdego dnia prób logowania. Poprzedni projekt encji służy do unikania punktów aktywnych podczas wstawiania jednostek. Usuwanie starych jednostek jest teraz po prostu kwestią usuwania jednej tabeli każdego dnia (pojedyncza operacja magazynowania), zamiast znajdowania i usuwania setek i tysięcy pojedynczych jednostek logowania każdego dnia.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy projekt obsługuje inne sposoby, w jakie aplikacja będzie używać danych, takie jak wyszukywanie określonych jednostek, łączenie z innymi danymi lub generowanie zagregowanych informacji?  
* Czy projekt uniknąć hot spotów podczas wstawiania nowych elementów?  
* Spodziewaj się opóźnienia, jeśli chcesz ponownie użyć tej samej nazwy tabeli po jej usunięciu. Lepiej zawsze używać unikatowych nazw tabel.  
* Spodziewaj się pewnego ograniczenia szybkości podczas pierwszego użycia nowej tabeli, podczas gdy magazyn tabel uczy się wzorców dostępu i rozdziela partycje między węzłami. Należy wziąć pod uwagę, jak często trzeba tworzyć nowe tabele.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy masz dużą liczbę jednostek, które należy usunąć w tym samym czasie.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Modyfikowanie jednostek](#modify-entities)  

### <a name="data-series-pattern"></a>Wzorzec serii danych
Przechowuj pełną serię danych w jednej jednostce, aby zminimalizować liczbę żądań.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym scenariuszem jest dla aplikacji do przechowywania serii danych, które zazwyczaj musi pobrać wszystkie na raz. Na przykład aplikacja może rejestrować liczbę wiadomości błyskawicznych, które każdy pracownik wysyła co godzinę, a następnie użyć tych informacji do wykreślenia liczby wiadomości wysłanych przez każdego użytkownika w ciągu ostatnich 24 godzin. Jednym z projektów może być przechowywanie 24 jednostek dla każdego pracownika:  

![Grafika jednostki statystyk wiadomości][22]

Za pomocą tego projektu można łatwo zlokalizować i zaktualizować jednostkę, aby zaktualizować dla każdego pracownika, gdy aplikacja musi zaktualizować wartość liczby wiadomości. Jednak aby pobrać informacje, aby wykreślić wykres działania dla poprzednich 24 godzin, należy pobrać 24 jednostek.  

#### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z oddzielną właściwością do przechowywania liczby wiadomości dla każdej godziny:  

![Grafika przedstawiająca encję statystyk wiadomości z oddzielnymi właściwościami][23]

Za pomocą tego projektu można użyć operacji scalania, aby zaktualizować liczbę wiadomości dla pracownika przez określoną godzinę. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli pełna seria danych nie mieści się w jednej jednostce (jednostka może mieć maksymalnie 252 właściwości), należy użyć alternatywnego magazynu danych, takiego jak obiekt blob.  
* Jeśli masz wielu klientów aktualizowanie jednostki jednocześnie, użyj **ETag** do zaimplementowania optymistycznej współbieżności. Jeśli masz wielu klientów, może wystąpić wysoki spór.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba zaktualizować i pobrać serię danych skojarzonych z poszczególnych jednostek.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Szyk dużych elementów](#large-entities-pattern)  
* [Scalanie lub zastępowanie](#merge-or-replace)  
* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern) (jeśli przechowujesz serię danych w obiekcie blob)  

### <a name="wide-entities-pattern"></a>Szeroki szyk elementów
Wiele jednostek fizycznych służy do przechowywania jednostek logicznych o więcej niż 252 właściwościach.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka może mieć nie więcej niż 252 właściwości (z wyłączeniem właściwości systemu obowiązkowego) i nie może przechowywać więcej niż 1 MB danych w sumie. W relacyjnej bazie danych zazwyczaj można obejść wszelkie limity rozmiaru wiersza, dodając nową tabelę i wymuszając relację 1-do-1 między nimi.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą magazynu tabel, można przechowywać wiele jednostek do reprezentowania jednego dużego obiektu biznesowego z więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości błyskawicznych wysłanych przez każdego pracownika w ciągu ostatnich 365 dni, można użyć następującego projektu, który używa dwóch jednostek z różnymi schematami:  

![Grafika przedstawiająca encję statystyk wiadomości z kluczem wiersza 01 i encją statystyk wiadomości z kluczem wiersza 02][24]

Jeśli trzeba wprowadzić zmianę, która wymaga aktualizacji obu jednostek, aby zachować je zsynchronizowane ze sobą, można użyć EUWT. W przeciwnym razie można użyć pojedynczej operacji scalania, aby zaktualizować liczbę wiadomości dla określonego dnia. Aby pobrać wszystkie dane dla pojedynczego pracownika, należy pobrać obie jednostki. Można to zrobić za pomocą dwóch `PartitionKey` wydajnych `RowKey` żądań, które używają wartości i wartości.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Należy wziąć pod uwagę następujący punkt przy podejmowaniu decyzji, jak zaimplementować ten wzorzec:  

* Pobieranie pełnej jednostki logicznej obejmuje co najmniej dwie transakcje magazynu: jedną do pobrania każdej jednostki fizycznej.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy używać, gdy trzeba przechowywać jednostki, których rozmiar lub liczba właściwości przekracza limity dla poszczególnych jednostek w magazynie tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Scalanie lub zastępowanie](#merge-or-replace)

### <a name="large-entities-pattern"></a>Szyk dużych elementów
Użyj magazynu obiektów Blob do przechowywania dużych wartości właściwości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza encja nie może przechowywać łącznie więcej niż 1 MB danych. Jeśli jedna lub kilka właściwości przechowuje wartości, które powodują, że całkowity rozmiar jednostki przekroczy tę wartość, nie można przechowywać całej encji w magazynie tabel.  

#### <a name="solution"></a>Rozwiązanie
Jeśli jednostka przekracza 1 MB w rozmiarze, ponieważ co najmniej jedna właściwości zawierają dużą ilość danych, można przechowywać dane w magazynie obiektów Blob, a następnie przechowywać adres obiektu blob we właściwości w jednostce. Na przykład można przechowywać zdjęcie pracownika w magazynie obiektów Blob i przechowywać łącze do zdjęcia we `Photo` właściwości jednostki pracownika:  

![Grafika przedstawiająca encję pracownika z ciągiem dla zdjęcia wskazującym magazyn obiektów Blob][25]

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczną między jednostką w magazynie tabel i danych w magazynie obiektów Blob, użyj [wzorzec ostatecznie spójne transakcje](#eventually-consistent-transactions-pattern) do obsługi jednostek.
* Pobieranie pełnej jednostki obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania jednostki i jeden do pobrania danych obiektu blob.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy użyć, gdy trzeba przechowywać jednostki, których rozmiar przekracza limity dla poszczególnych jednostek w magazynie tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  
* [Szeroki szyk elementów](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Poprzedzaj/dołączaj anty-wzór
Gdy masz dużą ilość wstawek, zwiększ skalowalność, rozprowadzając wstawki na wielu partycjach.  

#### <a name="context-and-problem"></a>Kontekst i problem
Poprzedzające lub dołączanie jednostek do przechowywanych jednostek zazwyczaj powoduje, że aplikacja dodaje nowe jednostki do pierwszej lub ostatniej partycji sekwencji partycji. W takim przypadku wszystkie wstawia w danym momencie odbywają się w tej samej partycji, tworząc hotspot. Zapobiega to magazyn tabely z równoważenia obciążenia wstawia w wielu węzłach i ewentualnie powoduje, że aplikacja trafić cele skalowalności dla partycji. Rozważmy na przykład przypadek aplikacji, która rejestruje dostęp do sieci i zasobów przez pracowników. Struktura jednostki, taka jak następujące może spowodować partycji bieżącej godziny staje się hotspot, jeśli liczba transakcji osiągnie docelową skalowalność dla poszczególnych partycji:  

![Grafika jednostki pracownika][26]

#### <a name="solution"></a>Rozwiązanie
Następująca alternatywna struktura encji pozwala uniknąć hotspotu na dowolnej określonej partycji, ponieważ aplikacja rejestruje zdarzenia:  

![Grafika przedstawiająca encję pracownika z kluczem wiersza, który potęguje rok, miesiąc, dzień, godzinę i identyfikator zdarzenia][27]

Zwróć uwagę w tym `PartitionKey` `RowKey` przykładzie, jak zarówno klucze złożone i są. Używa `PartitionKey` zarówno działu, jak i identyfikatora pracownika do dystrybucji rejestrowania na wielu partycjach.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy alternatywna struktura klucza, która pozwala uniknąć tworzenia gorących partycji na wstawianiach skutecznie obsługuje zapytania, które sprawia, że aplikacja kliencka?  
* Czy przewidywany wolumen transakcji oznacza, że prawdopodobnie osiągniesz cele skalowalności dla pojedynczej partycji i zostanie ograniczona przez magazyn tabel?  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać dołączania wzorca anty-wzorzec, gdy ilość transakcji może spowodować ograniczenie szybkości przez magazyn tabeli podczas uzyskiwania dostępu do partycji gorącej.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzór klucza złożonego](#compound-key-pattern)  
* [Wzór ogona dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Rejestrowanie danych anty-wzór
Zazwyczaj należy użyć magazynu obiektów Blob zamiast magazynu tabel do przechowywania danych dziennika.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym przypadkiem użycia danych dziennika jest pobranie wyboru wpisów dziennika dla określonego zakresu daty/godziny. Na przykład chcesz znaleźć wszystkie błędy i krytyczne komunikaty, które aplikacja rejestrowane między 15:04 i 15:06 w określonym dniu. Nie chcesz używać daty i godziny komunikatu dziennika, aby określić partycję, na której zapisujesz jednostki dziennika. Powoduje to gorącą partycję, ponieważ w dowolnym określonym czasie wszystkie `PartitionKey` jednostki dziennika będą współużytkować tę samą wartość (zobacz [prepend/append anti-pattern](#prepend-append-anti-pattern)). Na przykład następujący schemat jednostki dla komunikatu dziennika powoduje gorącą partycję, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika na partycji dla bieżącej daty i godziny:  

![Grafika encji wiadomości dziennika][28]

W tym przykładzie `RowKey` zawiera datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są sortowane w kolejności daty/godziny. Zawiera `RowKey` również identyfikator wiadomości, w przypadku wielu komunikatów dziennika współużytkować tę samą datę i godzinę.  

Innym podejściem `PartitionKey` jest użycie, który zapewnia, że aplikacja zapisuje komunikaty w zakresie partycji. Na przykład jeśli źródło komunikatu dziennika zapewnia sposób dystrybucji wiadomości na wielu partycjach, można użyć następującego schematu jednostki:  

![Grafika encji wiadomości dziennika][29]

Jednak problem z tym schematem jest to, że aby pobrać wszystkie komunikaty dziennika dla określonego przedziału czasu, należy przeszukać każdą partycję w tabeli.

#### <a name="solution"></a>Rozwiązanie
W poprzedniej sekcji podkreślono problem podczas próby użycia magazynu tabel do przechowywania wpisów dziennika i zaproponowano dwa niezadowalające projekty. Jedno rozwiązanie doprowadziło do gorącej partycji z ryzykiem niskiej wydajności zapisywania komunikatów dziennika. Inne rozwiązanie spowodowało niską wydajność kwerendy, ze względu na wymóg skanowania każdej partycji w tabeli, aby pobrać komunikaty dziennika dla określonego przedziału czasu. Magazyn obiektów Blob oferuje lepsze rozwiązanie dla tego typu scenariusza i tak jest, jak usługa Azure Storage przechowuje dane dziennika, które zbiera.  

W tej sekcji opisano, jak analizy magazynu przechowuje dane dziennika w magazynie obiektów Blob, jako ilustrację tego podejścia do przechowywania danych, które zazwyczaj kwerendy według zakresu.  

Analiza magazynu przechowuje komunikaty dziennika w formacie rozdzielanym w wielu obiektach blob. Format rozdzielany ułatwia aplikacji klienckiej analizowanie danych w komunikacie dziennika.  

Analiza magazynu używa konwencji nazewnictwa dla obiektów blob, która umożliwia zlokalizowanie obiektu blob (lub obiektów blob), które zawierają komunikaty dziennika, dla których są wyszukiwane. Na przykład obiekt blob o nazwie "queue/2014/07/31/1800/000001.log" zawiera komunikaty dziennika, które odnoszą się do usługi kolejki dla godziny rozpoczynającej się o godzinie 18:00 w dniu 31 lipca 2014 r. "000001" wskazuje, że jest to pierwszy plik dziennika dla tego okresu. Analiza magazynu rejestruje również sygnatury czasowe wiadomości pierwszego i ostatniego dziennika przechowywane w pliku jako część metadanych obiektu blob. Interfejs API magazynu obiektów Blob umożliwia lokalizowanie obiektów blob w kontenerze na podstawie prefiksu nazwy. Aby zlokalizować wszystkie obiekty blob, które zawierają dane dziennika kolejki dla godziny rozpoczynającej się od 18:00, można użyć prefiksu "queue/2014/07/31/1800".  

Analiza magazynu buforuje komunikaty dziennika wewnętrznie, a następnie okresowo aktualizuje odpowiedni obiekt blob lub tworzy nowy z najnowszą partią wpisów dziennika. Zmniejsza to liczbę zapisów, które musi wykonać do magazynu obiektów Blob.  

Jeśli implementujesz podobne rozwiązanie we własnej aplikacji, zastanów się, jak zarządzać kompromisem między niezawodnością a kosztami i skalowalnością. Innymi słowy, ocenić efekt pisania każdego wpisu dziennika do magazynu obiektów Blob, jak to się dzieje, w porównaniu do buforowania aktualizacji w aplikacji i zapisywania ich do magazynu obiektów Blob w partiach.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Przy podejmowaniu decyzji o przechowywaniu danych dziennika należy wziąć pod uwagę następujące kwestie:  

* Jeśli utworzysz projekt tabeli, który pozwala uniknąć potencjalnych gorących partycji, może się okazać, że nie można uzyskać dostępu do danych dziennika wydajnie.  
* Aby przetworzyć dane dziennika, klient często musi załadować wiele rekordów.  
* Chociaż dane dziennika jest często ustrukturyzowane, magazyn obiektów Blob może być lepszym rozwiązaniem.  

### <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, o których należy pamiętać podczas implementowania wzorców opisanych w poprzednich sekcjach. Większość z tej sekcji używa przykładów napisanych w języku C#, które używają biblioteki klienta magazynu (wersja 4.3.0 w momencie pisania).  

### <a name="retrieve-entities"></a>Pobieranie encji
Jak wspomniano w sekcji [Projektowanie do wykonywania zapytań,](#design-for-querying)najbardziej efektywną kwerendą jest kwerenda punktowa. Jednak w niektórych scenariuszach może być konieczne pobranie wielu jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek przy użyciu biblioteki klienta magazynu.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Uruchamianie kwerendy punktowej przy użyciu biblioteki klienta magazynu
Najprostszym sposobem uruchomienia kwerendy punktowej jest użycie operacji **Pobierz** tabelę. Jak pokazano w poniższym fragmentie kodu C#, ta operacja `PartitionKey` pobiera jednostkę o `RowKey` wartości "Sprzedaż" i wartości "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekuje `EmployeeEntity`jednostki, która pobiera być typu .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Pobieranie wielu jednostek przy użyciu funkcji LINQ
Można pobrać wiele jednostek przy użyciu LINQ z biblioteką klienta magazynu i określając kwerendę z **klauzuli where.** Aby uniknąć skanowania tabeli, należy `PartitionKey` zawsze dołączyć wartość w where `RowKey` klauzuli i, jeśli to możliwe wartość, aby uniknąć skanowania tabeli i partycji. Magazyn tabel obsługuje ograniczony zestaw operatorów porównania (większy niż, większy lub równy, mniejszy niż, mniejszy lub równy, równy i nie równy) do użycia w klauzuli where. Poniższy fragment kodu Języka C# znajduje wszystkich pracowników, których nazwisko zaczyna się `RowKey` od "B" (przy założeniu, `PartitionKey` że przechowuje nazwisko) w dziale sprzedaży (przy założeniu, że sklepy nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Zwróć uwagę, jak kwerenda określa zarówno a, `RowKey` jak i a, `PartitionKey` aby zapewnić lepszą wydajność.  

Poniższy przykładowy kod pokazuje równoważne funkcje przy użyciu płynnego interfejsu API (aby uzyskać więcej informacji na temat płynnych interfejsów API w ogóle, zobacz [Najważniejsze wskazówki dotyczące projektowania płynnego interfejsu API):](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)  

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
> Próbka zagnieżdża wiele `CombineFilters` metod, aby uwzględnić trzy warunki filtru.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Pobieranie dużej liczby encji z kwerendy
Optymalna kwerenda zwraca pojedynczą `PartitionKey` jednostkę `RowKey` na podstawie wartości i wartości. Jednak w niektórych scenariuszach może być wymagane, aby zwrócić wiele jednostek z tej samej partycji lub nawet z wielu partycji. Zawsze należy w pełni przetestować wydajność aplikacji w takich scenariuszach.  

Kwerenda względem magazynu tabel może zwracać maksymalnie 1000 jednostek jednocześnie i działać przez maksymalnie pięć sekund. Magazyn tabel zwraca token kontynuacji, aby umożliwić aplikacji klienckiej żądanie następnego zestawu jednostek, jeśli spełnione są następujące czynności:

- Zestaw wyników zawiera ponad 1000 encji.
- Kwerenda nie została ukończona w ciągu pięciu sekund.
- Kwerenda przekracza granicę partycji. 

Aby uzyskać więcej informacji na temat działania tokenów kontynuacji, zobacz [Limit czasu i podział na strony .](https://msdn.microsoft.com/library/azure/dd135718.aspx)  

Jeśli używasz biblioteki klienta magazynu, może ona automatycznie obsługiwać tokeny kontynuacji, ponieważ zwraca jednostki z magazynu tabel. Na przykład następujący przykład kodu Języka C# automatycznie obsługuje tokeny kontynuacji, jeśli magazyn tabel zwraca je w odpowiedzi:  

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

Następujący kod Języka C# obsługuje tokeny kontynuacji jawnie:  

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

Za pomocą tokenów kontynuacji jawnie, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład jeśli aplikacja kliencka umożliwia użytkownikom strony za pośrednictwem jednostek przechowywanych w tabeli, użytkownik może zdecydować, aby nie strony za pośrednictwem wszystkich jednostek pobranych przez kwerendę. Aplikacja będzie używać tylko token kontynuacji do pobierania następnego segmentu, gdy użytkownik zakończył stronicowania za pośrednictwem wszystkich jednostek w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Można ograniczyć ilość danych do pobrania z magazynu tabel i przenoszenia w sieci.  
* We/Wy asynchronizajne można wykonać w .NET.  
* Token kontynuacji można serializować do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zazwyczaj zwraca segment zawierający 1000 jednostek, chociaż może zawierać mniej. Jest to również w przypadku, jeśli ograniczyć liczbę wpisów kwerendy zwraca za pomocą **Take** do zwrócenia pierwszych n jednostek, które odpowiadają kryteriom odnośnika. Magazyn tabel może zwracać segment zawierający mniej niż n jednostek, wraz z tokenem kontynuacji, aby umożliwić pobieranie pozostałych jednostek.  
> 
> 

Poniższy kod Języka C# pokazuje, jak zmodyfikować liczbę jednostek zwróconych wewnątrz segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekcja po stronie serwera
Pojedyncza jednostka może mieć maksymalnie 255 właściwości i mieć rozmiar do 1 MB. Podczas kwerendy tabeli i pobierania jednostek, może nie być potrzebne wszystkie właściwości i można uniknąć niepotrzebnego przesyłania danych (w celu zmniejszenia opóźnienia i kosztów). Projekcja po stronie serwera umożliwia przeniesienie tylko potrzebnych właściwości. Poniższy przykład pobiera `Email` tylko właściwość `PartitionKey` `RowKey`(wraz z , , `Timestamp`i `ETag`) z jednostek wybranych przez kwerendę.  

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

Zwróć uwagę, `RowKey` jak wartość jest dostępna, nawet jeśli nie jest uwzględniona na liście właściwości do pobrania.  

### <a name="modify-entities"></a>Modyfikowanie jednostek
Biblioteka klienta magazynu umożliwia modyfikowanie jednostek przechowywanych w magazynie tabel przez wstawianie, usuwanie i aktualizowanie jednostek. Egtów służy do partii wielu wstawia, aktualizacji i usuwania operacji razem, aby zmniejszyć liczbę rund wymagane i poprawić wydajność rozwiązania.  

Wyjątki zgłaszane podczas biblioteki klienta magazynu uruchamia EGT zazwyczaj obejmują indeks jednostki, która spowodowała niepowodzenie partii. Jest to przydatne podczas debugowania kodu, który używa EGTs.  

Należy również rozważyć, jak projekt wpływa na sposób, w jaki aplikacja kliencka obsługuje operacje współbieżności i aktualizacji.  

#### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie magazyn tabel implementuje optymistyczne kontrole współbieżności na poziomie poszczególnych jednostek dla operacji wstawiania, scalania i usuwania, chociaż klient może wymusić na magazynie tabel, aby pominąć te kontrole. Aby uzyskać więcej informacji, zobacz [Zarządzanie współbieżnością w programie Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Scalanie lub zastępowanie
Metoda `Replace` `TableOperation` klasy zawsze zastępuje pełną jednostkę w magazynie tabel. Jeśli właściwość nie zostanie uwzględnina w żądaniu, gdy ta właściwość istnieje w encji przechowywanej, żądanie usuwa tę właściwość z przechowywanej jednostki. Jeśli nie chcesz usunąć właściwości jawnie z przechowywanej jednostki, należy uwzględnić wszystkie właściwości w żądaniu.  

Można użyć `Merge` metody klasy, `TableOperation` aby zmniejszyć ilość danych wysyłanych do magazynu tabel, gdy chcesz zaktualizować jednostkę. Metoda `Merge` zastępuje wszystkie właściwości w składowanej jednostki wartościami właściwości z jednostki uwzględnionej w żądaniu. Ta metoda pozostawia nienaruszone wszelkie właściwości w przechowywanej jednostce, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz duże jednostki i wystarczy zaktualizować niewielką liczbę właściwości w żądaniu.  

> [!NOTE]
> Metody `*Replace` `Merge` i nie powiedzie się, jeśli jednostka nie istnieje. Alternatywnie można użyć `InsertOrReplace` metody `InsertOrMerge` i, które tworzą nową jednostkę, jeśli nie istnieje.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Praca z heterogenizami typów jednostek
Magazyn tabel jest magazynem tabel *bez schematu.* Oznacza to, że pojedyncza tabela może przechowywać jednostki wielu typów, zapewniając dużą elastyczność w projekcie. W poniższym przykładzie przedstawiono tabelę przechowującą zarówno jednostki pracowników, jak i działów:  

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
<th>Adres e-mail</th>
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
<th>Adres e-mail</th>
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
<th>Liczba pracowników</th>
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
<th>Adres e-mail</th>
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

Każda encja `PartitionKey`musi `RowKey`nadal `Timestamp` mieć , i wartości, ale może mieć dowolny zestaw właściwości. Ponadto nic nie wskazuje typu jednostki, chyba że zdecydujesz się gdzieś przechowywać te informacje. Istnieją dwie opcje identyfikacji typu jednostki:  

* Przedawiać typ `RowKey` jednostki do `PartitionKey`(lub ewentualnie ). Na przykład `EMPLOYEE_000123` `DEPARTMENT_SALES` lub `RowKey` jako wartości.  
* Użyj osobnej właściwości, aby zarejestrować typ jednostki, jak pokazano w poniższej tabeli.  

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
<th>Typ obiektu</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Adres e-mail</th>
</tr>
<tr>
<td>Employee</td>
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
<th>Typ obiektu</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Adres e-mail</th>
</tr>
<tr>
<td>Employee</td>
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
<th>Typ obiektu</th>
<th>DepartmentName</th>
<th>Liczba pracowników</th>
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
<th>Typ obiektu</th>
<th>FirstName</th>
<th>LastName</th>
<th>Wiek</th>
<th>Adres e-mail</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Pierwsza opcja, poprzedzająca typ jednostki `RowKey`do , jest przydatna, jeśli istnieje możliwość, że dwie jednostki różnych typów mogą mieć tę samą wartość klucza. Grupuje również jednostki tego samego typu razem w partycji.  

Techniki omówione w tej sekcji są szczególnie istotne dla dyskusji na temat[relacji dziedziczenia](#inheritance-relationships).  

> [!NOTE]
> Należy wziąć pod uwagę dołączenie numeru wersji w wartości typu jednostki, aby umożliwić aplikacjom klienckim ewoluowanie obiektów POCO i pracę z różnymi wersjami.  
> 
> 

W dalszej części tej sekcji opisano niektóre funkcje w bibliotece klienta magazynu, które ułatwiają pracę z wieloma typami jednostek w tej samej tabeli.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Pobieranie heterogenicznych typów jednostek
Jeśli używasz biblioteki klienta magazynu, masz trzy opcje pracy z wieloma typami jednostek.  

Jeśli znasz typ jednostki przechowywanej `RowKey` `PartitionKey` z określonymi i wartościami, możesz określić typ jednostki podczas pobierania jednostki. To było widać w poprzednich dwóch przykładach, `EmployeeEntity`które pobierają jednostki typu: [Uruchom kwerendę punktową przy użyciu biblioteki klienta magazynu](#run-a-point-query-by-using-the-storage-client-library) i pobrać wiele [jednostek przy użyciu LINQ](#retrieve-multiple-entities-by-using-linq).  

Drugą opcją jest `DynamicTableEntity` użycie typu (worka właściwości), zamiast konkretnego typu jednostki POCO. Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby serializacji i deserializacji jednostki do typów .NET. Poniższy kod Języka C# potencjalnie pobiera wiele jednostek różnych typów z `DynamicTableEntity` tabeli, ale zwraca wszystkie jednostki jako wystąpienia. Następnie używa właściwości `EntityType` do określenia typu każdej jednostki:  

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

Aby pobrać inne właściwości, należy `TryGetValue` użyć metody `Properties` we `DynamicTableEntity` właściwości klasy.  

Trzecią opcją jest łączenie `DynamicTableEntity` przy użyciu `EntityResolver` typu i wystąpienia. Dzięki temu można rozpoznać do wielu typów POCO w tej samej kwerendzie. W tym przykładzie `EntityResolver` delegat `EntityType` używa właściwości do rozróżniania dwóch typów encji, które zwraca kwerenda. Metoda `Resolve` używa delegata `resolver` do `DynamicTableEntity` rozpoznawania `TableEntity` wystąpień do wystąpień.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Modyfikowanie heterogenicznych typów jednostek
Nie musisz znać typu jednostki, aby ją usunąć, a zawsze znasz typ jednostki podczas wstawiania. Można jednak użyć `DynamicTableEntity` tego typu do aktualizacji jednostki bez znajomości jej typu i bez użycia klasy jednostki POCO. Poniższy przykładowy kod pobiera pojedynczą jednostkę i sprawdza, czy `EmployeeCount` właściwość istnieje przed zaktualizowaniem go.  

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

### <a name="control-access-with-shared-access-signatures"></a>Kontrolowanie dostępu za pomocą podpisów dostępu współdzielonego
Tokeny sygnatury dostępu współdzielonego (SAS) umożliwiają aplikacjom klienckim bezpośrednie modyfikowanie (i wykonywanie zapytań) jednostek tabel, bez konieczności uwierzytelniania bezpośrednio za pomocą magazynu tabel. Zazwyczaj istnieją trzy główne korzyści z używania sygnatury dostępu Współdzielonego w aplikacji:  

* Nie trzeba rozpowszechniać klucza konta magazynu na niezabezpieczonej platformie (takiej jak urządzenie przenośne), aby umożliwić temu urządzeniu dostęp do jednostek w magazynie tabel i modyfikowanie ich.  
* Można odciążyć niektóre prace wykonywane przez role sieci Web i procesu roboczego podczas zarządzania jednostkami. Można odciążać do urządzeń klienckich, takich jak komputery użytkowników końcowych i urządzenia przenośne.  
* Do klienta można przypisać ograniczony i ograniczony czasowo zestaw uprawnień (na przykład zezwalanie na dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów sygnatury dostępu współdzielonego z magazynem tabel, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Jednak nadal należy wygenerować tokeny sygnatury dostępu Współdzielonego, które udzielają aplikacji klienckiej do jednostek w magazynie tabel. Zrób to w środowisku, które ma bezpieczny dostęp do kluczy konta magazynu. Zazwyczaj należy użyć roli sieci web lub procesu roboczego do generowania tokenów sygnatury dostępu Współdzielonego i dostarczania ich do aplikacji klienckich, które potrzebują dostępu do jednostek. Ponieważ nadal istnieje obciążenie związane z generowaniem i dostarczaniem tokenów sygnatury dostępu Współdzielonego do klientów, należy rozważyć, jak najlepiej zmniejszyć to obciążenie, szczególnie w scenariuszach dużej liczby woluminów.  

Jest możliwe do wygenerowania tokenu sygnatury dostępu współdzielonego, który udziela dostępu do podzbioru jednostek w tabeli. Domyślnie należy utworzyć token sygnatury dostępu Współdzielonego dla całej tabeli. Ale jest również możliwe, aby określić, że token sygnatury dostępu współdzielonego `PartitionKey` udzielić dostępu do zakresu wartości lub zakres `PartitionKey` i `RowKey` wartości. Można wybrać do generowania tokenów sygnatury dostępu Współdzielonego dla poszczególnych użytkowników systemu, tak, że każdy użytkownik sygnatury dostępu współdzielonego tylko umożliwia im dostęp do własnych jednostek w magazynie tabel.  

### <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Pod warunkiem, że rozmieszczasz żądania na wielu partycjach, można poprawić przepływność i szybkość reakcji klienta przy użyciu zapytań asynchronicznych lub równoległych.
Na przykład może mieć dwa lub więcej wystąpień roli procesu roboczego uzyskiwania dostępu do tabel równolegle. Możesz mieć poszczególne role procesu roboczego odpowiedzialne za określone zestawy partycji lub po prostu mieć wiele wystąpień roli procesu roboczego, z których każda może uzyskać dostęp do wszystkich partycji w tabeli.  

W wystąpieniu klienta można zwiększyć przepływność, uruchamiając operacje magazynu asynchronicznie. Biblioteka klienta magazynu ułatwia pisanie asynchronicznych zapytań i modyfikacji. Na przykład można rozpocząć od metody synchroniczną, która pobiera wszystkie jednostki w partycji, jak pokazano w następującym kodzie Języka C#:  

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

Można łatwo zmodyfikować ten kod, tak aby kwerenda działa asynchronicznie, w następujący sposób:  

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

W tym przykładzie asynchroniiowym można zobaczyć następujące zmiany z wersji synchroniczowej:  

* Podpis metody zawiera `async` teraz modyfikator `Task` i zwraca wystąpienie.  
* Zamiast wywoływania `ExecuteSegmented` metody, aby pobrać wyniki, metoda `ExecuteSegmentedAsync` teraz wywołuje metodę. Metoda używa modyfikatora `await` do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywołać tę metodę `department` wiele razy, z różnymi wartościami dla parametru. Każde zapytanie jest uruchamiane w osobnym wątku.  

Nie ma żadnej asynchroniiowej `Execute` wersji `TableQuery` metody w `IEnumerable` klasie, ponieważ interfejs nie obsługuje wyliczenia asynchroniczne.  

Można również wstawiać, aktualizować i usuwać jednostki asynchronicznie. W poniższym przykładzie języka C# przedstawiono prostą, synchroniczne metody wstawiania lub zastępowania jednostki pracownika:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Można łatwo zmodyfikować ten kod, tak aby aktualizacja działa asynchronicznie, w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchroniiowym można zobaczyć następujące zmiany z wersji synchroniczowej:  

* Podpis metody zawiera `async` teraz modyfikator `Task` i zwraca wystąpienie.  
* Zamiast wywoływania `Execute` metody, aby zaktualizować jednostkę, `ExecuteAsync` metoda teraz wywołuje metodę. Metoda używa modyfikatora `await` do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywołać wiele metod asynchronicznych, takich jak ta, a każde wywołanie metody jest uruchamiane w oddzielnym wątku.  


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

