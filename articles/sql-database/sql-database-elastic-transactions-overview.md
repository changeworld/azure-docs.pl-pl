---
title: Transakcje rozproszone w bazach danych w chmurze
description: Przegląd transakcji Elastic Database usługi Azure SQL Database
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
ms.date: 03/12/2019
ms.openlocfilehash: d7865d394dfc955a7b24115e747dd77352d89e3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331902"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transakcje rozproszone w bazach danych w chmurze

Transakcje elastyczne bazy danych usługi Azure SQL Database (baza danych SQL) umożliwiają uruchamianie transakcje obejmujące wiele baz danych w bazie danych SQL. Transakcji elastycznych baz danych dla bazy danych SQL są dostępne dla aplikacji .NET za pomocą ADO .NET oraz integracji z dobrze znanych programowania środowisko przy użyciu [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) klasy. Aby uzyskać biblioteki, zobacz [platformy .NET Framework 4.6.1 (Instalator internetowy)](https://www.microsoft.com/download/details.aspx?id=49981).

W środowisku lokalnym takiej sytuacji są zwykle wymagane uruchamianie transakcji Koordynator MSDTC (Microsoft Distributed). Ponieważ MSDTC nie jest dostępna dla aplikacji platformy jako usługi na platformie Azure, możliwość koordynować transakcje rozproszone ma teraz bezpośrednio zintegrowany do bazy danych SQL. Aplikacje można połączyć z dowolną bazą danych SQL, aby uruchomić transakcji rozproszonych i jedna baza danych sposób niewidoczny dla użytkownika będzie koordynować z transakcji rozproszonych, jak pokazano na poniższej ilustracji. 

  ![Transakcje rozproszone w usłudze Azure SQL Database za pomocą transakcji elastycznych baz danych ][1]

## <a name="common-scenarios"></a>Typowe scenariusze

Transakcji elastycznych baz danych dla bazy danych SQL umożliwiają aplikacjom Atomowej zmiany danych przechowywanych w wielu różnych baz danych SQL. (Wersja zapoznawcza) koncentruje się na środowisko programowania po stronie klienta w języku C# i .NET. Środowisko po stronie serwera, za pomocą języka T-SQL jest planowana na później.  
Transakcje elastyczne bazy danych jest przeznaczony dla następujących scenariuszy:

* Aplikacje wielu baz danych na platformie Azure: W tym scenariuszu w pionie partycjonowania danych w wielu bazach danych w bazie danych SQL tak, aby różne rodzaje danych znajdują się w różnych bazach danych. Niektóre operacje wymagają zmiany danych, który jest przechowywany w dwóch lub więcej baz danych. Aplikacja używa transakcji elastycznych baz danych koordynować zmian w bazach danych i zapewnienie niepodzielność.
* Aplikacje bazy danych podzielonej na fragmenty na platformie Azure: W tym scenariuszu warstwy danych użyto [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md) lub self dzielenie na fragmenty na poziomie podzielenia danych w wielu bazach danych w bazie danych SQL. Jeden przypadek użycia wyraźną jest konieczność przeprowadzenia Atomowej zmiany podzielonej na fragmenty aplikację wielodostępną podczas zmiany span dzierżaw. Można traktować, na przykład transfer z jednej dzierżawy do innego, zarówno znajdujących się w różnych bazach danych. Drugi przypadek jest precyzyjny fragmentowanie spełniają wymagania pojemności dla dużych dzierżawy, który z kolei zazwyczaj wskazuje, że niektóre operacje niepodzielne musi rozciągnąć na kilka baz danych, używane do tej samej dzierżawy. Trzeci wielkość liter jest atomic aktualizacje odwołują się do danych, które są replikowane w bazach danych. Operacje atomowe i transakcyjnych, wzdłuż tych wierszy teraz mogą być koordynowane między kilka baz danych przy użyciu wersji zapoznawczej.
  Transakcje elastyczne bazy danych używają dwufazowego, aby zapewnić niepodzielność transakcji w bazach danych. Jest dobrym rozwiązaniem dla transakcji, które obejmują mniej niż 100 baz danych w czasie w ramach jednej transakcji. Te limity nie są wymuszane, ale jeden należy się spodziewać, wydajności i odsetka pomyślnych dla transakcji elastycznych baz danych na po przekroczeniu limitów.

## <a name="installation-and-migration"></a>Instalacja i migracji

Możliwości dotyczące transakcji elastycznych baz danych w bazie danych SQL są realizowane za pośrednictwem aktualizacji do bibliotek .NET, System.Data.dll i System.Transactions.dll. Biblioteki DLL upewnij się, że ten dwufazowego jest używana w przypadku, gdy jest to niezbędne do zapewnienia niepodzielność. Aby rozpocząć tworzenie aplikacji za pomocą transakcji elastycznych baz danych, należy zainstalować [platformy .NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) lub nowszej. Podczas uruchamiania w starszej wersji programu .NET framework, transakcje zakończy się niepowodzeniem podwyższyć poziom do transakcji rozproszonych i będzie zgłaszany wyjątek.

Po zakończeniu instalacji można transakcji rozproszonej interfejsów API w System.Transactions z połączeniami z bazą danych SQL. Jeśli masz istniejące aplikacje usługi MSDTC, za pomocą tych interfejsów API, po prostu ponownie skompilować istniejących aplikacji dla platformy .NET 4.6 po zainstalowaniu 4.6.1 Framework. Jeśli Twoje projekty ukierunkowane na .NET 4.6, automatycznie użyje zaktualizowanych bibliotek DLL z nowej wersji Framework i transakcji rozproszonej, wywołań interfejsów API w połączeniu z połączenia z bazą danych SQL teraz powiedzie się.

Należy pamiętać, że transakcji elastycznych baz danych nie wymagają instalowania usługi MSDTC. Zamiast tego transakcji elastycznych baz danych są zarządzane bezpośrednio przez i w ramach bazy danych SQL. To znacznie upraszcza scenariusze chmury, ponieważ wdrożenie usługi MSDTC nie jest konieczne użycie transakcje rozproszone z bazą danych SQL. Sekcja 4 bardziej szczegółowo opisano sposób wdrażania transakcji elastycznych baz danych i wymaganych .NET framework razem z aplikacji w chmurze na platformie Azure.

## <a name="development-experience"></a>Środowisko programistyczne

### <a name="multi-database-applications"></a>Aplikacje dla wielu baz danych

Następujący przykładowy kod używa znanego środowiska programowania z .NET System.Transactions. Klasa elementu TransactionScope ustanawia otoczenia transakcji na platformie .NET. ("Otoczenia transakcji" jest taki, który znajduje się w bieżącym wątku). Wszystkie połączenia otwarte w obrębie elementu TransactionScope uczestniczyć w transakcji. Jeśli uczestniczyć różnych baz danych, transakcji jest automatycznie awansowani do poziomu transakcji rozproszonej. Wyniku transakcji jest kontrolowana przez ustawienie zakresu, który należy wykonać, aby wskazać zatwierdzenia.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Aplikacje baz danych podzielonych na fragmenty

Transakcji elastycznych baz danych dla bazy danych SQL obsługują także koordynacji transakcji rozproszonych, w której używana jest metoda OpenConnectionForKey Biblioteka kliencka elastic database do otwierania połączeń danych skalowanych w poziomie warstwy. Należy wziąć pod uwagę sytuacjach wymagających zagwarantowania spójności transakcyjnej dla zmian w kilku różnych fragmentowania wartości klucza. Połączenia do fragmentów hostingu dzielenia na fragmenty różnych wartości klucza są obsługiwane przez brokera przy użyciu OpenConnectionForKey. W przypadku ogólnych połączenia może być do różnych fragmentów taki sposób, że zapewnienie transakcyjnych gwarancje wymaga transakcji rozproszonej. To podejście pokazano w następującym przykładzie kodu. Przyjęto założenie, że zmienną o nazwie shardmap jest używana do reprezentowania mapowania fragmentów w postaci z biblioteki klienta elastycznej bazy danych:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Instalacja platformy .NET dla usług Azure Cloud Services

Platforma Azure udostępnia kilka oferty do hostowania aplikacji .NET. Porównanie różnych ofert jest dostępna w [porównanie usługi Azure App Service, Cloud Services i Virtual Machines](../app-service/overview-compare.md). Jeśli system operacyjny gościa oferty jest mniejszy niż .NET 4.6.1 wymaganych dla elastycznej transakcji, należy uaktualnić system operacyjny gościa do 4.6.1. 

Dla usług Azure App Services uaktualnienia do systemu operacyjnego gościa nie są obecnie obsługiwane. Usługi Azure Virtual Machines po prostu Zaloguj się do maszyny Wirtualnej i uruchom Instalatora programu najnowszy program .NET framework. Usług Azure Cloud Services muszą obejmować instalację nowszej wersji platformy .NET do zadania uruchamiania wdrożenia. Koncepcje i kroki są udokumentowane w artykule [Instalowanie programu .NET w roli usługi w chmurze](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Należy pamiętać, Instalator programu .NET 4.6.1 może wymagać więcej magazynu tymczasowego podczas procesu bootstrapping w usługach Azure cloud services niż Instalator dla .NET 4.6. W celu zapewnienia pomyślnej instalacji, należy zwiększyć magazynu tymczasowego dla usługi w chmurze platformy Azure w pliku ServiceDefinition.csdef w sekcji LocalResources i ustawień środowiska uruchamiania zadania, jak pokazano w następującym przykładzie:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>

## <a name="transactions-across-multiple-servers"></a>Transakcje na wielu serwerach

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Transakcje elastyczne bazy danych są obsługiwane na różnych serwerach bazy danych SQL w usłudze Azure SQL Database. Gdy transakcji przekraczają granice serwera bazy danych SQL, uczestniczących serwerów najpierw należy wprowadzić w relacji wzajemnego komunikacji. Po ustanowieniu relacji komunikacji, dowolnej bazy danych w jednym z dwóch serwerów mogą uczestniczyć w transakcji elastycznej bazy danych z innego serwera. Za pomocą transakcji obejmujące więcej niż dwa serwery baz danych SQL relacji komunikacji musi być w miejscu, aby jakaś para serwery usługi SQL Database.

Użyj następujących poleceń cmdlet programu PowerShell do zarządzania relacjami między serwerami komunikacji dla transakcji elastycznych baz danych:

* **New-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby utworzyć nową relację komunikacji między dwoma serwerami bazy danych SQL w usłudze Azure SQL Database. Relacja jest symetryczne, co oznacza, że oba serwery mogą inicjować transakcji z innym serwerem.
* **Get-AzSqlServerCommunicationLink**: Użyj następującego polecenia cmdlet, aby pobrać istniejące relacje komunikacyjne i ich właściwości.
* **Remove-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby usunąć istniejącą relację, która komunikacji. 

## <a name="monitoring-transaction-status"></a>Monitorowanie stanu transakcji

Użyj dynamicznych widoków zarządzania (DMV) w bazie danych SQL do Monitora stanu i postępu transakcji trwającej elastycznej bazy danych. Wszystkich dynamicznych widoków zarządzania, które są powiązane z transakcjami są odpowiednie dla transakcji rozproszonych w bazie danych SQL. Można znaleźć odpowiedniego listy dynamicznych widoków zarządzania tutaj: [Transakcji związanych z dynamicznych widoków zarządzania i funkcje (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Tych widokach DMV są szczególnie przydatne:

* **sys.dm\_tran\_active\_transactions**: Wyświetla listę aktualnie aktywnych transakcji i ich stan. Kolumna UOW (jednostki pracy), można zidentyfikować transakcji inny podrzędny, które należą do tej samej transakcji rozproszonych. Wszystkie transakcje w ramach jednej transakcji rozproszonej wykonuje tę samą wartość jednostką pracy. Zobacz [dokumentacji DMV](https://msdn.microsoft.com/library/ms174302.aspx) Aby uzyskać więcej informacji.
* **sys.dm\_tran\_database\_transactions**: Zawiera dodatkowe informacje o transakcjach, takich jak położenie transakcji w dzienniku. Zobacz [dokumentacji DMV](https://msdn.microsoft.com/library/ms186957.aspx) Aby uzyskać więcej informacji.
* **sys.dm\_tran\_locks**: Zawiera informacje dotyczące blokad, które są obecnie nakładane przez trwające transakcje. Zobacz [dokumentacji DMV](https://msdn.microsoft.com/library/ms190345.aspx) Aby uzyskać więcej informacji.

## <a name="limitations"></a>Ograniczenia

Poniższe ograniczenia mają zastosowanie obecnie do transakcji elastycznych baz danych w bazie danych SQL:

* Obsługiwane są tylko transakcje w bazach danych w bazie danych SQL. Inne [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) dostawcy zasobów i ich baz danych poza bazą danych SQL nie może uczestniczyć w transakcji elastycznych baz danych. Oznacza to, że transakcji elastycznych baz danych nie można rozciągnąć na lokalne programu SQL Server i usługi Azure SQL Database. Transakcje rozproszone w środowisku lokalnym nadal używać usługi MSDTC. 
* Obsługiwane są tylko koordynowany klienta transakcji z poziomu aplikacji .NET. Po stronie serwera Obsługa języka T-SQL, takich jak rozpocząć transakcji ROZPROSZONEJ jest planowane, ale nie jest jeszcze dostępna. 
* Transakcji w przypadku usług WCF nie są obsługiwane. Na przykład masz metody usługi WCF, który jest wykonywany transakcji. Otaczający wywołanie w zakresie transakcji nie powiedzie się jako [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Kolejne kroki

Masz pytania, skontaktuj się z nami na [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) i dla żądania funkcji, należy je dodać do [forum z opiniami bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
