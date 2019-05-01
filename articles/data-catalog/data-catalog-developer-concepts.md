---
title: Pojęcia dla deweloperów w usłudze Azure Data Catalog
description: Wprowadzenie do kluczowych koncepcji usługi Azure Data Catalog modelu koncepcyjnego jako narażonych za pośrednictwem interfejsu API REST katalogu.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 3cfd6bd453cd06be4676a806997697a71afb0b59
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727397"
---
# <a name="azure-data-catalog-developer-concepts"></a>Pojęcia dla deweloperów w usłudze Azure Data Catalog
Microsoft **usługi Azure Data Catalog** to usługa w pełni zarządzana usługa w chmurze, która zapewnia możliwości Odnajdowanie źródeł danych oraz crowdsourcing metadanych źródła danych. Deweloperzy mogą używać usługi za pośrednictwem jej interfejsów API REST. Zrozumienie z pojęciami dotyczącymi usługi jest ważne dla deweloperów pomyślnie zintegrować z usługą **usługi Azure Data Catalog**.

## <a name="key-concepts"></a>Kluczowe pojęcia
**Usługi Azure Data Catalog** modelu koncepcyjnego opiera się na czterech kluczowych pojęć: **Katalogu**, **użytkowników**, **zasoby**, i **adnotacje**.

![pojęcie][1]

*Rysunek 1. Usługa Azure Data Catalog uproszczony model koncepcyjny*

### <a name="catalog"></a>Wykaz
A **katalogu** jest kontenerem najwyższego poziomu dla wszystkich metadanych, które są przechowywane w organizacji. Istnieje **katalogu** dozwolonych dla jednego konta platformy Azure. Katalogi są powiązane z subskrypcją platformy Azure, ale tylko jeden **katalogu** mogą być tworzone dla danego konta platformy Azure, nawet jeśli konto może mieć wiele subskrypcji.

Zawiera wykaz **użytkowników** i **zasoby**.

### <a name="users"></a>Użytkownicy
Użytkownicy są podmiotów zabezpieczeń, które mają uprawnienia do wykonywania akcji (wyszukać w katalogu, dodawanie, edytowanie lub usuwanie elementów, itp.) w katalogu.

Istnieje kilka różnych ról, które użytkownik może mieć. Informacje w ramach ról można znaleźć w sekcji role i autoryzacji.

Można dodać poszczególnych użytkowników i grup zabezpieczeń.

Usługa Azure Data Catalog używa usługi Azure Active Directory do zarządzania tożsamościami i dostępem. Każdy użytkownik wykazu musi należeć do usługi Active Directory dla konta.

### <a name="assets"></a>Elementy zawartości
A **katalogu** zawiera zasoby danych. **Zasoby** jednostki zarządzany przez wykaz poziom szczegółowości.

Poziom szczegółowości element zawartości jest zależna od źródła danych. Dla programu SQL Server lub Oracle Database element zawartości może być tabelą ani widokiem. Dla programu SQL Server Analysis Services element zawartości może być miary, wymiary lub kluczowy wskaźnik wydajności (KPI). Dla programu SQL Server Reporting Services element zawartości jest raport.

**Zasobów** rzecz, dodawanie lub usuwanie z katalogu. Jest to jednostka wynik wrócić z **wyszukiwania**.

**Zasobów** składa się z nazwy, lokalizacji oraz typ i adnotacje opisujące dalsze go.

### <a name="annotations"></a>Adnotacje
Adnotacje są elementów reprezentujących metadane dotyczące zasobów.

Przykłady adnotacje opis tagów, schemat, dokumentacji, itp. Pełną listę typów zasobów i typów adnotacji znajdują się w sekcji zasobów obiektu modelu.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing adnotacje i perspektywy użytkownika (liczebność opinii)
Kluczowym aspektem usługi Azure Data Catalog jest jak obsługuje crowdsourcing metadanych w systemie. W przeciwieństwie do metody typu wiki — gdzie występuje tylko jeden opinii i ostatni składnik zapisywania usługi wins — model usługi Azure Data Catalog umożliwia wielu opinii na żywo obok siebie w systemie.

