---
title: Zarządzanie bazami danych w chmurze skalowanych w poziomie | Dokumentacja firmy Microsoft
description: Usługa zadań elastycznych baz danych do uruchomienia skryptu dla grupy baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8abb2e3ac4f62a3ea51cc686bbf23260fccc4077
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441376"
---
# <a name="managing-scaled-out-cloud-databases"></a>Zarządzanie bazami danych w chmurze skalowanych w poziomie

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Zadania elastic Database** jest hostowana przez klienta usługa w chmurze umożliwiająca wykonanie ad hoc i zaplanowane zadania administracyjne, które są nazywane **zadań**. Za pomocą zadań można łatwo i niezawodne zarządzanie dużymi grupami baz danych Azure SQL Database dzięki uruchamianiu skryptów języka Transact-SQL, aby wykonywać operacje administracyjne.

Do zarządzania bazami danych podzielonych na fragmenty skalowanych w poziomie, **zadania Elastic Database** funkcji (wersja zapoznawcza) umożliwia niezawodne wykonywanie skryptów języka Transact-SQL (T-SQL) w grupie baz danych, w tym:

- niestandardowy zbiór baz danych (opisana poniżej)
- wszystkie bazy danych w [puli elastycznej](sql-database-elastic-pool.md)
- zestaw fragmentów (utworzone za pomocą [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md)).

