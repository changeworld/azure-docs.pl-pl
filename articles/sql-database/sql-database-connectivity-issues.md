---
title: Praca z błędami przejściowymi
description: Dowiedz się, jak rozwiązywać problemy, diagnozować i zapobiegać błędowi połączenia SQL lub przejściowemu błędowi w bazie danych SQL Azure.
keywords: połączenie sql,parametry połączenia,problemy z łącznością,błąd przejściowy,błąd połączenia
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: d2b56e259f551f7655936c975a7a864a27a1df79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269083"
---
# <a name="troubleshooting-transient-connection-errors-to-sql-database"></a>Rozwiązywanie problemów z błędami połączenia przejściowego z bazą danych SQL

W tym artykule opisano, jak zapobiegać, rozwiązywać problemy, diagnozować i ograniczać błędy połączenia i przejściowe błędy napotykane przez aplikację kliencką podczas interakcji z usługą Azure SQL Database. Dowiedz się, jak skonfigurować logikę ponawiania, utworzyć parametry połączenia i dostosować inne ustawienia połączenia.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Błędy przejściowe (usterki przejściowe)

Błąd przejściowy, znany również jako błąd przejściowy, ma podstawową przyczynę, która wkrótce rozwiązuje się. Sporadyczne przyczyny błędów przejściowych jest, gdy system platformy Azure szybko przenosi zasoby sprzętowe, aby lepiej równoważenia obciążenia różnych obciążeń. Większość z tych zdarzeń ponownej konfiguracji kończy się w mniej niż 60 sekund. W tym okresie rekonfiguracji może być problemy z łącznością z bazą danych SQL. Aplikacje, które łączą się z bazy danych SQL powinny być zbudowane, aby oczekiwać tych błędów przejściowych. Aby je obsłużyć, zaimplementuj logikę ponawiania próby w kodzie zamiast napawać ich użytkowników jako błędy aplikacji.

Jeśli program kliencki używa ADO.NET, program jest informowany o błąd przejściowy przez rzut **SqlException**. 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Połączenie a polecenie

Spróbuj ponownie nawiązać połączenie SQL lub nawiąż go ponownie, w zależności od następujących czynności:

- **Błąd przejściowy występuje podczas próby połączenia**

Po kilkusekundowym opóźnieniu ponów próbę połączenia.

- **Błąd przejściowy występuje podczas polecenia kwerendy SQL**

Nie należy natychmiast ponowić próbę polecenia. Zamiast tego, po opóźnieniu, świeżo ustanowić połączenie. Następnie ponów próbę wykonania polecenia.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika ponawiania próby dla błędów przejściowych

Programy klienckie, które od czasu do czasu napotykają błąd przejściowy są bardziej niezawodne, gdy zawierają logikę ponawiania. Gdy program komunikuje się z bazą danych SQL za pośrednictwem oprogramowania pośredniczącego innych firm, zapytaj dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania błędów przejściowych.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zasady ponawiania prób

- Jeśli błąd jest przejściowy, ponów próbę otwarcia połączenia.
- Nie należy bezpośrednio ponowić próbę instrukcji SQL, `SELECT` która nie powiodła się z błędem przejściowym. Zamiast tego nawiąż nowe połączenie, `SELECT`a następnie ponów próbę utworzenia pliku .
- Gdy instrukcja SQL `UPDATE` nie powiedzie się z błędem przejściowym, nawiąż nowe połączenie przed ponowieniem próby update. Logika ponawiania musi upewnić się, że cała transakcja bazy danych została zakończona lub że cała transakcja jest przywracana.

### <a name="other-considerations-for-retry"></a>Inne kwestie dotyczące ponowiania prób

- Program wsadowy, który automatycznie uruchamia się po godzinach pracy i kończy się przed ranem, może pozwolić sobie na cierpliwość z długimi odstępami czasu między próbami ponawiania.
- Program interfejsu użytkownika powinien uwzględniać ludzką tendencję do rezygnacji po zbyt długim oczekiwaniu. Rozwiązanie nie może ponawiać próby co kilka sekund, ponieważ ta zasada może zalać system żądaniami.