To podejście odzwierciedla rzeczywistych danych przedsiębiorstwa gdzie różni użytkownicy mogą mieć różnych perspektyw dla danego zasobu:

* Administrator bazy danych mogą dostarczyć informacji o umów dotyczących poziomu usług lub w oknie dostępne przetwarzania potrzeby zbiorczych operacji ETL
* Zarządca danych może dostarczyć informacji na temat procesów biznesowych, których dotyczy elementu zawartości lub klasyfikacjami firmy została zastosowana do niego
* Analityk finansowego może dostarczyć informacji o sposobie korzystania z danych podczas koniec okresu zadań raportowania

Aby zapewnić obsługę tego przykładu, każdy użytkownik — administrator bazy danych, zarządca danych i analityka — można dodać opis do pojedynczej tabeli, która została zarejestrowana w wykazie. Wszystkie opisy są obsługiwane w systemie, a w portalu usługi Azure Data Catalog są wyświetlane wszystkie opisy.

Ten wzorzec jest stosowany do większości elementów w modelu obiektów, więc typów obiektów w ładunku JSON są często tablic właściwości, których można by oczekiwać pojedynczego.

Na przykład w ramach zasobu główny jest Tablica obiektów opis. Właściwości tablicy nosi nazwę "opis". Obiekt opisu ma jedną właściwość — opis. Wzorzec jest, że każdy użytkownik, który opis typów pobiera obiekt opisu utworzone dla wartości, podane przez użytkownika.

Środowiska użytkownika można następnie wybrać sposób wyświetlania połączenie. Istnieją trzy różne wzorce do wyświetlenia.

* Najprostszy wzorzec składa się "Pokaż wszystko". W tym wzorcu wszystkie obiekty są wyświetlane w widoku listy. Portal usługi Azure Data Catalog UX używa tego wzorca, opis.
* Wzorzec innego to "Merge". W tym wzorcu wszystkie wartości z różnych użytkowników są scalane, za pomocą zduplikowane usunięte. Przykładem tego wzorca, w portalu usługi Azure Data Catalog UX są właściwości tagi i ekspertów.
* Trzeci wzorzec jest "ostatni składnik zapisywania usługi wins". W tym wzorcu jest wyświetlany tylko najnowszą wartość wpisana. friendlyName znajduje się przykład tego wzorca.

## <a name="asset-object-model"></a>Model obiektów zasobu
Wprowadzonego w sekcji najważniejsze pojęcia dotyczące **usługi Azure Data Catalog** model obiektu zawiera elementy, które mogą być zasoby lub adnotacji. Elementy mają właściwości, które mogą być opcjonalne lub wymagane. Niektóre właściwości mają zastosowanie do wszystkich elementów. Niektóre właściwości mają zastosowanie do wszystkich zasobów. Niektóre właściwości mają zastosowanie tylko do typów określonych zasobów.

### <a name="system-properties"></a>Właściwości systemu
<table><tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>Czas ostatniej modyfikacji elementu. To pole jest generowany przez serwer, gdy element jest wstawiany i za każdym razem, gdy element jest aktualizowana. Wartość tej właściwości jest ignorowana na dane wejściowe operacji publikowania.</td></tr><tr><td>id</td><td>Identyfikator URI</td><td>Bezwzględny adres url elementu (tylko do odczytu). Jest unikatowym identyfikatorem URI mogą być adresowane dla elementu.  Wartość tej właściwości jest ignorowana na dane wejściowe operacji publikowania.</td></tr><tr><td>type</td><td>String</td><td>Typ zasobu (tylko do odczytu).</td></tr><tr><td>etag</td><td>String</td><td>Ciąg odpowiadający wersji elementu, który może służyć do mechanizmu kontroli optymistycznej współbieżności, podczas wykonywania operacji, które aktualizują elementów w wykazie. "*" umożliwia dopasowanie dowolnej wartości.</td></tr></table>

### <a name="common-properties"></a>Wspólne właściwości
Te właściwości mają zastosowanie do wszystkich typów zasobów katalogu głównego i wszystkich typów adnotacji.

