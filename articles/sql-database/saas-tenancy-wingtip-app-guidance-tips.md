---
title: Wskazówki dotyczące na przykład w aplikacji wielodostępnej bazy danych SQL — SaaS o nazwie Wingtip | Dokumentacja firmy Microsoft
description: Zawiera wskazówki i kroki umożliwiające instalowanie i uruchamianie przykładowej aplikacji wielodostępnych, która korzysta z usługi Azure SQL Database, w przykładzie SaaS o nazwie Wingtip Tickets.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 758cb47760f4a15e262a4d682089ac7d9fee64e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326277"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Ogólne wskazówki dotyczące pracy z Wingtip Tickets przykładowe aplikacje SaaS

Ten artykuł zawiera ogólne wskazówki dotyczące uruchamiania aplikacji SaaS przykładowej aplikacji Wingtip Tickets, korzystających z usługi Azure SQL Database. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Pobierz i odblokować skrypty SaaS o nazwie Wingtip Tickets

Zawartość pliku wykonywalnego (skrypty, biblioteki dll) może być blokowany przez Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębnić. Podczas wyodrębniania skryptów z pliku zip **wykonaj poniższe kroki, aby odblokować plik zip weryfikowany przed wyodrębnianiem**. Dzięki temu można uruchamiać skrypty.

1. Przejdź do repozytorium o nazwie Wingtip Tickets SaaS w usłudze GitHub dla wzorca dzierżawy bazy danych, którą chcesz, aby zapoznać się z: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kliknij przycisk **klonowania lub pobierania**.
3. Kliknij przycisk **Pobierz plik zip** i Zapisz plik.
4. Kliknij prawym przyciskiem myszy plik zip, a następnie wybierz pozycję **właściwości**. Nazwa pliku zip będzie odpowiadać Nazwa repozytorium. (np.) _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na **ogólne** zaznacz **odblokowanie**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty znajdują się w *... \\Learning Modules* folderu.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Praca ze skryptami PowerShell biletów o nazwie Wingtip

