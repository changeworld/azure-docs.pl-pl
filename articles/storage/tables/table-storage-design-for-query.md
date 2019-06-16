---
title: Projektowanie tabel usługi Azure storage dla zapytań | Dokumentacja firmy Microsoft
description: Projektowanie tabel dla zapytań w usłudze Azure table storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 97373f6f0138d3ed8028ed4327b7e6cf90ad76a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325871"
---
# <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Rozwiązania usługi tabeli mogą być odczytywane znaczne wykorzystanie mocy, intensywnie zapisu lub zmieszanie dwóch. Ten artykuł koncentruje się na tym, co można mieć na uwadze podczas projektowania usługi Table service, aby efektywnie obsługiwać operacje odczytu. Zazwyczaj projekt, że obsługuje operacje odczytu efektywnie również jest wydajne dla operacji zapisu. Jednak istnieją pewne dodatkowe zagadnienia dotyczące pamiętać podczas projektowania, aby obsługiwać operacje zapisu w magazynie, omówionych w artykule [projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md).

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
| **EmailAddress** |String |

Artykuł [Omówienie usługi Azure Table storage](table-storage-overview.md) opisano niektóre z kluczowymi funkcjami usługi Azure Table service, które mają bezpośredni wpływ na projektowanie pod kątem zapytań. Wynikiem tych ogólne wytyczne dotyczące projektowania zapytań usługi tabeli. Należy zauważyć, że składnia filtru użyte w poniższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [wykonywanie zapytań dotyczących jednostek](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***kwerendy punktu*** jest najbardziej efektywny sposób wyszukiwania, aby użyć i jest zalecana służący do wyszukiwania mocno obciążające lub wyszukiwania, wymagających najmniejszego opóźnienia. W przypadku takiego zapytania użyć indeksy, aby zlokalizować pojedynczą jednostkę w bardzo wydajny sposób przez określenie zarówno **PartitionKey** i **RowKey** wartości. Na przykład: $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq '2')  
* Po drugie, najlepiej jest ***kwerendy zakresu*** , który używa **PartitionKey** i filtry na szeroką gamę **RowKey** wartości do zwrócenia więcej niż jedną jednostkę. **PartitionKey** wartość określa określonej partycji i **RowKey** wartości identyfikują podzestawu jednostek w partycji. Na przykład: $filter = PartitionKey eq "Sprzedaż i RowKey ge" i RowKey lt t "  
* Trzeci najlepiej ***skanowania partycji*** , który używa **PartitionKey** i filtry na inną właściwość klucza i który może zwrócić więcej niż jedną jednostkę. **PartitionKey** wartość określa określonej partycji, a właściwość wartości wybranych dla podzestawu jednostek w partycji. Na przykład: $filter = PartitionKey eq "Sprzedaż" i LastName eq 'Smith'  
* A ***skanowanie tabeli*** nie obejmuje **PartitionKey** i jest bardzo mało wydajne, ponieważ wszystkie partycje, które tworzą tabelę z kolei do żadnych zgodnych jednostek wyszukiwania. Zostanie przeprowadzone skanowanie tabeli, niezależnie od tego, czy filtr używa **RowKey**. Na przykład: $filter = LastName eq "Kowalski"  
* Zapytania, które zwracają wiele jednostek zwraca je posortowane w **PartitionKey** i **RowKey** zamówienia. Aby uniknąć konieczności uciekania się jednostki w obiekcie klienta, wybierz opcję **RowKey** definiujący najbardziej typowe kolejność sortowania.  

Należy pamiętać, że używanie "**lub**" Aby określić filtr oparty na **RowKey** wartości wyniki podczas skanowania partycji i nie jest traktowana jako zapytanie zakresu. W związku z tym, należy unikać zapytań, które używają filtrów, takich jak: $filter = PartitionKey eq "Sales" oraz (RowKey eq "121" lub RowKey eq "322")  

Przykłady kodu po stronie klienta, które można wykonać wydajne zapytania za pomocą biblioteki klienta magazynu zobacz:  

