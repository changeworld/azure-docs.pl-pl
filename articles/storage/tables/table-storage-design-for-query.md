---
title: Projektowanie usługi Azure Table Storage na potrzeby zapytań | Microsoft Docs
description: Projektowanie tabel dla zapytań w usłudze Azure Table Storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457648"
---
# <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Rozwiązania usługi tabeli mogą być odczytywane znaczne wykorzystanie mocy, intensywnie zapisu lub zmieszanie dwóch. Ten artykuł koncentruje się na zagadnieniach, które należy wziąć pod uwagę podczas projektowania Table service w celu wydajnej obsługi operacji odczytu. Zazwyczaj projekt, że obsługuje operacje odczytu efektywnie również jest wydajne dla operacji zapisu. Istnieją jednak dodatkowe zagadnienia, które należy wziąć pod uwagę podczas projektowania programu w celu obsługi operacji zapisu, omówione w artykule [projektowanie artykułu na potrzeby modyfikacji danych](table-storage-design-for-modification.md).

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

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór właściwości PartitionKey i RowKey ma wpływ na wydajność zapytań
W poniższych przykładach założono, usłudze table service zapisuje jednostek pracownika o następującej strukturze (Pomiń Większość przykładów **sygnatura czasowa** właściwości dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **Imię** |Ciąg |
| **Nazwisko** |Ciąg |
| **Wiek** |Liczba całkowita |
| **EmailAddress** |Ciąg |

Artykuł [Omówienie usługi Azure Table Storage](table-storage-overview.md) zawiera opis niektórych kluczowych funkcji usługi Azure Table Service, które mają bezpośredni wpływ na projektowanie zapytań. Wynikiem tych ogólne wytyczne dotyczące projektowania zapytań usługi tabeli. Należy zauważyć, że składnia filtru użyta w poniższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz [jednostki zapytań](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***kwerendy punktu*** jest najbardziej efektywny sposób wyszukiwania, aby użyć i jest zalecana służący do wyszukiwania mocno obciążające lub wyszukiwania, wymagających najmniejszego opóźnienia. Takie zapytanie może używać indeksów, aby bardzo wydajnie lokalizować poszczególne jednostki przez określenie wartości **PartitionKey** i **RowKey** . Na przykład: $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq '2')  
* Po drugie, najlepiej jest ***kwerendy zakresu*** , który używa **PartitionKey** i filtry na szeroką gamę **RowKey** wartości do zwrócenia więcej niż jedną jednostkę. **PartitionKey** wartość określa określonej partycji i **RowKey** wartości identyfikują podzestawu jednostek w partycji. Na przykład: $filter = PartitionKey eq "Sprzedaż i RowKey ge" i RowKey lt t "  
* Trzeci najlepiej ***skanowania partycji*** , który używa **PartitionKey** i filtry na inną właściwość klucza i który może zwrócić więcej niż jedną jednostkę. **PartitionKey** wartość określa określonej partycji, a właściwość wartości wybranych dla podzestawu jednostek w partycji. Na przykład: $filter = PartitionKey eq "Sprzedaż" i LastName eq 'Smith'  
* ***Skanowanie tabeli*** nie obejmuje **PartitionKey** i jest bardzo wydajne, ponieważ przeszukuje wszystkie partycje, które składają się na tabelę, z kolei w przypadku wszystkich pasujących jednostek. Zostanie przeprowadzone skanowanie tabeli, niezależnie od tego, czy filtr używa **RowKey**. Na przykład: $filter = LastName eq "Kowalski"  
* Zapytania, które zwracają wiele jednostek zwraca je posortowane w **PartitionKey** i **RowKey** zamówienia. Aby uniknąć konieczności uciekania się jednostki w obiekcie klienta, wybierz opcję **RowKey** definiujący najbardziej typowe kolejność sortowania.  

Należy zauważyć, że użycie "**lub**" do określenia filtru opartego na wartościach **RowKey** skutkuje przeskanowaniem partycji i nie jest traktowane jako zapytanie o zakres. W związku z tym, należy unikać zapytań, które używają filtrów, takich jak: $filter = PartitionKey eq "Sales" oraz (RowKey eq "121" lub RowKey eq "322")  

Przykłady kodu po stronie klienta, które można wykonać wydajne zapytania za pomocą biblioteki klienta magazynu zobacz:  

* [Wykonywanie zapytania punktu przy użyciu biblioteki klienta usługi Storage](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](table-storage-design-patterns.md#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli zobacz:  

* [Współpraca z niejednorodnymi typami jednostek](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien równoważyć potrzeby włączyć EGTs (w celu zapewnienia spójności) wymaganiem, aby rozprowadzić jednostek na wiele partycji (w celu zapewnienia skalowalne rozwiązanie).  

Jeden extreme wszystkich jednostek można przechowywać w jednej partycji, ale to może ograniczać skalowalność rozwiązania i uniemożliwi usłudze table service mogli wysyłać żądania równoważenia obciążenia. Z drugiej najwyższej wartości można przechowywać jedną jednostkę na partycję, która byłaby wysoce skalowalna, co umożliwia usłudze tabel Równoważenie obciążenia żądań, ale co uniemożliwi korzystanie z transakcji grupy jednostek.  

Idealna **PartitionKey** jeden, który umożliwia wydajne zapytania i ma wystarczającą partycji, aby upewnić się, Twoje rozwiązanie jest skalowalna. Zazwyczaj znajdziesz, że obiekty odpowiednie właściwości, która dystrybuuje jednostek na partycji wystarczającą.

> [!NOTE]
> Na przykład w systemie, która przechowuje informacje dotyczące użytkowników lub pracowników, nazwa użytkownika może być dobrym PartitionKey. Może mieć kilka jednostek, korzystających z danego identyfikatora użytkownika jako klucza partycji. Każda jednostka, która przechowuje dane o użytkowniku są pogrupowane w jednej partycji, a więc te jednostki są dostępne za pośrednictwem transakcji grup jednostek, przy zachowaniu o wysokim stopniu skalowalności.
> 
> 

Dostępne są dodatkowe zagadnienia dotyczące wyboru **PartitionKey** , które odnoszą się do sposobu wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz [Projektowanie tabel do modyfikacji danych](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optymalizowanie zapytań dotyczących Table service
Usługa Table service automatycznie indeksuje jednostek za pomocą **PartitionKey** i **RowKey** wartości jeden indeks klastrowany, dlatego Przyczyna, wskazujące zapytania są najbardziej wydajne do użycia. Istnieją jednak żadne indeksy innych niż na indeks klastrowany w **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby włączyć wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład Lokalizowanie jednostek pracowników opartych na adres e-mail, identyfikator pracownika lub nazwisko. Wzorce opisane w [wzorach projektu tabeli](table-storage-design-patterns.md) dotyczą tego typu wymagania i opisują sposoby obejścia tego faktu, że Table Service nie udostępnia indeksów pomocniczych:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec pomocniczy indeks partycji między](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości w oddzielnych partycji lub w oddzielnych tabelach umożliwiające szybkie i wydajne wyszukiwania i sortowania zamówień za pomocą różnych **RowKey** wartości.  
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  

## <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w usłudze Table service
Usługa Table service zwraca jednostek posortowane w kolejności rosnącej na podstawie **PartitionKey** a następnie według **RowKey**. Te klucze są wartości typu ciąg i aby zapewnić poprawne sortowanie wartości liczbowych, należy przekonwertować je na stałej długości i uzupełniania zer. Na przykład, jeśli wartość identyfikatora pracowników jest używany jako **RowKey** jest liczbą całkowitą, należy przekonwertować identyfikator pracownika **123** do **00000123**.  

Wiele aplikacji ma wymagania dotyczące korzystania z danych w różnych zleceniach sortowane: na przykład sortowanie pracowników według nazwy lub dołączenie do daty. Następujące wzorce dotyczą sposobu wykonywania alternatywnych zamówień sortowania dla jednostek:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek przy użyciu różnych wartości RowKey (w tej samej partycji), aby umożliwić szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych wartości RowKey.  
* [Wzorzec pomocniczy indeks partycji między](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek przy użyciu różnych wartości RowKey w oddzielnych partycjach w oddzielnych tabelach, aby umożliwić szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych wartości RowKey .
* [Wzorzec ogona dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Relacje modelowania](table-storage-design-modeling.md)
- [Szyfruj dane tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie na potrzeby modyfikacji danych](table-storage-design-for-modification.md)
