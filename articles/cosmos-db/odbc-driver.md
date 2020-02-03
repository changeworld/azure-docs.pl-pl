---
title: Połączyć z usługą Azure Cosmos DB za pomocą narzędzi analitycznych do analizy Biznesowej
description: Dowiedz się, jak tworzyć tabele i widoki, tak aby znormalizowane dane mogą być wyświetlane w oprogramowaniu do analizy danych i analizy Biznesowej za pomocą sterownika ODBC programu usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721085"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Połączyć z usługą Azure Cosmos DB za pomocą narzędzi analitycznych do analizy Biznesowej ze sterownikiem ODBC

Sterownik platformy Azure Cosmos DB umożliwia Ci łączenie z usługą Azure Cosmos DB przy użyciu narzędzi analizy analizy Biznesowej, takich jak SQL Server Integration Services, usłudze Power Bi i Tableau można analizować i tworzyć wizualizacje danych usługi Azure Cosmos DB w tych rozwiązaniach.

Sterownik ODBC usługi Azure Cosmos DB jest zgodny ze sterownikiem ODBC 3.8 i obsługuje składnię ANSI SQL-92. Sterownik udostępnia zaawansowane funkcje ułatwiające ponowne normalizowanie danych w usłudze Azure Cosmos DB. Przy użyciu sterownika można przedstawiać dane w usłudze Azure Cosmos DB w formie tabel i widoków. Sterownik umożliwia wykonywanie operacji SQL dotyczących tabel i widoków, w tym grupowanie według zapytań, wstawień, aktualizacji i usunięć.

> [!NOTE]
> Łączenie z usługą Azure Cosmos DB przy użyciu sterownika ODBC jest obecnie obsługiwane w tylko konta interfejsu API SQL usługi Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Dlaczego trzeba normalizacji Moje dane?
Usługa Azure Cosmos DB to baza danych ze schematów, które umożliwia szybkie opracowywanie aplikacji i możliwości w celu wykonania iteracji modeli danych nie są ograniczone do ścisłego schematu. Pojedyncza baza danych usługi Azure Cosmos może zawierać dokumenty JSON o różnych strukturach. Jest to doskonałe rozwiązanie do szybkiego opracowywania aplikacji, ale jeśli chcesz analizować i tworzyć raporty danych za pomocą analizy danych i narzędzi do analizy Biznesowej danych często wymaga spłaszczone i być zgodne z określonym schematem.

Jest to, gdzie sterownik ODBC jest oferowana w. Za pomocą sterownika ODBC, możesz teraz ponownie normalizować dane w usłudze Azure Cosmos DB do tabel i widoków, które mieszczą się analizy danych i potrzeb dotyczących raportowania w. Schematy renormalized nie mają wpływu na dane, a nie ograniczają deweloperzy mogą stosować się do nich. Przeciwnie umożliwiają one zgodne z ODBC narzędzi do uzyskania dostępu do danych. W związku z tym teraz Twoja baza danych Azure Cosmos nie będzie ulubiona dla Twojego zespołu programistycznego, ale Twoje analityki danych pozostaną zbyt.

Zacznijmy od sterownika ODBC.

## <a id="install"></a>Krok 1. Instalowanie sterownika Azure Cosmos DB ODBC