* [Wykonaj zapytanie punktu, przy użyciu biblioteki klienta magazynu](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Pobieranie wielu jednostek za pomocą LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](table-storage-design-patterns.md#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli zobacz:  

* [Praca z typami encji heterogenicznych](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien równoważyć potrzeby włączyć EGTs (w celu zapewnienia spójności) wymaganiem, aby rozprowadzić jednostek na wiele partycji (w celu zapewnienia skalowalne rozwiązanie).  

Jeden extreme wszystkich jednostek można przechowywać w jednej partycji, ale to może ograniczać skalowalność rozwiązania i uniemożliwi usłudze table service mogli wysyłać żądania równoważenia obciążenia. Y. można przechowywać jednej jednostki w jednej partycji, będzie zapewniać wysoką skalowalność umożliwiająca usłudze table service, aby wysyłać żądania równoważenia obciążenia, ale co może uniemożliwić przy użyciu transakcji grup jednostek.  

Idealna **PartitionKey** jeden, który umożliwia wydajne zapytania i ma wystarczającą partycji, aby upewnić się, Twoje rozwiązanie jest skalowalna. Zazwyczaj znajdziesz, że obiekty odpowiednie właściwości, która dystrybuuje jednostek na partycji wystarczającą.

> [!NOTE]
> Na przykład w systemie, która przechowuje informacje dotyczące użytkowników lub pracowników, nazwa użytkownika może być dobrym PartitionKey. Może mieć kilka jednostek, korzystających z danego identyfikatora użytkownika jako klucza partycji. Każda jednostka, która przechowuje dane o użytkowniku są pogrupowane w jednej partycji, a więc te jednostki są dostępne za pośrednictwem transakcji grup jednostek, przy zachowaniu o wysokim stopniu skalowalności.
> 
> 

Istnieją pewne dodatkowe zagadnienia dotyczące w wybranym **PartitionKey** wydania jak będzie Wstawianie, aktualizowanie i usuwanie jednostek. Aby uzyskać więcej informacji, zobacz [projektowania tabel dla modyfikacji danych](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optymalizowanie zapytań dotyczących Table service
Usługa Table service automatycznie indeksuje jednostek za pomocą **PartitionKey** i **RowKey** wartości jeden indeks klastrowany, dlatego Przyczyna, wskazujące zapytania są najbardziej wydajne do użycia. Istnieją jednak żadne indeksy innych niż na indeks klastrowany w **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby włączyć wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład Lokalizowanie jednostek pracowników opartych na adres e-mail, identyfikator pracownika lub nazwisko. Wzorce opisane w [wzorce projektowe oparte na tabeli](table-storage-design-patterns.md) rozwiązać tego rodzaju wymagań oraz opisano sposoby obejścia faktów, usłudze Table service nie zapewnia indeksów pomocniczych:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec pomocniczy indeks partycji między](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości w oddzielnych partycji lub w oddzielnych tabelach umożliwiające szybkie i wydajne wyszukiwania i sortowania zamówień za pomocą różnych **RowKey** wartości.  
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  

## <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w usłudze Table service
Usługa Table service zwraca jednostek posortowane w kolejności rosnącej na podstawie **PartitionKey** a następnie według **RowKey**. Te klucze są wartości typu ciąg i aby zapewnić poprawne sortowanie wartości liczbowych, należy przekonwertować je na stałej długości i uzupełniania zer. Na przykład, jeśli wartość identyfikatora pracowników jest używany jako **RowKey** jest liczbą całkowitą, należy przekonwertować identyfikator pracownika **123** do **00000123**.  

Wiele aplikacji ma wymagania dotyczące korzystania z danych w różnych zleceniach sortowane: na przykład sortowanie pracowników według nazwy lub dołączenie do daty. Następujące wzorce adresów jak alternatywny porządek sortowania dla jednostki:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek przy użyciu różnych wartości RowKey (w tej samej partycji), aby umożliwić szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych wartości RowKey.  
* [Wzorzec pomocniczy indeks partycji między](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store wiele kopii poszczególnych jednostek przy użyciu różnych wartości RowKey w oddzielnych partycjach w oddzielnych tabelach, aby umożliwić szybkie i efektywne wyszukiwania i sortowania są porządkowane przy użyciu różnych wartości RowKey .
* [Wzorzec ogona dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe oparte na tabelę](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Szyfrowanie danych w tabelach](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
