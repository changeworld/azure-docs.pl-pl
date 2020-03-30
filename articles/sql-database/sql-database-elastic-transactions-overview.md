---
title: Transakcje rozproszone w bazach danych w chmurze
description: Omówienie transakcji elastycznej bazy danych za pomocą bazy danych SQL azure
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68568279"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Transakcje rozproszone w bazach danych w chmurze

Transakcje elastycznej bazy danych dla usługi Azure SQL Database (SQL DB) umożliwiają uruchamianie transakcji obejmujących kilka baz danych w bazie danych SQL. Transakcje elastycznej bazy danych dla bazy danych SQL DB są dostępne dla aplikacji .NET przy użyciu ADO .NET i integrują się ze znanym doświadczeniem programowania przy użyciu klas [System.Transaction.](https://msdn.microsoft.com/library/system.transactions.aspx) Aby uzyskać bibliotekę, zobacz [.NET Framework 4.6.1 (Instalator sieci Web)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalnie taki scenariusz zwykle wymagane uruchamianie koordynatora transakcji rozproszonych firmy Microsoft (MSDTC). Ponieważ usługa MSDTC nie jest dostępna dla aplikacji Platformy jako usługi na platformie Azure, możliwość koordynowania transakcji rozproszonych została teraz bezpośrednio zintegrowana z bazą danych SQL. Aplikacje mogą łączyć się z dowolnej bazy danych SQL, aby uruchomić transakcje rozproszone, a jedna z baz danych będzie w sposób przejrzysty koordynować transakcji rozproszonych, jak pokazano na poniższym rysunku. 

  ![Transakcje rozproszone z usługą Azure SQL Database przy użyciu transakcji elastycznej bazy danych ][1]

## <a name="common-scenarios"></a>Typowe scenariusze

Transakcje elastycznej bazy danych dla bazy danych SQL umożliwiają aplikacjom wprowadzanie zmian atomowych w danych przechowywanych w kilku różnych bazach danych SQL. Wersja zapoznawcza koncentruje się na środowiskach programistycznych po stronie klienta w językach C# i .NET. Środowisko po stronie serwera przy użyciu języka T-SQL jest planowane na późniejszy czas.  
Transakcje elastycznej bazy danych są przeznaczone dla następujących scenariuszy:

* Aplikacje z wieloma bazami danych na platformie Azure: w tym scenariuszu dane są przesyłane pionowo między kilka baz danych w bazie danych SQL, tak aby różne rodzaje danych znajdują się w różnych bazach danych. Niektóre operacje wymagają zmian w danych, które są przechowywane w dwóch lub więcej baz danych. Aplikacja używa transakcji elastycznej bazy danych do koordynowania zmian w bazach danych i zapewnienia niepodzielności.
* Aplikacje bazy danych podzielonej na s podzielonej na partycje na platformie Azure: w tym scenariuszu warstwa danych używa [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) lub samorozdzielania do poziomej partycjonowania danych w wielu bazach danych w bazie danych SQL. Jednym z widocznych przypadków użycia jest konieczność wykonywania zmian niepodzielnych dla podzielonej aplikacji wielodostępnych, gdy zmiany obejmują dzierżawców. Pomyśl na przykład o transferze z jednej dzierżawy do drugiej, oba miejsca w różnych bazach danych. Drugi przypadek jest szczegółowe dzielenia na fragmenty, aby zaspokoić potrzeby pojemności dla dużej dzierżawy, co z kolei zazwyczaj oznacza, że niektóre operacje niepodzielne musi rozciągać się na kilka baz danych używanych dla tej samej dzierżawy. Trzeci przypadek jest niepodzielne aktualizacje danych referencyjnych, które są replikowane w bazach danych. Atomic, transacted, operacje wzdłuż tych linii mogą być teraz koordynowane w kilku bazach danych przy użyciu wersji zapoznawczej.
  Transakcje elastycznej bazy danych używają zatwierdzania dwufazowego, aby zapewnić niepodzielność transakcji między bazami danych. Jest to dobre dopasowanie do transakcji, które obejmują mniej niż 100 baz danych naraz w ramach jednej transakcji. Limity te nie są wymuszane, ale należy się spodziewać wydajności i wskaźników powodzenia dla transakcji elastycznej bazy danych, aby cierpieć po przekroczeniu tych limitów.

## <a name="installation-and-migration"></a>Instalacja i migracja

Możliwości transakcji elastycznej bazy danych w bazie danych SQL są dostarczane za pośrednictwem aktualizacji bibliotek .NET System.Data.dll i System.Transactions.dll. Biblioteki DLL zapewniają, że zatwierdzanie dwufazowe jest używane w razie potrzeby w celu zapewnienia niepodzielności. Aby rozpocząć tworzenie aplikacji przy użyciu transakcji elastycznej bazy danych, należy zainstalować [program .NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) lub nowszą wersję. Po uruchomieniu na wcześniejszej wersji platformy .NET, transakcje nie będą podwyższać do transakcji rozproszonych i wyjątek zostanie zgłoszony.

Po instalacji można użyć rozproszonych interfejsów API transakcji w system.Transactions z połączeniami z bazą danych SQL. Jeśli masz istniejące aplikacje MSDTC przy użyciu tych interfejsów API, po prostu odbuduj istniejące aplikacje dla platformy .NET 4.6 po zainstalowaniu struktury 4.6.1. Jeśli twoje projekty docelowe .NET 4.6, będą automatycznie używać zaktualizowanych bibliotek DLL z nowej wersji frameworka i rozproszonych wywołań interfejsu API transakcji w połączeniu z połączeniami z bazy danych SQL zakończy się pomyślnie.

Należy pamiętać, że transakcje elastycznej bazy danych nie wymagają instalowania usługi MSDTC. Zamiast tego transakcje elastycznej bazy danych są zarządzane bezpośrednio przez i w ramach bazy danych SQL. To znacznie upraszcza scenariusze chmury, ponieważ wdrożenie usługi MSDTC nie jest konieczne do używania transakcji rozproszonych z bazą danych SQL. W sekcji 4 opisano bardziej szczegółowo, jak wdrożyć transakcje elastycznej bazy danych i wymaganą platformę .NET wraz z aplikacjami w chmurze na platformie Azure.

## <a name="development-experience"></a>Doświadczenie w rozwoju

### <a name="multi-database-applications"></a>Aplikacje z wieloma bazami danych

Poniższy przykładowy kod używa znanego środowiska programowania z .NET System.Transactions. TransactionScope Klasa ustanawia transakcji otoczenia w .NET. ("Transakcja otoczenia" to transakcja, która działa w bieżącym wątku.) Wszystkie połączenia otwarte w ramach TransactionScope uczestniczyć w transakcji. Jeśli uczestniczą różne bazy danych, transakcja jest automatycznie podwyższone do transakcji rozproszonej. Wynik transakcji jest kontrolowany przez ustawienie zakresu, aby zakończyć, aby wskazać zatwierdzenie.

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

### <a name="sharded-database-applications"></a>Aplikacje bazy danych podzielonej na fragmenty

Transakcje elastycznej bazy danych dla bazy danych SQL DB obsługują również koordynowanie transakcji rozproszonych, w przypadku których używana jest metoda OpenConnectionForKey biblioteki klienta elastycznej bazy danych do otwierania połączeń dla warstwy danych skalowanych w poziomie. Należy wziąć pod uwagę przypadki, w których należy zagwarantować spójność transakcyjną dla zmian w kilku różnych wartości klucza dzielenia na fragmenty. Połączenia z fragmentami obsługującymi różne wartości klucza dzielenia na fragmenty są brokerowane przy użyciu funkcji OpenConnectionForKey. W ogólnym przypadku połączenia mogą być do różnych fragmentów, takie jak zapewnienie gwarancji transakcyjnych wymaga transakcji rozproszonej. Poniższy przykład kodu ilustruje to podejście. Przyjęto założenie, że zmienna o nazwie mapy niezależnego fragmentu jest używana do reprezentowania mapy niezależnego fragmentu z biblioteki klienta elastycznej bazy danych:

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


## <a name="net-installation-for-azure-cloud-services"></a>Instalacja platformy .NET dla usług w chmurze platformy Azure

Platforma Azure oferuje kilka ofert do obsługi aplikacji platformy .NET. Porównanie różnych ofert jest dostępne w [porównaniu usługi Azure App Service, usług w chmurze i maszyn wirtualnych.](/azure/architecture/guide/technology-choices/compute-decision-tree) Jeśli system operacyjny gościa oferty jest mniejszy niż .NET 4.6.1 wymagane dla transakcji elastycznych, należy uaktualnić systemu operacyjnego gościa do 4.6.1. 

W przypadku usług Azure App Services uaktualnienia do systemu operacyjnego gościa nie są obecnie obsługiwane. W przypadku maszyn wirtualnych platformy Azure wystarczy zalogować się do maszyny wirtualnej i uruchomić instalator dla najnowszej platformy .NET. W przypadku usług w chmurze azure należy uwzględnić instalację nowszej wersji platformy .NET do zadań uruchamiania wdrożenia. Pojęcia i kroki są udokumentowane w [install .NET w roli usługi w chmurze](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Należy zauważyć, że instalator dla platformy .NET 4.6.1 może wymagać więcej tymczasowego magazynu podczas procesu uruchamiania w usługach w chmurze platformy Azure niż instalator dla platformy .NET 4.6. Aby zapewnić pomyślną instalację, należy zwiększyć tymczasowe miejsce do magazynowania usługi w chmurze platformy Azure w pliku ServiceDefinition.csdef w sekcji LocalResources i ustawieniach środowiska zadania uruchamiania, jak pokazano w poniższym przykładzie:

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
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Transakcje elastycznej bazy danych są obsługiwane na różnych serwerach bazy danych SQL w bazie danych SQL Azure. Gdy transakcje przekraczają granice serwera bazy danych SQL, serwery uczestniczące muszą najpierw zostać wprowadzone w relację wzajemnej komunikacji. Po nawiązaniu relacji komunikacyjnej każda baza danych na dowolnym z dwóch serwerów może uczestniczyć w transakcjach elastycznych z bazami danych z innego serwera. W przypadku transakcji obejmujących więcej niż dwa serwery bazy danych SQL należy nawiązać relację komunikacyjną dla dowolnej pary serwerów bazy danych SQL.

Aby zarządzać relacjami komunikacyjnymi między serwerami dla transakcji elastycznej bazy danych, należy użyć następujących poleceń cmdlet programu PowerShell:

* **New-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby utworzyć nową relację komunikacji między dwoma serwerami bazy danych SQL w bazie danych SQL Azure. Relacja jest symetryczna, co oznacza, że oba serwery mogą inicjować transakcje z innym serwerem.
* **Get-AzSqlServerCommunicationLink:** Użyj tego polecenia cmdlet, aby pobrać istniejące relacje komunikacyjne i ich właściwości.
* **Usuń—AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby usunąć istniejącą relację komunikacyjną. 

## <a name="monitoring-transaction-status"></a>Monitorowanie stanu transakcji

Użyj dynamicznych widoków zarządzania (DMV) w bazie danych SQL, aby monitorować stan i postęp bieżących transakcji elastycznej bazy danych. Wszystkie dmvs związane z transakcjami są istotne dla transakcji rozproszonych w bazy danych SQL. Odpowiednią listę dmv można znaleźć tutaj: [Widoki dynamicznego zarządzania związane z transakcjami i funkcje (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Te dmvs są szczególnie przydatne:

* **sys.dm\_transakcje tran\_\_active**: Wyświetla listę aktualnie aktywnych transakcji i ich statusu. Kolumna UOW (Unit Of Work) może identyfikować różne transakcje podrzędne, które należą do tej samej transakcji rozproszonej. Wszystkie transakcje w ramach tej samej transakcji rozproszonej mają tę samą wartość UOW. Więcej informacji można znaleźć w [dokumentacji DMV.](https://msdn.microsoft.com/library/ms174302.aspx)
* **sys.dm transakcji\_bazy\_\_danych trans:** Zawiera dodatkowe informacje o transakcjach, takie jak umieszczenie transakcji w dzienniku. Więcej informacji można znaleźć w [dokumentacji DMV.](https://msdn.microsoft.com/library/ms186957.aspx)
* **sys.dm\_blokady\_tran:** Zawiera informacje o blokadach, które są obecnie w posiadaniu bieżących transakcji. Więcej informacji można znaleźć w [dokumentacji DMV.](https://msdn.microsoft.com/library/ms190345.aspx)

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia mają obecnie zastosowanie do transakcji elastycznej bazy danych w bazie danych SQL:

* Obsługiwane są tylko transakcje w bazach danych w bazie danych SQL. Inni dostawcy zasobów [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) i bazy danych poza bazą danych SQL nie mogą uczestniczyć w transakcjach elastycznej bazy danych. Oznacza to, że transakcje elastycznej bazy danych nie mogą rozciągać się na lokalnym programie SQL Server i usłudze Azure SQL Database. W przypadku transakcji rozproszonych w środowisku lokalnym należy nadal używać usługi MSDTC. 
* Obsługiwane są tylko transakcje koordynowane przez klienta z aplikacji .NET. Obsługa po stronie serwera dla T-SQL, takich jak BEGIN DISTRIBUTED TRANSACTION jest planowane, ale nie jest jeszcze dostępna. 
* Transakcje w usługach WCF nie są obsługiwane. Na przykład masz WCF metody usługi, która wykonuje transakcję. Załączanie wywołania w zakresie transakcji zakończy się niepowodzeniem jako [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Następne kroki

W przypadku pytań, skontaktuj się z nami na [forum bazy danych SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) i dla żądań funkcji, dodaj je do forum opinii bazy danych [SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
