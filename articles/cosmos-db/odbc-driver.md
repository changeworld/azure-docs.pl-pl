---
title: Połączenie do bazy danych rozwiązania Cosmos Azure przy użyciu narzędzi do analizy Biznesowej analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać ze sterownika ODBC DB rozwiązania Cosmos Azure utworzyć tabele i widoki, tak aby znormalizowane danych mogą być wyświetlane w oprogramowania analizy danych i analizy Biznesowej.
keywords: ODBC i sterownik odbc
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 11e0a4e38739a775d977cf76b72d4dc92942a7b0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725095"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Łączenie do bazy danych rozwiązania Cosmos Azure przy użyciu narzędzia do analizy BI ze sterownikiem ODBC

Sterownik ODBC DB rozwiązania Cosmos Azure umożliwia podłączenie do bazy danych rozwiązania Cosmos Azure za pomocą narzędzia do analizy analizy Biznesowej, takich jak SQL Server Integration Services, Power BI Desktop i Tableau, dzięki czemu można analizować i tworzyć wizualizacje danych bazy danych Azure rozwiązania Cosmos w tych rozwiązań.

Sterownik ODBC DB rozwiązania Cosmos Azure jest zgodny z 3.8 ODBC i obsługuje składni ANSI SQL-92. Sterownik oferuje zaawansowane funkcje ułatwiające renormalize danych w usłudze Azure DB rozwiązania Cosmos. Sterownik może reprezentować danych w usłudze Azure DB rozwiązania Cosmos jako tabele i widoki. Sterownik umożliwia wykonywanie operacji SQL przed tabele i widoki, w tym grupy w zapytaniach, wstawiania, aktualizacji i usuwania.

## <a name="why-do-i-need-to-normalize-my-data"></a>Dlaczego muszę normalizacji danych?
Azure DB rozwiązania Cosmos jest bezschematową bazą danych, więc umożliwia szybkie opracowywanie aplikacji, umożliwiając aplikacjom iteracji modelu danych, ich na bieżąco i nie ograniczają ich strict schematu. Pojedyncza baza danych bazy danych Azure rozwiązania Cosmos może zawierać dokumentów JSON różnych struktur. Jest to doskonałe rozwiązanie dla szybkie programowanie aplikacji, ale do analizowania i tworzenia raportów, korzystając z analizy danych i narzędzi do analizy Biznesowej danych, należy dane często muszą być spłaszczane i być zgodne z określonym schematem.

Jest to, gdzie sterownik ODBC jest dostarczany. Za pomocą sterownika ODBC, można teraz renormalize danych w usłudze Azure DB rozwiązania Cosmos w tabelach i widokach dopasowania do potrzeb danych analitycznych i raportowania. Schematy renormalized nie mają wpływu na dane, a nie ograniczają deweloperom stosować się do nich, umożliwiają one korzystać ze standardem ODBC narzędzia dostępu do danych. Dlatego teraz bazy danych Azure DB rozwiązania Cosmos nie tylko będzie element ulubiony dla zespołu deweloperów, ale analityków danych będzie pokochasz je za.

Teraz umożliwia wprowadzenie sterownik ODBC.

## <a id="install"></a>Krok 1: Instalowanie sterownika ODBC DB rozwiązania Cosmos Azure

