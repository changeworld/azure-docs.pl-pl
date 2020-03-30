---
title: Wzorce projektowania tabel magazynu platformy Azure | Dokumenty firmy Microsoft
description: Użyj wzorców dla rozwiązań usługi tabel platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 5478163a6103bcc84b4f3608d7513c6e7cb11c01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529343"
---
# <a name="table-design-patterns"></a>Wzorce projektowe tabel
W tym artykule opisano niektóre wzorce odpowiednie do użytku z rozwiązaniami usługi tabel. Ponadto, zobaczysz, jak można praktycznie rozwiązać niektóre problemy i kompromisy omówione w innych artykułach projektu magazynu tabeli. Na poniższym diagramie podsumowano relacje między różnymi wzorcami:  

![, aby wyszukać powiązane dane](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Powyższa mapa wzorca wyróżnia niektóre relacje między wzorami (niebieskimi) i anty-wzorami (pomarańczowymi), które są udokumentowane w tym przewodniku. Istnieje wiele innych wzorców, które są warte rozważenia. Na przykład jednym z kluczowych scenariuszy dla usługi tabel jest użycie [wzorca widoku materializowanego](https://msdn.microsoft.com/library/azure/dn589782.aspx) z wzorca [segregacji odpowiedzialność kwerendy polecenia (CQRS).](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

## <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego wewnątrz partycji
Przechowuj wiele kopii każdej encji przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie i alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey.** Aktualizacje między kopiami mogą być spójne przy użyciu EGTs.  

### <a name="context-and-problem"></a>Kontekst i problem
Usługa tabel automatycznie indeksuje jednostki przy użyciu **partitionkey** i **RowKey** wartości. Dzięki temu aplikacja kliencka do pobierania jednostki wydajnie przy użyciu tych wartości. Na przykład za pomocą struktury tabeli pokazano poniżej, aplikacja kliencka może użyć kwerendy punktowej do pobrania jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator pracownika **(PartitionKey** i **RowKey** wartości). Klient może również pobierać jednostki posortowane według identyfikatora pracownika w każdym dziale.

![Obrazki06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Jeśli chcesz również mieć możliwość znalezienia jednostki pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, należy użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Dzieje się tak, ponieważ usługa tabel nie zapewnia indeksów pomocniczych. Ponadto nie ma opcji żądania listy pracowników posortowanych w innej kolejności niż kolejność **RowKey.**  

### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdej kopii przy użyciu innej wartości **RowKey.** Jeśli przechowujesz jednostkę ze strukturami pokazanymi poniżej, można skutecznie pobierać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksu **dla klawisza RowKey**, "empid_" i "email_" umożliwiają wykonywanie zapytań o jednego pracownika lub szereg pracowników przy użyciu szeregu adresów e-mail lub identyfikatorów pracowników.  

![Jednostki pracowników](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Następujące dwa kryteria filtrowania (jedno wyszukuje identyfikator pracownika i jedno wyszukuje według adresu e-mail) określają zarówno zapytania punktowe:  

* $filter=(PartitionKey eq 'Sales') i (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') i (RowKey eq 'email_jonesj@contoso.com')  

W przypadku kwerendy dla zakresu encji pracowników można określić zakres posortowany w kolejności identyfikatora pracownika lub zakres posortowany w kolejności adresów e-mail, odwołując się do encji z odpowiednim prefiksem w **klawiszie RowKey**.  

* Aby znaleźć wszystkich pracowników w dziale sprzedaży z identyfikatorem pracownika w zakresie od 000100 do 000199 użyj: $filter=(PartitionKey eq 'Sales') i (RowKey ge 'empid_000100') i (RowKey le 'empid_000199')  
* Aby znaleźć wszystkich pracowników w dziale sprzedaży z adresem e-mail, zaczynając od litery "a" użyj: $filter=(PartitionKey eq "Sales") i (RowKey ge "email_a") i (RowKey lt 'email_b')  
  
  Składnia filtru używana w powyższych przykładach pochodzi z interfejsu API REST usługi tabel, aby uzyskać więcej informacji, zobacz [Encje kwerend](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tani w użyciu, więc koszty związane z przechowywaniem zduplikowanych danych nie powinny być głównym problemem. Jednak zawsze należy ocenić koszt projektu na podstawie wymagań przewidywanego magazynu i tylko dodać zduplikowane jednostki do obsługi zapytań, które będzie wykonywana aplikacja kliencka.  
* Ponieważ jednostki indeksu pomocniczego są przechowywane w tej samej partycji co oryginalne jednostki, należy upewnić się, że nie przekraczasz docelowych skalowalności dla pojedynczej partycji.  
* Zduplikowane jednostki można zachować spójne ze sobą przy użyciu EGT do aktualizacji dwóch kopii jednostki niepodzielnie. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz sekcję [Korzystanie z transakcji grupy jednostek](table-storage-design.md#entity-group-transactions).  
* Wartość używana dla **RowKey** musi być unikatowa dla każdej jednostki. Należy rozważyć użycie złożonych wartości klucza.  
* Dopełnienie wartości liczbowych w **RowKey** (na przykład identyfikator pracownika 000223), umożliwia poprawne sortowanie i filtrowanie na podstawie górnej i dolnej granicy.  
* Nie musi być konieczne duplikowanie wszystkich właściwości jednostki. Na przykład jeśli kwerendy, które wyszukują jednostki przy użyciu adresu e-mail w **RowKey** nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:

   ![Struktura jednostki pracowniczej](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Zazwyczaj lepiej jest przechowywać zduplikowane dane i upewnić się, że można pobrać wszystkie dane potrzebne za pomocą jednej kwerendy, niż użyć jednej kwerendy do zlokalizowania jednostki, a drugiej do wyszukynia wymaganych danych.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy, gdy klient musi pobrać jednostki w różnych zamówieniach sortowania i gdzie można zidentyfikować każdą jednostkę przy użyciu różnych unikatowych wartości. Należy jednak upewnić się, że nie przekraczasz limitów skalowalności partycji podczas wykonywania odnośnik jednostek przy użyciu różnych wartości **Klawisza Wiersza.**  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern)
* [Wzór klucza złożonego](#compound-key-pattern)
* Transakcje grupy jednostek
* [Praca z heterogenizami typów jednostek](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego między partycjami
Przechowuj wiele kopii każdej encji przy użyciu różnych wartości **RowKey** w oddzielnych partycjach lub w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwania i alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey.**  

### <a name="context-and-problem"></a>Kontekst i problem
Usługa tabel automatycznie indeksuje jednostki przy użyciu **partitionkey** i **RowKey** wartości. Dzięki temu aplikacja kliencka do pobierania jednostki wydajnie przy użyciu tych wartości. Na przykład za pomocą struktury tabeli pokazano poniżej, aplikacja kliencka może użyć kwerendy punktowej do pobrania jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator pracownika **(PartitionKey** i **RowKey** wartości). Klient może również pobierać jednostki posortowane według identyfikatora pracownika w każdym dziale.  

![Identyfikator pracownika](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Jeśli chcesz również mieć możliwość znalezienia jednostki pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, należy użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Dzieje się tak, ponieważ usługa tabel nie zapewnia indeksów pomocniczych. Ponadto nie ma opcji żądania listy pracowników posortowanych w innej kolejności niż kolejność **RowKey.**  

Przewidujesz dużą liczbę transakcji względem tych jednostek i chcesz zminimalizować ryzyko ograniczania przepustowości klienta przez usługę Tabel.  

### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdej kopii przy użyciu różnych **PartitionKey** i **RowKey** wartości. Jeśli przechowujesz jednostkę ze strukturami pokazanymi poniżej, można skutecznie pobierać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksu **dla PartitionKey**, "empid_" i "email_" umożliwiają określenie, który indeks ma być używany dla kwerendy.  

![Indeks podstawowy i indeks pomocniczy](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Następujące dwa kryteria filtrowania (jedno wyszukuje identyfikator pracownika i jedno wyszukuje według adresu e-mail) określają zarówno zapytania punktowe:  

* $filter=(PartitionKey eq 'empid_Sales') i (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') i (RowKey eqjonesj@contoso.com' )  

W przypadku kwerendy dla zakresu encji pracowników można określić zakres posortowany w kolejności identyfikatora pracownika lub zakres posortowany w kolejności adresów e-mail, odwołując się do encji z odpowiednim prefiksem w **klawiszie RowKey**.  

* Aby znaleźć wszystkich pracowników w dziale sprzedaży z identyfikatorem pracownika w zakresie **od 000100** do **000199** posortowane w kolejności identyfikatora pracownika, użyj zamówienia: $filter=(PartitionKey eq 'empid_Sales') i (RowKey ge '000100') i (RowKey le '000199')  
* Aby znaleźć wszystkich pracowników w dziale sprzedaży z adresem e-mail, który zaczyna się od "a" posortowane w adres e-mail use: $filter=(PartitionKey eq "email_Sales") i (RowKey ge 'a') i (RowKey lt 'b')  

Składnia filtru używana w powyższych przykładach pochodzi z interfejsu API REST usługi tabel, aby uzyskać więcej informacji, zobacz [Encje kwerend](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Można zachować zduplikowane jednostki ostatecznie zgodne ze sobą przy użyciu [ostatecznie spójne wzorca transakcji](#eventually-consistent-transactions-pattern) do obsługi jednostek indeksu podstawowego i pomocniczego.  
* Magazyn tabel jest stosunkowo tani w użyciu, więc koszty związane z przechowywaniem zduplikowanych danych nie powinny być głównym problemem. Jednak zawsze należy ocenić koszt projektu na podstawie wymagań przewidywanego magazynu i tylko dodać zduplikowane jednostki do obsługi zapytań, które będzie wykonywana aplikacja kliencka.  
* Wartość używana dla **RowKey** musi być unikatowa dla każdej jednostki. Należy rozważyć użycie złożonych wartości klucza.  
* Dopełnienie wartości liczbowych w **RowKey** (na przykład identyfikator pracownika 000223), umożliwia poprawne sortowanie i filtrowanie na podstawie górnej i dolnej granicy.  
* Nie musi być konieczne duplikowanie wszystkich właściwości jednostki. Na przykład jeśli kwerendy, które wyszukują jednostki przy użyciu adresu e-mail w **RowKey** nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:
  
   ![Jednostka pracownika (indeks pomocniczy)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Zazwyczaj lepiej jest przechowywać zduplikowane dane i upewnić się, że można pobrać wszystkie dane potrzebne za pomocą jednej kwerendy niż użyć jednej kwerendy, aby zlokalizować jednostkę przy użyciu indeksu pomocniczego, a drugą do wyszukiwania wymaganych danych w indeksie podstawowym.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy, gdy klient musi pobrać jednostki w różnych zamówieniach sortowania i gdzie można zidentyfikować każdą jednostkę przy użyciu różnych unikatowych wartości. Użyj tego wzorca, aby uniknąć przekroczenia limitów skalowalności partycji podczas wykonywania odnośnik jednostek przy użyciu różnych wartości **Klawisza Wiersza.**  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzór klucza złożonego](#compound-key-pattern)  
* Transakcje grupy jednostek  
* [Praca z heterogenizami typów jednostek](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Ostatecznie spójny wzorzec transakcji
Włącz ostatecznie spójne zachowanie w granicach partycji lub granice systemu magazynu przy użyciu kolejek platformy Azure.  

### <a name="context-and-problem"></a>Kontekst i problem
EGT włączyć transakcje niepodzielne w wielu jednostkach, które współużytkuje ten sam klucz partycji. Ze względu na wydajność i skalowalność można zdecydować o przechowywaniu jednostek, które mają wymagania spójności w oddzielnych partycjach lub w oddzielnym systemie magazynu: w takim scenariuszu nie można użyć EGTs do zachowania spójności. Na przykład może być wymagane, aby zachować spójność ostateczną między:  

* Jednostki przechowywane w dwóch różnych partycjach w tej samej tabeli, w różnych tabelach lub na różnych kontach magazynu.  
* Jednostka przechowywana w usłudze tabel i obiekt blob przechowywana w usłudze obiektów Blob.  
* Jednostka przechowywana w usłudze tabel i plik w systemie plików.  
* Jednostka przechowywana w usłudze tabel jeszcze indeksowana przy użyciu usługi Azure Cognitive Search.  

### <a name="solution"></a>Rozwiązanie
Za pomocą kolejek platformy Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną między co najmniej dwie partycje lub systemy magazynu.
Aby zilustrować to podejście, załóżmy, że masz wymóg, aby móc archiwizować stare jednostki pracownika. Jednostki starych pracowników rzadko są poszukiwane i powinny być wyłączone z wszelkich działań, które dotyczą obecnych pracowników. Aby zaimplementować to wymaganie, należy przechowywać aktywnych pracowników w **bieżącej** tabeli i starych pracowników w tabeli **Archiwum.** Archiwizacja pracownika wymaga usunięcia encji z **bieżącej** tabeli i dodania encji do tabeli **Archiwum,** ale nie można użyć EUWT do wykonania tych dwóch operacji. Aby uniknąć ryzyka, że błąd powoduje, że jednostka ma pojawić się w obu lub żadnej z tabel, operacja archiwum musi być ostatecznie spójne. Na poniższym diagramie sekwencji przedstawiono kroki opisane w tej operacji. Więcej szczegółów jest przewidziane dla ścieżek wyjątków w tekście po.  

![Rozwiązanie kolejek platformy Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient inicjuje operację archiwum, umieszczając komunikat w kolejce platformy Azure, w tym przykładzie do archiwizacji #456 pracowników. Rola procesu roboczego sonduje kolejkę dla nowych wiadomości; gdy go znajdzie, odczytuje wiadomość i pozostawia ukrytą kopię w kolejce. Rola procesu roboczego następnie pobiera kopię encji z **bieżącej** tabeli, wstawia kopię do tabeli **Archiwum,** a następnie usuwa oryginał z **bieżącej** tabeli. Na koniec, jeśli nie było żadnych błędów z poprzednich kroków, rola procesu roboczego usuwa ukrytą wiadomość z kolejki.  

W tym przykładzie krok 4 wstawia pracownika do tabeli **Archiwum.** Może dodać pracownika do obiektu blob w usłudze obiektów Blob lub pliku w systemie plików.  

### <a name="recovering-from-failures"></a>Odzyskiwanie po błędach
Ważne jest, aby operacje w krokach **4** i **5** muszą być *idempotentne* w przypadku, gdy rola procesu roboczego musi ponownie uruchomić operację archiwum. Jeśli używasz usługi tabela, dla kroku **4** należy użyć operacji "wstawić lub zastąpić"; w kroku **5** należy użyć operacji "usuń, jeśli istnieje" w używanej bibliotece klienta. Jeśli używasz innego systemu magazynu, należy użyć odpowiedniej operacji idempotent.  

Jeśli rola procesu roboczego nigdy nie kończy kroku **6**, a następnie po upływie limitu czasu komunikat pojawia się ponownie w kolejce gotowy do roli procesu roboczego, aby spróbować ponownie przetworzyć go. Rola procesu roboczego może sprawdzić, ile razy wiadomość w kolejce została odczytana i, jeśli to konieczne, oznaczyć ją jako "trująca" wiadomość do zbadania, wysyłając ją do osobnej kolejki. Aby uzyskać więcej informacji na temat odczytywania wiadomości kolejki i sprawdzania liczby de-kolejek, zobacz [Pobierz wiadomości](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Niektóre błędy z usługi tabeli i kolejki są błędy przejściowe, a aplikacja kliencka powinna zawierać odpowiednie logiki ponawiania ich obsługi.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie zapewnia izolacji transakcji. Na przykład klient może odczytać **bieżące** i **archiwum** tabel, gdy rola procesu roboczego był między krokami **4** i **5**i zobaczyć niespójny widok danych. Dane będą spójne po pewnym czasie.  
* Należy upewnić się, że kroki 4 i 5 są idempotentne w celu zapewnienia spójności ostatecznej.  
* Rozwiązanie można skalować przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, aby zagwarantować spójność ostateczną między jednostkami, które istnieją w różnych partycjach lub tabelach. Można rozszerzyć ten wzorzec, aby zapewnić spójność ostateczną dla operacji w usłudze tabel i usługi obiektów Blob i innych źródeł danych usługi Azure Storage, takich jak baza danych lub system plików.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek  
* [Scalanie lub zastępowanie](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacja transakcji jest ważna dla rozwiązania, należy rozważyć przeprojektowanie tabel, aby umożliwić korzystanie z EGT.  
> 
> 

## <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Obsługa jednostek indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

### <a name="context-and-problem"></a>Kontekst i problem
Usługa tabel automatycznie indeksuje jednostki przy użyciu **partitionkey** i **RowKey** wartości. Dzięki temu aplikacja kliencka do pobierania jednostki wydajnie przy użyciu kwerendy punktowej. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka może skutecznie pobrać jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator pracownika **(PartitionKey** i **RowKey**).  

![Jednostka pracownika](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Jeśli chcesz również mieć możliwość pobierania listy jednostek pracowników na podstawie wartości innej właściwości nieunikatowych, takich jak ich nazwisko, należy użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowania, a nie przy użyciu indeksu, aby wyszukać je bezpośrednio. Dzieje się tak, ponieważ usługa tabel nie zapewnia indeksów pomocniczych.  

### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska z strukturą jednostki pokazano powyżej, należy obsługiwać listy identyfikatorów pracowników. Jeśli chcesz pobrać jednostki pracownika o określonym nazwisku, takim jak Jones, należy najpierw zlokalizować listę identyfikatorów pracowników dla pracowników z Jones jako ich nazwisko, a następnie pobrać te jednostki pracownika. Istnieją trzy główne opcje przechowywania list identyfikatorów pracowników:  

* Użyj magazynu obiektów blob.  
* Tworzenie jednostek indeksu w tej samej partycji co jednostki pracownika.  
* Tworzenie jednostek indeksu w osobnej partycji lub tabeli.  

<u>Opcja #1: Użyj magazynu obiektów blob</u>  

Dla pierwszej opcji należy utworzyć obiekt blob dla każdego unikatowego nazwiska, a w każdym magazynie obiektów blob listę **PartitionKey** (dział) i **RowKey** (identyfikator pracownika) wartości dla pracowników, którzy mają to nazwisko. Podczas dodawania lub usuwania pracownika, należy upewnić się, że zawartość odpowiedniego obiektu blob jest ostatecznie zgodne z encjami pracownika.  

<u>Opcja #2:</u> Tworzenie encji indeksu w tej samej partycji  

W przypadku drugiej opcji należy użyć jednostek indeksu, które przechowują następujące dane:  

![Encja indeksu pracowników](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

**Właściwość EmployeeIDs** zawiera listę identyfikatorów pracowników dla pracowników z nazwiskiem przechowywanym w **klawiszie RowKey**.  

Poniższe kroki przedstawiają proces, który należy wykonać podczas dodawania nowego pracownika, jeśli używasz drugiej opcji. W tym przykładzie dodajemy pracownika o identyfikatorze 000152 i nazwisku Jones w dziale sprzedaży:  

1. Pobierz encję indeksu z wartością **PartitionKey** "Sprzedaż" i wartość **RowKey** "Jones". Zapisz eTag tej encji do użycia w kroku 2.  
2. Utwórz transakcję grupy jednostek (czyli operację wsadową), która wstawia nową encję pracownika (Wartość**PartitionKey** "Sprzedaż" i **Wartość RowKey** "000152") i aktualizuje encję indeksu (Wartość**PartitionKey** "Sprzedaż" i Wartość **RowKey** "Jones") przez dodanie nowego identyfikatora pracownika do listy w polu Identyfikatory pracowników. Aby uzyskać więcej informacji o transakcjach grupy jednostek, zobacz Transakcje grupy jednostek.  
3. Jeśli transakcja grupy jednostek nie powiedzie się z powodu błędu współbieżności optymistyczne (ktoś inny właśnie zmodyfikował jednostkę indeksu), a następnie należy rozpocząć od nowa w kroku 1 ponownie.  

Podobne podejście można użyć do usunięcia pracownika, jeśli używasz drugiej opcji. Zmiana nazwiska pracownika jest nieco bardziej złożona, ponieważ konieczne będzie wykonanie transakcji grupy jednostek, która aktualizuje trzy jednostki: jednostkę pracownika, encję indeksu dla starego nazwiska i jednostkę indeksu dla nowej nazwy nazwiska. Należy pobrać każdej jednostki przed wprowadzeniem jakichkolwiek zmian w celu pobrania wartości ETag, które następnie można użyć do wykonywania aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy trzeba wyszukać wszystkich pracowników z danym nazwiskiem w dziale, jeśli używasz drugiej opcji. W tym przykładzie szukamy wszystkich pracowników z nazwiskiem Jones w dziale sprzedaży:  

1. Pobierz encję indeksu z wartością **PartitionKey** "Sprzedaż" i wartość **RowKey** "Jones".  
2. Przejaśnienie listy identyfikatorów pracowników w polu Identyfikatory pracowników.  
3. Jeśli potrzebujesz dodatkowych informacji o każdym z tych pracowników (takich jak ich adresy e-mail), pobierz każdą z jednostek pracownika przy użyciu **partitionKey** wartości "Sprzedaż" i **RowKey** wartości z listy pracowników uzyskanych w kroku 2.  

<u>Opcja #3:</u> Tworzenie encji indeksu w oddzielnej partycji lub tabeli  

W przypadku trzeciej opcji należy użyć jednostek indeksu, które przechowują następujące dane:  

![Jednostka indeksu pracownika w oddzielnej partycji](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


**Właściwość EmployeeIDs** zawiera listę identyfikatorów pracowników dla pracowników z nazwiskiem przechowywanym w **klawiszie RowKey**.  

Przy trzeciej opcji nie można użyć EGT do zachowania spójności, ponieważ jednostki indeksu znajdują się w oddzielnej partycji od jednostek pracownika. Upewnij się, że jednostki indeksu są po pewnym czasie zgodne z encjami pracownika.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań do pobierania pasujących jednostek: jeden do kwerendy jednostek indeksu, aby uzyskać listę wartości **RowKey,** a następnie kwerendy do pobrania każdej jednostki na liście.  
* Biorąc pod uwagę, że poszczególne jednostki ma maksymalny rozmiar 1 MB, opcja #2 i opcja #3 w rozwiązaniu zakłada, że lista identyfikatorów pracowników dla danego nazwiska nigdy nie jest większa niż 1 MB. Jeśli lista identyfikatorów pracowników może być większa niż 1 MB w rozmiarze, użyj opcji #1 i przechowywać dane indeksu w magazynie obiektów blob.  
* Jeśli używasz opcji #2 (przy użyciu EGTs do obsługi dodawania i usuwania pracowników i zmiany nazwiska pracownika) należy ocenić, czy liczba transakcji zbliży się do limitów skalowalności w danej partycji. W takim przypadku należy rozważyć ostatecznie spójne rozwiązanie (opcja #1 lub opcja #3), która używa kolejek do obsługi żądań aktualizacji i umożliwia przechowywanie jednostek indeksu w osobnej partycji z jednostek pracownika.  
* Opcja #2 w tym rozwiązaniu zakłada, że chcesz wyszukać według nazwiska w dziale: na przykład chcesz pobrać listę pracowników o nazwisku Jones w dziale sprzedaży. Jeśli chcesz mieć możliwość wyszukiwania wszystkich pracowników z nazwiskiem Jones w całej organizacji, użyj opcji #1 lub opcji #3.
* Można zaimplementować rozwiązanie oparte na kolejce, które zapewnia spójność ostateczną (zobacz [Wzorzec ostatecznie spójne transakcje,](#eventually-consistent-transactions-pattern) aby uzyskać więcej szczegółów).  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec służy do wyszukiwania zestawu jednostek, które mają wspólną wartość właściwości, na przykład wszystkich pracowników o nazwisku Jones.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzór klucza złożonego](#compound-key-pattern)  
* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  
* Transakcje grupy jednostek  
* [Praca z heterogenizami typów jednostek](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Wzór denormalizacji
Połącz powiązane dane w jednej jednostce, aby umożliwić pobranie wszystkich potrzebnych danych za pomocą zapytania pojedynczego punktu.  

### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizuje się dane w celu usunięcia duplikacji, co powoduje, że kwerendy pobierają dane z wielu tabel. Jeśli normalizujesz dane w tabelach platformy Azure, należy wykonać wiele rund podróży z klienta do serwera, aby pobrać powiązane dane. Na przykład ze strukturą tabeli pokazano poniżej, potrzebne są dwa rundy, aby pobrać szczegóły dla działu: jeden do pobrania jednostki działu, która zawiera identyfikator menedżera, a następnie inne żądanie pobrania szczegółów menedżera w jednostce pracownika.  

![Jednostka działu i jednostka pracownik](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Rozwiązanie
Zamiast przechowywać dane w dwóch oddzielnych jednostkach, zdenormalizuj dane i przechowuj kopię szczegółów menedżera w jednostce działu. Przykład:  

![Jednostka działu](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Z jednostek działów przechowywanych z tych właściwości, można teraz pobrać wszystkie szczegóły potrzebne o dziale przy użyciu kwerendy punktowej.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje pewne obciążenie kosztowe związane z przechowywaniem niektórych danych dwa razy. Korzyści z wydajności (wynikające z mniejszej liczby żądań do usługi magazynu) zazwyczaj przewyższają marginalny wzrost kosztów magazynowania (a koszt ten jest częściowo równoważony przez zmniejszenie liczby transakcji wymaganych do pobrania szczegółów działu ).  
* Należy zachować spójność dwóch jednostek, które przechowują informacje o menedżerach. Problem spójności można rozwiązać za pomocą EGTs do aktualizacji wielu jednostek w jednej transakcji atomowej: w tym przypadku jednostki działu i jednostki pracownika dla menedżera działu są przechowywane w tej samej partycji.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy często trzeba wyszukać powiązane informacje. Ten wzorzec zmniejsza liczbę zapytań, które klient musi wykonać, aby pobrać dane, których wymaga.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzór klucza złożonego](#compound-key-pattern)  
* Transakcje grupy jednostek  
* [Praca z heterogenizami typów jednostek](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Wzór klucza złożonego
Użyj złożonych wartości **RowKey,** aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą kwerendy pojedynczego punktu.  

### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych jest naturalne, aby użyć sprzężeń w kwerendach do zwrócenia powiązanych fragmentów danych do klienta w jednej kwerendzie. Na przykład można użyć identyfikatora pracownika, aby wyszukać listę powiązanych jednostek, które zawierają dane wydajności i przeglądu dla tego pracownika.  

Załóżmy, że są przechowywane jednostki pracowników w usłudze tabel przy użyciu następującej struktury:  

![Struktura jednostki pracowniczej](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Należy również przechowywać dane historyczne dotyczące opinii i wydajności dla każdego roku pracownik pracował dla twojej organizacji i musisz mieć dostęp do tych informacji według roku. Jedną z opcji jest utworzenie innej tabeli, która przechowuje jednostki o następującej strukturze:  

![Alternatywna struktura jednostki pracowniczej](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Należy zauważyć, że dzięki takiemu podejściu można zdecydować się na zduplikowanie niektórych informacji (takich jak imię i nazwisko) w nowej jednostce, aby umożliwić pobieranie danych za pomocą jednego żądania. Jednak nie można zachować silną spójność, ponieważ nie można użyć EGT do aktualizacji dwóch jednostek niepodzielnie.  

### <a name="solution"></a>Rozwiązanie
Przechowuj nowy typ encji w oryginalnej tabeli przy użyciu elementów o następującej strukturze:  

![Rozwiązanie dla struktury jednostki pracowniczej](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Zwróć uwagę, jak **RowKey** jest teraz klucz złożony składa się z identyfikatora pracownika i roku danych przeglądu, który umożliwia pobieranie wydajności pracownika i przeglądać dane za pomocą jednego żądania dla jednej jednostki.  

W poniższym przykładzie opisano, jak można pobrać wszystkie dane przeglądu dla określonego pracownika (na przykład pracownika 000123 w dziale sprzedaży):  

$filter=(PartitionKey eq 'Sales') i (RowKey ge 'empid_000123') oraz (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego znaku separatora, który ułatwia analizę wartości **Klawisza Wiersza:** na przykład **000123_2012**.  
* Są również przechowywania tej jednostki w tej samej partycji jako inne jednostki, które zawierają powiązane dane dla tego samego pracownika, co oznacza, że można użyć EGTs do utrzymania silnej spójności.
* Należy rozważyć, jak często będzie kwerendy danych, aby ustalić, czy ten wzorzec jest odpowiedni.  Na przykład jeśli dostęp do danych przeglądu rzadko i głównych danych pracowników często należy zachować je jako oddzielne jednostki.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec służy do przechowywania jednej lub więcej powiązanych jednostek, które często kwerendy.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek  
* [Praca z heterogenizami typów jednostek](#working-with-heterogeneous-entity-types)  
* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Wzór ogona dziennika
Pobieranie *n* jednostek ostatnio dodane do partycji przy użyciu **RowKey** wartość, która sortuje w odwrotnej kolejności daty i godziny.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymaganiem jest możliwość pobrania ostatnio utworzonych encji, na przykład 10 najnowszych oświadczeń o wydatkach złożonych przez pracownika. Kwerendy tabeli obsługują operację kwerendy **$top,** aby zwrócić pierwsze *n* jednostki z zestawu: nie ma równoważnej operacji kwerendy, aby zwrócić ostatnie n jednostki w zestawie.  

### <a name="solution"></a>Rozwiązanie
Przechowywać jednostki przy użyciu **RowKey,** który naturalnie sortuje w odwrotnej kolejności daty/godziny przy użyciu więc najnowszy wpis jest zawsze pierwszy w tabeli.  

Na przykład, aby móc pobrać 10 najnowszych oświadczeń o wydatkach przesłanych przez pracownika, można użyć odwrotnej wartości znacznika uzyskanej od bieżącej daty/godziny. Poniższy przykład kodu Języka C# pokazuje jeden sposób, aby utworzyć odpowiednią wartość "odwróconych znaczników" dla **RowKey,** który sortuje od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić do wartości daty i godziny przy użyciu następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Kwerenda tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zapewnić, że wartość ciągu zostanie sebrana zgodnie z oczekiwaniami, należy zaznaczyć wartość znacznika wstecznego z zerami wiodącymi.  
* Należy pamiętać o cele skalowalności na poziomie partycji. Należy uważać, aby nie tworzyć partycji hot spot.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec służy, gdy trzeba uzyskać dostęp do jednostek w odwrotnej kolejności daty/godziny lub gdy trzeba uzyskać dostęp do ostatnio dodanych jednostek.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Poprzedzaj / dołączanie anty-wzór](#prepend-append-anti-pattern)  
* [Pobieranie encji](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Wzorzec usuwania dużej objętości
Włącz usunięcie dużej liczby jednostek, przechowując wszystkie jednostki do jednoczesnego usunięcia w ich osobnej tabeli; usuwasz encje, usuwając tabelę.  

### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji usuwa stare dane, które nie muszą być już dostępne dla aplikacji klienckiej lub że aplikacja została zarchiwizowana na innym nośniku magazynu. Zazwyczaj identyfikujesz takie dane według daty: na przykład masz obowiązek usunąć rekordy wszystkich żądań logowania, które mają więcej niż 60 dni.  

Jednym z możliwych projektów jest użycie daty i godziny żądania logowania w **RowKey:**  

![Data i godzina próby logowania](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Takie podejście pozwala uniknąć hotspotów partycji, ponieważ aplikacja może wstawiać i usuwać jednostki logowania dla każdego użytkownika w osobnej partycji. Jednak takie podejście może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek, ponieważ najpierw należy wykonać skanowanie tabeli w celu zidentyfikowania wszystkich jednostek do usunięcia, a następnie należy usunąć każdą starą jednostkę. Można zmniejszyć liczbę rund do serwera wymagane do usunięcia starych jednostek przez przetwarzanie wielu żądań usuwania do EGTs.  

### <a name="solution"></a>Rozwiązanie
Użyj osobnej tabeli dla każdego dnia prób logowania. Można użyć projektu encji powyżej, aby uniknąć hotspotów podczas wstawiania encji, a usuwanie starych encji jest teraz po prostu kwestią usuwania jednej tabeli każdego dnia (pojedynczej operacji przechowywania) zamiast znajdowania i usuwania setek i tysięcy pojedynczych loginów.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy projekt obsługuje inne sposoby aplikacji będzie używać danych, takich jak wyszukywanie określonych jednostek, łączenie z innymi danymi lub generowanie informacji zbiorczych?  
* Czy projekt uniknąć hot spotów podczas wstawiania nowych elementów?  
* Spodziewaj się opóźnienia, jeśli chcesz ponownie użyć tej samej nazwy tabeli po jej usunięciu. Lepiej zawsze używać unikatowych nazw tabel.  
* Spodziewaj się niektórych ograniczania przepustowości podczas pierwszego użycia nowej tabeli, podczas gdy usługa tabel uczy się wzorców dostępu i dystrybuuje partycje między węzłami. Należy wziąć pod uwagę, jak często trzeba tworzyć nowe tabele.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy masz dużą liczbę jednostek, które należy usunąć w tym samym czasie.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek
* [Modyfikowanie jednostek](#modifying-entities)  

## <a name="data-series-pattern"></a>Wzorzec serii danych
Przechowuj pełną serię danych w jednej jednostce, aby zminimalizować liczbę żądań.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowym scenariuszem jest dla aplikacji do przechowywania serii danych, które zazwyczaj musi pobrać wszystkie na raz. Na przykład aplikacja może rejestrować liczbę wiadomości błyskawicznych, które każdy pracownik wysyła co godzinę, a następnie użyć tych informacji do wykreślenia liczby wiadomości wysłanych przez każdego użytkownika w ciągu ostatnich 24 godzin. Jednym z projektów może być przechowywanie 24 jednostek dla każdego pracownika:  

![Przechowywanie 24 jednostek dla każdego pracownika](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Za pomocą tego projektu można łatwo zlokalizować i zaktualizować jednostkę, aby zaktualizować dla każdego pracownika, gdy aplikacja musi zaktualizować wartość liczby wiadomości. Jednak aby pobrać informacje, aby wykreślić wykres działania dla poprzednich 24 godzin, należy pobrać 24 jednostek.  

### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z oddzielną właściwością do przechowywania liczby wiadomości dla każdej godziny:  

![Encja statystyk wiadomości](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Za pomocą tego projektu można użyć operacji scalania, aby zaktualizować liczbę wiadomości dla pracownika przez określoną godzinę. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli pełna seria danych nie mieści się w jednej jednostce (jednostka może mieć maksymalnie 252 właściwości), należy użyć alternatywnego magazynu danych, takiego jak obiekt blob.  
* Jeśli masz wielu klientów aktualizowanie jednostki jednocześnie, należy użyć **ETag** do zaimplementowania optymistycznej współbieżności. Jeśli masz wielu klientów, może wystąpić wysoki spór.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba zaktualizować i pobrać serię danych skojarzonych z poszczególnych jednostek.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Szyk dużych elementów](#large-entities-pattern)  
* [Scalanie lub zastępowanie](#merge-or-replace)  
* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern) (jeśli przechowujesz serię danych w obiekcie blob)  

## <a name="wide-entities-pattern"></a>Szeroki szyk elementów
Wiele jednostek fizycznych służy do przechowywania jednostek logicznych o więcej niż 252 właściwościach.  

### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka może mieć nie więcej niż 252 właściwości (z wyłączeniem właściwości systemu obowiązkowego) i nie może przechowywać więcej niż 1 MB danych w sumie. W relacyjnej bazie danych zazwyczaj można uzyskać zaokrąglenia wszelkich limitów rozmiaru wiersza, dodając nową tabelę i wymuszając relację 1-to-1 między nimi.  

### <a name="solution"></a>Rozwiązanie
Za pomocą usługi Tabela, można przechowywać wiele jednostek do reprezentowania jednego dużego obiektu biznesowego z więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości błyskawicznych wysłanych przez każdego pracownika w ciągu ostatnich 365 dni, można użyć następującego projektu, który używa dwóch jednostek z różnymi schematami:  

![Wiele encji](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Jeśli trzeba wprowadzić zmianę, która wymaga aktualizacji obu jednostek, aby zachować je zsynchronizowane ze sobą, można użyć EUWT. W przeciwnym razie można użyć pojedynczej operacji scalania, aby zaktualizować liczbę wiadomości dla określonego dnia. Aby pobrać wszystkie dane dla pojedynczego pracownika, należy pobrać obie jednostki, co można zrobić z dwóch wydajnych żądań, które używają zarówno **PartitionKey** i **RowKey** wartości.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Pobieranie pełnej jednostki logicznej obejmuje co najmniej dwie transakcje magazynu: jedną do pobrania każdej jednostki fizycznej.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy używać do przechowywania jednostek, których rozmiar lub liczba właściwości przekracza limity dla poszczególnych jednostek w usłudze tabel.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek
* [Scalanie lub zastępowanie](#merge-or-replace)

## <a name="large-entities-pattern"></a>Szyk dużych elementów
Użyj magazynu obiektów blob do przechowywania dużych wartości właściwości.  

### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka nie może przechowywać łącznie więcej niż 1 MB danych. Jeśli jedna lub kilka właściwości przechowywania wartości, które powodują całkowity rozmiar jednostki, aby przekroczyć tę wartość, nie można przechowywać całą jednostkę w usłudze tabel.  

### <a name="solution"></a>Rozwiązanie
Jeśli jednostka przekracza 1 MB w rozmiarze, ponieważ co najmniej jedna właściwości zawierają dużą ilość danych, można przechowywać dane w usłudze obiektów Blob, a następnie przechowywać adres obiektu blob we właściwości w jednostce. Na przykład można przechowywać zdjęcie pracownika w magazynie obiektów blob i przechowywać łącze do zdjęcia we właściwości **Zdjęcie** jednostki pracownika:  

![Zdjęcie właściwości](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczną między jednostką w usłudze tabeli a danymi w usłudze obiektów Blob, użyj [wzorca Ostatecznie spójne transakcje](#eventually-consistent-transactions-pattern) do obsługi jednostek.
* Pobieranie pełnej jednostki obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania jednostki i jeden do pobrania danych obiektu blob.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Ten wzorzec należy używać, gdy trzeba przechowywać jednostki, których rozmiar przekracza limity dla poszczególnych jednostek w usłudze tabel.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Ostatecznie spójny wzorzec transakcji](#eventually-consistent-transactions-pattern)  
* [Szeroki szyk elementów](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Poprzedzaj/dołączaj anty-wzór
Zwiększ skalowalność, gdy masz dużą ilość wstawia, rozprzestrzeniając wstawia na wielu partycjach.  

### <a name="context-and-problem"></a>Kontekst i problem
Poprzedzające lub dołączanie jednostek do przechowywanych jednostek zazwyczaj powoduje, że aplikacja dodaje nowe jednostki do pierwszej lub ostatniej partycji sekwencji partycji. W takim przypadku wszystkie wstawia w danym momencie odbywają się w tej samej partycji, tworząc hotspot, który uniemożliwia usługi tabel równoważenia obciążenia wstawia w wielu węzłach i ewentualnie powodując, że aplikacja trafi cele skalowalności dla Partycji. Na przykład jeśli masz aplikację, która rejestruje dostęp do sieci i zasobów przez pracowników, struktura jednostki, jak pokazano poniżej, może spowodować, że partycja bieżącej godziny stanie się punktem aktywnym, jeśli liczba transakcji osiągnie cel skalowalności dla pojedyncza partycja:  

![Struktura encji](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Rozwiązanie
Następująca alternatywna struktura encji pozwala uniknąć hotspotu na dowolnej określonej partycji, ponieważ aplikacja rejestruje zdarzenia:  

![Alternatywna struktura encji](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Zwróć uwagę w tym przykładzie, jak **PartitionKey** i **RowKey** są klucze złożone. **PartitionKey** używa zarówno działu, jak i identyfikatora pracownika do dystrybucji rejestrowania na wielu partycjach.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy alternatywna struktura klucza, która pozwala uniknąć tworzenia gorących partycji na wstawianiach skutecznie obsługuje zapytania, które sprawia, że aplikacja kliencka?  
* Czy przewidywana liczba transakcji oznacza, że prawdopodobnie osiągniesz cele skalowalności dla pojedynczej partycji i zostanie ograniczona przez usługę magazynu?  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać prepend/dołącz anty-wzorzec, gdy ilość transakcji może spowodować ograniczanie przez usługę magazynu podczas uzyskiwania dostępu do partycji gorącej.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzór klucza złożonego](#compound-key-pattern)  
* [Wzór ogona dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Rejestrowanie danych anty-wzór
Zazwyczaj należy użyć usługi obiektów Blob zamiast usługi Tabela do przechowywania danych dziennika.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowym przypadkiem użycia danych dziennika jest pobranie wyboru wpisów dziennika dla określonego zakresu daty/godziny: na przykład chcesz znaleźć wszystkie komunikaty o błędach i wiadomościach krytycznych, które aplikacja zarejestrowała między 15:04 a 15:06 w określonym dniu. Nie chcesz używać daty i godziny komunikatu dziennika do określenia partycji, na której zapisujesz jednostki dziennika: powoduje to gorącą partycję, ponieważ w danym momencie wszystkie jednostki dziennika będą miały tę samą wartość **PartitionKey** (zobacz sekcję [Prepend/append anti-pattern](#prepend-append-anti-pattern)). Na przykład następujący schemat jednostki dla komunikatu dziennika powoduje gorącą partycję, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika na partycji dla bieżącej daty i godziny:  

![Jednostka dziennika wiadomości](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

W tym przykładzie **RowKey** zawiera datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są przechowywane posortowane w kolejności daty/godziny i zawiera identyfikator wiadomości w przypadku wielu komunikatów dziennika współużytkować tę samą datę i godzinę.  

Innym podejściem jest użycie **PartitionKey,** który zapewnia, że aplikacja zapisuje komunikaty w zakresie partycji. Na przykład jeśli źródło komunikatu dziennika zapewnia sposób dystrybucji wiadomości na wielu partycjach, można użyć następującego schematu jednostki:  

![Jednostka alternatywnego komunikatu dziennika](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Jednak problem z tym schematem jest to, że aby pobrać wszystkie komunikaty dziennika dla określonego przedziału czasu należy przeszukać każdą partycję w tabeli.

### <a name="solution"></a>Rozwiązanie
W poprzedniej sekcji podkreślono problem podczas próby użycia usługi Tabela do przechowywania wpisów dziennika i zaproponowano dwa, niezadowalające projekty. Jedno rozwiązanie doprowadziło do gorącej partycji z ryzykiem niskiej wydajności pisania komunikatów dziennika; inne rozwiązanie spowodowało niską wydajność kwerendy ze względu na wymóg skanowania każdej partycji w tabeli, aby pobrać komunikaty dziennika dla określonego przedziału czasu. Magazyn obiektów Blob oferuje lepsze rozwiązanie dla tego typu scenariusza i tak jest, jak usługa Azure Storage Analytics przechowuje dane dziennika, które zbiera.  

W tej sekcji opisano, jak usługa Storage Analytics przechowuje dane dziennika w magazynie obiektów blob jako ilustrację tego podejścia do przechowywania danych, które zazwyczaj kwerendy według zakresu.  

Usługa Storage Analytics przechowuje komunikaty dziennika w formacie rozdzielanym w wielu obiektach blob. Format rozdzielany ułatwia aplikacji klienckiej analizowanie danych w komunikacie dziennika.  

Usługa Storage Analytics używa konwencji nazewnictwa obiektów blob, która umożliwia lokalizowanie obiektów blob (lub obiektów blob), które zawierają komunikaty dziennika, dla których wyszukujesz. Na przykład obiekt blob o nazwie "queue/2014/07/31/1800/000001.log" zawiera komunikaty dziennika, które odnoszą się do usługi kolejki dla godziny rozpoczynającej się o godzinie 18:00 w dniu 31 lipca 2014 r. "000001" wskazuje, że jest to pierwszy plik dziennika dla tego okresu. Usługa Storage Analytics rejestruje również sygnatury czasowe wiadomości pierwszego i ostatniego dziennika przechowywane w pliku jako część metadanych obiektu blob. Interfejs API dla magazynu obiektów blob umożliwia lokalizowanie obiektów blob w kontenerze na podstawie prefiksu nazwy: aby zlokalizować wszystkie obiekty blob, które zawierają dane dziennika kolejki dla godziny rozpoczynającej się od 18:00, można użyć prefiksu "queue/2014/07/31/1800".  

Usługa Analizy magazynu buforuje wiadomości dziennika wewnętrznie, a następnie okresowo aktualizuje odpowiedni obiekt blob lub tworzy nowy z najnowszą partią wpisów dziennika. Zmniejsza to liczbę zapisów, które musi wykonać w usłudze obiektów blob.  

Jeśli implementujesz podobne rozwiązanie we własnej aplikacji, należy rozważyć, jak zarządzać kompromisem między niezawodnością (pisanie każdego wpisu dziennika do magazynu obiektów blob, jak to się dzieje) a kosztem i skalowalnością (buforowanie aktualizacji w aplikacji i pisanie do przechowywania obiektów blob w partiach).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Przy podejmowaniu decyzji o przechowywaniu danych dziennika należy wziąć pod uwagę następujące kwestie:  

* Jeśli utworzysz projekt tabeli, który pozwala uniknąć potencjalnych gorących partycji, może się okazać, że nie można uzyskać dostępu do danych dziennika wydajnie.  
* Aby przetworzyć dane dziennika, klient często musi załadować wiele rekordów.  
* Chociaż dane dziennika jest często strukturalnych, magazynu obiektów blob może być lepszym rozwiązaniem.  

## <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, o których należy pamiętać podczas implementowania wzorców opisanych w poprzednich sekcjach. Większość z tej sekcji używa przykładów napisanych w języku C#, które używają biblioteki klienta magazynu (wersja 4.3.0 w momencie pisania).  

## <a name="retrieving-entities"></a>Pobieranie encji
Jak wspomniano w sekcji Projektowanie do wykonywania zapytań, najbardziej efektywną kwerendą jest kwerenda punktowa. Jednak w niektórych scenariuszach może być konieczne pobranie wielu jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek przy użyciu biblioteki klienta magazynu.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Wykonywanie kwerendy punktowej przy użyciu biblioteki klienta magazynu
Najprostszym sposobem wykonania kwerendy punktowej jest użycie operacji **Pobierz** tabelę, jak pokazano w następującym fragmentie kodu Języka C#, który pobiera jednostkę z **PartitionKey** wartości "Sales" i **RowKey** o wartości "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekuje jednostki, która pobiera być typu **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Pobieranie wielu jednostek przy użyciu linq
LinQ służy do pobierania wielu jednostek z usługi tabel podczas pracy z biblioteką standardową tabeli Microsoft Azure Cosmos. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Aby poniższe przykłady działały, musisz uwzględnić obszary nazw:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable jest CloudTable obiektu, który implementuje\<CreateQuery ITableEntity>(), który zwraca\<TableQuery ITableEntity>. Obiekty tego typu implementują IQueryable i zezwalają na używanie zarówno wyrażeń zapytania LINQ, jak i składni notacji kropkowej.

Pobieranie wielu jednostek i można osiągnąć przez określenie kwerendy z **klauzuli where.** Aby uniknąć skanowania tabeli, należy zawsze dołączyć **PartitionKey** wartość w where klauzuli i, jeśli to możliwe **RowKey** wartość, aby uniknąć skanowania tabeli i partycji. Usługa tabel obsługuje ograniczony zestaw operatorów porównania (większy niż, większy lub równy, mniejszy niż, mniejszy lub równy, równy i nie równy) do użycia w klauzuli where. 

Poniższy fragment kodu Języka C# znajduje wszystkich pracowników, których nazwisko zaczyna się od "B" (przy założeniu, że **RowKey** przechowuje nazwisko) w dziale sprzedaży (zakładając, że **PartitionKey** przechowuje nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Zwróć uwagę, jak kwerenda określa zarówno **RowKey** i **PartitionKey,** aby zapewnić lepszą wydajność.  

Poniższy przykładowy kod przedstawia równoważne funkcje bez użycia składni LINQ:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Przykład zagnieżdża wiele **CombineFilters** metody, aby uwzględnić trzy warunki filtru.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Pobieranie dużej liczby jednostek z kwerendy
Optymalna kwerenda zwraca pojedynczą jednostkę na podstawie wartości **PartitionKey** i **Wartości Klawisza Wiersza.** Jednak w niektórych scenariuszach może być wymagane, aby zwrócić wiele jednostek z tej samej partycji lub nawet z wielu partycji.  

Zawsze należy w pełni przetestować wydajność aplikacji w takich scenariuszach.  

Kwerenda względem usługi tabel może zwracać maksymalnie 1000 jednostek w tym samym czasie i może być wykonywana przez maksymalnie pięć sekund. Jeśli zestaw wyników zawiera więcej niż 1000 jednostek, jeśli kwerenda nie została ukończona w ciągu pięciu sekund lub jeśli kwerenda przekroczy granicę partycji, usługa Tabela zwraca token kontynuacji, aby umożliwić aplikacji klienckiej żądanie następnego zestawu jednostek. Aby uzyskać więcej informacji na temat działania tokenów kontynuacji, zobacz [Limit czasu kwerendy i Podział na strony](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Jeśli używasz biblioteki klienta magazynu, może ona automatycznie obsługiwać tokeny kontynuacji, ponieważ zwraca jednostki z usługi Tabela. Następujący przykład kodu Języka C# przy użyciu biblioteki klienta magazynu automatycznie obsługuje tokeny kontynuacji, jeśli usługa tabel zwraca je w odpowiedzi:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Następujący kod Języka C# obsługuje tokeny kontynuacji jawnie:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Za pomocą tokenów kontynuacji jawnie, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład jeśli aplikacja kliencka umożliwia użytkownikom stronę za pośrednictwem jednostek przechowywanych w tabeli, użytkownik może zdecydować, aby nie przeglądać wszystkich jednostek pobranych przez kwerendę, więc aplikacja będzie używać tylko token kontynuacji do pobierania następnego segmentu, gdy użytkownik zakończył stronicowanie przez wszystkie jednostki w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Umożliwia ograniczenie ilości danych do pobrania z usługi Tabela i przenoszenia przez sieć.  
* Umożliwia wykonywanie asynchroniiowych we/wy w .NET.  
* Umożliwia serializowanie tokenu kontynuacji do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zazwyczaj zwraca segment zawierający 1000 jednostek, chociaż może być mniejsza. Jest to również w przypadku, jeśli można ograniczyć liczbę wpisów kwerendy zwraca za pomocą **Take** do zwrócenia pierwszych n jednostek, które odpowiadają kryteriom odnośnika: usługa tabel może zwrócić segment zawierający mniej niż n jednostek wraz z tokenem kontynuacji, aby umożliwić pobranie pozostałych jednostek.  
> 
> 

Poniższy kod Języka C# pokazuje, jak zmodyfikować liczbę jednostek zwróconych wewnątrz segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projekcja po stronie serwera
Pojedyncza jednostka może mieć maksymalnie 255 właściwości i mieć rozmiar do 1 MB. Podczas kwerendy tabeli i pobierania jednostek, może nie być potrzebne wszystkie właściwości i można uniknąć niepotrzebnego przesyłania danych (w celu zmniejszenia opóźnienia i kosztów). Projekcja po stronie serwera umożliwia przeniesienie tylko potrzebnych właściwości. Poniższy przykład jest pobiera tylko **Email** właściwości (wraz z **PartitionKey**, **RowKey**, **Sygnatura czasowa**i **ETag**) z jednostek wybranych przez kwerendę.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Zwróć uwagę, jak **RowKey** wartość jest dostępna, mimo że nie został uwzględniony na liście właściwości do pobrania.  

## <a name="modifying-entities"></a>Modyfikowanie jednostek
Biblioteka klienta magazynu umożliwia modyfikowanie jednostek przechowywanych w usłudze tabel przez wstawianie, usuwanie i aktualizowanie jednostek. Egtów służy do partii wielu wstawia, aktualizacji i usuwania operacji razem, aby zmniejszyć liczbę rund wymagane i poprawić wydajność rozwiązania.  

Wyjątki zgłaszane podczas biblioteki klienta magazynu wykonuje EGT zazwyczaj obejmują indeks jednostki, która spowodowała niepowodzenie partii. Jest to przydatne podczas debugowania kodu, który używa EGTs.  

Należy również rozważyć, jak projekt wpływa na sposób, w jaki aplikacja kliencka obsługuje operacje współbieżności i aktualizacji.  

### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa tabel implementuje optymistyczne kontrole współbieżności na poziomie poszczególnych jednostek dla **operacji Wstaw,** **Scalanie**i **Usuń,** chociaż klient może wymusić na usłudze tabel ominięcie tych kontroli. Aby uzyskać więcej informacji na temat zarządzania współbieżnością usługi tabel, zobacz [Zarządzanie współbieżnością w usłudze Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Scalanie lub zastępowanie
Metoda **Replace** of the **TableOperation** klasy zawsze zastępuje pełną jednostkę w usłudze tabeli. Jeśli właściwość nie zostanie uwzględnina w żądaniu, gdy ta właściwość istnieje w encji przechowywanej, żądanie usuwa tę właściwość z przechowywanej jednostki. Jeśli nie chcesz usunąć właściwości jawnie z przechowywanej jednostki, należy uwzględnić wszystkie właściwości w żądaniu.  

Za pomocą metody **scalania** **tableoperation** klasy, aby zmniejszyć ilość danych wysyłanych do usługi Tabela, gdy chcesz zaktualizować jednostkę. Merge **Merge** Metoda zastępuje wszystkie właściwości w jednostce przechowywane z wartości właściwości z jednostki zawarte w żądaniu, ale pozostawia nienaruszone wszelkie właściwości w przechowywanej jednostki, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz duże jednostki i wystarczy zaktualizować tylko niewielką liczbę właściwości w żądaniu.  

> [!NOTE]
> Aby **zastąpić** i **scalić** metody nie powiedzie się, jeśli jednostka nie istnieje. Alternatywnie można użyć **InsertOrReplace** i **InsertOrMerge** metody, które tworzą nową jednostkę, jeśli nie istnieje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Praca z heterogenizami typów jednostek
Usługa tabel jest magazynem tabel *bez schematu,* co oznacza, że pojedyncza tabela może przechowywać jednostki wielu typów, zapewniając dużą elastyczność w projekcie. W poniższym przykładzie przedstawiono tabelę przechowującą zarówno jednostki pracowników, jak i działów:  

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

Każda jednostka musi nadal mieć **partitionkey**, **RowKey**i **timestamp** wartości, ale może mieć dowolny zestaw właściwości. Ponadto nic nie wskazuje na typ jednostki, chyba że zdecydujesz się gdzieś przechowywać te informacje. Istnieją dwie opcje identyfikacji typu jednostki:  

* Przedwładnienie typu jednostki do **rowkey** (lub ewentualnie **PartitionKey**). Na przykład **EMPLOYEE_000123** lub **DEPARTMENT_SALES** jako wartości **RowKey.**  
* Użyj osobnej właściwości, aby zarejestrować typ encji, jak pokazano w poniższej tabeli.  

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

Pierwsza opcja, poprzedzająca typ jednostki do **RowKey,** jest przydatna, jeśli istnieje możliwość, że dwie jednostki różnych typów mogą mieć tę samą wartość klucza. Grupuje również jednostki tego samego typu razem w partycji.  

Techniki omówione w tej sekcji są szczególnie istotne dla dyskusji [Dziedziczenie relacji](table-storage-design-modeling.md#inheritance-relationships) wcześniej w tym przewodniku w artykule [Modelowanie relacji](table-storage-design-modeling.md).  

> [!NOTE]
> Należy rozważyć uwzględnienie numeru wersji w wartości typu jednostki, aby umożliwić aplikacjom klienckim ewoluowanie obiektów POCO i pracę z różnymi wersjami.  
> 
> 

W dalszej części tej sekcji opisano niektóre funkcje w bibliotece klienta magazynu, które ułatwiają pracę z wieloma typami jednostek w tej samej tabeli.  

### <a name="retrieving-heterogeneous-entity-types"></a>Pobieranie heterogenicznych typów jednostek
Jeśli używasz biblioteki klienta magazynu, masz trzy opcje pracy z wieloma typami jednostek.  

Jeśli znasz typ jednostki przechowywanej przy użyciu określonych wartości **RowKey** i **PartitionKey,** możesz określić typ jednostki podczas pobierania encji, jak pokazano w poprzednich dwóch przykładach, które pobierają jednostki typu **EmployeeEntity**: [Wykonywanie kwerendy punktowej przy użyciu biblioteki klienta magazynu](#executing-a-point-query-using-the-storage-client-library) i pobieranie wielu [jednostek przy użyciu LINQ](#retrieving-multiple-entities-using-linq).  

Drugą opcją jest użycie typu **DynamicTableEntity** (worka właściwości) zamiast konkretnego typu jednostki POCO (ta opcja może również poprawić wydajność, ponieważ nie ma potrzeby serializacji i deserializacji jednostki do typów .NET). Poniższy kod Języka C# potencjalnie pobiera wiele jednostek różnych typów z tabeli, ale zwraca wszystkie jednostki jako **dynamictableentity** wystąpień. Następnie używa **EntityType** właściwość do określenia typu każdej jednostki:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
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
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Aby pobrać inne właściwości, należy użyć **Metody TryGetValue** we właściwości **Właściwości** klasy **DynamicTableEntity.**  

Trzecią opcją jest łączenie przy użyciu typu **DynamicTableEntity** i wystąpienia **EntityResolver.** Dzięki temu można rozpoznać do wielu typów POCO w tej samej kwerendzie. W tym przykładzie **delegat EntityResolver** używa **EntityType** właściwość do rozróżniania dwóch typów encji, które zwraca kwerenda. Metoda **Resolve** używa pełnomocnika **programu rozpoznawania nazw** do rozpoznawania wystąpień **DynamicTableEntity** do wystąpień **TableEntity.**  

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
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Modyfikowanie heterogenicznych typów jednostek
Nie trzeba znać typu jednostki, aby ją usunąć, a zawsze znasz typ jednostki podczas wstawiania. Można jednak użyć typu **DynamicTableEntity,** aby zaktualizować jednostkę bez znajomości jej typu i bez użycia klasy jednostki POCO. Poniższy przykładowy kod pobiera pojedynczą jednostkę i **sprawdza, że** właściwość EmployeeCount istnieje przed jej zaktualizowaniem.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Kontrolowanie dostępu za pomocą podpisów dostępu współdzielonego
Tokeny sygnatury dostępu współdzielonego (SAS) umożliwiają aplikacjom klienckim modyfikowanie (i wykonywanie zapytań) jednostek tabeli bez konieczności dołączania klucza konta magazynu do kodu. Zazwyczaj istnieją trzy główne korzyści z używania sygnatury dostępu Współdzielonego w aplikacji:  

* Nie trzeba rozpowszechniać klucza konta magazynu na niezabezpieczonej platformie (takiej jak urządzenie przenośne), aby umożliwić temu urządzeniu dostęp do jednostek w usłudze Tabel i modyfikowanie ich.  
* Można odciążyć niektóre prace wykonywane przez role sieci Web i procesu roboczego podczas zarządzania jednostkami na urządzeniach klienckich, takich jak komputery użytkowników końcowych i urządzenia przenośne.  
* Do klienta można przypisać ograniczony i ograniczony czas zestaw uprawnień (na przykład zezwalanie na dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów sygnatury dostępu współdzielonego z usługą Tabel, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md).  

Jednak nadal należy wygenerować tokeny sygnatury dostępu Współdzielonego, które udzielają aplikacji klienckiej do jednostek w usłudze tabel: należy to zrobić w środowisku, które ma bezpieczny dostęp do kluczy konta magazynu. Zazwyczaj należy użyć roli sieci web lub procesu roboczego do generowania tokenów sygnatury dostępu Współdzielonego i dostarczania ich do aplikacji klienckich, które potrzebują dostępu do jednostek. Ponieważ nadal istnieje obciążenie związane z generowaniem i dostarczaniem tokenów sygnatury dostępu Współdzielonego do klientów, należy rozważyć, jak najlepiej zmniejszyć to obciążenie, szczególnie w scenariuszach dużej liczby woluminów.  

Istnieje możliwość wygenerowania tokenu sygnatury dostępu współdzielonego, który udziela dostępu do podzbioru jednostek w tabeli. Domyślnie należy utworzyć token sygnatury dostępu Współdzielonego dla całej tabeli, ale jest również możliwe określenie, że token sygnatury dostępu współdzielonego udzielić dostępu do zakresu wartości **PartitionKey** lub zakres **PartitionKey** i **RowKey** wartości. Można wybrać do generowania tokenów sygnatury dostępu Współdzielonego dla poszczególnych użytkowników systemu, tak aby każdy użytkownik sygnatury dostępu współdzielonego tylko umożliwia im dostęp do własnych jednostek w usłudze tabel.  

## <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Pod warunkiem, że rozmieszczasz żądania na wielu partycjach, można poprawić przepływność i szybkość reakcji klienta przy użyciu zapytań asynchronicznych lub równoległych.
Na przykład może mieć dwa lub więcej wystąpień roli procesu roboczego uzyskiwania dostępu do tabel równolegle. Może mieć poszczególne role procesu roboczego odpowiedzialne za określone zestawy partycji lub po prostu mają wiele wystąpień roli procesu roboczego, z których każda może uzyskać dostęp do wszystkich partycji w tabeli.  

W wystąpieniu klienta można zwiększyć przepływność, wykonując operacje magazynu asynchronicznie. Biblioteka klienta magazynu ułatwia pisanie asynchronicznych zapytań i modyfikacji. Na przykład można rozpocząć od metody synchroniczną, która pobiera wszystkie jednostki w partycji, jak pokazano w następującym kodzie Języka C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Można łatwo zmodyfikować ten kod, tak aby kwerenda działa asynchronicznie w następujący sposób:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

W tym przykładzie asynchroniiowym można zobaczyć następujące zmiany z wersji synchroniczowej:  

* Podpis metody zawiera teraz modyfikator **asynchronizacza** i zwraca **wystąpienie zadania.**  
* Zamiast wywoływania **ExecuteSegmented** metody pobierania wyników, metoda teraz wywołuje **ExecuteSegmentedAsync** metody i używa **modyfikator await** do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywołać tę metodę wiele razy (z różnymi wartościami dla **parametru działu),** a każda kwerenda będzie uruchamiana w osobnym wątku.  

Nie ma asynchronicznej wersji **Execute** metody w TableQuery klasy, ponieważ **interfejs IEnumerable** nie obsługuje **wyliczenia** asynchroniczne.  

Można również wstawiać, aktualizować i usuwać jednostki asynchronicznie. W poniższym przykładzie języka C# przedstawiono prostą, synchroniczne metody wstawiania lub zastępowania jednostki pracownika:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Można łatwo zmodyfikować ten kod, tak aby aktualizacja działa asynchronicznie w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchroniiowym można zobaczyć następujące zmiany z wersji synchroniczowej:  

* Podpis metody zawiera teraz modyfikator **asynchronizacza** i zwraca **wystąpienie zadania.**  
* Zamiast **wywoływania Execute** metody, aby zaktualizować jednostkę, metoda teraz wywołuje **ExecuteAsync** metody i używa **modyfikator await** do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywołać wiele metod asynchronicznych, takich jak ta, a każde wywołanie metody zostanie uruchomione w oddzielnym wątku.  

## <a name="next-steps"></a>Następne kroki

- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