Aby maksymalnie wykorzystać możliwości przykładu należy Poznaj dostarczone skrypty. Używanie punktów przerwania i przechodzenie przez skrypty, wykonywanie i zbadać sposobu implementacji różnych wzorców SaaS. Aby łatwo krok po kroku dostarczone skrypty i moduły, które najlepiej zrozumienie, zaleca się używanie [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Należy zaktualizować plik konfiguracji wdrożenia

Edytuj **UserConfig.psm1** pliku zasobów grupy i użytkownika zostanie ustawiona wartość podczas wdrażania:

1. Otwórz *PowerShell ISE* i ładowanie... \\Learning Modules\\*UserConfig.psm1* 
2. Aktualizacja *ResourceGroupName* i *nazwa* z określonymi wartościami dla danego wdrożenia (w wierszach, 10 i 11 tylko).
3. Zapisać zmiany!

Ustawienia te wartości w tym miejscu po prostu wymaga znajomości zaktualizować te wartości specyficznych dla wdrożenia, wszystkie skrypty.

### <a name="execute-the-scripts-by-pressing-f5"></a>Wykonywanie skryptów poprzez naciśnięcie klawisza F5

Szereg skryptów używa *$PSScriptRoot* do przeglądania folderów, a *$PSScriptRoot* jest oceniane tylko, gdy skryptów są wykonywane przez naciśnięcie klawisza **F5**.  Wyróżnianie i uruchomienie go (**F8**) może spowodować błędy, więc naciśnij **F5** podczas uruchamiania skryptów.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Przechodzenie przez skrypty w celu zapoznania się z implementacją

Najlepszym sposobem zrozumienia skryptów polega na przechodzeniu przez ich o pojawieniu się, co robią. Zapoznaj się z dołączonej **Demo -** skrypty, które są dostępne, łatwe naśladowanie ogólny przepływ pracy. **Demo -** skrypty Pokaż kroki wymagane do wykonania poszczególnych zadań, więc ustawić punkty przerwania i przejść głębiej w poszczególne wywołania, aby wyświetlić szczegóły implementacji różnych wzorców SaaS.

Porady dotyczące eksplorowania i Krokowe przechodzenie przez skrypty programu PowerShell:

- Otwórz **Demo -** skrypty w środowisku PowerShell ISE.
- Wykonanie lub kontynuować **F5** (przy użyciu **F8** nie jest zalecane, ponieważ *$PSScriptRoot* nie są sprawdzane podczas uruchamiania skryptu).
- Umieść punkty przerwania, klikając lub zaznaczając wiersz i naciskając klawisz **F9**.
- Pomiń wywołanie funkcji lub skryptu, używając klawisza **F10**.
- Wejdź do wywoływanej funkcji lub skryptu, używając klawisza **F11**.
- Wyjdź z bieżącego wywołania funkcji lub skryptu, używając kombinacji klawiszy **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Badanie schematu bazy danych i wykonywanie zapytań SQL za pomocą aplikacji SSMS

Użyj [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do nawiązywania połączenia i przeglądania serwerów aplikacji i baz danych.

Wdrożenie ma początkowo dzierżaw i serwerów SQL w bazie danych wykazu, aby nawiązać połączenie. Nadawanie nazw serwerów zależy od wzorca dzierżawy bazy danych (zobacz pod kątem specyfiki). 

   - **Aplikacja autonomiczna:** serwerów dla każdego dzierżawcy (np.) *contosoconcerthall -&lt;użytkownika&gt;*  serwera) i *catalog-sa -&lt;użytkownika&gt;*
   - **Bazy danych dla każdego dzierżawcy:** *tenants1-dpt -&lt;użytkownika&gt;*  i *katalogu-dpt -&lt;użytkownika&gt;*  serwerów
   - **Wielodostępną bazą danych:** *tenants1-mt -&lt;użytkownika&gt;*  i *catalog-mt -&lt;użytkownika&gt;*  serwerów

W celu zapewnienia połączenia pokaz pomyślne, wszystkie serwery mają [reguły zapory](sql-database-firewall-configure.md) zezwolenie na wszystkie adresy IP za pośrednictwem.


1. Otwórz *SSMS* i nawiąż połączenie z dzierżawcami. Nazwa serwera zależy od wzorca dzierżawy bazy danych, wybranych (patrz pod kątem specyfiki):
    - **Aplikacja autonomiczna:** serwerów poszczególnych dzierżaw (np.) *contosoconcerthall-&lt;User&gt;.database.windows.net*) 
    - **Bazy danych dla każdego dzierżawcy:** *tenants1-dpt -&lt;użytkownika&gt;. database.windows.net*
    - **Wielodostępną bazą danych:** *tenants1-mt -&lt;użytkownika&gt;. database.windows.net* 
2. Kliknij kolejno opcje **Połącz** > **Aparat bazy danych...** :

   ![serwer wykazu](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Pokaz poświadczenia są: Identyfikator logowania = *developer*, hasło = *P\@ssword1*

    Na poniższej ilustracji przedstawiono nazwy logowania dla *bazy danych dla każdego dzierżawcy* wzorca. 
    ![połączenia](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Powtórz kroki 2 – 3 i połączyć się z serwerem catalog (poniżej nazw określonych serwerów, na podstawie wzorca dzierżawy bazy danych wybrane)
    - **Aplikacja autonomiczna:** *catalog-sa -&lt;użytkownika&gt;. database.windows.net*
    - **Bazy danych dla każdego dzierżawcy:** *catalog-dpt -&lt;użytkownika&gt;. database.windows.net*
    - **Wielodostępną bazą danych:** *catalog-mt -&lt;użytkownika&gt;. database.windows.net*


Po pomyślnym nawiązaniu połączenia powinny być widoczne wszystkie serwery. Lista baz danych mogą się różnić, w zależności od dzierżawcy, które zostały aprowizowane.

Na poniższej ilustracji przedstawiono logowania dla *bazy danych dla każdego dzierżawcy* wzorca.

![eksplorator obiektów](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Kolejne kroki
- [Wdróż aplikację Wingtip Tickets autonomiczny SaaS](saas-standaloneapp-get-started-deploy.md)
- [Wdrażanie bazy danych SaaS biletów o nazwie Wingtip każdej dzierżawy aplikacji](saas-dbpertenant-get-started-deploy.md)
- [Wdrażanie aplikacji Wingtip Tickets SaaS wielodostępnej w bazie danych](saas-multitenantdb-get-started-deploy.md)