1. Pobieranie sterowników w danym środowisku:

    | Instalator | Obsługiwane systemy operacyjne| 
    |---|---| 
    |[Microsoft Azure rozwiązania Cosmos bazy danych ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) dla 64-bitowego systemu Windows| 64-bitowe wersje systemu Windows 8.1 lub nowszego, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2.| 
    |[Microsoft Azure rozwiązania Cosmos bazy danych ODBC 32 x 64-bit.msi](https://aka.ms/documentdb-odbc-32x64) dla 32-bitowych na 64-bitowego systemu Windows| 64-bitowe wersje systemu Windows 8.1 lub nowszego, systemu Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 i systemu Windows Server 2003.| 
    |[Microsoft Azure rozwiązania Cosmos bazy danych ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) dla 32-bitowego systemu Windows|32-bitowe wersje systemu Windows 8.1 lub nowszym, Windows 8, Windows 7, Windows XP i Windows Vista.|

    Uruchom plik msi lokalnie, która uruchamia **Kreatora instalacji sterownika ODBC bazy danych Microsoft Azure rozwiązania Cosmos**. 
2. Ukończ pracę Kreatora instalacji przy użyciu domyślnego danych wejściowych do zainstalowania sterownika ODBC.
3. Otwórz **Administrator źródeł danych ODBC** aplikacji na komputerze, można to zrobić wpisanie **źródeł danych ODBC** polu wyszukiwania systemu Windows. 
    Można potwierdzić sterownik został zainstalowany, klikając **sterowniki** kartę i zapewnienie **sterownika ODBC usługi Microsoft Azure rozwiązania Cosmos DB** ma na liście.

    ![Administrator źródła danych ODBC Azure rozwiązania Cosmos bazy danych](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Krok 2: Łączenie się z bazą danych Azure DB rozwiązania Cosmos

1. Po [instalowania sterownika ODBC DB rozwiązania Cosmos Azure](#install)w **Administrator źródła danych ODBC** okna, kliknij przycisk **Dodaj**. Można utworzyć użytkownika lub nazwy DSN systemu. W tym przykładzie jest tworzony DSN użytkownika.
2. W **Utwórz nowe źródło danych** wybierz **sterownika ODBC usługi Microsoft Azure rozwiązania Cosmos DB**, a następnie kliknij przycisk **Zakończ**.
3. W **ustawienia SDN sterownika ODBC Azure rozwiązania Cosmos DB** okna, wypełnij następujące czynności: 

    ![Azure okno Ustawienia DSN sterownika ODBC DB rozwiązania Cosmos](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nazwa źródła danych**: przyjazną nazwę dla pliku DSN ODBC. Ta nazwa jest unikatowa dla konta bazy danych Azure rozwiązania Cosmos, więc nadaj jej nazwę odpowiednio Jeśli masz wiele kont.
    - **Opis elementu**: Krótki opis źródła danych.
    - **Host**: identyfikator URI dla Twojego konta bazy danych Azure rozwiązania Cosmos. Możesz pobrać to ze strony Azure rozwiązania Cosmos DB klucze w portalu Azure, jak pokazano na poniższym zrzucie ekranu. 
    - **Klawisz dostępu**: klucz podstawowy lub pomocniczy, Odczyt i zapis lub tylko do odczytu z kluczy Azure DB rozwiązania Cosmos strony w portalu Azure, jak pokazano na poniższym zrzucie ekranu. Firma Microsoft zaleca się, że używasz klucza tylko do odczytu, jeśli nazwa DSN jest używana do przetwarzania danych tylko do odczytu i raportowania.
    ![Strona rozwiązania Cosmos DB kluczy Azure](./media/odbc-driver/odbc-driver-keys.png)
    - **Szyfrowanie klucza dostępu dla**: Wybierz najlepszym wyborem na podstawie użytkowników tego komputera. 
4. Kliknij przycisk **testu** przycisk, aby się upewnić, że możesz nawiązać połączenie konta bazy danych Azure rozwiązania Cosmos. 
5. Kliknij przycisk **zaawansowane opcje** i ustaw następujące wartości:
    - **Zapytanie spójności**: Wybierz [poziomu spójności](consistency-levels.md) dla operacji. Wartość domyślna to sesji.
    - **Liczba ponownych prób**: Wprowadź liczbę ponownych prób operacji, jeśli żądania początkowego nie została zakończona z powodu dławienia usługi.
    - **Plik schematu**: w tym miejscu masz kilka opcji.
        - Domyślnie ten wpis jest (pusty), pozostawiając sterownika skanuje pierwsze dane strony dla wszystkich kolekcji w celu określenia schematu każdej kolekcji. Jest to nazywane mapowania kolekcji. Bez pliku schematu zdefiniowane sterownik musi wykonać skanowania dla każdej sesji sterowników i może skutkować wyższej czas uruchamiania aplikacji przy użyciu nazwy DSN. Firma Microsoft zaleca zawsze skojarzenia pliku schematu dla nazwy DSN.
        - Jeśli masz już plik schematu (prawdopodobnie jedną, które zostały utworzone za pomocą [Edytor schematów](#schema-editor)), możesz kliknąć **Przeglądaj**, przejdź do pliku, kliknij przycisk **zapisać**, a następnie kliknij przycisk **OK**.
        - Jeśli chcesz utworzyć nowy schemat, kliknij przycisk **OK**, a następnie kliknij przycisk **Edytor schematów** w oknie głównym. Następnie przejdź do [Edytor schematów](#schema-editor) informacji. Po utworzeniu nowego pliku schematu, pamiętaj powrócić do **zaawansowane opcje** okno, aby uwzględnić schematu nowo utworzony plik.

6. Po zakończeniu i zamknąć **ustawienia DSN sterownika ODBC Azure rozwiązania Cosmos DB** okna, nowej nazwy DSN użytkownika zostanie dodany do karty DSN użytkownika.

    ![Nowe Azure rozwiązania Cosmos DB DSN ODBC na karcie DSN użytkownika](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Krok 3: Tworzenie definicji schematu przy użyciu metody mapowanie kolekcji

Istnieją dwa typy metod pobierania próbek, których mogą używać: **mapowania kolekcji** lub **ograniczników tabeli**. Obie metody pobierania próbek mogą korzystać z sesji próbkowania, ale każdej kolekcji można używać tylko metody pobierania próbek określonych. Poniższe kroki tworzenia schematu dla danych w jednej lub kilku kolekcjach przy użyciu metody mapowania kolekcji. Ta metoda pobierania próbek pobiera dane na stronie kolekcji ustalenie struktury danych. Go transposes kolekcji do tabeli na stronie ODBC. Ta metoda pobierania próbek jest wydajne i szybkie po jednorodnego danych w kolekcji. Jeśli kolekcja zawiera heterogenicznych typu danych, zalecane jest użycie [ograniczników tabeli mapowania metody](#table-mapping) jako zapewnia bardziej niezawodna metoda pobierania próbek ustalenie struktury danych w kolekcji. 

1. Po zakończeniu kroków od 1 do 4 w [Połącz się z bazą danych Azure DB rozwiązania Cosmos](#connect), kliknij przycisk **Edytor schematów** w **ustawienia DSN sterownika ODBC Azure rozwiązania Cosmos DB** okna.

    ![Przycisk Edytor schematu w oknie Ustawienia DSN sterownika ODBC DB Azure rozwiązania Cosmos](./media/odbc-driver/odbc-driver-schema-editor.png)
2. W **Edytor schematów** okna, kliknij przycisk **Utwórz nowy**.
    **Generowania schematu** okno wyświetla wszystkie kolekcje w ramach konta bazy danych Azure rozwiązania Cosmos. 
3. Wybierz co najmniej jedną kolekcję próbki, a następnie kliknij przycisk **próbki**. 
4. W **widoku Projekt** są reprezentowane kartę bazy danych, schematu i tabeli. W widoku tabeli skanowania wyświetla zbiór właściwości skojarzone z nazwami kolumn (Nazwa SQL, nazwa źródła, itp.).
    Dla każdej kolumny, możesz zmodyfikować nazwę kolumny SQL, typ SQL, długość SQL (jeśli dotyczy), skali (jeśli dotyczy), Precision (jeśli dotyczy) oraz Nullable.
    - Można ustawić **Ukryj kolumnę** do **true** Aby wykluczyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Ukryj kolumnę = true nie są zwracane do wyboru i projekcji, mimo że nadal są częścią schematu. Na przykład można ukryć wszystkie właściwości wymagane systemu Azure DB rozwiązania Cosmos począwszy od "_".
    - **Identyfikator** kolumna jest tylko pola, które nie mogą być ukryte, ponieważ jest używany jako klucz podstawowy w schemacie znormalizowana. 
5. Po określeniu schematu kliknij **pliku** | **zapisać**, przejdź do katalogu, aby zapisać schematu, a następnie kliknij przycisk **zapisać**.

    Jeśli w przyszłości chcesz użyć tego schematu o nazwie DSN, Otwórz okno Ustawienia DSN sterownika ODBC Azure rozwiązania Cosmos bazy danych (za pośrednictwem Administrator źródła danych ODBC), kliknij przycisk Opcje zaawansowane i następnie w polu pliku schematu przejdź do zapisanego schematu. Zapisywanie pliku schematu do istniejącej nazwy DSN modyfikuje połączenia DSN, aby określić zakres danych i struktury zdefiniowane przez schemat.

## <a id="table-mapping"></a>Krok 4: Tworzenie definicji schematu przy użyciu ograniczników tabeli mapowania — metoda

Istnieją dwa typy metod pobierania próbek, których mogą używać: **mapowania kolekcji** lub **ograniczników tabeli**. Obie metody pobierania próbek mogą korzystać z sesji próbkowania, ale każdej kolekcji można używać tylko metody pobierania próbek określonych. 

Poniższe kroki tworzenia schematu dla danych w co najmniej jednej kolekcji przy użyciu **ograniczników tabeli** mapowanie metody. Firma Microsoft zaleca, użyj tej metody pobierania próbek, gdy kolekcji zawiera heterogeniczne typu danych. Ta metoda służy do określania zakresu próbkowania do zestawu atrybutów i jej wartości. Na przykład jeśli dokument zawiera właściwość "Type", możesz zakresu próbkowanie wartości tej właściwości. W rezultacie próbki byłoby zestawu tabel dla każdej z wartości dla typu został określony. Na przykład wpisz = samochodu spowoduje utworzenie tabeli samochodu podczas typu = płaszczyzny dałby w efekcie tabeli płaszczyzny.

1. Po zakończeniu kroków od 1 do 4 w [Połącz się z bazą danych Azure DB rozwiązania Cosmos](#connect), kliknij przycisk **Edytor schematów** w oknie Ustawienia DSN sterownika ODBC Azure rozwiązania Cosmos bazy danych.
2. W **Edytor schematów** okna, kliknij przycisk **Utwórz nowy**.
    **Generowania schematu** okno wyświetla wszystkie kolekcje w ramach konta bazy danych Azure rozwiązania Cosmos. 
3. Wybierz kolekcję na **przykładowy widok** karcie **definicji mapowanie** kolumny dla kolekcji, kliknij przycisk **Edytuj**. Następnie w **definicji mapowanie** wybierz **ograniczników tabeli** metody. Następnie wykonaj poniższe czynności:

    a. W **atrybuty** wpisz nazwę właściwości ogranicznika. Jest to właściwość w dokumencie, który ma być zakres pobierania próbek, aby na przykład miasto, a następnie naciśnij klawisz enter. 

    b. Jeśli chcesz tylko zakres próbkowania do niektórych wartości atrybutów podanej, wybierz atrybut pole wyboru, a następnie wprowadź wartość w **wartość** okno, na przykład Seattle a naciśnij klawisz enter. Można dodać wiele wartości atrybutów. Po prostu upewnij się, że właściwy atrybut jest zaznaczone, gdy wprowadzasz wartości.

    Na przykład, Jeśli dołączysz **atrybuty** wartość miasto i chce ograniczyć tabeli obejmujący tylko wiersze z wartością Miasto Warszawa i Dubaj, możesz wprowadzić Miasto w polu atrybutów i Nowy Jork i następnie Dubaj w **Wartości** pole.
4. Kliknij przycisk **OK**. 
5. Po zakończeniu definicji mapowanie dla kolekcji mają zostać pobrane w **Edytor schematów** okna, kliknij przycisk **próbki**.
     Dla każdej kolumny, możesz zmodyfikować nazwę kolumny SQL, typ SQL, długość SQL (jeśli dotyczy), skali (jeśli dotyczy), Precision (jeśli dotyczy) oraz Nullable.
    - Można ustawić **Ukryj kolumnę** do **true** Aby wykluczyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Ukryj kolumnę = true nie są zwracane do wyboru i projekcji, mimo że nadal są częścią schematu. Na przykład można ukryć wszystkie właściwości wymagane systemu bazy danych Azure rozwiązania Cosmos począwszy `_`.
    - **Identyfikator** kolumna jest tylko pola, które nie mogą być ukryte, ponieważ jest używany jako klucz podstawowy w schemacie znormalizowana. 
6. Po określeniu schematu kliknij **pliku** | **zapisać**, przejdź do katalogu, aby zapisać schematu, a następnie kliknij przycisk **zapisać**.
7. W **ustawienia DSN sterownika ODBC Azure rozwiązania Cosmos DB** okna, kliknij przycisk **zaawansowane opcje**. Następnie w **pliku schematu** polu, przejdź do pliku zapisanego schematu i kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać pliku DSN. Nazwa DSN to zapisuje schematu, który został utworzony. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcjonalnie) Skonfiguruj połączenie z serwerem połączonym

Bazy danych z programu SQL Server Management Studio (SSMS) rozwiązania Cosmos Azure można badać przy konfigurowaniu połączenia połączonego serwera.

1. Utwórz źródło danych systemu, zgodnie z opisem w [krok 2](#connect), na przykład nazwane `SDS Name`.
2. [Zainstaluj program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i nawiąż połączenie z serwerem. 
3. W edytorze zapytań SSMS, Utwórz obiekt połączonego serwera `DEMOCOSMOS` dla źródła danych za pomocą następujących poleceń. Zastąp `DEMOCOSMOS` o nazwie dla połączonego serwera, a `SDS Name` nazwą źródła danych systemu.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Aby wyświetlić nową nazwę połączonego serwera, Odśwież listę serwerów połączonych.

![Serwer połączony w programie SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Zapytanie połączonej bazy danych

Aby odpytać połączonej bazy danych, należy wprowadzić kwerendę SSMS. W tym przykładzie zapytanie wybiera z tabeli w kolekcji o nazwie `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Wykonanie kwerendy. Wynik powinien być podobny do poniższego:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Połączonego serwera rozwiązania Cosmos bazy danych nie obsługuje nazewnictwa czteroczęściową. Jest to komunikat o błędzie podobny do następującego:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcjonalnie) Tworzenie widoków
Można zdefiniować i tworzyć widoki w ramach procesu pobierania próbek. Widoki te są równoważne widoki SQL. Są tylko do odczytu i są opcje zakresu i zdefiniowane projekcje Azure rozwiązania Cosmos bazy danych SQL. 

Aby utworzyć widok danych, w **Edytor schematów** okna w **definicji widoku** kolumny, kliknij przycisk **Dodaj** w wierszu kolekcji do przykładowych. Następnie w **definicji widoku** okna, wykonaj następujące czynności:
1. Kliknij przycisk **nowy**, wprowadź nazwę widoku, na przykład EmployeesfromSeattleView, a następnie kliknij przycisk **OK**.
2. W **Edytowanie widoku** okna, wprowadź zapytanie bazy danych Azure rozwiązania Cosmos. Musi to być zapytania Azure rozwiązania Cosmos bazy danych SQL, na przykład`SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`, a następnie kliknij przycisk **OK**.

Można utworzyć wiele widoków, według uznania. Po zakończeniu definiowania widoków, można następnie pobrania przykładowych danych. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Zostaną wyświetlone dane narzędzi analizy Biznesowej, takich jak Power BI Desktop

Twoje nowe źródło danych umożliwia łączenie DocumentADB ze standardem ODBC narzędzi — ten krok po prostu przedstawiono sposób nawiązania połączenia programu Power BI Desktop i tworzenie wizualizacji usługi Power BI.

1. Otwórz program Power BI Desktop.
2. Kliknij przycisk **Pobierz dane**.
3. W **Pobierz dane** okna, kliknij przycisk **innych** | **ODBC** | **Connect**.
4. W **z ODBC** okna, wybierz nazwę utworzonego źródła danych, a następnie kliknij przycisk **OK**. Możesz pozostawić **zaawansowane opcje** wpisy puste.
5. W **dostęp do źródła danych za pomocą sterownika ODBC** wybierz **domyślnej lub niestandardowej** , a następnie kliknij przycisk **Connect**. Nie trzeba uwzględnić **poświadczeń właściwości parametrów połączenia**.
6. W **Nawigator** oknie w lewym okienku rozwiń bazę danych, schematów, a następnie wybierz tabeli. W okienku wyników zawiera dane przy użyciu schematu, który został utworzony.
7. Do wizualizacji danych w usłudze Power BI desktop, zaznacz pole obok nazwy tabeli, a następnie kliknij przycisk **obciążenia**.
8. W programie Power BI Desktop po lewej, wybierz kartę danych ![Na karcie dane Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) Aby potwierdzić dane zostały zaimportowane.
9. Teraz można utworzyć przy użyciu usługi Power BI, klikając na karcie raport elementy wizualne ![raport w programie Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), klikając pozycję **nowe Visual**, a następnie dostosowywania kafelka. Aby uzyskać więcej informacji o tworzeniu wizualizacje w programie Power BI Desktop, zobacz [wizualizację typów w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony następujący błąd, upewnij się, **hosta** i **klucz dostępu** wartości został skopiowany z portalu Azure w [krok 2](#connect) są poprawne, a następnie spróbuj ponownie. Użyj przycisków Kopiuj po prawej stronie **hosta** i **klucz dostępu** wartości w portalu Azure, aby skopiować bez błędów wartości.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat bazy danych rozwiązania Cosmos Azure, zobacz [Zapraszamy do bazy danych Azure rozwiązania Cosmos](introduction.md).
