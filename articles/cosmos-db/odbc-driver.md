---
title: Połączyć z usługą Azure Cosmos DB za pomocą narzędzi analitycznych do analizy Biznesowej
description: Dowiedz się, jak tworzyć tabele i widoki, tak aby znormalizowane dane mogą być wyświetlane w oprogramowaniu do analizy danych i analizy Biznesowej za pomocą sterownika ODBC programu usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: a4cf3a9c19497057d7799be3bec78ad0de97e71a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932114"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Połączyć z usługą Azure Cosmos DB za pomocą narzędzi analitycznych do analizy Biznesowej ze sterownikiem ODBC

Sterownik platformy Azure Cosmos DB umożliwia Ci łączenie z usługą Azure Cosmos DB przy użyciu narzędzi analizy analizy Biznesowej, takich jak SQL Server Integration Services, usłudze Power Bi i Tableau można analizować i tworzyć wizualizacje danych usługi Azure Cosmos DB w tych rozwiązaniach.

Sterownik ODBC usługi Azure Cosmos DB jest zgodny ze sterownikiem ODBC 3.8 i obsługuje składnię ANSI SQL-92. Sterownik udostępnia zaawansowane funkcje ułatwiające ponowne normalizowanie danych w usłudze Azure Cosmos DB. Przy użyciu sterownika można przedstawiać dane w usłudze Azure Cosmos DB w formie tabel i widoków. Sterownik umożliwia wykonywanie operacji SQL dotyczących tabel i widoków, w tym grupowanie według zapytań, wstawień, aktualizacji i usunięć.

> [!NOTE]
> Łączenie z usługą Azure Cosmos DB przy użyciu sterownika ODBC jest obecnie obsługiwane w tylko konta interfejsu API SQL usługi Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Dlaczego trzeba normalizacji Moje dane?
Usługa Azure Cosmos DB to baza danych ze schematów, które umożliwia szybkie opracowywanie aplikacji i możliwości w celu wykonania iteracji modeli danych nie są ograniczone do ścisłego schematu. Pojedynczą bazę danych usługi Azure Cosmos DB może zawierać dokumenty JSON w różnych struktur. Jest to doskonałe rozwiązanie do szybkiego opracowywania aplikacji, ale jeśli chcesz analizować i tworzyć raporty danych za pomocą analizy danych i narzędzi do analizy Biznesowej danych często wymaga spłaszczone i być zgodne z określonym schematem.

Jest to, gdzie sterownik ODBC jest oferowana w. Za pomocą sterownika ODBC, możesz teraz ponownie normalizować dane w usłudze Azure Cosmos DB do tabel i widoków, które mieszczą się analizy danych i potrzeb dotyczących raportowania w. Schematy renormalized nie mają wpływu na dane, a nie ograniczają deweloperzy mogą stosować się do nich. Przeciwnie umożliwiają one zgodne z ODBC narzędzi do uzyskania dostępu do danych. Dlatego teraz bazy danych Azure Cosmos DB nie będzie tylko element ulubiony dla zespołu deweloperów, ale analityków danych będzie pokochasz je za.

Zacznijmy od sterownika ODBC.

## <a id="install"></a>Krok 1. Zainstaluj sterownik ODBC programu usługi Azure Cosmos DB