Aby uzyskać więcej informacji na temat koncepcji podzielonej na fragmenty bazy danych, zobacz [fragmentowanie bazy danych programu SQL Server](https://blog.pythian.com/sharding-sql-server-database/).

## <a name="documentation"></a>Dokumentacja

- [Zainstaluj składniki zadania Elastic Database](sql-database-elastic-jobs-service-installation.md).
- [Wprowadzenie do zadań elastycznych baz danych](sql-database-elastic-jobs-getting-started.md).
- [Tworzenie zadań i zarządzanie nimi przy użyciu programu PowerShell](sql-database-elastic-jobs-powershell.md).
- [Tworzenie i zarządzanie nimi skalowanych w poziomie bazy danych Azure SQL](sql-database-elastic-jobs-getting-started.md)

![Usługa zadań elastycznej bazy danych][1]

## <a name="why-use-jobs"></a>Dlaczego warto korzystać z zadania

### <a name="manage"></a>Zarządzanie

Łatwe zmiany schematu, Zarządzanie poświadczeniami, aktualizacje danych referencyjnych, zbierania danych o wydajności lub zbieranie danych telemetrycznych dzierżaw (klientów).

### <a name="reports"></a>Raporty

Agregowanie danych z kolekcji baz danych Azure SQL Database w pojedynczej tabeli docelowej.

### <a name="reduce-overhead"></a>Zmniejszanie nakładu pracy

Zazwyczaj należy niezależnie nawiązywać połączenie z każdą bazą danych w celu uruchamiania instrukcji języka Transact-SQL lub wykonywania innych zadań administracyjnych. Zadanie obsługuje logowanie do każdej bazy danych w grupie docelowej. Możesz również definiować, obsługiwać i utrwalać skrypty języka Transact-SQL do wykonania w ramach grupy baz danych Azure SQL Database.

### <a name="accounting"></a>Księgowość

Zadania uruchamiania skryptu i rejestrować stan wykonania dla każdej bazy danych. Zapewniają również automatyczne ponawianie próby w przypadku wystąpienia błędów.

### <a name="flexibility"></a>Elastyczność

Definiowanie niestandardowych grup baz danych Azure SQL Database oraz określanie harmonogramów uruchamiania zadania.

> [!NOTE]
> W witrynie Azure portal zmniejszenie zbiór funkcji do maksymalnie pule elastyczne SQL Azure jest dostępna. Dostęp do pełnego zestawu funkcji bieżącego przy użyciu interfejsów API środowiska PowerShell.

## <a name="applications"></a>Aplikacje

- Wykonywanie zadań administracyjnych, takich jak wdrażanie nowego schematu.
- Zaktualizuj odwołanie do danych — informacje o produkcie wspólne dla wszystkich baz danych. Lub harmonogramy automatycznych aktualizacji każdy dzień powszedni po godzinach.
- Odbudowywanie indeksów w celu zwiększenia wydajności zapytań. Ponowne tworzenie można skonfigurować do wykonania w kolekcji baz danych cyklicznie, takie jak poza godzinami szczytu.
- Zbieranie na bieżąco wyników zapytań z zestawu baz danych w centralnej tabeli. Zapytania dotyczące wydajności mogą być nieprzerwanie wykonywane i skonfigurowane do wyzwalania dodatkowych zadań do wykonania.
- Wykonywanie dłużej działających zapytań dotyczących przetwarzania danych w ramach dużego zestawu baz danych, na przykład w celu zbierania telemetrii klienta. Wyniki są zbierane w pojedynczej tabeli docelowej na potrzeby dalszej analizy.

## <a name="elastic-database-jobs-end-to-end"></a>Zadania elastic Database: end-to-end

1. Zainstaluj **zadania Elastic Database** składników. Aby uzyskać więcej informacji, zobacz [zadania instalowania Elastic Database](sql-database-elastic-jobs-service-installation.md). W przypadku niepowodzenia instalacji, zobacz [odinstalowywania](sql-database-elastic-jobs-uninstall.md).
2. Dostęp więcej funkcji, na przykład tworzenia niestandardowej bazy danych kolekcji, dodawania harmonogramów i/lub zbieranie zestawów wyników przy użyciu interfejsów API programu PowerShell. Korzystanie z portalu prostą instalację i tworzenia/monitorowania zadań ograniczone do wykonywania względem **puli elastycznej**.
3. Utwórz zaszyfrowane poświadczenia do wykonywania zadań i [Dodaj użytkownika (lub rolach) do każdej bazy danych w grupie](sql-database-security-overview.md).
4. Utwórz idempotentne skryptu T-SQL, które mogą być uruchamiane względem każdej bazy danych w grupie.
5. Wykonaj następujące kroki, aby tworzyć zadania w witrynie Azure portal: [Tworzenie i zarządzanie nimi zadania Elastic Database](sql-database-elastic-jobs-create-and-manage.md).
6. Lub za pomocą skryptów programu PowerShell: [Tworzenie i zarządzanie nimi zadań elastycznej bazy danych SQL Database przy użyciu programu PowerShell (wersja zapoznawcza)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Skrypty idempotentne

Skrypty muszą być [idempotentne](https://en.wikipedia.org/wiki/Idempotence). W prostych słowach "idempotentne" oznacza, że jeśli skrypt zakończy się powodzeniem po ponownym uruchomieniu, występuje ten sam wynik. Skrypt może zakończyć się niepowodzeniem z powodu przejściowych problemów z siecią. W takim przypadku zadanie automatycznie ponowi próbę uruchomienia skryptu wstępnie zdefiniowaną liczbę razy przed zaniechaniem. Skrypt idempotentne ma ten sam wynik, nawet wtedy, gdy została pomyślnie uruchomiona dwukrotnie.

Prostym sposobem jest sprawdzenie istnienia obiektu przed jego utworzeniem.  

```sql
    IF NOT EXIST (some_object)
    -- Create the object
```

Podobnie musi być możliwe pomyślne wykonanie skryptu przez logiczne testowanie wszystkich znalezionych warunków i reagowanie na nie.

## <a name="failures-and-logs"></a>Błędy i dzienniki

Jeśli skrypt zakończy się niepowodzeniem po wielu próbach, zadanie rejestruje błąd i kontynuuje. Po zakończeniu zadania (to znaczy wykonywania względem wszystkich baz danych w grupie), możesz sprawdzić swoją listę nieudanych prób. Dzienniki zawierają szczegółowe informacje debugowania uszkodzone skrypty.

## <a name="group-types-and-creation"></a>Grupy typów i tworzenie

Istnieją dwa rodzaje grup:

1. Ustawia fragmentów
2. Grupy niestandardowe

Fragment zestawu grup są tworzone przy użyciu [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md). Podczas tworzenia grupy zestaw fragmentu bazy danych są dodawane lub automatycznie usunięte z grupy. Na przykład nowych fragmentów będzie automatycznie w grupie po dodaniu do mapy fragmentów. Następnie można uruchomić zadania względem grupy.

Niestandardowe grupy z drugiej strony, sztywno zdefiniowano. Należy jawnie dodać lub usunąć bazy danych z grup niestandardowych. Jeśli bazy danych w grupie zostało porzucone, zadanie będzie podejmować próby Uruchom skrypt w bazie danych, co w przypadku wystąpienia awarii. Grupy utworzone w witrynie Azure portal aktualnie są niestandardowe grupy.

## <a name="components-and-pricing"></a>Składniki i ceny

Następujące składniki współpracują ze sobą do utworzenia usługą w chmurze platformy Azure, która umożliwia ad-hoc wykonywania zadań administracyjnych. Składniki są instalowane i konfigurowane automatycznie podczas instalacji, w ramach subskrypcji. Usługi można zidentyfikować, ponieważ wszystkie one mają taką samą nazwę wygenerowany automatycznie. Nazwa jest unikatowa i składa się z prefiksu "edj" następują znaki losowo generowany 21.

- Azure Cloud Service

  Zadania elastic database (wersja zapoznawcza) są dostarczane jako usługa w chmurze platformy Azure obsługiwane przez klienta do wykonywania żądanych zadań do wykonania. Z poziomu portalu usługa jest wdrożona i hostowanych w ramach subskrypcji Microsoft Azure. Wartość domyślna wdrożona usługa działa z co najmniej dwie role procesów roboczych w celu zapewnienia wysokiej dostępności. Domyślny rozmiar każdej roli proces roboczy (ElasticDatabaseJobWorker) uruchomionej na wystąpieniu A0. Aby poznać ceny, zobacz [cennika usług Cloud services](https://azure.microsoft.com/pricing/details/cloud-services/).

- Azure SQL Database

  Usługa korzysta z usługi Azure SQL Database, znana jako **bazy danych kontroli** do przechowywania wszystkich metadanych zadania. Domyślna Warstwa usługi to S0. Aby poznać ceny, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

- Azure Service Bus

  Usługi Azure Service Bus jest do koordynowania prac w ramach usługi w chmurze Azure. Zobacz [cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

- Azure Storage

  Konto usługi Azure Storage jest używana do przechowywania rejestrowanie diagnostyczne dane wyjściowe, w przypadku, gdy problem wymaga dalszego debugowania (zobacz [Włączanie diagnostyki w usługach Azure Cloud Services i Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md)). Aby poznać ceny, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Jak działają zadania elastycznych baz danych

1. Usługi Azure SQL Database jest wyznaczony **bazy danych kontroli** która przechowuje wszystkie metadane i dane dotyczące stanu.
2. Dostępu do bazy danych kontroli przez **usługa zadań** można uruchomić i śledzić zadania do wykonania.
3. Dwie różne role komunikują się z bazą danych sterowania:
   - Kontroler: Określa zadania, które wymagają zadania do wykonania żądanego zadania i zadań zakończonych niepowodzeniem prób przez utworzenie nowego zadania.
   - Wykonania zadania: Wykonuje zadania.

### <a name="job-task-types"></a>Typy zadań zadania

Istnieje wiele typów zadań, wykonujących wykonywanie zadań:

- ShardMapRefresh

  Wykonuje kwerendę mapowania fragmentów w celu ustalenia wszystkich baz danych używanych jako fragmenty
- ScriptSplit

  Dzieli skryptu w instrukcji "Przejdź" w partie
- ExpandJob

  Tworzy podrzędne zadania dla każdej bazy danych od zadania, który jest przeznaczony dla grupy baz danych
- ScriptExecution

  Wykonuje skrypt dla konkretnej bazy danych przy użyciu określonych poświadczeń
- Dacpac

  Dotyczy określonej bazy danych przy użyciu poświadczeń określonego pliku DACPAC

## <a name="end-to-end-job-execution-work-flow"></a>End-to-end zadania wykonywania z przepływu pracy

1. Za pomocą portalu lub interfejsu API programu PowerShell, zadanie jest wstawiany do **bazy danych kontroli**. Wykonywanie żądania zadania skryptu języka Transact-SQL, względem grupy baz danych przy użyciu określonych poświadczeń.
2. Kontroler identyfikuje nowe zadanie. Zadania podrzędne są tworzone i wykonywane dzielenie skrypt i Odśwież grupę baz danych. Ponadto nowe zadanie jest tworzony i wykonywane w celu rozwiń zadanie i utworzyć nowy element podrzędny zadania, w którym określono każde zadanie podrzędne do uruchomienia skryptu języka Transact-SQL w odniesieniu poszczególnych baz danych w grupie.
3. Kontroler identyfikuje zadań podrzędnych utworzony. Dla każdego zadania kontrolera tworzy i uruchamia zadanie, można wykonać skryptu na bazie danych.
4. Po zakończeniu wszystkich zadań, kontroler aktualizacji zadania do stanu ukończenia.
   W dowolnym momencie podczas wykonywania zadania interfejs API środowiska PowerShell może służyć do wyświetlania bieżącego stanu wykonywania zadania. Cały czas zwracana przez interfejsy API programu PowerShell są reprezentowane w formacie UTC. Jeśli to konieczne, można zainicjować na żądanie anulowania także zatrzymać zadanie.

## <a name="next-steps"></a>Kolejne kroki

[Zainstaluj składniki](sql-database-elastic-jobs-service-installation.md), następnie [tworzenie i dodawanie logowania do każdej bazy danych w grupie baz danych](sql-database-manage-logins.md). Aby jeszcze lepiej zrozumieć zadania, tworzenie i zarządzanie nimi, zobacz [tworzenie i zarządzanie nimi zadania elastic database](sql-database-elastic-jobs-create-and-manage.md). Zobacz też [wprowadzenie do zadań elastycznych baz danych](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->
