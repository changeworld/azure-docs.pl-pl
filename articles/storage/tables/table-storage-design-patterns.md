---
title: Wzorce projektowe oparte na usłudze Azure storage table | Dokumentacja firmy Microsoft
description: Wzorce na użytek rozwiązania usługi Azure table.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/08/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.subservice: tables
ms.openlocfilehash: a428abd95f955a16d03c4ab86f05644f6db65da5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101439"
---
# <a name="table-design-patterns"></a>Wzorce projektowe tabel
W tym artykule opisano niektóre wzorce, które są przeznaczone do użycia z rozwiązaniami usługi tabeli. Ponadto zobaczysz, jak praktycznie rozwiązać niektóre problemy i charakterystyczne kompromisowe omówione w innych artykułach projektowania magazynu tabeli. Poniższy diagram przedstawia relacje między różnych wzorców:  

![Aby wyszukać powiązanych danych](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Mapa wzorzec powyżej wyróżnia niektórych relacji między (niebieski) wzorce i niezalecane wzorce (kolor pomarańczowy), które są opisane w tym przewodniku. Istnieje wiele innych wzorców, które są warte biorąc pod uwagę. Na przykład jednego z kluczowych scenariuszy dla usługi Table Service jest użycie [użycia wzorca zmaterializowanego widoku](https://msdn.microsoft.com/library/azure/dn589782.aspx) z [podział odpowiedzialności polecenia w zapytania (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) wzorca.  

## <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeks pomocniczy wewnątrz partycji
Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości (w tej samej partycji) Włącz szybkie i efektywne wyszukiwań naprzemienny porządek sortowania przy użyciu różnych **RowKey** wartości. Aktualizacje między kopie mogą być przechowywane na spójne przy użyciu EGT firmy.  

### <a name="context-and-problem"></a>Kontekst i problem
Usługa Table service automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja kliencka można pobrać jednostki wydajnie za pomocą tych wartości. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka można użyć kwerendy punktu można pobrać jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator ( **PartitionKey** i **RowKey**  wartości). Klienta można także pobrać jednostki posortowanych według identyfikatorów pracowników w ramach każdego działu.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Jeśli chcesz także być w stanie znaleźć jednostki pracowników na podstawie wartości innej właściwości, takie jak adres e-mail musi być znaleźć dopasowania mniej wydajne rozwiązanie skanowania partycji. Jest to spowodowane Usługa tabeli nie zapewnia indeksów pomocniczych. Ponadto, nie ma opcji żądania listy pracowników posortowane w kolejności innej niż **RowKey** zamówienia.  

### <a name="solution"></a>Rozwiązanie
Aby obejść braku indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdą kopią przy użyciu innego **RowKey** wartości. Jeśli przechowujesz jednostki ze strukturami, pokazano poniżej, pozwala na efektywne pobieranie jednostek pracowników opartych na identyfikatora poczty e-mail adres lub pracowników. Prefiks wartości **RowKey**, "empid_" i "email_" pozwalają na wykonywanie zapytań dla jednego pracownika lub szeroką gamę pracowników przy użyciu zakresu adresów e-mail lub identyfikatory pracownika.  

![Jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Następujące kryteria filtrowania dwóch (jeden wyszukiwanie według Identyfikatora pracowników i jeden wyszukiwanie według adresu e-mail), zarówno Określ zapytań o punkt:  

* $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Sprzedaż") i (RowKey eq 'email_jonesj@contoso.com")  

Po wykonaniu zapytania dotyczącego zakresu jednostek pracowników, można określić zakres posortowane w kolejności identyfikator pracownika lub posortowane w kolejności adres poczty e-mail przy użyciu zapytań dotyczących jednostek, używając odpowiedniego prefiksu w zakresie **RowKey**.  

* Można znaleźć wszyscy pracownicy działu sprzedaży z pracownikiem IDENTYFIKATORA w zakresie użycia 000100 do 000199: $filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "empid_000100") i (RowKey le "empid_000199")  
* Aby znaleźć wszyscy pracownicy działu sprzedaży przy użyciu adresu e-mail, rozpoczynając od litery "" Użyj: $filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "email_a") i (RowKey lt "email_b")  
  
  Należy zauważyć, że składnia filtru używanych w powyższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [wykonywanie zapytań dotyczących jednostek](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tania do użycia, więc obciążenie koszt przechowywania zduplikowanych danych, nie powinien być głównym problemem. Jednak należy zawsze należy przeprowadzić ocenę koszt projektu, w zależności od wymagań przewidywanych i dodawać tylko zduplikowane jednostki do obsługi zapytań, których będą wykonywane przez aplikację kliencką.  
* Ponieważ jednostki indeks pomocniczy są przechowywane w tej samej partycji co oryginalne jednostek, należy upewnić się, przekracza cele skalowalności dla poszczególnych partycji.  
* Możesz zachować swoje zduplikowanych wpisów zgodne ze sobą przy użyciu EGTs niepodzielne zaktualizować dwie kopie jednostki. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz sekcję [przy użyciu transakcji grup jednostek](table-storage-design.md#entity-group-transactions).  
* Wartość **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Wypełnienie wartości liczbowe w **RowKey** (na przykład pracownik 000223 identyfikator), pozwala rozwiązać, sortowanie i filtrowanie na podstawie górną i dolną.  
* Nie zawsze należy zduplikowane właściwości jednostki. Na przykład jeśli zapytania wyszukiwania tej jednostki przy użyciu wiadomości e-mail adresów w **RowKey** nigdy nie potrzebować wiek pracownika, te jednostki może mieć następującą strukturę:

   ![Struktury jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Zazwyczaj lepiej do przechowywania zduplikowanych danych i upewnij się, czy można pobrać wszystkie potrzebne dane za pomocą jednego zapytania, niż Aby użyć jednego zapytania, aby zlokalizować jednostki, a drugi do wyszukiwania wymaganych danych.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy Twoja aplikacja kliencka musi pobrać jednostki przy użyciu szeregu różnych kluczy, gdy Twój klient musi pobrać jednostki w poszczególnych kolejności sortowania, a w przypadku, gdy zidentyfikuje każdej jednostki przy użyciu różnych unikatowe wartości. Jednak należy się upewnić, że nie przekraczają limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostki przy użyciu różnych **RowKey** wartości.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Komunikacja między partycji wzorzec indeks pomocniczy](#inter-partition-secondary-index-pattern)
* [Wzorzec klucza złożona (c)](#compound-key-pattern)
* Transakcje grupy jednostek
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Komunikacja między partycji wzorzec indeks pomocniczy
Store wielu kopii każdej jednostki przy użyciu różnych **RowKey** wartości w oddzielnych partycji lub w oddzielnych tabele, aby włączyć szybkie i efektywne wyszukiwania i naprzemienny porządek sortowania przy użyciu różnych **RowKey**wartości.  

### <a name="context-and-problem"></a>Kontekst i problem
Usługa Table service automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja kliencka można pobrać jednostki wydajnie za pomocą tych wartości. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka można użyć kwerendy punktu można pobrać jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator ( **PartitionKey** i **RowKey**  wartości). Klienta można także pobrać jednostki posortowanych według identyfikatorów pracowników w ramach każdego działu.  

![Identyfikator pracownika](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Jeśli chcesz także być w stanie znaleźć jednostki pracowników na podstawie wartości innej właściwości, takie jak adres e-mail musi być znaleźć dopasowania mniej wydajne rozwiązanie skanowania partycji. Jest to spowodowane Usługa tabeli nie zapewnia indeksów pomocniczych. Ponadto, nie ma opcji żądania listy pracowników posortowane w kolejności innej niż **RowKey** zamówienia.  

Są przewidywania bardzo dużej liczby transakcji względem tych jednostek i chcesz zminimalizować ryzyko ograniczanie klienta usługi tabel.  

### <a name="solution"></a>Rozwiązanie
Aby obejść braku indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki z każdego kopiowania za pomocą różnych **PartitionKey** i **RowKey** wartości. Jeśli przechowujesz jednostki ze strukturami, pokazano poniżej, pozwala na efektywne pobieranie jednostek pracowników opartych na identyfikatora poczty e-mail adres lub pracowników. Prefiks wartości **PartitionKey**, "empid_" i "email_" pozwala na zidentyfikowanie indeksu, której chcesz użyć dla zapytania.  

![Indeks podstawowy i pomocniczy indeks](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Następujące kryteria filtrowania dwóch (jeden wyszukiwanie według Identyfikatora pracowników i jeden wyszukiwanie według adresu e-mail), zarówno Określ zapytań o punkt:  

* $filter = (PartitionKey eq ' empid_Sales") i (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") i (RowKey eq 'jonesj@contoso.com")  

Po wykonaniu zapytania dotyczącego zakresu jednostek pracowników, można określić zakres posortowane w kolejności identyfikator pracownika lub posortowane w kolejności adres poczty e-mail przy użyciu zapytań dotyczących jednostek, używając odpowiedniego prefiksu w zakresie **RowKey**.  

* Aby znaleźć wszyscy pracownicy działu sprzedaży o identyfikatorze pracowników w zakresie **000100** do **000199** sortowane mogli oni skorzystać identyfikator pracownika: $filter = (PartitionKey eq ' empid_Sales") i (RowKey ge"000100") i (RowKey le "000199")  
* Aby znaleźć wszyscy pracownicy działu sprzedaży za pomocą adresu e-mail, który rozpoczyna się od "" posortowanych w wiadomości e-mail adres mogli oni skorzystać: $filter = (PartitionKey eq ' email_Sales") i (RowKey ge"") i (RowKey lt"b")  

Należy zauważyć, że składnia filtru używanych w powyższych przykładach z usługi tabel interfejsu API REST, aby uzyskać więcej informacji, zobacz [wykonywanie zapytań dotyczących jednostek](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Możesz zachować swoje zduplikowanych wpisów ostatecznie spójną ze sobą przy użyciu [wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) do zachowania jednostek podstawowych i pomocniczych indeksu.  
* Magazyn tabel jest stosunkowo tania do użycia, więc obciążenie koszt przechowywania zduplikowanych danych, nie powinien być głównym problemem. Jednak należy zawsze należy przeprowadzić ocenę koszt projektu, w zależności od wymagań przewidywanych i dodawać tylko zduplikowane jednostki do obsługi zapytań, których będą wykonywane przez aplikację kliencką.  
* Wartość **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Wypełnienie wartości liczbowe w **RowKey** (na przykład pracownik 000223 identyfikator), pozwala rozwiązać, sortowanie i filtrowanie na podstawie górną i dolną.  
* Nie zawsze należy zduplikowane właściwości jednostki. Na przykład jeśli zapytania wyszukiwania tej jednostki przy użyciu wiadomości e-mail adresów w **RowKey** nigdy nie potrzebować wiek pracownika, te jednostki może mieć następującą strukturę:
  
   ![Pracownik jednostki (indeks pomocniczy)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Zazwyczaj lepiej do przechowywania zduplikowanych danych i upewnij się, że można pobrać wszystkie dane potrzebne przy użyciu pojedynczego zapytania niż Aby użyć jednego zapytania, aby zlokalizować jednostki za pomocą pomocniczy indeks i umożliwia wyszukiwanie wymagane dane w podstawowy indeks jest.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy Twoja aplikacja kliencka musi pobrać jednostki przy użyciu szeregu różnych kluczy, gdy Twój klient musi pobrać jednostki w poszczególnych kolejności sortowania, a w przypadku, gdy zidentyfikuje każdej jednostki przy użyciu różnych unikatowe wartości. Użyj tego wzorca, gdy użytkownik chce uniknąć przekroczenia limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostki przy użyciu różnych **RowKey** wartości.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeks pomocniczy wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* Transakcje grupy jednostek  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Wzorzec transakcji ostatecznie spójną
Włącz ostatecznie spójne zachowanie całej granice partycji lub granice system magazynu za pomocą kolejek systemu Azure.  

### <a name="context-and-problem"></a>Kontekst i problem
EGTs Włącz transakcje niepodzielne w wielu jednostkach, które współużytkują ten sam klucz partycji. Względu na wydajność i skalowalność, możesz zdecydować o do przechowywania obiektów, które mają wymagań spójności w oddzielnych partycjach lub w systemie magazynu oddzielne: w takiej sytuacji EGTs nie można użyć, aby zachować spójność. Na przykład może być wymagane, aby zachować spójność między:  

* Jednostki przechowywane w dwóch różnych partycji w tej samej tabeli, w różnych tabelach lub w różnych kontach magazynu.  
* Jednostki przechowywane w usłudze Table service i obiektów blob przechowywanych w usłudze obiektów Blob.  
* Jednostki przechowywane w usłudze Table service i pliku w systemie plików.  
* Magazyn jednostki w usłudze Table service jeszcze indeksowane, za pomocą usługi Azure Search.  

### <a name="solution"></a>Rozwiązanie
Za pomocą kolejek systemu Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną dwa lub więcej partycji lub systemów magazynowania.
Aby zilustrować to podejście, zakłada się, że masz wymaganie, aby można było archiwum starych jednostek pracownika. Starych jednostek pracowników badane są tabele rzadko i powinny być wykluczone z żadnych działań, które zajmują się obecnie pracowników. Aby zaimplementować ten wymóg przechowywania zatrudnionych pracowników w **bieżącego** tabeli i starego pracowników w **archiwum** tabeli. Archiwizowanie pracownika, musisz usunąć jednostkę z **bieżącego** tabeli, a następnie Dodaj jednostkę którą chcesz **archiwum** tabeli, ale nie można użyć EGT wykonywać te dwie operacje. Aby uniknąć ryzyka, że błąd spowoduje, że jednostki się pojawiać w obu lub ani tabel, operacja archiwizacji musi być ostatecznie spójny. Poniższy diagram sekwencji przedstawia kroki opisane w tej operacji. Więcej szczegółów znajduje się dla ścieżki wyjątku poniżej tekst.  

![Rozwiązanie kolejek platformy Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient inicjuje operację archiwum, umieszczając komunikat w kolejce platformy Azure, w tym przykładzie do archiwizacji pracowników #456. Rola procesu roboczego sonduje kolejkę dla nowych komunikatów; gdy zostanie znaleziony, odczytuje komunikat i pozostawia kopię ukryte w kolejce. Rola procesu roboczego obok pobiera kopię obiektu z **bieżącego** tabeli, wstawia kopię w **archiwum** tabeli, a następnie usuwa oryginalny z **bieżącego** tabeli. Na koniec Jeśli nie wystąpiły błędy z poprzednich kroków, rola procesu roboczego usuwa ukryty komunikat z kolejki.  

W tym przykładzie kroku 4 wstawia pracowników do **archiwum** tabeli. Można go dodać pracownika do obiektu blob w usłudze obiektów Blob lub pliku w systemie plików.  

### <a name="recovering-from-failures"></a>Odzyskiwanie po awarii
Ważne jest, że operacje w krokach **4** i **5** musi być *idempotentne* w przypadku, gdy rola procesu roboczego musi ponownie uruchomić operację archiwum. Jeśli używasz usługi Table w kroku **4** należy użyć operacji "wstawić lub Zastąp"; kroku **5** należy używać "usunąć, jeśli istnieje" operacji w bibliotece klienta, którego używasz. Jeśli używasz innego systemu magazynowania, należy użyć odpowiedniej idempotentna operacja.  

Jeśli rola procesu roboczego nigdy nie ukończy kroku **6**, a następnie po upływie limitu czasu pojawi się komunikat w kolejce, gotowe do roli procesu roboczego spróbować ponownie przetworzyć go. Rola procesu roboczego można sprawdzić, ile razy komunikat w kolejce został czytać i, jeśli to konieczne, Flaga jest komunikat "skażone" dla badania, wysyłając je do oddzielnej kolejki. Aby uzyskać więcej informacji na temat odczytywania komunikatów z kolejki i sprawdzanie liczby usuwania z kolejki, zobacz [pobierania komunikatów](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Błędy w usługach tabela i kolejka są błędami przejściowymi, a Twoja aplikacja kliencka powinna zawierać logikę ponawiania odpowiednie do ich obsługi.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie wymaga izolacji transakcji. Na przykład, klient może odczytać **bieżącego** i **archiwum** tabel, gdy rola procesu roboczego zostało między krokami **4** i **5**i zobacz niespójne widok danych. Należy pamiętać, że dane będą zgodne po pewnym czasie.  
* Należy się upewnić, że kroki 4 i 5 są idempotentne, w celu zapewnienia spójności ostatecznej.  
* Możesz skalować rozwiązanie przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy użytkownik chce zagwarantować spójność między jednostkami, które istnieją w różnych partycjach lub tabel. Możesz rozszerzyć tego wzorca, aby zapewnić spójność ostateczną dla operacji w usłudze Table service i usługi obiektów Blob i innych niż - usługi Azure Storage źródeł danych, takich jak bazy danych lub systemu plików.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek  
* [Scalanie lub zastępowanie](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacji transakcji ważne jest, aby rozwiązanie, należy rozważyć przeprojektowanie tabel w celu umożliwienia używania EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Obsługa jednostki indeksu umożliwiające wydajne wyszukiwanie, które zwracają listę jednostek.  

### <a name="context-and-problem"></a>Kontekst i problem
Usługa Table service automatycznie indeksuje jednostki przy użyciu **PartitionKey** i **RowKey** wartości. Dzięki temu aplikacja kliencka w celu pobrania jednostki wydajnie za pomocą kwerendy punktu. Na przykład przy użyciu struktury tabeli pokazano poniżej, aplikacja kliencka pozwala na efektywne pobieranie jednostki poszczególnych pracowników przy użyciu nazwy działu i identyfikator ( **PartitionKey** i **RowKey**).  

![Jednostka pracownika](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Jeśli chcesz również będzie można go pobrać listę jednostek pracowników na podstawie wartości innej właściwości nie jest unikatowa, takie jak nazwisko, musi być znaleźć dopasowań, a nie przy użyciu indeksu, aby wyszukać bezpośrednio mniej wydajne rozwiązanie skanowania partycji. Jest to spowodowane Usługa tabeli nie zapewnia indeksów pomocniczych.  

### <a name="solution"></a>Rozwiązanie
Umożliwia wyszukiwanie według nazwiska ze strukturą entity powyżej, musisz utrzymywać listę identyfikatorów pracownika. Jeśli chcesz pobrać jednostki pracownika o określonej nazwie ostatniego, takich jak Jones, należy w pierwszej kolejności zlokalizować listę identyfikatorów pracowników dla pracowników z Jones jako swoje nazwisko, a następnie pobrać tych jednostek pracownika. Istnieją trzy główne opcje przechowywania listy identyfikatorów pracowników:  

* Użyj usługi blob storage.  
* Utwórz jednostki indeksu w jednej partycji jako jednostki pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

<u>Opcja #1: Usługa blob storage</u>  

Pierwszej opcji Tworzenie obiektu blob dla każdego unikatowy nazwisko i w każdym magazynie obiektów blob listę **PartitionKey** (dział) i **RowKey** (identyfikator pracownika) wartości dla pracowników, które mają ten nazwisko. Po dodaniu lub usunięciu pracownika należy się upewnić, że zawartości odpowiedniego obiektu blob jest ostatecznie spójny z jednostkami pracownika.  

<u>Opcja #2.</u> Tworzenie jednostek indeksu w jednej partycji  

Druga opcja można użyć jednostki indeksu, zawierające następujące dane:  

![Jednostki indeksu pracownika](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

**EmployeeIDs** właściwość zawiera listę identyfikatorów pracowników dla pracowników z nazwisko przechowywane w **RowKey**.  

Następujące kroki przedstawiają procesu, które należy wykonać podczas korzystania z drugą opcję dodawania nowego pracownika. W tym przykładzie dodamy pracownika z identyfikatorem 000152 i nazwisko Jones działu sprzedaży:  

1. Pobrać jednostki indeksu z **PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Kowalski". Zapisz element ETag to jednostki do użycia w kroku 2.  
2. Tworzenie jednostki transakcji grupy (czyli operacji zbiorczej), która wstawia nową jednostkę pracowników (**PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "000152") i aktualizuje jednostki indeksu (**PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Kowalski") przez dodanie nowego Identyfikatora pracowników do listy w polu EmployeeIDs. Aby uzyskać więcej informacji na temat transakcji grup jednostek Zobacz transakcji grup jednostek.  
3. Jeśli transakcji grup jednostek zakończy się niepowodzeniem z powodu błędu optymistycznej współbieżności (kogoś innego po prostu zmodyfikował jednostki indeksu), należy zacząć od początku w kroku 1 ponownie.  

Można użyć podejście podobne do usuwania pracownika, korzystając z pierwszej opcji. Zmiana nazwisko pracownika jest nieco bardziej skomplikowane, ponieważ będą potrzebne do wykonania transakcji grupy jednostki, która aktualizuje trzech jednostek: jednostki pracowników, jednostki indeksu dla starego nazwisko i jednostki indeksu dla nowe nazwisko. Każda jednostka musi pobrać przed wprowadzeniem jakichkolwiek zmian w celu pobrania wartości element ETag, które następnie służy do przeprowadzania aktualizacji za pomocą optymistycznej współbieżności.  

Poniższe kroki opisują proces, który należy wykonać w przypadku konieczności wyszukania wszystkich pracowników z danym nazwisko w dziale, korzystając z drugą opcją. W tym przykładzie firma Microsoft jest wyszukanie wszystkich pracowników z nazwisko Jones działu sprzedaży:  

1. Pobrać jednostki indeksu z **PartitionKey** wartość "Sprzedaż" i **RowKey** wartość "Kowalski".  
2. Przeanalizować listy identyfikatorów w polu EmployeeIDs pracowników.  
3. Aby uzyskać dodatkowe informacje na temat każdego z tych pracowników (na przykład ich adresów e-mail), pobrać każdej jednostki pracowników przy użyciu **PartitionKey** wartość "Sprzedaż" i **RowKey** wartości z kolekcji Lista pracowników, który został uzyskany w kroku 2.  

<u>Opcja #3:</u> Tworzenie indeksu jednostek w oddzielnej partycji lub tabeli  

Trzecia opcja można użyć jednostki indeksu, zawierające następujące dane:  

![Jednostki indeksu pracowników w oddzielnej partycji](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


**EmployeeIDs** właściwość zawiera listę identyfikatorów pracowników dla pracowników z nazwisko przechowywane w **RowKey**.  

Trzecia opcja EGTs nie można użyć do zapewniania spójności, ponieważ jednostki indeksu znajdują się w oddzielnej partycji z jednostek pracownika. Należy upewnić się, że jednostki indeksu są ostatecznie spójny z jednostkami pracownika.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań do pobierania zgodnych jednostek: jeden do wykonywania zapytań jednostki indeks, aby uzyskać listę **RowKey** wartości i zapytania, aby pobrać każdej jednostki na liście.  
* Biorąc pod uwagę, że poszczególne jednostki zawiera maksymalnie 1 MB, opcję #2 i #3 w rozwiązaniu zakładać, że lista identyfikatorów pracowników dla dowolnej podanej nazwy ostatniego jest nigdy nie przekracza 1 MB. W przypadku może być większa niż 1 MB — na listę identyfikatorów pracowników, użyj opcji #1 i przechowywać dane indeksu w usłudze blob storage.  
* W przypadku użycia opcji #2 (przy użyciu EGTs obsługiwać Dodawanie i usuwanie pracowników i zmiana jego nazwisko) należy ocenić, jeśli wielkość transakcji będzie podejście limity skalowalności w danej partycji. Jeśli jest to możliwe, należy rozważyć ostatecznie spójną rozwiązanie (opcja #1 lub #3) używa kolejek do obsługi żądań aktualizacji, która umożliwia przechowywanie jednostek indeksu w oddzielnej partycji z jednostek pracownika.  
* Opcja #2, w tym rozwiązaniu założono, że można wyszukiwać według nazwiska w dziale: na przykład chcesz pobrać listę pracownikom nazwisko Jones w dziale sprzedaży. Jeśli chcesz można było wyszukać wszystkich pracowników z nazwisko Jones w całej organizacji, użyj opcji #1 lub opcja #3.
* Możesz zaimplementować rozwiązanie oparte na kolejce zapewnia spójność ostateczną (zobacz [wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) Aby uzyskać więcej informacji).  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy chcesz wyszukać zestaw jednostek, mających wspólne wartość właściwości, na przykład wszystkich pracowników z nazwisko Jones.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  
* Transakcje grupy jednostek  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Wzorzec denormalizacja
Ze sobą łączyć powiązanych danych w jednej jednostce umożliwiają można pobrać wszystkie dane potrzebne przy użyciu zapytania pojedynczy punkt.  

### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizacji danych, aby usunąć zduplikowane wynikiem zapytań, które pobierają dane z wielu tabel. Jeśli użytkownik normalizować dane w tabelach platformy Azure, upewnij się wiele rund od klienta do serwera w celu pobrania danych powiązanych. Na przykład struktura tabeli poniżej możesz wymaga dwóch wystąpień komunikacji dwustronnej można pobrać szczegółów dla działu: jeden można pobrać jednostki działu, które obejmują menedżera użytkownika, identyfikator, a następnie inne żądanie można pobrać szczegółów Menedżera w jednostce pracownika.  

![Dział a pracownika](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Rozwiązanie
Zamiast przechowywać dane w dwóch oddzielnych jednostkach, zdenormalizowanie danych i przechowywać kopię szczegółach menedżera w jednostce działu. Na przykład:  

![Dział jednostki](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Z jednostkami działu przechowywane z tymi właściwościami można teraz pobierać wszystkie szczegóły potrzebne o dział przy użyciu kwerendy punktu.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje pewien koszt obciążenie związanych z przechowywaniem niektóre dane w dwa razy. Korzyści w zakresie wydajności (wynikające z mniejszą liczbą żądań do usługi storage) zwykle przewyższa brzegowych wzrost kosztów magazynowania (i ten koszt jest częściowo przesunięcie poprzez zmniejszenie liczby transakcji wymagane w celu pobrania szczegółów dotyczących dział ).  
* Należy zachować spójność dwie jednostki, które przechowują informacje o menedżerów. Problem spójności może obsługiwać za pomocą EGTs do zaktualizowania wielu jednostek w ramach jednej transakcji niepodzielnej: w tym przypadku jednostki działu i jednostki pracowników działu Menedżera są przechowywane w tej samej partycji.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz często do wyszukania powiązanych informacji. Ten wzorzec pozwala zmniejszyć liczbę zapytań, które należy wprowadzić klienta, aby pobierać dane, które wymaga.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* Transakcje grupy jednostek  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Wzorzec klucza złożona (c)
Użyj złożone **RowKey** wartości, aby włączyć klienta do wyszukiwania danych powiązane z zapytaniem pojedynczy punkt.  

### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych jest dość fizycznych za pomocą sprzężeń w zapytaniach zwrócić powiązane elementy danych do klienta w ramach pojedynczego zapytania. Identyfikator pracownika może na przykład użyć, aby wyszukać listę powiązanych jednostek, które zawierają wydajności i przeglądać dane dla tego pracownika.  

Przyjęto założenie, że pracownik jednostki będą przechowywane w usłudze tabeli przy użyciu następującej struktury:  

![Struktury jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Należy również do przechowywania danych historycznych dotyczących przeglądów i wydajności dla każdego roku, w których pracownik pracował dla Twojej organizacji i muszą mieć możliwość dostępu do tych informacji przez rok. Jedną z opcji jest, aby utworzyć inną tabelę, która przechowuje jednostki o następującej strukturze:  

![Struktury jednostki alternatywnych pracownika](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Zwróć uwagę, w tym podejściu użytkownik może zdecydować o zduplikowane niektórych informacji (takich jak imię i nazwisko) w nowej jednostce umożliwiają pobranie danych za pomocą pojedynczego żądania. Nie można jednak obsługa silnej spójności, ponieważ EGT nie można użyć do zaktualizowania niepodzielne dwie jednostki.  

### <a name="solution"></a>Rozwiązanie
Store nowy typ jednostki w oryginalnej tabeli za pomocą jednostek o następującej strukturze:  

![Rozwiązanie dla struktury jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Zwróć uwagę sposób, w jaki **RowKey** jest teraz klucza złożonego składa się z identyfikator pracownika i roku przeglądanie danych, która umożliwia pobieranie wydajność pracowników i przeglądanie danych z pojedynczego żądania dla pojedynczej jednostki.  

Poniższy przykład przedstawia, jak pobrać przeglądanie danych dla danego pracownika (na przykład 000123 pracownik działu sprzedaży):  

$filter = (PartitionKey eq "Sprzedaż") i (RowKey ge "empid_000123") i (RowKey lt "empid_000124") & $select = RowKey, ocena menedżera, ocena elementów równorzędnych, komentarze  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego separatora, który ułatwia analizowanie **RowKey** wartość: na przykład **000123_2012**.  
* Tę jednostkę będą również przechowywane w tej samej partycji, co inne jednostki, które zawierają dane dotyczące tych samych pracownika, co oznacza, że używasz EGTs utrzymanie wysokiego poziomu spójności.
* Należy rozważyć, jak często będą zapytań danych, aby ustalić, czy ten wzorzec jest odpowiedni.  Na przykład jeśli będzie dostęp rzadko przeglądanie danych i danych o pracownikach głównego często warto przechowywać je jako osobne jednostki.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy chcesz przechować jeden lub więcej powiązanych jednostek to zapytanie można często.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek  
* [Praca z typami encji heterogenicznych](#working-with-heterogeneous-entity-types)  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Wzorzec ogona dziennika
Pobieranie *n* ostatnio dodany do partycji przy użyciu jednostek **RowKey** wartość, która sortuje w odwrotnej daty i porządku czasowym.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymogiem jest będzie można go pobrać ostatnio utworzone jednostki, na przykład dziesięć ostatnich wydatków oświadczenia złożone przez pracownika. Tabela zapytania pomocy technicznej **$top** operację, aby powrócić do pierwszego zapytania *n* jednostki z zestawu: istnieje żadna operacja równoważne zapytania do zwrócenia ostatnie n jednostki w zestawie.  

### <a name="solution"></a>Rozwiązanie
Store jednostki przy użyciu **RowKey** że naturalnie sortuje w kolejności odwrotnej daty/godziny przy użyciu więc najnowszy wpis jest zawsze pierwszej tabeli.  

Na przykład aby można było pobrać dziesięć ostatnich oświadczenia wydatków przesłane przez pracownika, służy wartość odwrotnej osi pochodzi od bieżącej daty/godziny. Poniższy przykładowy kod języka C# zawiera jeden ze sposobów tworzenia odpowiednią wartość "odwrócony znaczników" dla **RowKey** która sortuje od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić do wartość Data / czas, używając następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie dotyczące tabeli wygląda następująco:  

`https://myaccount.table.core.chinacloudapi.cn/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy konsoli wartość odwrotnej osi z wiodącymi zerami w celu zapewnienia, że wartość ciągu Sortuje zgodnie z oczekiwaniami.  
* Należy pamiętać o cele skalowalności na poziomie partycji. Należy zachować ostrożność nie należy tworzyć partycje aktywny.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz dostępu do jednostek w kolejności odwrotnej daty/godziny lub konieczność dostęp do ostatnio dodanych jednostek.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Dołączana / dołączanie wzorzec niezalecane](#prepend-append-anti-pattern)  
* [Pobieranie jednostki](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Wzorzec usuwania duża
Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki do usunięcia jednoczesnych w ich własnych osobnej tabeli; jednostki można usunąć przez usunięcie tabeli.  

### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji, Usuń stare dane, które nie musi już być dostępne dla aplikacji klienckiej lub która aplikacja ma zarchiwizować na innym urządzeniu pamięci masowej. Zazwyczaj zidentyfikować te dane według daty: na przykład nie można usunąć rekordy dla wszystkich żądań logowania, które są starsze niż 60 dni.  

Jeden projekt możliwe jest użycie datę i godzinę żądanie logowania w **RowKey**:  

![Data i godzina próby logowania](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

W tym podejściu unika obszarów nadmiernej aktywności partycji, ponieważ aplikacja może Wstawianie i usuwanie jednostek logowania dla każdego użytkownika w oddzielnej partycji. Jednak takie podejście może być kosztowna i czasochłonna Jeśli masz dużą liczbę jednostek, ponieważ najpierw skanowanie tabeli, aby zidentyfikować wszystkie jednostki do usunięcia, a następnie należy usunąć stare jednostki. Należy pamiętać, że można zmniejszyć liczby rund do serwera, wymagane, aby usunąć stare jednostek, przetwarzanie wsadowe wielu żądań delete służących do EGTs.  

### <a name="solution"></a>Rozwiązanie
Dla każdego dnia prób logowania, należy użyć osobnej tabeli. Powyżej projektu jednostki można użyć, aby uniknąć obszarów aktywności, gdy wstawiasz jednostek i usuwania starych jednostek jest teraz po prostu pytanie usunięcia każdego dnia w jednej tabeli (operacją pojedynczy magazyn) zamiast Znajdowanie i usuwanie setki i tysiące osoby jednostki logowania każdego dnia.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Projekt obsługuje inne sposoby, aplikacja będzie używać danych, takie jak wyszukiwanie określonych jednostek, łączenie z innymi danych lub generowania agregują informacje?  
* Projekt uniknąć punktów aktywnych, wstawiając nowych jednostek?  
* Oczekiwane opóźnienie, jeśli chcesz ponownie użyć tej samej nazwy tabeli po jego usunięciu. Zaleca się zawsze używać nazw unikatowej tabeli.  
* Oczekiwać, że niektóre ograniczenia przepustowości przy pierwszym użyciu nową tabelę Table service uczy się wzorców dostępu i dystrybuuje partycji na węzłach. Należy rozważyć, jak często należy utworzyć nowe tabele.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli masz dużą liczbę jednostek, które należy usunąć w tym samym czasie.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek
* [Modyfikowanie jednostek](#modifying-entities)  

## <a name="data-series-pattern"></a>Wzorzec serii danych
Seria kompletne dane Store w pojedynczą jednostkę, aby zminimalizować liczbę żądań, które wprowadzasz.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz polega na dla aplikacji, aby przechowywać serię danych, które zazwyczaj musi pobrać wszystkie na raz. Na przykład aplikacja może rejestrować liczbę komunikatów wiadomości Błyskawicznych każdemu pracownikowi wysyła co godzinę, a następnie te informacje służą do wykreślenia liczbę komunikatów każdego użytkownika wysłanych w ciągu poprzednich 24 godzin. Jeden projekt może być przechowywanie podmioty 24 dla każdego pracownika:  

![Store 24 jednostek dla każdego pracownika](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

W tym projekcie można łatwo zlokalizować i aktualizować jednostki do zaktualizowania dla każdego pracownika, zawsze wtedy, gdy aplikacja musi zaktualizować wartość licznika komunikatów. Jednak aby pobrać informacje do wykreślenia wykresu aktywności w poprzednich 24 godzin, możesz pobrać jednostki 24.  

### <a name="solution"></a>Rozwiązanie
Za pomocą następujących projektowych osobne właściwości do przechowywania liczba komunikatów dla każdej godziny:  

![Jednostki statystyki komunikatów](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

W tym projekcie można użyć operacji scalania, można zaktualizować liczba komunikatów dla pracownika określonej godziny. Teraz możesz pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli serii kompletne dane nie mieści się w pojedynczej jednostki (jednostka może mieć maksymalnie 252 właściwości), należy korzystać z magazynu danych alternatywne, np. obiekt blob.  
* Jeśli masz wielu klientów jednocześnie aktualizowania jednostki, musisz użyć **ETag** na potrzeby optymistycznej współbieżności. Jeśli masz wielu klientów, może wystąpić wysokiej rywalizacji o zasoby.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba zaktualizować i pobrania serii danych skojarzonych z pojedynczą jednostkę.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scalanie lub zastępowanie](#merge-or-replace)  
* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) (Jeśli przechowujesz serii danych w obiekcie blob)  

## <a name="wide-entities-pattern"></a>Wzorzec szerokiego jednostek
Wiele jednostek fizycznych umożliwiają przechowywanie jednostek logicznych z więcej niż 252 właściwości.  

### <a name="context-and-problem"></a>Kontekst i problem
Poszczególne jednostki może mieć maksymalnie 252 właściwości (z wyjątkiem właściwości systemowe obowiązkowy) i nie można zapisać więcej niż 1 MB danych w sumie. W relacyjnej bazie danych zazwyczaj otrzymamy round jakiekolwiek ograniczenia dotyczące rozmiaru wiersz, dodając nową tabelę i wymuszanie 1-do-1 relację między nimi.  

### <a name="solution"></a>Rozwiązanie
Usługa Table service można przechowywać wiele jednostek do reprezentowania obiekt pojedynczego dużych firm z ponad 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości Błyskawicznych komunikatów wysyłanych przez każdego pracownika w ciągu ostatnich 365 dni, można użyć następujących projekt, który wykorzystuje dwie jednostki z różnymi schematami:  

![Wiele jednostek](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Jeśli musisz wprowadzić zmianę, która wymaga, aby aktualizowanie obu jednostek, aby zachować synchronizację ze sobą, można użyć EGT. W przeciwnym razie można użyć operacji scalania pojedynczego można zaktualizować liczba komunikatów dla określonego dnia. Aby pobrać wszystkie dane dla poszczególnych pracowników musi pobrać obie jednostki, można to zrobić za pomocą dwóch żądania wydajne, które używają zarówno **PartitionKey** i **RowKey** wartości.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Trwa pobieranie całą jednostkę logiczną obejmuje co najmniej dwie transakcje magazynu: jedną do pobrania każda jednostka fizyczna.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy konieczne przechowywanie podmioty, których rozmiar lub liczbę właściwości przekracza limit pojedynczą jednostkę w usłudze Table service.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek
* [Scalanie lub zastępowanie](#merge-or-replace)

## <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Użyj usługi blob storage do przechowywania wartości właściwości dużych.  

### <a name="context-and-problem"></a>Kontekst i problem
Poszczególne jednostki nie można przechowywać więcej niż 1 MB danych, w sumie. Jeśli jeden lub kilka właściwości przechowywać wartości, które powodują całkowity rozmiar jednostki przekracza tę wartość, nie można zapisać całej jednostki w usłudze Table service.  

### <a name="solution"></a>Rozwiązanie
Jeśli jednostka przekracza 1 MB rozmiar, ponieważ co najmniej jedna właściwość zawiera dużą ilość danych, możesz przechowywać dane w usłudze obiektów Blob i następnie zapisać adres obiektu blob w właściwości w obiekcie. Na przykład można przechowywać zdjęcia pracownika w usłudze blob storage i przechowywać łącze do zdjęć w **zdjęcie** właściwości jednostki pracowników:  

![Właściwość zdjęć](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność między jednostki w usłudze Table service i danych w usłudze obiektów Blob, należy użyć [wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern) do zachowania jednostek.
* Pobieranie całą jednostkę obejmuje co najmniej dwie transakcje magazynowe: jeden do pobierania jednostki, a drugi do pobierania danych obiektów blob.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy trzeba przechowywanie podmioty, których rozmiar przekracza limit pojedynczą jednostkę w usłudze Table service.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec transakcji ostatecznie spójną](#eventually-consistent-transactions-pattern)  
* [Wzorzec szerokiego jednostek](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Dołączana/dołączanie zapobieganie wzorzec
Zwiększa skalowalność, jeśli masz dużą liczbę operacji wstawienia przez rozłożenie operacje wstawiania na wielu partycjach.  

### <a name="context-and-problem"></a>Kontekst i problem
Dołączenie lub dołączanie jednostek do jednostki przechowywanej zwykle powoduje w aplikacji, dodając nowe jednostki do partycji pierwszej lub ostatniej sekwencji partycji. W takim przypadku wszystkie operacje wstawiania w danym momencie pojawiają się w tej samej partycji, tworzenie informacji o hotspotach, który uniemożliwia usłudze tabeli z równoważenia obciążenia wstawia w wielu węzłach i może doprowadzić do aplikacji w taki sposób trafić cele skalowalności dla partycja. Na przykład, jeśli masz aplikację, dzienniki sieci i zasobów dostęp przez pracowników, następnie struktury jednostki, jak pokazano poniżej może spowodować partycji bieżącej godziny staje się aktywny, jeśli wielkość transakcji osiągnie tę docelową skalowalność dla poszczególnych partycji:  

![Struktury jednostki](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Rozwiązanie
Z następującej struktury jednostki alternatywnych pozwala uniknąć punkt aktywny na żadnej określonej partycji jako zdarzenia z dzienników aplikacji:  

![Struktura alternatywnej jednostki](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Powiadomienie z tym przykładem sposobu zarówno **PartitionKey** i **RowKey** są klucze złożone. **PartitionKey** używa Identyfikatora dziale i pracowników do dystrybucji rejestrowanie na wielu partycjach.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Alternatywne struktury klucza, który pozwala uniknąć tworzenia partycji w warstwie gorąca na operacje wstawiania efektywnie obsługuje zapytania, który sprawia, że Twoja aplikacja kliencka?  
* Przewidywane ilości transakcji oznacza że prawdopodobnie osiągnąć cele skalowalności dla poszczególnych partycji i ograniczona przez usługę storage?  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać prepend/dołączanie wzorzec zapobieganie po skutkować ograniczania, usługi magazynu, gdy uzyskujesz dostęp do gorącą partycją ilości transakcji.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożona (c)](#compound-key-pattern)  
* [Wzorzec ogona dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Wzorzec ochrony danych dziennika
Zazwyczaj należy użyć usługi obiektów Blob zamiast usługi tabel do przechowywania danych dziennika.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowy przypadek użycia, danych dziennika jest pobrać wybór przyrostu wpisów dziennika dla zakresu określonej daty/godziny: na przykład chcesz odnaleźć wszystkich błędów i krytycznych wiadomości o zarejestrowane przez aplikację między 15:04 i 15:06 w określonym dniu. Czy chcesz umożliwia określenia partycji, Zapisz dziennik jednostek do daty i godziny komunikatu dziennika: wyniki w warstwie gorąca partycji, ponieważ w dowolnym momencie wszystkie jednostki dziennika będą współużytkować ten sam **PartitionKey** wartości (zobacz sekcja [Prepend/dołączanie zapobieganie wzorzec](#prepend-append-anti-pattern)). Na przykład poniższy schemat jednostki dla komunikatu dziennika powoduje gorącą partycją ponieważ aplikacja zapisuje wszystkie komunikaty dziennika do partycji dla bieżącej daty i godziny:  

![Jednostka wiadomości dziennika](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

W tym przykładzie **RowKey** zawiera datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są przechowywane, sortowane w kolejności daty/godziny oraz identyfikator komunikatu w przypadku, gdy wiele komunikatów dziennika udostępnianie tej samej daty i godziny.  

Innym rozwiązaniem jest użycie **PartitionKey** temu, że aplikacja zapisuje komunikaty w wielu partycjach. Na przykład źródło komunikatu dziennika umożliwia rozłożenie wiadomości na wielu partycjach, można użyć następującego schematu jednostki:  

![Jednostki komunikatów dziennika alternatywne](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problem z tym schemacie jest jednak, że można pobrać wszystkie komunikaty dziennika dla zakresu określonej godzinie wyszukiwanie wszystkich partycji w tabeli.

### <a name="solution"></a>Rozwiązanie
Wcześniejsze wyróżnione problem próby Table service służy do przechowywania wpisów dziennika i sugerowane dwóm niezadowalające, projekty. Jedno rozwiązanie, które doprowadziły do gorącą partycją o ryzyku niską wydajnością, zapisywanie komunikatów dziennika; inne rozwiązanie w wyniku zapytania niskiej wydajności ze względu na konieczność skanowania wszystkich partycji w tabeli, aby pobrać komunikaty dziennika dla zakresu określonej godzinie. Usługa blob storage oferuje lepsze rozwiązania dla tego typu scenariusza i jest to, jak usługa Azure Storage Analytics magazyny danych dzienników są zbierane.  

W tej sekcji opisano, jak usługa Storage Analytics przechowuje dane dzienników w magazynie obiektów blob jako ilustrację tego podejścia do przechowywania danych, które zazwyczaj zapytania według zakresu.  

Usługa Storage Analytics przechowuje komunikaty w dzienniku w formacie rozdzielanym w wielu obiektów blob. Rozdzielany format ułatwia aplikacji klienckiej do analizowania danych w komunikacie dziennika.  

Usługa Storage Analytics używa konwencji nazewnictwa dla obiektów blob, które umożliwia lokalizowanie obiektu blob (lub obiektów blob), zawierające komunikaty dziennika, dla których potrzebujesz. Na przykład obiekt blob o nazwie "queue/2014/07/31/1800/000001.log" zawiera komunikaty dziennika, które odnoszą się do usługi kolejki, godzinę, począwszy od 18:00 dnia 31 lipca 2014. "000001" oznacza, że jest pierwszy plik dziennika dla tego okresu. Usługa Storage Analytics rejestruje także sygnatury czasowe komunikatów dziennika imię i nazwisko, przechowywane w pliku metadanych obiektu blob w ramach. Interfejs API umożliwia magazynu obiektów blob, zlokalizuj obiektów blob w kontenerze na podstawie prefiksu nazwy: Aby znaleźć wszystkie obiekty BLOB, które zawierają dane dziennika w kolejce na godzinę, począwszy od 18:00, możesz użyć prefiksu "kolejki/2014/07/31/1800."  

Storage Analytics buforów rejestrowania komunikatów wewnętrznie i następnie okresowo aktualizuje odpowiedni obiekt blob lub tworzy nową grupę za pomocą najnowszego wsadu wpisy dziennika. Zmniejsza to liczbę operacji zapisu, które należy wykonać na usługę blob Service.  

Przed zaimplementowaniem podobnego rozwiązania w swojej aplikacji, należy wziąć pod uwagę sposób zarządzania kompromis między niezawodności (zapisywania każdy wpis dziennika do magazynu obiektów blob, jak to się dzieje) oraz kosztu i skalowalności (buforowanie aktualizacje w aplikacji i zapisywanie ich do usługi blob storage w partiach).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie przechowywania danych dziennika, należy rozważyć następujące kwestie:  

* Jeśli tworzysz projektowaniu tabel, która pozwala uniknąć potencjalnych partycje gorąca, może się okazać, że nie masz dostępu do danych dziennika wydajnie.  
* Aby przetwarzać dane dziennika, klient często musi załadować wiele rekordów.  
* Mimo, że dane dziennika często mają strukturę, magazynu obiektów blob może być lepszym rozwiązaniem.  

## <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, mieć na uwadze podczas implementowania wzorców opisanych w poprzednich sekcjach. Większość w tej sekcji używa przykłady napisane w języku C#, korzystających z biblioteki klienta usługi Storage (wersja 4.3.0 w czasie pisania).  

## <a name="retrieving-entities"></a>Pobieranie jednostki
Zgodnie z opisem w sekcji Projekt do wykonywania zapytań, najbardziej wydajne zapytania jest kwerendy punktu. Jednak w niektórych scenariuszach może być konieczne pobieranie wielu jednostek. W tej sekcji opisano niektóre typowe sposoby pobierania jednostki przy użyciu biblioteki klienta usługi Storage.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Wykonywanie kwerendy punktu, przy użyciu biblioteki klienta magazynu
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

### <a name="retrieving-multiple-entities-using-linq"></a>Trwa pobieranie wielu jednostek za pomocą LINQ
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

Poniższy przykład kodu pokazuje równoważną funkcjonalność bez przy użyciu składni LINQ:  

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
> Przykład zagnieżdżony wielu **CombineFilters** metody służące do dołączania trzech warunków filtrowania.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Trwa pobieranie dużej liczby obiektów z kwerendy
Optymalne zapytanie zwraca pojedynczą jednostkę na podstawie **PartitionKey** wartość i **RowKey** wartości. Jednak w niektórych scenariuszach może być wymagane do zwrócenia wielu jednostek z jednej partycji lub nawet od liczby partycji.  

W takich scenariuszach, należy zawsze w pełni przetestować wydajność aplikacji.  

Zapytanie względem usługi tabeli może zwrócić więcej niż 1000 jednostek w tym samym czasie i może być wykonywane przez maksymalnie pięć sekund. Jeśli zestaw wyników zawiera więcej niż 1000 jednostek, jeśli zapytanie nie została ukończona w ciągu pięciu sekund lub zapytanie przecina granicę partycji, usłudze Table service zwraca token kontynuacji, aby umożliwić aplikacji klienckiej zażądać następnego zestawu jednostek. Aby uzyskać więcej informacji na temat sposobu kontynuacji tokenów pracy, zobacz [limit czasu zapytania i dzielenia na strony](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Jeśli używasz biblioteki klienta usługi Storage go automatycznie obsługiwać tokenów kontynuacji dla Ciebie jako zwraca jednostki z usługi tabel. Poniższy przykład kodu C# automatycznie przy użyciu biblioteki klienta usługi Storage obsługuje tokeny kontynuacji, jeśli usługa table service zwraca je w odpowiedzi:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Poniższy kod C# obsługuje tokeny kontynuacji jawnie:  

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

### <a name="server-side-projection"></a>Projekcja po stronie serwera
Pojedynczy element może mieć maksymalnie 255 właściwości i mieć rozmiar maksymalnie 1 MB. Gdy odpytać tabelę i pobieranie jednostek, mogą nie być potrzebne wszystkie właściwości i uniknąć przesyłania danych niepotrzebnie (w celu zmniejszenia opóźnień i kosztów). Projekcja po stronie serwera umożliwia transfer tylko właściwości, których potrzebujesz. Poniższy przykład jest pobiera tylko **E-mail** właściwości (wraz z **PartitionKey**, **RowKey**, **sygnatura czasowa**i **ETag**) z jednostek wybranych przez zapytanie.  

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

Zwróć uwagę sposób, w jaki **RowKey** wartość jest dostępna, nawet jeśli nie zostało uwzględnione w listy właściwości do pobrania.  

## <a name="modifying-entities"></a>Modyfikowanie jednostek
Biblioteki klienta usługi Storage umożliwia modyfikowanie jednostek przechowywanych w usłudze tabeli, wstawianie, usuwanie i aktualizowanie jednostek. EGTs Umożliwia wsadowe wielu insert, update i operacje usuwania razem w celu zmniejszenia liczby rund wymagane i zwiększa wydajność rozwiązania.  

Należy pamiętać, że wyjątki zgłaszane, gdy biblioteka klienta magazynu EGT zwykle obejmują indeks jednostki, który spowodował partii nie powiedzie się. Jest to przydatne podczas debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projekt wpływa na sposób Twoja aplikacja kliencka obsługi operacji współbieżności i aktualizacji.  

### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa table service implementuje optymistycznej współbieżności testy na poziomie poszczególnych jednostek dla **Wstaw**, **scalania**, i **Usuń** operacji, mimo że go jest możliwe, aby klient wymusić uruchomienia usługi tabeli, aby pominąć te testy. Aby uzyskać więcej informacji na temat sposobu zarządzania współbieżności w usłudze table service, zobacz [Zarządzanie współbieżnością w Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Scalanie lub zastępowanie
**Zastąp** metody **TableOperation** klasy zawsze zastępuje całą jednostkę w usłudze Table service. Jeśli nie ma właściwości w żądaniu w jednostki przechowywanej istnieje tę właściwość, żądanie usuwa tę właściwość z jednostki przechowywanej. Jeśli nie chcesz jawnie usunąć właściwość z jednostki przechowywanej musi zawierać dla każdej właściwości w żądaniu.  

Możesz użyć **scalania** metody **TableOperation** klasy, aby zmniejszyć ilość danych przesyłanych do usługi Table zaktualizować jednostkę. **Scalania** metoda zastępuje wszystkie właściwości w jednostki przechowywanej wartości właściwości z jednostki zawarty w żądaniu, i pozostawienie bez zmian żadnych właściwości przechowywanych jednostki, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz dużych jednostek i wymaga tylko zaktualizowania niewielkiej liczby właściwości w żądaniu.  

> [!NOTE]
> **Zastąp** i **scalania** metody się nie powieść, jeśli jednostka nie istnieje. Alternatywnie, można użyć **InsertOrReplace** i **InsertOrMerge** metody, które Utwórz nową jednostkę, jeśli nie istnieje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Praca z typami encji heterogenicznych
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

Należy zauważyć, że każda jednostka może nadal mieć **PartitionKey**, **RowKey**, i **sygnatura czasowa** wartości, ale mogą mieć dowolny zbiór właściwości. Ponadto nie ma nic do wskazać typ jednostki, jeśli nie chcesz przechowywać tych informacji w jakimś miejscu. Dostępne są dwie opcje do identyfikowania typu jednostki:  

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

Techniki opisane w tej sekcji są szczególnie istotne w dyskusji [relacje dziedziczenia](table-storage-design-modeling.md#inheritance-relationships) wcześniej w tym przewodniku w artykule [modelowanie relacji](table-storage-design-modeling.md).  

> [!NOTE]
> Należy rozważyć, w tym numer wersji w wartości typu jednostki, aby umożliwić aplikacjom klienckim ewoluować obiektów POCO i pracy z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre z funkcji w bibliotece klienta usługi Storage, które ułatwiają pracę z wieloma typami jednostki w tej samej tabeli.  

### <a name="retrieving-heterogeneous-entity-types"></a>Trwa pobieranie typów jednostek heterogenicznych
Jeśli używasz biblioteki klienta usługi Storage są trzy opcje do pracy z wieloma typami encji.  

Jeśli jest znany typ jednostki przechowywane z określonym **RowKey** i **PartitionKey** wartości, a następnie po pobraniu jednostki, jak pokazano w poprzednich dwóch przykładach, można określić typu jednostki, Pobieranie jednostki typu **EmployeeEntity**: [Wykonywanie kwerendy punktu, przy użyciu biblioteki klienta magazynu](#executing-a-point-query-using-the-storage-client-library) i [Pobieranie wielu jednostek za pomocą LINQ](#retrieving-multiple-entities-using-linq).  

Drugą opcją jest użycie **DynamicTableEntity** typu (zbiór właściwości) zamiast konkretny typ jednostki POCO (Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby do serializacji i deserializacji jednostki do typów .NET). Poniższy kod C# potencjalnie pobiera wielu jednostek o różnych typach z tabeli, ale zwraca wszystkie jednostki jako **DynamicTableEntity** wystąpień. Następnie używa **EntityType** właściwości, aby określić typ każdego obiektu:  

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

Należy pamiętać, że można pobrać inne właściwości, należy użyć **TryGetValue** metody **właściwości** właściwość **DynamicTableEntity** klasy.  

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

### <a name="modifying-heterogeneous-entity-types"></a>Modyfikowanie jednostek heterogenicznych typów
Nie trzeba znać typ jednostki, aby go usunąć, i zawsze znasz typ jednostki po wstawieniu go. Można jednak użyć **DynamicTableEntity** typu, aby zaktualizować jednostkę, nie wiedząc o tym jej typ i bez użycia klasą jednostki POCO. Poniższy przykładowy kod umożliwia pobranie pojedynczej jednostki i sprawdza **EmployeeCount** istnieje właściwość przed aktualizacją.  

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

## <a name="controlling-access-with-shared-access-signatures"></a>Kontrolowanie dostępu przy użyciu sygnatury dostępu współdzielonego
Tokeny sygnatury dostępu współdzielonego (SAS) można użyć, aby umożliwić aplikacjom klienckim zmodyfikować (i wykonywanie zapytań) jednostek tabeli bez konieczności Uwzględnij klucz konta magazynu w kodzie. Zwykle istnieją trzy główne zalety za pomocą sygnatury dostępu Współdzielonego w Twojej aplikacji:  

* Nie trzeba rozpowszechniać klucz konta magazynu, platformę niezabezpieczone (na przykład urządzenie przenośne) w celu umożliwienia tego urządzenia do dostępu i modyfikowania obiektów w usłudze Table service.  
* Można odciążyć niektóre czynności, które role sieć web i proces roboczy wykonywania związanych z zarządzaniem jednostek na urządzeniach klienckich, takich jak na komputerach użytkowników końcowych i urządzeniach przenośnych.  
* Możesz przypisać ograniczone i czas ograniczony zestaw uprawnień do klienta (na przykład zezwalając na dostęp tylko do odczytu, do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów sygnatur dostępu Współdzielonego z usługą tabeli, zobacz [przy użyciu dostępu współdzielonego Signatures (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Jednakże nadal należy wygenerować tokeny sygnatur dostępu Współdzielonego, określającymi udzielenie aplikacji klienckiej jednostek w usłudze table service: należy to zrobić w środowisku z bezpiecznego dostępu do kluczy konta magazynu. Zazwyczaj używasz rola sieć web lub procesu roboczego do generowania tokenów sygnatur dostępu Współdzielonego i dostarczaj je na potrzeby aplikacji klienckich, które muszą mieć dostęp do jednostek. Ponieważ jest nadal obciążenie związane z generowania i dostarczania tokeny sygnatur dostępu Współdzielonego do klientów, należy rozważyć, jak najlepiej Aby zmniejszyć to obciążenie, szczególnie w przypadku dużej liczby scenariuszy.  

Istnieje możliwość wygenerowania tokenu sygnatury dostępu Współdzielonego, która udziela dostępu do podzestawu jednostek w tabeli. Domyślnie, możesz utworzyć token sygnatury dostępu Współdzielonego dla całej tabeli, jednak istnieje również możliwość określenia, czy token sygnatury dostępu Współdzielonego udzielić dostępu do jednej szeroką gamę **PartitionKey** wartości lub zakres **PartitionKey** i **RowKey** wartości. Możesz wybrać do generowania tokenów sygnatur dostępu Współdzielonego dla poszczególnych użytkowników systemu w taki sposób, że każdy użytkownik tokenu sygnatury dostępu Współdzielonego tylko umożliwia im dostęp do ich własnych jednostek w usłudze table service.  

## <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Podana rozpraszania żądań na wielu partycjach możesz zwiększyć szybkość reakcji przepływność i klienta, za pomocą asynchronicznego lub równoległego zapytań.
Na przykład Niewykluczone, że wystąpienia roli co najmniej dwóch procesu roboczego uzyskiwania dostępu do tabel równolegle. Możesz używać ról procesów roboczych indywidualnych, które odpowiada za określonych zestawów partycji lub po prostu ma wiele wystąpienia roli procesu roboczego, każdy mogli korzystać z wszystkich partycji w tabeli.  

W ramach wystąpienie klienta może zwiększyć przepływność, wykonując operacje magazynu asynchronicznie. Biblioteki klienta usługi Storage ułatwia pisanie zapytania asynchroniczne i modyfikacji. Na przykład możesz zacząć od metody synchronicznej, który pobiera wszystkie jednostki w partycji, jak pokazano w poniższym kodzie języka C#:  

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

Można łatwo modyfikować ten kod, tak aby zapytanie jest uruchamiane asynchronicznie, w następujący sposób:  

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

W tym przykładzie asynchronicznych można wyświetlić następujące zmiany z wersji synchronicznego:  

* Podpis metody zawiera teraz **async** modyfikator i zwraca **zadań** wystąpienia.  
* Zamiast wywoływać metodę **ExecuteSegmented** wywołuje metodę, aby pobrać wyniki, metoda teraz **ExecuteSegmentedAsync** metody i używa **await** modyfikatora Pobierz wyniki asynchronicznie.  

Aplikacja kliencka tę metodę można wywołać wiele razy (z różnymi wartościami dla **działu** parametru), a każda kwerenda zostanie uruchomiony w oddzielnym wątku.  

Należy pamiętać, że nie ma wersji asynchronicznej **Execute** method in Class metoda **TableQuery** klasy, ponieważ **IEnumerable** interfejs nie obsługuje asynchroniczne wyliczenie.  

Można także wstawianie, aktualizowanie i usuwanie jednostek asynchronicznie. Poniższy przykład C# pokazuje prosty, synchronicznej metody Wstawianie lub zastępowanie jednostki pracowników:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Można łatwo modyfikować ten kod, tak aby aktualizacja jest uruchamiane asynchronicznie, w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchronicznych można wyświetlić następujące zmiany z wersji synchronicznego:  

* Podpis metody zawiera teraz **async** modyfikator i zwraca **zadań** wystąpienia.  
* Zamiast wywoływać metodę **Execute** wywołuje metodę, aby zaktualizować jednostki, metoda teraz **ExecuteAsync** metody i używa **await** modyfikator do pobierania wyników asynchronicznie.  

Aplikacja kliencka można wywołać wiele metod asynchronicznych, takich jak ta, a następnie uruchomi każdego wywołania metody w oddzielnym wątku.  

## <a name="next-steps"></a>Kolejne kroki

- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych w tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)