<table>
<tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boolean</td><td>Wskazuje, czy dane elementu jest pochodną systemie źródła (takich jak bazy danych Sql Server, Oracle Database), czy utworzone przez użytkownika.</td></tr>
</table>

### <a name="common-root-properties"></a>Wspólne właściwości katalogu głównego
<p>
Te właściwości mają zastosowanie do wszystkich typów zasobów katalogu głównego.

<table><tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr><tr><td>name</td><td>String</td><td>Nazwa tworzona na podstawie informacji o lokalizacji źródła danych</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Jednoznacznie opisuje źródła danych i jest jednym z identyfikatorów dla zasobu. (Patrz sekcja podwójną tożsamości).  Struktura język dsl jest zależna od typu protokołu i źródła.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Więcej szczegółów w typie zasób.</td></tr><tr><td>wartość elementu lastRegisteredBy</td><td>SecurityPrincipal</td><td>W tym artykule opisano użytkownika, który ostatnio zarejestrowany tego zasobu.  Zawiera zarówno Unikatowy identyfikator dla użytkownika (upn) i nazwę wyświetlaną (nazwisko i imię).</td></tr><tr><td>containerId</td><td>String</td><td>Identyfikator zasobu kontenera dla źródła danych. Ta właściwość nie jest obsługiwana dla typu kontenera.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Wspólne właściwości niepojedynczej adnotacji
Te właściwości mają zastosowanie do wszystkich typów niepojedynczej adnotacji (adnotacji, które może mieć wiele według zasobu).

<table>
<tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>key</td><td>String</td><td>Użytkownik określony klucz, który unikatowo identyfikuje adnotacji w bieżącej kolekcji. Długość klucza nie może przekraczać 256 znaków.</td></tr>
</table>

### <a name="root-asset-types"></a>Typy zasobów katalogu głównego
Typy zasobów katalogu głównego to tych typów, które reprezentują różne typy zasobów danych, które mogą być zarejestrowane w wykazie. Dla każdego typu głównego istnieje widok, w którym opisano zasobów i adnotacje uwzględnione w widoku. Nazwa widoku powinna być używana w odpowiadającym segmencie adresu url {view_name}, podczas publikowania zasobów przy użyciu interfejsu API REST.

<table><tr><td><b>Typ zasobu (nazwy widoku)</b></td><td><b>Dodatkowe właściwości</b></td><td><b>Typ danych</b></td><td><b>Dozwolone adnotacji</b></td><td><b>Komentarze</b></td></tr><tr><td>Tabela ("tabele")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Schemat<p>ColumnDescription<p>ColumnTag<p> Ekspert<p>Wersja zapoznawcza<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentacja<p></td><td>Tabela reprezentuje wszystkie dane tabelaryczne.  Na przykład: Tabela SQL, Widok SQL, tabela tabelarycznych usług Analysis Services, wielowymiarowych usług Analysis Services wymiaru, tabela bazy danych Oracle, itp.   </td></tr><tr><td>Miary ("środki")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja<p></td><td>Ten typ przedstawia miara usług Analysis Services.</td></tr><tr><td></td><td>miara</td><td>Kolumna</td><td></td><td>Metadane opisujące miary</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td></td><td>Określa, jeśli miara jest obliczana, czy nie.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Fizycznych kontenerów dla miary</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Fizycznych kontenerów dla miary</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Liczbowego wyrażenia MDX lub obliczeń, które zwraca docelowa wartość wskaźnika KPI.</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>Wyrażenie liczbowe MDX, które zwraca wartością rzeczywistą kluczowego wskaźnika wydajności.</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>Wyrażenie MDX, który reprezentuje stan kluczowego wskaźnika wydajności w określonym punkcie w czasie.</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>Wyrażenie MDX, które oblicza wartość wskaźnika KPI wraz z upływem czasu. Trend może być dowolnym opartych na czasie kryterium jest przydatne w kontekście firmy.</td>
<tr><td>Raport ("raporty")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja<p></td><td>Ten typ przedstawia raport usług SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>Kontener ("kontenery")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction<p>Dokumentacja<p></td><td>Ten typ przedstawia kontener inne zasoby, takie jak bazy danych SQL, kontener obiektów blob platformy Azure lub modelu usług Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Typy adnotacji
Adnotacja typów stanowią typy metadanych, które mogą być przypisane do innych typów w wykazie.

