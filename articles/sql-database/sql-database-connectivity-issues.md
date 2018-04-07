---
title: Napraw błąd połączenia SQL, błąd przejściowy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywanie problemów, diagnozowanie i zapobieganie błąd połączenia SQL lub Błąd przejściowy w bazie danych SQL Azure.
keywords: Połączenie SQL, ciąg połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: daleche
ms.openlocfilehash: 27e565845ec538a1982ec0742d7ca723fa9c348c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Rozwiązywanie problemów, diagnozowanie i unikanie błędów połączenia SQL oraz błędów przejściowych w usłudze SQL Database
W tym artykule opisano sposób zapobiec, rozwiązywanie problemów z zdiagnozować i ograniczyć błędów połączenia i błędom przejściowym, których aplikacja kliencka napotka przy interakcji z bazy danych SQL Azure. Dowiedz się, jak skonfigurować logiki ponawiania próby utworzenia parametrów połączenia i inne ustawienia połączenia.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Przejściowe błędy (błędów przejściowych)
Błąd przejściowy, znanej także jako błąd przejściowy, ma podstawową przyczyną rozwiązujący wkrótce się. Okazjonalne przyczyną błędów przejściowych jest podczas systemu Azure szybko przenosi zasoby sprzętowe na lepsze równoważenie obciążenia różnych obciążeń. Większość tych zdarzeń ponownej konfiguracji zakończone w mniej niż 60 sekund. Podczas tego zakresu czasu ponownej konfiguracji może mieć problemy z łącznością z bazą danych SQL. Aplikacje łączące się z bazą danych SQL powinny zostać skompilowane można oczekiwać w przypadku błędów przejściowych. Aby obsługiwać je, implementację logiki ponawiania próby w kodzie ich zamiast udostępniając je do użytkowników jako błędy aplikacji.

