---
title: What's New in Azure Data Catalog
description: Ten artykuł zawiera omówienie nowych możliwości dodanych do usługi Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 86c8e8c10811b1478ae2c853f1efef5b6b5caa83
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406332"
---
# <a name="whats-new-in-azure-data-catalog"></a>What's new in Azure Data Catalog
Aktualizacje **usługi Azure Data Catalog** są regularnie wydawane. Nie każda wersja zawiera nowe funkcje widocznych dla użytkownika, niektórych wersjach koncentrują się na możliwości usługi zaplecza. Ta strona prezentuje nowe możliwości widocznych dla użytkownika dodane do usługi Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>What's new for listopada 2017 r. 
Począwszy od listopada 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługa łączenia bezpośrednio do terminów słownika biznesowego określonych w portalu usługi Data Catalog. Użytkownikom można skopiować łącza z słownik biznesowy i osadzanie ich w dokumentów, wiadomości e-mail, raportów lub w innych lokalizacjach, aby bezpośrednio połączyć się z definicji termin słownika.
* Pomoc techniczna dla jednostki usługi Azure Active Directory. Administratorzy wykazu danych można autoryzować aplikacji klienckich za pomocą jednostki usługi dostępu do wykazu i można przyznać te aplikacje określone uprawnienia tak, jak powodują udzielenie uprawnień dla użytkowników i grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).
* Obsługa uwierzytelniania usługi Azure Active Directory podczas nawiązywania połączenia ze źródłami danych Azure SQL Database i Azure SQL Data Warehouse, za pomocą narzędzia rejestracji źródła danych wykazu danych. Aby uzyskać więcej informacji, zobacz [użyciu Azure uwierzytelnianie usługi Active Directory do uwierzytelniania przy użyciu bazy danych SQL Database lub SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>What's new for września 2017 r. 
We wrześniu 2017 następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Dołącz do metadanych relacji ze źródeł danych bazy danych DB2 obsługę wyodrębniania, zapisując powiązane tabele za pomocą narzędzia rejestracji źródła danych.
* Obsługa rejestracji bazy danych MongoDB w wersji 3.4 zdroje przy użyciu narzędzia rejestracji źródła danych.
* Obsługa usunięcie wszystkich metadanych dla obiektów znajdujących się w ramach jednej operacji, podczas usuwania innych kontenera lub bazy danych z wykazu danych.
* Obsługa wyświetlania maksymalnie 1000 tagów, terminów słownika biznesowego lub inne rodzaje wyszukiwania zestawów reguł, uściślając listę wyszukiwania w portalu usługi Data Catalog.


## <a name="whats-new-for-august-2017"></a>What's new for sierpnia 2017 r. 
Począwszy od sierpnia 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

*   Nowe próbki dla deweloperów jest dostępny do tworzenia i zarządzania metadanych relacji za pomocą interfejsu API REST usługi Data Catalog. *Importowania informacji o relacji do wykazu danych* przykład jest dostępny w [strona przykładów kodu Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Dołącz do metadanych relacji ze źródeł danych Teradata obsługę wyodrębniania, zapisując powiązane tabele za pomocą narzędzia rejestracji źródła danych.
* Obsługa funkcji zwracającej tabelę programu SQL Server (TVF) obiektów podczas rejestrowania źródeł danych programu SQL Server przy użyciu narzędzia rejestracji źródła danych.
* Wiele aktualizacji i uściślenia, aby zwiększyć wydajność i użyteczność portalu usługi Data Catalog.

## <a name="whats-new-for-july-2017"></a>What's new for lipca 2017 r. 
Począwszy od lipca 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Pomoc techniczna dla bardziej precyzyjną kontrolę nad tym operacji na metadanych najszerszym:
    - Administratorzy wykazu, można ograniczyć użytkownikowi możliwość współtworzenia tagów i powiązanych metadanych do wykazu, włączenie dostępu tylko do odczytu do katalogu.
    - Administratorzy katalogu mogą ograniczyć użytkownikom rejestrować nowe źródła w katalogu.
    - Administratorzy wykazu można ograniczyć użytkownikowi możliwość przejęcie na własność metadane zasobów danych w wykazie.
    - Można udzielić uprawnienia do grup zabezpieczeń usługi Azure Active Directory i użytkowników w celu ułatwienia zarządzania uprawnieniami.
* Obsługa relacji między zarejestrowanych zasobów danych i odnajdywanie zasobów danych powiązanych w portalu usługi Data Catalog, w tym:
    - Wyodrębnianie metadanych relacji z programu SQL Server (w tym usługi Azure SQL Database), Oracle i MySQL źródeł danych, korzystając z narzędzia rejestracji źródła danych wykazu danych.
    - Odnajdywanie zasobów powiązanych danych, podczas wyświetlania metadanych zasobów w portalu usługi Data Catalog.
    - Operacje, aby zdefiniować, odnajdywanie i zarządzanie relacjami między zasoby danych przy użyciu interfejsu API REST usługi Data Catalog.

Aby uzyskać więcej informacji na temat zarządzania uprawnieniami w usłudze Data Catalog, zobacz [sposób zabezpieczania dostępu do wykazu danych i zasobów danych](data-catalog-how-to-secure-catalog.md).
Więcej informacji na temat relacji w usłudze Data Catalog, zobacz [sposobu wyświetlania zasobów powiązanych danych w usłudze Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>What's new czerwca 2017 r. 
Począwszy od czerwca 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Pomoc techniczna dla źródła danych programu Sybase, bazy danych Apache Cassandra i bazy danych MongoDB. Użytkownicy mogą teraz rejestrowanie i odnajdywanie bazy danych Cassandra i usługi MongoDB bazy danych i tabel oraz baz danych programu Sybase, tabel i widoków.

> [!NOTE]
> Podczas rejestrowania tabel bazy danych MongoDB i bazy danych Cassandra, które zawierają kolumn o złożonych typach danych takich jak tablice i rekordów, te kolumny będą nie objęte metadane dodawane do usługi Data Catalog.

## <a name="whats-new-for-may-2017"></a>What's new for maja 2017 r. 
Od maja 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Nowe próbki dla deweloperów jest dostępny dla zbiorczym importowaniem terminów słownika biznesowego. Przykład importowania zbiorczego słownik jest dostępny w [strony przykłady dla deweloperów Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Obsługa, edytując informacje dotyczące połączenia ODBC w portalu usługi Azure Data Catalog. Właściciele zasobów danych i administratorów wykazu danych edytować informacje o połączeniu dla zarejestrowanego źródła danych ODBC bez konieczności ponownego zarejestrowania źródeł danych.
*   Obsługa możesz klikać adresy URL w definicji słownik biznesowych i opisy. Gdy adresy URL znajdują się w oknie właściwości Opis i definicji terminów słownika biznesowego, adresy URL będą wyświetlane jako hiperlinki w portalu usługi Data Catalog.
*   Obsługa wyświetlania nazw ekspertów oprócz adresy e-mail ekspertów. Podczas przeglądania ekspertów we właściwościach zasobu danych w portalu usługi Data Catalog, pełna nazwa eksperta z usługi Azure Active Directory będą wyświetlane w etykietce narzędzia.

## <a name="whats-new-for-april-2017"></a>What's new for kwietnia 2017 r. 
Od kwietnia 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Pomoc techniczna dla źródła danych ODBC. Użytkownicy mogą teraz rejestrowanie i odnajdywanie baz danych ODBC, tabel i widoków przy użyciu narzędzia rejestracji źródła danych wykazu danych.

## <a name="whats-new-for-march-2017"></a>What's new for marca 2017 r. 
Począwszy od marca 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Wsparcie dla korzystania z grup zabezpieczeń usługi AAD dla administratorów wykazu danych.
*   Usługa Azure Data Catalog jest teraz dostępna w nowym regionie platformy Azure. Klienci mogą aprowizować usługi Azure Data Catalog, w obszarze zachodnio-środkowe stany USA, oprócz wschodnie stany USA, zachodnie stany USA, Europa Zachodnia i Australia Wschodnia, Europa Północna i Azja południowo-wschodnia. Aby uzyskać więcej informacji, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>What's new for lutego 2017 r. 
Począwszy od lutego 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Obsługa zaawansowanych ustawień w narzędzia rejestracji źródła danych wykazu danych. Użytkownicy mogą określić wartości limitu czasu polecenia podczas rejestrowania z dużymi źródłami danych.
*   Obsługa protokołu FTP i PostgreSQL źródeł danych. Użytkownicy mogą teraz rejestrowanie i odnajdywanie FTP pliki i katalogi i bazy danych postgresql w warstwie, tabele i widoki.

## <a name="whats-new-for-january-2017"></a>What's new for stycznia 2017 r. 
Począwszy od stycznia 2017 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Usługa Azure Data Catalog jest teraz [CSA STAR](https://www.microsoft.com/en-us/trustcenter/compliance/csa-star-certification) zgodne.
*   Integracja z usługą [Pobierz i Transformuj w programie Excel 2016 i dodatku Power Query dla programu Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Excel użytkownicy mogą udostępniać zapytania i Odkryj zapytań przy użyciu usługi Azure Data Catalog z wewnątrz programu Excel. Ta funkcja jest dostępna dla użytkowników z licencjami na usługę Power BI Pro.

## <a name="whats-new-for-december-2016"></a>What's new for grudnia 2016 r.
Od grudnia 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Usługa Azure Data Catalog jest teraz [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) i [klauzule modelowe UE](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) zgodne.
*   Obsługa, edytując informacje dotyczące połączenia źródła danych. Właściciele zasobów danych i administratorów wykazu danych edytować informacje o połączeniu dla zarejestrowanych źródeł danych bez konieczności ponownego zarejestrowania źródeł danych.
*   Pomoc techniczna dla źródeł danych w witrynie Salesforce.com. Użytkownicy mogą teraz rejestrowanie i odnajdywanie obiektów usługi Salesforce.


## <a name="whats-new-for-november-2016"></a>What's new for listopada 2016 r.
Od listopada 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:
*   Usługa Azure Data Catalog jest teraz [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) i [ISO/IEC 27018](https://www.microsoft.com/en-us/TrustCenter/Compliance/iso-iec-27018) zgodne.
*   Obsługa ręcznej rejestracji źródła danych ODBC za pomocą portalu Data Catalog i interfejsu API REST.

## <a name="whats-new-for-september-2016"></a>What's new for września 2016 r.
Od września 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Pomoc techniczna dla źródła danych programu IBM DB2. Użytkownicy mogą teraz rejestrowanie i odnajdywanie baz danych, tabele i widoki bazy danych DB2.
* Pomoc techniczna dla źródeł danych usługi Azure Cosmos DB. Użytkownicy mogą teraz zarejestrować i odnajdowanie baz danych Cosmos DB i kolekcje.
* Obsługa Dostosowywanie nazwy katalogu, w portalu usługi Data Catalog. Administratorzy wykazu może teraz Podaj tekst, który jest wyświetlany w tytule portalu, takie jak nazwa organizacji.

## <a name="whats-new-for-august-2016"></a>What's new for sierpnia 2016 r.
Począwszy od sierpnia 2016 następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Ulepszenia rejestracji źródła danych programu SQL Server Master Data Services (MDS). Użytkownicy mogą teraz dodawać podglądy i dane profilów, podczas rejestrowania jednostek usług MDS przy użyciu narzędzia rejestracji źródła danych wykazu danych.
* Obsługa zdefiniowanych przez administratora organizacji zapisanych wyszukiwań. Podczas zapisywania wyszukiwania w portalu usługi Data Catalog, wykaz danych Administratorzy mogą teraz wybrać zapisywanie wyszukiwań do użytku osobistego lub dla wszystkich użytkowników wykazu. Zapisanych wyszukiwań w organizacji są udostępniane wszystkim użytkownikom w katalogu i może spowodować uzyskanie standardowych punktów początkowych Odnajdowanie źródeł danych.
* Aktualizacje, aby wyświetlić właściwości w portalu usługi Data Catalog. Wszystkie właściwości zasobu danych są teraz wyświetlane i zarządzane w pojedynczej, o zmiennym rozmiarze okienko, aby zapewnić bardziej spójny i wykrywalny środowisko.

## <a name="whats-new-for-july-2016"></a>What's new for lipca 2016 r.
Począwszy od lipca 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Pomoc techniczna dla źródła danych programu SQL Server Master Data Services (MDS). Użytkownicy mogą teraz rejestrowanie i odnajdywanie usług MDS modeli i jednostek.
* Obsługa procedur składowanych serwera SQL Server. Użytkownicy mogą teraz rejestrowanie i odnajdywanie obiektów procedury składowanej w źródłach danych programu SQL Server.
* Obsługa dodatkowych języków w portalu usługi Azure Data Catalog i narzędzia rejestracji źródła danych, łącznie z 18 obsługiwanych języków. Środowisko użytkownika usługi Azure Data Catalog jest zlokalizowana w oparciu o preferencje językowe, ustawić w Windows lub w przeglądarce sieci web.
* Aktualizacje i Uszczegółowienie wykazu danych strony głównej portalu, w tym ulepszenia wydajności i doświadczenie usprawnione użytkownika.

## <a name="whats-new-for-june-2016"></a>What's new for czerwca 2016 r.
Począwszy od czerwca 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługa zmiana rozmiaru kolumn w widoku listy, podczas odnajdywania zasobów danych w portalu usługi Data Catalog. Użytkownikom można teraz zmienić rozmiar poszczególnych kolumn można łatwiej odczytać metadanych długich zasobów, takich jak tagi i opisy.
* Dodatku Power Query dla programu Excel został został dodany do menu "Otwórz w" w portalu usługi Data Catalog. Użytkownikom można teraz otwierać obsługiwanych źródeł danych w programie Excel 2016 lub Excel 2010 i 2013 programu Excel z [dodatku Power Query dla programu Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) zainstalowany dodatek.
* Pomoc techniczna dla źródeł danych w usłudze Azure Table Storage. Użytkownicy mogą teraz rejestrowanie i odnajdywanie obiektów tabeli w źródłach danych usługi Azure Storage.

## <a name="whats-new-for-may-2016"></a>What's new for maja 2016 r.
Począwszy od maja 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Słownik biznesowy, który umożliwia administratorom wykazu Definiowanie terminów biznesowych i hierarchie do utworzenia wspólnego słownictwa biznesowego. Użytkownicy, można oznaczyć zarejestrowanych zasobów danych za pomocą terminy słownika aby ułatwić odnajdywanie i zrozumienie zawartości katalogu. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Jak skonfigurować słownik biznesowy na potrzeby znakowania zarządzanego).  
* Ulepszenia słownik biznesowy wykazu danych, który pozwala użytkownikom na aktualizowanie wielu terminy słownika w ramach jednej operacji. Użytkownicy mogą wybrać wiele wersji warunków do edycji następujące pola:
  * Termin nadrzędny: Użytkownik może wybrać nowy termin nadrzędny, a wszystkie wybrane warunki są zaktualizowane pod kątem dzieci termin nadrzędny wybrany. Jeśli wybrane warunki wszystkie mają ten sam element nadrzędny, a następnie elementem nadrzędnym jest wyświetlana w polu tekstowym, w przeciwnym razie termin nadrzędny, pole jest ustawione na puste.   
  * Znaczniki i zainteresowanych stron: użytkowników można dodawać i usuwać tagi i uczestnikom projektu dla wielu terminy słownika przy użyciu tego samego środowiska jako znakowanie wielu zasobów danych.

> [!NOTE]
> Słownik biznesowy jest dostępna tylko w Standard Edition usługi Azure Data Catalog. Bezpłatna wersja nie zapewnia możliwości znakowanie zarządzanych lub słownika biznesowego.

## <a name="whats-new-for-march-2016"></a>What's new for marca 2016 r.
Począwszy od marca 2016 r. następujące funkcje zostały dodane do platformy Azure Data Catalog: g:

* Skonsolidowane punkt końcowy interfejsu API REST programowy dostęp do możliwości wyszukiwania i wykazu zarządzanie zasobami usługi Azure Data Catalog. Ten punkt końcowy interfejsu API punktu końcowego i wykaz interfejsów API wyszukiwania została przestarzały i wycofane 21 marca 2016 r. Brak zmian do semantyki interfejsu API. Tylko punkt końcowy zmienić identyfikatora URI. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST dla danych wykazu, Azure](https://msdn.microsoft.com/library/azure/mt267595.aspx). Aby uzyskać przykłady interfejsu API, zobacz [Przykłady dewelopera usługi Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>What's new for lutego 2016 r.
Od lutego 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Wybór źródła danych zaprojektowaną na nowo witrynę środowisko w narzędzia rejestracji źródła danych usługi Azure Data Catalog. Narzędzia rejestracji źródła danych został zaktualizowany tak, aby ułatwić znalezienie i wybierz ze źródeł danych obsługiwanych przez usługę Azure Data Catalog.
* Obsługa 10 dodatkowych języków w portalu usługi Azure Data Catalog i narzędzia rejestracji źródła danych. Oprócz języka angielskiego środowisko usługi Azure Data Catalog jest teraz dostępna w niemiecki, hiszpański, francuski, włoski, japoński, koreański, portugalski (Brazylia), rosyjski, chiński (uproszczony) i chińskim tradycyjnym. Środowisko użytkownika usługi Azure Data Catalog jest zlokalizowana w oparciu o preferencje językowe, ustawić w Windows lub w przeglądarce sieci web.
* Obsługa replikacji geograficznej usługi Azure Data Catalog danych biznesowych ciągłość działalności biznesowej i odzyskiwanie po awarii. Cała zawartość usługi Azure Data Catalog, w tym adnotacji dodawanych i metadanych danych źródłowych, teraz są replikowane między dwoma regionami platformy Azure bez dodatkowych kosztów klientom. Regiony platformy Azure wstępnie są skojarzone, co najmniej 500 mil od siebie i postępuj zgodnie z mapowania, zgodnie z opisem w [firm ciągłości działania i odzyskiwania po awarii (BCDR): regiony sparowane platformy Azure](../best-practices-availability-paired-regions.md).
* Obsługa zmiany subskrypcji platformy Azure używane przez usługę Azure Data Catalog. Administratorzy usługi Azure Data Catalog umożliwia strony ustawień w portalu usługi Azure Data Catalog wybierz inną subskrypcję platformy Azure na potrzeby rozliczeń.

## <a name="whats-new-for-january-2016"></a>What's new for stycznia 2016 r.
Począwszy od stycznia 2016 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługa ręcznie rejestrowania dodatkowych źródeł danych. Możesz teraz użyć "Utwórz wpis ręczny" w portalu usługi Azure Data Catalog, lub użyj interfejsu API REST usługi Azure Data Catalog, aby zarejestrować następujące źródła danych:
  * OData - funkcja zestawu jednostek i kontener jednostek
  * HTTP - pliku, punkt końcowy, raport i lokacji
  * System plików — plik
  * SharePoint — lista
  * FTP - plików i katalogów
  * SalesForce.com — obiekt
  * Bazy danych DB2 — tabeli, widoku i bazy danych
  * PostgreSQL — tabeli, widoku i bazy danych
* Obsługa "Otwórz w SQL Server Data Tools" dla źródła danych programu SQL Server (w tym usługi Azure SQL DB i Azure SQL Data Warehouse).  
* Obsługa rejestracji i odnajdywania widoków platformy SAP HANA i pakietów. Można zarejestrować źródła danych SAP HANA przy użyciu usługi Azure Data Catalog, źródła danych narzędzie do rejestracji i może dodawać adnotacje i odnaleźć zarejestrowanego źródła danych SAP HANA przy użyciu portalu usługi Azure Data Catalog.
* Zdolność do przypięcia i Odepnij zasobów danych w portalu usługi Azure Data Catalog. Istnieje możliwość przypinanie zasobów danych, aby ułatwić ich ponowne odnajdywanie i ponowne użycie.
* Strona główna zaprojektowaną na nowo witrynę w portalu usługi Azure Data Catalog. Nowa strona główna zawiera wgląd w bieżące działanie użytkowników — w tym zasobów opublikowanych niedawno, przypięte zasoby i zapisanych wyszukiwań — i wgląd w działania w katalogu jako całości.
* Obsługa ustawień użytkownika trwałe w portalu usługi Azure Data Catalog. — W tym widoku siatki lub Kafelek, liczba wyników na stronę i naciśnij klawisz, lub wyłącz podświetlanie — ustawienia czynności użytkownika są zachowywane między sesjami użytkownika.
* Usługa Azure Data Catalog jest teraz dostępna w dwóch nowych regionów systemu Azure. Klienci mogą aprowizować usługi Azure Data Catalog w regionach Europa Północna i Azja południowo-wschodnia, oprócz wschodnie stany USA, zachodnie stany USA, Europa Zachodnia i Australia Wschodnia. Aby uzyskać więcej informacji, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Otwórz w programie SQL Server Data Tools" wymaga programu Visual Studio 2013 z aktualizacją Update 4 i narzędzi programu SQL Server, do zainstalowania. Aby zainstalować najnowszą wersję programu SQL Server Data Tools, odwiedź stronę [pobieranie programu SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>What's new for grudnia 2015 r.
Począwszy od grudnia 2015 następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługa danych profilów dla źródeł danych Azure SQL Data Warehouse. Podczas rejestrowania usługi Azure SQL Data Warehouse, tabele i widoki, użytkownicy mogą wybierać dołączany metryki profil danych z metadanych wyodrębnionych ze źródła danych.
* Obsługa rejestracji i odnajdywania obiektów MySQL i baz danych. Użytkownicy mogą rejestrować źródła danych MySQL przy użyciu usługi Azure Data Catalog, źródła danych narzędzie do rejestracji i może dodawać adnotacje i odnajdywanie zarejestrowanych źródeł danych MySQL przy użyciu portalu usługi Azure Data Catalog.
* Obsługa uwierzytelniania SPNEGO i Windows dla źródeł danych programu Teradata. Podczas rejestrowania programu Teradata tabele i widoki, użytkownicy mogą wybrać połączenie Teradata przy użyciu uwierzytelniania SPNEGO i Windows oraz LDAP oraz TD2.
* Pomoc techniczna dla źródeł danych usługi Azure Data Lake Store. Użytkownicy mogą teraz rejestrowanie i Odnajdywanie źródeł danych usługi Azure Data Lake Store przy użyciu usługi Azure Data Catalog.
* Obsługa ręczne określenie ustawień serwera proxy sieci w narzędzia rejestracji źródła danych usługi Azure Data Catalog. Użytkownicy mogą wybierać "Modyfikuj ustawienia serwera proxy" Strona powitalna narzędzia i określić adres serwera proxy i port używany przez narzędzie.

## <a name="whats-new-for-november-2015"></a>What's new for listopada 2015 r.
Począwszy od listopada 2015 roku następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Możliwość wyświetlania i skopiuj parametry połączenia z poziomu portalu usługi Azure Data Catalog na potrzeby programu SQL Server (w tym usługi Azure SQL Database) i źródła danych programu Oracle. Użytkownicy mogą kliknąć link "Wyświetl parametry połączenia" informacje o połączeniu dla programu SQL Server lub Oracle tabeli, widoku lub bazy danych, aby wyświetlić parametry połączenia używane do połączenia ze źródłem danych. Parametry połączenia ADO.NET, ODBC i OLEDB oraz JDBC znajdują się w przypadku źródeł danych programu SQL Server. ODBC i OLEDB parametry połączenia są dostarczane dla źródła danych programu Oracle.
* Obsługa danych w tym profile podczas rejestrowania programu Teradata tabele i widoki.
* Obsługa "Otwórz w programie Power BI Desktop" dla programu SQL Server (w tym usługi Azure SQL DB i Azure SQL Data Warehouse), SQL Server Analysis Services, usługi Azure Storage i systemie plików HDFS źródła.  
* Obsługa uwierzytelniania LDAP w przypadku źródeł danych programu Teradata. Podczas rejestrowania programu Teradata tabele i widoki, użytkownicy mogą wybierać nawiązać Teradata przy użyciu protokołu LDAP i TD2 uwierzytelniania.
* Obsługa "Otwórz w programie Excel" dla źródła danych programu Teradata.
* Obsługa najnowsze terminy wyszukiwania w portalu usługi Azure Data Catalog. Podczas wyszukiwania w portalu, użytkownicy mogą wybrać z ostatnio używanych wyszukiwane terminy aby przyspieszyć proces odnajdywania.
* Obsługa wersji zapoznawczej dla źródeł danych programu Teradata. Podczas rejestrowania programu Teradata tabele i widoki, użytkownicy mogą wybierać dołączany migawki rekordy z metadanych wyodrębnionych ze źródła danych.
* Obsługa "Otwórz w programie Excel" dla źródła danych Azure SQL Data Warehouse.
* Obsługa do definiowania i edytowanie schematów na poziomie kolumny na potrzeby ręcznie zarejestrowanych zasobów danych. Po ręcznego tworzenia zasobu danych, korzystając z portalu usługi Azure Data Catalog, użytkownicy mogą dodawać definicje kolumn we właściwościach zasobu danych.
* Obsługa zapytania "jest" podczas wyszukiwania usługi Azure Data Catalog, aby włączyć odnajdywanie zarejestrowanych zasobów danych, które posiadają określonych metadanych. Składnia zapytania w usłudze Azure Data Catalog zawiera teraz:

| Składnia zapytań | Przeznaczenie |
| --- | --- |
| `has:previews` |Umożliwia znalezienie zasobów danych, zawierające (wersja zapoznawcza) |
| `has:documentation` |Umożliwia znalezienie zasobów danych, dla których podano dokumentacji |
| `has:tableDataProfiles` |Umożliwia znalezienie zasobów danych przy użyciu informacji o profilu na poziomie tabeli danych |
| `has:columnsDataProfiles` |Umożliwia znalezienie zasobów danych przy użyciu informacji o profilu na poziomie kolumny danych |


> [!NOTE]
> "Otwórz w programie Power BI Desktop" wymaga bieżącej wersji aplikacji Power BI Desktop do zainstalowania. Jeśli wystąpią problemy lub błędy, korzystając z tej funkcji, upewnij się, że masz najnowszą wersję programu Power BI Desktop [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>What's new for października 2015 r.
Począwszy od października 2015 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługuje szyfrowanie danych magazynowanych danych podglądy i dane profilów dla zarejestrowanych źródeł danych. Usługa Azure Data Catalog w sposób niewidoczny dla użytkownika szyfruje żadnych rekordów (wersja zapoznawcza) i danych profili źródeł danych zarejestrowanych w usłudze bez konieczności zarządzania kluczami przez administratorów wykazu.
* Pomoc techniczna dla źródła danych programu Teradata. Użytkownicy mogą teraz rejestrowanie i odnajdywanie Teradata tabele i widoki.
* Pomoc techniczna dla lokalnych źródeł danych programu Hive. Użytkownicy mogą teraz zarejestrować i Odkryj tabel programu Hive dla Apache Hive w źródłach danych lokalnej usługi Hadoop.
* Obsługa zapisanych wyszukiwań w portalu usługi Azure Data Catalog. Użytkownicy mogą zapisać terminy wyszukiwania i filtrów można łatwo Powtarzaj poprzednich wyników wyszukiwania i określić widoki przydatne zawartości katalogu. Użytkownik może również oznaczać zapisanego kryterium wyszukiwania jako ich domyślne wyszukiwanie. Gdy użytkownik kliknie ikonę wyszukiwania "szkło powiększające i" na stronie głównej portalu usługi Azure Data Catalog, lub ze strony "wprowadzenie", użytkownik pochodzi bezpośrednio do zapisanego wyszukiwania, które są oznaczone jako domyślny.
* Obsługa tekstu sformatowanego dokumentacji dla zarejestrowanych zasobów danych i kontenerów w portalu usługi Azure Data Catalog. Użytkownicy mogą teraz podać dokumentacji, zasoby danych, takie jak tabele, widoki i raporty i kontenerach, takich jak bazy danych i modeli dla scenariuszy, w których tagi i opisy nie są wystarczające.
* Obsługa ręcznie rejestrowanie typów źródeł danych znane. Użytkownikom można ręcznie wprowadzić informacje o źródle danych za pomocą portalu usługi Azure Data Catalog dla wszystkich typów źródła danych obsługiwane przez usługę Azure Data Catalog.
* Obsługa autoryzowania grup zabezpieczeń usługi Azure Active Directory. Administratorzy katalogu mogą włączać dostępowi do grup zabezpieczeń, kontami użytkowników, dzięki czemu łatwiej jest zarządzać dostępem do usługi Azure Data Catalog.
* Obsługa otwierania źródła danych programu Hive w programie Excel w portalu usługi Azure Data Catalog.

> [!NOTE]
> W przypadku bieżącej wersji tylko Teradata TD2 uwierzytelnianie jest obsługiwane. Dodatkowe uwierzytelnianie, mechanizmów są obsługiwane w przyszłych wersjach.

> [!NOTE]
> Aby użyć funkcji "Otwórz w programie Excel" dla źródła danych programu Hive, użytkowników należy zainstalować sterownika ODBC programu Hive.

## <a name="whats-new-for-september-2015"></a>What's new for września 2015 r.
Począwszy od września 2015 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługa danych w tym profile podczas rejestrowania źródła danych programu Hive.
* Obsługa programowo odnajdywania przez interfejs API wykazu, ułatwiając aplikacje w celu integracji z usługą Azure Data Catalog.
* Nowe dane "wprowadzenie" źródła środowiska odnajdywania w portalu usługi Azure Data Catalog. Gdy użytkownicy wprowadzają "wykryć" stronie w portalu usługi Azure Data Catalog, bez konieczności wprowadzania wyszukiwany termin, zobaczą z omówieniem zawartości katalogu, w tym najczęściej używane znaczniki, ekspertami, typów źródeł danych i typów obiektów.
* Obsługa rejestracji i odnajdywania obiektów magazynu danych SQL Azure i bazy danych. Aby uzyskać dodatkowe informacje na temat usługi Azure SQL Data Warehouse, zobacz [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Obsługa rejestracji i odnajdywania usług SQL Server Analysis Services, modeli i serwerów SQL Server Reporting Services jako kontenery. Podczas rejestrowania obiektów SSAS i SSRS, Azure Data Catalog tworzy wpis modelem usług SSAS i serwera usług SSRS oraz raportów i innych obiektów. Kontenery mogą być wykrywane i przy użyciu portalu Azure Data Catalog. Użytkownicy mogą również wyszukiwanie i filtrowanie zawartości modelu lub serwerze oprócz wyszukiwania i filtrowania zawartości katalogu.
* Obsługa rejestracji i odnajdywania obiektów SQL Server Analysis Services za pośrednictwem protokołu HTTP/HTTPS. Użytkownicy mogą teraz łączyć się serwerów SSAS przy użyciu adresu URL (takie jak https://servername/olap/msmdpump.dll) zamiast serwera nazwę, a następnie użyć uwierzytelnianie podstawowe i anonimowe połączenia oprócz uwierzytelniania Windows. Aby uzyskać dodatkowe informacje na temat połączeń HTTP/HTTPS do usług SSAS, zobacz [Konfigurowanie HTTP dostępu do usług Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Pomoc techniczna dla źródeł danych Hive HDInsight. Użytkownicy mogą teraz zarejestrować i Odkryj tabel programu Hive dla Apache Hive na platformie Hadoop w HDInsight źródeł danych. Aby uzyskać dodatkowe informacje na temat programu Hive na HDInsight, zobacz [Centrum dokumentacji HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Obsługa rejestracji i odnajdywania baz danych Oracle i klastrów systemu plików HDFS jako kontenery. Podczas rejestrowania programu Oracle tabele i widoki lub systemu plików HDFS, Azure Data Catalog tworzy wpis dla bazy danych, tabel i widoków. Bazy danych mogą być wykrywane i przy użyciu portalu Azure Data Catalog. Użytkownicy mogą również wyszukiwanie i filtrowanie zawartości bazy danych lub klastra, oprócz wyszukiwania i filtrowania zawartości katalogu.
* Obsługa ręcznie rejestrowanie typów źródeł danych nieznany. Użytkownikom można ręcznie wprowadzić informacje o źródle danych za pomocą portalu usługi Azure Data Catalog, dzięki czemu źródła danych nie są jawnie obsługiwane przez narzędzia rejestracji źródła danych można dodawać adnotacje i odnalezione.
* Obsługa rejestracji i odnajdywania baz danych programu SQL Server jako kontenery. Podczas rejestrowania programu SQL Server, tabele i widoki, Azure Data Catalog tworzy wpis dla bazy danych, tabel i widoków. Bazy danych mogą być wykrywane i przy użyciu portalu Azure Data Catalog. Użytkownicy mogą również wyszukiwanie i filtrowanie zawartości bazy danych oprócz wyszukiwania i filtrowania zawartości katalogu.

> [!NOTE]
> Usługi SSAS i SSRS obiektów, które zostały zarejestrowane przed wydaniem 18 września muszą być zarejestrowane w ponownie, przy użyciu narzędzia rejestracji źródła danych, zanim wpis modelu lub serwer jest dodawany do wykazu. Ponowne rejestrowanie źródła danych nie ma wpływu na wszystkie adnotacje, które zostały dodane przez użytkowników w portalu usługi Azure Data Catalog.

> [!NOTE]
> Tabele bazy danych Oracle i widoków oraz systemu plików HDFS pliki i katalogi, które zostały zarejestrowane przed wydaniem 11 września należy ponownie zarejestrować przy użyciu narzędzia rejestracji źródła danych, zanim wpis bazy danych lub klastra jest dodawany do katalogu. Ponowne rejestrowanie źródła danych nie ma wpływu na wszystkie adnotacje, które zostały dodane przez użytkowników w portalu usługi Azure Data Catalog.

> [!NOTE]
> Tabel programu SQL Server i widoków, które zostały zarejestrowane przed wydaniem 4 września należy ponownie zarejestrować przy użyciu narzędzia rejestracji źródła danych, zanim wpis w bazie danych zostanie dodane do katalogu. Ponowne rejestrowanie źródła danych nie ma wpływu na wszystkie adnotacje, które zostały dodane przez użytkowników w portalu usługi Azure Data Catalog.

## <a name="whats-new-for-august-2015"></a>What's new for sierpnia 2015 r.
Począwszy od sierpnia 2015 r. następujące funkcje zostały dodane do usługi Azure Data Catalog:

* Obsługa danych profilowania źródeł danych programu SQL Server i Oracle. Podczas rejestrowania programu SQL Server i Oracle tabele i widoki, użytkownicy mogą wybierać obejmujący informacje o profilu danych dla obiektów, jest zarejestrowany. Profil danych obejmuje statystyki na poziomie obiektu i na poziomie kolumny.
* Pomoc techniczna dla źródeł danych HDFS, Hadoop. Użytkownicy mogą teraz rejestrowanie i odnajdywanie systemu plików HDFS plików i katalogów.
* Obsługa, podając informacje o żądaniu dostępu do źródeł danych zarejestrowanych. Do dowolnego zarejestrowanego zasobu danych użytkownicy mogą teraz podać instrukcje dotyczące żądania dostępu, w tym przesyłanie pocztą e-mail łączy lub adresy URL, łatwo zintegrować ją z istniejącymi narzędziami i procesami.
* Etykietki narzędzi dla tagów i ekspertów, aby ułatwić odnajdywanie, co użytkownicy zostały podane jakie metadanych dla zarejestrowanych zasobów danych.
* Dodaliśmy menu i nowy przycisk "User" do naszych górnym pasku nawigacyjnym. To menu umożliwia użytkownik widzi konto używane do logowania się do usługi Azure Data Catalog, a aby się wylogować, jeśli pożądane. To menu zawiera również nazwę katalogu, który jest przydatna dla deweloperów korzystających z interfejsu API REST usługi Azure Data Catalog.
* Tylko wersja Standard: Podczas dodawania właścicieli do zasobów danych, usługi Azure Data Catalog obsługuje teraz zarówno konta użytkowników i grup zabezpieczeń jako właścicieli. Aby dodać grupę zabezpieczeń jako właściciela dla wybranych zasobów danych, można wprowadzić nazwę wyświetlaną grupy lub adres e-mail UPN grupy, jeśli taki istnieje.
* Pomoc techniczna dla źródeł danych usługi Azure Blob Storage. Użytkownicy mogą teraz rejestrowanie i odnajdywanie obiektów blob usługi Azure Storage i katalogów.

