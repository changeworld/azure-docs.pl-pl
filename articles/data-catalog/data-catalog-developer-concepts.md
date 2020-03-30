---
title: Pojęcia deweloperów usługi Azure Data Catalog
description: Wprowadzenie do kluczowych pojęć w modelu koncepcyjnym usługi Azure Data Catalog, udostępniane za pośrednictwem interfejsu API REST katalogu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976827"
---
# <a name="azure-data-catalog-developer-concepts"></a>Pojęcia deweloperów usługi Azure Data Catalog
**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która zapewnia możliwości odnajdowania źródeł danych i metadanych źródła danych crowdsourcingu. Deweloperzy mogą korzystać z usługi za pośrednictwem interfejsów API REST. Zrozumienie koncepcji zaimplementowanych w usłudze jest ważne dla deweloperów, aby pomyślnie zintegrować się z **usługą Azure Data Catalog.**

## <a name="key-concepts"></a>Kluczowe pojęcia
Model koncepcyjny **usługi Azure Data Catalog** opiera się na czterech kluczowych pojęciach: **Wykaz,** **Użytkownicy,** **Zasoby**i **Adnotacje**.

![Ilustracja koncepcyjna usługi Azure Data Catalog](./media/data-catalog-developer-concepts/concept2.png)

*Rysunek 1 — uproszczony model koncepcyjny usługi Azure Data Catalog*

### <a name="catalog"></a>Wykaz
**Wykaz** jest kontenerem najwyższego poziomu dla wszystkich metadanych magazynowanych przez organizację. Na konto platformy Azure dozwolony jest jeden **katalog.** Katalogi są powiązane z subskrypcją platformy Azure, ale tylko jeden **katalog** można utworzyć dla dowolnego konta platformy Azure, nawet jeśli konto może mieć wiele subskrypcji.

Katalog zawiera **użytkowników** i **zasoby**.

### <a name="users"></a>Użytkownicy
Użytkownicy są podmiotami zabezpieczeń, które mają uprawnienia do wykonywania akcji (przeszukiwania katalogu, dodawania, edytowania lub usuwania elementów itp.) w katalogu.

Istnieje kilka różnych ról, które użytkownik może mieć. Aby uzyskać informacje na temat ról, zobacz sekcję Role i Autoryzacja.

Można dodawać indywidualnych użytkowników i grupy zabezpieczeń.

Usługa Azure Data Catalog używa usługi Azure Active Directory do zarządzania tożsamościami i dostępem. Każdy użytkownik katalogu musi być członkiem usługi Active Directory dla konta.

### <a name="assets"></a>Elementy zawartości
**Wykaz** zawiera zasoby danych. **Zasoby** są jednostką szczegółowości zarządzaną przez katalog.

Ziarnistość zasobu zależy od źródła danych. W przypadku programu SQL Server lub bazy danych Oracle Database zasób może być tabelą lub widokiem. W przypadku usług SQL Server Analysis Services zasób może być miarą, wymiarem lub kluczowym wskaźnikiem wydajności (KPI). W przypadku usług SQL Server Reporting Services zasób jest raportem.

**Zasób** to rzecz dodana lub emimiętna z katalogu. Jest to jednostka wyniku można uzyskać **z**search .

**Zasób** składa się z jego nazwy, lokalizacji i typu oraz adnotacji, które dalej go opisują.

### <a name="annotations"></a>Adnotacje
Adnotacje to elementy reprezentujące metadane dotyczące zasobów.

Przykładami adnotacji są opis, tagi, schemat, dokumentacja itp. Pełna lista typów zasobów i typów adnotacji znajduje się w sekcji Modelu obiektu zasobów.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Adnotacje crowdsourcingowe i perspektywa użytkownika (mnogość opinii)
Kluczowym aspektem usługi Azure Data Catalog jest sposób, w jaki obsługuje crowdsourcing metadanych w systemie. W przeciwieństwie do podejścia wiki — gdzie istnieje tylko jedna opinia i wygrywa ostatni moduł zapisujący — model usługi Azure Data Catalog umożliwia wiele opinii do życia obok siebie w systemie.

Takie podejście odzwierciedla rzeczywisty świat danych przedsiębiorstwa, w którym różni użytkownicy mogą mieć różne perspektywy dla danego zasobu:

* Administrator bazy danych może dostarczać informacje o umowach dotyczących poziomu usług lub dostępnym oknie przetwarzania dla zbiorczych operacji ETL
* Administrator danych może dostarczyć informacji o procesach biznesowych, do których stosuje się składnik aktywów, lub o klasyfikacjach, które firma zastosowała do niego
* Analityk finansowy może dostarczyć informacji o tym, jak dane są wykorzystywane podczas zadań sprawozdawczych na koniec okresu

