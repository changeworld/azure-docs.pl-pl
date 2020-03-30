---
title: Łączenie się z usługą Azure Cosmos DB przy użyciu narzędzi analitycznych analizy biznesowej
description: Dowiedz się, jak używać sterownika ODBC usługi Azure Cosmos DB do tworzenia tabel i widoków, dzięki czemu znormalizowane dane mogą być wyświetlane w analizie biznesowej i oprogramowaniu do analizy danych.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721085"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Łączenie się z usługą Azure Cosmos DB przy użyciu narzędzi analitycznych bi za pomocą sterownika ODBC

Sterownik ODBC usługi Azure Cosmos DB umożliwia łączenie się z usługą Azure Cosmos DB przy użyciu narzędzi analizy biznesowej, takich jak usługi integracji programu SQL Server, pulpit usługi Power BI i Tableau, dzięki czemu można analizować i tworzyć wizualizacje danych usługi Azure Cosmos DB w tych rozwiązaniach.

Sterownik ODBC usługi Azure Cosmos DB jest zgodny ze sterownikiem ODBC 3.8 i obsługuje składnię ANSI SQL-92. Sterownik udostępnia zaawansowane funkcje ułatwiające ponowne normalizowanie danych w usłudze Azure Cosmos DB. Przy użyciu sterownika można przedstawiać dane w usłudze Azure Cosmos DB w formie tabel i widoków. Sterownik umożliwia wykonywanie operacji SQL dotyczących tabel i widoków, w tym grupowanie według zapytań, wstawień, aktualizacji i usunięć.

> [!NOTE]
> Łączenie się z usługą Azure Cosmos DB za pomocą sterownika ODBC jest obecnie obsługiwane tylko dla kont interfejsu API sql usługi Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Dlaczego muszę normalizować swoje dane?
Usługa Azure Cosmos DB to baza danych bez schematu, która umożliwia szybkie tworzenie aplikacji i możliwość iteracji w modelach danych bez ograniczania się do ścisłego schematu. Pojedyncza baza danych usługi Azure Cosmos może zawierać dokumenty JSON różnych struktur. Jest to idealne rozwiązanie do szybkiego tworzenia aplikacji, ale jeśli chcesz analizować i tworzyć raporty danych przy użyciu analizy danych i narzędzi analizy biznesowej, dane często muszą być spłaszczone i stosować się do określonego schematu.

W tym miejscu pojawia się sterownik ODBC. Za pomocą sterownika ODBC można teraz ponownie znormalizować dane w usłudze Azure Cosmos DB w tabelach i widokach, które odpowiadają twoim potrzebom w zakresie analizy danych i raportowania. Schematy renormalized nie mają wpływu na dane źródłowe i nie ograniczają deweloperów do ich przestrzegania. Umożliwiają one raczej korzystanie z narzędzi zgodnych ze standardem ODBC w celu uzyskania dostępu do danych. Teraz baza danych usługi Azure Cosmos będzie nie tylko ulubiona dla zespołu deweloperów, ale analitycy danych również ją pokochają.

Zacznijmy od sterownika ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Krok 1: Instalowanie sterownika ODBC usługi Azure Cosmos DB