<table>
<tr><td><b>Typ adnotacji (nazwa widoku zagnieżdżone)</b></td><td><b>Dodatkowe właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>

<tr><td>Opis "(opisy)</td><td></td><td></td><td>Ta właściwość zawiera opis elementu zawartości. Każdy użytkownik systemu można dodać własne opis.  Tylko ten użytkownik może edytować obiekt opisu.  (Administratorzy i zawartości właściciele można usunąć obiektu opisu, ale nie można go edytować). System przechowuje opisy użytkowników osobno.  Ten sposób jest tablicą opisy dla każdego zasobu (po jednym dla każdego użytkownika, który współtworzonej ich wiedzę na temat zasobów, oprócz prawdopodobnie jeden, który zawiera informacje pochodzące ze źródła danych).</td></tr>
<tr><td></td><td>description</td><td>string</td><td>Krótki opis elementu zawartości (linie 2 – 3)</td></tr>

<tr><td>Tag ("tags")</td><td></td><td></td><td>Ta właściwość określa tag zasobu. Każdy użytkownik systemu można dodać kilka tagów dla zasobu.  Tylko użytkownik, który utworzył obiekty tagu, można je edytować.  (Administratorzy i zawartości właściciele można usunąć obiektu tagu, ale nie można go edytować). System obsługuje tagi użytkowników osobno.  Ten sposób jest Tablica obiektów tagu dla każdego zasobu.</td></tr>
<tr><td></td><td>tag</td><td>string</td><td>Tag, zawierająca opis elementu zawartości.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Ta właściwość zawiera przyjazną nazwę dla zasobu. FriendlyName jest adnotacja singleton — tylko jeden FriendlyName mogą być dodawane do elementu zawartości.  Tylko użytkownik, który utworzył obiekt FriendlyName, można go edytować. (Administratorzy i zawartości właściciele można usunąć obiektu FriendlyName, ale nie można go edytować). System przechowuje przyjaznych nazw użytkowników osobno.</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>Przyjazna nazwa elementu zawartości.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>Schemat opisujący strukturę danych.  Go Wyświetla listę nazw atrybutu (kolumny, atrybut, pole, itp.), typów, jak również inne metadane.  Informacja ta jest tworzony ze źródła danych.  Schemat jest adnotacja singleton — można dodać tylko jeden schemat dla zasobu.</td></tr>
<tr><td></td><td>Kolumny</td><td>[Kolumna]</td><td>Tablica obiektów kolumny. Opisano w nich kolumny za pomocą informacji pochodzących ze źródła danych.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Ta właściwość zawiera opis kolumny.  Każdy użytkownik systemu można dodać opisami dla wielu kolumn (co najwyżej jeden na kolumny). Tylko użytkownik, który utworzył obiekty ColumnDescription je edytować.  (Administratorzy i zawartości właściciele można usunąć obiektu ColumnDescription, ale nie można go edytować). System przechowuje opisy kolumn tych użytkowników oddzielnie.  Ten sposób jest Tablica obiektów ColumnDescription dla każdego zasobu (po jednej na kolumnę dla każdego użytkownika, który przyczynił się swoją wiedzą o kolumny oprócz prawdopodobnie jeden, który zawiera informacje pochodzące ze źródła danych).  ColumnDescription jest luźno powiązane ze schematem, dzięki czemu można uzyskać zsynchronizowane. ColumnDescription formułować kolumny, która już nie istnieje w schemacie.  Jest moduł zapisujący Synchronizuj opis i schematu.  Źródła danych mogą mieć również informacje dotyczące opisu kolumny i są dodatkowe obiekty ColumnDescription, które zostałyby utworzone podczas uruchamiania narzędzia.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Nazwa kolumny, której dotyczy ten opis.</td></tr>
<tr><td></td><td>description</td><td>String</td><td>Krótki opis (linie 2 – 3) kolumny.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Ta właściwość zawiera tag dla kolumny. Każdy użytkownik systemu można dodać wiele tagów dla danej kolumny i dodać znaczniki dla wielu kolumn. Tylko użytkownik, który utworzył obiekty ColumnTag je edytować. (Administratorzy i zawartości właściciele można usunąć obiektu ColumnTag, ale nie można go edytować). System obsługuje tagi kolumny tych użytkowników oddzielnie.  Ten sposób jest Tablica obiektów ColumnTag dla każdego zasobu.  ColumnTag jest luźno powiązane ze schematem, dzięki czemu można uzyskać zsynchronizowane. ColumnTag formułować kolumny, która już nie istnieje w schemacie.  Jest moduł zapisujący Synchronizuj tag kolumny i schematu.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Nazwa kolumny, której dotyczy ten znacznik.</td></tr>
<tr><td></td><td>tag</td><td>String</td><td>Tag, zawierający opis kolumny.</td></tr>