Aby obserwować ten przykład, każdy użytkownik — DBA, steward danych i analityk — można dodać opis do pojedynczej tabeli, która została zarejestrowana w katalogu. Wszystkie opisy są obsługiwane w systemie, a w portalu usługi Azure Data Catalog są wyświetlane wszystkie opisy.

Ten wzorzec jest stosowany do większości elementów w modelu obiektu, więc typy obiektów w ładunku JSON są często tablice dla właściwości, gdzie można oczekiwać singleton.

Na przykład w katalogu głównym zasobu znajduje się tablica obiektów opisu. Właściwość tablicy nosi nazwę "descriptions". Obiekt opisu ma jedną właściwość - opis. Wzorzec jest, że każdy użytkownik, który wpisuje opis pobiera obiekt opisu utworzone dla wartości dostarczonej przez użytkownika.

Środowisko użytkownika może następnie wybrać sposób wyświetlania kombinacji. Istnieją trzy różne wzorce do wyświetlania.

* Najprostszym wzorem jest "Pokaż wszystko". W tym wzorcu wszystkie obiekty są wyświetlane w widoku listy. Środowisko użytkownika portalu usługi Azure Data Catalog używa tego wzorca do opisu.
* Innym wzorcem jest "Merge". W tym wzorcu wszystkie wartości od różnych użytkowników są scalane razem, z duplikatem usunięte. Przykładami tego wzorca w UX portalu usługi Azure Data Catalog są tagi i właściwości ekspertów.
* Trzeci wzór to "ostatni pisarz wygrywa". W tym wzorcu wyświetlana jest tylko najnowsza wpisana wartość. friendlyName jest przykładem tego wzorca.

## <a name="asset-object-model"></a>Model obiektu zasobów
Zgodnie z wprowadzeniem w sekcji Pojęcia klucza model obiektu **usługi Azure Data Catalog** zawiera elementy, które mogą być zasobami lub adnotacjami. Elementy mają właściwości, które mogą być opcjonalne lub wymagane. Niektóre właściwości mają zastosowanie do wszystkich elementów. Niektóre właściwości mają zastosowanie do wszystkich zasobów. Niektóre właściwości mają zastosowanie tylko do określonych typów zasobów.

### <a name="system-properties"></a>Właściwości systemu
<table><tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr><tr><td>sygnatura czasowa</td><td>DateTime</td><td>Ostatni raz element został zmodyfikowany. To pole jest generowane przez serwer po wstawieniu elementu i każdej aktualizacji elementu. Wartość tej właściwości jest ignorowana po wprowadzeniu operacji publikowania.</td></tr><tr><td>id</td><td>Identyfikator uri</td><td>Bezwzględny adres URL elementu (tylko do odczytu). Jest to unikatowy adresowalny identyfikator URI dla elementu.  Wartość tej właściwości jest ignorowana po wprowadzeniu operacji publikowania.</td></tr><tr><td>type</td><td>Ciąg</td><td>Typ zasobu (tylko do odczytu).</td></tr><tr><td>Etag</td><td>Ciąg</td><td>Ciąg odpowiadający wersji elementu, który może służyć do kontroli współbieżności optymistyczne podczas wykonywania operacji, które aktualizują elementy w katalogu. "*" może być używany do dopasowania dowolnej wartości.</td></tr></table>

### <a name="common-properties"></a>Wspólne właściwości
Te właściwości mają zastosowanie do wszystkich typów zasobów głównych i wszystkich typów adnotacji.

<table>
<tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>fromSourceSystem</td><td>Wartość logiczna</td><td>Wskazuje, czy dane elementu pochodzą z systemu źródłowego (takiego jak baza danych programu Sql Server, baza danych Oracle Database) czy są one autorstwa użytkownika.</td></tr>
</table>

### <a name="common-root-properties"></a>Typowe właściwości główne
<p>
Te właściwości mają zastosowanie do wszystkich typów zasobów głównych.

