---
title: Wskazówki dotyczące na przykład wielodostępnych aplikacji bazy danych SQL — Wingtip SaaS | Dokumentacja firmy Microsoft
description: Zawiera wskazówki i kroki dotyczące instalowania i uruchamiania przykładowej aplikacji wielodostępnych, która używa usługi Azure SQL Database w przykładzie Wingtip biletów SaaS.
keywords: samouczek usługi sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 1e3ecd27ce7ec2e2763a7249428875e51872bf63
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Ogólne wskazówki dotyczące pracy z biletów Wingtip przykładowej aplikacji SaaS

Ten artykuł zawiera ogólne wskazówki dotyczące uruchamiania aplikacji SaaS próbki Wingtip biletów, które używają bazy danych SQL Azure. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Pobierz i odblokować skrypty Wingtip biletów SaaS

Zawartość pliku wykonywalnego (skrypty, biblioteki dll) mogą być blokowane przez system Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębnione. Podczas wyodrębniania pliku zip skrypty **wykonaj poniższe kroki, aby odblokować plik zip przed wyodrębniania**. Dzięki temu można uruchamiać skrypty.

1. Przejdź do repozytorium SaaS GitHub biletów Wingtip dla wzorca dzierżawy bazy danych, które chcesz zapoznać: 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kliknij przycisk **klonowania lub pobierania**.
3. Kliknij przycisk **Pobierz zip** i Zapisz plik.
4. Kliknij prawym przyciskiem myszy plik zip, a następnie wybierz **właściwości**. Nazwa pliku zip odpowiada nazwie repozytorium. (np. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Na **ogólne** wybierz opcję **Odblokuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty znajdują się w *... \\Modułów uczenia* folderu.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Praca z skryptów PowerShell biletów Wingtip

Maksymalne wykorzystanie próbki należy przejść do podanego skryptów. Użyj punktów przerwania i krok za pomocą skryptów, jak wykonanie i zbadać implementowania różnych wzorców SaaS. Aby łatwo krok po kroku podany skryptów i modułów do zrozumienia najlepsze, zaleca się używanie [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualizuj plik konfiguracji wdrożenia

Edytuj **UserConfig.psm1** pliku z zasobów grup i użytkowników zostanie ustawiona wartość podczas wdrażania:

1. Otwórz *PowerShell ISE* i obciążenia... \\Modułów szkoleniowych\\*UserConfig.psm1* 
2. Aktualizacja *ResourceGroupName* i *nazwa* z określonymi wartościami dla danego wdrożenia (wierszach 10 i 11 tylko).
3. Zapisać zmiany!

Ustawienie tych wartości w tym miejscu po prostu zapewnia trzeba zaktualizować te wartości specyficzne dla wdrażania wszystkie skrypty.

### <a name="execute-the-scripts-by-pressing-f5"></a>Wykonywanie skryptów, naciskając klawisz F5

Użyj skryptów kilka *$PSScriptRoot* do przeglądania folderów, i *$PSScriptRoot* jest oceniana tylko wtedy, gdy skryptów są wykonywane przez naciśnięcie przycisku **F5**.  Wyróżnianie i uruchamianie zaznaczenia (**F8**) może powodować błędy, więc naciśnij **F5** podczas uruchamiania skryptów.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Przechodzenie przez skrypty w celu zapoznania się z implementacją

Najlepszy sposób, aby zrozumieć skryptów jest krokowe je, aby zobaczyć, co zrobić. Zapoznaj się z dołączonej **pokaz -** skrypty, które stanowią czytelna ogólny przepływ pracy. **Pokaz -** skrypty Pokaż kroki wymagane do wykonywania poszczególnych zadań, a więc Ustaw punkty przerwania i przejść głębiej do poszczególnych wywołań, aby wyświetlić szczegóły implementacji dla różnych wzorców SaaS.

Wskazówki dotyczące eksplorowania i krokowe wykonywanie skryptów programu PowerShell:

- Otwórz **pokaz -** skryptów w programie PowerShell ISE.
- Wykonanie lub kontynuować **F5** (przy użyciu **F8** nie jest zalecana, ponieważ *$PSScriptRoot* nie jest oceniany, podczas uruchamiania opcji skryptu).
- Umieść punkty przerwania, klikając lub zaznaczając wiersz i naciskając klawisz **F9**.
- Pomiń wywołanie funkcji lub skryptu, używając klawisza **F10**.
- Wejdź do wywoływanej funkcji lub skryptu, używając klawisza **F11**.
- Wyjdź z bieżącego wywołania funkcji lub skryptu, używając kombinacji klawiszy **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Badanie schematu bazy danych i wykonywanie zapytań SQL za pomocą aplikacji SSMS

Użyj [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) połączenia i przeglądanie serwerów aplikacji i baz danych.

Wdrożenie ma początkowo dzierżawców i serwerów wykazu bazy danych SQL do nawiązania połączenia. Nadawanie nazw serwerów zależy od wzorca dzierżawy bazy danych (zobacz pod kątem specyfiki). 

   - **Aplikacja autonomiczna:** serwerów dla każdego dzierżawcy (np. *contosoconcerthall -&lt;użytkownika&gt;*  serwera) i *katalogu-sa -&lt;użytkownika&gt;*
   - **Bazy danych dla poszczególnych dzierżawców:** *tenants1-dpt -&lt;użytkownika&gt;*  i *katalogu-dpt -&lt;użytkownika&gt;*  serwerów
   - **Wielodostępne bazy danych:** *tenants1-mt -&lt;użytkownika&gt;*  i *katalogu-mt -&lt;użytkownika&gt;*  serwerów

W celu zapewnienia połączenia pokaz pomyślne, wszystkie serwery mają [reguły zapory](sql-database-firewall-configure.md) stosowanie wszystkich adresów IP za pośrednictwem.


1. Otwórz *SSMS* i połącz się dzierżawcy. Nazwa serwera jest zależna od wzorzec dzierżawy bazy danych, wybranych (patrz pod kątem specyfiki):
    - **Aplikacja autonomiczna:** serwerów poszczególnych dzierżawców (np. *contosoconcerthall-&lt;User&gt;.database.windows.net*) 
    - **Bazy danych dla poszczególnych dzierżawców:** *tenants1-dpt -&lt;użytkownika&gt;. database.windows.net*
    - **Wielodostępne bazy danych:** *tenants1-mt -&lt;użytkownika&gt;. database.windows.net* 
2. Kliknij kolejno opcje **Połącz** > **Aparat bazy danych...** :

   ![serwer wykazu](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. W wersji demonstracyjnej są używane następujące poświadczenia: Nazwa logowania = *developer*, hasło =*P@ssword1*

    Na poniższym obrazie przedstawiono dane logowania dla *bazy danych dla każdego dzierżawcy* wzorca. 
    ![Połączenia](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Powtórz kroki 2 i 3 i nawiąż połączenie z serwerem katalogu (zobacz poniżej określonego serwera nazw opartych na wzorcu dzierżawy bazy danych wybrane)
    - **Aplikacja autonomiczna:** *katalogu-sa -&lt;użytkownika&gt;. database.windows.net*
    - **Bazy danych dla poszczególnych dzierżawców:** *katalogu-dpt -&lt;użytkownika&gt;. database.windows.net*
    - **Wielodostępne bazy danych:** *katalogu-mt -&lt;użytkownika&gt;. database.windows.net*


Po pomyślnym nawiązaniu połączenia powinny być widoczne wszystkie serwery. Listy baz danych może być różne w zależności od dzierżawcy, które zostały udostępnione.

Na poniższym obrazie przedstawiono w dzienniku w *bazy danych dla każdego dzierżawcy* wzorca.

![eksplorator obiektów](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Kolejne kroki
- [Wdrażanie aplikacji SaaS autonomiczny biletów Wingtip](saas-standaloneapp-get-started-deploy.md)
- [Wdrażania bazy danych SaaS biletów Wingtip każdej dzierżawy aplikacji](saas-dbpertenant-get-started-deploy.md)
- [Wdrażanie aplikacji Wingtip biletów SaaS wielodostępne w bazie danych](saas-multitenantdb-get-started-deploy.md)

