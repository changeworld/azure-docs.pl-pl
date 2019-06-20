---
title: Praca z błędami przejściowymi — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywanie problemów, diagnozowanie i zapobieganie błąd połączenia SQL lub błędu przejściowego w usłudze Azure SQL Database.
keywords: połączenia z serwerem SQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 06/14/2019
ms.openlocfilehash: adbe8dfd41725c11516f820656b0476ed1aa8881
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144033"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>Praca z problemów z połączeniami bazy danych SQL i błędom przejściowym

W tym artykule opisano sposób zapobiec, rozwiązywanie problemów, diagnozowanie i rozwiązać błędy połączeń i błędów przejściowych, które Twoja aplikacja kliencka napotka przy współpracuje z usługą Azure SQL Database. Dowiedz się, jak konfigurować logikę ponawiania próby, Utwórz ciąg połączenia i Dostosuj inne ustawienia połączenia.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Przejściowych błędów (błędy przejściowe)

Błąd przejściowy, znany także jako błędów przejściowych, ma podstawowych przyczyn, rozpoznającej wkrótce się. Przyczyny okazjonalne błędy przejściowe jest, gdy Azure system szybko przenosi zasoby sprzętowe do lepszego równoważenia obciążenia różnych obciążeń. Większość z tych zdarzeń zmiany konfiguracji zakończyć w mniej niż 60 sekund. W tym okresie czasu ponownej konfiguracji może mieć problemy z łącznością z bazą danych SQL. Można oczekiwać, te błędy przejściowe, powinny zostać skompilowane aplikacji łączących się z bazą danych SQL. Aby obsługiwać je, implementować logikę ponawiania próby w kodzie zamiast udostępniając je do użytkowników jako błędy aplikacji.