<table><tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr><tr><td>name</td><td>Ciąg</td><td>Nazwa pochodząca z informacji o lokalizacji źródła danych</td></tr><tr><td>Dsl</td><td>Lokalizacja źródła danych</td><td>Unikatowo opisuje źródło danych i jest jednym z identyfikatorów zasobu. (Patrz sekcja podwójnej tożsamości).  Struktura dsl zależy od protokołu i typu źródła.</td></tr><tr><td>Datasource</td><td>DataSourceInfo</td><td>Więcej szczegółów na temat typu zasobu.</td></tr><tr><td>ostatnioZarejestrowane Przez</td><td>OchronaPrincipal</td><td>Opisuje użytkownika, który ostatnio zarejestrował ten zasób.  Zawiera zarówno unikatowy identyfikator użytkownika (upn), jak i nazwę wyświetlaną (nazwisko i imię).</td></tr><tr><td>identyfikator kontenera</td><td>Ciąg</td><td>Identyfikator zasobu kontenera dla źródła danych. Ta właściwość nie jest obsługiwana dla typu Container.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Typowe właściwości adnotacji bez pojedynczego przycisku
Właściwości te mają zastosowanie do wszystkich typów adnotacji innych niż singleton (adnotacje, które mogą być wielokrotne na środek trwały).

<table>
<tr><td><b>Nazwa właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>key</td><td>Ciąg</td><td>Klucz określony przez użytkownika, który jednoznacznie identyfikuje adnotację w bieżącej kolekcji. Długość klucza nie może przekraczać 256 znaków.</td></tr>
</table>

### <a name="root-asset-types"></a>Typy zasobów głównych
Typy zasobów głównych to te typy, które reprezentują różne typy zasobów danych, które mogą być zarejestrowane w katalogu. Dla każdego typu głównego istnieje widok, który opisuje zasób i adnotacje zawarte w widoku. Nazwa widoku powinna być używana w odpowiednim segmencie adresu URL {view_name} podczas publikowania zasobu przy użyciu interfejsu API REST.

<table><tr><td><b>Typ zasobu (nazwa widoku)</b></td><td><b>Dodatkowe właściwości</b></td><td><b>Typ danych</b></td><td><b>Dozwolone adnotacje</b></td><td><b>Komentarze</b></td></tr><tr><td>Tabela ("tabele")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Schemat<p>Opis kolumny<p>Tag kolumny<p> Ekspert<p>Wersja zapoznawcza<p>AccessInstruction (Struktura dostępu)<p>TableDataProfile<p>ColumnDataProfile (Profil danych kolumn)<p>Klasyfikacja danych kolumn<p>Dokumentacja<p></td><td>Tabela reprezentuje wszystkie dane tabelaryczne.  Na przykład: tabela SQL, widok SQL, tabela tabeli tabeli usług Analysis Services, wielowymiarowy wymiar usług Analysis Services, tabela Oracle itp.   </td></tr><tr><td>Środek ("środki")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction (Struktura dostępu)<p>Dokumentacja<p></td><td>Ten typ reprezentuje miarę usług Analysis Services.</td></tr><tr><td></td><td>Miara</td><td>Kolumna</td><td></td><td>Metadane opisujące miarę</td></tr><tr><td></td><td>isObliczona </td><td>Wartość logiczna</td><td></td><td>Określa, czy miara jest obliczana, czy nie.</td></tr><tr><td></td><td>Measuregroup</td><td>Ciąg</td><td></td><td>Fizyczny pojemnik do pomiaru</td></tr><td>Wskaźnik KPI ("kpis")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction (Struktura dostępu)<p>Dokumentacja</td><td></td></tr><tr><td></td><td>Measuregroup</td><td>Ciąg</td><td></td><td>Fizyczny pojemnik do pomiaru</td></tr><tr><td></td><td>celWyrażenie</td><td>Ciąg</td><td></td><td>Wyrażenie liczbowe MDX lub obliczenie, które zwraca wartość docelową kluczowego wskaźnika wydajności.</td></tr><tr><td></td><td>wartośćWyrażenie</td><td>Ciąg</td><td></td><td>Wyrażenie numeryczne MDX, które zwraca rzeczywistą wartość kluczowego wskaźnika wydajności.</td></tr><tr><td></td><td>statusWyrażenie</td><td>Ciąg</td><td></td><td>Wyrażenie MDX, które reprezentuje stan kluczowego wskaźnika wydajności w określonym momencie.</td></tr><tr><td></td><td>trendWyjaśnienie</td><td>Ciąg</td><td></td><td>Wyrażenie MDX, które ocenia wartość kluczowego wskaźnika wydajności w czasie. Trend może być dowolnym kryterium czasowym, które jest przydatne w określonym kontekście biznesowym.</td>
<tr><td>Raport ("raporty")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction (Struktura dostępu)<p>Dokumentacja<p></td><td>Ten typ reprezentuje raport usług SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Ciąg</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy (Sóbekstwostwo)</td><td>Ciąg</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Ciąg</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Ciąg</td><td></td><td></td></tr><tr><td>Kontener ("kontenery")</td><td></td><td></td><td>Opis<p>FriendlyName<p>Tag<p>Ekspert<p>AccessInstruction (Struktura dostępu)<p>Dokumentacja<p></td><td>Ten typ reprezentuje kontener innych zasobów, takich jak baza danych SQL, kontener obiektów blob platformy Azure lub model usług Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Typy adnotacji
Typy adnotacji reprezentują typy metadanych, które można przypisać do innych typów w katalogu.