<tr><td>Ekspert ("ekspertów")</td><td></td><td></td><td>Ta właściwość zawiera użytkownika, który jest uważany za eksperta w zestawie danych. Bąbelkowy opinions(descriptions) przez ekspertów na początku środowiska użytkownika, wyświetlając opisy. Każdy użytkownik może określić własne ekspertów. Tylko ten użytkownik może edytować obiekt ekspertów. (Administratorzy i zawartości właściciele mogą usuwać obiekty ekspertów, ale nie można go edytować).</td></tr>
<tr><td></td><td>ekspert</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>("Wersje zapoznawcze") w wersji zapoznawczej</td><td></td><td></td><td>(Wersja zapoznawcza) zawiera migawkę pierwszych 20 wierszy danych dla elementu zawartości. (Wersja zapoznawcza) tylko ma sensu w przypadku niektórych typów zasobów (sens dla tabeli, ale nie dla miary).</td></tr>
<tr><td></td><td>wersja zapoznawcza</td><td>obiekt]</td><td>Tablica obiektów, które reprezentują kolumny.  Każdy obiekt ma właściwość mapowanie do kolumny z wartością dla tej kolumny dla wiersza.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Typ mime zawartości.</td></tr>
<tr><td></td><td>content</td><td>string</td><td>Instrukcje dotyczące sposobu uzyskania dostępu do tego zasobu danych. Zawartość może być adresem URL, adres e-mail lub zbiór instrukcji.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Liczba wierszy w zestawie danych</td></tr>
<tr><td></td><td>rozmiar</td><td>długi</td><td>Rozmiar w bajtach zestawu danych.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>Czas ostatniej modyfikacji schematu</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>Czas ostatniego zestawu danych został zmodyfikowany (danych zostało dodane, zmodyfikowane, lub usuń)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Kolumny</td></td><td>ColumnDataProfile[]</td><td>Tablica profilów danych kolumny.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>Nazwa ta klasyfikacja odnosi się do kolumny.</td></tr>
<tr><td></td><td>klasyfikacja</td><td>String</td><td>Klasyfikacja danych w tej kolumnie.</td></tr>

<tr><td>Dokumentacja ("dokumentacja")</td><td></td><td></td><td>Dany zasób może mieć tylko jeden dokumentacji skojarzonych z nim.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Typ mime zawartości.</td></tr>
<tr><td></td><td>content</td><td>string</td><td>Zawartość dokumentacji.</td></tr>

</table>

### <a name="common-types"></a>Standardowe typy
Popularne typy może służyć jako typy właściwości, ale nie są elementami.

