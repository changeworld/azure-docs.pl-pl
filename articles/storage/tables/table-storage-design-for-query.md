---
title: Projektowanie tabelach magazynu Azure dla zapytań | Dokumentacja firmy Microsoft
description: Projektowanie tabelach kwerend w magazynie tabel platformy Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: b8d2033b0b29caddf165f4b582c7d0578109480c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660917"
---
# <a name="design-for-querying"></a>Projekt do wykonywania zapytań
Tabela rozwiązań usług mogą być odczytywane CPU, intensywnie zapisu lub zmieszanie dwóch. Ten artykuł skupia się na tym, co pamiętać podczas projektowania usługi tabeli, aby efektywnie obsługiwać operacje odczytu. Projekt, że obsługuje operacje odczytu wydajnie również jest zazwyczaj wydajne dla operacji zapisu. Istnieją dodatkowe zagadnienia dotyczące przy tym pamiętać podczas projektowania w celu obsługi operacji, omówionych w artykule zapisu [projektu do modyfikacji danych](table-storage-design-for-modification.md).

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

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór PartitionKey i RowKey ma wpływ na wydajność zapytań
W następujących przykładach przyjmuje usługi tabel jest przechowywanie pracownika jednostek o następującej strukturze (Pomiń Większość przykładów **sygnatury czasowej** właściwości dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **Imię** |Ciąg |
| **Nazwisko** |Ciąg |
| **okres ważności** |Liczba całkowita |
| **EmailAddress** |Ciąg |

Artykuł [omówienie magazynu tabel Azure](table-storage-overview.md) opisano niektóre najważniejsze funkcje usługi tabel Azure, które mają bezpośredni wpływ na projektowanie dla zapytania. Wynikiem tych następujące ogólne wskazówki dotyczące projektowania zapytań usługi tabeli. Należy zauważyć, że składnia filtru używane w poniższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [jednostek zapytania](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***zapytania punktu*** jest najbardziej wydajnym odnośnika do użycia i jest zalecany do zastosowania w przypadku dużych wyszukiwań lub wyszukiwań wymagające najniższym opóźnieniu. Takie zapytanie może być bardzo wydajnie Znajdź pojedynczą jednostkę, określając zarówno indeksy **PartitionKey** i **RowKey** wartości. Na przykład: $filter = (PartitionKey eq 'Sales') i (RowKey eq "2")  
* Drugi najlepiej jest ***kwerendy zakresu*** używającą **PartitionKey** i filtry w zakresie **RowKey** wartości do zwrócenia więcej niż jednej jednostki. **PartitionKey** wartość identyfikuje określonej partycji i **RowKey** identyfikuje podzbiór jednostki w tej partycji. Na przykład: $filter = PartitionKey eq "Sprzedaży i RowKey ge" i RowKey lt 'T "  
* Trzeci najlepiej ***skanowania partycji*** używającą **PartitionKey** i filtry w innej właściwości niekluczowe i mogą zwracać więcej niż jednej jednostki. **PartitionKey** wartość identyfikuje określonej partycji i wybierz dla podzbioru jednostek w partycji tej wartości właściwości. Na przykład: $filter = PartitionKey eq "Sprzedaży" i nazwisko eq 'Smith'  
* A ***skanowania tabeli*** nie obejmuje **PartitionKey** i jest bardzo mało wydajne, ponieważ wszystkie partycje, które składają się na tabelę z kolei do żadnych zgodnych jednostek wyszukiwania. Zostanie przeprowadzone skanowanie tabeli niezależnie od tego, czy korzysta z filtrem **RowKey**. Na przykład: $filter = nazwisko eq 'Nowak'  
* Zapytań zwracających wiele jednostek zwrócić posortowane w **PartitionKey** i **RowKey** kolejności. Aby uniknąć ponowne sortowanie jednostek na komputerze klienckim, wybierz opcję **RowKey** definiuje najczęściej kolejności sortowania.  

Należy pamiętać, że za pomocą "**lub**" do określenia filtru na podstawie **RowKey** wartości powoduje skanowania partycji i nie jest traktowana jako kwerendy zakresu. W związku z tym należy unikać zapytań, które używać filtrów, takich jak: $filter = PartitionKey eq 'Sales' i (RowKey eq "121" lub RowKey eq "322")  

Przykłady kodu po stronie klienta, korzystające z biblioteki klienta magazynu na wykonanie kwerend wydajne zobacz:  

* [Wykonywanie zapytań punktu za pomocą biblioteki klienta usługi Storage](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Pobieranie wiele jednostek za pomocą LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](table-storage-design-patterns.md#server-side-projection)  

Przykłady kodu po stronie klienta, który może obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli zobacz:  

* [Praca z typami jednostek heterogenicznych](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien saldo trzeba włączyć EGTs (w celu zapewnienia spójności) z wymaganiem, aby rozpowszechniają jednostek wiele partycji (w celu zapewnienia skalowalna).  

W jednym extreme można przechowywać wszystkie obiekty w jednej partycji, ale to może ograniczać skalowalność rozwiązania i uniemożliwi usłudze tabeli mogli wysyłać żądania równoważenia obciążenia. Y. można przechowywać jednej jednostki dla każdej partycji, będzie wysoce skalowalna i które umożliwia usłudze tabeli wysyłać żądania równoważenia obciążenia, ale co może uniemożliwić używanie jednostek grupy transakcji.  

Idealnym **PartitionKey** jest jedną, która pozwala na użycie wydajność zapytań, na którym jest wystarczające partycji w celu zapewnienia jest skalowalne rozwiązanie. Zazwyczaj można zauważyć, że obiekty odpowiednie właściwości, która dystrybuuje jednostek na partycji wystarczającą.

> [!NOTE]
> Na przykład w systemie, która przechowuje informacje dotyczące użytkowników lub pracowników, nazwa użytkownika może być dobrym PartitionKey. Może mieć kilka jednostki, które korzystają z danym UserID jako klucza partycji. Każda jednostka, która przechowuje dane o użytkowniku są pogrupowane w jednej partycji, a więc te jednostki są dostępne przy użyciu transakcji grupy jednostek, przy zachowaniu wysokiej skalowalności.
> 
> 

Istnieją dodatkowe zagadnienia w wybranym **PartitionKey** dotyczących sposobu będzie wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz [projektowanie tabelach modyfikacji danych](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optymalizacja zapytania dotyczące usługi tabel
Usługa tabel automatycznie indeksuje jednostek przy użyciu **PartitionKey** i **RowKey** wartości w jeden indeks klastrowany, dlatego powód punktu zapytania jest najbardziej wydajnym do użycia. Istnieją jednak nie indeksów inną niż indeksu klastrowanego na **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby włączyć wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład Lokalizowanie jednostek pracownika opartych na wiadomości e-mail, identyfikator lub nazwisko. Wzorce opisanego w [wzorce projektowe tabeli](table-storage-design-patterns.md) adresów tego rodzaju wymaganie oraz opisano sposoby obchodzić fakt, że usługa tabel nie udostępnia indeksów pomocniczych:  

* [Wzorzec pomocniczy indeks partycji wewnątrz](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji), Włącz szybkie i wydajne wyszukiwań i alternatywne sortowania przy użyciu różnych **RowKey** wartości.  
* [Wzorzec między partycji pomocniczy indeks](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -przechowywać wiele kopii każdej jednostki przy użyciu różnych **RowKey** wartości w osobnych partycji lub w różnych tabele, aby włączyć szybkie i wydajne wyszukiwanie i sortowanie alternatywne zamówienia przy użyciu różnych **RowKey** wartości.  
* [Wzorzec jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — Obsługa indeksu jednostek umożliwiające wydajne wyszukiwanie, które zwracają list jednostek.  

## <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w usłudze tabel
Usługa tabel zwraca jednostki sortowane w kolejności rosnącej według **PartitionKey** , a następnie według **RowKey**. Klucze te są ciągami i w celu zapewnienia poprawnie sortowanie wartości liczbowe, należy przekonwertować je na o stałej długości i uzupełniania zer. Na przykład, jeśli wartość identyfikatora pracownika jest używany jako **RowKey** jest wartość całkowita należy przekonwertować identyfikator **123** do **00000123**.  

Wiele aplikacji ma wymagania dotyczące korzystania z danych posortowane w różnej kolejności: na przykład sortowanie pracowników według nazwy lub przez przyłączenie daty. Wzorce następujących adresów jak alternatywny sortowania dla jednostki:  

* [Wzorzec pomocniczy indeks wewnątrz partycji](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowywać kopie każdego obiektu przy użyciu różnych wartości RowKey (w tej samej partycji), aby włączyć funkcję szybkiego i wydajne wyszukiwanie i sortowanie alternatywne porządkuje przy użyciu innej wartości RowKey.  
* [Wzorzec między partycji pomocniczy indeks](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowywać wiele kopii każdej jednostki przy użyciu innej wartości RowKey w osobnych partycji w oddzielnych tabelach umożliwia szybkie i wydajne wyszukiwanie i sortowanie alternatywne porządkuje przy użyciu innej wartości RowKey .
* [Wzorzec końcowego fragmentu dziennika](table-storage-design-patterns.md#log-tail-pattern) -pobrać *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość sortujące odwrotnej daty i czasu kolejności.  

## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Relacje modelowania](table-storage-design-modeling.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projekt do modyfikacji danych](table-storage-design-for-modification.md)
