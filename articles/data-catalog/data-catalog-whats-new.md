---
title: What's New in Azure Data Catalog | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie nowych możliwości dodane do wykazu danych Azure.
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 780ab56df19e8d6761fcf88fa7fbed3e8002fbe8
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025826"
---
# <a name="whats-new-in-azure-data-catalog"></a>What's new in Azure Data Catalog
Aktualizacje **Azure Data Catalog** są wydawane regularnie. Nie każdy wydanie obejmuje nowe funkcje dla użytkownika, ponieważ niektóre wersje są koncentruje się na możliwości usługi zaplecza. Ta strona przedstawia nowe możliwości dla użytkownika dodane do usługi Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>Nowości dotyczące 2017 listopada 
Począwszy od listopada 2017 następujące funkcje zostały dodane do wykazu danych Azure:

* Obsługa łączenie bezpośrednio do firmy terminów w portalu wykazu danych. Użytkownicy, można skopiować łącza z słownik biznesowy i osadzić je w folderze dokumenty, wiadomości e-mail, raporty lub innych lokalizacji, aby połączyć się bezpośrednio do słownika definicja terminu.
* Obsługa jednostki usługi Azure Active Directory. Administratorzy katalogu danych może autoryzować aplikacji klienckich za pomocą nazwy główne usług, aby uzyskać dostęp do katalogu, a uprawnienia można przyznać tych aplikacji określonych tak samo, jak przyznają uprawnienia dla użytkowników i grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).
* Obsługa uwierzytelniania usługi Azure Active Directory podczas nawiązywania połączenia ze źródłami danych Azure SQL Database i Azure SQL Data Warehouse, za pomocą narzędzia rejestracji źródła danych wykazu danych. Aby uzyskać więcej informacji, zobacz [Użyj Azure uwierzytelnianie usługi Active Directory do uwierzytelniania przy użyciu bazy danych SQL lub SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Nowości dotyczące 2017 września 
Począwszy od września 2017 r Azure Data Catalog dodano następujące funkcje:

* Obsługa wyodrębniania przyłączyć metadanych relacji ze źródeł danych bazy danych DB2 podczas rejestrowania powiązane tabele, za pomocą narzędzia rejestracji źródła danych.
* Obsługa rejestrowanie źródeł danych 3.4 wersji bazy danych MongoDB przy użyciu narzędzia rejestracji źródła danych.
* Obsługa usunięcie wszystkich metadanych dla obiektów znajdujących się w jednej operacji podczas usuwania z bazy danych lub innych kontenera z wykazu danych.
* Obsługa wyświetlania maksymalnie 1000 tagi, terminów biznesowych lub innych wyszukiwania aspektów, podczas korygowania wyszukiwania w portalu wykazu danych.


## <a name="whats-new-for-august-2017"></a>Co to jest nowe w sierpniu 2017 
Począwszy od sierpnia 2017 r Azure Data Catalog dodano następujące funkcje:

*   Nowe próbki dewelopera jest dostępny do tworzenia i zarządzania metadanych relacji przy użyciu interfejsu API REST wykazu danych. *Zaimportować informacje dotyczące relacji do wykazu danych* przykład jest dostępny na [strony przykłady kodu usługi Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Obsługa wyodrębniania przyłączyć metadanych relacji ze źródeł danych programu Teradata podczas rejestrowania powiązane tabele, za pomocą narzędzia rejestracji źródła danych.
* Obsługuje dla funkcji zwracającej tabelę serwera SQL (TVF) obiektów podczas rejestrowania źródła danych programu SQL Server przy użyciu narzędzia rejestracji źródła danych.
* Wiele aktualizacji i ulepszenia, aby zwiększyć wydajność i użyteczność portalu wykazu danych.

## <a name="whats-new-for-july-2017"></a>Nowości dotyczące 2017 lipca 
Począwszy od 2017 lipca zostały dodane do wykazu danych Azure następujące możliwości:
*   Obsługa bardziej szczegółową kontrolę nad tym operacji na metadanych dozwolonym:
    - Administratorzy katalogu można ograniczyć użytkownikom współtworzyć tagów i pokrewne metadane do katalogu, włączanie dostępu tylko do odczytu do katalogu.
    - Administratorzy katalogu mogą ograniczyć użytkownikom rejestrować nowych źródeł danych w katalogu.
    - Administratorzy katalogu mogą ograniczyć użytkownikom przejąć na własność metadanych zasobów danych w katalogu.
    - Może być uprawnienia do grup zabezpieczeń usługi Azure Active Directory i użytkowników w celu ułatwienia zarządzania uprawnieniami.
* Obsługa relacje między zarejestrowanych zasobów danych i odnajdowania zasobów powiązanych danych w portalu wykazu danych, w tym:
    - Wyodrębnianie metadanych relacji z programu SQL Server (w tym usługi Azure SQL Database), Oracle i MySQL źródła danych, korzystając z narzędzia rejestracji źródła danych wykazu danych.
    - Odnajdywanie zasobów powiązanych danych w przypadku wyświetlania metadanych zasobów w portalu wykazu danych.
    - Operacje, aby zdefiniować, odnajdywania i zarządzania relacjami między zasobów danych za pomocą interfejsu API REST wykazu danych.

Aby uzyskać dodatkowe informacje na temat zarządzania uprawnień w wykazie danych, zobacz [jak bezpieczny dostęp do katalogu danych i zasobów danych](data-catalog-how-to-secure-catalog.md).
Aby uzyskać dodatkowe informacje o relacje w wykazie danych, zobacz [sposobu wyświetlania zasobów powiązanych danych w usłudze Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Nowości dotyczące 2017 czerwca 
Począwszy od czerwca 2017 r Azure Data Catalog dodano następujące możliwości:
*   Obsługa źródeł danych programu Sybase, Apache Cassandra i bazy danych MongoDB. Użytkownicy mogą teraz zarejestrować i odnajdowania Cassandra i bazy danych MongoDB i tabele i baz danych programu Sybase, tabel i widoków.

> [!NOTE]
> Podczas rejestrowania bazy danych MongoDB i Cassandra tabel zawierających kolumny o złożonych typach danych przykład rekordów i tablic, te kolumny nie będą uwzględniane w metadanych dodane do wykazu danych.

## <a name="whats-new-for-may-2017"></a>Nowości dotyczące 2017 maja 
Począwszy od 2017 może zostały dodane do wykazu danych Azure następujące możliwości:
*   Nowe próbki dewelopera jest dostępna dla zbiorczym importowaniem terminów biznesowych. Przykład importowania zbiorczego słownik jest dostępny na [strona Przykłady dewelopera usługi Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Obsługa, edytując informacje dotyczące połączenia ODBC w portalu wykazu danych Azure. Właścicieli zasobów danych i administratorów usługi Data Catalog edytować informacje o połączeniu dla zarejestrowanego źródła danych bez konieczności ponownego zarejestrowania źródeł danych.
*   Obsługa aktywne adresy URL w definicji słownik biznesowych wraz z opisami. Gdy adresy URL znajdują się w wartości właściwości Opis i definicji terminów biznesowych, adresy URL zostaną wyświetlone jako hiperłącze w portalu wykazu danych.
*   Obsługa wyświetlania ekspertów nazwy oprócz adresy e-mail ekspertów. Podczas przeglądania ekspertów we właściwościach zasobu danych w portalu wykazu danych, eksperta pełną nazwę z usługi Azure Active Directory będzie wyświetlany w etykietce narzędzia.

## <a name="whats-new-for-april-2017"></a>Nowości dotyczące kwietnia 2017 
Z kwietnia 2017 r Azure Data Catalog dodano następujące możliwości:
*   Obsługa ODBC — źródła danych. Użytkownicy mogą teraz zarejestrować i odnajdywanie baz danych ODBC, tabel i widoków przy użyciu narzędzia rejestracji źródła danych wykazu danych.

## <a name="whats-new-for-march-2017"></a>Nowości dotyczące 2017 marca 
Począwszy od 2017 marca zostały dodane do wykazu danych Azure następujące możliwości:
*   Obsługa przy użyciu grup zabezpieczeń usługi AAD dla administratorów usługi Data Catalog.
*   Wykaz danych Azure jest obecnie dostępna w nowych region platformy Azure. Klientów można udostępnić Azure Data Catalog zachodnie centralnej nam obszaru, oprócz wschodnie stany USA, zachodnie stany USA, Europa Zachodnia i Australia Wschodnia, Europa Północna, Europa i Azja południowo-wschodnia. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Nowości dotyczące lutego 2017 
Począwszy od lutego 2017 r Azure Data Catalog dodano następujące funkcje:
*   Obsługa zaawansowanych ustawień w narzędzia rejestracji źródła danych wykazu danych. Użytkownicy mogą określić wartości limitu czasu polecenia podczas rejestrowania źródła danych.
*   Obsługa FTP i PostgreSQL źródeł danych. Użytkownicy mogą teraz zarejestrować i odnajdywanie FTP pliki i katalogi i bazy danych PostgreSQL, tabele i widoki.

## <a name="whats-new-for-january-2017"></a>Nowości dotyczące stycznia 2017 r 
Począwszy od stycznia 2017 r Azure Data Catalog dodano następujące funkcje:
*   Wykaz danych Azure jest obecnie [GWIAZDKĘ CSA](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) zgodne.
*   Integracja z [Get i Przekształć w programie Excel 2016 i dodatku Power Query dla programu Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Użytkownicy programu Excel można udostępnić zapytań i odnajdywanie zapytań przy użyciu usługi Azure Data Catalog z wewnątrz programu Excel. Ta funkcja jest dostępna dla użytkowników z licencjami usługi Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Nowości dotyczące grudnia 2016
Począwszy od grudnia 2016 r. zostały dodane do usługi Azure Data Catalog następujące możliwości:
*   Wykaz danych Azure jest obecnie [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) i [Klauzulami modelu UE](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) zgodne.
*   Obsługa do edycji informacji dotyczących połączenia źródła danych. Właścicieli zasobów danych i administratorów usługi Data Catalog edytować informacje o połączeniu dla zarejestrowanych źródeł danych bez konieczności ponownego zarejestrowania źródeł danych.
*   Obsługa źródeł danych witryny Salesforce.com. Użytkownicy mogą teraz zarejestrować i odnajdywanie obiektów Salesforce.


## <a name="whats-new-for-november-2016"></a>Nowości dotyczące listopada 2016
Od listopada 2016 r. zostały dodane do wykazu danych Azure następujące możliwości:
*   Wykaz danych Azure jest obecnie [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) i [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) zgodne.
*   Obsługa ręcznej rejestracji źródła danych ODBC, za pomocą portalu wykazu danych i interfejsu API REST.

## <a name="whats-new-for-september-2016"></a>Nowości dotyczące wrześniu 2016 r.
Począwszy od września 2016 r. zostały dodane do wykazu danych Azure następujące możliwości:

* Obsługa IBM DB2 źródeł danych. Użytkownicy mogą teraz zarejestrować i odnajdywanie bazy danych DB2, tabel i widoków.
* Obsługa źródeł danych bazy danych Azure rozwiązania Cosmos. Użytkownicy mogą teraz zarejestrować i odnajdywanie DB rozwiązania Cosmos baz danych i kolekcji.
* Obsługa dostosowywania nazwy wykazu w portalu wykazu danych. Administratorzy katalogu mogą teraz udostępnić tekst, który jest wyświetlany w tytule portalu, takie jak nazwa organizacji.

## <a name="whats-new-for-august-2016"></a>Co to jest nowe w sierpniu 2016
Począwszy od sierpnia 2016 następujące funkcje zostały dodane do wykazu danych Azure:

* Rozszerzenia dla rejestracji źródła danych programu SQL Server Master Data Services (MDS). Użytkownicy teraz mogą obejmować profile podglądy i dane podczas rejestrowania jednostki MDS przy użyciu narzędzia rejestracji źródła danych wykazu danych.
* Obsługa zdefiniowane przez administratora organizacji zapisane wyszukiwania. Podczas zapisywania wyszukiwania w portalu wykazu danych, Data Catalog Administratorzy mogą teraz wybrać zapisywanie wyszukiwań do użytku osobistego i dla wszystkich użytkowników wykazu. Organizacyjnej zapisane wyszukiwania są udostępniane wszystkim użytkownikom w katalogu i zapewnia standardowych punktów początkowych dla odnajdywanie źródła danych.
* Aktualizacje właściwości widoku w portalu wykazu danych. Wszystkie właściwości zasobów danych są teraz wyświetlane i zarządzane w okienku jednej, o zmiennych rozmiarach do zapewnienia bardziej spójnego i wykrywalny środowisko.

## <a name="whats-new-for-july-2016"></a>Nowości dotyczące lipca 2016 r.
Wersji lipca 2016 r Azure Data Catalog dodano następujące możliwości:

* Obsługa źródeł danych programu SQL Server Master Data Services (MDS). Użytkownicy mogą teraz zarejestrować i odnajdowania usług MDS modeli i jednostek.
* Obsługa procedur składowanych serwera SQL. Użytkownicy mogą teraz zarejestrować i odnajdywanie obiektów procedury składowanej w źródłach danych programu SQL Server.
* Obsługa dodatkowych języków w portalu usługi Azure Data Catalog i narzędzia rejestracji źródła danych, łącznie z 18 obsługiwanych języków. Środowisko użytkownika usługi Azure Data Catalog jest zlokalizowana na podstawie preferencji język ustawiony w systemie Windows lub w przeglądarce sieci web.
* Aktualizacje i uściślenia Data Catalog strony głównej portalu, tym lepsza wydajność i prostsze użytkowników.

## <a name="whats-new-for-june-2016"></a>Nowości dotyczące czerwca 2016 r.
Począwszy od czerwca 2016 r. zostały dodane do wykazu danych Azure następujące możliwości:

* Obsługa zmiana rozmiaru kolumn w widoku listy podczas odnajdywania zasobów danych w portalu wykazu danych. Teraz użytkownicy mogą zmieniać rozmiar poszczególnych kolumn łatwo odczytać metadanych długich zasobów, takich jak znaczniki wraz z opisami.
* Dodatek Power Query dla programu Excel dodano do menu "Otwórz w" w portalu wykazu danych. Użytkownicy mogą teraz otwierać obsługiwanych źródeł danych w programie Excel 2016 lub programu Excel 2010 i 2013 programu Excel z [dodatku Power Query dla programu Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) zainstalować dodatek.
* Obsługa źródeł danych magazynu tabel platformy Azure. Użytkownicy mogą teraz zarejestrować i odnajdywanie obiektów tabeli w źródłach danych usługi Azure Storage.

## <a name="whats-new-for-may-2016"></a>Nowości dotyczące maj 2016
Począwszy od maj 2016 zostały dodane do wykazu danych Azure następujące możliwości:

* Słownik biznesowy, która umożliwia administratorom wykazu Definiowanie warunki biznesowe i hierarchii do utworzenia wspólnego słownika biznesowych. Użytkownicy, można oznakować zarejestrowanych zasobów danych z terminów aby ułatwić odnajdywanie i zrozumienie zawartości katalogu. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Jak skonfigurować słownik biznesowy na potrzeby znakowania zarządzanego).  
* Ulepszenia słownik biznesowy wykazu danych, który umożliwia użytkownikom zaktualizować wiele terminów w ramach jednej operacji. Użytkownicy mogą wybrać wiele warunków można edytować następujących pól:
  * Termin nadrzędny: Użytkownik może wybrać nowy termin nadrzędny, a wszystkie wybrane warunki zostaną zaktualizowane, aby być podrzędne względem termin wybranej nadrzędnej. Jeśli wybrane wszystkie warunki mają ten sam element nadrzędny, a następnie elementem nadrzędnym jest wyświetlany w polu tekstowym, w przeciwnym razie termin nadrzędnego pola ma ustawioną wartość pusta.   
  * Znaczniki i uczestników: użytkownicy, można dodawać i usuwać tagów i uczestników dla wielu terminów, używając w tym samym środowisku jako znakowanie wielu zasobów danych.

> [!NOTE]
> Słownik biznesowy jest dostępna tylko w Standard Edition usługi Azure Data Catalog. Bezpłatna wersja nie udostępnia możliwości, dla której działalność znakowanie lub słownik biznesowy.

## <a name="whats-new-for-march-2016"></a>Nowości dotyczące marca 2016
Wersji marca 2016 r następujące funkcje zostały dodane do usługi Azure danych katalogu: g:

* Skonsolidowane punkt końcowy interfejsu API REST programowy dostęp do funkcji wyszukiwania i możliwości zarządzania zasobów katalogu usługi Azure Data Catalog. Ten punkt końcowy wyszukiwania interfejsu API interfejsu API punktu końcowego i katalog został przestarzałe i zaprzestać 21 marca 2016 r. Nie wprowadzono żadnych zmian do semantykę interfejsu API. Tylko punkt końcowy zmienić identyfikatora URI. Aby uzyskać dodatkowe informacje, zobacz [dokumentacja interfejsu API REST usługi Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267595.aspx). Aby uzyskać przykłady interfejsu API, zobacz [Przykłady dewelopera usługi Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Nowości w lutym 2016 r.
Począwszy od lutego 2016 r. zostały dodane do wykazu danych Azure następujące możliwości:

* Wybór źródła danych nowo zmienioną środowisko w narzędzia rejestracji źródła danych usługi Azure Data Catalog. Narzędzia rejestracji źródła danych został zaktualizowany w celu ułatwienia można znaleźć i wybrać ze źródeł danych obsługiwane przez usługi Azure Data Catalog.
* Obsługa 10 dodatkowych języków w portalu usługi Azure Data Catalog i narzędzia rejestracji źródła danych. Niż język angielski środowisko usługi Azure Data Catalog jest teraz dostępna w niemiecki, hiszpański, francuski, włoski, japoński, koreański, portugalski — Brazylia, rosyjski, chiński uproszczony i języka chińskiego tradycyjnego. Środowisko użytkownika usługi Azure Data Catalog jest zlokalizowana na podstawie preferencji język ustawiony w systemie Windows lub w przeglądarce sieci web.
* Obsługa — replikacja geograficzna usługi Azure Data Catalog danych biznesowych ciągłości i odzyskiwanie po awarii. Wszystkie zawartości wykazu danych Azure, w tym adnotacji metadanych i crowdsourced danych źródłowych, teraz są replikowane między dwóch regionach platformy Azure, bez ponoszenia dodatkowych kosztów dla klientów. Regiony platformy Azure są wstępnie skojarzone, co najmniej 500 mil od siebie i wykonaj mapowanie zgodnie z opisem w [firm ciągłości i odzyskiwanie po awarii (BCDR): Azure łączyć regionów](../best-practices-availability-paired-regions.md).
* Obsługa zmiany subskrypcji platformy Azure, używany przez wykaz danych Azure. Administratorzy usługi Azure Data Catalog umożliwia strony ustawień w portalu Azure Data Catalog wybierz inną subskrypcję platformy Azure na potrzeby rozliczeń.

## <a name="whats-new-for-january-2016"></a>Nowości dotyczące styczeń 2016
Począwszy od stycznia 2016 r. zostały dodane do wykazu danych Azure następujące możliwości:

* Obsługa ręcznie rejestrowanie dodatkowych źródeł danych. Można teraz używać w portalu Azure Data Catalog "Utwórz ręcznie wpis" lub użyj interfejsu API REST usługi Azure Data Catalog, aby zarejestrować następujące źródła danych:
  * OData - funkcja, zestaw jednostek oraz kontenera jednostek
  * HTTP - pliku, punkt końcowy, raportów i lokacji
  * System plików — plik
  * SharePoint — listy
  * FTP - plików i katalogów
  * Witryny SalesForce.com — obiekt
  * Bazy danych DB2 — tabeli, widoku i bazy danych
  * PostgreSQL — tabeli, widoku i bazy danych
* Obsługa "Otwórz w SQL Server Data Tools" dla źródła danych serwera SQL (łącznie z bazy danych SQL Azure i usługi Azure SQL Data Warehouse).  
* Możliwość rejestrowania i odnajdywanie, widoki SAP HANA i pakietów. Można zarejestrować za pomocą usługi Azure Data Catalog, źródła danych narzędzia rejestracji i może dodawać adnotacje i odnajdywać zarejestrowanego źródła danych SAP HANA przy użyciu portalu Azure Data Catalog źródeł danych SAP HANA.
* Możliwość Przypnij i odpiąć zasobów danych w portalu wykazu danych Azure. Można przypiąć zasobów danych, aby ułatwić ich ponowne odnajdywanie i ponowne użycie.
* Nowo przeprojektowana strona główna w portalu wykazu danych Azure. Nowej strony głównej zawiera wgląd w bieżącego działania użytkowników — w tym opublikowanych niedawno zasoby, przypięty zasobów i zapisanych wyszukiwań — wgląd w działania w katalogu jako całość.
* Obsługa trwałych ustawień w portalu wykazu danych Azure. Ustawienia czynności użytkownika — w tym widoku siatki lub kafelka, liczba wyników na stronie i naciśnij klawisz lub wyłącz podświetlanie - są zachowywane między sesjami użytkownika.
* Wykaz danych Azure jest obecnie dostępna w dwóch nowych regionów platformy Azure. Klientów można udostępnić Azure Data Catalog w regionach Północna Europie i Azji Południowo-Wschodnia, oprócz wschodnie stany USA, zachodnie stany USA, Europa Zachodnia i Australia Wschodnia. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Otwórz w SQL Server Data Tools" wymaga programu Visual Studio 2013 z aktualizacją Update 4 oraz narzędzia programu SQL Server, do zainstalowania. Aby zainstalować najnowszą wersję programu SQL Server Data Tools, odwiedź stronę [Pobierz programu SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Nowości dotyczące grudnia 2015 r.
Począwszy od grudnia 2015 zostały dodane do wykazu danych Azure następujące możliwości:

* Obsługa danych profilów dla źródeł danych magazynu danych SQL Azure. Podczas rejestrowania usługi Azure SQL Data Warehouse tabele i widoki, użytkowników można uwzględnić metryki profilu danych z metadanymi wyodrębnione ze źródła danych.
* Możliwość rejestrowania i odnajdywanie obiektów MySQL i baz danych. Użytkownicy mogą rejestrować się za pomocą usługi Azure Data Catalog, źródła danych narzędzia rejestracji i może dodawać adnotacje i odnajdywać zarejestrowanego źródła danych MySQL, przy użyciu portalu Azure Data Catalog źródeł danych MySQL.
* Obsługa SPNEGO i uwierzytelnianie systemu Windows dla źródeł danych programu Teradata. Podczas rejestrowania programu Teradata tabele i widoki, użytkownicy mogą wybrać połączenie Teradata przy użyciu uwierzytelniania SPNEGO i systemu Windows, a LDAP i TD2.
* Obsługa usługi Azure Data Lake Store źródeł danych. Użytkownicy mogą teraz zarejestrować i odnajdywania źródeł danych usługi Azure Data Lake Store za pomocą usługi Azure Data Catalog.
* Obsługa ręcznie określenie ustawień serwera proxy sieci w narzędzia rejestracji źródła danych usługi Azure Data Catalog. Użytkownicy mogą wybierać "Modyfikuj ustawienia serwera proxy" Strona powitalna narzędzia i można określić adres serwera proxy i port używany przez narzędzie.

## <a name="whats-new-for-november-2015"></a>Nowości dotyczące listopada 2015
Począwszy od listopada 2015 zostały dodane do wykazu danych Azure następujące możliwości:

* Możliwość wyświetlania i skopiuj parametry połączenia z portalu usługi Azure Data Catalog dla programu SQL Server (w tym usługi Azure SQL Database) i źródłami danych programu Oracle. Użytkownicy mogą kliknąć łącze "Wyświetl parametry połączenia" w informacje o połączeniu dla programu SQL Server lub Oracle tabeli, widoku lub bazy danych, aby wyświetlić parametry połączenia używane do łączenia się ze źródłem danych. Parametry połączenia ADO.NET, ODBC i OLEDB oraz JDBC są dostępne dla źródła danych serwera SQL. ODBC i OLEDB parametry połączenia są dostępne dla źródła danych Oracle.
* Obsługa podczas rejestrowania programu Teradata tabele i widoki, w tym danych profilów.
* Obsługę "Otwórz w Power BI Desktop" dla programu SQL Server (łącznie z bazy danych SQL Azure i usługi Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage i system plików HDFS źródła.  
* Obsługa uwierzytelniania LDAP dla źródeł danych programu Teradata. Podczas rejestrowania programu Teradata tabele i widoki, użytkownicy mogą wybrać połączenie Teradata przy użyciu protokołu LDAP, a TD2 uwierzytelniania.
* Obsługa "Otwórz w programie Excel" dla źródła danych programu Teradata.
* Obsługa ostatnie terminy wyszukiwania w portalu wykazu danych Azure. Podczas wyszukiwania w portalu, użytkownicy mogą wybrać z warunkami wyszukiwania ostatnio używane w celu przyspieszenia obsługi odnajdowania.
* Obsługa podglądu dla źródeł danych programu Teradata. Podczas rejestrowania programu Teradata tabele i widoki, użytkowników można uwzględnić migawki rekordy z metadanymi wyodrębnione ze źródła danych.
* Obsługa "Otwórz w programie Excel" dla źródła danych magazynu danych SQL Azure.
* Obsługa do definiowania i edytowania schematów na poziomie kolumny dla ręcznie zarejestrowanych zasobów danych. Po utworzeniu ręcznie zasobu danych za pomocą portalu wykazu danych Azure, użytkownicy mogą dodawać definicje kolumn we właściwościach zasobów danych.
* Obsługa zapytań "ma" podczas wyszukiwania Azure Data Catalog, aby umożliwić odnajdywania zarejestrowanych zasobów danych, które posiadają określonych metadanych. Składnia zapytania w usłudze Azure Data Catalog zawiera teraz:

| Składnia zapytań | Przeznaczenie |
| --- | --- |
| `has:previews` |Umożliwia znalezienie zasobów danych, które obejmują Podgląd |
| `has:documentation` |Umożliwia znalezienie zasobów danych, dla których dostarczono dokumentacji |
| `has:tableDataProfiles` |Umożliwia znalezienie zasobów danych z danych na poziomie tabeli informacje o profilu |
| `has:columnsDataProfiles` |Umożliwia znalezienie zasobów danych z danych na poziomie kolumny informacje o profilu |


> [!NOTE]
> "Otwórz w programie Power BI Desktop" wymaga bieżącej wersji aplikacji Power BI Desktop do zainstalowania. Jeśli wystąpią problemy lub błędy podczas korzystania z tej funkcji, upewnij się, że masz najnowszą wersję programu Power BI Desktop z [witryny PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Nowości dotyczące października 2015
Począwszy od października 2015 zostały dodane do wykazu danych Azure następujące możliwości:

* Obsługa szyfrowania przechowywanych danych podglądy i danych profilów dla zarejestrowanych źródeł danych. Wykaz danych Azure niewidocznie szyfruje wszystkie rekordy w wersji zapoznawczej i danych profili źródła danych zarejestrowane w usłudze, bez konieczności zarządzania kluczami przez administratorów w katalogu.
* Obsługa źródeł danych programu Teradata. Użytkownicy mogą teraz zarejestrować i odnajdywanie Teradata tabel i widoków.
* Obsługa lokalnych źródeł danych Hive. Użytkownicy mogą teraz zarejestrować i wykrywa tabele programu Hive dla Apache Hive w Hadoop lokalnych źródeł danych.
* Obsługa zapisane wyszukiwania w portalu wykazu danych Azure. Użytkownicy można zapisywać terminy wyszukiwania i filtrowania wybrane elementy, aby łatwo Powtórz poprzednie wyszukiwania i określić widoki przydatne zawartości katalogu. Użytkownika można również oznaczyć zapisane wyszukiwanie jako domyślny wyszukiwania. Gdy użytkownik kliknie ikonę "Lupa" wyszukiwania na stronie głównej portalu wykazu danych Azure lub ze strony "wprowadzenie", użytkownik jest pobierana bezpośrednio do zapisanego wyszukiwania, które są oznaczone jako domyślne.
* Obsługa tekstu sformatowanego dokumentacji zarejestrowanych zasobów danych i kontenerów w portalu wykazu danych Azure. Użytkownicy mogą teraz podać dokumentacji dla zasobów danych, takich jak tabele, widoki i raporty i kontenerów, takie jak bazy danych i modele dla scenariuszy, w którym tagów i opisy nie są wystarczające.
* Obsługa ręcznie rejestrowanie znanych danych typów źródła. Użytkownicy, można ręcznie wprowadzić informacje o źródle danych przy użyciu portalu Azure Data Catalog dla wszystkich typów źródła danych obsługiwane przez usługi Azure Data Catalog.
* Obsługa autoryzowanie grup zabezpieczeń usługi Azure Active Directory. Administratorzy katalogu mogą włączyć katalogu dostęp do grupy zabezpieczeń kont użytkowników, co ułatwia zarządzanie dostępem do usługi Azure Data Catalog.
* Obsługa otwieranie gałęzi źródeł danych w programie Excel z portalu wykazu danych Azure.

> [!NOTE]
> W bieżącej wersji tylko Teradata TD2 uwierzytelnianie jest obsługiwane. Dodatkowe uwierzytelnianie mechanizmów są obsługiwane w przyszłych wersjach.

> [!NOTE]
> Aby użyć funkcji "Otwórz w programie Excel" Hive źródeł danych, użytkownicy zainstalować sterownika ODBC dla gałęzi.

## <a name="whats-new-for-september-2015"></a>Nowości dotyczące wrzesień 2015
Począwszy od września 2015 r. zostały dodane do usługi Azure Data Catalog następujące możliwości:

* Obsługa podczas rejestrowania źródła danych gałęzi w tym danych profilów.
* Obsługa programowo odnajdywania API katalogu, ułatwiając aplikacje do integracji z usługą Azure Data Catalog.
* Nowe dane "wprowadzenie" źródła obsługi odnajdowania w portalu wykazu danych Azure. Wprowadzenie przez użytkownika na stronie portalu wykazu danych Azure "wykryć" bez wprowadzania wyszukiwanego terminu, mają być przedstawiane z omówieniem zawartości katalogu, w tym najczęściej używane znaczniki, ekspertów, typy źródeł danych i typów obiektów.
* Możliwość rejestrowania i odnajdywanie obiektów magazynu danych SQL Azure i baz danych. Aby uzyskać dodatkowe informacje na temat usługi Azure SQL Data Warehouse, zobacz [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Możliwość rejestrowania i odnajdywanie modele usług SQL Server Analysis Services i serwerów SQL Server Reporting Services jako kontenerów. Podczas rejestrowania obiektów SSAS i SSRS, usługa Azure Data Catalog tworzy wpis modelu SSAS i serwer SSRS oraz raporty i inne obiekty. Kontenery mogą być wykrywane i opatrzone adnotacjami za pomocą portalu wykazu danych Azure. Użytkownicy mogą również wyszukiwanie i filtrowanie zawartości modelu lub serwerze oprócz wyszukiwanie i filtrowanie zawartości katalogu.
* Możliwość rejestrowania i odnajdywania obiektów SQL Server Analysis Services za pośrednictwem protokołu HTTP/HTTPS. Użytkownicy mogą teraz łączyć się przy użyciu adresu URL serwerów usługi SSAS (takich jak https://servername/olap/msmdpump.dll) zamiast serwer nazwy i użyć uwierzytelniania podstawowego i oprócz uwierzytelniania systemu Windows na połączenia anonimowe. Aby uzyskać dodatkowe informacje w przypadku połączeń HTTP i HTTPS do usług SSAS, zobacz [skonfiguruj HTTP dostępu do usług Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Obsługa Hive źródeł danych w usłudze HDInsight. Użytkownicy mogą teraz zarejestrować i wykrywa tabele programu Hive dla Apache Hive w Hadoop w HDInsight źródeł danych. Aby uzyskać dodatkowe informacje na temat Hive w usłudze HDInsight, zobacz [Centrum dokumentacji usługi HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Możliwość rejestrowania i odnajdywanie bazami danych Oracle i klastrów systemu plików HDFS jako kontenerów. Podczas rejestrowania programu Oracle tabele i widoki lub system plików HDFS, Azure Data Catalog tworzy wpis dla bazy danych, tabel i widoków. Bazy danych mogą być wykrywane i opatrzone adnotacjami za pomocą portalu wykazu danych Azure. Użytkownicy mogą również wyszukiwanie i filtrowanie zawartości bazy danych lub klastra oprócz wyszukiwanie i filtrowanie zawartości katalogu.
* Obsługa ręcznie rejestrowanie typy źródeł danych nieznany. Użytkownicy, można ręcznie wprowadzić informacje o źródle danych za pomocą portalu wykazu danych Azure, dzięki czemu źródeł danych nie jest jawnie obsługiwany przez narzędzia rejestracji źródła danych można dodawać adnotacje i odnalezione.
* Możliwość rejestrowania i wykrywania baz danych programu SQL Server jako kontenerów. Podczas rejestrowania programu SQL Server tabele i widoki, usługa Azure Data Catalog tworzy wpis dla bazy danych, tabel i widoków. Bazy danych mogą być wykrywane i opatrzone adnotacjami za pomocą portalu wykazu danych Azure. Użytkownicy mogą również wyszukiwanie i filtrowanie zawartości bazy danych oprócz wyszukiwanie i filtrowanie zawartości katalogu.

> [!NOTE]
> Obiekty SSAS i SSRS, które zostały zarejestrowane przed 18 września wersji musi być zarejestrowana w ponownie, przy użyciu narzędzia rejestracji źródła danych przed wpis modelu lub serwer jest dodawany do katalogu. Ponowne rejestrowanie źródła danych nie ma wpływu na wszystkie adnotacje, które zostały dodane przez użytkowników w portalu wykazu danych Azure.

> [!NOTE]
> Oracle tabel i widoków systemu plików HDFS plików i katalogów, które zostały zarejestrowane przed w wersji 11 września, należy ponownie zarejestrować za pomocą narzędzia rejestracji źródła danych, przed dodaniem wpisu bazy danych lub w klastrze do katalogu. Ponowne rejestrowanie źródła danych nie ma wpływu na wszystkie adnotacje, które zostały dodane przez użytkowników w portalu wykazu danych Azure.

> [!NOTE]
> Tabel programu SQL Server i widoków, które zostały zarejestrowane przed w wersji 4 września, należy ponownie zarejestrować za pomocą narzędzia rejestracji źródła danych, przed dodaniem wpisu bazy danych do katalogu. Ponowne rejestrowanie źródła danych nie ma wpływu na wszystkie adnotacje, które zostały dodane przez użytkowników w portalu wykazu danych Azure.

## <a name="whats-new-for-august-2015"></a>Nowości dotyczące sierpień 2015
Począwszy od sierpnia 2015 r. zostały dodane do wykazu danych Azure następujące możliwości:

* Obsługa danych profilowania źródeł danych programu SQL Server i Oracle. Podczas rejestrowania programu SQL Server i Oracle tabele i widoki, użytkownicy mogą być obejmują informacje o danych profilu dla obiektów, jest zarejestrowany. Profil danych obejmuje statystyki na poziomie obiektu i na poziomie kolumny.
* Obsługa systemu plików HDFS Hadoop źródeł danych. Użytkownicy mogą teraz zarejestrować i odnajdywania systemu plików HDFS plików i katalogów.
* Obsługa zapewniające informacje dotyczące żądania dostępu do zarejestrowanych źródeł danych. Dla dowolnego zasobu zarejestrowanych danych użytkownicy mogą teraz podać instrukcje dotyczące żądania dostępu, w tym łącza poczty e-mail czy adresów URL, aby łatwo zintegrować ją z istniejących narzędzi i procesów.
* Etykietki narzędzi dla tagów i ekspertów, aby ułatwić odnajdywanie, co użytkownicy podano jakie metadane dla zarejestrowanych zasobów danych.
* Dodano nowy przycisk "Użytkownika" i menu do naszej górnym pasku nawigacyjnym. To menu umożliwia użytkownikowi Zobacz konto używane do logowania się do usługi Azure Data Catalog i się wylogować, jeśli konieczne. Rozwijalna również nazwę katalogu, który jest przydatna dla deweloperów korzystających z interfejsu API REST usługi Azure Data Catalog.
* Standard Edition tylko: Podczas dodawania właścicieli do zasobów danych, usługi Azure Data Catalog obsługuje teraz zarówno kont użytkowników i grup zabezpieczeń jako właścicieli. Aby dodać grupę zabezpieczeń jako właściciela dla wybranych zasobów danych, można wprowadzić nazwę wyświetlaną grupy lub adres e-mail grupy głównej nazwy użytkownika, jeśli istnieje.
* Obsługa źródeł danych magazynu obiektów Blob Azure. Użytkownicy mogą teraz zarejestrować i odnajdywanie obiektów blob magazynu Azure i katalogów.