<table>
<tr><td><b>Wspólny typ</b></td><td><b>Właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>Opisuje typ źródła danych.  Na przykład: SQL Server, Oracle Database, itp.  </td></tr>
<tr><td></td><td>Typ obiektu</td><td>string</td><td>Opisuje typ obiektu źródła danych. Na przykład: Tabela, widok dla programu SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokół</td><td>string</td><td>Wymagany. W tym artykule opisano protokół używany do komunikowania się ze źródłem danych. Na przykład: "tds" dla programu SQl Server "oracle" Oracle itp. Zapoznaj się <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">Specyfikacja odwołania - DSL struktury źródła danych</a> listę aktualnie obsługiwanych protokołów.</td></tr>
<tr><td></td><td>adres</td><td>Słownik&lt;ciąg, obiekt&gt;</td><td>Wymagany. Adres jest zestaw danych, które są specyficzne dla protokołu, który jest używany do identyfikowania źródła danych, do którego nastąpiło odwołanie. Dane adres zakresu określonego protokołu, czyli jest bez znaczenia, nie wiedząc o tym protokołu.</td></tr>
<tr><td></td><td>uwierzytelnianie</td><td>string</td><td>Opcjonalny. Schemat uwierzytelniania używany do komunikowania się ze źródłem danych. Na przykład: windows, oauth, itp.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Słownik&lt;ciąg, obiekt&gt;</td><td>Opcjonalny. Dodatkowe informacje na temat nawiązywania połączenia ze źródłem danych.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Wewnętrznej bazy danych nie wykonuje żadnych weryfikacji podanej właściwości względem usługi AAD podczas publikowania.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Unikatowy adres e-mail użytkownika. Musi być określona, jeśli nie zostanie podany identyfikator obiektu lub w kontekście właściwość "wartość elementu lastRegisteredBy", w przeciwnym razie opcjonalny.</td></tr>
<tr><td></td><td>Identyfikator obiektu</td><td>Guid</td><td>Tożsamości usługi AAD grupy użytkowników lub zabezpieczeń. Opcjonalny. Musi być określona, jeśli nazwy upn nie zostanie podany, w przeciwnym razie opcjonalne.</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>Imię użytkownika (w celach wyświetlania). Opcjonalny. Jest to prawidłowe tylko w kontekście właściwość "wartość elementu lastRegisteredBy". Nie można określić podczas dostarczania podmiotu zabezpieczeń "role", "uprawnienia" i "ekspertów".</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>Nazwisko użytkownika (w celach wyświetlania). Opcjonalny. Jest to prawidłowe tylko w kontekście właściwość "wartość elementu lastRegisteredBy". Nie można określić podczas dostarczania podmiotu zabezpieczeń "role", "uprawnienia" i "ekspertów".</td></tr>

<tr><td>Kolumna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nazwa kolumny lub atrybutu.</td></tr>
<tr><td></td><td>type</td><td>string</td><td>Typ danych kolumny lub atrybutu. Dopuszczalne typy są zależne od danych sourceType elementu zawartości.  Tylko podzbiór typów jest obsługiwany.</td></tr>
<tr><td></td><td>Element maxLength</td><td>int</td><td>Maksymalna dozwolona długość kolumny lub atrybutu. Tworzony na podstawie źródła danych. Dotyczy to tylko niektóre typy źródeł.</td></tr>
<tr><td></td><td>dokładność</td><td>bajt</td><td>Dokładność kolumny lub atrybutu. Tworzony na podstawie źródła danych. Dotyczy to tylko niektóre typy źródeł.</td></tr>
<tr><td></td><td>isNullable</td><td>Boolean</td><td>Czy kolumna może mieć wartości null lub nie. Tworzony na podstawie źródła danych. Dotyczy to tylko niektóre typy źródeł.</td></tr>
<tr><td></td><td>expression</td><td>string</td><td>Jeśli wartość jest kolumną obliczaną, to pole zawiera wyrażenie, które określa wartości. Tworzony na podstawie źródła danych. Dotyczy to tylko niektóre typy źródeł.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>Nazwa kolumny</td></tr>
<tr><td></td><td>type </td><td>string</td><td>Typ kolumny</td></tr>
<tr><td></td><td>min. </td><td>string</td><td>Minimalna wartość w zestawie danych</td></tr>
<tr><td></td><td>maks. </td><td>string</td><td>Maksymalna wartość w zestawie danych</td></tr>
<tr><td></td><td>średnio </td><td>double</td><td>Średnia wartość w zestawie danych</td></tr>
<tr><td></td><td>StDev </td><td>double</td><td>Odchylenie standardowe dla zestawu danych</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Liczba wartości null w zestawie danych</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Liczba odrębnych wartości w zestawie danych</td></tr>


</table>