1. Pobieranie sterowników, które w danym środowisku:

    | Instalator | Obsługiwane systemy operacyjne| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit. msi](https://aka.ms/cosmos-odbc-64x64) dla systemu Windows 64 — bit| 64-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit. msi](https://aka.ms/cosmos-odbc-32x64) dla 32-bitowej w 64-bitowym systemie Windows| 64-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 i Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit. msi](https://aka.ms/cosmos-odbc-32x32) dla systemu Windows 32 — bit|32-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows XP i Windows Vista.|

    Uruchom lokalnie plik msi, co spowoduje uruchomienie **Kreatora instalacji sterownika ODBC Microsoft Azure Cosmos DB**. 

1. Ukończ pracę Kreatora instalacji, przy użyciu domyślnego dane wejściowe, aby zainstalować sterownik ODBC.

1. Otwórz na komputerze aplikację **administratora źródła danych ODBC** . Można to zrobić, wpisując **źródła danych ODBC** w polu wyszukiwania systemu Windows. 
    Zainstalowanie sterownika można potwierdzić, klikając kartę **sterowniki** i upewniając się, że na liście znajduje się **Microsoft Azure Cosmos DB sterownik ODBC** .

    ![Administrator źródła danych ODBC usługi Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Krok 2. nawiązanie połączenia z bazą danych Azure Cosmos

1. Po [zainstalowaniu Azure Cosmos DB sterownika ODBC](#install), w oknie **Administrator źródła danych ODBC** kliknij przycisk **Dodaj**. Można utworzyć użytkownika lub System DSN. W tym przykładzie jest tworzona DSN użytkownika.

1. W oknie **Tworzenie nowego źródła danych** wybierz pozycję **Microsoft Azure Cosmos DB sterownika ODBC**, a następnie kliknij przycisk **Zakończ**.

1. W oknie **instalatora Azure Cosmos DB ODBC Driver SDN** wprowadź następujące informacje: 

    ![Usługa Azure Cosmos DB ODBC Driver DSN Instalatora okna](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nazwa źródła danych**: przyjazna nazwa DSN ODBC. Ta nazwa jest unikatowa dla konta usługi Azure Cosmos DB, więc nazwij ją odpowiednio w przypadku wielu kont.
    - **Opis**: Krótki opis źródła danych.
    - **Host**: identyfikator URI konta Azure Cosmos DB. Możesz pobrać ten ze strony usługi Azure Cosmos DB klucze w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu. 
    - **Klucz dostępu**: podstawowy lub pomocniczy klucz do odczytu lub zapisu ze strony Azure Cosmos DB klucze w Azure Portal, jak pokazano na poniższym zrzucie ekranu. Zaleca się, że używasz klucza tylko do odczytu, jeśli nazwy DSN jest używana do przetwarzania danych tylko do odczytu i raportowania.
    Strona ![klucze Azure Cosmos DB](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Szyfruj klucz dostępu dla**: Wybierz najlepszy wybór na podstawie użytkowników tego komputera. 
    
1. Kliknij przycisk **Testuj** , aby upewnić się, że możesz nawiązać połączenie z kontem Azure Cosmos DB. 

1.  Kliknij pozycję **Opcje zaawansowane** i ustaw następujące wartości:
    *  **Wersja interfejsu API REST**: Wybierz [wersję interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/) dla operacji. Wartość domyślna 2015-12-16. Jeśli masz kontenery z [dużymi kluczami partycji](large-partition-keys.md) i potrzebujesz interfejsu API REST w wersji 2018-12-31:
        - Wpisz **2018-12-31** dla wersji interfejsu API REST
        - W menu **Start** wpisz ciąg "regedit", aby znaleźć i otworzyć aplikację **Edytor rejestru** .
        - W Edytorze rejestru przejdź do ścieżki: **Computer \ HKEY_LOCAL_MACHINE \software\odbc\odbc. Plik INI**
        - Utwórz nowy podklucz o takiej samej nazwie jak nazwa DSN, np. "konto contoso ODBC DSN".
        - Przejdź do podklucza "konto contoso ODBC DSN".
        - Kliknij prawym przyciskiem myszy, aby dodać nową wartość **ciągu** :
            - Nazwa wartości: **IgnoreSessionToken**
            - Dane wartości: **1**
            ![ustawienia edytora rejestru](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Spójność zapytań**: Wybierz [poziom spójności](consistency-levels.md) dla operacji. Wartość domyślna to sesji.
    - **Liczba ponownych prób**: Określ liczbę ponownych prób wykonania operacji, jeśli początkowe żądanie nie zostało ukończone z powodu ograniczenia szybkości usługi.
    - **Plik schematu**: masz tutaj kilka opcji.
        - Domyślnie pozostawienie tego wpisu jako (pustego) powoduje, że sterownik skanuje pierwszą stronę danych dla wszystkich kontenerów, aby określić schemat każdego kontenera. Jest to tzw. mapowanie kontenerów. Nie zdefiniowano pliku schematu sterownik ma skanowania dla każdej sesji sterowników i może spowodować wyższe czas uruchamiania aplikacji przy użyciu nazwy DSN. Firma Microsoft zaleca, zawsze skojarzenie pliku schematu dla nazwy DSN.
        - Jeśli masz już plik schematu (prawdopodobnie taki został utworzony za pomocą Edytora schematu), możesz kliknąć przycisk **Przeglądaj**, przejść do pliku, kliknąć przycisk **Zapisz**, a następnie kliknąć przycisk **OK**.
        - Jeśli chcesz utworzyć nowy schemat, kliknij przycisk **OK**, a następnie kliknij przycisk **Edytor schematu** w oknie głównym. Następnie przechodzenie do informacji Edytora schematu. Po utworzeniu nowego pliku schematu Pamiętaj, aby wrócić do okna **Opcje zaawansowane** , aby uwzględnić nowo utworzony plik schematu.

1. Po zakończeniu i zamknięciu okna **instalacji dsn Azure Cosmos DB ODBC** nowe DSN użytkownika zostanie dodane do karty DSN użytkownika.

    ![Nowe usługi Azure Cosmos DB nazwy DSN ODBC na karcie DSN użytkownika](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>Krok 3. Tworzenie definicji schematu przy użyciu metody mapowania kontenera

Istnieją dwa typy metod próbkowania, których można użyć: **mapowania kontenerów** lub **ograniczników tabel**. Sesja próbkowania może wykorzystywać obydwie metody próbkowania, ale każdy kontener może korzystać tylko z określonej metody próbkowania. Poniższe kroki tworzą schemat dla danych w jednym lub większej liczbie kontenerów przy użyciu metody mapowania kontenera. Ta metoda próbkowania pobiera dane na stronie kontenera w celu określenia struktury danych. Powoduje to przełączenie kontenera do tabeli po stronie ODBC. Ta metoda próbkowania jest wydajna i szybka, gdy dane w kontenerze są jednorodne. Jeśli kontener zawiera niejednorodny typ danych, zalecamy użycie [metody mapowania tabeli ograniczników](#table-mapping) , ponieważ zapewnia ona bardziej niezawodną metodę próbkowania w celu określenia struktur danych w kontenerze. 

1. Po wykonaniu kroków 1-4 w [nawiązaniu połączenia z bazą danych Azure Cosmos](#connect)kliknij pozycję **Edytor schematu** w oknie **Konfiguracja DSN sterownika ODBC Azure Cosmos DB** .

    ![Przycisk Edytor schematu w oknie Ustawienia DSN sterownika ODBC usługi Azure Cosmos DB](./media/odbc-driver/odbc-driver-schema-editor.png)
1. W oknie **Edytor schematu** kliknij pozycję **Utwórz nowy**.
    W oknie **Generuj schemat** są wyświetlane wszystkie kontenery na koncie Azure Cosmos DB. 

1. Wybierz co najmniej jeden kontener do próbkowania, a następnie kliknij pozycję **przykład**. 

1. Na karcie **Widok projektu** są reprezentowane bazy danych, schematu i tabeli. W widoku tabeli skanowania wyświetla zestaw właściwości związanych z nazwami kolumn (Nazwa SQL, nazwa źródła itp.).
    Dla każdej kolumny, możesz zmodyfikować nazwę kolumny SQL, typ SQL, długości SQL (jeśli dotyczy), skalowanie (jeśli dotyczy), dokładność (jeśli dotyczy) oraz Nullable.
    - Możesz ustawić **wartość true** dla opcji **Ukryj kolumnę** , jeśli chcesz wykluczyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Ukryj kolumnę = true nie są zwracane do wyboru i projekcji, mimo że wciąż są częścią schematu. Na przykład można ukryć wszystkie właściwości systemu wymagane usługi Azure Cosmos DB, począwszy od "_".
    - Kolumna **ID** jest jedynym polem, które nie może być ukryte, ponieważ jest używane jako klucz podstawowy w znormalizowanym schemacie. 

1. Po zakończeniu definiowania schematu kliknij pozycję **plik** | **Zapisz**, przejdź do katalogu, aby zapisać schemat, a następnie kliknij przycisk **Zapisz**.

1. Aby użyć tego schematu z DSN, Otwórz **okno instalacji Azure Cosmos DB ODBC Driver DSN** (za pomocą administratora źródła danych ODBC), kliknij przycisk **Opcje zaawansowane**, a następnie w polu **plik schematu** przejdź do zapisanego schematu. Zapisywanie pliku schematu istniejącej nazwy DSN modyfikuje połączenia DSN do zakresu do danych i struktury definiowana przez schemat.

## <a id="table-mapping"></a>Krok 4. Tworzenie definicji schematu przy użyciu metody mapowania tabeli ograniczników

Istnieją dwa typy metod próbkowania, których można użyć: **mapowania kontenerów** lub **ograniczników tabel**. Sesja próbkowania może wykorzystywać obydwie metody próbkowania, ale każdy kontener może korzystać tylko z określonej metody próbkowania. 

Poniższe kroki tworzą schemat dla danych w jednym lub większej liczbie kontenerów przy użyciu metody mapowania **ograniczników tabel** . Zalecamy używanie tej metody próbkowania, gdy kontenery zawierają heterogeniczny typ danych. Ta metoda służy do określania zakresu próbkowania do zestawu atrybutów i ich odpowiednie wartości. Na przykład jeśli dokument zawiera właściwość "Type", możesz ograniczyć próbkowanie, aby wartości tej właściwości. Wynik końcowy próbkowanie będzie zestawu tabel dla każdej wartości mają określonego typu. Na przykład wpisz = samochodu będzie utworzyć tabelę samochodu podczas typu = płaszczyzny dałby w efekcie tabeli płaszczyzny.

1. Po wykonaniu kroków 1-4 w [nawiązaniu połączenia z bazą danych Azure Cosmos](#connect)kliknij pozycję **Edytor schematu** w oknie konfiguracja DSN sterownika ODBC Azure Cosmos DB.

1. W oknie **Edytor schematu** kliknij pozycję **Utwórz nowy**.
    W oknie **Generuj schemat** są wyświetlane wszystkie kontenery na koncie Azure Cosmos DB. 

1. Wybierz kontener na karcie **Widok przykładowy** , w kolumnie **Definicja mapowania** dla kontenera, kliknij przycisk **Edytuj**. Następnie w oknie **Definicja mapowania** wybierz opcję **ograniczniki tabeli** . Następnie wykonaj poniższe czynności:

    a. W polu **atrybuty** wpisz nazwę właściwości ogranicznika. Jest to właściwość w dokumencie, który chcesz ograniczyć zakres pobierania próbek, aby na przykład miasta, a następnie naciśnij klawisz enter. 

    b. Jeśli chcesz tylko określić zakres próbkowania do określonych wartości atrybutu wprowadzonego powyżej, zaznacz atrybut w polu zaznacz, wprowadź wartość w polu **wartość** (np. Seattle) i naciśnij klawisz ENTER. Można dodać wiele wartości dla atrybutów. Po prostu upewnij się, że właściwy atrybut jest zaznaczone, gdy wprowadzasz wartości.

    Na przykład jeśli dołączysz wartość **atrybutu** miasto i chcesz ograniczyć tabelę do uwzględnienia tylko wierszy z wartością miasto New York i Dubaj, wpisz miasto w polu atrybuty, a Nowy Jork, a następnie Dubaj w polu **wartości** .

1. Kliknij przycisk **OK**. 

1. Po zakończeniu definiowania mapowania dla kontenerów, które chcesz próbkować, w oknie **Edytor schematu** kliknij **przykład**.
     Dla każdej kolumny, możesz zmodyfikować nazwę kolumny SQL, typ SQL, długości SQL (jeśli dotyczy), skalowanie (jeśli dotyczy), dokładność (jeśli dotyczy) oraz Nullable.
    - Możesz ustawić **wartość true** dla opcji **Ukryj kolumnę** , jeśli chcesz wykluczyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Ukryj kolumnę = true nie są zwracane do wyboru i projekcji, mimo że wciąż są częścią schematu. Można na przykład ukryć wszystkie właściwości wymagane przez system Azure Cosmos DB, zaczynając od `_`.
    - Kolumna **ID** jest jedynym polem, które nie może być ukryte, ponieważ jest używane jako klucz podstawowy w znormalizowanym schemacie. 

1. Po zakończeniu definiowania schematu kliknij pozycję **plik** | **Zapisz**, przejdź do katalogu, aby zapisać schemat, a następnie kliknij przycisk **Zapisz**.

1. W oknie **Konfiguracja DSN sterownika ODBC Azure Cosmos DB** kliknij przycisk **Opcje zaawansowane**. Następnie w polu **plik schematu** przejdź do zapisanego pliku schematu, a następnie kliknij przycisk **OK**. Kliknij ponownie przycisk **OK** , aby zapisać nazwę DSN. Spowoduje to zapisanie schematu, który został utworzony do nazwy DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcjonalnie) Konfigurowanie połączenia z połączonym serwerem

Można tworzyć zapytania usługi Azure Cosmos DB z programu SQL Server Management Studio (SSMS), konfigurując połączenia z połączonym serwerem.

1. Utwórz systemowe źródło danych zgodnie z opisem w [kroku 2](#connect), o nazwie na przykład `SDS Name`.

1. [Zainstaluj SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i nawiąż połączenie z serwerem. 

1. W edytorze zapytań programu SSMS Utwórz połączony obiekt serwera `DEMOCOSMOS` dla źródła danych za pomocą następujących poleceń. Zastąp `DEMOCOSMOS` nazwą połączonego serwera i `SDS Name` nazwą swojego źródła danych systemu.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Aby wyświetlić nazwę nowego serwera połączonego, Odśwież listę serwerów połączonych.

![Połączonego serwera w programie SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Twórz zapytania bazy danych połączone

Aby wysłać zapytanie połączonej bazy danych, wprowadź kwerendę programu SSMS. W tym przykładzie zapytanie wybiera się z tabeli w kontenerze o nazwie `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Wykonaj zapytanie. Wynik powinien wyglądać podobnie jak poniżej:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Połączony serwer usługi Cosmos DB nie obsługuje nazewnictwa czteroczęściową. Zostanie zwrócony błąd podobny do następującego:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcjonalnie) Tworzenie widoków
Można definiować i tworzyć widoki w ramach procesu pobierania próbek. Widoki te są równoważne widoki SQL. One są tylko do odczytu i zakresu opcji i projekcji zapytań SQL usługi Azure Cosmos DB zdefiniowane. 

Aby utworzyć widok dla danych, w oknie **Edytor schematu** w kolumnie **Definicje widoku** kliknij przycisk **Dodaj** w wierszu kontenera do przykładu. 
    ![utworzyć widok danych](./media/odbc-driver/odbc-driver-create-view.png)


Następnie w oknie **Definicje widoku** wykonaj następujące czynności:

1. Kliknij pozycję **Nowy**, wprowadź nazwę widoku, na przykład EmployeesfromSeattleView, a następnie kliknij przycisk **OK**.

1. W oknie **Edycja widoku** wprowadź zapytanie Azure Cosmos DB. Musi to być [Azure Cosmos DB zapytanie SQL](how-to-sql-query.md), na przykład `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, a następnie kliknij przycisk **OK**.

    ![Dodaj zapytanie, podczas tworzenia widoku](./media/odbc-driver/odbc-driver-create-view-2.png)


Można utworzyć wiele widoków, jak chcesz. Po zakończeniu definiowania widoków, możesz następnie próbkowanie danych. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Wyświetlanie danych w narzędziach, takich jak Power BI Desktop

Twoje nowe źródło danych umożliwia łączenie z usługi Azure Cosmos DB przy użyciu dowolnych narzędzi standardem ODBC — w tym kroku po prostu dowiesz się, jak nawiązać połączenie z programu Power BI Desktop i tworzenie wizualizacji usługi Power BI.

1. Otwórz program Power BI Desktop.

1. Kliknij pozycję **Pobierz dane**.

    ![Pobieranie danych w programie Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. W oknie **pobieranie danych** kliknij pozycję **inne** | **ODBC** | **Connect**.

    ![Wybierz źródło danych ODBC podczas pobierania danych w programie Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. W oknie **z ODBC** Wybierz utworzoną nazwę źródła danych, a następnie kliknij przycisk **OK**. Możesz pozostawić puste pozycje **opcji zaawansowana** .

    ![Wybierz nazwę źródła danych (DSN) podczas pobierania danych w programie Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. W oknie **dostęp do źródła danych przy użyciu sterownika ODBC** wybierz opcję **domyślne lub niestandardowe** , a następnie kliknij przycisk **Połącz**. Nie trzeba dołączać **właściwości parametrów połączenia poświadczeń**.

1. W oknie **Nawigator** w lewym okienku rozwiń bazę danych, schemat, a następnie wybierz tabelę. W okienku wyników zawiera dane przy użyciu schematu, który został utworzony.

    ![Wybierz tabelę w usłudze Power BI pobieranie danych](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Aby wizualizować dane w Power BI pulpicie, zaznacz pole przed nazwą tabeli, a następnie kliknij przycisk **Załaduj**.

1. W programie Power BI Desktop daleko po lewej, wybierz kartę danych ![Na karcie dane programu Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) Aby upewnić się, Twoje dane zostały zaimportowane.

1. Możesz teraz tworzyć wizualizacje przy użyciu Power BI, klikając kartę raport ![karcie raport w Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), klikając pozycję **Nowa Wizualizacja**, a następnie dostosowując kafelek. Aby uzyskać więcej informacji na temat tworzenia wizualizacji w Power BI Desktop, zobacz [typy wizualizacji w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony następujący błąd, upewnij się, że wartości **klucza** **hosta** i dostępu zostały skopiowane Azure Portal w [kroku 2](#connect) są poprawne, a następnie ponów próbę. Użyj przycisków kopiowania po prawej stronie wartości **hosta** i **klucz dostępu** w Azure Portal, aby skopiować wartości błędne.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](introduction.md)
