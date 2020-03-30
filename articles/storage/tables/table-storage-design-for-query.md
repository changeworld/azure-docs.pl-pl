---
title: Projektowanie magazynu tabel platformy Azure dla zapytań | Dokumenty firmy Microsoft
description: Projektowania tabel dla zapytań w magazynie tabel platformy Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457648"
---
# <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Rozwiązania usługi tabeli mogą być odczytywane intensywnie, intensywnie pisać lub mieszać te dwa. W tym artykule koncentruje się na rzeczy, o których należy pamiętać podczas projektowania usługi tabeli do obsługi operacji odczytu skutecznie. Zazwyczaj projekt, który obsługuje operacje odczytu wydajnie jest również wydajne dla operacji zapisu. Istnieją jednak dodatkowe kwestie, o których należy pamiętać podczas projektowania w celu obsługi operacji zapisu, omówione w artykule [Projektowanie modyfikacji danych](table-storage-design-for-modification.md).

Dobrym punktem wyjścia do projektowania rozwiązania usługi tabel, aby umożliwić efektywne odczytywanie danych jest pytanie "Jakie zapytania będzie musiała wykonać moja aplikacja w celu pobrania danych potrzebnych z usługi tabeli?"  

> [!NOTE]
> Dzięki usłudze Tabele ważne jest, aby projekt był poprawny z góry, ponieważ późniejsza jego zmiana jest trudna i kosztowna. Na przykład w relacyjnej bazie danych często można rozwiązać problemy z wydajnością, po prostu dodając indeksy do istniejącej bazy danych: nie jest to opcja z usługą tabel.  
> 
> 

W tej sekcji skupiono się na kluczowych problemach, które należy rozwiązać podczas projektowania tabel do wykonywania zapytań. Tematy omówione w tej sekcji obejmują:

* [Jak wybór PartitionKey i RowKey wpływa na wydajność kwerendy](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Wybór odpowiedniego klawisza PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optymalizacja zapytań dla usługi tabel](#optimizing-queries-for-the-table-service)
* [Sortowanie danych w usłudze tabel](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór PartitionKey i RowKey wpływa na wydajność kwerendy
Poniższe przykłady zakładają, że usługa tabel przechowuje jednostki pracowników o następującej strukturze (większość przykładów pomija właściwość **sygnatury czasowej** dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **Imię** |Ciąg |
| **Nazwisko** |Ciąg |
| **Wiek** |Liczba całkowita |
| **Emailaddress** |Ciąg |

W artykule [Usługa Azure Table Storage omówienie](table-storage-overview.md) niektórych kluczowych funkcji usługi Tabela platformy Azure, które mają bezpośredni wpływ na projektowanie kwerendy. Wynik tych wyników w następujących ogólnych wytycznych dotyczących projektowania kwerend usługi tabeli. Należy zauważyć, że składnia filtru używana w poniższych przykładach pochodzi z interfejsu API REST usługi tabel, aby uzyskać więcej informacji, zobacz [Jednostki kwerend](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* ***Kwerenda punktowa*** jest najbardziej efektywnym wyszukiwaniem w użyciu i jest zalecana do użycia w przypadku wyszukiwań o dużej objętości lub wyszukiwań wymagających najniższego opóźnienia. Takie zapytanie można użyć indeksów, aby zlokalizować poszczególne jednostki bardzo skutecznie, określając wartości **PartitionKey** i **RowKey.** Na przykład: $filter=(PartitionKey eq 'Sales') i (RowKey eq '2')  
* Drugi najlepszy jest ***zakres kwerendy,*** która używa **PartitionKey** i filtry w zakresie **rowkey** wartości, aby zwrócić więcej niż jedną jednostkę. **Value PartitionKey** identyfikuje określoną partycję, a wartości **RowKey** identyfikują podzbiór jednostek w tej partycji. Na przykład: $filter=PartitionKey eq 'Sales' i RowKey ge 'S' i RowKey lt 'T'  
* Trzeci najlepszy jest ***skanowanie partycji,*** który używa **PartitionKey** i filtry na innej właściwości nie klucz i który może zwrócić więcej niż jedną jednostkę. **Value PartitionKey** identyfikuje określoną partycję, a wartości właściwości wybierają dla podzbioru jednostek w tej partycji. Na przykład: $filter=PartitionKey eq 'Sales' i LastName eq 'Smith'  
* ***Skanowanie tabeli*** nie zawiera **PartitionKey** i jest bardzo nieefektywne, ponieważ przeszukuje wszystkie partycje, które tworzą tabelę z kolei dla wszystkich pasujących jednostek. Wykona skanowanie tabeli niezależnie od tego, czy filtr używa **klawisza RowKey**. Na przykład: $filter=LastName eq 'Jones'  
* Kwerendy zwracające wiele jednostek zwracają je posortowane w **kolejności PartitionKey** i **RowKey.** Aby uniknąć uciekania jednostek w kliencie, wybierz **Klawisz Wiersza,** który definiuje najbardziej typowe kolejność sortowania.  

Należy zauważyć, że za pomocą "**lub**" do określenia filtru na podstawie wartości **RowKey** powoduje skanowanie partycji i nie jest traktowany jako kwerenda zakres. W związku z tym należy unikać zapytań, które używają filtrów, takich jak: $filter=PartitionKey eq "Sales" i (RowKey eq '121' lub RowKey eq '322')  

Przykłady kodu po stronie klienta, które używają biblioteki klienta magazynu do wykonywania wydajnych zapytań, zobacz:  

* [Wykonywanie kwerendy punktowej przy użyciu biblioteki klienta magazynu](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu linq](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](table-storage-design-patterns.md#server-side-projection)  

Przykłady kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli, zobacz:  

* [Praca z heterogenizami typów jednostek](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Wybór odpowiedniego klawisza PartitionKey
Wybór **PartitionKey** należy zrównoważyć potrzebę włączenia użycia EGTs (w celu zapewnienia spójności) z wymogu dystrybucji jednostek na wielu partycjach (w celu zapewnienia skalowalne rozwiązanie).  

W jednej skrajności można przechowywać wszystkie jednostki w jednej partycji, ale może to ograniczyć skalowalność rozwiązania i uniemożliwić usługi tabel jest w stanie obciążenia żądań równoważenia. Z drugiej strony można przechowywać jedną jednostkę na partycję, która byłaby wysoce skalowalna i która umożliwia usłudze tabel żądania równoważenia obciążenia, ale która uniemożliwiłaby korzystanie z transakcji grupy jednostek.  

Idealny **PartitionKey** to taki, który umożliwia korzystanie z wydajnych zapytań i który ma wystarczające partycje, aby upewnić się, że rozwiązanie jest skalowalne. Zazwyczaj okaże się, że jednostki będą miały odpowiednią właściwość, która dystrybuuje jednostki na wystarczające partycje.

> [!NOTE]
> Na przykład w systemie, który przechowuje informacje o użytkownikach lub pracownikach, UserID może być dobrym PartitionKey. Może być kilka jednostek, które używają danego identyfikatora użytkownika jako klucza partycji. Każda jednostka, która przechowuje dane o użytkowniku jest pogrupowana w jedną partycję, a więc te jednostki są dostępne za pośrednictwem transakcji grupy jednostek, przy jednoczesnym zachowaniu wysoce skalowalne.
> 
> 

Istnieją dodatkowe zagadnienia w wyborze **PartitionKey,** które odnoszą się do sposobu wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz [Projektowanie tabel do modyfikacji danych](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optymalizacja zapytań dla usługi tabel
Usługa tabela automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości w jednym indeksie klastrowanym, stąd powód, że zapytania punktowe są najbardziej efektywne w użyciu. Jednak nie istnieją żadne indeksy inne niż indeksy klastrowane na **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby umożliwić wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład lokalizowanie jednostek pracowników na podstawie wiadomości e-mail, identyfikatora pracownika lub nazwiska. Wzorce opisane w [wzorce projektowania tabel](table-storage-design-patterns.md) adres tych typów wymagań i opisują sposoby pracy wokół faktu, że usługa tabel nie zapewnia indeksów pomocniczych:  

* [Wewnątrzpoziomowy wzorzec indeksu pomocniczego](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **Klawisza Wiersza** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey.**  
* [Wzorzec indeksu pomocniczego między partycjami](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **RowKey** w oddzielnych partycjach lub w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey.**  
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — obsługa jednostek indeksu w celu umożliwienia efektywnego wyszukiwania, które zwracają listy jednostek.  

## <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w usłudze tabel
Usługa tabel zwraca jednostki posortowane w porządku rosnącym na podstawie **klawisza PartitionKey,** a następnie przez **klawisz RowKey**. Te klucze są wartościami ciągu i aby upewnić się, że wartości liczbowe sortują poprawnie, należy przekonwertować je na stałą długość i zasypać je zerami. Jeśli na przykład wartość identyfikatora pracownika używana jako **klawisz RowKey** jest wartością całkowitą, należy przekonwertować identyfikator pracownika **123** na **00000123**.  

Wiele aplikacji ma wymagania dotyczące używania danych posortowanych w różnych zamówieniach: na przykład sortowania pracowników według nazwy lub przez datę przystąpienia. Następujące wzorce obejmują sposób alternatywnego sortowania zamówień dla encji:  

* [Wewnątrzpoziomowy wzorzec indeksu pomocniczego](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości Klawisza Wiersza (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości RowKey.  
* [Wzorzec indeksu pomocniczego między partycjami](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości RowKey w oddzielnych partycjach w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości RowKey.
* [Wzorzec wiersza dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobieranie *n* jednostek ostatnio dodane do partycji przy użyciu **RowKey** wartość, która sortuje w odwrotnej kolejności daty i godziny.  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabel](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Szyfruj dane tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
