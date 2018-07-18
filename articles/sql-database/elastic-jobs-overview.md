---
title: Zadania Elastic Database usługi Azure SQL | Microsoft Docs
description: Za pomocą zadań Elastic Database możesz uruchamiać skrypty Transact-SQL (T-SQL) w zestawie co najmniej jednej bazy danych Azure SQL Database
services: sql-database
author: srinia
manager: craigg
ms.service: sql-database
ms.topic: overview
ms.date: 06/14/2018
ms.author: srinia
ms.openlocfilehash: 65e920858b1c859dc9f8e911cdf59d4ab02da6d2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972166"
---
# <a name="manage-groups-of-databases-with-elastic-database-jobs"></a>Zarządzanie grupami baz danych za pomocą zadań Elastic Database

**Zadania Elastic Database** umożliwiają równoległe uruchamianie wielu skryptów T-SQL względem wielu baz danych na podstawie harmonogramu lub na żądanie.

**Uruchamianie zadań w ramach dowolnej kombinacji baz danych**: co najmniej jedna pojedyncza baza danych, wszystkie bazy danych na serwerze, wszystkie bazy danych w elastycznej puli lub mapa fragmentów ze zwiększoną elastycznością umożliwiającą dołączanie lub wykluczanie dowolnej określonej bazy danych. **Zadania można wykonywać na wielu serwerach, w wielu pulach, a nawet w obrębie baz danych w ramach różnych subskrypcji.** Serwery i pule są dynamicznie wyliczane w czasie wykonywania, więc zadania są uruchamiane względem wszystkich baz danych, które istnieją w docelowej grupie w czasie wykonywania.

Na poniższej ilustracji przedstawiono agenta zadań wykonującego zadania w ramach różnych typów grup docelowych:

![Model koncepcyjny agenta zadań elastycznych](media/elastic-jobs-overview/conceptual-diagram.png)


## <a name="why-use-elastic-jobs"></a>W jakim celu warto używać zadań elastycznych?

### <a name="manage-many-databases"></a>Zarządzanie wieloma bazami danych

- Planowanie zadań administracyjnych uruchamianych każdego dnia tygodnia, po godzinach pracy itp.
- Wdrażanie zmian schematu, zarządzanie poświadczeniami i zbieranie danych o wydajności lub danych telemetrycznych dotyczących dzierżaw (klientów). Aktualizowanie danych referencyjnych (informacje wspólne dla wszystkich baz danych).
- Odbudowywanie indeksów w celu zwiększenia wydajności zapytań. Konfigurowanie zadań w celu cyklicznego wykonywania w kolekcji baz danych, na przykład poza godzinami szczytu.
- Zbieranie na bieżąco wyników zapytań z zestawu baz danych w centralnej tabeli. Zapytania dotyczące wydajności mogą być nieprzerwanie wykonywane i skonfigurowane do wyzwalania dodatkowych zadań do wykonania.

### <a name="collect-data-for-reporting"></a>Zbieranie danych na potrzeby raportowania

- Agregowanie danych z kolekcji baz danych Azure SQL w pojedynczej tabeli docelowej.
- Wykonywanie dłużej działających zapytań dotyczących przetwarzania danych w ramach dużego zestawu baz danych, na przykład w celu zbierania telemetrii klienta. Wyniki są zbierane w pojedynczej tabeli docelowej na potrzeby dalszej analizy.

### <a name="reduce-overhead"></a>Zmniejszanie nakładu pracy

- Zazwyczaj należy niezależnie nawiązywać połączenie z każdą bazą danych w celu uruchamiania instrukcji języka Transact-SQL lub wykonywania innych zadań administracyjnych. Zadanie obsługuje logowanie do każdej bazy danych w grupie docelowej. Możesz również definiować, obsługiwać i utrwalać skrypty języka Transact-SQL do wykonania w ramach grupy baz danych Azure SQL.

### <a name="accounting"></a>Księgowość