### <a name="interval-increase-between-retries"></a>Zwiększenie interwału między próbami prób

Zalecamy odczekanie 5 sekund przed pierwszym ponowieniem próby. Ponowna próba po opóźnieniu krótszym niż 5 sekund może przytłaczać usługę w chmurze. Dla każdej kolejnej próby ponowienia opóźnienie powinno rosnąć wykładniczo, maksymalnie do 60 sekund.

Aby zapoznać się z omówieniem okresu blokowania dla klientów korzystających z ADO.NET, zobacz [Buforowanie połączeń programu SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Można również ustawić maksymalną liczbę ponownych prób przed zakończeniem programu.

### <a name="code-samples-with-retry-logic"></a>Przykłady kodu z logiką ponawiania próby

Przykłady kodu z logiką ponawiania próby są dostępne pod adresem:

- [Nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą środowiska ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testowanie logiki ponawiania prób

Aby przetestować logikę ponawiania, należy symulować lub spowodować błąd, który można poprawić, gdy program jest nadal uruchomiony.

#### <a name="test-by-disconnecting-from-the-network"></a>Test przez odłączenie od sieci

Jednym ze sposobów przetestowania logiki ponawiania próby jest odłączenie komputera klienckiego od sieci, gdy program jest uruchomiony. Błąd jest:

- **SqlException.Number** = 11001
- Komunikat: "Nie jest znany taki host"

W ramach pierwszej próby ponowienia próby można ponownie podłączyć komputer kliencki do sieci, a następnie spróbować nawiązać połączenie.

Aby ten test był praktyczny, przed uruchomieniem programu odłącz komputer od sieci. Następnie program rozpoznaje parametr środowiska uruchomieniowego, który powoduje, że program:

- Tymczasowo dodać 11001 do listy błędów do rozważenia jako przejściowe.
- Spróbuj pierwszego połączenia jak zwykle.
- Po przechłonięcia błędu usuń 11001 z listy.
- Wyświetl komunikat informujący użytkownika o podłączeniu komputera do sieci.
- Wstrzymaj dalsze wykonanie przy użyciu **metody Console.ReadLine** lub okna dialogowego z przyciskiem OK. Użytkownik naciśnie klawisz Enter po podłączeniu komputera do sieci.
- Spróbuj ponownie połączyć, oczekując sukcesu.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testowanie przez błędną pisownię nazwy bazy danych podczas nawiązywania połączenia

Program może celowo błędnie wpisać nazwę użytkownika przed pierwszą próbą połączenia. Błąd jest:

- **SqlException.Number** = 18456
- Komunikat: "Nie można zalogować się dla użytkownika 'WRONG_MyUserName'."

W ramach pierwszej próby ponowienia próby program może poprawić błąd pisowni, a następnie spróbować połączyć.

Aby ten test był praktyczny, program rozpoznaje parametr środowiska wykonawczego, który powoduje, że program:

- Tymczasowo dodać 18456 do listy błędów do rozważenia jako przejściowe.
- Celowo dodaj "WRONG_" do nazwy użytkownika.
- Po przeładunek błędu usuń 18456 z listy.
- Usuń "WRONG_" z nazwy użytkownika.
- Spróbuj ponownie połączyć, oczekując sukcesu.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry programu .NET SqlConnection dla ponawiania próby połączenia

Jeśli program kliencki łączy się z bazą danych SQL przy użyciu klasy .NET Framework **System.Data.SqlClient.SqlConnection,** użyj .NET 4.6.1 lub nowszego (lub .NET Core), aby można było użyć funkcji ponawiania połączenia. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [tę stronę internetową](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Podczas tworzenia [ciągu połączenia](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) dla obiektu **SqlConnection** należy koordynować wartości między następującymi parametrami:

- **ConnectRetryCount**&nbsp;&nbsp;: Wartość domyślna to 1. Zakres wynosi od 0 do 255.
- **ConnectRetryInterval**&nbsp;&nbsp;: Wartość domyślna to 10 sekund. Zakres wynosi od 1 do 60.
- **Limit czasu połączenia:**&nbsp;&nbsp;domyślnie jest to 15 sekund. Zakres od 0 do 2147483647.

W szczególności wybrane wartości powinny spełniać następujące wartości równania: Limit czasu połączenia = ConnectRetryCount * ConnectionRetryInterval

Na przykład jeśli liczba jest równa 3 i interwał jest równy 10 sekund, limit czasu tylko 29 sekund nie daje systemowi wystarczająco dużo czasu na jego trzeci i końcowy ponowić próbę połączenia: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Połączenie a polecenie

**ConnectRetryCount** i **ConnectRetryInterval** parametry pozwalają **sqlconnection** obiekt ponowić próbę operacji łączenia bez informowania lub przeszkadza program, takich jak powrót kontroli do programu. Ponownych prób może wystąpić w następujących sytuacjach:

- wywołanie metody mySqlConnection.Open
- wywołanie metody mySqlConnection.Execute

Jest subtelność. Jeśli podczas wykonywania *kwerendy* wystąpi błąd przejściowy, obiekt **SqlConnection** nie ponowi próby operacji łączenia. Z pewnością nie ponowić próbę zapytania. Jednak **SqlConnection** bardzo szybko sprawdza połączenie przed wysłaniem zapytania do wykonania. Jeśli szybkie sprawdzenie wykryje problem z połączeniem, **sqlconnection** ponawia ponowną operację połączenia. Jeśli ponowna próba zakończy się pomyślnie, kwerenda jest wysyłana do wykonania.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Jeśli ConnectRetryCount być połączone z logiką ponawiania aplikacji

Załóżmy, że aplikacja ma niezawodną niestandardową logikę ponawiania. Może ponowić próbę operacji łączenia cztery razy. Jeśli dodasz **ConnectRetryInterval** i **ConnectRetryCount** = 3 do ciągu połączenia, zwiększysz liczbę ponownych prób do 4 * 3 = 12 ponownych prób. Możesz nie zamierzać tak dużej liczby ponownych prób.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Połączenia z bazą danych SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Połączenie: parametry połączenia

Parametry połączenia, które jest niezbędne do łączenia się z bazą danych SQL jest nieco różni się od ciągu używanego do łączenia się z programem SQL Server. Ciąg połączenia bazy danych można skopiować z [witryny Azure Portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Połączenie: adres IP

Należy skonfigurować serwer bazy danych SQL, aby akceptował komunikację z adresu IP komputera, na którym znajduje się program kliencki. Aby skonfigurować tę konfigurację, edytuj ustawienia zapory w [witrynie Azure portal](https://portal.azure.com/).

Jeśli zapomnisz skonfigurować adres IP, program ulegnie awarii dzięki poręcznemu komunikatowi o błędzie, który określa niezbędny adres IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Połączenie: Porty

Zazwyczaj należy upewnić się, że tylko port 1433 jest otwarty dla komunikacji wychodzącej na komputerze, na którym znajduje się program kliencki.

Na przykład, gdy program kliencki jest obsługiwany na komputerze z systemem Windows, można użyć Zapory systemu Windows na hoście, aby otworzyć port 1433.

1. Otwórz Panel sterowania.
2. Wybierz **wszystkie elementy** > Panelu sterowania Ustawienia zaawansowane**ustawienia** > **zapory** > systemu Windows Akcje**reguł** > **wychodzących** > **Nowa reguła**.

Jeśli program kliencki jest obsługiwany na maszynie wirtualnej platformy Azure (VM), odczyt [portów po 1433 dla ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Aby uzyskać podstawowe informacje na temat konfiguracji portów i adresów IP, zobacz [Zapora usługi Azure SQL Database](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Połączenie: ADO.NET 4.6.2 lub nowszym

Jeśli program używa ADO.NET klas, takich jak **System.Data.SqlClient.SqlConnection,** aby połączyć się z bazą danych SQL, zaleca się użycie programu .NET Framework w wersji 4.6.2 lub nowszej.

#### <a name="starting-with-adonet-462"></a>Począwszy od ADO.NET 4.6.2

- Otwarte połączenie próby ponownego ponowienia natychmiast dla baz danych SQL platformy Azure, co zwiększa wydajność aplikacji z obsługą chmury.

#### <a name="starting-with-adonet-461"></a>Począwszy od ADO.NET 4.6.1

- W przypadku bazy danych SQL zwiększa się niezawodność podczas otwierania połączenia przy użyciu metody **SqlConnection.Open.** **Open** Metoda zawiera teraz mechanizmy ponawiania najlepszych starań w odpowiedzi na błędy przejściowe dla niektórych błędów w okresie limitu czasu połączenia.
- Buforowanie połączeń jest obsługiwane, co obejmuje skuteczną weryfikację, czy obiekt połączenia, który daje program, działa.

W przypadku korzystania z obiektu połączenia z puli połączeń zaleca się tymczasowe zamknięcie połączenia, gdy nie jest ono natychmiast używane. Ponowne otwarcie połączenia nie jest kosztowne, ale jest tworzenie nowego połączenia.

Jeśli używasz ADO.NET 4.0 lub wcześniejszych, zalecamy uaktualnienie do najnowszej ADO.NET. Od sierpnia 2018 r. można [pobrać ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostyka

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostyka: Sprawdź, czy narzędzia mogą się łączyć

Jeśli program nie może połączyć się z bazą danych SQL, jedną z opcji diagnostycznych jest próba nawiązania połączenia z programem narzędziowym. W idealnym przypadku narzędzie łączy się przy użyciu tej samej biblioteki, z której korzysta program.

Na dowolnym komputerze z systemem Windows można wypróbować następujące narzędzia:

- SQL Server Management Studio (ssms.exe), który łączy się przy użyciu ADO.NET
- `sqlcmd.exe`, który łączy się za pomocą [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Po podłączeniu programu sprawdź, czy krótkie zapytanie SQL SELECT działa.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostyka: Sprawdź otwarte porty

Jeśli podejrzewasz, że próby połączenia nie powiodą się z powodu problemów z portem, możesz uruchomić narzędzie na komputerze, które raportuje konfiguracje portów.

W systemie Linux pomocne mogą być następujące narzędzia:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Zmień wartość przykładową na twój adres IP.

W systemie Windows narzędzie [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) może być pomocne. Oto przykładowe wykonanie, które zbadało sytuację portu na serwerze bazy danych SQL i zostało uruchomione na komputerze przenośnym:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostyka: rejestrowanie błędów

Przerywany problem jest czasami najlepiej rozpoznać poprzez wykrycie ogólnego wzorca w ciągu dni lub tygodni.

Klient może pomóc w diagnostyce, rejestrując wszystkie błędy, które napotka. Można skorelować wpisy dziennika z danymi błędów, które sql database rejestruje się wewnętrznie.

Biblioteka enterprise library 6 (EntLib60) oferuje klasy zarządzane .NET, aby pomóc w rejestrowaniu. Aby uzyskać więcej informacji, zobacz [5 - Tak proste, jak wypadnięcie z dziennika: Użyj bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostyka: Sprawdź dzienniki systemowe pod kątem błędów

Oto niektóre instrukcje Transact-SQL SELECT, które rejestrują błędy kwerend i inne informacje.

| Zapytanie dziennika | Opis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Widok [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) oferuje informacje o poszczególnych zdarzeniach, które obejmują niektóre, które mogą powodować błędy przejściowe lub awarie łączności.<br/><br/>W idealnym przypadku można skorelować **wartości start_time** lub **end_time** z informacjami o tym, kiedy program kliencki napotkał problemy.<br/><br/>Aby uruchomić tę kwerendę, należy połączyć się z *główną* bazą danych. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Widok [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) oferuje zagregowane liczby typów zdarzeń dla dodatkowej diagnostyki.<br/><br/>Aby uruchomić tę kwerendę, należy połączyć się z *główną* bazą danych. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostyka: wyszukiwanie zdarzeń problemowych w dzienniku bazy danych SQL

Można wyszukiwać wpisy dotyczące zdarzeń problemowych w dzienniku bazy danych SQL. Wypróbuj następującą instrukcję Transact-SQL SELECT w *głównej* bazie danych:

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Kilka zwróconych wierszy z sys.fn_xe_telemetry_blob_target_read_file

Poniższy przykład pokazuje, jak może wyglądać zwrócony wiersz. Wartości null wyświetlane często nie są null w innych wierszach.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteka przedsiębiorstwa 6

Biblioteka enterprise library 6 (EntLib60) to struktura klas platformy .NET, która ułatwia implementowanie niezawodnych klientów usług w chmurze, z których jedną jest usługa bazy danych SQL. Aby zlokalizować tematy poświęcone każdemu obszarowi, w którym EntLib60 może pomóc, zobacz [Biblioteka Enterprise 6 - kwiecień 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika ponawiania obsługi błędów przejściowych jest jednym z obszarów, w którym EntLib60 może pomóc. Aby uzyskać więcej informacji, zobacz [4 - Wytrwałość, tajemnica wszystkich triumfów: Użyj przejściowego bloku aplikacji obsługi usterek](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Kod źródłowy EntLib60 jest dostępny do publicznego pobrania z [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkID=290898). Firma Microsoft nie planuje wprowadzania dalszych aktualizacji funkcji lub aktualizacji konserwacji entlib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasy dla błędów przejściowych i ponów próbę

Następujące EntLib60 klasy są szczególnie przydatne dla logiki ponawiania. Wszystkie te klasy znajdują się w obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**lub w obszarze nazw .

W obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **Ponawianie próbyPolicy,** klasa
  - **Metoda ExecuteAction**
- **Klasa ExponentialBackoff**
- **SqlDatabaseTransientErrorDetectionStylid,** klasa
- **ReliableSqlConnection,** klasa
  - **Metoda ExecuteCommand**

W obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionKlasa orientacji**
- **NeverTransientErrorDetectionKlasa orientacji**

Oto kilka linków do informacji o EntLib60:

- Darmowe pobieranie książek: [Developer's Guide to Microsoft Enterprise Library, wydanie 2.](https://www.microsoft.com/download/details.aspx?id=41145)
- Najlepsze rozwiązania: [Ponów próbę ogólnych wskazówek](../best-practices-retry-general.md) zawiera doskonałe szczegółowe omówienie logiki ponawiania.
- Pobieranie NuGet: [Biblioteka przedsiębiorstwa — przejściowa obsługa błędów Blok aplikacji 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Blok rejestrowania

- Blok rejestrowania jest wysoce elastycznym i konfigurowalnym rozwiązaniem, którego można użyć do:
  - Tworzenie i przechowywanie komunikatów dziennika w wielu różnych lokalizacjach.
  - Kategoryzuj i filtruj wiadomości.
  - Zbieranie informacji kontekstowych, które są przydatne do debugowania i śledzenia, a także do inspekcji i ogólnych wymagań rejestrowania.
- Blok rejestrowania abstrakcji funkcji rejestrowania z miejsca docelowego dziennika, tak aby kod aplikacji jest spójny, niezależnie od lokalizacji i typu magazynu rejestrowania docelowego.

Aby uzyskać więcej informacji, zobacz [5 - Tak proste, jak wypadnięcie z dziennika: Użyj bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient kod źródłowy metody

Następnie z **SqlDatabaseTransientErrorDetectionStrategy** klasy, jest kod źródłowy języka C# dla **IsTransient** metody. Kod źródłowy precyzuje, które błędy zostały uznane za przejściowe i godne ponowienia próby, od kwietnia 2013 r.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md)
- [Buforowanie połączeń programu SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Ponowienie próby* jest Apache 2.0 licencjonowanych biblioteki prób ogólnego przeznaczenia, napisany w Języku Python,](https://pypi.python.org/pypi/retrying) aby uprościć zadanie dodawania ponowić zachowanie prawie wszystko.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