Jeśli program kliencki korzysta z programu ADO.NET, program jest powiadamiany o błąd przejściowy, throw z **sqlexception —** . Porównaj **numer** właściwości z listą błędów przejściowych, które znajdują się w górnej części tego artykułu [kody błędów SQL dla aplikacji klienckich bazy danych SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Połączenie, a polecenie

Ponów próbę połączenia SQL lub ponownie ustanów je w zależności od następujących czynności:

- **Przejściowy błąd wystąpi podczas próbie połączenia**

Po chwili kilka sekund próbę połączenia.

- **Przejściowy błąd występuje w ciągu polecenia zapytania SQL**

Nie od razu spróbuj ponownie wykonać polecenie. Zamiast tego po chwili świeżo połączenie jest nawiązywane. Następnie ponów próbę wykonania polecenia.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika ponawiania próby dla błędów przejściowych

Programy klienckie, które czasami wystąpi błąd przejściowy są bardziej niezawodne, gdy zawierają logikę ponawiania próby. Gdy program komunikuje się z usługą SQL Database za pomocą oprogramowania pośredniczącego innych firm, należy poprosić dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponowień w przypadku błędów przejściowych.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zasady ponawiania prób

- Jeśli ten błąd jest przejściowy, ponów próbę, aby otworzyć połączenia.
- Należy wykonywać ponowień bezpośrednio SQL `SELECT` instrukcję, która nie powiodła się z powodu błędu przejściowego. Zamiast tego należy ustanowić połączenie świeże, a następnie ponów `SELECT`.
- Gdy SQL `UPDATE` instrukcja kończy się niepowodzeniem z powodu błędu przejściowego, ustanów połączenie świeże przed ponowieniem próby aktualizacji. Logikę ponawiania należy się upewnić, że transakcja całej bazy danych zostało zakończone lub że cała transakcja zostanie wycofana.

### <a name="other-considerations-for-retry"></a>Inne uwagi dotyczące ponawiania prób

- Program wsadowy, który automatycznie rozpoczyna się po godzinach pracy i zakończy się przed rano akceptowalny jest to bardzo pacjentów przy użyciu długo przedziałów czasu między jego ponownych prób.
- Program interfejsu użytkownika powinny uwzględniać ludzi tendencja oddawać po zbyt długim czasie oczekiwania. Rozwiązanie nie musi ponowień co kilka sekund, ponieważ te zasady można zalać system żądaniami.

### <a name="interval-increase-between-retries"></a>Zwiększ interwał między ponownymi próbami

Firma Microsoft zaleca, oczekiwania na 5 sekund przed swoje pierwsze ponowienie. Ponawianie próby po opóźnieniu mniej niż 5 sekund ryzyka przeciąża usługę w chmurze. Na każdym kolejnym ponowieniem próby powinien być zwiększany opóźnienie wykładniczo, maksymalnie 60 sekund.

Omówienie blokowania okres dla klientów korzystających z programu ADO.NET, zobacz [połączenia programu SQL Server (ADO.NET) buforowanie](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Można również ustawić maksymalną liczbę ponownych prób, zanim program kończy się samodzielnie.

### <a name="code-samples-with-retry-logic"></a>Przykłady kodu za pomocą logikę ponawiania próby

Przykłady kodu za pomocą logiki ponawiania prób są dostępne pod adresem:

- [Nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą środowiska ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testowanie logikę ponawiania prób

Aby przetestować logikę ponawiania, możesz zasymulować lub powodują wystąpienie błędu, który można rozwiązać, gdy program jest nadal uruchomiona.

#### <a name="test-by-disconnecting-from-the-network"></a>Testowanie przez odłączenie od sieci

Jednym ze sposobów, które można przetestować logikę ponawiania jest odłączyć od sieci na komputerze klienckim, gdy program jest uruchomiony. Błąd to:

- **SqlException.Number** = 11001
- Komunikat: "Nieznany host nie"

W ramach pierwszej ponowienia próby można ponownie połączyć komputer kliencki do sieci, a następnie spróbuj połączyć.

Aby ten test praktyczny, odłącz komputer od sieci, przed rozpoczęciem korzystania z programu. Następnie program rozpozna parametrów środowiska uruchomieniowego, który powoduje, że program:

- Dodaj tymczasowo 11001 do swojej listy błędów, można rozważyć jako przejściowe.
- Próba jej pierwszego połączenia w zwykły sposób.
- Po zostaje przechwycony błąd, należy usunąć 11001 z listy.
- Wyświetlenie komunikatu, informujący użytkownika, aby podłączyć komputer do sieci.
- Zatrzymać dalsze wykonywanie przy użyciu **Console.ReadLine** metody lub okno dialogowe z przyciskiem OK. Użytkownik naciśnie klawisz Enter, po której komputer jest podłączony do sieci.
- Próbować ponownie nawiązać połączenie, oczekiwano sukces.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Przetestuj, nazwę bazy danych. błędy podczas nawiązywania połączenia

Program może celowo błędnego wpisania nazwy użytkownika przed pierwszą próbę połączenia. Błąd to:

- **SqlException.Number** = 18456
- Komunikat: "Nie można zalogować użytkownika"WRONG_MyUserName"."

Jako część pierwszej ponowienia próby program może poprawić podana przez Ciebie, a następnie spróbuj połączyć.

Aby wprowadzić ten test jest praktyczne, program rozpoznaje parametrów środowiska uruchomieniowego, który powoduje, że program:

- Dodaj tymczasowo 18456 do swojej listy błędów, można rozważyć jako przejściowe.
- Celowo Dodaj "WRONG_" do nazwy użytkownika.
- Po zostaje przechwycony błąd, należy usunąć 18456 z listy.
- Usuń "WRONG_" z nazwą użytkownika.
- Próbować ponownie nawiązać połączenie, oczekiwano sukces.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry .NET SqlConnection ponawiania prób połączenia

Jeśli program kliencki łączy się z bazą danych SQL za pomocą klasy .NET Framework **System.Data.SqlClient.SqlConnection**, przy użyciu programu .NET 4.6.1 lub nowszej (lub .NET Core), aby mogli używać swoich funkcji ponawiania prób połączenia. Aby uzyskać więcej informacji na temat funkcji, zobacz [tej strony sieci Web](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Podczas kompilowania [parametry połączenia](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) dla Twojego **SqlConnection** obiektów, koordynować wartości między następującymi parametrami:

- **ConnectRetryCount**:&nbsp;&nbsp;wartość domyślna to 1. Zakres to od 0 do 255.
- **ConnectRetryInterval**:&nbsp;&nbsp;domyślna to 10 sekund. Zakres to od 1 do 60.
- **Limit czasu połączenia**:&nbsp;&nbsp;domyślna wynosi 15 sekund. Zakres to od 0 do 2147483647.

W szczególności wybranej wartości upewnić następujące true równości: Limit czasu połączenia = ConnectRetryCount * ConnectionRetryInterval

Na przykład jeśli liczba jest równa 3 jest równy 10 sekund, limit czasu równy tylko kilka sekund 29 nie dać systemowi wystarczającą ilość czasu jego trzeciej i ostatniej ponów próbę, aby połączyć: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Połączenie, a polecenie

**ConnectRetryCount** i **ConnectRetryInterval** umożliwiają parametry usługi **SqlConnection** obiektu ponów próbę wykonania operacji nawiązywania połączenia bez informuje, czy bothering usługi Program, takich jak zwracanie formantu do programu. Ponowne próby może wystąpić w następujących sytuacjach:

- Wywołanie metody mySqlConnection.Open
- Wywołanie metody mySqlConnection.Execute

Brak subtlety. Jeśli wystąpi błąd przejściowy podczas Twojej *zapytania* jest wykonywana, Twoje **SqlConnection** obiektu nie ponów próbę wykonania operacji nawiązywania połączenia. Go na pewno nie ponów próbę wykonania zapytania. Jednak **SqlConnection** bardzo szybko sprawdza połączenie przed wysłaniem kwerendy do wykonania. Jeśli szybkie sprawdzenie wykryje problem z połączeniem **SqlConnection** ponownych prób operacji nawiązywania połączenia. Jeśli ponowienie próby zakończy się pomyślnie, zapytanie jest wysyłane do wykonania.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount można łączyć za pomocą aplikacji Logika ponawiania

Załóżmy, że Twoja aplikacja ma logiki niezawodne niestandardowych ponowień. Może on ponów operację connect cztery razy. Jeśli dodasz **ConnectRetryInterval** i **ConnectRetryCount** = 3 do parametrów połączenia, spowoduje zwiększenie liczby ponownych prób do 4 * 3 = 12 ponownych prób. Użytkownik może nie planuje dużą liczbą ponownych prób.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Połączenia z usługą SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Połączenie: Parametry połączenia

Parametry połączenia, które są niezbędne do łączenia z bazą danych SQL jest nieco inne niż ciąg używany do łączenia się z serwerem SQL. Możesz skopiować parametry połączenia dla bazy danych z [witryny Azure portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Połączenie: Adres IP

Należy skonfigurować serwer bazy danych SQL, aby akceptował komunikację z adresu IP komputera, który hostuje program kliencki. Aby skonfigurować tę konfigurację, edytować ustawienia zapory za pośrednictwem [witryny Azure portal](https://portal.azure.com/).

Jeśli zapomnisz skonfigurować adres IP, program kończy się niepowodzeniem przydatną komunikat, stwierdzający niezbędne adresu IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Połączenie: Porty

Zazwyczaj należy się upewnić, że tylko port 1433 jest otwarty dla komunikacji wychodzącej, na komputerze hostującym program kliencki.

Na przykład kiedy program kliencki znajduje się na komputerze Windows, jednak używasz zapory Windows na hoście otworzyć port 1433.

1. Otwórz Panel sterowania.
2. Wybierz **kontrolować wszystkie elementy panelu** > **zapory Windows** > **Zaawansowane ustawienia** > **reguł dla ruchu wychodzącego**   >  **Akcje** > **nową regułę**.

Jeśli program kliencki jest hostowany na maszynie wirtualnej (VM) platformy Azure, zapoznaj się z [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Aby uzyskać informacje dotyczące konfiguracji portów i adresów IP, zobacz [zapory usługi Azure SQL Database](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Połączenie: ADO.NET 4.6.2 lub nowszy

Jeśli program używa klas ADO.NET, takich jak **System.Data.SqlClient.SqlConnection** do łączenia z bazą danych SQL, zalecane jest użycie .NET Framework w wersji 4.6.2 lub nowszy.

#### <a name="starting-with-adonet-462"></a>Począwszy od programu ADO.NET 4.6.2

- Próba otwarte połączenia należy natychmiast ponowić baz danych Azure SQL, a więc poprawa wydajności aplikacji z obsługą chmury.

#### <a name="starting-with-adonet-461"></a>Począwszy od programu ADO.NET 4.6.1

- Usługi SQL Database, Zwiększona niezawodność Otwórz połączenie przy użyciu **SqlConnection.Open** metody. **Otwórz** metoda teraz obejmuje mechanizmy ponawiania optymalnych w odpowiedzi na błędy przejściowe pewne błędy przed upływem limitu czasu połączenia.
- Buforowanie połączeń jest obsługiwane, który zawiera wydajne sprawdzenie, czy obiekt połączenia, które zapewnia program działa prawidłowo.

Podczas korzystania z obiektu połączenia z puli połączeń, zaleca się, że program tymczasowo zamyka połączenie, gdy nie jest od razu użycia. Nie jest kosztowna ponownie otworzyć połączenie, ale można utworzyć nowe połączenie.

Jeśli używasz ADO.NET 4.0 lub wcześniej, zaleca się uaktualnienie do najnowszej ADO.NET. Począwszy od sierpnia 2018 r. można [Pobierz ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostyka

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostyka: Sprawdź, czy narzędzia mogą się łączyć.

Jeśli program nie uda się nawiązać połączenia z bazą danych SQL, jedną z opcji diagnostyki jest próby nawiązania połączenia za pomocą programu narzędziowego. W idealnym przypadku narzędzie nawiązuje połączenie za pomocą tej samej bibliotece, używany przez program.

Na dowolnym komputerze Windows możesz spróbować tych narzędzi:

- SQL Server Management Studio (ssms.exe), który jest połączony za pomocą pakietu ADO.NET
- `sqlcmd.exe`, który łączy usługę przy użyciu [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Po połączeniu programu należy sprawdzić, czy działa krótki zapytania SQL ZAZNACZYĆ.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostyka: Sprawdź otwarte porty

Jeśli podejrzewasz, że próby nawiązania połączenia zakończy się niepowodzeniem z powodu problemów z portów, należy uruchomić narzędzie na komputerze, który raport dotyczący konfiguracji portów.

W systemie Linux następujące narzędzia mogą być pomocne:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Zmień wartość przykład Twój adres IP.

W Windows [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) narzędzie mogą być pomocne. Oto przykład wykonywania, wyszukiwane sytuacji portu na serwerze bazy danych SQL Database i uruchomioną na komputerze przenośnym:

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

### <a name="diagnostics-log-your-errors"></a>Diagnostyka: Błędy logowania

Sporadyczny problem jest czasami najlepiej zdiagnozowanie wykrywanie ogólnego wzorca za pośrednictwem dni lub tygodni.

Klient może pomóc w diagnozy w przez funkcję rejestrowania wszystkich błędów, które napotka. Dzięki temu można skorelować z wpisy dziennika z błąd danych, która rejestruje się wewnętrznie bazy danych SQL.

Enterprise 6 biblioteki (EntLib60) oferuje klasy zarządzanego na platformie .NET, aby pomóc za pomocą funkcji rejestrowania. Aby uzyskać więcej informacji, zobacz [5 - bardzo proste — wystarczy objętych wyłączanie dziennika: Użyj bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostyka: Sprawdź dzienniki błędów systemu

Poniżej przedstawiono niektóre instrukcje języka Transact-SQL ZAZNACZYĆ, które tworzą zapytania dotyczące dzienniki błędów i innych informacji.

| Zapytanie dziennika | Opis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) widok zawiera informacje na temat poszczególnych zdarzeń, w tym z niektórych funkcji, które mogą powodować przejściowe błędy lub awarie połączenia.<br/><br/>W idealnym przypadku można skorelować **godzina_rozpoczęcia** lub **end_time** wartościami dowiedzieć się, gdy program kliencki wystąpienia problemów.<br/><br/>Należy połączyć *wzorca* bazy danych w celu wykonania tego zapytania. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) widoku oferuje zagregowane liczby typów zdarzeń dodatkowe diagnostyki.<br/><br/>Należy połączyć *wzorca* bazy danych w celu wykonania tego zapytania. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostyka: Wyszukaj zdarzenia problem w dzienniku bazy danych SQL

Można wyszukiwać wpisy dotyczące problemu zdarzenia w dzienniku bazy danych SQL. Spróbuj wykonać następującą instrukcję języka Transact-SQL ZAZNACZYĆ *wzorca* bazy danych:

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

#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Kilka wierszy zwrócony z sys.fn_xe_telemetry_blob_target_read_file

Poniższy przykład pokazuje, jak może wyglądać zwracany wiersz. Wartości null, pokazano często nie są wartości null w innych wierszy.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteka Enterprise 6

Enterprise 6 biblioteki (EntLib60) to struktura klas platformy .NET, która ułatwia zaimplementowanie klientów niezawodnych usług w chmurze, jednym z nich jest usługa SQL Database. Aby znaleźć tematy przeznaczonych dla każdego obszaru, w którym mogą pomóc EntLib60, zobacz [biblioteki Enterprise 6 — kwiecień 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika ponawiania do obsługi błędów przejściowych jest jeden obszar, w którym EntLib60 może pomóc. Aby uzyskać więcej informacji, zobacz [4 - Perseverance, klucz tajny sukcesy wszystkich: Użyj bloku aplikacji obsługi błędów przejściowych](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Kod źródłowy EntLib60 jest dostępny do pobrania publicznego [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkID=290898). Firma Microsoft nie ma żadnych planów, aby wprowadzić więcej aktualizacji funkcji lub aktualizacji konserwacji EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Klasy EntLib60 dla błędów przejściowych, a następnie spróbuj ponownie

Następujące klasy EntLib60 są szczególnie przydatne w przypadku logikę ponawiania próby. Te klasy znajdują się w przestrzeni nazw lub **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

W obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **RetryPolicy** klasy
  - **ExecuteAction** — metoda
- **ExponentialBackoff** klasy
- **SqlDatabaseTransientErrorDetectionStrategy** class
- **ReliableSqlConnection** klasy
  - **Parametr ExecuteCommand** — metoda

W obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** class
- **NeverTransientErrorDetectionStrategy** class

Poniżej przedstawiono niektóre łącza do informacji na temat EntLib60:

- Pobierania bezpłatnej książki: [Przewodnik dewelopera Microsoft Enterprise Library, wydanie 2](https://www.microsoft.com/download/details.aspx?id=41145).
- Najlepsze rozwiązania: [Ogólne wskazówki dotyczące ponawiania prób](../best-practices-retry-general.md) ma doskonałą szczegółowe omówienie logikę ponawiania próby.
- Pobieranie NuGet: [Biblioteka przedsiębiorstwa — blok aplikacji do obsługi błędów przejściowych 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Zablokuj rejestrowanie

- Blok rejestrowania jest wysoce elastyczny i można je konfigurować rozwiązanie, które umożliwia:
  - Utwórz i Zapisz komunikaty w dzienniku w wielu różnych lokalizacjach.
  - Kategoryzować i filtrować wiadomości.
  - Zbieraj informacje kontekstowe rejestrowania przydatne na potrzeby debugowania i śledzenia, a także do przeprowadzania inspekcji i ogólnych wymagań.
- Zablokuj rejestrowanie przenosi funkcji rejestrowania miejsce docelowe dziennika, aby kod aplikacji jest zgodne, niezależnie od tego, lokalizację i typ magazynu docelowego rejestrowania.

Aby uzyskać więcej informacji, zobacz [5 - bardzo proste — wystarczy objętych wyłączanie dziennika: Użyj bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Kod źródłowy metody EntLib60, IsTransient

Następnie z **SqlDatabaseTransientErrorDetectionStrategy** klasy, to kod źródłowy języka C# dla **IsTransient** metody. Kod źródłowy wyjaśnia, które błędy były traktowane jako błędy przejściowe i Alberta ponawiania prób od kwietnia 2013.

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

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat rozwiązywania problemów innych typowych problemów z połączeniem SQL Database, zobacz [rozwiązywać problemy z połączeniem do usługi Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
- [Biblioteki połączeń dla usługi SQL Database i programu SQL Server](sql-database-libraries.md)
- [Buforowanie (ADO.NET) połączenia z programem SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Ponawianie próby* jest ogólnego przeznaczenia, ponawianie próby biblioteki, napisany w języku Python, Apache w wersji 2.0, licencjonowane](https://pypi.python.org/pypi/retrying) upraszcza zadanie dodawania sposób ponawiania do wszystko, co.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