<table>
<tr><td><b>Typ adnotacji (nazwa widoku zagnieżdżonego)</b></td><td><b>Dodatkowe właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>

<tr><td>Opis ("opisy")</td><td></td><td></td><td>Ta właściwość zawiera opis zasobu. Każdy użytkownik systemu może dodać swój własny opis.  Tylko ten użytkownik może edytować obiekt Opis.  (Administratorzy i właściciele zasobów mogą usuwać obiekt Opis, ale nie edytować go). System przechowuje opisy użytkowników oddzielnie.  W związku z tym istnieje tablica opisów na każdy zasób (po jednym dla każdego użytkownika, który przekazał swoją wiedzę na temat zasobu, oprócz ewentualnie taki, który zawiera informacje pochodzące ze źródła danych).</td></tr>
<tr><td></td><td>description</td><td>ciąg</td><td>Krótki opis (2-3 wiersze) składnika aktywów</td></tr>

<tr><td>Tag ("tagi")</td><td></td><td></td><td>Ta właściwość definiuje znacznik dla zasobu. Każdy użytkownik systemu może dodać wiele tagów dla zasobu.  Tylko użytkownik, który utworzył obiekty znaczników, może je edytować.  (Administratorzy i właściciele zasobów mogą usuwać obiekt Tag, ale nie edytować go). System przechowuje tagi użytkowników oddzielnie.  W związku z tym istnieje tablica Tag obiektów na każdym zasobie.</td></tr>
<tr><td></td><td>tag</td><td>ciąg</td><td>Tag opisujący zasób.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Ta właściwość zawiera przyjazną nazwę zasobu. FriendlyName jest adnotacją singleton — tylko jeden FriendlyName można dodać do zasobu.  Tylko użytkownik, który utworzył obiekt FriendlyName, może go edytować. (Administratorzy i właściciele zasobów mogą usuwać obiekt FriendlyName, ale nie edytować go). System zachowuje przyjazne nazwy użytkowników oddzielnie.</td></tr>
<tr><td></td><td>Friendlyname</td><td>ciąg</td><td>Przyjazna nazwa zasobu.</td></tr>

<tr><td>Schemat ("schemat")</td><td></td><td></td><td>Schemat opisuje strukturę danych.  Zawiera listę nazw atrybutów (kolumna, atrybut, pole itp.), typów, a także innych metadanych.  Wszystkie te informacje pochodzą ze źródła danych.  Schemat jest adnotacją singleton - dla zasobu można dodać tylko jeden schemat.</td></tr>
<tr><td></td><td>columns</td><td>Kolumna[]</td><td>Tablica obiektów kolumn. Opisują one kolumnę z informacjami pochodzącymi ze źródła danych.</td></tr>

<tr><td>Opis kolumny ("columnDescriptions")</td><td></td><td></td><td>Ta właściwość zawiera opis kolumny.  Każdy użytkownik systemu może dodawać własne opisy dla wielu kolumn (co najwyżej po jednej na kolumnę). Tylko użytkownik, który utworzył obiekty ColumnDescription, może je edytować.  (Administratorzy i właściciele zasobów mogą usuwać obiekt ColumnDescription, ale nie edytować go). System przechowuje opisy kolumn tych użytkowników oddzielnie.  W związku z tym istnieje tablica ColumnDescription obiektów na każdy zasób (po jednym na kolumnę dla każdego użytkownika, który przyczynił się ich wiedzy na temat kolumny oprócz ewentualnie jeden, który zawiera informacje pochodzące ze źródła danych).  ColumnDescription jest luźno powiązany ze schematem, dzięki czemu można uzyskać zsynchronizowane. ColumnDescription może opisywać kolumnę, która już nie istnieje w schemacie.  To do modułu zapisującego, aby zachować opis i schemat w synchronizacji.  Źródło danych może również mieć informacje o opisie kolumn i są to dodatkowe obiekty ColumnDescription, które zostaną utworzone podczas uruchamiania narzędzia.</td></tr>
<tr><td></td><td>columnName</td><td>Ciąg</td><td>Nazwa kolumny, do w odniesieniu do tej kolumny.</td></tr>
<tr><td></td><td>description</td><td>Ciąg</td><td>krótki opis (2-3 wiersze) kolumny.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Ta właściwość zawiera znacznik dla kolumny. Każdy użytkownik systemu może dodać wiele tagów dla danej kolumny i może dodawać znaczniki dla wielu kolumn. Tylko użytkownik, który utworzył obiekty ColumnTag, może je edytować. (Administratorzy i właściciele zasobów mogą usuwać obiekt ColumnTag, ale nie edytować go). System przechowuje znaczniki kolumn tych użytkowników oddzielnie.  W związku z tym istnieje tablica ColumnTag obiektów na każdym zasobie.  ColumnTag jest luźno powiązany ze schematem, dzięki czemu może wyjść z synchronizacji. ColumnTag może opisać kolumnę, która już nie istnieje w schemacie.  Do modułu zapisującego należy synchronizacja tagu kolumny i schematu.</td></tr>
<tr><td></td><td>columnName</td><td>Ciąg</td><td>Nazwa kolumny, do w odniesieniu do tej etykiety.</td></tr>
<tr><td></td><td>tag</td><td>Ciąg</td><td>Tag opisujący kolumnę.</td></tr>