- Zadania służą do rejestrowania stanu wykonywania dla każdej bazy danych. Zapewniają również automatyczne ponawianie próby w przypadku wystąpienia błędów.

### <a name="flexibility"></a>Elastyczność

- Definiowanie niestandardowych grup baz danych Azure SQL Database oraz określanie harmonogramów uruchamiania zadania.


## <a name="elastic-job-components"></a>Składniki zadań elastycznych

|Składnik  | Opis (dodatkowe szczegóły znajdują się pod tabelą) |
|---------|---------|
|[**Agent zadań elastycznych**](#elastic-job-agent) |  Zasób platformy Azure tworzony w celu uruchamiania zadań i zarządzania nimi.   |
|[**Baza danych zadań**](#job-database)    |    Baza danych Azure SQL Database używana przez agenta zadań do przechowywania zadań powiązanych z danymi, definicji zadań itp.      |
|[**Grupa docelowa**](#target-group)      |  Zestaw serwerów, pul, baz danych i map fragmentów, względem których ma być uruchamiane zadanie.       |
|[**Zadanie**](#job)  |  Zadanie to jednostka pracy, która składa się z co najmniej jednego [kroku zadania](#job-step). Kroki zadania określają skrypty T-SQL do uruchomienia, a także inne szczegóły wymagane do wykonania skryptu.  |


### <a name="elastic-job-agent"></a>Agent zadań elastycznych

Agent zadań elastycznych jest zasobem platformy Azure służącym do tworzenia i uruchamiania zadań oraz do zarządzania nimi. Agent zadań elastycznych jest zasobem platformy Azure tworzonym w witrynie Azure Portal (obsługiwany jest również program [PowerShell](elastic-jobs-powershell.md) i interfejs REST). 

Tworzenie **agenta zadań elastycznych** wymaga istniejącej bazy danych SQL Database. Agent konfiguruje tę istniejącą bazę danych jako [*bazę danych zadań*](#job-database).

Agent zadań elastycznych jest bezpłatny. Baza danych zadań jest rozliczana przy użyciu tych samych stawek, które są używane w przypadku wszystkich innych baz danych SQL Database.

### <a name="job-database"></a>Baza danych zadań

*Baza danych zadań* służy do definiowania zadań oraz śledzenia stanu i historii wykonywania zadań. *Baza danych zadań* służy również do przechowywania metadanych agenta, dzienników, wyników i definicji zadań oraz zawiera także wiele przydatnych procedur składowanych i innych obiektów bazy danych umożliwiających tworzenie i uruchamianie zadań oraz zarządzanie nimi — wszystko to za pomocą języka T-SQL.

W ramach bieżącej wersji zapoznawczej do utworzenia agenta zadań elastycznych jest wymagana istniejąca baza danych Azure SQL Database (w warstwie S0 lub wyższej).

Nie jest wymagane, aby *baza danych zadań* była nowa, ale powinna być wyczyszczonym, pustym wystąpieniem bazy danych w warstwie usługi S0 lub wyższej. Zalecana warstwa usługi dla *bazy danych zadań* to warstwa S1 lub wyższa, ale tak naprawdę zależy to od wymaganej wydajności na potrzeby zadań: liczby kroków zadań oraz liczby powtórzeń i częstotliwości ich uruchamiania. Na przykład baza danych w warstwie S0 może być wystarczająca na potrzeby agenta zadań, który uruchamia kilka zadań na godzinę, ale uruchamianie zadań co minutę może nie być wystarczająco wydajne i lepszym rozwiązaniem będzie skorzystanie z wyższej warstwy usługi.


#### <a name="job-database-permissions"></a>Uprawnienia dotyczące bazy danych zadań

Podczas tworzenia agenta zadań ma miejsce tworzenie schematu, tabel i roli o nazwie *jobs_reader* w *bazie danych zadań*. Ta rola jest tworzona z następującymi uprawnieniami i została zaprojektowana w celu umożliwienia administratorom bardziej precyzyjnej kontroli dostępu na potrzeby monitorowania zadań:


|Nazwa roli  |Uprawnienia do schematu „jobs”  |Uprawnienia do schematu „jobs_internal”  |
|---------|---------|---------|
|**jobs_reader**     |    SELECT     |    Brak     |

> [!IMPORTANT]
> Przed udzieleniem dostępu do *bazy danych zadań* na poziomie administratora bazy danych rozważ konsekwencje takiego działania w zakresie zabezpieczeń. Złośliwy użytkownik z uprawnieniami do tworzenia lub edytowania zadań może utworzyć lub edytować zadanie korzystające z przechowywanych poświadczeń do nawiązywania połączenia z kontrolowaną przez niego bazą danych, co może mu umożliwić określenie hasła poświadczeń.



### <a name="target-group"></a>Grupa docelowa

*Grupa docelowa* definiuje zestaw baz danych, względem którego będzie wykonywany krok zadania. Grupa docelowa może zawierać dowolną liczbę i kombinację następujących elementów:

- **Serwer Azure SQL** — jeśli serwer jest określony, wszystkie bazy danych istniejące na nim w czasie wykonywania zadania są częścią grupy. Konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania.
- **Elastyczna pula** — jeśli elastyczna pula jest określona, wszystkie bazy danych istniejące w niej w czasie wykonywania zadania są częścią grupy. Podobnie jak w przypadku serwera, konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania.
- **Pojedyncza baza danych** — określ co najmniej jedną pojedynczą bazę danych, która będzie należeć do grupy.
- **Mapa fragmentów** — bazy danych mapy fragmentów.

> [!TIP]
> W momencie wykonywania zadania za pomocą *dynamicznego wyliczania* ma miejsce ponowne obliczenie zestawu baz danych w grupach docelowych, które zawierają serwery lub pule. Pozwala to zapewnić, że **zadania są uruchamiane we wszystkich bazach danych, które istnieją na serwerze lub w puli w czasie wykonywania zadania**. Ponowne obliczanie listy baz danych w czasie wykonywania jest szczególnie przydatne w scenariuszach, w których często zmienia się członkostwo w puli lub na serwerze.


Pule i pojedyncze bazy danych można określić jako uwzględnione lub wykluczone z grupy. Umożliwia to tworzenie grupy docelowej z dowolną kombinacją baz danych. Na przykład możesz dodać serwer do grupy docelowej, ale wykluczyć określone bazy danych znajdujące się w elastycznej puli (lub wykluczyć całą pulę).

Grupa docelowa może zawierać bazy danych znajdujące się w wielu subskrypcjach i regionach. Należy pamiętać, że operacje wykonywane między regionami mają większe opóźnienia niż te wykonywane w tym samym regionie.


### <a name="job"></a>Zadanie

*Zadanie* to jednostka pracy, która jest wykonywana na podstawie harmonogramu lub jako zadanie jednorazowe. Zadanie składa się z co najmniej jednego *kroku zadania*.

#### <a name="job-step"></a>Krok zadania

Każdy krok zadania określa skrypt T-SQL do wykonania, co najmniej jedną grupę docelową, względem której ten skrypt będzie uruchamiany, oraz poświadczenia wymagane dla agenta zadań do nawiązania połączenia z docelową bazą danych. Każdy krok zadania posiada możliwy do dostosowania limit czasu i zasady ponawiania oraz pozwala na opcjonalne określenie parametrów wyjściowych.

#### <a name="job-output"></a>Dane wyjściowe zadania

Wyniki wykonywania kroków zadania w każdej docelowej bazie danych są szczegółowo rejestrowane, a dane wyjściowe skryptu mogą zostać przechwycone w określonej tabeli. Możesz określić bazę danych, aby zapisać wszystkie dane zwrócone z zadania.

#### <a name="job-history"></a>Historia zadania

Historia wykonywania zadań jest przechowywana w *bazie danych zadań*. Zadanie oczyszczania systemu czyści historię wykonywania, która jest starsza niż 45 dni. Aby usunąć historię, która ma mniej niż 45 dni, wywołaj procedurę składowaną **sp_purge_history** w *bazie danych zadań*.

## <a name="workflow-to-create-configure-and-manage-jobs"></a>Przepływ pracy służący do tworzenia i konfigurowania zadań oraz do zarządzania nimi

### <a name="create-and-configure-the-agent"></a>Tworzenie i konfigurowanie agenta

1. Utwórz lub zidentyfikuj pustą bazę danych SQL w warstwie S0 lub wyższej. Będzie ona używana jako *baza danych zadań* podczas tworzenia agenta zadań elastycznych.
2. Utwórz agenta zadań elastycznych w witrynie [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) lub za pomocą programu [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Tworzenie agenta zadań elastycznych](media/elastic-jobs-overview/create-elastic-job-agent.png)

### <a name="create-run-and-manage-jobs"></a>Tworzenie i uruchamianie zadań oraz zarządzanie nimi

1. Utwórz poświadczenia służące do wykonywania zadań w *bazie danych zadań* za pomocą programu [PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets) lub języka [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Zdefiniuj grupę docelową (bazy danych, względem których ma być uruchamiane zadanie) za pomocą programu [PowerShell](elastic-jobs-powershell.md#define-the-target-databases-you-want-to-run-the-job-against) lub języka [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Utwórz poświadczenia agenta zadań w każdej bazie danych, w której będzie wykonywane zadanie [(dodaj użytkownika lub rolę do każdej bazy danych w grupie)](https://docs.microsoft.com/azure/sql-database/sql-database-control-access). Aby uzyskać przykład, zobacz [samouczek programu PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets).
4. Utwórz zadanie za pomocą programu [PowerShell](elastic-jobs-powershell.md#create-a-job) lub języka [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Dodaj kroki zadania za pomocą programu [PowerShell](elastic-jobs-powershell.md#create-a-job-step) lub języka [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Uruchom zadanie za pomocą programu [PowerShell](elastic-jobs-powershell.md#run-the-job) lub języka [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitoruj stan wykonywania zadania za pomocą witryny Azure Portal, programu [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) lub języka [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portal](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Poświadczenia na potrzeby uruchamiania zadań

Za pomocą [poświadczeń o zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) zadania łączą się z bazami danych określonymi przez grupę docelową w momencie wykonania. Jeśli grupa docelowa obejmuje serwery lub pule, te poświadczenia o zakresie bazy danych są używane do łączenia się z bazą danych master w celu wyliczenia dostępnych baz danych.

Konfigurowanie odpowiednich poświadczeń służących do uruchamiania zadania może wydawać się nieco mylące, więc należy mieć na uwadze następujące kwestie:

- Poświadczenia o zakresie bazy danych należy utworzyć w *bazie danych zadań*.
- **Dla wszystkich docelowych baz danych muszą istnieć [wystarczające uprawnienia](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) do pomyślnego wykonania zadania** (użytkownik jobuser na poniższym diagramie).
- Oczekiwane jest, że poświadczenia będą ponownie używane w różnych zadaniach, a hasła poświadczeń będą zaszyfrowane i zabezpieczone przed użytkownikami, którzy mają dostęp tylko do odczytu do obiektów zadań.

Poniższa ilustracja ułatwia zrozumienie i ustawienie odpowiednich poświadczeń zadań. **Pamiętaj, aby utworzyć użytkownika w każdej bazie danych (wszystkie *docelowe bazy danych użytkowników*), w której ma być uruchamiane zadanie**.

![Poświadczenia zadań elastycznych](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń

Kilka uwag dotyczących najlepszych rozwiązań podczas pracy z zadaniami elastycznymi:

- Ogranicz użycie interfejsów API do tych zaufanych.
- Poświadczenia powinny mieć możliwie najmniejsze uprawnienia niezbędne do wykonania kroku zadania. Aby uzyskać dodatkowe informacje, zobacz [Autoryzacja i uprawnienia w programie SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Podczas korzystania z serwera i/lub docelowego członka grupy puli zdecydowanie zaleca się utworzenie oddzielnych poświadczeń z uprawnieniami w bazie danych master służących do wyświetlania/uzyskiwania listy baz danych w celu rozwijania list baz danych serwerów i/lub pul przed wykonaniem zadania.



## <a name="agent-performance-capacity-and-limitations"></a>Wydajność agenta, pojemność i ograniczenia

Zadania elastyczne używają minimalnych zasobów obliczeniowych podczas oczekiwania na zakończenie długotrwałych zadań.

W zależności od rozmiaru grupy docelowej baz danych i żądanego czasu wykonywania zadania (liczba równoczesnych procesów roboczych) agent wymaga różnej ilości zasobów obliczeniowych i wydajności *bazy danych zadań* (im więcej elementów docelowych i większa liczba zadań, tym wymagana jest większa ilość zasobów obliczeniowych).

Wersja zapoznawcza jest obecnie ograniczona do 100 współbieżnych zadań.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zapobieganie zmniejszania wydajności docelowej bazy danych przez zadania

Aby zapewnić, że zasoby nie będą przeciążone podczas uruchamiania zadań w ramach baz danych w elastycznej puli SQL, możliwe jest skonfigurowanie zadań w taki sposób, aby ograniczana była liczba baz danych, w ramach których mogą one być jednocześnie uruchamiane.

##  <a name="differences-between-elastic-jobs-and-sql-server-agent"></a>Różnice między zadaniami elastycznymi i programem SQL Server Agent

Warto zauważyć kilka różnic między programem SQL Server Agent (dostępnym lokalnie i w ramach wystąpienia zarządzanego usługi SQL Database) i agentem zadań elastycznych usługi Azure SQL Database (teraz dostępnym na potrzeby usługi SQL Database i SQL Data Warehouse).


|  |Zadania elastyczne  |Program SQL Server Agent |
|---------|---------|---------|
|Zakres     |  Dowolna liczba baz danych Azure SQL Database i/lub magazynów danych w tej samej chmurze platformy Azure jako agent zadań. Elementy docelowe mogą znajdować się w ramach różnych serwerów logicznych, subskrypcji i/lub regionów. <br><br>Grupy docelowe mogą składać się z pojedynczych baz danych lub hurtowni danych albo wszystkich baz danych na serwerze, w puli lub w mapie fragmentów (wyliczanych dynamicznie w czasie wykonywania zadania). | Dowolna pojedyncza baza danych w tym samym wystąpieniu programu SQL Server jako agent SQL. |
|Obsługiwane interfejsy API i narzędzia     |  Witryna Azure Portal, program PowerShell, język T-SQL, usługa Azure Resource Manager      |   Język T-SQL, program SQL Server Management Studio (SSMS)     |





## <a name="best-practices-for-creating-jobs"></a>Najlepsze rozwiązania dotyczące tworzenia zadań

### <a name="idempotent-scripts"></a>Skrypty idempotentne
Skrypty T-SQL zadania muszą być [idempotentne](https://en.wikipedia.org/wiki/Idempotence). **Idempotentność** oznacza, że jeśli skrypt zakończy się pomyślnie i zostanie uruchomiony ponownie, da to taki sam wynik. Skrypt może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. W takim przypadku zadanie automatycznie ponowi próbę uruchomienia skryptu wstępnie zdefiniowaną liczbę razy przed zaniechaniem. Wynik działania skryptu idempotentnego będzie taki sam, nawet jeśli zostanie pomyślnie uruchomiony dwukrotnie (lub większą liczbę razy).

Prostym sposobem jest sprawdzenie istnienia obiektu przed jego utworzeniem.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Podobnie musi być możliwe pomyślne wykonanie skryptu przez logiczne testowanie wszystkich znalezionych warunków i reagowanie na nie.



## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą programu PowerShell](elastic-jobs-powershell.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą języka Transact-SQL (T-SQL)](elastic-jobs-tsql.md)