## <a name="asset-identity"></a>Tożsamość zasobu
Usługa Azure Data Catalog używa właściwości "protocol" i tożsamości ze zbioru właściwości "address" właściwości "dsl" elementu DataSourceLocation do wygenerowania tożsamości zasobu, który jest wykorzystywana do adresowania zasobów w wykazie.
Na przykład protokołu "tds" ma właściwości tożsamości "server", "baza danych", "schema" i "object". Kombinacje protokołu i właściwości tożsamości są używane do generowania tożsamości zasobu tabel serwera SQL.
Usługa Azure Data Catalog zawiera kilka wbudowanych źródła protokołów, które są wymienione w części [Specyfikacja odwołania - DSL struktury źródła danych](data-catalog-dsr.md).
Zestaw obsługiwanych protokołów, które mogą zostać rozszerzone programowo (Zobacz odwołania API REST usługi Data Catalog). Administratorzy katalogu mogą zarejestrować niestandardowych protokołów źródeł danych. W poniższej tabeli opisano właściwości wymaganych do zarejestrowania protokołu niestandardowego.

### <a name="custom-data-source-protocol-specification"></a>Specyfikacja protokołu źródła danych niestandardowych
<table>
<tr><td><b>Typ</b></td><td><b>Właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>przestrzeń nazw</td><td>string</td><td>Przestrzeń nazw protokołu. Namespace musi mieć długość od 1 do 255 znaków długości, zawierać co najmniej jeden części niepuste, oddzielone znakiem kropki (.). Każda część musi mieć długość od 1 do 255 znaków, rozpoczynać się literą i zawierać tylko litery i cyfry.</td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nazwa protokołu. Nazwa musi mieć długość od 1 do 255 znaków, rozpoczynać się literą i zawierać tylko litery, cyfry i znak kreski (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista właściwości tożsamości, musi zawierać co najmniej jeden, ale nie więcej niż 20 właściwości. Na przykład: "server", "baza danych", "schema", "object" są właściwości tożsamości protokołu "tds".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista zestawów tożsamości. Określa zestawy właściwości tożsamości, które reprezentują tożsamość prawidłowy element zawartości. Musi zawierać co najmniej jeden, ale nie więcej niż 20 zestawów. Na przykład: {"server", "baza danych", "schema" i "obiekt"} jest tożsamością dla protokołu "tds", który określa tożsamość zasobu tabela programu Sql Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nazwa właściwości. Nazwa musi mieć długość od 1 do 100 znaków, rozpoczynać się literą i mogą zawierać tylko litery i cyfry.</td></tr>
<tr><td></td><td>type</td><td>string</td><td>Typ właściwości. Obsługiwane wartości: "bool" boolean ","bajtów","guid","int","integer","długi","string","url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Wskazuje, czy przypadek mają być ignorowane w przypadku korzystania z wartości właściwości. Można określić tylko dla właściwości typu "string". Wartość domyślna to false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Wskazuje, czy przypadek mają być ignorowane w przypadku każdego segmentu ścieżki adresu url. Można określić tylko dla właściwości typu "url". Wartość domyślna to [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nazwa tożsamości zestawu.</td></tr>
<tr><td></td><td>properties</td><td>ciąg]</td><td>Lista właściwości tożsamości zawarte w tej tożsamości zestawu. Nie może zawierać duplikatów. Każda właściwość odwołuje się zestaw tożsamości musi być zdefiniowany na liście "identityProperties" protokołu.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Role i autoryzacji
Microsoft Azure Data Catalog udostępnia funkcje autoryzacji dostępu do operacji CRUD na zasoby i adnotacji.

## <a name="key-concepts"></a>Kluczowe pojęcia
Usługa Azure Data Catalog korzysta z dwóch mechanizmów autoryzacji:

* Autoryzacja oparta na rolach
* Autoryzacja na podstawie uprawnień

### <a name="roles"></a>Role
Istnieją trzy role: **Administrator**, **właściciela**, i **Współautor**.  Każda rola ma swój zakres i praw, które są podsumowane w poniższej tabeli.

<table><tr><td><b>Rola</b></td><td><b>Zakres</b></td><td><b>Prawa</b></td></tr><tr><td>Administrator</td><td>Katalog (wszystkie zasoby/adnotacje w katalogu)</td><td>ViewRoles Delete odczytu

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Właściciel</td><td>Każdy zasób (element główny)</td><td>ViewRoles Delete odczytu

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Współautor</td><td>Każdy poszczególnych zasobów i adnotacji</td><td>Odczyt aktualizacji usuń ViewRoles Uwaga: wszystkie prawa zostaną odwołane po odebraniu odczytu bezpośrednio na element z Współautor</td></tr></table>

> [!NOTE]
> **Odczyt**, **aktualizacji**, **Usuń**, **ViewRoles** prawa są stosowane do dowolnego elementu (zasobów lub adnotacji) podczas **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** dotyczą tylko zasobów katalogu głównego.
> 
> **Usuń** prawo dotyczy elementów i podelementów lub pojedynczego elementu podrzędne. Na przykład usunięcie zasobu spowoduje również usunięcie wszelkich adnotacji dla tego zasobu.
> 
> 

### <a name="permissions"></a>Uprawnienia
Uprawnienie jest jako lista wpisów kontroli dostępu. Każdy wpis kontroli dostępu przypisuje zestawu praw do podmiotu zabezpieczeń. Uprawnienia można określić tylko dla elementu zawartości (czyli element główny) i zastosować do elementu zawartości i wszelkich podrzędnych.

Podczas **usługi Azure Data Catalog** tylko podgląd **odczytu** po prawej stronie jest obsługiwana na liście uprawnień do scenariusza ograniczyć widoczność elementu zawartości.

Domyślnie każdy uwierzytelniony użytkownik ma **odczytu** prawym przyciskiem myszy dla każdego elementu w katalogu, chyba że widoczność jest ograniczona do zestawu jednostek w uprawnieniach.

## <a name="rest-api"></a>Interfejs API REST
**Umieść** i **WPIS** elementu widoku żądań może służyć do kontrolowania role i uprawnienia: oprócz ładunku element można określić dwie właściwości systemu **role** i  **uprawnienia**.

> [!NOTE]
> **uprawnienia** dotyczy to tylko element główny.
> 
> **Właściciel** roli dotyczy to tylko element główny.
> 
> Domyślnie, gdy element zostanie utworzony w katalogu jego **Współautor** jest ustawiana obecnie uwierzytelnionego użytkownika. Jeśli element ma być nadaje się do aktualizacji dla każdej osoby, **Współautor** powinna być równa &lt;wszyscy&gt; specjalne podmiot zabezpieczeń w **role** opublikowanych właściwości, gdy element jest pierwszy (zobacz na poniższym przykładzie). **Współautor** nie można zmienić, i pozostaje taka sama podczas okresu istnienia elementu (nawet **administratora** lub **właściciela** nie ma prawo do zmiany **Współautor**). To jedyna wartość obsługiwana w przypadku jawnego ustawienia **Współautor** jest &lt;wszyscy&gt;: **Współautor** może być tylko użytkownika, który utworzył element lub &lt;wszyscy&gt;.
> 
> 

### <a name="examples"></a>Przykłady
**Ustaw Współautor &lt;wszyscy&gt; podczas publikowania elementu.**
Podmiot zabezpieczeń specjalne &lt;wszyscy&gt; ma objectId "00000000-0000-0000-0000-000000000201".
  **WPIS** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Niektóre implementacje klienta HTTP może automatycznie ponownie wykonaj żądania w odpowiedzi 302 z serwera, ale zazwyczaj paska nagłówki autoryzacji w żądaniu. Nagłówek autoryzacji jest wymagane, aby wysyłać żądania do usługi Azure Data Catalog, musisz zapewnić, że nagłówek autoryzacji jest nadal podawana podczas ponownego wystawienia żądanie przekierowania lokalizacji określonej przez usługę Azure Data Catalog. Następujący przykładowy kod pokazuje go za pomocą obiektu .NET HttpWebRequest.
> 
> 

**Treść**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **Przypisz właścicieli i ograniczyć widoczność istniejącego elementu głównego**: **Umieść** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> W PUT nie jest to wymagane do określenia ładunków elementu w treści: PUT może służyć do aktualizacji, po prostu ról lub uprawnień.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