1. Pobieranie sterowników, które w danym środowisku:

    | Instalator | Obsługiwane systemy operacyjne| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) dla Windows 64-bitowych| 64-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64 — bit.msi](https://aka.ms/cosmos-odbc-32x64) dla 32-bitowy na 64-bitowych Windows| 64-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 i Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) dla Windows 32-bitowy|32-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows XP i Windows Vista.|

    Uruchom plik msi lokalnie, który uruchamia **Kreatora instalacji sterownika Microsoft Azure Cosmos DB ODBC**. 

1. Ukończ pracę Kreatora instalacji, przy użyciu domyślnego dane wejściowe, aby zainstalować sterownik ODBC.

1. Otwórz **Administrator źródeł danych ODBC** aplikacji na komputerze. Można to zrobić, wpisując **źródła danych ODBC** Windows — w polu wyszukiwania. 
    Możesz potwierdzić, sterownik został zainstalowany, klikając **sterowniki** kartę przy zapewnieniu **sterownika ODBC usługi Microsoft Azure Cosmos DB** znajduje się na liście.

    ![Administrator źródła danych ODBC usługi Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Krok 2. Łączenie z bazą danych Azure Cosmos DB

1. Po [instalowania sterownika ODBC programu usługi Azure Cosmos DB](#install)w **Administrator źródła danych ODBC** okna, kliknij przycisk **Dodaj**. Można utworzyć użytkownika lub System DSN. W tym przykładzie jest tworzona DSN użytkownika.

1. W **Utwórz nowe źródło danych** wybierz **sterownika ODBC usługi Microsoft Azure Cosmos DB**, a następnie kliknij przycisk **Zakończ**.

1. W **ustawienia sieci SDN sterownika ODBC usługi Azure Cosmos DB** okna, wprowadź następujące informacje: 

    ![Usługa Azure Cosmos DB ODBC Driver DSN Instalatora okna](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nazwa źródła danych**: Własne przyjazną nazwę ODBC DSN. Ta nazwa jest unikatowa dla konta usługi Azure Cosmos DB, więc nazwij ją odpowiednio w przypadku wielu kont.
    - **Opis**: Krótki opis źródła danych.
    - **Host**: Identyfikator URI dla konta usługi Azure Cosmos DB. Możesz pobrać ten ze strony usługi Azure Cosmos DB klucze w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu. 
    - **Klucz dostępu**: Klucz podstawowy lub pomocniczy, odczytu i zapisu lub tylko do odczytu ze strony usługi Azure Cosmos DB klucze w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu. Zaleca się, że używasz klucza tylko do odczytu, jeśli nazwy DSN jest używana do przetwarzania danych tylko do odczytu i raportowania.
    ![Strona usługi Azure Cosmos DB kluczy](./media/odbc-driver/odbc-driver-keys.png)
    - **Szyfrowanie klucza dostępu dla**: Wybierz najlepszy wybór na podstawie użytkowników tego komputera. 
    
1. Kliknij przycisk **testu** przycisk, aby upewnić się, czy można połączyć z kontem usługi Azure Cosmos DB. 

1. Kliknij przycisk **zaawansowane opcje** i ustaw następujące wartości:
    - **Zapytanie spójności**: Wybierz [poziomu spójności](consistency-levels.md) operacji. Wartość domyślna to sesji.
    - **Liczba ponownych prób**: Wprowadź liczbę ponownych prób operacji, jeśli żądania wstępnego nie została zakończona z powodu ograniczania szybkości usług.
    - **Plik schematu**: W tym miejscu masz kilka opcji.
        - Domyślnie ten wpis jest (pusty), pozostawiając sterownik skanuje pierwszej strony danych dla wszystkich kolekcji ustalić schemat każdej kolekcji. Jest to nazywane mapowanie kolekcji. Nie zdefiniowano pliku schematu sterownik ma skanowania dla każdej sesji sterowników i może spowodować wyższe czas uruchamiania aplikacji przy użyciu nazwy DSN. Firma Microsoft zaleca, zawsze skojarzenie pliku schematu dla nazwy DSN.
        - Jeśli masz już plik schematu (prawdopodobnie jeden, utworzony za pomocą edytora schemat), możesz kliknąć **Przeglądaj**, przejdź do pliku, kliknij przycisk **Zapisz**, a następnie kliknij przycisk **OK**.
        - Jeśli chcesz utworzyć nowy schemat, kliknij przycisk **OK**, a następnie kliknij przycisk **Edytor schematów** w głównym oknie. Następnie należy przejść do informacji Edytor schematów. Po utworzeniu nowego pliku schematu, pamiętaj, aby przejść z powrotem do **zaawansowane opcje** okna, aby dołączyć plik schematu nowo utworzony.

1. Po ukończeniu i Zamknij **ustawienia DSN sterownika ODBC usługi Azure Cosmos DB** oknie nowego użytkownika nazwy DSN jest dodawany do karty DSN użytkownika.

    ![Nowe usługi Azure Cosmos DB nazwy DSN ODBC na karcie DSN użytkownika](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Krok 3. Utwórz definicję schematu przy użyciu metody mapowania zbioru

Istnieją dwa typy metod próbkowanie, których mogą używać: **mapowanie kolekcji** lub **ograniczniki tabeli**. Sesji próbkowania, mogą korzystać z obu metod próbkowanie, ale każdej kolekcji można używać tylko metody pobierania próbek określone. Poniższe kroki Utwórz schemat dla danych w jednej lub kilku kolekcji przy użyciu metody mapowania kolekcji. Ta metoda pobierania próbek pobiera dane na stronie kolekcję, aby określić strukturę danych. Podstawianie go kolekcji do tabeli na stronie ODBC. Ta metoda pobierania próbek jest wydajny i szybkie po jednorodnych dane w kolekcji. Jeśli kolekcja zawiera heterogenicznych typów danych, zalecamy użycie [ograniczniki tabeli mapowania metoda](#table-mapping) ponieważ oferuje bardziej niezawodna metoda pobierania próbek ustalenie struktury danych w kolekcji. 

1. Po ukończeniu kroków 1 – 4 w [Połącz z bazą danych Azure Cosmos DB](#connect), kliknij przycisk **Edytor schematów** w **ustawienia DSN sterownika ODBC usługi Azure Cosmos DB** okna.

    ![Przycisk Edytor schematu w oknie Ustawienia DSN sterownika ODBC usługi Azure Cosmos DB](./media/odbc-driver/odbc-driver-schema-editor.png)
1. W **Edytor schematów** okna, kliknij przycisk **Utwórz nowy**.
    **Generowanie schematu** okna wyświetla wszystkie kolekcje w ramach konta usługi Azure Cosmos DB. 

1. Wybierz co najmniej jedną kolekcję próbki, a następnie kliknij przycisk **przykładowe**. 

1. W **widoku projektu** są reprezentowane kartę bazy danych, schematu i tabeli. W widoku tabeli skanowania wyświetla zestaw właściwości związanych z nazwami kolumn (Nazwa SQL, nazwa źródła itp.).
    Dla każdej kolumny, możesz zmodyfikować nazwę kolumny SQL, typ SQL, długości SQL (jeśli dotyczy), skalowanie (jeśli dotyczy), dokładność (jeśli dotyczy) oraz Nullable.
    - Możesz ustawić **Ukryj kolumnę** do **true** Jeśli chcesz wyłączyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Ukryj kolumnę = true nie są zwracane do wyboru i projekcji, mimo że wciąż są częścią schematu. Na przykład można ukryć wszystkie właściwości systemu wymagane usługi Azure Cosmos DB, począwszy od "_".
    - **Identyfikator** kolumna jest tylko pola, które nie mogą być ukryte, ponieważ jest ona używana jako klucz podstawowy w schemacie znormalizowana. 

1. Po określeniu schematu kliknij **pliku** | **Zapisz**, przejdź do katalogu, aby zapisać schematu, a następnie kliknij **Zapisz**.

1. Aby użyć tego schematu przy użyciu nazwy DSN, otwórz **okno Ustawienia DSN sterownika ODBC usługi Azure Cosmos DB** (za pośrednictwem Administrator źródła danych ODBC), kliknij przycisk **zaawansowane opcje**, a następnie w polu **pliku schematu** , przejdź do zapisanego schematu. Zapisywanie pliku schematu istniejącej nazwy DSN modyfikuje połączenia DSN do zakresu do danych i struktury definiowana przez schemat.

## <a id="table-mapping"></a>Krok 4. Utwórz definicję schematu za pomocą ograniczniki tabeli mapowania — metoda

Istnieją dwa typy metod próbkowanie, których mogą używać: **mapowanie kolekcji** lub **ograniczniki tabeli**. Sesji próbkowania, mogą korzystać z obu metod próbkowanie, ale każdej kolekcji można używać tylko metody pobierania próbek określone. 

Poniższe kroki pozwalają utworzyć schemat dla danych w jednej lub kilku kolekcji za pomocą **ograniczniki tabeli** mapowanie metody. Zaleca się korzystać z tej metody pobierania próbek, jeśli kolekcje zawierają heterogenicznych typów danych. Ta metoda służy do określania zakresu próbkowania do zestawu atrybutów i ich odpowiednie wartości. Na przykład jeśli dokument zawiera właściwość "Type", możesz ograniczyć próbkowanie, aby wartości tej właściwości. Wynik końcowy próbkowanie będzie zestawu tabel dla każdej wartości mają określonego typu. Na przykład wpisz = samochodu będzie utworzyć tabelę samochodu podczas typu = płaszczyzny dałby w efekcie tabeli płaszczyzny.

1. Po ukończeniu kroków 1 – 4 w [Połącz z bazą danych Azure Cosmos DB](#connect), kliknij przycisk **Edytor schematów** w oknie Ustawienia DSN sterownika ODBC usługi Azure Cosmos DB.

1. W **Edytor schematów** okna, kliknij przycisk **Utwórz nowy**.
    **Generowanie schematu** okna wyświetla wszystkie kolekcje w ramach konta usługi Azure Cosmos DB. 

1. Wybierz kolekcję na **przykładowy widok** na karcie **Mapping Definition** kolumny dla kolekcji, kliknij przycisk **Edytuj**. Następnie w **Mapping Definition** wybierz **ograniczniki tabeli** metody. Następnie wykonaj poniższe czynności:

    a. W **atrybuty** wpisz nazwę właściwości ogranicznika. Jest to właściwość w dokumencie, który chcesz ograniczyć zakres pobierania próbek, aby na przykład miasta, a następnie naciśnij klawisz enter. 

    b. Jeśli chcesz ograniczyć zakres próbkowania do określonych wartości dla atrybutu wprowadzony powyżej, wybierz atrybut, w polu wyboru, wprowadź wartość w **wartość** pola (np. Seattle), a następnie naciśnij klawisz, wprowadź. Można dodać wiele wartości dla atrybutów. Po prostu upewnij się, że właściwy atrybut jest zaznaczone, gdy wprowadzasz wartości.

    Na przykład Jeśli dołączysz **atrybuty** wartości City, a chcesz ograniczyć tabeli obejmujący tylko wiersze z wartością Miasto Warszawa i Dubaj, miasta w polu atrybutów i Nowy Jork, a następnie Dubaj wprowadzaliby w **Wartości** pole.

1. Kliknij przycisk **OK**. 

1. Po zakończeniu definicji mapowania dla kolekcji mają zostać pobrane w **Edytor schematów** okna, kliknij przycisk **przykładowe**.
     Dla każdej kolumny, możesz zmodyfikować nazwę kolumny SQL, typ SQL, długości SQL (jeśli dotyczy), skalowanie (jeśli dotyczy), dokładność (jeśli dotyczy) oraz Nullable.
    - Możesz ustawić **Ukryj kolumnę** do **true** Jeśli chcesz wyłączyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Ukryj kolumnę = true nie są zwracane do wyboru i projekcji, mimo że wciąż są częścią schematu. Na przykład możesz ukryć wszystkie właściwości systemu wymagane usługi Azure Cosmos DB, począwszy od `_`.
    - **Identyfikator** kolumna jest tylko pola, które nie mogą być ukryte, ponieważ jest ona używana jako klucz podstawowy w schemacie znormalizowana. 

1. Po określeniu schematu kliknij **pliku** | **Zapisz**, przejdź do katalogu, aby zapisać schematu, a następnie kliknij **Zapisz**.

1. Ponownie **ustawienia DSN sterownika ODBC usługi Azure Cosmos DB** okna, kliknij przycisk **zaawansowane opcje**. Następnie w **pliku schematu** pola, przejdź do pliku zapisanego schematu i kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać nazwę DSN. Spowoduje to zapisanie schematu, który został utworzony do nazwy DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcjonalnie) Konfigurowanie połączenia z połączonym serwerem

Można tworzyć zapytania usługi Azure Cosmos DB z programu SQL Server Management Studio (SSMS), konfigurując połączenia z połączonym serwerem.

1. Utwórz źródło danych systemu, zgodnie z opisem w [kroku 2](#connect)nazwanego, na przykład `SDS Name`.

1. [Zainstaluj program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i połączyć się z serwerem. 

1. W edytorze zapytań programu SSMS, Utwórz obiekt serwera połączonego `DEMOCOSMOS` dla źródła danych za pomocą następujących poleceń. Zastąp `DEMOCOSMOS` o nazwie dla połączonego serwera i `SDS Name` nazwą źródła danych systemu.

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

Aby wysłać zapytanie połączonej bazy danych, wprowadź kwerendę programu SSMS. W tym przykładzie zapytanie wybiera się z tabelą w kolekcji o nazwie `customers`:

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

Aby utworzyć widok dla swoich danych w **Edytor schematów** okna w **definicji widoku** kolumny, kliknij przycisk **Dodaj** w wierszu kolekcji do przykładu. 
    ![Utwórz widok danych](./media/odbc-driver/odbc-driver-create-view.png)


Następnie w **definicji widoku** okna, wykonaj następujące czynności:

1. Kliknij przycisk **New**, wprowadź nazwę dla widoku, na przykład EmployeesfromSeattleView, a następnie kliknij przycisk **OK**.

1. W **Edytowanie widoku** oknie wprowadź zapytanie usługi Azure Cosmos DB. Musi to być [zapytania SQL usługi Azure Cosmos DB](how-to-sql-query.md), na przykład `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, a następnie kliknij przycisk **OK**.

    ![Dodaj zapytanie, podczas tworzenia widoku](./media/odbc-driver/odbc-driver-create-view-2.png)


Można utworzyć wiele widoków, jak chcesz. Po zakończeniu definiowania widoków, możesz następnie próbkowanie danych. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5. Wyświetlanie danych w narzędziach, takich jak Power BI Desktop

Twoje nowe źródło danych umożliwia łączenie z usługi Azure Cosmos DB przy użyciu dowolnych narzędzi standardem ODBC — w tym kroku po prostu dowiesz się, jak nawiązać połączenie z programu Power BI Desktop i tworzenie wizualizacji usługi Power BI.

1. Otwórz program Power BI Desktop.

1. Kliknij przycisk **pobieranie danych**.

    ![Pobieranie danych w programie Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. W **Pobierz dane** okna, kliknij przycisk **innych** | **ODBC** | **Connect**.

    ![Wybierz źródło danych ODBC podczas pobierania danych w programie Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. W **z ODBC** okna, wybierz nazwę utworzonego źródła danych, a następnie kliknij przycisk **OK**. Możesz pozostawić **zaawansowane opcje** wpisy puste.

    ![Wybierz nazwę źródła danych (DSN) podczas pobierania danych w programie Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. W **dostęp do źródła danych za pomocą sterownika ODBC** wybierz **domyślny lub niestandardowy** a następnie kliknij przycisk **Connect**. Nie trzeba uwzględnić **poświadczeń właściwości parametrów połączenia**.

1. W **Nawigator** oknie w lewym okienku rozwiń węzeł bazy danych, schematów, a następnie wybierz pozycję tabeli. W okienku wyników zawiera dane przy użyciu schematu, który został utworzony.

    ![Wybierz tabelę w usłudze Power BI pobieranie danych](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. W celu wizualizacji danych w usłudze Power BI desktop, zaznacz pole obok nazwy tabeli, a następnie kliknij przycisk **obciążenia**.

1. W programie Power BI Desktop daleko po lewej, wybierz kartę danych ![Na karcie dane programu Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) Aby upewnić się, Twoje dane zostały zaimportowane.

1. Można teraz tworzyć elementy wizualne przy użyciu usługi Power BI, klikając kartę raportu ![kartę raportu w programie Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), klikając pozycję **nowego programu Visual**, a następnie dostosowanie kafelka. Aby uzyskać więcej informacji na temat tworzenia wizualizacji w programie Power BI Desktop, zobacz [typy wizualizacji w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony następujący błąd, upewnij się, **hosta** i **klucz dostępu** wartości skopiowaną z witryny Azure portal w [kroku 2](#connect) są poprawne, a następnie spróbuj ponownie. Użyj przycisków kopiowania dostępnych po prawej stronie **hosta** i **klucz dostępu** wartości w witrynie Azure portal, aby skopiować bez błędów wartości.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](introduction.md)
