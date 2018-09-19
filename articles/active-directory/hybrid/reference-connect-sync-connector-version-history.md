---
title: Historia wersji łącznika | Dokumentacja firmy Microsoft
description: Ten temat zawiera listę wszystkich wersji łączników programu Forefront Identity Manager (FIM) i Microsoft Identity Manager (MIM)
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 95f2ffb1a51184f1194f87a4a5e9a54e682edf80
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312004"
---
# <a name="connector-version-release-history"></a>Historia wersji łącznika
Łączniki dla programu Forefront Identity Manager (FIM) i Microsoft Identity Manager (MIM) są często aktualizowane.

> [!NOTE]
> Ten temat dotyczy tylko w programie FIM i MIM. Te łączniki nie są obsługiwane dla instalacji w usłudze Azure AD Connect. Wydana łączniki są wstępnie zainstalowane na AADConnect, podczas uaktualniania do określonej kompilacji.


W tym temacie wymieniono wszystkie wersje łączników, które zostały zwolnione.

Linki pokrewne:

* [Pobieranie najnowszych łączników](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Ogólny łącznik LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) dokumentacji
* [Ogólny łącznik SQL](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) dokumentacji
* [Łącznik usługi internetowy](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) dokumentacji
* [Łącznik programu PowerShell](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) dokumentacji
* [Łącznik programu Lotus Domino](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) dokumentacji


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>Rozwiązano problemy:
* Rozwiązany System.Diagnostics.EventLogInternal.InternalWriteEvent(Message: A device attached to the system is not functioning) ConnectorsLog
* W tej wersji łączników programu należy zaktualizować przekierowanie powiązania z 3.3.0.0-4.1.3.0 do 4.1.4.0 w miiserver.exe.config
* Usługi sieci Web ogólnych:
    * Nie można zapisać rozwiązania odpowiedzi prawidłowym kodem JSON w narzędziu konfiguracji
* Ogólny łącznik SQL:
    * Eksport zawsze generuje tylko zapytanie aktualizujące dla operacji usuwania. Dodano do generowania zapytanie delete
    * Zapytanie SQL, które pobiera obiekty dla operacji importu zmian, jeśli Change Tracking "Delta strategii" został naprawiony. W tej implementacji znane ograniczenia: Import zmian z trybem Change Tracking nie śledzi zmiany w wielowartościowych atrybutach
    * Dodano możliwość generowania zapytanie delete dla przypadku, gdy należy usunąć ostatnią wartość atrybutu wielowartościowego ten wiersz nie zawiera innych danych, z wyjątkiem wartość, która jest usunąć.
    * System.ArgumentException obsługi, kiedy zaimplementowane parametrów wyjściowych przez SP 
    * Nieprawidłowe zapytanie w celu wykonania operacji eksportowania do pola, które jest typu varbinary(max)
    * Problem ze zmienną Listaparametrów został zainicjowany dwukrotnie (w funkcji ExportAttributes i GetQueryForMultiValue)


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Rozwiązano problemy:

* Program Lotus Notes:
  * Niestandardowe wydających świadectwa opcję filtrowania
  * Importuj klasy ImportOperations stała definicji jakie operacje mogą być uruchamiane w trybie "Views", które w trybie "Wyszukaj".
* Ogólny łącznik LDAP:
  * Katalog OpenLDAP używa nazwy domeny zamiast entryUUI zakotwiczenia. Nowa opcja umożliwiająca GLDAP łącznika, co umożliwia modyfikowanie zakotwiczenia
* Ogólny łącznik SQL:
  * Naprawiono eksportu do pola, które jest typu varbinary(max).
  * Podczas dodawania danych binarnych ze źródła danych do obiektu CSEntry, funkcja DataTypeConversion nie powiodło się na zero bajtów. Fixed — funkcja DataTypeConversion klasy CSEntryOperationBase.




### <a name="enhancements"></a>Ulepszenia:

* Ogólny łącznik SQL:
  * Możliwość skonfigurowania trybu wykonania procedura składowana zawierająca nazwanych parametrów lub nie o nazwie zostanie dodany w oknie Konfiguracja agenta zarządzania ogólny łącznik SQL, na stronie "Parametry globalne". Na stronie "Globalne Parameters" jest pole wyboru z etykietą "Use nazwanych parametrów do wykonania procedury składowanej" który jest odpowiedzialny za tryb wykonania przechowywane procedury z nazwanych parametrów lub nie.
    * Obecnie możliwość wykonywania procedury składowanej z nazwanymi parametrami działa tylko dla baz danych programu IBM DB2 i MSSQL. To podejście nie działa dla baz danych Oracle i MySQL: 
      * Składni SQL, MySQL nie obsługuje nazwanych parametrów w procedurach składowanych.
      * Sterownik ODBC dla bazy danych Oracle nie obsługuje nazwanych parametrów nazwanych parametrów w procedurach składowanych)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Rozwiązano problemy:

* Usługi sieci Web ogólnych:
  * Rozwiązano problem uniemożliwia projektu SOAP tworzone podczas istniały dwa lub więcej punktów końcowych.
* Ogólny łącznik SQL:
  * Podczas operacji importowania GSQL został nie konwertowania czasu poprawnie, gdy zapisane w przestrzeni łącznika. Domyślny format daty i godziny dla obszaru łącznika GSQL został zmieniony z "RRRR MM-dd: ssZ" do "RRRR MM-dd: ssZ".

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Rozwiązano problemy:

* Usługi sieci Web ogólnych:
  * Narzędzie Wsconfig nie zostały przekonwertowane poprawnie tablicę Json z "przykładowe żądanie" dla metody usługi REST. Przyczyną problemów z serializacji tej tablicy Json dla żądania REST.
  * Narzędzia konfiguracji łącznika usług sieci Web nie obsługuje użycia symboli miejsca w nazwach atrybutów JSON 
    * Wzorzec podstawienia można dodać ręcznie do pliku WSConfigTool.exe.config, np. ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```
> [!NOTE]
> Wymagany jest klucz JSONSpaceNamePattern, jak eksportu zostanie wyświetlony następujący błąd: komunikat: pusta nazwa jest niedozwolona. 

* Program Lotus Notes:
  * Po opcji **umożliwiają wydających świadectwa niestandardowe jednostki organizacyjne/organizacji** jest wyłączona, a następnie łącznika nie powiedzie się podczas eksportowania (aktualizacja) po przepływ eksportu wszystkie atrybuty są eksportowane do Domino, ale w czasie eksportu Keynotfoundexception — jest zwracany do synchronizacji. 
    * Dzieje się tak, ponieważ zmiana nazwy kończy się niepowodzeniem podczas próby zmiany nazwy domeny (atrybut nazwy użytkownika), zmieniając jeden z atrybutów poniżej:  
      - LastName
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - ou
      - altcommonname

  * Gdy **umożliwiają wydających świadectwa niestandardowe jednostki organizacyjne/organizacji** opcja jest włączona, ale wymagane wydających świadectwa są nadal puste, a następnie wystąpi wyjątek KeyNotFoundException.

### <a name="enhancements"></a>Ulepszenia:

* Ogólny łącznik SQL:
  * **Scenariusz: przeprojektowany niezaimplementowane:** "*" funkcji
  * **Opis rozwiązania:** zmienić podejście do [wielokrotne odwołanie atrybuty obsługi](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).


### <a name="fixed-issues"></a>Rozwiązano problemy:

* Usługi sieci Web ogólnych:
  * Nie można zaimportować konfigurację serwera, jeśli występuje łącznika usługi sieci Web
  * Łącznik usługi sieci Web nie działa z wieloma usługami sieci Web

* Ogólny łącznik SQL:
  * Nie typów obiektów są wyświetlane dla pojedynczej wartości atrybutu odwołania
  * Import zmian na Change Tracking strategii usuwa obiekt po wartość zostanie usunięta z tabeli wielowartościowymi
  * Overflowexception — w łączniku GSQL z bazy danych DB2 na AS / 400

Program Lotus:
  * Dodano możliwość enable\disable wyszukiwanie jednostek organizacyjnych, przed otwarciem GlobalParameters strony

## <a name="114430"></a>1.1.443.0

Wydanie: 2017 marca

### <a name="enhancements"></a>Ulepszenia

* Ogólny łącznik SQL:</br>
  **Scenariusz objawy:** jest dobrze znane ograniczenia przy użyciu łącznika SQL, gdzie możemy tylko Zezwalaj na odwołanie do jednego typu obiektu i wymagają odsyłaczy z elementami członkowskimi. </br>
  **Opis rozwiązania:** w kroku przetwarzania odwołań były "*" opcja zostanie wybrana, wszystkie kombinacje typów obiektów zostaną zwrócone do aparatu synchronizacji.

>[!Important]
- Spowoduje to utworzenie liczbę symboli zastępczych
- Jest ona wymagana, aby upewnić się, że nazwa jest unikatowa wielu typów obiektów.


* Ogólny łącznik LDAP:</br>
 **Scenariusz:** po wybraniu kilku kontenerów w określonej partycji, następnie wyszukiwanie nadal będzie realizowane w całej partycji. Określonych będą filtrowane przez usługę synchronizacji, ale nie MA, co może prowadzić do spadku wydajności. </br>

 **Opis rozwiązania:** zmienione GLDAP łącznika kod, aby przekształcić ją w możliwych przejdź przez wszystkie kontenery i wyszukiwanie obiektów w każdej z nich, zamiast wyszukiwania w całej partycji.


* Programu Lotus Domino:

  **Scenariusz:** Domino poczty usunięcia obsługę usuwania osoby, podczas eksportowania. </br>
  **Rozwiązanie:** usunięcie osoby podczas eksportowania obsługę usuwania można skonfigurować wiadomości e-mail.

### <a name="fixed-issues"></a>Rozwiązano problemy:
* Usługi sieci Web ogólnych:
 * Po zmianie adresu URL usługi w domyślnych SAP wsconfig projektów za pomocą narzędzia konfiguracji usługi sieci Web, a następnie się dzieje w następujący błąd: nie można odnaleźć części ścieżki

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Ogólny łącznik LDAP:
 * Łącznik GLDAP nie widzi wszystkie atrybuty w usług AD LDS
 * Podziały kreatora po wykryciu żadnych atrybutów nazwy UPN od schematu katalogu LDAP
 * Różnicowe Importy kończy się niepowodzeniem z błędami odnajdywania nie występuje podczas pełny import, gdy atrybut "objectclass" nie jest zaznaczona.
 * Na stronie konfiguracji "Konfiguruj partycje i hierarchie" nie pokazuje żadnych obiektów, jakiego typu jest równa partycji dla nowych serwerów w ogólnej  
LDAP MA. Pokazują one tylko obiekty z partycji RootDSE.


* Ogólny łącznik SQL:
 * Naprawiano błąd dla limitu ogólny łącznik SQL importu zmian niezaimportowanych atrybutu wielowartościowego
 * Podczas eksportowania deleted\added wartości atrybutu wielowartościowego, nie są one deleted\added w źródle danych.  


* Program Lotus Notes:
 * Określone pole "Pełna nazwa" jest wyświetlany w obiekcie metaverse poprawnie jednak podczas eksportowania do notatek wartość atrybutu jest wartość Null lub jest pusta.
 * Naprawiono błąd zduplikowanego Certifier
 * Po wybraniu obiektu, bez żadnych danych na łącznik programu Lotus Domino z innymi obiektami następnie firma Microsoft otrzyma błąd wyszukiwania podczas wykonywania pełnego importu.
 * Gdy Import zmian jest była uruchomiona na łącznik programu Lotus Domino, na końcu tego uruchomienia Microsoft.IdentityManagement.MA.LotusDomino.Service.exe usługi czasami zwraca błąd aplikacji.
 * Członkostwo w grupach, ogólną działa prawidłowo i są obsługiwane, ale podczas uruchamiania eksportu, aby spróbować usunąć użytkownika z członkostwa pokazuje pomyślnym przeprowadzeniu z aktualizacją, ale faktycznie nie uzyskać usunąć użytkownika z członkostwa programu Lotus Notes.
 * Możliwość wybierz tryb eksportu, zgodnie z "Dołącz element u dołu" został dodany w konfiguracji graficznego interfejsu użytkownika programu Lotus MA dołączyć nowe elementy u dołu podczas eksportowania dla atrybutów wielowartościowych.
 * Łącznik spowoduje to dodanie wymaganych logiki można usunąć pliku z folderu poczty i identyfikator magazynu.
 * Usunięcie członkostwa nie działa dla wielu NAB element członkowski.
 * Wartości, które powinny być pomyślnie usunięte z atrybutów wielowartościowych

## <a name="111170"></a>1.1.117.0
Wydanie: 2016 marca

**Nowy łącznik**  
Początkowa wersja [ogólnego łącznika SQL](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).

**Nowe funkcje:**

* Ogólny łącznik LDAP:
  * Dodano obsługę importu zmian za pomocą Isode.
* Łącznik usług sieci Web:
  * Zaktualizowano działanie csEntryChangeResult i działanie setImportErrorCode, aby zezwolić na błędy na poziomie obiektu ma zostać zwrócony aparat synchronizacji.
  * Zaktualizowane szablony SAP6 i SAP6User, aby korzystać z nowych funkcji błąd na poziomie obiektu.
* Łącznik programu Lotus Domino:
  * Eksport należy jeden certifier na książki adresowej. Można teraz używać tego samego hasła dla wszystkich wydających świadectwa ułatwiają zarządzanie.

**Rozwiązano problemy:**

* Ogólny łącznik LDAP:
  * Dla firmy IBM Tivoli DS nie wykryto poprawnie niektórych atrybutów odwołania.
  * Dla usługi LDAP otwarty podczas importu zmian spacji na początku i końcu ciągów zostały obcięte.
  * Novell i NetIQ Eksport przeniesiona obiektu między jednostkami organizacyjnymi/kontenerów, a w tym samym czasie, należy zmienić nazwy obiektu nie powiodła się.
* Łącznik usług sieci Web:
  * Jeśli wiele punktów końcowych dla tego samego powiązania usługi sieci web, następnie łącznika nie poprawnie wykrył te punkty końcowe.
* Łącznik programu Lotus Domino:
  * Eksport atrybutu imię i nazwisko do bazy danych w wiadomości e-mail nie działa.
  * Eksport zarówno dodać i usunąć członka z grupy wyeksportowane tylko dodane elementy członkowskie.
  * Jeśli dokument o jest nieprawidłowy (isValid atrybut ustawiony na wartość false), a następnie łącznika kończy się niepowodzeniem.

## <a name="older-releases"></a>Starsze wersje
Przed marca 2016 r. łączników zostały wydane jako tematy pomocy technicznej.

**Ogólny łącznik LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, września 2015 roku
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, marzec 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, styczeń 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, wrzesień 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, 2014 marca

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, wrzesień 2014

**Program PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, wrzesień 2014

**Programu Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, września 2015 roku
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, marzec 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, sierpnia 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, lutego 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, październik 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, sierpnia 2013

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

> [!NOTE]
> Podczas aktualizowania programu Microsoft Identity Manager lub AADConnect, przy użyciu dowolnego z łączników jednostka organizacyjna ECMA2. 

Należy odświeżyć definicji łącznika po uaktualnieniu do dopasowania lub zostanie wyświetlony następujący błąd w menu start dziennika zdarzeń aplikacji, aby zgłosić ostrzeżenie 6947 identyfikator: "wersja zestawu w konfiguracji łącznika usługi AAD ("X.X.XXX. "X") jest starsza niż wersja rzeczywista ("X.X.XXX. "X") z "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".

Aby odświeżyć definicję:
* Otwórz okno właściwości dla wystąpienia łącznika
* Kliknij połączenie / Connect do karty
  * Wprowadź hasło dla konta łącznika
* Kliknij każdą z kart właściwości z kolei
  * Jeśli ten typ łącznika ma kartę partycje, przy użyciu przycisku odświeżania, kliknij przycisk Odśwież znajduje się na tej karcie
* Po były używane wszystkie karty właściwości, kliknij przycisk OK, aby zapisać zmiany.


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