<tr><td>Ekspert ("eksperci")</td><td></td><td></td><td>Ta właściwość zawiera użytkownika, który jest uważany za eksperta w zestawie danych. Opinie ekspertów (opisy) bańki do góry UX podczas aukcji opisów. Każdy użytkownik może określić własnych ekspertów. Tylko ten użytkownik może edytować obiekt ekspertów. (Administratorzy i właściciele zasobów mogą usuwać obiekty eksperta, ale nie edytować ich).</td></tr>
<tr><td></td><td>Ekspertów</td><td>OchronaPrincipal</td><td></td></tr>

<tr><td>Podgląd ("podglądy")</td><td></td><td></td><td>Podgląd zawiera migawkę 20 pierwszych wierszy danych dla zasobu. Podgląd ma sens tylko dla niektórych typów zasobów (ma sens dla tabeli, ale nie dla miary).</td></tr>
<tr><td></td><td>podgląd</td><td>obiekt[]</td><td>Tablica obiektów, które reprezentują kolumnę.  Każdy obiekt ma mapowanie właściwości do kolumny z wartością dla tej kolumny dla wiersza.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Mimetype</td><td>ciąg</td><td>Typ mime zawartości.</td></tr>
<tr><td></td><td>content</td><td>ciąg</td><td>Instrukcje dotyczące uzyskiwania dostępu do tego zasobu danych. Zawartość może być adresem URL, adresem e-mail lub zestawem instrukcji.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>liczbaOfRows</td></td><td>int</td><td>Liczba wierszy w zestawie danych</td></tr>
<tr><td></td><td>size</td><td>long</td><td>Rozmiar w bajtach zestawu danych.  </td></tr>
<tr><td></td><td>schematModifiedTime</td><td>ciąg</td><td>Ostatni raz schemat został zmodyfikowany</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>ciąg</td><td>Ostatni raz zestaw danych został zmodyfikowany (dane zostały dodane, zmodyfikowane lub usunięte)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>Tablica profili danych kolumn.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Ciąg</td><td>Nazwa kolumny, do w odniesieniu do tej klasyfikacji.</td></tr>
<tr><td></td><td>klasyfikacja</td><td>Ciąg</td><td>Klasyfikacja danych w tej kolumnie.</td></tr>

<tr><td>Dokumentacja ("dokumentacja")</td><td></td><td></td><td>Dany zasób może mieć tylko jedną dokumentację skojarzoną z nim.</td></tr>
<tr><td></td><td>Mimetype</td><td>ciąg</td><td>Typ mime zawartości.</td></tr>
<tr><td></td><td>content</td><td>ciąg</td><td>Zawartość dokumentacji.</td></tr>

</table>

### <a name="common-types"></a>Typowe typy
Typowe typy mogą być używane jako typy właściwości, ale nie są elementy.

<table>
<tr><td><b>Typ wspólny</b></td><td><b>Właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Sourcetype</td><td>ciąg</td><td>Opisuje typ źródła danych.  Na przykład: SQL Server, Oracle Database itp.  </td></tr>
<tr><td></td><td>Objecttype</td><td>ciąg</td><td>Opisuje typ obiektu w źródle danych. Na przykład: Tabela, Widok dla programu SQL Server.</td></tr>