Jeśli program kliencki używa ADO.NET, program jest powiadamiany o błąd przejściowy przez throw z **SqlException**. Porównaj **numer** właściwości z listą błędów przejściowych, które znajdują się w górnej części tego artykułu [kody błędów SQL dla aplikacji klienckich, baza danych SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Połączenie, a polecenie
Ponów próbę połączenia SQL lub ustanawiania go ponownie, w zależności od następujących czynności:

* **Błąd przejściowy występuje podczas próby połączenia**: z opóźnieniem kilka sekund, ponów próbę połączenia.
* **Błąd przejściowy występuje w ciągu polecenia zapytania SQL**: nie natychmiast próbuj ponownie polecenie. Zamiast tego z opóźnieniem świeżo nawiązania połączenia. Następnie ponów próbę wykonania polecenia.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika ponawiania próby dla błędów przejściowych
Programy klienckie, które od czasu do czasu wystąpienia błędu przejściowego są bardziej niezawodne, jeśli zawierają one logiki ponawiania próby.

Gdy program komunikuje się z bazą danych SQL za pomocą oprogramowania pośredniczącego innych firm, należy poprosić dostawcę, czy oprogramowanie pośredniczące zawiera logiki ponawiania próby w przypadku błędów przejściowych.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zasady ponawiania
* W przypadku przejściowy błąd, spróbuj ponownie otworzyć połączenia.
* Nie bezpośrednio próbuj ponownie instrukcję SQL SELECT, która nie powiodła się z powodu błędu przejściowego.
  * Zamiast tego należy ustanowić nowego połączenia, a następnie ponów wyboru.
* Po instrukcji SQL UPDATE zakończy się niepowodzeniem z powodu błędu przejściowego, należy ustanowić połączenie świeże przed możesz ponowić próbę aktualizacji.
  * Logika ponawiania należy się upewnić, że transakcja całej bazy danych zostało zakończone lub że cała transakcja zostanie wycofana.

### <a name="other-considerations-for-retry"></a>Inne uwagi dotyczące ponownych prób
* Program wsadowy, który automatycznie rozpoczyna się po godzinach pracy i zakończeniu przed rano można pozwolić sobie być bardzo pacjentów z długo interwałów czasu między jego ponownych prób.
* Program interfejsu użytkownika należy uwzględnić tendencje ludzi po zbyt długim czasie oczekiwania.
  * Rozwiązanie nie powtórzyć co kilka sekund, ponieważ te zasady mogą wypełniania system z żądania.

### <a name="interval-increase-between-retries"></a>Zwiększ interwał między ponownymi próbami
Firma Microsoft zaleca, poczekaj na 5 sekund przed ponowną próbą wykonania Twojego pierwszego. Ponawianie próby opóźnieniem krótszy niż 5 sekund ryzyka przeciążając uda się rozpoznać usługi w chmurze. Każda kolejne próby, powinien być zwiększany, opóźnienie wykładniczo, maksymalnie 60 sekund.

Aby uzyskać informacje dotyczące okresu blokowania dla klientów używających ADO.NET, zobacz [połączenia programu SQL Server buforowania (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Można również ustawić maksymalnej liczby ponownych prób zanim program własnym zakończy.

### <a name="code-samples-with-retry-logic"></a>Przykłady kodu z logiki ponawiania próby
Przykłady kodu z Logika ponawiania są dostępne pod adresem:

- [Resiliently połączenia z serwerem SQL z ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Resiliently połączenia z serwerem SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Logika ponawiania testu
Aby przetestować Logika ponawiania, musi symulować lub spowodować błąd, który można rozwiązać, gdy program jest nadal uruchomiona.

#### <a name="test-by-disconnecting-from-the-network"></a>Testowanie przez odłączenie od sieci
Jednym ze sposobów można przetestować Logika ponawiania jest odłączyć od sieci na komputerze klienckim, gdy program jest uruchomiony. Błąd to:

* **SqlException.Number** = 11001
* Komunikat o błędzie: "nie Nieznany host"

W ramach pierwszej ponowienia próby programu można Popraw błąd, a następnie spróbuj się połączyć.

Aby ten test praktyczne, odłączenia komputera od sieci, przed rozpoczęciem pracy z programem. Następnie program rozpoznaje parametr środowiska uruchomieniowego, który powoduje, że program:

* Dodaj tymczasowo 11001 do swojej listy błędów można rozważyć jako przejściowy.
* Próba jego pierwszego połączenia w zwykły sposób.
* Po zostanie przechwycony błąd, należy usunąć 11001 z listy.
* Wyświetla komunikat informujący o użytkownikowi Podłącz komputer do sieci.
   * Zatrzymać dalsze wykonywanie za pomocą **Console.ReadLine** metody lub okna dialogowego z przycisku OK. Użytkownik naciśnie klawisz Enter, po której komputer jest podłączony do sieci.
* Spróbuj ponownie połączyć, oczekiwano Powodzenie.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testowanie przez nazwę bazy danych. błędy podczas nawiązywania połączenia
Program można celowo błędnie nazwy użytkownika przed pierwszą próbę połączenia. Błąd to:

* **SqlException.Number** = 18456
* Komunikat o błędzie: "Logowanie użytkownika"WRONG_MyUserName"nie powiodło się."

W ramach pierwszej ponowienia próby programu można Popraw błąd, a następnie spróbuj się połączyć.

Aby ten test praktyczne, program rozpoznaje parametr środowiska uruchomieniowego, który powoduje, że program:

* Dodaj tymczasowo 18456 do swojej listy błędów można rozważyć jako przejściowy.
* Celowo Dodaj "WRONG_" do nazwy użytkownika.
* Po zostanie przechwycony błąd, należy usunąć 18456 z listy.
* Usuń "WRONG_" z nazwą użytkownika.
* Spróbuj ponownie połączyć, oczekiwano Powodzenie.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry .NET SqlConnection ponownych prób połączenia
Jeśli program kliencki połączy się z bazą danych SQL za pomocą klasy .NET Framework **System.Data.SqlClient.SqlConnection**, użyj .NET 4.6.1 lub nowszej (lub .NET Core), aby mogli używać swoich funkcji ponów próbę połączenia. Aby uzyskać więcej informacji na temat funkcji, zobacz [tej strony sieci Web](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Podczas budowania [ciąg połączenia](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) dla Twojego **SqlConnection** obiektów, koordynować wartości między następującymi parametrami:

* **ConnectRetryCount**:&nbsp;&nbsp;wartość domyślna to 1. Zakres to od 0 do 255.
* **ConnectRetryInterval**:&nbsp;&nbsp;domyślna wynosi 1 s. Zakres to od 1 do 60.
* **Limit czasu połączenia**:&nbsp;&nbsp;domyślną jest 15 sekund. Zakres to od 0 do 2147483647.

W szczególności wybranej wartości upewnić następujące true równości:

Limit czasu połączenia = ConnectRetryCount * ConnectionRetryInterval

Na przykład, jeśli liczba jest równa 3 i interwał to 10 sekund, limit czasu równy tylko 29 sekund nie zapewnia system wystarczająco dużo czasu na jej trzeci i końcowych ponów próbę, aby połączyć: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Połączenie, a polecenie
**ConnectRetryCount** i **ConnectRetryInterval** let parametrów z **SqlConnection** obiektu spróbuj ponownie wykonać operację połączenia bez informuje lub bothering programu, takie jak zwracanie formantu do programu. Ponowne próby mogą wystąpić w następujących sytuacjach:

* Wywołanie metody mySqlConnection.Open
* Wywołanie metody mySqlConnection.Execute

Brak subtlety. Jeśli wystąpi błąd przejściowy podczas Twojej *zapytania* jest wykonywana, Twoje **SqlConnection** obiektu nie ponów próbę wykonania operacji połączenia. Go na pewno nie ponów próbę wykonania zapytania. Jednak **SqlConnection** bardzo szybko sprawdzić połączenie przed wysłaniem kwerendy do wykonania. Jeśli szybkie sprawdzenie wykryje problem z połączeniem **SqlConnection** ponawia operację połączenia. Jeśli próba powiedzie się, zapytanie jest wysyłane do wykonania.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Czy ConnectRetryCount powinny być połączone z aplikacji logiki ponawiania próby?
Załóżmy, że aplikacja ma Logika ponawiania niestandardowych niezawodny. Może on ponów operację connect cztery razy. Jeśli dodasz **ConnectRetryInterval** i **ConnectRetryCount** = 3 do parametrów połączenia, spowoduje zwiększenie liczby ponownych prób do 4 * 3 = 12 ponownych prób. Może nie ma takich dużą liczbę ponownych prób.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Połączenia z bazą danych SQL
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Połączenia: Parametry
Parametry połączenia, które są niezbędne do łączenia z bazą danych SQL jest nieco inne niż ciąg używany do połączenia z serwerem SQL. Możesz skopiować parametry połączenia bazy danych z [portalu Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Połączenia: Adres IP
Należy skonfigurować serwer bazy danych SQL, aby akceptował komunikację z adresu IP komputera, który obsługuje program kliencki. Aby skonfigurować tę konfigurację, Edytuj ustawienia zapory za pośrednictwem [portalu Azure](https://portal.azure.com/).

Jeśli zapomnisz skonfigurować adres IP, program nie powiedzie się pod ręką komunikat o błędzie stwierdzający niezbędne adresu IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Połączenia: porty
Zazwyczaj należy się upewnić, że tylko port 1433 jest otwarty dla komunikacji wychodzącej na komputerze, który hostuje program kliencki.

Na przykład gdy program kliencki znajduje się na komputerze z systemem Windows, można zapory systemu Windows na hoście otwórz port 1433.

1. Otwórz Panel sterowania.

2. Wybierz **sterowania wszystkie elementy panelu** > **zapory systemu Windows** > **Zaawansowane ustawienia** > **reguły ruchu wychodzącego**   >  **Akcje** > **nową regułę**.

Jeśli program kliencki znajduje się na maszynie wirtualnej platformy Azure (VM), przeczytaj [porty inne niż 1433 ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Aby uzyskać informacje dotyczące konfiguracji portów i adresów IP, zobacz [zapory bazy danych SQL Azure](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Połączenie: ADO.NET 4.6.1
Jeśli program korzysta z klas ADO.NET, takich jak **System.Data.SqlClient.SqlConnection** nawiązywania połączenia z bazą danych SQL, zalecane jest użycie .NET Framework w wersji 4.6.1 lub nowszej.

ADO.NET 4.6.1:

* Bazy danych SQL poprawia niezawodność po otwarciu połączenia przy użyciu **SqlConnection.Open** metody. **Otwórz** metody teraz zawiera mechanizmy optymalnych ponownych prób w odpowiedzi na błędów przejściowych niektóre błędy przed upływem limitu czasu połączenia.
* Buforowanie połączeń jest obsługiwana, w tym skutecznej weryfikacji, który obiekt połączenia udostępnia program działa.

Podczas korzystania z obiektu połączenia z puli połączeń, zaleca się, że program tymczasowo zamknąć połączenie, gdy nie jest od razu używany. Nie jest kosztowna ponownie otworzyć połączenie, ale należy utworzyć nowe połączenie.

Jeśli używasz ADO.NET 4.0 lub wcześniej, zaleca się uaktualnienie do najnowszej ADO.NET. Począwszy od listopada 2015 r, możesz [Pobierz ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostyka
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostyka: Test sprawdzający, czy można połączyć z narzędzia
Jeśli program nie może nawiązać połączenia z bazą danych SQL, jedną opcję diagnostyczne jest próby nawiązania połączenia z programem narzędzia. W idealnym przypadku narzędzie łączy się przy użyciu tej samej bibliotece, używany przez program.

Na dowolnym komputerze z systemem Windows możesz spróbować tych narzędzi:

* SQL Server Management Studio (ssms.exe), który jest połączony za pomocą ADO.NET
* sqlcmd.exe, który jest połączony za pomocą [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

Po połączeniu z programem należy sprawdzić, czy działa krótkich zapytanie SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostyka: Sprawdź otwartych portów
Jeśli podejrzewasz, że próby połączenia zakończyć się niepowodzeniem z powodu problemów z portu, narzędzie można uruchomić na komputerze, który raportów dotyczących konfiguracji portów.

W systemie Linux pomocne może być następujących narzędzi:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Zmień wartość przykład jako adresu IP.

W systemie Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) narzędzia mogą być pomocne. Oto przykład wykonywania, który zbadać sytuację portu na serwerze bazy danych SQL i zostało uruchomione na komputerze przenośnym:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostycznego: Błędy dziennika
Problem tymczasowy jest czasami najlepiej zdiagnozowany przez wykrywanie ogólne wzorca przez dni lub tygodnie.

Klient może pomóc w diagnozy przez funkcję rejestrowania wszystkich błędów napotkaniu. Dzięki temu można skorelować wpisy dziennika z danymi błąd, który loguje się wewnętrznie bazy danych SQL.

6 biblioteki przedsiębiorstwa (EntLib60) oferuje klas zarządzanych .NET z rejestrowania. Aby uzyskać więcej informacji, zobacz [5 - tak proste, jak objętych poza dziennika: przy użyciu bloku aplikacji rejestrowania](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostyka: Sprawdź dzienniki systemu pod kątem błędów
Poniżej przedstawiono niektóre języka Transact-SQL SELECT instrukcje, które zapytania dzienniki błędów i inne informacje.

| Zapytanie dziennika | Opis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) widok zawiera informacje na temat poszczególnych zdarzeń, w tym te, które mogą powodować przejściowe błędy lub awarie połączenia.<br/><br/>W idealnym przypadku można skorelować **godzina_rozpoczęcia** lub **end_time** wartości informująca, gdy program kliencki wystąpienia problemów.<br/><br/>Wymagane jest połączenie z *wzorca* bazy danych w celu wykonania tego zapytania. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) widoku oferuje zagregowanej liczby typów zdarzeń dla dodatkowych diagnostyczne.<br/><br/>Wymagane jest połączenie z *wzorca* bazy danych w celu wykonania tego zapytania. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostyka: Wyszukaj problem zdarzenia w dzienniku bazy danych SQL
Możesz wyszukać wpisy dotyczące problemu zdarzenia w dzienniku bazy danych SQL. Spróbuj następujących instrukcji języka Transact-SQL SELECT *wzorca* bazy danych:

```
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
W poniższym przykładzie pokazano, jak może wyglądać zwróconego wiersza. Wartości null, często nie są wartości null w innych wierszy.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteka Enterprise 6
6 biblioteki przedsiębiorstwa (EntLib60) to platforma klas .NET ułatwiająca implementację niezawodne klientów usługi w chmurze, z których jeden jest usługą bazy danych SQL. Aby znaleźć tematy przeznaczona do każdego obszaru, w którym można pomóc EntLib60, zobacz [biblioteki Enterprise 6 kwietnia 2013 r](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika ponawiania do obsługi błędów przejściowych jest jeden obszar, w którym można pomóc EntLib60. Aby uzyskać więcej informacji, zobacz [4 - Perseverance, klucz tajny wszystkie sukcesy: przy użyciu bloku przejściowej aplikacji obsługi błędów](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Kod źródłowy EntLib60 jest dostępny do pobrania publicznego [Centrum pobierania](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft nie planuje wprowadzić dodatkowe aktualizacje funkcji lub aktualizacje konserwacji EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Klasy EntLib60 dla błędów przejściowych i spróbuj ponownie
Następujące klasy EntLib60 są szczególnie użyteczne w przypadku logiki ponawiania próby. Te klasy znajdują się w lub w przestrzeni nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

W obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** — klasa
  
  * **ExecuteAction** — metoda
* **ExponentialBackoff** — klasa
* **SqlDatabaseTransientErrorDetectionStrategy** — klasa
* **ReliableSqlConnection** — klasa
  
  * **Parametr ExecuteCommand** — metoda

W obszarze nazw **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** — klasa
* **NeverTransientErrorDetectionStrategy** — klasa

Poniżej przedstawiono niektóre linki do informacji dotyczących EntLib60:

* Pobieranie książki wolnego: [przewodnik dewelopera Microsoft Enterprise bibliotece, wydanie 2](http://www.microsoft.com/download/details.aspx?id=41145).
* Najlepsze rozwiązania: [ponów ogólne wskazówki](../best-practices-retry-general.md) ma znakomity szczegółowym omówieniem logiki ponawiania próby.
* Pobieranie NuGet: [Enterprise Library – przejściowy błąd obsługi aplikacji bloku 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Rejestrowanie bloku
* Blok rejestrowania jest bardzo elastyczny i można skonfigurować rozwiązania, które umożliwia:
  
  * Utwórz i wiadomości dziennika są przechowywane w różnych lokalizacjach.
  * Przeprowadzania kategoryzacji i filtrowania wiadomości.
  * Zbierz informacje kontekstowe, które jest rejestrowanie przydatne do debugowania i śledzenia, a także do przeprowadzania inspekcji i ogólnych wymagań.
* Blok rejestrowania abstracts funkcji rejestrowania miejsce docelowe dziennika, aby kod aplikacji jest zgodny, niezależnie od lokalizacji i typ magazynu docelowego rejestrowania.

Aby uzyskać więcej informacji, zobacz [5 - tak proste, jak objętych poza dziennika: przy użyciu bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Kod źródłowy EntLib60 IsTransient — metoda
Dalej z **SqlDatabaseTransientErrorDetectionStrategy** klasy, kodu źródłowego C# dla **IsTransient** metody. Kod źródłowy wyjaśnia, błędów, które zostały uznane za przejściowych i warta ponownych prób, począwszy od kwietnia 2013.

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
* Aby uzyskać więcej informacji na temat rozwiązywania inne typowe problemy z połączeniami bazy danych SQL, zobacz [Rozwiązywanie problemów z połączeniem z bazą danych SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
* [Biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md)
* [Połączenie z serwerem SQL buforowanie (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Ponawianie próby* jest Apache 2.0 licencjonowane ogólnego przeznaczenia, ponawianie próby biblioteki napisanych w języku Python,](https://pypi.python.org/pypi/retrying) uprościć zadanie dodawania do wszystko, co podczas ponowienia próby.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