1. Pobierz sterowniki dla swojego środowiska:

    | Instalator | Obsługiwane systemy operacyjne| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) dla 64-bitowego systemu Windows| 64-bitowe wersje systemu Windows 8.1 lub nowszego, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) dla 32-bitowych w 64-bitowym systemie Windows| 64-bitowe wersje systemu Windows 8.1 lub nowszego, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 i Windows Server 2003.| 
    |[32-bitowy.msi usługi Microsoft Azure Cosmos DB ODBC](https://aka.ms/cosmos-odbc-32x32) dla 32-bitowego systemu Windows|32-bitowe wersje systemu Windows 8.1 lub nowszego, Windows 8, Windows 7, Windows XP i Windows Vista.|

    Uruchom plik msi lokalnie, który uruchamia **Kreator instalacji sterowników usługi Microsoft Azure Cosmos DB ODBC**. 

1. Ukończ kreatora instalacji przy użyciu domyślnego wejścia, aby zainstalować sterownik ODBC.

1. Otwórz aplikację **ADMINISTRATOR ŹRÓDŁA DANYCH ODBC** na komputerze. Można to zrobić, wpisując **źródła danych ODBC** w polu wyszukiwania systemu Windows. 
    Sterownik został zainstalowany, klikając kartę **Sterowniki** i zapewniając wyświetlenie **sterownika ODBC usługi Microsoft Azure Cosmos DB.**

    ![Administrator źródła danych usługi Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver.png)

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Krok 2: Łączenie się z bazą danych usługi Azure Cosmos

1. Po [zainstalowaniu sterownika ODBC usługi Azure Cosmos DB](#install)w oknie **Administrator źródła danych ODBC** kliknij przycisk **Dodaj**. Można utworzyć uszytę lub systemową sieć DSN. W tym przykładzie tworzysz nazwy DSN użytkownika.

1. W oknie **Tworzenie nowego źródła danych** wybierz pozycję Sterownik **ODBC bazy danych usługi Microsoft Azure Cosmos DB,** a następnie kliknij przycisk **Zakończ**.

1. W oknie **Ustawienia SDN sterownika usługi Azure Cosmos DB ODBC** wprowadź następujące informacje: 

    ![Okno Instalatora sterownika DSN sterownika usługi Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nazwa źródła danych:** Twoja własna przyjazna nazwa dla ODBC DSN. Ta nazwa jest unikatowa dla konta usługi Azure Cosmos DB, więc nazwij ją odpowiednio, jeśli masz wiele kont.
    - **Opis**: Krótki opis źródła danych.
    - **Host:** Identyfikator URI dla konta usługi Azure Cosmos DB. Można pobrać to ze strony klucze bazy danych usługi Azure Cosmos w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu. 
    - **Klucz dostępu:** Podstawowy lub pomocniczy, do odczytu i tylko do odczytu klucz ze strony Klucze bazy danych usługi Azure Cosmos w portalu Azure, jak pokazano na poniższym zrzucie ekranu. Zaleca się używanie klucza tylko do odczytu, jeśli dsn jest używany do przetwarzania danych tylko do odczytu i raportowania.
    ![Strona Klucze bazy danych usługi Azure Cosmos](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Klucz dostępu do szyfrowania**: Wybierz najlepszy wybór na podstawie użytkowników tego komputera. 
    
1. Kliknij przycisk **Testuj,** aby upewnić się, że możesz połączyć się z kontem usługi Azure Cosmos DB. 

1.  Kliknij **pozycję Opcje zaawansowane** i ustaw następujące wartości:
    *  **REST WERSJA INTERFEJSU API:** Wybierz [wersję interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/) dla operacji. Wartość domyślna 2015-12-16. Jeśli masz kontenery z [dużymi kluczami partycji](large-partition-keys.md) i wymagają REST API w wersji 2018-12-31:
        - Wpisz w **wersji 2018-12-31** dla REST API
        - W menu **Start** wpisz "regedit", aby znaleźć i otworzyć aplikację **Edytor rejestru.**
        - W Edytorze rejestru przejdź do ścieżki: **Komputer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC. INI (INI)**
        - Utwórz nowy podklucz o takiej samej nazwie jak nazwa DSN, np.
        - Przejdź do podklucza "Contoso Account ODBC DSN".
        - Kliknij prawym przyciskiem myszy, aby dodać nową wartość **string:**
            - Nazwa wartości: **IgnoreSessionToken**
            - Dane wartości: **1**
            ![Ustawienia Edytora rejestru](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Spójność kwerend:** Wybierz [poziom spójności](consistency-levels.md) dla operacji. Wartość domyślna to Sesja.
    - **Liczba ponownych prób:** Wprowadź liczbę ponownych prób wykonania operacji, jeśli początkowe żądanie nie zostanie ukończone z powodu ograniczenia szybkości serwisu.
    - **Plik schematu**: Masz tu wiele opcji.
        - Domyślnie pozostawiając ten wpis jako pusty (pusty), sterownik skanuje pierwszą stronę danych dla wszystkich kontenerów, aby określić schemat każdego kontenera. Jest to znane jako mapowanie kontenerów. Bez zdefiniowanego pliku schematu sterownik musi wykonać skanowanie dla każdej sesji sterownika i może spowodować wyższy czas uruchamiania aplikacji przy użyciu nazwy DSN. Zaleca się, aby zawsze skojarzyć plik schematu dla DSN.
        - Jeśli masz już plik schematu (prawdopodobnie utworzony za pomocą Edytora schematów), możesz kliknąć przycisk **Przeglądaj**, przejdź do pliku, kliknij przycisk **Zapisz**, a następnie kliknij przycisk **OK**.
        - Jeśli chcesz utworzyć nowy schemat, kliknij przycisk **OK**, a następnie kliknij pozycję **Edytor schematów** w oknie głównym. Następnie przejdź do informacji edytora schematów. Po utworzeniu nowego pliku schematu należy pamiętać o powrocie do okna **Opcje zaawansowane,** aby dołączyć nowo utworzony plik schematu.

1. Po zakończeniu i zamknięciu okna **ustawienia sterownika DSN sterownika usługi Azure Cosmos DB ODBC** nowy identyfikator DSN użytkownika jest dodawany do karty Dsn użytkownika.

    ![Nowa usługa Azure Cosmos DB ODBC DSN na karcie DsN użytkownika](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Krok 3: Tworzenie definicji schematu przy użyciu metody mapowania kontenera

Istnieją dwa typy metod próbkowania, których można użyć: **mapowanie kontenerów** lub **ograniczniki tabeli.** Sesja próbkowania może korzystać z obu metod próbkowania, ale każdy kontener może używać tylko określonej metody próbkowania. Poniższe kroki tworzą schemat danych w jednym lub kilku kontenerach przy użyciu metody mapowania kontenerów. Ta metoda próbkowania pobiera dane na stronie kontenera w celu określenia struktury danych. Transponuje kontener do tabeli po stronie ODBC. Ta metoda pobierania próbek jest wydajna i szybka, gdy dane w kontenerze są jednorodne. Jeśli kontener zawiera heterogeniczny typ danych, zaleca się użycie [metody mapowania ograniczników tabel,](#table-mapping) ponieważ zapewnia bardziej niezawodną metodę próbkowania w celu określenia struktur danych w kontenerze. 

1. Po wykonaniu kroków 1-4 w [połącz z bazą danych usługi Azure Cosmos](#connect)kliknij pozycję **Edytor schematów** w oknie **Instalatora dsn sterownika usługi Azure Cosmos DB ODBC.**

    ![Przycisk Edytor schematów w oknie Instalatora dsn sterownika usługi Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver-schema-editor.png)
1. W oknie **Edytor schematów** kliknij pozycję **Utwórz nowy**.
    Okno **Generowanie schematu** wyświetla wszystkie kontenery na koncie usługi Azure Cosmos DB. 

1. Zaznacz jeden lub więcej kontenerów do próbkowania, a następnie kliknij **przycisk Próbka**. 

1. Na karcie **Widok projektu** reprezentowana jest baza danych, schemat i tabela. W widoku tabeli skanowanie wyświetla zestaw właściwości skojarzonych z nazwami kolumn (nazwa SQL, nazwa źródła itp.).
    Dla każdej kolumny można zmodyfikować kolumnę nazwę SQL, typ SQL, długość SQL (jeśli dotyczy), Skala (jeśli dotyczy), Precyzja (jeśli dotyczy) i Nullable.
    - Jeśli chcesz wykluczyć tę kolumnę z wyników kwerendy, można ustawić **wartość Wartość Prawda** **Ukryj** kolumnę. Kolumny oznaczone Ukryj kolumnę = wartość true nie są zwracane do zaznaczenia i projekcji, chociaż nadal są częścią schematu. Na przykład można ukryć wszystkie usługi Azure Cosmos DB wymagane właściwości, począwszy od "_".
    - Kolumna id jest jedynym **polem,** którego nie można ukryć, ponieważ jest używana jako klucz podstawowy w znormalizowanym schemacie. 

1. Po zakończeniu definiowania schematu kliknij pozycję**Zapisz** **plik,** | przejdź do katalogu, aby zapisać schemat, a następnie kliknij przycisk **Zapisz**.

1. Aby użyć tego schematu z siecią DSN, otwórz **okno Instalatora dsn sterownika usługi Azure Cosmos DB ODBC** (za pośrednictwem administratora źródła danych ODBC), kliknij pozycję **Opcje zaawansowane**, a następnie w polu **Plik schematu** przejdź do zapisanego schematu. Zapisanie pliku schematu w istniejącej sieci DSN modyfikuje połączenie DSN z zakresem do danych i struktury zdefiniowanej przez schemat.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Krok 4: Tworzenie definicji schematu przy użyciu metody mapowania ograniczników tabeli

Istnieją dwa typy metod próbkowania, których można użyć: **mapowanie kontenerów** lub **ograniczniki tabeli.** Sesja próbkowania może korzystać z obu metod próbkowania, ale każdy kontener może używać tylko określonej metody próbkowania. 

Poniższe kroki utworzyć schemat danych w jednym lub więcej kontenerów przy użyciu metody mapowania **ograniczników tabeli.** Zaleca się użycie tej metody próbkowania, gdy kontenery zawierają niejednorodny typ danych. Ta metoda służy do zakresu próbkowania do zestawu atrybutów i jego odpowiednich wartości. Na przykład jeśli dokument zawiera właściwość "Typ", można zakres próbkowania do wartości tej właściwości. Wynikiem końcowym próbkowania będzie zestaw tabel dla każdej z wartości dla typu, który został określony. Na przykład Typ = Samochód będzie produkować tabeli samochód, podczas gdy typ = płaszczyzna będzie produkować tabeli Płaszczyzna.

1. Po wykonaniu kroków 1-4 w [połącz z bazą danych usługi Azure Cosmos](#connect)kliknij pozycję **Edytor schematów** w oknie Instalatora dsn sterownika usługi Azure Cosmos DB ODBC.

1. W oknie **Edytor schematów** kliknij pozycję **Utwórz nowy**.
    Okno **Generowanie schematu** wyświetla wszystkie kontenery na koncie usługi Azure Cosmos DB. 

1. Wybierz kontener na karcie **Widok próbki** w kolumnie **Definicja mapowania** kontenera kliknij pozycję **Edytuj**. Następnie w oknie **Definicja mapowania** wybierz metodę **Ograniczniki tabeli.** Następnie wykonaj poniższe czynności:

    a. W polu **Atrybuty** wpisz nazwę właściwości ogranicznika. Jest to właściwość w dokumencie, która ma być zakres próbkowania, na przykład Miasto i naciśnij enter. 

    b. Jeśli próbkowanie ma być ograniczone tylko do określonych wartości atrybutu wprowadzonego powyżej, zaznacz atrybut w polu wyboru, wprowadź wartość w polu **Wartość** (np. Można nadal dodawać wiele wartości dla atrybutów. Po prostu upewnij się, że podczas wprowadzania wartości jest zaznaczony poprawny atrybut.

    Jeśli na przykład uwzględnisz wartość **Atrybuty** miasta i chcesz ograniczyć tabelę do uwzględniania tylko wierszy o wartości miasta Nowy Jork i Dubaj, należy wprowadzić miasto w polu Atrybuty i Nowy Jork, a następnie Dubaj w polu **Wartości.**

1. Kliknij przycisk **OK**. 

1. Po zakończeniu definicji mapowania kontenerów, które chcesz próbkować, w oknie **Edytor schematów** kliknij pozycję **Przykład .**
     Dla każdej kolumny można zmodyfikować kolumnę nazwę SQL, typ SQL, długość SQL (jeśli dotyczy), Skala (jeśli dotyczy), Precyzja (jeśli dotyczy) i Nullable.
    - Jeśli chcesz wykluczyć tę kolumnę z wyników kwerendy, można ustawić **wartość Wartość Prawda** **Ukryj** kolumnę. Kolumny oznaczone Ukryj kolumnę = wartość true nie są zwracane do zaznaczenia i projekcji, chociaż nadal są częścią schematu. Na przykład można ukryć wszystkie właściwości wymagane przez system Usługi `_`Azure Cosmos DB, zaczynając od .
    - Kolumna id jest jedynym **polem,** którego nie można ukryć, ponieważ jest używana jako klucz podstawowy w znormalizowanym schemacie. 

1. Po zakończeniu definiowania schematu kliknij pozycję**Zapisz** **plik,** | przejdź do katalogu, aby zapisać schemat, a następnie kliknij przycisk **Zapisz**.

1. W oknie **Ustawienia systemu DSN sterownika usługi Azure Cosmos DB ODBC** kliknij pozycję **Opcje zaawansowane**. Następnie w polu **Plik schematu** przejdź do zapisanego pliku schematu i kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać nazwę DSN. Spowoduje to zapisanie schematu utworzonego w dsn. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcjonalnie) Konfigurowanie połączenia z serwerem połączonym

Usługę Azure Cosmos DB można wysyłać do usługi SQL Server Management Studio (SSMS), konfigurując połączenie z serwerem połączonym.

1. Utwórz systemowe źródło danych zgodnie z opisem `SDS Name`w kroku [2](#connect), nazwanym na przykład .

1. [Zainstaluj program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i połącz się z serwerem. 

1. W edytorze zapytań SSMS utwórz obiekt `DEMOCOSMOS` serwera połączonego dla źródła danych za pomocą następujących poleceń. Zastąp `DEMOCOSMOS` nazwą połączonego `SDS Name` serwera i nazwą źródła danych systemu.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Aby wyświetlić nową nazwę serwera połączonego, odśwież listę Serwery połączone.

![Serwer połączony w systemie SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Połączona baza danych kwerendy

Aby zbadać połączony plik danych, wprowadź kwerendę SSMS. W tym przykładzie kwerenda wybiera z tabeli w kontenerze o nazwie: `customers`

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Wykonaj zapytanie. Wynik powinien być podobny do tego:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Połączony serwer usługi Cosmos DB nie obsługuje nazewnictwa czteroczęściowego. Zwracany jest błąd podobny do następującego komunikatu:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcjonalnie) Tworzenie widoków
Widoki można definiować i tworzyć jako część procesu próbkowania. Widoki te są równoważne widokom SQL. Są one tylko do odczytu i są zakres wyborów i projekcji usługi Azure Cosmos DB sql kwerendy zdefiniowane. 

Aby utworzyć widok dla danych, w oknie **Edytor schematów** w kolumnie **Wyświetl definicje** kliknij pozycję **Dodaj** w wierszu kontenera, aby je pobrać. 
    ![Tworzenie widoku danych](./media/odbc-driver/odbc-driver-create-view.png)


Następnie w oknie **Definicje widoku** wykonaj następujące czynności:

1. Kliknij **przycisk Nowy**, wprowadź nazwę widoku, na przykład EmployeesfromSeattleView, a następnie kliknij przycisk **OK**.

1. W oknie **Edytuj widok** wprowadź kwerendę usługi Azure Cosmos DB. Musi to być na przykład `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` [kwerenda SQL usługi Azure Cosmos DB](how-to-sql-query.md), a następnie kliknij przycisk **OK**.

    ![Dodawanie kwerendy podczas tworzenia widoku](./media/odbc-driver/odbc-driver-create-view-2.png)


Możesz utworzyć wiele widoków, jak chcesz. Po zakończeniu definiowania widoków można następnie próbkować dane. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Wyświetlanie danych w narzędziach analizy biznesowej, takich jak power bi desktop

Za pomocą nowej sieci DSN można połączyć się z usługą Azure Cosmos DB za pomocą dowolnych narzędzi zgodnych ze standardem ODBC — ten krok pokazuje po prostu, jak połączyć się z programem Power BI Desktop i utworzyć wizualizację usługi Power BI.

1. Otwórz program Power BI Desktop.

1. Kliknij **pozycję Pobierz dane**.

    ![Pobieranie danych w programie Power BI Desktop](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. W oknie **Pobierz dane** kliknij pozycję **Inne** | **odbc** | **connect**.

    ![Wybieranie źródła danych ODBC w usłudze Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. W oknie **Od ODBC** wybierz utworzoną nazwę źródła danych, a następnie kliknij przycisk **OK**. Wpisy **opcji zaawansowanych** można pozostawić puste.

    ![Wybieranie nazwy źródła danych (DSN) w usłudze Get Data usługi Power BI](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. W oknie **Dostęp do źródła danych przy użyciu sterownika ODBC** wybierz pozycję **Domyślne lub Niestandardowe,** a następnie kliknij pozycję **Połącz**. Nie trzeba dołączać **właściwości ciągu połączenia poświadczeń**.

1. W oknie **Nawigator** w lewym okienku rozwiń bazę danych, schemat, a następnie wybierz tabelę. Okienko wyników zawiera dane przy użyciu utworzonego schematu.

    ![Wybieranie tabeli w usłudze Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Aby zwizualizować dane na pulpicie usługi Power BI, zaznacz pole przed nazwą tabeli, a następnie kliknij przycisk **Załaduj**.

1. W programie Power BI Desktop po lewej stronie wybierz kartę Dane ![Karta Dane w programie Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) , aby potwierdzić, że dane zostały zaimportowane.

1. Wizualizacje można teraz tworzyć za pomocą usługi Power ![BI, klikając](./media/odbc-driver/odbc-driver-report-tab.png)kartę Raport na karcie Raport w programie Power BI Desktop , klikając pozycję **Nowa wizualizacja,** a następnie dostosowując kafelek. Aby uzyskać więcej informacji na temat tworzenia wizualizacji w programie Power BI Desktop, zobacz [Typy wizualizacji w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony następujący błąd, upewnij się, że wartości **hosta** i **klucza dostępu** skopiowane do portalu Azure w [kroku 2](#connect) są poprawne, a następnie ponów próbę. Użyj przycisków kopiowania po prawej stronie wartości **hosta** i **klucza dostępu** w witrynie Azure portal, aby skopiować wartości bez błędów.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](introduction.md)