<tr><td>Lokalizacja źródła danych</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokół</td><td>ciąg</td><td>Wymagany. W tym artykule opisano protokół używany do komunikowania się ze źródłem danych. Na przykład: "tds" dla SQl Server, "oracle" dla Oracle itp. Zobacz <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">specyfikację źródła danych — Struktura DSL</a> dla listy aktualnie obsługiwanych protokołów.</td></tr>
<tr><td></td><td>adres</td><td>Ciąg&lt;słownika, obiekt&gt;</td><td>Wymagany. Adres to zestaw danych specyficznych dla protokołu, który jest używany do identyfikowania źródła danych, do którego się odwołuje. Dane adresowe są ograniczone do określonego protokołu, co oznacza, że nie ma znaczenia, nie znając protokołu.</td></tr>
<tr><td></td><td>uwierzytelnianie</td><td>ciąg</td><td>Element opcjonalny. Schemat uwierzytelniania używany do komunikowania się ze źródłem danych. Na przykład: windows, oauth, itp.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Ciąg&lt;słownika, obiekt&gt;</td><td>Element opcjonalny. Dodatkowe informacje dotyczące łączenia się ze źródłem danych.</td></tr>

<tr><td>OchronaPrincipal</td><td></td><td></td><td>Wewnętrznej bazy danych nie wykonuje sprawdzania poprawności podanych właściwości względem AAD podczas publikowania.</td></tr>
<tr><td></td><td>Upn</td><td>ciąg</td><td>Unikalny adres e-mail użytkownika. Należy określić, jeśli objectId nie jest podana lub w kontekście "lastRegisteredBy" właściwość, w przeciwnym razie opcjonalne.</td></tr>
<tr><td></td><td>Objectid</td><td>Guid (identyfikator GUID)</td><td>Tożsamość usługi AAD użytkownika lub grupy zabezpieczeń. Element opcjonalny. Należy określić, jeśli upn nie jest pod warunkiem, w przeciwnym razie opcjonalne.</td></tr>
<tr><td></td><td>firstName</td><td>ciąg</td><td>Imię użytkownika (do celów wyświetlania). Element opcjonalny. Prawidłowa tylko w kontekście właściwości "lastRegisteredBy". Nie można określić podczas dostarczania podmiotu zabezpieczeń dla "role", "uprawnienia" i "eksperci".</td></tr>
<tr><td></td><td>lastName</td><td>ciąg</td><td>Nazwisko użytkownika (do celów wyświetlania). Element opcjonalny. Prawidłowa tylko w kontekście właściwości "lastRegisteredBy". Nie można określić podczas dostarczania podmiotu zabezpieczeń dla "role", "uprawnienia" i "eksperci".</td></tr>

<tr><td>Kolumna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa kolumny lub atrybutu.</td></tr>
<tr><td></td><td>type</td><td>ciąg</td><td>typu danych kolumny lub atrybutu. Typy dopuszczalne zależą od źródła danychTyp zasobu.  Obsługiwany jest tylko podzbiór typów.</td></tr>
<tr><td></td><td>Maxlength</td><td>int</td><td>Maksymalna długość dozwolona dla kolumny lub atrybutu. Pochodzi ze źródła danych. Dotyczy tylko niektórych typów źródeł.</td></tr>
<tr><td></td><td>Precyzji</td><td>byte</td><td>Dokładność kolumny lub atrybutu. Pochodzi ze źródła danych. Dotyczy tylko niektórych typów źródeł.</td></tr>
<tr><td></td><td>Isnullable</td><td>Wartość logiczna</td><td>Czy kolumna może mieć wartość null, czy nie. Pochodzi ze źródła danych. Dotyczy tylko niektórych typów źródeł.</td></tr>
<tr><td></td><td>wyrażenie</td><td>ciąg</td><td>Jeśli wartość jest kolumną obliczeniową, to pole zawiera wyrażenie, które wyraża wartość. Pochodzi ze źródła danych. Dotyczy tylko niektórych typów źródeł.</td></tr>

<tr><td>ColumnDataProfile (Profil danych kolumn)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>ciąg</td><td>Nazwa kolumny</td></tr>
<tr><td></td><td>type </td><td>ciąg</td><td>Typ kolumny</td></tr>
<tr><td></td><td>min </td><td>ciąg</td><td>Minimalna wartość w zestawie danych</td></tr>
<tr><td></td><td>max </td><td>ciąg</td><td>Maksymalna wartość w zestawie danych</td></tr>
<tr><td></td><td>Średnia </td><td>double</td><td>Średnia wartość w zestawie danych</td></tr>
<tr><td></td><td>Stdev </td><td>double</td><td>Odchylenie standardowe dla zestawu danych</td></tr>
<tr><td></td><td>liczba nullCount </td><td>int</td><td>Liczba wartości null w zestawie danych</td></tr>
<tr><td></td><td>Distinctcount  </td><td>int</td><td>Liczba różnych wartości w zestawie danych</td></tr>


