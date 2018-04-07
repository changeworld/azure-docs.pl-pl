---
title: Transakcje rozproszone w bazach danych w chmurze
description: Omówienie transakcji elastycznej bazy danych z bazy danych Azure SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.topic: article
ms.custom: scale out apps
ms.workload: On Demand
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 37de2a6918012707ae171415ac8f043d6f9cb1ae
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transakcje rozproszone w bazach danych w chmurze
Transakcji elastycznej bazy danych dla usługi Azure SQL Database (baza danych SQL) pozwala na uruchamianie transakcje obejmujące wiele baz danych w bazie danych SQL. Transakcji elastycznej bazy danych dla bazy danych SQL są dostępne dla aplikacji .NET przy użyciu ADO .NET oraz integrują się z znanych programowania środowisko przy użyciu [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) klasy. Aby pobrać bibliotekę, zobacz [.NET Framework 4.6.1 (Instalator internetowy)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalnie takiej sytuacji są zwykle wymagane systemem koordynatora transakcji rozproszonych (MSDTC). Ponieważ usługa MSDTC nie jest dostępna dla aplikacji platformy jako usługa na platformie Azure, może koordynować transakcje rozproszone teraz zostało bezpośrednio zintegrowane bazy danych SQL. Aplikacje mogą łączyć się z dowolną bazą danych SQL, można uruchomić transakcji rozproszonych, a jedna baza danych niewidocznie będzie koordynować z transakcji rozproszonych, jak pokazano na poniższej ilustracji. 

  ![Transakcje rozproszone w usłudze Azure SQL Database przy użyciu transakcji elastycznej bazy danych ][1]

## <a name="common-scenarios"></a>Typowe scenariusze
Transakcji elastycznej bazy danych dla bazy danych SQL umożliwiają aplikacjom atomic zmiany danych przechowywanych w wielu różnych baz danych SQL. Wersja zapoznawcza koncentruje się na procesy programowanie po stronie klienta w C# i platformy .NET. Czynności po stronie serwera, za pomocą T-SQL jest zaplanowane na później.  
Transakcji elastycznej bazy danych jest przeznaczony dla następujących scenariuszy:

* Bazy danych wielu aplikacji na platformie Azure: W tym scenariuszu danych jest pionowo podzielonym na partycje w kilku baz danych w bazie danych SQL tak, aby różne rodzaje danych znajdują się na różnych baz danych. Niektóre operacje wymagają zmiany danych, który jest przechowywany w dwóch lub więcej baz danych. Aplikacja używa transakcji elastycznej bazy danych do koordynowania zmiany dla baz danych i zapewnienia niepodzielność.
* Aplikacje bazy danych podzielonej na platformie Azure: W tym scenariuszu warstwy danych użyto [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) lub podanie samoobsługowego na poziomie partycji danych przez wiele baz danych w bazie danych SQL. Jeden przypadek użycia poświęcone jest potrzeba dokonać zmian atomic dla podzielonej aplikacji wielodostępnych podczas zmiany span dzierżaw. Traktować dla wystąpienia transfer z jednej dzierżawy do innej, zarówno znajdującej się na różnych baz danych. Drugim przypadku jest szczegółowych dzielenia na fragmenty do uwzględnienia wymagań wydajności dla dużych dzierżawy, co z kolei zwykle oznacza, że niektóre operacje atomic musi rozciągnąć na kilka bazy danych używane do tej samej dzierżawy. Trzeci zdarza się atomic aktualizacje odwołują się dane, które są replikowane między bazami danych. Niepodzielne, transakcyjne, operacje na te wiersze można teraz koordynowane przez wiele baz danych przy użyciu wersji zapoznawczej.
  Transakcji elastycznej bazy danych użyj dwufazowe w celu zapewnienia niepodzielności transakcji dla baz danych. Należy dobrze dla transakcji, które obejmują mniej niż 100 baz danych w czasie w ramach jednej transakcji. Ograniczenia te nie są wymuszane, ale jeden oczekiwać wydajności i odsetka pomyślnych dla transakcji elastycznej bazy danych to obniżenie po przekroczeniu tych limitów.

## <a name="installation-and-migration"></a>Instalacja i migracji
Za pomocą funkcji Aktualizacje do bibliotek .NET System.Data.dll i Biblioteka System.Transactions.dll dostępne stają się funkcje dla transakcji elastycznej bazy danych w bazie danych SQL. Biblioteki DLL upewnij się, że w tym dwufazowe jest używany w przypadku, gdy jest to niezbędne do zapewnienia niepodzielność. Aby rozpocząć tworzenie aplikacji przy użyciu transakcji elastycznej bazy danych, należy zainstalować [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) lub nowszym. Podczas uruchamiania w starszej wersji programu .NET framework, transakcje zakończy się niepowodzeniem podwyższyć poziom do transakcji rozproszonej i zostanie zgłoszony wyjątek.

Po instalacji można transakcji rozproszonej interfejsów API w System.Transactions z połączeniami z bazą danych SQL. Jeśli masz istniejące aplikacje usługi MSDTC za pomocą tych interfejsów API, po zainstalowaniu wersji 4.6.1 po prostu odbudować istniejące aplikacje dla .NET 4.6 Framework. Projekty skierowania .NET 4.6, automatycznie używają zaktualizowanej biblioteki dll z nowej wersji Framework i transakcji rozproszonej, którą wywołania interfejsu API w połączeniu z połączeniami z bazą danych SQL teraz powiedzie się.

Należy pamiętać, że transakcji elastycznej bazy danych nie wymagają instalowania usługi MSDTC. Zamiast tego transakcji elastycznej bazy danych są zarządzane bezpośrednio przez i w obrębie bazy danych SQL. To znacznie upraszcza scenariusze chmury, ponieważ wdrożenie usługi MSDTC nie jest konieczne do korzystania z bazy danych SQL z transakcji rozproszonych. 4 z sekcji omówiono bardziej szczegółowo wdrażanie transakcji elastycznej bazy danych i .NET framework wymagana wraz z aplikacji w chmurze na platformie Azure.

## <a name="development-experience"></a>Środowisko programistyczne
### <a name="multi-database-applications"></a>Aplikacje wielu baz danych
Następujący przykładowy kod używa środowisko programowania zapoznać się z .NET System.Transactions. Klasa elementu TransactionScope ustanawia transakcja otoczenia w .NET. ("Transakcja otoczenia" jest taki, który znajduje się w bieżącym wątku). Wszystkie połączenia otworzyć wewnątrz elementu TransactionScope uczestniczyć w transakcji. Jeśli udział różnych baz danych, transakcji jest automatycznie podwyższony do transakcji rozproszonej. Wynik transakcji jest kontrolowany przez ustawienie zakresu wykonać, aby wskazać na zatwierdzenie.

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

### <a name="sharded-database-applications"></a>Aplikacje baz danych podzielonej
Transakcji elastycznej bazy danych dla bazy danych SQL obsługuje również koordynowanie transakcji rozproszonych, gdy używana jest metoda OpenConnectionForKey biblioteki klienta elastycznej bazy danych można otworzyć połączenia danych skalowanej warstwy. Należy wziąć pod uwagę przypadków, w których należy zagwarantować spójności transakcyjnej zmiany na kilka różnych dzielenia na fragmenty wartości klucza. Połączenia odłamków hosting dzielenia na fragmenty różnych wartości klucza są obsługiwane przez brokera przy użyciu OpenConnectionForKey. W przypadku ogólnych połączenia może być inny odłamków tak, aby zapewnienie transakcyjne gwarancje wymaga transakcji rozproszonej. Poniższy przykładowy kod przedstawia tej metody. Zakłada się, że zmienna o nazwie shardmap jest używana do reprezentowania mapy niezależnego fragmentu z biblioteki klienta elastycznej bazy danych:

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


## <a name="net-installation-for-azure-cloud-services"></a>Instalacja środowiska .NET dla usługi w chmurze Azure
Platforma Azure udostępnia kilka ofert umożliwia obsługę aplikacji programu .NET. Porównanie różnych ofert jest dostępna w [porównanie usługi Azure App Service, usługi w chmurze i maszyn wirtualnych](../app-service/choose-web-site-cloud-service-vm.md). Jeśli system operacyjny gościa oferty jest mniejsza niż .NET 4.6.1 wymagane dla transakcji elastycznej, należy uaktualnić system operacyjny gościa do wersji 4.6.1. 

Usługi aplikacji Azure z systemem operacyjnym gościa obecnie nie są obsługiwane uaktualnienia. Maszyn wirtualnych platformy Azure, wystarczy zalogować się do maszyny Wirtualnej i uruchom Instalatora programu .NET framework najnowsze. Usługi w chmurze Azure muszą obejmować instalację nowszej wersji platformy .NET do uruchamiania zadań wdrożenia. Pojęcia i kroki opisano w [zainstalować .NET dla roli usługi chmury](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Należy pamiętać, Instalator programu .NET 4.6.1 może wymagać więcej magazyn tymczasowy podczas procesu bootstrapping na usług w chmurze Azure niż Instalator dla .NET 4.6. W celu zapewnienia pomyślnej instalacji, należy zwiększyć tymczasowego magazynu dla usługi w chmurze platformy Azure w pliku ServiceDefinition.csdef w sekcji LocalResources i ustawienia środowiska uruchamiania zadania, jak pokazano w poniższym przykładzie:

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
Transakcji elastycznej bazy danych są obsługiwane na różnych serwerach logicznych w bazie danych SQL Azure. Gdy transakcje przekraczają granice serwer logiczny, serwerów najpierw należy można wprowadzić w relacji wzajemnego komunikacji. Po ustanowieniu relacji komunikacji, wszystkie bazy danych w jednym z dwóch serwerów mogą uczestniczyć w transakcji elastycznej z bazami danych z innego serwera. Z transakcjami obejmujące więcej niż dwa serwery logiczne relację komunikacji musi być w miejscu dla każdej pary serwerów logicznych.

Użyj następujących poleceń cmdlet programu PowerShell do zarządzania relacjami między serwerami komunikacji dla transakcji elastycznej bazy danych:

* **Nowy AzureRmSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby utworzyć nową relację komunikacji między dwoma serwerami logicznych w bazie danych SQL Azure. Relacja jest symetrycznego, co oznacza, że oba serwery mogą inicjować transakcji z innego serwera.
* **Get-AzureRmSqlServerCommunicationLink**: Użyj tego polecenia cmdlet można pobrać istniejące relacje komunikacji oraz ich właściwości.
* **Usuń AzureRmSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby usunąć istniejącą relację, która komunikacji. 

## <a name="monitoring-transaction-status"></a>Monitorowanie stanu transakcji
Użyj dynamicznych widoków zarządzania (widoków DMV) w bazie danych SQL do monitorowania stanu i postępu transakcji trwającą elastycznej bazy danych. Wszystkie widoków DMV powiązane z transakcjami są odpowiednie dla transakcji rozproszonych w bazie danych SQL. Można znaleźć odpowiedniej liście widoków DMV tutaj: [powiązane dynamicznych widoków zarządzania transakcji i funkcji (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Tych widoków DMV jest szczególnie przydatna:

* **sys.DM\_tran\_active\_transakcji**: Wyświetla listę obecnie aktywnych transakcji i ich stan. Kolumna UOW (jednostka pracy) można zidentyfikować transakcji inny podrzędny, które należą do tej samej transakcji rozproszonej. Wszystkie transakcje w ramach jednej transakcji rozproszonej posiadać taką samą wartość UOW. Zobacz [dokumentacji DMV](https://msdn.microsoft.com/library/ms174302.aspx) Aby uzyskać więcej informacji.
* **sys.DM\_tran\_bazy danych\_transakcji**: udostępnia dodatkowe informacje o transakcji, takich jak umieszczania transakcji w dzienniku. Zobacz [dokumentacji DMV](https://msdn.microsoft.com/library/ms186957.aspx) Aby uzyskać więcej informacji.
* **sys.DM\_tran\_blokad**: zawiera informacje o blokad, które są obecnie przechowywane w bieżących transakcji. Zobacz [dokumentacji DMV](https://msdn.microsoft.com/library/ms190345.aspx) Aby uzyskać więcej informacji.

## <a name="limitations"></a>Ograniczenia
Do transakcji elastycznej bazy danych w bazie danych SQL obecnie obowiązują następujące ograniczenia:

* Obsługiwane są tylko transakcje dla baz danych w bazie danych SQL. Inne [X / Otwórz XA](https://en.wikipedia.org/wiki/X/Open_XA) dostawców zasobów i baz danych poza bazy danych SQL nie może brać udziału w transakcji elastycznej bazy danych. Oznacza to, że transakcji elastycznej bazy danych nie można rozciągnąć na lokalnie serwer SQL i baz danych SQL Azure. Transakcji rozproszonych lokalnie nadal używać usługi MSDTC. 
* Tylko koordynowane klienta transakcje z aplikacji .NET są obsługiwane. Obsługa po stronie serwera T-SQL, takich jak rozpocząć transakcji ROZPROSZONYCH jest planowane, ale nie jest jeszcze dostępna. 
* Transakcje w usługach WCF nie są obsługiwane. Na przykład masz metody usługi WCF, która wykonuje transakcji. Otaczający wywołanie w zakresie transakcji nie powiedzie się jako [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Kolejne kroki
Odpowiedzi na pytania, proszę dotrzeć do nas na [forum bazy danych SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) i funkcja żądań, dodaj je do [forum opinii bazy danych SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



