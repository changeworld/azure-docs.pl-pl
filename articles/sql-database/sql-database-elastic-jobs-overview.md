---
title: Zarządzanie bazami danych w chmurze skalowalnych w poziomie | Dokumentacja firmy Microsoft
description: Użyj usługi zadania elastycznej bazy danych do uruchomienia skryptu przez grupę baz danych.
metakeywords: azure sql database elastic databases
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 3fdc24a811544de87d97da498d7946507653cec5
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="managing-scaled-out-cloud-databases"></a>Zarządzanie bazami danych w chmurze skalowalnych w poziomie
Aby zarządzać bazy danych podzielonej skalowalnych w poziomie, **zadania elastycznej bazy danych** funkcji (wersja zapoznawcza) umożliwia niezawodnie wykonać skryptu języka Transact-SQL (T-SQL) między grupą baz danych, w tym:

* niestandardowy zbiór baz danych (co omówiono poniżej)
* wszystkie bazy danych w [puli elastycznej](sql-database-elastic-pool.md)
* zestaw niezależnego fragmentu (utworzone za pomocą [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Dokumentacja
* [Zainstaluj składniki zadania elastycznej bazy danych](sql-database-elastic-jobs-service-installation.md). 
* [Rozpoczynanie pracy z zadaniami elastycznej bazy danych](sql-database-elastic-jobs-getting-started.md).
* [Utwórz zadania i zarządzać nimi za pomocą programu PowerShell](sql-database-elastic-jobs-powershell.md).
* [Tworzenie i zarządzanie nimi skalowanej baz danych SQL Azure](sql-database-elastic-jobs-getting-started.md)

**Zadania elastyczne bazy danych** jest obecnie obsługiwana w środowisku klienta Azure usługi chmury, która umożliwia wykonanie ad hoc i zaplanowanych zadań administracyjnych, które są nazywane **zadania**. Z zadaniami można łatwo i niezawodny sposób zarządzać grupami dużych baz danych SQL Azure, uruchamiając skrypty języka Transact-SQL do wykonania operacji administracyjnych. 

![Usługa zadań elastycznej bazy danych][1]

## <a name="why-use-jobs"></a>Dlaczego warto używać zadania?
**Zarządzanie**

Łatwe zmiany schematu, Zarządzanie poświadczeniami, aktualizacje danych odwołania, zbierania danych wydajności lub gromadzenia danych telemetrii dzierżawy (klienta).

**Raporty**

Agregowanie danych z kolekcji baz danych SQL Azure do tabeli pojedynczego miejsca docelowego.

**Zmniejszenie nakładów pracy**

Zazwyczaj należy nawiązać każdej bazy danych niezależnie w celu uruchamiania instrukcji języka Transact-SQL lub innych zadań administracyjnych. Zadanie obsługuje zadanie logowanie do każdej bazy danych w grupie docelowej. Można również zdefiniować, obsługa i utrwalić skrypty języka Transact-SQL do wykonania między grupą baz danych SQL Azure.

**Ewidencjonowanie aktywności**

Zadania uruchamiania skryptu i rejestrowanie stanu wykonywania dla każdej bazy danych. Umożliwia również wyświetlenie automatyczny, gdy występują błędy.

**Elastyczność**

Zdefiniuj niestandardowe grupy baz danych SQL Azure oraz określić harmonogramy uruchamiania zadania.

> [!NOTE]
> W portalu Azure ograniczonego zestawu funkcji ograniczone do puli elastycznej SQL Azure jest dostępna. Dostęp do pełnego zestawu funkcji bieżącego przy użyciu interfejsów API środowiska PowerShell.
> 
> 

## <a name="applications"></a>Aplikacje
* Wykonywanie zadań administracyjnych, takich jak wdrażanie nowego schematu.
* Zaktualizuj informacje produktu danych wspólnych dla wszystkich baz danych. Lub aktualizacji automatycznych harmonogramy każdy dzień tygodnia po godzinach.
* Odbuduj indeksy w celu zwiększenia wydajności zapytania. Ponowne tworzenie można skonfigurować do uruchamiania wszystkich kolekcji baz danych cyklicznie, takich jak poza godzinami szczytu.
* Zbieranie wyników zapytania z zestawu baz danych do centralnej tabeli na podstawie bieżącego. Stale wykonać kwerendy wydajności i skonfigurowane do wyzwalania dodatkowe zadania do wykonania.
* Wykonanie dłużej uruchomione zapytania przetwarzania danych w dużych zestawach baz danych, na przykład kolekcja telemetrii klienta. Wyniki są zbierane w tabeli docelowej jednego do dalszej analizy.

## <a name="elastic-database-jobs-end-to-end"></a>Zadania elastyczne bazy danych: end-to-end
1. Zainstaluj **zadania elastycznej bazy danych** składników. Aby uzyskać więcej informacji, zobacz [zadania instalowania elastycznej bazy danych](sql-database-elastic-jobs-service-installation.md). W przypadku niepowodzenia instalacji, zobacz [odinstalowywania](sql-database-elastic-jobs-uninstall.md).
2. Użyj interfejsów API środowiska PowerShell dla dostępu więcej funkcji, na przykład tworzenie kolekcji niestandardowy bazy danych, dodawanie harmonogramów i/lub zbierania zestawów wyników. Korzystanie z portalu prostą instalację i tworzenia/monitorowanie zadań ograniczone do wykonania przed **puli elastycznej**. 
3. Utwórz zaszyfrowane poświadczenia do wykonywania zadań i [dodać użytkownika (lub roli) do każdej bazy danych w grupie](sql-database-security-overview.md).
4. Utwórz idempotentności skryptu T-SQL, który można uruchomić dla każdej bazy danych w grupie. 
5. Wykonaj następujące kroki, aby tworzyć zadania przy użyciu portalu Azure: [Tworzenie zadania i zarządzać nimi elastycznej bazy danych](sql-database-elastic-jobs-create-and-manage.md). 
6. Lub użyć skryptów środowiska PowerShell: [Utwórz zadania elastycznej bazy danych SQL Database, przy użyciu programu PowerShell (wersja zapoznawcza) i zarządzać nimi](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Skrypty idempotentności
Skrypty musi być [idempotentności](https://en.wikipedia.org/wiki/Idempotence). W proste warunki "idempotentności" oznacza, że jeśli skrypt zakończy się pomyślnie i jest uruchamiany ponownie, wystąpi ten sam rezultat. Skrypt może zakończyć się niepowodzeniem z powodu przejściowe problemy z siecią. W takim przypadku zadania automatycznie ponowi próbę uruchomienia skryptu wstępnie zdefiniowane kilka razy przed desisting. Skrypt idempotentności ma ten sam rezultat nawet wtedy, gdy pomyślnym uruchomieniu dwa razy. 

To proste działanie do sprawdzania istnienia obiektu przed utworzeniem jej.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

Podobnie skryptu musi być w stanie pomyślnie wykonać przez logicznie testowanie pod kątem i przeciwdziałanie wszystkie problemy znalezione.

## <a name="failures-and-logs"></a>Awarie i dzienniki
Jeśli skrypt nie powiedzie się po wielu próbach, zadanie rejestruje błąd i kontynuuje. Po zakończeniu zadania (to znaczy do uruchomienia wszystkich baz danych w grupie), można sprawdzić jego lista nieudanych prób. Dzienniki zawierają szczegółowe informacje debugowania uszkodzone skryptów. 

## <a name="group-types-and-creation"></a>Typy grupy i utworzenia
Istnieją dwa rodzaje grup: 

1. Ustawia niezależnego fragmentu
2. Grupy niestandardowe

Identyfikator niezależnego fragmentu zestaw grup są tworzone przy użyciu [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md). Podczas tworzenia grupy zestaw niezależnego fragmentu bazy danych są dodawane lub automatycznie usunięte z grupy. Na przykład nowy identyfikator niezależnego fragmentu zostanie automatycznie w grupie po dodaniu go do mapy niezależnego fragmentu. Zadania mogą być następnie uruchamiane na grupie.

Niestandardowe grupy z drugiej strony, sztywno zdefiniowano. Należy jawnie dodać lub usunąć baz danych z niestandardowych grup. Jeśli bazy danych w grupie zostało porzucone, zadanie będzie podejmować próby Uruchom skrypt w bazie danych, co spowoduje niepowodzenie ostatecznego. Grup utworzonych za pomocą portalu Azure, obecnie są grup niestandardowych. 

## <a name="components-and-pricing"></a>Składniki i cen
Następujące składniki współpracują, aby utworzyć usługi w chmurze Azure, który umożliwia wykonanie ad hoc zadań administracyjnych. Składniki zostaną zainstalowane i skonfigurowane automatycznie podczas instalacji, w ramach subskrypcji. Usługi można zidentyfikować wszystkie mają taką samą nazwę wygenerowany automatycznie. Nazwa jest unikatowa i składa się z prefiksu "edj" następują znaki losowo generowany 21.

* **Usługa w chmurze Azure**: zadania elastycznej bazy danych (wersja zapoznawcza) jest dostarczany jako usługa w chmurze Azure obsługiwany przez klienta do wykonania wykonywania żądane zadania. Z portalu wdrożyć i usługi hostowanej w subskrypcji platformy Microsoft Azure. Wartość domyślna wdrożona usługa działa z co najmniej dwie role proces roboczy wysokiej dostępności. Domyślny rozmiar każdej roli proces roboczy (ElasticDatabaseJobWorker) działa na wystąpienie A0. O cenach, zobacz [cennik usługi w chmurze](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Baza danych SQL Azure**: usługa korzysta z bazy danych SQL Azure, znany jako **bazy danych kontroli** do przechowywania wszystkich metadanych zadania. Warstwy usług domyślne to S0. O cenach, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* **Usługa Azure Service Bus**: Azure Service Bus jest koordynacji pracy w ramach usługi w chmurze Azure. Zobacz [usługi magistrali cennik](https://azure.microsoft.com/pricing/details/service-bus/).
* **Magazyn Azure**: konta usługi Magazyn Azure jest używana do przechowywania, dane wyjściowe diagnostyki rejestrowania w przypadku, gdy problem wymaga dalszych debugowania (zobacz [Włączanie diagnostyki w usług Azure Cloud Services i maszyn wirtualnych](../cloud-services/cloud-services-dotnet-diagnostics.md)). O cenach, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Jak działają zadania elastycznej bazy danych
1. Baza danych SQL Azure jest wyznaczony **bazy danych kontroli** który przechowuje wszystkie dane meta dane i stan.
2. Jest dostęp do bazy danych kontroli **zadania usługi** do uruchomienia i śledzenia zadań do wykonania.
3. Dwa różne role komunikują się z bazą danych sterowania: 
   * Kontroler: Określa zadania, które wymagają zadania do wykonania żądanej zadań i zadań zakończonych niepowodzeniem prób przez utworzenie nowego zadania.
   * Wykonywanie zadań dla zadania: Wykonuje zadania.

### <a name="job-task-types"></a>Typy zadań zadania
Istnieje wiele typów zadania, wykonujących wykonywanie zadań:

* ShardMapRefresh: Mapy niezależnego fragmentu, aby określić wszystkie bazy danych używane jako niezależne odpytuje
* ScriptSplit: Zostaje skrypt między "Przejdź" instrukcji w partii
* ExpandJob: Tworzy podrzędnych zadania dla każdej bazy danych z zadania przeznaczonego dla grupy baz danych
* ScriptExecution: Skrypt dla określonej bazy danych przy użyciu określonych poświadczeń wykonuje
* Dacpac: Dotyczy określonej bazy danych przy użyciu danego poświadczenia DACPAC

## <a name="end-to-end-job-execution-work-flow"></a>Zadanie end-to-end wykonywania z przepływu pracy
1. Za pomocą portalu lub interfejs API środowiska PowerShell, zadania są wstawiane do **bazy danych kontroli**. Zadanie żąda wykonania skryptu języka Transact-SQL w odniesieniu do grupy baz danych przy użyciu określonych poświadczeń.
2. Kontroler identyfikuje nowe zadanie. Zadania są tworzone i wykonać, aby podzielić skryptu i do odświeżania bazy danych grupy. Ponadto nowe zadanie jest tworzone i wykonywany w celu rozwiń zadanie i utworzyć nowy element podrzędny określoną każdego zadania podrzędne do uruchomienia skryptu języka Transact-SQL w poszczególnych bazy danych w grupie zadań.
3. Kontroler identyfikuje zadania podrzędne utworzony. Dla każdego zadania kontrolera tworzy i uruchamia zadanie, można wykonać skryptu w bazie danych. 
4. Po zakończeniu wszystkich zadań zadania, kontrolera aktualizacje stanu ukończenia zadania. 
   W dowolnym momencie podczas wykonywania zadania interfejs API środowiska PowerShell pozwala wyświetlać bieżący stan wykonywania zadania. Wszystkie godziny zwracana przez interfejsy API programu PowerShell są reprezentowane w formacie UTC. W razie potrzeby można zatrzymać zadania można zainicjować żądanie anulowania. 

## <a name="next-steps"></a>Kolejne kroki
[Zainstaluj składniki](sql-database-elastic-jobs-service-installation.md), następnie [Utwórz i Dodaj dziennika w każdej bazie danych w grupie baz danych](sql-database-manage-logins.md). Aby jeszcze lepiej zrozumieć Tworzenie zadania i zarządzanie nimi, zobacz [tworzenie i zarządzanie nimi zadania elastycznej bazy danych](sql-database-elastic-jobs-create-and-manage.md). Zobacz też [wprowadzenie zadania elastycznej bazy danych](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->