</table>

## <a name="asset-identity"></a>Tożsamość zasobu
Usługa Azure Data Catalog używa właściwości "protokołu" i tożsamości z worka właściwości "adres" właściwości DataSourceLocation "dsl" do generowania tożsamości zasobu, który jest używany do adresowania zasobu wewnątrz wykazu.
Na przykład protokół "tds" ma właściwości tożsamości "serwer", "baza danych", "schemat" i "obiekt". Kombinacje protokołu i właściwości tożsamości są używane do generowania tożsamości zasobu tabeli programu SQL Server.
Usługa Azure Data Catalog udostępnia kilka wbudowanych protokołów źródła danych, które są wymienione w [specyfikacji źródła danych — DSL Structure](data-catalog-dsr.md).
Zestaw obsługiwanych protokołów można rozszerzyć programowo (zapoznaj się z odwołaniem do interfejsu API REST katalogu danych). Administratorzy katalogu mogą rejestrować niestandardowe protokoły źródła danych. W poniższej tabeli opisano właściwości potrzebne do zarejestrowania protokołu niestandardowego.

### <a name="custom-data-source-protocol-specification"></a>Specyfikacja niestandardowego protokołu źródła danych
<table>
<tr><td><b>Typ</b></td><td><b>Właściwości</b></td><td><b>Typ danych</b></td><td><b>Komentarze</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>ciąg</td><td>Obszar nazw protokołu. Obszar nazw musi mieć od 1 do 255 znaków długości, zawierać jedną lub więcej niepustych części oddzielonych kropką (.). Każda część musi mieć od 1 do 255 znaków, zaczynać się od litery i zawierać tylko litery i cyfry.</td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa protokołu. Nazwa musi mieć od 1 do 255 znaków, zaczynać się od litery i zawierać tylko litery, cyfry i znak kreski (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista właściwości tożsamości, musi zawierać co najmniej jeden, ale nie więcej niż 20 właściwości. Na przykład: "serwer", "baza danych", "schemat", "obiekt" są właściwościami tożsamości protokołu "tds".</td></tr>
<tr><td></td><td>zestawy tożsamości</td><td>Zestaw identyfikacji datasourceprotocolIdentitySet[]</td><td>Lista zestawów tożsamości. Definiuje zestawy właściwości tożsamości, które reprezentują tożsamość prawidłowego zasobu. Musi zawierać co najmniej jeden, ale nie więcej niż 20 zestawów. Na przykład: {"server", "database", "schema" i "object"} to zestaw tożsamości dla protokołu "tds", który definiuje tożsamość zasobu tabeli programu Sql Server.</td></tr>

<tr><td>DataSourceProtocolIdentityNierowawość</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa właściwości. Nazwa musi mieć od 1 do 100 znaków, zaczynać się od litery i zawierać tylko litery i cyfry.</td></tr>
<tr><td></td><td>type</td><td>ciąg</td><td>Typ właściwości. Obsługiwane wartości: "bool", boolean", "byte", "guid", "int", "integer", "long", "string", "url"</td></tr>
<tr><td></td><td>Ignorecase</td><td>bool</td><td>Wskazuje, czy przypadek powinien być ignorowany podczas korzystania z wartości właściwości. Można określić tylko dla właściwości typu "string". Wartość domyślna jest false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreKładka</td><td>bool[]</td><td>Wskazuje, czy przypadek powinien być ignorowany dla każdego segmentu ścieżki adresu URL. Można określić tylko dla właściwości typu "url". Wartością domyślną jest [false].</td></tr>

<tr><td>Zestaw identyfikatorów DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>ciąg</td><td>Nazwa zestawu tożsamości.</td></tr>
<tr><td></td><td>properties</td><td>ciąg[]</td><td>Lista właściwości tożsamości zawartych w tym zestawie tożsamości. Nie może zawierać duplikatów. Każda właściwość, do których odwołuje się zestaw tożsamości, musi być zdefiniowana na liście "właściwości tożsamości" protokołu.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Role i autoryzacja
Usługa Microsoft Azure Data Catalog udostępnia funkcje autoryzacji operacji CRUD dla zasobów i adnotacji.

## <a name="key-concepts"></a>Kluczowe pojęcia
Usługa Azure Data Catalog używa dwóch mechanizmów autoryzacji:

* Autoryzacja oparta na rolach
* Autoryzacja oparta na uprawnieniach

### <a name="roles"></a>Role
Istnieją trzy role: **Administrator,** **Właściciel**i **Współautor**.  Każda rola ma swój zakres i prawa, które są podsumowane w poniższej tabeli.

<table><tr><td><b>Roli</b></td><td><b>Zakres</b></td><td><b>Prawa</b></td></tr><tr><td>Administrator</td><td>Katalog (wszystkie zasoby/adnotacje w katalogu)</td><td>Przeczytaj usuń zazacie

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Właściciel</td><td>Każdy zasób (element główny)</td><td>Przeczytaj usuń zazacie

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Współautor</td><td>Każdy indywidualny zasób i adnotacja</td><td>Przeczytaj aktualizację Usuń ViewRoles Uwaga: wszystkie prawa są odwołane, jeśli prawo do odczytu elementu jest odwołany z współautora</td></tr></table>

> [!NOTE]
> **Read**, **Update**, **Delete**, **ViewRoles** prawa mają zastosowanie do dowolnego elementu (zasobu lub adnotacji), podczas gdy **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** mają zastosowanie tylko do zasobu głównego.
> 
> **Usuń** prawo ma zastosowanie do elementu i wszelkich podelementów lub pojedynczego elementu pod nim. Na przykład usunięcie zasobu powoduje również usunięcie adnotacji dla tego zasobu.
> 
> 

### <a name="permissions"></a>Uprawnienia
Uprawnienie jest jako lista wpisów kontroli dostępu. Każdy wpis kontroli dostępu przypisuje zestaw praw do podmiotu zabezpieczeń. Uprawnienia można określić tylko w odniesieniu do zasobu (czyli elementu głównego) i zastosować do zasobu i wszelkich podelementów.

Podczas podglądu **usługi Azure Data Catalog** tylko prawo do **odczytu** jest obsługiwane na liście uprawnień, aby włączyć scenariusz, aby ograniczyć widoczność zasobu.

Domyślnie każdy uwierzytelniony użytkownik ma prawo **do odczytu** dla dowolnego elementu w katalogu, chyba że widoczność jest ograniczona do zestawu podmiotów w uprawnieniach.

## <a name="rest-api"></a>Interfejs API REST
**Żądania** elementów widoku PUT i **POST** mogą służyć do kontrolowania ról i uprawnień: oprócz ładunku elementu można **określić** dwie właściwości systemu i **uprawnienia**.

> [!NOTE]
> **uprawnienia** mają zastosowanie tylko do elementu głównego.
> 
> **Rola właściciela** ma zastosowanie tylko do elementu głównego.
> 
> Domyślnie, gdy element jest tworzony w katalogu jego **współautor** jest ustawiony na aktualnie uwierzytelnionego użytkownika. Jeśli element powinien być aktualizowany przez wszystkich,&gt; **współautor** powinien być ustawiony na &lt;Wszyscy specjalnego podmiotu zabezpieczeń we właściwości **role,** gdy element jest po raz pierwszy opublikowany (patrz w poniższym przykładzie). **Współautora** nie można zmienić i pozostaje to samo w okresie życia elementu (nawet **Administrator** lub **Właściciel** nie ma prawa do zmiany **Współautora).** Jedyną wartością obsługiwaną dla jawnego &lt;&gt;ustawienia **współautora** jest Wszyscy: **Współautorem** może być tylko użytkownik, który utworzył element lub &lt;Wszyscy&gt;.
> 
> 

### <a name="examples"></a>Przykłady
**Ustaw współautora na &lt;wszystkich&gt; podczas publikowania elementu.**
Specjalny podmiot &lt;&gt; zabezpieczeń Każdy ma objectId "00000000-0000-0000-0000-0000000000201".
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Niektóre implementacje klienta HTTP mogą automatycznie ponownie wydawać żądania w odpowiedzi na 302 z serwera, ale zazwyczaj pozbawiają nagłówki autoryzacji z żądania. Ponieważ nagłówek autoryzacji jest wymagany do żądania do usługi Azure Data Catalog, należy upewnić się, że nagłówek autoryzacji jest nadal dostarczany podczas ponownego wystawiania żądania do lokalizacji przekierowania określonej przez usługę Azure Data Catalog. Poniższy przykładowy kod demonstruje go przy użyciu obiektu .NET HttpWebRequest.
> 
> 

**Treść**
```json
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
```

  **Przypisywanie właścicieli i ograniczanie widoczności istniejącego elementu głównego**: **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

```json
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
```

> [!NOTE]
> W PUT nie jest wymagane, aby określić ładunek elementu w treści: PUT może służyć do aktualizacji tylko role i/lub uprawnienia.
> 
